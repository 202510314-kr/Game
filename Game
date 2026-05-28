<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>모두의 마블 - 주사위 & AI 대전 프로토타입</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <style>
        /* 1. 전체 레이아웃 세팅 */
        body {
            background-color: #f0f4f8;
            color: #2d3748;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            margin: 0;
        }

        h1 {
            margin: 10px 0;
            color: #2b6cb0;
            letter-spacing: 2px;
        }

        /* 자산 보드판 (플레이어 vs AI) */
        .scoreboard {
            display: flex;
            gap: 20px;
            margin-bottom: 15px;
        }
        .score-box {
            font-size: 1.1rem;
            font-weight: bold;
            padding: 8px 20px;
            border-radius: 20px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.05);
        }
        #p-box { color: #2f855a; background: #c6f6d5; }
        #ai-box { color: #9b2c2c; background: #fed7d7; }

        /* 2. 보드판 컨테이너 */
        .board {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            grid-template-rows: repeat(5, 1fr);
            width: 600px;
            height: 600px;
            gap: 6px;
            background-color: #cbd5e0;
            padding: 6px;
            border-radius: 16px;
            box-shadow: 0 12px 28px rgba(0,0,0,0.1);
        }

        /* 3. 각 칸(Tile) 스타일 */
        .tile {
            background-color: #ffffff;
            border-radius: 8px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            font-size: 0.9rem;
            font-weight: bold;
            color: #4a5568;
            border: 2px solid transparent;
            transition: all 0.2s;
            position: relative;
        }

        .tile-name { margin-bottom: 2px; }
        .build-status { font-size: 0.75rem; color: #718096; margin-top: 2px; }

        .corner { background-color: #edf2f7; color: #2b6cb0; font-size: 1rem; }
        .start { background-color: #ffeb3b; color: #000; }

        /* 4. 플레이어 말 (은준: 핫핑크, AI: 블루) */
        .token {
            width: 20px;
            height: 20px;
            border: 2px solid #ffffff;
            border-radius: 50%;
            position: absolute;
            bottom: 6px;
            z-index: 10;
            transition: all 0.25s ease-in-out;
        }
        .player-token {
            background-color: #ff007f;
            left: 10px;
            box-shadow: 0 0 8px rgba(255, 0, 127, 0.8);
        }
        .ai-token {
            background-color: #00bfff;
            right: 10px;
            box-shadow: 0 0 8px rgba(0, 191, 255, 0.8);
        }

        /* 5. 보드판 정중앙 빈 공간 */
        .center-space {
            grid-column: 2 / 5;
            grid-row: 2 / 5;
            background-color: #edf2f7;
            border-radius: 12px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 20px;
            box-shadow: inset 0 2px 8px rgba(0,0,0,0.05);
            position: relative;
            overflow: hidden;
        }

        .dice-btn {
            background-color: #3182ce;
            color: white;
            border: none;
            padding: 12px 30px;
            font-size: 1.1rem;
            font-weight: bold;
            border-radius: 8px;
            cursor: pointer;
            box-shadow: 0 4px 10px rgba(49, 130, 206, 0.3);
            transition: 0.2s;
        }

        .dice-btn:hover { background-color: #2b6cb0; transform: translateY(-2px); }
        .dice-btn:disabled { background-color: #a0aec0; cursor: not-allowed; transform: none; box-shadow: none; }

        #status-text {
            margin-top: 15px;
            font-size: 0.95rem;
            color: #4a5568;
            font-weight: 600;
            text-align: center;
            max-height: 80px;
            overflow-y: auto;
        }

        /* 7. CSS Grid 좌표 */
        .t0  { grid-area: 1 / 1; } .t1  { grid-area: 1 / 2; } .t2  { grid-area: 1 / 3; } .t3  { grid-area: 1 / 4; }
        .t4  { grid-area: 1 / 5; } .t5  { grid-area: 2 / 5; } .t6  { grid-area: 3 / 5; } .t7  { grid-area: 4 / 5; }
        .t8  { grid-area: 5 / 5; } .t9  { grid-area: 5 / 4; } .t10 { grid-area: 5 / 3; } .t11 { grid-area: 5 / 2; }
        .t12 { grid-area: 5 / 1; } .t13 { grid-area: 4 / 1; } .t14 { grid-area: 3 / 1; } .t15 { grid-area: 2 / 1; }
    </style>
</head>
<body>

    <h1>MARBLE PROTOTYPE</h1>

    <div class="scoreboard">
        <div id="p-box" class="score-box">은준 자산: ₩<span id="player-money">3,000,000</span></div>
        <div id="ai-box" class="score-box">AI 컴퓨터 자산: ₩<span id="ai-money">3,000,000</span></div>
    </div>

    <div class="board">
        <div class="tile corner start t0"><span class="tile-name">🛫 시작</span></div>
        <div class="tile corner t4"><span class="tile-name">🚀 무인도</span></div>
        <div class="tile corner t8"><span class="tile-name">💎 올림픽</span></div>
        <div class="tile corner t12"><span class="tile-name">✈️ 세계여행</span></div>

        <div class="tile t1"><span class="tile-name">서울</span><span class="build-status" id="b1">-</span></div>
        <div class="tile t2"><span class="tile-name">도쿄</span><span class="build-status" id="b2">-</span></div>
        <div class="tile t3"><span class="tile-name">시드니</span><span class="build-status" id="b3">-</span></div>
        
        <div class="tile t5"><span class="tile-name">파리</span><span class="build-status" id="b5">-</span></div>
        <div class="tile t6"><span class="tile-name">런던</span><span class="build-status" id="b6">-</span></div>
        <div class="tile t7"><span class="tile-name">로마</span><span class="build-status" id="b7">-</span></div>
        
        <div class="tile t9"><span class="tile-name">뉴욕</span><span class="build-status" id="b9">-</span></div>
        <div class="tile t10"><span class="tile-name">베이징</span><span class="build-status" id="b10">-</span></div>
        <div class="tile t11"><span class="tile-name">방콕</span><span class="build-status" id="b11">-</span></div>
        
        <div class="tile t13"><span class="tile-name">베를린</span><span class="build-status" id="b13">-</span></div>
        <div class="tile t14"><span class="tile-name">마드리드</span><span class="build-status" id="b14">-</span></div>
        <div class="tile t15"><span class="tile-name">카이로</span><span class="build-status" id="b15">-</span></div>

        <div class="center-space">
            <div id="dice-canvas-container" style="width: 250px; height: 160px;"></div>
            
            <button class="dice-btn" id="roll-btn" style="z-index: 5;">주사위 던지기</button>
            <div id="status-text" style="z-index: 5;">은준 님의 차례입니다. 주사위를 던지세요!</div>
        </div>
    </div>

<script>
    document.addEventListener('DOMContentLoaded', () => {
        const rollBtn = document.getElementById('roll-btn');
        const statusText = document.getElementById('status-text');
        const pMoneyDisplay = document.getElementById('player-money');
        const aiMoneyDisplay = document.getElementById('ai-money');

        // 게임 핵심 상태 변수
        let currentTurn = 'player'; // 'player' 또는 'ai'
        
        let gameState = {
            player: { money: 3000000, position: 0, islandTurns: 0, doubleCount: 0, color: '#ff007f', name: '은준', token: null },
            ai: { money: 3000000, position: 0, islandTurns: 0, doubleCount: 0, color: '#00bfff', name: 'AI 컴퓨터', token: null }
        };
        
        // 부동산 정보 세팅 (건물 단계별 가격 및 통행료)
        // 건물 단계: 0(대지), 1(빌라), 2(빌딩), 3(랜드마크)
        const tilesData = {
            0: { name: "시작점" }, 4: { name: "무인도" }, 8: { name: "올림픽" }, 12: { name: "세계여행" },
            1: { name: "서울", basePrice: 500000, owner: null, buildLevel: 0 },
            2: { name: "도쿄", basePrice: 300000, owner: null, buildLevel: 0 },
            3: { name: "시드니", basePrice: 250000, owner: null, buildLevel: 0 },
            5: { name: "파리", basePrice: 400000, owner: null, buildLevel: 0 },
            6: { name: "런던", basePrice: 350000, owner: null, buildLevel: 0 },
            7: { name: "로마", basePrice: 300000, owner: null, buildLevel: 0 },
            9: { name: "뉴욕", basePrice: 450000, owner: null, buildLevel: 0 },
            10: { name: "베이징", basePrice: 280000, owner: null, buildLevel: 0 },
            11: { name: "방콕", basePrice: 200000, owner: null, buildLevel: 0 },
            13: { name: "베를린", basePrice: 320000, owner: null, buildLevel: 0 },
            14: { name: "마드리드", basePrice: 220000, owner: null, buildLevel: 0 },
            15: { name: "카이로", basePrice: 180000, owner: null, buildLevel: 0 }
        };

        const levelNames = ["땅", "빌라", "빌딩", "랜드마크💥"];

        // 말 생성 및 배치
        gameState.player.token = document.createElement('div');
        gameState.player.token.className = 'token player-token';
        document.querySelector('.t0').appendChild(gameState.player.token);

        gameState.ai.token = document.createElement('div');
        gameState.ai.token.className = 'token ai-token';
        document.querySelector('.t0').appendChild(gameState.ai.token);

        // [3D 주사위 세팅]
        const container = document.getElementById('dice-canvas-container');
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(45, 250 / 160, 0.1, 100);
        camera.position.set(0, 5, 4); 
        camera.lookAt(0, 0, 0);
        
        const renderer = new THREE.WebGLRenderer({ alpha: true, antialias: true });
        renderer.setSize(250, 160);
        container.appendChild(renderer.domElement);
        
        const light = new THREE.DirectionalLight(0xffffff, 1.2); light.position.set(2, 5, 3); scene.add(light);
        scene.add(new THREE.AmbientLight(0xffffff, 0.6));

        // 3D 큐브 주사위 제작
        const diceGeo = new THREE.BoxGeometry(1.3, 1.3, 1.3);
        const diceMat = new THREE.MeshStandardMaterial({ color: 0x3182ce });
        const diceMesh = new THREE.Mesh(diceGeo, diceMat);
        scene.add(diceMesh);

        let isRolling3D = false; let rollVelocityY = 0; let rollRot = { x: 0, y: 0 };
        
        function animate3D() {
            requestAnimationFrame(animate3D);
            if (isRolling3D) {
                diceMesh.position.y += rollVelocityY; rollVelocityY -= 0.015; // 중력 낙하
                diceMesh.rotation.x += rollRot.x; diceMesh.rotation.y += rollRot.y; // 회전
                if (diceMesh.position.y < -0.3) { // 바닥 접지
                    diceMesh.position.y = -0.3;
                    if (Math.abs(rollVelocityY) > 0.03) rollVelocityY = -rollVelocityY * 0.4; // 튕김
                    else isRolling3D = false; // 정지
                }
            }
            renderer.render(scene, camera);
        }
        animate3D();

        // UI 정보 업데이트 함수
        function updateUI() {
            pMoneyDisplay.innerText = gameState.player.money.toLocaleString();
            aiMoneyDisplay.innerText = gameState.ai.money.toLocaleString();
        }

        // 주사위 버튼 이벤트 등록
        rollBtn.addEventListener('click', () => {
            if (currentTurn === 'player') {
                executeTurn('player');
            }
        });

        // 턴 통합 실행 엔진
        function executeTurn(turn) {
            rollBtn.disabled = true;
            const actor = gameState[turn];
            statusText.innerHTML = `🎲 ${actor.name}이(가) 주사위를 굴립니다!`;

            // 3D 주사위 점프 연출
            diceMesh.position.set(0, 1.5, 0); rollVelocityY = 0.14;
            rollRot.x = Math.random() * 0.5 + 0.2; rollRot.y = Math.random() * 0.5 + 0.2;
            // 주사위 색깔 변경 연출 (은준: 핫핑크, AI: 하늘색)
            diceMesh.material.color.setHex(turn === 'player' ? 0xff007f : 0x00bfff);
            isRolling3D = true;

            setTimeout(() => {
                const dice1 = Math.floor(Math.random() * 6) + 1;
                const dice2 = Math.floor(Math.random() * 6) + 1;
                const totalSteps = dice1 + dice2;
                const isDouble = (dice1 === dice2);

                // 무인도 탈출 판정
                if (actor.islandTurns > 0) {
                    if (isDouble) {
                        statusText.innerHTML = `🔓 <b>더블(${dice1}, ${dice2})! 무인도 탈출 성공!</b>`;
                        actor.islandTurns = 0;
                        actor.doubleCount = 0; // 탈출 더블은 보너스 없음
                    } else {
                        actor.islandTurns++;
                        statusText.innerHTML = `🔒 주사위: ${dice1}, ${dice2}. 탈출 실패! (${actor.name} 무인도 ${actor.islandTurns-1}/3턴째)`;
                        if (actor.islandTurns > 3) {
                            actor.islandTurns = 0; // 3턴 지나면 강제 석방
                            statusText.innerHTML += `<br>보석금을 내고 다음 턴에 탈출합니다.`;
                        }
                        setTimeout(switchTurn, 1500);
                        return;
                    }
                }

                // 일반 더블 연속 판정
                if (isDouble && actor.islandTurns === 0) {
                    actor.doubleCount++;
                    statusText.innerHTML = `🎲 결과: ${dice1}, ${dice2} (총 ${totalSteps}칸 전진)<br>✨ <b>더블! 주사위를 한번 더 던집니다! (연속 ${actor.doubleCount}회)</b>`;
                    if (actor.doubleCount === 3) {
                        statusText.innerHTML = `🚨 <b>3연속 더블! 과속으로 무인도 연행!</b>`;
                        actor.doubleCount = 0;
                        actor.islandTurns = 1;
                        actor.position = 4;
                        document.querySelector('.t4').appendChild(actor.token);
                        setTimeout(switchTurn, 1500);
                        return;
                    }
                } else {
                    actor.doubleCount = 0;
                    statusText.innerHTML = `🎲 주사위 결과: ${dice1}, ${dice2} (총 ${totalSteps}칸 전진!)`;
                }

                movePlayerStepByStep(turn, totalSteps, isDouble);
            }, 1200);
        }

        // 말 한 칸씩 이동시키는 애니메이션 함수
        function movePlayerStepByStep(turn, steps, isDouble) {
            let currentStep = 0;
            const actor = gameState[turn];
            
            const moveInterval = setInterval(() => {
                if (currentStep >= steps) {
                    clearInterval(moveInterval);
                    handleProperty(turn, isDouble);
                    return;
                }
                actor.position = (actor.position + 1) % 16;
                
                // 출발지(0번 칸) 월급 지급
                if (actor.position === 0) {
                    actor.money += 300000;
                    updateUI();
                    statusText.innerHTML += `<br>💰 <b>월급 30만 원 보너스 지급!</b>`;
                }

                document.querySelector(`.t${actor.position}`).appendChild(actor.token);
                currentStep++;
            }, 300);
        }

        // 부동산 거래 / 통행료 지불 연산 핵심 시스템 (1, 2, 3번 기능 집약)
        function handleProperty(turn, isDouble) {
            const actor = gameState[turn];
            const opponentTurn = (turn === 'player') ? 'ai' : 'player';
            const opponent = gameState[opponentTurn];
            
            const pos = actor.position;
            const tile = tilesData[pos];
            const targetDOM = document.querySelector(`.t${pos}`);

            // 1. 특수칸 체크 (무인도)
            if (pos === 4 && actor.islandTurns === 0) {
                statusText.innerHTML += `<br>🚨 무인도에 불시착했습니다! (3턴간 고립됩니다)`;
                actor.islandTurns = 1;
                setTimeout(switchTurn, 1500);
                return;
            }
            
            // 특수 관광지 코너칸 패스
            if (!tile.basePrice) {
                setTimeout(() => {
                    if (isDouble && actor.doubleCount > 0) {
                        if (turn === 'player') rollBtn.disabled = false;
                        else setTimeout(() => executeTurn('ai'), 1500);
                    } else {
                        switchTurn();
                    }
                }, 1000);
                return;
            }

            // 가격 및 통행료 자동 연산 공식
            // 가격 = 기본가 + (기본가 * 레벨 * 0.5)
            // 통행료 = 기본가의 40% + (레벨 * 기본가의 50%)
            let currentCost = tile.basePrice * (1 + tile.buildLevel * 0.5);
            let toll = Math.floor(tile.basePrice * 0.4 + (tile.buildLevel * tile.basePrice * 0.5));

            // 상황 2. 타인의 땅 밟음 -> [통행료 지불 시스템 적용] 💸
            if (tile.owner && tile.owner !== turn) {
                statusText.innerHTML += `<br>💸 ${opponent.name}의 땅에 걸렸습니다! 통행료 <b>₩${toll.toLocaleString()}</b> 지불!`;
                
                actor.money -= toll;
                opponent.money += toll;
                updateUI();

                // 파산 체크 기본 알림
                if (actor.money < 0) {
                    statusText.innerHTML += `<br>☠️ <b>${actor.name} 파산! 게임이 종료되었습니다.</b>`;
                    return;
                }
                
                proceedAfterTurn(turn, isDouble);
            } 
            // 상황 3. 내 땅 재방문 -> [건물 건설 및 업그레이드 시스템 적용] 🏨
            else if (tile.owner && tile.owner === turn) {
                if (tile.buildLevel < 3) {
                    let upgradeCost = tile.basePrice * 0.5; // 업그레이드 비용은 기본값의 50%
                    
                    if (turn === 'player') {
                        setTimeout(() => {
                            let buy = confirm(`${tile.name}에 재도착했습니다!\n건물을 한 단계 더 올리시겠습니까?\n(건축비: ₩${upgradeCost.toLocaleString()})`);
                            if (buy && actor.money >= upgradeCost) {
                                actor.money -= upgradeCost;
                                tile.buildLevel++;
                                document.getElementById(`b${pos}`).innerText = levelNames[tile.buildLevel];
                                statusText.innerHTML += `<br>🏨 건물 업그레이드 완료! 현재: <b>${levelNames[tile.buildLevel]}</b>`;
                                updateUI();
                            }
                            proceedAfterTurn(turn, isDouble);
                        }, 200);
                    } else {
                        // AI는 돈이 기본값 이상 있으면 무조건 업그레이드
                        if (actor.money >= upgradeCost) {
                            actor.money -= upgradeCost;
                            tile.buildLevel++;
                            document.getElementById(`b${pos}`).innerText = levelNames[tile.buildLevel];
                            statusText.innerHTML += `<br>🤖 AI가 ${tile.name}에 건물을 세워 <b>${levelNames[tile.buildLevel]}</b>(으)로 업그레이드했습니다!`;
                            updateUI();
                        }
                        proceedAfterTurn(turn, isDouble);
                    }
                } else {
                    statusText.innerHTML += `<br>🏰 이미 최고 단계인 랜드마크가 건설되어 있습니다!`;
                    proceedAfterTurn(turn, isDouble);
                }
            } 
            // 상황 4. 주인 없는 빈 땅 도착 -> [부동산 최초 구매]
            else {
                if (turn === 'player') {
                    setTimeout(() => {
                        if (actor.money >= tile.basePrice) {
                            let buy = confirm(`${tile.name}을(를) 매입하시겠습니까?\n(가격: ₩${tile.basePrice.toLocaleString()})`);
                            if (buy) {
                                actor.money -= tile.basePrice;
                                tile.owner = 'player';
                                tile.buildLevel = 0;
                                targetDOM.style.backgroundColor = '#ffd1dc'; // 은준 소유: 연한 핑크
                                document.getElementById(`b${pos}`).innerText = "땅";
                                statusText.innerHTML += `<br>🏠 <b>${tile.name}</b> 구매 성공!`;
                                updateUI();
                            }
                        } else {
                            statusText.innerHTML += `<br>💸 자산이 부족하여 땅을 살 수 없습니다.`;
                        }
                        proceedAfterTurn(turn, isDouble);
                    }, 200);
                } else {
                    // AI의 자동 매입 알고리즘
                    setTimeout(() => {
                        if (actor.money >= tile.basePrice) {
                            actor.money -= tile.basePrice;
                            tile.owner = 'ai';
                            tile.buildLevel = 0;
                            targetDOM.style.backgroundColor = '#e0f7fa'; // AI 소유: 연한 하늘색
                            document.getElementById(`b${pos}`).innerText = "땅";
                            statusText.innerHTML += `<br>🤖 AI 컴퓨터가 <b>${tile.name}</b>을(를) 매입했습니다.`;
                            updateUI();
                        }
                        proceedAfterTurn(turn, isDouble);
                    }, 500);
                }
            }
        }

        // 거래 후 다음 행동 제어
        function proceedAfterTurn(turn, isDouble) {
            setTimeout(() => {
                if (isDouble && gameState[turn].doubleCount > 0) {
                    if (turn === 'player') {
                        rollBtn.disabled = false;
                    } else {
                        executeTurn('ai');
                    }
                } else {
                    switchTurn();
                }
            }, 1200);
        }

        // 턴 교체 매니저 (AI 차례 자동 유도)
        function switchTurn() {
            if (currentTurn === 'player') {
                currentTurn = 'ai';
                rollBtn.disabled = true;
                statusText.innerHTML = `🤖 AI 컴퓨터의 턴입니다... (생각 중)`;
                setTimeout(() => {
                    executeTurn('ai');
                }, 1500);
            } else {
                currentTurn = 'player';
                rollBtn.disabled = false;
                statusText.innerHTML = `👋 은준 님의 차례입니다. 주사위를 던지세요!`;
            }
        }
    });
</script>
</body>
</html>
