<template>
  <div class="extension">
    <div class="header">
      <h2>Get Members Groups Facebook</h2>
    </div>
    <div class="container">
      <div v-if="urlTrue">
        <button class="learn-more" @click="getLinks" v-if="!processing">
          <span class="circle" aria-hidden="true">
            <span class="icon arrow"></span>
          </span>
          <span class="button-text">Get Profiles</span>
        </button>
        <div class="load-9" v-if="processing">
          <p>Processing</p>
          <div class="spinner">
            <div class="bubble-1"></div>
            <div class="bubble-2"></div>
          </div>
        </div>
      </div>
      <div v-if="!urlTrue">
        <h2>
          You need to access the group that you have joined in order for the
          tool to work!
        </h2>
      </div>
    </div>
  </div>
</template>

<script>
// const browser = require("webextension-polyfill");
import XLSX from "xlsx";
export default {
  name: "popupView",
  data() {
    return {
      keyword: "",
      urlTrue: false,
      trigger: false,
      processing: false,
      numberProcessing: 0
    };
  },
  mounted() {
    var self = this;
    window.addEventListener("DOMContentLoaded", function () {
      chrome.tabs.query(
        {
          active: true,
          currentWindow: true
        },
        function (tabs) {
          var url = tabs[0].url;
          var checkUrlFb = /https:\/\/www.facebook.com\/groups\/*/;
          if (checkUrlFb.test(url)) {
            self.urlTrue = true;
          }
        }
      );

      chrome.runtime.onMessage.addListener(function (
        message,
        sender,
        sendResponse
      ) {
        if (message.msg === "getStatusTrigger") {
          sendResponse({
            data: self.trigger
          });
        } else if (message.msg === "arrayProfile") {
          var ws = XLSX.utils.json_to_sheet(message.data);
          var wb = XLSX.utils.book_new();
          XLSX.utils.book_append_sheet(wb, ws, "link");
          XLSX.writeFile(wb, "linksProfile.xlsx");
          self.processing = false;
          sendResponse({
            received: true
          });
        } else if (message.msg === "numberProcessing") {
          self.numberProcessing = message.data;
        } else {
          sendResponse("Not found");
        }
      });
    });
  },
  methods: {
    setDOMInfo(res) {
      console.log("setDOMInfo", res);
    },
    getLinks() {
      var self = this;
      this.trigger = true;
      this.processing = true;
      chrome.tabs.query(
        {
          active: true,
          currentWindow: true
        },
        function (tabs) {
          chrome.tabs.sendMessage(
            tabs[0].id,
            { trigger: true },
            self.setDOMInfo
          );
        }
      );
    },
    exportExcel() {
      chrome.tabs.query(
        {
          active: true,
          currentWindow: true
        },
        function (tabs) {
          chrome.tabs.sendMessage(
            tabs[0].id,
            { export: true },
            self.setDOMInfo
          );
        }
      );
    }
  }
};
</script>

<style>
body {
  background: #fff;
  font-family: sans-serif;
  font-size: 0.8rem;
  min-width: 30rem;
}

.header {
  align-items: center;
  height: 4rem;
  display: flex;
  background: #4266b1;

  div {
    text-align: center;
  }

  h2 {
    width: 100%;
    text-align: center;
    color: #fff;
  }
}

.container {
  min-height: 5rem;
  padding: 1.5rem;
}

p {
  font-size: 16px;
}

.extension {
  min-width: 300px;
  text-align: center;
}

.load-wrapp p {
  padding: 0 0 20px;
}

.load-wrapp:last-child {
  margin-right: 0;
}

.l-9 {
  animation-delay: 1.44s;
}

.load-9 .spinner {
  animation: loadingI 2s linear infinite;
}

.load-9 .bubble-1,
.load-9 .bubble-2 {
  animation: bounce 2s ease-in-out infinite;
}

.load-9 .bubble-2 {
  animation-delay: -1s;
}

.spinner {
  position: relative;
  width: 45px;
  height: 45px;
  margin: 0 auto;
}

.bubble-1,
.bubble-2 {
  position: absolute;
  top: 0;
  width: 25px;
  height: 25px;
  border-radius: 100%;
  background-color: #4b9cdb;
}

.bubble-2 {
  top: auto;
  bottom: 0;
}

.load-9 .spinner {
  animation: loadingI 2s linear infinite;
}

.load-9 .bubble-1,
.load-9 .bubble-2 {
  animation: bounce 2s ease-in-out infinite;
}

.load-9 .bubble-2 {
  animation-delay: -1s;
}

.load-9 p {
  font-size: 25px;
  font-family: "Poppins", sans-serif;
  font-weight: 600;
  color: #4b9cdb;
}

@keyframes loadingI {
  100% {
    transform: rotate(360deg);
  }
}

@keyframes bounce {
  0%,
  100% {
    transform: scale(0);
  }

  50% {
    transform: scale(1);
  }
}
* {
  box-sizing: border-box;

  &::before,
  &::after {
    box-sizing: border-box;
  }
}

button {
  position: relative;
  display: inline-block;
  cursor: pointer;
  outline: none;
  border: 0;
  vertical-align: middle;
  text-decoration: none;
  background: transparent;
  padding: 0;
  font-size: inherit;
  font-family: inherit;

  &.learn-more {
    width: 12rem;
    height: auto;

    .circle {
      @include transition(all, 0.45s, cubic-bezier(0.65, 0, 0.076, 1));
      position: relative;
      display: block;
      margin: 0;
      width: 3rem;
      height: 3rem;
      background: #000000;
      border-radius: 1.625rem;

      .icon {
        @include transition(all, 0.45s, cubic-bezier(0.65, 0, 0.076, 1));
        position: absolute;
        top: 0;
        bottom: 0;
        margin: auto;
        background: #fff;

        &.arrow {
          @include transition(all, 0.45s, cubic-bezier(0.65, 0, 0.076, 1));
          left: 1.2rem;
          width: 0.125rem;
          height: 1.125rem;
          background: none;
          transform: rotate(-90deg);
          background: none;

          &::before {
            position: absolute;
            content: "";
            top: 0.45rem;
            left: -0.25rem;
            width: 0.625rem;
            height: 0.625rem;
            border-bottom: 0.125rem solid #fff;
            border-right: 0.125rem solid #fff;
            -webkit-transform: rotate(45deg);
            transform: rotate(45deg);
          }
        }
      }
    }

    .button-text {
      @include transition(all, 0.45s, cubic-bezier(0.65, 0, 0.076, 1));
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      padding: 0.75rem 0;
      margin: 0 0 0 1.85rem;
      color: #000000;
      font-weight: 700;
      line-height: 1.6;
      text-align: center;
      text-transform: uppercase;
    }
  }

  &:hover {
    .circle {
      width: 100%;

      .icon {
        &.arrow {
          background: #fff;
          transform: translate(1rem, 0);
        }
      }
    }

    .button-text {
      color: #fff;
    }
  }
}
@import url("https://fonts.googleapis.com/css?family=Mukta:700");
.main_app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
