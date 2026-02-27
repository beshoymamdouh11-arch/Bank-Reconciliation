// ═══════════════════════════════════════════════════════════════
// BANKRECON AI v3 — Enterprise Bank Reconciliation Platform
// ═══════════════════════════════════════════════════════════════
import { useState, useEffect, useRef, useCallback, useReducer } from "react";

// ─── GLOBAL STYLES ───────────────────────────────────────────
const STYLES = `
@import url('https://fonts.googleapis.com/css2?family=Syne:wght@400;600;700;800&family=IBM+Plex+Mono:wght@300;400;500;600&display=swap');
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
body{background:#06080f;color:#dde2f2;font-family:'Syne',sans-serif;min-height:100vh;overflow-x:hidden}
::-webkit-scrollbar{width:5px;height:5px}
::-webkit-scrollbar-track{background:#090c1a}
::-webkit-scrollbar-thumb{background:#1c2848;border-radius:3px}
::-webkit-scrollbar-thumb:hover{background:#2a3a6a}
.mono{font-family:'IBM Plex Mono',monospace}

@keyframes fadeUp{from{opacity:0;transform:translateY(12px)}to{opacity:1;transform:translateY(0)}}
@keyframes pulse{0%,100%{opacity:1}50%{opacity:.4}}
@keyframes spin{to{transform:rotate(360deg)}}
@keyframes scan{0%{top:-2px}100%{top:100%}}
@keyframes glow{0%,100%{box-shadow:0 0 20px rgba(0,210,140,.15)}50%{box-shadow:0 0 40px rgba(0,210,140,.35)}}
@keyframes slideR{from{transform:translateX(100%);opacity:0}to{transform:translateX(0);opacity:1}}
@keyframes typewriter{from{width:0}to{width:100%}}
@keyframes blink{0%,100%{opacity:1}50%{opacity:0}}
@keyframes ripple{0%{transform:scale(1);opacity:.6}100%{transform:scale(3);opacity:0}}
@keyframes gradFlow{0%{background-position:0% 50%}50%{background-position:100% 50%}100%{background-position:0% 50%}}

.fadeUp{animation:fadeUp .32s ease forwards}
.pulse{animation:pulse 2s infinite}
.spin{animation:spin .9s linear infinite;display:inline-block}
.glow{animation:glow 3s ease infinite}
.slideR{animation:slideR .3s ease forwards}
.blink{animation:blink 1s step-end infinite}

/* Cards */
.card{background:linear-gradient(145deg,#0a0d1e,#0d1128);border:1px solid #161e38;border-radius:16px;padding:20px;transition:border-color .2s,transform .2s,box-shadow .2s}
.card:hover{border-color:#22305a}
.card-flat{background:#080a18;border:1px solid #111828;border-radius:12px;padding:14px}
.card-glow{box-shadow:0 0 30px rgba(0,210,140,.12),0 0 60px rgba(50,100,255,.08)}
.glass{background:rgba(10,13,30,.8);backdrop-filter:blur(12px);border:1px solid rgba(255,255,255,.06)}

/* Buttons */
.btn{padding:8px 16px;border-radius:10px;border:none;cursor:pointer;font-family:'Syne',sans-serif;font-weight:700;font-size:12.5px;transition:all .18s;display:inline-flex;align-items:center;gap:6px;white-space:nowrap;letter-spacing:.01em}
.btn:disabled{opacity:.45;cursor:not-allowed;transform:none!important}
.btn-primary{background:linear-gradient(135deg,#2a46cc,#3d5fef);color:#fff;box-shadow:0 3px 12px rgba(61,95,239,.3)}
.btn-primary:hover:not(:disabled){transform:translateY(-1px);box-shadow:0 5px 18px rgba(61,95,239,.45)}
.btn-ghost{background:#0d1220;color:#5a6f9a;border:1px solid #182040}
.btn-ghost:hover:not(:disabled){background:#121930;color:#8090bb}
.btn-success{background:linear-gradient(135deg,#007a55,#00c486);color:#fff;box-shadow:0 3px 12px rgba(0,196,134,.25)}
.btn-success:hover:not(:disabled){transform:translateY(-1px)}
.btn-warn{background:linear-gradient(135deg,#92580a,#d97706);color:#fff}
.btn-danger{background:linear-gradient(135deg,#8b1212,#dc2626);color:#fff}
.btn-purple{background:linear-gradient(135deg,#4c1d95,#7c3aed);color:#fff;box-shadow:0 3px 12px rgba(124,58,237,.3)}
.btn-teal{background:linear-gradient(135deg,#065f56,#0d9488);color:#fff}
.btn-sm{padding:5px 11px;font-size:11px;border-radius:8px}
.btn-xs{padding:3px 8px;font-size:10px;border-radius:6px;gap:4px}

/* Forms */
input,select,textarea{background:#080c1c;border:1px solid #161e38;border-radius:9px;color:#dde2f2;font-family:'IBM Plex Mono',monospace;font-size:12px;padding:8px 12px;width:100%;transition:border-color .2s,box-shadow .2s;outline:none}
input:focus,select:focus,textarea:focus{border-color:#2a46cc;box-shadow:0 0 0 3px rgba(42,70,204,.12)}
select option{background:#0a0d1e}
label{font-size:10.5px;font-weight:700;color:#3a4e72;margin-bottom:5px;display:block;letter-spacing:.07em;text-transform:uppercase}

/* Badges */
.badge{padding:2px 8px;border-radius:20px;font-size:9.5px;font-weight:700;font-family:'IBM Plex Mono',monospace;letter-spacing:.05em;text-transform:uppercase;white-space:nowrap}

/* Progress */
.prog{height:4px;background:#0f1525;border-radius:3px;overflow:hidden}
.prog-fill{height:100%;border-radius:3px;transition:width .7s cubic-bezier(.4,0,.2,1)}
.prog-sm{height:3px}

/* Tables */
table{width:100%;border-collapse:collapse}
th{font-size:10px;font-weight:700;letter-spacing:.08em;text-transform:uppercase;color:#2e3f60;padding:10px 13px;text-align:left;border-bottom:1px solid #0f1525;white-space:nowrap}
td{padding:10px 13px;font-size:12px;border-bottom:1px solid #0a0f1e;vertical-align:middle}
tr:hover td{background:rgba(42,70,204,.04)}
tbody tr:last-child td{border-bottom:none}

/* Layout */
.sidebar-link{display:flex;align-items:center;gap:10px;padding:9px 13px;border-radius:10px;cursor:pointer;transition:all .17s;font-size:12.5px;font-weight:600;color:#3a4e72;margin-bottom:2px;white-space:nowrap}
.sidebar-link:hover{background:#0d1220;color:#7a8fbb}
.sidebar-link.active{background:rgba(42,70,204,.15);color:#5a7af5;border-left:3px solid #2a46cc;padding-left:10px}

/* Toggle */
.toggle{position:relative;width:40px;height:21px;border-radius:11px;cursor:pointer;transition:background .2s;flex-shrink:0;border:none;padding:0}
.toggle-dot{position:absolute;top:2.5px;width:16px;height:16px;border-radius:50%;background:#fff;transition:left .2s;box-shadow:0 1px 4px rgba(0,0,0,.5)}

/* Gradient text */
.gt{background:linear-gradient(135deg,#4d70ff,#00d48c);-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text}
.gt-warm{background:linear-gradient(135deg,#f97316,#eab308);-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text}
.gt-purple{background:linear-gradient(135deg,#8b5cf6,#ec4899);-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text}

/* Chat */
.chat-bubble{padding:11px 15px;border-radius:14px;font-size:12.5px;line-height:1.6;max-width:80%;animation:fadeUp .25s ease}
.chat-user{background:rgba(42,70,204,.25);border:1px solid rgba(42,70,204,.3);border-radius:14px 14px 4px 14px;align-self:flex-end}
.chat-bot{background:rgba(0,196,134,.08);border:1px solid rgba(0,196,134,.2);border-radius:14px 14px 14px 4px;align-self:flex-start}

/* Tabs */
.tab-pill{padding:7px 16px;border-radius:20px;font-size:11.5px;font-weight:700;cursor:pointer;transition:all .18s;border:1px solid transparent}
.tab-pill.active{background:rgba(42,70,204,.2);border-color:#2a46cc;color:#6080ff}
.tab-pill:not(.active){color:#3a4e72}
.tab-pill:not(.active):hover{color:#6a7f9a}

/* Upload */
.upload-zone{border:2px dashed #182040;border-radius:14px;padding:28px 18px;text-align:center;transition:all .2s;cursor:pointer;position:relative;overflow:hidden}
.upload-zone:hover,.upload-zone.drag{border-color:#2a46cc;background:rgba(42,70,204,.05)}

/* Notification */
.notif{position:fixed;top:68px;right:20px;z-index:3000;padding:12px 16px;border-radius:12px;display:flex;align-items:center;gap:10px;font-size:12.5px;font-weight:600;animation:slideR .3s ease;box-shadow:0 8px 30px rgba(0,0,0,.4);max-width:380px}

/* Modal */
.modal-bg{position:fixed;inset:0;background:rgba(0,0,0,.75);z-index:1000;display:flex;align-items:center;justify-content:center;backdrop-filter:blur(5px)}
.modal{background:#090c1c;border:1px solid #1c2848;border-radius:20px;padding:28px;width:92%;max-width:640px;max-height:90vh;overflow-y:auto;animation:fadeUp .28s ease}
.modal-xl{max-width:960px}

/* Donut */
.donut-wrap{position:relative}

/* Section header */
.sec-hdr{display:flex;justify-content:space-between;align-items:flex-start;margin-bottom:18px}

/* Formula builder */
.formula-token{display:inline-flex;align-items:center;gap:5px;padding:4px 10px;background:#0f1525;border:1px solid #1c2848;border-radius:8px;font-size:11px;font-family:'IBM Plex Mono',monospace;color:#8090bb;cursor:pointer;transition:all .15s}
.formula-token:hover{border-color:#2a46cc;color:#6080ff}
.formula-token.active{background:rgba(42,70,204,.2);border-color:#2a46cc;color:#8090ff}

/* Cashflow */
.cf-row{display:grid;grid-template-columns:2fr repeat(6,1fr);gap:1px;padding:0}
.cf-cell{padding:9px 12px;font-size:11.5px;border-bottom:1px solid #0a0f1e}
.cf-header{background:#0a0d1e;font-size:10px;font-weight:700;letter-spacing:.07em;text-transform:uppercase;color:#2e3f60;padding:10px 12px}
.cf-total{background:rgba(42,70,204,.08);font-weight:700}
.cf-positive{color:#00c486}
.cf-negative{color:#dc2626}

/* Score card */
.kpi-card{background:linear-gradient(145deg,#0a0d1e,#0d1128);border:1px solid #161e38;border-radius:14px;padding:16px;transition:all .22s;cursor:default}
.kpi-card:hover{border-color:#22305a;transform:translateY(-2px);box-shadow:0 8px 24px rgba(0,0,0,.3)}

/* AI scanner line */
.ai-scan{position:absolute;left:0;right:0;height:2px;background:linear-gradient(90deg,transparent,#00d48c,transparent);animation:scan 2s linear infinite}

/* Tag */
.tag{display:inline-flex;align-items:center;gap:5px;padding:3px 9px;background:#0d1220;border:1px solid #182040;border-radius:18px;font-size:10.5px;color:#6a7f9a;font-family:'IBM Plex Mono',monospace}
.tag-remove{cursor:pointer;color:#3a4e72;transition:color .15s;font-size:11px}
.tag-remove:hover{color:#dc2626}

/* View mode banner */
.view-banner{padding:8px 16px;border-radius:8px;font-size:11px;font-weight:700;letter-spacing:.06em;display:flex;align-items:center;gap:8px}
`;

// ─── STATUS COLORS ────────────────────────────────────────────
const SC = {
  matched:"#00c486", partial:"#d97706", unmatched:"#dc2626",
  reconciled:"#00c486", review:"#d97706", open:"#dc2626", pending:"#7c3aed",
  positive:"#00c486", negative:"#dc2626", neutral:"#5a7af5"
};

// ─── MOCK DATA ────────────────────────────────────────────────
const BANKS = [
  {id:1,name:"HSBC - Main Account",short:"HSBC",currency:"USD",balance:1425300.50,glBalance:1420100.00,status:"review",items:342,matched:315,unmatched:27,glAccount:"110001",accountType:"current",isDebt:false,bankAccNo:"GB29HSBC12345678901234"},
  {id:2,name:"Barclays - Payroll",short:"Barclays",currency:"GBP",balance:892400.00,glBalance:892400.00,status:"reconciled",items:218,matched:218,unmatched:0,glAccount:"110002",accountType:"current",isDebt:false,bankAccNo:"GB12BARC20000055779911"},
  {id:3,name:"Citi - Operations",short:"Citi",currency:"EUR",balance:3210750.25,glBalance:3198200.00,status:"open",items:567,matched:480,unmatched:87,glAccount:"110003",accountType:"current",isDebt:false,bankAccNo:"DE89370400440532013000"},
  {id:4,name:"JPMorgan - Treasury",short:"JPM",currency:"USD",balance:5100000.00,glBalance:5100000.00,status:"reconciled",items:124,matched:124,unmatched:0,glAccount:"110004",accountType:"current",isDebt:false,bankAccNo:"US64CHAS0004500000000101"},
  {id:5,name:"Deutsche - Debt Facility",short:"Deutsche",currency:"EUR",balance:-8200000.00,glBalance:-8200000.00,status:"reconciled",items:89,matched:89,unmatched:0,glAccount:"210001",accountType:"loan",isDebt:true,bankAccNo:"DE12345678901234567890",baseRate:"EURIBOR",margin:2.5,currentRate:6.45},
  {id:6,name:"HSBC - RCF Facility",short:"HSBC RCF",currency:"USD",balance:-15000000.00,glBalance:-15000000.00,status:"review",items:45,matched:42,unmatched:3,glAccount:"210002",accountType:"revolving",isDebt:true,bankAccNo:"GB29HSBC98765432109876",baseRate:"SOFR",margin:1.75,currentRate:7.05},
];

const CATEGORIES = [
  {id:"c1",name:"Bank Charges",color:"#f59e0b",icon:"🏦",type:"expense",side:"both",conditions:[{field:"description",op:"contains",value:"charge"},{field:"description",op:"contains",value:"fee"}],columns:["date","description","amount","bank","vat"],autoMatch:true},
  {id:"c2",name:"T-Bills",color:"#3b82f6",icon:"📜",type:"investment",side:"both",conditions:[{field:"description",op:"contains",value:"treasury"},{field:"description",op:"contains",value:"T-bill"}],columns:["date","description","amount","maturity","yield","bank"],autoMatch:true},
  {id:"c3",name:"Reversal Transactions",color:"#8b5cf6",icon:"↩",type:"reversal",side:"same",conditions:[{field:"description",op:"contains",value:"reversal"},{field:"amount",op:"matches_prior","value":"negative_of_prior"}],columns:["date","description","amount","originalRef","bank"],autoMatch:true},
  {id:"c4",name:"Payroll",color:"#10b981",icon:"👥",type:"cashout",side:"debit",conditions:[{field:"description",op:"contains",value:"payroll"},{field:"description",op:"contains",value:"salary"}],columns:["date","description","amount","bank","department"],autoMatch:true},
  {id:"c5",name:"Vendor Payments",color:"#06b6d4",icon:"🏭",type:"cashout",side:"debit",conditions:[{field:"description",op:"contains",value:"vendor"},{field:"description",op:"contains",value:"supplier"}],columns:["date","description","amount","bank","vendor","reference"],autoMatch:false},
  {id:"c6",name:"Customer Receipts",color:"#00c486",icon:"💰",type:"cashin",side:"credit",conditions:[{field:"description",op:"contains",value:"receipt"},{field:"amount",op:"greater_than",value:"0"}],columns:["date","description","amount","bank","customer","reference"],autoMatch:true},
  {id:"c7",name:"Interest Income",color:"#84cc16",icon:"📈",type:"cashin",side:"credit",conditions:[{field:"description",op:"contains",value:"interest"},{field:"description",op:"contains",value:"income"}],columns:["date","description","amount","bank","rate","period"],autoMatch:true},
  {id:"c8",name:"Interest Expense",color:"#ef4444",icon:"💸",type:"expense",side:"debit",conditions:[{field:"description",op:"contains",value:"interest"},{field:"description",op:"contains",value:"debit"}],columns:["date","description","amount","bank","rate","principal","valueDate"],autoMatch:true},
  {id:"c9",name:"Stamp Duty / Tax",color:"#f97316",icon:"🏛",type:"expense",side:"debit",conditions:[{field:"description",op:"contains",value:"stamp"},{field:"description",op:"contains",value:"duty"}],columns:["date","description","amount","bank","taxCode","rate"],autoMatch:true},
  {id:"c10",name:"FX Conversion",color:"#a78bfa",icon:"💱",type:"transfer",side:"both",conditions:[{field:"description",op:"contains",value:"FX"},{field:"description",op:"contains",value:"conversion"}],columns:["date","description","amount","bank","fromCcy","toCcy","rate"],autoMatch:false},
];

const USERS = [
  {id:"u1",name:"Admin User",email:"admin@company.com",role:"admin",group:"Administrators",company:"Global Corp",active:true,avatar:"AD"},
  {id:"u2",name:"Sarah Chen",email:"s.chen@corp.com",role:"senior_accountant",group:"Finance Team",company:"Global Corp",active:true,avatar:"SC"},
  {id:"u3",name:"Mark Thomas",email:"m.thomas@corp.com",role:"approver",group:"Management",company:"Global Corp",active:true,avatar:"MT"},
  {id:"u4",name:"Fatima Al-Rashid",email:"f.rashid@corp.com",role:"viewer",group:"Finance Team",company:"Global Corp",active:false,avatar:"FA"},
  {id:"u5",name:"James Liu",email:"j.liu@corp.com",role:"analyst",group:"FP&A Team",company:"Global Corp",active:true,avatar:"JL"},
];

const ROLE_GROUPS = [
  {id:"g1",name:"Administrators",permissions:["all"],company:"Global Corp"},
  {id:"g2",name:"Finance Team",permissions:["reconcile","upload","view_analysis","view_reports"],company:"Global Corp"},
  {id:"g3",name:"Management",permissions:["approve","view_analysis","view_cashflow","view_reports"],company:"Global Corp"},
  {id:"g4",name:"FP&A Team",permissions:["view_cashflow","edit_forecast","view_analysis","view_reports"],company:"Global Corp"},
  {id:"g5",name:"Top Management",permissions:["view_dashboard","view_cashflow","view_reports","view_kpi"],company:"Global Corp"},
];

const INTEREST_RATES = [
  {date:"2025-01-01",rate:"SOFR",value:5.30,change:0},
  {date:"2025-01-08",rate:"SOFR",value:5.30,change:0},
  {date:"2025-01-15",rate:"SOFR",value:5.30,change:0},
  {date:"2025-01-22",rate:"SOFR",value:5.28,change:-0.02},
  {date:"2025-01-01",rate:"EURIBOR",value:3.89,change:0},
  {date:"2025-01-15",rate:"EURIBOR",value:3.87,change:-0.02},
];

const CF_DATA = {
  months:["Aug","Sep","Oct","Nov","Dec","Jan"],
  operating:{
    actual:[1850000,2100000,1750000,2400000,3100000,2800000],
    aiForecast:[1900000,2050000,1800000,2350000,3050000,2750000],
    fpaForecast:[1820000,2150000,1700000,2450000,3200000,2900000],
  },
  investing:{
    actual:[-450000,-890000,-320000,-670000,-1200000,-550000],
    aiForecast:[-480000,-870000,-300000,-690000,-1150000,-580000],
    fpaForecast:[-420000,-950000,-340000,-650000,-1250000,-500000],
  },
  financing:{
    actual:[-250000,500000,-180000,-350000,800000,-420000],
    aiForecast:[-230000,520000,-200000,-330000,780000,-400000],
    fpaForecast:[-270000,480000,-160000,-370000,820000,-440000],
  },
};

const BANK_CHARGES = [
  {bank:"HSBC",type:"Account Maintenance",amount:450,period:"Jan 2025",category:"c1"},
  {bank:"HSBC",type:"SWIFT Transfer Fee",amount:85,period:"Jan 2025",category:"c1"},
  {bank:"Citi",type:"Account Maintenance",amount:380,period:"Jan 2025",category:"c1"},
  {bank:"Citi",type:"FX Commission",amount:1250,period:"Jan 2025",category:"c1"},
  {bank:"Barclays",type:"Account Maintenance",amount:290,period:"Jan 2025",category:"c1"},
  {bank:"JPMorgan",type:"Wire Fee",amount:175,period:"Jan 2025",category:"c1"},
  {bank:"Deutsche",type:"Arrangement Fee",amount:15000,period:"Jan 2025",category:"c1"},
  {bank:"Deutsche",type:"Commitment Fee",amount:7500,period:"Jan 2025",category:"c1"},
];

