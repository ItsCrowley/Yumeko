<script setup lang="ts">
type AnimeItem = {
    slug: string
    title: string
    image: string
    alt: string
    year: number
    rating: string
    director: string
    runtime: number
    genres: string[]
    description: string
}

const items: AnimeItem[] = [
    {
        slug: 'castle-in-the-sky',
        title: 'Castle in the Sky',
        image: '/files/images/castle-in-the-sky.webp',
        alt: 'Castle in the Sky',
        year: 1986,
        rating: '8.0',
        director: 'Hayao Miyazaki',
        runtime: 124,
        genres: ['Adventure', 'Fantasy', 'Family'],
        description: 'A young mining boy meets a mysterious girl carrying a magical crystal. Together, they embark on an unforgettable adventure to find Laputa, a legendary castle floating high above the clouds, while escaping ruthless pirates and military forces.'
    },
    {
        slug: 'from-up-on-poppy-hill',
        title: 'From Up on Poppy Hill',
        image: '/files/images/from-up-on-poppy-hill.webp',
        alt: 'From Up on Poppy Hill',
        year: 2011,
        rating: '7.4',
        director: 'Gorō Miyazaki',
        runtime: 91,
        genres: ['Drama', 'Romance', 'Slice of Life'],
        description: 'Set in 1960s Yokohama, a young girl named Umi and her fellow students fight to save their beloved old school clubhouse from demolition ahead of the Tokyo Olympics. Along the way, friendship and unexpected feelings begin to unfold.'
    },
    {
        slug: 'howls-moving-castle',
        title: "Howl's Moving Castle",
        image: '/files/images/howls-moving-castle.webp',
        alt: "Howl's Moving Castle",
        year: 2004,
        rating: '8.2',
        director: 'Hayao Miyazaki',
        runtime: 119,
        genres: ['Fantasy', 'Adventure', 'Romance'],
        description: 'After being cursed by a mysterious witch and transformed into an old woman, young Sophie seeks help from the mysterious wizard Howl. Inside his extraordinary moving castle, she discovers a world filled with magic, secrets and unexpected adventures.'
    },
    {
        slug: 'my-neighbor-totoro',
        title: 'My Neighbor Totoro',
        image: '/files/images/my-neighbor-totoro.webp',
        alt: 'My Neighbor Totoro',
        year: 1988,
        rating: '8.1',
        director: 'Hayao Miyazaki',
        runtime: 86,
        genres: ['Fantasy', 'Family', 'Adventure'],
        description: 'Two young sisters move to the countryside with their father while their mother recovers in a hospital. In their new home, they encounter magical forest spirits, including the gentle and mysterious creature known as Totoro.'
    },
    {
        slug: 'ponyo',
        title: 'Ponyo',
        image: '/files/images/ponyo.webp',
        alt: 'Ponyo',
        year: 2008,
        rating: '7.6',
        director: 'Hayao Miyazaki',
        runtime: 101,
        genres: ['Fantasy', 'Adventure', 'Family'],
        description: 'A young goldfish princess named Ponyo escapes from the ocean and befriends a five-year-old boy named Sōsuke. As their friendship grows, Ponyo dreams of becoming human, setting off a magical adventure that changes the balance of the natural world.'
    },
    {
        slug: 'princess-mononoke',
        title: 'Princess Mononoke',
        image: '/files/images/princess-mononoke.webp',
        alt: 'Princess Mononoke',
        year: 1997,
        rating: '8.3',
        director: 'Hayao Miyazaki',
        runtime: 133,
        genres: ['Adventure', 'Fantasy', 'Drama'],
        description: 'After being cursed by a deadly demon, young warrior Ashitaka travels west in search of a cure. There, he becomes caught between the forces of industrial civilization and the ancient spirits of the forest, including a fierce girl raised by wolves.'
    },
    {
        slug: 'spirited-away',
        title: 'Spirited Away',
        image: '/files/images/spirited-away.webp',
        alt: 'Spirited Away',
        year: 2001,
        rating: '8.6',
        director: 'Hayao Miyazaki',
        runtime: 124,
        genres: ['Fantasy', 'Adventure', 'Coming-of-Age'],
        description: 'When ten-year-old Chihiro and her parents stumble into a mysterious abandoned world, her parents are transformed into pigs. Trapped in a supernatural realm inhabited by spirits and strange creatures, Chihiro must find the courage to save her family and return home.'
    },
    {
        slug: 'the-wind-rises',
        title: 'The Wind Rises',
        image: '/files/images/the-wind-rises.webp',
        alt: 'The Wind Rises',
        year: 2013,
        rating: '7.8',
        director: 'Hayao Miyazaki',
        runtime: 126,
        genres: ['Drama', 'Romance', 'Biography'],
        description: 'Inspired by the life of Japanese aircraft designer Jiro Horikoshi, this story follows a young man whose lifelong dream is to create beautiful airplanes. As he pursues his ambitions through a turbulent era, he discovers love, loss and the complicated reality of his dreams.'
    },
    {
        slug: 'weathering-with-you',
        title: 'Weathering with You',
        image: '/files/images/weathering-with-you.webp',
        alt: 'Weathering with You',
        year: 2019,
        rating: '7.5',
        director: 'Makoto Shinkai',
        runtime: 112,
        genres: ['Fantasy', 'Romance', 'Drama'],
        description: 'A runaway high school boy arrives in Tokyo and meets Hina, a mysterious girl with the extraordinary ability to control the weather. As their lives become intertwined, they face an impossible choice that could change their world forever.'
    },
    {
        slug: 'when-marnie-was-there',
        title: 'When Marnie Was There',
        image: '/files/images/when-marnie-was-there.webp',
        alt: 'When Marnie Was There',
        year: 2014,
        rating: '7.6',
        director: 'Hiromasa Yonebayashi',
        runtime: 103,
        genres: ['Drama', 'Mystery', 'Family'],
        description: 'Sent to spend the summer in a quiet seaside town, a lonely young girl named Anna discovers an abandoned mansion across the marshes. There, she meets a mysterious girl named Marnie, beginning a special friendship filled with memories, secrets and unexpected revelations.'
    },
    {
        slug: 'your-name',
        title: 'Your Name',
        image: '/files/images/your-name.webp',
        alt: 'Your Name',
        year: 2016,
        rating: '8.4',
        director: 'Makoto Shinkai',
        runtime: 106,
        genres: ['Romance', 'Fantasy', 'Drama'],
        description: 'Two teenagers, Mitsuha living in a quiet mountain town and Taki in the heart of Tokyo, mysteriously begin switching bodies in their dreams. As they struggle to understand their strange connection, an extraordinary event brings their intertwined destinies into focus.'
    }
]

