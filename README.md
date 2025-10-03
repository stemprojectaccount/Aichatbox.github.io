<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Trò chuyện với Gemini - TTS Tiếng Việt</title>
<style>
  *{box-sizing:border-box;}
  body{
    font-family:"Segoe UI", sans-serif;
    background:linear-gradient(135deg,#74ABE2,#5563DE);
    display:flex;flex-direction:column;height:100vh;margin:0;
    color: #333;
  }
  .header {
    text-align: center;
    padding: 15px;
    background: rgba(255, 255, 255, 0.2);
    backdrop-filter: blur(5px);
    border-bottom: 1px solid rgba(255, 255, 255, 0.3);
  }
  .header h1 {
    margin: 0;
    color: white;
    text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.2);
  }
  .header p {
    margin: 5px 0 0;
    color: #f0f0f0;
    font-size: 14px;
  }
  #chatbox{
    flex:1;overflow-y:auto;padding:20px;
    display:flex;flex-direction:column;gap:12px;
  }
  .msg{
    padding:12px 16px;border-radius:20px;max-width:75%;
    word-wrap:break-word;white-space:pre-wrap;
    font-size:15px;line-height:1.4;
    animation:fadeIn .3s ease;
    box-shadow: 0 2px 5px rgba(0,0,0,0.1);
  }
  .user{background:#d1e7dd;align-self:flex-end;color:#0f5132;}
  .bot{background:#fff;border:1px solid #ddd;align-self:flex-start;}
  .img-preview{max-width:200px;border-radius:10px;margin-top:6px;}
  #inputArea{
    display:flex;padding:12px;
    background:rgba(255,255,255,0.95);
    backdrop-filter:blur(8px);
    align-items:center;
    gap:8px;
    border-top: 1px solid #ddd;
  }
  #userInput{
    flex:1;padding:12px 16px;
    border:1px solid #ccc;border-radius:20px;outline:none;
    font-size: 15px;
  }
  #sendBtn, #imgBtn, #ttsBtn, #micBtn{
    border:none;border-radius:50%;
    background:#5563DE;color:#fff;cursor:pointer;
    font-weight:bold;padding:12px;
    width: 45px;
    height: 45px;
    display: flex;
    align-items: center;
    justify-content: center;
    transition:background .2s, transform .1s;
  }
  #sendBtn:hover, #imgBtn:hover, #ttsBtn:hover, #micBtn:hover{
    background:#3b48b6;
    transform: scale(1.05);
  }
  
  /* TTS Panel */
  #ttsPanel {
    position: fixed;
    bottom: 80px;
    right: 20px;
    background: rgba(255, 255, 255, 0.98);
    border-radius: 12px;
    padding: 15px;
    box-shadow: 0 4px 15px rgba(0,0,0,0.2);
    width: 300px;
    z-index: 100;
    display: none;
    backdrop-filter: blur(10px);
    border: 1px solid #ddd;
  }
  #ttsPanel.visible {
    display: block;
    animation: slideIn 0.3s ease;
  }
  .tts-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 15px;
    border-bottom: 1px solid #eee;
    padding-bottom: 10px;
  }
  .tts-header h3 {
    margin: 0;
    color: #3b48b6;
  }
  .tts-control {
    margin: 12px 0;
  }
  .tts-control label {
    display: block;
    margin-bottom: 6px;
    font-weight: bold;
    color: #333;
  }
  .tts-control select, .tts-control input {
    width: 100%;
    padding: 8px 12px;
    border-radius: 8px;
    border: 1px solid #ccc;
    background: white;
  }
  .tts-control input[type="range"] {
    padding: 0;
    height: 20px;
  }
  #closeTtsPanel {
    background: none;
    border: none;
    font-size: 20px;
    cursor: pointer;
    color: #666;
    padding: 5px;
  }
  #closeTtsPanel:hover {
    color: #333;
  }
  #testVoice {
    width:100%;
    padding:10px;
    margin-top:12px;
    background:#5563DE;
    color:white;
    border:none;
    border-radius:8px;
    cursor: pointer;
    font-weight: bold;
    transition: background .2s;
  }
  #testVoice:hover {
    background:#3b48b6;
  }
  
  .typing-indicator {
    display: inline-flex;
    align-items: center;
    color: #666;
  }
  .typing-dot {
    width: 6px;
    height: 6px;
    border-radius: 50%;
    background-color: #666;
    margin-left: 4px;
    animation: typingAnimation 1.4s infinite ease-in-out;
  }
  .typing-dot:nth-child(1) { animation-delay: 0s; }
  .typing-dot:nth-child(2) { animation-delay: 0.2s; }
  .typing-dot:nth-child(3) { animation-delay: 0.4s; }
  
  .language-section {
    background: #f0f5ff;
    padding: 10px;
    border-radius: 8px;
    margin-bottom: 15px;
    border-left: 4px solid #3b48b6;
  }
  
  .language-option {
    display: flex;
    align-items: center;
    margin: 8px 0;
  }
  
  .language-option input {
    margin-right: 10px;
  }
  
  @keyframes fadeIn{from{opacity:0;transform:translateY(5px);}to{opacity:1;transform:translateY(0);}}
  @keyframes slideIn{from{opacity:0;transform:translateY(20px);}to{opacity:1;transform:translateY(0);}}
  @keyframes typingAnimation {
    0%, 60%, 100% { transform: translateY(0); }
    30% { transform: translateY(-5px); }
  }
  
  /* Footer */
  .footer {
    text-align: center;
    padding: 10px;
    font-size: 12px;
    color: rgba(255, 255, 255, 0.8);
    background: rgba(0, 0, 0, 0.1);
  }
  
  /* Responsive */
  @media (max-width: 600px) {
    .msg {
      max-width: 85%;
    }
    #inputArea {
      flex-wrap: wrap;
    }
    #userInput {
      width: 100%;
      order: 1;
      margin-bottom: 8px;
    }
    #ttsPanel {
      width: 90%;
      right: 5%;
      left: 5%;
    }
  }
