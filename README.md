!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>一年生マップ</title>
  <style>
    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      font-family: -apple-system, BlinkMacSystemFont, "Hiragino Sans", "Yu Gothic", sans-serif;
      background: linear-gradient(180deg, #fff7ed 0%, #ffffff 45%, #fffbeb 100%);
      color: #1f2937;
    }

    .container {
      max-width: 430px;
      margin: 0 auto;
      padding: 16px;
    }

    .card {
      background: #ffffff;
      border-radius: 24px;
      padding: 20px;
      box-shadow: 0 10px 30px rgba(15, 23, 42, 0.08);
      margin-bottom: 16px;
    }

    .badge {
      display: inline-block;
      background: #ffedd5;
      color: #c2410c;
      font-size: 12px;
      font-weight: 700;
      padding: 6px 10px;
      border-radius: 999px;
      margin-bottom: 10px;
    }

    h1 {
      margin: 0 0 8px;
      font-size: 28px;
    }

    p {
      margin: 0;
      line-height: 1.7;
    }

    .sub {
      color: #6b7280;
      font-size: 14px;
      margin-top: 6px;
    }

    .stats {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 10px;
      margin-top: 16px;
    }

    .stat-box {
      background: #fff7ed;
      border-radius: 18px;
      padding: 14px;
    }

    .stat-box:nth-child(2) {
      background: #fffbeb;
    }

    .stat-label {
      font-size: 12px;
      color: #6b7280;
      margin-bottom: 4px;
    }

    .stat-value {
      font-size: 24px;
      font-weight: 800;
    }

    .grid-title {
      display: flex;
      align-items: center;
      justify-content: space-between;
      margin-bottom: 12px;
    }

    .complete {
      background: #dcfce7;
      color: #15803d;
      font-size: 12px;
      font-weight: 700;
      padding: 6px 10px;
      border-radius: 999px;
      display: none;
    }

    .grid {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 10px;
    }

    .cell {
      position: relative;
      aspect-ratio: 1 / 1;
      border: none;
      border-radius: 22px;
      background: #ffffff;
      box-shadow: 0 8px 18px rgba(15, 23, 42, 0.08);
      padding: 10px;
      text-align: left;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      cursor: pointer;
    }

    .cell-number {
      font-size: 11px;
      font-weight: 700;
      color: #9ca3af;
    }

    .cell-text {
      font-size: 12px;
      line-height: 1.5;
      font-weight: 700;
      color: #374151;
      margin: 8px 0;
    }

    .cell-hint {
      font-size: 10px;
      color: #6b7280;
    }

    .cell.completed {
      background: #ffedd5;
    }

    .cell.opened {
      background: #111827;
      color: #ffffff;
    }

    .cell.opened .cell-text,
    .cell.opened .cell-hint,
    .cell.opened .cell-number {
      color: #ffffff;
    }

    .hole {
      position: absolute;
      inset: 0;
      display: none;
      align-items: center;
      justify-content: center;
      background: rgba(0, 0, 0, 0.3);
      border-radius: 22px;
    }

    .cell.opened .hole {
      display: flex;
    }

    .hole-circle {
      width: 60px;
      height: 60px;
      border-radius: 999px;
      border: 4px dashed #ffffff;
    }

    .guide {
      color: #6b7280;
      font-size: 12px;
      margin-top: 12px;
    }

    .progress-bar {
      width: 100%;
      height: 12px;
      background: #f3f4f6;
      border-radius: 999px;
      overflow: hidden;
      margin-top: 14px;
    }

    .progress {
      height: 100%;
      width: 0%;
      background: #111827;
      border-radius: 999px;
      transition: width 0.2s ease;
    }

    .actions {
      display: flex;
      justify-content: space-between;
      align-items: center;
      gap: 12px;
      margin-top: 14px;
    }

    .reset-btn {
      border: none;
      background: #111827;
      color: #ffffff;
      border-radius: 16px;
      padding: 12px 16px;
      font-weight: 700;
      cursor: pointer;
    }

    ol {
      padding-left: 18px;
      margin: 10px 0 0;
      color: #4b5563;
      line-height: 1.8;
      font-size: 14px;
    }
  </style>
