<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Radio Web con Chat</title>
    <!-- Carga de Tailwind CSS para estilos modernos y responsivos -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Carga de la fuente Inter de Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f0f2f5; /* Un fondo gris claro para la página */
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh; /* Asegura que ocupe al menos toda la altura de la ventana */
            margin: 0;
            padding: 20px; /* Espaciado general */
            box-sizing: border-box;
        }
        .container {
            background-color: #ffffff; /* Fondo blanco para el contenedor principal */
            border-radius: 16px; /* Esquinas redondeadas */
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1); /* Sombra suave */
            width: 100%;
            max-width: 900px; /* Ancho máximo para el contenedor */
            display: flex;
            flex-direction: column;
            overflow: hidden; /* Oculta cualquier contenido que se desborde */
        }
        @media (min-width: 768px) {
            .container {
                flex-direction: row; /* Diseño en fila en pantallas más grandes */
                height: 700px; /* Altura fija para el contenedor en desktop */
            }
        }
        .radio-section, .chat-section {
            padding: 24px;
            flex: 1; /* Ambas secciones ocupan el mismo espacio */
            display: flex;
            flex-direction: column;
        }
        .radio-section {
            background-color: #6366f1; /* Un color morado vibrante para la sección de radio */
            color: white;
            justify-content: center;
            align-items: center;
            text-align: center;
            min-height: 250px; /* Altura mínima para la sección de radio en móviles */
        }
        @media (min-width: 768px) {
            .radio-section {
                min-height: auto; /* Elimina la altura mínima en desktop */
            }
        }
        .chat-section {
            background-color: #ffffff;
            border-left: 1px solid #e5e7eb; /* Separador en desktop */
        }
        .chat-messages {
            flex-grow: 1; /* Hace que el área de mensajes crezca y ocupe el espacio disponible */
            overflow-y: auto; /* Permite el desplazamiento si hay muchos mensajes */
            margin-bottom: 16px;
            padding-right: 8px; /* Espacio para la barra de desplazamiento */
            scroll-behavior: smooth; /* Desplazamiento suave */
        }
        .message-bubble {
            background-color: #e0e7ff; /* Fondo azul claro para los mensajes */
            border-radius: 12px;
            padding: 10px 14px;
            margin-bottom: 8px;
            max-width: 80%; /* Ancho máximo del mensaje */
            word-wrap: break-word; /* Rompe palabras largas */
        }
        .message-bubble.own {
            background-color: #d1fae5; /* Fondo verde claro para los mensajes propios */
            margin-left: auto; /* Alinea los mensajes propios a la derecha */
        }
        .message-sender {
            font-size: 0.75rem; /* Tamaño de fuente más pequeño para el remitente */
            color: #4b5563; /* Color gris oscuro */
            margin-bottom: 4px;
            font-weight: 600;
        }
        .message-content {
            font-size: 0.9rem;
            color: #1f2937; /* Color de texto oscuro */
        }
        .input-group {
            display: flex;
            gap: 10px;
        }
        .input-group input {
            flex-grow: 1;
            border: 1px solid #d1d5db;
            border-radius: 8px;
            padding: 10px 14px;
            font-size: 0.9rem;
            outline: none;
            transition: border-color 0.2s;
        }
        .input-group input:focus {
            border-color: #6366f1; /* Borde morado al enfocar */
        }
        .input-group button {
            background-color: #6366f1;
            color: white;
            padding: 10px 20px;
            border-radius: 8px;
            font-weight: 600;
            cursor: pointer;
            transition: background-color 0.2s;
        }
        .input-group button:hover {
            background-color: #4f46e5; /* Morado más oscuro al pasar el ratón */
        }
        .audio-player {
            width: 100%;
            max-width: 400px; /* Ancho máximo para el reproductor de audio */
            margin-top: 20px;
        }
        /* Estilos para el mensaje de carga/error */
        .loading-message, .error-message, .info-message {
            background-color: #fffbeb; /* Amarillo claro */
            color: #92400e; /* Naranja oscuro */
            padding: 12px;
            border-radius: 8px;
            margin-bottom: 16px;
            border: 1px solid #fcd34d; /* Borde amarillo */
            text-align: center;
        }
        .error-message {
            background-color: #fee2e2; /* Rojo claro */
            color: #991b1b; /* Rojo oscuro */
            border: 1px solid #ef4444; /* Borde rojo */
        }
        .info-message {
            background-color: #e0f2fe; /* Azul claro */
            color: #1e40af; /* Azul oscuro */
            border: 1px solid #60a5fa; /* Borde azul */
        }
    </style>
