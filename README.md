<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Panel de Control - Liga de Fútbol en Vivo</title>
    <!-- Cargamos html2canvas para la conversión de elementos HTML a imágenes PNG -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <style>
        /* ==========================================================================
           1. VARIABLES GLOBALES (Modo Oscuro, Tipografía Fluida y Espaciados)
           ========================================================================== */
        :root {
            --bg-main: #0d1117;
            --bg-card: #161b22;
            --border-color: #30363d;
            --text-primary: #f0f6fc;
            --text-secondary: #8b949e;
            --pitch-green: #238636;
            --pitch-green-hover: #2ea043;
            --alert-red: #da3633;
            --warning-yellow: #f1e05a;
            --assist-blue: #58a6ff;

            --fs-h1: clamp(1.4rem, 4vw, 2.2rem);
            --fs-h2: clamp(1.1rem, 3vw, 1.5rem);
            --fs-h3: clamp(1.1rem, 2.5vw, 1.4rem);
            --fs-base: clamp(0.875rem, 2vw, 1rem);
            --fs-small: clamp(0.75rem, 1.5vw, 0.9rem);
            --fs-timer: clamp(2.2rem, 8vw, 3.5rem);
            --fs-goals: clamp(2.5rem, 7vw, 4rem);

            --sp-xs: clamp(0.25rem, 1vw, 0.5rem);
            --sp-sm: clamp(0.5rem, 1.5vw, 0.75rem);
            --sp-md: clamp(1rem, 3vw, 1.5rem);
            --sp-lg: clamp(1.2rem, 4vw, 2rem);
            --sp-xl: clamp(1.5rem, 5vw, 2.5rem);

            --radius-sm: 0.375rem;
            --radius-md: 0.5rem;
            --radius-lg: 0.75rem;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--bg-main);
            color: var(--text-primary);
            padding: var(--sp-md);
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
            font-size: var(--fs-base);
        }

        .container {
            width: 100%;
            max-width: min(100%, 60rem); 
            margin: 0 auto;
        }

        header {
            margin-bottom: var(--sp-lg);
            text-align: center;
            width: 100%;
        }

        header h1 {
            font-size: var(--fs-h1);
            color: var(--text-primary);
            text-shadow: 0 0.25em 0.5em rgba(46, 160, 67, 0.3);
            padding: 0 var(--sp-sm);
        }

        h2 {
            font-size: var(--fs-h2);
            margin-bottom: var(--sp-md);
            border-bottom: 2px solid var(--border-color);
            padding-bottom: var(--sp-sm);
            color: var(--text-secondary);
        }

        .card {
            background-color: var(--bg-card);
            border: 1px solid var(--border-color);
            border-radius: var(--radius-lg);
            padding: var(--sp-lg);
            margin-bottom: var(--sp-md);
            box-shadow: 0 0.25em 0.75em rgba(0, 0, 0, 0.5);
            width: 100%;
        }

        .form-group {
            display: flex;
            flex-direction: column;
            gap: var(--sp-xs);
            text-align: left;
            width: 100%;
        }

        label {
            font-weight: 600;
            font-size: var(--fs-small);
        }

        select, input {
            background-color: var(--bg-main);
            border: 1px solid var(--border-color);
            color: var(--text-primary);
            padding: var(--sp-sm);
            border-radius: var(--radius-sm);
            font-size: var(--fs-base);
            outline: none;
            width: 100%;
            transition: border-color 0.2s ease;
        }

        select:focus, input:focus { border-color: var(--pitch-green); }

        .btn {
            padding: var(--sp-sm) var(--sp-md);
            border: none;
            border-radius: var(--radius-sm);
            font-size: var(--fs-base);
            font-weight: bold;
            cursor: pointer;
            transition: all 0.2s ease;
            text-align: center;
            width: 100%;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 0.5em;
            min-height: 2.75rem; 
        }

        .btn:disabled {
            opacity: 0.4;
            cursor: not-allowed;
            transform: scale(1) !important;
        }

        .btn-success { background-color: var(--pitch-green); color: white; }
        .btn-success:hover:not(:disabled) { background-color: var(--pitch-green-hover); transform: translateY(-1px); }
        .btn-danger { background-color: var(--alert-red); color: white; }
        .btn-primary { background-color: #1f6feb; color: white; }
        .btn-primary:hover:not(:disabled) { background-color: #388bfd; }
        .btn-secondary { background-color: #21262d; color: var(--text-primary); border: 1px solid var(--border-color); }
        .btn-secondary:hover:not(:disabled) { background-color: #30363d; }
        .btn-goal { border-bottom: 0.25rem solid #fff; }
        .btn-card { border-bottom: 0.25rem solid var(--warning-yellow); }
        .btn-assist { border-bottom: 0.25rem solid var(--assist-blue); }

        .login-card {
            max-width: min(100%, 26rem);
            margin: var(--sp-xl) auto;
            text-align: center;
            border-top: 5px solid var(--pitch-green);
            animation: fadeIn 0.4s ease;
        }

        .login-logo { font-size: clamp(2.5rem, 8vw, 3.5rem); margin-bottom: var(--sp-sm); }

        .login-error {
            background-color: rgba(218, 54, 51, 0.15);
            border: 1px solid var(--alert-red);
            color: #ff7b72;
            padding: var(--sp-sm);
            border-radius: var(--radius-sm);
            margin-bottom: var(--sp-md);
            font-size: var(--fs-small);
            font-weight: bold;
        }

        .status-bar {
            display: flex;
            flex-direction: column; 
            gap: var(--sp-sm);
            align-items: center;
            padding: var(--sp-sm) var(--sp-md);
            background-color: var(--bg-card);
            border: 1px solid var(--border-color);
            border-radius: var(--radius-md);
            margin-bottom: var(--sp-md);
            text-align: center;
            width: 100%;
        }

        .status-user {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            align-items: center;
            gap: var(--sp-sm);
            font-size: var(--fs-small);
        }

        .role-badge {
            padding: 0.2em 0.6em;
            border-radius: 1em;
            font-size: 0.7em;
            font-weight: bold;
            text-transform: uppercase;
        }

        .role-admin { background-color: rgba(35, 134, 54, 0.2); color: #56d364; border: 1px solid rgba(35, 134, 54, 0.4); }
        .role-spectator { background-color: rgba(88, 166, 255, 0.2); color: #79c0ff; border: 1px solid rgba(88, 166, 255, 0.4); }

        .setup-grid {
            display: grid;
            grid-template-columns: 1fr;
            gap: var(--sp-md);
            margin-bottom: var(--sp-md);
        }

        .scoreboard-card { border-left: 0.4rem solid var(--pitch-green); }

        .scoreboard {
            display: flex;
            flex-direction: column; 
            align-items: center;
            gap: var(--sp-lg);
            width: 100%;
        }

        .team-display { 
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 100%;
        }
        
        .shield-box { 
            width: clamp(4rem, 15vw, 6rem); 
            height: clamp(4rem, 15vw, 6rem); 
            margin-bottom: var(--sp-sm);
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: clamp(2rem, 8vw, 3rem);
        }

        .shield-box img {
            max-width: 100%;
            max-height: 100%;
            object-fit: contain;
            filter: drop-shadow(0 4px 6px rgba(0,0,0,0.3));
        }

        .team-display h3 { 
            font-size: var(--fs-h3); 
            margin-bottom: var(--sp-sm);
            text-align: center;
            word-break: break-word; 
        }
        
        .goals {
            font-size: var(--fs-goals);
            font-weight: 800;
            background: #21262d;
            padding: 0.1em 0.5em;
            border-radius: var(--radius-md);
            border: 1px solid var(--border-color);
            display: inline-block;
            min-width: 2em;
            text-align: center;
            margin-bottom: var(--sp-md);
            line-height: 1.2;
        }

        .match-status {
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 100%;
            padding: var(--sp-sm) 0;
            border-top: 1px solid var(--border-color);
            border-bottom: 1px solid var(--border-color);
        }

        .timer {
            font-size: var(--fs-timer);
            font-family: monospace;
            font-weight: bold;
            color: #58a6ff;
            line-height: 1;
        }

        .period-badge {
            background-color: var(--border-color);
            padding: 0.3em 1em;
            border-radius: 1.5em;
            font-size: var(--fs-small);
            font-weight: bold;
            margin-top: var(--sp-xs);
            letter-spacing: 0.05em;
            text-align: center;
        }

        .timer-controls {
            display: grid;
            grid-template-columns: 1fr;
            gap: var(--sp-sm);
            margin-top: var(--sp-lg);
            width: 100%;
        }

        .team-incidents {
            width: 100%;
            background-color: rgba(0, 0, 0, 0.2);
            border-radius: var(--radius-md);
            padding: var(--sp-sm);
            min-height: 4rem;
            text-align: left;
            border: 1px dashed var(--border-color);
        }

        .incident-item {
            font-size: var(--fs-small);
            padding: 0.3em 0;
            border-bottom: 1px solid rgba(48, 54, 61, 0.5);
            animation: fadeIn 0.3s ease;
            word-wrap: break-word;
        }
        .incident-item:last-child { border-bottom: none; }

        .incidents-empty {
            color: var(--text-secondary);
            font-style: italic;
            font-size: var(--fs-small);
            text-align: center;
            padding-top: var(--sp-sm);
        }

        .action-buttons {
            display: grid;
            grid-template-columns: 1fr;
            gap: var(--sp-md);
            width: 100%;
        }

        .modal {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background-color: rgba(0,0,0,0.75);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 100;
            backdrop-filter: blur(4px);
            padding: var(--sp-md);
        }

        .modal-content {
            background-color: var(--bg-card);
            border: 1px solid var(--border-color);
            padding: var(--sp-lg);
            border-radius: var(--radius-lg);
            width: 100%;
            max-width: min(100%, 28rem); 
            max-height: 90vh;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: var(--sp-md);
            box-shadow: 0 0.5em 1.5em rgba(0,0,0,0.6);
        }

        .modal-actions {
            display: grid;
            grid-template-columns: 1fr;
            gap: var(--sp-sm);
            margin-top: var(--sp-xs);
        }

        #status-toast {
            position: fixed; 
            bottom: var(--sp-lg); 
            right: 50%;
            transform: translateX(50%);
            background-color: var(--bg-card); 
            border: 1px solid var(--border-color); 
            padding: var(--sp-sm) var(--sp-md); 
            border-radius: var(--radius-md); 
            box-shadow: 0 0.25em 0.75em rgba(0,0,0,0.5); 
            z-index: 1000; 
            display: none; 
            font-weight: bold; 
            color: var(--text-primary);
            font-size: var(--fs-small);
            text-align: center;
            width: max-content;
            max-width: 90vw;
        }

        .hidden { display: none !important; }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(-5px); }
            to { opacity: 1; transform: translateY(0); }
        }

        @media (min-width: 480px) {
            .setup-grid, .modal-actions, .timer-controls { grid-template-columns: 1fr 1fr; }
        }

        @media (min-width: 768px) {
            .scoreboard {
                flex-direction: row;
                justify-content: space-between;
                align-items: flex-start;
            }
            .match-status {
                flex: 1;
                border: none;
                padding: 0;
                margin-top: var(--sp-md);
            }
            .team-display { flex: 1; }
            .action-buttons { grid-template-columns: repeat(3, 1fr); }
            .status-bar {
                flex-direction: row;
                justify-content: space-between;
            }
            #status-toast {
                right: var(--sp-lg);
                transform: none; 
            }
        }

        @media (min-width: 1024px) {
            .card { padding: var(--sp-xl); }
            .btn:hover { filter: brightness(1.1); }
        }

        @media (min-width: 1366px) {
            body { padding-top: var(--sp-xl); }
        }

        /* Indicador de carga para Firebase */
        #loading-overlay {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background-color: var(--bg-main);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 2000;
        }
        .spinner {
            width: 50px;
            height: 50px;
            border: 5px solid var(--border-color);
            border-top: 5px solid var(--pitch-green);
            border-radius: 50%;
            animation: spin 1s linear infinite;
            margin-bottom: 20px;
        }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
    </style>
</head>
<body>

    <!-- Pantalla de carga mientras conecta a Firebase -->
    <div id="loading-overlay">
        <div class="spinner"></div>
        <h2 style="font-size: var(--fs-h2); padding: 0 10px; text-align: center;">Conectando con el servidor en tiempo real...</h2>
    </div>

    <header>
        <h1>⚽ PANEL DE CONTROL LIGA EN VIVO ⚽</h1>
    </header>

    <main class="container">
        
        <!-- PANTALLA DE INICIO DE SESIÓN -->
        <section id="login-section" class="card login-card hidden">
            <div class="login-logo">🏆</div>
            <h2>Iniciar Sesión</h2>
            <p style="color: var(--text-secondary); margin-bottom: 20px; font-size: var(--fs-small);">
                Ingresa tus credenciales de administrador o accede en modo espectador para ver el partido en vivo.
            </p>

            <div id="login-error-msg" class="login-error hidden">Usuario o contraseña incorrectos</div>

            <div class="form-group" style="margin-bottom: var(--sp-md);">
                <label for="login-username">Usuario:</label>
                <input type="text" id="login-username" placeholder="Ej: Fray">
            </div>

            <div class="form-group" style="margin-bottom: var(--sp-lg);">
                <label for="login-password">Contraseña:</label>
                <input type="password" id="login-password" placeholder="••••••">
            </div>

            <div style="display: flex; flex-direction: column; gap: var(--sp-sm);">
                <button id="btn-login" class="btn btn-success">🔓 Iniciar Sesión (Admin)</button>
                <button id="btn-login-spectator" class="btn btn-secondary">👁️ Entrar como Espectador</button>
            </div>
        </section>

        <!-- PANEL PRINCIPAL DEL JUEGO (OCULTO AL INICIO) -->
        <div id="app-panel" class="hidden">
            
            <!-- BARRA DE ESTADO DE USUARIO -->
            <div class="status-bar">
                <div class="status-user">
                    <span>Usuario: <strong id="current-user-name">...</strong></span>
                    <span id="current-user-role" class="role-badge">...</span>
                </div>
                <div style="display:flex; align-items: center; gap: 10px;">
                    <span id="sync-status" title="Sincronizado" style="color: var(--pitch-green); font-size: 1.2rem;">🟢</span>
                    <button id="btn-logout" class="btn btn-secondary" style="width: auto; font-size: var(--fs-small);">🚪 Cerrar Sesión</button>
                </div>
            </div>

            <!-- PANEL DE CONFIGURACIÓN INICIAL (SÓLO ADMINISTRADORES) -->
            <section class="card" id="setup-panel">
                <h2>Iniciar Nuevo Partido</h2>
                <div class="setup-grid">
                    <div class="form-group">
                        <label for="local-select">Equipo 1:</label>
                        <select id="local-select">
                            <option value="">-- Elige Equipo 1 --</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label for="visitante-select">Equipo 2:</label>
                        <select id="visitante-select">
                            <option value="">-- Elige Equipo 2 --</option>
                        </select>
                    </div>
                </div>
                <button id="start-match-btn" class="btn btn-success" disabled>Iniciar Partido para todos</button>
            </section>

            <!-- CONTENEDOR DEL PARTIDO -->
            <div id="match-panel" class="hidden">
                
                <section class="card scoreboard-card" id="capture-area">
                    <div class="scoreboard">
                        <!-- EQUIPO LOCAL -->
                        <div class="team-display">
                            <div class="shield-box" id="shield-local">⚽</div>
                            <h3 id="score-local-name">Equipo 1</h3>
                            <span id="score-local-goals" class="goals">0</span>
                            
                            <div class="team-incidents" id="incidents-local">
                                <p class="incidents-empty">Sin incidencias</p>
                            </div>
                        </div>
                        
                        <!-- CRONÓMETRO CENTRAL -->
                        <div class="match-status">
                            <div id="cronometro" class="timer">00:00</div>
                            <div id="periodo-display" class="period-badge">PRIMER TIEMPO</div>
                        </div>

                        <!-- EQUIPO VISITANTE -->
                        <div class="team-display">
                            <div class="shield-box" id="shield-visitante">⚽</div>
                            <h3 id="score-visitante-name">Equipo 2</h3>
                            <span id="score-visitante-goals" class="goals">0</span>
                            
                            <div class="team-incidents" id="incidents-visitante">
                                <p class="incidents-empty">Sin incidencias</p>
                            </div>
                        </div>
                    </div>

                    <!-- CONTROLES DEL TIEMPO (SÓLO ADMINISTRADORES) -->
                    <div class="timer-controls hidden" id="admin-timer-controls">
                        <button id="timer-toggle-btn" class="btn btn-primary">▶️ Iniciar Tiempo</button>
                        <button id="add-time-btn" class="btn btn-secondary">➕ +1 Min Extra</button>
                    </div>

                    <!-- BOTÓN COMPARTIR RESULTADO -->
                    <div class="share-container" id="share-action-container" style="margin-top: var(--sp-lg);">
                        <button id="btn-share-result" class="btn btn-secondary" style="border-color: var(--assist-blue); color: var(--assist-blue);">
                            📤 Compartir Resultado
                        </button>
                    </div>
                </section>

                <!-- CONSOLA DE REGISTRO (SÓLO ADMINISTRADORES) -->
                <section class="card hidden" id="admin-action-card">
                    <h2>Consola de Registro</h2>
                    <div class="action-buttons">
                        <button id="btn-modal-gol" class="btn action-btn btn-goal">⚽ Registrar Gol</button>
                        <button id="btn-modal-tarjeta" class="btn action-btn btn-card">🟨🟥 Registrar Tarjeta</button>
                        <button id="btn-modal-asistencia" class="btn action-btn btn-assist">👟 Registrar Asistencia</button>
                    </div>
                </section>
            </div>
        </div>
    </main>

    <!-- MODALES DE REGISTRO -->
    <div id="modal-gol" class="modal hidden">
        <div class="modal-content">
            <h3>⚽ Anotar Gol</h3>
            <div class="form-group">
                <label for="gol-equipo">Equipo:</label>
                <select id="gol-equipo"></select>
            </div>
            <div class="form-group">
                <label for="gol-jugador">Autor:</label>
                <select id="gol-jugador"></select>
            </div>
            <div class="form-group">
                <label for="gol-minuto">Minuto de Juego:</label>
                <input type="text" id="gol-minuto" readonly style="background-color: #21262d; cursor: not-allowed; color: var(--text-secondary);">
            </div>
            <div class="modal-actions">
                <button id="btn-guardar-gol" class="btn btn-success">Guardar Gol</button>
                <button class="btn btn-danger close-modal">Cancelar</button>
            </div>
        </div>
    </div>

    <div id="modal-tarjeta" class="modal hidden">
        <div class="modal-content">
            <h3>🟨🟥 Sanción Disciplinaria</h3>
            <div class="form-group">
                <label for="tarjeta-tipo">Tarjeta:</label>
                <select id="tarjeta-tipo">
                    <option value="🟨 Tarjeta Amarilla">🟨 Amarilla</option>
                    <option value="🟥 Tarjeta Roja">🟥 Roja</option>
                </select>
            </div>
            <div class="form-group">
                <label for="tarjeta-equipo">Equipo:</label>
                <select id="tarjeta-equipo"></select>
            </div>
            <div class="form-group">
                <label for="tarjeta-jugador">Jugador Infractor:</label>
                <select id="tarjeta-jugador"></select>
            </div>
            <div class="form-group">
                <label for="tarjeta-minuto">Minuto:</label>
                <input type="text" id="tarjeta-minuto" readonly style="background-color: #21262d; cursor: not-allowed; color: var(--text-secondary);">
            </div>
            <div class="modal-actions">
                <button id="btn-guardar-tarjeta" class="btn btn-success">Aplicar Tarjeta</button>
                <button class="btn btn-danger close-modal">Cancelar</button>
            </div>
        </div>
    </div>

    <div id="modal-asistencia" class="modal hidden">
        <div class="modal-content">
            <h3>👟 Registrar Asistencia</h3>
            <div class="form-group">
                <label for="asistencia-equipo">Equipo:</label>
                <select id="asistencia-equipo"></select>
            </div>
            <div class="form-group">
                <label for="asistencia-jugador">Asistidor:</label>
                <select id="asistencia-jugador"></select>
            </div>
            <div class="form-group">
                <label for="asistencia-minuto">Minuto:</label>
                <input type="text" id="asistencia-minuto" readonly style="background-color: #21262d; cursor: not-allowed; color: var(--text-secondary);">
            </div>
            <div class="modal-actions">
                <button id="btn-guardar-asistencia" class="btn btn-success">Guardar Asistencia</button>
                <button class="btn btn-danger close-modal">Cancelar</button>
            </div>
        </div>
    </div>

    <div id="status-toast"></div>

    <!-- SCRIPTS DE FIREBASE (Módulos de la versión 11) -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, onAuthStateChanged, signInWithCustomToken } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, setDoc, onSnapshot } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // ==============================================================================
        // CONFIGURACIÓN DE FIREBASE
        // ==============================================================================
        let firebaseConfig;
        const isCanvasEnvironment = typeof __firebase_config !== 'undefined';
        
        if (isCanvasEnvironment) {
            firebaseConfig = JSON.parse(__firebase_config);
        } else {
            // 👇👇👇 CUANDO LO SUBAS A GITHUB PAGES, REEMPLAZA ESTO CON TUS CLAVES DE FIREBASE 👇👇👇
            firebaseConfig = {
                apiKey: "TU_API_KEY",
                authDomain: "TU_PROYECTO.firebaseapp.com",
                projectId: "TU_PROYECTO",
                storageBucket: "TU_PROYECTO.appspot.com",
                messagingSenderId: "TUS_NUMEROS",
                appId: "TU_APP_ID"
            };
            // 👆👆👆 ----------------------------------------------------------------------- 👆👆👆
        }

        // Detectar si el usuario está usando las credenciales vacías de prueba (en local)
        const esCredencialesDePrueba = !isCanvasEnvironment && (firebaseConfig.apiKey === "TU_API_KEY" || firebaseConfig.apiKey.includes("TU_"));

        let auth = null;
        let db = null;
        let matchDocRef = null;
        let isFirebaseActive = false; // Indica si estamos usando base de datos real o simulación local

        // Base de datos de equipos
        const baseDatosEquipos = {
            "Bebitas FC": { logo: "https://upload.wikimedia.org/wikipedia/en/a/a7/Paris_Saint-Germain_F.C._logo.svg", jugadores: ["Alber Beyker Coronel Dávila", "Esmit Delgado Morales", "Peter Daniel Lavan Alva", "Daniel Colunche Vazques", "Steven Melendez Trigoso"] },
            "Field warriors FC": { logo: "https://upload.wikimedia.org/wikipedia/en/e/eb/Manchester_City_FC_badge.svg", jugadores: ["Gesler Alexis Epquin Arce", "Fray Euler Nuñez Colunche", "Adyen Jesús Vasquez Guevara", "Erick Yair Colunche Perales", "Franklin Maykol Mayta Valqui"] },
            "King Nasir FC": { logo: "https://upload.wikimedia.org/wikipedia/en/5/56/Real_Madrid_CF.svg", jugadores: ["Jacob Abdel Trigoso Rodríguez", "John Erik Torres Zelada", "Harlem Fernández Yoplac", "Fabio Stefano Muñoz López", "Cesar Snayder Delgado Castro", "Harli Llanca Valqui", "Jefferson David Chingo Quevara"] },
            "Atletico Cerveza FC": { logo: "https://upload.wikimedia.org/wikipedia/commons/0/05/FC_Internazionale_Milano_2021.svg", jugadores: ["Jhan Song Yu Cayaca Gómez", "Patrick Fernando Romero", "Jheampier Mosqueda Díaz", "Alexander Valqui Culqui", "Daniel Saldaña Castro"] },
            "Toque y Fuga": { logo: "https://upload.wikimedia.org/wikipedia/commons/1/1b/FC_Bayern_M%C3%BCnchen_logo_%282017%29.svg", jugadores: ["Erick Mario Guerrero Villanueva", "Berin Quintana Flores", "Joe Suarez Coronel", "Michael Chavez Villacrez", "Osmer Villegas Herrera"] },
            "Final Five": { logo: "https://upload.wikimedia.org/wikipedia/commons/d/d0/Logo_of_AC_Milan.svg", jugadores: ["Cleber Farid Zumaeta Saucedo", "Wilder Zuta Chavez", "Luiggi Bryan Lopez Llaja", "Jhon Freddy Soplin Rojas", "Yampier Yair Maldonado Diaz"] },
            "Los 4 fantasticos y Paveth": { logo: "https://upload.wikimedia.org/wikipedia/en/c/cc/Chelsea_FC.svg", jugadores: ["Royer Jhunior Villegas Villalobos", "Comeca Vargas Alvaro Andree", "Jhosber Daisson González Becerra", "Paveth Fabiano Quispe Flores", "Eyker Styvens Torres Palomino"] },
            "Los Migajeros": { logo: "https://upload.wikimedia.org/wikipedia/en/4/47/FC_Barcelona_%28crest%29.svg", jugadores: ["Dayron Vasquez Rafael", "Jhohann Villalobos Rojas", "Cristhian Vargas Briceño", "Erik Bardales Chávez", "Anderson Zumaeta Muñoz"] },
            "Apex FC": { logo: "https://upload.wikimedia.org/wikipedia/en/5/53/Arsenal_FC.svg", jugadores: ["Jhampier Goñas Vega", "Cristhofer Guevara Zuta", "Carlos Dayron Tapia Tarrilo", "Aron Yosias Pachas Colgantes", "Stalin Idrogo Vela"] }
        };

        const credencialesAdmins = { "Fray": "615974", "Alexis": "654321" };

        let usuarioAutenticado = null; // 'admin' | 'spectator'
        let nombreUsuario = "";
        let timerInterval = null; 

        // ESTADO GLOBAL REACTIVO
        let gameState = {
            partidoIniciado: false,
            equipoLocal: "", equipoVisitante: "",
            golesLocal: 0, golesVisitante: 0,
            minutos: 0, segundos: 0,
            adicionalAsignado: 0, adicionalTranscurrido: 0,
            periodoActual: "PRIMER_TIEMPO",
            enEjecucion: false,
            incidenciasLocal: [], incidenciasVisitante: []
        };

        // DOM Elements
        const loadingOverlay = document.getElementById("loading-overlay");
        const loginSection = document.getElementById("login-section");
        const appPanel = document.getElementById("app-panel");
        const matchPanel = document.getElementById("match-panel");
        const setupPanel = document.getElementById("setup-panel");
        const adminTimerControls = document.getElementById("admin-timer-controls");
        const adminActionCard = document.getElementById("admin-action-card");

        // Inicializar Auth de Firebase de manera protegida
        const initAuth = async () => {
            // Si el código está en local sin configurar Firebase, inicia el modo de prueba automáticamente
            if (esCredencialesDePrueba) {
                console.warn("Firebase no configurado. Iniciando aplicación en 'Modo Local Offline'.");
                isFirebaseActive = false;
                
                // Desvanecer overlay y mostrar login directo
                loadingOverlay.style.display = "none";
                loginSection.classList.remove("hidden");
                
                // Cambiar el indicador de sincronización a amarillo (Modo Local)
                const syncBadge = document.getElementById("sync-status");
                if (syncBadge) {
                    syncBadge.textContent = "🟡 (Modo Local)";
                    syncBadge.style.color = "var(--warning-yellow)";
                    syncBadge.title = "Ejecutando en Modo de Prueba Offline. Los datos solo se guardarán en tu dispositivo.";
                }
                showToast("Modo de Prueba Local Activo (Offline)");
                return;
            }

            try {
                const app = initializeApp(firebaseConfig);
                auth = getAuth(app);
                db = getFirestore(app);
                
                if (isCanvasEnvironment && typeof __app_id !== 'undefined') {
                    matchDocRef = doc(db, 'artifacts', __app_id, 'public', 'data', 'match', 'current');
                } else {
                    matchDocRef = doc(db, 'matches', 'current');
                }

                isFirebaseActive = true;

                if (isCanvasEnvironment && typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                    await signInWithCustomToken(auth, __initial_auth_token);
                } else {
                    await signInAnonymously(auth); 
                }
            } catch (error) {
                console.error("Error al conectar con Firebase:", error);
                
                // Si la conexión falla por red u otra causa, forzar inicio en Modo Local en vez de romperse
                isFirebaseActive = false;
                loadingOverlay.style.display = "none";
                loginSection.classList.remove("hidden");
                
                const syncBadge = document.getElementById("sync-status");
                if (syncBadge) {
                    syncBadge.textContent = "🔴 (Modo Local)";
                    syncBadge.style.color = "var(--alert-red)";
                    syncBadge.title = "No se pudo establecer conexión en vivo. Modo local activado de emergencia.";
                }
                showToast("Error de servidor. Activando modo offline local.");
            }
        };

        // Escuchar estado de autenticación (Cuando conectamos a Firebase)
        const listenAuth = () => {
            if (!isFirebaseActive || !auth) return;
            onAuthStateChanged(auth, (user) => {
                if (user) {
                    loadingOverlay.style.display = "none";
                    loginSection.classList.remove("hidden");
                    iniciarEscuchaBaseDeDatos();
                }
            });
        };

        // ============================================================================
        // LÓGICA DE SINCRONIZACIÓN CON FIREBASE
        // ============================================================================
        
        async function guardarEstadoEnFirebase() {
            if (usuarioAutenticado !== 'admin') return; 
            
            // Si no estamos conectados, actualizamos los paneles de manera local inmediata
            if (!isFirebaseActive) {
                renderizarUI();
                return;
            }

            try {
                document.getElementById("sync-status").textContent = "🔄";
                await setDoc(matchDocRef, gameState);
                document.getElementById("sync-status").textContent = "🟢";
                document.getElementById("sync-status").style.color = "var(--pitch-green)";
            } catch (e) {
                console.error("Error guardando en la nube:", e);
                document.getElementById("sync-status").textContent = "🔴";
                document.getElementById("sync-status").style.color = "var(--alert-red)";
            }
        }

        function iniciarEscuchaBaseDeDatos() {
            if (!isFirebaseActive || !db) return;
            
            onSnapshot(matchDocRef, (docSnap) => {
                if (docSnap.exists()) {
                    const dataServidor = docSnap.data();
                    gameState = dataServidor; 
                    renderizarUI(); 
                }
            }, (error) => {
                console.error("Error al recibir actualizaciones del partido:", error);
                showToast("Sincronización pausada temporalmente.");
            });
        }

        // Función auxiliar para manejar imágenes caídas o bloqueadas y reemplazarlas por un balón de fútbol
        window.handleImageError = function(imgElement) {
            imgElement.onerror = null; // Prevenir bucle infinito
            const parent = imgElement.parentElement;
            if (parent) {
                parent.innerHTML = "⚽";
            }
        };

        // ============================================================================
        // RENDERIZADO REACTIVO (Dibuja la UI basándose en gameState)
        // ============================================================================
        function renderizarUI() {
            if (usuarioAutenticado === null) return; 

            // Visibilidad de paneles
            if (!gameState.partidoIniciado) {
                if (usuarioAutenticado === 'admin') {
                    setupPanel.classList.remove("hidden");
                    matchPanel.classList.add("hidden");
                } else {
                    setupPanel.classList.add("hidden");
                    matchPanel.classList.remove("hidden");
                    document.getElementById("score-local-name").textContent = "Por empezar";
                    document.getElementById("score-visitante-name").textContent = "Por empezar";
                }
                return;
            }

            // Si el partido está iniciado, ocultar setup
            setupPanel.classList.add("hidden");
            matchPanel.classList.remove("hidden");

            // Nombres, Goles y Escudos
            document.getElementById("score-local-name").textContent = gameState.equipoLocal;
            document.getElementById("score-visitante-name").textContent = gameState.equipoVisitante;
            document.getElementById("score-local-goals").textContent = gameState.golesLocal;
            document.getElementById("score-visitante-goals").textContent = gameState.golesVisitante;

            // Escudos con manejador de errores seguro
            if(gameState.equipoLocal && baseDatosEquipos[gameState.equipoLocal]) {
                document.getElementById("shield-local").innerHTML = `<img src="${baseDatosEquipos[gameState.equipoLocal].logo}" alt="Escudo" crossorigin="anonymous" onerror="window.handleImageError(this)">`;
            }
            if(gameState.equipoVisitante && baseDatosEquipos[gameState.equipoVisitante]) {
                document.getElementById("shield-visitante").innerHTML = `<img src="${baseDatosEquipos[gameState.equipoVisitante].logo}" alt="Escudo" crossorigin="anonymous" onerror="window.handleImageError(this)">`;
            }

            // Cronómetro
            let minVis = gameState.minutos;
            if (gameState.adicionalTranscurrido > 0) {
                let base = (gameState.periodoActual === "PRIMER_TIEMPO") ? 15 : 30;
                minVis = `${base}+${gameState.adicionalTranscurrido}`;
            }
            document.getElementById("cronometro").textContent = `${minVis.toString().padStart(2, '0')}:${gameState.segundos.toString().padStart(2, '0')}`;

            // Periodo
            const periodoDisplay = document.getElementById("periodo-display");
            if(gameState.periodoActual === "PRIMER_TIEMPO") periodoDisplay.textContent = "PRIMER TIEMPO";
            else if(gameState.periodoActual === "DESCANSO") periodoDisplay.textContent = "DESCANSO";
            else if(gameState.periodoActual === "SEGUNDO_TIEMPO") periodoDisplay.textContent = "SEGUNDO TIEMPO";
            else if(gameState.periodoActual === "FINAL") {
                periodoDisplay.textContent = "FINAL DEL PARTIDO";
                document.getElementById("cronometro").textContent = "FINAL";
            }

            // Botón del temporizador (Solo Admin)
            if(usuarioAutenticado === 'admin') {
                document.getElementById("timer-toggle-btn").textContent = gameState.enEjecucion ? "⏸ Pausar Tiempo" : "▶️ Iniciar Tiempo";
            }

            // Renderizar Incidencias
            const renderLista = (lista, contenedorId) => {
                const contenedor = document.getElementById(contenedorId);
                contenedor.innerHTML = "";
                if(lista.length === 0) {
                    contenedor.innerHTML = '<p class="incidents-empty">Sin incidencias</p>';
                    return;
                }
                lista.forEach(e => {
                    let minMostrar = e.minuto.endsWith("'") ? e.minuto : `${e.minuto}'`;
                    contenedor.innerHTML += `<div class="incident-item"><span><strong>${e.icono} ${minMostrar}</strong> ${e.jugador} ${e.extra || ""}</span></div>`;
                });
            }
            renderLista(gameState.incidenciasLocal, "incidents-local");
            renderLista(gameState.incidenciasVisitante, "incidents-visitante");
        }


        // ============================================================================
        // LÓGICA DE LA INTERFAZ DE USUARIO (Eventos y Botones)
        // ============================================================================

        // --- Inicio de Sesión ---
        document.getElementById("btn-login").addEventListener("click", () => {
            const userVal = document.getElementById("login-username").value.trim();
            const passVal = document.getElementById("login-password").value;
            const errorMsg = document.getElementById("login-error-msg");

            if (credencialesAdmins[userVal] && credencialesAdmins[userVal] === passVal) {
                usuarioAutenticado = 'admin';
                nombreUsuario = userVal;
                errorMsg.classList.add("hidden");
                aplicarPermisosDeRol();
            } else {
                errorMsg.classList.remove("hidden");
            }
        });

        document.getElementById("btn-login-spectator").addEventListener("click", () => {
            usuarioAutenticado = 'spectator';
            nombreUsuario = "Espectador Invitado";
            aplicarPermisosDeRol();
        });

        document.getElementById("btn-logout").addEventListener("click", () => {
            usuarioAutenticado = null;
            if(timerInterval) { clearInterval(timerInterval); timerInterval = null; }
            appPanel.classList.add("hidden");
            loginSection.classList.remove("hidden");
        });

        function aplicarPermisosDeRol() {
            loginSection.classList.add("hidden");
            appPanel.classList.remove("hidden");
            
            document.getElementById("current-user-name").textContent = nombreUsuario;
            const roleDisp = document.getElementById("current-user-role");

            if (usuarioAutenticado === 'admin') {
                roleDisp.textContent = "Administrador";
                roleDisp.className = "role-badge role-admin";
                adminActionCard.classList.remove("hidden");
                adminTimerControls.classList.remove("hidden");
            } else {
                roleDisp.textContent = "Espectador (En Vivo)";
                roleDisp.className = "role-badge role-spectator";
                adminActionCard.classList.add("hidden");
                adminTimerControls.classList.add("hidden");
            }
            renderizarUI(); 
        }

        // --- Inicialización del partido (Solo Admin) ---
        const selLocal = document.getElementById("local-select");
        const selVis = document.getElementById("visitante-select");
        const btnStartMatch = document.getElementById("start-match-btn");

        Object.keys(baseDatosEquipos).forEach(eq => {
            selLocal.options.add(new Option(eq, eq));
            selVis.options.add(new Option(eq, eq));
        });

        const validarEquipos = () => {
            const loc = selLocal.value, vis = selVis.value;
            Array.from(selVis.options).forEach(o => o.disabled = (o.value === loc && o.value !== ""));
            Array.from(selLocal.options).forEach(o => o.disabled = (o.value === vis && o.value !== ""));
            btnStartMatch.disabled = !(loc && vis && loc !== vis);
        }
        selLocal.addEventListener("change", validarEquipos);
        selVis.addEventListener("change", validarEquipos);

        btnStartMatch.addEventListener("click", () => {
            if (usuarioAutenticado !== 'admin') return;
            gameState = {
                partidoIniciado: true,
                equipoLocal: selLocal.value,
                equipoVisitante: selVis.value,
                golesLocal: 0, golesVisitante: 0,
                minutos: 0, segundos: 0, adicionalAsignado: 0, adicionalTranscurrido: 0,
                periodoActual: "PRIMER_TIEMPO",
                enEjecucion: false,
                incidenciasLocal: [], incidenciasVisitante: []
            };
            guardarEstadoEnFirebase();
        });

        // --- Reloj y Control del Tiempo ---
        document.getElementById("timer-toggle-btn").addEventListener("click", () => {
            if (usuarioAutenticado !== 'admin') return;
            
            gameState.enEjecucion = !gameState.enEjecucion;
            
            if (gameState.enEjecucion) {
                if(!timerInterval) timerInterval = setInterval(correrReloj, 1000);
            } else {
                if(timerInterval) { clearInterval(timerInterval); timerInterval = null; }
            }
            guardarEstadoEnFirebase();
        });

        document.getElementById("add-time-btn").addEventListener("click", () => {
            if (usuarioAutenticado !== 'admin') return;
            gameState.adicionalAsignado++;
            showToast(`Tiempo extra total: +${gameState.adicionalAsignado} MIN`);
            guardarEstadoEnFirebase();
        });

        function correrReloj() {
            if(usuarioAutenticado !== 'admin' || !gameState.enEjecucion) return;

            gameState.segundos++;
            if (gameState.segundos >= 60) {
                gameState.segundos = 0;
                let limiteOriginal = (gameState.periodoActual === "PRIMER_TIEMPO") ? 15 : 30;

                if (gameState.minutos < limiteOriginal) {
                    gameState.minutos++;
                    if (gameState.minutos === limiteOriginal && gameState.adicionalAsignado === 0) {
                        avanzarEtapa();
                    }
                } else {
                    gameState.adicionalTranscurrido++;
                    if (gameState.adicionalTranscurrido >= gameState.adicionalAsignado) {
                        avanzarEtapa();
                    }
                }
            }
            guardarEstadoEnFirebase();
        }

        function avanzarEtapa() {
            if (gameState.periodoActual === "PRIMER_TIEMPO") {
                gameState.periodoActual = "DESCANSO";
                gameState.enEjecucion = false;
                if(timerInterval) { clearInterval(timerInterval); timerInterval = null; }
                
                setTimeout(() => {
                    gameState.periodoActual = "SEGUNDO_TIEMPO";
                    gameState.minutos = 15;
                    gameState.segundos = 0;
                    gameState.adicionalAsignado = 0;
                    gameState.adicionalTranscurrido = 0;
                    guardarEstadoEnFirebase();
                }, 10000); 

            } else if (gameState.periodoActual === "SEGUNDO_TIEMPO") {
                gameState.periodoActual = "FINAL";
                gameState.enEjecucion = false;
                if(timerInterval) { clearInterval(timerInterval); timerInterval = null; }
            }
        }

        // --- Registro de Goles / Tarjetas / Asistencias ---
        function obtenerMinutoTexto() {
            let limiteOriginal = (gameState.periodoActual === "PRIMER_TIEMPO") ? 15 : 30;
            if (gameState.minutos >= limiteOriginal) {
                let extra = gameState.adicionalTranscurrido + (gameState.segundos > 0 ? 1 : 0);
                if (extra === 0) extra = 1; 
                return `${limiteOriginal}+${extra}'`;
            } else {
                let minActual = gameState.minutos + (gameState.segundos > 0 ? 1 : 0);
                if (minActual === 0) minActual = 1; 
                return `${minActual}'`;
            }
        }

        function configurarModal(modalId, eqSelId, jugSelId, minInputId) {
            if (usuarioAutenticado !== 'admin') return;
            const eqSel = document.getElementById(eqSelId);
            const jugSel = document.getElementById(jugSelId);
            
            eqSel.innerHTML = "";
            eqSel.options.add(new Option(gameState.equipoLocal, gameState.equipoLocal));
            eqSel.options.add(new Option(gameState.equipoVisitante, gameState.equipoVisitante));

            const rellenarJugadores = () => {
                jugSel.innerHTML = "";
                baseDatosEquipos[eqSel.value].jugadores.forEach(j => jugSel.options.add(new Option(j, j)));
            };
            eqSel.onchange = rellenarJugadores;
            rellenarJugadores();

            document.getElementById(minInputId).value = obtenerMinutoTexto();
            document.getElementById(modalId).classList.remove("hidden");
        }

        document.querySelectorAll(".close-modal").forEach(b => {
            b.addEventListener("click", (e) => e.target.closest(".modal").classList.add("hidden"));
        });

        document.getElementById("btn-modal-gol").addEventListener("click", () => configurarModal("modal-gol", "gol-equipo", "gol-jugador", "gol-minuto"));
        document.getElementById("btn-modal-tarjeta").addEventListener("click", () => configurarModal("modal-tarjeta", "tarjeta-equipo", "tarjeta-jugador", "tarjeta-minuto"));
        document.getElementById("btn-modal-asistencia").addEventListener("click", () => configurarModal("modal-asistencia", "asistencia-equipo", "asistencia-jugador", "asistencia-minuto"));

        function agregarIncidencia(evento) {
            let lista = (evento.equipo === gameState.equipoLocal) ? gameState.incidenciasLocal : gameState.incidenciasVisitante;
            lista.push(evento);
            lista.sort((a, b) => parseFloat(a.minuto.replace('+', '.').replace("'", "")) - parseFloat(b.minuto.replace('+', '.').replace("'", "")));
        }

        document.getElementById("btn-guardar-gol").addEventListener("click", () => {
            if (usuarioAutenticado !== 'admin') return;
            let eq = document.getElementById("gol-equipo").value;
            let jug = document.getElementById("gol-jugador").value;
            
            if (eq === gameState.equipoLocal) gameState.golesLocal++; 
            else gameState.golesVisitante++;

            agregarIncidencia({ icono: "⚽", minuto: document.getElementById("gol-minuto").value, jugador: jug, equipo: eq });
            document.getElementById("modal-gol").classList.add("hidden");
            guardarEstadoEnFirebase();
        });

        document.getElementById("btn-guardar-tarjeta").addEventListener("click", () => {
            if (usuarioAutenticado !== 'admin') return;
            let tipo = document.getElementById("tarjeta-tipo").value;
            let eq = document.getElementById("tarjeta-equipo").value;
            let jug = document.getElementById("tarjeta-jugador").value;
            
            agregarIncidencia({ icono: tipo.includes("Amarilla") ? "🟨" : "🟥", minuto: document.getElementById("tarjeta-minuto").value, jugador: jug, equipo: eq });
            document.getElementById("modal-tarjeta").classList.add("hidden");
            guardarEstadoEnFirebase();
        });

        document.getElementById("btn-guardar-asistencia").addEventListener("click", () => {
            if (usuarioAutenticado !== 'admin') return;
            let eq = document.getElementById("asistencia-equipo").value;
            let jug = document.getElementById("asistencia-jugador").value;
            
            agregarIncidencia({ icono: "👟", minuto: document.getElementById("asistencia-minuto").value, jugador: jug, equipo: eq, extra: "(Asist.)" });
            document.getElementById("modal-asistencia").classList.add("hidden");
            guardarEstadoEnFirebase();
        });

        // --- Utilidades ---
        function showToast(message) {
            const toast = document.getElementById("status-toast");
            toast.textContent = message;
            toast.style.display = "block";
            setTimeout(() => { toast.style.display = "none"; }, 3000);
        }

        // --- Compartir (Exportación a PNG con html2canvas) ---
        document.getElementById("btn-share-result").addEventListener("click", () => {
            if (!gameState.partidoIniciado) return;
            const btnShare = document.getElementById("btn-share-result");
            btnShare.disabled = true;
            btnShare.textContent = "⏳ Generando imagen...";

            const controlsDisplay = adminTimerControls.style.display;
            const shareDisplay = document.getElementById("share-action-container").style.display;
            adminTimerControls.style.display = "none";
            document.getElementById("share-action-container").style.display = "none";

            const nombreArchivo = `Resultado_${gameState.equipoLocal.replace(/\s+/g,"")}_vs_${gameState.equipoVisitante.replace(/\s+/g,"")}.png`;

            html2canvas(document.getElementById("capture-area"), {
                useCORS: true, allowTaint: false, backgroundColor: "#161b22", scale: 2
            }).then(canvas => {
                adminTimerControls.style.display = controlsDisplay;
                document.getElementById("share-action-container").style.display = shareDisplay;
                btnShare.disabled = false; btnShare.textContent = "📤 Compartir Resultado";

                if (navigator.canShare && navigator.share) {
                    canvas.toBlob(blob => {
                        if (!blob) return;
                        const file = new File([blob], nombreArchivo, { type: "image/png" });
                        if (navigator.canShare({ files: [file] })) {
                            navigator.share({
                                files: [file],
                                title: `Resultado: ${gameState.equipoLocal} vs ${gameState.equipoVisitante}`,
                                text: `🔥 ¡Resultado al momento! ${gameState.equipoLocal} (${gameState.golesLocal}) - (${gameState.golesVisitante}) ${gameState.equipoVisitante}.`
                            }).then(() => showToast("¡Resultado compartido con éxito!")).catch(err => {
                                console.warn("Compartido cancelado, descargando...", err);
                                descargarImagen(canvas, nombreArchivo);
                            });
                        } else descargarImagen(canvas, nombreArchivo);
                    }, "image/png");
                } else descargarImagen(canvas, nombreArchivo);
            }).catch(err => {
                adminTimerControls.style.display = controlsDisplay;
                document.getElementById("share-action-container").style.display = shareDisplay;
                btnShare.disabled = false; btnShare.textContent = "📤 Compartir Resultado";
                showToast("Error al generar imagen.");
            });
        });

        function descargarImagen(canvas, fileName) {
            const link = document.createElement("a");
            link.download = fileName; link.href = canvas.toDataURL("image/png");
            document.body.appendChild(link); link.click(); document.body.removeChild(link);
        }

        // Ejecutar inicialización de seguridad
        const runSetup = async () => {
            await initAuth();
            listenAuth();
        };
        runSetup();
    </script>
</body>
</html>
