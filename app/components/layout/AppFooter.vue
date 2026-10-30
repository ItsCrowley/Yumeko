<template>
    <footer class="border-t border-border/50 bg-background-soft/50">
        <div class="container">
            <div class="flex flex-col gap-10 py-12 sm:flex-row sm:items-start sm:justify-between">
                <div class="max-w-xs">
                    <NuxtLink to="/" class="inline-flex items-center gap-2">
                        <Logo class="size-7 text-white" />
                        <span class="text-base font-semibold tracking-tight text-white">Yumeko</span>
                    </NuxtLink>
                    <p class="mt-4 text-xs leading-6 text-foreground-muted">
                        A place for stories, imagination and the art of Japanese animation.
                    </p>
                </div>

                <div class="grid grid-cols-2 gap-x-16 gap-y-8 sm:gap-x-20">
                    <div>
                        <h3 class="text-xs font-semibold text-white">Discover</h3>
                        <div class="mt-4 flex flex-col gap-3">
                            <NuxtLink to="/anime"
                                class="text-xs text-foreground-muted transition-colors hover:text-white">
                                All anime</NuxtLink>
                            <NuxtLink to="/genres"
                                class="text-xs text-foreground-muted transition-colors hover:text-white">
                                Genres</NuxtLink>
                            <NuxtLink to="/new-season"
                                class="text-xs text-foreground-muted transition-colors hover:text-white">New season
                            </NuxtLink>
                            <NuxtLink to="/now"
                                class="text-xs text-foreground-muted transition-colors hover:text-white">
                                Currently airing</NuxtLink>
                        </div>
                    </div>

                    <div>
                        <h3 class="text-xs font-semibold text-white">Yumeko</h3>
                        <div class="mt-4 flex flex-col gap-3">
                            <NuxtLink to="/about"
                                class="text-xs text-foreground-muted transition-colors hover:text-white">
                                About us</NuxtLink>
                            <NuxtLink to="/contact"
                                class="text-xs text-foreground-muted transition-colors hover:text-white">Contact
                            </NuxtLink>
                            <NuxtLink to="/privacy"
                                class="text-xs text-foreground-muted transition-colors hover:text-white">Privacy
                                policy
                            </NuxtLink>
                        </div>
                    </div>
                </div>
            </div>

            <div
                class="flex flex-col justify-center md:items-center gap-y-2 border-t border-border/50 py-5 text-[0.6875rem] text-foreground-muted font-medium">
                <span class="sm:text-sm flex items-center gap-3">
                    <span>UI design and development by</span> 
                    <a class="flex items-center gap-1 group" href="https://github.com/ItsCrowley" target="_blank">
                        <img class="size-6 rounded-full" src="/files/images/kian.jpg" />
                        <span class="text-white group-hover:underline">ItsCrowley</span>
                    </a>
                </span>
                <span>
                    For the source code, visit the <a href="https://github.com/ItsCrowley/Yumeko" class="text-foreground-secondary hover:text-white hover:underline" target="_blank">
                        Yumko repository
                    </a>
                </span>
            </div>

            <div
                class="flex justify-center items-center border-t border-border/50 py-5 text-[0.6875rem] text-foreground-muted font-medium">
                <span>
                    Please respect the original author's work and comply with all applicable copyright and licensing terms.
                </span>
            </div>
        </div>
    </footer>
</template>