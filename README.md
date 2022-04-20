<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Joniwan Video Meet</title>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <script src="https://unpkg.com/peerjs@1.3.1/dist/peerjs.min.js"></script>
    <link rel="stylesheet" href="style.css" />
  </head>
<body>
    <!-- App code -->
<style type="text/css">
#live {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  width: 100%;
  height: 100%;
  background-color: #000;
  display: none;
}
#local-video {
  /*position: absolute;  
  bottom: 0;  
  left: 0;
  width: 250px;
  -webkit-transform: scaleX(-1);
  transform: scaleX(-1);
  margin: 16px;
  border: 2px solid #fff;
  */
  position: absolute;  
  bottom: 0;  
  left: 0;
  width: 20%;
  -webkit-transform: scaleX(-1);
  transform: scaleX(-1);
  margin: 16px;
  border: 2px solid #fff;
}
#canvas {
  /*position: absolute;  
  top: 0;  
  left: 0;
  width: 100px;
  -webkit-transform: scaleX(-1);
  transform: scaleX(-1);
  margin: 16px;
  border: 2px solid #fff;
  */
  position: absolute;  
  top: 0;  
  right: 0;  
  width : 15%;  
}
#snap {
  position: absolute;
  top: 0;
  right: 0;
  padding: 8px;
  background-color: transparent;
  color: white;
  border: none;  
}
#remote-video {
  /*position: absolute;
  max-width: 100%;
  height: 100%;  
  top: 50%;
  left: 50%;  
  bottom: 0;
  left: 0;
  transform: translate(-50%, -50%);
  */
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;  
  bottom: 0;
  left: 0;
  transform: translate(0%, 0%);
}
#end-call {
  position: absolute;
  bottom: 0;
  left: 0;
  padding: 8px;
  background-color: red;
  color: white;
  border: none;
  margin: 16px;
}
}

</style>
<script src="main.js"></script>
<script>
    window.addEventListener('load',(event)=>{
        
        var tech = getURLParam('undangan');
        if (tech!=undefined)
        {
            document.querySelector("input").value= tech;
        }       
        
    })
    

    const peer = new Peer();
    var currentCall;
    peer.on("open", function (id) {
      document.getElementById("uuid").textContent = id;
    });

    async function callUser() {
      // get the id entered by the user
      const peerId = document.querySelector("input").value;
    // grab the camera and mic
      const stream = await navigator.mediaDevices.getUserMedia({
        video: true,
        audio: true,
      });
    // switch to the video call and play the camera preview
      document.getElementById("menu").style.display = "none";
      document.getElementById("live").style.display = "block";
      document.getElementById("local-video").srcObject = stream;
      document.getElementById("local-video").play();

      //2022
      setTimeout(snap(), 3000);
    // make the call
      const call = peer.call(peerId, stream);
      call.on("stream", (stream) => {
        document.getElementById("remote-video").srcObject = stream;
        document.getElementById("remote-video").play();
      });
      call.on("data", (stream) => {
        document.querySelector("#remote-video").srcObject = stream;
      });
      call.on("error", (err) => {
        console.log(err);
      });
      call.on('close', () => {
        endCall()
      })
    // save the close function
      currentCall = call;
    }


    peer.on("call", (call) => {
      if (confirm(`Accept call from ${call.peer}?`)) {
        // grab the camera and mic
        navigator.mediaDevices
          .getUserMedia({ video: true, audio: true })
          .then((stream) => {
            // play the local preview
            document.querySelector("#local-video").srcObject = stream;
            document.querySelector("#local-video").play();
    // answer the call
            call.answer(stream);
    // save the close function
            currentCall = call;
    // change to the video view
            document.querySelector("#menu").style.display = "none";
            document.querySelector("#live").style.display = "block";
            call.on("stream", (remoteStream) => {
              // when we receive the remote stream, play it
              document.getElementById("remote-video").srcObject = remoteStream;
              document.getElementById("remote-video").play();
			  
			  //alert("Camera Will");

            });
          })
          .catch((err) => {
            console.log("Failed to get local stream:", err);
          });
      } else {
        // user rejected the call, close it
        call.close();
      }
    });

    function endCall() {
      // Go back to the menu
      document.querySelector("#menu").style.display = "block";
      document.querySelector("#live").style.display = "none";
    // If there is no current call, return
      if (!currentCall) return;
    // Close the call, and reset the function
      try {
        currentCall.close();
      } catch {}
      currentCall = undefined;
    }


