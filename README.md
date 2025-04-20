<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>한글 자음·모음 카드 언어치료 게임 (위치 수정+결과 표시)</title>
  <style>
    /* ... (이전 CSS 코드 유지) ... */

    /* 음절 박스 크기 조정 */
    .syllable-box {
      width: 140px;
      height: 140px;
      position: relative;
      display: inline-block;
      margin: 0 4px;
      background: #f8f9fa;
      border-radius: 12px;
    }

    /* 드롭존 위치 재조정 (실제 한글 조합 위치) */
    .dropzone.cho { left: 10%; top: 10%; }
    .dropzone.jung { left: 70%; top: 10%; }
    .dropzone.jong { left: 40%; top: 70%; }

    /* 결과 표시 영역 */
    .result-box {
      position: absolute;
      left: 0; right: 0; bottom: -40px;
      text-align: center;
      font-size: 3em;
      color: #2e7d32;
      font-weight: bold;
      height: 60px;
    }
  </style>
</head>
<body>
  <!-- ... (이전 HTML 구조 유지) ... -->
  <script>
    // ... (이전 JS 코드 유지) ...

    function createDropzone(answer, type) {
      const dz = document.createElement('div');
      dz.className = 'dropzone ' + type;
      // 힌트(회색) 추가
      const hint = document.createElement('span');
      hint.className = 'hint';
      hint.textContent = answer;
      dz.appendChild(hint);

      // 결과 표시용 div 추가
      const resultDiv = document.createElement('div');
      resultDiv.className = 'result-box';
      dz.parentNode?.appendChild(resultDiv);

      dz.ondragover = e => e.preventDefault();
      dz.ondrop = function(e) {
        e.preventDefault();
        const dropped = e.dataTransfer.getData('text');
        if (dz.classList.contains('filled')) return;

        if (dropped === answer) {
          dz.textContent = dropped;
          dz.classList.add('correct', 'filled');
          dz.classList.remove('wrong');
          
          // 현재 음절의 모든 드롭존 체크
          const syllableBox = dz.closest('.syllable-box');
          const filled = [...syllableBox.querySelectorAll('.filled')];
          if (filled.length === 3 || (filled.length === 2 && !syllableBox.querySelector('.jong'))) {
            const cho = syllableBox.querySelector('.cho').textContent;
            const jung = syllableBox.querySelector('.jung').textContent;
            const jong = syllableBox.querySelector('.jong')?.textContent || '';
            const combined = combineHangul(cho, jung, jong);
            resultDiv.textContent = combined;
          }

          showStar(e.clientX, e.clientY);
          playSuccessSound();
          showPraise();
          checkAllCorrect();
        } else {
          dz.classList.add('wrong');
          showMessage('틀렸어요! 다시 시도해보세요.');
          setTimeout(() => dz.classList.remove('wrong'), 600);
        }
      };
      return dz;
    }

    // 한글 조합 함수 (초성, 중성, 종성 -> 완성형)
    function combineHangul(cho, jung, jong = '') {
      const CHO = ["ㄱ","ㄲ","ㄴ","ㄷ","ㄸ","ㄹ","ㅁ","ㅂ","ㅃ","ㅅ","ㅆ","ㅇ","ㅈ","ㅉ","ㅊ","ㅋ","ㅌ","ㅍ","ㅎ"];
      const JUNG = ["ㅏ","ㅐ","ㅑ","ㅒ","ㅓ","ㅔ","ㅕ","ㅖ","ㅗ","ㅘ","ㅙ","ㅚ","ㅛ","ㅜ","ㅝ","ㅞ","ㅟ","ㅠ","ㅡ","ㅢ","ㅣ"];
      const JONG = ["","ㄱ","ㄲ","ㄳ","ㄴ","ㄵ","ㄶ","ㄷ","ㄹ","ㄺ","ㄻ","ㄼ","ㄽ","ㄾ","ㄿ","ㅀ","ㅁ","ㅂ","ㅄ","ㅅ","ㅆ","ㅇ","ㅈ","ㅊ","ㅋ","ㅌ","ㅍ","ㅎ"];
      
      const choIdx = CHO.indexOf(cho);
      const jungIdx = JUNG.indexOf(jung);
      const jongIdx = JONG.indexOf(jong);
      
      if (choIdx === -1 || jungIdx === -1 || jongIdx === -1) return '';
      
      const code = 44032 + (choIdx * 588) + (jungIdx * 28) + jongIdx;
      return String.fromCharCode(code);
    }

    // ... (나머지 JS 코드 유지) ...
  </script>
</body>
</html>
