<template>
  <div class="p-6 space-y-6">
    <h1 class="text-2xl font-bold" style="font-family: 'Expletus Sans';">Create ICO</h1>

    <div class="flex justify-center md:justify-start space-x-2 mt-6 mb-9">
      <div>
          <UButton
              size="lg"
              @click="$router.push('/')"
              icon="i-lucide-chevron-left"
              class="text-white dark:text-white w-fit"
          >
              Launchpad</UButton
          >
      </div>
    </div>
    <div v-if="!ethAddress" class="text-red-600">Please connect your wallet to continue.</div>

    <div v-else class="space-y-4">
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
        <div>
          <label class="block font-medium">Token Address</label>
          <input v-model="icoForm.token" type="text" class="input" placeholder="0x..."  className="border border-gray-300 p-2 rounded-md w-full" />
        </div>

       <div>
          <label class="block font-medium">Payment Token</label>
          <select v-model="icoForm.paymentTokenSelection" class="border border-gray-300 p-2 rounded-md w-full">
            <option value="fuma">FUMA (Native)</option>
            <option value="usdc">USDC</option>
            <option value="usdt">USDT</option>
            <option value="custom">Other (Custom Address)</option>
          </select>
        </div>

        <div v-if="icoForm.paymentTokenSelection === 'custom'">
          <label class="block font-medium mt-2">Custom Token Address</label>
          <input
            v-model="icoForm.customPaymentToken"
            type="text"
            placeholder="0x..."
            class="border border-gray-300 p-2 rounded-md w-full"
          />
        </div>

        <div>
          <label class="block font-medium">Amount (in Tokens)</label>
          <input v-model="icoForm.amount" type="text" class="input" placeholder="e.g. 1000000" className="border border-gray-300 p-2 rounded-md w-full"/>
        </div>

        <div>
          <label class="block font-medium">Bonus Reserve</label>
          <input v-model="icoForm.bonusReserve" type="text" class="input" placeholder="e.g. 100000" className="border border-gray-300 p-2 rounded-md w-full"/>
        </div>

        <div>
          <label class="block font-medium">Start Price (in FUMA)</label>
          <input v-model="icoForm.startPrice" type="text" class="input" placeholder="e.g. 0.01" className="border border-gray-300 p-2 rounded-md w-full"/>
        </div>

        <div>
          <label class="block font-medium">End Price (0 for fixed)</label>
          <input v-model="icoForm.endPrice" type="text" class="input" placeholder="e.g. 0" className="border border-gray-300 p-2 rounded-md w-full"/>
        </div>

        <div>
          <label class="block font-medium">Start Date</label>
          <input
            v-model="icoForm.startDate"
            type="datetime-local"
            class="border border-gray-300 p-2 rounded-md w-full"
          />
        </div>

        <div>
          <label class="block font-medium">End Date</label>
          <input
            v-model="icoForm.endDate"
            type="datetime-local"
            class="border border-gray-300 p-2 rounded-md w-full"
          />
          <small class="text-gray-400">Leave empty for unlimited</small>
        </div>

        <div>
          <label class="block font-medium">Bonus Percentage (e.g. 2500 for 25%)</label>
          <input v-model="icoForm.bonusPercentage" type="number" class="input" className="border border-gray-300 p-2 rounded-md w-full"/>
        </div>

        <div>
          <label class="block font-medium">Bonus Activator (e.g. 1000 for 10%)</label>
          <input v-model="icoForm.bonusActivator" type="number" class="input" className="border border-gray-300 p-2 rounded-md w-full"/>
        </div>

        <div>
          <label class="block font-medium">Unlock Percentage (e.g. 5000 = 50%)</label>
          <input v-model="icoForm.unlockPercentage" type="number" min="0" max="10000" class="input" className="border border-gray-300 p-2 rounded-md w-full"/>
        </div>

        <div>
          <label class="block font-medium">Cliff Period (seconds)</label>
          <input v-model="icoForm.cliffPeriod" type="number" class="input" className="border border-gray-300 p-2 rounded-md w-full"/>
        </div>

        <div>
          <label class="block font-medium">Vesting Percentage (e.g. 1000 = 10%)</label>
          <input v-model="icoForm.vestingPercentage" type="number" min="100" max="10000" class="input" className="border border-gray-300 p-2 rounded-md w-full"/>
        </div>

        <div>
          <label class="block font-medium">Vesting Interval (seconds)</label>
          <input v-model="icoForm.vestingInterval" type="number" class="input" className="border border-gray-300 p-2 rounded-md w-full"/>
        </div>
      </div>

      <button @click="createICO" class="bg-[#c42f29] text-white px-4 py-2 rounded-md">
        Create ICO
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
  import Web3 from 'web3';
  import { ref, onMounted } from 'vue';
  import LaunchpadABI from '@/abis/Launchpad.json';
  import ERC20ABI from '@/abis/ERC20.json';
  import { proxyAddress, getMetaMaskEthereum } from '~/js/ico-evm';
  import { ethers } from 'ethers';
  import { useRouter } from 'vue-router'

  const router = useRouter()
  const web3 = new Web3(window.ethereum);
  const now = new Date();
  const inOneHour = new Date(now.getTime() + 3600 * 1000).toISOString().slice(0, 16); // yyyy-MM-ddTHH:mm


  const ethAddress = ref<string | null>(null);
  const icoForm = ref({
    token: '',
    paymentTokenSelection: 'fuma', // dropdown selection
    customPaymentToken: '',        // manual input
    amount: '',
    startPrice: '0.01',
    endPrice: '0',
    startDate: inOneHour,
    endDate: '',
    bonusReserve: '',
    bonusPercentage: 2500,
    bonusActivator: 1000,
    unlockPercentage: 5000,
    cliffPeriod: 60,
    vestingPercentage: 1000,
    vestingInterval: 60
  });


  onMounted(async () => {
    const provider = new ethers.BrowserProvider(getMetaMaskEthereum());
    await provider.send("eth_requestAccounts", []); // prompts MetaMask connect
    const signer = await provider.getSigner();
    ethAddress.value = signer.address;
  });

  const approveToken = async () => {
    try {
      if(!ethAddress.value) return
      const provider = new ethers.BrowserProvider(getMetaMaskEthereum());
      await provider.send("eth_requestAccounts", []); // prompts MetaMask connect
      const signer = await provider.getSigner();
      const token = new ethers.Contract(icoForm.value.token, ERC20ABI, signer);
      const total = web3.utils.toWei((Number(icoForm.value.amount) + Number(icoForm.value.bonusReserve)).toString(), 'ether');

      const tx = await token.approve(proxyAddress, total);
      const result = await tx.wait();
      if (result) {
        return result
      }
    } catch (error) {
      console.log("error: error");
    }
  };

  const getResolvedPaymentToken = () => {
    switch (icoForm.value.paymentTokenSelection) {
      case 'fuma':
        return '0x0000000000000000000000000000000000000000'; // native FUMA
      case 'usdc':
        return '0xf8EA5627691E041dae171350E8Df13c592084848'; // replace with actual address
      case 'usdt':
        return '0x1e11d176117dbEDbd234b1c6a10C6eb8dceD275e'; // replace with actual address
      case 'custom':
        return icoForm.value.customPaymentToken;
      default:
        return '0x0000000000000000000000000000000000000000';
    }
  }

  function validateICOInputs(): string | null {
    const now = Date.now();
    const start = new Date(icoForm.value.startDate).getTime();
    const end = icoForm.value.endDate ? new Date(icoForm.value.endDate).getTime() : 0;

    if (!icoForm.value.startDate) return "Start date is required.";

    if (start < now - 60 * 1000) return "Start date must be in the future.";

    if (icoForm.value.endDate && start >= end) return "End date must be after start date.";

    const startPrice = parseFloat(icoForm.value.startPrice);
    const endPrice = parseFloat(icoForm.value.endPrice);

    if (isNaN(startPrice) || isNaN(endPrice)) return "Start and End price must be valid numbers.";

    const vestingPercentage = icoForm.value.vestingPercentage;
    if (isNaN(vestingPercentage) || vestingPercentage < 100 || vestingPercentage > 10000) {
      return "Vesting percentage must be between 1% and 100%.";
    }

    const cliffPeriod = icoForm.value.cliffPeriod;
    if (isNaN(cliffPeriod) || cliffPeriod < 60) {
      return "Cliff period must be greater than 60 seconds.";
    }

    const vestingInterval = icoForm.value.vestingInterval;
    if (isNaN(vestingInterval) || vestingInterval < 60) {
      return "Vesting interval must be greater than 60 seconds.";
    }

    return null;
  }



  const createICO = async () => {
    try {
  
      const error = validateICOInputs();
      
      if (error) {
        alert(error);
        return;
      }

      if(!ethAddress.value) return
      const provider = new ethers.BrowserProvider(getMetaMaskEthereum());
      console.log(provider);
      await provider.send("eth_requestAccounts", []); // prompts MetaMask connect
      const signer = await provider.getSigner();

      const proxyAsLaunchpad = new ethers.Contract(proxyAddress, LaunchpadABI, signer);
      await approveToken();
      const creationFee = await proxyAsLaunchpad.creationFee();

      const icoParams = {
        token: icoForm.value.token,
        paymentToken: getResolvedPaymentToken(),
        amount: (web3.utils.toWei(icoForm.value.amount, 'ether')),
        startPrice: (web3.utils.toWei(icoForm.value.startPrice, 'ether')),
        endPrice: (web3.utils.toWei(icoForm.value.endPrice, 'ether')),
        startDate: Math.floor(new Date(icoForm.value.startDate).getTime() / 1000),
        endDate: icoForm.value.endDate ? Math.floor(new Date(icoForm.value.endDate).getTime() / 1000) : 0,
        bonusReserve: (web3.utils.toWei(icoForm.value.bonusReserve, 'ether')),
        bonusPercentage: +icoForm.value.bonusPercentage,
        bonusActivator: +icoForm.value.bonusActivator,
        vestingParams: {
          unlockPercentage: +icoForm.value.unlockPercentage,
          cliffPeriod: +icoForm.value.cliffPeriod,
          vestingPercentage: +icoForm.value.vestingPercentage,
          vestingInterval: +icoForm.value.vestingInterval
        }
      };
      console.log(icoParams);
    
      const tx = await proxyAsLaunchpad.createICO(icoParams,  
        {
            value: creationFee.toString(),
        }
      );

      console.log("transaction hash is ", tx);
      if ( await tx.wait()) {
        router.push('/')
      }
    } catch (err:any) {
      console.error("Create ICO failed:", err);
      alert("Create ICO failed: " + (err.message || "Unknown error"));
    }
  };
</script>
