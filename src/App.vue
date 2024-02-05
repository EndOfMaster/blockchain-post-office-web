<template>
  <div style="width: 1600px;">
    <el-container>
      <el-header>
        <el-row>
          <el-col :offset="9" :span="6" style="font-size: 30px;">
            Blockchain Post Office
          </el-col>
          <el-col :offset="5" :span="4">
            <div style=" padding-top: 8px;">
              <el-button type="primary" @click="connectWallet()" v-show="network == null">Connect Wallet</el-button>
              <div v-show="network != null" style="font-size: 20px;">{{ network }}&nbsp;&nbsp;&nbsp;{{ address }}
              </div>
            </div>
          </el-col>
        </el-row>
      </el-header>
    </el-container>
    <el-divider>
      <el-icon><star-filled /></el-icon>
    </el-divider>
    <el-tabs v-model="activeName" class="demo-tabs" @tab-click="handleClick" stretch=true>
      <el-tab-pane label="Send" name="first">
        <el-container>
          <el-main>
            <el-row>
              <el-col :offset="8" :span="6">
                <div style="font-size: 25px;">Base Info</div>
              </el-col>
            </el-row>
            <br>
            <el-row>
              <el-col :offset="4" :span="3" style="font-size: 25px;">
                receiver address
              </el-col>
              <el-col :span="4">
                <div style=" padding-top: 6px;">
                  <el-input v-model="receiver" placeholder="receiver address" />
                </div>
              </el-col>
              <el-col :offset="1" :span="2" style="font-size: 25px;">
                deadline
              </el-col>
              <el-col :span="4">
                <div style=" padding-top: 6px;">
                  <el-input v-model="deadline" placeholder="deadline" />
                </div>
              </el-col>
            </el-row>
            <br>
            <hr>
            <el-row>
              <el-col :offset="8" :span="6">
                <div style="font-size: 25px;">Payment Info</div><br>
                <div>What do you want the recipient to pay?</div>
              </el-col>
            </el-row>
            <br>
            <el-row>
              <el-col :offset="4" :span="3" style="font-size: 25px;">
                token address
              </el-col>
              <el-col :span="4">
                <div style=" padding-top: 6px;">
                  <el-input v-model="payInfo.token" placeholder="token address" />
                </div>
              </el-col>
              <el-col :offset="1" :span="2" style="font-size: 25px;">
                amount
              </el-col>
              <el-col :span="4">
                <div style=" padding-top: 6px;">
                  <el-input v-model="payInfo.amount" placeholder="token amount" />
                </div>
              </el-col>
            </el-row>
            <br><br><br>
            <hr>
            <el-row>
              <el-col :offset="8" :span="6">
                <div style="font-size: 25px;">Send Annexs</div><br>
                <div>what do you want to send?</div>
              </el-col>
            </el-row>
            <br><br>

            <el-row v-for="(item, index) in annexs" style="font-size: 20px;">
              <el-col :span="4">
                type
                <el-radio-group v-model="item.type">
                  <el-radio :label="1">ERC20</el-radio>
                  <el-radio :label="2">ERC721</el-radio>
                  <el-radio :label="3">ERC1155</el-radio>
                </el-radio-group>
                <!-- <el-divider direction="vertical" /> -->
              </el-col>
              <el-col :span="7">
                address
                <el-input type="input" placeholder="address" v-model="item.address" style="width: 320px;" />
              </el-col>
              <el-col :span="3" v-if="item.type == 2 || item.type == 3">
                id
                <el-input type="input" placeholder="id" v-model="item.id" style="width: 100px;" />
              </el-col>
              <el-col :span="5" v-if="item.type == 1 || item.type == 3">
                amount
                <el-input type="input" placeholder="amount" v-model="item.amount" style="width: 200px;" />
              </el-col>
              <el-col :span="1" v-if="index > 0">
                <el-button type="danger" @click="deleteAnnex(index)">-</el-button>
              </el-col>
              <br>
            </el-row>
            <el-row>
              <el-col :offset="23" :span="1">
                <el-button type="primary" @click="addAnnex()">+</el-button>
              </el-col>
            </el-row>
            <br>
            <hr>
            <br>
            <el-row>
              <el-col :offset="10" :span="2">
                <el-button type="success">Send</el-button>
              </el-col>
            </el-row>
            <br><br><br><br><br>
          </el-main>
        </el-container>
      </el-tab-pane>
      <el-tab-pane label="Claim" name="second">Claim</el-tab-pane>
    </el-tabs>
  </div>
</template>

<style>
.el-radio {
  margin-right: 5px;
}
</style>

<script>
import { ethers } from "ethers";
import { ElMessage } from 'element-plus'
import { ref } from 'vue'

const delay = ms => new Promise(resolve => setTimeout(resolve, ms));

export default {
  name: 'App',
  created() {
    ethereum.on('accountsChanged', this.accountsChanged);
    ethereum.on('chainChanged', this.chainChanged);
    ethereum.on('disconnect', this.disconnect);
  },
  data() {
    return {
      activeName: ref('first'),
      provider: undefined,
      signer: null,
      network: null,
      address: null,
      receiver: null,
      deadline: null,
      payInfo: {
        token: null,
        amount: null
      },
      annexs: [
        {
          type: "",
          address: "",
          amount: "",
          id: ""
        }
      ],
      annexNum: 1
    }
  },
  methods: {
    sendLetter() {
      //TODO
    },
    async connectWallet() {
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
      console.log(accounts);
      if (accounts.length > 0) {
        let provider0 = new ethers.BrowserProvider(window.ethereum)
        let signer0 = await provider0.getSigner();
        this.address = signer0.address.substring(0, 8) + "..."
        this.signer = signer0
      } else {
        this.network = null;
        this.provider = undefined;
        this.signer = null
        this.address = null
      }
    },
    async chainChanged(networkId) {
      console.log(networkId);
      let provider0 = new ethers.BrowserProvider(window.ethereum)
      let signer0 = await provider0.getSigner();
      this.network = (await provider0.getNetwork()).name

      this.provider = provider0;
      this.signer = signer0;
    },
    disconnect() {
      this.network = null;
      this.provider = undefined;
      this.signer = null
      this.address = null
    },
    deleteAnnex(index) {
      if (this.annexs.length <= 1) {
        return false
      }
      this.annexs.splice(index, 1)
    },
    addAnnex() {
      this.annexs.push(
        {
          type: "",
          address: "",
          amount: "",
          id: ""
        }
      )
    },
  }
}

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
}

</script>