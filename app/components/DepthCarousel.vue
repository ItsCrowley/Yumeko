<script setup lang="ts">
import gsap from 'gsap'

type DepthCarouselItem = string | { image: string; alt?: string }
type TiltDirection = 'left' | 'right'

interface DepthCarouselProps {
    items?: DepthCarouselItem[]
    cardWidth?: number
    cardHeight?: number
    radius?: number
    tint?: string
    depth?: number
    spread?: number
    tilt?: number
    tiltDirection?: TiltDirection
    perspective?: number
    visibleCards?: number
    falloff?: number
    blur?: number
    duration?: number
    ease?: string
    autoplay?: boolean
    autoplayDelay?: number
    loop?: boolean
    showIndicators?: boolean
    fullBleed?: boolean
    onChange?: (index: number, item: { image: string; alt?: string }) => void
    className?: string
}

interface CarouselConfig {
    count: number
    depth: number
    spread: number
    tilt: number
    tiltDirection: TiltDirection
    visibleCards: number
    falloff: number
    blur: number
    duration: number
    ease: string
    loop: boolean
    cardWidth: number
    autoplayDelay: number
}

interface DragState {
    x: number
    startPos: number
    lastX: number
    lastT: number
    v: number
    moved: boolean
    id: number
}

const DEFAULT_ITEMS: DepthCarouselItem[] = [
    { image: 'https://picsum.photos/seed/depth1/800/1000', alt: 'Slide 1' },
    { image: 'https://picsum.photos/seed/depth2/800/1000', alt: 'Slide 2' },
    { image: 'https://picsum.photos/seed/depth3/800/1000', alt: 'Slide 3' },
    { image: 'https://picsum.photos/seed/depth4/800/1000', alt: 'Slide 4' },
    { image: 'https://picsum.photos/seed/depth5/800/1000', alt: 'Slide 5' },
    { image: 'https://picsum.photos/seed/depth6/800/1000', alt: 'Slide 6' }
]

const clamp = (v: number, min: number, max: number) =>
    Math.min(Math.max(v, min), max)

const normalizeItem = (
    item: DepthCarouselItem
): { image: string; alt?: string } =>
    typeof item === 'string' ? { image: item, alt: '' } : item

const props = withDefaults(defineProps<DepthCarouselProps>(), {
    cardWidth: 300,
    cardHeight: 380,
    radius: 18,
    tint: '#05060a',
    depth: 220,
    spread: 90,
    tilt: 22,
    tiltDirection: 'right',
    perspective: 1400,
    visibleCards: 4,
    falloff: 0.2,
    blur: 6,
    duration: 700,
    ease: 'power3.out',
    autoplay: false,
    autoplayDelay: 3200,
    loop: true,
    showIndicators: true,
    fullBleed: false,
    className: ''
})

const data = computed(() =>
    (Array.isArray(props.items) ? props.items : DEFAULT_ITEMS).map(normalizeItem)
)

const count = computed(() => data.value.length)

const rootRef = ref<HTMLDivElement | null>(null)
const stageRef = ref<HTMLDivElement | null>(null)

const cardRefs: (HTMLDivElement | null)[] = []
const overlayRefs: (HTMLSpanElement | null)[] = []

const posRef = { current: 0 }
const focusRef = { current: 0 }
const tweenRef: { current: gsap.core.Tween | null } = { current: null }
const scaleRef = { current: 1 }

const dragRef: { current: DragState | null } = { current: null }

const wheelTimerRef: {
    current: ReturnType<typeof setTimeout> | null
} = { current: null }

const autoTimerRef: {
    current: ReturnType<typeof setInterval> | null
} = { current: null }

const reducedRef = { current: false }

const active = ref(0)

const widthRef = { current: 0 }

