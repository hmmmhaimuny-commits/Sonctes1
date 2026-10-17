<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🔒 Nhập mật khẩu để tiếp tục</title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
    background: linear-gradient(135deg, #ff758c 0%, #ff7eb3 40%, #a18cd1 100%);
    overflow: hidden;
    padding: 20px;
  }

  /* Trái tim bay lơ lửng phía sau */
  .heart-bg {
    position: fixed;
    color: rgba(255, 255, 255, .3);
    font-size: 22px;
    animation: floatUp 12s linear infinite;
    pointer-events: none;
    user-select: none;
  }
  @keyframes floatUp {
    0%   { transform: translateY(105vh) scale(.4) rotate(0deg); opacity: 0; }
    10%  { opacity: 1; }
    90%  { opacity: 1; }
    100% { transform: translateY(-10vh) scale(1.1) rotate(25deg); opacity: 0; }
  }

  .card {
    position: relative;
    z-index: 10;
    width: 100%;
    max-width: 380px;
    background: rgba(255, 255, 255, .95);
    backdrop-filter: blur(10px);
    border-radius: 24px;
    padding: 40px 32px 32px;
    box-shadow: 0 25px 60px rgba(0, 0, 0, .25);
    text-align: center;
    animation: popIn .5s cubic-bezier(.34, 1.56, .64, 1);
  }
  @keyframes popIn {
    from { opacity: 0; transform: scale(.85) translateY(20px); }
    to   { opacity: 1; transform: scale(1) translateY(0); }
  }

  .lock-icon {
    font-size: 52px;
    line-height: 1;
    margin-bottom: 16px;
    display: inline-block;
    animation: pulse 2.2s ease-in-out infinite;
  }
  @keyframes pulse {
    0%, 100% { transform: scale(1); }
    50%      { transform: scale(1.12); }
  }

  h1 {
    font-size: 22px;
    color: #333;
    margin-bottom: 8px;
    font-weight: 700;
  }
  .subtitle {
    font-size: 14px;
    color: #888;
    margin-bottom: 26px;
  }

  .input-wrap {
    position: relative;
    margin-bottom: 16px;
  }
  input[type="password"],
  input[type="text"] {
    width: 100%;
    padding: 15px 48px 15px 18px;
    font-size: 16px;
    border: 2px solid #eee;
    border-radius: 14px;
    outline: none;
    transition: border-color .25s, box-shadow .25s;
    background: #fafafa;
    color: #333;
  }
  input:focus {
    border-color: #ff7eb3;
    background: #fff;
    box-shadow: 0 0 0 4px rgba(255, 126, 179, .15);
  }

  .toggle-eye {
    position: absolute;
    right: 14px;
    top: 50%;
    transform: translateY(-50%);
    background: none;
    border: none;
    cursor: pointer;
    font-size: 19px;
    color: #aaa;
    padding: 4px;
    line-height: 1;
  }
  .toggle-eye:hover { color: #ff7eb3; }

  button.submit {
    width: 100%;
    padding: 15px;
    font-size: 16px;
    font-weight: 700;
    color: #fff;
    border: none;
    border-radius: 14px;
    cursor: pointer;
    background: linear-gradient(135deg, #ff758c, #a18cd1);
    box-shadow: 0 8px 22px rgba(255, 117, 140, .45);
    transition: transform .15s, box-shadow .2s, opacity .2s;
    letter-spacing: .3px;
  }
  button.submit:hover { transform: translateY(-2px); box-shadow: 0 12px 28px rgba(255, 117, 140, .55); }
  button.submit:active { transform: translateY(0) scale(.98); }
  button.submit:disabled { opacity: .6; cursor: not-allowed; transform: none; }

  .error {
    color: #e74c3c;
    font-size: 13.5px;
    margin-top: 12px;
    min-height: 18px;
    font-weight: 600;
    opacity: 0;
    transition: opacity .2s;
  }
  .error.show { opacity: 1; }

  .shake { animation: shake .45s; }
  @keyframes shake {
    0%, 100% { transform: translateX(0); }
    20%, 60% { transform: translateX(-9px); }
    40%, 80% { transform: translateX(9px); }
  }

  .hint {
    margin-top: 20px;
    font-size: 12px;
    color: #bbb;
  }
</style>
</head>
<body>

<div class="card" id="card">
  <div class="lock-icon">🔒</div>
  <h1>Khu vực bảo mật</h1>
  <p class="subtitle">Nhập mật khẩu để mở khóa 💖</p>

  <form id="form" autocomplete="off">
    <div class="input-wrap">
      <input type="password" id="pass" placeholder="Nhập mật khẩu..." autofocus>
      <button type="button" class="toggle-eye" id="eye" aria-label="Hiện/ẩn mật khẩu">👁️</button>
    </div>
    <button type="submit" class="submit" id="btn">Mở khóa</button>
    <div class="error" id="error"></div>
  </form>

  <p class="hint" id="hint">💡 Gợi ý: mật khẩu là tên của người bạn yêu</p>
</div>

<script>
  /* =========================================================
     ⚙️  CẤU HÌNH — CHỈ CẦN SỬA Ở ĐÂY
     ========================================================= */
  const PASSWORD = "1111"; // 👈 ĐỔI MẬT KHẨU Ở ĐÂY

  const TARGET_URL = "https://lovecodes2.vercel.app/lovex/heartz.html?d=HYegJgdAtgDgzAHwMQDMUE4AMWEEsK4BGhEAxgPYgCyAngFYBKA6gIwBuAciAE4o0sAmCHRgBzABYAXSTADOALhAh8REhRBM2ARQAepGAA0uAV1x0AHMGFiANCuJlKm3fqMhTFqyNF4AV8YACAUwQFkwAtjYA4HFyBEAgQGAAgC+kwERAQHK8GgC0wHO8aPEAUygEKkB2vFIAgC8AQ+yqgBeAnQBbgMlyAENcAIBHY06cgGekntJxAvkASgQAYTAAA+BRANx-BEAEQAbl6MBgQChozuyAKl7stkBtvB6O7oKAXimZjcBjvDHSQEu8StJcwZueqoW5ACUAAn4M0tsVjE4mtAuJkrgEABxMowAKDQD7eAFSJJuIBdvB6MWmUyAA";

  const SHOW_HINT = true;        // false nếu không muốn hiện gợi ý
  const HINT_TEXT = "💡 Gợi ý: mật khẩu là ngày sinh của trai đẹp
  /* ========================================================= */


  const form   = document.getElementById('form');
  const pass   = document.getElementById('pass');
  const eye    = document.getElementById('eye');
  const error  = document.getElementById('error');
  const card   = document.getElementById('card');
  const btn    = document.getElementById('btn');
  const hintEl = document.getElementById('hint');

  if (!SHOW_HINT) hintEl.style.display = 'none';
  else hintEl.textContent = HINT_TEXT;

  // Hiện / ẩn mật khẩu
  eye.addEventListener('click', () => {
    const isHidden = pass.type === 'password';
    pass.type = isHidden ? 'text' : 'password';
    eye.textContent = isHidden ? '🙈' : '👁️';
    pass.focus();
  });

  // Kiểm tra mật khẩu
  form.addEventListener('submit', (e) => {
    e.preventDefault();

    if (pass.value === PASSWORD) {
      error.classList.remove('show');
      btn.disabled = true;
      btn.textContent = '✅ Đúng rồi! Đang mở...';

      setTimeout(() => {
        window.location.href = TARGET_URL;
      }, 400);

    } else {
      error.textContent = '❌ Mật khẩu không đúng, thử lại nhé!';
      error.classList.add('show');
      card.classList.remove('shake');
      void card.offsetWidth; // reset animation
      card.classList.add('shake');
      pass.value = '';
      pass.focus();
    }
  });

  // Tạo trái tim bay lơ lửng
  const hearts = ['💖', '💕', '💗', '❤️', '💘', '🌸'];
  for (let i = 0; i < 14; i++) {
    const h = document.createElement('div');
    h.className = 'heart-bg';
    h.textContent = hearts[Math.floor(Math.random() * hearts.length)];
    h.style.left = Math.random() * 100 + 'vw';
    h.style.fontSize = (14 + Math.random() * 20) + 'px';
    h.style.animationDuration = (8 + Math.random() * 8) + 's';
    h.style.animationDelay = (Math.random() * 10) + 's';
    document.body.appendChild(h);
  }
</script>

</body>
</html>
