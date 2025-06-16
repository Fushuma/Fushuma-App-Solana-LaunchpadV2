<template>
    <div class="min-h-screen flex flex-col bg-white dark:!bg-black transition-colors duration-300">
        <!-- Header with Navigation -->
        <header
            class="border-b border-gray-200 bg-white dark:border-gray-800 sticky top-0 z-50 py-4 dark:bg-black shadow-sm"
        >
            <div class="container mx-auto px-4 py-3">
                <div class="flex justify-between items-center">
                    <!-- Logo -->
                    <div class="flex items-center space-x-2">
                        <a
                            href="https://launchpad.fushuma.com/"
                            target="_blank"
                            class="text-2xl font-bold text-[#DA342E] dark:text-[#DA342E]  !hover:opacity-100"
                        >
                        
                            <img class="md:hidden h-8" :src="fushumaAvatar" />
                            <img
                                v-if="colorMode.value === 'dark'"
                                class="hidden md:block md:h-9 w-full"
                                :src="fushumaBlack"
                            />
                            <img
                                v-if="colorMode.value === 'light'"
                                class="hidden md:block md:h-9 w-full"
                                :src="fushumaWhite"
                            />
                        </a>
                        <nav class="hidden md:flex uppercase font-semibold space-x-6">
                            <!-- 🌐 New "Website" Link -->
                            <a
                                href="https://fushuma.com/"
                                target="_blank"
                                 class="py-2 hover:text-[#DA342E] dark:text-gray-300 dark:hover:text-[#DA342E] transition-colors"
                            >
                                Website
                            </a>
                            <a
                                target="_blank"
                                href="https://fushuma.com/blueprint-for-decentralization/"
                                class="py-2 hover:text-[#DA342E] dark:text-gray-300 dark:hover:text-[#DA342E] transition-colors"
                            >
                                Blueprint
                            </a>
                            <NuxtLink
                                target="_blank"
                                href="https://fushuma.com/about-fushuma/"
                                class="py-2 hover:text-[#DA342E] dark:text-gray-300 dark:hover:text-[#DA342E] transition-colors"
                            >
                                About
                            </NuxtLink>
                        </nav>
                    </div>

                    <!-- Center Navigation -->

                    <!-- Right Side Tools -->
                    <div class="flex items-center space-x-4">
                        <!-- Color Theme Toggle -->