</head>
<body>
  <div class="container">
    <section class="card">
      <div class="badge">QRでアクセス</div>
      <h1>一年生マップ</h1>
      <p class="sub">ミッションをクリアしたらタップして、穴をあけよう。9個ぜんぶ開いたらコンプリート！</p>

      <div class="stats">
        <div class="stat-box">
          <div class="stat-label">クリア数</div>
          <div class="stat-value"><span id="completedCount">0</span> / 9</div>
        </div>
        <div class="stat-box">
          <div class="stat-label">穴をあけた数</div>
          <div class="stat-value"><span id="openedCount">0</span> / 9</div>
        </div>
      </div>
    </section>

    <section class="card">
      <div class="grid-title">
        <h2 style="margin: 0; font-size: 18px;">ミッションマップ</h2>
        <div id="completeBadge" class="complete">COMPLETE!</div>
      </div>

      <div id="grid" class="grid"></div>
      <p class="guide">1回目のタップで「クリア」、2回目のタップで「穴あけ」になります。</p>
    </section>

    <section class="card">
      <h2 style="margin: 0; font-size: 18px;">使い方</h2>
      <ol>
        <li>QRコードを読み取ってサイトを開く</li>
        <li>ミッションを達成したらそのマスをタップ</li>
        <li>もう一度タップすると穴あけ表示になる</li>
        <li>9マス全部あいたらゴール</li>
      </ol>
    </section>

    <section class="card">
      <div class="actions">
        <div>
          <h2 style="margin: 0; font-size: 18px;">進み具合</h2>
          <p class="sub">オリエンや新歓イベントにも使えます。</p>
        </div>
        <button class="reset-btn" id="resetBtn">リセット</button>
      </div>
      <div class="progress-bar">
        <div id="progress" class="progress"></div>
      </div>
    </section>
  </div>

  <script>
    const missions = [
      "先輩にあいさつする",
      "学内のお気に入りスポットを見つける",
      "サークルを1つ調べる",
      "学食を食べる",
      "新しい友だちに話しかける",
      "学生証の使い方を確認する",
      "図書館に行く",
      "授業の教室を確認する",
      "キャンパスで写真を1枚撮る"
    ];

    const state = missions.map(() => ({ completed: false, opened: false }));

    const grid = document.getElementById("grid");
    const completedCount = document.getElementById("completedCount");
    const openedCount = document.getElementById("openedCount");
    const progress = document.getElementById("progress");
    const completeBadge = document.getElementById("completeBadge");
    const resetBtn = document.getElementById("resetBtn");

    function render() {
      grid.innerHTML = "";

      state.forEach((item, index) => {
        const cell = document.createElement("button");
        cell.className = "cell";
        if (item.completed) cell.classList.add("completed");
        if (item.opened) cell.classList.add("opened");

        const hint = item.opened
          ? "穴あけ完了"
          : item.completed
          ? "もう一度タップで穴あけ"
          : "タップでクリア";

        cell.innerHTML = `
          <div class="cell-number">MISSION ${index + 1}</div>
          <div class="cell-text">${missions[index]}</div>
          <div class="cell-hint">${hint}</div>
          <div class="hole"><div class="hole-circle"></div></div>
        `;

        cell.addEventListener("click", () => {
          if (!state[index].completed) {
            state[index].completed = true;
          } else if (!state[index].opened) {
            state[index].opened = true;
          }
          update();
        });

        grid.appendChild(cell);
      });

      const completed = state.filter((item) => item.completed).length;
      const opened = state.filter((item) => item.opened).length;

      completedCount.textContent = completed;
      openedCount.textContent = opened;
      progress.style.width = `${(opened / 9) * 100}%`;
      completeBadge.style.display = opened === 9 ? "inline-block" : "none";
    }

    function update() {
      render();
    }

    resetBtn.addEventListener("click", () => {
      state.forEach((item) => {
        item.completed = false;
        item.opened = false;
      });
      update();
    });

    render();
  </script>
</body>
</html>
