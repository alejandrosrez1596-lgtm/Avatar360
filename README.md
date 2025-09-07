<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Avatar Inteligente IA 360</title>
  <script src="https://aframe.io/releases/1.7.0/aframe.min.js"></script>
</head>
<body>
  <a-scene>
    <!-- Escenario 360 -->
    <a-sky src="https://cdn.aframe.io/360-image-gallery-boilerplate/img/city.jpg" rotation="0 -90 0"></a-sky>

    <!-- Avatar Ready Player Me (más centrado) -->
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
  </div>

  <script>
    const HF_API_KEY = "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";

    async function consultarIA(pregunta) {
      let prompt = `Eres un avatar experto en HISTORIA DEL ARTE.
      Si la pregunta no es sobre historia del arte, responde: "Lo siento, no tengo información sobre eso."
      Pregunta: ${pregunta}`;

      let response = await fetch("https://api-inference.huggingface.co/models/google/flan-t5-small", {
        method: "POST",
        headers: {
          "Authorization": `Bearer ${HF_API_KEY}`,
          "Content-Type": "application/json"
        },
        body: JSON.stringify({ inputs: prompt })
      });

      let data = await response.json();
      console.log(data);
      // corregimos la lectura de la respuesta
      return data[0]?.generated_text || data?.generated_text || "Lo siento, no entendí.";
    }

    function hablar(texto) {
      let utter = new SpeechSynthesisUtterance(texto);
      utter.lang = "es-ES";
      speechSynthesis.speak(utter);
    }

    async function responder() {
      let pregunta = document.getElementById("pregunta").value;
      let respuesta = await consultarIA(pregunta);
      hablar(respuesta);
    }

    // Saludo solo cuando el usuario pulse el botón
    function saludar() {
      hablar("Hola, bienvenido a esta biblioteca virtual. Pregúntame sobre historia del arte.");
    }
  </script>
</body>
</html>
