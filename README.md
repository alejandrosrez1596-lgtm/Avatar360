<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Avatar Inteligente IA 360</title>
  <script src="https://aframe.io/releases/1.7.0/aframe.min.js"></script>
</head>
<body>
  <a-scene>
    <!-- Escenario 360 (biblioteca real) -->
    <a-sky src="https://cdn.aframe.io/360-image-gallery-boilerplate/img/library.jpg" rotation="0 -90 0"></a-sky>

    <!-- Avatar Ready Player Me (centrado) -->
    <a-entity 
      gltf-model="https://models.readyplayer.me/68bda9d5c11cea25ec396d86.glb" 
      position="0 -1.2 -3" 
      scale="1 1 1">
    </a-entity>

    <!-- Cámara -->
    <a-entity camera look-controls></a-entity>
  </a-scene>

  <!-- Interfaz -->
  <div style="position:absolute; bottom:20px; left:20px; background:white; padding:10px; border-radius:10px;">
    <input type="text" id="pregunta" placeholder="Hazme una pregunta..." style="width:250px;">
    <button onclick="responder()">Preguntar</button>
    <button onclick="saludar()">Saludar</button>
    <br><br>
    <label>Voz:</label>
    <select id="vozSelect"></select>
  </div>

  <script>
    const HF_API_KEY = "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";
    let selectedVoice = null;

    // Cargar voces disponibles
    function cargarVoces() {
      let voces = speechSynthesis.getVoices();
      let select = document.getElementById("vozSelect");
      select.innerHTML = "";

      voces.forEach((voz, i) => {
        let option = document.createElement("option");
        option.value = i;
        option.textContent = `${voz.name} (${voz.lang})`;
        select.appendChild(option);
      });

      select.onchange = () => {
        selectedVoice = voces[select.value];
      };

      if (voces.length > 0) {
        selectedVoice = voces[0];
      }
    }
    window.speechSynthesis.onvoiceschanged = cargarVoces;

    // Función para hablar
    function hablar(texto) {
      let utter = new SpeechSynthesisUtterance(texto);
      utter.lang = "es-ES";
      if (selectedVoice) utter.voice = selectedVoice;
      speechSynthesis.speak(utter);
    }

    // Consultar IA Hugging Face
    async function consultarIA(pregunta) {
      let prompt = `Eres un avatar experto en HISTORIA DEL ARTE.
      Si la pregunta no es sobre historia del arte, responde: "Lo siento, no tengo información sobre eso."
      Pregunta: ${pregunta}`;

      let response = await fetch("https://api-inference.huggingface.co/models/google/flan-t5-base", {
        method: "POST",
        headers: {
          "Authorization": `Bearer ${HF_API_KEY}`,
          "Content-Type": "application/json"
        },
        body: JSON.stringify({ inputs: prompt })
      });

      let data = await response.json();
      console.log("Respuesta IA:", data);

      // Capturar diferentes posibles salidas
      if (Array.isArray(data) && data[0]) {
        return data[0].generated_text || data[0].output_text || "Lo siento, no entendí.";
      }
      return "Lo siento, no entendí.";
    }

    // Responder pregunta
    async function responder() {
      let pregunta = document.getElementById("pregunta").value;
      let respuesta = await consultarIA(pregunta);
      hablar(respuesta);
    }

    // Saludo manual
    function saludar() {
      hablar("Hola, bienvenido a esta biblioteca virtual. Pregúntame sobre historia del arte.");
    }
  </script>
</body>
</html>
