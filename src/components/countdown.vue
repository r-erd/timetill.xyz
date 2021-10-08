<template>
  <div v-if="this.unsupportedBrowser">
    <h3>Sorry, Safari doesn't support required features :(</h3>
  </div>

  <div v-if="!this.unsupportedBrowser">
    <div
      v-if="!result"
      style="
        display: block;
        margin-left: auto;
        margin-right: auto;
        margin-bottom: 25px;
        border: 1px solid black;
        width: 200px;
        border-radius: 10px;
        padding: 0;
      "
    >
      <button
        v-bind:class="{ selected: timerUI }"
        @click="switchUI()"
        style="
          border: none;
          border-radius: 8px;
          width: 100px;
          height: 30px;
          margin: 0px;
          padding: 5px;
          cursor: pointer;
        "
      >
        Timer
      </button>

      <button
        v-bind:class="{ selected: !timerUI }"
        @click="switchUI()"
        style="
          border: none;
          border-radius: 8px;
          width: 100px;
          height: 30px;
          margin: 0px;
          padding: 5px;
          cursor: pointer;
        "
      >
        Countdown
      </button>
    </div>
    <div class="site-wrapper">
      <div class="site-wrapper-inner">
        <div class="container">
          <p v-if="error" style="color: #c0392b" class="lead">
            <b>[error: no valid input provided]</b>
          </p>

          <div v-if="timerUI">
            <div v-if="!result">
              <div>
                <input
                  v-model="hr"
                  type="number"
                  placeholder="h"
                  style="
                    border-radius: 3px;
                    border: 0.5mm solid black;
                    color: black;
                    background-color: white;
                    width: 70px;
                    height: 18px;
                    margin: 10px;
                    padding: 5px;
                    cursor: pointer;
                  "
                />

                <input
                  v-model="min"
                  type="number"
                  placeholder="min"
                  style="
                    border-radius: 3px;
                    border: 0.5mm solid black;
                    color: black;
                    background-color: white;
                    width: 70px;
                    height: 18px;
                    margin: 10px;
                    padding: 5px;
                    cursor: pointer;
                  "
                />
                <input
                  v-model="sec"
                  type="number"
                  v-on:keyup.enter="
                    startTimer(Math.floor(hr), Math.floor(min), Math.floor(sec))
                  "
                  placeholder="sec"
                  style="
                    border-radius: 3px;
                    border: 0.5mm solid black;
                    color: black;
                    background-color: white;
                    width: 70px;
                    height: 18px;
                    margin: 10px;
                    padding: 5px;
                    cursor: pointer;
                  "
                />

                <select
                  v-model="unit"
                  style="
                    font-size: 11pt;
                    font-family: sans-serif;
                    border-radius: 3px;
                    border: 0.5mm solid black;
                    cursor: pointer;
                    margin: 10px;
                    height: 31.75px;
                    font-size: 9pt;
                    width: 83.75px;
                    padding: 5px;
                    display: inline;
                  "
                >
                  <option style="font-size: 11pt" value="seconds">
                    seconds
                  </option>
                  <option style="font-size: 11pt" value="minutes">
                    minutes
                  </option>
                  <option style="font-size: 11pt" value="hours">hours</option>
                  <option style="font-size: 11pt" value="days">days</option>
                  <option style="font-size: 11pt" value="">combined</option>
                </select>
              </div>
              <button
                @click="startTimer(Math.floor(hr), Math.floor(min), Math.floor(sec))"
                style="
                  border-radius: 3px;
                  border: 0.5mm solid black;
                  color: white;
                  background-color: black;
                  width: 100px;
                  height: 30px;
                  margin: 10px;
                  margin-top: 20px;
                  padding: 5px;
                  cursor: pointer;
                "
              >
                Start
              </button>
            </div>
          </div>

          <div v-if="!timerUI && !result">
            <div style="margin: 0">
              <p style="display: inline; margin: 10px; height: 30px">
                count down until
              </p>
              <input
                v-model="datey"
                style="
                  width: 135px;
                  font-size: 11pt;
                  font-family: sans-serif;
                  border: none;
                  cursor: pointer;
                  display: inline;
                  margin: 5px;
                  height: 30px;
                "
                type="date"
              />
              <input
                v-model="timey"
                style="
                  font-size: 11pt;
                  font-family: sans-serif;
                  border: none;
                  cursor: pointer;
                  display: inline;
                  margin: 5px;
                  height: 30px;
                "
                type="time"
              />
              <p style="display: inline">in</p>
              <select
                v-model="unit"
                style="
                  font-size: 11pt;
                  font-family: sans-serif;
                  border: none;
                  cursor: pointer;
                  margin: 5px;
                  height: 30px;
                  padding: 5px;
                  display: inline;
                "
              >
                <option style="font-size: 11pt" value="seconds">seconds</option>
                <option style="font-size: 11pt" value="minutes">minutes</option>
                <option style="font-size: 11pt" value="hours">hours</option>
                <option style="font-size: 11pt" value="days">days</option>
                <option style="font-size: 11pt" value="">combined</option>
              </select>
            </div>
            <br />
            <button
              @click="startCountdown(datey, timey)"
              style="
                border-radius: 3px;
                border: 0.5mm solid black;
                color: white;
                background-color: black;
                width: 100px;
                height: 30px;
                margin: 10px;
                padding: 5px;
                cursor: pointer;
              "
            >
              Start
            </button>
          </div>

          <div v-if="result">
            <p style="margin: 0; font-size: 10pt">
              <b> {{ inputDate }} </b>
            </p>
            <button
              @click="cancel()"
              class="btn-default"
              style="font-size: 8pt; margin-bottom: 50px"
            >
              cancel
            </button>
            <p>
              <span style="font-size: 40pt; margin: 0"
                >{{ message.split(".")[0].trim() }}.</span
              ><span style="font-size: 20pt; margin: 0; text-align: left">
                {{ message.split(".")[1] }}</span
              >
            </p>
            <button
              v-if="!notVisualizable"
              @click="visual = !visual"
              class="btn-default"
              style="font-size: 8pt; margin-bottom: 50px"
            >
              visualize
            </button>
          </div>

          <transition name="fade">
            <div v-show="visual && result && !notVisualizable">
              <canvas
                ref="canvas"
                height="1000"
                width="2000"
                style="width: 80vmin; height: 40vmin"
              />
            </div>
          </transition>

          <!-- <p v-show="result && notVisualizable && visual"> Sadly too long to visualize this way </p> -->
        </div>
      </div>
    </div>
    <footer>
      <a
        style="text-decoration: none; color: grey"
        href="https://github.com/r-erd"
        target="_blank"
      >
        <p
          style="
            font-family: sans-serif;
            font-size: 8pt;
            text-align: center;
            vertical-align: bottom;
            margin-top: 2vh;
            width: 100%;
            color: grey;
            text-decoration: none;
          "
        >
          made by Robin Erd
        </p>
      </a>
    </footer>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },

  created() {},
  mounted() {
    if (
      navigator.userAgent.indexOf("Safari") != -1 &&
      navigator.userAgent.indexOf("Chrome") == -1
    ) {
      this.unsupportedBrowser = true;
    }
    this.canvas = this.$refs.canvas.getContext("2d");
    if (
      this.$cookie.isCookieAvailable("countdown-date") &&
      this.$cookie.isCookieAvailable("countdown-unit")
    ) {
      this.result = true;
      this.visual = true;
      this.unit = this.$cookie.getCookie("countdown-unit");
      this.inputDate = new Date(this.$cookie.getCookie("countdown-date"));
      this.drawCanvas();
      this.displayCountdown();
    }
  },
  data() {
    return {
      x: 0,
      y: 0,
      timerUI: false,
      countdownUI: true,
      hr: undefined,
      min: undefined,
      sec: undefined,
      result: false,
      error: false,
      unsupportedBrowser: false,
      visual: false,
      notVisualizable: false,
      side: 10,
      margin: 8,
      squares: 0,
      rectangles: [],
      canvas: null,
      unit: "seconds",
      datey: undefined,
      timey: "00:00",
      inputDate: Date,
      message: "",
      canvasTimer: undefined,
      digitTimer: undefined,
    };
  },
  methods: {
    switchUI() {
      if (this.result == false) {
        this.timerUI = !this.timerUI;
      }
    },

    refresh() {
      let diff = this.inputDate - new Date()
      let secDiff = diff / 1000;
      if (diff < 1) {
        this.visual = false;
        this.notVisualizable = true;
      }

      switch (this.unit) {
        case "seconds":
          this.message = secDiff.toFixed(2);
          break;
        case "minutes":
          this.message = (secDiff / 60).toFixed(5);
          break;
        case "hours":
          this.message = (secDiff / 60 / 60).toFixed(5);
          break;
        case "days":
          this.message = (secDiff / 60 / 60 / 24).toFixed(5);
          break;
        case "":
          this.message =
            (secDiff / 60 / 60).toFixed(0) +
            ":" +
            ((secDiff / 60) % 60).toFixed(0) +
            ":" +
            (secDiff % 60).toFixed(0);
          break;
      }
    },

    removeSquare() {
      let rect = this.rectangles.pop();
      if (rect != undefined) {
        this.canvas.clearRect(rect.x, rect.y, this.side, this.side);
      }
    },

    displayCountdown() {
      this.error = false;
      this.result = true;
      this.digitTimer = setInterval(this.refresh, 50);
      this.$cookie.setCookie("countdown-date", this.inputDate);
      this.$cookie.setCookie("countdown-unit", this.unit);
    },
    cancel() {
      this.result = false;
      this.visual = false;
      this.canvas.clearRect(
        0,
        0,
        this.$refs.canvas.width,
        this.$refs.canvas.height
      );
      clearInterval(this.digitTimer);
      clearInterval(this.canvasTimer);
      this.$cookie.removeCookie("countdown-date");
    },
    startTimer(hr, min, sec) {
      let today = new Date()
      if (isNaN(hr)){
        hr = 0
      }
      if (isNaN(min)){
        min = 0
      }
      if (isNaN(sec)){
        sec = 0
      }
      today.setHours(today.getHours() + hr, today.getMinutes() + min, today.getSeconds() + sec)
      var tzoffset = (new Date()).getTimezoneOffset() * 60000;
      var localISOTime = (new Date(today.getTime()- tzoffset)).toISOString().slice(0, -1)

      this.inputDate = new Date(localISOTime.substring(0,10) + " " + localISOTime.substring(11,19));
      this.displayCountdown();
      this.drawCanvas();
    },
    startCountdown(datestring, timestring) {
      if (datestring == undefined) {
        var tzoffset = (new Date()).getTimezoneOffset() * 60000;
        var localISOTime = (new Date(new Date().getTime()- tzoffset)).toISOString().slice(0, -1)
        datestring = localISOTime.substring(0,10)
      }
      if (timestring == undefined) {
        timestring = "00:00";
      }

      this.inputDate = new Date(datestring + " " + timestring);
      this.displayCountdown();
      this.drawCanvas();
    },
    drawCanvas() {
      let diff = this.inputDate - Date.now();
      let secDiff = diff / 1000;
      let squares;

      switch (this.unit) {
        case "seconds":
          squares = secDiff;
          break;
        case "minutes":
          squares = secDiff / 60;
          break;
        case "hours":
          squares = secDiff / 60 / 60;
          break;
        case "days":
          squares = secDiff / 60 / 60 / 24;
          break;
        case "":
          squares = secDiff / 60 / 60;
          break;
      }

      //1000 is canvas height, half the width!!
      this.side = Math.floor(1000 / Math.sqrt(squares) - this.margin);

      let lineCapacity = Math.floor(
        this.$refs.canvas.width / (this.side + this.margin)
      );
      let lines = Math.floor(squares / lineCapacity);
      let leftover = Math.floor(squares % lineCapacity);

      if (
        squares >=
          Math.floor(this.$refs.canvas.width / (this.side + this.margin)) *
            Math.floor(this.$refs.canvas.height / (this.side + this.margin)) ||
        secDiff < 0 ||
        this.side < 2
      ) {
        this.notVisualizable = true;
      } else {
        for (let i = 0; i < lines + 1; i++) {
          let squaresInRow;
          if (i == lines) {
            squaresInRow = leftover;
          } else {
            squaresInRow = lineCapacity;
          }
          for (let j = 0; j < squaresInRow; j++) {
            this.canvas.fillRect(
              this.x + j * (this.side + this.margin),
              this.y + i * (this.side + this.margin),
              this.side,
              this.side
            );
            this.rectangles.push({
              x: this.x + j * (this.side + this.margin),
              y: this.y + i * (this.side + this.margin),
            });
          }
        }
        this.notVisualizable = false;

        let squareTime;
        switch (this.unit) {
          case "seconds":
            squareTime = 1000;
            break;
          case "minutes":
            squareTime = 1000 * 60;
            break;
          case "hours":
            squareTime = 1000 * 60 * 60;
            break;
          case "days":
            squareTime = 1000 * 60 * 60 * 24;
            break;
          case "":
            squareTime = 1000 * 60 * 60;
            break;
        }
        this.canvasTimer = setInterval(this.removeSquare, squareTime);
      }
    },
  },
};
</script>

<style scoped>
canvas {
  border: none;
}

button {
  background-color: white;
}

.selected {
  background-color: black;
  color: white;
}

.btn-default {
  cursor: pointer;
  text-decoration: none;
  color: grey;
  text-shadow: none; /* Prevent inheritence from `body` */
  background-color: white;
  border: none;
}
.btn-default:hover {
  cursor: pointer;
  text-decoration: underline;
  color: grey;
  text-shadow: none; /* Prevent inheritence from `body` */
  background-color: white;
  border: none;
}

html,
body {
  height: 100%;
  background-color: #fff;
  color: #fff;
  text-align: center;
  text-shadow: 0 1px 3px rgba(0, 0, 0, 0.5);
}

p {
  color: black;
  font-family: sans-serif;
}

.site-wrapper {
  display: table;
  width: 100%;
  height: 600px;
  background-color: white;
}
.site-wrapper-inner {
  display: table-cell;
  vertical-align: top;
}

.inner {
  padding: 30px;
}

@media (min-width: 768px) {
  .site-wrapper-inner {
    vertical-align: middle;
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
