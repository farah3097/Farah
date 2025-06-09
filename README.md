<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>💖 Girly Virtual Assistant</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap" rel="stylesheet">
  <style>
    body {
      font-family: 'Poppins', sans-serif;
      background: linear-gradient(to right, #ffe0f0, #ffeaf6);
      margin: 0;
      padding: 20px;
      text-align: center;
    }

    h1 {
      color: #d63384;
      font-size: 2em;
    }

    .chat-box {
      max-width: 600px;
      margin: 20px auto;
      background: #fff0f6;
      border-radius: 16px;
      padding: 20px;
      box-shadow: 0 4px 15px rgba(255, 182, 193, 0.3);
      text-align: left;
      min-height: 150px;
    }

    .message {
      margin: 10px 0;
      padding: 10px 14px;
      border-radius: 12px;
      line-height: 1.4;
    }

    .user {
      background-color: #ffd6e8;
      text-align: right;
      font-weight: 600;
    }

    .bot {
      background-color: #f9d9ec;
      font-style: italic;
    }

    button {
      background: #ff69b4;
      color: white;
      border: none;
      padding: 12px 25px;
      font-size: 16px;
      border-radius: 12px;
      cursor: pointer;
      margin-top: 10px;
      transition: background 0.3s ease;
    }

    button:hover {
      background: #e754a6;
    }

    input[type="text"] {
      padding: 12px;
      font-size: 16px;
      border-radius: 12px;
      border: 1px solid #ffc0cb;
      width: 80%;
      margin-top: 20px;
      outline: none;
    }
  </style>
</head>
<body>

<h1>🎀 Your Cute AI Assistant 💕</h1>
<p>Click or type below to chat with me 💬</p>

<div class="chat-box" id="chatBox">
  <div class="message bot">Hi sweetie! I'm here to help you 🌸</div>
</div>

<button onclick="startListening()">🎤 Speak</button><br>
<input type="text" id="manualInput" placeholder="Type your question..." />
<button onclick="handleManualInput()">💌 Send</button>

<script>
  const chatBox = document.getElementById('chatBox');
  const openaiKey = "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";
  function addMessage(sender, text) {
    const msg = document.createElement('div');
    msg.className = `message ${sender}`;
    msg.textContent = text;
    chatBox.appendChild(msg);
    chatBox.scrollTop = chatBox.scrollHeight;
  }

  function speak(text) {
    const utterance = new SpeechSynthesisUtterance(text);
    utterance.lang = "en-US";
    speechSynthesis.speak(utterance);
  }

  async function getResponse(text) {
    addMessage("user", text);

    try {
      const res = await fetch("https://api.openai.com/v1/chat/completions", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          "Authorization": `Bearer ${openaiKey}`
        },
        body: JSON.stringify({
          model: "gpt-3.5-turbo",
          messages: [{ role: "user", content: text }]
        })
      });

      if (!res.ok) {
        const errorText = await res.text();
        console.error("OpenAI error:", errorText);
        addMessage("bot", "Oops! Something went wrong 💔");
        speak("Oops! Something went wrong.");
        return;
      }

      const data = await res.json();
      console.log("API response:", data);

      const reply = data.choices?.[0]?.message?.content?.trim();
      if (reply) {
        addMessage("bot", reply);
        speak(reply);
      } else {
        addMessage("bot", "Hmm... I don’t know that yet 💭");
        speak("Hmm... I don’t know that yet.");
      }
    } catch (err) {
      console.error("Fetch error:", err);
      addMessage("bot", "Couldn’t connect to OpenAI 😢");
      speak("Couldn’t connect to OpenAI.");
    }
  }

  function startListening() {
    const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
    if (!SpeechRecognition) {
      alert("Your browser doesn’t support voice recognition 😔");
      return;
    }

    const recognition = new SpeechRecognition();
    recognition.lang = "en-US";
    recognition.interimResults = false;

    recognition.onresult = event => {
      const userSpeech = event.results[0][0].transcript;
      console.log("User said:", userSpeech);
      getResponse(userSpeech);
    };

    recognition.onerror = (event) => {
      console.error("Speech error:", event.error);
      addMessage("bot", "Oops, I couldn’t hear you 💬");
      speak("Oops, I couldn’t hear you.");
    };

    recognition.start();
  }

  function handleManualInput() {
    const input = document.getElementById("manualInput");
    const text = input.value.trim();
    if (text) {
      getResponse(text);
      input.value = '';
    }
  }
</script>

</body>
</html>
