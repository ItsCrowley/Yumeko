<script setup lang="ts">
import { gsap } from 'gsap'

const menuItems = [
    {
        title: 'Anime',
        slug: '/anime',
        isMenu: true
    },
    {
        title: 'Genres',
        slug: '/genres',
        isMenu: true
    },
    {
        title: 'New Season',
        slug: '/new-season',
        isMenu: false
    },
    {
        title: 'Now',
        slug: '/now',
        isMenu: false
    }
]

const searchFields = [
    { key: 'title', label: 'Title' },
    { key: 'englishTitle', label: 'English Title' },
    { key: 'japaneseTitle', label: 'Japanese Title' },
    { key: 'synonyms', label: 'Synonyms' },
    { key: 'genres', label: 'Genres' },
    { key: 'themes', label: 'Themes' },
    { key: 'type', label: 'Type' },
    { key: 'status', label: 'Status' },
    { key: 'season', label: 'Season' },
    { key: 'year', label: 'Release Year' },
    { key: 'studio', label: 'Studio' },
    { key: 'producer', label: 'Producer' },
    { key: 'director', label: 'Director' },
    { key: 'source', label: 'Source' },
    { key: 'episodes', label: 'Episodes' },
    { key: 'duration', label: 'Duration' },
    { key: 'rating', label: 'Rating' },
    { key: 'score', label: 'Score' },
    { key: 'popularity', label: 'Popularity' },
    { key: 'demographic', label: 'Demographic' }
]

const menuPanelRef = ref<HTMLElement | null>(null)
const menuContentRef = ref<HTMLElement | null>(null)
const panelInnerRef = ref<HTMLElement | null>(null)

const activeMenuId = ref<string | null>(null)
const displayedMenuId = ref<string | null>(null)
const previousMenuId = ref<string | null>(null)
const panelVisible = ref(false)
const panelHeight = ref(0)
const isHoveringContent = ref(false)
const transitionToken = ref(0)

const heightTweenState = { value: 0 }

let closeTimer: ReturnType<typeof setTimeout> | null = null
let desktopMediaQuery: MediaQueryList | null = null

const CLOSE_DELAY = 250
const isDesktop = ref(false)

const mobileMenuOpen = ref(false)
const mobileMenuContainerRef = ref<HTMLElement | null>(null)

const isSearchOpen = ref(false)
const searchQuery = ref('')
const selectedSearchFields = ref(searchFields.map(field => field.key))

const searchOverlayRef = ref<HTMLElement | null>(null)
const searchDialogRef = ref<HTMLElement | null>(null)
const searchInputRef = ref<HTMLInputElement | null>(null)

const searchTransitionToken = ref(0)

const allSearchFieldsSelected = computed(
    () => selectedSearchFields.value.length === searchFields.length
)

const clearCloseTimer = () => {
    if (closeTimer) {
        clearTimeout(closeTimer)
        closeTimer = null
    }
}

const handleDesktopChange = (event: MediaQueryListEvent) => {
    isDesktop.value = event.matches

    if (!event.matches) {
        clearCloseTimer()
        transitionToken.value++

        gsap.killTweensOf(menuPanelRef.value)
        gsap.killTweensOf(menuContentRef.value)
        gsap.killTweensOf(heightTweenState)

        panelVisible.value = false
        activeMenuId.value = null
        displayedMenuId.value = null
        previousMenuId.value = null
        panelHeight.value = 0
        mobileMenuOpen.value = false
    } else {
        mobileMenuOpen.value = false
    }
}

const menuIndex = (id: string | null) => {
    if (!id) return -1
    return menuItems.findIndex(item => item.title === id)
}

const startCloseTimer = () => {
    clearCloseTimer()

    closeTimer = setTimeout(() => {
        if (!isHoveringContent.value) {
            activeMenuId.value = null
        }

        closeTimer = null
    }, CLOSE_DELAY)
}

const measureInnerHeight = () => {
    return panelInnerRef.value?.scrollHeight ?? 0
}

const animatePanelIn = () => {
    const panel = menuPanelRef.value

    if (!panel) return

    gsap.killTweensOf(panel)

    gsap.fromTo(panel, {
        autoAlpha: 0,
        scale: 0.96,
        y: -4
    }, {
        autoAlpha: 1,
        scale: 1,
        y: 0,
        duration: 0.18,
        ease: 'power2.out'
    })
}

