<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interactive Particle Simulation</title>
    <style>
        :root {
            --primary: #1e3a8a;
            --secondary: #3b82f6;
            --accent: #93c5fd;
            --background: #e0f2fe;
            --sidebar-bg: rgba(255, 255, 255, 0.95);
            --text: #1e293b;
            --glow: rgba(59, 130, 246, 0.5);
        }

        * {
            box-sizing: border-box;
        }

        body {
            font-family: 'Poppins', sans-serif;
            margin: 0;
            padding: 0;
            background: linear-gradient(135deg, var(--background), #bae6fd);
            color: var(--text);
            display: flex;
            flex-direction: column;
            height: 100vh;
            overflow: hidden;
        }

        header {
            background: linear-gradient(90deg, var(--primary), var(--secondary));
            color: white;
            padding: 1rem;
            text-align: center;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
        }

        header h1 {
            margin: 0;
            font-size: clamp(1.5rem, 5vw, 2.2rem);
            font-weight: 700;
            letter-spacing: 0.5px;
        }

        header p {
            margin: 0.3rem 0 0;
            font-size: clamp(0.9rem, 3vw, 1.1rem);
            opacity: 0.9;
        }

        .container {
            display: flex;
            flex-grow: 1;
            width: 100%;
            height: calc(100vh - 85px);
        }

        .canvas-container {
            flex-grow: 1;
            background: #000000;
            position: relative;
            margin: 1rem;
            border-radius: 12px;
            box-shadow: 0 0 20px var(--glow);
            transition: box-shadow 0.3s ease;
            min-height: 200px;
        }

        #particleCanvas {
            width: 100%;
            height: 100%;
            cursor: default;
            border-radius: 12px;
        }

        .sidebar {
            width: 280px;
            background: linear-gradient(180deg, var(--sidebar-bg), rgba(230, 242, 255, 0.9));
            backdrop-filter: blur(12px);
            padding: 1.2rem;
            display: flex;
            flex-direction: column;
            gap: 0.5rem;
            margin: 1rem 1rem 1rem 0;
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
            max-height: calc(100vh - 85px);
            overflow-y: auto;
        }

        .btn {
            background-color: var(--secondary);
            color: white;
            padding: 0.7rem;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            font-size: 0.9rem;
            font-weight: 500;
            margin: 0.2rem 0;
            text-align: left;
            transition: all 0.3s ease;
            width: 100%;
        }

        .btn:hover {
            background-color: var(--primary);
            transform: translateY(-2px) scale(1.02);
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
        }

        .btn:active {
            transform: translateY(0);
        }

        .control-group {
            display: flex;
            flex-direction: column;
            margin: 0.6rem 0;
        }

        label {
            font-size: 0.9rem;
            font-weight: 600;
            margin-bottom: 0.3rem;
            color: var(--text);
        }

        input[type="range"] {
            width: 100%;
            accent-color: var(--secondary);
            cursor: pointer;
        }

        .value {
            font-size: 0.85rem;
            margin-top: 0.3rem;
            color: var(--text);
            opacity: 0.85;
        }

        /* Mobile sidebar toggle */
        .sidebar-toggle {
            display: none;
            position: fixed;
            bottom: 20px;
            right: 20px;
            z-index: 1000;
            background-color: var(--primary);
            color: white;
            width: 50px;
            height: 50px;
            border-radius: 50%;
            border: none;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
            cursor: pointer;
            font-size: 1.5rem;
            padding: 0;
        }

        @media (max-width: 768px) {
            .container {
                flex-direction: column;
                height: auto;
            }

            .sidebar {
                width: auto;
                margin: 0 1rem 1rem 1rem;
                max-height: 300px;
            }

            .canvas-container {
                margin: 1rem;
                min-height: 50vh;
            }
            
            .sidebar-toggle {
                display: block;
            }
            
            .sidebar.hidden {
                display: none;
            }
        }

        @media (max-width: 480px) {
            header {
                padding: 0.8rem;
            }
            
            .canvas-container {
                margin: 0.8rem;
                min-height: 40vh;
            }
            
            .sidebar {
                padding: 1rem;
                margin: 0 0.8rem 0.8rem 0.8rem;
            }
            
            .btn {
                padding: 0.6rem;
                font-size: 0.85rem;
            }
        }

        @media (orientation: landscape) and (max-height: 500px) {
            header {
                padding: 0.5rem;
            }
            
            header h1 {
                font-size: 1.2rem;
            }
            
            header p {
                font-size: 0.8rem;
            }
            
            .container {
                height: calc(100vh - 60px);
            }
            
            .sidebar {
                width: 220px;
                padding: 0.8rem;
            }
            
            .btn {
                padding: 0.5rem;
                margin: 0.1rem 0;
            }
            
            .control-group {
                margin: 0.4rem 0;
            }
        }
    </style>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600;700&display=swap" rel="stylesheet">
