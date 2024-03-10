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
              <el-col :offset="0" :span="3" style="font-size: 25px;">
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
              <el-col :offset="1" :span="2" style="font-size: 25px;">
                password
              </el-col>
              <el-col :span="4">
                <div style=" padding-top: 6px;">
                  <el-input v-model="password" placeholder="only a-z,A-Z,0-9" />
                </div>
              </el-col>
            </el-row>
            <br>
            <hr>
            <el-row>
              <el-col :offset="10" :span="3">
                <div style="font-size: 25px;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Message Info</div>
              </el-col>
            </el-row>
            <br>
            <el-row>
              <el-col :offset="4" :span="3" style="font-size: 25px;">
                public content
              </el-col>
              <el-col :span="4">
                <div style=" padding-top: 6px;">
                  <el-input v-model="message" style="width: 240px" :autosize="{ minRows: 2, maxRows: 4 }"
                    type="textarea" placeholder="Please input" />
                </div>
              </el-col>
              <el-col :offset="1" :span="2" style="font-size: 25px;">
                secretWords
              </el-col>
              <el-col :span="4">
                <div style=" padding-top: 6px;">
                  <el-input v-model="secretWords" style="width: 240px" :autosize="{ minRows: 2, maxRows: 4 }"
                    type="textarea" placeholder="Please input" />
                </div>
              </el-col>
            </el-row>
            <br>
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
                <el-progress :percentage="70" :indeterminate="true" width="300" v-if="claiming" />
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
              <el-col :offset="7" :span="8">
                LetterId:&nbsp; <el-input v-model="queryId" placeholder="letter id" style="width: 300px;" />&nbsp;
                <el-button type="primary" @click="getLetter()">Query</el-button>
              </el-col>
            </el-row>
            <br>
            <el-row>
              <el-col :offset="7" :span="8">
                Password:&nbsp; <el-input v-model="queryPassword" placeholder="password" style="width: 300px;" />&nbsp;
                <el-button type="primary" @click="getLetterByPassword()">Query</el-button>
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
              <el-col :offset="4" :span="3" style="font-size: 25px;">
                public content
              </el-col>
              <el-col :span="4">
                <div style=" padding-top: 6px;">
                  {{ queryLetter.message }}
                </div>
              </el-col>
              <el-col :offset="1" :span="2" style="font-size: 25px;">
                secretWords
              </el-col>
              <el-col :span="4">
                <div style=" padding-top: 6px;">
                  {{ queryLetter.secretWords }}
                </div>
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
                <br>
                <el-progress :percentage="70" :indeterminate="true" width="300" v-if="claiming" />
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
import { ethers, Contract } from "ethers";
import { ElMessage } from 'element-plus'
import { ref } from 'vue'
import BigNumber from "bignumber.js";
import { erc20Abi, vaultAbi } from '../assets/config'

const CONTRACT_ADDRESS = {
  "11155111": "0x9e121b995cd1D8Cc2e0E58735F1143047Ce76aFE",
  // "168587773": "0xabba4D35cCb5da1A0daaaF171C0480A25d802eD7"
}

export default {
  name: 'Vault',
  data() {
    return {
      activeName: ref('first'),
      password: null,
      sending: false,
      claiming: false,
      contract: null,
      address: null,
      receiver: null,
      deadline: null,
      message: null,
      secretWords: null,
      annexs: [{
        type: "",
        address: "",
        amount: "",
        id: "0"
      }],
      letterId: null,
      queryId: null,
      queryPassword: '',
      queryAnnexsShow: [],
      queryLetter: {
        deadline: null,
        recipient: null,
        sender: null,
        annexAmount: null,
        message: null,
        annexAmount: 0,
        secretWords: null
      },
    }
  },
  methods: {
    async claim() {
      const provider = new ethers.BrowserProvider(window.ethereum)
      const signer = await provider.getSigner()
      const network = await provider.getNetwork()
      const chainId = network.chainId;
      const vault = new Contract(CONTRACT_ADDRESS[chainId], vaultAbi, provider)
      if (this.queryLetter.recipient == null || this.queryLetter.recipient == undefined || this.queryLetter.recipient == '') {
        ElMessage({ message: "password is empty", type: 'warning', });
        return;
      }

      this.claiming = true;
      await vault.connect(signer).claim(this.queryPassword)
      this.claiming = false;
    },
    async getLetter() {
      this.clearAll();
      const provider = new ethers.BrowserProvider(window.ethereum)
      const network = await provider.getNetwork()
      const chainId = network.chainId;
      const vault = new Contract(CONTRACT_ADDRESS[chainId], vaultAbi, provider)
      const letter = await vault.letterPublicParams(this.queryId);
      if (letter[0] === ethers.ZeroAddress) {
        ElMessage({ message: "There is no such letter", type: 'warning', });
        return;
      }

      //base info
      this.queryLetter.sender = letter[0];
      this.queryLetter.recipient = letter[1];
      this.queryLetter.message = letter[2];
      this.queryLetter.deadline = this.getFormatDate(letter[3] * 1000n);
    },
    async getLetterByPassword() {
      this.clearAll();
      const provider = new ethers.BrowserProvider(window.ethereum)
      const signer = await provider.getSigner()
      const network = await provider.getNetwork()
      const chainId = network.chainId;
      const vault = new Contract(CONTRACT_ADDRESS[chainId], vaultAbi, provider)

      const [letter, annexes] = await vault.connect(signer).letterAllParams(this.queryPassword);

      if (letter[0] === ethers.ZeroAddress) {
        ElMessage({ message: "There is no such letter", type: 'warning', });
        return;
      }

      //base info
      this.queryLetter.sender = letter[0];
      this.queryLetter.recipient = letter[1];
      this.queryLetter.deadline = this.getFormatDate(letter[3] * 1000n);
      this.queryLetter.message = letter[4];
      this.queryLetter.secretWords = letter[5];

      for (let i = 0; i < annexes.length; i++) {
        this.queryAnnexsShow.push((await this.getQueryShow(annexes[i], provider)));
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

      const signer = await provider.getSigner()
      const userAddress = await signer.getAddress();
      const password = ethers.solidityPackedKeccak256(["address", "string"], [userAddress, this.password]);

      this.sending = true;
      try {
        const vault = new Contract(CONTRACT_ADDRESS[chainId], vaultAbi, provider)
        const tx = await vault.connect(signer).sendLetter(annexsData, this.message, this.secretWords, password, this.receiver, this.deadline, { value: e });
        const returnData = await tx.wait()
        const id = returnData.logs[returnData.logs.length - 1].args[0]
        this.sending = false;
        this.letterId = id;
      } catch (error) {
        ElMessage({ message: error.message, type: 'error', duration: 10000 });
        this.sending = false;
      }

    },
    clearAll() {
      this.password = null;
      this.sending = false;
      this.contract = null;
      this.address = null;
      this.receiver = null;
      this.deadline = null;
      this.message = null;
      this.secretWords = null;
      this.annexs = [{
        type: "",
        address: "",
        amount: "",
        id: "0"
      }];
      this.letterId = null;
      this.queryAnnexsShow = [];
      this.queryLetter = {
        deadline: null,
        recipient: null,
        sender: null,
        annexAmount: null,
        message: null,
        annexAmount: 0,
        secretWords: null
      };
    }
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

</script>