const MATCHING_RULES_CUSTOM = [
  {id:"mr1",name:"HSBC FX Same-Day",bank:"HSBC",type:"specific",condition:"amount_match AND date_match AND description_contains('FX')",priority:1,active:true},
  {id:"mr2",name:"Citi Payroll Weekly",bank:"Citi",type:"pattern",condition:"day_of_week('Friday') AND description_contains('Payroll') AND amount_tolerance(0.5%)",priority:2,active:true},
  {id:"mr3",name:"All Banks - Bank Charges",bank:"ALL",type:"category",condition:"description_contains('charge','fee','maintenance') AND amount_less_than(5000)",priority:3,active:true},
  {id:"mr4",name:"Reversal Detection",bank:"ALL",type:"reversal",condition:"amount_equals(negative_of(prior_transaction)) AND date_within(5_days)",priority:4,active:true},
];


// ─── MOCK_TXN (needed by ReconcileTab) ────────────────────────
const MOCK_TXN=[
  {id:"T001",date:"2025-01-15",desc:"SWIFT Transfer - Vendor Alpha Ltd",bank:15000.00,gl:15000.00,status:"matched",conf:99,glRef:"1000045",rule:"Exact Match",catId:"c5"},
  {id:"T002",date:"2025-01-15",desc:"ACH Payment #4521 Payroll Run",bank:3200.50,gl:3200.50,status:"matched",conf:100,glRef:"1000046",rule:"ML Pattern",catId:"c4"},
  {id:"T003",date:"2025-01-16",desc:"Bank Service Charges Q1",bank:450.00,gl:null,status:"unmatched",conf:0,glRef:null,rule:null,catId:"c1"},
  {id:"T004",date:"2025-01-16",desc:"Wire Transfer Client XYZ Corp",bank:82000.00,gl:82000.00,status:"matched",conf:97,glRef:"1000048",rule:"AI Fuzzy",catId:"c6"},
  {id:"T005",date:"2025-01-17",desc:"Interest Expense - Debt Facility",bank:74250.00,gl:74250.00,status:"matched",conf:95,glRef:"1000049",rule:"Interest Calc",catId:"c8"},
  {id:"T006",date:"2025-01-17",desc:"Payroll Run January Week 3",bank:245600.00,gl:245600.00,status:"matched",conf:100,glRef:"1000050",rule:"ML Pattern",catId:"c4"},
  {id:"T007",date:"2025-01-18",desc:"FX Conversion USD to EUR",bank:50000.00,gl:null,status:"unmatched",conf:0,glRef:null,rule:null,catId:"c10"},
  {id:"T008",date:"2025-01-18",desc:"T-Bill Maturity Receipt",bank:500000.00,gl:500000.00,status:"matched",conf:98,glRef:"1000052",rule:"Category Rule",catId:"c2"},
  {id:"T009",date:"2025-01-18",desc:"Reversal - T-Bill Purchase Jan-03",bank:-500000.00,gl:-500000.00,status:"matched",conf:99,glRef:"1000053",rule:"Reversal Detect",catId:"c3"},
  {id:"T010",date:"2025-01-19",desc:"Stamp Duty on Facility",bank:8250.00,gl:null,status:"unmatched",conf:0,glRef:null,rule:null,catId:"c9"},
];


const PREV_UNMATCHED = [
  {id:"PU001",date:"2024-12-28",desc:"Wire Transfer - Pending Confirm",bank:15000,gl:null,status:"unmatched",period:"Dec 2024",bankName:"HSBC"},
  {id:"PU002",date:"2024-12-30",desc:"FX Conversion EUR/USD",bank:42000,gl:null,status:"unmatched",period:"Dec 2024",bankName:"Citi"},
  {id:"PU003",date:"2024-12-31",desc:"Year-End Accrual Reversal",bank:-8500,gl:null,status:"unmatched",period:"Dec 2024",bankName:"Barclays"},
];

// ─── ANALYTICS VISIBILITY SETTINGS ───────────────────────────
const DEFAULT_ANALYTICS_MODULES = {
  cashflow_statement:true,
  cash_position:true,
  cash_conversion:true,
  cash_trend:true,
  budget_vs_actual:true,
  ai_forecast:true,
  fpa_forecast:true,
  variance_analysis:true,
  bank_charges_analysis:true,
  interest_calculation:true,
  cashin_cashout_breakdown:true,
  category_analysis:true,
  forecast_insights:true,
};

// ─── HELPER COMPONENTS ────────────────────────────────────────
function Notif({n,onClose}){
  useEffect(()=>{const t=setTimeout(onClose,5000);return()=>clearTimeout(t)},[onClose]);
  const map={success:{bg:"#002a1c",bc:"#00c486",ic:"✓"},error:{bg:"#2a0a0a",bc:"#dc2626",ic:"✕"},info:{bg:"#0a1130",bc:"#3d5fef",ic:"ℹ"},warn:{bg:"#2a1800",bc:"#d97706",ic:"⚠"}};
  const c=map[n.type]||map.info;
  return <div className="notif" style={{background:c.bg,border:`1px solid ${c.bc}`}}>
    <span style={{color:c.bc,fontSize:16}}>{c.ic}</span>
    <span style={{flex:1,lineHeight:1.4}}>{n.msg}</span>
    <span style={{cursor:"pointer",color:"#3a4e72",marginLeft:6,fontSize:14}} onClick={onClose}>✕</span>
  </div>;
}

function Toggle({on,onChange,size="md"}){
  const h=size==="sm"?18:21,w=size==="sm"?34:40;
  return <button className="toggle" style={{background:on?"#2a46cc":"#101828",width:w,height:h}} onClick={()=>onChange(!on)}>
    <div className="toggle-dot" style={{left:on?w-h+2.5:2.5,width:h-5,height:h-5}}/>
  </button>;
}

function DonutChart({val,size=80,color="#00c486",label,sublabel}){
  const r=(size/2)-8;const c=2*Math.PI*r;const s=c*Math.min(val,100)/100;
  return <div className="donut-wrap" style={{width:size,height:size}}>
    <svg width={size} height={size} style={{transform:"rotate(-90deg)"}}>
      <circle cx={size/2} cy={size/2} r={r} fill="none" stroke="#0f1525" strokeWidth={7}/>
      <circle cx={size/2} cy={size/2} r={r} fill="none" stroke={color} strokeWidth={7}
        strokeDasharray={`${s} ${c}`} strokeLinecap="round" style={{transition:"stroke-dasharray 1.1s ease"}}/>
    </svg>
    <div style={{position:"absolute",inset:0,display:"flex",flexDirection:"column",alignItems:"center",justifyContent:"center"}}>
      <span style={{fontSize:size>70?13:10,fontWeight:800,color,lineHeight:1}}>{val}%</span>
      {label&&<span style={{fontSize:size>70?8:7,color:"#2e3f60",textAlign:"center",lineHeight:1.2,marginTop:2,maxWidth:size-20}}>{label}</span>}
    </div>
  </div>;
}

function MiniBar({data,labels,colors,height=54}){
  const mx=Math.max(...data.map(Math.abs),1);
  return <div style={{display:"flex",alignItems:"flex-end",gap:2,height}}>
    {data.map((v,i)=><div key={i} style={{flex:1,display:"flex",flexDirection:"column",alignItems:"center",gap:3}}>
      <div style={{width:"100%",background:colors?.[i]||(v<0?"#dc2626":"#2a46cc"),borderRadius:"3px 3px 0 0",height:`${(Math.abs(v)/mx)*(height-16)}px`,opacity:.82,transition:"height .6s ease",minHeight:2}}/>
      {labels&&<span style={{fontSize:7,color:"#2e3f60",textAlign:"center"}}>{labels[i]}</span>}
    </div>)}
  </div>;
}

function LineChart({datasets,labels,height=120}){
  const allVals=datasets.flatMap(d=>d.data);
  const mn=Math.min(...allVals);const mx=Math.max(...allVals);const rng=mx-mn||1;
  const W=400,H=height,PL=30,PB=20,PR=10,PT=10;
  const cw=W-PL-PR;const ch=H-PB-PT;
  const toX=(i)=>PL+i*(cw/(labels.length-1));
  const toY=(v)=>PT+ch-(((v-mn)/rng)*ch);
  return <svg viewBox={`0 0 ${W} ${H}`} style={{width:"100%",height,overflow:"visible"}}>
    {/* Grid */}
    {[0,.25,.5,.75,1].map((t,i)=>{
      const y=PT+ch*t;
      const val=mx-(t*rng);
      return <g key={i}>
        <line x1={PL} y1={y} x2={W-PR} y2={y} stroke="#0f1525" strokeWidth={1}/>
        <text x={PL-4} y={y+4} textAnchor="end" fontSize={8} fill="#2e3f60">{(val/1000).toFixed(0)}k</text>
      </g>;
    })}
    {/* X labels */}
    {labels.map((l,i)=><text key={i} x={toX(i)} y={H-4} textAnchor="middle" fontSize={8} fill="#2e3f60">{l}</text>)}
    {/* Lines */}
    {datasets.map((ds,di)=>{
      const pts=ds.data.map((v,i)=>`${toX(i)},${toY(v)}`).join(" ");
      return <g key={di}>
        <polyline points={pts} fill="none" stroke={ds.color} strokeWidth={2} strokeLinecap="round" strokeLinejoin="round" strokeDasharray={ds.dashed?"6 3":"none"} opacity={.9}/>
        {ds.data.map((v,i)=><circle key={i} cx={toX(i)} cy={toY(v)} r={3} fill={ds.color}/>)}
      </g>;
    })}
  </svg>;
}

function ProgBar({val,color,className=""}){
  return <div className={`prog ${className}`}><div className="prog-fill" style={{width:`${Math.min(val,100)}%`,background:color||"#2a46cc"}}/></div>;
}

function ConfPill({v}){
  const c=v>=90?"#00c486":v>=65?"#d97706":"#dc2626";
  return <span className="badge" style={{background:c+"22",color:c}}>{v}%</span>;
}

function SectionHdr({title,sub,action}){
  return <div className="sec-hdr">
    <div>
      <h2 style={{fontSize:15,fontWeight:800}}>{title}</h2>
      {sub&&<p style={{fontSize:11.5,color:"#3a4e72",marginTop:3,lineHeight:1.5}}>{sub}</p>}
    </div>
    {action&&<div style={{flexShrink:0}}>{action}</div>}
  </div>;
}

function ViewBanner({mode,onChange}){
  const modes=[
    {id:"user",label:"Accountant View",color:"#3d5fef"},
    {id:"manager",label:"Manager View",color:"#7c3aed"},
    {id:"top_mgmt",label:"Executive View",color:"#0d9488"},
    {id:"analyst",label:"FP&A View",color:"#d97706"},
  ];
  const cur=modes.find(m=>m.id===mode)||modes[0];
  return <div style={{display:"flex",gap:6,alignItems:"center",background:"#0a0d1e",border:"1px solid #182040",borderRadius:10,padding:"5px 8px"}}>
    <span style={{fontSize:11,color:"#3a4e72",marginRight:4}}>View:</span>
    {modes.map(m=>(
      <button key={m.id} className="btn btn-xs" onClick={()=>onChange(m.id)}
        style={{background:mode===m.id?m.color+"33":"transparent",border:`1px solid ${mode===m.id?m.color:"transparent"}`,color:mode===m.id?m.color:"#3a4e72"}}>
        {m.label}
      </button>
    ))}
  </div>;
}

// ═══════════════════════════════════════════════════════════════
// CHATBOT COMPONENT
// ═══════════════════════════════════════════════════════════════
function ChatBot({currentUser,banks,categories,notify,onClose}){
  const [msgs,setMsgs]=useState([
    {role:"bot",text:`Hello ${currentUser.name}! I'm your BankRecon AI assistant. I can help you with reconciliation, analysis, cashflow insights, interest calculations, and more. Ask me anything about your financial data.`,ts:new Date()}
  ]);
  const [input,setInput]=useState("");
  const [loading,setLoading]=useState(false);
  const [expanded,setExpanded]=useState(false);
  const endRef=useRef(null);
  const inputRef=useRef(null);

  useEffect(()=>{endRef.current?.scrollIntoView({behavior:"smooth"})},[msgs]);

  const QUICK_PROMPTS={
    admin:["Show me overall reconciliation status","Which banks have unmatched items?","Calculate interest for debt facilities","Show bank charges analysis","What's the cash position today?"],
    senior_accountant:["Show me HSBC unmatched transactions","What are the bank charges this month?","Show me reversal transactions","Check Citi reconciliation status"],
    approver:["Which items need approval?","Show variance summary","Outstanding items report"],
    analyst:["Show cashflow vs forecast","AI insights on variances","Cash conversion trend","FP&A vs AI forecast comparison"],
    viewer:["Show reconciliation summary","Dashboard overview"],
  };
  const prompts=(QUICK_PROMPTS[currentUser.role]||QUICK_PROMPTS.viewer);

  const buildContext=()=>{
    // Role-based context building
    const baseContext=`You are BankRecon AI, an embedded financial assistant for a bank reconciliation platform. 
User: ${currentUser.name}, Role: ${currentUser.role}, Company: ${currentUser.company}.
Access level: ${currentUser.role==="admin"?"Full access":currentUser.role==="senior_accountant"?"Can reconcile and view all":"Limited view"}.

Current data summary:
- Banks: ${banks.length} active (${banks.filter(b=>b.status==="reconciled").length} reconciled, ${banks.filter(b=>b.status==="review").length} under review, ${banks.filter(b=>b.status==="open").length} open)
- Total variance: $${banks.reduce((a,b)=>a+Math.abs(b.balance-b.glBalance),0).toLocaleString()}
- Debt facilities: ${banks.filter(b=>b.isDebt).length} (rates: SOFR+1.75%, EURIBOR+2.5%)
- Categories configured: ${categories.length}
- Previous unmatched: 3 transactions from Dec 2024

IMPORTANT: Only provide information appropriate for the user's role. Admin can see everything. Viewers can only see summaries. Never reveal sensitive API keys or system settings to non-admin users. Be concise and professional.`;
    return baseContext;
  };

  const send=async()=>{
    if(!input.trim()||loading)return;
    const userMsg={role:"user",text:input.trim(),ts:new Date()};
    setMsgs(m=>[...m,userMsg]);
    setInput("");
    setLoading(true);
    try{
      const res=await fetch("https://api.anthropic.com/v1/messages",{
        method:"POST",
        headers:{"Content-Type":"application/json"},
        body:JSON.stringify({
          model:"claude-sonnet-4-20250514",
          max_tokens:1000,
          system:buildContext(),
          messages:[
            ...msgs.filter(m=>m.role!=="bot"||msgs.indexOf(m)>0).map(m=>({role:m.role==="user"?"user":"assistant",content:m.text})),
            {role:"user",content:input.trim()}
          ]
        })
      });
      const data=await res.json();
      const reply=data.content?.[0]?.text||"I'm sorry, I couldn't process that request. Please try again.";
      setMsgs(m=>[...m,{role:"bot",text:reply,ts:new Date()}]);
    }catch(e){
      setMsgs(m=>[...m,{role:"bot",text:"I'm having trouble connecting right now. Please check your API configuration in Settings → AI & API Keys.",ts:new Date()}]);
    }
    setLoading(false);
  };

  const w=expanded?520:360;
  return <div style={{position:"fixed",bottom:24,right:24,width:w,zIndex:2000,display:"flex",flexDirection:"column",transition:"width .3s ease"}}>
    <div className="glass" style={{borderRadius:20,overflow:"hidden",boxShadow:"0 20px 60px rgba(0,0,0,.6)",display:"flex",flexDirection:"column",height:expanded?600:460}}>
      {/* Header */}
      <div style={{background:"linear-gradient(135deg,#0d162e,#131c38)",padding:"14px 16px",display:"flex",alignItems:"center",gap:12,borderBottom:"1px solid rgba(255,255,255,.06)"}}>
        <div style={{width:36,height:36,borderRadius:10,background:"linear-gradient(135deg,#2a46cc,#00c486)",display:"flex",alignItems:"center",justifyContent:"center",fontSize:18,flexShrink:0}}>🧠</div>
        <div style={{flex:1}}>
          <div style={{fontWeight:800,fontSize:13}}>BankRecon AI</div>
          <div style={{fontSize:10,color:"#3a4e72",display:"flex",alignItems:"center",gap:5}}>
            <span className="pulse" style={{width:6,height:6,borderRadius:"50%",background:"#00c486",display:"inline-block"}}/>
            Active · {currentUser.role} access
          </div>
        </div>
        <div style={{display:"flex",gap:6}}>
          <button className="btn btn-ghost btn-xs" onClick={()=>setExpanded(e=>!e)}>{expanded?"⊡":"⊞"}</button>
          <button className="btn btn-ghost btn-xs" onClick={onClose}>✕</button>
        </div>
      </div>

      {/* Messages */}
      <div style={{flex:1,overflowY:"auto",padding:"14px 14px 0",display:"flex",flexDirection:"column",gap:10}}>
        {msgs.map((m,i)=><div key={i} style={{display:"flex",flexDirection:"column",alignItems:m.role==="user"?"flex-end":"flex-start",gap:4}}>
          <div className={`chat-bubble ${m.role==="user"?"chat-user":"chat-bot"}`} style={{fontSize:12.5,lineHeight:1.6}}>
            {m.text.split('\n').map((line,li)=><span key={li}>{line}{li<m.text.split('\n').length-1&&<br/>}</span>)}
          </div>
          <span style={{fontSize:9,color:"#2e3f60",marginTop:2}}>
            {m.ts.toLocaleTimeString([], {hour:'2-digit',minute:'2-digit'})}
            {m.role==="bot"&&" · AI"}
          </span>
        </div>)}
        {loading&&<div className="chat-bubble chat-bot" style={{display:"flex",gap:6,alignItems:"center"}}>
          <span className="spin">⟳</span><span style={{fontSize:12,color:"#3a4e72"}}>Analyzing your request...</span>
        </div>}
        <div ref={endRef}/>
      </div>

      {/* Quick prompts */}
      <div style={{padding:"10px 14px 0",display:"flex",gap:5,flexWrap:"wrap"}}>
        {prompts.slice(0,3).map((p,i)=>(
          <button key={i} className="btn btn-ghost btn-xs" onClick={()=>{setInput(p);setTimeout(()=>inputRef.current?.focus(),50);}} style={{fontSize:10,padding:"3px 8px"}}>
            {p}
          </button>
        ))}
      </div>

      {/* Input */}
      <div style={{padding:14,display:"flex",gap:8,borderTop:"1px solid rgba(255,255,255,.04)",marginTop:10}}>
        <input ref={inputRef} value={input} onChange={e=>setInput(e.target.value)}
          onKeyDown={e=>e.key==="Enter"&&!e.shiftKey&&send()}
          placeholder={`Ask about ${currentUser.role==="admin"?"anything...":"your data..."}`}
          style={{flex:1,borderRadius:10}}/>
        <button className="btn btn-primary btn-sm" onClick={send} disabled={loading||!input.trim()}>
          {loading?<span className="spin">⟳</span>:"▶"}
        </button>
      </div>
    </div>
  </div>;
}