</head>
<body>
    <div class="container">
        <!-- Sección de la Radio -->
        <div class="radio-section">
            <h1 class="text-4xl font-bold mb-4">Mi Radio Web</h1>
            <p class="text-lg mb-6">Sintoniza nuestra transmisión en vivo y chatea con la comunidad.</p>
            <!-- Reproductor de Audio -->
            <audio controls class="audio-player rounded-lg shadow-lg">
                <!-- Puedes reemplazar esta URL con tu propia transmisión de radio o un archivo de audio -->
                <source src="https://centova.hostingelectrica.net/proxy/baulmania/stream.mp3" type="audio/mpeg">
                Tu navegador no soporta el elemento de audio.
            </audio>
            <p class="text-sm mt-4 opacity-80">
                ()
            </p>
        </div>

        <!-- Sección del Chat -->
        <div class="chat-section">
            <h2 class="text-2xl font-bold text-gray-800 mb-6">Chat en Vivo</h2>
            <!-- Mensajes de estado (cargando, error, información) -->
            <div id="statusMessage" class="info-message hidden"></div>

            <!-- Área de Mensajes del Chat -->
            <div id="chatMessages" class="chat-messages">
                <!-- Los mensajes del chat se cargarán aquí -->
            </div>

            <!-- Grupo de Entrada de Mensajes -->
            <div class="input-group">
                <input type="text" id="messageInput" placeholder="Escribe tu mensaje..." class="rounded-lg">
                <button id="sendMessageBtn" class="rounded-lg shadow-md">Enviar</button>
            </div>
        </div>
    </div>

    <!-- Firebase SDKs -->
    <script type="module">
        // Importa las funciones necesarias de Firebase
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, addDoc, onSnapshot, query, orderBy, serverTimestamp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Variables globales proporcionadas por el entorno de Canvas
        // __app_id: ID de la aplicación actual
        // __firebase_config: Configuración de Firebase en formato JSON
        // __initial_auth_token: Token de autenticación inicial para Firebase

        // Asegúrate de que las variables globales estén definidas o usa valores predeterminados
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {};
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        // Inicializa Firebase
        let app;
        let db;
        let auth;
        let userId = 'cargando...'; // Valor inicial mientras se autentica
        let isAuthReady = false; // Bandera para indicar si la autenticación ha terminado

        const statusMessageElement = document.getElementById('statusMessage');

        // Función para mostrar mensajes de estado
        function showStatusMessage(message, type = 'info') {
            statusMessageElement.textContent = message;
            statusMessageElement.className = `info-message ${type}-message`; // Resetea clases y aplica la nueva
            statusMessageElement.classList.remove('hidden'); // Asegura que sea visible
        }

        // Función para ocultar mensajes de estado
        function hideStatusMessage() {
            statusMessageElement.classList.add('hidden');
        }

        // Inicialización de Firebase y autenticación
        async function initializeFirebase() {
            try {
                showStatusMessage('Inicializando Firebase...');
                app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);

                // Escucha los cambios en el estado de autenticación
                onAuthStateChanged(auth, async (user) => {
                    if (user) {
                        userId = user.uid;
                        showStatusMessage(`Autenticado como: ${userId}`, 'info');
                        isAuthReady = true;
                        // Una vez autenticado, carga los mensajes del chat
                        loadChatMessages();
                    } else {
                        // Si no hay usuario, intenta iniciar sesión anónimamente
                        try {
                            showStatusMessage('Iniciando sesión anónimamente...');
                            if (initialAuthToken) {
                                await signInWithCustomToken(auth, initialAuthToken);
                            } else {
                                await signInAnonymously(auth);
                            }
                        } catch (error) {
                            console.error("Error al iniciar sesión:", error);
                            showStatusMessage(`Error al autenticar: ${error.message}`, 'error');
                            isAuthReady = true; // Marca como listo incluso si hay error para no bloquear la UI
                        }
                    }
                });

            } catch (error) {
                console.error("Error al inicializar Firebase:", error);
                showStatusMessage(`Error crítico al inicializar Firebase: ${error.message}`, 'error');
            }
        }

        // Carga los mensajes del chat desde Firestore
        function loadChatMessages() {
            if (!db || !isAuthReady) {
                console.warn("Firestore o autenticación no están listos. Reintentando carga de mensajes...");
                setTimeout(loadChatMessages, 500); // Reintenta después de 500ms
                return;
            }

            hideStatusMessage(); // Oculta el mensaje de inicialización una vez que se está cargando el chat

            // Construye la ruta de la colección de chat
            // Para datos públicos (chat compartido por todos los usuarios de la app)
            const chatCollectionRef = collection(db, `artifacts/${appId}/public/data/chatMessages`);

            // Crea una consulta para obtener los mensajes, ordenados por fecha de creación
            // Nota: orderBy() puede requerir un índice en Firestore. Si experimentas errores,
            // considera eliminar orderBy() y ordenar los mensajes en el cliente.
            const q = query(chatCollectionRef); // Eliminado orderBy para evitar errores de índice

            // Escucha los cambios en tiempo real en la colección de mensajes
            onSnapshot(q, (snapshot) => {
                const chatMessagesElement = document.getElementById('chatMessages');
                chatMessagesElement.innerHTML = ''; // Limpia los mensajes existentes

                const messages = [];
                snapshot.forEach((doc) => {
                    messages.push({ id: doc.id, ...doc.data() });
                });

                // Ordena los mensajes por marca de tiempo en el cliente si orderBy no se usa en la consulta
                messages.sort((a, b) => (a.timestamp?.toDate() || 0) - (b.timestamp?.toDate() || 0));

                messages.forEach((msg) => {
                    const messageBubble = document.createElement('div');
                    messageBubble.classList.add('message-bubble');
                    if (msg.senderId === userId) {
                        messageBubble.classList.add('own'); // Marca los mensajes propios
                    }

                    const senderElement = document.createElement('div');
                    senderElement.classList.add('message-sender');
                    senderElement.textContent = msg.senderId === userId ? 'Tú' : `Usuario: ${msg.senderId.substring(0, 8)}...`; // Muestra un ID corto

                    const contentElement = document.createElement('div');
                    contentElement.classList.add('message-content');
                    contentElement.textContent = msg.text;

                    messageBubble.appendChild(senderElement);
                    messageBubble.appendChild(contentElement);
                    chatMessagesElement.appendChild(messageBubble);
                });

                // Desplázate automáticamente hasta el último mensaje
                chatMessagesElement.scrollTop = chatMessagesElement.scrollHeight;
            }, (error) => {
                console.error("Error al cargar mensajes del chat:", error);
                showStatusMessage(`Error al cargar mensajes: ${error.message}`, 'error');
            });
        }

        // Envía un nuevo mensaje al chat
        document.getElementById('sendMessageBtn').addEventListener('click', async () => {
            const messageInput = document.getElementById('messageInput');
            const messageText = messageInput.value.trim();

            if (messageText === '') {
                showStatusMessage('Por favor, escribe un mensaje.', 'info');
                return;
            }
            if (!db || !userId) {
                showStatusMessage('El chat no está listo. Por favor, espera.', 'error');
                return;
            }

            try {
                // Añade el mensaje a la colección de chat en Firestore
                await addDoc(collection(db, `artifacts/${appId}/public/data/chatMessages`), {
                    text: messageText,
                    senderId: userId,
                    timestamp: serverTimestamp() // Marca de tiempo del servidor para ordenar
                });
                messageInput.value = ''; // Limpia el campo de entrada
                hideStatusMessage(); // Oculta cualquier mensaje de estado anterior
            } catch (error) {
                console.error("Error al enviar mensaje:", error);
                showStatusMessage(`Error al enviar mensaje: ${error.message}`, 'error');
            }
        });

        // Permite enviar mensajes presionando Enter en el campo de texto
        document.getElementById('messageInput').addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                document.getElementById('sendMessageBtn').click();
            }
        });

        // Inicializa Firebase cuando la ventana se haya cargado completamente
        window.onload = initializeFirebase;
    </script>
</body>
</html>
