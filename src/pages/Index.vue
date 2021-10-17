<template>
  <q-page style="max-width: 700px; margin: auto; margin-top: 50px;">
    <div class="flex justify-around">
      <q-btn label="Start test" color="primary" @click="Click()" filled/>
      <q-option-group
      v-model="mode"
      inline
      :options="[
        {
          label: 'Sync',
          value: 'sync'
        },
        {
          label: 'Async',
          value: 'async'
        },
        {
          label: 'Batch',
          value: 'batch'
        }
      ]"
      color="primary"
    />

      <q-input v-model="amount" filled type="number" />
    </div>
    <br>
        <div v-if="mode == 'batch'" class="flex justify-center">
      <q-slider v-model="batchSize" :min="2" :max="100" label/>
    </div>
    <br>
    <div>
      <div class=" flex">
        Load time:
        <div v-if="end && start">
          {{end - start}} ms
        </div>
        <div v-else-if="!end && start">
          {{Math.round((new Date()).getTime()) - start}} ms
        </div>
      </div>
      <div>

      </div>
    </div>



    <br>

    <div class="flex" >
      <div v-for="i in parseInt(amount)" :key="i" class="Ibox text-center" :class="{complete: complete.length >= i}">
        {{i}}
      </div>
    </div>
  </q-page>
</template>

<script>
//:class="{complete: complete.filter(x=> {return x === i - 1}).length != 0}"
import axios from 'axios'
import eachLimit from "async/eachLimit";
export default {
  name: 'PageIndex',
  data() {
    return {
      amount: 100,
      complete: [],
      start: null,
      end: null,
      mode: 'sync',
      batchSize: 10,
    }
  },
  methods: {
    async Click() {
      var vm = this
      var ts = Math.round((new Date()).getTime());
      vm.complete = []
      vm.end = null
      vm.start = ts

      var ls = []
      for (let i = 0; i < this.amount; i++) {
        ls.push(i)
      }
if (this.mode == 'batch') {
  eachLimit(ls,this.batchSize, async function(item, callback) {
          await axios.get(`statics/icons/favicon-16x16.png?rnd=${Math.random()}` ).then(x=> {
          vm.complete.push(item)
          callback();
        })
        })
} else {
  for (let i = 0; i < this.amount; i++) {
        if (this.mode == 'async') {
        await axios.get(`statics/icons/favicon-16x16.png?rnd=${Math.random()}` ).then(x=> {
          vm.complete.push(i)
        })
      } else {
        axios.get(`statics/icons/favicon-16x16.png?rnd=${Math.random()}` ).then(x=> {
          vm.complete.push(i)
        })
      }
}




      }
    }
  },
  watch: {
    complete() {
      var ts = Math.round((new Date()).getTime());

      if (this.complete.length == this.amount) {
        this.end = ts
      }
    }
  }
}
</script>
<style scoped>
.Ibox {
  width: 30px;
  height: 30px;
  border: 1px solid black;
  background-color: rgb(182, 182, 182);
}
.complete {
  background-color: white;
}

</style>
