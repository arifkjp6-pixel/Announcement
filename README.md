<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Global Announcement App</title>

<meta name="theme-color" content="#111827">

<style>
body{
    margin:0;
    height:100vh;
    display:flex;
    flex-direction:column;
    justify-content:center;
    align-items:center;
    background:linear-gradient(135deg,#0f172a,#1e293b);
    font-family:-apple-system,BlinkMacSystemFont,sans-serif;
    color:white;
    overflow:hidden;
    text-align:center;
}

h1{
    font-size:2rem;
    margin-bottom:20px;
}

.button-group{
    display:flex;
    gap:15px;
    flex-wrap:wrap;
    justify-content:center;
}

button{
    padding:12px 24px;
    border:none;
    border-radius:30px;
    font-size:1rem;
    cursor:pointer;
    background:linear-gradient(45deg,#22d3ee,#3b82f6);
    color:white;
    box-shadow:0 10px 30px rgba(0,0,0,0.5);
    transition:.3s;
}
button:active{ transform:scale(.95); }

/* Notification */
.notification{
    position:fixed;
    top:-200px;
    left:50%;
    transform:translateX(-50%);
    width:92%;
    max-width:420px;
    background:rgba(255,255,255,0.12);
    backdrop-filter:blur(25px);
    border-radius:25px;
    padding:18px 20px;
    box-shadow:0 20px 50px rgba(0,0,0,0.7);
    transition:top .6s cubic-bezier(.17,.67,.83,.67);
    z-index:9999;
}

.notification.show{ top:20px; }

.notification-title{
    font-weight:bold;
    font-size:.95rem;
    margin-bottom:6px;
}

.notification-body{
    font-size:.9rem;
}

/* Bubble */
.bubble{
    position:absolute;
    bottom:0;
    border-radius:50%;
    background:rgba(255,255,255,0.4);
    animation:rise linear forwards;
}

@keyframes rise{
    0%{ transform:translateY(0) scale(1); opacity:1;}
    100%{ transform:translateY(-100vh) scale(2); opacity:0;}
}
</style>
</head>
<body>

<h1>Just wait, the owner will come</h1>

<div class="button-group">
    <button onclick="sendAnnouncement()">📢 Send Global</button>
    <button id="installBtn" style="display:none;">⬇ Install App</button>
</div>

<div class="notification" id="notif">
    <div class="notification-title">🌍 Global Message</div>
    <div class="notification-body" id="notifMessage"></div>
</div>

<audio id="sound">
<source src="https://actions.google.com/sounds/v1/alarms/notification_simple-02.mp3" type="audio/mpeg">
</audio>

<script>
const channel=new BroadcastChannel("global_announcement");

// SEND
function sendAnnouncement(){
    const msg=prompt("Enter Global Message:");
    if(msg && msg.trim()!==""){
        channel.postMessage(msg);
        showNotification(msg);
    }
}

// RECEIVE
channel.onmessage=(event)=>{
    showNotification(event.data);
};

// SHOW NOTIF
function showNotification(message){
    const notif=document.getElementById("notif");
    document.getElementById("notifMessage").innerText=message;
    notif.classList.add("show");
    document.getElementById("sound").play();
    bubbleEffect();

    let duration=2000+(message.length*80);
    if(duration<3000) duration=3000;
    if(duration>15000) duration=15000;

    setTimeout(()=>{
        notif.classList.remove("show");
    },duration);
}

// BUBBLE EFFECT
function bubbleEffect(){
    for(let i=0;i<20;i++){
        const b=document.createElement("div");
        b.className="bubble";
        b.style.left=Math.random()*100+"%";
        b.style.width=b.style.height=(8+Math.random()*20)+"px";
        b.style.animationDuration=(2+Math.random()*3)+"s";
        document.body.appendChild(b);
        setTimeout(()=>b.remove(),5000);
    }
}

// INSTALL BUTTON
let deferredPrompt;
const installBtn=document.getElementById("installBtn");

window.addEventListener("beforeinstallprompt",(e)=>{
    e.preventDefault();
    deferredPrompt=e;
    installBtn.style.display="inline-block";
});

installBtn.addEventListener("click",async()=>{
    if(deferredPrompt){
        deferredPrompt.prompt();
        deferredPrompt=null;
        installBtn.style.display="none";
    }
});
</script>

</body>
</html>
