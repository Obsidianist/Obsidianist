
```dataviewjs
const css = [
  ".ib { font-family: var(--font-interface); padding: 2px 0 28px; }",

  ".ib-bar { display:flex; flex-direction:column; gap:7px; margin-bottom:12px; }",

  ".ib-row1 { display:flex; align-items:center; gap:8px; padding:8px 12px; background:var(--background-secondary); border:1px solid var(--background-modifier-border); border-radius:var(--radius-m); }",
  ".ib-ico-prefix { display:flex; align-items:center; flex-shrink:0; color:var(--text-muted); }",
  ".ib-ico-prefix svg { width:15px; height:15px; }",
  ".ib-search { flex:1; padding:3px 4px; border:none; background:transparent; color:var(--text-normal); font-size:var(--font-ui-small); outline:none; min-width:0; }",
  ".ib-search::placeholder { color:var(--text-faint); }",
  ".ib-kbd { padding:1px 5px; border-radius:3px; background:var(--background-modifier-border); color:var(--text-faint); font-size:10px; border:1px solid var(--background-modifier-border); white-space:nowrap; flex-shrink:0; }",
  ".ib-vsep { width:1px; height:14px; background:var(--background-modifier-border); flex-shrink:0; }",
  ".ib-stats { font-size:11px; color:var(--text-muted); white-space:nowrap; flex-shrink:0; }",
  ".ib-stats b { color:var(--interactive-accent); font-weight:600; }",
  ".ib-clear { background:none; border:none; cursor:pointer; color:var(--text-faint); font-size:14px; padding:0 2px; line-height:1; display:none; flex-shrink:0; }",
  ".ib-clear.on { display:block; }",
  ".ib-clear:hover { color:var(--text-normal); }",

  ".ib-row2 { display:flex; align-items:center; gap:6px; flex-wrap:wrap; }",
  ".ib-tabs { display:flex; gap:2px; background:var(--background-secondary); border:1px solid var(--background-modifier-border); border-radius:var(--radius-s); padding:3px; }",
  ".ib-tab { padding:3px 11px; border:none; background:transparent; color:var(--text-muted); font-size:12px; cursor:pointer; border-radius:calc(var(--radius-s) - 1px); transition:all 0.12s; white-space:nowrap; }",
  ".ib-tab:hover { color:var(--text-normal); }",
  ".ib-tab.on { background:var(--interactive-accent); color:var(--text-on-accent); border-color:var(--interactive-accent); }",

  ".ib-row3 { display:flex; align-items:center; flex-wrap:wrap; gap:8px; padding:7px 11px; background:var(--background-secondary); border:1px solid var(--background-modifier-border); border-radius:var(--radius-m); }",
  ".ib-cg { display:flex; align-items:center; gap:3px; }",
  ".ib-cl { font-size:11px; color:var(--text-faint); margin-right:1px; white-space:nowrap; }",
  ".ib-btn { padding:3px 9px; border-radius:var(--radius-s); border:1px solid var(--background-modifier-border); background:var(--background-primary); color:var(--text-muted); cursor:pointer; font-size:11px; transition:all 0.12s; white-space:nowrap; }",
  ".ib-btn:hover { color:var(--text-normal); background:var(--background-modifier-hover); }",
  ".ib-btn.on { background:var(--interactive-accent); color:var(--text-on-accent); border-color:var(--interactive-accent); }",
  ".ib-select { padding:3px 6px; border-radius:var(--radius-s); border:1px solid var(--background-modifier-border); background:var(--background-primary); color:var(--text-normal); font-size:11px; cursor:pointer; outline:none; }",
  ".ib-divider { width:1px; height:18px; background:var(--background-modifier-border); }",
  ".ib-btn.ib-rand:hover { background:var(--color-purple); color:#fff; border-color:var(--color-purple); }",

  ".ib-grid { min-height:80px; transition:background 0.2s; }",
  ".ib-grid.dark { background:#1e1e2e; border-radius:var(--radius-m); }",
  ".ib-item { display:flex; flex-direction:column; align-items:center; justify-content:center; border-radius:var(--radius-s); cursor:pointer; transition:background 0.1s, transform 0.12s; user-select:none; flex-shrink:0; position:relative; }",
  ".ib-item:hover { transform:scale(1.1); z-index:2; }",
  ".ib-item:active { transform:scale(0.93); }",
  ".ib-item:hover { background:var(--background-modifier-hover); }",
  ".ib-grid.dark .ib-item { color:#cdd6f4; }",
  ".ib-grid.dark .ib-item svg { color:#cdd6f4 !important; }",
  ".ib-grid.dark .ib-item:hover { background:#313244; }",
  ".ib-grid.dark .ib-lbl { color:#585b70 !important; }",
  ".ib-item.s { width:32px; height:32px; }",
  ".ib-item.m { width:52px; height:52px; gap:3px; }",
  ".ib-item.l { width:76px; height:76px; gap:5px; }",
  ".ib-item.s svg { width:15px; height:15px; }",
  ".ib-item.m svg { width:21px; height:21px; }",
  ".ib-item.l svg { width:30px; height:30px; }",
  ".ib-lbl { font-size:9px; color:var(--text-muted); max-width:100%; overflow:hidden; text-overflow:ellipsis; white-space:nowrap; text-align:center; line-height:1.3; display:none; padding:0 3px; }",
  ".ib-item.s .ib-lbl { display:none !important; }",
  ".ib-item.m.lbl .ib-lbl { display:block; max-width:50px; }",
  ".ib-item.l.lbl .ib-lbl { display:block; max-width:72px; font-size:10px; }",
  ".ib-item.m.lbl { height:64px; }",
  ".ib-item.l.lbl { height:92px; }",
  ".ib-badge { position:absolute; top:2px; right:2px; background:var(--interactive-accent); color:var(--text-on-accent); font-size:8px; min-width:13px; height:13px; border-radius:7px; display:flex; align-items:center; justify-content:center; padding:0 3px; line-height:1; font-weight:700; display:none; }",
  ".ib-item.has-badge .ib-badge { display:flex; }",
  ".ib-item.highlight { outline:2px solid var(--interactive-accent); outline-offset:1px; }",
  "@keyframes ib-pop { 0%{background:var(--interactive-accent);transform:scale(1.2);} 60%{transform:scale(1.04);} 100%{background:transparent;transform:scale(1);} }",
  ".ib-item.copied { animation:ib-pop 0.42s ease-out forwards; }",

  ".ib-toast { position:fixed; bottom:24px; left:50%; transform:translateX(-50%) translateY(14px); background:var(--background-primary); color:var(--text-normal); border:1px solid var(--background-modifier-border); box-shadow:0 4px 20px rgba(0,0,0,0.18); padding:7px 16px 7px 12px; border-radius:var(--radius-m); font-size:12px; opacity:0; pointer-events:none; transition:opacity 0.16s,transform 0.16s; z-index:9999; white-space:nowrap; display:flex; align-items:center; gap:8px; }",
  ".ib-toast.on { opacity:1; transform:translateX(-50%) translateY(0); }",
  ".ib-toast-icon { display:flex; align-items:center; }",
  ".ib-toast-icon svg { width:15px; height:15px; color:var(--interactive-accent); }",
  ".ib-toast b { color:var(--interactive-accent); }",

  ".ib-empty { width:100%; padding:52px 0; text-align:center; color:var(--text-muted); }",
  ".ib-empty svg { width:30px; height:30px; margin:0 auto 10px; display:block; opacity:0.35; }",
  ".ib-empty p { font-size:13px; margin:0; }",

  ".ib-recent { margin-top:10px; padding:10px 13px; background:var(--background-secondary); border-radius:var(--radius-m); border:1px solid var(--background-modifier-border); }",
  ".ib-rec-head { display:flex; align-items:center; justify-content:space-between; margin-bottom:7px; }",
  ".ib-rec-title { display:flex; align-items:center; gap:5px; font-size:11px; color:var(--text-muted); font-weight:500; letter-spacing:0.05em; text-transform:uppercase; }",
  ".ib-rec-title svg { width:12px; height:12px; }",
  ".ib-rec-clear { font-size:11px; color:var(--text-faint); background:none; border:none; cursor:pointer; padding:0; }",
  ".ib-rec-clear:hover { color:var(--text-muted); }",
  ".ib-rec-list { display:flex; flex-wrap:wrap; gap:5px; }",
  ".ib-tag { display:inline-flex; align-items:center; gap:5px; padding:3px 9px 3px 7px; border-radius:var(--radius-s); background:var(--background-primary); border:1px solid var(--background-modifier-border); font-size:11px; color:var(--text-normal); cursor:pointer; transition:all 0.1s; }",
  ".ib-tag:hover { background:var(--background-modifier-hover); border-color:var(--interactive-accent); }",
  ".ib-tag svg { width:13px; height:13px; flex-shrink:0; }",

  ".dv-sw-wrap { display:flex; flex-direction:column; gap:2px; }",
  ".dv-ml { font-size:11px; color:var(--text-faint); white-space:nowrap; }",
  ".dv-csel-wrap { display:inline-flex; align-items:stretch; position:relative; box-shadow:inset 0 0 0 1px var(--background-modifier-border); border-radius:4px; background:var(--background-primary); flex-shrink:0; min-width:80px; cursor:pointer; user-select:none; transition:box-shadow .12s,background .12s; }",
  ".dv-csel-wrap:hover { background:var(--background-modifier-hover); }",
  ".dv-csel-wrap--open, .dv-csel-wrap:focus { box-shadow:inset 0 0 0 1px var(--interactive-accent); outline:none; }",
  ".dv-csel-trigger { display:flex; align-items:center; justify-content:space-between; padding:4px 7px; gap:4px; width:100%; box-sizing:border-box; font-size:11px; color:var(--text-normal); min-height:26px; }",
  ".dv-csel-text { flex:1; overflow:hidden; text-overflow:ellipsis; white-space:nowrap; line-height:1.4; }",
  ".dv-csel-arrow { font-size:9px; color:var(--text-muted); flex-shrink:0; line-height:1; padding-bottom:1px; }",
  ".dv-dropdown-panel { position:fixed; background:var(--background-primary); border:1px solid var(--background-modifier-border); border-radius:4px; box-shadow:0 4px 16px rgba(0,0,0,.14); z-index:99999; max-height:260px; overflow-y:auto; overflow-x:hidden; box-sizing:border-box; }",
  ".dv-dropdown-option { padding:5px 10px; font-size:11px; color:var(--text-normal); cursor:pointer; white-space:nowrap; line-height:1.5; }",
  ".dv-dropdown-option:hover { background:var(--background-modifier-hover); }",
  ".dv-dropdown-option--active { background:var(--interactive-accent); color:var(--text-on-accent); }"
].join(" ");

dv.container.createEl("style", { text: css });

const root      = dv.container.createDiv("ib");
const allRaw    = Array.from(obsidian.getIconIds()).sort();
const lucideIds = allRaw.filter(function(id){ return id.startsWith("lucide-"); });
const otherIds  = allRaw.filter(function(id){ return !id.startsWith("lucide-"); });

let query     = "";
let category  = "all";
let size      = "m";
let darkBg    = true;
let showLabel = false;
let copyFmt   = "short";
let sortMode  = "az";
let recent    = [];
let copyCount = {};

const _ibPanels = [];

function createCustomSelect(parentEl, options, initValue, onChange, label) {
    const outer = parentEl.createEl("div", { cls: "dv-sw-wrap" });
    if (label) outer.createEl("div", { cls: "dv-ml", text: label });

    const wrap = outer.createEl("div", { cls: "dv-csel-wrap" });
    wrap.setAttribute("tabindex", "0");

    const trigger  = wrap.createEl("div", { cls: "dv-csel-trigger" });
    const trigText = trigger.createEl("span", { cls: "dv-csel-text" });
    trigger.createEl("span", { cls: "dv-csel-arrow", text: "▾" });

    const panel = document.body.createEl("div", { cls: "dv-dropdown-panel" });
    panel.style.display = "none";
    _ibPanels.push(panel);

    let currentValue = initValue;
    let isOpen = false;

    const optEls = options.map(([val, text]) => {
        const item = panel.createEl("div", { cls: "dv-dropdown-option", text });
        item.dataset.value = String(val);
        item.addEventListener("mousedown", e => e.preventDefault());
        item.addEventListener("click", () => {
            currentValue = val;
            refreshDisplay();
            closePanel();
            onChange(val);
        });
        return item;
    });

    function refreshDisplay() {
        const opt = options.find(o => String(o[0]) === String(currentValue));
        trigText.textContent = opt ? opt[1] : String(currentValue);
        optEls.forEach(el =>
            el.classList.toggle("dv-dropdown-option--active", el.dataset.value === String(currentValue))
        );
    }

    function positionPanel() {
        const rect = wrap.getBoundingClientRect();
        panel.style.visibility = "hidden";
        panel.style.display = "block";
        const panelW = Math.max(panel.scrollWidth, rect.width, 80);
        const panelH = Math.min(panel.scrollHeight, 260);
        panel.style.minWidth = panelW + "px";
        let left = rect.left;
        if (left + panelW > window.innerWidth - 6) left = Math.max(6, window.innerWidth - panelW - 6);
        panel.style.left = left + "px";
        const spaceBelow = window.innerHeight - rect.bottom - 4;
        const spaceAbove = rect.top - 4;
        if (spaceBelow >= panelH || spaceBelow >= spaceAbove) {
            panel.style.top    = (rect.bottom + 2) + "px";
            panel.style.bottom = "auto";
        } else {
            panel.style.top    = "auto";
            panel.style.bottom = (window.innerHeight - rect.top + 2) + "px";
        }
        panel.style.visibility = "";
    }

    function openPanel() {
        if (isOpen) return;
        isOpen = true;
        refreshDisplay();
        positionPanel();
        wrap.classList.add("dv-csel-wrap--open");
    }

    function closePanel() {
        if (!isOpen) return;
        isOpen = false;
        panel.style.display = "none";
        wrap.classList.remove("dv-csel-wrap--open");
    }

    trigger.addEventListener("click", () => { isOpen ? closePanel() : openPanel(); });
    document.addEventListener("click", e => {
        if (isOpen && !wrap.contains(e.target) && !panel.contains(e.target)) closePanel();
    });
    document.addEventListener("scroll", e => {
        if (isOpen && !panel.contains(e.target)) closePanel();
    }, true);
    wrap.addEventListener("keydown", e => {
        e.stopPropagation();
        if (e.key === "Enter" || e.key === " ") { e.preventDefault(); isOpen ? closePanel() : openPanel(); }
        if (e.key === "Escape") { closePanel(); wrap.blur(); }
        if (e.key === "ArrowDown") {
            e.preventDefault();
            const idx = options.findIndex(o => String(o[0]) === String(currentValue));
            if (idx < options.length - 1) { currentValue = options[idx + 1][0]; refreshDisplay(); onChange(currentValue); }
        }
        if (e.key === "ArrowUp") {
            e.preventDefault();
            const idx = options.findIndex(o => String(o[0]) === String(currentValue));
            if (idx > 0) { currentValue = options[idx - 1][0]; refreshDisplay(); onChange(currentValue); }
        }
    });

    refreshDisplay();
    return {
        el: outer,
        getValue: () => currentValue,
        setValue: v => { currentValue = v; refreshDisplay(); }
    };
}

const _ibCleanupObs = new MutationObserver(() => {
    if (!document.contains(root)) {
        _ibPanels.forEach(p => { try { p.remove(); } catch {} });
        _ibCleanupObs.disconnect();
    }
});
if (dv.container.parentNode) {
    _ibCleanupObs.observe(dv.container.parentNode, { childList: true });
}

const bar = root.createDiv("ib-bar");

const row1 = bar.createDiv("ib-row1");

const pfx = row1.createSpan("ib-ico-prefix");
obsidian.setIcon(pfx, "search");

const inp = row1.createEl("input", {
    cls: "ib-search",
    attr: { type:"text", placeholder:"搜索图标名称…", spellcheck:"false" }
});

const clrBtn = row1.createEl("button", { cls:"ib-clear", text:"✕" });
row1.createEl("span", { cls:"ib-kbd", text:"ESC 清空" });
row1.createDiv("ib-vsep");
const statsEl = row1.createDiv("ib-stats");

const row2  = bar.createDiv("ib-row2");
const tabs  = row2.createDiv("ib-tabs");

function mkTab(key, label) {
    const t = tabs.createEl("button", {
        cls: "ib-tab" + (key === category ? " on" : ""),
        text: label
    });
    t.onclick = function() {
        category = key;
        tabs.querySelectorAll(".ib-tab").forEach(function(b){ b.removeClass("on"); });
        t.addClass("on");
        renderGrid();
    };
}
mkTab("all",    "全部 " + allRaw.length);
mkTab("lucide", "Lucide " + lucideIds.length);
mkTab("other",  "其他 " + otherIds.length);

const row3 = bar.createDiv("ib-row3");

const sgGrp = row3.createDiv("ib-cg");
sgGrp.createSpan({ cls:"ib-cl", text:"尺寸" });
["s","m","l"].forEach(function(s_) {
    const map = { s:"小", m:"中", l:"大" };
    const b = sgGrp.createEl("button", { cls:"ib-btn"+(s_===size?" on":""), text:map[s_] });
    b.onclick = function() {
        size = s_;
        sgGrp.querySelectorAll(".ib-btn").forEach(function(x){ x.removeClass("on"); });
        b.addClass("on");
        renderGrid();
    };
});

row3.createDiv("ib-divider");

const lblGrp = row3.createDiv("ib-cg");
lblGrp.createSpan({ cls:"ib-cl", text:"名称标签" });
const lblBtn = lblGrp.createEl("button", { cls:"ib-btn", text:"隐藏" });
lblBtn.onclick = function() {
    showLabel = !showLabel;
    lblBtn.textContent = showLabel ? "显示" : "隐藏";
    lblBtn.toggleClass("on", showLabel);
    renderGrid();
};

row3.createDiv("ib-divider");

const darkGrp = row3.createDiv("ib-cg");
darkGrp.createSpan({ cls:"ib-cl", text:"背景" });
const darkBtn = darkGrp.createEl("button", { cls:"ib-btn on", text:"深色" });
darkBtn.onclick = function() {
    darkBg = !darkBg;
    darkBtn.textContent = darkBg ? "深色" : "浅色";
    darkBtn.toggleClass("on", darkBg);
    grid.toggleClass("dark", darkBg);
};

row3.createDiv("ib-divider");

createCustomSelect(row3,
    [["az", "A → Z"], ["za", "Z → A"], ["count", "复制次数↓"]],
    "az",
    v => { sortMode = v; renderGrid(); },
    "排序"
);

row3.createDiv("ib-divider");

createCustomSelect(row3,
    [["full", "完整 ID"], ["short", "短名（去前缀）"], ["call", "setIcon() 调用"]],
    "short",
    v => { copyFmt = v; },
    "复制为"
);

row3.createDiv("ib-divider");

const randGrp = row3.createDiv("ib-cg");
const randBtn = randGrp.createEl("button", { cls:"ib-btn ib-rand", text:"🎲 随机" });
randBtn.onclick = function() {
    const list = allRaw;
    const id = list[Math.floor(Math.random() * list.length)];
    inp.value = id;
    query = id;
    clrBtn.addClass("on");
    renderGrid();
    setTimeout(function() {
        const found = grid.querySelector(".ib-item");
        if (found) {
            found.addClass("highlight");
            setTimeout(function(){ found.removeClass("highlight"); }, 1200);
        }
    }, 50);
};

const grid = root.createDiv("ib-grid");

const recPanel = root.createDiv("ib-recent");
recPanel.style.display = "none";

const recHead  = recPanel.createDiv("ib-rec-head");
const recTitle = recHead.createDiv("ib-rec-title");
const recIco   = recTitle.createSpan();
obsidian.setIcon(recIco, "clock");
recTitle.createSpan({ text:"最近复制" });

const recClearBtn = recHead.createEl("button", { cls:"ib-rec-clear", text:"清空" });
recClearBtn.onclick = function() { recent = []; renderRecent(); };

const recList = recPanel.createDiv("ib-rec-list");

const toast = document.body.createDiv("ib-toast");
let toastTmr = null;

function showToast(id, text) {
    toast.empty();
    const icoSpan = toast.createSpan("ib-toast-icon");
    obsidian.setIcon(icoSpan, id);
    toast.appendText("已复制 ");
    toast.createEl("b", { text: text });
    toast.addClass("on");
    clearTimeout(toastTmr);
    toastTmr = setTimeout(function(){ toast.removeClass("on"); }, 2000);
}

function fmtText(id) {
    if (copyFmt === "short" && id.startsWith("lucide-")) return id.slice(7);
    if (copyFmt === "call")  return 'setIcon(el, "' + id + '")';
    return id;
}

function doCopy(id, el) {
    const text = fmtText(id);
    navigator.clipboard.writeText(text).then(function() {
        showToast(id, text);
        el.addClass("copied");
        setTimeout(function(){ el.removeClass("copied"); }, 460);
        copyCount[id] = (copyCount[id] || 0) + 1;
        var badge = el.querySelector(".ib-badge");
        if (badge) {
            badge.textContent = String(copyCount[id]);
            el.addClass("has-badge");
        }
        recent = [id].concat(recent.filter(function(x){ return x !== id; })).slice(0, 12);
        renderRecent();
    });
}

function renderRecent() {
    recList.empty();
    if (recent.length === 0) { recPanel.style.display = "none"; return; }
    recPanel.style.display = "";
    recent.forEach(function(id) {
        const tag = recList.createDiv("ib-tag");
        const ico = tag.createSpan();
        obsidian.setIcon(ico, id);
        tag.createSpan({ text: fmtText(id) });
        tag.onclick = function() {
            const text = fmtText(id);
            navigator.clipboard.writeText(text);
            showToast(id, text);
        };
        obsidian.setTooltip(tag, "点击再次复制：" + fmtText(id), { delay: 150 });
    });
}

function renderGrid() {
    grid.empty();

    const szCfg = { s:{w:32,gap:3}, m:{w:52,gap:4}, l:{w:76,gap:6} };
    const sz = szCfg[size];
    grid.style.display             = "grid";
    grid.style.gridTemplateColumns = "repeat(auto-fill, " + sz.w + "px)";
    grid.style.justifyContent      = "space-evenly";
    grid.style.columnGap           = "0";
    grid.style.rowGap              = sz.gap + "px";
    grid.style.paddingTop          = sz.gap + "px";
    grid.style.paddingBottom       = sz.gap + "px";
    grid.style.paddingLeft         = "0";
    grid.style.paddingRight        = "0";

    let src;
    if      (category === "lucide") src = lucideIds.slice();
    else if (category === "other")  src = otherIds.slice();
    else                            src = allRaw.slice();

    const q = query.trim().toLowerCase();
    let filtered = q ? src.filter(function(id){ return id.toLowerCase().includes(q); }) : src;

    if (sortMode === "za") {
        filtered = filtered.slice().reverse();
    } else if (sortMode === "count") {
        filtered = filtered.slice().sort(function(a, b) {
            return (copyCount[b] || 0) - (copyCount[a] || 0);
        });
    }

    statsEl.empty();
    if (q) {
        statsEl.appendText("找到 ");
        statsEl.createEl("b", { text: String(filtered.length) });
        statsEl.appendText(" / " + src.length);
    } else {
        statsEl.createEl("b", { text: String(filtered.length) });
        statsEl.appendText(" 个图标");
    }

    if (filtered.length === 0) {
        const emp = grid.createDiv("ib-empty");
        const eico = emp.createDiv();
        obsidian.setIcon(eico, "search-x");
        emp.createEl("p", { text: "没有找到包含 [" + query + "] 的图标" });
        return;
    }

    const szCls  = size;
    const lblCls = showLabel ? " lbl" : "";
    const recentSet = new Set(recent);

    filtered.forEach(function(id) {
        const item = grid.createDiv("ib-item " + szCls + lblCls);
        if (recentSet.has(id)) item.addClass("highlight");
        const iSpan = item.createSpan();
        obsidian.setIcon(iSpan, id);
        const displayName = (id.startsWith("lucide-")) ? id.slice(7) : id;
        item.createSpan({ cls:"ib-lbl", text: displayName });
        const badge = item.createSpan({ cls:"ib-badge" });
        if (copyCount[id]) {
            badge.textContent = String(copyCount[id]);
            item.addClass("has-badge");
        }
        obsidian.setTooltip(item, id + (copyCount[id] ? "  （已复制 " + copyCount[id] + " 次）" : ""), { delay: 60 });
        item.onclick = function() { doCopy(id, item); };
    });

    if (darkBg) grid.addClass("dark");
}

inp.addEventListener("keydown", function(e) {
    if (e.key === "Escape") {
        inp.value = "";
        query = "";
        clrBtn.removeClass("on");
        renderGrid();
    }
});

inp.addEventListener("input", function() {
    query = inp.value;
    clrBtn.toggleClass("on", query.length > 0);
    renderGrid();
});

clrBtn.onclick = function() {
    inp.value = "";
    query = "";
    clrBtn.removeClass("on");
    inp.focus();
    renderGrid();
};

renderGrid();
```