// ═══════════════════════════════════════════════════════════════
// DASHBOARD TAB
// ═══════════════════════════════════════════════════════════════
function DashboardTab({banks,categories,viewMode,notify,currentUser}){
  const totalMatch=banks.reduce((a,b)=>a+b.matched,0);
  const totalItems=banks.reduce((a,b)=>a+b.items,0);
  const matchRate=Math.round((totalMatch/totalItems)*100);
  const totalVariance=banks.reduce((a,b)=>a+Math.abs(b.balance-b.glBalance),0);
  const isExec=viewMode==="top_mgmt";

  // Executive view: less detail, bigger numbers
  const KPIs=isExec?[
    {label:"Overall Match Rate",val:`${matchRate}%`,color:"#00c486",icon:"⇌",sub:"All banks combined"},
    {label:"Total Variance",val:`$${(totalVariance/1000).toFixed(0)}k`,color:"#d97706",icon:"△",sub:"Needs resolution"},
    {label:"Banks Reconciled",val:`${banks.filter(b=>b.status==="reconciled").length}/${banks.length}`,color:"#00c486",icon:"✓",sub:"Current period"},
    {label:"Cash Position",val:"$8.2M",color:"#3d5fef",icon:"💵",sub:"Net across all accounts"},
    {label:"Debt Exposure",val:"$23.2M",color:"#dc2626",icon:"📊",sub:"Active facilities"},
    {label:"AI Accuracy",val:"94.2%",color:"#7c3aed",icon:"🧠",sub:"Model confidence"},
  ]:[
    {label:"Active Banks",val:banks.length,color:"#3d5fef",icon:"🏦",sub:"This period"},
    {label:"Match Rate",val:`${matchRate}%`,color:"#00c486",icon:"⇌",sub:`${totalMatch}/${totalItems}`},
    {label:"Reconciled",val:banks.filter(b=>b.status==="reconciled").length,color:"#00c486",icon:"✓",sub:"Zero variance"},
    {label:"Total Variance",val:`$${(totalVariance/1000).toFixed(1)}k`,color:"#d97706",icon:"△",sub:"Needs resolution"},
    {label:"Review Pending",val:banks.filter(b=>b.status==="review").length,color:"#d97706",icon:"⚠",sub:"Awaiting action"},
    {label:"Prev. Unmatched",val:PREV_UNMATCHED.length,color:"#dc2626",icon:"↩",sub:"From Dec 2024"},
    {label:"Categories",val:categories.length,color:"#3d5fef",icon:"🗂",sub:"Auto-classify"},
    {label:"AI Confidence",val:"94.2%",color:"#7c3aed",icon:"🧠",sub:"Current model"},
  ];

  return <div className="fadeUp" style={{display:"flex",flexDirection:"column",gap:20}}>
    {/* KPI Grid */}
    <div style={{display:"grid",gridTemplateColumns:`repeat(auto-fit,minmax(${isExec?200:170}px,1fr))`,gap:12}}>
      {KPIs.map((k,i)=><div key={i} className="kpi-card">
        <div style={{display:"flex",justifyContent:"space-between",marginBottom:10}}>
          <span style={{fontSize:20}}>{k.icon}</span>
          <span style={{fontSize:9,color:"#2e3f60",letterSpacing:".08em",textTransform:"uppercase",textAlign:"right"}}>{k.label}</span>
        </div>
        <div style={{fontSize:isExec?30:26,fontWeight:800,color:k.color,letterSpacing:"-.01em"}}>{k.val}</div>
        <div style={{fontSize:10.5,color:"#3a4e72",marginTop:3}}>{k.sub}</div>
      </div>)}
    </div>

    <div style={{display:"grid",gridTemplateColumns:isExec?"1fr 1fr":"3fr 2fr",gap:16}}>
      {/* Bank Status */}
      <div className="card">
        <SectionHdr title="Bank Reconciliation Status" sub="Real-time matching progress with GL account mapping"/>
        {banks.map(b=>{
          const pct=Math.round((b.matched/b.items)*100);
          return <div key={b.id} style={{marginBottom:14}}>
            <div style={{display:"flex",justifyContent:"space-between",marginBottom:5,gap:8,flexWrap:"wrap"}}>
              <div>
                <span style={{fontWeight:700,fontSize:12.5}}>{b.name}</span>
                <span className="mono" style={{fontSize:9,color:"#3a4e72",marginLeft:8}}>GL:{b.glAccount}</span>
                {b.isDebt&&<span className="badge" style={{background:"#dc262622",color:"#dc2626",marginLeft:6}}>DEBT</span>}
              </div>
              <div style={{display:"flex",gap:5,alignItems:"center",flexShrink:0}}>
                <ConfPill v={pct}/>
                <span className="badge" style={{background:SC[b.status]+"22",color:SC[b.status]}}>{b.status}</span>
              </div>
            </div>
            <ProgBar val={pct} color={SC[b.status]}/>
            <div style={{display:"flex",justifyContent:"space-between",marginTop:3,fontSize:10,color:"#3a4e72"}}>
              <span>{b.matched} matched · {b.unmatched} unmatched</span>
              <span style={{color:Math.abs(b.balance-b.glBalance)>0?"#d97706":"#00c486"}}>
                {b.currency} {Math.abs(b.balance-b.glBalance).toLocaleString()} var
              </span>
            </div>
          </div>;
        })}
      </div>

      {/* Right panel */}
      <div style={{display:"flex",flexDirection:"column",gap:14}}>
        <div className="card">
          <h3 style={{fontSize:12,fontWeight:700,color:"#5a7af5",marginBottom:14}}>PERIOD BREAKDOWN</h3>
          <div style={{display:"flex",justifyContent:"space-around",flexWrap:"wrap",gap:8}}>
            {[{l:"Auto-matched",v:matchRate,c:"#00c486"},{l:"Partial",v:6,c:"#d97706"},{l:"Unmatched",v:100-matchRate-6,c:"#dc2626"}].map(d=>(
              <div key={d.l} style={{textAlign:"center"}}><DonutChart val={d.v} size={74} color={d.c} label={d.l}/></div>
            ))}
          </div>
        </div>

        {/* Previous Unmatched Alert */}
        <div className="card" style={{borderColor:"#dc262640",background:"rgba(220,38,38,.04)"}}>
          <div style={{display:"flex",gap:8,marginBottom:12}}>
            <span style={{color:"#dc2626",fontSize:18}}>⚠</span>
            <div>
              <div style={{fontWeight:700,fontSize:12.5,color:"#dc2626"}}>Previous Period Unmatched</div>
              <div style={{fontSize:11,color:"#5a4040",marginTop:2}}>3 items from Dec 2024 need attention</div>
            </div>
          </div>
          {PREV_UNMATCHED.map(p=><div key={p.id} style={{padding:"6px 0",borderBottom:"1px solid rgba(220,38,38,.1)",fontSize:11}}>
            <div style={{display:"flex",justifyContent:"space-between"}}>
              <span style={{color:"#c8a0a0"}}>{p.desc}</span>
              <span className="mono" style={{color:"#dc2626"}}>${p.bank?.toLocaleString()}</span>
            </div>
            <div style={{fontSize:9.5,color:"#5a4040",marginTop:1}}>{p.bankName} · {p.date}</div>
          </div>)}
          <button className="btn btn-danger btn-sm" style={{marginTop:10,width:"100%",justifyContent:"center"}} onClick={()=>notify("Cross-period reconciliation started","info")}>
            ↩ Reconcile with Current Period
          </button>
        </div>

        <div className="card">
          <h3 style={{fontSize:12,fontWeight:700,color:"#5a7af5",marginBottom:12}}>7-DAY TREND</h3>
          <MiniBar data={[88,91,89,93,94,95,matchRate]} labels={["Mon","Tue","Wed","Thu","Fri","Sat","Sun"]}
            colors={Array(7).fill(null).map((_,i)=>i===6?"#00c486":"#2a46cc")}/>
        </div>
      </div>
    </div>

    {/* Category Quick View */}
    <div className="card">
      <SectionHdr title="Transaction Categories Overview" sub="Auto-classified transactions by category this period"
        action={<button className="btn btn-ghost btn-sm">Manage in Settings →</button>}/>
      <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fill,minmax(180px,1fr))",gap:10}}>
        {categories.slice(0,8).map(cat=>{
          const count=Math.floor(Math.random()*150+20);
          const amt=Math.floor(Math.random()*500000+10000);
          return <div key={cat.id} className="card-flat" style={{padding:12,borderLeft:`3px solid ${cat.color}`}}>
            <div style={{display:"flex",justifyContent:"space-between",marginBottom:6}}>
              <span style={{fontSize:18}}>{cat.icon}</span>
              <span className="badge" style={{background:cat.color+"22",color:cat.color,fontSize:9}}>{cat.type.toUpperCase()}</span>
            </div>
            <div style={{fontWeight:700,fontSize:12.5,marginBottom:3}}>{cat.name}</div>
            <div className="mono" style={{fontSize:11,color:cat.type==="cashin"||cat.type==="investment"?"#00c486":"#dc2626"}}>
              ${(amt/1000).toFixed(0)}k
            </div>
            <div style={{fontSize:10,color:"#3a4e72",marginTop:2}}>{count} transactions</div>
          </div>;
        })}
      </div>
    </div>

    {/* Activity Log */}
    <div className="card">
      <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:16}}>
        <h3 style={{fontSize:13,fontWeight:700}}>AI Activity Log</h3>
        <span className="badge pulse" style={{background:"#00c48622",color:"#00c486"}}>● LIVE</span>
      </div>
      {[
        {t:"2 min ago",m:"AI categorized 47 transactions as Bank Charges using category rules",type:"success"},
        {t:"8 min ago",m:"Cross-period reconciliation: 2 Dec 2024 items matched to Jan 2025 entries",type:"success"},
        {t:"15 min ago",m:"Reversal detected: T-Bill maturity reversal $450,000 — same-side match applied",type:"info"},
        {t:"1h ago",m:"Interest calculation completed: Deutsche Facility — $74,250 (EURIBOR 3.87% + 2.50% × 30 days)",type:"info"},
        {t:"2h ago",m:"ML model retrained with 47 corrections — category pattern accuracy: 96.8%",type:"success"},
      ].map((a,i)=><div key={i} style={{display:"flex",gap:10,padding:"9px 0",borderBottom:"1px solid #0a0f1e"}}>
        <span style={{color:a.type==="success"?"#00c486":"#3d5fef",fontSize:14}}>{a.type==="success"?"✓":"ℹ"}</span>
        <div style={{flex:1}}>
          <div style={{fontSize:12,color:"#b0bcd8"}}>{a.m}</div>
          <div style={{fontSize:10,color:"#2e3f60",marginTop:2}}>{a.t}</div>
        </div>
      </div>)}
    </div>
  </div>;
}

// ═══════════════════════════════════════════════════════════════
// RECONCILE TAB — Enhanced with cross-period & custom matching
// ═══════════════════════════════════════════════════════════════
function ReconcileTab({banks,categories,customRules,notify}){
  const [selBank,setSelBank]=useState(banks[0]);
  const [filter,setFilter]=useState("all");
  const [running,setRunning]=useState(false);
  const [selected,setSelected]=useState([]);
  const [showPrev,setShowPrev]=useState(false);
  const [reconcileMode,setReconcileMode]=useState("current"); // current | crossperiod
  const [assignCat,setAssignCat]=useState(null); // txn being assigned a category
  const [showCustomRule,setShowCustomRule]=useState(false);

  const txns=MOCK_TXN.filter(t=>filter==="all"||t.status===filter);
  const runAI=()=>{
    setRunning(true);
    setTimeout(()=>{setRunning(false);notify("AI matched 8 items. 2 categorized as Bank Charges. 1 reversal detected.","success");},3000);
  };

  return <div className="fadeUp" style={{display:"flex",gap:18}}>
    {/* Bank list */}
    <div style={{width:210,flexShrink:0}}>
      <div className="card" style={{padding:12}}>
        <div style={{fontSize:10,fontWeight:700,color:"#2e3f60",marginBottom:12,letterSpacing:".08em"}}>SELECT BANK</div>
        {banks.map(b=><div key={b.id} onClick={()=>setSelBank(b)} style={{
          padding:"10px 11px",borderRadius:9,cursor:"pointer",marginBottom:4,
          background:selBank.id===b.id?"rgba(42,70,204,.14)":"transparent",
          border:`1px solid ${selBank.id===b.id?"#2a46cc":"transparent"}`,transition:"all .17s"
        }}>
          <div style={{display:"flex",justifyContent:"space-between",gap:4}}>
            <span style={{fontSize:12,fontWeight:700}}>{b.short}</span>
            <div style={{display:"flex",gap:4}}>
              {b.isDebt&&<span className="badge" style={{background:"#dc262622",color:"#dc2626",fontSize:8}}>DEBT</span>}
              <span className="badge" style={{background:SC[b.status]+"22",color:SC[b.status],fontSize:8}}>{b.currency}</span>
            </div>
          </div>
          <div className="mono" style={{fontSize:9.5,color:"#3a4e72",marginTop:2}}>GL:{b.glAccount}</div>
          <div style={{fontSize:10,color:SC[b.status],marginTop:2}}>● {b.status}</div>
        </div>)}
      </div>

      {/* Custom Rules Summary */}
      <div className="card" style={{padding:12,marginTop:14}}>
        <div style={{fontSize:10,fontWeight:700,color:"#2e3f60",marginBottom:10,letterSpacing:".08em"}}>ACTIVE CUSTOM RULES</div>
        {customRules.filter(r=>r.active&&(r.bank==="ALL"||banks.find(b=>b.short===r.bank)?.id===selBank.id)).slice(0,3).map(r=>(
          <div key={r.id} style={{padding:"6px 8px",background:"#0a0d1e",borderRadius:8,marginBottom:5,borderLeft:`2px solid #2a46cc`}}>
            <div style={{fontSize:10.5,fontWeight:700,color:"#7a90cc"}}>{r.name}</div>
            <div style={{fontSize:9.5,color:"#3a4e72",marginTop:2}}>{r.type}</div>
          </div>
        ))}
        <button className="btn btn-ghost btn-xs" style={{width:"100%",justifyContent:"center",marginTop:8}} onClick={()=>setShowCustomRule(true)}>
          + Add Rule for {selBank.short}
        </button>
      </div>
    </div>

    {/* Main */}
    <div style={{flex:1,display:"flex",flexDirection:"column",gap:14,minWidth:0}}>
      {/* Header */}
      <div className="card" style={{padding:"12px 18px"}}>
        <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start",flexWrap:"wrap",gap:10}}>
          <div>
            <div style={{display:"flex",gap:10,alignItems:"center"}}>
              <h2 style={{fontSize:17,fontWeight:800}}>{selBank.name}</h2>
              {selBank.isDebt&&<span className="badge" style={{background:"#dc262622",color:"#dc2626"}}>DEBT FACILITY</span>}
            </div>
            <div style={{fontSize:11.5,color:"#5a7af5",marginTop:4,display:"flex",gap:14,flexWrap:"wrap"}}>
              <span>Bank: <span className="mono" style={{color:"#dde2f2"}}>{selBank.currency} {selBank.balance.toLocaleString()}</span></span>
              <span>GL({selBank.glAccount}): <span className="mono" style={{color:"#dde2f2"}}>{selBank.currency} {selBank.glBalance.toLocaleString()}</span></span>
              <span>Δ Variance: <span className="mono" style={{color:Math.abs(selBank.balance-selBank.glBalance)>0?"#d97706":"#00c486"}}>
                {selBank.currency} {Math.abs(selBank.balance-selBank.glBalance).toLocaleString()}
              </span></span>
              {selBank.isDebt&&<span>Rate: <span className="mono" style={{color:"#dc2626"}}>{selBank.currentRate}% p.a.</span></span>}
            </div>
          </div>
          <div style={{display:"flex",gap:7,flexWrap:"wrap"}}>
            <button className="btn btn-ghost btn-sm" onClick={()=>notify("Exported!","success")}>⬇ Export</button>
            <button className="btn btn-ghost btn-sm" onClick={()=>setShowCustomRule(true)}>⚙ Custom Rule</button>
            <button className="btn btn-primary btn-sm" onClick={runAI} disabled={running}>
              {running?<span className="spin">⟳</span>:"🧠"} {running?"Running...":"AI Match"}
            </button>
          </div>
        </div>
      </div>

      {/* Reconcile Mode */}
      <div style={{display:"flex",gap:8,alignItems:"center",flexWrap:"wrap"}}>
        <div style={{display:"flex",gap:4,background:"#080c1c",border:"1px solid #161e38",borderRadius:9,padding:4}}>
          {[{id:"current",label:"Current Period"},{id:"crossperiod",label:`+ Prior Unmatched (${PREV_UNMATCHED.length})`}].map(m=>(
            <button key={m.id} className="btn btn-xs" onClick={()=>setReconcileMode(m.id)} style={{
              background:reconcileMode===m.id?"#2a46cc":"transparent",
              color:reconcileMode===m.id?"#fff":"#3a4e72",border:"none",borderRadius:6
            }}>{m.label}</button>
          ))}
        </div>
        <div style={{height:18,width:1,background:"#161e38"}}/>
        {["all","matched","partial","unmatched"].map(f=>(
          <button key={f} className="tab-pill" onClick={()=>setFilter(f)} style={{padding:"4px 12px"}}>
            <span className={filter===f?"active":""} style={{color:filter===f?"#6080ff":"#3a4e72",fontSize:11,fontWeight:700}}>
              {f.charAt(0).toUpperCase()+f.slice(1)}
            </span>
          </button>
        ))}
        <div style={{flex:1}}/>
        {selected.length>0&&<>
          <button className="btn btn-success btn-sm" onClick={()=>{notify(`${selected.length} items manually matched — ML learned!`,"success");setSelected([]);}}>✓ Match</button>
          <button className="btn btn-ghost btn-sm" onClick={()=>{setAssignCat(selected[0]);}}>🏷 Categorize</button>
          <button className="btn btn-warn btn-sm" onClick={()=>{notify("Flagged for review","warn");setSelected([]);}}>⚑ Flag</button>
        </>}
      </div>

      {/* Previous Unmatched Banner */}
      {reconcileMode==="crossperiod"&&<div style={{background:"rgba(220,38,38,.06)",border:"1px solid rgba(220,38,38,.25)",borderRadius:12,padding:"10px 14px",display:"flex",gap:10,alignItems:"center"}}>
        <span style={{color:"#dc2626",fontSize:16}}>↩</span>
        <div style={{flex:1}}>
          <div style={{fontSize:12,fontWeight:700,color:"#dc2626"}}>Cross-Period Mode Active</div>
          <div style={{fontSize:11,color:"#7a4040"}}>Showing {PREV_UNMATCHED.length} unmatched items from Dec 2024 alongside current period transactions</div>
        </div>
      </div>}

      {/* Transaction Table */}
      <div className="card" style={{padding:0,overflow:"hidden"}}>
        <table>
          <thead><tr>
            <th style={{width:32}}><input type="checkbox" style={{width:"auto"}} onChange={e=>setSelected(e.target.checked?txns.map(t=>t.id):[])}/></th>
            <th>ID</th><th>Date</th><th>Description</th><th>Category</th>
            <th style={{textAlign:"right"}}>Bank</th><th style={{textAlign:"right"}}>GL</th>
            <th>Rule Used</th><th>Status</th><th>Conf.</th><th>Actions</th>
          </tr></thead>
          <tbody>
            {reconcileMode==="crossperiod"&&PREV_UNMATCHED.map(p=>(
              <tr key={p.id} style={{background:"rgba(220,38,38,.04)"}}>
                <td><input type="checkbox" style={{width:"auto"}}/></td>
                <td className="mono" style={{fontSize:10,color:"#7a4040"}}>{p.id}</td>
                <td className="mono" style={{fontSize:10}}><span style={{color:"#dc2626"}}>↩</span> {p.date}</td>
                <td style={{fontSize:12}}>{p.desc}</td>
                <td><span style={{fontSize:10,color:"#dc2626"}}>Prior period</span></td>
                <td className="mono" style={{textAlign:"right",color:"#dc2626"}}>{p.bank?.toLocaleString("en",{style:"currency",currency:"USD"})}</td>
                <td style={{textAlign:"right",color:"#3a4e72"}}>—</td>
                <td><span style={{fontSize:10,color:"#3a4e72"}}>Unresolved</span></td>
                <td><span className="badge" style={{background:"#dc262622",color:"#dc2626"}}>unmatched</span></td>
                <td><ConfPill v={0}/></td>
                <td><button className="btn btn-danger btn-xs" onClick={()=>notify("Matching prior transaction...","info")}>Match</button></td>
              </tr>
            ))}
            {txns.map(tx=>{
              const cat=categories.find(c=>c.id===tx.catId||c.name===tx.category);
              return <tr key={tx.id}>
                <td><input type="checkbox" checked={selected.includes(tx.id)} style={{width:"auto"}}
                  onChange={e=>setSelected(s=>e.target.checked?[...s,tx.id]:s.filter(x=>x!==tx.id))}/></td>
                <td className="mono" style={{fontSize:10,color:"#3a4e72"}}>{tx.id}</td>
                <td className="mono" style={{fontSize:10}}>{tx.date}</td>
                <td style={{maxWidth:180,fontSize:12,overflow:"hidden",textOverflow:"ellipsis",whiteSpace:"nowrap"}}>{tx.desc}</td>
                <td>
                  {cat?<span className="badge" style={{background:cat.color+"22",color:cat.color}}>{cat.icon} {cat.name}</span>
                  :<button className="btn btn-ghost btn-xs" onClick={()=>setAssignCat(tx.id)} style={{fontSize:9}}>+ Cat.</button>}
                </td>
                <td style={{textAlign:"right"}} className="mono">{tx.bank?.toLocaleString("en",{style:"currency",currency:"USD"})}</td>
                <td style={{textAlign:"right"}} className="mono">{tx.gl?.toLocaleString("en",{style:"currency",currency:"USD"})||<span style={{color:"#3a4e72"}}>—</span>}</td>
                <td>{tx.rule?<span className="tag" style={{fontSize:9}}>{tx.rule}</span>:<span style={{color:"#3a4e72",fontSize:10}}>—</span>}</td>
                <td><span className="badge" style={{background:SC[tx.status]+"22",color:SC[tx.status]}}>{tx.status}</span></td>
                <td><ConfPill v={tx.conf}/></td>
                <td><div style={{display:"flex",gap:4}}>
                  {tx.status!=="matched"&&<button className="btn btn-success btn-xs" onClick={()=>notify("Matched! ML learned.","success")}>✓</button>}
                  <button className="btn btn-ghost btn-xs" onClick={()=>notify("Flagged","info")}>⚑</button>
                </div></td>
              </tr>;
            })}
          </tbody>
        </table>
      </div>
    </div>

    {/* Custom Rule Modal */}
    {showCustomRule&&<CustomRuleModal bank={selBank} categories={categories} onClose={()=>setShowCustomRule(false)} onSave={()=>{setShowCustomRule(false);notify("Custom matching rule saved!","success");}}/>}
    {assignCat&&<div className="modal-bg" onClick={()=>setAssignCat(null)}>
      <div className="modal" onClick={e=>e.stopPropagation()}>
        <SectionHdr title="Assign Category" sub="Select or create a category for selected transactions"/>
        <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:8}}>
          {categories.map(cat=>(
            <div key={cat.id} className="card-flat" style={{cursor:"pointer",padding:"10px 12px",borderLeft:`3px solid ${cat.color}`,transition:"all .17s"}}
              onClick={()=>{setAssignCat(null);notify(`Categorized as "${cat.name}"!`,"success");}}>
              <div style={{display:"flex",gap:8,alignItems:"center"}}>
                <span style={{fontSize:18}}>{cat.icon}</span>
                <div>
                  <div style={{fontWeight:700,fontSize:12}}>{cat.name}</div>
                  <div style={{fontSize:10,color:"#3a4e72"}}>{cat.type}</div>
                </div>
              </div>
            </div>
          ))}
        </div>
        <button className="btn btn-ghost btn-sm" style={{marginTop:14,width:"100%",justifyContent:"center"}} onClick={()=>setAssignCat(null)}>Cancel</button>
      </div>
    </div>}
  </div>;
}