</style>
</head>
<body>

<div class="header">
  <h1>Trò chuyện với Gemini</h1>
  <p>Ứng dụng trò chuyện với trí tuệ nhân tạo và hỗ trợ giọng nói tiếng Việt</p>
</div>

<div id="chatbox"></div>

<div id="inputArea">
  <input type="text" id="userInput" placeholder="Nhập câu hỏi của bạn hoặc nhấn nút mic..."/>
  <button id="imgBtn" title="Tải ảnh lên">📷</button>
  <button id="micBtn" title="Nhận diện giọng nói">🎤</button>
  <button id="ttsBtn" title="Cài đặt giọng nói">🔊</button>
  <button id="sendBtn" title="Gửi tin nhắn">➤</button>
  <input type="file" id="fileInput" accept="image/*" style="display:none"/>
</div>

<div id="ttsPanel">
  <div class="tts-header">
    <h3>Cài đặt giọng nói</h3>
    <button id="closeTtsPanel">×</button>
  </div>
  
  <div class="language-section">
    <h4>Lựa chọn ngôn ngữ</h4>
    <div class="language-option">
      <input type="radio" id="vietnamese" name="language" value="vi" checked>
      <label for="vietnamese">Tiếng Việt</label>
    </div>
    <div class="language-option">
      <input type="radio" id="english" name="language" value="en">
      <label for="english">Tiếng Anh</label>
    </div>
  </div>
  
  <div class="tts-control">
    <label for="voiceSelect">Giọng đọc:</label>
    <select id="voiceSelect"></select>
  </div>
  
  <div class="tts-control">
    <label for="volume">Âm lượng: <span id="volumeValue">1</span></label>
    <input type="range" id="volume" min="0" max="1" step="0.1" value="1">
  </div>
  
  <div class="tts-control">
    <label for="rate">Tốc độ: <span id="rateValue">1</span></label>
    <input type="range" id="rate" min="0.5" max="2" step="0.1" value="1">
  </div>
  
  <div class="tts-control">
    <label for="pitch">Độ cao: <span id="pitchValue">1</span></label>
    <input type="range" id="pitch" min="0" max="2" step="0.1" value="1">
  </div>
  
  <button id="testVoice">Nghe thử giọng đọc</button>
</div>

<div class="footer">
  <p>Ứng dụng trò chuyện với AI Gemini - Hỗ trợ tiếng Việt</p>
</div>

<script>
const API_KEY = "AIzaSyDzvHqoNxtXDbFHS2SOSXzcGbc2evbAZr0"; // Thay bằng API key Gemini
const ENDPOINT = "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent";