const animatePanelOut = () => {
    const panel = menuPanelRef.value

    if (!panel) return Promise.resolve()

    gsap.killTweensOf(panel)

    return new Promise<void>(resolve => {
        gsap.to(panel, {
            autoAlpha: 0,
            scale: 0.96,
            y: -4,
            duration: 0.15,
            ease: 'power2.in',
            onComplete: resolve
        })
    })
}

const animatePanelHeight = (nextHeight: number, duration = 0.2) => {
    gsap.killTweensOf(heightTweenState)

    heightTweenState.value = panelHeight.value || measureInnerHeight()

    return new Promise<void>(resolve => {
        gsap.to(heightTweenState, {
            value: nextHeight,
            duration,
            ease: 'power2.out',
            onUpdate: () => {
                panelHeight.value = heightTweenState.value
            },
            onComplete: () => {
                panelHeight.value = nextHeight
                resolve()
            }
        })
    })
}

const animateContentIn = (
    direction: 'from-start' | 'from-end' | 'center' = 'center'
) => {
    const wrap = menuContentRef.value

    if (!wrap) return

    gsap.killTweensOf(wrap)

    if (direction === 'center') {
        gsap.fromTo(wrap, {
            autoAlpha: 0,
            y: 4
        }, {
            autoAlpha: 1,
            y: 0,
            duration: 0.18,
            ease: 'power2.out'
        })

        return
    }

    gsap.fromTo(wrap, {
        autoAlpha: 0,
        x: direction === 'from-end' ? 20 : -20
    }, {
        autoAlpha: 1,
        x: 0,
        duration: 0.18,
        ease: 'power2.out'
    })
}

const animateContentOut = (direction: 'to-start' | 'to-end') => {
    const wrap = menuContentRef.value

    if (!wrap) return Promise.resolve()

    gsap.killTweensOf(wrap)

    return new Promise<void>(resolve => {
        gsap.to(wrap, {
            autoAlpha: 0,
            x: direction === 'to-start' ? -20 : 20,
            duration: 0.12,
            ease: 'power2.in',
            onComplete: resolve
        })
    })
}

const switchMenu = async (nextMenuId: string) => {
    const currentMenuId = displayedMenuId.value

    if (currentMenuId === null || currentMenuId === nextMenuId) {
        previousMenuId.value = currentMenuId
        displayedMenuId.value = nextMenuId

        await nextTick()

        panelHeight.value = measureInnerHeight()

        await nextTick()

        animatePanelIn()
        animateContentIn('center')

        return
    }

    const token = ++transitionToken.value
    const fromIndex = menuIndex(currentMenuId)
    const toIndex = menuIndex(nextMenuId)
    const isMovingForward = toIndex > fromIndex

    const outDirection: 'to-start' | 'to-end' = isMovingForward
        ? 'to-start'
        : 'to-end'

    const inDirection: 'from-start' | 'from-end' = isMovingForward
        ? 'from-end'
        : 'from-start'

    await animateContentOut(outDirection)

    if (token !== transitionToken.value) return

    previousMenuId.value = currentMenuId
    displayedMenuId.value = nextMenuId

    await nextTick()

    if (token !== transitionToken.value) return

    const nextHeight = measureInnerHeight()

    await Promise.all([
        animatePanelHeight(nextHeight, 0.2),
        (async () => {
            animateContentIn(inDirection)
        })()
    ])
}

const openMenu = async (menuId: string) => {
    if (!isDesktop.value) return

    clearCloseTimer()

    if (activeMenuId.value === menuId) return

    const wasOpen = panelVisible.value

    activeMenuId.value = menuId

    if (!wasOpen) {
        previousMenuId.value = null
        displayedMenuId.value = menuId
        panelVisible.value = true

        await nextTick()

        panelHeight.value = measureInnerHeight()

        await nextTick()

        animatePanelIn()
        animateContentIn('center')

        return
    }

    if (displayedMenuId.value !== menuId) {
        await switchMenu(menuId)
    }
}

const closeMenu = async () => {
    clearCloseTimer()

    if (!panelVisible.value) {
        activeMenuId.value = null
        return
    }

    const token = ++transitionToken.value

    activeMenuId.value = null

    await animatePanelOut()

    if (token !== transitionToken.value) return

    panelVisible.value = false
    previousMenuId.value = displayedMenuId.value
    displayedMenuId.value = null
    panelHeight.value = 0
}

const handleMenuPointerEnter = (item: typeof menuItems[number]) => {
    if (!isDesktop.value) return

    clearCloseTimer()

    if (!item.isMenu) {
        activeMenuId.value = null
        return
    }

    openMenu(item.title)
}

