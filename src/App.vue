<template>
  <div id="app">
    <h1>Parentheses Remover</h1>
    <h2>入力</h2>
    <textarea v-model="input" placeholder="変換元" rows="4" cols="60"></textarea>
    <button type="button" class="btn btn-primary" v-on:click="convert">変換</button>

    <div class="d-flex align-items-center">
      <h2>出力</h2>
      <button type="button" class="btn btn-outline-primary" v-on:click="copy">コピー</button>
    </div>
    <div class="output" v-html="output"></div>
  </div>
</template>

<script>
export default {
  data: function () {
    return {
      input: "",
      output: "",
    }
  },
  methods: {
    convert: function () {
      this.output = this.replace(this.input)
    },
    replace: function ( str ) {
      var parentheses_cnt = 0;
      var replaced = str.split('');
      replaced.splice(0, 0, '')
      for (var i = 0; i < replaced.length; i++) {
        if (replaced[i]==='(' ||  replaced[i]==='（'){
          replaced[i-1] = '<span class="opener opn-'+ String(parentheses_cnt) + '">' + replaced[i-1] + '</span>';
          replaced[i] = '<span class="hide prn-' + String(parentheses_cnt) + '">' + replaced[i];
          parentheses_cnt += 1;
        } else if (replaced[i]===')' ||  replaced[i]==='）'){
          replaced[i] = replaced[i] + '</span>';
        }
      }
      return replaced.join('') 
    },
    open: function() {
      var elm = event.target;
      var parentheses_cnt = elm.className.split(' ')[1].split('-')[1];
      var p_elm = document.getElementsByClassName('prn-'+String(parentheses_cnt))[0];
      p_elm.classList.toggle('hide')
    },
    copy: function() {
      var copyText = this.$el.getElementsByClassName('output')[0].innerText
      navigator.clipboard
        .writeText(copyText)
        .then(() => {
        })
        .catch(e => {
          console.error(e)
        })
    },
  },
  watch: {
    output: function() {
      setTimeout(()=>{
        var openers = document.getElementsByClassName('opener');
        for (var i=0; i<openers.length; i++){
          openers[i].addEventListener('click', this.open, false);
        }
      }, 1000)
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 20px;
  margin-left: 20px;
}

h2 {
  margin: 20px 20px 20px 0;
}

textarea {
  display: block;
  margin-bottom: 10px;
}

.output {
  max-width: 1000px;
  white-space: pre-wrap;
}

[class*="opn-"] {
  background-color: rgba(255,0,0,0.2);
  cursor: pointer;
}

[class*="prn-"] {
  background-color: rgba(0,0,255,0.1);
  padding: 0;
}

.hide {
  display: none;
}
</style>
