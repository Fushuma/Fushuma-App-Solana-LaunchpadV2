<template>
    <div>
        <div
            class="flex min-h-screen bg-gray-50 !dark:!bg-black/70 relative md:min-h-[calc(100vh-440px)] pt-[40px] lg:pt-[px] flex-col items-center"
        >
            <div
                v-if="!data?.fetched && hasConnectedWallet"
                class="absolute top-1/2 w-fit left-1/2 -translate-x-1/2 -translate-y-1/2"
            >
                <div class="md:flex items-center justify-center w-fit md:space-x-3">
                    <AppSpinner :size="4"></AppSpinner>
                </div>
            </div>

            <div v-else class="container mx-auto px-4 grid-cols-1 md:grid-cols-9 w-full">
                <div class="md:col-start-2 xl:col-start-3 col-span-7 xl:col-span-5 h-full mx-auto gap-6 mb-3 w-full">
                    <div class="flex items-center flex-col">
                        <div class="w-3/4 lg:w-1/4">
                            <video
                                playsinline="true"
                                width="100%"
                                style="object-fit: cover"
                                autoplay="true"
                                muted="true"
                                preload="auto"
                            >
                                <source
                                    src="https://fushuma.com/wp-content/uploads/sites/2/2024/06/Fushuma-Final-Animation-for-web-alpha.webm"
                                    type="video/mp4"
                                />
                                Sorry, your browser doesn't support embedded videos.
                            </video>
                        </div>
                        <ClientOnly>
                            <h1
                                ref="launchpadText"
                                data-aos="fade-down"
                                class="text-primary-500 uppercase expletus text-2xl lg:text-3xl tracking-tighter text-center"
                                style="opacity: 0"
                            >
                                launchpad
                            </h1>
                        </ClientOnly>

                        <AppStateCard error class="mt-6 text-center" v-if="data.fetched && data.error"
                            >Failed to load data</AppStateCard
                        >

                        <AppStateCard class="mt-5" v-else-if="!data?.data?.length && data.fetched">
                            <p class="text-sm text-left text-black/60 font-normal tracking-tight max-w-5xl">
                                No launchpad are published right now.
                            </p>
                        </AppStateCard>

                        <div class="mt-12">
                            <NuxtLink
                                to="/create-ico"
                                class="inline-block bg-[#da342e] px-6 py-3 rounded-lg text-white font-semibold text-center transition-colors duration-200 hover:bg-[#c12e29] focus:outline-none focus:ring-2 focus:ring-[#da342e] focus:ring-offset-2"
                                aria-label="Navigate to the Create ICO page"
                            >
                                <span class="text-white">Create New ICO</span>
                            </NuxtLink>
                        </div>

                        <div
                            v-if="data.fetched && data.data?.length && !data.error"
                            class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 my-12"
                        >
                            <AppLaunchpadCard
                                v-for="(item, index) in data.data"
                                :launchpad="launchpads.find((el) => el.key === item.key)"
                                :key="index"
                                :data="item"
                            />
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
    import Web3 from 'web3';
    import { DataWrapper } from '@/types/DataWrapper';
    import { SolanaIcoLaunchpad } from '@/js/ico';
    import { useWallet } from 'solana-wallets-vue';
    import { gsap } from 'gsap';
    import launchpads from '@/assets/launchpads.json';
    import { fetchAllICOs } from '~/js/ico-evm';

    const { publicKey } = useWallet();
    const launchpadText = ref<HTMLElement | null>(null);

    const hasConnectedWallet = computed(() => {
        return publicKey.value;
    });

    import type { IIcoInfoWithKey } from '@/types/Ico';

    const data = ref(new DataWrapper<IIcoInfoWithKey[]>());

    onMounted(async () => {
        await fetchData();

        setTimeout(() => {
            if (launchpadText.value) {
                gsap.to(launchpadText.value, {
                    opacity: 1,
                    delay: 1,
                    y: 0,
                    duration: 1,
                    ease: 'power1.in',
                });
            }
        }, 3000);
    });

    const fetchData = async () => {
        try {
            const ignoredIcos = ['GhtTs6TvVJTUYUAeAP2vFMX26y8ZRY6eEsUdmzdNuLuJ', 'J84vLC7N9AF2WD2JLZJZMhcdjzSqtCu3wyt5NeiqACLE'];

            const solanaIcos = await SolanaIcoLaunchpad.getAllIco({});
            const filteredIcos = solanaIcos.filter((ico: IIcoInfoWithKey) => !ignoredIcos.includes(ico.key));

            filteredIcos.forEach((pitch: any) => {
                pitch.data.startDate *= 1000;
                pitch.data.endDate *= 1000;
            });

            const evmIcos = await fetchAllICOs();
            const combined = [...evmIcos, ...solanaIcos];
            // const combined = evmIcos;

            combined.sort((a, b) => (a.data.startDate > b.data.startDate ? -1 : 1));

            data.value.setData(combined);
        } catch (e: any) {
            data.value.setError();
        }
    };

    watch(publicKey, fetchData);
</script>
