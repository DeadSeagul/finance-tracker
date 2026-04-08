import { useState, useRef, useEffect, useCallback } from "react";

const MONTHS = ["January","February","March","April","May","June","July","August","September","October","November","December"];
const today = new Date();

function formatCurrency(n) {
  return new Intl.NumberFormat("en-US", { style: "currency", currency: "USD" }).format(n || 0);
}
function getKey(profile, year, month) { return `ft:${profile}:${year}-${month}`; }
function getProfilePrefix(profile) { return `ft:${profile}:`; }

// ── Storage helpers (localStorage — works everywhere) ────────────────────────
async function storeMonth(key, data) {
  try { localStorage.setItem(key, JSON.stringify(data)); } catch {}
}
async function loadMonth(key) {
  try {
    const r = localStorage.getItem(key);
    return r ? JSON.parse(r) : null;
  } catch { return null; }
}
async function listMonthKeys(prefix) {
  try {
    return Object.keys(localStorage).filter(k => k.startsWith(prefix));
  } catch { return []; }
}
async function storeProfileNames(names) {
  try { localStorage.setItem("ft:profiles", JSON.stringify(names)); } catch {}
}
async function loadProfileNames() {
  try {
    const r = localStorage.getItem("ft:profiles");
    return r ? JSON.parse(r) : ["Person 1","Person 2"];
  } catch { return ["Person 1","Person 2"]; }
}

