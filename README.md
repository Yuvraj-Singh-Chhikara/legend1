<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Electrostatic Force Simulator</title>
<style>
* { margin: 0; padding: 0; box-sizing: border-box; }
body {
background: #0a0a1a;
color: #e0e0e0;
font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
overflow: hidden;
}
#canvas-container { width: 100vw; height: 100vh; position: relative; }
canvas { display: block; }

#controls {
position: absolute;
top: 20px;
left: 20px;
background: rgba(10, 10, 30, 0.92);
border: 1px solid rgba(100, 140, 255, 0.3);
border-radius: 16px;
padding: 24px;
width: 320px;
backdrop-filter: blur(12px);
box-shadow: 0 8px 32px rgba(0,0,0,0.5), inset 0 1px 0 rgba(255,255,255,0.05);
}
#controls h2 {
font-size: 18px;
margin-bottom: 6px;
color: #7eb8ff;
letter-spacing: 1px;
}
#controls .subtitle {
font-size: 11px;
color: #667;
margin-bottom: 18px;
font-style: italic;
}
.control-group {
margin-bottom: 16px;
}
.control-group label {
display: flex;
justify-content: space-between;
font-size: 13px;
margin-bottom: 6px;
color: #aab;
}
.control-group label span.val {
color: #7eb8ff;
font-weight: 600;
font-family: 'Courier New', monospace;
}
input[type="range"] {
-webkit-appearance: none;
width: 100%;
height: 6px;
border-radius: 3px;
background: linear-gradient(90deg, #1a1a3a, #2a2a5a);
outline: none;
}
input[type="range"]::-webkit-slider-thumb {
-webkit-appearance: none;
width: 18px;
height: 18px;
border-radius: 50%;
background: radial-gradient(circle at 30% 30%, #7eb8ff, #3a6fdf);
cursor: pointer;
box-shadow: 0 0 10px rgba(100,160,255,0.5);
}
input[type="range"].red::-webkit-slider-thumb {
background: radial-gradient(circle at 30% 30%, #ff7e7e, #df3a3a);
box-shadow: 0 0 10px rgba(255,100,100,0.5);
}

#force-display {
background: rgba(20, 20, 50, 0.8);
border: 1px solid rgba(100, 140, 255, 0.2);
border-radius: 10px;
padding: 14px;
margin-top: 12px;
}
#force-display .formula {
font-family: 'Courier New', monospace;
font-size: 12px;
color: #889;
margin-bottom: 8px;
text-align: center;
}
#force-display .force-value {
font-size: 24px;
font-weight: 700;
text-align: center;
font-family: 'Courier New', monospace;
}
.force-attract { color: #ff6b6b; }
.force-repel { color: #6bff6b; }
#force-display .force-type {
text-align: center;
font-size: 12px;
margin-top: 4px;
}

#info-badge {
position: absolute;
bottom: 20px;
left: 50%;
transform: translateX(-50%);
background: rgba(10, 10, 30, 0.85);
border: 1px solid rgba(100, 140, 255, 0.2);
border-radius: 10px;
padding: 10px 20px;
font-size: 12px;
color: #667;
text-align: center;
}
#info-badge span { color: #7eb8ff; }

#legend {
position: absolute;
top: 20px;
right: 20px;
background: rgba(10, 10, 30, 0.92);
border: 1px solid rgba(100, 140, 255, 0.3);
border-radius: 16px;
padding: 20px;
width: 240px;
backdrop-filter: blur(12px);
}
#legend h3 {
font-size: 14px;
color: #7eb8ff;
margin-bottom: 12px;
}
.legend-item {
display: flex;
align-items: center;
gap: 10px;
margin-bottom: 8px;
font-size: 12px;
}
.legend-dot {
width: 12px;
height: 12px;
border-radius: 50%;
flex-shrink: 0;
}
.legend-line {
width: 24px;
height: 3px;
border-radius: 2px;
flex-shrink: 0;
}

.charge-indicator {
display: flex;
align-items: center;
gap: 8px;
padding: 8px 12px;
border-radius: 8px;
margin-bottom: 8px;
font-size: 12px;
font-weight: 600;
}
.charge-positive { background: rgba(50, 120, 255, 0.15); border: 1px solid rgba(50, 120, 255, 0.3); }
.charge-negative { background: rgba(255, 50, 50, 0.15); border: 1px solid rgba(255, 50, 50, 0.3); }
</style>
</head>
<body>

