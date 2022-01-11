<template>
  <div>
    <h1>Bingo（vue3）</h1>
    <div class="box">
      <div class="currentNum" v-if="showNumber">{{ currentNum }}</div>
      <div class="message" v-if="showMessage">{{ message }}</div>
    </div>
    <div>
      <button type="button" v-on:click="onclick" v-if="showNumber">
        {{ button }}</button
      >&nbsp;&nbsp;
      <button type="button" v-on:click="reset">Reset</button>
    </div>
    <ul>
      <li class="random" v-for="(item, i) of random" v-bind:key="item">
        {{ random[i] }}
      </li>
    </ul>
  </div>
</template>
<script lang="ts">
import { defineComponent, ref } from "vue";

export default defineComponent({
  setup() {
    //最小値
    let min = ref(1);
    //最大値
    let max = ref(5);
    //表示用
    let currentNum = ref(0);
    //ビンゴ回す用の数値配列
    let bingo: number[] = [];
    //出た数字を入れ込む配列
    let random = ref([0]);
    //スタートストップボタン
    let button = ref("Start");
    //スロットタイマー初期化
    let timer = ref(0);
    //リセット確認用
    let confirmReset = ref(true);
    //数字表示用
    let showNumber = ref(true);
    //メッセージ表示用
    let showMessage = ref(false);
    //終了時メッセージ
    let message = ref("Complete");

    /**
     * 最小値から最大値をスロット用配列に入れる
     */
    const created = () => {
      for (let i = min.value; i <= max.value; i++) {
        bingo.push(i);
      }
      random.value = [];
    };
    created();
    /**
     * ランダムに選ばれた数値を表示し配列に入れる
     */
    const onclick = () => {
      if (bingo.length === 0) {
        showNumber.value = false;
        showMessage.value = true;
      } else if (button.value === "Start") {
        //スロット開始
        timer.value = setInterval(() => {
          //ランダムの数字を選ぶ
          currentNum.value = bingo[Math.floor(Math.random() * bingo.length)];
        }, 25);
        //ボタンの表示を変更
        button.value = "Stop";
      } else {
        //スロットを止める
        clearInterval(timer.value);
        //選ばれた数字を配列に入れる
        random.value.push(currentNum.value);
        //スロット用配列から選ばれた数字を削除
        bingo = bingo.filter((item) => item !== currentNum.value);
        //ボタンの表示を変更
        button.value = "Start";
      }
    };
    /**
     * リセットする
     */
    const reset = () => {
      //リセット確認用メッセージ
      confirmReset.value = window.confirm("リセットしてもよろしいですか？");
      if (confirmReset.value === true) {
        bingo = [];
        random.value = [];
        created();
        showNumber.value = true;
        showMessage.value = false;
        currentNum.value = 0;
        clearInterval(timer.value);
        button.value = "Start";
      } else {
        return;
      }
    };

    return {
      currentNum,
      bingo,
      button,
      random,
      showNumber,
      showMessage,
      message,
      created,
      onclick,
      reset,
    };
  },
});
</script>
<style>
/* div {
  .h1 {
    border: solid;
  }
} */
.box {
  width: 70%;
  border: solid 1px;
  margin: 0 auto;
}
li {
  list-style: none;
}
</style>
