# t-passkey-auth

<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>T パスキー認証（画面たたき台）</title>
  <style>
    :root {
      --bg-page: #f3f5f8;
      --bg-card: #ffffff;
      --text-main: #1e293b;
      --text-sub: #64748b;
      --border-soft: #cbd5e1;

      /* 単独の共通認証玄関として落ち着いた中立カラーを採用 */
      --primary: #1d4ed8;
      --primary-hover: #1e40af;

      --register: #15803d;
      --register-hover: #166534;

      --success-bg: #f0fdf4;
      --success-border: #86efac;
      --success-text: #166534;

      --error-bg: #fef2f2;
      --error-border: #fca5a5;
      --error-text: #991b1b;
    }

    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      padding: 16px;
      background-color: var(--bg-page);
      color: var(--text-main);
      font-family: -apple-system, BlinkMacSystemFont, "Hiragino Kaku Gothic ProN", "Yu Gothic", sans-serif;
      font-size: 16px;
      line-height: 1.5;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
    }

    .app-shell {
      width: 100%;
      max-width: 420px;
    }

    /* ヘッダー（①・②共通） */
    .brand-header {
      text-align: center;
      margin-bottom: 18px;
    }

    .brand-title {
      margin: 0;
      font-size: 24px;
      font-weight: 700;
      letter-spacing: 0.02em;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 8px;
    }

    .brand-subtitle {
      margin: 6px 0 0;
      font-size: 14px;
      color: var(--text-sub);
      font-weight: 600;
    }

    /* メインカード */
    .card {
      background-color: var(--bg-card);
      border-radius: 18px;
      padding: 24px 20px;
      box-shadow: 0 8px 24px rgba(15, 23, 42, 0.07);
      border: 1px solid #e2e8f0;
    }

    .screen-badge {
      display: inline-block;
      font-size: 13px;
      font-weight: 700;
      padding: 4px 10px;
      border-radius: 999px;
      margin-bottom: 12px;
    }

    .badge-login {
      background-color: #eff6ff;
      color: var(--primary);
    }

    .badge-register {
      background-color: #f0fdf4;
      color: var(--register);
    }

    .screen-heading {
      margin: 0 0 8px;
      font-size: 19px;
      font-weight: 700;
    }

    .screen-note {
      margin: 0 0 18px;
      font-size: 14px;
      color: var(--text-sub);
    }

    /* 氏名選択セレクト */
    .field-group {
      margin-bottom: 18px;
    }

    .field-label {
      display: block;
      font-size: 15px;
      font-weight: 700;
      margin-bottom: 8px;
    }

    .select-box {
      width: 100%;
      min-height: 54px;
      padding: 12px 14px;
      font-size: 16px;
      color: var(--text-main);
      background-color: #ffffff;
      border: 2px solid var(--border-soft);
      border-radius: 12px;
      outline: none;
    }

    .select-box:focus {
      border-color: var(--primary);
    }

    /* ボタン共通（スマホ親指押しやすい高さ） */
    .btn {
      width: 100%;
      min-height: 56px;
      padding: 14px 16px;
      font-size: 17px;
      font-weight: 700;
      border-radius: 12px;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 8px;
      transition: background-color 0.15s ease, transform 0.05s ease;
    }

    .btn:active {
      transform: scale(0.99);
    }

    .btn-primary {
      background-color: var(--primary);
      color: #ffffff;
      border: none;
    }

    .btn-primary:hover {
      background-color: var(--primary-hover);
    }

    .btn-register {
      background-color: var(--register);
      color: #ffffff;
      border: none;
    }

    .btn-register:hover {
      background-color: var(--register-hover);
    }

    .btn-secondary-outline {
      background-color: #ffffff;
      color: var(--text-main);
      border: 1.5px solid var(--border-soft);
      min-height: 50px;
      font-size: 15px;
      margin-top: 8px;
    }

    .btn-secondary-outline:hover {
      background-color: #f8fafc;
    }

    .btn-ghost-back {
      width: 100%;
      min-height: 46px;
      margin-top: 12px;
      background: transparent;
      border: none;
      color: var(--text-sub);
      font-size: 15px;
      font-weight: 600;
      cursor: pointer;
    }

    .btn-ghost-back:hover {
      color: var(--text-main);
      text-decoration: underline;
    }

    /* 下部エリア（ログイン画面からの新規登録誘導） */
    .sub-action-area {
      margin-top: 20px;
      text-align: center;
    }

    .sub-action-label {
      font-size: 13px;
      color: var(--text-sub);
      margin: 0 0 4px;
    }

    /* メッセージ表示（通常は非表示） */
    .message-box {
      display: none;
      margin-top: 16px;
      padding: 14px;
      border-radius: 12px;
      font-size: 14px;
    }

    .message-error {
      background-color: var(--error-bg);
      border: 1px solid var(--error-border);
      color: var(--error-text);
    }

    .message-success {
      background-color: var(--success-bg);
      border: 1px solid var(--success-border);
      color: var(--success-text);
    }

    .after-register-box {
      display: none;
      margin-top: 16px;
      padding-top: 14px;
      border-top: 1px dashed #bbf7d0;
    }

    .after-register-text {
      font-size: 14px;
      font-weight: 700;
      color: var(--success-text);
      margin: 0 0 10px;
      text-align: center;
    }

    .hidden {
      display: none;
    }

    /* 試作確認用の小さな操作バー（本番画面からは外す前提） */
    .mock-bar {
      margin-top: 24px;
      padding: 10px 12px;
      border-radius: 10px;
      background-color: #e2e8f0;
      font-size: 12px;
      color: #475569;
      text-align: center;
    }

    .mock-bar button {
      margin: 4px 4px 0;
      padding: 4px 8px;
      font-size: 12px;
      border-radius: 6px;
      border: 1px solid #94a3b8;
      background: #ffffff;
      color: #334155;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <div class="app-shell">
    <!-- ①・② 共通ヘッダー -->
    <header class="brand-header">
      <h1 class="brand-title"><span>🔑</span><span>T パスキー認証</span></h1>
      <p class="brand-subtitle">郡山おもちゃの病院 業務システム</p>
    </header>

    <!-- ① メイン画面：ログイン -->
    <section id="screen-login" class="card" aria-label="ログイン画面">
      <span class="screen-badge badge-login">ログイン</span>
      <h2 class="screen-heading">パスキーでログイン</h2>
      <p class="screen-note">お名前を選んでから、指紋または顔認証を押してください。</p>

      <div class="field-group">
        <label for="login-member-select" class="field-label">お名前を選択してください</label>
        <select id="login-member-select" class="select-box">
          <option value="">▼ 選択してください</option>
          <!-- 画面表示は氏名のみ・valueに内部IDを持たせる想定 -->
          <option value="internal_id_001">佐藤 太郎</option>
          <option value="internal_id_002">鈴木 花子</option>
          <option value="internal_id_003">高橋 一郎</option>
        </select>
      </div>

      <button type="button" id="btn-do-login" class="btn btn-primary">
        <span>👆</span><span>指紋・顔認証でログイン</span>
      </button>

      <div id="login-message" class="message-box message-error"></div>
    </section>

    <!-- ログイン画面の下部：新規登録へ進むボタン -->
    <div id="login-footer-register" class="sub-action-area">
      <p class="sub-action-label">初めての方・機種変更された方はこちら</p>
      <button type="button" id="btn-go-register" class="btn btn-secondary-outline">
        <span>新しい端末にパスキーを登録する</span>
      </button>
    </div>

    <!-- ② サブ画面：初回パスキー登録 -->
    <section id="screen-register" class="card hidden" aria-label="パスキー新規登録画面">
      <span class="screen-badge badge-register">初回・端末登録</span>
      <h2 class="screen-heading">この端末にパスキーを登録</h2>
      <p class="screen-note">あらかじめ登録されたお名前を選び、端末の生体認証で鍵を作成します。</p>

      <div class="field-group">
        <label for="register-member-select" class="field-label">ご自身のお名前を選択してください</label>
        <select id="register-member-select" class="select-box">
          <option value="">▼ 選択してください</option>
          <option value="internal_id_001">佐藤 太郎</option>
          <option value="internal_id_002">鈴木 花子</option>
          <option value="internal_id_003">高橋 一郎</option>
        </select>
      </div>

      <button type="button" id="btn-do-register" class="btn btn-register">
        <span>📲</span><span>この端末にパスキーを登録</span>
      </button>

      <div id="register-message" class="message-box"></div>

      <!-- 登録完了後：選択したお名前のままログインへ進める動線 -->
      <div id="after-register-area" class="after-register-box">
        <p id="after-register-text" class="after-register-text">登録が完了しました！</p>
        <button type="button" id="btn-login-now" class="btn btn-primary">
          <span>👆</span><span>このままログインする</span>
        </button>
      </div>

      <button type="button" id="btn-back-login" class="btn-ghost-back">
        ← ログイン画面に戻る
      </button>
    </section>

    <!-- 見た目確認用のモック操作（本番では削除） -->
    <div class="mock-bar">
      <div>【見た目確認用モック】※内部IDは画面に出さず裏側で保持</div>
      <button type="button" onclick="showLoginScreen()">① ログイン画面</button>
      <button type="button" onclick="showRegisterScreen()">② 登録画面</button>
      <button type="button" onclick="simulateRegisterDone()">② 登録完了状態を見る</button>
    </div>
  </div>

  <script>
    const loginScreen = document.getElementById("screen-login");
    const loginFooter = document.getElementById("login-footer-register");
    const registerScreen = document.getElementById("screen-register");

    const loginSelect = document.getElementById("login-member-select");
    const registerSelect = document.getElementById("register-member-select");

    const loginMessage = document.getElementById("login-message");
    const registerMessage = document.getElementById("register-message");
    const afterRegisterArea = document.getElementById("after-register-area");
    const afterRegisterText = document.getElementById("after-register-text");

    function clearMessages() {
      loginMessage.style.display = "none";
      loginMessage.textContent = "";
      registerMessage.style.display = "none";
      registerMessage.textContent = "";
      afterRegisterArea.style.display = "none";
    }

    function showLoginScreen(preselectedInternalId = "") {
      clearMessages();
      registerScreen.classList.add("hidden");
      loginScreen.classList.remove("hidden");
      loginFooter.classList.remove("hidden");

      if (preselectedInternalId) {
        loginSelect.value = preselectedInternalId;
      }
    }

    function showRegisterScreen() {
      clearMessages();
      // ログイン側で選んでいた氏名があれば引き継ぎ（任意）
      if (loginSelect.value && !registerSelect.value) {
        registerSelect.value = loginSelect.value;
      }
      loginScreen.classList.add("hidden");
      loginFooter.classList.add("hidden");
      registerScreen.classList.remove("hidden");
    }

    function simulateRegisterDone() {
      showRegisterScreen();
      if (!registerSelect.value) {
        registerSelect.value = "internal_id_001";
      }
      const selectedName = registerSelect.options[registerSelect.selectedIndex].text;
      registerMessage.className = "message-box message-success";
      registerMessage.style.display = "block";
      registerMessage.textContent = selectedName + " さんのパスキー登録が完了しました（モック表示）。";
      afterRegisterText.textContent = selectedName + " さんの準備ができました";
      afterRegisterArea.style.display = "block";
    }

    document.getElementById("btn-go-register").addEventListener("click", showRegisterScreen);
    document.getElementById("btn-back-login").addEventListener("click", () => showLoginScreen());

    document.getElementById("btn-do-login").addEventListener("click", () => {
      clearMessages();
      if (!loginSelect.value) {
        loginMessage.className = "message-box message-error";
        loginMessage.style.display = "block";
        loginMessage.textContent = "お名前を選択してからボタンを押してください。";
        return;
      }
      const selectedName = loginSelect.options[loginSelect.selectedIndex].text;
      loginMessage.className = "message-box message-success";
      loginMessage.style.display = "block";
      loginMessage.textContent = selectedName + " さん（内部IDは裏側で処理）の認証フロー呼び出し位置です（モック）。";
    });

    document.getElementById("btn-do-register").addEventListener("click", () => {
      clearMessages();
      if (!registerSelect.value) {
        registerMessage.className = "message-box message-error";
        registerMessage.style.display = "block";
        registerMessage.textContent = "ご自身のお名前を選択してからボタンを押してください。";
        return;
      }
      simulateRegisterDone();
    });

    document.getElementById("btn-login-now").addEventListener("click", () => {
      const targetId = registerSelect.value;
      showLoginScreen(targetId);
      const selectedName = loginSelect.options[loginSelect.selectedIndex]?.text || "";
      if (selectedName) {
        loginMessage.className = "message-box message-success";
        loginMessage.style.display = "block";
        loginMessage.textContent = selectedName + " さんを選択したままログイン画面へ移動しました。「指紋・顔認証でログイン」を押してください。";
      }
    });
  </script>
</body>
</html>
