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
            <h1 class="page-title">Clip playback</h1>
            <div v-show = false>
              <!-- audio clip -->
              <audio disabled class="soundClip" id="clipPlayer">
                <source src="C:\Users\Joel\Documents\VSCode\DT2112\DT2112-ST-Project\web-application\client\public\bttb.mp3" type="audio/mpeg">
                Your browser does not support the audio element.
              </audio>
              <audio disabled class="soundClip" id="player2">
                <source src="C:\Users\Joel\Documents\VSCode\DT2112\DT2112-ST-Project\web-application\client\public\2.wav" type="audio/wav">
                Your browser does not support the audio element.
              </audio>
              <audio disabled class="soundClip" id="player5">
                <source src="C:\Users\Joel\Documents\VSCode\DT2112\DT2112-ST-Project\web-application\client\public\5.mp3" type="audio/mpeg">
                Your browser does not support the audio element.
              </audio>
              <audio disabled class="soundClip" id="player6">
                <source src="C:\Users\Joel\Documents\VSCode\DT2112\DT2112-ST-Project\web-application\client\public\6.wav" type="audio/wav">
                Your browser does not support the audio element.
              </audio>
              <audio disabled class="soundClip" id="player7">
                <source src="C:\Users\Joel\Documents\VSCode\DT2112\DT2112-ST-Project\web-application\client\public\7.mp3" type="audio/mpeg">
                Your browser does not support the audio element.
              </audio>
              <audio disabled class="soundClip" id="player11">
                <source src="C:\Users\Joel\Documents\VSCode\DT2112\DT2112-ST-Project\web-application\client\public\11.wav" type="audio/wav">
                Your browser does not support the audio element.
              </audio>
              <audio disabled class="soundClip" id="player13">
                <source src="C:\Users\Joel\Documents\VSCode\DT2112\DT2112-ST-Project\web-application\client\public\13.wav" type="audio/wav">
                Your browser does not support the audio element.
              </audio>
              <audio disabled class="soundClip" id="player18">
                <source src="C:\Users\Joel\Documents\VSCode\DT2112\DT2112-ST-Project\web-application\client\public\18.wav" type="audio/wav">
                Your browser does not support the audio element.
              </audio>
              <audio disabled class="soundClip" id="player19">
                <source src="C:\Users\Joel\Documents\VSCode\DT2112\DT2112-ST-Project\web-application\client\public\19.mp3" type="audio/mpeg">
                Your browser does not support the audio element.
              </audio>
              <audio disabled class="soundClip" id="test-player">
                <source src="C:\Users\Joel\Documents\VSCode\DT2112\DT2112-ST-Project\web-application\client\public\test.mp3" type="audio/mpeg">
                Your browser does not support the audio element.
              </audio>
              
            </div>
            <div>
              <button
                type="button"
                class="audio-button"
                @click="
                  stopAll()
                "
              >
                Stop
              </button>
              
            </div>
            <div>
              <button
                type="button"
                class="audio-button"
                @click="
                  play('test-player')
                "
              >
                Test
              </button>
              
            </div>
            <div>
              <button
                v-if = "false"
                type="button"
                class="audio-button"
                @click="
                  play('clipPlayer')
                "
              >
                Bad to the Bone
              </button>
              
            </div>
            <div>
              <button
                type="button"
                class="audio-button"
                @click="
                  play('player2')
                "
              >
                Clip 2
              </button>
              
            </div>
            <div>
              <button
                type="button"
                class="audio-button"
                @click="
                  play('player5')
                "
              >
                Clip 5
              </button>
              
            </div>
            <div>
              <button
                type="button"
                class="audio-button"
                @click="
                  play('player6')
                "
              >
                Clip 6
              </button>
              
            </div>
            <div>
              <button
                type="button"
                class="audio-button"
                @click="
                  play('player7')
                "
              >
                Clip 7
              </button>
              
            </div>
            <div>
              <button
                type="button"
                class="audio-button"
                @click="
                  play('player11')
                "
              >
                Clip 11
              </button>
              
            </div>
            <div>
              <button
                type="button"
                class="audio-button"
                @click="
                  play('player13')
                "
              >
                Clip 13
              </button>
              
            </div>
            <div>
              <button
                type="button"
                class="audio-button"
                @click="
                  play('player18')
                "
              >
                Clip 18
              </button>
              
            </div>
            <div>
              <button
                type="button"
                class="audio-button"
                @click="
                  play('player19')
                "
              >
                Clip 19
              </button>
              
            </div>

            <div>
              <button
                type="button"
                class="audio-button"
                @click="
                  report('group1')
                "
              >
                Group 1
              </button>
              
            </div>
            <div>
              <button
                type="button"
                class="audio-button"
                @click="
                  report('group2')
                "
              >
                Group 2
              </button>
              
            </div>
            <div>
              <button
                type="button"
                class="audio-button"
                @click="
                  report('group3')
                "
              >
                Group 3
              </button>
              
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
  name: "PsychologistChatOverview",
  components: {},
  beforeRouteLeave(to, from, next) {
    this.socket.disconnect();
    next();
  },
  data: () => ({
    socket: io.connect({
      rejectUnauthorized: false,
    }),
    conversations: [],
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
      this.socket.on("psychologistIdle", () => {
        this.$store.commit("setAuthenticated", false);
        this.$store.commit("setAuthenticatedPsychologist", false);
        document.cookie =
          "sessionId=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;";
        this.$store.state.msg = "idleSignout";
        this.$router.push("/psychologist-signin");
      });

      this.socket.on("psychologistConversationsUpdate", () => {
        fetch("/api/load-psychologist-conversations", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
        })
          .then((response) => response.json())
          .then((data) => {
            this.conversations = data;
          })
          .catch((error) => {
            console.error("Error:", error);
          });
      });

      this.socket.on("newMessageFromBot", () => {
        fetch("/api/load-psychologist-conversations", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
        })
          .then((response) => response.json())
          .then((data) => {
            this.conversations = data;
          })
          .catch((error) => {
            console.error("Error:", error);
          });
      });
    }

    window.addEventListener("beforeunload", this.signOutPsychologist);
  },
  created() {
    const sessionId = Cookies.get("sessionId");

    document.onmousemove = () => {
      if (this.$store.state.authenticated !== false) {
        this.socket.emit("psychologistNotIdle", sessionId);
      }
    };
    document.onkeydown = () => {
      if (this.$store.state.authenticated !== false) {
        this.socket.emit("psychologistNotIdle", sessionId);
      }
    };
    document.onmousedown = () => {
      if (this.$store.state.authenticated !== false) {
        this.socket.emit("psychologistNotIdle", sessionId);
      }
    };

    fetch("/api/load-psychologist-conversations", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
    })
      .then((response) => response.json())
      .then((data) => {
        this.conversations = data;
      })
      .catch((error) => {
        console.error("Error:", error);
      });
  },
  methods: {
    redirect(conversationId, userName) {
      this.$router.push({
        path: `/psychologist-chat/${conversationId}/${userName}`,
        params: {
          conversation: conversationId,
          user: userName,
        },
      });
    },

    signOutPsychologist() {
      fetch("/api/psychologist-signout", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
      });
    },

    play(player){

      const now = new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000);

      fetch("/api/sound-played", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          clipId: player,
          time: now,
        }),
      })
        .catch((error) => {
          console.error("Error:", error);
        });
      
      let players = document.getElementsByClassName("soundClip");
      for (let i = 0; i<players.length; i++){
        players[i].pause();
        players[i].currentTime = 0;
      }
      document.getElementById(player).play();
    },

    report(group){

      const now = new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000);

      fetch("/api/sound-played", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          clipId: group,
          time: now,
        }),
      })
        .catch((error) => {
          console.error("Error:", error);
        });
    },

    stopAll(){
      let players = document.getElementsByClassName("soundClip");
      console.log(players);
      for (let i = 0; i<players.length; i++){
        players[i].pause();
        players[i].currentTime = 0;
      }
    }
  },
};
</script>
