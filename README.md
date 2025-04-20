<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>한글 자음·모음 카드 언어치료 게임 (최적화+정답만 카드)</title>
  <style>
    body {
      font-family: 'Malgun Gothic', '맑은 고딕', sans-serif;
      background: #f6f8fc;
      margin: 0; padding: 0;
    }
    .container {
      display: flex;
      max-width: 1000px;
      margin: 40px auto;
      background: #fff;
      border-radius: 18px;
      box-shadow: 0 4px 24px rgba(0,0,0,0.08);
      min-height: 600px;
      padding: 30px;
    }
    .cards {
      width: 180px;
      background: #e8eaf6;
      border-radius: 12px;
      padding: 18px 8px;
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
      align-content: flex-start;
      margin-right: 32px;
      min-width: 180px;
      min-height: 420px;
      justify-content: center;
    }
    .card {
      width: 60px; height: 60px;
      background: #fff;
      border: 2px solid #3949ab;
      border-radius: 10px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 2.5em;
      font-weight: bold;
      color: #3949ab;
      cursor: grab;
      user-select: none;
      transition: transform 0.1s;
    }
    .card:active {
      transform: scale(1.08);
    }
    .game-area {
      flex: 1;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-start;
    }
    .word-area {
      margin-top: 40px;
      margin-bottom: 30px;
      display: flex;
      align-items: center;
      gap: 18px;
      flex-wrap: wrap;
      justify-content: center;
    }
    .syllable-box {
      width: 120px;
      height: 120px;
      position: relative;
      display: inline-block;
      margin: 0 4px;
    }
    .dropzone {
      position: absolute;
      background: #f0f4ff;
      border: 2px dashed #b0bec5;
      border-radius: 8px;
      width: 48px; height: 48px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 2em;
      transition: border-color 0.2s, background 0.2s;
      z-index: 2;
      box-sizing: border-box;
    }
    .dropzone.cho { left: 3px; top: 6px;}
    .dropzone.jung { left: 65px; top: 6px;}
    .dropzone.jong { left: 35px; top: 68px;}
    .dropzone.correct {
      border-color: #43a047;
      background: #e8f5e9;
      animation: correctPop 0.4s;
    }
    @keyframes correctPop {
      0% { transform: scale(1);}
      60% { transform: scale(1.18);}
      100% { transform: scale(1);}
    }
    .dropzone.wrong {
      border-color: #e53935;
      background: #ffebee;
      animation: shake 0.4s;
    }
    @keyframes shake {
      0% { transform: translateX(0);}
      20% { transform: translateX(-8px);}
      40% { transform: translateX(8px);}
      60% { transform: translateX(-4px);}
      80% { transform: translateX(4px);}
      100% { transform: translateX(0);}
    }
    .hint {
      color: #bbb;
      font-weight: normal;
      pointer-events: none;
      user-select: none;
      position: absolute;
      left: 0; right: 0; top: 0; bottom: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      width: 100%; height: 100%;
      font-size: 1em;
      z-index: 0;
    }
    .dropzone.filled .hint {
      display: none;
    }
    #message {
      min-height: 32px;
      font-size: 1.3em;
      font-weight: bold;
      color: #3949ab;
      margin-bottom: 10px;
      margin-top: 15px;
      text-align: center;
      height: 36px;
    }
    #score {
      font-size: 1.1em;
      color: #666;
      margin-bottom: 10px;
      text-align: center;
    }
    .star {
      position: absolute;
      color: gold;
      font-size: 2.2em;
      pointer-events: none;
      animation: pop 0.7s ease;
      z-index: 100;
    }
    @keyframes pop {
      0% { transform: scale(0.5) translateY(0);}
      70% { transform: scale(1.3) translateY(-20px);}
      100% { opacity: 0; transform: scale(0.8) translateY(-40px);}
    }
    .next-btn {
      margin-top: 18px;
      padding: 9px 28px;
      background: #3949ab;
      color: #fff;
      border: none;
      border-radius: 8px;
      font-size: 1.2em;
      cursor: pointer;
      font-weight: bold;
      transition: background 0.2s;
    }
    .next-btn:active {
      background: #283593;
    }
    @media (max-width: 700px) {
      .container {flex-direction: column; padding: 10px;}
      .cards {flex-direction: row; min-width: unset; min-height: unset; margin: 0 auto 15px auto;}
      .game-area {align-items: stretch;}
      .word-area {justify-content: center;}
      .syllable-box {width: 70px; height: 70px;}
      .dropzone {width: 28px; height: 28px; font-size: 1.1em;}
      .dropzone.cho { left: 2px; top: 2px;}
      .dropzone.jung { left: 36px; top: 2px;}
      .dropzone.jong { left: 18px; top: 40px;}
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="cards" id="cards"></div>
    <div class="game-area">
      <div id="score"></div>
      <div id="message"></div>
      <div class="word-area" id="word-area"></div>
      <button class="next-btn" id="next-btn" style="display:none;">다음 단어</button>
    </div>
  </div>
  <!-- 성공 사운드 -->
  <audio id="successSound" src="https://cdn.pixabay.com/audio/2022/03/15/audio_115b9f6e2c.mp3"></audio>
  <script>
    // 자음/모음 리스트
    const consonants = ['ㄱ','ㄴ','ㄷ','ㄹ','ㅁ','ㅂ','ㅅ','ㅇ','ㅈ','ㅊ','ㅋ','ㅌ','ㅍ','ㅎ'];
    const vowels = ['ㅏ','ㅑ','ㅓ','ㅕ','ㅗ','ㅛ','ㅜ','ㅠ','ㅡ','ㅣ'];
    // 과일 단어 리스트
    const fruits = ['사과','배','복숭아','포도','귤','바나나','키위','수박','참외','딸기','오렌지','자두','레몬','망고','파인애플','토마토','체리','블루베리','멜론','감'];

    let quizWords = [];
    let currentIndex = 0;
    let score = 0;

    function shuffle(array) {
      return array.sort(() => Math.random() - 0.5);
    }

    // 한글 음절 분해 함수
    function decomposeHangul(syllable) {
      const CHO = ["ㄱ","ㄲ","ㄴ","ㄷ","ㄸ","ㄹ","ㅁ","ㅂ","ㅃ","ㅅ","ㅆ","ㅇ","ㅈ","ㅉ","ㅊ","ㅋ","ㅌ","ㅍ","ㅎ"];
      const JUNG = ["ㅏ","ㅐ","ㅑ","ㅒ","ㅓ","ㅔ","ㅕ","ㅖ","ㅗ","ㅘ","ㅙ","ㅚ","ㅛ","ㅜ","ㅝ","ㅞ","ㅟ","ㅠ","ㅡ","ㅢ","ㅣ"];
      const JONG = ["","ㄱ","ㄲ","ㄳ","ㄴ","ㄵ","ㄶ","ㄷ","ㄹ","ㄺ","ㄻ","ㄼ","ㄽ","ㄾ","ㄿ","ㅀ","ㅁ","ㅂ","ㅄ","ㅅ","ㅆ","ㅇ","ㅈ","ㅊ","ㅋ","ㅌ","ㅍ","ㅎ"];
      const code = syllable.charCodeAt(0) - 0xAC00;
      if (code < 0 || code > 11171) return [syllable, '', '']; // 한글이 아니면
      const cho = CHO[Math.floor(code/588)];
      const jung = JUNG[Math.floor((code%588)/28)];
      const jong = JONG[code%28];
      return [cho, jung, jong];
    }

    // 카드 렌더링 (정답+랜덤5개)
    function renderCardsForCurrentWord() {
      const cardsDiv = document.getElementById('cards');
      cardsDiv.innerHTML = '';
      let word = quizWords[currentIndex];

      // 1. 정답에 필요한 자음/모음 추출
      let answerSet = new Set();
      for (let i = 0; i < word.length; i++) {
        const [cho, jung, jong] = decomposeHangul(word[i]);
        answerSet.add(cho);
        answerSet.add(jung);
        if (jong) answerSet.add(jong);
      }
      // 2. 오답 후보 목록 만들기
      const allChars = [...consonants, ...vowels];
      let wrongCandidates = allChars.filter(x => !answerSet.has(x));
      let wrongs = shuffle(wrongCandidates).slice(0, 5);

      // 3. 카드 배열 만들기 (정답+오답)
      let cardArr = shuffle([...Array.from(answerSet), ...wrongs]);

      // 4. 카드 렌더링
      cardArr.forEach(char => {
        const card = document.createElement('div');
        card.className = 'card';
        card.textContent = char;
        card.draggable = true;
        card.ondragstart = e => { e.dataTransfer.setData('text', char); };
        cardsDiv.appendChild(card);
      });
    }

    // 단어 문제 렌더링
    let dropzones = [];
    let answerArr = [];
    let filledArr = [];

    function renderWord() {
      const wordArea = document.getElementById('word-area');
      wordArea.innerHTML = '';
      dropzones = [];
      answerArr = [];
      filledArr = [];
      let word = quizWords[currentIndex];
      for (let i = 0; i < word.length; i++) {
        const [cho, jung, jong] = decomposeHangul(word[i]);
        answerArr.push([cho, jung, jong]);
        // 한 글자(음절)마다 박스 생성
        let box = document.createElement('div');
        box.className = 'syllable-box';
        // 초성(왼쪽 위)
        let dzCho = createDropzone(cho, 'cho');
        box.appendChild(dzCho);
        dropzones.push(dzCho);
        // 중성(오른쪽 위)
        let dzJung = createDropzone(jung, 'jung');
        box.appendChild(dzJung);
        dropzones.push(dzJung);
        // 종성(아래)
        if (jong) {
          let dzJong = createDropzone(jong, 'jong');
          box.appendChild(dzJong);
          dropzones.push(dzJong);
        }
        wordArea.appendChild(box);
      }
      filledArr = Array(dropzones.length).fill(false);
      updateScore();
      document.getElementById('next-btn').style.display = 'none';
      renderCardsForCurrentWord();
    }

    function createDropzone(answer, type) {
      const dz = document.createElement('div');
      dz.className = 'dropzone ' + type;
      // 힌트(회색) 추가
      const hint = document.createElement('span');
      hint.className = 'hint';
      hint.textContent = answer;
      dz.appendChild(hint);

      dz.ondragover = e => e.preventDefault();
      dz.ondrop = function(e) {
        e.preventDefault();
        const dropped = e.dataTransfer.getData('text');
        if (dz.classList.contains('filled')) return; // 이미 채워진 칸은 무시
        if (dropped === answer) {
          dz.textContent = dropped;
          dz.classList.add('correct', 'filled');
          dz.classList.remove('wrong');
          dz.ondrop = null;
          showStar(e.clientX, e.clientY);
          playSuccessSound();
          showPraise();
          // 정답 체크
          let idx = dropzones.indexOf(dz);
          filledArr[idx] = true;
          checkAllCorrect();
        } else {
          dz.classList.add('wrong');
          showMessage('틀렸어요! 다시 시도해보세요.');
          setTimeout(() => dz.classList.remove('wrong'), 600);
        }
      };
      return dz;
    }

    // 별 애니메이션
    function showStar(x, y) {
      const star = document.createElement('span');
      star.className = 'star';
      star.textContent = '★';
      star.style.left = (x-20) + 'px';
      star.style.top = (y-20) + 'px';
      document.body.appendChild(star);
      setTimeout(() => document.body.removeChild(star), 700);
    }
    // 사운드
    function playSuccessSound() {
      const audio = document.getElementById('successSound');
      audio.currentTime = 0;
      audio.play();
    }
    // 칭찬 메시지
    function showPraise() {
      const messages = ['잘했어요!', '정답!', '멋져요!', '최고예요!'];
      showMessage(messages[Math.floor(Math.random()*messages.length)]);
    }
    // 메시지
    let msgTimeout;
    function showMessage(msg) {
      clearTimeout(msgTimeout);
      const msgDiv = document.getElementById('message');
      msgDiv.textContent = msg;
      msgTimeout = setTimeout(() => msgDiv.textContent = '', 1200);
    }

    // 점수판
    function updateScore() {
      document.getElementById('score').textContent = `문제 ${currentIndex+1} / ${quizWords.length}   |   점수: ${score}`;
    }

    // 모두 맞췄는지 체크
    function checkAllCorrect() {
      if (filledArr.every(f => f)) {
        score++;
        showMessage('PASS! 다음 단어로 넘어갑니다.');
        document.getElementById('next-btn').style.display = 'inline-block';
        if (currentIndex === quizWords.length-1) {
          document.getElementById('next-btn').textContent = '게임 종료';
        } else {
          document.getElementById('next-btn').textContent = '다음 단어';
        }
      }
    }

    // 다음 문제
    function nextWord() {
      if (currentIndex < quizWords.length-1) {
        currentIndex++;
        renderWord();
      } else {
        finishGame();
      }
    }

    // 게임 종료
    function finishGame() {
      document.getElementById('word-area').innerHTML = '';
      document.getElementById('cards').innerHTML = '';
      document.getElementById('message').innerHTML = `<span style="color:#43a047;">모든 단어를 완료했습니다!<br>최종 점수: ${score} / ${quizWords.length}</span>`;
      document.getElementById('next-btn').style.display = 'none';
      document.getElementById('score').textContent = '';
    }

    // 초기화
    function startGame() {
      quizWords = shuffle(fruits).slice(0, 10);
      currentIndex = 0;
      score = 0;
      renderWord();
    }

    document.getElementById('next-btn').onclick = nextWord;

    // 시작!
    startGame();
  </script>
</body>
</html>
