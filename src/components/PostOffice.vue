<template>
  <div style="width: 1600px;">
    <br>
    <br>
    <el-tabs v-model="activeName" class="demo-tabs" stretch=true>
      <el-tab-pane label="Send" name="first">
        <el-container>
          <el-main>
            <el-row>
              <el-col :offset="11" :span="2">
                <div style="font-size: 25px;">Base Info</div>
              </el-col>
            </el-row>
            <br>
            <el-row>
              <el-col :offset="4" :span="3" style="font-size: 25px;">
                recipient address
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
                  <el-input v-model="deadline" placeholder="timestamp" />
                </div>
              </el-col>
            </el-row>
            <br>
            <hr>
            <el-row>
              <el-col :offset="9" :span="6">
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
                  <el-input v-model="payInfo.amount" placeholder="like 1.132" />
                </div>
              </el-col>
            </el-row>
            <br><br><br>
            <hr>
            <el-row>
              <el-col :offset="9" :span="6">
                <div style="font-size: 25px;">Send Annexs</div><br>
                <div>what do you want to send?</div>
                <div style="color: red;">Currently you still need to manually approve</div>
              </el-col>
            </el-row>
            <br><br>
            <el-row v-for="(item, index) in annexs" style="font-size: 20px;">
              <el-col :span="5">
                type
                <el-radio-group v-model="item.type">
                  <el-radio :label="0">ETH</el-radio>
                  <el-radio :label="1">ERC20</el-radio>
                  <el-radio :label="2">ERC721</el-radio>
                  <el-radio :label="3">ERC1155</el-radio>
                </el-radio-group>
                <!-- <el-divider direction="vertical" /> -->
              </el-col>
              <el-col :span="7" v-if="item.type == 1 || item.type == 2 || item.type == 3">
                address
                <el-input type="input" placeholder="address" v-model="item.address" style="width: 320px;" />
              </el-col>
              <el-col :span="3" v-if="item.type == 2 || item.type == 3">
                id
                <el-input type="input" placeholder="id" v-model="item.id" style="width: 100px;" />
              </el-col>
              <el-col :span="5" v-if="item.type == 0 || item.type == 1 || item.type == 3">
                amount
                <el-input type="input" placeholder="like 1.123" v-if="item.type == 0 || item.type == 1"
                  v-model="item.amount" style="width: 200px;" />
                <el-input type="input" placeholder="Only integers" v-if="item.type == 3" v-model="item.amount"
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
              <el-col :offset="11" :span="2">
                <el-button type="success" @click="sendLetter()" v-if="!sending">Send</el-button>
                <br>
                <br>
                <el-progress :percentage="70" :indeterminate="true" width="300" v-if="sending" />
              </el-col>
            </el-row>
            <div style="font-size: 20px;">letterId: {{ letterId }}</div>
            <br><br><br><br><br>
          </el-main>
        </el-container>
      </el-tab-pane>
      <el-tab-pane label="Claim" name="second">
        <el-container>
          <el-main>
            <el-row>
              <el-col :offset="8" :span="6">
                <el-input v-model="queryId" placeholder="letter id" style="width: 300px;" />&nbsp;
                <el-button type="primary" @click="getLetter()">Query</el-button>
              </el-col>
            </el-row>
            <br>
            <hr>
            <el-row>
              <el-col :offset="11" :span="2">
                <div style="font-size: 25px;">Base Info</div>
              </el-col>
            </el-row>
            <br>
            <el-row style="font-size: 15px;">
              <el-col :offset="2" :span="2">
                Recipient address
              </el-col>
              <el-col :span="5">
                {{ queryLetter.recipient }}
              </el-col>
              <el-col :offset="1" :span="2">
                Sender address
              </el-col>
              <el-col :span="5">
                {{ queryLetter.sender }}
              </el-col>
              <el-col :offset="1" :span="1">
                Deadline
              </el-col>
              <el-col :span="3">
                {{ queryLetter.deadline }}
              </el-col>
            </el-row>
            <br>
            <hr>
            <el-row>
              <el-col :offset="9" :span="6">
                <div style="font-size: 25px;">Payment Info</div><br>
              </el-col>
              <br>
            </el-row>
            <el-row>
              <el-col :offset="3" :span="10" style="font-size: 25px;">
                {{ queryPayment.name }} ({{ queryPayment.address }})
              </el-col>
              <el-col :offset="1" :span="5" style="font-size: 25px;">
                amount: {{ queryPayment.showAmount }}
              </el-col>
            </el-row>
            <br>
            <hr>
            <el-row>
              <el-col :offset="9" :span="6">
                <div style="font-size: 25px;">Annexs Info</div><br>
              </el-col>
            </el-row>
            <br>
            <el-row v-for="(item, index) in queryAnnexsShow" style="font-size: 20px;">
              <el-col :offset="2" :span="2">
                type: {{ item.type }}
              </el-col>
              <el-col :span="9" v-if="item.type == 'ERC721' || item.type == 'ERC20' || item.type == 'ERC1155'">
                address: {{ item.token }}
              </el-col>
              <el-col :span="3" v-if="item.type == 'ERC721' || item.type == 'ERC1155'">
                id: {{ item.id }}
              </el-col>
              <el-col :span="4" v-if="item.type == 'ETH' || item.type == 'ERC20' || item.type == 'ERC1155'">
                amount: {{ item.amount }}
              </el-col>
            </el-row>
            <br>
            <hr>
            <br>
            <el-row>
              <el-col :offset="11" :span="2">
                <el-button type="success" @click="claim()">Claim</el-button>
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