</script>

<div id="menu">
  <p>Your ID:  </p>
  <p id="uuid">      
  </p>
  <p>
      <button onclick="share()" class="mobileShow">
        Share to whatsapp
        </button>
  </p>
  <input type="text" placeholder="Peer id" />
  <button onclick="callUser()">Connect</button>
</div>


  
    <script src=
"https://cdnjs.cloudflare.com/ajax/libs/jquery/3.2.1/jquery.js">
    </script>
      
    <script type="text/javascript">
          
        // Function to share on whatsapp
        function share() {
  
            // Getting user input
            var message = "Teman Anda Joniwan, Sedang Mengundang Anda Untuk Video Meet di %0A%0A https://gmeet.pakerelay.com/?undangan="+document.getElementById("uuid").textContent+"%0A%0Adengan Kode Undangan";

  
            // Opening URL
            window.open(
                "https://api.whatsapp.com/send?text=" + message,
  
                // This is what makes it 
                // open in a new window.
                '_blank' 
            );
        }
        function getURLParam(sParam){

            var sPageURL = window.location.search.substring(1);
            var sURLVariables = sPageURL.split('&');
           for (var i = 0; i < sURLVariables.length; i++) 
            {
                var sParameterName = sURLVariables[i].split('=');
                if (sParameterName[0] == sParam) 
                {
                    return sParameterName[1];
                }
            }
        }
        
    </script>


<div id="live">
  <video id="remote-video"></video>
  <video id="local-video" muted="true"></video>
  <button id="end-call" onclick="endCall()">End Call</button>
  <canvas id="canvas" width="640" height="480"></canvas>
  <button id="snap" onclick="snap()"></button>
  <script type="text/javascript">
       // Set the date we're counting down to
        var besok = new Date();
        besok.setDate(besok.getDate() + 1);

        var countDownDate = new Date(besok).getTime();

        // Update the count down every 1 second
        var x = setInterval(function() {

          // Get today's date and time
          var now = new Date().getTime();
            
          // Find the distance between now and the count down date
          var distance = countDownDate - now;
            
          // Time calculations for days, hours, minutes and seconds
          var days = Math.floor(distance / (1000 * 60 * 60 * 24));
          var hours = Math.floor((distance % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
          var minutes = Math.floor((distance % (1000 * 60 * 60)) / (1000 * 60));
          var seconds = Math.floor((distance % (1000 * 60)) / 1000);
            
          // Output the result in an element with id="demo"
          //document.getElementById("demo").innerHTML = days + "d " + hours + "h "
          //+ minutes + "m " + seconds + "s ";
            
             
            document.querySelector('#snap').innerHTML = (seconds % 5)+1;
            if ((seconds % 5)+1 == 1) 
            {  
                snap();
            }

          // If the count down is over, write some text 
          if (distance < 0) {
            clearInterval(x);
            document.querySelector('#snap').innerHTML = "EXPIRED";
          }
        }, 1000);

  </script>
</div>

   
  

  <!-- start the script ... within that declare variables as follows... -->
  <script>
  var video=document.getElementById("remote-video");
  var canvas=document.querySelector('canvas');
  var context=canvas.getContext('2d');
  var w,h,ratio;

  //add loadedmetadata which will helps to identify video attributes

  video.addEventListener('loadedmetadata', function() {
    ratio = video.videoWidth/video.videoHeight;
    w = video.videoWidth-100;
    //w = video.videoWidth;
    h = parseInt(w/ratio,10);
    //h = video.videoHeight
    canvas.width = w;
    canvas.height = h;
  },false);

  function snap() {
    context.fillRect(0,0,w,h);
    context.drawImage(video,0,0,w,h);
  }



  </script>


  </body>
</html>