const cfgRef = computed<CarouselConfig>(() => ({
    count: count.value,
    depth: props.depth,
    spread: props.spread,
    tilt: props.tilt,
    tiltDirection: props.tiltDirection,
    visibleCards: props.visibleCards,
    falloff: props.falloff,
    blur: props.blur,
    duration: props.duration,
    ease: props.ease,
    loop: props.loop,
    cardWidth: props.cardWidth,
    autoplayDelay: props.autoplayDelay
}))

const setCardRef = (el: unknown, index: number) => {
    cardRefs[index] = el as HTMLDivElement | null
}

const setOverlayRef = (el: unknown, index: number) => {
    overlayRefs[index] = el as HTMLSpanElement | null
}

const updateScale = (width: number) => {
    const cfg = cfgRef.value
    widthRef.current = width

    const needed = cfg.cardWidth + Math.abs(cfg.spread) * 1.5 + 2

    scaleRef.current = props.fullBleed
        ? 1
        : clamp(width / needed, 0.4, 1.2)
}

const layout = (pos: number) => {
    const cfg = cfgRef.value
    const n = cfg.count

    if (!n) return

    const dir = cfg.tiltDirection === 'left' ? -1 : 1
    const sc = scaleRef.current

    const halfCard = (cfg.cardWidth * sc) / 2
    const offsetX =
        widthRef.current > 0 ? -(widthRef.current / 2 - halfCard) : 0

    for (let i = 0; i < n; i++) {
        const el = cardRefs[i]

        if (!el) continue

        let d = i - pos

        if (cfg.loop && n > 1) {
            d = ((d % n) + n) % n

            if (d > n / 2) d -= n
        }

        const back = Math.max(0, d)
        const az = Math.abs(d)
        const shown = az <= cfg.visibleCards + 0.5

        const tz = -cfg.depth * d
        const tx = dir * cfg.spread * d + offsetX
        const ry = dir * cfg.tilt * clamp(d, 0, 1)

        let opacity = d < 0 ? Math.max(0, 1 + d) : 1

        if (!shown) opacity = 0

        const brightness = Math.max(0.15, 1 - back * cfg.falloff)

        const blurPx =
            cfg.blur > 0
                ? Math.min(
                    cfg.blur,
                    (back / Math.max(1, cfg.visibleCards)) * cfg.blur
                )
                : 0

        const zi = Math.round(2000 - d * 20)

        el.style.transform = `translate(-50%, -50%) scale(${sc}) translateX(${tx.toFixed(2)}px) translateZ(${tz.toFixed(2)}px) rotateY(${ry.toFixed(3)}deg)`
        el.style.opacity = opacity.toFixed(3)
        el.style.filter = `brightness(${brightness.toFixed(3)}) blur(${blurPx.toFixed(2)}px)`
        el.style.zIndex = String(zi)
        el.style.pointerEvents = shown && opacity > 0.05 ? 'auto' : 'none'

        const overlay = overlayRefs[i]

        if (overlay) {
            overlay.style.opacity = clamp(
                back * cfg.falloff * 1.25,
                0,
                0.86
            ).toFixed(3)
        }
    }
}

const notify = (index: number) => {
    active.value = index

    const item = data.value[index]

    if (item) {
        props.onChange?.(index, item)
    }
}

const tweenTo = (target: number, animate: boolean) => {
    tweenRef.current?.kill()

    const cfg = cfgRef.value
    const proxy = { p: posRef.current }

    const dur = animate && !reducedRef.current
        ? cfg.duration / 1000
        : 0

    tweenRef.current = gsap.to(proxy, {
        p: target,
        duration: dur,
        ease: cfg.ease,
        onUpdate: () => {
            posRef.current = proxy.p
            layout(proxy.p)
        },
        onComplete: () => {
            const n = cfg.count

            if (n > 0) {
                posRef.current = ((posRef.current % n) + n) % n
            }

            layout(posRef.current)
        }
    })
}