<div id="canvas-container"></div>

<div id="controls">
    <h2>⚡ Electrostatic Force</h2>
    <div class="subtitle">Coulomb's Law: F = k·|q₁·q₂| / r²</div>

    <div class="control-group">
        <label>Charge q₁ <span class="val" id="q1-val">+5 μC</span></label>
        <input type="range" id="q1" min="-10" max="10" step="0.5" value="5" class="">
    </div>

    <div class="control-group">
        <label>Charge q₂ <span class="val" id="q2-val">-3 μC</span></label>
        <input type="range" id="q2" min="-10" max="10" step="0.5" value="-3" class="red">
    </div>

    <div class="control-group">
        <label>Distance (r) <span class="val" id="dist-val">4.0 m</span></label>
        <input type="range" id="distance" min="1" max="10" step="0.1" value="4">
    </div>

    <div id="force-display">
        <div class="formula">F = (8.99×10⁹) × |q₁ × q₂| / r²</div>
        <div class="force-value" id="force-value">3.37 N</div>
        <div class="force-type" id="force-type" style="color:#ff6b6b;">⬡ Attraction</div>
    </div>
</div>

<div id="legend">
    <h3>Visual Legend</h3>
    <div class="legend-item"><div class="legend-dot" style="background:#4488ff;box-shadow:0 0 6px #4488ff"></div> Positive Charge (+)</div>
    <div class="legend-item"><div class="legend-dot" style="background:#ff4444;box-shadow:0 0 6px #ff4444"></div> Negative Charge (−)</div>
    <div class="legend-item"><div class="legend-line" style="background:#00ff88;box-shadow:0 0 4px #00ff88"></div> Force Vector</div>
    <div class="legend-item"><div class="legend-line" style="background:rgba(255,255,100,0.6)"></div> Field Lines</div>
    <div style="height:8px"></div>
    <div class="charge-indicator charge-positive">q₁: <span id="leg-q1">Positive</span></div>
    <div class="charge-indicator charge-negative">q₂: <span id="leg-q2">Negative</span></div>
</div>

