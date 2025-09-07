<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Avatar Inteligente IA 360</title>
  <script src="https://aframe.io/releases/1.7.0/aframe.min.js"></script>
</head>
<body>
  <a-scene>
    <!-- Escenario 360 (biblioteca/museo) -->
    <a-sky src="https://cdn.aframe.io/360-image-gallery-boilerplate/img/library-360.jpg" rotation="0 -90 0"></a-sky>

    <!-- Avatar desde Ready Player Me -->
    <a-entity 
      gltf-model="https://models.readyplayer.me/68bda9d5c11cea25ec396d86.glb" 
      position="0 0 -3" 
      scale="1 1 1">
    </a-entity>

    <!-- Cámara -->
    <a-entity camera look-controls></a-entity>
  </a-scene>

  <!-- Interfaz de preguntas -->
  <div style="position:absolute; bottom:20px; left:20px; background:white; padding:10px; border-radius:10px;">
    <input type="text" id="pregunta" placeholder="Hazme una pregunta..." style="width:250px;">
    <button onclick="responder()">Preguntar</button>
  </div>

  <script>
    // 🔑 Tu API Key de Hugging Face
    const HF_API_KEY = "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";

    // IA: consultar Hugging Face
    async function consultarIA(pregunta) {
      // Prompt especializado → aquí definimos el tema
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
      console.log(data); // Para depuración en la consola
      return data[0]?.generated_text || "Lo siento, no entendí.";
    }

    // Función de voz
    function hablar(texto) {
      let utter = new SpeechSynthesisUtterance(texto);
      utter.lang = "es-ES";
      speechSynthesis.speak(utter);
    }

    // Responder con IA
    async function responder() {
      let pregunta = document.getElementById("pregunta").value;
      let respuesta = await consultarIA(pregunta);
      hablar(respuesta);
    }

    // Saludo inicial
    window.onload = () => {
      hablar("Bienvenido a esta biblioteca virtual. Pregúntame sobre historia del arte.");
    };
  </script>
</body>
</html>
