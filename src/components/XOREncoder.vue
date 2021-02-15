<template>
  <div class="xorencoder">
      
      

    <form class="absoluteCentre">
      <h2>This is a tool that allows encoding and decoding strings based on the <small>IBM WebSphere XOR Algorithm</small></h2>
    
      <div class="group">      
        <input 
          type="text"
          v-model="decoded"
          @change="encode()" 
          required
        />
        <span class="highlight"></span>
        <span class="bar"></span>
        <label>Decoded</label>
      </div>
        
      <div class="group">      
        <input 
          type="text"
          v-model="encoded"
          @change="decode()" 
          required
        />
        <span class="highlight"></span>
        <span class="bar"></span>
        <label>Encoded</label>
      </div>
      
    </form>
  </div>
</template>

<script>
export default {
  name: 'XOREncoder',
  props: {
    msg: String
  },
  data() {
    return {
      END_OF_INPUT: -1,
      base64Chars: new Array(
        'A','B','C','D','E','F','G','H',
        'I','J','K','L','M','N','O','P',
        'Q','R','S','T','U','V','W','X',
        'Y','Z','a','b','c','d','e','f',
        'g','h','i','j','k','l','m','n',
        'o','p','q','r','s','t','u','v',
        'w','x','y','z','0','1','2','3',
        '4','5','6','7','8','9','+','/'
      ),
      base64Str: '',
      base64Count: '',
      encoded: '',
      decoded: '',
    }
  },
  computed: {
    reverseBase64Chars () {
      let thisArr = new Array();
      for (let i=0; i < this.base64Chars.length; i++){
          thisArr[this.base64Chars[i]] = i;
      }
      return thisArr;
    }
  },
  methods: {
    setBase64Str(str) {
      this.base64Str = str;
      this.base64Count = 0;
    },
    readBase64(){    
      if (!this.base64Str) return this.END_OF_INPUT;
      if (this.base64Count >= this.base64Str.length) return this.END_OF_INPUT;
      let c = this.base64Str.charCodeAt(this.base64Count) & 0xff;
      this.base64Count++;
      return c;
    },
    encodeBase64(str) {
      this.setBase64Str(str);
      let result = '';
      let inBuffer = new Array(3);
      let lineCount = 0;
      let done = false;
      while (!done && (inBuffer[0] = this.readBase64()) != this.END_OF_INPUT){
          inBuffer[1] = this.readBase64();
          inBuffer[2] = this.readBase64();
          result += (this.base64Chars[ inBuffer[0] >> 2 ]);
          if (inBuffer[1] != this.END_OF_INPUT){
              result += (this.base64Chars [(( inBuffer[0] << 4 ) & 0x30) | (inBuffer[1] >> 4) ]);
              if (inBuffer[2] != this.END_OF_INPUT){
                  result += (this.base64Chars [((inBuffer[1] << 2) & 0x3c) | (inBuffer[2] >> 6) ]);
                  result += (this.base64Chars [inBuffer[2] & 0x3F]);
              } else {
                  result += (this.base64Chars [((inBuffer[1] << 2) & 0x3c)]);
                  result += ('=');
                  done = true;
              }
          } else {
              result += (this.base64Chars [(( inBuffer[0] << 4 ) & 0x30)]);
              result += ('=');
              result += ('=');
              done = true;
          }
          lineCount += 4;
          if (lineCount >= 76){
              result += ('\n');
              lineCount = 0;
          }
      }
      return result;
    },
    readReverseBase64() {   
      if (!this.base64Str) return this.END_OF_INPUT;
      for (;;){      
          if (this.base64Count >= this.base64Str.length) return this.END_OF_INPUT;
          let nextCharacter = this.base64Str.charAt(this.base64Count);
          this.base64Count++;
          if (this.reverseBase64Chars[nextCharacter]){
              return this.reverseBase64Chars[nextCharacter];
          }
          if (nextCharacter == 'A') return 0;
      }
      // return this.END_OF_INPUT;
    },
    ntos(n) {
      n=n.toString(16);
      if (n.length == 1) n="0"+n;
      n="%"+n;
      return unescape(n);
    },
    decodeBase64(str) {
      this.setBase64Str(str);
      let result = "";
      let inBuffer = new Array(4);
      let done = false;
      while (!done && (inBuffer[0] = this.readReverseBase64()) != this.END_OF_INPUT
          && (inBuffer[1] = this.readReverseBase64()) != this.END_OF_INPUT){
          inBuffer[2] = this.readReverseBase64();
          inBuffer[3] = this.readReverseBase64();
          result += this.ntos((((inBuffer[0] << 2) & 0xff)| inBuffer[1] >> 4));
          if (inBuffer[2] != this.END_OF_INPUT){
              result +=  this.ntos((((inBuffer[1] << 4) & 0xff)| inBuffer[2] >> 2));
              if (inBuffer[3] != this.END_OF_INPUT){
                  result +=  this.ntos((((inBuffer[2] << 6)  & 0xff) | inBuffer[3]));
              } else {
                  done = true;
              }
          } else {
              done = true;
          }
      }
      return result;
    },
    decode() {
      let s = this.encoded;
      // strip {xor} if existant
      if (s.toUpperCase().substring(0,5)=="{XOR}") {
        s = s.substr(5);
      }
      s = this.decodeBase64(s);
      // XOR each char to ASCII('_') (underscore is 95)
      let r = '';
      for (let i=0; i< s.length; i++) {
        r += String.fromCharCode(s.charCodeAt(i) ^ 95 );
      }
      
      this.decoded = r;
      
    },
    encode() {  
      let s = this.decoded
      // XOR each char to ASCII('_') (underscore is 95)
      let r = '';
      for (let i=0; i< s.length; i++) {
        r += String.fromCharCode(s.charCodeAt(i) ^ 95 );
      
      }  
      r = this.encodeBase64( r );
      
      // add {xor}      
      this.encoded = "{xor}" + r;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
* { box-sizing:border-box; }

/* basic stylings ------------------------------------------ */
body 				 { background:url(https://scotch.io/wp-content/uploads/2014/07/61.jpg); }
.container 		{ 
  font-family:'Roboto';
  width:600px; 
  margin:30px auto 0; 
  display:block; 
  background:#FFF;
  padding:10px 50px 50px;
}
h2 		 { 
  text-align:center; 
  margin-bottom:50px; 
}
h2 small { 
  font-weight:normal; 
  color:#888; 
  display:block; 
}
.footer 	{ text-align:center; }
.footer a  { color:#53B2C8; }

/* form starting stylings ------------------------------- */
.group 			  { 
  position:relative; 
  margin-bottom:45px; 
}
input 				{
  font-size:18px;
  padding:10px 10px 10px 5px;
  display:block;
  width:100%;
  border:none;
  border-bottom:1px solid #757575;
}
input:focus 		{ outline:none; }

/* LABEL ======================================= */
label 				 {
  color:#999; 
  font-size:18px;
  font-weight:normal;
  position:absolute;
  pointer-events:none;
  left:5px;
  top:10px;
  transition:0.2s ease all; 
  -moz-transition:0.2s ease all; 
  -webkit-transition:0.2s ease all;
}

/* active state */
input:focus ~ label, input:valid ~ label 		{
  top:-20px;
  font-size:14px;
  color:#5264AE;
}

/* BOTTOM BARS ================================= */
.bar 	{ position:relative; display:block; width:100%; }
.bar:before, .bar:after 	{
  content:'';
  height:2px; 
  width:0;
  bottom:1px; 
  position:absolute;
  background:#5264AE; 
  transition:0.2s ease all; 
  -moz-transition:0.2s ease all; 
  -webkit-transition:0.2s ease all;
}
.bar:before {
  left:50%;
}
.bar:after {
  right:50%; 
}

/* active state */
input:focus ~ .bar:before, input:focus ~ .bar:after {
  width:50%;
}

/* HIGHLIGHTER ================================== */
.highlight {
  position:absolute;
  height:60%; 
  width:100px; 
  top:25%; 
  left:0;
  pointer-events:none;
  opacity:0.5;
}

/* active state */
input:focus ~ .highlight {
  -webkit-animation:inputHighlighter 0.3s ease;
  -moz-animation:inputHighlighter 0.3s ease;
  animation:inputHighlighter 0.3s ease;
}

/* ANIMATIONS ================ */
@-webkit-keyframes inputHighlighter {
	from { background:#5264AE; }
  to 	{ width:0; background:transparent; }
}
@-moz-keyframes inputHighlighter {
	from { background:#5264AE; }
  to 	{ width:0; background:transparent; }
}
@keyframes inputHighlighter {
	from { background:#5264AE; }
  to 	{ width:0; background:transparent; }
}

.xorencoder {
  display: flex;
  min-height: 100vh;
}
.absoluteCentre {
  margin: auto;
}
</style>