const heroItems = items.map(item => ({
    ...item,
    image: item.image.replace(/\.webp$/, '-hero.webp')
}))

const activeIndex = ref(0)
const isAutoplayPlaying = ref(true)
const viewportWidth = ref(1280)

const carouselCardWidth = computed(() => {
    if (viewportWidth.value < 480) return 220
    if (viewportWidth.value < 1280) return 280
    return 360
})

const carouselCardHeight = computed(() =>
    Math.round(carouselCardWidth.value * 1.3889)
)

const carouselSpread = computed(() => {
    if (viewportWidth.value < 480) return 30
    if (viewportWidth.value < 1280) return 45
    return 65
})

const carouselDepth = computed(() => {
    if (viewportWidth.value < 480) return 80
    if (viewportWidth.value < 1280) return 120
    return 170
})

const activeItem = computed(
    () => items[activeIndex.value] ?? items[0]!
)

const activeHero = computed(
    () => heroItems[activeIndex.value] ?? heroItems[0]!
)

const featuredItems = computed(() => [
    items[6]!,
    items[9]!,
    items[2]!,
    items[5]!,
    items[0]!,
    items[8]!
])

const topRatedItems = computed(() =>
    [...items]
        .sort((a, b) => Number(b.rating) - Number(a.rating))
        .slice(0, 6)
)

