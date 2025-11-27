<script setup>
import { ZoomMtg } from '@zoom/meetingsdk';

ZoomMtg.preLoadWasm();
ZoomMtg.prepareWebSDK();

var authEndpoint = 'https://nonresisting-ela-subparalytic.ngrok-free.dev' // 'https://zoom.us/oauth/authorize?response_type=code&client_id=PKwH9lvjSgi9p23IZa7WxQ&redirect_uri=https://nonresisting-ela-subparalytic.ngrok-free.dev'
var meetingNumber = '89991537918'
var passWord = 'Jh58SF'
var role = 0
var userName = 'Vue.js'
var userEmail = ''
var registrantToken = ''
var zakToken = ''
var leaveUrl = 'http://localhost:5173/'

function getSignature() {
  fetch(authEndpoint, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      meetingNumber: meetingNumber,
      role: role,
      videoWebRtcMode: 1,
    })
  }).then((response) => {
    return response.json()
  }).then((data) => {
    console.log(data)
    startMeeting(data.signature)
  }).catch((error) => {
    console.log(error)
  })
}

function startMeeting(signature) {
  document.getElementById('zmmtg-root').style.display = 'block';

  ZoomMtg.init({
    leaveUrl: leaveUrl,
    patchJsMedia: true,
    leaveOnPageUnload: true,
    success: (success) => {
      console.log(success);
      ZoomMtg.join({
        signature: signature,
        meetingNumber: meetingNumber,
        passWord: passWord,
        userName: userName,
        userEmail: userEmail,
        tk: registrantToken,
        zak: zakToken,
        success: (success) => {
          console.log('join', success);
          console.log({sharedarraybuffer: typeof SharedArrayBuffer === 'function'});

          const vbListing = [{
            displayName: 'LoremPicsum',
            fileName: 'lorempicsum.jpg',
            id: 'lorempicsum',
            url: 'https://picsum.photos/1600/900'
          }];

          ZoomMtg.updateVirtualBackgroundList({
            error: function(error) { 
              console.log('updateVirtualBackgroundList error', error) 
            },
            success: function() { 
              console.log('updateVirtualBackgroundList success') 
            },
            vbList: vbListing
          })
        },
        error: (error) => {
          console.log(error);
        }
      });
    },
    error: (error) => {
      console.log(error);
    }
  });
}
</script>

<template>
  <h1>Zoom Meeting SDK Vue.js Sample</h1>

  <button @click='getSignature'>Join Meeting</button>
</template>

<style scoped>

</style>
