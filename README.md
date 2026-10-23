**<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Forge & Conquer 3D - Full Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@700;900&family=Inter:wght@400;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background: #050811;
            color: #f8fafc;
            overflow-x: hidden;
        }
        .font-cinzel { font-family: 'Cinzel', serif; }
        
        .shake {
            animation: shake 0.3s cubic-bezier(.36,.07,.19,.97) both;
        }
        @keyframes shake {
            10%, 90% { transform: translate3d(-2px, 0, 0); }
            20%, 80% { transform: translate3d(4px, 0, 0); }
            30%, 50%, 70% { transform: translate3d(-6px, 0, 0); }
            40%, 60% { transform: translate3d(6px, 0, 0); }
        }

        .damage-pop {
            position: absolute;
            animation: floatUp 0.8s ease-out forwards;
            pointer-events: none;
            font-weight: 800;
            text-shadow: 0 0 10px rgba(0,0,0,0.9);
            z-index: 50;
        }
        @keyframes floatUp {
            0% { opacity: 1; transform: translateY(0) scale(0.8); }
            50% { opacity: 1; transform: translateY(-35px) scale(1.3); }
            100% { opacity: 0; transform: translateY(-70px) scale(1); }
        }

        .tab-btn-active {
            background: linear-gradient(135deg, #d97706 0%, #b45309 100%);
            border-color: #f59e0b;
            box-shadow: 0 0 20px rgba(245, 158, 11, 0.4);
        }

        .rarity-Common { border-color: #94a3b8; }
        .rarity-Rare { border-color: #3b82f6; box-shadow: 0 0 10px rgba(59, 130, 246, 0.3); }
        .rarity-Epic { border-color: #a855f7; box-shadow: 0 0 15px rgba(168, 85, 247, 0.4); }
        .rarity-Legendary { border-color: #eab308; box-shadow: 0 0 20px rgba(234, 179, 8, 0.6); }
        .rarity-Mythic { border-color: #10b981; box-shadow: 0 0 25px rgba(16, 185, 129, 0.6); }

        .rarity-text-Common { color: #94a3b8; }
        .rarity-text-Rare { color: #60a5fa; }
        .rarity-text-Epic { color: #c084fc; }
        .rarity-text-Legendary { color: #fde047; }
        .rarity-text-Mythic { color: #34d399; }
    </style>
</head>
<body class="min-h-screen flex flex-col items-center justify-between p-2 sm:p-6 bg-slate-950">

    <header class="w-full max-w-5xl bg-slate-900/90 border border-slate-800/80 backdrop-blur-md rounded-2xl p-4 mb-4 shadow-2xl flex flex-wrap justify-between items-center gap-4">
        <div class="flex items-center gap-3">
            <div class="w-12 h-12 bg-amber-600/20 border border-amber-500/50 rounded-xl flex items-center justify-center text-2xl shadow-inner">
                ⚒️
            </div>
            <div>
                <h1 class="font-cinzel text-xl font-bold text-amber-400 tracking-wider">FORGE & CONQUER 3D</h1>
                <p class="text-xs text-slate-400">Cấp Độ Thợ Rèn: <span id="playerLvl" class="text-amber-300 font-bold">1</span></p>
            </div>
        </div>

        <div class="flex flex-wrap gap-2.5 text-xs sm:text-sm">
            <div class="bg-slate-950/80 px-3 py-1.5 rounded-xl border border-slate-800 flex items-center gap-1.5">
                <span>💰 Vàng:</span>
                <span id="resGold" class="text-yellow-400 font-bold">0</span>
            </div>
            <div class="bg-slate-950/80 px-3 py-1.5 rounded-xl border border-slate-800 flex items-center gap-1.5">
                <span>🪵 Gỗ:</span>
                <span id="resWood" class="text-emerald-400 font-bold">0</span>
            </div>
            <div class="bg-slate-950/80 px-3 py-1.5 rounded-xl border border-slate-800 flex items-center gap-1.5">
                <span>🪨 Sắt:</span>
                <span id="resIron" class="text-slate-300 font-bold">0</span>
            </div>
            <div class="bg-slate-950/80 px-3 py-1.5 rounded-xl border border-slate-800 flex items-center gap-1.5">
                <span>🔮 Đá Hồn:</span>
                <span id="resSoul" class="text-purple-400 font-bold">0</span>
            </div>
        </div>
    </header>

    <nav class="w-full max-w-5xl flex gap-3 mb-4">
        <button id="tabForgeBtn" onclick="switchTab('forge')" class="tab-btn-active flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-800 transition flex items-center justify-center gap-2 text-white shadow-lg">
            <span>🔨</span> RÈN VŨ KHÍ 3D & KHO
        </button>
        <button id="tabBattleBtn" onclick="switchTab('battle')" class="flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-800 hover:bg-slate-700 transition flex items-center justify-center gap-2 text-slate-300 shadow-lg">
            <span>⚔️</span> PLAY / CHIẾN ĐẤU BOSS 3D
        </button>
    </nav>

    <main class="w-full max-w-5xl flex-1 flex flex-col">
        
        <section id="pageForge" class="grid grid-cols-1 md:grid-cols-2 gap-5 flex-1">
            <div class="bg-slate-900/80 border border-slate-800/80 backdrop-blur rounded-2xl p-5 flex flex-col gap-4 shadow-xl">
                <h2 class="text-lg font-bold text-amber-400 border-b border-slate-800 pb-2.5 flex items-center justify-between">
                    <span>🔨 Lò Rèn & Xem Vũ Khí 3D</span>
                    <button onclick="gatherResources()" class="text-xs bg-emerald-600 hover:bg-emerald-500 text-white font-bold py-1.5 px-3 rounded-lg transition active:scale-95 shadow">
                        ⛏️ Khai Thác Nguyên Liệu
                    </button>
                </h2>

                <div class="relative w-full h-52 bg-slate-950/90 rounded-xl border border-slate-800 overflow-hidden flex items-center justify-center">
                    <div id="weapon3DCanvas" class="w-full h-full"></div>
                    <div class="absolute bottom-2 left-3 text-[10px] text-amber-400 font-semibold bg-slate-900/80 px-2 py-0.5 rounded border border-amber-500/30">
                        🔄 Xoay Mẫu Vũ Khí 3D
                    </div>
                </div>

                <div class="space-y-3">
                    <label class="text-xs text-slate-400 block font-semibold">Chọn Công Thức Rèn Vũ Khí:</label>
                    <div id="recipeList" class="grid grid-cols-1 gap-2 max-h-44 overflow-y-auto pr-1"></div>

                    <button id="btnForgeAction" onclick="forgeWeapon()" class="w-full py-3.5 bg-gradient-to-r from-amber-600 to-amber-500 hover:from-amber-500 hover:to-amber-400 font-bold rounded-xl text-slate-950 shadow-lg shadow-amber-600/30 active:scale-98 transition flex items-center justify-center gap-2 text-base">
                        <span>🔥</span> RÈN VŨ KHÍ NÀY
                    </button>
                </div>
            </div>

            <div class="bg-slate-900/80 border border-slate-800/80 backdrop-blur rounded-2xl p-5 flex flex-col gap-3 shadow-xl">
                <h2 class="text-lg font-bold text-amber-400 border-b border-slate-800 pb-2.5 flex justify-between items-center">
                    <span>🎒 Kho Vũ Khí Của Bạn</span>
                    <span class="text-xs text-slate-500" id="inventoryCount">0/12</span>
                </h2>

                <div id="inventoryGrid" class="grid grid-cols-1 gap-2.5 flex-1 max-h-[460px] overflow-y-auto pr-1"></div>
            </div>
        </section>

        <section id="pageBattle" class="hidden flex-1 flex-col gap-4">
            <div class="bg-slate-900/80 border border-slate-800/80 backdrop-blur rounded-2xl p-3 flex flex-wrap items-center justify-between gap-2 shadow-lg">
                <span class="text-xs text-slate-400 font-bold">CHỌN VÙNG ĐẤT SĂN BOSS:</span>
                <div class="flex gap-2" id="dungeonButtons">
                    <button onclick="selectDungeon(0)" class="btn-dungeon border border-emerald-500/50 bg-emerald-950/40 text-emerald-300 text-xs px-3 py-1.5 rounded-xl font-semibold hover:bg-emerald-800/50 transition">🌲 Rừng Yêu Tinh</button>
                    <button onclick="selectDungeon(1)" class="btn-dungeon border border-slate-600 bg-slate-800/40 text-slate-300 text-xs px-3 py-1.5 rounded-xl font-semibold hover:bg-slate-700/50 transition">🗿 Golem Cổ Đại</button>
                    <button onclick="selectDungeon(2)" class="btn-dungeon border border-red-500/50 bg-red-950/40 text-red-300 text-xs px-3 py-1.5 rounded-xl font-semibold hover:bg-red-800/50 transition">🌋 Hỏa Rồng</button>
                    <button onclick="selectDungeon(3)" class="btn-dungeon border border-cyan-500/50 bg-cyan-950/40 text-cyan-300 text-xs px-3 py-1.5 rounded-xl font-semibold hover:bg-cyan-800/50 transition">🌊 Leviathan</button>
                    <button onclick="selectDungeon(4)" class="btn-dungeon border border-purple-500/50 bg-purple-950/40 text-purple-300 text-xs px-3 py-1.5 rounded-xl font-semibold hover:bg-purple-800/50 transition">👑 Ma Vương</button>
                </div>
            </div>

            <div id="battleArena" class="relative bg-slate-900/90 border border-slate-800/80 rounded-2xl p-6 flex-1 flex flex-col items-center justify-between min-h-[440px] overflow-hidden shadow-2xl">
                <div class="text-center z-10">
                    <span id="dungeonName" class="text-xs font-bold uppercase tracking-widest text-amber-500">Rừng Yêu Tinh</span>
                    <h3 id="monsterName" class="text-2xl font-bold font-cinzel text-slate-100 mt-0.5">Yêu Tinh Rừng 3D</h3>
                </div>

                <div class="relative my-2 w-full max-w-md h-64 flex flex-col items-center justify-center">
                    <div id="boss3DCanvas" class="w-full h-full cursor-pointer bg-slate-950/40 rounded-2xl border border-slate-800/50" onclick="playerAttack()"></div>
                    
                    <div class="w-full bg-slate-950 rounded-full h-4 p-0.5 border border-slate-800 overflow-hidden shadow-inner mt-2">
                        <div id="monsterHpBar" class="bg-gradient-to-r from-red-600 to-rose-500 h-full rounded-full transition-all duration-200" style="width: 100%;"></div>
                    </div>
                    <div id="monsterHpText" class="text-xs text-slate-400 font-bold mt-1">120 / 120 HP</div>
                </div>

                <div class="w-full max-w-md flex gap-3 z-10">
                    <button onclick="playerAttack()" class="flex-1 py-3.5 bg-gradient-to-r from-red-600 to-rose-600 hover:from-red-500 hover:to-rose-500 font-bold rounded-xl text-white shadow-lg shadow-red-600/30 active:scale-95 transition flex items-center justify-center gap-2 text-base">
                        <span>⚔️</span> TẤN CÔNG (CLICK)
                    </button>
                    <button id="btnAutoAttack" onclick="toggleAutoAttack()" class="py-3.5 px-5 bg-slate-800 border border-slate-700 hover:bg-slate-700 font-semibold rounded-xl text-slate-300 transition text-sm">
                        🤖 Tự Động: OFF
                    </button>
                </div>
            </div>
        </section>

    </main>

    <footer class="w-full max-w-5xl mt-4 bg-slate-900/60 border border-slate-800/80 backdrop-blur rounded-xl p-3 text-xs text-slate-400 flex items-center justify-between shadow-lg">
        <span class="font-semibold text-amber-500/90">📜 Nhật Ký:</span>
        <span id="gameLog" class="truncate max-w-md text-right text-slate-300">Chào mừng bạn đến với Forge & Conquer 3D!</span>
    </footer>

    <script>
        let audioCtx = null;
        function initAudio() {
            if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            if (audioCtx.state === 'suspended') audioCtx.resume();
        }

        function playSound(type) {
            try {
                initAudio();
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.connect(gain);
                gain.connect(audioCtx.destination);

                if (type === 'hit') {
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(160, audioCtx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(30, audioCtx.currentTime + 0.08);
                    gain.gain.setValueAtTime(0.3, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.08);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.08);
                } else if (type === 'crit') {
                    osc.type = 'sawtooth';
                    osc.frequency.setValueAtTime(320, audioCtx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(650, audioCtx.currentTime + 0.15);
                    gain.gain.setValueAtTime(0.4, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.15);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.15);
                } else if (type === 'forge') {
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(523.25, audioCtx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(1046.5, audioCtx.currentTime + 0.2);
                    gain.gain.setValueAtTime(0.2, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.2);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.2);
                }
            } catch(e) {}
        }

        let player = {
            level: 1,
            gold: 100,
            wood: 30,
            iron: 20,
            soul: 5,
            inventory: [],
            equippedId: null
        };

        const recipes = [
            { id: 'sword_wood', name: 'Kiếm Gỗ Tập Sự', modelType: 'wood', wood: 10, iron: 0, soul: 0, atk: 10, crit: 5, rarity: 'Common' },
            { id: 'sword_iron', name: 'Trường Kiếm Sắt', modelType: 'iron', wood: 15, iron: 15, soul: 0, atk: 28, crit: 10, rarity: 'Rare' },
            { id: 'axe_steel', name: 'Rìu Chiến Thép', modelType: 'axe', wood: 15, iron: 30, soul: 3, atk: 60, crit: 15, rarity: 'Epic' },
            { id: 'sword_emerald', name: 'Kiếm Lục Bảo Sáng', modelType: 'emerald', wood: 20, iron: 40, soul: 5, atk: 110, crit: 20, rarity: 'Epic' },
            { id: 'sword_diamond', name: 'Thánh Kiếm Kim Cương', modelType: 'diamond', wood: 30, iron: 60, soul: 8, atk: 180, crit: 25, rarity: 'Legendary' },
            { id: 'sword_mythic', name: 'Cự Kiếm Hư Vô Tối Thượng', modelType: 'mythic', wood: 50, iron: 100, soul: 20, atk: 350, crit: 40, rarity: 'Mythic' }
        ];

        let selectedRecipeId = 'sword_wood';

        const dungeons = [
            { name: "Rừng Yêu Tinh", monster: "Yêu Tinh Rừng 3D", bossType: 'goblin', maxHp: 120, goldMin: 10, goldMax: 20, woodDrop: 5, ironDrop: 2, soulDrop: 0 },
            { name: "Hang Cổ Đảo", monster: "Golem Cổ Đại 3D", bossType: 'golem', maxHp: 450, goldMin: 35, goldMax: 60, woodDrop: 2, ironDrop: 10, soulDrop: 2 },
            { name: "Hang Rồng Lửa", monster: "Hỏa Rồng Cổ Đại 3D", bossType: 'dragon', maxHp: 1500, goldMin: 100, goldMax: 200, woodDrop: 0, ironDrop: 20, soulDrop: 5 },
            { name: "Vực Thẳm Đại Dương", monster: "Leviathan Thủy Quái 3D", bossType: 'leviathan', maxHp: 5000, goldMin: 300, goldMax: 600, woodDrop: 0, ironDrop: 40, soulDrop: 12 },
            { name: "Lâu Đài Hắc Ám", monster: "Ma Vương Bóng Tối 3D", bossType: 'demon', maxHp: 15000, goldMin: 1000, goldMax: 2000, woodDrop: 10, ironDrop: 80, soulDrop: 30 }
        ];

        let currentDungeonIdx = 0;
        let currentMonsterHp = dungeons[0].maxHp;
        let autoAttackInterval = null;

        let wpScene, wpCamera, wpRenderer, wpMeshGroup;
        let bossScene, bossCamera, bossRenderer, bossMeshGroup;

        function init3DScenes() {
            const wpContainer = document.getElementById("weapon3DCanvas");
            wpScene = new THREE.Scene();
            wpCamera = new THREE.PerspectiveCamera(45, wpContainer.clientWidth / wpContainer.clientHeight, 0.1, 1000);
            wpCamera.position.set(0, 0, 5.5);

            wpRenderer = new THREE.WebGLRenderer({ alpha: true, antialias: true });
            wpRenderer.setSize(wpContainer.clientWidth, wpContainer.clientHeight);
            wpContainer.appendChild(wpRenderer.domElement);

            const wpLight1 = new THREE.DirectionalLight(0xffffff, 1.5);
            wpLight1.position.set(5, 5, 5);
            wpScene.add(wpLight1);
            wpScene.add(new THREE.AmbientLight(0xffffff, 0.7));

            wpMeshGroup = new THREE.Group();
            wpScene.add(wpMeshGroup);

            const bossContainer = document.getElementById("boss3DCanvas");
            bossScene = new THREE.Scene();
            bossCamera = new THREE.PerspectiveCamera(45, bossContainer.clientWidth / bossContainer.clientHeight, 0.1, 1000);
            bossCamera.position.set(0, 0, 6);

            bossRenderer = new THREE.WebGLRenderer({ alpha: true, antialias: true });
            bossRenderer.setSize(bossContainer.clientWidth, bossContainer.clientHeight);
            bossContainer.appendChild(bossRenderer.domElement);

            const bossLight1 = new THREE.DirectionalLight(0xffffff, 1.8);
            bossLight1.position.set(5, 8, 5);
            bossScene.add(bossLight1);
            const bossLight2 = new THREE.DirectionalLight(0x38bdf8, 1.2);
            bossLight2.position.set(-5, -2, -2);
            bossScene.add(bossLight2);
            bossScene.add(new THREE.AmbientLight(0xffffff, 0.8));

            bossMeshGroup = new THREE.Group();
            bossScene.add(bossMeshGroup);

            animate3D();
            updateWeapon3DModel('wood');
            updateBoss3DModel('goblin');
        }

        function resize3DCanvases() {
            setTimeout(() => {
                const bossContainer = document.getElementById("boss3DCanvas");
                if (bossContainer && bossRenderer && bossCamera) {
                    const w = bossContainer.clientWidth || 300;
                    const h = bossContainer.clientHeight || 240;
                    bossCamera.aspect = w / h;
                    bossCamera.updateProjectionMatrix();
                    bossRenderer.setSize(w, h);
                }

                const wpContainer = document.getElementById("weapon3DCanvas");
                if (wpContainer && wpRenderer && wpCamera) {
                    const w = wpContainer.clientWidth || 300;
                    const h = wpContainer.clientHeight || 200;
                    wpCamera.aspect = w / h;
                    wpCamera.updateProjectionMatrix();
                    wpRenderer.setSize(w, h);
                }
            }, 50);
        }

        function createWeaponMesh(modelType) {
            const group = new THREE.Group();

            if (modelType === 'wood') {
                const bladeGeo = new THREE.ConeGeometry(0.25, 3.2, 4);
                const bladeMat = new THREE.MeshStandardMaterial({ color: 0x9a6b38, roughness: 0.7 });
                const blade = new THREE.Mesh(bladeGeo, bladeMat);
                blade.rotation.y = Math.PI / 4;
                blade.position.y = 0.8;
                group.add(blade);
            } else if (modelType === 'iron') {
                const bladeGeo = new THREE.ConeGeometry(0.3, 3.6, 4);
                const bladeMat = new THREE.MeshStandardMaterial({ color: 0xe2e8f0, metalness: 0.95, roughness: 0.15 });
                const blade = new THREE.Mesh(bladeGeo, bladeMat);
                blade.rotation.y = Math.PI / 4;
                blade.position.y = 1;
                group.add(blade);
            } else if (modelType === 'axe') {
                const handleGeo = new THREE.CylinderGeometry(0.09, 0.09, 3.6, 12);
                const handleMat = new THREE.MeshStandardMaterial({ color: 0x334155, metalness: 0.6 });
                const handle = new THREE.Mesh(handleGeo, handleMat);
                group.add(handle);

                const headGeo = new THREE.CylinderGeometry(0.8, 1.2, 0.1, 3);
                const headMat = new THREE.MeshStandardMaterial({ color: 0xa855f7, metalness: 0.9 });
                const head = new THREE.Mesh(headGeo, headMat);
                head.rotation.z = Math.PI / 2;
                head.position.set(0.6, 0.8, 0);
                group.add(head);
            } else if (modelType === 'emerald') {
                const bladeGeo = new THREE.ConeGeometry(0.35, 4, 4);
                const bladeMat = new THREE.MeshStandardMaterial({ color: 0x10b981, metalness: 0.8, roughness: 0.2, emissive: 0x059669, emissiveIntensity: 0.3 });
                const blade = new THREE.Mesh(bladeGeo, bladeMat);
                blade.rotation.y = Math.PI / 4;
                blade.position.y = 1;
                group.add(blade);
            } else if (modelType === 'diamond') {
                const bladeGeo = new THREE.OctahedronGeometry(1.5, 0);
                const bladeMat = new THREE.MeshStandardMaterial({ color: 0x38bdf8, metalness: 0.9, roughness: 0.1, emissive: 0x0284c7, emissiveIntensity: 0.4 });
                const blade = new THREE.Mesh(bladeGeo, bladeMat);
                blade.position.y = 0.5;
                group.add(blade);
            } else if (modelType === 'mythic') {
                const bladeGeo = new THREE.TorusKnotGeometry(1.2, 0.4, 64, 16);
                const bladeMat = new THREE.MeshStandardMaterial({ color: 0xa855f7, wireframe: true, emissive: 0xc084fc, emissiveIntensity: 0.8 });
                const blade = new THREE.Mesh(bladeGeo, bladeMat);
                group.add(blade);
            }

            return group;
        }

        function updateWeapon3DModel(modelType) {
            while(wpMeshGroup.children.length > 0) { 
                wpMeshGroup.remove(wpMeshGroup.children[0]); 
            }
            const mesh = createWeaponMesh(modelType);
            wpMeshGroup.add(mesh);
        }

        function createBossMesh(type) {
            const group = new THREE.Group();

            if (type === 'goblin') {
                const headGeo = new THREE.SphereGeometry(1.2, 16, 16);
                const skinMat = new THREE.MeshStandardMaterial({ color: 0x22c55e, roughness: 0.4 });
                const head = new THREE.Mesh(headGeo, skinMat);
                head.position.y = 0.5;
                group.add(head);
            } else if (type === 'golem') {
                const stoneMat = new THREE.MeshStandardMaterial({ color: 0x94a3b8, roughness: 0.8 });
                const headGeo = new THREE.BoxGeometry(1.6, 1.4, 1.3);
                const head = new THREE.Mesh(headGeo, stoneMat);
                head.position.y = 1.0;
                group.add(head);

                const bodyGeo = new THREE.BoxGeometry(2.6, 2.2, 1.8);
                const body = new THREE.Mesh(bodyGeo, stoneMat);
                body.position.y = -0.8;
                group.add(body);
            } else if (type === 'dragon') {
                const dragonMat = new THREE.MeshStandardMaterial({ color: 0xeab308, roughness: 0.2, emissive: 0xd97706, emissiveIntensity: 0.5 });
                const headGeo = new THREE.ConeGeometry(1.2, 2.8, 8);
                const head = new THREE.Mesh(headGeo, dragonMat);
                head.rotation.x = Math.PI / 2.5;
                head.position.set(0, 0.2, 0.4);
                group.add(head);
            } else if (type === 'leviathan') {
                const waterMat = new THREE.MeshStandardMaterial({ color: 0x06b6d4, roughness: 0.1, metalness: 0.5, transparent: true, opacity: 0.85 });
                const bodyGeo = new THREE.CylinderGeometry(1.5, 2.5, 4, 16);
                const body = new THREE.Mesh(bodyGeo, waterMat);
                body.rotation.z = Math.PI / 2;
                group.add(body);

                const tentacleGeo = new THREE.TorusGeometry(2, 0.5, 8, 24);
                const tentacle = new THREE.Mesh(tentacleGeo, waterMat);
                tentacle.rotation.x = Math.PI / 2;
                group.add(tentacle);
            } else if (type === 'demon') {
                const demonMat = new THREE.MeshStandardMaterial({ color: 0x4c1d95, roughness: 0.3, emissive: 0x6d28d9, emissiveIntensity: 0.6 });
                const bodyGeo = new THREE.BoxGeometry(2, 3, 1.5);
                const body = new THREE.Mesh(bodyGeo, demonMat);
                group.add(body);

                const hornGeo = new THREE.ConeGeometry(0.5, 2, 6);
                const horn = new THREE.Mesh(hornGeo, demonMat);
                horn.position.set(-1, 2, 0);
                horn.rotation.z = -Math.PI / 6;
                const horn2 = horn.clone();
                horn2.position.x = 1;
                horn2.rotation.z = Math.PI / 6;
                group.add(horn); group.add(horn2);
            }

            return group;
        }

        function updateBoss3DModel(type) {
            while(bossMeshGroup.children.length > 0) { 
                bossMeshGroup.remove(bossMeshGroup.children[0]); 
            }
            const mesh = createBossMesh(type);
            bossMeshGroup.add(mesh);
        }

        let time3D = 0;
        function animate3D() {
            requestAnimationFrame(animate3D);
            time3D += 0.03;

            if (wpMeshGroup) {
                wpMeshGroup.rotation.y += 0.025;
            }

            if (bossMeshGroup) {
                bossMeshGroup.rotation.y = Math.sin(time3D * 0.5) * 0.4;
                bossMeshGroup.position.y = Math.sin(time3D * 1.5) * 0.15;
            }

            wpRenderer.render(wpScene, wpCamera);
            bossRenderer.render(bossScene, bossCamera);
        }

        function switchTab(tab) {
            const pageForge = document.getElementById("pageForge");
            const pageBattle = document.getElementById("pageBattle");
            const btnForge = document.getElementById("tabForgeBtn");
            const btnBattle = document.getElementById("tabBattleBtn");

            if (tab === 'forge') {
                pageForge.classList.remove("hidden");
                pageForge.classList.add("grid");
                pageBattle.classList.add("hidden");
                pageBattle.classList.remove("flex");

                btnForge.className = "tab-btn-active flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 transition flex items-center justify-center gap-2 text-white shadow-lg";
                btnBattle.className = "flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-800 hover:bg-slate-700 transition flex items-center justify-center gap-2 text-slate-300 shadow-lg";
            } else {
                pageForge.classList.add("hidden");
                pageForge.classList.remove("grid");
                pageBattle.classList.remove("hidden");
                pageBattle.classList.add("flex");

                btnBattle.className = "tab-btn-active flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 transition flex items-center justify-center gap-2 text-white shadow-lg";
                btnForge.className = "flex-1 py-3.5 px-4 rounded-xl font-bold text-sm sm:text-base border border-slate-700 bg-slate-800 hover:bg-slate-700 transition flex items-center justify-center gap-2 text-slate-300 shadow-lg";
                resize3DCanvases();
            }
        }

        function gatherResources() {
            player.wood += Math.floor(Math.random() * 4) + 3;
            player.iron += Math.floor(Math.random() * 3) + 2;
            log("Đã khai thác thêm nguyên liệu gỗ và sắt!");
            updateUI();
        }

        function renderRecipes() {
            const list = document.getElementById("recipeList");
            list.innerHTML = "";
            recipes.forEach(r => {
                const isSelected = r.id === selectedRecipeId;
                const div = document.createElement("div");
                div.className = `p-2.5 rounded-xl border cursor-pointer transition flex items-center justify-between ${isSelected ? 'border-amber-500 bg-amber-950/40' : 'border-slate-800 bg-slate-950/50 hover:bg-slate-800/40'}`;
                div.onclick = () => { 
                    selectedRecipeId = r.id; 
                    updateWeapon3DModel(r.modelType);
                    renderRecipes(); 
                };
                
                div.innerHTML = `
                    <div>
                        <div class="font-bold text-xs sm:text-sm rarity-text-${r.rarity}">${r.name}</div>
                        <div class="text-[11px] text-slate-400">ATK: +${r.atk} | CRIT: ${r.crit}%</div>
                    </div>
                    <div class="text-right text-[11px] text-slate-400">
                        <div>🪵 ${r.wood} | 🪨 ${r.iron} | 🔮 ${r.soul}</div>
                    </div>
                `;
                list.appendChild(div);
            });
        }

        function forgeWeapon() {
            const recipe = recipes.find(r => r.id === selectedRecipeId);
            if (!recipe) return;

            if (player.inventory.length >= 12) {
                log("Túi đồ đã đầy (Tối đa 12 vũ khí)!");
                return;
            }

            if (player.wood < recipe.wood || player.iron < recipe.iron || player.soul < recipe.soul) {
                log("Không đủ nguyên liệu để rèn vũ khí này!");
                return;
            }

            player.wood -= recipe.wood;
            player.iron -= recipe.iron;
            player.soul -= recipe.soul;

            const newWeapon = {
                id: 'wp_' + Date.now(),
                name: recipe.name,
                modelType: recipe.modelType,
                atk: recipe.atk,
                crit: recipe.crit,
                rarity: recipe.rarity,
                level: 1
            };

            player.inventory.push(newWeapon);
            if (!player.equippedId) player.equippedId = newWeapon.id;

            playSound('forge');
            log(`Rèn thành công: ${newWeapon.name}!`);
            updateUI();
        }

        function equipWeapon(id) {
            player.equippedId = id;
            log("Đã trang bị vũ khí mới!");
            updateUI();
        }

        function upgradeWeapon(id) {
            const item = player.inventory.find(i => i.id === id);
            if (!item) return;
            const cost = item.level * 40;
            if (player.gold < cost) {
                log(`Cần ${cost} Vàng để nâng cấp!`);
                return;
            }
            player.gold -= cost;
            item.level += 1;
            item.atk += Math.floor(item.atk * 0.25);
            log(`Nâng cấp thành công ${item.name} lên Cấp +${item.level}!`);
            updateUI();
        }

        function sellWeapon(id) {
            const idx = player.inventory.findIndex(i => i.id === id);
            if (idx === -1) return;
            if (player.equippedId === id) player.equippedId = null;
            player.inventory.splice(idx, 1);
            player.gold += 35;
            log("Đã bán vũ khí lấy +35 Vàng!");
            updateUI();
        }

        function renderInventory() {
            const grid = document.getElementById("inventoryGrid");
            document.getElementById("inventoryCount").innerText = `${player.inventory.length}/12`;
            grid.innerHTML = "";

            if (player.inventory.length === 0) {
                grid.innerHTML = `<div class="text-xs text-slate-500 text-center py-8">Chưa có vũ khí 3D nào trong kho.</div>`;
                return;
            }

            player.inventory.forEach(item => {
                const isEquipped = item.id === player.equippedId;
                const div = document.createElement("div");
                div.className = `p-3 rounded-xl border rarity-${item.rarity} bg-slate-950/70 flex items-center justify-between gap-2 shadow-md`;

                div.innerHTML = `
                    <div>
                        <div class="font-bold text-xs sm:text-sm rarity-text-${item.rarity}">${item.name} <span class="text-amber-400">+${item.level}</span></div>
                        <div class="text-[10px] text-slate-400">ATK: +${item.atk} | CRIT: ${item.crit}%</div>
                    </div>
                    <div class="flex gap-1">
                        ${isEquipped 
                            ? `<span class="text-[10px] bg-amber-500/20 text-amber-400 px-2 py-1 rounded border border-amber-500/30 font-bold">Đang dùng</span>` 
                            : `<button onclick="equipWeapon('${item.id}')" class="text-[10px] bg-slate-800 hover:bg-slate-700 text-slate-200 px-2 py-1 rounded border border-slate-700">Dùng</button>`
                        }
                        <button onclick="upgradeWeapon('${item.id}')" class="text-[10px] bg-indigo-900/60 hover:bg-indigo-800 text-indigo-200 px-2 py-1 rounded border border-indigo-700/50">Cường hóa (${item.level*40}G)</button>
                        <button onclick="sellWeapon('${item.id}')" class="text-[10px] bg-rose-950/50 hover:bg-rose-900 text-rose-300 px-1.5 py-1 rounded border border-rose-800/40">Bán</button>
                    </div>
                `;
                grid.appendChild(div);
            });
        }

        function selectDungeon(idx) {
            currentDungeonIdx = idx;
            currentMonsterHp = dungeons[idx].maxHp;
            updateBoss3DModel(dungeons[idx].bossType);
            updateBattleDisplay();
        }

        function updateBattleDisplay() {
            const d = dungeons[currentDungeonIdx];
            document.getElementById("dungeonName").innerText = d.name;
            document.getElementById("monsterName").innerText = d.monster;
            
            const pct = Math.max(0, (currentMonsterHp / d.maxHp) * 100);
            document.getElementById("monsterHpBar").style.width = pct + "%";
            document.getElementById("monsterHpText").innerText = `${Math.max(0, currentMonsterHp)} / ${d.maxHp} HP`;
        }

        function getPlayerStats() {
            let baseAtk = 12;
            let baseCrit = 5;
            if (player.equippedId) {
                const eq = player.inventory.find(i => i.id === player.equippedId);
                if (eq) {
                    baseAtk += eq.atk;
                    baseCrit += eq.crit;
                }
            }
            return { atk: baseAtk, crit: baseCrit };
        }

        function playerAttack() {
            const stats = getPlayerStats();
            const isCrit = Math.random() * 100 < stats.crit;
            let dmg = stats.atk;
            if (isCrit) dmg = Math.floor(dmg * 1.8);

            currentMonsterHp -= dmg;

            const bossCanvas = document.getElementById("boss3DCanvas");
            bossCanvas.classList.remove("shake");
            void bossCanvas.offsetWidth;
            bossCanvas.classList.add("shake");

            playSound(isCrit ? 'crit' : 'hit');
            spawnDamagePop(dmg, isCrit);

            if (currentMonsterHp <= 0) {
                monsterKilled();
            }

            updateBattleDisplay();
        }

        function spawnDamagePop(dmg, isCrit) {
            const arena = document.getElementById("battleArena");
            const pop = document.createElement("div");
            pop.className = `damage-pop ${isCrit ? 'text-purple-400 text-2xl' : 'text-amber-400 text-lg'}`;
            pop.innerText = (isCrit ? "💥 " : "") + "-" + dmg;
            pop.style.left = (Math.random() * 40 + 30) + "%";
            pop.style.top = "40%";
            arena.appendChild(pop);
            setTimeout(() => pop.remove(), 800);
        }

        function monsterKilled() {
            const d = dungeons[currentDungeonIdx];
            const rewardGold = Math.floor(Math.random() * (d.goldMax - d.goldMin + 1)) + d.goldMin;
            
            player.gold += rewardGold;
            player.wood += d.woodDrop;
            player.iron += d.ironDrop;
            player.soul += d.soulDrop;

            log(`Hạ gục ${d.monster}! Nhận +${rewardGold} Vàng, +${d.woodDrop} Gỗ, +${d.ironDrop} Sắt, +${d.soulDrop} Đá Hồn.`);
            
            currentMonsterHp = d.maxHp;
            updateUI();
        }

        function toggleAutoAttack() {
            const btn = document.getElementById("btnAutoAttack");
            if (autoAttackInterval) {
                clearInterval(autoAttackInterval);
                autoAttackInterval = null;
                btn.innerText = "🤖 Tự Động: OFF";
                btn.className = "py-3.5 px-5 bg-slate-800 border border-slate-700 hover:bg-slate-700 font-semibold rounded-xl text-slate-300 transition text-sm";
            } else {
                autoAttackInterval = setInterval(() => {
                    playerAttack();
                }, 800);
                btn.innerText = "🤖 Tự Động: ON";
                btn.className = "py-3.5 px-5 bg-amber-600/30 border border-amber-500/50 hover:bg-amber-600/40 font-bold rounded-xl text-amber-300 transition text-sm shadow-lg shadow-amber-600/20";
            }
        }

        function log(msg) {
            document.getElementById("gameLog").innerText = msg;
        }

        function updateUI() {
            document.getElementById("resGold").innerText = player.gold.toLocaleString();
            document.getElementById("resWood").innerText = player.wood.toLocaleString();
            document.getElementById("resIron").innerText = player.iron.toLocaleString();
            document.getElementById("resSoul").innerText = player.soul.toLocaleString();

            renderRecipes();
            renderInventory();
            updateBattleDisplay();
            saveGame();
        }

        function saveGame() {
            localStorage.setItem("forgeAndConquerFullSave", JSON.stringify(player));
        }

        function loadGame() {
            const data = localStorage.getItem("forgeAndConquerFullSave");
            if (data) {
                try {
                    player = Object.assign(player, JSON.parse(data));
                } catch(e) {}
            }
            updateUI();
        }

        window.onload = () => {
            init3DScenes();
            loadGame();
            window.addEventListener('resize', resize3DCanvases);
        };
    </script>
</body>
</html>**