const handleMenuPointerLeave = () => {
    if (!isDesktop.value) return

    startCloseTimer()
}

const handlePanelPointerEnter = () => {
    if (!isDesktop.value) return

    isHoveringContent.value = true
    clearCloseTimer()
}

const handlePanelPointerLeave = () => {
    if (!isDesktop.value) return

    isHoveringContent.value = false
    startCloseTimer()
}

const handleMenuFocus = (item: typeof menuItems[number]) => {
    if (item.isMenu && isDesktop.value) {
        openMenu(item.title)
    }
}

const handleMenuKeydown = (
    event: KeyboardEvent,
    item: typeof menuItems[number]
) => {
    if (event.key === 'Escape') {
        closeMenu()
        return
    }

    if (
        (event.key === 'Enter' || event.key === ' ') &&
        item.isMenu &&
        isDesktop.value
    ) {
        event.preventDefault()

        if (activeMenuId.value === item.title) {
            closeMenu()
        } else {
            openMenu(item.title)
        }
    }
}

const openMobileMenu = () => {
    mobileMenuOpen.value = !mobileMenuOpen.value
}

const closeMobileMenu = () => {
    mobileMenuOpen.value = false
}

const toggleSearchField = (key: string) => {
    if (selectedSearchFields.value.includes(key)) {
        selectedSearchFields.value = selectedSearchFields.value.filter(
            field => field !== key
        )
    } else {
        selectedSearchFields.value = [
            ...selectedSearchFields.value,
            key
        ]
    }
}

const selectAllSearchFields = () => {
    selectedSearchFields.value = searchFields.map(field => field.key)
}

const clearSearchFields = () => {
    selectedSearchFields.value = []
}

const openSearch = async () => {
    closeMobileMenu()
    clearCloseTimer()

    if (panelVisible.value) {
        closeMenu()
    }

    if (isSearchOpen.value) return

    isSearchOpen.value = true

    const token = ++searchTransitionToken.value

    await nextTick()

    if (token !== searchTransitionToken.value) return

    const overlay = searchOverlayRef.value
    const dialog = searchDialogRef.value

    if (!overlay || !dialog) return

    gsap.killTweensOf([overlay, dialog])

    gsap.fromTo(overlay, {
        autoAlpha: 0
    }, {
        autoAlpha: 1,
        duration: 0.2,
        ease: 'power2.out'
    })

    gsap.fromTo(dialog, {
        autoAlpha: 0,
        y: 18,
        scale: 0.97
    }, {
        autoAlpha: 1,
        y: 0,
        scale: 1,
        duration: 0.28,
        ease: 'power3.out'
    })

    searchInputRef.value?.focus()
}

const closeSearch = () => {
    if (!isSearchOpen.value) return Promise.resolve()

    const token = ++searchTransitionToken.value

    const overlay = searchOverlayRef.value
    const dialog = searchDialogRef.value

    if (!overlay || !dialog) {
        isSearchOpen.value = false
        return Promise.resolve()
    }

    gsap.killTweensOf([overlay, dialog])

    return new Promise<void>(resolve => {
        const timeline = gsap.timeline({
            onComplete: () => {
                if (token === searchTransitionToken.value) {
                    isSearchOpen.value = false
                }

                resolve()
            }
        })

        timeline
            .to(dialog, {
                autoAlpha: 0,
                y: 12,
                scale: 0.98,
                duration: 0.16,
                ease: 'power2.in'
            })
            .to(overlay, {
                autoAlpha: 0,
                duration: 0.18,
                ease: 'power2.in'
            }, '<')
    })
}

const submitSearch = async () => {
    const query = searchQuery.value.trim()

    if (!query || selectedSearchFields.value.length === 0) return

    const fields = selectedSearchFields.value.join(',')

    await closeSearch()

    await navigateTo({
        path: '/search',
        query: {
            q: query,
            fields
        }
    })
}

const handleDocumentPointerDown = (event: PointerEvent) => {
    const target = event.target

    if (!(target instanceof Node)) return

    if (isDesktop.value) {
        const panel = menuPanelRef.value

        if (panel?.contains(target)) return

        const menuTrigger = document.querySelector('[data-menu-navigation]')

        if (menuTrigger?.contains(target)) return

        closeMenu()
        return
    }

    if (
        mobileMenuOpen.value &&
        !mobileMenuContainerRef.value?.contains(target)
    ) {
        closeMobileMenu()
    }
}