const setFocus = (rawIndex: number, animate = true) => {
    const cfg = cfgRef.value
    const n = cfg.count

    if (!n) return

    const index = cfg.loop
        ? ((rawIndex % n) + n) % n
        : clamp(rawIndex, 0, n - 1)

    let delta = index - posRef.current

    if (cfg.loop && n > 1) {
        delta = ((delta % n) + n) % n

        if (delta > n / 2) delta -= n
    }

    tweenTo(posRef.current + delta, animate)

    if (index !== focusRef.current) {
        focusRef.current = index
        notify(index)
    }
}

const navigateBy = (step: number) => {
    setFocus(focusRef.current + step, true)
}

const onPointerDown = (event: PointerEvent) => {
    const cfg = cfgRef.value

    if (cfg.count < 2) return

    tweenRef.current?.kill()

    dragRef.current = {
        x: event.clientX,
        startPos: posRef.current,
        lastX: event.clientX,
        lastT: performance.now(),
        v: 0,
        moved: false,
        id: event.pointerId
    }
}

const onPointerMove = (event: PointerEvent) => {
    const drag = dragRef.current

    if (!drag) return

    const cfg = cfgRef.value
    const stepPx = Math.max(
        cfg.cardWidth * 0.55 * scaleRef.current,
        40
    )

    const dx = event.clientX - drag.x

    if (!drag.moved && Math.abs(dx) > 4) {
        drag.moved = true
        rootRef.value?.setPointerCapture(drag.id)
    }

    if (!drag.moved) return

    const now = performance.now()
    const dt = Math.max(now - drag.lastT, 1)

    drag.v = (event.clientX - drag.lastX) / dt
    drag.lastX = event.clientX
    drag.lastT = now

    posRef.current = drag.startPos - dx / stepPx

    layout(posRef.current)
}

const onPointerEnd = () => {
    const drag = dragRef.current

    if (!drag) return

    dragRef.current = null

    if (!drag.moved) return

    const cfg = cfgRef.value
    const stepPx = Math.max(
        cfg.cardWidth * 0.55 * scaleRef.current,
        40
    )

    const projected = posRef.current - (drag.v * 180) / stepPx

    setFocus(Math.round(projected), true)
}

const onKeyDown = (event: KeyboardEvent) => {
    if (event.key === 'ArrowLeft') {
        event.preventDefault()
        navigateBy(-1)
    } else if (event.key === 'ArrowRight') {
        event.preventDefault()
        navigateBy(1)
    }
}

const onCardClick = (index: number) => {
    if (dragRef.current?.moved) return

    setFocus(index, true)
}

let isMounted = false
let resizeObserver: ResizeObserver | null = null
let wheelListener: ((event: WheelEvent) => void) | null = null
let cleanupAutoplay: (() => void) | null = null

const setupAutoplay = () => {
    cleanupAutoplay?.()
    cleanupAutoplay = null

    if (!isMounted) return

    reducedRef.current =
        typeof window !== 'undefined' &&
        window.matchMedia('(prefers-reduced-motion: reduce)').matches

    if (!props.autoplay || reducedRef.current || count.value < 2) {
        return
    }

    const stop = () => {
        if (autoTimerRef.current) {
            clearInterval(autoTimerRef.current)
        }

        autoTimerRef.current = null
    }

    autoTimerRef.current = setInterval(() => {
        navigateBy(1)
    }, Math.max(cfgRef.value.autoplayDelay, 600))

    cleanupAutoplay = stop
}

watch(
    [
        () => props.autoplay,
        () => props.autoplayDelay,
        count
    ],
    setupAutoplay
)

watch(
    [
        () => props.depth,
        () => props.spread,
        () => props.tilt,
        () => props.tiltDirection,
        () => props.visibleCards,
        () => props.falloff,
        () => props.blur,
        () => props.cardWidth,
        () => props.cardHeight,
        () => props.radius,
        () => props.fullBleed,
        count
    ],
    () => {
        if (rootRef.value) {
            updateScale(rootRef.value.clientWidth)
        }

        layout(posRef.current)
    },
    { flush: 'post' }
)