</head>
<body>
    <header>
        <h1>Interactive Particle Simulation</h1>
        <p>Create and explore particle effects with ease</p>
    </header>
    <div class="container">
        <div class="canvas-container">
            <canvas id="particleCanvas"></canvas>
        </div>
        <div class="sidebar">
            <button id="toggleVelocityBtn" class="btn">Toggle Show Velocity</button>
            <button id="addEmitterBtn" class="btn">Add Emitter</button>
            <button id="addOscEmitterBtn" class="btn">Add Oscillating Emitter</button>
            <button id="addPulseEmitterBtn" class="btn">Add Pulse Emitter</button>
            <button id="resetBtn" class="btn">Reset</button>
            <button id="toggleGravityBtn" class="btn">Toggle Gravity</button>
            <button id="pauseResumeBtn" class="btn">Pause/Resume</button>
            <button id="stepBtn" class="btn">Step</button>
            <button id="savePresetBtn" class="btn">Save Preset</button>
            <button id="loadPresetBtn" class="btn">Load Preset</button>
            <button id="clearBtn" class="btn">Clear</button>
            <div class="control-group">
                <label for="velocityControl">VELOCITY:</label>
                <input type="range" id="velocityControl" min="1" max="10" value="3">
                <span id="velocityValue" class="value">3</span>
            </div>
            <div class="control-group">
                <label for="spreadControl">SPREAD ANGLE:</label>
                <input type="range" id="spreadControl" min="0" max="6.28" step="0.01" value="1">
                <span id="spreadValue" class="value">1rad</span>
            </div>
            <div class="control-group">
                <label for="angleControl">EMISSION ANGLE:</label>
                <input type="range" id="angleControl" min="0" max="6.28" step="0.01" value="0">
                <span id="angleValue" class="value">0rad</span>
            </div>
            <div class="control-group">
                <label for="frequencyControl">PULSE FREQUENCY:</label>
                <input type="range" id="frequencyControl" min="0.01" max="0.1" step="0.01" value="0.02">
                <span id="frequencyValue" class="value">0.02Hz</span>
            </div>
            <button id="deleteEmitterBtn" class="btn">Delete Emitter</button>
        </div>
        <button class="sidebar-toggle" id="sidebarToggle">≡</button>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const canvas = document.getElementById('particleCanvas');
            const ctx = canvas.getContext('2d');
            const sidebar = document.querySelector('.sidebar');
            const sidebarToggle = document.getElementById('sidebarToggle');
            const toggleVelocityBtn = document.getElementById('toggleVelocityBtn');
            const addEmitterBtn = document.getElementById('addEmitterBtn');
            const addOscEmitterBtn = document.getElementById('addOscEmitterBtn');
            const addPulseEmitterBtn = document.getElementById('addPulseEmitterBtn');
            const resetBtn = document.getElementById('resetBtn');
            const toggleGravityBtn = document.getElementById('toggleGravityBtn');
            const pauseResumeBtn = document.getElementById('pauseResumeBtn');
            const stepBtn = document.getElementById('stepBtn');
            const savePresetBtn = document.getElementById('savePresetBtn');
            const loadPresetBtn = document.getElementById('loadPresetBtn');
            const clearBtn = document.getElementById('clearBtn');
            const deleteEmitterBtn = document.getElementById('deleteEmitterBtn');
            const velocityControl = document.getElementById('velocityControl');
            const spreadControl = document.getElementById('spreadControl');
            const angleControl = document.getElementById('angleControl');
            const frequencyControl = document.getElementById('frequencyControl');
            const velocityValue = document.getElementById('velocityValue');
            const spreadValue = document.getElementById('spreadValue');
            const angleValue = document.getElementById('angleValue');
            const frequencyValue = document.getElementById('frequencyValue');

            let particleSystem = {
                particles: [],
                emitters: [],
                width: 0,
                height: 0,
                gravityEnabled: false,
                isPaused: false,
                showVelocity: false
            };

            let controls = {
                velocity: 3,
                spread: 1,
                angle: 0,
                frequency: 0.02
            };

            let pendingEmitterType = null;
            let selectedEmitterIndex = -1;
            let isDragging = false;
            let draggedEmitterIndex = -1;
            let isMobile = window.innerWidth <= 768;

            // Toggle sidebar visibility on mobile
            sidebarToggle.addEventListener('click', function() {
                sidebar.classList.toggle('hidden');
            });

            function resizeCanvas() {
                const container = document.querySelector('.canvas-container');
                canvas.width = container.clientWidth;
                canvas.height = container.clientHeight;
                particleSystem.width = canvas.width;
                particleSystem.height = canvas.height;
                
                // Check if the screen size is mobile
                isMobile = window.innerWidth <= 768;
                if (isMobile && !sidebar.classList.contains('hidden')) {
                    sidebar.classList.add('hidden');
                }
            }

            window.addEventListener('resize', resizeCanvas);
            window.addEventListener('orientationchange', function() {
                // Small delay to allow the browser to update dimensions
                setTimeout(resizeCanvas, 100);
            });
            
            resizeCanvas();

            velocityControl.addEventListener('input', function() {
                controls.velocity = parseFloat(this.value);
                velocityValue.textContent = controls.velocity.toFixed(1);
                updateEmitters();
            });

            spreadControl.addEventListener('input', function() {
                controls.spread = parseFloat(this.value);
                spreadValue.textContent = controls.spread.toFixed(2) + 'rad';
                updateEmitters();
            });

            angleControl.addEventListener('input', function() {
                controls.angle = parseFloat(this.value);
                angleValue.textContent = controls.angle.toFixed(2) + 'rad';
                updateEmitters();
            });

            frequencyControl.addEventListener('input', function() {
                controls.frequency = parseFloat(this.value);
                frequencyValue.textContent = controls.frequency.toFixed(2) + 'Hz';
                updateEmitters();
            });

            function updateEmitters() {
                for (let emitter of particleSystem.emitters) {
                    emitter.speed = controls.velocity;
                    emitter.spread = controls.spread;
                    emitter.angle = controls.angle;
                    if (emitter.type === 'pulse') {
                        emitter.frequency = controls.frequency;
                    }
                }
            }

            toggleVelocityBtn.addEventListener('click', function() {
                particleSystem.showVelocity = !particleSystem.showVelocity;
            });

            addEmitterBtn.addEventListener('click', function() {
                pendingEmitterType = 'basic';
                canvas.style.cursor = 'crosshair';
                if (isMobile) sidebar.classList.add('hidden');
            });

            addOscEmitterBtn.addEventListener('click', function() {
                pendingEmitterType = 'oscillating';
                canvas.style.cursor = 'crosshair';
                if (isMobile) sidebar.classList.add('hidden');
            });

            addPulseEmitterBtn.addEventListener('click', function() {
                pendingEmitterType = 'pulse';
                canvas.style.cursor = 'crosshair';
                if (isMobile) sidebar.classList.add('hidden');
            });

            resetBtn.addEventListener('click', function() {
                particleSystem.particles = [];
                particleSystem.emitters = [];
                particleSystem.gravityEnabled = false;
                particleSystem.isPaused = false;
                particleSystem.showVelocity = false;
                controls.velocity = 3;
                controls.spread = 1;
                controls.angle = 0;
                controls.frequency = 0.02;
                velocityControl.value = 3;
                spreadControl.value = 1;
                angleControl.value = 0;
                frequencyControl.value = 0.02;
                velocityValue.textContent = '3';
                spreadValue.textContent = '1rad';
                angleValue.textContent = '0rad';
                frequencyValue.textContent = '0.02Hz';
                selectedEmitterIndex = -1;
                draggedEmitterIndex = -1;
            });

            toggleGravityBtn.addEventListener('click', function() {
                particleSystem.gravityEnabled = !particleSystem.gravityEnabled;
            });

            pauseResumeBtn.addEventListener('click', function() {
                particleSystem.isPaused = !particleSystem.isPaused;
                pauseResumeBtn.textContent = particleSystem.isPaused ? 'Resume' : 'Pause';
            });

            stepBtn.addEventListener('click', function() {
                if (particleSystem.isPaused) {
                    updateParticleSystem();
                }
            });

            deleteEmitterBtn.addEventListener('click', function() {
                if (selectedEmitterIndex !== -1) {
                    particleSystem.emitters.splice(selectedEmitterIndex, 1);
                    selectedEmitterIndex = -1;
                    draggedEmitterIndex = -1;
                }
            });

            // Handle touch events for mobile
            canvas.addEventListener('touchstart', handleTouchStart, { passive: false });
            canvas.addEventListener('touchmove', handleTouchMove, { passive: false });
            canvas.addEventListener('touchend', handleTouchEnd);

            function handleTouchStart(event) {
                event.preventDefault();
                const touch = event.touches[0];
                const rect = canvas.getBoundingClientRect();
                const x = touch.clientX - rect.left;
                const y = touch.clientY - rect.top;
                
                handleInteractionStart(x, y);
            }

            function handleTouchMove(event) {
                if (isDragging && draggedEmitterIndex !== -1) {
                    event.preventDefault();
                    const touch = event.touches[0];
                    const rect = canvas.getBoundingClientRect();
                    const x = touch.clientX - rect.left;
                    const y = touch.clientY - rect.top;
                    
                    updateDraggedEmitter(x, y);
                }
            }

            function handleTouchEnd() {
                isDragging = false;
                draggedEmitterIndex = -1;
                if (!pendingEmitterType) {
                    canvas.style.cursor = 'default';
                }
            }

            canvas.addEventListener('mousedown', function(event) {
                const rect = canvas.getBoundingClientRect();
                const x = event.clientX - rect.left;
                const y = event.clientY - rect.top;
                
                handleInteractionStart(x, y);
            });

            function handleInteractionStart(x, y) {
                if (pendingEmitterType) {
                    if (x >= 0 && x <= particleSystem.width && y >= 0 && y <= particleSystem.height) {
                        if (pendingEmitterType === 'basic') {
                            particleSystem.emitters.push({
                                type: 'basic',
                                x: x,
                                y: y,
                                speed: controls.velocity,
                                spread: controls.spread,
                                angle: controls.angle
                            });
                        } else if (pendingEmitterType === 'oscillating') {
                            particleSystem.emitters.push({
                                type: 'oscillating',
                                x: x,
                                y: y,
                                meanX: x,
                                meanY: y,
                                speed: controls.velocity,
                                spread: controls.spread,
                                angle: controls.angle,
                                amplitude: 50,
                                frequency: 0.01,
                                theta: 0
                            });
                        } else if (pendingEmitterType === 'pulse') {
                            particleSystem.emitters.push({
                                type: 'pulse',
                                x: x,
                                y: y,
                                speed: controls.velocity,
                                spread: controls.spread,
                                angle: controls.angle,
                                frequency: controls.frequency,
                                lastUpdateTime: 0,
                                shouldEmit: true
                            });
                        }
                    }
                    pendingEmitterType = null;
                    canvas.style.cursor = 'default';
                } else {
                    // Check for emitter to drag or select
                    for (let i = 0; i < particleSystem.emitters.length; i++) {
                        const emitter = particleSystem.emitters[i];
                        const dx = x - emitter.x;
                        const dy = y - emitter.y;
                        const distance = Math.sqrt(dx * dx + dy * dy);
                        if (distance < 15) { // Increased touch target size
                            isDragging = true;
                            draggedEmitterIndex = i;
                            selectedEmitterIndex = i;
                            canvas.style.cursor = 'move';
                            break;
                        }
                    }
                }
            }

            canvas.addEventListener('mousemove', function(event) {
                if (isDragging && draggedEmitterIndex !== -1) {
                    const rect = canvas.getBoundingClientRect();
                    const x = event.clientX - rect.left;
                    const y = event.clientY - rect.top;
                    
                    updateDraggedEmitter(x, y);
                }
            });

            function updateDraggedEmitter(x, y) {
                const emitter = particleSystem.emitters[draggedEmitterIndex];
                if (x >= 0 && x <= particleSystem.width && y >= 0 && y <= particleSystem.height) {
                    emitter.x = x;
                    emitter.y = y;
                    if (emitter.type === 'oscillating') {
                        emitter.meanX = x;
                        emitter.meanY = y;
                    }
                }
            }

            canvas.addEventListener('mouseup', function() {
                isDragging = false;
                draggedEmitterIndex = -1;
                if (!pendingEmitterType) {
                    canvas.style.cursor = 'default';
                }
            });

            savePresetBtn.addEventListener('click', function() {
                let presetData = "Emitters\n";
                for (let emitter of particleSystem.emitters) {
                    presetData += `type=${emitter.type},x=${emitter.x},y=${emitter.y},speed=${emitter.speed},spread=${emitter.spread},angle=${emitter.angle}`;
                    if (emitter.type === 'oscillating') {
                        presetData += `,amplitude=${emitter.amplitude},frequency=${emitter.frequency},theta=${emitter.theta}`;
                    } else if (emitter.type === 'pulse') {
                        presetData += `,frequency=${emitter.frequency}`;
                    }
                    presetData += "\n";
                }
                presetData += "Settings\n";
                presetData += `GravityEnabled=${particleSystem.gravityEnabled}\n`;

                const blob = new Blob([presetData], { type: 'text/plain' });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = 'preset.txt';
                a.click();
                URL.revokeObjectURL(url);
            });

            loadPresetBtn.addEventListener('click', function() {
                const input = document.createElement('input');
                input.type = 'file';
                input.accept = '.txt';
                input.onchange = function(event) {
                    const file = event.target.files[0];
                    if (file) {
                        const reader = new FileReader();
                        reader.onload = function(e) {
                            const lines = e.target.result.split('\n');
                            let section = null;
                            particleSystem.emitters = [];

                            for (let line of lines) {
                                if (line === 'Emitters') {
                                    section = 'Emitters';
                                } else if (line === 'Settings') {
                                    section = 'Settings';
                                } else if (section === 'Emitters' && line) {
                                    const props = {};
                                    line.split(',').forEach(prop => {
                                        const [key, value] = prop.split('=');
                                        props[key] = value;
                                    });

                                    const emitter = {
                                        type: props.type,
                                        x: parseFloat(props.x),
                                        y: parseFloat(props.y),
                                        speed: parseFloat(props.speed),
                                        spread: parseFloat(props.spread),
                                        angle: parseFloat(props.angle)
                                    };

                                    if (props.type === 'oscillating') {
                                        emitter.meanX = emitter.x;
                                        emitter.meanY = emitter.y;
                                        emitter.amplitude = parseFloat(props.amplitude);
                                        emitter.frequency = parseFloat(props.frequency);
                                        emitter.theta = parseFloat(props.theta);
                                    } else if (props.type === 'pulse') {
                                        emitter.frequency = parseFloat(props.frequency);
                                        emitter.lastUpdateTime = 0;
                                        emitter.shouldEmit = true;
                                    }

                                    particleSystem.emitters.push(emitter);
                                } else if (section === 'Settings' && line) {
                                    const [key, value] = line.split('=');
                                    if (key === 'GravityEnabled') {
                                        particleSystem.gravityEnabled = value === 'true';
                                    }
                                }
                            }
                        };
                        reader.readAsText(file);
                    }
                };
                input.click();
            });

            clearBtn.addEventListener('click', function() {
                particleSystem.particles = [];
                particleSystem.emitters = [];
                selectedEmitterIndex = -1;
                draggedEmitterIndex = -1;
            });

            function emitParticles(emitter) {
                if (emitter.type === 'pulse' && !emitter.shouldEmit) return;

                // Reduce particle count on mobile for better performance
                const particleCount = isMobile ? 5 : 10;
                for (let i = 0; i < particleCount; i++) {
                    const angleRad = emitter.angle + (Math.random() - 0.5) * emitter.spread;
                    particleSystem.particles.push({
                        x: emitter.x,
                        y: emitter.y,
                        vx: Math.cos(angleRad) * emitter.speed,
                        vy: Math.sin(angleRad) * emitter.speed,
                        radius: 2,
                        color: 'rgba(147, 197, 253, 0.8)',
                        life: 150,
                        mass: 0.5
                    });
                }
            }

            function updateParticleSystem() {
                if (particleSystem.isPaused) return;

                for (let emitter of particleSystem.emitters) {
                    if (emitter.type === 'oscillating') {
                        emitter.theta += emitter.frequency;
                        emitter.x = emitter.meanX + emitter.amplitude * Math.sin(emitter.theta);
                    } else if (emitter.type === 'pulse') {
                        const currentTime = Date.now();
                        if (currentTime - emitter.lastUpdateTime >= 1000 / emitter.frequency) {
                            emitter.shouldEmit = !emitter.shouldEmit;
                            emitter.lastUpdateTime = currentTime;
                        }
                    }
                    emitParticles(emitter);
                }

                for (let i = particleSystem.particles.length - 1; i >= 0; i--) {
                    const p = particleSystem.particles[i];
                    p.x += p.vx;
                    p.y += p.vy;
                    if (particleSystem.gravityEnabled) {
                        p.vy += 0.05 * p.mass;
                    }
                    p.life--;

                    if (p.life <= 0 || p.x < 0 || p.x > particleSystem.width || p.y < 0 || p.y > particleSystem.height) {
                        particleSystem.particles.splice(i, 1);
                    }
                }
            }

            function render() {
                ctx.fillStyle = 'black';
                ctx.fillRect(0, 0, canvas.width, canvas.height);

                for (let i = 0; i < particleSystem.emitters.length; i++) {
                    const emitter = particleSystem.emitters[i];
                    ctx.beginPath();
                    // Larger touch target for mobile
                    const emitterRadius = isMobile ? 15 : 10;
                    ctx.arc(emitter.x, emitter.y, emitterRadius, 0, Math.PI * 2);
                    ctx.fillStyle = i === selectedEmitterIndex ? '#ffff00' : emitter.type === 'basic' ? '#00ff00' : emitter.type === 'oscillating' ? '#00ffff' : '#ff0000';
                    ctx.fill();

                    if (particleSystem.showVelocity) {
                        const angleRad = emitter.angle;
                        ctx.beginPath();
                        ctx.moveTo(emitter.x, emitter.y);
                        ctx.lineTo(emitter.x + Math.cos(angleRad) * 30, emitter.y + Math.sin(angleRad) * 30);
                        ctx.strokeStyle = '#ffffff';
                        ctx.lineWidth = 2;
                        ctx.stroke();
                    }
                }

                for (const p of particleSystem.particles) {
                    ctx.beginPath();
                    ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2);
                    ctx.fillStyle = p.color;
                    ctx.globalAlpha = p.life / 150;
                    ctx.fill();
                }
                ctx.globalAlpha = 1.0;
            }

            function animate() {
                updateParticleSystem();
                render();
                requestAnimationFrame(animate);
            }

            animate();
        });
    </script>
</body>
</html>