const ghibliItems = computed(() =>
    items.filter(item => !['your-name', 'weathering-with-you'].includes(item.slug))
)

const genres = computed(() =>
    [
        'Adventure',
        'Fantasy',
        'Drama',
        'Romance',
        'Family',
        'Mystery',
        'Slice of Life',
        'Coming-of-Age'
    ].map(title => ({
        title,
        slug: title.toLowerCase().replaceAll(' ', '-'),
        count: items.filter(item => item.genres.includes(title)).length
    }))
)

const handleCarouselChange = (index: number) => {
    activeIndex.value = index
}

const toggleAutoplay = () => {
    isAutoplayPlaying.value = !isAutoplayPlaying.value
}

const preloadHero = (index: number) => {
    const source = heroItems[index]?.image

    if (!source) return

    const image = new Image()
    image.src = source
}

const formatRuntime = (runtime: number) => {
    const hours = Math.floor(runtime / 60)
    const minutes = runtime % 60

    return `${hours}h ${minutes.toString().padStart(2, '0')}m`
}

const updateViewportWidth = () => {
    viewportWidth.value = window.innerWidth
}

onMounted(() => {
    updateViewportWidth()
    window.addEventListener('resize', updateViewportWidth)
    preloadHero((activeIndex.value + 1) % heroItems.length)
})

onBeforeUnmount(() => {
    window.removeEventListener('resize', updateViewportWidth)
})

watch(activeIndex, index => {
    preloadHero((index + 1) % heroItems.length)
})
</script>