onMounted(() => {
    const root = rootRef.value

    if (!root) return

    resizeObserver = new ResizeObserver(entries => {
        const entry = entries[0]

        if (!entry) return

        updateScale(entry.contentRect.width)
        layout(posRef.current)
    })

    resizeObserver.observe(root)

    wheelListener = (event: WheelEvent) => {
        const cfg = cfgRef.value

        if (cfg.count < 2) return

        event.preventDefault()

        tweenRef.current?.kill()

        const raw =
            Math.abs(event.deltaX) > Math.abs(event.deltaY)
                ? event.deltaX
                : event.deltaY

        const delta = event.deltaMode === 1 ? raw * 24 : raw

        const step = clamp(
            delta / (cfg.cardWidth * 0.9),
            -0.6,
            0.6
        )

        posRef.current += step

        layout(posRef.current)

        if (wheelTimerRef.current) {
            clearTimeout(wheelTimerRef.current)
        }

        wheelTimerRef.current = setTimeout(() => {
            setFocus(Math.round(posRef.current), true)
        }, 130)
    }

    root.addEventListener('wheel', wheelListener, {
        passive: false
    })

    layout(posRef.current)

    isMounted = true

    setupAutoplay()
})

onBeforeUnmount(() => {
    isMounted = false

    cleanupAutoplay?.()
    cleanupAutoplay = null

    tweenRef.current?.kill()

    if (wheelTimerRef.current) {
        clearTimeout(wheelTimerRef.current)
    }

    if (autoTimerRef.current) {
        clearInterval(autoTimerRef.current)
    }

    resizeObserver?.disconnect()

    if (rootRef.value && wheelListener) {
        rootRef.value.removeEventListener('wheel', wheelListener)
    }
})
</script>

<template>
    <div ref="rootRef" :class="[
        'relative flex h-full min-h-80 w-full cursor-grab touch-pan-y select-none items-center justify-center outline-none perspective-origin-[50%_50%] active:cursor-grabbing focus-visible:rounded-xl focus-visible:outline-2 focus-visible:outline-white/50 focus-visible:outline-offset-4',
        props.fullBleed ? 'overflow-hidden' : '',
        props.className
    ]" :style="{ perspective: `${props.perspective}px` }" role="group" aria-roledescription="carousel"
        aria-label="Depth carousel" tabindex="0" @pointerdown="onPointerDown" @pointermove="onPointerMove"
        @pointerup="onPointerEnd" @pointercancel="onPointerEnd" @keydown="onKeyDown">
        <div ref="stageRef" class="absolute inset-0 transform-3d">
            <div v-for="(item, i) in data" :key="i" :ref="el => setCardRef(el, i)"
                class="absolute left-1/2 top-1/2 cursor-pointer overflow-hidden bg-[#0b0d12] shadow-[0_30px_60px_-20px_rgba(0,0,0,0.65),0_8px_20px_-10px_rgba(0,0,0,0.5)] transform-[translate(-50%,-50%)] origin-center will-change-[transform,opacity,filter]"
                :style="{
                    width: props.fullBleed ? '100%' : `${props.cardWidth}px`,
                    height: props.fullBleed ? '100%' : `${props.cardHeight}px`,
                    borderRadius: props.fullBleed ? '0px' : `${props.radius}px`
                }" aria-roledescription="slide" :aria-label="`${i + 1} of ${count}`" :aria-hidden="active !== i"
                @click="onCardClick(i)">
                <img class="pointer-events-none block h-full w-full select-none object-cover [-webkit-user-drag:none]"
                    :src="item.image" :alt="item.alt || ''" :draggable="false" />

                <span :ref="el => setOverlayRef(el, i)"
                    class="pointer-events-none absolute inset-0 opacity-0 mix-blend-multiply"
                    :style="{ background: props.tint }"></span>
            </div>
        </div>
    </div>
</template>