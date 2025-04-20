<!-- 별 애니메이션용 CSS -->
<style>
  .star {
    position: absolute; color: gold; font-size: 2em;
    animation: pop 0.7s ease;
    pointer-events: none;
  }
  @keyframes pop {
    0% { transform: scale(0.5) translateY(0);}
    70% { transform: scale(1.3) translateY(-20px);}
    100% { opacity: 0; transform: scale(0.8) translateY(-40px);}
  }
  .dropzone.correct { border-color: green; background: #d0ffd0;}
</style>
<!-- 성공 사운드 -->
<audio id="successSound" src="https://cdn.pixabay.com/audio/2022/03/15/audio_115b9f6e2c.mp3"></audio>
<div id="message"></div>
<!-- ... (위에서 제공한 게임 코드의 drop 이벤트에 아래 코드 추가) ... -->

<script>
function showStar(x, y) {
  const star = document.createElement('span');
  star.className = 'star';
  star.textContent = '★';
  star.style.left = x + 'px';
  star.style.top = y + 'px';
  document.body.appendChild(star);
  setTimeout(() => document.body.removeChild(star), 700);
}

function playSuccessSound() {
  document.getElementById('successSound').play();
}

function showPraise() {
  const messages = ['잘했어요!', '정답!', '멋져요!', '최고예요!'];
  const msg = messages[Math.floor(Math.random() * messages.length)];
  document.getElementById('message').textContent = msg;
  setTimeout(() => document.getElementById('message').textContent = '', 1000);
}

// dropzone.ondrop 이벤트에서 정답일 때 아래 함수들 호출
dz.ondrop = function(e) {
  // ... (정답 체크 코드)
  if (정답) {
    dz.textContent = dropped;
    dz.classList.add('correct');
    // 보상 효과
    showStar(e.clientX, e.clientY);
    playSuccessSound();
    showPraise();
    // ...
  }
  // ...
}
</script>
