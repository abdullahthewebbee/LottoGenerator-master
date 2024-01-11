<template>
  <Lotto-header></Lotto-header>
  <div> 
    <img src="./assets/lottogenerator.jpg">
    <div>당첨 숫자</div>
    <div id="결과창">
      <lotto-ball v-for="ball in winBalls" v-bind:number="ball" :key="ball"></lotto-ball>
    </div>
    <div id="보너스창">보너스 </div>
    <lotto-ball v-if="bonus" :number="bonus"></lotto-ball>
    <button v-if="redo" @click="onClickRedo">한번더!</button>
  </div>
  <Lotto-footer></Lotto-footer>
</template>
  
<script>
//셔플정렬한 후 7개의 랜덤한 숫자를 뽑는 함수
  function getWinNumbers() {
    console.log('getWinNumbers');
    const candidate = Array(45).fill().map((v,i) => i+1);
    const shuffle = [];
    while(candidate.length > 0) {
      shuffle.push(candidate.splice(Math.floor(Math.random() * candidate.length),1)[0]);
    }
    const bonusNumber = shuffle[shuffle.length-1];
    const winNumbers = shuffle.slice(0, 6).sort((p,c) => p-c);
    return [...winNumbers, bonusNumber];
  }

import LottoHeader from './components/LottoHeader.vue'
import LottoBall from './components/LottoBall.vue'
import LottoFooter from './components/LottoFooter.vue'


const timeouts=[];
export default {
  name: 'App',
  components: {
    LottoBall, // lotto-ball : LottoBall
    LottoHeader,
    LottoFooter,
  },
  data(){
    return{
      winNumbers : getWinNumbers(),
      winBalls:[],
      bonus:null,
      redo:false,
    }
  },
  computed:{

  },
  methods:{
    onClickRedo() {
      //모든 값을 초기화
      this.winNumbers = getWinNumbers();
      this.winBalls = [];
      this.bonus = null;
      this.redo = false;
      // 초기화 후 다시 공을 뽑는다
      this.showBalls();
    },
    showBalls() {
      // 7개 공
       for (let i =0; i< this.winNumbers.length - 1; i++) {
        timeouts[i] = setTimeout(() => {
          this.winBalls.push(this.winNumbers[i]);
        },(i + 1) * 1000);
      }
      //보너스 공
      timeouts[6] = setTimeout(() => {
        this.bonus = this.winNumbers[6];
        this.redo = true;
      }, 7000)
    },
  },
  mounted() {
    this.showBalls();
  },
  beforeUnmount() {
    timeouts.forEach((t) => {
      clearTimeout(t);
    });
  },
  watch:{

  },
}
</script>

<style>
body{
  padding:0;
  margin:0;
}
div{
  text-align:center;
}
#결과창 {
  margin:20px auto;
}
#보너스창 {
  margin:20px auto;
}
img{
  width:100px;
  height:100px;
}
</style>
