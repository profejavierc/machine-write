<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NEXUS PLOTTER // SYSTEM CONTROL</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Consolas', 'Courier New', monospace;
            background: linear-gradient(135deg, #0B0E14 0%, #1a1f2e 100%);
            color: #FFFFFF;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }

        .container {
            background: #121824;
            border: 2px solid #00E5FF;
            border-radius: 20px;
            padding: 30px;
            width: 100%;
            max-width: 500px;
            box-shadow: 0 0 40px rgba(0, 229, 255, 0.3);
        }

        .header {
            text-align: center;
            margin-bottom: 25px;
        }

        .title {
            font-size: 24px;
            font-weight: bold;
            color: #FFFFFF;
            margin-bottom: 8px;
            text-shadow: 0 0 10px rgba(0, 229, 255, 0.5);
        }

        .subtitle {
            font-size: 12px;
            color: #00E5FF;
            letter-spacing: 1px;
        }

        .section {
            margin-bottom: 20px;
        }

        .label {
            display: block;
            font-size: 11px;
            font-weight: bold;
            color: #A0AAB0;
            margin-bottom: 8px;
            letter-spacing: 0.5px;
        }

        .connection-group {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
        }

        .btn {
            padding: 12px 20px;
            border: none;
            border-radius: 8px;
            font-family: 'Consolas', monospace;
            font-size: 13px;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .btn-primary {
            background: #00E5FF;
            color: #0B0E14;
            width: 100%;
            margin-bottom: 10px;
        }

        .btn-primary:hover {
            background: #00B0FF;
            box-shadow: 0 0 20px rgba(0, 229, 255, 0.5);
        }

        .btn-secondary {
            background: #1A2332;
            color: #FFFFFF;
            border: 1px solid #00E5FF;
            width: 50px;
        }

        .btn-secondary:hover {
            background: #00E5FF;
            color: #0B0E14;
        }

        .btn-action {
            background: #FFFFFF;
            color: #0B0E14;
            width: 100%;
            padding: 15px;
            font-size: 14px;
            margin-top: 10px;
        }

        .btn-action:hover {
            background: #D5E5FF;
            box-shadow: 0 0 25px rgba(255, 255, 255, 0.4);
        }

        .btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }

        .input-text {
            width: 100%;
            padding: 12px 15px;
            background: #1A2332;
            border: 1px solid #00E5FF;
            border-radius: 8px;
            color: #FFFFFF;
            font-family: 'Consolas', monospace;
            font-size: 14px;
            outline: none;
            transition: all 0.3s ease;
        }

        .input-text:focus {
            border-color: #00E5FF;
            box-shadow: 0 0 15px rgba(0, 229, 255, 0.3);
        }

        .input-text::placeholder {
            color: #556677;
        }

        .separator {
            height: 2px;
            background: #1A2332;
            margin: 20px 0;
        }

        .status {
            text-align: center;
            font-size: 12px;
            font-weight: bold;
            padding: 10px;
            border-radius: 8px;
            margin-top: 15px;
        }

        .status-disconnected {
            color: #FF3366;
            background: rgba(255, 51, 102, 0.1);
            border: 1px solid #FF3366;
        }

        .status-connected {
            color: #00E5FF;
            background: rgba(0, 229, 255, 0.1);
            border: 1px solid #00E5FF;
        }

        .status-processing {
            color: #FFD700;
            background: rgba(255, 215, 0, 0.1);
            border: 1px solid #FFD700;
        }

        .warning {
            background: rgba(255, 152, 0, 0.1);
            border: 1px solid #FF9800;
            border-radius: 8px;
            padding: 12px;
            margin-bottom: 20px;
            font-size: 11px;
            color: #FF9800;
            text-align: center;
        }

        .log {
            background: #0B0E14;
            border: 1px solid #1A2332;
            border-radius: 8px;
            padding: 10px;
            margin-top: 15px;
            max-height: 150px;
            overflow-y: auto;
            font-size: 10px;
            color: #A0AAB0;
        }

        .log-entry {
            margin-bottom: 5px;
            padding: 3px 0;
            border-bottom: 1px solid #1A2332;
        }

        .log-entry:last-child {
            border-bottom: none;
        }

        .log-time {
            color: #00E5FF;
            margin-right: 8px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <div class="title">⚡ NEXUS PLOTTER CNC</div>
            <div class="subtitle">[ INTERFAZ WEB DE CONTROL CNC / ARDUINO ]</div>
        </div>

        <div id="warning" class="warning" style="display: none;">
            ⚠️ Tu navegador no soporta Web Serial API.<br>
            Usa Chrome, Edge u Opera para controlar el plotter.
        </div>

        <div class="section">
            <label class="label">> CONEXIÓN SERIAL:</label>
            <button id="btnConnect" class="btn btn-primary">🔌 CONECTAR ARDUINO</button>
        </div>

        <div class="separator"></div>

        <div class="section">
            <label class="label">> COMANDO DE TEXTO A TRAZAR:</label>
            <input 
                type="text" 
                id="textInput" 
                class="input-text" 
                placeholder="EJ: SISTEMA 2026"
                maxlength="100"
            >
            <button id="btnSend" class="btn btn-action" disabled>
                ▶ INICIAR TRAZADO CNC
            </button>
        </div>

        <div id="status" class="status status-disconnected">
            ESTADO: DESCONECTADO 🔴
        </div>

        <div id="log" class="log">
            <div class="log-entry">
                <span class="log-time">[SYSTEM]</span>
                Esperando conexión...
            </div>
        </div>
    </div>

    <script>
        let port = null;
        let writer = null;
        let isConnected = false;

        const btnConnect = document.getElementById('btnConnect');
        const btnSend = document.getElementById('btnSend');
        const textInput = document.getElementById('textInput');
        const status = document.getElementById('status');
        const log = document.getElementById('log');
        const warning = document.getElementById('warning');

        // Verificar soporte de Web Serial API
        if (!('serial' in navigator)) {
            warning.style.display = 'block';
            btnConnect.disabled = true;
            btnConnect.textContent = '❌ NAVEGADOR NO COMPATIBLE';
        }

        // Función para agregar entradas al log
        function addLog(message, type = 'INFO') {
            const time = new Date().toLocaleTimeString();
            const entry = document.createElement('div');
            entry.className = 'log-entry';
            entry.innerHTML = `<span class="log-time">[${time}]</span> ${message}`;
            log.appendChild(entry);
            log.scrollTop = log.scrollHeight;
        }

        // Actualizar estado de conexión
        function updateStatus(text, type) {
            status.textContent = text;
            status.className = `status status-${type}`;
        }

        // Conectar al Arduino
        btnConnect.addEventListener('click', async () => {
            try {
                if (isConnected) {
                    // Desconectar
                    if (writer) {
                        await writer.close();
                        writer.releaseLock();
                    }
                    await port.close();
                    port = null;
                    isConnected = false;
                    btnConnect.textContent = '🔌 CONECTAR ARDUINO';
                    btnSend.disabled = true;
                    updateStatus('ESTADO: DESCONECTADO 🔴', 'disconnected');
                    addLog('Desconectado del Arduino', 'DISCONNECT');
                } else {
                    // Conectar
                    port = await navigator.serial.requestPort();
                    await port.open({ baudRate: 9600 });
                    
                    const textEncoder = new TextEncoderStream();
                    const writableStreamClosed = textEncoder.readable.pipeTo(port.writable);
                    writer = textEncoder.writable.getWriter();
                    
                    isConnected = true;
                    btnConnect.textContent = '🔌 DESCONECTAR';
                    btnSend.disabled = false;
                    updateStatus('ESTADO: EN LÍNEA 🟢', 'connected');
                    addLog('Conectado al Arduino exitosamente', 'CONNECT');
                }
            } catch (error) {
                addLog(`Error: ${error.message}`, 'ERROR');
                updateStatus('ERROR DE CONEXIÓN 🔴', 'disconnected');
            }
        });

        // Enviar texto al Arduino
        btnSend.addEventListener('click', async () => {
            if (!isConnected) {
                alert('⚠️ Debes conectar el Arduino primero');
                return;
            }

            const texto = textInput.value.trim();
            if (!texto) {
                alert('⚠️ Ingresa un texto para trazar');
                return;
            }

            // Filtrar solo letras y espacios (igual que la app Python)
            const textoFiltrado = texto.replace(/[^a-zA-Z\s]/g, '');
            
            if (!textoFiltrado) {
                alert('⚠️ El texto no contiene caracteres válidos (solo letras y espacios)');
                return;
            }

            try {
                btnSend.disabled = true;
                btnSend.textContent = '⚡ PROCESANDO TRAZADO...';
                updateStatus('ESTADO: TRAZANDO... ⚡', 'processing');
                addLog(`Enviando: "${textoFiltrado}"`, 'SEND');

                // Enviar texto al Arduino
                await writer.write(textoFiltrado);
                
                // Calcular tiempo estimado (0.6 segundos por carácter)
                const tiempoEspera = Math.max(1000, textoFiltrado.length * 600);
                
                addLog(`Trazado iniciado. Tiempo estimado: ${(tiempoEspera/1000).toFixed(1)}s`, 'INFO');
                
                // Esperar a que termine el trazado
                setTimeout(() => {
                    btnSend.disabled = false;
                    btnSend.textContent = '▶ INICIAR TRAZADO CNC';
                    updateStatus('ESTADO: EN LÍNEA 🟢', 'connected');
                    addLog('Trazado completado', 'COMPLETE');
                    textInput.value = '';
                }, tiempoEspera);

            } catch (error) {
                addLog(`Error de envío: ${error.message}`, 'ERROR');
                btnSend.disabled = false;
                btnSend.textContent = '▶ INICIAR TRAZADO CNC';
                updateStatus('ERROR DE COMUNICACIÓN 🔴', 'disconnected');
            }
        });

        // Permitir enviar con Enter
        textInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter' && !btnSend.disabled) {
                btnSend.click();
            }
        });

        // Log inicial
        addLog('Sistema NEXUS PLOTTER iniciado', 'SYSTEM');
    </script>
</body>
</html>
