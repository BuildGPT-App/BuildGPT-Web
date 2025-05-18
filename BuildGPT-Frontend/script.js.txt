const chatBox = document.getElementById('chatBox');
const userInput = document.getElementById('userInput');
const sendBtn = document.getElementById('sendBtn');
const uploadImage = document.getElementById('uploadImage');

sendBtn.onclick = async () => {
  const question = userInput.value;
  if (!question) return;
  appendMessage("You", question);
  userInput.value = '';
  appendMessage("BuildGPT", "Typing...");
  
  const response = await fetch('https://build-gpt-backend.vercel.app/', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ question })
  });
  
  const data = await response.json();
  updateLastMessage(data.answer);
};

uploadImage.onchange = async (e) => {
  const file = e.target.files[0];
  if (!file) return;
  appendMessage("You", "Uploaded an image...");
  appendMessage("BuildGPT", "Analyzing image...");
  
  const formData = new FormData();
  formData.append("image", file);

  const response = await fetch('https://build-gpt-backend.vercel.app/api/analyze', {
    method: 'POST',
    body: formData
  });

  const data = await response.json();
  updateLastMessage(data.result);
};

function appendMessage(sender, text) {
  const msg = document.createElement("div");
  msg.innerHTML = `<strong>${sender}:</strong> ${text}`;
  chatBox.appendChild(msg);
  chatBox.scrollTop = chatBox.scrollHeight;
}

function updateLastMessage(newText) {
  const messages = chatBox.querySelectorAll("div");
  if (messages.length > 0) {
    messages[messages.length - 1].innerHTML = `<strong>BuildGPT:</strong> ${newText}`;
  }
}