const chatbox = document.getElementById('chatbox');
const input = document.getElementById('userInput');
const sendBtn = document.getElementById('sendBtn');
const imgBtn = document.getElementById('imgBtn');
const micBtn = document.getElementById('micBtn');
const fileInput = document.getElementById('fileInput');
const ttsBtn = document.getElementById('ttsBtn');
const ttsPanel = document.getElementById('ttsPanel');
const closeTtsPanel = document.getElementById('closeTtsPanel');
const voiceSelect = document.getElementById('voiceSelect');
const volumeControl = document.getElementById('volume');
const rateControl = document.getElementById('rate');
const pitchControl = document.getElementById('pitch');
const testVoiceBtn = document.getElementById('testVoice');
const volumeValue = document.getElementById('volumeValue');
const rateValue = document.getElementById('rateValue');
const pitchValue = document.getElementById('pitchValue');
const vietnameseRadio = document.getElementById('vietnamese');
const englishRadio = document.getElementById('english');

let ttsEnabled = true;
let recognition; // SpeechRecognition instance
let voices = [];
let currentUtterance = null;
let selectedLanguage = 'vi';

// Khởi tạo TTS
function initTTS() {
  // Chờ voices được tải
  speechSynthesis.onvoiceschanged = function() {
    voices = speechSynthesis.getVoices();
    populateVoiceList();
    
    // Tìm giọng tiếng Việt nếu có
    const vietnameseVoice = voices.find(voice => voice.lang.includes('vi'));
    if (vietnameseVoice) {
      voiceSelect.value = vietnameseVoice.voiceURI;
    }
  };
  
  // Tải voices ngay lập tức (nếu đã có sẵn)
  voices = speechSynthesis.getVoices();
  if (voices.length > 0) {
    populateVoiceList();
  }
}

// Điền danh sách giọng nói vào dropdown
function populateVoiceList() {
  voiceSelect.innerHTML = '';
  
  // Lọc giọng nói theo ngôn ngữ đã chọn
  const filteredVoices = voices.filter(voice => {
    return selectedLanguage === 'vi' ? voice.lang.includes('vi') : voice.lang.includes('en');
  });
  
  if (filteredVoices.length > 0) {
    filteredVoices.forEach(voice => {
      const option = document.createElement('option');
      option.value = voice.voiceURI;
      option.textContent = `${voice.name} (${voice.lang})`;
      voiceSelect.appendChild(option);
    });
  } else {
    const option = document.createElement('option');
    option.textContent = selectedLanguage === 'vi' 
      ? 'Không tìm thấy giọng tiếng Việt' 
      : 'No English voices found';
    voiceSelect.appendChild(option);
  }
}

// Nói văn bản với cài đặt hiện tại
function speak(text) {
  if (!ttsEnabled || !window.speechSynthesis) return;
  
  // Dừng phát hiện tại nếu có
  if (currentUtterance) {
    speechSynthesis.cancel();
  }
  
  const selectedVoice = voiceSelect.value;
  const voice = voices.find(v => v.voiceURI === selectedVoice);
  
  currentUtterance = new SpeechSynthesisUtterance(text);
  if (voice) {
    currentUtterance.voice = voice;
  }
  currentUtterance.volume = parseFloat(volumeControl.value);
  currentUtterance.rate = parseFloat(rateControl.value);
  currentUtterance.pitch = parseFloat(pitchControl.value);
  
  currentUtterance.onend = function() {
    currentUtterance = null;
  };
  
  speechSynthesis.speak(currentUtterance);
}

// Dừng phát âm thanh
function stopSpeaking() {
  if (window.speechSynthesis && currentUtterance) {
    speechSynthesis.cancel();
    currentUtterance = null;
  }
}

function addMessage(text, cls, imgSrc){
  const div = document.createElement('div');
  div.className = `msg ${cls}`;
  
  // Xử lý tin nhắn đang gõ
  if (text === 'Đang gõ...') {
    const typingIndicator = document.createElement('div');
    typingIndicator.className = 'typing-indicator';
    typingIndicator.innerHTML = 'Đang gõ<span class="typing-dot"></span><span class="typing-dot"></span><span class="typing-dot"></span>';
    div.appendChild(typingIndicator);
  } else {
    div.textContent = text;
  }
  
  if(imgSrc){
    const img = document.createElement('img');
    img.src = imgSrc;
    img.className = 'img-preview';
    div.appendChild(document.createElement('br'));
    div.appendChild(img);
  }
  chatbox.appendChild(div);
  chatbox.scrollTo({top: chatbox.scrollHeight, behavior:"smooth"});
  return div;
}