.el-main {
  padding: 0;
}
</style>

<script>
import { ethers, Contract, AbiCoder } from "ethers";
import { ElMessage } from 'element-plus'
import { ref } from 'vue'
import BigNumber from "bignumber.js";
import { erc20Abi, postOfficAbi } from '../assets/config'
const coder = AbiCoder.defaultAbiCoder();

const CONTRACT_ADDRESS = {
  "11155111": "0xA345df28B272E617c0179912dB98c767a1A03406",
  "168587773": "0xabba4D35cCb5da1A0daaaF171C0480A25d802eD7"
}

export default {
  name: 'PostOffice',
  data() {
    return {
      activeName: ref('first'),
      sending: false,
      contract: null,
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
          id: "0"
        }
      ],
      letterId: "",
      queryPayment: {
        address: null,
        name: null,
        amount: null,
        showAmount: null
      },
      queryId: "",
      queryAnnexsShow: [],
      queryLetter: {
        deadline: null,
        recipient: null,
        sender: null,
        annexAmount: null,
        id: null
      }
    }
  },
  methods: {
    async claim() {
      let provider = new ethers.BrowserProvider(window.ethereum)
      let signer = await provider.getSigner()
      const network = await provider.getNetwork()
      const chainId = network.chainId;
      const postOffice = new Contract(CONTRACT_ADDRESS[chainId], postOfficAbi, provider)
      if (this.queryId == null || this.queryId == undefined || this.queryId == '') {
        ElMessage({ message: "letterId is empty", type: 'warning', });
        return;
      }
      await postOffice.connect(signer).claim(this.queryId)
    },
    async getLetter() {
      let provider = new ethers.BrowserProvider(window.ethereum)
      const network = await provider.getNetwork()
      const chainId = network.chainId;
      const postOffice = new Contract(CONTRACT_ADDRESS[chainId], postOfficAbi, provider)
      const letter = await postOffice.letters(this.queryId);
      if (letter[0] === ethers.ZeroAddress) {
        ElMessage({ message: "There is no such letter", type: 'warning', });
        return;
      }

      //base info
      this.queryLetter.sender = letter[0];
      this.queryLetter.recipient = letter[1];
      this.annexAmount = letter[2]

      this.queryPayment.address = letter[3][0]
      this.queryPayment.amount = letter[3][1]

      const erc20 = new Contract(this.queryPayment.address, erc20Abi, provider)
      const decimals = await erc20.decimals();
      this.queryPayment.showAmount = BigNumber(this.queryPayment.amount).div(BigNumber(10).pow(decimals)).decimalPlaces(6).toString()
      const name = await erc20.symbol();
      this.queryPayment.name = name;
      this.queryLetter.deadline = this.getFormatDate(letter[4] * 1000n)

      this.queryAnnexsShow = []
      //annex info
      for (let i = 0; i < this.annexAmount; i++) {
        let index = coder.encode(["uint256"], [i])
        index = index.substring(2, index.length)
        const annex = await postOffice.annex(this.queryId + index)

        this.queryAnnexsShow.push((await this.getQueryShow(annex, provider)));
      }
    },
    async getQueryShow(annex, provider) {
      const type = annex[0];
      if (type == 0) {
        const amount = BigNumber(annex[2]).div(BigNumber(10).pow(18)).decimalPlaces(6).toString()
        return { type: "ETH", amount: amount };
      }
      if (type == 1) {
        const erc20 = new Contract(annex[1], erc20Abi, provider)
        const decimals = await erc20.decimals();
        const amount = BigNumber(annex[2]).div(BigNumber(10).pow(decimals)).decimalPlaces(6).toString()
        const name = await erc20.symbol();
        return { type: "ERC20", token: annex[1], amount: amount, name: name }
      }
      if (type == 2) {
        const erc20 = new Contract(annex[1], erc20Abi, provider)
        const name = await erc20.symbol();
        return { type: "ERC721", name: name, token: annex[1], id: annex[3] }
      }
      if (type == 3) {
        return { type: "ERC1155", token: annex[1], amount: annex[2], id: annex[3] }
      };
    },
    getFormatDate(cellValue) {
      const date = new Date(parseInt(cellValue));
      const Y = date.getFullYear() + '-';
      const M = (date.getMonth() + 1 < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1) + '-';
      const D = date.getDate() + ' ';
      const h = date.getHours() + ':';
      const m = date.getMinutes() + ':';
      const s = date.getSeconds();
      return Y + M + D + h + m + s;
    },
    paymentShow() {
      return BigNumber(this.queryPayment.amount).div(BigNumber(10).pow(this.queryPayment.decimals)).decimalPlaces(6)
    },
    async sendLetter() {
      const annexsData = []
      let e = BigNumber(0);
      let provider = new ethers.BrowserProvider(window.ethereum)
      const network = await provider.getNetwork()
      const chainId = network.chainId;
      for (let i = 0; i < this.annexs.length; i++) {
        const element = this.annexs[i];
        let amount = element.amount
        let address = element.address
        let id = element.id;
        if (element.type == 0) {
          e = e.plus(BigNumber(amount).multipliedBy(10n ** 18n));
          amount = BigNumber(amount).multipliedBy(10n ** 18n).toFixed(0);
          address = ethers.ZeroAddress
          id = 0;
        }
        if (element.type == 1) {
          const erc20 = new Contract(element.address, erc20Abi, provider)
          const decimals = await erc20.decimals();
          amount = BigNumber(amount).multipliedBy(10n ** decimals).toFixed(0);
          id = 0;
        }
        if (element.type == 2) {
          amount = 0
        }
        annexsData.push([element.type, address, amount, id])
      }
      e = BigInt(e.toFixed(0));
      const payToken = new Contract(this.payInfo.token, erc20Abi, provider)
      const payTokenDecimals = await payToken.decimals();
      const paymentInfo = [this.payInfo.token, BigNumber(this.payInfo.amount).multipliedBy(10n ** payTokenDecimals).toFixed(0)]

      const postOffice = new Contract(CONTRACT_ADDRESS[chainId], postOfficAbi, provider)

      this.sending = true;

      let signer = await provider.getSigner()
      try {
        const tx = await postOffice.connect(signer).sendLetter(annexsData, paymentInfo, this.receiver, this.deadline, { value: e });
        const returnData = await tx.wait()
        const id = returnData.logs[returnData.logs.length - 1].args[0]
        this.sending = false;
        this.letterId = id;
      } catch (error) {
        ElMessage({ message: error.message, type: 'error', duration: 10000 });
        this.sending = false;
      }
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

</script>