// ── CSV Parser ────────────────────────────────────────────────────────────────
function splitCSVLine(line) {
  const result = []; let cur = "", inQ = false;
  for (const c of line) {
    if (c === '"') inQ = !inQ;
    else if (c === ',' && !inQ) { result.push(cur); cur = ""; }
    else cur += c;
  }
  result.push(cur); return result;
}
function parseDate(str) {
  if (!str) return null;
  const s = str.trim().replace(/['"]/g,"");
  let d = new Date(s);
  if (!isNaN(d.getTime())) return d;
  const m = s.match(/^(\d{1,2})[\/\-](\d{1,2})[\/\-](\d{2,4})$/);
  if (m) {
    const y = m[3].length===2 ? 2000+parseInt(m[3]) : parseInt(m[3]);
    d = new Date(y, parseInt(m[1])-1, parseInt(m[2]));
    if (!isNaN(d.getTime())) return d;
  }
  return null;
}
function parseCSV(text) {
  const lines = text.trim().split(/\r?\n/);
  if (lines.length < 2) return [];
  let hi = 0;
  for (let i = 0; i < Math.min(5,lines.length); i++)
    if (/date|description|amount|debit|credit|memo/i.test(lines[i])) { hi=i; break; }
  const headers = splitCSVLine(lines[hi]).map(h=>h.toLowerCase().trim().replace(/['"]/g,""));
  const rows = [];
  for (let i=hi+1; i<lines.length; i++) {
    if (!lines[i].trim()) continue;
    const cols = splitCSVLine(lines[i]);
    const obj = {}; headers.forEach((h,j)=>{ obj[h]=(cols[j]||"").trim().replace(/^"|"$/g,""); });
    rows.push(obj);
  }
  const dc = headers.find(h=>/^(date|transaction.?date|posted.?date)$/i.test(h))||headers.find(h=>/date/i.test(h));
  const ec = headers.find(h=>/^(description|memo|name|payee|narrative)$/i.test(h))||headers.find(h=>/desc|memo|name/i.test(h));
  const ac = headers.find(h=>/^(amount|transaction.?amount)$/i.test(h));
  const dbc = headers.find(h=>/^(debit|withdrawal|dr)$/i.test(h));
  const crc = headers.find(h=>/^(credit|deposit|cr)$/i.test(h));
  return rows.map(row=>{
    const date = dc ? parseDate(row[dc]) : null;
    const desc = (ec ? row[ec] : Object.values(row).find(v=>v&&isNaN(parseFloat(v.replace(/[$,]/g,"")))) || "Unknown").trim();
    let amount = 0;
    if (ac) amount = parseFloat(row[ac].replace(/[$, ]/g,""))||0;
    else if (dbc||crc) { const d=parseFloat((row[dbc]||"0").replace(/[$, ]/g,""))||0; const c=parseFloat((row[crc]||"0").replace(/[$, ]/g,""))||0; amount=c-d; }
    return {date,desc,amount};
  }).filter(r=>r.desc);
}

function detectMonth(rows) {
  const counts = {};
  rows.forEach(r=>{ if(r.date){const k=`${r.date.getFullYear()}-${r.date.getMonth()}`; counts[k]=(counts[k]||0)+1;} });
  const top = Object.entries(counts).sort((a,b)=>b[1]-a[1])[0]?.[0];
  if (!top) return null;
  const [y,m] = top.split("-").map(Number);
  return {month:m,year:y};
}
function autoTag(rows) {
  const t={};
  rows.forEach((r,i)=>{ t[i]=r.amount>0?"income":r.amount<0?"bill":"ignore"; });
  return t;
}

// ── Main App ──────────────────────────────────────────────────────────────────
export default function App() {
  const [ready, setReady]           = useState(false);
  const [profile, setProfile]       = useState(0); // 0 or 1
  const [profileNames, setProfileNames] = useState(["Person 1","Person 2"]);
  const [editingName, setEditingName]   = useState(null); // null | 0 | 1
  const [nameInput, setNameInput]       = useState("");
  const [allData, setAllData]   = useState({});
  const [month, setMonth]       = useState(today.getMonth());
  const [year,  setYear]        = useState(today.getFullYear());
  const [activeTab, setActiveTab] = useState("overview");

  const [showIncomeModal, setShowIncomeModal] = useState(false);
  const [incomeForm, setIncomeForm] = useState({label:"",amount:""});
  const [editIncomeIdx, setEditIncomeIdx] = useState(null);

  const [showBillModal, setShowBillModal] = useState(false);
  const [billForm, setBillForm] = useState({label:"",amount:"",due:"",paid:false});
  const [editBillIdx, setEditBillIdx] = useState(null);

  const [showCopyModal, setShowCopyModal] = useState(false);
  const [copyTargets, setCopyTargets] = useState([]);
  const [copyWhat, setCopyWhat] = useState({income:true,bills:true});
  const [copySuccess, setCopySuccess] = useState(false);

  const [importStep,  setImportStep]  = useState(null);
  const [importRows,  setImportRows]  = useState([]);
  const [importTags,  setImportTags]  = useState({});
  const [importError, setImportError] = useState("");
  const [importMonth, setImportMonth] = useState(null);
  const [filterMonth, setFilterMonth] = useState(true);
  const [importMode,  setImportMode]  = useState("csv");
  const [loadingMsg,  setLoadingMsg]  = useState("");
  const csvRef     = useRef();
  const pdfRef     = useRef();
  const jsonImport = useRef();

  // ── Export: show JSON in a copyable modal ──
  const [showBackup, setShowBackup]   = useState(false);
  const [showRestore, setShowRestore] = useState(false);
  const [restorePaste, setRestorePaste] = useState("");
  const backupText = showBackup ? JSON.stringify(allData, null, 2) : "";

  async function handleRestore() {
    try {
      const data = JSON.parse(restorePaste);
      const isValid = typeof data === "object" && !Array.isArray(data) && Object.values(data).every(
        v => v && typeof v === "object" && Array.isArray(v.income) && Array.isArray(v.bills)
      );
      if (!isValid) { alert("That doesn't look like a Finance Tracker backup."); return; }
      for (const [k, v] of Object.entries(data)) await storeMonth(k, v);
      setAllData(data);
      setShowRestore(false); setRestorePaste("");
      alert("Restored " + Object.keys(data).length + " months of data!");
    } catch { alert("Could not parse that — make sure you copied the full backup text."); }
  }

  function copyBackup() {
    navigator.clipboard.writeText(backupText).then(()=>alert("Copied to clipboard!")).catch(()=>{
      const ta = document.getElementById("backup-ta");
      if (ta) { ta.select(); document.execCommand("copy"); alert("Copied!"); }
    });
  }

  // ── Import a JSON backup to restore all data ──
  async function handleJSONImport(e) {
    const file = e.target.files[0]; if (!file) return;
    const reader = new FileReader();
    reader.onload = async ev => {
      try {
        const data = JSON.parse(ev.target.result);
        const isValid = typeof data === "object" && !Array.isArray(data) && Object.values(data).every(
          v => v && typeof v === "object" && Array.isArray(v.income) && Array.isArray(v.bills)
        );
        if (!isValid) { alert("That doesn't look like a Finance Tracker backup file."); return; }
        for (const [k, v] of Object.entries(data)) await storeMonth(k, v);
        setAllData(data);
        alert("Restored " + Object.keys(data).length + " months of data!");
      } catch { alert("Could not read that file."); }
    };
    reader.readAsText(file); e.target.value = "";
  }

  // ── Boot: load profile names + all months for current profile ──
  useEffect(()=>{
    (async()=>{
      const names = await loadProfileNames();
      setProfileNames(names);
      const keys = await listMonthKeys(getProfilePrefix(0));
      const loaded = {};
      for (const k of keys) {
        const d = await loadMonth(k);
        if (d) loaded[k] = d;
      }
      setAllData(loaded);
      setReady(true);
    })();
  },[]);

  // ── Switch profile: load that profile's data ──
  async function switchProfile(idx) {
    setProfile(idx);
    const keys = await listMonthKeys(getProfilePrefix(idx));
    const loaded = {};
    for (const k of keys) {
      const d = await loadMonth(k);
      if (d) loaded[k] = d;
    }
    setAllData(loaded);
    setActiveTab("overview");
  }

  // ── Rename profile ──
  async function saveProfileName() {
    if (editingName === null) return;
    const updated = [...profileNames];
    updated[editingName] = nameInput.trim() || profileNames[editingName];
    setProfileNames(updated);
    await storeProfileNames(updated);
    setEditingName(null); setNameInput("");
  }

  // ── Save code: compact base64 of all data ────────────────────────────────
  const [showSaveCode, setShowSaveCode]   = useState(false);
  const [showLoadCode, setShowLoadCode]   = useState(false);
  const [loadCodeText, setLoadCodeText]   = useState("");
  const [loadCodeErr,  setLoadCodeErr]    = useState("");
  const [copied,       setCopied]         = useState(false);

  function getSaveCode() {
    try { return btoa(unescape(encodeURIComponent(JSON.stringify({profile, allData})))); }
    catch { return ""; }
  }

  function copySaveCode() {
    const code = getSaveCode();
    if (!code) return;
    navigator.clipboard.writeText(code).catch(()=>{});
    setCopied(true); setTimeout(()=>setCopied(false), 2000);
  }

  async function applyLoadCode() {
    try {
      const json    = decodeURIComponent(escape(atob(loadCodeText.trim())));
      const parsed  = JSON.parse(json);
      // Support both new {profile, allData} format and old plain data format
      const savedProfile = parsed.profile ?? 0;
      const data = parsed.allData ?? parsed;
      const ok = typeof data==="object" && !Array.isArray(data) &&
                 Object.values(data).every(v=>v&&Array.isArray(v.income)&&Array.isArray(v.bills));
      if (!ok) { setLoadCodeErr("That doesn't look right — copy the full save code."); return; }
      for (const [k,v] of Object.entries(data)) await storeMonth(k,v);
      setAllData(data);
      if (savedProfile !== profile) await switchProfile(savedProfile);
      setShowLoadCode(false); setLoadCodeText(""); setLoadCodeErr("");
    } catch { setLoadCodeErr("Couldn't read that code. Make sure you copied it completely."); }
  }

  const key       = getKey(profile, year, month);
  const monthData = allData[key] || {income:[],bills:[]};
  const totalIncome = monthData.income.reduce((s,i)=>s+(parseFloat(i.amount)||0),0);
  const totalBills  = monthData.bills.reduce((s,b)=>s+(parseFloat(b.amount)||0),0);
  const totalPaid   = monthData.bills.filter(b=>b.paid).reduce((s,b)=>s+(parseFloat(b.amount)||0),0);
  const totalUnpaid = totalBills - totalPaid;
  const remaining   = totalIncome - totalBills;
  const paidPct     = totalBills>0 ? Math.round((totalPaid/totalBills)*100) : 0;

  async function updateMonthData(newData) {
    const updated = {...allData,[key]:newData};
    setAllData(updated);
    await storeMonth(key, newData);
  }

  // Income
  async function saveIncome() {
    if (!incomeForm.label||!incomeForm.amount) return;
    const items = [...(monthData.income||[])];
    if (editIncomeIdx!==null) items[editIncomeIdx]={...incomeForm}; else items.push({...incomeForm});
    await updateMonthData({...monthData,income:items});
    setShowIncomeModal(false); setIncomeForm({label:"",amount:""}); setEditIncomeIdx(null);
  }
  async function deleteIncome(idx) { await updateMonthData({...monthData,income:monthData.income.filter((_,i)=>i!==idx)}); }

  // Bills
  async function saveBill() {
    if (!billForm.label||!billForm.amount) return;
    const items = [...(monthData.bills||[])];
    if (editBillIdx!==null) items[editBillIdx]={...billForm}; else items.push({...billForm});
    await updateMonthData({...monthData,bills:items});
    setShowBillModal(false); setBillForm({label:"",amount:"",due:"",paid:false}); setEditBillIdx(null);
  }
  async function deleteBill(idx) { await updateMonthData({...monthData,bills:monthData.bills.filter((_,i)=>i!==idx)}); }
  async function togglePaid(idx) { await updateMonthData({...monthData,bills:monthData.bills.map((b,i)=>i===idx?{...b,paid:!b.paid}:b)}); }

  // Copy
  function getCopyOptions() {
    return Array.from({length:23},(_,i)=>i-11).filter(d=>d!==0).map(delta=>{
      let m=month+delta,y=year;
      while(m<0){m+=12;y--;} while(m>11){m-=12;y++;}
      const k=getKey(profile,y,m);
      return {key:k,label:`${MONTHS[m]} ${y}`,hasData:!!(allData[k]?.income?.length||allData[k]?.bills?.length)};
    });
  }
  async function executeCopy() {
    if (!copyTargets.length) return;
    let updated={...allData};
    for (const tk of copyTargets) {
      const ex=updated[tk]||{income:[],bills:[]};
      const nd={income:copyWhat.income?monthData.income.map(i=>({...i})):ex.income,bills:copyWhat.bills?monthData.bills.map(b=>({...b,paid:false})):ex.bills};
      updated[tk]=nd; await storeMonth(tk,nd);
    }
    setAllData(updated); setCopySuccess(true);
    setTimeout(()=>{setCopySuccess(false);setShowCopyModal(false);setCopyTargets([]);},1400);
  }

  // CSV
  function handleCSVFile(e) {
    const file=e.target.files[0]; if(!file) return;
    setImportError(""); setImportMode("csv");
    const reader=new FileReader();
    reader.onload=ev=>{
      try {
        const rows=parseCSV(ev.target.result);
        if(!rows.length){setImportError("No transactions found.");return;}
        setImportRows(rows); setImportTags(autoTag(rows)); setImportMonth(detectMonth(rows)); setImportStep("review");
      } catch { setImportError("Could not parse this file."); }
    };
    reader.readAsText(file); e.target.value="";
  }

  // PDF — user shares PDF in chat, I load it. Button kept for future.
  function handlePDFFile(e) {
    const file=e.target.files[0]; if(!file) return;
    setImportError("Share your Wells Fargo PDF directly in this chat and I'll add it for you instantly — that's the most reliable method."); 
    e.target.value="";
  }

  function getFilteredRows() {
    const idx=importRows.map((r,i)=>({...r,idx:i}));
    if (!filterMonth||!importMonth) return idx;
    return idx.filter(r=>r.date&&r.date.getMonth()===importMonth.month&&r.date.getFullYear()===importMonth.year);
  }
  async function commitImport() {
    const filtered=getFilteredRows();
    const tMonth=importMonth?.month??month, tYear=importMonth?.year??year;
    const tKey=getKey(profile,tYear,tMonth);
    const existing=allData[tKey]||{income:[],bills:[]};
    const newIncome=[...existing.income], newBills=[...existing.bills];
    filtered.forEach(r=>{
      const tag=importTags[r.idx];
      if(tag==="income") newIncome.push({label:r.desc,amount:String(Math.abs(r.amount))});
      else if(tag==="bill") newBills.push({label:r.desc,amount:String(Math.abs(r.amount)),due:"",paid:true});
    });
    const nd={income:newIncome,bills:newBills};
    const updated={...allData,[tKey]:nd};
    setAllData(updated); await storeMonth(tKey,nd);
    setMonth(tMonth); setYear(tYear); setImportStep("done");
    setTimeout(()=>{setImportStep(null);setImportRows([]);setImportTags({});},2000);
  }

  const filteredRows = importStep==="review" ? getFilteredRows() : [];
  const incomeRows   = filteredRows.filter(r=>importTags[r.idx]==="income");
  const billRows     = filteredRows.filter(r=>importTags[r.idx]==="bill");
  const ignoredRows  = filteredRows.filter(r=>importTags[r.idx]==="ignore");

  // ── Styles ────────────────────────────────────────────────────────────────
  const css = `
    @import url('https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=DM+Mono:wght@300;400;500&display=swap');
    *{box-sizing:border-box;margin:0;padding:0;}
    ::-webkit-scrollbar{width:6px;}::-webkit-scrollbar-track{background:#1a1a20;}::-webkit-scrollbar-thumb{background:#3a3a45;border-radius:3px;}
    .tab-btn{background:none;border:none;cursor:pointer;font-family:'DM Mono',monospace;font-size:12px;letter-spacing:.12em;text-transform:uppercase;padding:10px 20px;border-radius:4px;transition:all .2s;}
    .tab-btn.active{background:#c9a84c;color:#0d0d10;}.tab-btn:not(.active){color:#888;}.tab-btn:not(.active):hover{color:#f0ede6;}
    .card{background:#16161c;border:1px solid #2a2a33;border-radius:12px;padding:24px;}
    .icon-btn{background:none;border:none;cursor:pointer;color:#666;transition:color .2s;padding:4px 6px;border-radius:4px;font-size:16px;}
    .icon-btn:hover{color:#c9a84c;}.icon-btn.danger:hover{color:#e05252;}
    .add-btn{display:flex;align-items:center;gap:8px;background:none;border:1px dashed #3a3a45;border-radius:8px;color:#666;cursor:pointer;padding:12px 16px;width:100%;font-family:'DM Mono',monospace;font-size:13px;transition:all .2s;margin-top:10px;}
    .add-btn:hover{border-color:#c9a84c;color:#c9a84c;}
    .overlay{position:fixed;inset:0;background:rgba(0,0,0,.82);display:flex;align-items:center;justify-content:center;z-index:100;padding:16px;}
    .modal{background:#1a1a22;border:1px solid #2a2a38;border-radius:16px;padding:28px;width:100%;max-width:420px;}
    .modal input{width:100%;background:#0d0d10;border:1px solid #2a2a38;border-radius:8px;color:#f0ede6;font-family:'DM Mono',monospace;font-size:14px;padding:10px 14px;outline:none;transition:border .2s;}
    .modal input:focus{border-color:#c9a84c;}
    .modal label{font-family:'DM Mono',monospace;font-size:11px;letter-spacing:.1em;text-transform:uppercase;color:#888;display:block;margin-bottom:6px;}
    .btn-save{background:#c9a84c;color:#0d0d10;border:none;border-radius:8px;cursor:pointer;font-family:'DM Mono',monospace;font-size:13px;font-weight:500;letter-spacing:.05em;padding:11px 24px;transition:opacity .2s;}
    .btn-save:hover{opacity:.85;}
    .btn-cancel{background:none;border:1px solid #2a2a38;border-radius:8px;color:#888;cursor:pointer;font-family:'DM Mono',monospace;font-size:13px;padding:11px 20px;transition:all .2s;}
    .btn-cancel:hover{border-color:#666;color:#f0ede6;}
    .checkbox{width:18px;height:18px;accent-color:#c9a84c;cursor:pointer;}
    .pill{font-family:'DM Mono',monospace;font-size:11px;padding:3px 10px;border-radius:20px;letter-spacing:.05em;}
    .pill.paid{background:#1a3a25;color:#5ec97e;}.pill.unpaid{background:#3a1a1a;color:#e07070;}
    .nav-arrow{background:#1e1e28;border:1px solid #2a2a38;border-radius:8px;color:#aaa;cursor:pointer;font-size:18px;width:36px;height:36px;display:flex;align-items:center;justify-content:center;transition:all .2s;}
    .nav-arrow:hover{background:#2a2a38;color:#f0ede6;}
    .progress-bar{height:6px;background:#2a2a38;border-radius:3px;overflow:hidden;margin-top:8px;}
    .progress-fill{height:100%;background:linear-gradient(90deg,#c9a84c,#e8cc7a);border-radius:3px;transition:width .5s ease;}
    .row-item{display:flex;align-items:center;gap:12px;padding:12px 14px;border-radius:8px;transition:background .15s;}
    .row-item:hover{background:#1e1e28;}
    .stat-label{font-family:'DM Mono',monospace;font-size:11px;letter-spacing:.12em;text-transform:uppercase;color:#666;margin-bottom:6px;}
    select{background:#1a1a22;border:1px solid #2a2a38;border-radius:8px;color:#f0ede6;font-family:'DM Mono',monospace;font-size:13px;padding:8px 12px;cursor:pointer;outline:none;}
    select:focus{border-color:#c9a84c;}
    .copy-btn{display:flex;align-items:center;gap:6px;background:#1e1e28;border:1px solid #2a2a38;border-radius:8px;color:#aaa;cursor:pointer;font-family:'DM Mono',monospace;font-size:12px;letter-spacing:.08em;padding:8px 14px;transition:all .2s;}
    .copy-btn:hover{border-color:#c9a84c;color:#c9a84c;}
    .copy-target{display:flex;align-items:center;gap:10px;padding:8px 12px;border-radius:8px;cursor:pointer;transition:background .15s;border:1px solid transparent;}
    .copy-target:hover{background:#1e1e28;}.copy-target.selected{background:#1e2a1a;border-color:#3a6040;}
    .copy-grid{display:grid;grid-template-columns:1fr 1fr;gap:6px;max-height:280px;overflow-y:auto;margin:4px 0;}
    .what-toggle{display:flex;align-items:center;gap:8px;padding:8px 12px;border-radius:8px;border:1px solid #2a2a38;cursor:pointer;transition:all .2s;font-family:'DM Mono',monospace;font-size:12px;background:none;}
    .what-toggle.on{border-color:#c9a84c;background:#1e1e14;color:#c9a84c;}.what-toggle:not(.on){color:#666;}
    .success-flash{background:#1a3a25;border:1px solid #3a6040;color:#5ec97e;display:flex;align-items:center;justify-content:center;gap:8px;padding:14px;border-radius:10px;margin-top:8px;font-family:'DM Mono',monospace;font-size:14px;}
    .import-btn{display:flex;align-items:center;gap:6px;background:#1a2a1e;border:1px solid #2a4a32;border-radius:8px;color:#5ec97e;cursor:pointer;font-family:'DM Mono',monospace;font-size:12px;letter-spacing:.08em;padding:8px 14px;transition:all .2s;}
    .import-btn:hover{background:#1e3a24;border-color:#5ec97e;}
    .pick-card{border:2px dashed #2a2a38;border-radius:12px;padding:28px 20px;text-align:center;cursor:pointer;transition:all .2s;}
    .pick-card:hover,.pick-card.csv-card:hover{border-color:#5ec97e;background:#0f1f12;}
    .big-modal{background:#1a1a22;border:1px solid #2a2a38;border-radius:16px;width:100%;max-width:740px;max-height:90vh;display:flex;flex-direction:column;overflow:hidden;}
    .bm-header{padding:24px 28px 16px;border-bottom:1px solid #2a2a38;flex-shrink:0;}
    .bm-body{overflow-y:auto;flex:1;padding:0 28px;}
    .bm-footer{padding:16px 28px;border-top:1px solid #2a2a38;flex-shrink:0;display:flex;gap:10px;justify-content:space-between;align-items:center;flex-wrap:wrap;}
    .tx-row{display:grid;grid-template-columns:1fr auto auto;align-items:center;gap:12px;padding:10px 4px;border-bottom:1px solid #1e1e28;}
    .tx-row:last-child{border-bottom:none;}
    .tag-btn{font-family:'DM Mono',monospace;font-size:11px;letter-spacing:.05em;padding:4px 10px;border-radius:20px;border:1px solid;cursor:pointer;transition:all .15s;white-space:nowrap;background:none;}
    .tag-btn.income-on{background:#1a3a25;border-color:#3a6040;color:#5ec97e;}
    .tag-btn.income-off{border-color:#2a2a38;color:#444;}
    .tag-btn.bill-on{background:#3a1a1a;border-color:#6a3030;color:#e07070;}
    .tag-btn.bill-off{border-color:#2a2a38;color:#444;}
    .tag-btn.ignore-on{background:#2a2a38;border-color:#3a3a48;color:#888;}
    .tag-btn.ignore-off{border-color:#1e1e28;color:#333;}
    .sum-bar{display:flex;gap:12px;padding:10px 28px;border-bottom:1px solid #2a2a38;flex-shrink:0;flex-wrap:wrap;background:#13131a;}
    .chip{font-family:'DM Mono',monospace;font-size:12px;padding:4px 12px;border-radius:20px;}
    .toggle-row{display:flex;align-items:center;gap:8px;cursor:pointer;font-family:'DM Mono',monospace;font-size:12px;color:#777;margin-top:12px;user-select:none;}
    .notice{background:#1a2a1e;border:1px solid #2a4a32;border-radius:10px;padding:12px 16px;font-family:'DM Mono',monospace;font-size:12px;color:#5ec97e;display:flex;align-items:center;gap:10px;margin-bottom:16px;}
    .loading-box{background:#1a1a22;border:1px solid #2a2a38;border-radius:16px;padding:48px 56px;text-align:center;}
    .spinner{width:40px;height:40px;border:3px solid #2a2a38;border-top-color:#c9a84c;border-radius:50%;animation:spin .9s linear infinite;margin:0 auto 20px;}
    @keyframes spin{to{transform:rotate(360deg);}}
  `;

  if (!ready) return (
    <div style={{minHeight:"100vh",background:"#0d0d10",display:"flex",alignItems:"center",justifyContent:"center"}}>
      <style>{css}</style>
      <div className="loading-box"><div className="spinner"/><div style={{fontFamily:"'DM Mono',monospace",fontSize:13,color:"#666"}}>Loading your data…</div></div>
    </div>
  );

  return (
    <div style={{minHeight:"100vh",background:"#0d0d10",color:"#f0ede6",fontFamily:"'DM Serif Display',Georgia,serif"}}>
      <style>{css}</style>

      {/* Header */}
      <div style={{borderBottom:"1px solid #1e1e28",padding:"20px 24px",display:"flex",alignItems:"center",justifyContent:"space-between",flexWrap:"wrap",gap:12}}>
        <div style={{display:"flex",flexDirection:"column",gap:8}}>
          <div style={{fontFamily:"'DM Mono',monospace",fontSize:11,letterSpacing:".2em",textTransform:"uppercase",color:"#c9a84c"}}>Finance Tracker</div>
          <div style={{display:"flex",gap:6}}>
            {profileNames.map((name,idx)=>(
              <div key={idx} style={{display:"flex",alignItems:"center",gap:0}}>
                {editingName===idx ? (
                  <div style={{display:"flex",alignItems:"center",gap:4}}>
                    <input
                      autoFocus
                      value={nameInput}
                      onChange={e=>setNameInput(e.target.value)}
                      onKeyDown={e=>{if(e.key==="Enter")saveProfileName();if(e.key==="Escape"){setEditingName(null);setNameInput("");}}}
                      style={{background:"#1e1e28",border:"1px solid #c9a84c",borderRadius:6,color:"#f0ede6",fontFamily:"'DM Mono',monospace",fontSize:12,padding:"4px 8px",width:100,outline:"none"}}
                    />
                    <button onClick={saveProfileName} style={{background:"#c9a84c",border:"none",borderRadius:4,color:"#0d0d10",cursor:"pointer",fontFamily:"'DM Mono',monospace",fontSize:11,padding:"4px 8px"}}>✓</button>
                  </div>
                ) : (
                  <button
                    onClick={()=>switchProfile(idx)}
                    onDoubleClick={()=>{setEditingName(idx);setNameInput(profileNames[idx]);}}
                    title="Double-tap to rename"
                    style={{background:profile===idx?"#c9a84c":"#1e1e28",border:`1px solid ${profile===idx?"#c9a84c":"#2a2a38"}`,borderRadius:6,color:profile===idx?"#0d0d10":"#888",cursor:"pointer",fontFamily:"'DM Mono',monospace",fontSize:12,fontWeight:profile===idx?500:400,padding:"5px 14px",transition:"all .2s"}}
                  >
                    {name}
                  </button>
                )}
              </div>
            ))}
          </div>
        </div>
        <div style={{display:"flex",alignItems:"center",gap:8,flexWrap:"wrap"}}>
          <button className="import-btn" onClick={()=>{setImportStep("pick");setImportError("");}}>↑ Import CSV</button>
          <button className="copy-btn" style={{color:"#9a8aff",borderColor:"#3a2a6a"}} onClick={()=>setShowSaveCode(true)}>💾 Save Code</button>
          <button className="copy-btn" style={{color:"#9a8aff",borderColor:"#3a2a6a"}} onClick={()=>{setShowLoadCode(true);setLoadCodeErr("");}}>📂 Load Code</button>
          {(monthData.income.length>0||monthData.bills.length>0)&&(
            <button className="copy-btn" onClick={()=>{setCopyTargets([]);setCopySuccess(false);setShowCopyModal(true);}}>⎘ Copy Month</button>
          )}
          <button className="nav-arrow" onClick={()=>{if(month===0){setMonth(11);setYear(y=>y-1);}else setMonth(m=>m-1);}}>‹</button>
          <select value={month} onChange={e=>setMonth(Number(e.target.value))}>
            {MONTHS.map((m,i)=><option key={i} value={i}>{m}</option>)}
          </select>
          <input type="number" value={year} onChange={e=>setYear(Number(e.target.value))} style={{background:"#1a1a22",border:"1px solid #2a2a38",borderRadius:8,color:"#f0ede6",fontFamily:"'DM Mono',monospace",fontSize:13,padding:"8px 10px",width:80,outline:"none",textAlign:"center"}}/>
          <button className="nav-arrow" onClick={()=>{if(month===11){setMonth(0);setYear(y=>y+1);}else setMonth(m=>m+1);}}>›</button>
        </div>
      </div>

      {/* Save Code Bar — always visible when there's data */}
      {Object.keys(allData).length>0&&(
        <div style={{background:"#13131a",borderBottom:"1px solid #1e1e28",padding:"10px 24px",display:"flex",alignItems:"center",gap:10,flexWrap:"wrap"}}>
          <span style={{fontFamily:"'DM Mono',monospace",fontSize:11,color:"#555",letterSpacing:".08em",textTransform:"uppercase"}}>{profileNames[profile]} — Save before closing →</span>
          <button onClick={copySaveCode} style={{background:copied?"#1a3a25":"#1e1e28",border:`1px solid ${copied?"#3a6040":"#2a2a38"}`,borderRadius:6,color:copied?"#5ec97e":"#9a8aff",cursor:"pointer",fontFamily:"'DM Mono',monospace",fontSize:12,padding:"5px 14px",transition:"all .2s"}}>
            {copied?"✓ Copied!":"📋 Copy Save Code"}
          </button>
          <button onClick={()=>{setShowLoadCode(true);setLoadCodeErr("");}} style={{background:"#1e1e28",border:"1px solid #2a2a38",borderRadius:6,color:"#666",cursor:"pointer",fontFamily:"'DM Mono',monospace",fontSize:12,padding:"5px 14px"}}>
            📂 Load Saved Code
          </button>
          <span style={{fontFamily:"'DM Mono',monospace",fontSize:10,color:"#333",marginLeft:"auto"}}>{Object.keys(allData).length} month{Object.keys(allData).length!==1?"s":""} saved</span>
        </div>
      )}

      {/* Tabs */}
      <div style={{padding:"16px 24px 0",display:"flex",gap:4}}>
        {["overview","income","bills"].map(t=>(
          <button key={t} className={`tab-btn ${activeTab===t?"active":""}`} onClick={()=>setActiveTab(t)}>{t}</button>
        ))}
      </div>

      <div style={{padding:"20px 24px",maxWidth:860,margin:"0 auto"}}>

        {/* OVERVIEW */}
        {activeTab==="overview"&&(
          <div style={{display:"flex",flexDirection:"column",gap:16}}>
            <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(180px,1fr))",gap:12}}>
              {[
                {label:"Total Income", value:formatCurrency(totalIncome), color:"#5ec97e"},
                {label:"Total Bills",  value:formatCurrency(totalBills),  color:"#e07070"},
                {label:"Remaining",    value:formatCurrency(remaining),   color:remaining>=0?"#c9a84c":"#e05252"},
              ].map(s=>(
                <div key={s.label} className="card">
                  <div className="stat-label">{s.label}</div>
                  <div style={{fontSize:28,color:s.color}}>{s.value}</div>
                </div>
              ))}
            </div>
            <div className="card">
              <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-end",marginBottom:6}}>
                <div><div className="stat-label">Bills Paid</div>
                  <div style={{fontSize:20}}>{formatCurrency(totalPaid)} <span style={{color:"#555",fontSize:14}}>of {formatCurrency(totalBills)}</span></div>
                </div>
                <div style={{fontFamily:"'DM Mono',monospace",fontSize:24,color:"#c9a84c"}}>{paidPct}%</div>
              </div>
              <div className="progress-bar"><div className="progress-fill" style={{width:`${paidPct}%`}}/></div>
              <div style={{display:"flex",justifyContent:"space-between",marginTop:10,fontFamily:"'DM Mono',monospace",fontSize:12,color:"#666"}}>
                <span>Still owed: <span style={{color:"#e07070"}}>{formatCurrency(totalUnpaid)}</span></span>
                <span>{monthData.bills.filter(b=>b.paid).length}/{monthData.bills.length} bills paid</span>
              </div>
            </div>
            {monthData.bills.length>0&&(
              <div className="card">
                <div className="stat-label" style={{marginBottom:12}}>Bill Status</div>
                {monthData.bills.map((b,i)=>(
                  <div key={i} className="row-item" style={{justifyContent:"space-between"}}>
                    <div style={{display:"flex",alignItems:"center",gap:10}}>
                      <input type="checkbox" className="checkbox" checked={b.paid} onChange={()=>togglePaid(i)}/>
                      <span style={{textDecoration:b.paid?"line-through":"none",opacity:b.paid?.5:1}}>{b.label}</span>
                    </div>
                    <div style={{display:"flex",alignItems:"center",gap:10}}>
                      <span style={{fontFamily:"'DM Mono',monospace",fontSize:13}}>{formatCurrency(parseFloat(b.amount))}</span>
                      <span className={`pill ${b.paid?"paid":"unpaid"}`}>{b.paid?"paid":"due"}</span>
                    </div>
                  </div>
                ))}
              </div>
            )}
            {monthData.income.length===0&&monthData.bills.length===0&&(
              <div style={{textAlign:"center",padding:"60px 0",color:"#444"}}>
                <div style={{fontSize:40,marginBottom:12}}>📒</div>
                <div style={{fontFamily:"'DM Mono',monospace",fontSize:13}}>No data for {MONTHS[month]} {year}</div>
                <div style={{fontFamily:"'DM Mono',monospace",fontSize:12,color:"#333",marginTop:6}}>Share a PDF in chat or import a CSV</div>
              </div>
            )}
          </div>
        )}

        {/* INCOME */}
        {activeTab==="income"&&(
          <div className="card">
            <div style={{marginBottom:20}}>
              <div className="stat-label">Income — {MONTHS[month]} {year}</div>
              <div style={{fontSize:26,color:"#5ec97e"}}>{formatCurrency(totalIncome)}</div>
            </div>
            {monthData.income.length===0&&<div style={{textAlign:"center",padding:"30px 0",color:"#444",fontFamily:"'DM Mono',monospace",fontSize:13}}>No income entries yet</div>}
            {monthData.income.map((inc,i)=>(
              <div key={i} className="row-item" style={{justifyContent:"space-between"}}>
                <span>{inc.label}</span>
                <div style={{display:"flex",alignItems:"center",gap:8}}>
                  <span style={{fontFamily:"'DM Mono',monospace",fontSize:14,color:"#5ec97e"}}>{formatCurrency(parseFloat(inc.amount))}</span>
                  <button className="icon-btn" onClick={()=>{setIncomeForm(inc);setEditIncomeIdx(i);setShowIncomeModal(true);}}>✏️</button>
                  <button className="icon-btn danger" onClick={()=>deleteIncome(i)}>🗑</button>
                </div>
              </div>
            ))}
            <button className="add-btn" onClick={()=>{setIncomeForm({label:"",amount:""});setEditIncomeIdx(null);setShowIncomeModal(true);}}>
              <span style={{fontSize:18}}>+</span> Add Income Source
            </button>
          </div>
        )}

        {/* BILLS */}
        {activeTab==="bills"&&(
          <div className="card">
            <div style={{marginBottom:20}}>
              <div className="stat-label">Bills — {MONTHS[month]} {year}</div>
              <div style={{fontSize:26}}>
                <span style={{color:"#e07070"}}>{formatCurrency(totalBills)}</span>
                <span style={{color:"#444",fontSize:14,marginLeft:10}}>{formatCurrency(totalUnpaid)} remaining</span>
              </div>
            </div>
            {monthData.bills.length===0&&<div style={{textAlign:"center",padding:"30px 0",color:"#444",fontFamily:"'DM Mono',monospace",fontSize:13}}>No bills yet</div>}
            {monthData.bills.map((b,i)=>(
              <div key={i} className="row-item" style={{justifyContent:"space-between"}}>
                <div style={{display:"flex",alignItems:"center",gap:10}}>
                  <input type="checkbox" className="checkbox" checked={b.paid} onChange={()=>togglePaid(i)}/>
                  <div>
                    <div style={{textDecoration:b.paid?"line-through":"none",opacity:b.paid?.5:1}}>{b.label}</div>
                    {b.due&&<div style={{fontFamily:"'DM Mono',monospace",fontSize:11,color:"#555"}}>Due: {b.due}</div>}
                  </div>
                </div>
                <div style={{display:"flex",alignItems:"center",gap:8}}>
                  <span style={{fontFamily:"'DM Mono',monospace",fontSize:14}}>{formatCurrency(parseFloat(b.amount))}</span>
                  <span className={`pill ${b.paid?"paid":"unpaid"}`}>{b.paid?"paid":"due"}</span>
                  <button className="icon-btn" onClick={()=>{setBillForm(b);setEditBillIdx(i);setShowBillModal(true);}}>✏️</button>
                  <button className="icon-btn danger" onClick={()=>deleteBill(i)}>🗑</button>
                </div>
              </div>
            ))}
            <button className="add-btn" onClick={()=>{setBillForm({label:"",amount:"",due:"",paid:false});setEditBillIdx(null);setShowBillModal(true);}}>
              <span style={{fontSize:18}}>+</span> Add Bill
            </button>
          </div>
        )}
      </div>

      {/* Income Modal */}
      {showIncomeModal&&(
        <div className="overlay" onClick={()=>setShowIncomeModal(false)}>
          <div className="modal" onClick={e=>e.stopPropagation()}>
            <h2 style={{fontSize:20,fontWeight:400,marginBottom:20}}>{editIncomeIdx!==null?"Edit":"Add"} Income</h2>
            <div style={{display:"flex",flexDirection:"column",gap:14}}>
              <div><label>Source / Label</label><input placeholder="e.g. Salary, Freelance" value={incomeForm.label} onChange={e=>setIncomeForm(f=>({...f,label:e.target.value}))}/></div>
              <div><label>Amount ($)</label><input type="number" placeholder="0.00" value={incomeForm.amount} onChange={e=>setIncomeForm(f=>({...f,amount:e.target.value}))}/></div>
            </div>
            <div style={{display:"flex",gap:10,marginTop:22,justifyContent:"flex-end"}}>
              <button className="btn-cancel" onClick={()=>setShowIncomeModal(false)}>Cancel</button>
              <button className="btn-save" onClick={saveIncome}>Save</button>
            </div>
          </div>
        </div>
      )}

      {/* Bill Modal */}
      {showBillModal&&(
        <div className="overlay" onClick={()=>setShowBillModal(false)}>
          <div className="modal" onClick={e=>e.stopPropagation()}>
            <h2 style={{fontSize:20,fontWeight:400,marginBottom:20}}>{editBillIdx!==null?"Edit":"Add"} Bill</h2>
            <div style={{display:"flex",flexDirection:"column",gap:14}}>
              <div><label>Bill Name</label><input placeholder="e.g. Rent, Netflix" value={billForm.label} onChange={e=>setBillForm(f=>({...f,label:e.target.value}))}/></div>
              <div><label>Amount ($)</label><input type="number" placeholder="0.00" value={billForm.amount} onChange={e=>setBillForm(f=>({...f,amount:e.target.value}))}/></div>
              <div><label>Due Date (optional)</label><input type="date" value={billForm.due} onChange={e=>setBillForm(f=>({...f,due:e.target.value}))}/></div>
              <div style={{display:"flex",alignItems:"center",gap:10}}>
                <input type="checkbox" className="checkbox" id="pc" checked={billForm.paid} onChange={e=>setBillForm(f=>({...f,paid:e.target.checked}))}/>
                <label htmlFor="pc" style={{fontFamily:"'DM Mono',monospace",fontSize:12,letterSpacing:".08em",textTransform:"uppercase",color:"#888",cursor:"pointer",marginBottom:0}}>Already Paid</label>
              </div>
            </div>
            <div style={{display:"flex",gap:10,marginTop:22,justifyContent:"flex-end"}}>
              <button className="btn-cancel" onClick={()=>setShowBillModal(false)}>Cancel</button>
              <button className="btn-save" onClick={saveBill}>Save</button>
            </div>
          </div>
        </div>
      )}

      {/* Copy Modal */}
      {showCopyModal&&(
        <div className="overlay" onClick={()=>setShowCopyModal(false)}>
          <div className="modal" style={{maxWidth:500}} onClick={e=>e.stopPropagation()}>
            <h2 style={{fontSize:20,fontWeight:400,marginBottom:4}}>Copy {MONTHS[month]} {year}</h2>
            <p style={{fontFamily:"'DM Mono',monospace",fontSize:12,color:"#666",marginBottom:18}}>Select which months to copy this data into.</p>
            <div style={{marginBottom:16}}>
              <div className="stat-label" style={{marginBottom:8}}>What to copy</div>
              <div style={{display:"flex",gap:8}}>
                <button className={`what-toggle ${copyWhat.income?"on":""}`} onClick={()=>setCopyWhat(w=>({...w,income:!w.income}))}>{copyWhat.income?"✓":"○"} Income ({monthData.income.length})</button>
                <button className={`what-toggle ${copyWhat.bills?"on":""}`} onClick={()=>setCopyWhat(w=>({...w,bills:!w.bills}))}>{copyWhat.bills?"✓":"○"} Bills ({monthData.bills.length})</button>
              </div>
            </div>
            <div className="stat-label" style={{marginBottom:8}}>Copy to</div>
            <div className="copy-grid">
              {getCopyOptions().map(({key:k,label,hasData})=>(
                <div key={k} className={`copy-target ${copyTargets.includes(k)?"selected":""}`} onClick={()=>setCopyTargets(p=>p.includes(k)?p.filter(x=>x!==k):[...p,k])}>
                  <span style={{fontSize:16}}>{copyTargets.includes(k)?"☑":"☐"}</span>
                  <div>
                    <div style={{fontFamily:"'DM Mono',monospace",fontSize:13}}>{label}</div>
                    {hasData&&<div style={{fontFamily:"'DM Mono',monospace",fontSize:10,color:"#c9a84c"}}>has data — will overwrite</div>}
                  </div>
                </div>
              ))}
            </div>
            {copySuccess?(
              <div className="success-flash">✓ Copied to {copyTargets.length} month{copyTargets.length!==1?"s":""}!</div>
            ):(
              <div style={{display:"flex",gap:10,marginTop:20,justifyContent:"flex-end",alignItems:"center"}}>
                {copyTargets.length>0&&<span style={{fontFamily:"'DM Mono',monospace",fontSize:12,color:"#666",marginRight:"auto"}}>{copyTargets.length} selected</span>}
                <button className="btn-cancel" onClick={()=>setShowCopyModal(false)}>Cancel</button>
                <button className="btn-save" onClick={executeCopy} style={{opacity:copyTargets.length===0||(!copyWhat.income&&!copyWhat.bills)?.4:1}}>Copy →</button>
              </div>
            )}
          </div>
        </div>
      )}

      {/* Import: Pick */}
      {importStep==="pick"&&(
        <div className="overlay" onClick={()=>setImportStep(null)}>
          <div className="modal" style={{maxWidth:460}} onClick={e=>e.stopPropagation()}>
            <h2 style={{fontSize:20,fontWeight:400,marginBottom:6}}>Import Transactions</h2>
            <p style={{fontFamily:"'DM Mono',monospace",fontSize:12,color:"#666",marginBottom:20,lineHeight:1.7}}>
              For CSV files from your bank, upload below. For Wells Fargo PDFs, just share the PDF in this chat and it gets added automatically.
            </p>
            <div className="pick-card csv-card" onClick={()=>csvRef.current?.click()}>
              <div style={{fontSize:32,marginBottom:10}}>📄</div>
              <div style={{fontFamily:"'DM Mono',monospace",fontSize:13,color:"#5ec97e",marginBottom:6}}>Upload CSV File</div>
              <div style={{fontFamily:"'DM Mono',monospace",fontSize:11,color:"#555",lineHeight:1.5}}>Chase, Bank of America, Wells Fargo desktop export</div>
            </div>
            <input ref={csvRef} type="file" accept=".csv,text/csv" style={{display:"none"}} onChange={handleCSVFile}/>
            <input ref={pdfRef} type="file" accept=".pdf" style={{display:"none"}} onChange={handlePDFFile}/>
            {importError&&(
              <div style={{background:"#1a2a1e",border:"1px solid #2a4a32",borderRadius:8,padding:"12px 14px",marginTop:14,fontFamily:"'DM Mono',monospace",fontSize:12,color:"#5ec97e",lineHeight:1.6}}>
                💡 {importError}
              </div>
            )}
            <div style={{marginTop:16,textAlign:"right"}}><button className="btn-cancel" onClick={()=>setImportStep(null)}>Cancel</button></div>
          </div>
        </div>
      )}

      {/* Import: Review */}
      {importStep==="review"&&(
        <div className="overlay">
          <div className="big-modal">
            <div className="bm-header">
              <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start",gap:12,flexWrap:"wrap"}}>
                <div>
                  <h2 style={{fontSize:20,fontWeight:400,marginBottom:4}}>Review Transactions</h2>
                  <p style={{fontFamily:"'DM Mono',monospace",fontSize:12,color:"#666"}}>Tag each transaction. Bills are marked paid since they've cleared.</p>
                </div>
                {importMonth&&(
                  <div style={{fontFamily:"'DM Mono',monospace",fontSize:12,background:"#1a2a1e",border:"1px solid #2a4a32",borderRadius:8,padding:"6px 12px",color:"#5ec97e",whiteSpace:"nowrap"}}>
                    {MONTHS[importMonth.month]} {importMonth.year}
                  </div>
                )}
              </div>
              {importMonth&&(
                <label className="toggle-row">
                  <input type="checkbox" checked={filterMonth} onChange={e=>setFilterMonth(e.target.checked)} style={{accentColor:"#c9a84c"}}/>
                  Only show {MONTHS[importMonth.month]} {importMonth.year} ({getFilteredRows().length} of {importRows.length})
                </label>
              )}
            </div>
            <div className="sum-bar">
              <span className="chip" style={{background:"#1a3a25",color:"#5ec97e"}}>↑ {incomeRows.length} income · {formatCurrency(incomeRows.reduce((s,r)=>s+r.amount,0))}</span>
              <span className="chip" style={{background:"#3a1a1a",color:"#e07070"}}>↓ {billRows.length} bills · {formatCurrency(Math.abs(billRows.reduce((s,r)=>s+r.amount,0)))}</span>
              <span className="chip" style={{background:"#222230",color:"#555"}}>— {ignoredRows.length} ignored</span>
            </div>
            <div className="bm-body">
              {filteredRows.length===0&&<div style={{textAlign:"center",padding:"40px 0",color:"#444",fontFamily:"'DM Mono',monospace",fontSize:13}}>No transactions match</div>}
              {filteredRows.map(r=>{
                const tag=importTags[r.idx];
                return (
                  <div key={r.idx} className="tx-row">
                    <div style={{minWidth:0}}>
                      <div style={{fontSize:14,marginBottom:2,whiteSpace:"nowrap",overflow:"hidden",textOverflow:"ellipsis"}}>{r.desc}</div>
                      <div style={{fontFamily:"'DM Mono',monospace",fontSize:11,color:"#555"}}>
                        {r.date?r.date.toLocaleDateString("en-US",{month:"short",day:"numeric",year:"numeric"}):"No date"}
                      </div>
                    </div>
                    <div style={{fontFamily:"'DM Mono',monospace",fontSize:14,whiteSpace:"nowrap",color:r.amount>=0?"#5ec97e":"#e07070",textAlign:"right"}}>
                      {r.amount>=0?"+":""}{formatCurrency(r.amount)}
                    </div>
                    <div style={{display:"flex",gap:4}}>
                      <button className={`tag-btn income-${tag==="income"?"on":"off"}`} onClick={()=>setImportTags(t=>({...t,[r.idx]:"income"}))}>Income</button>
                      <button className={`tag-btn bill-${tag==="bill"?"on":"off"}`} onClick={()=>setImportTags(t=>({...t,[r.idx]:"bill"}))}>Bill</button>
                      <button className={`tag-btn ignore-${tag==="ignore"?"on":"off"}`} onClick={()=>setImportTags(t=>({...t,[r.idx]:"ignore"}))}>Skip</button>
                    </div>
                  </div>
                );
              })}
            </div>
            <div className="bm-footer">
              <button className="btn-cancel" onClick={()=>setImportStep("pick")}>← Back</button>
              <div style={{display:"flex",gap:10,alignItems:"center",flexWrap:"wrap",justifyContent:"flex-end"}}>
                <span style={{fontFamily:"'DM Mono',monospace",fontSize:12,color:"#555"}}>→ {importMonth?`${MONTHS[importMonth.month]} ${importMonth.year}`:"current month"}</span>
                <button className="btn-save" onClick={commitImport} style={{opacity:(incomeRows.length+billRows.length)===0?.4:1}}>
                  Import {incomeRows.length+billRows.length} transactions →
                </button>
              </div>
            </div>
          </div>
        </div>
      )}

      {/* Import: Done */}
      {importStep==="done"&&(
        <div className="overlay">
          <div style={{background:"#1a3a25",border:"1px solid #3a6040",borderRadius:16,padding:"40px 56px",textAlign:"center",color:"#5ec97e"}}>
            <div style={{fontSize:44,marginBottom:12}}>✓</div>
            <div style={{fontSize:20,fontWeight:400,marginBottom:6}}>Import complete!</div>
          </div>
        </div>
      )}

      {/* Save Code Modal */}
      {showSaveCode&&(
        <div className="overlay" onClick={()=>setShowSaveCode(false)}>
          <div className="modal" style={{maxWidth:520}} onClick={e=>e.stopPropagation()}>
            <h2 style={{fontSize:20,fontWeight:400,marginBottom:6}}>Your Save Code</h2>
            <p style={{fontFamily:"'DM Mono',monospace",fontSize:12,color:"#666",marginBottom:14,lineHeight:1.6}}>
              Copy this and save it somewhere — Notes app, email, anywhere. Paste it back in with <b style={{color:"#f0ede6"}}>📂 Load Code</b> next time to restore everything.
            </p>
            <textarea readOnly value={getSaveCode()} style={{width:"100%",height:120,background:"#0d0d10",border:"1px solid #2a2a38",borderRadius:8,color:"#9a8aff",fontFamily:"'DM Mono',monospace",fontSize:10,padding:"10px 12px",resize:"none",outline:"none",wordBreak:"break-all"}}/>
            <div style={{display:"flex",gap:10,marginTop:14,justifyContent:"flex-end"}}>
              <button className="btn-cancel" onClick={()=>setShowSaveCode(false)}>Close</button>
              <button className="btn-save" style={{background:copied?"#1a3a25":"#c9a84c",color:copied?"#5ec97e":"#0d0d10"}} onClick={copySaveCode}>{copied?"✓ Copied!":"Copy to Clipboard"}</button>
            </div>
          </div>
        </div>
      )}

      {/* Load Code Modal */}
      {showLoadCode&&(
        <div className="overlay" onClick={()=>setShowLoadCode(false)}>
          <div className="modal" style={{maxWidth:520}} onClick={e=>e.stopPropagation()}>
            <h2 style={{fontSize:20,fontWeight:400,marginBottom:6}}>Load Saved Data</h2>
            <p style={{fontFamily:"'DM Mono',monospace",fontSize:12,color:"#666",marginBottom:14,lineHeight:1.6}}>
              Paste your save code below to restore all your months.
            </p>
            <textarea
              value={loadCodeText}
              onChange={e=>{setLoadCodeText(e.target.value);setLoadCodeErr("");}}
              placeholder="Paste your save code here…"
              style={{width:"100%",height:120,background:"#0d0d10",border:`1px solid ${loadCodeErr?"#6a3030":"#2a2a38"}`,borderRadius:8,color:"#f0ede6",fontFamily:"'DM Mono',monospace",fontSize:10,padding:"10px 12px",resize:"none",outline:"none",wordBreak:"break-all"}}
            />
            {loadCodeErr&&<div style={{fontFamily:"'DM Mono',monospace",fontSize:11,color:"#e07070",marginTop:8}}>⚠ {loadCodeErr}</div>}
            <div style={{display:"flex",gap:10,marginTop:14,justifyContent:"flex-end"}}>
              <button className="btn-cancel" onClick={()=>{setShowLoadCode(false);setLoadCodeText("");setLoadCodeErr("");}}>Cancel</button>
              <button className="btn-save" onClick={applyLoadCode} style={{opacity:loadCodeText.trim()?1:.4}}>Restore →</button>
            </div>
          </div>
        </div>
      )}

      {/* OLD Restore Modal — kept for compat */}
      {showRestore&&(
        <div className="overlay" onClick={()=>setShowRestore(false)}>
          <div className="modal" style={{maxWidth:560}} onClick={e=>e.stopPropagation()}>
            <h2 style={{fontSize:20,fontWeight:400,marginBottom:6}}>Restore Data</h2>
            <p style={{fontFamily:"'DM Mono',monospace",fontSize:12,color:"#666",marginBottom:14,lineHeight:1.6}}>
              Paste your backup text below and tap Restore. This will overwrite your current data.
            </p>
            <textarea
              value={restorePaste}
              onChange={e=>setRestorePaste(e.target.value)}
              placeholder='Paste your backup JSON here...'
              style={{width:"100%",height:200,background:"#0d0d10",border:"1px solid #2a2a38",borderRadius:8,color:"#f0ede6",fontFamily:"'DM Mono',monospace",fontSize:11,padding:"10px 12px",resize:"none",outline:"none"}}
            />
            <div style={{display:"flex",gap:10,marginTop:14,justifyContent:"flex-end"}}>
              <button className="btn-cancel" onClick={()=>{setShowRestore(false);setRestorePaste("");}}>Cancel</button>
              <button className="btn-save" onClick={handleRestore} style={{opacity:restorePaste.trim()?1:.4}}>Restore</button>
            </div>
          </div>
        </div>
      )}

      {/* Backup Modal */}
      {showBackup&&(
        <div className="overlay" onClick={()=>setShowBackup(false)}>
          <div className="modal" style={{maxWidth:560}} onClick={e=>e.stopPropagation()}>
            <h2 style={{fontSize:20,fontWeight:400,marginBottom:6}}>Backup Your Data</h2>
            <p style={{fontFamily:"'DM Mono',monospace",fontSize:12,color:"#666",marginBottom:14,lineHeight:1.6}}>
              Copy all of this text and save it somewhere safe (Notes app, email to yourself, etc). When you need to restore, click <b style={{color:"#f0ede6"}}>⬆ Restore</b> and paste it in.
            </p>
            <textarea
              id="backup-ta"
              readOnly
              value={backupText}
              style={{width:"100%",height:200,background:"#0d0d10",border:"1px solid #2a2a38",borderRadius:8,color:"#5ec97e",fontFamily:"'DM Mono',monospace",fontSize:11,padding:"10px 12px",resize:"none",outline:"none"}}
            />
            <div style={{display:"flex",gap:10,marginTop:14,justifyContent:"flex-end"}}>
              <button className="btn-cancel" onClick={()=>setShowBackup(false)}>Close</button>
              <button className="btn-save" onClick={copyBackup}>Copy to Clipboard</button>
            </div>
          </div>
        </div>
      )}
    </div>
  );
}