async function sendMessage(base64Img){
  const question = input.value.trim();
  if(!question && !base64Img) return;
  addMessage(question || '[Đã gửi ảnh]', 'user', base64Img);
  input.value = '';

  const typing = addMessage('Đang gõ...','bot');

  let contents = [{parts:[{text: question || 'Phân tích hình ảnh'}]}];
  if(base64Img){
    contents[0].parts.push({
      inline_data: { mime_type: "image/png", data: base64Img.split(',')[1] }
    });
  }

  try {
    const res = await fetch(`${ENDPOINT}?key=${API_KEY}`, {
      method:'POST',
      headers:{'Content-Type':'application/json'},
      body: JSON.stringify({ contents })
    });
    const data = await res.json();
    const reply = data?.candidates?.[0]?.content?.parts?.[0]?.text || 'Không nhận được phản hồi';
    typing.textContent = reply;
    speak(reply);
  } catch(err){
    typing.textContent = 'Lỗi kết nối: ' + err.message;
  }
}

// Event listeners
sendBtn.addEventListener('click', ()=> sendMessage());
input.addEventListener('keydown', e => { if(e.key==='Enter') sendMessage(); });

imgBtn.addEventListener('click', ()=> fileInput.click());
fileInput.addEventListener('change', ()=>{
  const file = fileInput.files[0];
  if(!file) return;
  const reader = new FileReader();
  reader.onload = e => sendMessage(e.target.result);
  reader.readAsDataURL(file);
});

// TTS Panel
ttsBtn.addEventListener('click', ()=>{
  ttsPanel.classList.toggle('visible');
});

closeTtsPanel.addEventListener('click', ()=>{
  ttsPanel.classList.remove('visible');
});

// Cập nhật giá trị thanh trượt
volumeControl.addEventListener('input', () => {
  volumeValue.textContent = volumeControl.value;
});

rateControl.addEventListener('input', () => {
  rateValue.textContent = rateControl.value;
});

pitchControl.addEventListener('input', () => {
  pitchValue.textContent = pitchControl.value;
});

// Test voice
testVoiceBtn.addEventListener('click', () => {
  if (selectedLanguage === 'vi') {
    speak('Xin chào! Đây là giọng nói tiếng Việt. Bạn đang nghe thử tính năng chuyển văn bản thành giọng nói.');
  } else {
    speak('Hello! This is an English voice. You are testing the text-to-speech feature.');
  }
});

// Xử lý thay đổi ngôn ngữ
vietnameseRadio.addEventListener('change', () => {
  if (vietnameseRadio.checked) {
    selectedLanguage = 'vi';
    populateVoiceList();
  }
});

englishRadio.addEventListener('change', () => {
  if (englishRadio.checked) {
    selectedLanguage = 'en';
    populateVoiceList();
  }
});

// -------- Voice Recognition --------
if ('webkitSpeechRecognition' in window || 'SpeechRecognition' in window) {
  const SR = window.SpeechRecognition || window.webkitSpeechRecognition;
  recognition = new SR();
  recognition.lang = 'vi-VN';
  recognition.interimResults = false;
  recognition.continuous = false;

  recognition.onresult = (event) => {
    const transcript = event.results[0][0].transcript;
    input.value = transcript;
  };
  
  recognition.onerror = (e) => {
    console.log('Lỗi nhận diện giọng nói:', e);
    addMessage('Xin lỗi, tôi không nghe rõ. Bạn có thể nói lại được không?', 'bot');
    micBtn.textContent = '🎤';
    isListening = false;
  };
  
  recognition.onend = () => {
    micBtn.textContent = '🎤';
    isListening = false;
  };
} else {
  micBtn.disabled = true;
  micBtn.title = "Trình duyệt không hỗ trợ nhận diện giọng nói";
}

let isListening = false;

micBtn.addEventListener('click', () => {
  if (!recognition) return;
  
  if (!isListening) {
    recognition.start();
    isListening = true;
    micBtn.textContent = '🎙️';
    addMessage('Đang lắng nghe...', 'bot');
  } else {
    recognition.stop();
    isListening = false;
    micBtn.textContent = '🎤';
  }
});

// Khởi tạo TTS khi trang tải xong
window.addEventListener('DOMContentLoaded', function() {
  initTTS();
  
  // Thêm tin nhắn chào mừng
  setTimeout(() => {
    const welcomeMsg = "Xin chào! Tôi là trợ lý ảo Gemini. Tôi có thể giúp gì cho bạn?";
    addMessage(welcomeMsg, 'bot');
    speak(welcomeMsg);
  }, 500);
});

// Hiển thị giá trị ban đầu của thanh trượt
volumeValue.textContent = volumeControl.value;
rateValue.textContent = rateControl.value;
pitchValue.textContent = pitchControl.value;
</script>

</body>
</html>