// ─── CUSTOM RULE MODAL ────────────────────────────────────────
function CustomRuleModal({bank,categories,onClose,onSave}){
  const [ruleName,setRuleName]=useState(`${bank.short} Custom Rule`);
  const [conditions,setConditions]=useState([{field:"description",op:"contains",value:""}]);
  const [ruleType,setRuleType]=useState("specific");
  const [priority,setPriority]=useState(5);
  const [applyToAll,setApplyToAll]=useState(false);

  const FIELDS=["description","amount","date","reference","counterparty","currency","category","day_of_week","balance"];
  const OPS={description:["contains","starts_with","ends_with","equals","not_contains"],
    amount:["equals","greater_than","less_than","tolerance_pct","tolerance_abs","matches_negative"],
    date:["equals","day_of_week","day_of_month","within_days"],
    reference:["equals","contains","matches_pattern"],
    default:["equals","contains","greater_than","less_than"]
  };

  const addCond=()=>setConditions(c=>[...c,{field:"description",op:"contains",value:""}]);
  const removeCond=(i)=>setConditions(c=>c.filter((_,idx)=>idx!==i));
  const updateCond=(i,k,v)=>setConditions(c=>c.map((x,idx)=>idx===i?{...x,[k]:v}:x));

  return <div className="modal-bg" onClick={e=>e.target===e.currentTarget&&onClose()}>
    <div className="modal modal-xl">
      <SectionHdr title="Custom Matching Rule" sub={`Create a specific rule for ${applyToAll?"All Banks":bank.name}`}/>

      <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:12,marginBottom:16}}>
        <div><label>Rule Name</label><input value={ruleName} onChange={e=>setRuleName(e.target.value)}/></div>
        <div><label>Rule Type</label>
          <select value={ruleType} onChange={e=>setRuleType(e.target.value)}>
            <option value="specific">Specific Transaction</option>
            <option value="pattern">Recurring Pattern</option>
            <option value="category">Category Auto-Match</option>
            <option value="reversal">Reversal Detection</option>
            <option value="amount_range">Amount Range</option>
          </select>
        </div>
        <div><label>Priority (1=Highest)</label><input type="number" value={priority} onChange={e=>setPriority(e.target.value)} min={1} max={20}/></div>
        <div style={{display:"flex",alignItems:"center",gap:10,paddingTop:20}}>
          <Toggle on={applyToAll} onChange={setApplyToAll}/>
          <span style={{fontSize:12}}>Apply to All Banks</span>
        </div>
      </div>

      {/* Condition Builder */}
      <div style={{background:"#060910",border:"1px solid #161e38",borderRadius:12,padding:16,marginBottom:16}}>
        <div style={{display:"flex",justifyContent:"space-between",marginBottom:12}}>
          <span style={{fontSize:11,fontWeight:700,color:"#3a4e72",letterSpacing:".07em"}}>CONDITIONS — use plain logic, no coding needed</span>
          <button className="btn btn-ghost btn-xs" onClick={addCond}>+ Add Condition</button>
        </div>

        {/* Formula helpers */}
        <div style={{display:"flex",gap:6,flexWrap:"wrap",marginBottom:14}}>
          {["AND","OR","NOT","( )","amount_match","date_match","description_contains","tolerance(%)","day_of_week","matches_negative","within_N_days"].map(t=>(
            <span key={t} className="formula-token">{t}</span>
          ))}
        </div>

        {conditions.map((cond,i)=>(
          <div key={i} style={{display:"grid",gridTemplateColumns:"auto 1fr 1fr 1fr auto",gap:8,marginBottom:8,alignItems:"center"}}>
            {i>0&&<select style={{width:50,padding:"6px 4px",fontSize:11}}><option>AND</option><option>OR</option></select>}
            {i===0&&<span style={{fontSize:11,color:"#3a4e72",fontFamily:"IBM Plex Mono"}}>IF</span>}
            <select value={cond.field} onChange={e=>updateCond(i,"field",e.target.value)}>
              {FIELDS.map(f=><option key={f}>{f}</option>)}
            </select>
            <select value={cond.op} onChange={e=>updateCond(i,"op",e.target.value)}>
              {(OPS[cond.field]||OPS.default).map(o=><option key={o}>{o}</option>)}
            </select>
            <input value={cond.value} onChange={e=>updateCond(i,"value",e.target.value)} placeholder="Value..."/>
            {conditions.length>1&&<button className="btn btn-ghost btn-xs" onClick={()=>removeCond(i)} style={{color:"#dc2626",padding:"3px 6px"}}>✕</button>}
          </div>
        ))}

        {/* Preview */}
        <div style={{marginTop:12,padding:"8px 12px",background:"#0a0d1e",borderRadius:8,fontFamily:"IBM Plex Mono",fontSize:11,color:"#6080cc"}}>
          <span style={{color:"#3a4e72"}}>Preview: </span>
          {conditions.map((c,i)=>`${i>0?" AND ":""}${c.field} ${c.op} "${c.value}"`).join("")}
        </div>
      </div>

      {/* Match Action */}
      <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:12,marginBottom:20}}>
        <div><label>On Match — Apply Category</label>
          <select><option value="">None</option>{categories.map(c=><option key={c.id} value={c.id}>{c.icon} {c.name}</option>)}</select>
        </div>
        <div><label>Confidence Override (%)</label>
          <input type="number" defaultValue={85} min={0} max={100}/>
        </div>
        <div style={{display:"flex",gap:10,alignItems:"center",paddingTop:16}}>
          <Toggle on={true} onChange={()=>{}}/>
          <span style={{fontSize:12}}>Auto-match when rule matches</span>
        </div>
        <div style={{display:"flex",gap:10,alignItems:"center",paddingTop:16}}>
          <Toggle on={true} onChange={()=>{}}/>
          <span style={{fontSize:12}}>ML should learn from this rule</span>
        </div>
      </div>

      <div style={{display:"flex",gap:10,justifyContent:"flex-end"}}>
        <button className="btn btn-ghost" onClick={onClose}>Cancel</button>
        <button className="btn btn-success" onClick={onSave}>✓ Save Rule</button>
      </div>
    </div>
  </div>;
}