const handleDocumentKeydown = (event: KeyboardEvent) => {
    if (event.key === 'Escape') {
        if (isSearchOpen.value) {
            closeSearch()
        }

        closeMobileMenu()
        closeMenu()
    }

    if (
        (event.ctrlKey || event.metaKey) &&
        event.key.toLowerCase() === 'k'
    ) {
        event.preventDefault()

        if (!isSearchOpen.value) {
            openSearch()
        }
    }
}

watch(activeMenuId, async (newId) => {
    if (newId === null) {
        const token = ++transitionToken.value

        await animatePanelOut()

        if (token !== transitionToken.value) return

        panelVisible.value = false
        previousMenuId.value = displayedMenuId.value
        displayedMenuId.value = null
        panelHeight.value = 0

        return
    }

    const nextMenu = menuItems.find(
        item => item.title === newId && item.isMenu
    )

    if (!nextMenu) return

    if (!panelVisible.value) {
        previousMenuId.value = null
        displayedMenuId.value = newId
        panelVisible.value = true

        await nextTick()

        panelHeight.value = measureInnerHeight()

        await nextTick()

        animatePanelIn()
        animateContentIn('center')

        return
    }

    if (displayedMenuId.value !== newId) {
        await switchMenu(newId)
    }
})

onMounted(() => {
    desktopMediaQuery = window.matchMedia('(min-width: 1024px)')

    isDesktop.value = desktopMediaQuery.matches

    desktopMediaQuery.addEventListener('change', handleDesktopChange)

    document.addEventListener('pointerdown', handleDocumentPointerDown)
    document.addEventListener('keydown', handleDocumentKeydown)
})

onBeforeUnmount(() => {
    clearCloseTimer()

    desktopMediaQuery?.removeEventListener('change', handleDesktopChange)

    document.removeEventListener('pointerdown', handleDocumentPointerDown)
    document.removeEventListener('keydown', handleDocumentKeydown)

    gsap.killTweensOf(menuPanelRef.value)
    gsap.killTweensOf(menuContentRef.value)
    gsap.killTweensOf(heightTweenState)
    gsap.killTweensOf(searchOverlayRef.value)
    gsap.killTweensOf(searchDialogRef.value)
})
</script>

