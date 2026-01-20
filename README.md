<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Minimalist Text Dungeon</title>
    <style>
        body { background: #1a1a1a; color: #00ff41; font-family: 'Courier New', Courier, monospace; display: flex; justify-content: center; padding: 20px; }
        #game-container { width: 100%; max-width: 600px; border: 2px solid #00ff41; padding: 20px; background: #000; box-shadow: 0 0 15px rgba(0, 255, 65, 0.2); }
        .stats-bar { display: flex; justify-content: space-between; border-bottom: 1px solid #333; padding-bottom: 10px; margin-bottom: 20px; }
        #log { height: 200px; overflow-y: auto; border: 1px solid #333; padding: 10px; margin-bottom: 20px; background: #050505; }
        .controls { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
        button { background: #000; color: #00ff41; border: 1px solid #00ff41; padding: 10px; cursor: pointer; font-family: inherit; font-weight: bold; }
        button:hover { background: #00ff41; color: #000; }
        button:disabled { border-color: #333; color: #333; cursor: not-allowed; }
        .upgrade-section { margin-top: 20px; padding-top: 10px; border-top: 1px dashed #333; }
        .danger { color: #ff4141; }
        .success { color: #ffff41; }
    </style>
</head>
<body>

<div id="game-container">
    <div class="stats-bar">
        <div>HP: <span id="hp">20</span>/20</div>
        <div>ATK: <span id="atk">5</span></div>
        <div>Points: <span id="points">0</span></div>
    </div>

    <div id="log">Welcome to the Deep Dungeon... <br>Click "Find Enemy" to begin.</div>

    <div class="controls" id="battle-controls" style="display: none;">
        <button onclick="attackEnemy()">Attack</button>
        <button onclick="heal()">Heal (1 Point)</button>
    </div>

    <div class="controls" id="explore-controls">
        <button onclick="spawnEnemy()">Find Enemy</button>
    </div>

    <div class="upgrade-section">
        <strong>Upgrades (Cost: 2 Points)</strong>
        <div class="controls">
            <button id="upg-atk" onclick="upgrade('atk')">+1 Attack</button>
            <button id="upg-hp" onclick="upgrade('hp')">+5 Max HP</button>
        </div>
    </div>
</div>

<script>
    // Game State
    let player = { hp: 20, maxHp: 20, atk: 5, points: 0 };
    let enemy = { name: "", hp: 0, atk: 0 };
    const logEl = document.getElementById('log');

    function updateUI() {
        document.getElementById('hp').innerText = player.hp;
        document.getElementById('atk').innerText = player.atk;
        document.getElementById('points').innerText = player.points;
        
        // Disable upgrades if not enough points
        document.getElementById('upg-atk').disabled = player.points < 2;
        document.getElementById('upg-hp').disabled = player.points < 2;
    }

    function log(message, className = "") {
        const entry = document.createElement('div');
        entry.className = className;
        entry.innerHTML = `> ${message}`;
        logEl.appendChild(entry);
        logEl.scrollTop = logEl.scrollHeight;
    }

    function spawnEnemy() {
        const names = ["Goblin", "Slime", "Skeleton", "Dark Mage"];
        enemy.name = names[Math.floor(Math.random() * names.length)];
        enemy.hp = 10 + (player.points * 2); // Enemies get tougher
        enemy.atk = 3 + Math.floor(player.points / 2);
        
        log(`A wild ${enemy.name} appeared! (HP: ${enemy.hp})`);
        document.getElementById('explore-controls').style.display = 'none';
        document.getElementById('battle-controls').style.display = 'grid';
    }

    function attackEnemy() {
        // Player Attacks
        let damage = player.atk;
        enemy.hp -= damage;
        log(`You hit ${enemy.name} for ${damage}.`, "success");

        if (enemy.hp <= 0) {
            log(`${enemy.name} defeated! You gained 1 point.`);
            player.points++;
            endBattle();
            return;
        }

        // Enemy Attacks Back
        let enemyDamage = Math.max(1, enemy.atk - Math.floor(Math.random() * 2));
        player.hp -= enemyDamage;
        log(`${enemy.name} hits you for ${enemyDamage}.`, "danger");

        if (player.hp <= 0) {
            log("YOU DIED. Refresh to restart.", "danger");
            document.getElementById('battle-controls').style.display = 'none';
        }
        updateUI();
    }

    function heal() {
        if (player.points >= 1 && player.hp < player.maxHp) {
            player.points--;
            player.hp = Math.min(player.maxHp, player.hp + 10);
            log("Healed 10 HP!");
            updateUI();
        } else {
            log("Cannot heal right now.");
        }
    }

    function upgrade(type) {
        if (player.points >= 2) {
            player.points -= 2;
            if (type === 'atk') player.atk += 1;
            if (type === 'hp') {
                player.maxHp += 5;
                player.hp += 5;
            }
            log(`Upgraded ${type.toUpperCase()}!`);
            updateUI();
        }
    }

    function endBattle() {
        document.getElementById('explore-controls').style.display = 'grid';
        document.getElementById('battle-controls').style.display = 'none';
        updateUI();
    }

    updateUI(); // Initial call
</script>

</body>
</html>
