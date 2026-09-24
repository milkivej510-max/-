# -
Очень и Распознай английский и американский язык это два разных языка Если ты не знал советую тебе посетить наш сайт
from pathlib import Path

path = Path("/mnt/data/wolingo.html")
html = path.read_text(encoding="utf-8")

# Add assistant UI + styles before </style>
assistant_css = r"""
.ai-tail{position:fixed;right:24px;bottom:24px;width:58px;height:58px;border:0;border-radius:50%;background:#171b20;box-shadow:0 10px 28px rgba(0,0,0,.2);z-index:20;color:#fff;display:grid;place-items:center;transition:.2s}
.ai-tail:hover{transform:translateY(-3px) scale(1.04)}
.ai-tail .tail{width:30px;height:30px;position:relative}
.ai-tail .tail:before{content:"";position:absolute;width:24px;height:12px;border:5px solid #fff;border-left-color:transparent;border-bottom-color:transparent;border-radius:50%;transform:rotate(-35deg);left:1px;top:8px}
.ai-panel{position:fixed;right:24px;bottom:94px;width:min(370px,calc(100vw - 30px));background:#fff;border:1px solid var(--line);border-radius:22px;box-shadow:0 18px 55px rgba(20,25,35,.2);z-index:19;overflow:hidden;display:none}
.ai-panel.open{display:block;animation:pop .2s ease-out}
@keyframes pop{from{opacity:0;transform:translateY(10px) scale(.97)}to{opacity:1;transform:none}}
.ai-head{background:linear-gradient(135deg,#252b33,#5c6672);color:#fff;padding:17px;display:flex;gap:12px;align-items:center}
.ai-avatar{width:66px;height:66px;border-radius:18px;background:#dfe3e7;display:grid;place-items:center;overflow:hidden;flex:none}
.ai-avatar svg{width:62px;height:62px}
.ai-head strong{display:block}.ai-head small{color:#d8dde2}
.ai-body{padding:15px}.ai-messages{height:170px;overflow:auto;display:flex;flex-direction:column;gap:9px}
.bubble{max-width:85%;padding:10px 12px;border-radius:14px;font-size:14px;line-height:1.4}
.bubble.wolf{background:#f0f2f5;align-self:flex-start;color:#343a42}
.bubble.user{background:#eef0ff;align-self:flex-end;color:#414ac0}
.ai-input{display:flex;gap:8px;margin-top:12px}
.ai-input input{min-width:0;flex:1;border:1px solid var(--line);border-radius:12px;padding:11px 12px;outline:none}
.ai-input button{border:0;border-radius:12px;background:var(--accent);color:#fff;font-weight:800;padding:0 14px}
.talking .mouth{animation:talk .14s infinite alternate}
.talking .head{animation:bob .35s infinite alternate}
@keyframes talk{from{transform:scaleY(.45)}to{transform:scaleY(1.25)}}
@keyframes bob{from{transform:translateY(0)}to{transform:translateY(-1.5px)}}
.eye{transform-origin:center;animation:blink 4s infinite}
@keyframes blink{0%,44%,48%,100%{transform:scaleY(1)}46%{transform:scaleY(.08)}}
"""
html = html.replace("</style>", assistant_css + "\n</style>", 1)

# Add UI before </body>
assistant_html = r"""
<button class="ai-tail" id="aiTail" aria-label="Открыть AI помощника" onclick="toggleAI()"><span class="tail"></span></button>

<section class="ai-panel" id="aiPanel">
  <div class="ai-head">
    <div class="ai-avatar" id="aiAvatar">
      <svg viewBox="0 0 100 100">
        <g class="head">
          <path fill="#8e969f" d="M22 31 16 9l24 15c7-4 14-5 20-3L84 9l-5 23c8 9 11 18 10 29-2 21-17 32-39 32S13 82 11 61c-1-11 3-21 11-30Z"/>
          <path fill="#69717b" d="M27 51c-5 18 6 31 23 35 18-4 29-17 23-35-10 7-35 7-46 0Z"/>
          <ellipse class="eye" cx="37" cy="48" rx="4.5" ry="6" fill="#171b20"/>
          <ellipse class="eye" cx="63" cy="48" rx="4.5" ry="6" fill="#171b20"/>
          <circle cx="38.2" cy="46.5" r="1.5" fill="#fff"/><circle cx="64.2" cy="46.5" r="1.5" fill="#fff"/>
          <path fill="#242a30" d="M44 67h12l-3 7h-6Z"/>
          <ellipse class="mouth" cx="50" cy="76" rx="6" ry="2.2" fill="#242a30"/>
        </g>
      </svg>
    </div>
    <div><strong>Серый Волк · AI</strong><small>Помогу с уроком и объясню непонятное</small></div>
  </div>
  <div class="ai-body">
    <div class="ai-messages" id="aiMessages">
      <div class="bubble wolf">Привет! 🐺 Я твой помощник. Спроси меня о слове, грамматике или текущем задании.</div>
    </div>
    <div class="ai-input">
      <input id="aiInput" placeholder="Напиши вопрос..." onkeydown="if(event.key==='Enter') sendAI()">
      <button onclick="sendAI()">→</button>
    </div>
  </div>
</section>
"""
html = html.replace("</body>", assistant_html + "\n</body>", 1)

# Add JS before </script> of the existing script
assistant_js = r"""
function toggleAI(){
  document.getElementById('aiPanel').classList.toggle('open');
  if(document.getElementById('aiPanel').classList.contains('open')) setTimeout(()=>document.getElementById('aiInput').focus(),50);
}
function addBubble(text,who){
  const box=document.getElementById('aiMessages');
  const div=document.createElement('div');
  div.className='bubble '+who; div.textContent=text; box.appendChild(div); box.scrollTop=box.scrollHeight;
}
function wolfSpeak(text){
  addBubble(text,'wolf');
  const avatar=document.getElementById('aiAvatar');
  avatar.classList.add('talking');
  if('speechSynthesis' in window){
    speechSynthesis.cancel();
    const u=new SpeechSynthesisUtterance(text);
    u.lang='ru-RU'; u.rate=.96; u.pitch=1.02;
    u.onend=()=>avatar.classList.remove('talking');
    speechSynthesis.speak(u);
  }else{
    setTimeout(()=>avatar.classList.remove('talking'),Math.min(3500,text.length*35));
  }
}
function sendAI(){
  const input=document.getElementById('aiInput');
  const text=input.value.trim(); if(!text)return;
  addBubble(text,'user'); input.value='';
  setTimeout(()=>{
    const q=text.toLowerCase();
    let answer='Я рядом! Попробуй спросить меня о переводе слова, грамматике или попроси объяснить задание проще.';
    if(q.includes('привет')) answer='Привет! 🐺 Готов учиться вместе. Что разберём?';
    else if(q.includes('помощ')||q.includes('объясни')) answer='Конечно. Пришли мне слово или задание, и я объясню его простыми шагами.';
    else if(q.includes('good morning')) answer='“Good morning” означает «Доброе утро». Запомнить легко: morning — утро.';
    else if(q.includes('xp')||q.includes('опыт')) answer='XP — это очки опыта. Они помогают отслеживать твой прогресс в Wolingo.';
    wolfSpeak(answer);
  },350);
}
"""
html = html.replace("</script>", assistant_js + "\n</script>", 1)

path.write_text(html, encoding="utf-8")
print(f"Обновлено: {path}")
