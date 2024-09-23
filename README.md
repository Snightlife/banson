<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>볼링 계산기</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            text-align: center;
            padding: 50px;
        }
        h1 {
            color: #333;
        }
        label {
            font-size: 18px;
            margin-bottom: 10px;
            display: block;
        }
        input[type="number"] {
            padding: 10px;
            font-size: 16px;
            width: 150px;
            margin-top: 20px;
            margin-bottom: 20px;
        }
        button {
            padding: 10px 20px;
            font-size: 16px;
            background-color: #007BFF;
            color: white;
            border: none;
            cursor: pointer;
        }
        button:hover {
            background-color: #0056b3;
        }
        .team-scores {
            margin-top: 20px;
        }
        .team-scores input {
            margin-bottom: 10px;
        }
        #teamSizeSection, #scoreSection {
            display: none;
        }
        .team-row {
            display: flex;
            justify-content: center;
            align-items: center;
            margin-bottom: 15px;
        }
        .team-row label {
            margin-right: 10px;
        }
        .crown {
            color: gold;
            font-size: 24px;
            margin-left: 10px;
        }
    </style>
</head>
<body>

    <h1>볼링 계산기</h1>

    <!-- 팀 수 선택 폼 -->
    <form id="teamForm">
        <label for="teamCount">몇 팀까지 있나요?</label>
        <input type="number" id="teamCount" name="teamCount" min="1" max="5" placeholder="팀 수 입력">
        <br>
        <button type="submit">다음</button>
    </form>

    <!-- 팀별 인원수 입력 섹션 -->
    <div id="teamSizeSection">
        <h2>팀별 인원수 입력</h2>
        <div id="teamSizeInputs"></div>
        <button id="submitTeamSizes">다음</button>
    </div>

    <!-- 팀별 점수 입력 섹션 -->
    <div id="scoreSection" class="team-scores">
        <h2>팀별 점수 입력</h2>
        <div id="scoreInputs"></div>
        <button id="submitScores">점수 제출</button>
        <h3>입력한 팀별 점수 결과 (평균):</h3>
        <div id="totalScores"></div>
    </div>

    <script>
        let teamScores = []; // 팀별 총합 점수를 저장하는 배열
        let teamSizes = []; // 팀별 인원수를 저장하는 배열
        let currentRound = 1; // 현재 라운드 (최대 3번)
        let teamCount = 0;

        // 팀 수 선택 후 처리
        document.getElementById('teamForm').addEventListener('submit', function(event) {
            event.preventDefault();
            teamCount = document.getElementById('teamCount').value;
            if (teamCount && teamCount > 0 && teamCount <= 5) {
                // 팀 수를 입력했을 때 팀별 인원수 입력 페이지로 전환
                document.getElementById('teamForm').style.display = 'none';
                document.getElementById('teamSizeSection').style.display = 'block';

                // 팀별 인원수 입력 필드 생성
                const teamSizeInputs = document.getElementById('teamSizeInputs');
                teamSizeInputs.innerHTML = ''; // 기존 입력 필드 초기화

                for (let i = 1; i <= teamCount; i++) {
                    const teamRow = document.createElement('div');
                    teamRow.className = 'team-row';

                    const sizeLabel = document.createElement('label');
                    sizeLabel.textContent = `팀 ${i} 인원수:`;

                    const sizeInput = document.createElement('input');
                    sizeInput.type = 'number';
                    sizeInput.name = `team${i}Size`;
                    sizeInput.placeholder = `팀 ${i} 인원수`;
                    sizeInput.min = 1; // 인원수는 최소 1명 이상이어야 함

                    teamRow.appendChild(sizeLabel);
                    teamRow.appendChild(sizeInput);

                    teamSizeInputs.appendChild(teamRow);
                }
            } else {
                alert("팀 수는 1에서 5 사이로 입력해주세요.");
            }
        });

        // 팀별 인원수 제출 후 점수 입력 페이지로 전환
        document.getElementById('submitTeamSizes').addEventListener('click', function() {
            // 팀별 인원수를 저장
            for (let i = 1; i <= teamCount; i++) {
                const sizeInput = document.querySelector(`input[name=team${i}Size]`).value;
                if (sizeInput && sizeInput > 0) {
                    teamSizes[i - 1] = parseInt(sizeInput); // 인원수 저장
                } else {
                    alert(`팀 ${i}의 인원수를 올바르게 입력해주세요.`);
                    return;
                }
            }

            // 인원수 입력 완료 후 점수 입력 페이지로 전환
            document.getElementById('teamSizeSection').style.display = 'none';
            document.getElementById('scoreSection').style.display = 'block';

            // 팀 점수 입력 필드 생성
            const scoreInputs = document.getElementById('scoreInputs');
            scoreInputs.innerHTML = ''; // 기존 입력 필드 초기화
            teamScores = Array(parseInt(teamCount)).fill(0); // 팀별 합산 점수 초기화

            for (let i = 1; i <= teamCount; i++) {
                const teamRow = document.createElement('div');
                teamRow.className = 'team-row';

                const scoreLabel = document.createElement('label');
                scoreLabel.textContent = `팀 ${i} 점수 입력:`;

                const scoreInput = document.createElement('input');
                scoreInput.type = 'number';
                scoreInput.name = `team${i}Score`;
                scoreInput.placeholder = `팀 ${i} 점수`;

                teamRow.appendChild(scoreLabel);
                teamRow.appendChild(scoreInput);

                scoreInputs.appendChild(teamRow);
            }
        });

        // 점수 제출 버튼 처리
        document.getElementById('submitScores').addEventListener('click', function() {
            if (currentRound <= 3) {
                let totalScoresDiv = document.getElementById('totalScores');
                totalScoresDiv.innerHTML = ''; // 총합 결과 초기화

                // 각 팀의 점수를 읽어서 합산
                for (let i = 1; i <= teamCount; i++) {
                    const scoreInput = document.querySelector(`input[name=team${i}Score]`).value;

                    if (scoreInput) {
                        teamScores[i - 1] += parseInt(scoreInput); // 해당 팀 점수 누적
                    } else {
                        alert(`팀 ${i}의 점수를 입력해주세요.`);
                        return;
                    }
                }

                // 팀별 평균 점수(총점/인원수) 배열 생성
                let teamResults = [];
                for (let i = 1; i <= teamCount; i++) {
                    const avgScore = teamScores[i - 1] / teamSizes[i - 1]; // 총점 / 인원수 계산
                    teamResults.push({ team: i, total: teamScores[i - 1], avg: avgScore.toFixed(2) });
                }

                // 평균 점수가 높은 순으로 정렬
                teamResults.sort((a, b) => b.avg - a.avg);

                // 팀별 결과 표시 (1등 팀에 왕관 표시)
                teamResults.forEach((result, index) => {
                    const scoreResult = document.createElement('p');
                    scoreResult.textContent = `팀 ${result.team}: 총점 ${result.total}점, 평균 점수: ${result.avg}점`;

                    if (index === 0) {
                        // 1등 팀에 왕관 표시
                        const crown = document.createElement('span');
                        crown.innerHTML = '👑';
                        crown.classList.add('crown');
                        scoreResult.appendChild(crown);
                    }

                    totalScoresDiv.appendChild(scoreResult);
                });

                currentRound++; // 라운드 증가

                if (currentRound > 3) {
                    alert("3번의 점수 입력이 완료되었습니다.");
                    document.getElementById('submitScores').disabled = true; // 제출 버튼 비활성화
                } else {
                    alert(`현재 ${currentRound - 1}번째 점수 입력이 완료되었습니다. ${currentRound}번째 점수를 입력해주세요.`);
                }
            }
        });
    </script>

</body>
</html>