// ═══════════════════════════════════════════════════════════════
// ANALYSIS TAB — Full cashflow, categories, interest, forecast
// ═══════════════════════════════════════════════════════════════
function AnalysisTab({banks,categories,analyticsModules,viewMode,notify,currentUser}){
  const [activeSection,setActiveSection]=useState("cashflow");
  const [cfView,setCfView]=useState("monthly"); // monthly | daily
  const [forecastMode,setForecastMode]=useState("combined"); // ai | fpa | combined
  const [fpaResponse,setFpaResponse]=useState({});
  const [reanalysisPrompt,setReanalysisPrompt]=useState("");
  const [showReanalysis,setShowReanalysis]=useState(false);
  const [cashflowLoading,setCashflowLoading]=useState(false);
  const [aiInsight,setAiInsight]=useState(null);
  const [insightLoading,setInsightLoading]=useState(false);

  const months=CF_DATA.months;
  const netCF=months.map((_,i)=>CF_DATA.operating.actual[i]+CF_DATA.investing.actual[i]+CF_DATA.financing.actual[i]);
  const netAI=months.map((_,i)=>CF_DATA.operating.aiForecast[i]+CF_DATA.investing.aiForecast[i]+CF_DATA.financing.aiForecast[i]);
  const netFPA=months.map((_,i)=>CF_DATA.operating.fpaForecast[i]+CF_DATA.investing.fpaForecast[i]+CF_DATA.financing.fpaForecast[i]);

  const runAIInsight=async(topic)=>{
    setInsightLoading(true);
    try{
      const res=await fetch("https://api.anthropic.com/v1/messages",{
        method:"POST",
        headers:{"Content-Type":"application/json"},
        body:JSON.stringify({
          model:"claude-sonnet-4-20250514",
          max_tokens:1000,
          system:`You are a senior financial analyst for a bank reconciliation AI platform. Provide concise, insightful analysis in 3-4 bullet points. Be specific with numbers when possible. Current role: ${currentUser.role}.`,
          messages:[{role:"user",content:`Analyze this financial data and provide key insights about ${topic}:
Operating CF: Actual ${JSON.stringify(CF_DATA.operating.actual)}, AI Forecast ${JSON.stringify(CF_DATA.operating.aiForecast)}, FP&A ${JSON.stringify(CF_DATA.operating.fpaForecast)}
Months: ${months.join(",")}
Banks: ${banks.length}, Total variance: $${banks.reduce((a,b)=>a+Math.abs(b.balance-b.glBalance),0).toLocaleString()}
Debt facilities: ${banks.filter(b=>b.isDebt).map(b=>`${b.name} at ${b.currentRate}%`).join(", ")}
Please provide: 1) Key trend observation 2) AI vs FP&A variance reason 3) Action recommendation 4) Risk flag if any`}]
        })
      });
      const data=await res.json();
      setAiInsight(data.content?.[0]?.text||"Analysis complete.");
    }catch(e){
      setAiInsight("Unable to generate AI insights. Please check API configuration in Settings.");
    }
    setInsightLoading(false);
  };

  const sections=[
    {id:"cashflow",label:"Cash Flow Statement",icon:"💧",show:analyticsModules.cashflow_statement},
    {id:"position",label:"Cash Position",icon:"💵",show:analyticsModules.cash_position},
    {id:"forecast",label:"Forecast vs Actual",icon:"📈",show:analyticsModules.ai_forecast||analyticsModules.fpa_forecast},
    {id:"categories",label:"Cash In/Out Analysis",icon:"⇅",show:analyticsModules.cashin_cashout_breakdown},
    {id:"interest",label:"Interest & Charges",icon:"💸",show:analyticsModules.interest_calculation},
    {id:"bankfees",label:"Bank Fees Analysis",icon:"🏦",show:analyticsModules.bank_charges_analysis},
  ].filter(s=>s.show);

  const fmt=(n)=>n>=0?`+$${(n/1000000).toFixed(2)}M`:`-$${(Math.abs(n)/1000000).toFixed(2)}M`;

  return <div className="fadeUp" style={{display:"flex",gap:18}}>
    {/* Section Nav */}
    <div style={{width:190,flexShrink:0}}>
      <div className="card" style={{padding:10}}>
        <div style={{fontSize:10,fontWeight:700,color:"#2e3f60",marginBottom:10,letterSpacing:".08em"}}>ANALYSIS SECTIONS</div>
        {sections.map(s=>(
          <div key={s.id} className={`sidebar-link ${activeSection===s.id?"active":""}`} onClick={()=>setActiveSection(s.id)}>
            <span style={{fontSize:14}}>{s.icon}</span><span style={{fontSize:12}}>{s.label}</span>
          </div>
        ))}
        <div style={{marginTop:14,paddingTop:14,borderTop:"1px solid #161e38"}}>
          <button className="btn btn-purple btn-sm" style={{width:"100%",justifyContent:"center"}} onClick={()=>setShowReanalysis(true)}>
            🔄 Re-analyse
          </button>
        </div>
      </div>
    </div>

    {/* Content */}
    <div style={{flex:1,display:"flex",flexDirection:"column",gap:16,minWidth:0}}>

      {/* CASH FLOW STATEMENT */}
      {activeSection==="cashflow"&&<>
        <div className="card">
          <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start",flexWrap:"wrap",gap:10,marginBottom:20}}>
            <div>
              <h2 style={{fontSize:16,fontWeight:800}}>Cash Flow Statement</h2>
              <p style={{fontSize:11.5,color:"#3a4e72",marginTop:3}}>Actual vs AI Forecast vs FP&A · {viewMode==="top_mgmt"?"Executive Summary":viewMode==="manager"?"Management View":"Detailed View"}</p>
            </div>
            <div style={{display:"flex",gap:8,alignItems:"center",flexWrap:"wrap"}}>
              <div style={{display:"flex",gap:4,background:"#080c1c",border:"1px solid #161e38",borderRadius:8,padding:3}}>
                {["monthly","daily"].map(m=><button key={m} className="btn btn-xs" onClick={()=>setCfView(m)} style={{background:cfView===m?"#2a46cc":"transparent",color:cfView===m?"#fff":"#3a4e72",border:"none"}}>{m}</button>)}
              </div>
              <button className="btn btn-purple btn-sm" onClick={()=>runAIInsight("cashflow trends")} disabled={insightLoading}>
                {insightLoading?<span className="spin">⟳</span>:"🧠"} AI Insights
              </button>
              <button className="btn btn-ghost btn-sm" onClick={()=>notify("Cashflow exported","success")}>⬇ Export</button>
            </div>
          </div>

          {/* CF Table */}
          <div style={{overflowX:"auto"}}>
            <div style={{minWidth:700}}>
              {/* Header */}
              <div className="cf-row" style={{display:"grid",gridTemplateColumns:"2fr repeat(6,1fr)",gap:0}}>
                <div className="cf-header" style={{borderRadius:"10px 0 0 0"}}>Category</div>
                {months.map(m=><div key={m} className="cf-header" style={{textAlign:"right"}}>{m}</div>)}
              </div>
              {/* Operating */}
              {[
                {label:"Operating Activities",key:"operating",color:"#00c486",bold:true},
                {label:"  ↳ Actual",key:"operatingA",data:CF_DATA.operating.actual,indent:true,color:"#00c486"},
                {label:"  ↳ AI Forecast",key:"operatingAI",data:CF_DATA.operating.aiForecast,indent:true,color:"#7c3aed",dashed:true},
                {label:"  ↳ FP&A Forecast",key:"operatingFPA",data:CF_DATA.operating.fpaForecast,indent:true,color:"#d97706",dashed:true},
                {label:"Investing Activities",key:"investing",color:"#dc2626",bold:true},
                {label:"  ↳ Actual",key:"investingA",data:CF_DATA.investing.actual,indent:true,color:"#dc2626"},
                {label:"  ↳ AI Forecast",key:"investingAI",data:CF_DATA.investing.aiForecast,indent:true,color:"#7c3aed",dashed:true},
                {label:"Financing Activities",key:"financing",color:"#3d5fef",bold:true},
                {label:"  ↳ Actual",key:"financingA",data:CF_DATA.financing.actual,indent:true,color:"#3d5fef"},
              ].map((row,ri)=>(
                <div key={ri} className="cf-row" style={{display:"grid",gridTemplateColumns:"2fr repeat(6,1fr)",gap:0,background:row.bold?"rgba(255,255,255,.02)":"transparent"}}>
                  <div className="cf-cell" style={{fontWeight:row.bold?700:400,color:row.bold?row.color:"#7a8fb8",paddingLeft:row.indent?24:14,fontSize:row.bold?12:11}}>{row.label}</div>
                  {row.data?row.data.map((v,i)=>(
                    <div key={i} className={`cf-cell mono ${v>=0?"cf-positive":"cf-negative"}`} style={{textAlign:"right",fontSize:10.5,fontStyle:row.dashed?"italic":"normal"}}>
                      {(v/1000).toFixed(0)}k
                    </div>
                  )):months.map((_,i)=><div key={i} className="cf-cell"/>)}
                </div>
              ))}
              {/* NET */}
              <div style={{display:"grid",gridTemplateColumns:"2fr repeat(6,1fr)",gap:0,background:"rgba(42,70,204,.1)",borderTop:"2px solid #2a46cc"}}>
                <div className="cf-cell" style={{fontWeight:800,color:"#5a7af5",fontSize:12}}>NET CASH FLOW</div>
                {netCF.map((v,i)=>(
                  <div key={i} className={`cf-cell mono`} style={{textAlign:"right",fontWeight:800,color:v>=0?"#00c486":"#dc2626",fontSize:11}}>{fmt(v)}</div>
                ))}
              </div>
            </div>
          </div>
        </div>

        {/* Line Chart */}
        <div className="card">
          <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:16,flexWrap:"wrap",gap:8}}>
            <h3 style={{fontSize:13,fontWeight:700}}>Net Cash Flow — 3-Way Comparison</h3>
            <div style={{display:"flex",gap:12}}>
              {[{c:"#00c486",l:"Actual"},{c:"#7c3aed",l:"AI Forecast"},{c:"#d97706",l:"FP&A Forecast"}].map(l=>(
                <div key={l.l} style={{display:"flex",gap:5,alignItems:"center",fontSize:11}}>
                  <div style={{width:20,height:2,background:l.c,borderRadius:1}}/>
                  <span style={{color:"#5a7af5"}}>{l.l}</span>
                </div>
              ))}
            </div>
          </div>
          <LineChart height={160} labels={months} datasets={[
            {data:netCF,color:"#00c486",label:"Actual"},
            {data:netAI,color:"#7c3aed",label:"AI",dashed:true},
            {data:netFPA,color:"#d97706",label:"FP&A",dashed:true},
          ]}/>
        </div>

        {/* Variance Analysis */}
        <div className="card">
          <SectionHdr title="Variance Analysis — Actual vs Forecasts"
            sub="AI-generated reasons for deviations with FP&A response field"/>
          {months.slice(-3).map((m,i)=>{
            const actual=netCF[netCF.length-3+i];
            const ai=netAI[netAI.length-3+i];
            const fpa=netFPA[netFPA.length-3+i];
            const varAI=actual-ai;const varFPA=actual-fpa;
            return <div key={m} className="card-flat" style={{marginBottom:12}}>
              <div style={{display:"flex",gap:16,alignItems:"flex-start",flexWrap:"wrap"}}>
                <div style={{minWidth:120}}>
                  <div style={{fontWeight:700,fontSize:13}}>{m} 2024/25</div>
                  <div className="mono" style={{fontSize:18,fontWeight:800,color:actual>=0?"#00c486":"#dc2626",marginTop:4}}>
                    {fmt(actual)}
                  </div>
                </div>
                <div style={{flex:1,display:"flex",gap:14,flexWrap:"wrap"}}>
                  <div style={{minWidth:140}}>
                    <div style={{fontSize:10,color:"#3a4e72",marginBottom:4}}>VS AI FORECAST</div>
                    <div className="mono" style={{color:varAI>=0?"#00c486":"#dc2626",fontWeight:700}}>{fmt(varAI)}</div>
                    <div style={{fontSize:10.5,color:"#5a7af5",marginTop:4}}>🧠 {varAI>0?"Higher than AI predicted — likely driven by accelerated customer collections.":"Lower than AI predicted — delayed vendor payments affected inflow timing."}</div>
                  </div>
                  <div style={{minWidth:140}}>
                    <div style={{fontSize:10,color:"#3a4e72",marginBottom:4}}>VS FP&A FORECAST</div>
                    <div className="mono" style={{color:varFPA>=0?"#00c486":"#dc2626",fontWeight:700}}>{fmt(varFPA)}</div>
                  </div>
                  <div style={{flex:1,minWidth:200}}>
                    <label>FP&A Response / Explanation</label>
                    <textarea rows={2} value={fpaResponse[m]||""} onChange={e=>setFpaResponse(p=>({...p,[m]:e.target.value}))}
                      placeholder="Enter FP&A team's explanation for variance — AI will learn from this..." style={{resize:"none",fontSize:11}}/>
                    {fpaResponse[m]&&<button className="btn btn-ghost btn-xs" style={{marginTop:4}} onClick={()=>notify("FP&A response saved — AI will incorporate in next forecast","success")}>💾 Save & Train AI</button>}
                  </div>
                </div>
              </div>
            </div>;
          })}
        </div>

        {/* AI Insights Panel */}
        {aiInsight&&<div className="card" style={{borderColor:"#00c48640",background:"rgba(0,196,134,.04)"}}>
          <div style={{display:"flex",gap:10,marginBottom:14}}>
            <span style={{fontSize:22}}>🧠</span>
            <div><div style={{fontWeight:800,fontSize:13,color:"#00c486"}}>AI Cashflow Analysis</div>
              <div style={{fontSize:11,color:"#3a4e72"}}>Generated by Claude · Based on your actual data</div></div>
          </div>
          <div style={{fontSize:12.5,color:"#b0c0e0",lineHeight:1.8,whiteSpace:"pre-wrap"}}>{aiInsight}</div>
        </div>}
      </>}

      {/* CASH POSITION */}
      {activeSection==="position"&&<>
        <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(200px,1fr))",gap:12}}>
          {[
            {label:"Current Cash",val:"$10.6M",sub:"Across all current accounts",color:"#00c486",icon:"💵"},
            {label:"Debt Obligations",val:"-$23.2M",sub:"Active facilities",color:"#dc2626",icon:"📊"},
            {label:"Net Cash Position",val:"-$12.6M",sub:"After debt",color:"#d97706",icon:"⚖"},
            {label:"Available RCF",val:"$5.0M",sub:"HSBC facility headroom",color:"#3d5fef",icon:"🔄"},
            {label:"Cash Conversion",val:"18 days",sub:"Avg collection cycle",color:"#7c3aed",icon:"⟳"},
            {label:"Interest Coverage",val:"3.2x",sub:"EBITDA / interest",color:"#00c486",icon:"📐"},
          ].map((k,i)=><div key={i} className="kpi-card">
            <div style={{display:"flex",justifyContent:"space-between",marginBottom:10}}>
              <span style={{fontSize:22}}>{k.icon}</span>
              <span style={{fontSize:9,color:"#2e3f60",textTransform:"uppercase",letterSpacing:".07em"}}>{k.label}</span>
            </div>
            <div style={{fontSize:28,fontWeight:800,color:k.color}}>{k.val}</div>
            <div style={{fontSize:10.5,color:"#3a4e72",marginTop:3}}>{k.sub}</div>
          </div>)}
        </div>

        <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:16}}>
          <div className="card">
            <h3 style={{fontSize:13,fontWeight:700,marginBottom:16}}>Cash by Bank</h3>
            {banks.filter(b=>!b.isDebt).map(b=>(
              <div key={b.id} style={{marginBottom:12}}>
                <div style={{display:"flex",justifyContent:"space-between",marginBottom:5}}>
                  <span style={{fontSize:12,fontWeight:700}}>{b.short}</span>
                  <span className="mono" style={{fontSize:12,color:b.balance>=0?"#00c486":"#dc2626"}}>{b.currency} {(b.balance/1000000).toFixed(2)}M</span>
                </div>
                <ProgBar val={Math.abs(b.balance)/5000000*100} color="#2a46cc"/>
              </div>
            ))}
          </div>
          <div className="card">
            <h3 style={{fontSize:13,fontWeight:700,marginBottom:16}}>Cash Trend (30 Days)</h3>
            <LineChart height={140} labels={["Jan1","Jan8","Jan15","Jan22","Jan29"]}
              datasets={[{data:[9800000,10200000,9600000,11000000,10600000],color:"#00c486"}]}/>
          </div>
        </div>
      </>}

      {/* FORECAST */}
      {activeSection==="forecast"&&<>
        <div className="card">
          <SectionHdr title="AI Forecast vs FP&A vs Actual"
            sub="3-way comparison with AI-generated variance insights and FP&A response"
            action={<div style={{display:"flex",gap:8}}>
              <select style={{width:"auto"}} value={forecastMode} onChange={e=>setForecastMode(e.target.value)}>
                <option value="combined">All Three Lines</option>
                <option value="ai">AI Forecast Only</option>
                <option value="fpa">FP&A Only</option>
              </select>
              <button className="btn btn-purple btn-sm" onClick={()=>runAIInsight("forecast accuracy")} disabled={insightLoading}>
                🧠 Analyze
              </button>
            </div>}
          />
          <LineChart height={180} labels={months} datasets={[
            {data:CF_DATA.operating.actual,color:"#00c486",label:"Actual Operating"},
            ...(forecastMode!=="fpa"?[{data:CF_DATA.operating.aiForecast,color:"#7c3aed",label:"AI Forecast",dashed:true}]:[]),
            ...(forecastMode!=="ai"?[{data:CF_DATA.operating.fpaForecast,color:"#d97706",label:"FP&A Forecast",dashed:true}]:[]),
          ]}/>
        </div>

        <div className="card">
          <SectionHdr title="Forecast Accuracy Scorecard"/>
          <table>
            <thead><tr><th>Period</th><th>Actual</th><th>AI Forecast</th><th>AI Variance</th><th>FP&A Forecast</th><th>FP&A Variance</th><th>AI Better?</th><th>FP&A Response</th></tr></thead>
            <tbody>{months.map((m,i)=>{
              const act=CF_DATA.operating.actual[i];
              const ai=CF_DATA.operating.aiForecast[i];
              const fpa=CF_DATA.operating.fpaForecast[i];
              const aiBetter=Math.abs(act-ai)<Math.abs(act-fpa);
              return <tr key={m}>
                <td style={{fontWeight:700}}>{m}</td>
                <td className="mono" style={{color:"#00c486"}}>{(act/1000).toFixed(0)}k</td>
                <td className="mono" style={{color:"#7c3aed"}}>{(ai/1000).toFixed(0)}k</td>
                <td className="mono" style={{color:Math.abs(act-ai)<50000?"#00c486":"#d97706"}}>{((act-ai)/1000).toFixed(0)}k</td>
                <td className="mono" style={{color:"#d97706"}}>{(fpa/1000).toFixed(0)}k</td>
                <td className="mono" style={{color:Math.abs(act-fpa)<50000?"#00c486":"#dc2626"}}>{((act-fpa)/1000).toFixed(0)}k</td>
                <td><span className="badge" style={{background:aiBetter?"#00c48622":"#dc262622",color:aiBetter?"#00c486":"#dc2626"}}>{aiBetter?"✓ AI":"FP&A"}</span></td>
                <td><input value={fpaResponse[m]||""} onChange={e=>setFpaResponse(p=>({...p,[m]:e.target.value}))} placeholder="FP&A note..." style={{width:150,fontSize:10}}/></td>
              </tr>;
            })}</tbody>
          </table>
        </div>
        {aiInsight&&<div className="card" style={{borderColor:"#7c3aed40"}}><div style={{fontSize:12.5,lineHeight:1.8,color:"#b0c0e0",whiteSpace:"pre-wrap"}}><span style={{color:"#7c3aed",fontWeight:800}}>🧠 AI: </span>{aiInsight}</div></div>}
      </>}

      {/* CASH IN/OUT CATEGORIES */}
      {activeSection==="categories"&&<>
        <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:16}}>
          <div className="card">
            <SectionHdr title="Cash In by Category"/>
            {categories.filter(c=>c.type==="cashin"||c.type==="investment").map(cat=>{
              const amt=Math.floor(Math.random()*2000000+100000);
              const pct=Math.floor(Math.random()*40+5);
              return <div key={cat.id} style={{marginBottom:14}}>
                <div style={{display:"flex",justifyContent:"space-between",marginBottom:4}}>
                  <span style={{fontSize:12,fontWeight:600}}>{cat.icon} {cat.name}</span>
                  <div style={{display:"flex",gap:10}}>
                    <span className="mono" style={{fontSize:11,color:"#00c486"}}>${(amt/1000).toFixed(0)}k</span>
                    <span style={{fontSize:10,color:"#3a4e72"}}>{pct}%</span>
                  </div>
                </div>
                <ProgBar val={pct} color={cat.color}/>
              </div>;
            })}
          </div>
          <div className="card">
            <SectionHdr title="Cash Out by Category"/>
            {categories.filter(c=>c.type==="cashout"||c.type==="expense").map(cat=>{
              const amt=Math.floor(Math.random()*1500000+50000);
              const pct=Math.floor(Math.random()*35+5);
              return <div key={cat.id} style={{marginBottom:14}}>
                <div style={{display:"flex",justifyContent:"space-between",marginBottom:4}}>
                  <span style={{fontSize:12,fontWeight:600}}>{cat.icon} {cat.name}</span>
                  <div style={{display:"flex",gap:10}}>
                    <span className="mono" style={{fontSize:11,color:"#dc2626"}}>-${(amt/1000).toFixed(0)}k</span>
                    <span style={{fontSize:10,color:"#3a4e72"}}>{pct}%</span>
                  </div>
                </div>
                <ProgBar val={pct} color={cat.color}/>
              </div>;
            })}
          </div>
        </div>
      </>}

      {/* INTEREST & CHARGES */}
      {activeSection==="interest"&&<>
        <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(200px,1fr))",gap:12}}>
          {[
            {label:"Total Interest Expense",val:"$218,750",sub:"Jan 2025 · All facilities",color:"#dc2626"},
            {label:"Total Interest Income",val:"$12,400",sub:"Jan 2025 · Bank deposits",color:"#00c486"},
            {label:"Net Interest Cost",val:"$206,350",sub:"Expense minus income",color:"#d97706"},
            {label:"Stamp Duty",val:"$8,250",sub:"On new facility drawdown",color:"#f97316"},
            {label:"High Debit Balance Fee",val:"$2,100",sub:"Deutsche facility",color:"#dc2626"},
            {label:"Effective Rate (avg)",val:"6.78%",sub:"Weighted across facilities",color:"#3d5fef"},
          ].map((k,i)=><div key={i} className="kpi-card">
            <div style={{fontSize:9,color:"#2e3f60",textTransform:"uppercase",letterSpacing:".07em",marginBottom:10}}>{k.label}</div>
            <div style={{fontSize:26,fontWeight:800,color:k.color}}>{k.val}</div>
            <div style={{fontSize:10.5,color:"#3a4e72",marginTop:3}}>{k.sub}</div>
          </div>)}
        </div>

        <div className="card">
          <SectionHdr title="Interest Calculation per Facility" sub="Calculated on value date basis · Base rate + margin"/>
          <table>
            <thead><tr>
              <th>Facility</th><th>GL Account</th><th>Type</th><th>Balance</th><th>Base Rate</th><th>Margin</th><th>Total Rate</th>
              <th>Value Date</th><th>Days</th><th>Interest Jan</th><th>Stamp Duty</th><th>High Debit Fee</th>
            </tr></thead>
            <tbody>{banks.filter(b=>b.isDebt).map(b=>{
              const days=31;const interest=Math.abs(b.balance)*(b.currentRate/100)*(days/365);
              const stamp=interest*0.001;const hdb=Math.abs(b.balance)>10000000?interest*0.01:0;
              return <tr key={b.id}>
                <td style={{fontWeight:700,fontSize:12}}>{b.short}</td>
                <td className="mono" style={{color:"#3d5fef"}}>{b.glAccount}</td>
                <td><span className="badge" style={{background:"#dc262622",color:"#dc2626"}}>{b.accountType}</span></td>
                <td className="mono" style={{color:"#dc2626"}}>{b.currency} {(Math.abs(b.balance)/1000000).toFixed(1)}M</td>
                <td className="mono">{b.baseRate}</td>
                <td className="mono" style={{color:"#d97706"}}>{b.margin}%</td>
                <td className="mono" style={{fontWeight:700,color:"#dc2626"}}>{b.currentRate}%</td>
                <td className="mono" style={{fontSize:10}}>2025-01-01</td>
                <td className="mono">{days}</td>
                <td className="mono" style={{color:"#dc2626",fontWeight:700}}>${interest.toLocaleString("en",{maximumFractionDigits:0})}</td>
                <td className="mono" style={{color:"#f97316"}}>${stamp.toLocaleString("en",{maximumFractionDigits:0})}</td>
                <td className="mono" style={{color:hdb>0?"#dc2626":"#3a4e72"}}>{hdb>0?`$${hdb.toLocaleString("en",{maximumFractionDigits:0})}`:"—"}</td>
              </tr>;
            })}</tbody>
          </table>
        </div>

        <div className="card">
          <SectionHdr title="Daily Rate Changes" sub="SOFR & EURIBOR rate history — affects interest calculations"/>
          <table>
            <thead><tr><th>Date</th><th>Rate Index</th><th>Rate</th><th>Daily Change</th><th>Impact on Interest</th></tr></thead>
            <tbody>{INTEREST_RATES.map((r,i)=>(
              <tr key={i}>
                <td className="mono" style={{fontSize:11}}>{r.date}</td>
                <td><span className="badge" style={{background:"#3d5fef22",color:"#5a7af5"}}>{r.rate}</span></td>
                <td className="mono" style={{fontWeight:700}}>{r.value}%</td>
                <td className="mono" style={{color:r.change<0?"#00c486":r.change>0?"#dc2626":"#3a4e72"}}>
                  {r.change===0?"—":`${r.change>0?"+":""}${r.change}%`}
                </td>
                <td className="mono" style={{fontSize:11,color:r.change!==0?"#d97706":"#3a4e72"}}>
                  {r.change!==0?`≈ $${Math.abs(r.change*0.01*15000000/365).toFixed(0)}/day change`:"No change"}
                </td>
              </tr>
            ))}</tbody>
          </table>
        </div>
      </>}

      {/* BANK FEES */}
      {activeSection==="bankfees"&&<>
        <div className="card">
          <SectionHdr title="Bank Charges & Fee Analysis" sub="Non-financing fees · Account charges · Transaction fees"/>
          <table>
            <thead><tr><th>Bank</th><th>Fee Type</th><th>Amount</th><th>Period</th><th>Category</th><th>Is Financing?</th><th>YTD</th></tr></thead>
            <tbody>{BANK_CHARGES.map((c,i)=>{
              const isFin=c.type.includes("Arrangement")||c.type.includes("Commitment");
              return <tr key={i}>
                <td style={{fontWeight:700}}>{c.bank}</td>
                <td>{c.type}</td>
                <td className="mono" style={{color:isFin?"#dc2626":"#d97706"}}>${c.amount.toLocaleString()}</td>
                <td className="mono" style={{fontSize:10}}>{c.period}</td>
                <td><span className="badge" style={{background:"#d9770622",color:"#d97706"}}>Bank Charges</span></td>
                <td><span className="badge" style={{background:isFin?"#dc262622":"#3a4e7222",color:isFin?"#dc2626":"#5a7af5"}}>{isFin?"Financing":"Operating"}</span></td>
                <td className="mono">${(c.amount*12).toLocaleString()}</td>
              </tr>;
            })}</tbody>
          </table>
        </div>

        <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:16}}>
          <div className="card">
            <h3 style={{fontSize:13,fontWeight:700,marginBottom:16}}>Fees by Bank</h3>
            {Object.entries(BANK_CHARGES.reduce((acc,c)=>{acc[c.bank]=(acc[c.bank]||0)+c.amount;return acc},{})).map(([bank,amt])=>(
              <div key={bank} style={{marginBottom:12}}>
                <div style={{display:"flex",justifyContent:"space-between",marginBottom:4}}>
                  <span style={{fontSize:12,fontWeight:600}}>{bank}</span>
                  <span className="mono" style={{fontSize:11,color:"#d97706"}}>${amt.toLocaleString()}</span>
                </div>
                <ProgBar val={amt/200*100} color="#d97706"/>
              </div>
            ))}
          </div>
          <div className="card">
            <h3 style={{fontSize:13,fontWeight:700,marginBottom:16}}>Fee Trend</h3>
            <MiniBar data={[18200,21500,19800,22400,24100,23500]} labels={["Aug","Sep","Oct","Nov","Dec","Jan"]}
              colors={Array(6).fill("#d97706")}/>
          </div>
        </div>
      </>}
    </div>

    {/* Re-analysis Modal */}
    {showReanalysis&&<div className="modal-bg" onClick={()=>setShowReanalysis(false)}>
      <div className="modal" onClick={e=>e.stopPropagation()}>
        <SectionHdr title="Re-Analyse from a Different View" sub="Ask AI to restructure the analysis for a specific audience or perspective"/>
        <div style={{marginBottom:16}}>
          <label>Describe the view or question you want analysed</label>
          <textarea rows={4} value={reanalysisPrompt} onChange={e=>setReanalysisPrompt(e.target.value)}
            placeholder="Examples:&#10;• Show me this cashflow from a lender's perspective&#10;• What would a CFO want to see here?&#10;• Focus analysis on FX risk and interest rate sensitivity&#10;• Summarize for a Board presentation"/>
        </div>
        <div style={{marginBottom:16,display:"flex",flexWrap:"wrap",gap:6}}>
          {["CFO / Board View","Lender / Bank View","FX Risk Focus","Cost Reduction Analysis","Liquidity Focus","Credit Rating Agency View"].map(p=>(
            <button key={p} className="btn btn-ghost btn-xs" onClick={()=>setReanalysisPrompt(p)}>{p}</button>
          ))}
        </div>
        <div style={{display:"flex",gap:10,justifyContent:"flex-end"}}>
          <button className="btn btn-ghost" onClick={()=>setShowReanalysis(false)}>Cancel</button>
          <button className="btn btn-purple" onClick={()=>{setShowReanalysis(false);runAIInsight(reanalysisPrompt||"cashflow from executive perspective");}}>
            🧠 Generate Analysis
          </button>
        </div>
      </div>
    </div>}
  </div>;
}

// ═══════════════════════════════════════════════════════════════
// SETTINGS TAB — Comprehensive with all new sections
// ═══════════════════════════════════════════════════════════════
const SETTINGS_SECTIONS=[
  {id:"ai_api",label:"AI & API Keys",icon:"🧠",group:"AI Engine"},
  {id:"ai_parsing",label:"AI Parsing Engine",icon:"⚡",group:"AI Engine"},
  {id:"ml_learning",label:"ML Learning",icon:"📐",group:"AI Engine"},
  {id:"categories",label:"Categories & Rules",icon:"🏷",group:"Classification"},
  {id:"matching",label:"Custom Matching Rules",icon:"⇌",group:"Classification"},
  {id:"gl_mapping",label:"GL ↔ Bank Mapping",icon:"🔗",group:"Mapping"},
  {id:"format_tpl",label:"Format Templates",icon:"📄",group:"Mapping"},
  {id:"bank_apis",label:"Bank API Connections",icon:"🏦",group:"Connections"},
  {id:"sap_conn",label:"SAP GL Connection",icon:"📋",group:"Connections"},
  {id:"interest_rates",label:"Interest Rates & Fees",icon:"📈",group:"Finance"},
  {id:"analytics_vis",label:"Analytics Visibility",icon:"👁",group:"Display"},
  {id:"match_rules",label:"Matching Engine Rules",icon:"⚖",group:"Display"},
  {id:"tolerance",label:"Tolerance & Thresholds",icon:"△",group:"Display"},
  {id:"cloud_storage",label:"Cloud Storage",icon:"☁",group:"Data"},
  {id:"workflow",label:"Workflow & Approvals",icon:"✦",group:"Workflow"},
  {id:"notifications",label:"Notifications",icon:"🔔",group:"Workflow"},
  {id:"users",label:"Users & Access",icon:"👤",group:"Admin"},
  {id:"groups",label:"Role Groups",icon:"👥",group:"Admin"},
  {id:"companies",label:"Companies",icon:"🏢",group:"Admin"},
  {id:"audit",label:"Audit Log",icon:"📜",group:"Admin"},
  {id:"export",label:"Export & Reports",icon:"📤",group:"Admin"},
];

