<template>
  <!-- <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp > 16 ?
    'warm' : ''"> -->
  <div id="app" :class="typeof weather.main != 'undefined' && weather.weather[0].main == 'Clear' ?
    'warm' : ''">
    <p id="log"></p>
    <main class="cover-container d-flex w-100 h-100 p-3 mx-auto flex-column">
      <div class="search-box">
        <header class="masthead mb-auto">
          <div class="inner">
            <h1 class="masthead-brand">sunnY Vue</h1>
            <nav class="nav nav-masthead justify-content-center">
              <a class="nav-link active" href="#">Home</a>
              <a class="nav-link" href="#">Features</a>
              <a class="nav-link" href="#">Contact</a>
            </nav>
          </div>
        </header>
        <input type="text" 
               class="search-bar col-auto" 
               placeholder="現在晴れている国や地域を入力してみてね（※英語で）" 
               v-model="query"
               @keypress="fetchWeather"
               />
      </div>

      <div class="weather-rap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>
        <div class="weather-box">
          <transition enter-active-class="animate__animated animate__flipInY" leave-active-class="animate__animated animate__zoomOutUp" appear>
            <!-- <div>
              <p class="clear" v-if="weather.weather[0].main == 'Clear'">晴れてるよー!外に出ていこう！！</p>
              <p class="clouds" v-else-if="weather.weather[0].main == 'Clouds'">曇りだよー</p>
              <p class="rain" v-else-if="weather.weather[0].main == 'Rain'">雨！！</p>
            </div> -->
            <p :class="changeClass()">{{ changeText() }}</p>
          </transition>
          <div :class="typeof weather.main != 'undefined' && weather.main.temp > 16 ?
                'red' : 'temp'">{{ Math.round(weather.main.temp) }}℃</div>
          <div class="weather">{{ weather.weather[0].main }}</div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      api_key: 'b6feaa4991fa4fd5d93c8a397fd36ea2', 
      url_base: 'https://api.openweathermap.org/data/2.5/',
      // ,'http://api.openweathermap.org/data/2.5/weather'
      query: '',
      weather: {}
    }
  },
  methods: {
    fetchWeather(e) {
      if(e.key == "Enter") {
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
          .then(res => {
            return res.json();
          }).then(this.setResults);
      }
    },
    setResults(results) {
      this.weather = results;
    },
    dateBuilder() {
      let d = new Date();
      let months = ["1月", "2月", "3月", "4月", "5月", "6月", "7月", "8月", "9月", "10月", "11月", "12月"];
      let days = ["日曜日", "月曜日", "火曜日", "水曜日", "木曜日", "金曜日", "土曜日"];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${year}年 ${month} ${date}日 ${day}`;
    },
    changeText() {
      let result;
      if (this.weather.weather[0].main == 'Clear') {
        result = `大正解！！晴れてるよー!`;
      } else if (this.weather.weather[0].main == 'Clouds') {
        result = 'ざんねん〜曇りだよ';
      } else if (this.weather.weather[0].main == 'Rain') {
        result = '雨だねー';
      } else {
        result = '';
      }
      return result;
    },
    changeClass() {
      let result;
      if (this.weather.weather[0].main == 'Clear') {
        result = 'clear';
      } else if (this.weather.weather[0].main == 'Clouds') {
        result = 'clouds';
      } else if (this.weather.weather[0].main == 'Rain') {
        result = 'rain';
      } else {
        result = '';
      }
      return result;
    }
  }
}

const totalTime = 15000; // 10秒
const oldTime = Date.now();

const timerId = setInterval(() => {
  const currentTime = Date.now();
  // 差分を求める
  const diff = currentTime - oldTime;

  // 残りミリ秒を計算する
  const remainMSec = totalTime - diff;
  // ミリ秒を整数の秒数に変換する
  const remainSec = Math.ceil(remainMSec / 1000);

  let label = `残り${remainSec}秒`;

  // 0秒以下になったら
  if (remainMSec <= 0) {
    // タイマーを終了する
    clearInterval(timerId);

    // タイマー終了の文言を表示
    label = '終了!';
  }

  // 画面に表示する
  document.querySelector('#log').innerHTML = label;
}, 1000);

</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'montserrat', sans-serif;
}

#app {
  background-image: url('./assets/samui-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

#app.warm {
  background-image: url('./assets/sunny-bg.jpg');
  animation-name: pulse;
  animation-duration: 0.24s;
}

main {
  min-height: 100vh;
  padding: 25px;
  border-radius: 70px;
  width: 100%;
	/* max-width: 768px; */
  max-width: 600px;
	margin: 0 auto;
  padding: 0 15px 15px;
  box-sizing: border-box;
  background-image: linear-gradient(to bottom, rgba(223, 31, 31, 0.25), rgba(31, 199, 45, 0.534));
  box-shadow: 0 10px 25px 0 rgba(94, 195, 226, 0.897), inset 0 10px 25px 0 rgba(191, 108, 212, 0.788);
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 700px;
  padding: 15px;
  margin: auto;

  color: #313131;
  font-size: 20px;

  appearance: none;
  border: none;
  outline: none;
  background: none;

  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}

.location-box .location {
  color: #FFF;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #FFF;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}

.weather-box {
  text-align: center;
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #FFF;
  font-size: 102px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: #FFF;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.red {
  display: inline-block;
  padding: 10px 25px;
  font-size: 102px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  color: #fdbf64;
}

p {
  color: #FFF;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.clouds {
  animation-name: heartBeat;
  animation-duration: 2s;
  color: aqua;
}

.clear {
  animation-name: flash;
  animation-duration: 1s;
  color: orange;
}

.rain {
  animation-name: backInUp;
  animation-duration: 1s;
  color: violet;
  font-size: 60px;
}

h1 {
  text-shadow: 3px 7px rgba(216, 54, 54, 0.25);
  color: rgba(62, 248, 146, 0.644);
  
}

nav a {
  text-shadow: 1px 3px rgba(28, 148, 204, 0.568);
  color: #f4f2f8d7;
  font-weight: bold;
}

#log {
      font-size: 60px;
    }
</style>
