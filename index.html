<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Pixel Drift Emulation</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- React and ReactDOM -->
    <script src="https://unpkg.com/react@18/umd/react.development.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js" crossorigin></script>
    <!-- Babel to transpile JSX -->
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <style>
        body, html, #root {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            background-color: #000000;
            font-family: 'Courier New', Courier, monospace;
        }
        canvas {
            display: block;
        }
        /* Custom Checkbox Style for pure B&W */
        input[type="checkbox"] {
            appearance: none;
            background-color: black;
            margin: 0;
            font: inherit;
            color: white;
            width: 1em;
            height: 1em;
            border: 1px solid white;
            display: grid;
            place-content: center;
            cursor: pointer;
        }
        input[type="checkbox"]::before {
            content: "";
            width: 0.6em;
            height: 0.6em;
            transform: scale(0);
            transition: 120ms transform ease-in-out;
            box-shadow: inset 1em 1em white;
            background-color: white;
        }
        input[type="checkbox"]:checked::before {
            transform: scale(1);
        }
        
        /* Custom Scrollbar */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #000; 
            border-left: 1px solid #333;
        }
        ::-webkit-scrollbar-thumb {
            background: #fff; 
        }
    </style>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">
        const { useRef, useEffect, useCallback, useState } = React;

        // --- CONFIGURATION ---
        const CONFIG = {
            INTERNAL_RES_X: 2048,   // Higher resolution for finer threads
            INTERNAL_RES_Y: 2048,
            MIN_ZONES: 5,           // Minimum number of Mondrian sections
            MAX_ZONES: 11,          // Maximum number of Mondrian sections
            RESET_INTERVAL: 8000,   // Interval to regenerate layout
        };

        const EFFECTS_MAP = {
            0: "Rain",
            1: "Rise",
            2: "Wind",
            3: "Turbulence",
            4: "Implode",
            5: "Explode",
            6: "Static",
            7: "Wave",
            // New Effects
            8: "Swirl",
            9: "Vortex",
            10: "Laser",
            11: "Zipper",
            12: "Shear",
            13: "Grid",
            14: "Cross",
            15: "Bounce",
            16: "Shiver",
            17: "Glitch V",
            18: "Glitch H",
            19: "Warp",
            20: "Bulge",
            21: "Pinch",
            22: "Ripple",
            23: "Twist",
            24: "Scan",
            25: "Slide",
            26: "Drip",
            27: "Shred"
        };

        // --- GLSL SHADERS ---

        const vertexShaderSource = `
        #version 300 es 
        in vec2 a_position;
        void main() {
            gl_Position = vec4(a_position, 0.0, 1.0); 
        }
        `;

        /**
         * Zone-based Displacement Shader with Mondrian Logic.
         */
        const feedbackShaderSource = `
        #version 300 es 
        precision highp float;
        precision highp int;

        uniform vec2 iResolution;
        uniform float iTime;
        uniform sampler2D u_prevFrame;
        
        // Zone Arrays - Increased size for Mondrian grid
        uniform vec4 u_rects[32];       // (x, y, width, height) normalized 0.0-1.0
        uniform int u_effects[32];      // Effect ID
        uniform float u_speeds[32];     // Effect speed/intensity mod
        uniform float u_seeds[32];      // Random seed per zone
        uniform int u_rotations[32];    // Rotation (0,1,2,3) for 90deg increments

        uniform float u_feedbackDecay;
        uniform float u_pixelSorting;

        out vec4 fragColorOut;

        // --- UTILS ---
        float random(vec2 st) { 
            return fract(sin(dot(st.xy, vec2(12.9898,78.233))) * 43758.5453123); 
        }

        float noise(vec2 st) {
            vec2 i = floor(st); vec2 f = fract(st); vec2 u = f*f*(3.0-2.0*f);
            float a = random(i); float b = random(i + vec2(1.0, 0.0));
            float c = random(i + vec2(0.0, 1.0)); float d = random(i + vec2(1.0, 1.0));
            return mix(a, b, u.x) + (c - a)* u.y * (1.0 - u.x) + (d - b) * u.x * u.y;
        }

        bool insideBox(vec2 uv, vec4 rect) {
            return uv.x >= rect.x && uv.x < rect.x + rect.z &&
                   uv.y >= rect.y && uv.y < rect.y + rect.w;
        }

        // Rotate vector by 90 degree increments
        vec2 rotateVector(vec2 v, int r) {
            if (r == 1) return vec2(-v.y, v.x); // 90 deg
            if (r == 2) return vec2(-v.x, -v.y); // 180 deg
            if (r == 3) return vec2(v.y, -v.x); // 270 deg
            return v;
        }

        void main() {
            vec2 uv = gl_FragCoord.xy / iResolution.xy;
            vec2 texel = 1.0 / iResolution.xy;
            
            vec2 totalOffset = vec2(0.0);
            bool inAnyZone = false;
            float borderDist = 1.0;
            
            // This will store the UV coords used for calculations (standard 1x1)
            vec2 calcUV = uv; 

            // Iterate over all zones to find which one we are in
            // Mondrian layout is non-overlapping, but we use accumulation loop for simplicity
            for(int i = 0; i < 32; i++) {
                vec4 rect = u_rects[i];
                
                // Break if we hit an empty rect (w/h == 0)
                if (rect.z <= 0.0001) continue;

                // Calculate distance to edges for border effect (using smooth UV)
                // Distance to closest edge of this rect
                float d = min(min(abs(uv.x - rect.x), abs(uv.x - (rect.x + rect.z))),
                              min(abs(uv.y - rect.y), abs(uv.y - (rect.y + rect.w))));
                
                if (insideBox(uv, rect)) {
                    inAnyZone = true;
                    borderDist = min(borderDist, d);

                    int effect = u_effects[i];
                    float speed = u_speeds[i];
                    int rotation = u_rotations[i];
                    
                    // Zone-local coordinates
                    vec2 center = rect.xy + rect.zw * 0.5;
                    vec2 toCenter = center - calcUV;
                    float dist = length(toCenter);
                    
                    vec2 offset = vec2(0.0);

                    // --- EFFECT LOGIC (uses calcUV) ---
                    if (effect == 0) offset.y = -2.0 * texel.y * speed; // Rain
                    else if (effect == 1) offset.y = 2.0 * texel.y * speed; // Rise
                    else if (effect == 2) offset.x = 2.0 * texel.x * speed; // Wind
                    else if (effect == 3) { // Turbulence
                        vec2 nCoord = calcUV * 4.0 + iTime * 0.02; 
                        float ang = noise(nCoord) * 6.28 * 2.0;
                        offset = vec2(cos(ang), sin(ang)) * texel * 1.5 * speed;
                    }
                    else if (effect == 4) offset = normalize(toCenter) * texel * speed * 1.5; // Implode
                    else if (effect == 5) offset = -normalize(toCenter) * texel * speed * 1.5; // Explode
                    else if (effect == 6) { // Static
                        if (mod(iTime * 20.0, 10.0) < 2.0) offset.x = (random(calcUV + iTime) - 0.5) * 20.0 * texel.x;
                    }
                    else if (effect == 7) offset.y = sin(calcUV.x * 12.0 + iTime * 1.0) * 4.0 * texel.y * speed; // Wave
                    else if (effect == 8) offset = vec2(-toCenter.y, toCenter.x) * speed * texel.x * 2.0; // Swirl
                    else if (effect == 9) offset = (vec2(-toCenter.y, toCenter.x) + normalize(toCenter)*0.5) * speed * texel.x * 2.0; // Vortex
                    else if (effect == 10) { // Laser
                        float beam = step(0.95, fract(calcUV.y * 10.0 + iTime * 0.5));
                        offset.x = beam * 10.0 * texel.x * speed;
                    }
                    else if (effect == 11) { // Zipper
                        // calcUV is 0-1. gl_FragCoord is pixel coords. 
                        // Convert calcUV back to approx fragY for the check
                        float fragY = calcUV.y * iResolution.y;
                        float dir = (mod(fragY, 4.0) < 2.0) ? 1.0 : -1.0;
                        offset.x = dir * 2.0 * texel.x * speed;
                    }
                    else if (effect == 12) offset.y = (calcUV.x - center.x) * speed * texel.y * 4.0; // Shear
                    else if (effect == 13) { // Grid
                        vec2 grid = step(0.9, fract(calcUV * 20.0));
                        offset = vec2(grid.y, grid.x) * speed * texel * 2.0;
                    }
                    else if (effect == 14) offset = vec2(sin(calcUV.y*50.0), cos(calcUV.x*50.0)) * speed * texel; // Cross
                    else if (effect == 15) offset.y = sin(iTime * 5.0) * speed * texel.y * 4.0; // Bounce
                    else if (effect == 16) offset = (vec2(random(calcUV+iTime), random(calcUV-iTime)) - 0.5) * speed * texel * 4.0; // Shiver
                    else if (effect == 17) { // Glitch V
                        if(random(vec2(floor(calcUV.x*10.0), floor(iTime*5.0))) > 0.8) offset.y = (random(calcUV) - 0.5) * speed * texel.y * 20.0;
                    }
                    else if (effect == 18) { // Glitch H
                        if(random(vec2(floor(calcUV.y*10.0), floor(iTime*5.0))) > 0.8) offset.x = (random(calcUV) - 0.5) * speed * texel.x * 20.0;
                    }
                    else if (effect == 19) offset = vec2(sin(calcUV.y * 20.0 + iTime), cos(calcUV.x * 20.0 + iTime)) * speed * texel * 2.0; // Warp
                    else if (effect == 20) offset = normalize(toCenter) * smoothstep(0.5, 0.0, dist) * speed * texel * 4.0; // Bulge
                    else if (effect == 21) offset = -normalize(toCenter) * smoothstep(0.5, 0.0, dist) * speed * texel * 4.0; // Pinch
                    else if (effect == 22) offset = normalize(toCenter) * sin(dist * 50.0 - iTime * 5.0) * speed * texel * 2.0; // Ripple
                    else if (effect == 23) { // Twist
                        float ang = dist * 10.0;
                        float s = sin(ang); float c = cos(ang);
                        vec2 rot = vec2(toCenter.x*c - toCenter.y*s, toCenter.x*s + toCenter.y*c);
                        offset = (rot - toCenter) * speed * 0.05 * texel;
                    }
                    else if (effect == 24) { // Scan
                        float bar = step(0.9, 1.0 - abs(fract(calcUV.y + iTime * 0.2) - 0.5) * 2.0);
                        offset.x = bar * speed * texel.x * 5.0;
                    }
                    else if (effect == 25) { // Slide
                        float row = floor(calcUV.y * 20.0);
                        float slide = step(0.5, random(vec2(row, 0.0))) * 2.0 - 1.0;
                        offset.x = slide * speed * texel.x;
                    }
                    else if (effect == 26) { // Drip
                        float viscous = noise(vec2(calcUV.x * 20.0, 0.0));
                        offset.y = -(viscous + 0.5) * speed * texel.y * 2.0;
                    }
                    else if (effect == 27) { // Shred
                        offset.x = (random(vec2(0.0, calcUV.y)) - 0.5) * speed * texel.x * 10.0;
                    }
                    
                    // Apply 90-degree rotation to the effect vector
                    offset = rotateVector(offset, rotation);

                    totalOffset += offset;
                }
            }

            // 1. Apply Displacement
            // Use calcUV (pixelated) as the base for sampling to ensure blocky look
            vec2 sampleUV = calcUV - totalOffset;

            // 2. Pixel Sorting
            if (u_pixelSorting > 0.5 && random(calcUV * iTime) > 0.995) {
                sampleUV.x += (random(calcUV) - 0.5) * 0.05;
            }

            // 3. Sample Frame
            vec4 color = texture(u_prevFrame, sampleUV);

            // 4. Feedback Decay
            color.rgb *= u_feedbackDecay;
            
            // 5. Borders (Mondrian Lines)
            // If near edge of a zone, draw black. Using borderDist (high res) for clean lines
            if (borderDist < 0.002) {
                color.rgb = vec3(0.0);
            }

            // 6. Injection / Seeding
            // Use calcUV for injection to align with pixels? Or high res?
            // High res injection looks better floating on top, but blocky fits theme.
            // Let's use high res for "gl_FragCoord" based randomness to keep it lively.
            float injectThreshold = 0.997; 
            if (random(gl_FragCoord.xy + iTime * 1.2) > injectThreshold) {
                float r = random(uv + iTime);
                float val = r > 0.4 ? 1.0 : 0.0;
                color.rgb = mix(color.rgb, vec3(val), 0.5);
                color.a = 1.0;
            }

            fragColorOut = color;
        }
        `;
        
        /**
         * Post-processing: Sharpening, Thresholding for 1-bit BW.
         */
        const drawScreenShaderSource = `
        #version 300 es
        precision highp float;
        uniform vec2 iResolution;
        uniform sampler2D u_frame;
        uniform float u_sharpenEnabled;

        out vec4 fragColorOut;

        void main() {
            vec2 uv = gl_FragCoord.xy / iResolution.xy;
            vec4 source = texture(u_frame, uv);
            
            // 1. Sharpening
            if (u_sharpenEnabled > 0.5) {
                vec2 step = 1.0 / iResolution;
                vec4 sum = texture(u_frame, uv + vec2(-step.x, 0.0)) * -1.0 +
                           texture(u_frame, uv + vec2(0.0, -step.y)) * -1.0 +
                           texture(u_frame, uv + vec2(step.x, 0.0)) * -1.0 +
                           texture(u_frame, uv + vec2(0.0, step.y)) * -1.0 +
                           source * 5.0;
                source = sum;
            }

            // 2. Convert to Grayscale
            float lum = dot(source.rgb, vec3(0.299, 0.587, 0.114));

            // 3. Threshold
            float lumaThreshold = 0.1;
            float binary = step(lumaThreshold, lum);
            
            fragColorOut = vec4(vec3(binary), 1.0);
        }
        `;

        // --- WEBGL HELPERS ---
        const createShader = (gl, type, source) => {
            const shader = gl.createShader(type);
            gl.shaderSource(shader, source.trim());
            gl.compileShader(shader);
            if (gl.getShaderParameter(shader, gl.COMPILE_STATUS)) return shader;
            console.error("Shader error:", gl.getShaderInfoLog(shader));
            return null;
        };

        const createProgram = (gl, vs, fs) => {
            if (!vs || !fs) return null; // Add check to prevent attaching null shaders
            const p = gl.createProgram();
            gl.attachShader(p, vs);
            gl.attachShader(p, fs);
            gl.linkProgram(p);
            if (gl.getProgramParameter(p, gl.LINK_STATUS)) return p;
            console.error("Program error:", gl.getProgramInfoLog(p));
            return null;
        };

        const createFBO = (gl, w, h) => {
            const fbo = gl.createFramebuffer();
            gl.bindFramebuffer(gl.FRAMEBUFFER, fbo);
            const tex = gl.createTexture();
            gl.bindTexture(gl.TEXTURE_2D, tex);
            gl.texImage2D(gl.TEXTURE_2D, 0, gl.RGBA, w, h, 0, gl.RGBA, gl.UNSIGNED_BYTE, null);
            gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR);
            gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MAG_FILTER, gl.LINEAR);
            gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.REPEAT);
            gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.REPEAT);
            gl.framebufferTexture2D(gl.FRAMEBUFFER, gl.COLOR_ATTACHMENT0, gl.TEXTURE_2D, tex, 0);
            return { fbo, texture: tex };
        };

        // --- MONDRIAN LAYOUT GENERATOR ---
        
        const generateMondrianLayout = (targetCount) => {
            let rects = [{x:0, y:0, w:1, h:1}];
            
            while (rects.length < targetCount) {
                // Find the largest rectangle to split
                let largestIdx = 0;
                let maxArea = 0;
                
                for(let i=0; i<rects.length; i++) {
                    const area = rects[i].w * rects[i].h;
                    if (area > maxArea) {
                        maxArea = area;
                        largestIdx = i;
                    }
                }
                
                const r = rects[largestIdx];
                
                // Stop if rects are getting too small
                if (maxArea < 0.02) break; 

                rects.splice(largestIdx, 1);
                
                // Decide split direction (prefer splitting long edge)
                const splitVert = r.w > r.h ? true : (r.h > r.w ? false : Math.random() > 0.5);
                const split = 0.3 + Math.random() * 0.4; // 30% to 70% split
                
                if (splitVert) {
                    const w1 = r.w * split;
                    const w2 = r.w - w1;
                    rects.push({x: r.x, y: r.y, w: w1, h: r.h});
                    rects.push({x: r.x + w1, y: r.y, w: w2, h: r.h});
                } else {
                    const h1 = r.h * split;
                    const h2 = r.h - h1;
                    rects.push({x: r.x, y: r.y, w: r.w, h: h1});
                    rects.push({x: r.x, y: r.y + h1, w: r.w, h: h2});
                }
            }
            
            // Pad array if needed (though we won't render zero-sized rects)
            while(rects.length < 32) {
                rects.push({x:0, y:0, w:0, h:0});
            }
            
            return rects.slice(0, 32);
        };

        const generateEffects = (allowedIds, count) => {
            const effects = [];
            const seeds = [];
            const speeds = [];
            const rotations = [];
            
            // Create a pool from allowed IDs
            let pool = allowedIds && allowedIds.length > 0 ? [...allowedIds] : [3];
            
            // Shuffle to ensure we pick unique effects for the active zones if possible
            for (let i = pool.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [pool[i], pool[j]] = [pool[j], pool[i]];
            }

            for(let i=0; i<32; i++) {
                let effect;
                if (i < count) {
                    // Pick unique effect from pool, or wrap if pool is smaller than count
                    effect = pool[i % pool.length];
                } else {
                    effect = 0;
                }
                effects.push(effect);
                seeds.push(Math.random());
                speeds.push(0.5 + Math.random() * 0.5);
                rotations.push(Math.floor(Math.random() * 4)); // 0, 1, 2, 3
            }
            return { effects, seeds, speeds, rotations };
        };


        // --- REACT COMPONENTS ---

        const ShaderCanvas = () => {
            const canvasRef = useRef(null);
            const glRef = useRef(null);
            const reqIdRef = useRef(null);
            
            const [config, setConfig] = useState({
                feedbackDecay: 0.99, 
                sharpen: false,
                pixelSorting: false
            });
            
            const [allowedEffects, setAllowedEffects] = useState(new Set(Object.keys(EFFECTS_MAP).map(Number)));
            const allowedEffectsRef = useRef(allowedEffects);
            const configRef = useRef(config);
            const forceResetRef = useRef(false);

            useEffect(() => { configRef.current = config; }, [config]);
            useEffect(() => { allowedEffectsRef.current = allowedEffects; }, [allowedEffects]);

            // Simulation State
            const simState = useRef({
                rects: [],
                effects: [],
                seeds: [],
                speeds: [],
                rotations: [],
                lastReset: 0
            });

            useEffect(() => {
                const canvas = canvasRef.current;
                const gl = canvas.getContext('webgl2', { preserveDrawingBuffer: true });
                if (!gl) return;
                glRef.current = gl;

                const resize = () => {
                    canvas.width = window.innerWidth; 
                    canvas.height = window.innerHeight;
                    gl.viewport(0, 0, window.innerWidth, window.innerHeight);
                };
                window.addEventListener('resize', resize);
                resize();

                const vs = createShader(gl, gl.VERTEX_SHADER, vertexShaderSource);
                const fsFeedback = createShader(gl, gl.FRAGMENT_SHADER, feedbackShaderSource);
                const fsScreen = createShader(gl, gl.FRAGMENT_SHADER, drawScreenShaderSource);

                if (!vs || !fsFeedback || !fsScreen) {
                    console.error("Critical shader initialization failure.");
                    return;
                }
                
                const progFeedback = createProgram(gl, vs, fsFeedback);
                const progScreen = createProgram(gl, vs, fsScreen);

                if (!progFeedback || !progScreen) return;

                let fbo1 = createFBO(gl, CONFIG.INTERNAL_RES_X, CONFIG.INTERNAL_RES_Y);
                let fbo2 = createFBO(gl, CONFIG.INTERNAL_RES_X, CONFIG.INTERNAL_RES_Y);

                const buf = gl.createBuffer();
                gl.bindBuffer(gl.ARRAY_BUFFER, buf);
                gl.bufferData(gl.ARRAY_BUFFER, new Float32Array([-1,-1, 1,-1, -1,1, -1,1, 1,-1, 1,1]), gl.STATIC_DRAW);

                const locs = {
                    fb: {
                        iRes: gl.getUniformLocation(progFeedback, "iResolution"),
                        iTime: gl.getUniformLocation(progFeedback, "iTime"),
                        uPrev: gl.getUniformLocation(progFeedback, "u_prevFrame"),
                        uDecay: gl.getUniformLocation(progFeedback, "u_feedbackDecay"),
                        uSort: gl.getUniformLocation(progFeedback, "u_pixelSorting"),
                        uRects: gl.getUniformLocation(progFeedback, "u_rects"),
                        uEffects: gl.getUniformLocation(progFeedback, "u_effects"),
                        uSpeeds: gl.getUniformLocation(progFeedback, "u_speeds"),
                        uSeeds: gl.getUniformLocation(progFeedback, "u_seeds"),
                        uRotations: gl.getUniformLocation(progFeedback, "u_rotations"),
                        aPos: gl.getAttribLocation(progFeedback, "a_position"),
                    },
                    sc: {
                        iRes: gl.getUniformLocation(progScreen, "iResolution"),
                        uFrame: gl.getUniformLocation(progScreen, "u_frame"),
                        uSharp: gl.getUniformLocation(progScreen, "u_sharpenEnabled"),
                        aPos: gl.getAttribLocation(progScreen, "a_position"),
                    }
                };

                const resetZones = () => {
                    // Generate Mondrian Grid with random count between MIN and MAX
                    const count = Math.floor(Math.random() * (CONFIG.MAX_ZONES - CONFIG.MIN_ZONES + 1)) + CONFIG.MIN_ZONES;
                    const rects = generateMondrianLayout(count);
                    simState.current.rects = rects;
                    
                    const allowedArray = Array.from(allowedEffectsRef.current);
                    // Pass 'count' to ensure we assign unique effects to the active zones
                    const { effects, seeds, speeds, rotations } = generateEffects(allowedArray, count);
                    
                    simState.current.effects = effects;
                    simState.current.seeds = seeds;
                    simState.current.speeds = speeds;
                    simState.current.rotations = rotations;
                    simState.current.lastReset = performance.now();
                };
                resetZones();

                // Seed
                const initData = new Uint8Array(CONFIG.INTERNAL_RES_X * CONFIG.INTERNAL_RES_Y * 4);
                for(let i=0; i<initData.length; i+=4) {
                    const val = Math.random() > 0.99 ? 255 : 0;
                    initData[i] = val;
                    initData[i+1] = val;
                    initData[i+2] = val;
                    initData[i+3] = 255;
                }
                gl.bindTexture(gl.TEXTURE_2D, fbo1.texture);
                gl.texImage2D(gl.TEXTURE_2D, 0, gl.RGBA, CONFIG.INTERNAL_RES_X, CONFIG.INTERNAL_RES_Y, 0, gl.RGBA, gl.UNSIGNED_BYTE, initData);

                const render = (time) => {
                    if ((time - simState.current.lastReset > CONFIG.RESET_INTERVAL) || forceResetRef.current) {
                        resetZones();
                        forceResetRef.current = false;
                    }

                    // Prepare Uniform Data
                    // We need flat arrays for all 32 potential zones
                    const rectData = new Float32Array(32 * 4);
                    for(let i=0; i<32; i++) {
                        const r = simState.current.rects[i];
                        if(r) {
                            rectData[i*4 + 0] = r.x;
                            rectData[i*4 + 1] = r.y;
                            rectData[i*4 + 2] = r.w;
                            rectData[i*4 + 3] = r.h;
                        } else {
                             rectData[i*4] = 0; // Empty
                        }
                    }

                    // PASS 1: Feedback
                    gl.bindFramebuffer(gl.FRAMEBUFFER, fbo2.fbo);
                    gl.viewport(0, 0, CONFIG.INTERNAL_RES_X, CONFIG.INTERNAL_RES_Y);
                    gl.useProgram(progFeedback);

                    gl.uniform2f(locs.fb.iRes, CONFIG.INTERNAL_RES_X, CONFIG.INTERNAL_RES_Y);
                    gl.uniform1f(locs.fb.iTime, time * 0.001);
                    gl.uniform1f(locs.fb.uDecay, configRef.current.feedbackDecay);
                    gl.uniform1f(locs.fb.uSort, configRef.current.pixelSorting ? 1.0 : 0.0);

                    gl.uniform4fv(locs.fb.uRects, rectData);
                    gl.uniform1iv(locs.fb.uEffects, new Int32Array(simState.current.effects));
                    gl.uniform1fv(locs.fb.uSpeeds, new Float32Array(simState.current.speeds));
                    gl.uniform1fv(locs.fb.uSeeds, new Float32Array(simState.current.seeds));
                    gl.uniform1iv(locs.fb.uRotations, new Int32Array(simState.current.rotations));

                    gl.activeTexture(gl.TEXTURE0);
                    gl.bindTexture(gl.TEXTURE_2D, fbo1.texture);
                    gl.uniform1i(locs.fb.uPrev, 0);

                    gl.bindBuffer(gl.ARRAY_BUFFER, buf);
                    gl.enableVertexAttribArray(locs.fb.aPos);
                    gl.vertexAttribPointer(locs.fb.aPos, 2, gl.FLOAT, false, 0, 0);
                    gl.drawArrays(gl.TRIANGLES, 0, 6);

                    // PASS 2: Screen
                    gl.bindFramebuffer(gl.FRAMEBUFFER, null);
                    gl.viewport(0, 0, canvas.width, canvas.height);
                    gl.useProgram(progScreen);

                    gl.uniform2f(locs.sc.iRes, canvas.width, canvas.height);
                    gl.uniform1f(locs.sc.uSharp, configRef.current.sharpen ? 1.0 : 0.0);

                    gl.activeTexture(gl.TEXTURE0);
                    gl.bindTexture(gl.TEXTURE_2D, fbo2.texture);
                    gl.uniform1i(locs.sc.uFrame, 0);

                    gl.drawArrays(gl.TRIANGLES, 0, 6);

                    [fbo1, fbo2] = [fbo2, fbo1];
                    reqIdRef.current = requestAnimationFrame(render);
                };
                
                reqIdRef.current = requestAnimationFrame(render);

                return () => {
                    cancelAnimationFrame(reqIdRef.current);
                    window.removeEventListener('resize', resize);
                    gl.deleteProgram(progFeedback);
                    gl.deleteProgram(progScreen);
                    gl.deleteFramebuffer(fbo1.fbo);
                    gl.deleteFramebuffer(fbo2.fbo);
                };
            }, []);

            return (
                <div className="relative w-full h-full group bg-black flex items-center justify-center overflow-hidden">
                    <canvas 
                        ref={canvasRef} 
                        className="w-full h-full object-cover"
                    />
                </div>
            );
        };

        const App = () => {
            return (
                <div className="w-screen h-screen flex items-center justify-center bg-black">
                    <ShaderCanvas />
                </div>
            );
        };
        
        const rootElement = document.getElementById('root');
        if (!rootElement) {
            throw new Error("Could not find root element to mount to");
        }
        
        const root = ReactDOM.createRoot(rootElement);
        root.render(
            <React.StrictMode>
                <App />
            </React.StrictMode>
        );

    </script>
</body>
</html>