function SettingsTab({categories,setCategories,analyticsModules,setAnalyticsModules,customRules,setCustomRules,notify,currentUser}){
  const [sec,setSec]=useState("categories");
  const [editCat,setEditCat]=useState(null);
  const [showNewCat,setShowNewCat]=useState(false);
  const groups=[...new Set(SETTINGS_SECTIONS.map(s=>s.group))];

  // Only admins can see all sections
  const visibleSections=SETTINGS_SECTIONS.filter(s=>{
    if(currentUser.role==="admin")return true;
    const restricted=["users","groups","companies","ai_api","cloud_storage","audit","analytics_vis"];
    return !restricted.includes(s.id);
  });

  const renderSection=()=>{
    switch(sec){

      // ─── CATEGORIES ─────────────────────────────────────────
      case "categories": return <div style={{display:"flex",flexDirection:"column",gap:14}}>
        <SectionHdr title="Transaction Categories" sub="Define auto-classification rules for all transaction types. Conditions use plain language — no coding."
          action={<button className="btn btn-primary btn-sm" onClick={()=>setShowNewCat(true)}>+ New Category</button>}/>

        {categories.map(cat=>(
          <div key={cat.id} className="card-flat" style={{borderLeft:`3px solid ${cat.color}`}}>
            <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start",flexWrap:"wrap",gap:8}}>
              <div style={{display:"flex",gap:10,alignItems:"center"}}>
                <span style={{fontSize:22}}>{cat.icon}</span>
                <div>
                  <div style={{display:"flex",gap:8,alignItems:"center",flexWrap:"wrap"}}>
                    <span style={{fontWeight:800,fontSize:13}}>{cat.name}</span>
                    <span className="badge" style={{background:cat.color+"22",color:cat.color}}>{cat.type}</span>
                    <span className="badge" style={{background:cat.side==="both"?"#3d5fef22":"#d9770622",color:cat.side==="both"?"#5a7af5":"#d97706"}}>{cat.side}</span>
                    {cat.autoMatch&&<span className="badge" style={{background:"#00c48622",color:"#00c486"}}>AUTO</span>}
                  </div>
                  {/* Conditions */}
                  <div style={{marginTop:6,display:"flex",gap:5,flexWrap:"wrap"}}>
                    {cat.conditions.map((c,ci)=>(
                      <span key={ci} className="tag" style={{fontSize:9.5}}>
                        {c.field} {c.op} "{c.value}"
                      </span>
                    ))}
                  </div>
                  {/* Columns */}
                  <div style={{marginTop:5,display:"flex",gap:4,flexWrap:"wrap"}}>
                    <span style={{fontSize:9.5,color:"#2e3f60",marginRight:4}}>Columns:</span>
                    {cat.columns.map(col=><span key={col} className="tag" style={{fontSize:9}}>{col}</span>)}
                  </div>
                </div>
              </div>
              <div style={{display:"flex",gap:6,flexShrink:0}}>
                <button className="btn btn-ghost btn-xs" onClick={()=>setEditCat({...cat})}>Edit</button>
                <button className="btn btn-ghost btn-xs" style={{color:"#dc2626"}} onClick={()=>{setCategories(c=>c.filter(x=>x.id!==cat.id));notify("Category deleted","warn");}}>Delete</button>
              </div>
            </div>
          </div>
        ))}

        {(showNewCat||editCat)&&<CategoryEditorModal
          cat={editCat}
          onClose={()=>{setShowNewCat(false);setEditCat(null);}}
          onSave={(c)=>{
            if(editCat){setCategories(cats=>cats.map(x=>x.id===c.id?c:x));}
            else{setCategories(cats=>[...cats,{...c,id:"c"+(cats.length+1)}]);}
            setShowNewCat(false);setEditCat(null);
            notify(editCat?"Category updated!":"Category created!","success");
          }}
        />}
      </div>;

      // ─── CUSTOM MATCHING ─────────────────────────────────────
      case "matching": return <div style={{display:"flex",flexDirection:"column",gap:14}}>
        <SectionHdr title="Custom Matching Rules" sub="Bank-specific or transaction-specific matching conditions. Plain language — not code."
          action={<button className="btn btn-primary btn-sm" onClick={()=>notify("Open rule builder — also accessible from Reconcile tab","info")}>+ New Rule</button>}/>
        {customRules.map(rule=>(
          <div key={rule.id} className="card-flat">
            <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start",flexWrap:"wrap",gap:8}}>
              <div style={{flex:1}}>
                <div style={{display:"flex",gap:8,alignItems:"center",marginBottom:6,flexWrap:"wrap"}}>
                  <span style={{fontWeight:700,fontSize:13}}>{rule.name}</span>
                  <span className="badge" style={{background:"#3d5fef22",color:"#5a7af5"}}>{rule.bank==="ALL"?"All Banks":rule.bank}</span>
                  <span className="badge" style={{background:"#7c3aed22",color:"#a78bfa"}}>{rule.type}</span>
                  <span className="badge" style={{background:"#0f1525",color:"#3a4e72"}}>P{rule.priority}</span>
                </div>
                <div className="card-flat" style={{padding:"8px 12px",fontFamily:"IBM Plex Mono",fontSize:10.5,color:"#6080cc",background:"#060910"}}>
                  {rule.condition}
                </div>
              </div>
              <div style={{display:"flex",gap:8,alignItems:"center"}}>
                <Toggle on={rule.active} onChange={v=>setCustomRules(r=>r.map(x=>x.id===rule.id?{...x,active:v}:x))}/>
                <button className="btn btn-ghost btn-xs">Edit</button>
              </div>
            </div>
          </div>
        ))}
      </div>;

      // ─── ANALYTICS VISIBILITY ────────────────────────────────
      case "analytics_vis": return <div style={{display:"flex",flexDirection:"column",gap:12}}>
        <SectionHdr title="Analytics Module Visibility" sub="Control which analysis sections appear for all users or per role group"/>
        <div className="card-flat" style={{marginBottom:8,display:"flex",gap:10,alignItems:"center",background:"rgba(42,70,204,.06)",borderColor:"#2a46cc44"}}>
          <span style={{color:"#5a7af5",fontSize:16}}>ℹ</span>
          <span style={{fontSize:12,color:"#7a8fb8"}}>These settings apply globally. Use Role Groups to control per-user visibility. Changes take effect immediately.</span>
        </div>
        {Object.entries(analyticsModules).map(([key,val])=>{
          const labels={
            cashflow_statement:"Cash Flow Statement",cash_position:"Cash Position Dashboard",
            cash_conversion:"Cash Conversion Metrics",cash_trend:"Cash Trend Chart",
            budget_vs_actual:"Budget vs Actual",ai_forecast:"AI Forecast",
            fpa_forecast:"FP&A Forecast Comparison",variance_analysis:"Variance Analysis",
            bank_charges_analysis:"Bank Charges Analysis",interest_calculation:"Interest Calculations",
            cashin_cashout_breakdown:"Cash In/Out Breakdown",category_analysis:"Category Analysis",
            forecast_insights:"AI Forecast Insights",
          };
          return <div key={key} className="card-flat" style={{display:"flex",justifyContent:"space-between",alignItems:"center",padding:"12px 14px"}}>
            <div>
              <div style={{fontWeight:600,fontSize:12.5}}>{labels[key]||key}</div>
            </div>
            <Toggle on={val} onChange={v=>setAnalyticsModules(m=>({...m,[key]:v}))}/>
          </div>;
        })}
      </div>;

      // ─── INTEREST RATES ──────────────────────────────────────
      case "interest_rates": return <div style={{display:"flex",flexDirection:"column",gap:14}}>
        <SectionHdr title="Interest Rates, Stamp Duty & Fees" sub="Configure base rates, margins, stamp tax rates, and high debit balance thresholds per facility"/>
        {banks.filter(b=>b.isDebt).map(b=><div key={b.id} className="card-flat">
          <div style={{display:"flex",gap:10,alignItems:"center",marginBottom:12}}>
            <span style={{fontWeight:700,fontSize:14}}>{b.name}</span>
            <span className="badge" style={{background:"#dc262622",color:"#dc2626"}}>DEBT FACILITY</span>
            <span className="mono" style={{fontSize:11,color:"#3a4e72"}}>GL: {b.glAccount}</span>
          </div>
          <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(160px,1fr))",gap:10}}>
            <div><label>Base Rate Index</label>
              <select defaultValue={b.baseRate}><option>SOFR</option><option>EURIBOR</option><option>LIBOR</option><option>SONIA</option><option>Fixed</option></select></div>
            <div><label>Margin (%)</label><input type="number" step={.05} defaultValue={b.margin}/></div>
            <div><label>Current Rate (auto-calc)</label><input defaultValue={`${b.currentRate}%`} readOnly style={{color:"#dc2626"}}/></div>
            <div><label>Calculation Basis</label>
              <select><option>Actual/365</option><option>Actual/360</option><option>30/360</option></select></div>
            <div><label>Stamp Duty Rate (%)</label><input type="number" step={.001} defaultValue={0.1}/></div>
            <div><label>Stamp Duty Basis</label>
              <select><option>On interest</option><option>On principal</option><option>On drawdown</option></select></div>
            <div><label>High Debit Threshold ($M)</label><input type="number" defaultValue={10}/></div>
            <div><label>High Debit Fee Rate (%)</label><input type="number" step={.05} defaultValue={0.5}/></div>
            <div><label>Calculation Frequency</label>
              <select><option>Daily (value date)</option><option>Monthly</option><option>Quarterly</option></select></div>
            <div><label>Payment Day</label><input type="number" defaultValue={15}/></div>
          </div>
          <div style={{marginTop:12}}>
            <label>Daily Rate Changes Log</label>
            <div style={{display:"flex",gap:8,marginTop:6}}>
              <input type="date" style={{width:"auto"}}/>
              <input type="number" placeholder="New rate %" style={{width:120}}/>
              <button className="btn btn-success btn-sm" onClick={()=>notify("Rate change recorded — interest will be recalculated on value date basis","success")}>+ Log Rate</button>
            </div>
          </div>
          <div style={{display:"flex",gap:10,marginTop:12}}>
            <button className="btn btn-ghost btn-sm" onClick={()=>notify("Recalculating interest...","info")}>🔄 Recalculate Interest</button>
            <button className="btn btn-ghost btn-sm" onClick={()=>notify("Comparing vs bank statement...","info")}>⇌ Compare vs Bank Statement</button>
            <button className="btn btn-primary btn-sm" onClick={()=>notify("Facility settings saved","success")}>💾 Save</button>
          </div>
        </div>)}
        <div className="card-flat">
          <h4 style={{fontSize:12,fontWeight:700,color:"#3a4e72",marginBottom:12}}>STAMP DUTY CONFIGURATION (ALL FACILITIES)</h4>
          <div style={{display:"grid",gridTemplateColumns:"1fr 1fr 1fr",gap:10}}>
            <div><label>Default Stamp Duty Rate (%)</label><input type="number" step={.001} defaultValue={0.1}/></div>
            <div><label>Applies to</label><select><option>Interest payments</option><option>Principal drawdowns</option><option>Both</option></select></div>
            <div><label>Auto-calculate on reconcile</label>
              <div style={{paddingTop:8}}><Toggle on={true} onChange={()=>{}}/></div>
            </div>
          </div>
          <button className="btn btn-primary btn-sm" style={{marginTop:12}} onClick={()=>notify("Stamp duty settings saved","success")}>💾 Save</button>
        </div>
      </div>;

      // ─── CLOUD STORAGE ───────────────────────────────────────
      case "cloud_storage": return <div style={{display:"flex",flexDirection:"column",gap:14}}>
        <SectionHdr title="Cloud Storage Integration" sub="Link your data store to OneDrive, Google Drive, SharePoint or other cloud providers"/>
        {[
          {name:"Microsoft OneDrive",icon:"🟦",connected:true,desc:"Sync bank statements and GL exports automatically"},
          {name:"Google Drive",icon:"🟧",connected:false,desc:"Connect for file storage and sharing"},
          {name:"SharePoint",icon:"🟩",connected:false,desc:"Enterprise document management"},
          {name:"Dropbox Business",icon:"🟪",connected:false,desc:"Team file storage"},
          {name:"AWS S3",icon:"🟨",connected:false,desc:"Scalable object storage for large datasets"},
          {name:"Azure Blob",icon:"🟦",connected:false,desc:"Azure cloud storage for enterprise deployments"},
        ].map((s,i)=><div key={i} className="card-flat">
          <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",flexWrap:"wrap",gap:10}}>
            <div style={{display:"flex",gap:12,alignItems:"center"}}>
              <span style={{fontSize:28}}>{s.icon}</span>
              <div>
                <div style={{fontWeight:700,fontSize:13}}>{s.name}</div>
                <div style={{fontSize:11,color:"#3a4e72",marginTop:2}}>{s.desc}</div>
              </div>
            </div>
            <div style={{display:"flex",gap:8,alignItems:"center"}}>
              <span className="badge" style={{background:s.connected?"#00c48622":"#3a4e7222",color:s.connected?"#00c486":"#5a7af5"}}>
                {s.connected?"● Connected":"○ Not Connected"}
              </span>
              {s.connected&&<button className="btn btn-ghost btn-xs" onClick={()=>notify("Syncing...","info")}>🔄 Sync</button>}
              <button className="btn btn-primary btn-xs" onClick={()=>notify(`${s.name} OAuth flow would open`,"info")}>{s.connected?"Manage":"Connect"}</button>
            </div>
          </div>
          {s.connected&&<div style={{marginTop:12,display:"grid",gridTemplateColumns:"1fr 1fr",gap:10}}>
            <div><label>Bank Statements Folder</label><input defaultValue="/BankRecon/Statements"/></div>
            <div><label>GL Exports Folder</label><input defaultValue="/BankRecon/GL"/></div>
            <div><label>Auto-sync Frequency</label><select><option>Every 15 min</option><option>Hourly</option><option>Daily</option></select></div>
            <div><label>Archive After (days)</label><input type="number" defaultValue={90}/></div>
          </div>}
        </div>)}
      </div>;

      // ─── USERS ───────────────────────────────────────────────
      case "users": return <div style={{display:"flex",flexDirection:"column",gap:14}}>
        <SectionHdr title="Users & Access Management" sub="Manage users, roles and company assignment"
          action={<button className="btn btn-primary btn-sm">+ Invite User</button>}/>
        <table>
          <thead><tr><th>User</th><th>Email</th><th>Role</th><th>Group</th><th>Company</th><th>Status</th><th>Actions</th></tr></thead>
          <tbody>{USERS.map(u=><tr key={u.id}>
            <td style={{display:"flex",gap:9,alignItems:"center"}}>
              <div style={{width:30,height:30,borderRadius:"50%",background:"linear-gradient(135deg,#2a46cc,#7c3aed)",display:"flex",alignItems:"center",justifyContent:"center",fontSize:11,fontWeight:800,flexShrink:0}}>{u.avatar}</div>
              <span style={{fontWeight:700,fontSize:12}}>{u.name}</span>
            </td>
            <td className="mono" style={{fontSize:10.5}}>{u.email}</td>
            <td><select defaultValue={u.role} style={{width:160}}>
              {["admin","senior_accountant","accountant","approver","analyst","viewer"].map(r=><option key={r}>{r}</option>)}
            </select></td>
            <td><select defaultValue={u.group} style={{width:150}}>
              {ROLE_GROUPS.map(g=><option key={g.id}>{g.name}</option>)}
            </select></td>
            <td style={{fontSize:11}}>{u.company}</td>
            <td><Toggle on={u.active} onChange={()=>notify("User status updated","info")} size="sm"/></td>
            <td><div style={{display:"flex",gap:4}}>
              <button className="btn btn-ghost btn-xs" onClick={()=>notify("Saved","success")}>Save</button>
              <button className="btn btn-ghost btn-xs" style={{color:"#dc2626"}}>Remove</button>
            </div></td>
          </tr>)}</tbody>
        </table>
      </div>;

      // ─── GROUPS ──────────────────────────────────────────────
      case "groups": return <div style={{display:"flex",flexDirection:"column",gap:14}}>
        <SectionHdr title="Role Groups" sub="Groups are assigned to companies — users inherit access based on their group"
          action={<button className="btn btn-primary btn-sm">+ New Group</button>}/>
        {ROLE_GROUPS.map(g=><div key={g.id} className="card-flat">
          <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start",flexWrap:"wrap",gap:10}}>
            <div>
              <div style={{fontWeight:700,fontSize:13,marginBottom:4}}>{g.name}</div>
              <div style={{fontSize:11,color:"#3a4e72",marginBottom:8}}>Company: {g.company}</div>
              <div style={{display:"flex",flexWrap:"wrap",gap:5}}>
                {g.permissions.map(p=><span key={p} className="tag" style={{fontSize:9.5,color:"#5a7af5"}}>{p}</span>)}
              </div>
            </div>
            <div style={{display:"flex",gap:6}}>
              <button className="btn btn-ghost btn-xs">Edit</button>
              <button className="btn btn-ghost btn-xs">Permissions</button>
            </div>
          </div>
          {/* Analytics visibility per group */}
          <div style={{marginTop:12,paddingTop:10,borderTop:"1px solid #161e38"}}>
            <div style={{fontSize:10,color:"#2e3f60",marginBottom:8,letterSpacing:".07em"}}>ANALYTICS MODULE ACCESS</div>
            <div style={{display:"flex",flexWrap:"wrap",gap:6}}>
              {["cashflow_statement","cash_position","ai_forecast","fpa_forecast","interest_calculation","bank_charges_analysis"].map(mod=>(
                <div key={mod} style={{display:"flex",gap:5,alignItems:"center"}}>
                  <Toggle on={g.name!=="Top Management"||mod!=="interest_calculation"} onChange={()=>notify("Group permission updated","info")} size="sm"/>
                  <span style={{fontSize:10,color:"#3a4e72"}}>{mod.replace(/_/g," ")}</span>
                </div>
              ))}
            </div>
          </div>
        </div>)}
      </div>;

      // ─── COMPANIES ───────────────────────────────────────────
      case "companies": return <div style={{display:"flex",flexDirection:"column",gap:14}}>
        <SectionHdr title="Companies" sub="Each company has its own data namespace, users and cloud storage configuration"
          action={<button className="btn btn-primary btn-sm">+ New Company</button>}/>
        {["Global Corp","Subsidiary EMEA","Subsidiary APAC"].map((co,i)=><div key={i} className="card-flat">
          <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start",flexWrap:"wrap",gap:10}}>
            <div>
              <div style={{fontWeight:700,fontSize:14}}>{co}</div>
              <div style={{fontSize:11,color:"#3a4e72",marginTop:3}}>
                {USERS.filter(u=>u.company===co).length} users · {BANKS.length} banks · SAP Client 1000
              </div>
              <div style={{display:"flex",gap:6,marginTop:8}}>
                <span className="badge" style={{background:"#3d5fef22",color:"#5a7af5"}}>Active</span>
                {i===0&&<span className="badge" style={{background:"#00c48622",color:"#00c486"}}>Primary</span>}
              </div>
            </div>
            <div style={{display:"flex",gap:6}}>
              <button className="btn btn-ghost btn-xs">Configure</button>
              <button className="btn btn-ghost btn-xs">Groups</button>
              {i===0&&<button className="btn btn-ghost btn-xs" style={{color:"#dc2626"}}>Disable</button>}
            </div>
          </div>
        </div>)}
      </div>;

      // ─── GL MAPPING ──────────────────────────────────────────
      case "gl_mapping": return <div style={{display:"flex",flexDirection:"column",gap:14}}>
        <SectionHdr title="GL ↔ Bank Account Mapping" sub="Link GL accounts to physical bank accounts. Debt facilities get additional rate configuration."
          action={<button className="btn btn-primary btn-sm" onClick={()=>notify("Row added","info")}>+ Add Mapping</button>}/>
        <div style={{overflowX:"auto"}}>
          <table>
            <thead><tr><th>GL Account</th><th>Description</th><th>Bank</th><th>Account No.</th><th>Currency</th><th>Account Type</th><th>Debt Facility</th><th>Active</th><th>Actions</th></tr></thead>
            <tbody>{BANKS.map((b,i)=><tr key={i}>
              <td><input defaultValue={b.glAccount} style={{width:80}}/></td>
              <td><input defaultValue={b.name} style={{width:200}}/></td>
              <td><select defaultValue={b.short} style={{width:120}}>{["HSBC","Barclays","Citi","JPMorgan","Deutsche Bank"].map(x=><option key={x}>{x}</option>)}</select></td>
              <td><input defaultValue={b.bankAccNo} className="mono" style={{width:200,fontSize:10}}/></td>
              <td><select defaultValue={b.currency} style={{width:65}}><option>USD</option><option>GBP</option><option>EUR</option></select></td>
              <td><select defaultValue={b.accountType} style={{width:110}}><option>current</option><option>loan</option><option>revolving</option><option>deposit</option></select></td>
              <td><Toggle on={b.isDebt} onChange={()=>{}} size="sm"/></td>
              <td><Toggle on={true} onChange={()=>{}} size="sm"/></td>
              <td><div style={{display:"flex",gap:4}}>
                <button className="btn btn-success btn-xs" onClick={()=>notify("Saved","success")}>Save</button>
                <button className="btn btn-ghost btn-xs" style={{color:"#dc2626"}}>Del</button>
              </div></td>
            </tr>)}</tbody>
          </table>
        </div>
        <div style={{display:"flex",gap:10}}>
          <button className="btn btn-ghost btn-sm" onClick={()=>notify("CSV downloaded","success")}>⬇ Export CSV</button>
          <button className="btn btn-ghost btn-sm" onClick={()=>notify("Import CSV","info")}>⬆ Import CSV</button>
          <button className="btn btn-ghost btn-sm" onClick={()=>notify("Syncing from SAP...","info")}>🔄 Sync from SAP</button>
        </div>
      </div>;

      // ─── TOLERANCE ───────────────────────────────────────────
      case "tolerance": return <div style={{display:"flex",flexDirection:"column",gap:12}}>
        <SectionHdr title="Tolerance & Thresholds" sub="Set matching tolerances, confidence levels and escalation thresholds"/>
        {[
          {l:"Amount Tolerance (%)",v:"0.50",desc:"Percentage variance allowed for amount matching"},
          {l:"Amount Tolerance (Absolute)",v:"0.01",desc:"Fixed absolute tolerance for rounding differences"},
          {l:"Date Tolerance (days)",v:"3",desc:"Days ± for date range matching"},
          {l:"Description Similarity (%)",v:"75",desc:"Min NLP similarity score for description match"},
          {l:"Auto-match Confidence (%)",v:"90",desc:"AI confidence required to auto-match without review"},
          {l:"Flag for Review Threshold (%)",v:"60",desc:"Confidence below this triggers manual review"},
          {l:"FX Rate Tolerance (%)",v:"1.00",desc:"Exchange rate variance for cross-currency matching"},
          {l:"Interest Calculation Tolerance ($)",v:"10",desc:"Acceptable difference vs bank statement for interest"},
          {l:"Bulk Match Max Items",v:"500",desc:"Maximum transactions per AI matching run"},
          {l:"Cross-Period Lookback (months)",v:"3",desc:"How far back to search for unmatched items"},
        ].map((t,i)=><div key={i} className="card-flat" style={{display:"flex",alignItems:"center",gap:16}}>
          <div style={{flex:1}}>
            <div style={{fontWeight:700,fontSize:12.5}}>{t.l}</div>
            <div style={{fontSize:11,color:"#3a4e72",marginTop:2}}>{t.desc}</div>
          </div>
          <input type="number" defaultValue={t.v} style={{width:90}}/>
        </div>)}
        <button className="btn btn-primary btn-sm" style={{alignSelf:"flex-start"}} onClick={()=>notify("Thresholds saved","success")}>💾 Save</button>
      </div>;

      // ─── AUDIT ───────────────────────────────────────────────
      case "audit": return <div style={{display:"flex",flexDirection:"column",gap:14}}>
        <SectionHdr title="Audit Log" sub="Full immutable audit trail of all actions"/>
        <div className="card-flat" style={{display:"flex",gap:10,flexWrap:"wrap"}}>
          {[{l:"From",t:"date"},{l:"To",t:"date"}].map(f=><div key={f.l}><label>{f.l}</label><input type={f.t}/></div>)}
          <div><label>User</label><select><option>All Users</option>{USERS.map(u=><option key={u.id}>{u.name}</option>)}</select></div>
          <div><label>Action</label><select><option>All</option><option>Match</option><option>Upload</option><option>Settings</option><option>Category</option><option>Interest</option></select></div>
          <button className="btn btn-primary btn-sm" style={{alignSelf:"flex-end"}}>Filter</button>
        </div>
        <div className="card-flat" style={{padding:0,overflow:"hidden"}}>
          <table>
            <thead><tr><th>Timestamp</th><th>User</th><th>Role</th><th>Action</th><th>Entity</th><th>Details</th></tr></thead>
            <tbody>{[
              {ts:"2025-01-18 14:23",u:"Admin",r:"admin",a:"Category Edit",e:"Bank Charges",d:"Added condition: amount_less_than(5000)"},
              {ts:"2025-01-18 14:20",u:"S. Chen",r:"senior_accountant",a:"Manual Match",e:"T007",d:"Matched to GL 1000061"},
              {ts:"2025-01-18 13:45",u:"System",r:"system",a:"ML Retrain",e:"Global Model",d:"Accuracy: 94.2% (+0.3%)"},
              {ts:"2025-01-18 12:30",u:"J. Liu",r:"analyst",a:"FP&A Response",e:"Dec 2024",d:"Variance explained: supplier payment delay"},
              {ts:"2025-01-18 11:15",u:"Admin",r:"admin",a:"Rate Change",e:"Deutsche Facility",d:"EURIBOR updated 3.89% → 3.87%"},
            ].map((l,i)=><tr key={i}>
              <td className="mono" style={{fontSize:10}}>{l.ts}</td>
              <td style={{fontWeight:600,fontSize:12}}>{l.u}</td>
              <td><span className="badge" style={{background:"#3d5fef22",color:"#5a7af5"}}>{l.r}</span></td>
              <td><span className="badge" style={{background:"#d9770622",color:"#d97706"}}>{l.a}</span></td>
              <td className="mono" style={{fontSize:10}}>{l.e}</td>
              <td style={{fontSize:11,color:"#7a8fb8"}}>{l.d}</td>
            </tr>)}
            </tbody>
          </table>
        </div>
        <div style={{display:"flex",gap:8}}>
          <button className="btn btn-ghost btn-sm" onClick={()=>notify("Exported","success")}>⬇ Export CSV</button>
          <button className="btn btn-ghost btn-sm" onClick={()=>notify("PDF report generated","success")}>⬇ Export PDF</button>
        </div>
      </div>;

      // ─── NOTIFICATIONS ───────────────────────────────────────
      case "notifications": return <div style={{display:"flex",flexDirection:"column",gap:14}}>
        <SectionHdr title="Notifications" sub="Configure alerts per event and channel"/>
        <div className="card-flat">
          <h4 style={{fontSize:11,fontWeight:700,color:"#3a4e72",marginBottom:12}}>CHANNELS</h4>
          {["Email","Slack","Microsoft Teams","SMS","Webhook"].map((c,i)=>(
            <div key={i} style={{display:"flex",justifyContent:"space-between",alignItems:"center",padding:"9px 0",borderBottom:"1px solid #0c1525"}}>
              <span style={{fontWeight:600,fontSize:12.5}}>{c}</span>
              <div style={{display:"flex",gap:10,alignItems:"center"}}>
                <input placeholder={`${c} endpoint or email...`} style={{width:220}}/>
                <Toggle on={i<2} onChange={()=>{}} size="sm"/>
              </div>
            </div>
          ))}
        </div>
        <div className="card-flat">
          <h4 style={{fontSize:11,fontWeight:700,color:"#3a4e72",marginBottom:12}}>TRIGGERS</h4>
          {["Reconciliation completed","Variance detected","Interest mismatch found","Rate change logged","ML retrain completed","FP&A response needed","Category auto-applied","Cloud sync complete"].map((e,i)=>(
            <div key={i} style={{display:"flex",justifyContent:"space-between",padding:"8px 0",borderBottom:"1px solid #0c1525"}}>
              <span style={{fontSize:12.5}}>{e}</span><Toggle on={i<5} onChange={()=>{}} size="sm"/>
            </div>
          ))}
        </div>
        <button className="btn btn-primary btn-sm" style={{alignSelf:"flex-start"}} onClick={()=>notify("Notification settings saved","success")}>💾 Save</button>
      </div>;

      // ─── WORKFLOW ────────────────────────────────────────────
      case "workflow": return <div style={{display:"flex",flexDirection:"column",gap:12}}>
        <SectionHdr title="Workflow & Approvals"/>
        {[
          {l:"Require approval for manual matches",sub:"Route matched items through approver",on:true},
          {l:"Auto-approve AI matches >95% confidence",sub:"Skip queue for high-confidence matches",on:true},
          {l:"Require approval for rate changes",sub:"Interest rate updates need CFO sign-off",on:true},
          {l:"Lock period after final approval",sub:"Prevent edits after manager sign-off",on:true},
          {l:"Multi-level approval for large variances",sub:"Variances > $50k need two approvers",on:false},
        ].map((w,i)=><div key={i} className="card-flat" style={{display:"flex",justifyContent:"space-between",alignItems:"center"}}>
          <div><div style={{fontWeight:700,fontSize:12.5}}>{w.l}</div><div style={{fontSize:11,color:"#3a4e72",marginTop:2}}>{w.sub}</div></div>
          <Toggle on={w.on} onChange={()=>{}}/>
        </div>)}
        <div className="card-flat">
          <h4 style={{fontSize:11,fontWeight:700,color:"#3a4e72",marginBottom:12}}>THRESHOLDS</h4>
          <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:10}}>
            <div><label>Level 1 Variance Max ($)</label><input type="number" defaultValue={5000}/></div>
            <div><label>Level 2 Variance Max ($)</label><input type="number" defaultValue={50000}/></div>
            <div><label>Escalate Unmatched After (days)</label><input type="number" defaultValue={5}/></div>
            <div><label>Approver Email</label><input placeholder="approver@company.com"/></div>
          </div>
        </div>
        <button className="btn btn-primary btn-sm" style={{alignSelf:"flex-start"}} onClick={()=>notify("Workflow saved","success")}>💾 Save</button>
      </div>;

      // ─── EXPORT ──────────────────────────────────────────────
      case "export": return <div style={{display:"flex",flexDirection:"column",gap:14}}>
        <SectionHdr title="Export & Reports"/>
        <div className="card-flat">
          {["Reconciliation Summary","Unmatched Items Report","Interest & Charges Report","Cashflow Statement","Forecast vs Actual","Bank Charges Analysis","Category Breakdown","Audit Trail"].map((r,i)=>(
            <div key={i} style={{display:"flex",justifyContent:"space-between",padding:"9px 0",borderBottom:"1px solid #0c1525",alignItems:"center"}}>
              <span style={{fontSize:12.5,fontWeight:600}}>{r}</span>
              <div style={{display:"flex",gap:8,alignItems:"center"}}>
                <select style={{width:75}}><option>PDF</option><option>Excel</option><option>CSV</option></select>
                <button className="btn btn-ghost btn-xs" onClick={()=>notify(`${r} generated!`,"success")}>Generate</button>
                <Toggle on={i<4} onChange={()=>{}} size="sm"/>
              </div>
            </div>
          ))}
        </div>
        <div className="card-flat">
          <h4 style={{fontSize:11,fontWeight:700,color:"#3a4e72",marginBottom:12}}>SCHEDULED REPORTS</h4>
          <div style={{display:"grid",gridTemplateColumns:"1fr 1fr 1fr",gap:10}}>
            <div><label>Frequency</label><select><option>Daily</option><option>Weekly</option><option>Monthly</option></select></div>
            <div><label>Recipients</label><input placeholder="email@company.com"/></div>
            <div><label>Format</label><select><option>PDF</option><option>Excel</option><option>Both</option></select></div>
          </div>
          <button className="btn btn-primary btn-sm" style={{marginTop:12}} onClick={()=>notify("Schedule saved","success")}>💾 Save Schedule</button>
        </div>
      </div>;

      // ─── AI API ──────────────────────────────────────────────
      case "ai_api": return <div style={{display:"flex",flexDirection:"column",gap:14}}>
        <SectionHdr title="AI & API Configuration" sub="Configure AI providers for parsing, matching and forecasting"/>
        <div className="card-flat">
          <h4 style={{fontSize:11,fontWeight:700,color:"#3a4e72",marginBottom:14}}>PRIMARY PROVIDER</h4>
          <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:12}}>
            <div><label>Provider</label><select><option>Anthropic Claude</option><option>OpenAI GPT-4</option><option>Azure OpenAI</option><option>AWS Bedrock</option><option>Google Gemini</option><option>Local LLM</option></select></div>
            <div><label>Model</label><select><option>claude-sonnet-4-20250514</option><option>claude-opus-4-20250514</option><option>gpt-4o</option></select></div>
            <div><label>Anthropic API Key</label><input type="password" placeholder="sk-ant-..."/></div>
            <div><label>OpenAI API Key (fallback)</label><input type="password" placeholder="sk-..."/></div>
            <div><label>Max Tokens</label><input type="number" defaultValue={4096}/></div>
            <div><label>Temperature</label><input type="number" step={.1} defaultValue={.1}/></div>
          </div>
          <div style={{display:"flex",gap:8,marginTop:12}}>
            <button className="btn btn-ghost btn-sm" onClick={()=>notify("API connection OK!","success")}>🔌 Test</button>
            <button className="btn btn-primary btn-sm" onClick={()=>notify("Saved","success")}>💾 Save</button>
          </div>
        </div>
        <div className="card-flat">
          <h4 style={{fontSize:11,fontWeight:700,color:"#3a4e72",marginBottom:12}}>AI FEATURE TOGGLES</h4>
          {["AI Parsing on Upload","AI Auto-matching","AI Cashflow Forecasting","AI Variance Insights","AI FP&A Learning","ChatBot Assistant"].map((f,i)=>(
            <div key={i} style={{display:"flex",justifyContent:"space-between",padding:"8px 0",borderBottom:"1px solid #0c1525"}}>
              <span style={{fontSize:12.5}}>{f}</span><Toggle on={true} onChange={()=>{}} size="sm"/>
            </div>
          ))}
        </div>
      </div>;

      // Default for other sections
      default: return <div style={{padding:40,textAlign:"center",color:"#3a4e72",fontSize:13}}>
        This settings section is under construction. More options coming soon.
      </div>;
    }
  };

  return <div className="fadeUp" style={{display:"flex",gap:18}}>
    {/* Settings Sidebar */}
    <div style={{width:200,flexShrink:0}}>
      <div className="card" style={{padding:"12px 8px"}}>
        {groups.map(g=>{
          const items=visibleSections.filter(s=>s.group===g);
          if(!items.length)return null;
          return <div key={g}>
            <div style={{fontSize:9,fontWeight:700,color:"#1e2c4a",letterSpacing:".1em",padding:"10px 12px 5px",textTransform:"uppercase"}}>{g}</div>
            {items.map(s=>(
              <div key={s.id} className={`sidebar-link ${sec===s.id?"active":""}`} onClick={()=>setSec(s.id)} style={{padding:"7px 12px",fontSize:11.5}}>
                <span style={{fontSize:13}}>{s.icon}</span><span>{s.label}</span>
              </div>
            ))}
          </div>;
        })}
      </div>
    </div>
    <div style={{flex:1,minWidth:0}}>{renderSection()}</div>
    {editCat&&<CategoryEditorModal cat={editCat} onClose={()=>setEditCat(null)} onSave={(c)=>{setCategories(cats=>cats.map(x=>x.id===c.id?c:x));setEditCat(null);notify("Category updated!","success");}}/>}
  </div>;
}

