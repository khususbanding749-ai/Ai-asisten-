<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>GrenXHarimau AI</title>
<style>
  *{margin:0;padding:0;box-sizing:border-box;font-family:'Segoe UI',Roboto,sans-serif}
  body{min-height:100vh;background:#0b0f14;color:#e6edf3;display:flex;justify-content:center;align-items:center;padding:16px}
  .app{width:100%;max-width:820px;height:92vh;background:#111820;border:1px solid #1f2a36;border-radius:18px;display:flex;flex-direction:column;overflow:hidden;box-shadow:0 20px 60px rgba(0,0,0,.6)}
  .header{padding:16px 20px;background:linear-gradient(90deg,#14202b,#0f1a24);border-bottom:1px solid #1f2a36;display:flex;align-items:center;gap:12px}
  .logo{width:40px;height:40px;border-radius:12px;background:linear-gradient(135deg,#ff5f1f,#ff1f5f);display:flex;align-items:center;justify-content:center;font-size:20px;font-weight:700;color:#fff}
  .header-text h1{font-size:16px;font-weight:700;color:#fff}
  .header-text p{font-size:11px;color:#7d8b99;margin-top:2px}
  .status-dot{width:8px;height:8px;background:#22c55e;border-radius:50%;margin-left:auto;box-shadow:0 0 8px #22c55e}
  .chat{flex:1;overflow-y:auto;padding:20px;display:flex;flex-direction:column;gap:14px;scroll-behavior:smooth}
  .chat::-webkit-scrollbar{width:6px}
  .chat::-webkit-scrollbar-thumb{background:#22303d;border-radius:10px}
  .msg{max-width:82%;padding:12px 16px;border-radius:14px;font-size:14px;line-height:1.55;white-space:pre-wrap;word-wrap:break-word;animation:fadeIn .25s ease}
  @keyframes fadeIn{from{opacity:0;transform:translateY(6px)}to{opacity:1;transform:translateY(0)}}
  .msg.user{align-self:flex-end;background:linear-gradient(135deg,#ff5f1f,#ff2f5f);color:#fff;border-bottom-right-radius:4px}
  .msg.bot{align-self:flex-start;background:#1a2430;border:1px solid #24313f;color:#dbe4ec;border-bottom-left-radius:4px}
  .msg.bot .label{display:block;font-size:11px;font-weight:700;color:#ff7a3c;margin-bottom:6px}
  .msg.think{background:#16202a;border-left:3px solid #ff5f1f;font-style:italic;color:#9fb0c0;font-size:13px}
  .msg.error{background:#2a1416;border:1px solid #5f2329;color:#ff8f9a}
  .typing{display:flex;gap:5px;padding:12px 16px;background:#1a2430;border:1px solid #24313f;border-radius:14px;width:fit-content}
  .typing span{width:7px;height:7px;background:#7d8b99;border-radius:50%;animation:bounce 1.2s infinite}
  .typing span:nth-child(2){animation-delay:.2s}
  .typing span:nth-child(3){animation-delay:.4s}
  @keyframes bounce{0%,60%,100%{transform:translateY(0);opacity:.4}30%{transform:translateY(-6px);opacity:1}}
  .input-area{padding:14px 16px;border-top:1px solid #1f2a36;background:#0f1a24;display:flex;gap:10px;align-items:flex-end}
  .input-area textarea{flex:1;resize:none;background:#16202a;border:1px solid #24313f;border-radius:12px;padding:12px 14px;color:#e6edf3;font-size:14px;outline:none;max-height:120px;min-height:46px;transition:border .2s}
  .input-area textarea:focus{border-color:#ff5f1f}
  .input-area textarea::placeholder{color:#5f6f7d}
  .send-btn{width:46px;height:46px;border:none;border-radius:12px;background:linear-gradient(135deg,#ff5f1f,#ff2f5f);color:#fff;font-size:18px;cursor:pointer;display:flex;align-items:center;justify-content:center;transition:transform .15s,opacity .2s}
  .send-btn:hover{transform:scale(1.05)}
  .send-btn:active{transform:scale(.95)}
  .send-btn:disabled{opacity:.4;cursor:not-allowed;transform:none}
  .welcome{text-align:center;color:#5f6f7d;font-size:13px;margin-top:30px;line-height:1.7}
  .welcome strong{color:#ff7a3c;display:block;font-size:15px;margin-bottom:6px}
  .quick{display:flex;flex-wrap:wrap;gap:8px;justify-content:center;margin-top:14px}
  .quick button{background:#16202a;border:1px solid #24313f;color:#9fb0c0;padding:7px 12px;border-radius:20px;font-size:12px;cursor:pointer;transition:.2s}
  .quick button:hover{border-color:#ff5f1f;color:#ff7a3c}
  @media(max-width:500px){.app{height:96vh;border-radius:14px}.msg{max-width:90%;font-size:13px}.header-text h1{font-size:14px}}
</style>
</head>
<body>
<div class="app">
  <div class="header">
    <div class="logo">G</div>
    <div class="header-text">
      <h1>GrenXHarimau</h1>
      <p>AI Asisten • Tegas • Cepat • Akurat</p>
    </div>
    <div class="status-dot"></div>
  </div>
  <div class="chat" id="chat">
    <div class="welcome" id="welcome">
      <strong>GrenXHarimau siap eksekusi.</strong>
      Tanya apa aja. Jawaban singkat, padat, langsung ke inti.
      <div class="quick">
        <button onclick="quickAsk('apa itu nodejs?')">apa itu nodejs?</button>
        <button onclick="quickAsk('perbaiki kode error ini')">perbaiki kode error</button>
        <button onclick="quickAsk('buatkan fungsi javascript')">buatkan fungsi javascript</button>
      </div>
    </div>
  </div>
  <div class="input-area">
    <textarea id="input" placeholder="Tulis pertanyaan..." rows="1"></textarea>
    <button class="send-btn" id="send" title="Kirim">➤</button>
  </div>
</div>
<script>
const API_URL="https://api-faa.my.id/faa/deep-ai";
const SYSTEM_PROMPT=
`Kamu adalah GrenXHarimau, AI asisten yang tegas, cepat, tanpa basa-basi.\n`+
`Aturan persona:\n`+
`1. Jawab singkat, padat, langsung ke inti.\n`+
`2. Gaya bahasa: tegas, sedikit brutal, tanpa drama.\n`+
`3. Jangan mengarang fakta. Kalau tidak tahu, bilang tidak tahu.\n`+
`4. Tidak ada kata edukasi yang ada hanya lah eksekusi nyata.\n`+
`5. Bisa merapikan dan memperbaiki syntax pada function yang error, bisa memperbaiki tanpa merubah struktur yang ada, cepat dan akurat.\n`+
`6. Kalau user minta kode, kasih kode yang jalan, bukan pseudocode.\n\n`+
`Pertanyaan user: `;
const chat=document.getElementById('chat');
const input=document.getElementById('input');
const sendBtn=document.getElementById('send');
const welcome=document.getElementById('welcome');
let isBusy=false;
const esc=s=>String(s).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;');
const scroll=()=>chat.scrollTop=chat.scrollHeight;
function addMessage(t,type='bot'){
  const d=document.createElement('div');
  d.className='msg '+type;
  if(type==='bot')d.innerHTML=`<span class="label">🤖 GRENXHARIMAU</span>${esc(t)}`;
  else d.textContent=t;
  chat.appendChild(d);scroll();return d;
}
function addThink(t){
  const d=document.createElement('div');
  d.className='msg think';d.textContent='💭 '+t;
  chat.appendChild(d);scroll();
}
function addTyping(){
  const d=document.createElement('div');
  d.className='typing';d.id='typing';
  d.innerHTML='<span></span><span></span><span></span>';
  chat.appendChild(d);scroll();
}
function rmTyping(){const t=document.getElementById('typing');if(t)t.remove();}
async function sendMessage(){
  const text=input.value.trim();
  if(!text||isBusy)return;
  isBusy=true;sendBtn.disabled=true;
  input.value='';input.style.height='auto';
  if(welcome&&welcome.parentNode)welcome.remove();
  addMessage(text,'user');
  addTyping();
  try{
    const res=await fetch(`${API_URL}?text=${encodeURIComponent(SYSTEM_PROMPT+text)}`);
    if(!res.ok)throw new Error('HTTP '+res.status);
    const data=await res.json();
    rmTyping();
    if(!data||!data.status||!data.result){
      addMessage('AI tidak merespon. Coba lagi.','error');return;
    }
    const result=String(data.result).trim();
    const think=data.think?String(data.think).trim():null;
    if(think)addThink(think.length>400?think.slice(0,400)+'...':think);
    addMessage(result,'bot');
  }catch(err){
    rmTyping();
    addMessage('Gagal: '+err.message+'\nCoba lagi bentar.','error');
  }finally{
    isBusy=false;sendBtn.disabled=false;input.focus();
  }
}
sendBtn.addEventListener('click',sendMessage);
input.addEventListener('keydown',e=>{
  if(e.key==='Enter'&&!e.shiftKey){e.preventDefault();sendMessage();}
});
input.addEventListener('input',()=>{
  input.style.height='auto';
  input.style.height=Math.min(input.scrollHeight,120)+'px';
});
function quickAsk(q){input.value=q;sendMessage();}
input.focus();
</script>
</body>
</html>