<!--                        <button
                            class="p-2 rounded-full flex items-center cursor-pointer hover:brightness-125 dark:hover:bg-gray-800 transition-colors duration-300"
                            @click="toggleColorMode"
                        >
                            <Icon
                                v-if="$colorMode.value === 'dark'"
                                name="heroicons:sun"
                                class="text-gray-300 text-xl w-5 h-5"
                            />
                            <Icon v-else name="heroicons:moon" class="text-gray-700 text-xl w-5 h-5" />
                        </button>-->

                        <!-- Wallet Connect Button -->
                        <ConnectButton />
                    </div>
                </div>
            </div>
        </header>

        <!-- Main Content -->
        <main class="flex-grow bg-white dark:bg-black transition-colors">
            <slot />
        </main>

        <!-- Footer -->
        <footer class="bg-gray-100 dark:bg-[#0a0a0a] py-10 border-t border-gray-200 darxk:border-gray-800">
            <div class="container mx-auto px-4">
                <div class="grid grid-cols-1 md:grid-cols-5 gap-8">
                    <div class="md:col-span-3 space-y-4">
                        <div class="text-2xl font-bold text-[#DA342E] dark:text-[#DA342E]">
                            <a
                                href="https://launchpad.fushuma.com/"
                                target="_blank"
                                class="text-2xl font-bold text-[#DA342E] dark:text-[#DA342E]  !hover:opacity-100"
                            >
                                <img v-if="colorMode.value === 'dark'" class="h-10" :src="fushumaBlack" />
                                <img v-if="colorMode.value === 'light'" class="h-10" :src="fushumaWhite" />
                            </a>
                        </div>
                        <!-- Social Links -->
                        <div class="flex space-x-4 pt-2">
                            <a
                                target="_blank"
                                href="https://twitter.com/FushumaChain"
                                class="text-gray-500 hover:text-[#DA342E] dark:text-gray-400 dark:hover:text-[#DA342E] transition-colors flex items-center"
                            >
                                <Icon name="simple-icons:x" class="mr-1 w-4 h-4" /> 
                            </a>
                            <a
                                target="_blank"
                                href="https://t.me/FushumaChain"
                                class="text-gray-500 hover:text-[#DA342E] dark:text-gray-400 dark:hover:text-[#DA342E] transition-colors flex items-center"
                            >
                                <Icon name="mdi:telegram" class="mr-1 w-5 h-5" /> 
                            </a>
                            <a
                                target="_blank"
                                href="https://www.facebook.com/FushumaChain"
                                class="text-gray-500 hover:text-[#DA342E] dark:text-gray-400 dark:hover:text-[#DA342E] transition-colors flex items-center"
                            >
                                <Icon name="mdi:facebook" class="mr-1 w-5 h-5" />
                            </a>
                            <a
                                target="_blank"
                                href="https://reddit.com/r/Fushuma"
                                class="text-gray-500 hover:text-[#DA342E] dark:text-gray-400 dark:hover:text-[#DA342E] transition-colors flex items-center"
                            >
                                <Icon name="mdi:reddit" class="mr-1 w-5 h-5" />
                            </a>
                        </div>
                    </div>

                    <!-- Column 2: Links (1/5) -->
                    <div class="md:col-span-1">
                        <h3 class="font-semibold text-lg mb-4 text-gray-900 expletus dark:text-white">Links</h3>
                        <ul class="space-y-2">
                            <li>
                                <NuxtLink
                                    to="/fushuma-roadmap"
                                    class="text-gray-600 hover:text-[#DA342E] dark:text-gray-400 dark:hover:text-[#DA342E] transition-colors"
                                    >Fushuma Roadmap</NuxtLink
                                >
                            </li>
                            <li>
                                <a
                                    target="_blank"
                                    href="https://fushuma.com/fuma-tokenomics/"
                                    class="text-gray-600 hover:text-[#DA342E] dark:text-gray-400 dark:hover:text-[#DA342E] transition-colors"
                                    >Fushuma Tokenomics</a
                                >
                            </li>
                            <li>
                                <a
                                    target="_blank"
                                    href="https://fushuma.com/dojo/"
                                    class="text-gray-600 hover:text-[#DA342E] dark:text-gray-400 dark:hover:text-[#DA342E] transition-colors"
                                    >Blog
                                </a>
                            </li>
                        </ul>
                    </div>

                    <!-- Column 3: Technical Partners (1/5) -->
                    <div class="md:col-span-1">
                        <h3 class="font-semibold text-lg mb-4 text-gray-900 expletus dark:text-white">
                            Technical Partners
                        </h3>
                        <ul class="space-y-2">
                            <li>
                                <a
                                    href="https://www.zeeve.io/"
                                    target="_blank"
                                    class="hover:text-[#DA342E] dark:text-gray-400 dark:hover:text-[#DA342E] transition-colors"
                                    >Zeeve</a
                                >
                            </li>
                        </ul>
                    </div>
                </div>

                <!-- Copyright -->
                <div
                    class="border-t border-gray-200 dark:border-gray-800 mt-8 pt-6 text-center text-gray-500 dark:text-gray-400"
                >
                    <p>{{ new Date().getFullYear() }} Fushuma. All rights reserved.</p>
                </div>
            </div>
        </footer>
    </div>
</template>

<script setup>
    import fushumaBlack from '~/assets/img/fushuma-black.webp';
    import fushumaWhite from '~/assets/img/launchpad.webp';
    import fushumaAvatar from '~/assets/img/fushuma-avatar.png';
    const colorMode = useColorMode();

    function toggleColorMode() {
        colorMode.preference = colorMode.value === 'dark' ? 'light' : 'dark';
    }
</script>

<style>
    /* Extra styles for layout components */
    .container {
        max-width: 1280px;
    }

    /* Improved transitions */
    .transition-colors {
        transition-property: background-color, border-color, color, fill, stroke;
        transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
        transition-duration: 300ms;
    }
</style>
