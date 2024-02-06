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
                <el-input type="input" placeholder="amount(100.1)" v-if="item.type == 1" v-model="item.amount"
                  style="width: 200px;" />
                <el-input type="input" placeholder="amount(Only integers)" v-if="item.type == 3" v-model="item.amount"
                  style="width: 200px;" />
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
              <el-col :offset="9" :span="5">
                <el-button type="success" @click="sendLetter()" v-if="!sending">Send</el-button>
                <br>
                <br>
                <el-progress :percentage="70" :indeterminate="true" width="300" v-if="sending" />
              </el-col>
            </el-row>
            <br><br><br><br><br>
          </el-main>
        </el-container>
      </el-tab-pane>
      <el-tab-pane label="Claim" name="second">
        <el-container>
          <el-main>
            <el-row>
              <el-col :offset="8" :span="6">
                <el-input v-model="queryId" placeholder="id" style="width: 300px;" />&nbsp;
                <el-button type="primary">Query</el-button>
              </el-col>
            </el-row>
            <br>
            <hr>
            <el-row>
              <el-col :offset="8" :span="6">
                <div style="font-size: 25px;">Payment Info</div><br>
              </el-col>
              <br>
            </el-row>
            <el-row>
              <el-col :offset="2" :span="10" style="font-size: 25px;">
                {{ queryPayment.name }} ({{ queryPayment.address }})
              </el-col>
              <el-col :offset="1" :span="5" style="font-size: 25px;">
                amount: {{ paymentShow() }}
              </el-col>
            </el-row>
            <br>
            <hr>
            <el-row>
              <el-col :offset="8" :span="6">
                <div style="font-size: 25px;">Annexs Info</div><br>
              </el-col>
            </el-row>
            <br>
            <el-row v-for="(item, index) in queryAnnexsShow" style="font-size: 20px;">
              <el-col :offset="2" :span="2">
                type: {{ item.type }}
              </el-col>
              <el-col :span="9">
                address: {{ item.address }}
              </el-col>
              <el-col :span="3" v-if="item.type == 'ERC721' || item.type == 'ERC1155'">
                id: {{ item.id }}
              </el-col>
              <el-col :span="4" v-if="item.type == 'ERC20' || item.type == 'ERC1155'">
                amount: {{ item.amount }}
              </el-col>
            </el-row>
            <br>
            <hr>
            <br>
            <el-row>
              <el-col :offset="10" :span="2">
                <el-button type="success">Claim</el-button>
              </el-col>
            </el-row>
          </el-main>
        </el-container>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<style>
.el-radio {
  margin-right: 5px;
}

.el-progress__text {
  display: none;
}
</style>

<script>
import { ethers, Contract } from "ethers";
import { ElMessage } from 'element-plus'
import { ref } from 'vue'
import BigNumber from "bignumber.js";
import { erc20Abi, postOfficAbi } from './assets/config'

const CONTRACT_ADDRESS = "0xca4C532B10b8E9d48A65239C7E9eAafBF170b3Ce"
const coder = new ethers.AbiCoder

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
      sending: false,
      provider: undefined,
      signer: null,
      contract: null,
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
          amount: "0",
          id: "0"
        }
      ],
      annexNum: 1,
      queryPayment: {
        address: "0xca4C532B10b8E9d48A65239C7E9eAafBF170b3Ce",
        name: "usdt",
        decimals: 18n,
        amount: 1000000010000000000000n,
      },
      queryId: null,
      queryAnnexs: [{
        type: "1",
        address: "0xca4C532B10b8E9d48A65239C7E9eAafBF170b3Ce",
        amount: 1000000010000000000000n,
        id: "0xca4C532B10b8E9d48A65239C7E9eAafBF170b3Ce1"
      }],
      queryAnnexsShow: [{
        type: "ERC1155",
        address: "0xca4C532B10b8E9d48A65239C7E9eAafBF170b3Ce",
        amount: "2",
        id: "1"
      }]
    }
  },
  methods: {
    paymentShow() {
      return BigNumber(this.queryPayment.amount).div(BigNumber(10).pow(this.queryPayment.decimals)).decimalPlaces(6)
    },
    async sendLetter() {
      const annexsData = []
      for (let i = 0; i < this.annexs.length; i++) {
        const element = annexs[i];
        let amount = element.amount
        if (element.type == 1) {
          const erc20 = new Contract(element.address, erc20Abi, this.provider)
          const decimals = erc20.decimals();
          amount = BigNumber(amount).multipliedBy(BigInt(10) ** decimals).toFormat(0);
        }
        annexsData.push([element.type, element.address, amount, element.id])
      }

      const payToken = new Contract(this.payInfo.token, erc20Abi, this.provider)
      const payTokenDecimals = payToken.decimals();
      const paymentInfo = [this.payInfo.token, BigNumber(this.payInfo.amount).multipliedBy(BigInt(10) ** payTokenDecimals).toFormat(0)]

      const postOffice = new Contract(CONTRACT_ADDRESS, postOfficAbi, this.provider)

      this.sending = true;

      const tx = await postOffice.connect(this.signer).sendLetter(annexsData, paymentInfo, this.receiver, this.deadline);
      const returnData = await tx.wait()
      const id = returnData.logs[returnData.logs.length - 1].args
      this.sending = false;
      console.log(id);
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