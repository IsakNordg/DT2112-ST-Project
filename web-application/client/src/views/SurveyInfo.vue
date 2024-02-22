<template>
  <div class="container">
    <div class="row clearfix">
      <div class="col-lg-12">
        <div class="card chat-app">
          <div class="chat-info">
            <div
              v-if="$store.state.serverDown === true"
              role="alert"
              aria-live="polite"
              aria-atomic="true"
              class="alert text-center alert-danger"
            >
              Can't connect to the server. Please wait a few minutes and try
              again.
            </div>
            <div v-show = false>
              <!-- audio clip -->
              <audio autoplay class="soundClip">
                <source src="C:\GitHub\DT2112-ST-Project\web-application\client\public\bttb.mp3" type="audio/mpeg">
                Your browser does not support the audio element.
              </audio>
            </div>
            <div class="clickInfo">
              <h1 style="text-align:center;"> 
                Press the space bar when you think the person talking is fearmongering, 
                i.e. taking advantage of the users feelings of fear for rethorical benefit.
              </h1>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import io from "socket.io-client";
import Cookies from "js-cookie";

export default {
  name: "SurveryInfoView",
  components: {},
  beforeRouteLeave(to, from, next) {
    this.socket.disconnect();
    next();
  },
  data: () => ({
    socket: io.connect({
      rejectUnauthorized: false,
    }),
  }),

  mounted() {
    this.socket.on("connect_error", () => {
      console.error("Socket connection error");
      this.$store.state.serverDown = true;
    });
    this.socket.on("connect", () => {
      this.$store.state.serverDown = false;
    });

    if (this.$store.state.serverDown === false) {
      this.socket.on("userIdle", () => {
        this.$store.commit("setAuthenticated", false);
        document.cookie =
          "sessionId=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;";
        this.$store.state.msg = "idleSignout";
        this.$router.push("/signin");
      });
    }

    window.addEventListener("beforeunload", this.signOutUser);
  },
  created() {
    const sessionId = Cookies.get("sessionId");

    document.onmousemove = () => {
      if (this.$store.state.authenticated !== false) {
        this.socket.emit("userNotIdle", sessionId);
      }
    };
    document.onkeydown = () => {
      if (this.$store.state.authenticated !== false) {
        this.socket.emit("userNotIdle", sessionId);
      }
    };
    document.onmousedown = () => {
      if (this.$store.state.authenticated !== false) {
        this.socket.emit("userNotIdle", sessionId);
      }
    };
  },
  methods: {
    redirect(target, version) {
      if (version === "Mike") {
        this.$store.state.version = "gpt_default";
      } else if (version === "Laura") {
        this.$store.state.version = "gpt_extended";
      } else if (version === "Liza") {
        this.$store.state.version = "psychologist";
      }

      this.$router.push(target);
    },

    formatedIndex(index) {
      const botIndex = index + 1;

      let formatedIndex = "";

      if (botIndex === 1) {
        formatedIndex = "first";
      } else if (botIndex === 2) {
        formatedIndex = "second";
      } else if (botIndex === 3) {
        formatedIndex = "third";
      }

      return formatedIndex;
    },

    signOutUser() {
      fetch("/api/signout", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
      });
    },
  },
};
</script>