<template>
    <section class="relative h-[80vh] overflow-hidden pt-26 pb-18 xl:pb-0 xl:pt-18">
        <div class="pointer-events-none absolute inset-x-0 -z-10 top-0 bottom-0 hero opacity-20! lg:opacity-50!">
            <Transition name="hero-fade">
                <img :key="activeHero.image" :src="activeHero.image" :alt="activeHero.alt" fetchpriority="high"
                    decoding="async" class="absolute inset-0 size-full object-cover object-top" />
            </Transition>
        </div>

        <div class="container relative z-0 mt-9 sm:mt-12 lg:mt-14">
            <div class="flex w-full items-center justify-between">
                <div class="flex min-w-0 flex-col lg:max-w-xl">
                    <Transition name="content-fade" mode="out-in">
                        <div :key="activeItem.title">
                            <h1 class="text-2xl font-bold text-white sm:text-4xl lg:text-[2rem] xl:text-[2.5rem]">
                                {{ activeItem.title }}
                            </h1>

                            <div class="mt-4 flex flex-wrap items-center gap-y-2">
                                <span class="text-sm font-semibold text-foreground-muted">
                                    {{ activeItem.year }}
                                </span>

                                <div class="mx-3 h-4 w-px bg-border-strong"></div>

                                <div class="flex items-center">
                                    <Imdb class="mr-2 h-5 w-10 shrink-0 text-yellow-500" />

                                    <div class="flex items-center gap-px text-sm font-semibold text-foreground-muted">
                                        <span class="text-base text-white">
                                            {{ activeItem.rating }}
                                        </span>
                                        <span>/</span>
                                        <span>10</span>
                                    </div>
                                </div>

                                <div class="mx-3 h-4 w-px bg-border-strong"></div>

                                <span class="text-sm font-semibold text-foreground-muted">
                                    {{ activeItem.runtime }} min
                                </span>
                            </div>

                            <div
                                class="mt-5 flex flex-wrap items-center gap-2 text-xs font-medium text-foreground-muted">
                                <template v-for="(genre, index) in activeItem.genres" :key="genre">
                                    <span>{{ genre }}</span>
                                    <span v-if="index !== activeItem.genres.length - 1">-</span>
                                </template>
                            </div>

                            <p
                                class="mt-6 max-w-lg text-xs font-medium leading-5 text-foreground-secondary sm:mt-8 xl:max-w-xl xl:text-sm xl:leading-7">
                                {{ activeItem.description }}
                            </p>

                            <div
                                class="mt-5 flex flex-wrap items-center gap-2 text-xs font-medium text-foreground-muted">
                                <span>Directed by</span>
                                <span class="text-white">
                                    {{ activeItem.director }}
                                </span>
                            </div>

                            <div class="mt-7 flex gap-4 sm:mt-10">
                                <NuxtLink :to="`/anime/${activeItem.slug}`"
                                    class="flex h-8 items-center gap-2 whitespace-nowrap rounded-full bg-primary px-3 text-[0.8125rem] font-semibold text-white transition-all duration-300 hover:bg-primary-hover active:bg-primary-active">
                                    <span>Watch Now</span>

                                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
                                        stroke-width="3" stroke="currentColor" class="mt-px size-3">
                                        <path stroke-linecap="round" stroke-linejoin="round"
                                            d="M13.5 4.5 21 12m0 0-7.5 7.5M21 12H3" />
                                    </svg>
                                </NuxtLink>
                            </div>
                        </div>
                    </Transition>
                </div>

                <div class="z-10 mx-auto w-full max-w-90 shrink-0 sm:max-w-105 xl:mx-0 xl:w-90 hidden lg:block scale-70 -mr-10 xl:scale-100 xl:mr-0">
                    <div class="mx-auto flex h-80 w-full items-center justify-center sm:h-97.5 xl:h-125">
                        <DepthCarousel :items="items" :card-width="carouselCardWidth" :card-height="carouselCardHeight"
                            :spread="carouselSpread" :depth="carouselDepth" :visible-cards="3" :tilt="18"
                            tilt-direction="right" :perspective="1400" :falloff="0.18" :blur="5"
                            :autoplay="isAutoplayPlaying" :loop="true" :radius="18" tint="#05060a" :duration="700"
                            ease="power3.out" :autoplay-delay="5000" :show-indicators="true"
                            :on-change="handleCarouselChange" />
                    </div>
                </div>
            </div>
        </div>
    </section>

    <section class="container relative z-10 mt-6 sm:mt-10">
        <div class="flex items-end justify-between gap-4">
            <div class="min-w-0">
                <h2 class="text-xl font-bold tracking-tight text-white sm:text-2xl">
                    A good place to start
                </h2>
                <p class="mt-2 text-xs text-foreground-muted">
                    Stories worth spending your time on.
                </p>
            </div>

            <NuxtLink to="/anime"
                class="group hidden shrink-0 items-center gap-2 pb-1 text-xs font-semibold text-foreground-muted transition-colors hover:text-white sm:flex">
                Explore collection
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2"
                    stroke="currentColor" class="size-3.5">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M13.5 4.5 21 12m0 0-7.5 7.5M21 12H3" />
                </svg>
            </NuxtLink>
        </div>

        <div
            class="mt-7 grid grid-cols-2 gap-x-3 gap-y-7 min-[480px]:gap-x-4 sm:grid-cols-3 sm:gap-x-3 sm:gap-y-8 lg:grid-cols-6">
            <NuxtLink v-for="item in featuredItems" :key="item.slug" :to="`/anime/${item.slug}`" class="group min-w-0">
                <div class="relative aspect-3/4 overflow-hidden rounded-xl border border-white/6 bg-surface">
                    <img :src="item.image" :alt="item.alt" loading="lazy" decoding="async"
                        class="size-full object-cover transition-transform duration-500 ease-out group-hover:scale-[1.045]" />

                    <div
                        class="absolute left-2 top-2 flex items-center gap-1.5 rounded-md border border-white/10 bg-black/45 px-1.5 py-1 backdrop-blur-md sm:left-3 sm:top-3 sm:px-2">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="currentColor" viewBox="0 0 24 24"
                            class="size-3 text-yellow-400">
                            <path
                                d="m12 2 3.09 6.26L22 9.27l-5 4.87L18.18 21 12 17.77 5.82 21 7 14.14 2 9.27l6.91-1.01L12 2Z" />
                        </svg>
                        <span class="text-[0.6875rem] font-bold text-white">{{ item.rating }}</span>
                    </div>

                    <div class="absolute inset-x-0 bottom-0 p-2 sm:p-3">
                        <span
                            class="rounded-full bg-black/50 px-2 py-0.75 text-[0.6875rem] font-medium text-white/65 backdrop-blur-sm">
                            {{ item.year }}
                        </span>
                    </div>
                </div>

                <div class="mt-3">
                    <h3
                        class="truncate text-[0.8125rem] font-semibold text-foreground transition-colors group-hover:text-white">
                        {{ item.title }}
                    </h3>
                    <p class="mt-1.5 truncate text-[0.6875rem] text-foreground-muted">
                        {{ item.genres.slice(0, 2).join(' · ') }}
                    </p>
                </div>
            </NuxtLink>
        </div>
    </section>

    <section class="container mt-20 sm:mt-28">
        <div class="relative isolate overflow-hidden rounded-2xl border border-border/60 bg-background-soft">
            <div class="absolute inset-0 -z-10">
                <img src="/files/images/castle-in-the-sky.webp" alt="" loading="lazy"
                    class="size-full object-cover object-center opacity-25 hidden lg:block" />
                <div class="absolute inset-0 bg-linear-to-r from-background via-background/90 to-background/50"></div>
            </div>

            <div
                class="grid min-h-90 items-center gap-8 p-5 sm:gap-10 sm:p-10 lg:grid-cols-[1fr_0.8fr] lg:px-16 lg:py-14">
                <div class="max-w-xl">
                    <h2 class="max-w-lg text-2xl font-bold leading-tight tracking-tight text-white sm:text-4xl">
                        Some stories stay with you forever.
                    </h2>

                    <p class="mt-5 max-w-md text-sm leading-7 text-foreground-secondary">
                        Discover imaginative worlds, unforgettable characters and beautifully crafted stories from
                        the masters of Japanese animation.
                    </p>

                    <NuxtLink to="/anime"
                        class="mt-7 inline-flex h-9 items-center gap-3 rounded-full bg-white px-5 text-xs font-semibold text-background transition-all duration-200 hover:bg-foreground-secondary sm:mt-8">
                        Discover the collection
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2"
                            stroke="currentColor" class="size-3.5">
                            <path stroke-linecap="round" stroke-linejoin="round"
                                d="M13.5 4.5 21 12m0 0-7.5 7.5M21 12H3" />
                        </svg>
                    </NuxtLink>
                </div>

                <div class="hidden items-center justify-center lg:flex">
                    <div
                        class="relative w-full max-w-77.5 rotate-3 overflow-hidden rounded-xl border border-white/10 shadow-2xl transition-transform duration-500 hover:rotate-0">
                        <img src="/files/images/the-wind-rises.webp" alt="The Wind Rises" loading="lazy"
                            class="aspect-3/4 w-full object-cover" />
                    </div>
                    <div
                        class="relative -ml-12 mt-28 w-full max-w-47.5 -rotate-6 overflow-hidden rounded-xl border border-white/10 shadow-2xl transition-transform duration-500 hover:rotate-0">
                        <img src="/files/images/howls-moving-castle.webp" alt="Howl's Moving Castle" loading="lazy"
                            class="aspect-3/4 w-full object-cover" />
                    </div>
                </div>
            </div>
        </div>
    </section>

    <section class="container mt-20 sm:mt-28">
        <div class="flex items-end justify-between gap-4">
            <div class="min-w-0">
                <h2 class="text-xl font-bold tracking-tight text-white sm:text-2xl">
                    The world of Studio Ghibli
                </h2>
                <p class="mt-2 text-xs text-foreground-muted">
                    A collection of extraordinary animated films.
                </p>
            </div>

            <NuxtLink to="/anime?studio=ghibli"
                class="group flex shrink-0 items-center gap-2 pb-1 text-xs font-semibold text-foreground-muted transition-colors hover:text-white">
                View all
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2"
                    stroke="currentColor" class="size-3.5">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M13.5 4.5 21 12m0 0-7.5 7.5M21 12H3" />
                </svg>
            </NuxtLink>
        </div>

        <div class="ghibli-scroll mt-7 flex gap-4 overflow-x-auto pb-4 sm:gap-5">
            <NuxtLink v-for="item in ghibliItems" :key="item.slug" :to="`/anime/${item.slug}`"
                class="group w-40 shrink-0 min-[480px]:w-47.5 sm:w-52.5">
                <div class="relative aspect-3/4 overflow-hidden rounded-xl border border-white/6 bg-surface">
                    <img :src="item.image" :alt="item.alt" loading="lazy" decoding="async"
                        class="size-full object-cover transition-transform duration-500 ease-out group-hover:scale-[1.05]" />

                    <div
                        class="absolute left-2 top-2 flex items-center gap-1.5 rounded-md border border-white/10 bg-black/45 px-1.5 py-1 backdrop-blur-md sm:left-3 sm:top-3 sm:px-2">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="currentColor" viewBox="0 0 24 24"
                            class="size-3 text-yellow-400">
                            <path
                                d="m12 2 3.09 6.26L22 9.27l-5 4.87L18.18 21 12 17.77 5.82 21 7 14.14 2 9.27l6.91-1.01L12 2Z" />
                        </svg>
                        <span class="text-[0.6875rem] font-bold text-white">{{ item.rating }}</span>
                    </div>

                    <div class="absolute inset-x-0 bottom-0 p-2 sm:p-3">
                        <span class="text-[0.6875rem] font-medium text-white/65">{{ item.year }}</span>
                    </div>
                </div>

                <div class="mt-3">
                    <h3
                        class="truncate text-[0.8125rem] font-semibold text-foreground transition-colors group-hover:text-white">
                        {{ item.title }}
                    </h3>
                    <p class="mt-1.5 truncate text-[0.6875rem] text-foreground-muted">
                        {{ item.director }}
                    </p>
                </div>
            </NuxtLink>
        </div>
    </section>

    <section class="container mt-20 sm:mt-28">
        <div>
            <h2 class="text-xl font-bold tracking-tight text-white sm:text-2xl">
                Explore by genre
            </h2>
            <p class="mt-2 text-xs text-foreground-muted">
                Every mood has a story.
            </p>
        </div>

        <div class="mt-7 grid grid-cols-2 gap-3 sm:grid-cols-4">
            <NuxtLink v-for="(genre, index) in genres" :key="genre.slug" :to="`/genre/${genre.slug}`"
                class="genre-card group relative flex min-h-32.5 flex-col justify-between overflow-hidden rounded-xl border border-border/60 bg-background-soft p-4 transition-all duration-300 hover:border-border-strong hover:bg-surface sm:p-5">
                <div class="relative z-10 flex items-center justify-between">
                    <span class="text-[0.6875rem] font-medium text-foreground-muted">
                        0{{ index + 1 }}
                    </span>

                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.7"
                        stroke="currentColor" class="size-4 text-foreground-disabled group-hover:text-white">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M13.5 4.5 21 12m0 0-7.5 7.5M21 12H3" />
                    </svg>
                </div>

                <div class="relative z-10">
                    <h3 class="text-sm font-semibold text-foreground transition-colors group-hover:text-white">
                        {{ genre.title }}
                    </h3>
                    <p class="mt-1.5 text-[0.6875rem] text-foreground-muted">
                        {{ genre.count }} titles
                    </p>
                </div>
            </NuxtLink>
        </div>
    </section>

    <section class="container mb-20 mt-20 sm:mb-32 sm:mt-28">
        <div class="flex items-end justify-between gap-4">
            <div class="min-w-0">
                <h2 class="text-xl font-bold tracking-tight text-white sm:text-2xl">
                    Highest rated
                </h2>
                <p class="mt-2 text-xs text-foreground-muted">
                    Stories loved by anime fans.
                </p>
            </div>

            <NuxtLink to="/anime?sort=rating"
                class="group flex shrink-0 items-center gap-2 pb-1 text-xs font-semibold text-foreground-muted transition-colors hover:text-white">
                All ratings
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2"
                    stroke="currentColor" class="size-3.5">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M13.5 4.5 21 12m0 0-7.5 7.5M21 12H3" />
                </svg>
            </NuxtLink>
        </div>

        <div
            class="mt-7 grid grid-cols-2 gap-x-3 gap-y-7 min-[480px]:gap-x-4 sm:grid-cols-3 sm:gap-x-5 sm:gap-y-8 lg:grid-cols-6">
            <NuxtLink v-for="item in topRatedItems" :key="item.slug" :to="`/anime/${item.slug}`" class="group min-w-0">
                <div class="relative aspect-3/4 overflow-hidden rounded-xl border border-white/6 bg-surface">
                    <img :src="item.image" :alt="item.alt" loading="lazy" decoding="async"
                        class="size-full object-cover transition-transform duration-500 ease-out group-hover:scale-[1.045]" />

                    <div
                        class="absolute left-2 top-2 flex items-center gap-1.5 rounded-md border border-white/10 bg-black/45 px-1.5 py-1 backdrop-blur-md sm:left-3 sm:top-3 sm:px-2">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="currentColor" viewBox="0 0 24 24"
                            class="size-3 text-yellow-400">
                            <path
                                d="m12 2 3.09 6.26L22 9.27l-5 4.87L18.18 21 12 17.77 5.82 21 7 14.14 2 9.27l6.91-1.01L12 2Z" />
                        </svg>
                        <span class="text-[0.6875rem] font-bold text-white">{{ item.rating }}</span>
                    </div>

                    <div class="absolute inset-x-0 bottom-0 p-2 sm:p-3">
                        <span class="text-[0.6875rem] font-medium text-white/65">{{ item.year }}</span>
                    </div>
                </div>

                <div class="mt-3">
                    <h3
                        class="truncate text-[0.8125rem] font-semibold text-foreground transition-colors group-hover:text-white">
                        {{ item.title }}
                    </h3>
                    <p class="mt-1.5 truncate text-[0.6875rem] text-foreground-muted">
                        {{ formatRuntime(item.runtime) }} · {{ item.genres[0] }}
                    </p>
                </div>
            </NuxtLink>
        </div>
    </section>
