<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>
      For a guide and recipes on how to configure / customize this project,<br>
      check out the
      <a href="https://cli.vuejs.org" target="_blank" rel="noopener">vue-cli documentation</a>.
    </p>
    <h3>Installed CLI Plugins</h3>
    <ul>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-babel" target="_blank" rel="noopener">babel</a></li>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-eslint" target="_blank" rel="noopener">eslint</a></li>
    </ul>
    <h3>Essential Links</h3>
    <ul>
      <li><a href="https://vuejs.org" target="_blank" rel="noopener">Core Docs</a></li>
      <li><a href="https://forum.vuejs.org" target="_blank" rel="noopener">Forum</a></li>
      <li><a href="https://chat.vuejs.org" target="_blank" rel="noopener">Community Chat</a></li>
      <li><a href="https://twitter.com/vuejs" target="_blank" rel="noopener">Twitter</a></li>
      <li><a href="https://news.vuejs.org" target="_blank" rel="noopener">News</a></li>
    </ul>
    <h3>Ecosystem</h3>
    <ul>
      <li><a href="https://router.vuejs.org" target="_blank" rel="noopener">vue-router</a></li>
      <li><a href="https://vuex.vuejs.org" target="_blank" rel="noopener">vuex</a></li>
      <li><a href="https://github.com/vuejs/vue-devtools#vue-devtools" target="_blank" rel="noopener">vue-devtools</a></li>
      <li><a href="https://vue-loader.vuejs.org" target="_blank" rel="noopener">vue-loader</a></li>
      <li><a href="https://github.com/vuejs/awesome-vue" target="_blank" rel="noopener">awesome-vue</a></li>
    </ul>
  </div>
</template>

<script>
import {ref} from 'vue'
import Web3 from 'web3'
import * as songABI from './songABI.json'

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
    let contractAddress = "0x004a84209A0021b8FF182FfD8BB874c53F65e90E"
    let tokenContract = new window.web3.eth.Contract(songABI.abi, contractAddress)
    
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
     
      const name = await tokenContract.methods.name().call()
      const totalSupply = await tokenContract.methods.totalSupply().call()
      const metaData = await tokenContract.methods.tokenURI(0).call()
      console.log({...tokenContract.methods}, name, totalSupply, metaData)
    }
    const claimToken = async () => console.log(await tokenContract.methods.claimAToken().send({ from: window.web3.currentProvider.selectedAddress, gas: 2500000}))
    
    
    return {networkId, account, id, blockNumber}
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
