<template>
  <div class="parent">
    <div class="child">
    <h1 class="text-2xl md:text-4xl my-2">Check info</h1>
    <form class="mt-10" @submit.prevent="networkId">
      
      <button
        class="border rounded bg-gradient-to-r from-blue-700 to-black text-white ml-2 px-2"
        type="submit"
      >
        Check 
      </button>
    </form>
    <button v-on:click="claimToken" class="border rounded bg-gradient-to-r from-blue-700 to-black text-white ml-2 px-2">Claim a token</button>
    <button v-on:click="buyToken" class="border rounded bg-gradient-to-r from-blue-700 to-black text-white ml-2 px-2">Buy a token</button>
    <p class="mt-10"  ref="id" id="result"></p>
    <p class="mt-10"  ref="blockNumber" id="blockNumber"></p>
    <p class="mt-10"  ref="account" id="account"></p>
    <p class="mt-10"  ref="name" id="name"></p>
    <p class="mt-10"  ref="totSupply" id="totSupply"></p>
    <p class="mt-10"  ref="metaData" id="metaData"></p>
    </div>
  </div>
</template>

<script>
import {ref} from 'vue'
import Web3 from 'web3'
import * as songABI from './songABI.json'
import * as toutABI from './ToutABI.json'

export default {
  name: 'CheckID',
  setup() {
    
    const ethEnabled = () => {
    if (window.web3) {
    window.web3 = new Web3(window.web3.currentProvider);
    window.ethereum.enable();
    console.log(window.web3.currentProvider)
    return true;
    }
  return false;
  }

  if (!ethEnabled()) {
  alert("Please install MetaMask to use this dApp!");
}
    let id = ref('Id du network:')
    let blockNumber = ref('')
    let account = ref('')
    let name = ref('')
    let totSupply = ref('')
    let metaData = ref('')
    let contractAddress = "0x004a84209A0021b8FF182FfD8BB874c53F65e90E"
    let contractAddress2 = "0x89150a0325ecc830a2304a44de98551051b4f466"
    let tokenContract = new window.web3.eth.Contract(songABI.abi, contractAddress)
    let tokenContract2 = new window.web3.eth.Contract(toutABI.abi, contractAddress2)
    
    const networkId =  async () => {
      const res = await window.web3.eth.net.getId()
      const blockNumberRes = await window.web3.eth.getBlockNumber()
      blockNumber.value.innerText = `Numero du dernier block: ${ blockNumberRes }`
      id.value.innerText = `Id du network: ${ res }`
      account.value.innerText = window.web3.currentProvider.selectedAddress
      console.log(window.web3.currentProvider.selectedAddress, window.web3.eth.accounts)
      // amount.value = id
      console.log(id)
      // return id
     
      const nameRes = await tokenContract.methods.name().call()
      name.value.innerText = `Name: ${ nameRes }`

      const totalSupply = await tokenContract.methods.totalSupply().call()
      totSupply.value.innerText = `Supply: ${ totalSupply }`

      const metaDataRes = await tokenContract.methods.tokenURI(0).call()
      metaData.value.innerText = `MetaData: ${ metaDataRes }`
      
      const balance1 = await tokenContract.methods.balanceOf(window.web3.currentProvider.selectedAddress);
      const balance2 = await tokenContract2.methods.balanceOf(window.web3.currentProvider.selectedAddress); 
      console.log(balance1)
      console.log(balance2)
    }
    const claimToken = async () => console.log(await tokenContract.methods.claimAToken().send({ from: window.web3.currentProvider.selectedAddress, gas: 2500000}))
    
    const buyToken = async () => console.log(await tokenContract2.methods.buyAToken().send({ from: window.web3.currentProvider.selectedAddress,  value:150000000000000000 , gas: 2500000 }))

    return {networkId, account, id, blockNumber, claimToken, name,totSupply,metaData, buyToken}
  }
}
</script>
<style>

.parent {
  display: grid;
  place-items: center;
  
  background:#FFFFFF;
  width: 100%;
  height: 100%;
  
  
  overflow: auto;
}

.child {
  
  padding: 0.5rem;
  border-radius: 10px;
  border: 2px solid rgba(39, 38, 39, 0.418);
  background: rgb(160, 156, 216);
  font-size: 2rem;
  text-align: center;
}

body {
  font-family: system-ui, serif;
}
</style>