</template>

<style scoped>
.hero-fade-enter-active,
.hero-fade-leave-active {
    transition: opacity 1.5s cubic-bezier(0.22, 1, 0.36, 1);
}

.hero-fade-enter-from,
.hero-fade-leave-to {
    opacity: 0;
}

.content-fade-enter-active,
.content-fade-leave-active {
    transition: opacity 300ms ease, transform 300ms ease;
}

.content-fade-enter-from {
    opacity: 0;
    transform: translateY(8px);
}

.content-fade-leave-to {
    opacity: 0;
    transform: translateY(-8px);
}

.hero {
    -webkit-mask-image:
        radial-gradient(ellipse 140% 140% at 100% 0%, #000 5%, transparent 100%),
        linear-gradient(to bottom, #000 0%, #000 45%, rgba(0, 0, 0, 0.9) 60%, rgba(0, 0, 0, 0.5) 75%, transparent 100%);

    -webkit-mask-composite: source-in;

    mask-image:
        radial-gradient(ellipse 140% 140% at 100% 0%, #000 5%, transparent 100%),
        linear-gradient(to bottom, #000 0%, #000 45%, rgba(0, 0, 0, 0.9) 60%, rgba(0, 0, 0, 0.5) 75%, transparent 100%);

    mask-composite: intersect;
}

.ghibli-scroll {
    scrollbar-width: none;
    -ms-overflow-style: none;
}

.ghibli-scroll::-webkit-scrollbar {
    display: none;
}

@media (prefers-reduced-motion: reduce) {

    .hero-fade-enter-active,
    .hero-fade-leave-active,
    .content-fade-enter-active,
    .content-fade-leave-active,
    .genre-card,
    .ghibli-scroll {
        transition: none;
    }
}
</style>