<template>
  <div style="width: 1500px;">
    <el-container>
      <el-header>
        <el-row>
          <el-col :offset="8" :span="6" style="font-size: 30px;">
            Blockchain Post Office
          </el-col>
          <el-col :offset="6" :span="4">
            <div style=" padding-top: 8px;">
              <el-button type="primary" @click="connectWallet()" v-show="network == null">Connect Wallet</el-button>
              <div v-show="network != null" style="font-size: 20px;">{{ network }}&nbsp;&nbsp;&nbsp;{{ address }}</div>
              <!-- <el-button type="primary" @click="log()">aaa</el-button> -->
            </div>
          </el-col>
        </el-row>
      </el-header>
      <el-main>
        <el-row>
          <el-col :span="24">
            <div class="grid-content ep-bg-purple-dark" />
          </el-col>
        </el-row>
      </el-main>
    </el-container>
  </div>
</template>

<!-- <style scoped>
</style> -->

<script>
import { ethers } from "ethers";
import { ElMessage } from 'element-plus'

const delay = ms => new Promise(resolve => setTimeout(resolve, ms));

// let provider;
// let signer = null;
// let network = null

// window.ethereum.on("chainChanged", async function (a) {
//   console.log(a);
//   await delay(1000)
//   provider = new ethers.BrowserProvider(window.ethereum)
//   signer = await provider.getSigner();
//   network = (await provider.getNetwork()).name
//   console.log("aaa");
// })

// window.ethereum.on("accountsChanged", async function (a) {
//   signer = await provider.getSigner();
// })

export default {
  name: 'App',
  created() {
    ethereum.on('accountsChanged', this.accountsChanged);
    ethereum.on('chainChanged', this.chainChanged);
    ethereum.on('disconnect', this.disconnect);
  },
  data() {
    return {
      provider: undefined,
      signer: null,
      network: null,
      address: null,
    }
  },
  methods: {
    async connectWallet() {
      //child.value.testLog();
      if (window.ethereum == null) {
        ElMessage.error('Please install metamask')
      } else {
        try {
          let provider0 = new ethers.BrowserProvider(window.ethereum)
          let signer0 = await provider0.getSigner();
          this.address = signer0.address.substring(0, 8) + "..."
          this.network = (await provider0.getNetwork()).name

          this.provider = provider0;
          this.signer = signer0;
        } catch (error) {
          if (error.info.error.code === 4001) {
            ElMessage({
              message: "User rejected the request",
              type: 'warning',
            })
          }
        }
      }
    },
    async accountsChanged(accounts) {
      let signer0 = await this.provider.getSigner();
      this.signer = signer0
    },
    async chainChanged(networkId) {
      console.log(networkId);
      let provider0 = new ethers.BrowserProvider(window.ethereum)
      let signer0 = await provider0.getSigner();
      this.network = (await provider0.getNetwork()).name

      this.provider = provider0;
      this.signer = signer0;
      console.log("aaa");
    },
    disconnect() {
      this.network = '';
      this.provider = undefined;
      this.signer = null
    },
    log() {
      console.log(this.provider);
      console.log(this.signer);
      console.log(this.network);
    }
  }
}

// const getProvider = async () => {
//   await delay(500)
//   let aa = new ethers.BrowserProvider(window.ethereum)
//   console.log(aa.ready);
//   if (aa.ready === true) {
//     return aa;
//   }
//   await getProvider();
// }

const connectWallet = async () => {
  //child.value.testLog();
  if (window.ethereum == null) {
    ElMessage.error('Please install metamask')
  } else {
    try {
      provider = new ethers.BrowserProvider(window.ethereum)
      signer = await provider.getSigner();
      network = (await provider.getNetwork()).name
    } catch (error) {
      if (error.info.error.code === 4001) {
        ElMessage({
          message: "User rejected the request",
          type: 'warning',
        })
      }
    }
  }
  console.log(provider);
  console.log(signer);
}

</script>