// ─── CATEGORY EDITOR MODAL ───────────────────────────────────
function CategoryEditorModal({cat,onClose,onSave}){
  const isNew=!cat;
  const [form,setForm]=useState(cat||{name:"",icon:"🏷",color:"#3d5fef",type:"expense",side:"both",autoMatch:true,conditions:[{field:"description",op:"contains",value:""}],columns:["date","description","amount","bank"]});
  const FIELDS=["description","amount","date","reference","counterparty","currency","category","day_of_week"];
  const OPS=["contains","starts_with","ends_with","equals","not_contains","greater_than","less_than","tolerance_pct","matches_negative","within_N_days"];
  const AVAILABLE_COLS=["date","description","amount","bank","reference","currency","counterparty","vat","vendor","customer","department","maturity","yield","rate","period","valueDate","taxCode","principal","fromCcy","toCcy","originalRef","costCenter","profitCenter","notes"];
  const [newCol,setNewCol]=useState("");

  const update=(k,v)=>setForm(f=>({...f,[k]:v}));
  const addCond=()=>update("conditions",[...form.conditions,{field:"description",op:"contains",value:""}]);
  const updCond=(i,k,v)=>update("conditions",form.conditions.map((c,ci)=>ci===i?{...c,[k]:v}:c));
  const remCond=(i)=>update("conditions",form.conditions.filter((_,ci)=>ci!==i));
  const addCol=()=>{if(newCol&&!form.columns.includes(newCol)){update("columns",[...form.columns,newCol]);setNewCol("");}};
  const remCol=(c)=>update("columns",form.columns.filter(x=>x!==c));

  return <div className="modal-bg" onClick={e=>e.target===e.currentTarget&&onClose()}>
    <div className="modal modal-xl" onClick={e=>e.stopPropagation()}>
      <SectionHdr title={isNew?"New Category":"Edit Category"} sub="Define auto-classification rules using plain language conditions — no coding required"/>

      <div style={{display:"grid",gridTemplateColumns:"1fr 1fr 1fr 1fr",gap:10,marginBottom:16}}>
        <div><label>Category Name</label><input value={form.name} onChange={e=>update("name",e.target.value)}/></div>
        <div><label>Icon (emoji)</label><input value={form.icon} onChange={e=>update("icon",e.target.value)}/></div>
        <div><label>Color</label><input type="color" value={form.color} onChange={e=>update("color",e.target.value)} style={{height:38,padding:4,cursor:"pointer"}}/></div>
        <div><label>Type</label>
          <select value={form.type} onChange={e=>update("type",e.target.value)}>
            {["cashin","cashout","expense","transfer","investment","reversal","other"].map(t=><option key={t}>{t}</option>)}
          </select></div>
        <div><label>Transaction Side</label>
          <select value={form.side} onChange={e=>update("side",e.target.value)}>
            <option value="both">Both (debit & credit)</option>
            <option value="debit">Debit only</option>
            <option value="credit">Credit only</option>
            <option value="same">Same side (for reversals)</option>
          </select></div>
        <div style={{display:"flex",gap:10,alignItems:"center",paddingTop:20}}>
          <Toggle on={form.autoMatch} onChange={v=>update("autoMatch",v)} size="sm"/>
          <span style={{fontSize:11.5}}>Auto-match transactions</span>
        </div>
      </div>

      {/* Conditions */}
      <div style={{background:"#060910",border:"1px solid #161e38",borderRadius:12,padding:14,marginBottom:14}}>
        <div style={{display:"flex",justifyContent:"space-between",marginBottom:10}}>
          <div>
            <span style={{fontSize:11,fontWeight:700,color:"#3a4e72",letterSpacing:".07em"}}>AUTO-CLASSIFY CONDITIONS</span>
            <p style={{fontSize:10.5,color:"#2e3f60",marginTop:2}}>Plain language rules — AI interprets these. No coding needed.</p>
          </div>
          <button className="btn btn-ghost btn-xs" onClick={addCond}>+ Add Condition</button>
        </div>
        {/* Formula helper tokens */}
        <div style={{display:"flex",gap:5,flexWrap:"wrap",marginBottom:12}}>
          {["AND","OR","NOT","amount_match","date_match","description_contains","amount_less_than","amount_greater_than","tolerance(%)","day_of_week","matches_negative","within_N_days","counterparty_contains"].map(t=>(
            <span key={t} className="formula-token">{t}</span>
          ))}
        </div>
        {form.conditions.map((cond,i)=>(
          <div key={i} style={{display:"grid",gridTemplateColumns:"40px 1fr 1fr 1fr 30px",gap:8,marginBottom:8,alignItems:"center"}}>
            <span style={{fontSize:10.5,color:"#3a4e72",fontFamily:"IBM Plex Mono",textAlign:"center"}}>{i===0?"IF":"AND"}</span>
            <select value={cond.field} onChange={e=>updCond(i,"field",e.target.value)}>{FIELDS.map(f=><option key={f}>{f}</option>)}</select>
            <select value={cond.op} onChange={e=>updCond(i,"op",e.target.value)}>{OPS.map(o=><option key={o}>{o}</option>)}</select>
            <input value={cond.value} onChange={e=>updCond(i,"value",e.target.value)} placeholder="Value..."/>
            <button className="btn btn-ghost btn-xs" onClick={()=>remCond(i)} style={{color:"#dc2626",padding:"2px 5px"}}>✕</button>
          </div>
        ))}
        {/* Preview */}
        <div style={{marginTop:10,padding:"7px 11px",background:"#0a0d1e",borderRadius:8,fontFamily:"IBM Plex Mono",fontSize:10.5,color:"#5a7af5"}}>
          <span style={{color:"#2e3f60"}}>Rule: </span>
          {form.conditions.map((c,i)=>`${i>0?" AND ":""}${c.field} ${c.op} "${c.value}"`).join("")||"No conditions yet"}
        </div>
      </div>

      {/* Columns */}
      <div style={{background:"#060910",border:"1px solid #161e38",borderRadius:12,padding:14,marginBottom:16}}>
        <div style={{display:"flex",justifyContent:"space-between",marginBottom:10,alignItems:"center"}}>
          <span style={{fontSize:11,fontWeight:700,color:"#3a4e72",letterSpacing:".07em"}}>COLUMNS FOR THIS CATEGORY</span>
          <div style={{display:"flex",gap:6}}>
            <select value={newCol} onChange={e=>setNewCol(e.target.value)} style={{width:150}}>
              <option value="">Add column...</option>
              {AVAILABLE_COLS.filter(c=>!form.columns.includes(c)).map(c=><option key={c} value={c}>{c}</option>)}
            </select>
            <input value={newCol} onChange={e=>setNewCol(e.target.value)} placeholder="Or type custom..." style={{width:130}}/>
            <button className="btn btn-success btn-xs" onClick={addCol}>+ Add</button>
          </div>
        </div>
        <div style={{display:"flex",flexWrap:"wrap",gap:6}}>
          {form.columns.map(col=>(
            <span key={col} className="tag">
              {col}
              <span className="tag-remove" onClick={()=>remCol(col)}>✕</span>
            </span>
          ))}
        </div>
      </div>

      <div style={{display:"flex",gap:10,justifyContent:"flex-end"}}>
        <button className="btn btn-ghost" onClick={onClose}>Cancel</button>
        <button className="btn btn-success" onClick={()=>onSave(form)} disabled={!form.name}>
          ✓ {isNew?"Create Category":"Save Changes"}
        </button>
      </div>
    </div>
  </div>;
}