<template>
    <div class="fixed inset-x-0 top-0 z-50 h-18 border-b border-border/50 bg-background-soft backdrop-blur-xl center">
        <div class="container flex items-center justify-between">
            <NuxtLink to="/" class="flex items-center gap-1.5">
                <Logo class="size-7 text-white" />
                <span class="font-semibold tracking-tight text-white">Yumeko</span>
            </NuxtLink>

            <div class="hidden lg:flex items-center relative" data-menu-navigation>
                <div v-if="panelVisible && displayedMenuId" ref="menuPanelRef"
                    class="absolute right-0 top-[calc(100%+12px)] z-60 w-200 bg-background rounded-xl border border-border/30 p-2 flex flex-col origin-top"
                    @pointerenter="handlePanelPointerEnter" @pointerleave="handlePanelPointerLeave">
                    <div class="w-full overflow-hidden rounded-lg" :style="{ height: `${panelHeight}px` }">
                        <div ref="panelInnerRef">
                            <div ref="menuContentRef">
                                <div v-if="displayedMenuId === 'Anime'"
                                    class="size-full bg-background-soft rounded-lg border border-border/50 grid grid-cols-3 p-2">
                                    <div class="flex flex-col gap-y-1 pr-2 border-r border-border/50">
                                        <NuxtLink to="/"
                                            class="p-4 rounded-md hover:bg-[#1B1D24] transition-all duration-100">
                                            <h4 class="text-white text-[0.8125rem] font-semibold">All Anime</h4>
                                            <p class="text-foreground-muted text-[0.75rem] mt-3">
                                                Explore the complete anime collection.
                                            </p>
                                        </NuxtLink>

                                        <NuxtLink to="/"
                                            class="p-4 rounded-md hover:bg-[#1B1D24] transition-all duration-100">
                                            <h4 class="text-white text-[0.8125rem] font-semibold">Trending Now</h4>
                                            <p class="text-foreground-muted text-[0.75rem] mt-3">
                                                See what anime fans are watching right now.
                                            </p>
                                        </NuxtLink>
                                    </div>

                                    <div class="flex flex-col gap-y-1 px-2 border-r border-border/50">
                                        <NuxtLink to="/"
                                            class="p-4 rounded-md hover:bg-[#1B1D24] transition-all duration-100">
                                            <h4 class="text-white text-[0.8125rem] font-semibold">Top Rated</h4>
                                            <p class="text-foreground-muted text-[0.75rem] mt-3">
                                                Discover highly acclaimed anime series.
                                            </p>
                                        </NuxtLink>

                                        <NuxtLink to="/"
                                            class="p-4 rounded-md hover:bg-[#1B1D24] transition-all duration-100">
                                            <h4 class="text-white text-[0.8125rem] font-semibold">Most Popular</h4>
                                            <p class="text-foreground-muted text-[0.75rem] mt-3">
                                                Fan favorites worth adding to your list.
                                            </p>
                                        </NuxtLink>
                                    </div>

                                    <div class="flex flex-col gap-y-1 pl-2">
                                        <NuxtLink to="/"
                                            class="px-4 py-3 rounded-lg hover:bg-[#1B1D24] transition-all duration-100 text-foreground-muted hover:text-white">
                                            <h4 class="text-[0.75rem] font-semibold">Recently Added</h4>
                                        </NuxtLink>

                                        <NuxtLink to="/"
                                            class="px-4 py-3 rounded-lg hover:bg-[#1B1D24] transition-all duration-100 text-foreground-muted hover:text-white">
                                            <h4 class="text-[0.75rem] font-semibold">Upcoming Anime</h4>
                                        </NuxtLink>

                                        <NuxtLink to="/"
                                            class="px-4 py-3 rounded-lg hover:bg-[#1B1D24] transition-all duration-100 text-foreground-muted hover:text-white">
                                            <h4 class="text-[0.75rem] font-semibold">Recently Updated</h4>
                                        </NuxtLink>

                                        <NuxtLink to="/"
                                            class="px-4 py-3 rounded-lg hover:bg-[#1B1D24] transition-all duration-100 text-foreground-muted hover:text-white">
                                            <h4 class="text-[0.75rem] font-semibold">Completed Anime</h4>
                                        </NuxtLink>
                                    </div>
                                </div>

                                <div v-else-if="displayedMenuId === 'Genres'"
                                    class="size-full bg-background-soft rounded-lg border border-border/50 grid grid-cols-4 gap-px p-2">
                                    <NuxtLink to="/genre/anime"
                                        class="px-3 py-2 rounded-lg hover:bg-[#1B1D24] transition-all duration-100 text-foreground-muted hover:text-white flex items-center gap-2">
                                        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"
                                            class="size-4" stroke-linecap="round" stroke-linejoin="round"
                                            aria-label="انیمه">
                                            <circle cx="11" cy="13" r="9" />
                                            <path
                                                d="M14.35 4.65L16.3 2.7a2.41 2.41 0 0 1 3.4 0l1.6 1.6a2.4 2.4 0 0 1 0 3.4l-1.95 1.95M22 2l-1.5 1.5" />
                                        </svg>
                                        <h4 class="text-[0.75rem] font-semibold">Anime</h4>
                                    </NuxtLink>

                                    <NuxtLink to="/genre/animation"
                                        class="px-3 py-2 rounded-lg hover:bg-[#1B1D24] transition-all duration-100 text-foreground-muted hover:text-white flex items-center gap-2">
                                        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"
                                            class="size-4" stroke-linecap="round" stroke-linejoin="round"
                                            aria-label="انیمیشن">
                                            <rect x="3" y="3" width="18" height="18" rx="3" />
                                            <path d="m9 8 7 4-7 4V8Z" />
                                        </svg>
                                        <h4 class="text-[0.75rem] font-semibold">Animation</h4>
                                    </NuxtLink>

                                    <NuxtLink to="/genre/fantasy"
                                        class="px-3 py-2 rounded-lg hover:bg-[#1B1D24] transition-all duration-100 text-foreground-muted hover:text-white flex items-center gap-2">
                                        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"
                                            class="size-4" stroke-linecap="round" stroke-linejoin="round"
                                            aria-label="فانتزی">
                                            <path d="M12 3 14.5 9.5 21 12l-6.5 2.5L12 21l-2.5-6.5L3 12l6.5-2.5L12 3Z" />
                                        </svg>
                                        <h4 class="text-[0.75rem] font-semibold">Fantasy</h4>
                                    </NuxtLink>

                                    <NuxtLink to="/genre/sci-fi"
                                        class="px-3 py-2 rounded-lg hover:bg-[#1B1D24] transition-all duration-100 text-foreground-muted hover:text-white flex items-center gap-2">
                                        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"
                                            class="size-4" stroke-linecap="round" stroke-linejoin="round"
                                            aria-label="علمی تخیلی">
                                            <circle cx="12" cy="12" r="2" />
                                            <ellipse cx="12" cy="12" rx="10" ry="4" />
                                            <ellipse cx="12" cy="12" rx="4" ry="10" transform="rotate(45 12 12)" />
                                        </svg>
                                        <h4 class="text-[0.75rem] font-semibold">Sci-Fi</h4>
                                    </NuxtLink>

                                    <NuxtLink to="/genre/adventure"
                                        class="px-3 py-2 rounded-lg hover:bg-[#1B1D24] transition-all duration-100 text-foreground-muted hover:text-white flex items-center gap-2">
                                        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"
                                            class="size-4" stroke-linecap="round" stroke-linejoin="round"
                                            aria-label="ماجراجویی">
                                            <path d="m3 3 7.5 18L14 14l7-3.5L3 3Z" />
                                            <path d="m14 14 7 7" />
                                        </svg>
                                        <h4 class="text-[0.75rem] font-semibold">Adventure</h4>
                                    </NuxtLink>

                                    <NuxtLink to="/genre/action"
                                        class="px-3 py-2 rounded-lg hover:bg-[#1B1D24] transition-all duration-100 text-foreground-muted hover:text-white flex items-center gap-2">
                                        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"
                                            class="size-4" stroke-linecap="round" stroke-linejoin="round"
                                            aria-label="اکشن">
                                            <path d="M13 2 3 14h9l-1 8 10-12h-9l1-8Z" />
                                        </svg>
                                        <h4 class="text-[0.75rem] font-semibold">Action</h4>
                                    </NuxtLink>

                                    <NuxtLink to="/genre/comedy"
                                        class="px-3 py-2 rounded-lg hover:bg-[#1B1D24] transition-all duration-100 text-foreground-muted hover:text-white flex items-center gap-2">
                                        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"
                                            class="size-4" stroke-linecap="round" stroke-linejoin="round"
                                            aria-label="کمدی">
                                            <circle cx="12" cy="12" r="10" />
                                            <path d="M8 14s1.5 2 4 2 4-2 4-2" />
                                            <path d="M9 9h.01M15 9h.01" />
                                        </svg>
                                        <h4 class="text-[0.75rem] font-semibold">Comedy</h4>
                                    </NuxtLink>

                                    <NuxtLink to="/genre/drama"
                                        class="px-3 py-2 rounded-lg hover:bg-[#1B1D24] transition-all duration-100 text-foreground-muted hover:text-white flex items-center gap-2">
                                        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"
                                            class="size-4" stroke-linecap="round" stroke-linejoin="round"
                                            aria-label="درام">
                                            <path d="M12 3v18M3 12h18" />
                                            <circle cx="12" cy="12" r="9" />
                                        </svg>
                                        <h4 class="text-[0.75rem] font-semibold">Drama</h4>
                                    </NuxtLink>

                                    <NuxtLink to="/genre/romance"
                                        class="px-3 py-2 rounded-lg hover:bg-[#1B1D24] transition-all duration-100 text-foreground-muted hover:text-white flex items-center gap-2">
                                        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"
                                            class="size-4" stroke-linecap="round" stroke-linejoin="round"
                                            aria-label="عاشقانه">
                                            <path
                                                d="M20.8 4.6a5.5 5.5 0 0 0-7.8 0L12 5.7l-1.1-1.1a5.5 5.5 0 0 0-7.8 7.8l1.1 1.1L12 21l7.8-7.5 1.1-1.1a5.5 5.5 0 0 0-.1-7.8Z" />
                                        </svg>
                                        <h4 class="text-[0.75rem] font-semibold">Romance</h4>
                                    </NuxtLink>

                                    <NuxtLink to="/genre/horror"
                                        class="px-3 py-2 rounded-lg hover:bg-[#1B1D24] transition-all duration-100 text-foreground-muted hover:text-white flex items-center gap-2">
                                        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"
                                            class="size-4" stroke-linecap="round" stroke-linejoin="round"
                                            aria-label="ترسناک">
                                            <path d="M12 22a8 8 0 0 0 8-8V9a8 8 0 0 0-16 0v5a8 8 0 0 0 8 8Z" />
                                            <path d="M9 10h.01M15 10h.01M9 16h6" />
                                        </svg>
                                        <h4 class="text-[0.75rem] font-semibold">Horror</h4>
                                    </NuxtLink>

                                    <NuxtLink to="/genre/mystery"
                                        class="px-3 py-2 rounded-lg hover:bg-[#1B1D24] transition-all duration-100 text-foreground-muted hover:text-white flex items-center gap-2">
                                        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"
                                            class="size-4" stroke-linecap="round" stroke-linejoin="round"
                                            aria-label="معمایی">
                                            <circle cx="12" cy="12" r="10" />
                                            <path d="M9.1 9a3 3 0 0 1 5.8 1c0 2-3 3-3 3" />
                                            <path d="M12 17h.01" />
                                        </svg>
                                        <h4 class="text-[0.75rem] font-semibold">Mystery</h4>
                                    </NuxtLink>

                                    <NuxtLink to="/genre/thriller"
                                        class="px-3 py-2 rounded-lg hover:bg-[#1B1D24] transition-all duration-100 text-foreground-muted hover:text-white flex items-center gap-2">
                                        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"
                                            class="size-4" stroke-linecap="round" stroke-linejoin="round"
                                            aria-label="دلهره‌آور">
                                            <path d="M12 22s8-4 8-11V5l-8-3-8 3v6c0 7 8 11 8 11Z" />
                                            <path d="m10 8 5 4-5 4V8Z" />
                                        </svg>
                                        <h4 class="text-[0.75rem] font-semibold">Thriller</h4>
                                    </NuxtLink>
                                </div>

                                <button v-if="displayedMenuId === 'Genres'"
                                    class="h-8 w-full mt-1.5 px-5 text-foreground-muted text-[0.75rem] font-semibold flex justify-between items-center hover:bg-[#1B1D24] hover:text-white rounded-lg transition-all duration-100">
                                    <span>See All</span>
                                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
                                        stroke-width="2" stroke="currentColor" class="size-3">
                                        <path stroke-linecap="round" stroke-linejoin="round"
                                            d="M13.5 4.5 21 12m0 0-7.5 7.5M21 12H3" />
                                    </svg>
                                </button>
                            </div>
                        </div>
                    </div>
                </div>

                <ul class="relative flex items-center gap-2 z-10">
                    <li v-for="item in menuItems" :key="item.slug">
                        <NuxtLink :to="item.slug"
                            class="text-foreground-muted text-[0.8125rem] font-semibold h-8 px-3 flex items-center rounded-full hover:bg-[#1B1D24] hover:text-white transition-all duration-300 foregroundspace-nowrap"
                            @pointerenter="handleMenuPointerEnter(item)" @pointerleave="handleMenuPointerLeave"
                            @focus="handleMenuFocus(item)"
                            @click="item.isMenu ? ($event.preventDefault(), activeMenuId === item.title ? closeMenu() : openMenu(item.title)) : closeMenu()"
                            @keydown="handleMenuKeydown($event, item)">
                            {{ item.title }}
                        </NuxtLink>
                    </li>
                </ul>

                <div class="w-px h-4 bg-border mx-3"></div>

                <div class="flex items-center gap-2">
                    <button @click="openSearch"
                        class="size-8 text-foreground-muted center hover:bg-[#1B1D24] hover:text-white transition-all duration-300 rounded-full"
                        aria-label="Search">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2"
                            stroke="currentColor" class="size-4">
                            <path stroke-linecap="round" stroke-linejoin="round"
                                d="m21 21-5.197-5.197m0 0A7.5 7.5 0 1 0 5.196 5.196a7.5 7.5 0 0 0 10.607 10.607Z" />
                        </svg>
                    </button>

                    <NuxtLink to="/"
                        class="flex h-8 items-center gap-2 whitespace-nowrap rounded-full bg-primary px-3 text-[0.8125rem] font-semibold text-white transition-all duration-300 hover:bg-primary-hover active:bg-primary-active">
                        <span>Login</span>
                    </NuxtLink>
                </div>
            </div>
        </div>
    </div>

    <div v-if="isSearchOpen" ref="searchOverlayRef"
        class="fixed inset-0 z-100 flex items-start justify-center overflow-y-auto bg-black/65 px-4 pb-10 pt-[10vh] backdrop-blur-sm sm:pt-[14vh]"
        @click.self="closeSearch">
        <div ref="searchDialogRef"
            class="w-full max-w-2xl overflow-hidden rounded-2xl border border-border/50 bg-background shadow-2xl">
            <form @submit.prevent="submitSearch">
                <div class="flex items-center gap-3 border-b border-border/50 px-4 py-4 sm:px-5">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2"
                        stroke="currentColor" class="size-5 shrink-0 text-foreground-muted">
                        <path stroke-linecap="round" stroke-linejoin="round"
                            d="m21 21-5.197-5.197m0 0A7.5 7.5 0 1 0 5.196 5.196a7.5 7.5 0 0 0 10.607 10.607Z" />
                    </svg>

                    <input ref="searchInputRef" v-model="searchQuery" type="search"
                        placeholder="Search anime, genres, studios..."
                        class="h-9 min-w-0 flex-1 bg-transparent text-sm text-white outline-none placeholder:text-foreground-muted"
                        autocomplete="off">

                    <button type="button" @click="closeSearch"
                        class="size-8 shrink-0 rounded-full text-foreground-muted transition-all duration-200 hover:bg-[#1B1D24] hover:text-white"
                        aria-label="Close search">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2"
                            stroke="currentColor" class="mx-auto size-4">
                            <path stroke-linecap="round" stroke-linejoin="round" d="m6 6 12 12M18 6 6 18" />
                        </svg>
                    </button>
                </div>

                <div class="p-4 sm:p-5">
                    <div class="mb-3 flex flex-wrap items-center justify-between gap-2">
                        <div>
                            <h3 class="text-sm font-semibold text-white">Search filters</h3>
                            <p class="mt-1 text-xs text-foreground-muted">
                                Choose which anime attributes to search through.
                            </p>
                        </div>

                        <span
                            class="rounded-full border border-border/50 bg-background-soft px-2.5 py-1 text-[0.6875rem] text-foreground-muted">
                            {{ selectedSearchFields.length }} / 20 selected
                        </span>
                    </div>

                    <div class="mb-3 flex items-center gap-2">
                        <button type="button" @click="selectAllSearchFields"
                            class="rounded-md px-2.5 py-1.5 text-[0.6875rem] font-semibold transition-all duration-200"
                            :class="allSearchFieldsSelected ? 'bg-white/10 text-white' : 'text-foreground-muted hover:bg-[#1B1D24] hover:text-white'">
                            Select all
                        </button>

                        <button type="button" @click="clearSearchFields"
                            class="rounded-md px-2.5 py-1.5 text-[0.6875rem] font-semibold text-foreground-muted transition-all duration-200 hover:bg-[#1B1D24] hover:text-white">
                            Clear
                        </button>
                    </div>

                    <div class="grid max-h-56 grid-cols-2 gap-2 overflow-y-auto pr-1 sm:grid-cols-3 md:grid-cols-4">
                        <button v-for="field in searchFields" :key="field.key" type="button"
                            @click="toggleSearchField(field.key)"
                            class="flex min-h-9 items-center gap-2 rounded-lg border px-2.5 py-2 text-left transition-all duration-200"
                            :class="selectedSearchFields.includes(field.key)
                                ? 'border-white/20 bg-white/10 text-white'
                                : 'border-border/40 bg-background-soft text-foreground-muted hover:border-border hover:bg-[#1B1D24] hover:text-white'">
                            <span class="flex size-3.5 shrink-0 items-center justify-center rounded border" :class="selectedSearchFields.includes(field.key)
                                ? 'border-white bg-white text-black'
                                : 'border-foreground-muted/50'">
                                <svg v-if="selectedSearchFields.includes(field.key)" viewBox="0 0 16 16" fill="none"
                                    class="size-2.5">
                                    <path d="m3 8 3.2 3.2L13 4.5" stroke="currentColor" stroke-width="2"
                                        stroke-linecap="round" stroke-linejoin="round" />
                                </svg>
                            </span>
                            <span class="truncate text-[0.6875rem] font-medium">{{ field.label }}</span>
                        </button>
                    </div>

                    <div
                        class="mt-5 flex flex-col-reverse items-stretch justify-between gap-3 border-t border-border/50 pt-4 sm:flex-row sm:items-center">
                        <p class="text-[0.6875rem] text-foreground-muted">
                            Press Enter to search
                        </p>

                        <button type="submit" :disabled="!searchQuery.trim() || selectedSearchFields.length === 0"
                            class="flex h-10 items-center justify-center gap-2 rounded-lg bg-white px-5 text-xs font-semibold text-black transition-all duration-200 hover:bg-white/85 disabled:cursor-not-allowed disabled:opacity-40">
                            <span>Search</span>
                            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2"
                                stroke="currentColor" class="size-3.5">
                                <path stroke-linecap="round" stroke-linejoin="round"
                                    d="M13.5 4.5 21 12m0 0-7.5 7.5M21 12H3" />
                            </svg>
                        </button>
                    </div>
                </div>
            </form>
        </div>
    </div>
</template>