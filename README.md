<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>AW 料理示範與備料工具查詢系統</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body { font-family: -apple-system, BlinkMacSystemFont, "Microsoft JhengHei", "Noto Sans TC", sans-serif; background: #f6f5f0; color: #292524; line-height: 1.5; padding-bottom: 60px; }
    header { background: #065f46; color: #fff; padding: 16px; position: sticky; top: 0; z-index: 20; box-shadow: 0 2px 8px rgba(0,0,0,0.12); }
    .container { max-width: 960px; margin: 0 auto; padding: 0 16px; }
    .header-flex { display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 10px; }
    h1 { font-size: 18px; font-weight: 700; }
    .sub { font-size: 12px; color: #a7f3d0; }
    .btn { border: none; border-radius: 8px; padding: 10px 16px; font-size: 14px; font-weight: 700; cursor: pointer; display: inline-flex; align-items: center; gap: 6px; transition: 0.15s; }
    .btn:active { transform: scale(0.97); }
    .btn-primary { background: #059669; color: #fff; }
    .btn-primary:hover { background: #047857; }
    .btn-amber { background: #f59e0b; color: #1c1917; }
    .btn-light { background: #e7e5e4; color: #44403c; }
    .btn-danger { background: #fee2e2; color: #b91c1c; font-size: 12px; padding: 6px 10px; }
    .card { background: #fff; border-radius: 14px; padding: 18px; margin-top: 16px; border: 1px solid #e7e5e4; box-shadow: 0 1px 4px rgba(0,0,0,0.04); }
    .batch-box { background: linear-gradient(135deg, #ecfdf5 0%, #fffbeb 100%); border: 2px solid #059669; }
    .batch-title { font-size: 16px; font-weight: 700; color: #064e3b; margin-bottom: 6px; display: flex; justify-content: space-between; align-items: center; }
    textarea, input[type="text"], select { width: 100%; border: 1px solid #d6d3d1; border-radius: 10px; padding: 10px 12px; font-size: 15px; font-family: inherit; background: #fff; }
    textarea:focus, input:focus { outline: 2px solid #059669; border-color: transparent; }
    .btn-row { display: flex; gap: 8px; margin-top: 10px; flex-wrap: wrap; }
    
    /* 結果直接展開區塊 */
    #resultSection { display: none; border: 2px solid #065f46; background: #fff; }
    .result-header { display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #e7e5e4; padding-bottom: 10px; margin-bottom: 12px; flex-wrap: wrap; gap: 8px; }
    .tag-list { display: flex; flex-wrap: wrap; gap: 6px; margin-bottom: 14px; }
    .tag { background: #d1fae5; color: #065f46; padding: 4px 10px; border-radius: 99px; font-size: 13px; font-weight: 700; cursor: pointer; }
    .tag:hover { background: #fee2e2; color: #991b1b; }
    .tabs { display: flex; gap: 8px; margin-bottom: 14px; }
    .tab-btn { flex: 1; padding: 10px; border-radius: 8px; border: 1px solid #d6d3d1; background: #f5f5f4; font-weight: 700; font-size: 14px; cursor: pointer; }
    .tab-btn.active { background: #065f46; color: #fff; border-color: #065f46; }
    .group-box { background: #fafaf9; border: 1px solid #e7e5e4; border-radius: 10px; padding: 12px 14px; margin-bottom: 12px; }
    .group-title { font-weight: 700; font-size: 15px; color: #065f46; margin-bottom: 6px; border-bottom: 1px dashed #d6d3d1; padding-bottom: 4px; }
    ul.clean-list { list-style: none; font-size: 14px; }
    ul.clean-list li { padding: 3px 0; }

    /* 食譜卡片網格 */
    .filter-bar { display: flex; flex-wrap: wrap; gap: 6px; margin-top: 10px; align-items: center; }
    .chip { padding: 6px 12px; border-radius: 99px; border: 1px solid #d6d3d1; background: #f5f5f4; font-size: 13px; cursor: pointer; font-weight: 500; }
    .chip.active { background: #065f46; color: #fff; border-color: #065f46; font-weight: 700; }
    .grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(280px, 1fr)); gap: 14px; margin-top: 16px; }
    .recipe-card { background: #fff; border: 1px solid #e7e5e4; border-radius: 12px; padding: 14px; display: flex; flex-direction: column; justify-content: space-between; }
    .recipe-card.selected { border: 2px solid #059669; background: #f0fdf4; }
    .badge { display: inline-block; background: #fef3c7; color: #92400e; font-size: 12px; font-weight: 700; padding: 2px 8px; border-radius: 6px; margin-right: 4px; }
    .recipe-title { font-size: 16px; font-weight: 700; margin: 8px 0 6px; color: #1c1917; }
    .recipe-ing { font-size: 13px; color: #57534e; margin-bottom: 12px; }
    .card-actions { display: flex; justify-content: space-between; gap: 8px; border-top: 1px solid #f5f5f4; padding-top: 10px; }

    /* 彈出詳細視窗 */
    .modal-bg { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.55); z-index: 50; padding: 16px; overflow-y: auto; }
    .modal-box { background: #fff; max-width: 600px; margin: 20px auto; border-radius: 14px; padding: 20px; }
  </style>
</head>
<body>

  <header>
    <div class="container header-flex">
      <div>
        <h1>🍳 AW 料理示範與備料工具查詢系統</h1>
        <div class="sub">專屬食譜庫 · 支援多道菜單一次查詢工具</div>
      </div>
      <div style="display:flex; gap:8px;">
        <button type="button" class="btn btn-light" style="padding:8px 12px; font-size:13px;" onclick="openAddModal()">+ 新增食譜</button>
        <button type="button" class="btn btn-amber" style="padding:8px 12px; font-size:13px;" onclick="scrollToResult()">
          📋 已選 (<span id="countBadge">0</span>)
        </button>
      </div>
    </div>
  </header>

  <div class="container">

    <!-- 1. 一次查詢多道料理工具 -->
    <div class="card batch-box">
      <div class="batch-title">
        <span>⚡ 一次查詢多道料理工具</span>
        <button type="button" class="btn btn-light" style="padding:4px 10px; font-size:12px;" onclick="fillDemo()">填入範例測試</button>
      </div>
      <p style="font-size:13px; color:#44403c; margin-bottom:8px;">
        輸入多道菜名（可用<strong>換行、頓號「、」或逗號</strong>分隔），按下綠色確認鈕立刻產生總工具表：
      </p>
      <textarea id="batchInput" rows="3" placeholder="例如：溫沙拉、炒蛤蜊、冬瓜排骨湯、炒青菜、披薩"></textarea>
      <div class="btn-row">
        <button type="button" class="btn btn-primary" onclick="runBatch()">✅ 確認查詢工具</button>
        <button type="button" class="btn btn-light" onclick="document.getElementById('batchInput').value='';">清空輸入</button>
      </div>
      <div id="batchMsg" style="margin-top:8px; font-size:13px; font-weight:700;"></div>
    </div>

    <!-- 2. 自動展開的工具與食材總表 -->
    <div id="resultSection" class="card">
      <div class="result-header">
        <h2 style="font-size:17px; color:#065f46;">📋 本場活動備料與工具總表</h2>
        <button type="button" class="btn btn-danger" onclick="clearAll()">全部清空</button>
      </div>
      <div style="font-size:12px; color:#57534e; margin-bottom:6px;">已選菜單（點擊標籤可移除單道）：</div>
      <div id="selectedTags" class="tag-list"></div>

      <div class="tabs">
        <button type="button" id="tabTools" class="tab-btn active" onclick="setTab('tools')">🛠️ 純工具清單（不含食材）</button>
        <button type="button" id="tabIng" class="tab-btn" onclick="setTab('ing')">🥬 食材與調味料清單</button>
      </div>

      <div id="resultBody"></div>

      <button type="button" class="btn btn-primary" style="width:100%; justify-content:center; margin-top:8px; padding:12px;" onclick="copyResult()">
        📋 一鍵複製清單（可直接貼到 LINE 群組）
      </button>
      <div id="copyMsg" style="text-align:center; color:#059669; font-size:13px; font-weight:700; margin-top:6px;"></div>
    </div>

    <!-- 3. 單道搜尋與鍋具篩選 -->
    <div class="card">
      <div style="display:flex; gap:8px;">
        <input type="text" id="searchInput" placeholder="搜尋菜名、食材（如：鮭魚）或鍋具（如：煎炸鍋）..." onkeyup="renderCards()">
        <button type="button" class="btn btn-primary" onclick="renderCards()" style="white-space:nowrap;">🔍 搜尋</button>
      </div>
      <div class="filter-bar">
        <span style="font-size:13px; font-weight:700; color:#57534e;">鍋具篩選：</span>
        <button type="button" class="chip active" onclick="setPotFilter('ALL', this)">全部</button>
        <button type="button" class="chip" onclick="setPotFilter('煎炸鍋', this)">煎炸鍋</button>
        <button type="button" class="chip" onclick="setPotFilter('燉煮鍋', this)">燉煮鍋</button>
        <button type="button" class="chip" onclick="setPotFilter('三公升湯鍋', this)">三公升湯鍋</button>
        <button type="button" class="chip" onclick="setPotFilter('不沾鍋', this)">不沾鍋</button>
        <button type="button" class="chip" onclick="setPotFilter('示範項目', this)">示範項目</button>
      </div>
    </div>

    <!-- 食譜列表 -->
    <div id="cardGrid" class="grid"></div>
  </div>

  <!-- 食譜步驟詳情視窗 -->
  <div id="detailModal" class="modal-bg" onclick="if(event.target===this) closeModal('detailModal')">
    <div class="modal-box">
      <div style="display:flex; justify-content:space-between; align-items:center; border-bottom:1px solid #e7e5e4; padding-bottom:10px; margin-bottom:12px;">
        <h3 id="mTitle" style="font-size:18px; color:#065f46;"></h3>
        <button type="button" class="btn btn-light" style="padding:4px 10px;" onclick="closeModal('detailModal')">✕ 關閉</button>
      </div>
      <div id="mBody" style="font-size:14px; line-height:1.6;"></div>
    </div>
  </div>

  <!-- 新增食譜視窗 -->
  <div id="addModal" class="modal-bg" onclick="if(event.target===this) closeModal('addModal')">
    <div class="modal-box">
      <h3 style="font-size:18px; color:#065f46; margin-bottom:12px;">➕ 新增專屬食譜</h3>
      <div style="display:flex; flex-direction:column; gap:10px;">
        <input type="text" id="nTitle" placeholder="菜名（必填，例如：無水燙青菜）">
        <select id="nPot">
          <option value="煎炸鍋">AW 主要鍋具：煎炸鍋</option>
          <option value="燉煮鍋">AW 主要鍋具：燉煮鍋</option>
          <option value="三公升湯鍋">AW 主要鍋具：三公升湯鍋</option>
          <option value="不沾鍋">AW 主要鍋具：不沾鍋</option>
          <option value="示範項目">示範項目（不用 AW 主鍋）</option>
        </select>
        <input type="text" id="nTools" placeholder="額外專用工具（逗號分隔，如：烘焙紙 1張, 叉子）">
        <textarea id="nIng" rows="3" placeholder="食材（一行一項）"></textarea>
        <input type="text" id="nSeason" placeholder="調味料（如：鹽、橄欖油）">
        <textarea id="nSteps" rows="3" placeholder="步驟（一行一步）"></textarea>
        <div style="display:flex; justify-content:flex-end; gap:8px; margin-top:6px;">
          <button type="button" class="btn btn-light" onclick="closeModal('addModal')">取消</button>
          <button type="button" class="btn btn-primary" onclick="saveRecipe()">儲存食譜</button>
        </div>
      </div>
    </div>
  </div>

  <script>
    var defaultRecipes = [
      { id: 'r1', title: '煎鮭魚（一鍋兩道）', aliases: ['煎鮭魚', '鮭魚'], pots: ['煎炸鍋'], extra: ['烘焙紙（折成小船狀）'], sugg: [], ing: ['鮭魚 1片'], season: '米酒、鹽、胡椒', steps: ['鮭魚先用米酒、鹽、胡椒醃漬去腥。', '烘焙紙折成小船狀包入鮭魚，放入煎炸鍋中，蓋鍋蓋開中小火烹調。'] },
      { id: 'r2', title: '活妍彩椒雞（一鍋兩道）', aliases: ['活妍彩椒雞', '彩椒雞'], pots: ['煎炸鍋'], extra: ['烘焙紙（折成小船狀）'], sugg: [], ing: ['雞胸肉 適量', '節瓜 適量', '甜椒 適量', '紅蘿蔔 適量'], season: '鹽、黑胡椒', steps: ['烘焙紙鋪底折成小船狀，依序鋪上節瓜、甜椒、紅蘿蔔。', '最上面蓋上雞胸肉，蓋上煎炸鍋鍋蓋開中小火烹調。'] },
      { id: 'r3', title: '高蛋白大阪燒', aliases: ['大阪燒', '高蛋白大阪燒'], pots: ['不沾鍋'], extra: ['鋼盆或調理盆（拌麵糊）'], sugg: ['電子秤', '量匙', '刮刀或打蛋器', '紗布（擠乾高麗菜水分）'], ing: ['高麗菜絲 250g', '板豆腐 100g', '雞蛋 2顆', '豬肉片 6片', '洋蔥 半個', '優質蛋白素 1匙', '柴魚片 5g＋適量'], season: '鹽 些許', steps: ['高麗菜絲加鹽靜置出水後擠乾水分。', '加入板豆腐、雞蛋、柴魚片5g、蛋白素、洋蔥拌勻至無粉狀。', '放入不沾鍋鋪肉片，中小火蓋鍋蓋煎至雙面金黃，盛盤撒柴魚片。'] },
      { id: 'r4', title: '無水豆漿火鍋', aliases: ['豆漿火鍋', '無水豆漿火鍋', '火鍋'], pots: ['三公升湯鍋'], extra: [], sugg: ['個人湯碗'], ing: ['高麗菜或大白菜', '洋蔥', '紅蘿蔔', '各式菇菇', '花椰菜', '木耳', '肉片', '無糖豆漿'], season: '依個人口味', steps: ['高麗菜或大白菜鋪底，依序放入其餘蔬菜與肉片。', '蓋鍋蓋中小火加熱，食材出水消下去後加入無糖豆漿煮滾即可。'] },
      { id: 'r5', title: '巧克力布朗尼', aliases: ['布朗尼', '巧克力布朗尼'], pots: [], extra: ['兩公升湯鍋＋鍋蓋（當烤箱用）', '四吋模具 1個', '鋼盆 1個（隔水加熱）', '篩子 1個'], sugg: ['電子秤', '量匙', '刮刀或打蛋器', '計時器'], ing: ['板豆腐 200g', '85%巧克力 65g', '無糖可可粉 10g', '雞蛋 1顆', '可可 Bodykey 1包', '原味蛋白素 1匙', '綜合好果實 1包', '無糖優格 適量'], season: '無', steps: ['隔水加熱融化85%巧克力倒入鋼盆，加入豆腐、雞蛋與過篩粉類拌勻。', '加入綜合好果實拌勻倒入四吋模具。', '放入兩公升湯鍋預熱5分鐘，烤40分鐘再悶10分鐘，倒扣塗無糖優格裝飾。'] },
      { id: 'r6', title: '免治豬肉飯（10人份）', aliases: ['免治豬肉飯', '豬肉飯'], pots: ['不沾鍋'], extra: ['電鍋或電子鍋 1台（煮米飯）'], sugg: ['削皮器'], ing: ['豬絞肉 1斤半', '洋蔥 4顆', '馬鈴薯 6顆', '紅蘿蔔 適量', '蒜頭 少許', '白米 1包', '雞蛋 適量'], season: '醬油 酌量', steps: ['白米用電鍋煮熟。', '不沾鍋爆香蒜頭與洋蔥，炒散豬絞肉後加入馬鈴薯與紅蘿蔔丁炒熟，醬油調味。'] },
      { id: 'r7', title: '香菇雞湯', aliases: ['香菇雞湯', '雞湯'], pots: ['三公升湯鍋'], extra: [], sugg: ['調理盆或小碗（泡乾香菇）', '個人湯碗'], ing: ['全雞肉（或半隻雞切小塊）', '乾香菇 100～200g', '杏鮑菇 數根', '好肝萃 2顆（可選）'], season: '鹽 適量', steps: ['乾香菇泡水後，與雞肉、杏鮑菇一同放入三公升湯鍋加水燉煮至熟透。'] },
      { id: 'r8', title: '手工披薩（一般版 1個／150g）', aliases: ['披薩', '手工披薩'], pots: ['煎炸鍋'], extra: ['叉子 1支（餅皮戳洞）', '調理盆 1個（麵團發酵）'], sugg: ['電子秤', '量匙', '量杯', '保鮮膜'], ing: ['中筋麵粉 150g', '酵母粉 5g', '溫水 50～70cc', '洋蔥', '番茄', '玉米筍', '雞肉 150g', '起司絲'], season: '糖 3g、鹽 3g、橄欖油 1湯匙', steps: ['麵團材料揉勻隔熱水發酵10分鐘。', '鍋內倒橄欖油，放入餅皮用叉子戳洞，鋪料與起司絲。', '蓋鍋蓋烤5分鐘，擦乾鍋蓋水滴後再悶10分鐘。'] },
      { id: 'r9', title: '手工披薩（大份量 450g／約3塊）', aliases: ['披薩3塊', '大披薩'], pots: ['煎炸鍋'], extra: ['叉子 1支（餅皮戳洞）', '調理盆 1～2個（麵團發酵）'], sugg: ['電子秤', '量匙', '量杯', '保鮮膜'], ing: ['中筋麵粉 450g（150g×3）', '酵母粉 1包', '溫水 150～210cc', '洋蔥 1個', '番茄 1個', '玉米筍 1盒', '雞肉 150g', '起司絲 1包'], season: '糖、鹽、橄欖油', steps: ['麵團發酵後分3份，鍋中放油入餅皮戳洞鋪料。', '蓋鍋蓋烤5分鐘，擦拭鍋蓋水滴後再悶10分鐘。'] },
      { id: 'r10', title: '手工披薩（瑪格麗特口味）', aliases: ['瑪格麗特', '瑪格麗特披薩'], pots: ['煎炸鍋'], extra: ['叉子 1支（餅皮戳洞）', '調理盆 1個（麵團發酵）'], sugg: ['電子秤', '量匙', '保鮮膜'], ing: ['中筋麵粉 150g', '酵母粉 5g', '牛番茄 1顆', '九層塔 適量', '披薩起司 適量'], season: '鹽、糖、橄欖油、番茄醬', steps: ['餅皮發酵10分鐘後入鍋戳洞，抹番茄醬鋪牛番茄、九層塔與起司。', '蓋鍋蓋烤5分鐘，擦乾鍋蓋水滴後再悶10分鐘。'] },
      { id: 'r11', title: '西班牙烘蛋', aliases: ['西班牙烘蛋', '烘蛋'], pots: ['不沾鍋'], extra: ['鋼盆或大碗 1個（打蛋液）'], sugg: ['削皮器'], ing: ['雞蛋 6顆', '洋蔥 1顆', '馬鈴薯 1顆', '紅黃彩椒 各1顆'], season: '無鹽奶油 1/5條（或橄欖油）、鹽、黑胡椒', steps: ['熱鍋冷油炒軟洋蔥、馬鈴薯與彩椒，調味後熄火倒入打散蛋液拌勻。', '中小火熱鍋放油倒入蔬菜蛋液，烘至表面熟透盛盤。'] },
      { id: 'r12', title: '金鍋講稿＆天生一對（10道全餐）', aliases: ['金鍋', '天生一對', '金鍋講稿'], pots: ['三公升湯鍋', '煎炸鍋', '不沾鍋'], extra: ['盛盤大盤子 10個', '調理盆 3個（泡香菇、泡米、泡米粉）'], sugg: [], ing: ['雞腿肉 1大隻', '豬絞肉 $50', '豬五花', '透抽 1隻', '臘腸/肝腸/臘肉', '米 1斤', '米粉 1包', '乾香菇/杏鮑菇/茄子/茭白筍/木耳/紅蘿蔔/彩椒/玉米筍/櫛瓜/芹菜/皮蛋/雞蛋'], season: '鹽、橄欖油、醬油、黑胡椒、義式香料、美乃滋、辣油', steps: ['三公升湯鍋15分：香菇雞湯、蒜泥白肉、素鮑魚、涼拌鮮茄。', '不沾鍋15分：港式臘味飯、鮮炒時蔬、金銀島。', '煎炸鍋10分：中式炒米粉、地中海堅果溫沙拉。'] },
      { id: 'r13', title: '炒蛤蜊', aliases: ['炒蛤蜊', '蛤蜊'], pots: ['煎炸鍋'], extra: [], sugg: ['吐沙盆 1個'], ing: ['大蛤蜊 1.5斤'], season: '米酒 適量', steps: ['大蛤蜊吐沙洗淨後放入煎炸鍋，嗆入米酒蓋鍋蓋加熱至全開即可。'] },
      { id: 'r14', title: '日式炒烏龍', aliases: ['日式炒烏龍', '炒烏龍', '烏龍麵'], pots: ['煎炸鍋'], extra: [], sugg: [], ing: ['冷凍烏龍麵', '鮮香菇 1小包', '紅蘿蔔絲', '洋蔥 1顆', '蔥段', '柴魚片 1包'], season: '糖、安麗醬油', steps: ['煎炸鍋炒香洋蔥、香菇與紅蘿蔔絲，加入烏龍麵與少許水悶軟炒散，調味撒柴魚片。'] },
      { id: 'r15', title: '彩漾溫沙拉', aliases: ['彩漾溫沙拉', '溫沙拉'], pots: ['燉煮鍋'], extra: [], sugg: ['量匙'], ing: ['青花菜 1大朵', '紅黃彩椒 各1顆', '杏鮑菇 2～3根', '玉米筍 1～2盒', '小番茄 約15顆', '堅果碎或熟白芝麻'], season: '初榨橄欖油 3大匙、黑胡椒、海鹽', steps: ['蔬菜放入燉煮鍋低溫悶煮保留脆度，盛盤淋初榨橄欖油、海鹽、黑胡椒與堅果碎。'] },
      { id: 'r16', title: '蘿蔔大骨排骨湯', aliases: ['蘿蔔大骨排骨湯', '蘿蔔排骨湯', '排骨湯'], pots: ['燉煮鍋'], extra: [], sugg: ['削皮器 1支', '量匙', '個人湯碗'], ing: ['豬小排 約2斤', '白蘿蔔 1條（約1公斤）', '老薑 5～6片', '水 3,000～3,500ml'], season: '米酒 1～2大匙、海鹽、白胡椒粉', steps: ['白蘿蔔削皮切塊，與豬小排、老薑片放入燉煮鍋加水與米酒燉煮，起鍋前調味。'] },
      { id: 'r17', title: '冬瓜大骨排骨湯', aliases: ['冬瓜大骨排骨湯', '冬瓜排骨湯'], pots: ['燉煮鍋'], extra: [], sugg: ['削皮器 1支', '量匙', '個人湯碗'], ing: ['豬小排 約2斤', '白皮冬瓜 1顆（約1公斤）', '老薑 5～6片', '水 3,000～3,500ml'], season: '米酒 1～2大匙、海鹽、白胡椒粉', steps: ['白皮冬瓜去皮切塊，與豬小排、老薑片放入燉煮鍋加水與米酒燉煮至軟爛調味。'] },
      { id: 'r18', title: '巴斯克乳酪蛋糕（2倍量）', aliases: ['巴斯克乳酪蛋糕', '巴斯克', '乳酪蛋糕'], pots: ['煎炸鍋'], extra: ['鋼盆或調理盆 1個', '篩子 1個', '烘焙紙 1張（或6吋烤模2個）'], sugg: ['電子秤', '量匙', '打蛋器或刮刀'], ing: ['奶油乳酪 500g', '二砂糖 120g', '無糖優格 300g', '蛋白粉 1.5匙', '雞蛋 4顆'], season: '無', steps: ['奶油乳酪與二砂糖拌勻，分次加蛋、優格與過篩蛋白粉拌勻，倒入鋪烘焙紙的煎炸鍋烘烤。'] },
      { id: 'r19', title: '鮭魚＋烤蔬菜（單份）', aliases: ['鮭魚烤蔬菜', '烤蔬菜'], pots: ['煎炸鍋'], extra: ['烘焙紙 1張（折成小船狀）'], sugg: [], ing: ['鮭魚 1片', '花椰菜', '玉米筍', '紅黃彩椒'], season: '海鹽、黑胡椒、橄欖油', steps: ['烘焙紙折小船放入鮭魚與蔬菜，入煎炸鍋蓋鍋蓋中小火悶烤至熟透。'] },
      { id: 'r20', title: '清蒸節瓜鱸魚', aliases: ['清蒸節瓜鱸魚', '清蒸鱸魚', '鱸魚'], pots: ['燉煮鍋'], extra: ['蒸架或蒸盤 1個', '深盤 1個（盛魚用）'], sugg: ['量匙'], ing: ['金目鱸魚排 約1公斤', '節瓜', '青蔥 2根', '老薑 半塊', '蒜頭 4～5瓣'], season: '米酒 2大匙、薄鹽醬油、香油、白胡椒', steps: ['深盤鋪節瓜、薑蔥放上魚排淋調味，放入燉煮鍋蒸架上蓋鍋蓋蒸熟，起鍋淋香油。'] },
      { id: 'r21', title: '鮮炒菠菜', aliases: ['鮮炒菠菜', '炒菠菜', '炒青菜'], pots: ['煎炸鍋'], extra: [], sugg: ['量匙'], ing: ['新鮮菠菜 3把（約800g）', '蒜仁 5～6瓣'], season: '食用油 1.5大匙、鹽 1茶匙、白胡椒', steps: ['煎炸鍋爆香蒜仁，放入菠菜與少許水氣快速翻炒，加鹽與白胡椒調味。'] },
      { id: 'r22', title: '麻油菜飯', aliases: ['麻油菜飯', '菜飯'], pots: ['不沾鍋'], extra: ['小碗 1個（泡乾香菇）'], sugg: ['量米杯 1個', '量匙'], ing: ['白米 3～3.5米杯', '高麗菜 約400g', '乾香菇 6～8朵', '老薑 1小塊'], season: '純黑麻油 2～3大匙、薄鹽醬油 1大匙、鹽、白胡椒', steps: ['不沾鍋用黑麻油爆香薑末與香菇丁，加入高麗菜與白米炒勻，加水與調味蓋鍋蓋炊煮悶10分鐘。'] },
      { id: 'r23', title: '紅蘿蔔低溫烹調營養示範', aliases: ['紅蘿蔔示範', '紅蘿蔔'], pots: [], extra: ['削皮器 1支', '搖搖杯 1個', '透明杯 1個'], sugg: [], ing: ['紅蘿蔔 適量', '水 適量'], season: '無', steps: ['削紅蘿蔔皮放入搖搖杯加水搖晃倒入透明杯，對比傳統水煮營養流失與低溫烹調保留原味。'] },
      { id: 'r24', title: '花椰菜清洗示範', aliases: ['花椰菜示範', '花椰菜'], pots: [], extra: ['清洗盆 2個（一盆鹽水、一盆蔬果清潔劑）'], sugg: [], ing: ['花椰菜 1朵', '水 適量'], season: '鹽巴、蔬果清潔劑', steps: ['準備兩盆水分別用鹽巴與蔬果清潔劑清洗花椰菜，對比農藥與脂溶性蠟質清潔效果。'] }
    ];

    var recipes = defaultRecipes;
    try {
      var saved = localStorage.getItem('aw_recipes_v3');
      if (saved) recipes = JSON.parse(saved);
    } catch (e) {}

    var selectedIds = [];
    var potFilter = 'ALL';
    var currentTab = 'tools';

    function fillDemo() {
      document.getElementById('batchInput').value = '溫沙拉、炒蛤蜊、冬瓜排骨湯、炒青菜、披薩';
    }

    function runBatch() {
      var raw = document.getElementById('batchInput').value.trim();
      var msgBox = document.getElementById('batchMsg');
      if (!raw) {
        msgBox.style.color = '#b91c1c';
        msgBox.textContent = '⚠️ 請先輸入至少一道菜名再按確認！';
        return;
      }

      var tokens = raw.split(/[\n,，、;；]+/);
      var matched = 0;
      var unmatched = [];

      for (var i = 0; i < tokens.length; i++) {
        var t = tokens[i].trim();
        if (!t) continue;
        var found = null;
        for (var j = 0; j < recipes.length; j++) {
          var r = recipes[j];
          if (r.title === t || (r.aliases && r.aliases.indexOf(t) !== -1)) {
            found = r; break;
          }
        }
        if (!found) {
          for (var k = 0; k < recipes.length; k++) {
            var r2 = recipes[k];
            if (r2.title.indexOf(t) !== -1) { found = r2; break; }
            if (r2.aliases) {
              for (var a = 0; a < r2.aliases.length; a++) {
                if (t.indexOf(r2.aliases[a]) !== -1 || r2.aliases[a].indexOf(t) !== -1) {
                  found = r2; break;
                }
              }
            }
            if (found) break;
          }
        }

        if (found) {
          if (selectedIds.indexOf(found.id) === -1) selectedIds.push(found.id);
          matched++;
        } else {
          unmatched.push(t);
        }
      }

      msgBox.style.color = '#065f46';
      msgBox.textContent = '✅ 已成功加入 ' + matched + ' 道料理！' + (unmatched.length ? '（找不到：' + unmatched.join('、') + '）' : '');
      updateAll();
      if (matched > 0) scrollToResult();
    }

    function scrollToResult() {
      var sec = document.getElementById('resultSection');
      sec.style.display = 'block';
      sec.scrollIntoView({ behavior: 'smooth' });
    }

    function toggleId(id) {
      var idx = selectedIds.indexOf(id);
      if (idx === -1) selectedIds.push(id);
      else selectedIds.splice(idx, 1);
      updateAll();
    }

    function clearAll() {
      selectedIds = [];
      updateAll();
    }

    function setTab(t) {
      currentTab = t;
      document.getElementById('tabTools').className = t === 'tools' ? 'tab-btn active' : 'tab-btn';
      document.getElementById('tabIng').className = t === 'ing' ? 'tab-btn active' : 'tab-btn';
      renderResult();
    }

    function setPotFilter(pot, btn) {
      potFilter = pot;
      var chips = document.querySelectorAll('.chip');
      for (var i = 0; i < chips.length; i++) chips[i].className = 'chip';
      btn.className = 'chip active';
      renderCards();
    }

    function updateAll() {
      document.getElementById('countBadge').textContent = selectedIds.length;
      renderResult();
      renderCards();
    }

    function getSelectedRecipes() {
      var list = [];
      for (var i = 0; i < selectedIds.length; i++) {
        for (var j = 0; j < recipes.length; j++) {
          if (recipes[j].id === selectedIds[i]) list.push(recipes[j]);
        }
      }
      return list;
    }

    function buildSummary() {
      var list = getSelectedRecipes();
      var dishCount = 0;
      var hasJinGuo = false;
      var potMap = {};
      var extraMap = {};
      var suggMap = {};

      for (var i = 0; i < list.length; i++) {
        var r = list[i];
        if (r.pots.length > 0 || r.id === 'r5') dishCount++;
        if (r.id === 'r12') hasJinGuo = true;

        for (var p = 0; p < r.pots.length; p++) {
          var pn = r.pots[p];
          if (!potMap[pn]) potMap[pn] = [];
          potMap[pn].push(r.title);
        }
        for (var e = 0; e < r.extra.length; e++) {
          var en = r.extra[e];
          if (!extraMap[en]) extraMap[en] = [];
          extraMap[en].push(r.title);
        }
        for (var s = 0; s < r.sugg.length; s++) {
          var sn = r.sugg[s];
          if (!suggMap[sn]) suggMap[sn] = [];
          suggMap[sn].push(r.title);
        }
      }

      var plateText = hasJinGuo ? '至少 10 個（含金鍋全餐）' : (dishCount > 0 ? dishCount + ' 道菜，至少 ' + dishCount + ' 個' : '依料理數量加減');
      return { list: list, plateText: plateText, potMap: potMap, extraMap: extraMap, suggMap: suggMap };
    }

    function renderResult() {
      var sec = document.getElementById('resultSection');
      var tagsDiv = document.getElementById('selectedTags');
      var bodyDiv = document.getElementById('resultBody');
      var data = buildSummary();

      if (data.list.length === 0) {
        sec.style.display = 'none';
        return;
      }
      sec.style.display = 'block';

      var tagsHtml = '';
      for (var i = 0; i < data.list.length; i++) {
        tagsHtml += '<span class="tag" onclick="toggleId(\'' + data.list[i].id + '\')">' + data.list[i].title + ' ✕</span>';
      }
      tagsDiv.innerHTML = tagsHtml;

      if (currentTab === 'tools') {
        var html = '<div class="group-box"><div class="group-title">📌 固定備品（每場必備）</div><ul class="clean-list">' +
          '<li>• 卡式爐或電磁爐：2 組</li>' +
          '<li>• 鍋鏟、煎匙、湯勺、夾子：各 1～2 支</li>' +
          '<li>• 刀子、砧板：1～2 組</li>' +
          '<li>• 衛生紙 1 包、廚房紙巾 1～2 包</li>' +
          '<li>• 抹布 1～2 條、菜瓜布 1 塊、金剛刷 1 個、洗碗精 1 瓶</li>' +
          '<li style="font-weight:700; color:#065f46;">• 盛盤的盤子：' + data.plateText + '</li>' +
          '</ul></div>';

        html += '<div class="group-box" style="background:#fffbeb; border-color:#fde68a;"><div class="group-title" style="color:#92400e;">🍳 AW 主要鍋具</div>';
        var potKeys = Object.keys(data.potMap);
        if (potKeys.length > 0) {
          html += '<ul class="clean-list">';
          for (var p = 0; p < potKeys.length; p++) {
            var k = potKeys[p];
            html += '<li>• <strong>' + k + '＋鍋蓋</strong>：' + data.potMap[k].join('、') + '</li>';
          }
          html += '</ul>';
        } else {
          html += '<div style="font-size:13px; color:#78716c;">本場次未使用 AW 主要鍋具</div>';
        }
        html += '</div>';

        var extraKeys = Object.keys(data.extraMap);
        var suggKeys = Object.keys(data.suggMap);
        html += '<div class="group-box"><div class="group-title">🧰 其他工具（＊為建議備品）</div>';
        if (extraKeys.length + suggKeys.length > 0) {
          html += '<ul class="clean-list">';
          for (var e = 0; e < extraKeys.length; e++) {
            html += '<li>• <strong>' + extraKeys[e] + '</strong>：' + data.extraMap[extraKeys[e]].join('、') + '</li>';
          }
          for (var s = 0; s < suggKeys.length; s++) {
            html += '<li style="color:#57534e;">• ＊' + suggKeys[s] + '：' + data.suggMap[suggKeys[s]].join('、') + '</li>';
          }
          html += '</ul>';
        } else {
          html += '<div style="font-size:13px; color:#78716c;">無額外專用工具需求</div>';
        }
        html += '</div>';
        bodyDiv.innerHTML = html;
      } else {
        var ingHtml = '';
        for (var m = 0; m < data.list.length; m++) {
          var item = data.list[m];
          ingHtml += '<div class="group-box"><div class="group-title">' + item.title + '</div>' +
            '<div style="font-size:13px; margin-bottom:4px;"><strong>食材：</strong>' + item.ing.join('、') + '</div>' +
            '<div style="font-size:13px; color:#57534e;"><strong>調味：</strong>' + (item.season || '無') + '</div></div>';
        }
        bodyDiv.innerHTML = ingHtml;
      }
    }

    function copyResult() {
      var data = buildSummary();
      if (data.list.length === 0) return;
      var lines = ['【本場菜單】'];
      for (var i = 0; i < data.list.length; i++) lines.push('• ' + data.list[i].title);
      lines.push('');

      if (currentTab === 'tools') {
        lines.push('【固定備品】');
        lines.push('• 卡式爐或電磁爐：2 組');
        lines.push('• 鍋鏟、煎匙、湯勺、夾子：各 1～2 支');
        lines.push('• 刀子、砧板：1～2 組');
        lines.push('• 衛生紙 1 包、廚房紙巾 1～2 包');
        lines.push('• 抹布 1～2 條、菜瓜布 1 塊、金剛刷 1 個、洗碗精 1 瓶');
        lines.push('• 盛盤的盤子：' + data.plateText);
        lines.push('');

        var potKeys = Object.keys(data.potMap);
        if (potKeys.length > 0) {
          lines.push('【AW 主要鍋具】');
          for (var p = 0; p < potKeys.length; p++) {
            lines.push('• ' + potKeys[p] + '＋鍋蓋：' + data.potMap[potKeys[p]].join('、'));
          }
          lines.push('');
        }

        var extraKeys = Object.keys(data.extraMap);
        var suggKeys = Object.keys(data.suggMap);
        if (extraKeys.length + suggKeys.length > 0) {
          lines.push('【其他工具】');
          for (var e = 0; e < extraKeys.length; e++) lines.push('• ' + extraKeys[e] + '：' + data.extraMap[extraKeys[e]].join('、'));
          for (var s = 0; s < suggKeys.length; s++) lines.push('• ＊' + suggKeys[s] + '：' + data.suggMap[suggKeys[s]].join('、'));
        }
      } else {
        lines.push('【食材與調味料清單】');
        for (var m = 0; m < data.list.length; m++) {
          lines.push('\n▸ ' + data.list[m].title);
          lines.push('  食材：' + data.list[m].ing.join('、'));
          lines.push('  調味：' + (data.list[m].season || '無'));
        }
      }

      var text = lines.join('\n');
      var ta = document.createElement('textarea');
      ta.value = text;
      document.body.appendChild(ta);
      ta.select();
      document.execCommand('copy');
      document.body.removeChild(ta);

      var msg = document.getElementById('copyMsg');
      msg.textContent = '✅ 已成功複製！請直接到 LINE 貼上';
      setTimeout(function() { msg.textContent = ''; }, 3000);
    }

    function renderCards() {
      var q = document.getElementById('searchInput').value.trim().toLowerCase();
      var grid = document.getElementById('cardGrid');
      var html = '';

      for (var i = 0; i < recipes.length; i++) {
        var r = recipes[i];
        var matchPot = (potFilter === 'ALL') ||
          (potFilter === '示範項目' && r.pots.length === 0) ||
          (r.pots.indexOf(potFilter) !== -1);
        var blob = (r.title + ' ' + r.pots.join(' ') + ' ' + r.ing.join(' ')).toLowerCase();
        var matchQ = !q || blob.indexOf(q) !== -1;

        if (!matchPot || !matchQ) continue;

        var isSel = selectedIds.indexOf(r.id) !== -1;
        var badges = r.pots.length > 0
          ? r.pots.map(function(p) { return '<span class="badge">🍳 ' + p + '</span>'; }).join('')
          : '<span class="badge" style="background:#e0f2fe; color:#075985;">🔬 示範項目</span>';

        html += '<div class="recipe-card ' + (isSel ? 'selected' : '') + '">' +
          '<div>' +
            '<div>' + badges + '</div>' +
            '<div class="recipe-title">' + r.title + '</div>' +
            '<div class="recipe-ing"><strong>食材：</strong>' + r.ing.slice(0, 4).join('、') + (r.ing.length > 4 ? '...' : '') + '</div>' +
          '</div>' +
          '<div class="card-actions">' +
            '<button type="button" class="btn btn-light" style="padding:6px 10px; font-size:13px;" onclick="openDetail(\'' + r.id + '\')">看步驟</button>' +
            '<button type="button" class="btn ' + (isSel ? 'btn-amber' : 'btn-primary') + '" style="padding:6px 12px; font-size:13px;" onclick="toggleId(\'' + r.id + '\')">' +
              (isSel ? '✓ 已加入' : '+ 加入菜單') +
            '</button>' +
          '</div>' +
        '</div>';
      }
      grid.innerHTML = html || '<div style="grid-column:1/-1; text-align:center; padding:30px; color:#78716c;">找不到符合的食譜</div>';
    }

    function openDetail(id) {
      var r = null;
      for (var i = 0; i < recipes.length; i++) if (recipes[i].id === id) r = recipes[i];
      if (!r) return;
      document.getElementById('mTitle').textContent = r.title;
      var html = '<p style="margin-bottom:8px;"><strong>🍳 AW 主要鍋具：</strong>' + (r.pots.length ? r.pots.join('、') + '（含鍋蓋）' : '無') + '</p>';
      if (r.extra.length) html += '<p style="margin-bottom:8px;"><strong>🧰 專用工具：</strong>' + r.extra.join('、') + '</p>';
      if (r.sugg.length) html += '<p style="margin-bottom:8px; color:#92400e;"><strong>＊建議備品：</strong>' + r.sugg.join('、') + '</p>';
      html += '<p style="margin-bottom:4px;"><strong>🥬 食材：</strong>' + r.ing.join('、') + '</p>';
      html += '<p style="margin-bottom:12px;"><strong>🧂 調味：</strong>' + (r.season || '無') + '</p>';
      html += '<div><strong>👩‍🍳 步驟：</strong><ol style="padding-left:20px; margin-top:4px;">' +
        r.steps.map(function(s) { return '<li style="margin-bottom:4px;">' + s + '</li>'; }).join('') +
        '</ol></div>';
      document.getElementById('mBody').innerHTML = html;
      document.getElementById('detailModal').style.display = 'block';
    }

    function closeModal(id) {
      document.getElementById(id).style.display = 'none';
    }

    function openAddModal() {
      document.getElementById('addModal').style.display = 'block';
    }

    function saveRecipe() {
      var title = document.getElementById('nTitle').value.trim();
      if (!title) return alert('請輸入菜名！');
      var pot = document.getElementById('nPot').value;
      var extra = document.getElementById('nTools').value.split(/[,，、]/).map(function(s){return s.trim();}).filter(Boolean);
      var ing = document.getElementById('nIng').value.split('\n').map(function(s){return s.trim();}).filter(Boolean);
      var season = document.getElementById('nSeason').value.trim();
      var steps = document.getElementById('nSteps').value.split('\n').map(function(s){return s.trim();}).filter(Boolean);

      recipes.unshift({
        id: 'c_' + Date.now(),
        title: title,
        aliases: [title],
        pots: pot === '示範項目' ? [] : [pot],
        extra: extra,
        sugg: [],
        ing: ing.length ? ing : ['依需求準備'],
        season: season || '依口味',
        steps: steps.length ? steps : ['依示範流程烹調']
      });
      try { localStorage.setItem('aw_recipes_v3', JSON.stringify(recipes)); } catch (e) {}
      closeModal('addModal');
      renderCards();
    }

    renderCards();
  </script>
</body>
</html>
