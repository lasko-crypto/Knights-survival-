<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Knight Quest</title>
    <script src="https://cdn.jsdelivr.net/npm/phaser@3.55.2/dist/phaser.min.js"></script>
</head>
<body>
<script>
    const config = {
        type: Phaser.AUTO,
        width: 800,
        height: 600,
        physics: { default: 'arcade', arcade: { gravity: { y: 0 } } },
        scene: { preload, create, update }
    };

    let knight, joystick, attackButton, health = 100, boss, bossHP = 200;
    let cursors, enemies = [], currentLevel = 1, levelText, map;

    const game = new Phaser.Game(config);

    function preload() {
        this.load.image('map', 'https://i.imgur.com/3YlXb4D.png'); // Map placeholder
        this.load.image('knight', 'https://i.imgur.com/bHHLGBh.png');
        this.load.image('enemy', 'https://i.imgur.com/WfOeMlW.png');
        this.load.image('boss', 'https://i.imgur.com/NZ3MuoB.png');
        this.load.image('joystick', 'https://i.imgur.com/Nsk3HBX.png');
        this.load.image('attack', 'https://i.imgur.com/FN9xJ8b.png');
    }

    function create() {
        // Add scrolling map
        map = this.add.tileSprite(0, 0, 1600, 1200, 'map').setOrigin(0, 0);
        this.cameras.main.setBounds(0, 0, 1600, 1200);
        this.physics.world.setBounds(0, 0, 1600, 1200);

        // Knight
        knight = this.physics.add.sprite(400, 300, 'knight').setScale(0.5).setCollideWorldBounds(true);
        this.cameras.main.startFollow(knight, true);

        // Joystick
        cursors = this.input.keyboard.createCursorKeys();

        // Attack Button
        attackButton = this.add.image(700, 500, 'attack').setInteractive().setScale(0.5);
        attackButton.on('pointerdown', () => attack());

        // Enemies
        spawnEnemies(this);

        // Boss
        boss = this.physics.add.sprite(1500, 200, 'boss').setScale(0.7);
        this.physics.add.collider(knight, boss, () => battleBoss());

        // Level Text
        levelText = this.add.text(20, 20, 'Level: 1', { fontSize: '24px', fill: '#FFF' }).setScrollFactor(0);
    }

    function update() {
        // Joystick Movement
        if (cursors.left.isDown) { knight.x -= 3; }
        if (cursors.right.isDown) { knight.x += 3; }
        if (cursors.up.isDown) { knight.y -= 3; }
        if (cursors.down.isDown) { knight.y += 3; }
    }

    function spawnEnemies(scene) {
        for (let i = 0; i < 5; i++) {
            let enemy = scene.physics.add.sprite(Phaser.Math.Between(200, 1400), Phaser.Math.Between(200, 1000), 'enemy').setScale(0.5);
            scene.physics.add.collider(knight, enemy, () => battleEnemy(enemy));
            enemies.push(enemy);
        }
    }

    function attack() {
        enemies.forEach((enemy, index) => {
            if (Phaser.Math.Distance.Between(knight.x, knight.y, enemy.x, enemy.y) < 50) {
                enemy.destroy();
                enemies.splice(index, 1);
                if (enemies.length === 0) {
                    nextLevel();
                }
            }
        });
    }

    function battleEnemy(enemy) {
        health -= 20;
        if (health <= 0) {
            alert("You died! Game Over.");
            location.reload();
        }
    }

    function battleBoss() {
        health -= 30;
        bossHP -= 50;
        if (bossHP <= 0) {
            alert("You saved the queen! Game Over.");
            location.reload();
        }
        if (health <= 0) {
            alert("You died! Game Over.");
            location.reload();
        }
    }

    function nextLevel() {
        currentLevel++;
        if (currentLevel > 3) {
            alert("You defeated all levels! Prepare for the boss!");
        } else {
            levelText.setText('Level: ' + currentLevel);
            spawnEnemies(this);
        }
    }
</script>
</body>
</html>