// ═══════════════════════════════════════════════════════════════
// ML INSIGHTS TAB
// ═══════════════════════════════════════════════════════════════
function MLTab({categories,notify}){
  const [training,setTraining]=useState(false);
  const [tab,setTab]=useState("model");

  return <div className="fadeUp" style={{display:"flex",flexDirection:"column",gap:18}}>
    <div style={{display:"flex",gap:8}}>
      {["model","patterns","category_learning","format_learning"].map(t=>(
        <button key={t} className={`tab-pill ${tab===t?"active":""}`} onClick={()=>setTab(t)}>
          {t.replace(/_/g," ").replace(/\b\w/g,c=>c.toUpperCase())}
        </button>
      ))}
    </div>

    {tab==="model"&&<>
      <div className="card card-glow" style={{borderColor:"#00c48640"}}>
        <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start",flexWrap:"wrap",gap:10}}>
          <div>
            <h2 style={{fontSize:15,fontWeight:800,color:"#00c486"}}>🧠 ML Model Health</h2>
            <p style={{fontSize:11.5,color:"#3a4e72",marginTop:3}}>Trained on 15,420 transactions · Last updated 2025-01-18 · Learns from every manual action</p>
          </div>
          <button className="btn btn-success btn-sm" onClick={()=>{setTraining(true);setTimeout(()=>{setTraining(false);notify("Retrained! Accuracy +0.3%","success");},3500);}} disabled={training}>
            {training?<span className="spin">⟳</span>:"⟳"} {training?"Retraining...":"Retrain Model"}
          </button>
        </div>
        <div style={{display:"grid",gridTemplateColumns:"repeat(4,1fr)",gap:14,marginTop:20}}>
          {[{l:"Accuracy",v:94},{l:"Precision",v:92},{l:"Recall",v:96},{l:"F1 Score",v:94}].map(m=>(
            <div key={m.l} style={{textAlign:"center"}}>
              <DonutChart val={m.v} size={84} color="#00c486" label={m.l}/>
              <div style={{fontSize:10.5,color:"#3a4e72",marginTop:5}}>{m.v+".2"}%</div>
            </div>
          ))}
        </div>
      </div>
      <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:16}}>
        <div className="card">
          <h3 style={{fontSize:13,fontWeight:700,marginBottom:16}}>Learning Progress</h3>
          {[
            {l:"Manual corrections this month",v:"47",c:"#3d5fef"},
            {l:"New patterns discovered",v:"8",c:"#00c486"},
            {l:"Accuracy improvement",v:"+0.3%",c:"#00c486"},
            {l:"Category rules learned",v:"12",c:"#7c3aed"},
            {l:"FP&A responses trained",v:"6",c:"#d97706"},
            {l:"Format templates matched",v:"4",c:"#3d5fef"},
          ].map(s=><div key={s.l} style={{display:"flex",justifyContent:"space-between",padding:"7px 0",borderBottom:"1px solid #0a0f1e",fontSize:12}}>
            <span style={{color:"#7a8fb8"}}>{s.l}</span>
            <span className="mono" style={{color:s.c,fontWeight:700}}>{s.v}</span>
          </div>)}
        </div>
        <div className="card">
          <h3 style={{fontSize:13,fontWeight:700,marginBottom:16}}>Category ML Performance</h3>
          {categories.slice(0,6).map(cat=>{
            const acc=Math.floor(Math.random()*15+82);
            return <div key={cat.id} style={{marginBottom:12}}>
              <div style={{display:"flex",justifyContent:"space-between",marginBottom:4}}>
                <span style={{fontSize:11.5,fontWeight:600}}>{cat.icon} {cat.name}</span>
                <ConfPill v={acc}/>
              </div>
              <ProgBar val={acc} color={cat.color}/>
            </div>;
          })}
        </div>
      </div>
    </>}

    {tab==="patterns"&&<div className="card">
      <SectionHdr title="Learned Matching Patterns" sub="Patterns discovered from historical reconciliations and manual corrections"/>
      <table>
        <thead><tr><th>Pattern</th><th>Bank / Scope</th><th>Frequency</th><th>Confidence</th><th>Type</th><th>Active</th></tr></thead>
        <tbody>{[
          {p:"SWIFT amount ±0.01 tolerance",bank:"All",freq:2341,conf:99,type:"ml"},
          {p:"Payroll batch end-of-week",bank:"Barclays",freq:892,conf:98,type:"ml"},
          {p:"Bank charges quarterly cycle",bank:"HSBC",freq:156,conf:87,type:"learned"},
          {p:"FX revaluation cross-reference",bank:"Citi",freq:445,conf:92,type:"ml"},
          {p:"T-Bill reversal same amount",bank:"All",freq:89,conf:96,type:"category"},
          {p:"Interest expense value date",bank:"Deutsche",freq:312,conf:94,type:"interest"},
        ].map((p,i)=><tr key={i}>
          <td style={{fontWeight:600,fontSize:12}}>{p.p}</td>
          <td style={{fontSize:11.5,color:"#5a7af5"}}>{p.bank}</td>
          <td className="mono">{p.freq.toLocaleString()}</td>
          <td><ConfPill v={p.conf}/></td>
          <td><span className="badge" style={{background:p.type==="ml"?"#3d5fef22":"#00c48622",color:p.type==="ml"?"#5a7af5":"#00c486"}}>{p.type}</span></td>
          <td><Toggle on={true} onChange={()=>{}} size="sm"/></td>
        </tr>)}
        </tbody>
      </table>
    </div>}
  </div>;
}

// ═══════════════════════════════════════════════════════════════
// UPLOAD TAB (simplified but complete)
// ═══════════════════════════════════════════════════════════════
function UploadTab({categories,notify}){
  const [files,setFiles]=useState([]);
  const [dragOver,setDragOver]=useState(null);
  const [processing,setProcessing]=useState(false);

  const addFiles=(type,rawFiles)=>{
    const added=rawFiles.map(f=>({
      id:Date.now()+Math.random(),name:f.name,size:(f.size/1024).toFixed(1)+"KB",
      type,status:"queued",progress:0,templateMatch:null,
      mlSuggestion:type==="bank"?"HSBC CSV Template":type==="gl"?"SAP GL Template":null
    }));
    setFiles(p=>[...p,...added]);
    notify(`${rawFiles.length} file(s) added — AI will parse and auto-categorize`,"info");
  };

  const processAll=()=>{
    setProcessing(true);
    const iv=setInterval(()=>{
      setFiles(f=>f.map(x=>({...x,progress:Math.min(x.progress+Math.random()*10+5,100),status:x.progress>90?"done":"processing"})));
    },250);
    setTimeout(()=>{clearInterval(iv);setProcessing(false);setFiles(f=>f.map(x=>({...x,progress:100,status:"done"})));notify("All files processed! Categories applied. Reconciliation started.","success");},5000);
  };

  return <div className="fadeUp" style={{display:"flex",flexDirection:"column",gap:20}}>
    <div style={{display:"grid",gridTemplateColumns:"1fr 1fr 1fr",gap:14}}>
      {[
        {type:"bank",label:"Bank Statements",icon:"🏦",formats:"CSV · XLS · XLSX · OFX · MT940 · BAI2",color:"#3d5fef"},
        {type:"gl",label:"SAP GL Extract",icon:"📋",formats:"CSV · XLSX · TXT · XML · IDoc",color:"#00c486"},
        {type:"zip",label:"ZIP Bundle",icon:"📦",formats:"ZIP — AI auto-detects all file types",color:"#7c3aed"},
      ].map(({type,label,icon,formats,color})=>(
        <div key={type} className={`upload-zone ${dragOver===type?"drag":""}`}
          onDragOver={e=>{e.preventDefault();setDragOver(type);}}
          onDragLeave={()=>setDragOver(null)}
          onDrop={e=>{e.preventDefault();setDragOver(null);const f=Array.from(e.dataTransfer?.files||[]);if(f.length)addFiles(type,f);}}
          onClick={()=>document.getElementById(`fi-${type}`).click()}>
          <input id={`fi-${type}`} type="file" multiple style={{display:"none"}} onChange={e=>{addFiles(type,Array.from(e.target.files));e.target.value="";}}/>
          <div style={{fontSize:38,marginBottom:10}}>{icon}</div>
          <div style={{fontWeight:800,fontSize:13,color,marginBottom:5}}>{label}</div>
          <div style={{fontSize:11,color:"#3a4e72",marginBottom:8}}>{formats}</div>
          <div style={{fontSize:10,color:"#2e3f60",background:"#080c1c",border:"1px solid #161e38",borderRadius:7,padding:"3px 10px",display:"inline-block"}}>
            Drop or click to browse
          </div>
        </div>
      ))}
    </div>

    <div className="card">
      <SectionHdr title="Auto-Classification Preview" sub="Categories that will be auto-applied when files are processed"/>
      <div style={{display:"flex",gap:8,flexWrap:"wrap"}}>
        {categories.filter(c=>c.autoMatch).map(cat=>(
          <div key={cat.id} style={{display:"flex",gap:6,alignItems:"center",padding:"6px 12px",background:"#0a0d1e",border:`1px solid ${cat.color}44`,borderRadius:20}}>
            <span>{cat.icon}</span>
            <span style={{fontSize:11.5,fontWeight:600,color:cat.color}}>{cat.name}</span>
            <span className="badge" style={{background:"#00c48622",color:"#00c486",fontSize:8}}>AUTO</span>
          </div>
        ))}
      </div>
    </div>

    {files.length>0&&<div className="card">
      <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:16}}>
        <h3 style={{fontSize:14,fontWeight:700}}>Queue ({files.length} files)</h3>
        <div style={{display:"flex",gap:8}}>
          <button className="btn btn-ghost btn-sm" onClick={()=>setFiles([])}>Clear</button>
          <button className="btn btn-primary" onClick={processAll} disabled={processing}>
            {processing?<span className="spin">⟳</span>:"🚀"} {processing?"Processing...":"Process & Categorize All"}
          </button>
        </div>
      </div>
      <table>
        <thead><tr><th>File</th><th>Size</th><th>Type</th><th>AI Template Suggestion</th><th>Status</th><th>Progress</th></tr></thead>
        <tbody>{files.map(f=><tr key={f.id}>
          <td className="mono" style={{fontSize:10.5}}>{f.name}</td>
          <td className="mono" style={{fontSize:10,color:"#3a4e72"}}>{f.size}</td>
          <td><span className="badge" style={{background:f.type==="gl"?"#00c48622":f.type==="zip"?"#7c3aed22":"#3d5fef22",color:f.type==="gl"?"#00c486":f.type==="zip"?"#a78bfa":"#5a7af5"}}>{f.type.toUpperCase()}</span></td>
          <td style={{fontSize:11,color:f.mlSuggestion?"#00c486":"#d97706"}}>{f.mlSuggestion||"New format — AI will scan"}</td>
          <td><span className="badge" style={{background:f.status==="done"?"#00c48622":f.status==="processing"?"#3d5fef22":"#3a4e7222",color:f.status==="done"?"#00c486":f.status==="processing"?"#5a7af5":"#5a7af5"}}>{f.status}</span></td>
          <td style={{minWidth:100}}><ProgBar val={f.progress} color={f.progress===100?"#00c486":"#2a46cc"}/></td>
        </tr>)}</tbody>
      </table>
    </div>}
  </div>;
}

// ═══════════════════════════════════════════════════════════════
// ROOT APP
// ═══════════════════════════════════════════════════════════════
const TABS=[
  {id:"dashboard",label:"Dashboard",icon:"◈"},
  {id:"reconcile",label:"Reconcile",icon:"⇌"},
  {id:"upload",label:"Upload",icon:"⬆"},
  {id:"analysis",label:"Analysis",icon:"▦"},
  {id:"ml",label:"ML Insights",icon:"⬡"},
  {id:"settings",label:"Settings",icon:"⚙"},
];

export default function App(){
  const [tab,setTab]=useState("dashboard");
  const [viewMode,setViewMode]=useState("user");
  const [notif,setNotif]=useState(null);
  const [showChat,setShowChat]=useState(false);
  const [sideOpen,setSideOpen]=useState(true);
  const [categories,setCategories]=useState(CATEGORIES);
  const [customRules,setCustomRules]=useState(MATCHING_RULES_CUSTOM);
  const [analyticsModules,setAnalyticsModules]=useState(DEFAULT_ANALYTICS_MODULES);
  const [currentUser]=useState(USERS[0]); // Admin by default

  const notify=useCallback((msg,type="info")=>setNotif({msg,type,id:Date.now()}),[]);

  const commonProps={banks:BANKS,categories,setCategories,customRules,setCustomRules,analyticsModules,setAnalyticsModules,viewMode,notify,currentUser};

  const renderTab=()=>{
    switch(tab){
      case "dashboard":return <DashboardTab {...commonProps}/>;
      case "reconcile":return <ReconcileTab {...commonProps}/>;
      case "upload":return <UploadTab {...commonProps}/>;
      case "analysis":return <AnalysisTab {...commonProps}/>;
      case "ml":return <MLTab {...commonProps}/>;
      case "settings":return <SettingsTab {...commonProps}/>;
      default:return null;
    }
  };

  return <>
    <style>{STYLES}</style>
    {notif&&<Notif key={notif.id} n={notif} onClose={()=>setNotif(null)}/>}
    {showChat&&<ChatBot {...commonProps} onClose={()=>setShowChat(false)}/>}

    <div style={{display:"flex",minHeight:"100vh"}}>
      {/* Sidebar */}
      <div style={{width:sideOpen?206:52,background:"#060810",borderRight:"1px solid #0f1525",display:"flex",flexDirection:"column",transition:"width .28s ease",overflow:"hidden",flexShrink:0}}>
        {/* Logo */}
        <div style={{padding:"18px 12px",borderBottom:"1px solid #0f1525"}}>
          <div style={{display:"flex",alignItems:"center",gap:9}}>
            <div style={{width:30,height:30,borderRadius:9,background:"linear-gradient(135deg,#2a46cc,#00c486)",display:"flex",alignItems:"center",justifyContent:"center",fontSize:15,flexShrink:0}}>⇌</div>
            {sideOpen&&<div>
              <div style={{fontSize:12.5,fontWeight:800}}>BankRecon <span className="gt">AI</span></div>
              <div style={{fontSize:8.5,color:"#1e2c4a",letterSpacing:".08em"}}>ENTERPRISE v3</div>
            </div>}
          </div>
        </div>

        {/* Current User */}
        {sideOpen&&<div style={{padding:"10px 12px",borderBottom:"1px solid #0f1525"}}>
          <div style={{display:"flex",gap:8,alignItems:"center"}}>
            <div style={{width:26,height:26,borderRadius:"50%",background:"linear-gradient(135deg,#2a46cc,#7c3aed)",display:"flex",alignItems:"center",justifyContent:"center",fontSize:10,fontWeight:800,flexShrink:0}}>{currentUser.avatar}</div>
            <div>
              <div style={{fontSize:11,fontWeight:700}}>{currentUser.name}</div>
              <div style={{fontSize:9,color:"#3a4e72"}}>{currentUser.role} · {currentUser.company}</div>
            </div>
          </div>
        </div>}

        <nav style={{flex:1,padding:"10px 6px"}}>
          {TABS.map(t=>(
            <div key={t.id} className={`sidebar-link ${tab===t.id?"active":""}`}
              onClick={()=>setTab(t.id)}
              style={{justifyContent:sideOpen?"flex-start":"center",paddingLeft:sideOpen?13:0}}>
              <span style={{fontSize:16,flexShrink:0}}>{t.icon}</span>
              {sideOpen&&<span>{t.label}</span>}
            </div>
          ))}
        </nav>

        {/* Chat button */}
        <div style={{padding:"10px 6px",borderTop:"1px solid #0f1525"}}>
          <div className="sidebar-link" style={{justifyContent:sideOpen?"flex-start":"center",paddingLeft:sideOpen?13:0,color:"#00c486",background:showChat?"rgba(0,196,134,.1)":"transparent"}}
            onClick={()=>setShowChat(s=>!s)}>
            <span style={{fontSize:16}}>🧠</span>
            {sideOpen&&<span>AI Assistant</span>}
          </div>
          <button className="btn btn-ghost btn-sm" style={{width:"100%",justifyContent:"center",marginTop:6}} onClick={()=>setSideOpen(o=>!o)}>
            {sideOpen?"◀":"▶"}
          </button>
        </div>
      </div>

      {/* Main */}
      <div style={{flex:1,display:"flex",flexDirection:"column",overflow:"hidden"}}>
        {/* Topbar */}
        <div style={{padding:"12px 22px",borderBottom:"1px solid #0f1525",display:"flex",justifyContent:"space-between",alignItems:"center",background:"#060810",flexShrink:0,gap:10,flexWrap:"wrap"}}>
          <div>
            <h1 style={{fontSize:16,fontWeight:800}}>
              <span className="gt">{TABS.find(t=>t.id===tab)?.label}</span>
            </h1>
            <div style={{fontSize:10.5,color:"#1e2c4a",marginTop:1}}>BankRecon AI v3 · Enterprise · FY 2025 · Powered by Claude</div>
          </div>
          <div style={{display:"flex",gap:8,alignItems:"center",flexWrap:"wrap"}}>
            {tab==="analysis"&&<ViewBanner mode={viewMode} onChange={setViewMode}/>}
            <div style={{display:"flex",gap:5,flexWrap:"wrap"}}>
              {BANKS.filter(b=>b.status!=="reconciled").slice(0,3).map(b=>(
                <span key={b.id} className="badge" style={{background:SC[b.status]+"22",color:SC[b.status]}}>{b.short}: {b.status}</span>
              ))}
            </div>
            <button className="btn btn-primary btn-sm" onClick={()=>notify("Running full AI reconciliation on all banks...","info")}>⚡ Run All</button>
            <button className="btn btn-ghost btn-sm" onClick={()=>setShowChat(s=>!s)} style={{color:"#00c486",borderColor:"#00c48640"}}>🧠 Chat</button>
          </div>
        </div>

        {/* Content */}
        <div style={{flex:1,overflow:"auto",padding:"20px 22px"}}>
          {renderTab()}
        </div>
      </div>
    </div>
  </>;
}