<div id="info-badge">
    <span>Scroll</span> to zoom · <span>Click + Drag</span> to rotate · Adjust sliders to see real-time changes
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>
<script>
(function() {
    const K = 8.99e9;
    const SCALE = 1.0;

    // Scene setup
    const container = document.getElementById('canvas-container');
    const scene = new THREE.Scene();
    scene.fog = new THREE.FogExp2(0x0a0a1a, 0.025);

    const camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 200);
    camera.position.set(8, 8, 12);

    const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    renderer.shadowMap.enabled = true;
    renderer.shadowMap.type = THREE.PCFSoftShadowMap;
    renderer.toneMapping = THREE.ACESFilmicToneMapping;
    renderer.toneMappingExposure = 1.2;
    container.appendChild(renderer.domElement);

    const controls = new THREE.OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true;
    controls.dampingFactor = 0.05;
    controls.minDistance = 3;
    controls.maxDistance = 40;

    // Lighting
    const ambientLight = new THREE.AmbientLight(0x334466, 0.4);
    scene.add(ambientLight);

    const mainLight = new THREE.DirectionalLight(0xffffff, 0.8);
    mainLight.position.set(10, 15, 10);
    mainLight.castShadow = true;
    mainLight.shadow.mapSize.set(1024, 1024);
    scene.add(mainLight);

    const pointLight1 = new THREE.PointLight(0x4466ff, 0.6, 30);
    pointLight1.position.set(-5, 5, 5);
    scene.add(pointLight1);

    const pointLight2 = new THREE.PointLight(0xff4444, 0.4, 30);
    pointLight2.position.set(5, 3, -5);
    scene.add(pointLight2);

    // Grid
    const gridHelper = new THREE.GridHelper(30, 30, 0x1a1a3a, 0x111133);
    scene.add(gridHelper);

    // Charge spheres
    const sphereGeo = new THREE.SphereGeometry(0.6, 32, 32);
    const sphereGeoSmall = new THREE.SphereGeometry(0.4, 24, 24);

    const charge1Mat = new THREE.MeshPhysicalMaterial({
        color: 0x4488ff,
        emissive: 0x2244aa,
        emissiveIntensity: 0.5,
        metalness: 0.3,
        roughness: 0.2,
        clearcoat: 1.0,
        clearcoatRoughness: 0.1,
    });

    const charge2Mat = new THREE.MeshPhysicalMaterial({
        color: 0xff4444,
        emissive: 0xaa2222,
        emissiveIntensity: 0.5,
        metalness: 0.3,
        roughness: 0.2,
        clearcoat: 1.0,
        clearcoatRoughness: 0.1,
    });

    const charge1 = new THREE.Mesh(sphereGeo, charge1Mat);
    charge1.castShadow = true;
    charge1.receiveShadow = true;
    scene.add(charge1);

    const charge2 = new THREE.Mesh(sphereGeo, charge2Mat);
    charge2.castShadow = true;
    charge2.receiveShadow = true;
    scene.add(charge2);

    // Glow effect using larger transparent spheres
    const glowGeo1 = new THREE.SphereGeometry(0.9, 32, 32);
    const glowMat1 = new THREE.MeshBasicMaterial({
        color: 0x4488ff,
        transparent: true,
        opacity: 0.12,
    });
    const glow1 = new THREE.Mesh(glowGeo1, glowMat1);
    scene.add(glow1);

    const glowGeo2 = new THREE.SphereGeometry(0.9, 32, 32);
    const glowMat2 = new THREE.MeshBasicMaterial({
        color: 0xff4444,
        transparent: true,
        opacity: 0.12,
    });
    const glow2 = new THREE.Mesh(glowGeo2, glowMat2);
    scene.add(glow2);

    // Point lights on charges
    const c1Light = new THREE.PointLight(0x4488ff, 1.5, 8);
    scene.add(c1Light);

    const c2Light = new THREE.PointLight(0xff4444, 1.5, 8);
    scene.add(c2Light);

    // Force arrows
    const arrowLength = 2;
    const arrowColor1 = new THREE.Color(0x00ff88);
    const arrowColor2 = new THREE.Color(0x00ff88);

    const arrow1Helper = new THREE.ArrowHelper(new THREE.Vector3(1, 0, 0), new THREE.Vector3(), 2, 0x00ff88, 0.3, 0.2);
    scene.add(arrow1Helper);

    const arrow2Helper = new THREE.ArrowHelper(new THREE.Vector3(1, 0, 0), new THREE.Vector3(), 2, 0x00ff88, 0.3, 0.2);
    scene.add(arrow2Helper);

    // Distance line
    const lineMat = new THREE.LineDashedMaterial({
        color: 0xffdd44,
        dashSize: 0.2,
        gapSize: 0.15,
        transparent: true,
        opacity: 0.6,
    });
    const lineGeo = new THREE.BufferGeometry().setFromPoints([new THREE.Vector3(), new THREE.Vector3(1, 0, 0)]);
    const distanceLine = new THREE.Line(lineGeo, lineMat);
    scene.add(distanceLine);

    // Field lines
    let fieldLineGroup = new THREE.Group();
    scene.add(fieldLineGroup);

    // Particles for atmospheric effect
    const particleCount = 500;
    const particleGeo = new THREE.BufferGeometry();
    const positions = new Float32Array(particleCount * 3);
    const colors = new Float32Array(particleCount * 3);
    for (let i = 0; i < particleCount; i++) {
        positions[i * 3] = (Math.random() - 0.5) * 30;
        positions[i * 3 + 1] = Math.random() * 15;
        positions[i * 3 + 2] = (Math.random() - 0.5) * 30;
        colors[i * 3] = 0.3 + Math.random() * 0.3;
        colors[i * 3 + 1] = 0.3 + Math.random() * 0.3;
        colors[i * 3 + 2] = 0.5 + Math.random() * 0.5;
    }
    particleGeo.setAttribute('position', new THREE.BufferAttribute(positions, 3));
    particleGeo.setAttribute('color', new THREE.BufferAttribute(colors, 3));

    const particleMat = new THREE.PointsMaterial({
        size: 0.05,
        vertexColors: true,
        transparent: true,
        opacity: 0.6,
    });
    const particles = new THREE.Points(particleGeo, particleMat);
    scene.add(particles);

    // Charge label sprites
    function createLabel(text) {
        const canvas = document.createElement('canvas');
        canvas.width = 128;
        canvas.height = 64;
        const ctx = canvas.getContext('2d');
        ctx.fillStyle = 'rgba(0,0,0,0)';
        ctx.fillRect(0, 0, 128, 64);
        ctx.font = 'bold 40px Arial';
        ctx.textAlign = 'center';
        ctx.textBaseline = 'middle';
        ctx.fillStyle = '#ffffff';
        ctx.fillText(text, 64, 32);

        const texture = new THREE.CanvasTexture(canvas);
        const spriteMat = new THREE.SpriteMaterial({
            map: texture,
            transparent: true,
        });
        const sprite = new THREE.Sprite(spriteMat);
        sprite.scale.set(1.2, 0.6, 1);
        return sprite;
    }

    const label1 = createLabel('+q₁');
    const label2 = createLabel('-q₂');
    scene.add(label1);
    scene.add(label2);

    // Force text sprite
    const forceSpriteCanvas = document.createElement('canvas');
    forceSpriteCanvas.width = 256;
    forceSpriteCanvas.height = 64;
    const forceTexture = new THREE.CanvasTexture(forceSpriteCanvas);
    const forceSpriteMat = new THREE.SpriteMaterial({ map: forceTexture, transparent: true });
    const forceSprite = new THREE.Sprite(forceSpriteMat);
    forceSprite.scale.set(2, 0.5, 1);
    scene.add(forceSprite);

    function updateForceLabel(force, isAttractive) {
        const fctx = forceSpriteCanvas.getContext('2d');
        fctx.clearRect(0, 0, 256, 64);
        fctx.font = 'bold 28px Courier New';
        fctx.textAlign = 'center';
        fctx.textBaseline = 'middle';
        fctx.fillStyle = isAttractive ? '#ff6b6b' : '#6bff6b';
        fctx.fillText(`F = ${force.toFixed(3)} N`, 128, 32);
        forceTexture.needsUpdate = true;
    }

    function updateLabels(q1Val, q2Val) {
        const ctx1 = label1.material.map.image.getContext('2d');
        ctx1.clearRect(0, 0, 128, 64);
        ctx1.font = 'bold 38px Arial';
        ctx1.textAlign = 'center';
        ctx1.textBaseline = 'middle';
        ctx1.fillStyle = '#ffffff';
        ctx1.fillText(`q₁=${q1Val>0?'+':''}${q1Val}`, 64, 32);
        label1.material.map.needsUpdate = true;

        const ctx2 = label2.material.map.image.getContext('2d');
        ctx2.clearRect(0, 0, 128, 64);
        ctx2.font = 'bold 38px Arial';
        ctx2.textAlign = 'center';
        ctx2.textBaseline = 'middle';
        ctx2.fillStyle = '#ffffff';
        ctx2.fillText(`q₂=${q2Val>0?'+':''}${q2Val}`, 64, 32);
        label2.material.map.needsUpdate = true;
    }

    // Build field lines
    function buildFieldLines(q1Val, q2Val, dist) {
        while (fieldLineGroup.children.length > 0) {
            fieldLineGroup.remove(fieldLineGroup.children[0]);
        }

        const numLines = Math.min(20, Math.floor(Math.abs(q1Val) + Math.abs(q2Val)) * 2 + 4);
        const q1Pos = new THREE.Vector3(-dist / 2, 0, 0);
        const q2Pos = new THREE.Vector3(dist / 2, 0, 0);

        const sameSign = (q1Val > 0 && q2Val > 0) || (q1Val < 0 && q2Val < 0);
        const bothOpposite = !sameSign && q1Val !== 0 && q2Val !== 0;

        if (q1Val === 0 || q2Val === 0) {
            // Field radiating from or to single charge
            const activeQ = q1Val !== 0 ? q1Pos : q2Pos;
            const activeVal = q1Val !== 0 ? q1Val : q2Val;
            const color = activeVal > 0 ? 0x4488ff : 0xff4444;

            for (let i = 0; i < 12; i++) {
                const phi = (i / 12) * Math.PI * 2;
                const points = [];
                const steps = 40;
                for (let s = 0; s < steps; s++) {
                    const r = 0.7 + (s / steps) * 5;
                    points.push(new THREE.Vector3(
                        activeQ.x + r * Math.cos(phi) * 0.3,
                        r * Math.sin(phi),
                        r * Math.cos(phi) * 0.3
                    ));
                }
                const lineGeo = new THREE.BufferGeometry().setFromPoints(points);
                const lineMat = new THREE.LineBasicMaterial({
                    color: color,
                    transparent: true,
                    opacity: 0.25,
                });
                const line = new THREE.Line(lineGeo, lineMat);
                fieldLineGroup.add(line);
            }
            return;
        }

        if (bothOpposite) {
            // Attraction: field lines from positive to negative
            for (let i = 0; i < numLines; i++) {
                const phi = (i / numLines) * Math.PI * 2;
                const cosPhi = Math.cos(phi);
                const sinPhi = Math.sin(phi);
                const points = [];
                const steps = 60;

                for (let s = 0; s <= steps; s++) {
                    const t = s / steps;
                    // Curved path from q1 to q2
                    const baseX = q1Pos.x + (q2Pos.x - q1Pos.x) * t;
                    const maxDeviation = dist * 0.4 * Math.sin(t * Math.PI);
                    const x = baseX;
                    const y = maxDeviation * sinPhi;
                    const z = maxDeviation * cosPhi;
                    points.push(new THREE.Vector3(x, y, z));
                }

                const lineGeo = new THREE.BufferGeometry().setFromPoints(points);
                const lineMat = new THREE.LineBasicMaterial({
                    color: 0xffdd66,
                    transparent: true,
                    opacity: 0.35,
                });
                const line = new THREE.Line(lineGeo, lineMat);
                fieldLineGroup.add(line);
            }
        } else {
            // Repulsion: field lines radiating outward
            const fromQ1 = q1Val > 0;
            const color1 = fromQ1 ? 0x4488ff : 0xff4444;

            for (let i = 0; i < numLines; i++) {
                const phi = (i / numLines) * Math.PI * 2;
                const sinPhi = Math.sin(phi);
                const cosPhi = Math.cos(phi);

                // From q1
                const pts1 = [];
                for (let s = 0; s < 40; s++) {
                    const t = s / 40;
                    const r = 0.7 + t * 4;
                    const x = q1Pos.x + r * (fromQ1 ? 1 : -1) * 0.5;
                    const y = r * sinPhi * 0.6;
                    const z = r * cosPhi * 0.6;
                    pts1.push(new THREE.Vector3(x, y, z));
                }
                const g1 = new THREE.BufferGeometry().setFromPoints(pts1);
                fieldLineGroup.add(new THREE.Line(g1, new THREE.LineBasicMaterial({ color: color1, transparent: true, opacity: 0.25 })));

                // From q2
                const pts2 = [];
                for (let s = 0; s < 40; s++) {
                    const t = s / 40;
                    const r = 0.7 + t * 4;
                    const x = q2Pos.x + r * (fromQ1 ? -1 : 1) * 0.5;
                    const y = r * sinPhi * 0.6;
                    const z = r * cosPhi * 0.6;
                    pts2.push(new THREE.Vector3(x, y, z));
                }
                const g2 = new THREE.BufferGeometry().setFromPoints(pts2);
                fieldLineGroup.add(new THREE.Line(g2, new THREE.LineBasicMaterial({ color: color1, transparent: true, opacity: 0.25 })));
            }
        }
    }

    // Update function
    let targetQ1 = 5, targetQ2 = -3, targetDist = 4;
    let currentDist = 4;

    function updateVisualization() {
        const q1Val = parseFloat(document.getElementById('q1').value);
        const q2Val = parseFloat(document.getElementById('q2').value);
        const dist = parseFloat(document.getElementById('distance').value);

        targetQ1 = q1Val;
        targetQ2 = q2Val;
        targetDist = dist;

        // Update UI labels
        document.getElementById('q1-val').textContent = `${q1Val > 0 ? '+' : ''}${q1Val} μC`;
        document.getElementById('q2-val').textContent = `${q2Val > 0 ? '+' : ''}${q2Val} μC`;
        document.getElementById('dist-val').textContent = `${dist.toFixed(1)} m`;

        // Calculate force
        const force = (K * Math.abs(q1Val * 1e-6) * Math.abs(q2Val * 1e-6)) / (dist * dist);
        const isAttractive = (q1Val > 0 && q2Val < 0) || (q1Val < 0 && q2Val > 0);
        const forceTypeText = isAttractive ? '⬡ Attraction' : (force > 0 ? '⬡ Repulsion' : 'No Force');

        const forceEl = document.getElementById('force-value');
        forceEl.textContent = `${force.toExponential(3)} N`;
        forceEl.className = 'force-value ' + (isAttractive ? 'force-attract' : 'force-repel') + (force === 0 ? '' : '');
        if (force === 0) forceEl.style.color = '#888';
        else forceEl.style.color = '';

        document.getElementById('force-type').textContent = forceTypeText;
        document.getElementById('force-type').style.color = isAttractive ? '#ff6b6b' : (force > 0 ? '#6bff6b' : '#888');

        // Update legend
        document.getElementById('leg-q1').textContent = q1Val > 0 ? 'Positive' : q1Val < 0 ? 'Negative' : 'Neutral';
        document.getElementById('leg-q2').textContent = q2Val > 0 ? 'Positive' : q2Val < 0 ? 'Negative' : 'Neutral';
        document.querySelector('.charge-indicator.charge-positive').className =
            'charge-indicator ' + (q1Val >= 0 ? 'charge-positive' : 'charge-negative');
        document.querySelector('.charge-indicator.charge-positive').innerHTML =
            `q₁: <span>${q1Val > 0 ? 'Positive' : q1Val < 0 ? 'Negative' : 'Neutral'}</span>`;
        document.querySelector('.charge-indicator.charge-negative').className =
            'charge-indicator ' + (q2Val >= 0 ? 'charge-positive' : 'charge-negative');
        document.querySelector('.charge-indicator.charge-negative').innerHTML =
            `q₂: <span>${q2Val > 0 ? 'Positive' : q2Val < 0 ? 'Negative' : 'Neutral'}</span>`;

        // Update charge materials
        if (q1Val >= 0) {
            charge1Mat.color.set(0x4488ff);
            charge1Mat.emissive.set(0x2244aa);
            glowMat1.color.set(0x4488ff);
            c1Light.color.set(0x4488ff);
        } else {
            charge1Mat.color.set(0xff4444);
            charge1Mat.emissive.set(0xaa2222);
            glowMat1.color.set(0xff4444);
            c1Light.color.set(0xff4444);
        }

        if (q2Val >= 0) {
            charge2Mat.color.set(0x4488ff);
            charge2Mat.emissive.set(0x2244aa);
            glowMat2.color.set(0x4488ff);
            c2Light.color.set(0x4488ff);
        } else {
            charge2Mat.color.set(0xff4444);
            charge2Mat.emissive.set(0xaa2222);
            glowMat2.color.set(0xff4444);
            c2Light.color.set(0xff4444);
        }

        // Sphere size proportional to absolute charge
        const s1 = Math.max(0.35, Math.abs(q1Val) * 0.08 + 0.3);
        const s2 = Math.max(0.35, Math.abs(q2Val) * 0.08 + 0.3);
        charge1.scale.set(s1, s1, s1);
        charge2.scale.set(s2, s2, s2);

        const gs1 = s1 * 1.5;
        const gs2 = s2 * 1.5;
        glow1.scale.set(gs1, gs1, gs1);
        glow2.scale.set(gs2, gs2, gs2);

        glowMat1.opacity = Math.min(0.18, Math.abs(q1Val) * 0.015);
        glowMat2.opacity = Math.min(0.18, Math.abs(q2Val) * 0.015);
        c1Light.intensity = Math.abs(q1Val) * 0.25;
        c2Light.intensity = Math.abs(q2Val) * 0.25;

        // Update labels
        updateLabels(q1Val, q2Val);

        // Update force label sprite
        updateForceLabel(force, isAttractive);
        const midX = 0;
        forceSprite.position.set(midX, Math.max(s1, s2) + 1.5, 0);
    }

    // Animation loop
    let time = 0;

    function animate() {
        requestAnimationFrame(animate);
        time += 0.016;

        // Smooth distance transition
        currentDist += (targetDist - currentDist) * 0.08;
        dist = currentDist;

        const q1Val = targetQ1;
        const q2Val = targetQ2;

        // Position charges
        const halfDist = dist / 2;
        charge1.position.set(-halfDist, 0, 0);
        charge2.position.set(halfDist, 0, 0);
        glow1.position.copy(charge1.position);
        glow2.position.copy(charge2.position);
        c1Light.position.copy(charge1.position);
        c2Light.position.copy(charge2.position);
        c1Light.position.y += 0.5;
        c2Light.position.y += 0.5;

        label1.position.set(-halfDist, 1.5, 0);
        label2.position.set(halfDist, 1.5, 0);

        // Force calculation
        const force = (K * Math.abs(q1Val * 1e-6) * Math.abs(q2Val * 1e-6)) / (dist * dist);
        const isAttractive = (q1Val > 0 && q2Val < 0) || (q1Val < 0 && q2Val > 0);
        const isRepulsive = (q1Val > 0 && q2Val > 0) || (q1Val < 0 && q2Val < 0);
        const noForce = q1Val === 0 || q2Val === 0;

        // Arrow magnitude (scaled for visibility)
        const arrowMag = Math.min(4, Math.log10(force + 1) * 1.2 + 0.3);

        if (noForce || force === 0) {
            arrow1Helper.visible = false;
            arrow2Helper.visible = false;
        } else {
            arrow1Helper.visible = true;
            arrow2Helper.visible = true;

            if (isAttractive) {
                // Arrows point toward each other
                arrow1Helper.setDirection(new THREE.Vector3(1, 0, 0));
                arrow1Helper.position.copy(charge1.position);
                arrow1Helper.position.y += 0.3;
                arrow1Helper.setLength(arrowMag, 0.3, 0.15);
                arrow1Helper.setColor(0x00ff88);

                arrow2Helper.setDirection(new THREE.Vector3(-1, 0, 0));
                arrow2Helper.position.copy(charge2.position);
                arrow2Helper.position.y += 0.3;
                arrow2Helper.setLength(arrowMag, 0.3, 0.15);
                arrow2Helper.setColor(0x00ff88);
            } else {
                // Arrows point away from each other
                arrow1Helper.setDirection(new THREE.Vector3(-1, 0, 0));
                arrow1Helper.position.copy(charge1.position);
                arrow1Helper.position.y += 0.3;
                arrow1Helper.setLength(arrowMag, 0.3, 0.15);
                arrow1Helper.setColor(0xff6644);

                arrow2Helper.setDirection(new THREE.Vector3(1, 0, 0));
                arrow2Helper.position.copy(charge2.position);
                arrow2Helper.position.y += 0.3;
                arrow2Helper.setLength(arrowMag, 0.3, 0.15);
                arrow2Helper.setColor(0xff6644);
            }
        }

        // Distance line
        const linePoints = [charge1.position.clone(), charge2.position.clone()];
        distanceLine.geometry.setFromPoints(linePoints);
        distanceLine.computeLineDistances();

        // Pulse glow
        const pulse1 = 1 + 0.05 * Math.sin(time * 3) * Math.abs(q1Val);
        const pulse2 = 1 + 0.05 * Math.sin(time * 3 + 1) * Math.abs(q2Val);
        glow1.scale.set(
            charge1.scale.x * 1.5 * pulse1,
            charge1.scale.y * 1.5 * pulse1,
            charge1.scale.z * 1.5 * pulse1
        );
        glow2.scale.set(
            charge2.scale.x * 1.5 * pulse2,
            charge2.scale.y * 1.5 * pulse2,
            charge2.scale.z * 1.5 * pulse2
        );

        // Rotate particles slowly
        particles.rotation.y = time * 0.02;

        // Charge sphere rotation
        charge1.rotation.y = time * 0.5;
        charge2.rotation.y = -time * 0.5;

        controls.update();
        renderer.render(scene, camera);
    }

    // Event listeners
    document.getElementById('q1').addEventListener('input', () => {
        updateVisualization();
        buildFieldLines(targetQ1, targetQ2, targetDist);
    });
    document.getElementById('q2').addEventListener('input', () => {
        updateVisualization();
        buildFieldLines(targetQ1, targetQ2, targetDist);
    });
    document.getElementById('distance').addEventListener('input', () => {
        updateVisualization();
        buildFieldLines(targetQ1, targetQ2, targetDist);
    });

    window.addEventListener('resize', () => {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
    });

    // Initial setup
    updateVisualization();
    buildFieldLines(targetQ1, targetQ2, targetDist);
    animate();
})();
</script>
</body>
</html>
