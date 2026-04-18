import { useState, useEffect, useRef } from "react";

// ── Capital presets ──────────────────────────────────────────────────
const CAPITAL_PRESETS = [
  { label: "₹10K",  value: 10000 },
  { label: "₹25K",  value: 25000 },
  { label: "₹50K",  value: 50000 },
  { label: "₹1L",   value: 100000 },
  { label: "₹2L",   value: 200000 },
  { label: "₹5L",   value: 500000 },
  { label: "₹10L",  value: 1000000 },
  { label: "₹25L",  value: 2500000 },
  { label: "₹50L",  value: 5000000 },
  { label: "₹1Cr",  value: 10000000 },
];

// ── Assets ───────────────────────────────────────────────────────────
const NSE_STOCKS = [
  { symbol:"RELIANCE",  name:"Reliance Industries",       exchange:"NSE", basePrice:2923,  sector:"Energy" },
  { symbol:"TCS",       name:"Tata Consultancy Services", exchange:"NSE", basePrice:3870,  sector:"IT" },
  { symbol:"HDFCBANK",  name:"HDFC Bank",                 exchange:"NSE", basePrice:1895,  sector:"Banking" },
  { symbol:"BHARTIARTL",name:"Bharti Airtel",             exchange:"NSE", basePrice:1842,  sector:"Telecom" },
  { symbol:"ICICIBANK", name:"ICICI Bank",                exchange:"NSE", basePrice:1289,  sector:"Banking" },
  { symbol:"INFY",      name:"Infosys",                   exchange:"NSE", basePrice:1567,  sector:"IT" },
  { symbol:"SBIN",      name:"State Bank of India",       exchange:"NSE", basePrice:812,   sector:"Banking" },
  { symbol:"BAJFINANCE",name:"Bajaj Finance",             exchange:"NSE", basePrice:8234,  sector:"Finance" },
  { symbol:"LT",        name:"Larsen & Toubro",           exchange:"NSE", basePrice:3560,  sector:"Infra" },
  { symbol:"HINDUNILVR",name:"Hindustan Unilever",        exchange:"NSE", basePrice:2178,  sector:"FMCG" },
  { symbol:"MARUTI",    name:"Maruti Suzuki India",       exchange:"NSE", basePrice:12450, sector:"Auto" },
  { symbol:"AXISBANK",  name:"Axis Bank",                 exchange:"NSE", basePrice:1134,  sector:"Banking" },
  { symbol:"SUNPHARMA", name:"Sun Pharmaceutical",        exchange:"NSE", basePrice:1780,  sector:"Pharma" },
  { symbol:"M&M",       name:"Mahindra & Mahindra",       exchange:"NSE", basePrice:2890,  sector:"Auto" },
  { symbol:"TITAN",     name:"Titan Company",             exchange:"NSE", basePrice:3245,  sector:"Consumer" },
  { symbol:"HCLTECH",   name:"HCL Technologies",          exchange:"NSE", basePrice:1623,  sector:"IT" },
  { symbol:"ITC",       name:"ITC Limited",               exchange:"NSE", basePrice:452,   sector:"FMCG" },
  { symbol:"KOTAKBANK", name:"Kotak Mahindra Bank",       exchange:"NSE", basePrice:2145,  sector:"Banking" },
  { symbol:"NTPC",      name:"NTPC",                      exchange:"NSE", basePrice:356,   sector:"Power" },
  { symbol:"ULTRACEMCO",name:"UltraTech Cement",          exchange:"NSE", basePrice:11230, sector:"Cement" },
  { symbol:"ADANIPORTS",name:"Adani Ports & SEZ",         exchange:"NSE", basePrice:1312,  sector:"Infra" },
  { symbol:"BEL",       name:"Bharat Electronics",        exchange:"NSE", basePrice:278,   sector:"Defence" },
  { symbol:"WIPRO",     name:"Wipro Limited",             exchange:"NSE", basePrice:312,   sector:"IT" },
  { symbol:"TATAMOTORS",name:"Tata Motors",               exchange:"NSE", basePrice:678,   sector:"Auto" },
  { symbol:"ONGC",      name:"Oil & Natural Gas Corp",    exchange:"NSE", basePrice:265,   sector:"Energy" },
  { symbol:"POWERGRID", name:"Power Grid Corp",           exchange:"NSE", basePrice:298,   sector:"Power" },
  { symbol:"NESTLEIND", name:"Nestle India",              exchange:"NSE", basePrice:2234,  sector:"FMCG" },
  { symbol:"TATASTEEL", name:"Tata Steel",                exchange:"NSE", basePrice:167,   sector:"Metal" },
  { symbol:"JSWSTEEL",  name:"JSW Steel",                 exchange:"NSE", basePrice:923,   sector:"Metal" },
  { symbol:"TECHM",     name:"Tech Mahindra",             exchange:"NSE", basePrice:1456,  sector:"IT" },
  { symbol:"GRASIM",    name:"Grasim Industries",         exchange:"NSE", basePrice:2678,  sector:"Diversified" },
  { symbol:"DIVISLAB",  name:"Divi's Laboratories",       exchange:"NSE", basePrice:5230,  sector:"Pharma" },
  { symbol:"BPCL",      name:"Bharat Petroleum",          exchange:"NSE", basePrice:345,   sector:"Energy" },
  { symbol:"EICHERMOT", name:"Eicher Motors",             exchange:"NSE", basePrice:4890,  sector:"Auto" },
  { symbol:"CIPLA",     name:"Cipla",                     exchange:"NSE", basePrice:1543,  sector:"Pharma" },
  { symbol:"DRREDDY",   name:"Dr. Reddy's Labs",          exchange:"NSE", basePrice:6780,  sector:"Pharma" },
  { symbol:"COALINDIA", name:"Coal India",                exchange:"NSE", basePrice:412,   sector:"Mining" },
  { symbol:"APOLLOHOSP",name:"Apollo Hospitals",          exchange:"NSE", basePrice:7230,  sector:"Healthcare" },
  { symbol:"BAJAJFINSV",name:"Bajaj Finserv",             exchange:"NSE", basePrice:1923,  sector:"Finance" },
  { symbol:"VEDL",      name:"Vedanta",                   exchange:"NSE", basePrice:456,   sector:"Metal" },
  { symbol:"INDUSINDBK",name:"IndusInd Bank",             exchange:"NSE", basePrice:987,   sector:"Banking" },
  { symbol:"HEROMOTOCO",name:"Hero MotoCorp",             exchange:"NSE", basePrice:4120,  sector:"Auto" },
  { symbol:"TATACONSUM",name:"Tata Consumer Products",    exchange:"NSE", basePrice:1089,  sector:"FMCG" },
  { symbol:"DLF",       name:"DLF Limited",               exchange:"NSE", basePrice:789,   sector:"Realty" },
  { symbol:"HINDALCO",  name:"Hindalco Industries",       exchange:"NSE", basePrice:678,   sector:"Metal" },
  { symbol:"ZOMATO",    name:"Zomato (Eternal)",          exchange:"NSE", basePrice:234,   sector:"Tech" },
  { symbol:"PAYTM",     name:"One 97 Communications",     exchange:"NSE", basePrice:678,   sector:"Fintech" },
  { symbol:"IRCTC",     name:"IRCTC",                     exchange:"NSE", basePrice:812,   sector:"Travel" },
  { symbol:"HDFCLIFE",  name:"HDFC Life Insurance",       exchange:"NSE", basePrice:712,   sector:"Insurance" },
  { symbol:"SBILIFE",   name:"SBI Life Insurance",        exchange:"NSE", basePrice:1678,  sector:"Insurance" },
];
const BSE_STOCKS = [
  { symbol:"ADANIENT",  name:"Adani Enterprises",         exchange:"BSE", basePrice:2234,  sector:"Diversified" },
  { symbol:"ADANIGREEN",name:"Adani Green Energy",        exchange:"BSE", basePrice:1456,  sector:"Power" },
  { symbol:"ADANIPOWER",name:"Adani Power",               exchange:"BSE", basePrice:567,   sector:"Power" },
  { symbol:"AMBUJACEM", name:"Ambuja Cements",            exchange:"BSE", basePrice:612,   sector:"Cement" },
  { symbol:"ASIANPAINT",name:"Asian Paints",              exchange:"BSE", basePrice:2890,  sector:"Consumer" },
  { symbol:"AUROPHARMA",name:"Aurobindo Pharma",          exchange:"BSE", basePrice:1234,  sector:"Pharma" },
  { symbol:"BANKBARODA",name:"Bank of Baroda",            exchange:"BSE", basePrice:234,   sector:"Banking" },
  { symbol:"BERGEPAINT",name:"Berger Paints India",       exchange:"BSE", basePrice:567,   sector:"Consumer" },
  { symbol:"BOSCHLTD",  name:"Bosch Limited",             exchange:"BSE", basePrice:32450, sector:"Auto Parts" },
  { symbol:"BRITANNIA", name:"Britannia Industries",      exchange:"BSE", basePrice:5234,  sector:"FMCG" },
  { symbol:"CANBK",     name:"Canara Bank",               exchange:"BSE", basePrice:112,   sector:"Banking" },
  { symbol:"CHOLAFIN",  name:"Cholamandalam Invest.",     exchange:"BSE", basePrice:1456,  sector:"Finance" },
  { symbol:"DABUR",     name:"Dabur India",               exchange:"BSE", basePrice:534,   sector:"FMCG" },
  { symbol:"DMART",     name:"Avenue Supermarts",         exchange:"BSE", basePrice:3890,  sector:"Retail" },
  { symbol:"FEDERALBNK",name:"Federal Bank",              exchange:"BSE", basePrice:198,   sector:"Banking" },
  { symbol:"FORTIS",    name:"Fortis Healthcare",         exchange:"BSE", basePrice:678,   sector:"Healthcare" },
  { symbol:"GAIL",      name:"GAIL (India)",              exchange:"BSE", basePrice:193,   sector:"Energy" },
  { symbol:"GODREJCP",  name:"Godrej Consumer Prod.",     exchange:"BSE", basePrice:1234,  sector:"FMCG" },
  { symbol:"HAVELLS",   name:"Havells India",             exchange:"BSE", basePrice:1678,  sector:"Electricals" },
  { symbol:"ICICILOM",  name:"ICICI Lombard",             exchange:"BSE", basePrice:1890,  sector:"Insurance" },
  { symbol:"INDUSTOWER",name:"Indus Towers",              exchange:"BSE", basePrice:345,   sector:"Telecom" },
  { symbol:"JINDALSTEL",name:"Jindal Steel & Power",      exchange:"BSE", basePrice:912,   sector:"Metal" },
  { symbol:"JUBLFOOD",  name:"Jubilant Foodworks",        exchange:"BSE", basePrice:678,   sector:"Consumer" },
  { symbol:"LUPIN",     name:"Lupin",                     exchange:"BSE", basePrice:2123,  sector:"Pharma" },
  { symbol:"MARICO",    name:"Marico",                    exchange:"BSE", basePrice:645,   sector:"FMCG" },
  { symbol:"MPHASIS",   name:"Mphasis",                   exchange:"BSE", basePrice:2890,  sector:"IT" },
  { symbol:"MUTHOOTFIN",name:"Muthoot Finance",           exchange:"BSE", basePrice:1987,  sector:"Finance" },
  { symbol:"OBEROIRLTY",name:"Oberoi Realty",             exchange:"BSE", basePrice:1789,  sector:"Realty" },
  { symbol:"PERSISTENT",name:"Persistent Systems",        exchange:"BSE", basePrice:5670,  sector:"IT" },
  { symbol:"PNB",       name:"Punjab National Bank",      exchange:"BSE", basePrice:112,   sector:"Banking" },
  { symbol:"POLYCAB",   name:"Polycab India",             exchange:"BSE", basePrice:5678,  sector:"Electricals" },
  { symbol:"RECLTD",    name:"REC Limited",               exchange:"BSE", basePrice:456,   sector:"Finance" },
  { symbol:"SBICARD",   name:"SBI Cards & Payment",       exchange:"BSE", basePrice:890,   sector:"Finance" },
  { symbol:"SHREECEM",  name:"Shree Cement",              exchange:"BSE", basePrice:26780, sector:"Cement" },
  { symbol:"SIEMENS",   name:"Siemens India",             exchange:"BSE", basePrice:5890,  sector:"Engineering" },
  { symbol:"TATACOMM",  name:"Tata Communications",       exchange:"BSE", basePrice:1789,  sector:"Telecom" },
  { symbol:"TATAELXSI", name:"Tata Elxsi",                exchange:"BSE", basePrice:6780,  sector:"IT" },
  { symbol:"TORNTPHARM",name:"Torrent Pharma",            exchange:"BSE", basePrice:2890,  sector:"Pharma" },
  { symbol:"TRENT",     name:"Trent Limited",             exchange:"BSE", basePrice:5670,  sector:"Retail" },
  { symbol:"UNIONBANK", name:"Union Bank of India",       exchange:"BSE", basePrice:134,   sector:"Banking" },
  { symbol:"VOLTAS",    name:"Voltas",                    exchange:"BSE", basePrice:1456,  sector:"Consumer" },
  { symbol:"ZYDUSLIFE", name:"Zydus Lifesciences",        exchange:"BSE", basePrice:987,   sector:"Pharma" },
];
const CRYPTO_ASSETS = [
  { symbol:"BTC",  name:"Bitcoin",         exchange:"CRYPTO", basePrice:5780000, sector:"Layer 1" },
  { symbol:"ETH",  name:"Ethereum",        exchange:"CRYPTO", basePrice:289000,  sector:"Layer 1" },
  { symbol:"BNB",  name:"BNB",             exchange:"CRYPTO", basePrice:52000,   sector:"Exchange" },
  { symbol:"SOL",  name:"Solana",          exchange:"CRYPTO", basePrice:11900,   sector:"Layer 1" },
  { symbol:"XRP",  name:"XRP (Ripple)",    exchange:"CRYPTO", basePrice:425,     sector:"Payments" },
  { symbol:"DOGE", name:"Dogecoin",        exchange:"CRYPTO", basePrice:18,      sector:"Meme" },
  { symbol:"ADA",  name:"Cardano",         exchange:"CRYPTO", basePrice:62,      sector:"Layer 1" },
  { symbol:"AVAX", name:"Avalanche",       exchange:"CRYPTO", basePrice:2100,    sector:"Layer 1" },
  { symbol:"DOT",  name:"Polkadot",        exchange:"CRYPTO", basePrice:540,     sector:"Layer 0" },
  { symbol:"LINK", name:"Chainlink",       exchange:"CRYPTO", basePrice:1100,    sector:"Oracle" },
  { symbol:"MATIC",name:"Polygon",         exchange:"CRYPTO", basePrice:38,      sector:"Layer 2" },
  { symbol:"LTC",  name:"Litecoin",        exchange:"CRYPTO", basePrice:7100,    sector:"Payments" },
  { symbol:"UNI",  name:"Uniswap",         exchange:"CRYPTO", basePrice:890,     sector:"DeFi" },
  { symbol:"ATOM", name:"Cosmos",          exchange:"CRYPTO", basePrice:490,     sector:"Layer 0" },
  { symbol:"XLM",  name:"Stellar",         exchange:"CRYPTO", basePrice:28,      sector:"Payments" },
  { symbol:"NEAR", name:"NEAR Protocol",   exchange:"CRYPTO", basePrice:235,     sector:"Layer 1" },
  { symbol:"ARB",  name:"Arbitrum",        exchange:"CRYPTO", basePrice:78,      sector:"Layer 2" },
  { symbol:"OP",   name:"Optimism",        exchange:"CRYPTO", basePrice:145,     sector:"Layer 2" },
  { symbol:"INJ",  name:"Injective",       exchange:"CRYPTO", basePrice:1890,    sector:"DeFi" },
  { symbol:"SUI",  name:"Sui",             exchange:"CRYPTO", basePrice:320,     sector:"Layer 1" },
  { symbol:"APT",  name:"Aptos",           exchange:"CRYPTO", basePrice:645,     sector:"Layer 1" },
  { symbol:"TON",  name:"Toncoin",         exchange:"CRYPTO", basePrice:456,     sector:"Layer 1" },
  { symbol:"PEPE", name:"Pepe",            exchange:"CRYPTO", basePrice:0.0009,  sector:"Meme" },
  { symbol:"SHIB", name:"Shiba Inu",       exchange:"CRYPTO", basePrice:0.0012,  sector:"Meme" },
  { symbol:"WIF",  name:"dogwifhat",       exchange:"CRYPTO", basePrice:145,     sector:"Meme" },
  { symbol:"FIL",  name:"Filecoin",        exchange:"CRYPTO", basePrice:390,     sector:"Storage" },
  { symbol:"SAND", name:"The Sandbox",     exchange:"CRYPTO", basePrice:45,      sector:"Metaverse" },
  { symbol:"ALGO", name:"Algorand",        exchange:"CRYPTO", basePrice:22,      sector:"Layer 1" },
  { symbol:"VET",  name:"VeChain",         exchange:"CRYPTO", basePrice:3,       sector:"Supply Chain" },
  { symbol:"MANA", name:"Decentraland",    exchange:"CRYPTO", basePrice:34,      sector:"Metaverse" },
];
const ALL_ASSETS = [...NSE_STOCKS, ...BSE_STOCKS, ...CRYPTO_ASSETS];

// ── Trading modes ────────────────────────────────────────────────────
const TRADING_MODES = {
  INTRADAY:   { label:"Intraday",   short:"INTRA",  icon:"⚡", color:"#f59e0b", bg:"#1c1107", holdTime:"< 1 day",    riskLevel:"High",        stopLossPct:0.5,  target1:0.8,  target2:1.2,  target3:1.8,  desc:"Same-day trades. Fast, high-frequency scalps." },
  SWING:      { label:"Swing",      short:"SWING",  icon:"〜", color:"#38bdf8", bg:"#0c1a2e", holdTime:"2–7 days",   riskLevel:"Medium",      stopLossPct:2,    target1:3,    target2:5,    target3:8,    desc:"Ride momentum waves over days." },
  POSITIONAL: { label:"Positional", short:"POSIT",  icon:"📐", color:"#a78bfa", bg:"#1a0f2e", holdTime:"Weeks",      riskLevel:"Medium",      stopLossPct:4,    target1:8,    target2:12,   target3:18,   desc:"Trend-based entries held for weeks." },
  LONGTERM:   { label:"Long Term",  short:"LONG",   icon:"🏦", color:"#22c55e", bg:"#052e16", holdTime:"6+ months",  riskLevel:"Low",         stopLossPct:8,    target1:20,   target2:40,   target3:80,   desc:"Fundamental investing. Buy & hold." },
  SHORTTERM:  { label:"Short Term", short:"SHORT",  icon:"📅", color:"#fb923c", bg:"#1c0f07", holdTime:"1–4 weeks",  riskLevel:"Medium-High", stopLossPct:3,    target1:5,    target2:8,    target3:12,   desc:"News & catalyst plays." },
  BREAKOUT:   { label:"Breakout",   short:"BRKOUT", icon:"🚀", color:"#f43f5e", bg:"#1f0712", holdTime:"1–5 days",   riskLevel:"High",        stopLossPct:1.5,  target1:3,    target2:6,    target3:10,   desc:"Resistance breakout + volume spike." },
};

// ── Helpers ──────────────────────────────────────────────────────────
function genHistory(base, n = 80) {
  let p = base * (0.93 + Math.random() * 0.07), h = [];
  for (let i = 0; i < n; i++) {
    const v = base < 1 ? 0.04 : base < 50 ? 0.03 : base < 500 ? 0.015 : 0.008;
    p = Math.max(p * (1 + (Math.random() - 0.49) * v), 0.00001);
    h.push(parseFloat(p.toFixed(base < 0.01 ? 8 : base < 1 ? 4 : 2)));
  }
  return h;
}
function calcEMA(h, n) {
  if (h.length < n) return h[h.length - 1];
  const k = 2 / (n + 1); let e = h.slice(0, n).reduce((a, b) => a + b, 0) / n;
  for (let i = n; i < h.length; i++) e = h[i] * k + e * (1 - k);
  return e;
}
function isBreakout(h) {
  if (h.length < 25) return false;
  const res = Math.max(...h.slice(-25, -5));
  return h[h.length - 1] > res * 1.005;
}
function computeSignals(h, price, trend, rsi) {
  const e10 = calcEMA(h, 10), e20 = calcEMA(h, 20), e50 = calcEMA(h, 50);
  const brk = isBreakout(h);
  let signal = "HOLD";
  if (rsi < 32 && trend === "BULLISH" && price > e20) signal = "BUY";
  else if (rsi > 70 && trend === "BEARISH") signal = "SELL";
  else if (price > e10 && e10 > e20 && trend === "BULLISH") signal = "BUY";
  else if (price < e10 && trend === "BEARISH") signal = "SELL";
  return { signal, e10, e20, e50, brk };
}
function getModeSignal(mode, a) {
  if (mode === "BREAKOUT")  return a.brk ? "BUY" : a.signal === "BUY" ? "WATCH" : "HOLD";
  if (mode === "INTRADAY")  return Math.abs(a.change) > 0.3 ? a.signal : "HOLD";
  if (mode === "LONGTERM")  return a.price > a.e50 && a.signal === "BUY" ? "BUY" : a.signal === "SELL" ? "SELL" : "HOLD";
  if (mode === "POSITIONAL")return a.price > a.e20 && a.signal === "BUY" ? "BUY" : a.signal;
  return a.signal;
}
function calcTargets(price, mode) {
  const m = TRADING_MODES[mode];
  return {
    sl: +(price * (1 - m.stopLossPct / 100)).toFixed(2),
    t1: +(price * (1 + m.target1 / 100)).toFixed(2),
    t2: +(price * (1 + m.target2 / 100)).toFixed(2),
    t3: +(price * (1 + m.target3 / 100)).toFixed(2),
  };
}
function fmtP(p) {
  if (p < 0.0001) return p.toFixed(8);
  if (p < 0.1) return p.toFixed(4);
  if (p < 10) return p.toFixed(2);
  return p.toLocaleString("en-IN", { maximumFractionDigits: 2 });
}
function fmtN(n) { return (n || 0).toLocaleString("en-IN", { maximumFractionDigits: 2 }); }
function fmtC(n) {
  if (n >= 10000000) return "₹" + (n / 10000000).toFixed(2) + "Cr";
  if (n >= 100000)   return "₹" + (n / 100000).toFixed(2) + "L";
  if (n >= 1000)     return "₹" + (n / 1000).toFixed(1) + "K";
  return "₹" + n;
}

function initMarket() {
  const m = {};
  ALL_ASSETS.forEach(a => {
    const h = genHistory(a.basePrice), price = h[h.length - 1], prev = h[h.length - 2];
    const rsi = 35 + Math.random() * 30, trend = Math.random() > 0.5 ? "BULLISH" : "BEARISH";
    const { signal, e10, e20, e50, brk } = computeSignals(h, price, trend, rsi);
    m[a.symbol] = { ...a, price, prev, change: +((price - prev) / prev * 100).toFixed(2), volume: Math.floor(Math.random() * 2e6 + 1e5), history: h, rsi, macd: (Math.random() - 0.5) * 10, trend, signal, e10, e20, e50, brk };
  });
  return m;
}
function tickMarket(prev) {
  const next = {};
  Object.keys(prev).forEach(sym => {
    const a = prev[sym];
    const isCrypto = a.exchange === "CRYPTO";
    const vol = isCrypto ? 0.02 : a.basePrice < 50 ? 0.012 : 0.007;
    const bias = a.trend === "BULLISH" ? 0.002 : -0.002;
    const dp = a.basePrice < 0.01 ? 8 : a.basePrice < 1 ? 4 : 2;
    const newPrice = Math.max(+(a.price * (1 + bias + (Math.random() - 0.5) * vol)).toFixed(dp), 0.00001);
    const h = [...a.history.slice(-79), newPrice];
    const change = +((newPrice - a.prev) / a.prev * 100).toFixed(2);
    const trend = Math.random() < 0.015 ? (a.trend === "BULLISH" ? "BEARISH" : "BULLISH") : a.trend;
    const rsi = Math.max(5, Math.min(95, a.rsi + (Math.random() - 0.5) * 4));
    const macd = a.macd + (Math.random() - 0.5) * 0.5;
    const { signal, e10, e20, e50, brk } = computeSignals(h, newPrice, trend, rsi);
    next[sym] = { ...a, prev: a.price, price: newPrice, change, history: h, trend, rsi, macd, signal, e10, e20, e50, brk };
  });
  return next;
}

// ── Small chart components ────────────────────────────────────────────
function MiniChart({ history, color }) {
  const w = 70, h = 26;
  if (!history || history.length < 2) return null;
  const mn = Math.min(...history), mx = Math.max(...history), rng = mx - mn || 1;
  const pts = history.map((v, i) => `${(i / (history.length - 1) * w).toFixed(1)},${(h - (v - mn) / rng * (h - 4) - 2).toFixed(1)}`).join(" ");
  return <svg width={w} height={h} style={{ display: "block" }}><polyline points={pts} fill="none" stroke={color} strokeWidth="1.5" strokeLinejoin="round" /></svg>;
}
function CandleChart({ history, modeColor }) {
  const W = 400, H = 160;
  if (!history || history.length < 10) return null;
  const sl = history.slice(-35), mn = Math.min(...sl) * 0.997, mx = Math.max(...sl) * 1.003, rng = mx - mn || 1;
  const bw = Math.max(2, Math.floor(W / sl.length) - 1);
  return (
    <svg width="100%" height={H} viewBox={`0 0 ${W} ${H}`} style={{ display: "block" }}>
      <rect width={W} height={H} fill="#020617" rx="6" />
      {[0.2, 0.4, 0.6, 0.8].map((f, i) => <line key={i} x1={0} y1={H * f} x2={W} y2={H * f} stroke="#0a1628" strokeWidth="1" />)}
      {sl.map((p, i) => {
        const pp = sl[i - 1] || p, up = p >= pp;
        const x = i * (bw + 1) + 1, y1 = H - (Math.max(p, pp) - mn) / rng * (H - 8) - 4, y2 = H - (Math.min(p, pp) - mn) / rng * (H - 8) - 4;
        return <rect key={i} x={x} y={y1} width={bw} height={Math.max(y2 - y1, 1)} fill={up ? "#22c55e" : "#ef4444"} opacity={0.88} rx={1} />;
      })}
    </svg>
  );
}

// ── Bot activity popup ─────────────────────────────────────────────
function BotActivity({ items }) {
  return (
    <div style={{ position: "fixed", bottom: 20, right: 16, zIndex: 998, display: "flex", flexDirection: "column", gap: 5, pointerEvents: "none" }}>
      {items.slice(0, 4).map(a => (
        <div key={a.id} style={{ background: a.type === "BUY" ? "#052e16" : "#2d1515", border: `1px solid ${a.type === "BUY" ? "#16a34a" : "#dc2626"}`, borderRadius: 8, padding: "6px 12px", display: "flex", alignItems: "center", gap: 8, animation: "botpop .3s ease", opacity: a.fade ? 0.3 : 1, transition: "opacity 1s", boxShadow: "0 4px 16px rgba(0,0,0,.6)" }}>
          <div style={{ width: 6, height: 6, borderRadius: "50%", background: a.type === "BUY" ? "#22c55e" : "#ef4444", animation: "pulse 1s infinite" }} />
          <span style={{ fontWeight: 700, fontSize: 10, color: a.type === "BUY" ? "#4ade80" : "#f87171" }}>BOT {a.type}</span>
          <span style={{ color: "#94a3b8", fontSize: 10 }}>{a.symbol}</span>
          {a.mode && <span style={{ fontSize: 9, color: TRADING_MODES[a.mode]?.color, background: TRADING_MODES[a.mode]?.bg, padding: "1px 4px", borderRadius: 3, fontWeight: 700 }}>{TRADING_MODES[a.mode]?.icon}{TRADING_MODES[a.mode]?.short}</span>}
        </div>
      ))}
    </div>
  );
}

// ── Breakout scanner ─────────────────────────────────────────────────
function BreakoutScanner({ market, onSelect }) {
  const breakouts  = Object.values(market).filter(a => a.brk).sort((a, b) => b.change - a.change);
  const momentum   = Object.values(market).filter(a => a.change > 1.5 && a.trend === "BULLISH").sort((a, b) => b.change - a.change).slice(0, 8);
  const oversold   = Object.values(market).filter(a => a.rsi < 30).sort((a, b) => a.rsi - b.rsi).slice(0, 8);
  const Section = ({ title, icon, color, border, bg, items, badge }) => (
    <div style={{ marginBottom: 16 }}>
      <div style={{ display: "flex", alignItems: "center", gap: 8, marginBottom: 8 }}>
        <span style={{ fontSize: 14 }}>{icon}</span>
        <span style={{ color, fontWeight: 700, fontSize: 11, letterSpacing: ".08em" }}>{title}</span>
        <span style={{ background: bg, color, borderRadius: 4, fontSize: 9, padding: "1px 6px", fontWeight: 700 }}>{items.length}</span>
      </div>
      {items.length === 0 && <div style={{ color: "#1e293b", fontSize: 11, padding: "6px 0" }}>Scanning…</div>}
      {items.map(a => (
        <div key={a.symbol} onClick={() => onSelect(a.symbol)} style={{ display: "flex", alignItems: "center", gap: 8, padding: "6px 10px", borderRadius: 6, cursor: "pointer", border: `1px solid ${border}`, background: bg, marginBottom: 3 }}>
          <div style={{ flex: 1 }}>
            <span style={{ fontWeight: 700, color: "#f1f5f9", fontSize: 11 }}>{a.symbol}</span>
            <span style={{ color: "#334155", fontSize: 9, marginLeft: 5 }}>{a.exchange}</span>
          </div>
          <MiniChart history={a.history} color={color} />
          <span style={{ color, fontWeight: 700, fontSize: 11, minWidth: 52, textAlign: "right" }}>{a.change >= 0 ? "+" : ""}{a.change}%</span>
          <span style={{ background: bg, color, borderRadius: 3, fontSize: 9, padding: "1px 5px", fontWeight: 700 }}>{badge(a)}</span>
        </div>
      ))}
    </div>
  );
  return (
    <div style={{ padding: "10px 12px" }}>
      <Section title="BREAKOUT STOCKS" icon="🚀" color="#f43f5e" border="#1f0712" bg="#0a0306" items={breakouts.slice(0, 10)} badge={() => "BREAKOUT"} />
      <Section title="HIGH MOMENTUM"   icon="⚡" color="#f59e0b" border="#1c1107" bg="#080500" items={momentum}                badge={a => `RSI ${a.rsi.toFixed(0)}`} />
      <Section title="OVERSOLD / BOUNCE" icon="📉" color="#22c55e" border="#052e16" bg="#020c05" items={oversold}             badge={a => `RSI ${a.rsi.toFixed(0)}`} />
    </div>
  );
}

// ── Capital Setup Screen ─────────────────────────────────────────────
function CapitalSetup({ onStart }) {
  const [selected, setSelected] = useState(500000);
  const [custom, setCustom]     = useState("");
  const [useCustom, setUseCustom] = useState(false);

  const finalAmt = useCustom ? (parseInt(custom.replace(/[^0-9]/g, "")) || 0) : selected;

  return (
    <div style={{ minHeight: "100vh", background: "#020617", display: "flex", alignItems: "center", justifyContent: "center", fontFamily: "'JetBrains Mono', monospace" }}>
      <style>{`@import url('https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;600;700&display=swap');*{box-sizing:border-box;margin:0;padding:0;}`}</style>
      <div style={{ width: 440, padding: 24 }}>
        <div style={{ textAlign: "center", marginBottom: 28 }}>
          <div style={{ display: "flex", alignItems: "center", justifyContent: "center", gap: 10, marginBottom: 6 }}>
            <div style={{ width: 10, height: 10, borderRadius: "50%", background: "#22c55e", boxShadow: "0 0 12px #22c55e" }} />
            <span style={{ fontWeight: 700, fontSize: 22, color: "#f8fafc", letterSpacing: ".1em" }}>TRADELAB</span>
          </div>
          <div style={{ color: "#1e293b", fontSize: 11, letterSpacing: ".1em" }}>PAPER TRADING SIMULATOR</div>
        </div>

        <div style={{ background: "#050d1a", border: "1px solid #0f172a", borderRadius: 14, padding: 24 }}>
          <div style={{ color: "#334155", fontSize: 10, fontWeight: 700, letterSpacing: ".12em", marginBottom: 14 }}>CHOOSE YOUR STARTING CAPITAL</div>

          {/* Preset buttons */}
          <div style={{ display: "grid", gridTemplateColumns: "repeat(5, 1fr)", gap: 7, marginBottom: 16 }}>
            {CAPITAL_PRESETS.map(p => (
              <button key={p.value} onClick={() => { setSelected(p.value); setUseCustom(false); }}
                style={{ padding: "9px 4px", border: `1.5px solid ${!useCustom && selected === p.value ? "#22c55e" : "#0f172a"}`, background: !useCustom && selected === p.value ? "#052e16" : "#0a1628", color: !useCustom && selected === p.value ? "#4ade80" : "#475569", borderRadius: 7, cursor: "pointer", fontFamily: "inherit", fontSize: 11, fontWeight: 700, transition: "all .15s" }}>
                {p.label}
              </button>
            ))}
          </div>

          {/* Custom input */}
          <div style={{ marginBottom: 18 }}>
            <div style={{ color: "#1e293b", fontSize: 9, letterSpacing: ".1em", marginBottom: 6 }}>OR ENTER A CUSTOM AMOUNT</div>
            <div style={{ position: "relative" }}>
              <span style={{ position: "absolute", left: 12, top: "50%", transform: "translateY(-50%)", color: "#475569", fontSize: 14, fontWeight: 700 }}>₹</span>
              <input
                style={{ background: "#0a1628", border: `1.5px solid ${useCustom ? "#38bdf8" : "#0f172a"}`, borderRadius: 8, color: "#e2e8f0", fontFamily: "inherit", fontSize: 14, padding: "10px 12px 10px 28px", width: "100%", outline: "none", transition: "border .15s" }}
                placeholder="e.g. 75000"
                value={custom}
                onChange={e => { setCustom(e.target.value); setUseCustom(true); }}
                onFocus={() => setUseCustom(true)}
              />
            </div>
            {useCustom && finalAmt > 0 && (
              <div style={{ color: "#38bdf8", fontSize: 10, marginTop: 5 }}>= ₹{finalAmt.toLocaleString("en-IN")}</div>
            )}
          </div>

          {/* Summary bar */}
          <div style={{ background: "#0a1628", border: "1px solid #0f172a", borderRadius: 8, padding: "11px 16px", marginBottom: 18, display: "flex", justifyContent: "space-between", alignItems: "center" }}>
            <span style={{ color: "#475569", fontSize: 11 }}>Starting Capital</span>
            <span style={{ color: "#22c55e", fontWeight: 700, fontSize: 18 }}>₹{finalAmt.toLocaleString("en-IN")}</span>
          </div>

          <button onClick={() => finalAmt >= 1000 && onStart(finalAmt)}
            style={{ width: "100%", background: finalAmt >= 1000 ? "#16a34a" : "#0f172a", color: finalAmt >= 1000 ? "#fff" : "#334155", border: "none", borderRadius: 8, padding: "13px", fontFamily: "inherit", fontSize: 13, fontWeight: 700, cursor: finalAmt >= 1000 ? "pointer" : "not-allowed", letterSpacing: ".06em", transition: "background .15s" }}>
            START TRADING →
          </button>
          <div style={{ color: "#0f172a", fontSize: 9, textAlign: "center", marginTop: 10 }}>Simulated only — no real money involved.</div>
        </div>
      </div>
    </div>
  );
}

// ── Wallet Modal ──────────────────────────────────────────────────────
function WalletModal({ capital, initialCapital, onAddFunds, onReset, onClose }) {
  const [wTab,    setWTab]    = useState("add");
  const [topUpV,  setTopUpV]  = useState("");
  const [resetV,  setResetV]  = useState(initialCapital);
  const [customR, setCustomR] = useState("");
  const [useCustomR, setUseCustomR] = useState(false);

  const addAmt   = parseInt(topUpV.replace(/[^0-9]/g, "")) || 0;
  const resetAmt = useCustomR ? (parseInt(customR.replace(/[^0-9]/g, "")) || 0) : resetV;

  return (
    <div style={{ position: "fixed", inset: 0, background: "rgba(0,0,0,.8)", zIndex: 2000, display: "flex", alignItems: "center", justifyContent: "center" }} onClick={onClose}>
      <div style={{ background: "#050d1a", border: "1px solid #0f172a", borderRadius: 14, padding: 24, width: 360, fontFamily: "'JetBrains Mono', monospace" }} onClick={e => e.stopPropagation()}>
        <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 16 }}>
          <span style={{ fontWeight: 700, fontSize: 13, color: "#f8fafc" }}>💼 Wallet Manager</span>
          <button onClick={onClose} style={{ background: "none", border: "none", color: "#475569", cursor: "pointer", fontSize: 18, lineHeight: 1 }}>✕</button>
        </div>

        {/* Balance summary */}
        <div style={{ background: "#0a1628", borderRadius: 8, padding: "10px 14px", marginBottom: 16, display: "grid", gridTemplateColumns: "1fr 1fr", gap: 8 }}>
          <div><div style={{ color: "#334155", fontSize: 9 }}>CASH AVAILABLE</div><div style={{ color: "#38bdf8", fontWeight: 700, fontSize: 15, marginTop: 2 }}>₹{fmtN(capital)}</div></div>
          <div><div style={{ color: "#334155", fontSize: 9 }}>INITIAL CAPITAL</div><div style={{ color: "#64748b", fontWeight: 600, fontSize: 13, marginTop: 2 }}>₹{fmtN(initialCapital)}</div></div>
        </div>

        {/* Tab switcher */}
        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 6, marginBottom: 16 }}>
          {["add", "reset"].map(t => (
            <button key={t} onClick={() => setWTab(t)}
              style={{ padding: "8px", border: `1.5px solid ${wTab === t ? "#38bdf8" : "#0f172a"}`, background: wTab === t ? "#0c1a2e" : "#0a1628", color: wTab === t ? "#38bdf8" : "#334155", borderRadius: 7, cursor: "pointer", fontFamily: "inherit", fontSize: 11, fontWeight: 700 }}>
              {t === "add" ? "➕ Add Funds" : "🔄 Reset Capital"}
            </button>
          ))}
        </div>

        {wTab === "add" && (
          <div>
            <div style={{ color: "#1e293b", fontSize: 9, letterSpacing: ".1em", marginBottom: 8 }}>QUICK ADD</div>
            <div style={{ display: "grid", gridTemplateColumns: "repeat(4, 1fr)", gap: 6, marginBottom: 12 }}>
              {[5000, 10000, 25000, 50000, 100000, 250000, 500000, 1000000].map(v => (
                <button key={v} onClick={() => setTopUpV(String(v))}
                  style={{ padding: "7px 3px", background: topUpV === String(v) ? "#0c1a2e" : "#0a1628", border: `1px solid ${topUpV === String(v) ? "#38bdf8" : "#0f172a"}`, color: topUpV === String(v) ? "#38bdf8" : "#475569", borderRadius: 5, cursor: "pointer", fontFamily: "inherit", fontSize: 10, fontWeight: 600 }}>
                  {fmtC(v)}
                </button>
              ))}
            </div>
            <div style={{ position: "relative", marginBottom: 12 }}>
              <span style={{ position: "absolute", left: 10, top: "50%", transform: "translateY(-50%)", color: "#475569", fontWeight: 700 }}>₹</span>
              <input style={{ background: "#0a1628", border: "1px solid #0f172a", borderRadius: 7, color: "#e2e8f0", fontFamily: "inherit", fontSize: 13, padding: "9px 10px 9px 24px", width: "100%", outline: "none" }}
                placeholder="Custom amount" value={topUpV} onChange={e => setTopUpV(e.target.value)} />
            </div>
            {addAmt > 0 && <div style={{ color: "#22c55e", fontSize: 10, marginBottom: 8 }}>Adding ₹{addAmt.toLocaleString("en-IN")} → New balance: ₹{(capital + addAmt).toLocaleString("en-IN")}</div>}
            <button onClick={() => { if (addAmt > 0) { onAddFunds(addAmt); onClose(); } }}
              style={{ width: "100%", background: addAmt > 0 ? "#16a34a" : "#0f172a", color: addAmt > 0 ? "#fff" : "#334155", border: "none", borderRadius: 7, padding: "10px", fontFamily: "inherit", fontSize: 12, fontWeight: 700, cursor: addAmt > 0 ? "pointer" : "not-allowed" }}>
              ADD ₹{addAmt.toLocaleString("en-IN")} TO WALLET
            </button>
          </div>
        )}

        {wTab === "reset" && (
          <div>
            <div style={{ color: "#334155", fontSize: 10, marginBottom: 12, lineHeight: 1.6 }}>Reset clears all trades & positions and restores capital.</div>
            <div style={{ color: "#1e293b", fontSize: 9, letterSpacing: ".1em", marginBottom: 8 }}>SELECT NEW CAPITAL</div>
            <div style={{ display: "grid", gridTemplateColumns: "repeat(5, 1fr)", gap: 6, marginBottom: 12 }}>
              {CAPITAL_PRESETS.map(p => (
                <button key={p.value} onClick={() => { setResetV(p.value); setUseCustomR(false); }}
                  style={{ padding: "7px 4px", background: !useCustomR && resetV === p.value ? "#052e16" : "#0a1628", border: `1px solid ${!useCustomR && resetV === p.value ? "#22c55e" : "#0f172a"}`, color: !useCustomR && resetV === p.value ? "#4ade80" : "#475569", borderRadius: 5, cursor: "pointer", fontFamily: "inherit", fontSize: 10, fontWeight: 600 }}>
                  {p.label}
                </button>
              ))}
            </div>
            <div style={{ position: "relative", marginBottom: 12 }}>
              <span style={{ position: "absolute", left: 10, top: "50%", transform: "translateY(-50%)", color: "#475569", fontWeight: 700 }}>₹</span>
              <input style={{ background: "#0a1628", border: `1px solid ${useCustomR ? "#22c55e" : "#0f172a"}`, borderRadius: 7, color: "#e2e8f0", fontFamily: "inherit", fontSize: 13, padding: "9px 10px 9px 24px", width: "100%", outline: "none" }}
                placeholder="Custom amount" value={customR}
                onChange={e => { setCustomR(e.target.value); setUseCustomR(true); }}
                onFocus={() => setUseCustomR(true)} />
            </div>
            <div style={{ background: "#0a1628", border: "1px solid #0f172a", borderRadius: 6, padding: "8px 12px", marginBottom: 12, display: "flex", justifyContent: "space-between" }}>
              <span style={{ color: "#475569", fontSize: 11 }}>Reset to</span>
              <span style={{ color: "#22c55e", fontWeight: 700 }}>₹{resetAmt.toLocaleString("en-IN")}</span>
            </div>
            <button onClick={() => { if (resetAmt >= 1000) { onReset(resetAmt); onClose(); } }}
              style={{ width: "100%", background: "#dc2626", color: "#fff", border: "none", borderRadius: 7, padding: "10px", fontFamily: "inherit", fontSize: 12, fontWeight: 700, cursor: "pointer" }}>
              🔄 RESET TO ₹{resetAmt.toLocaleString("en-IN")}
            </button>
          </div>
        )}
      </div>
    </div>
  );
}

// ── MAIN APP ─────────────────────────────────────────────────────────
export default function TradingApp() {
  const [started,        setStarted]        = useState(false);
  const [initialCapital, setInitialCapital] = useState(500000);
  const [capital,        setCapital]        = useState(500000);
  const [market,         setMarket]         = useState(() => initMarket());
  const [portfolio,      setPortfolio]      = useState({});
  const [trades,         setTrades]         = useState([]);
  const [tab,            setTab]            = useState("market");
  const [selectedSym,    setSelectedSym]    = useState("RELIANCE");
  const [tradingMode,    setTradingMode]    = useState("INTRADAY");
  const [orderType,      setOrderType]      = useState("BUY");

  // ── Investment input state ────────────────────────────────────────
  const [iMode,     setIMode]     = useState("qty");    // "qty" | "amount" | "pct"
  const [qtyVal,    setQtyVal]    = useState("");
  const [amtVal,    setAmtVal]    = useState("");
  const [pctVal,    setPctVal]    = useState(10);       // % of capital

  const [filter,     setFilter]     = useState("ALL");
  const [searchQ,    setSearchQ]    = useState("");
  const [autoTrade,  setAutoTrade]  = useState(false);
  const [botItems,   setBotItems]   = useState([]);
  const [botScanMap, setBotScanMap] = useState({});
  const [botStatus,  setBotStatus]  = useState("IDLE");
  const [showWallet, setShowWallet] = useState(false);
  const [toast,      setToast]      = useState(null);

  const stateRef = useRef({ market, capital, portfolio, tradingMode });
  useEffect(() => { stateRef.current = { market, capital, portfolio, tradingMode }; }, [market, capital, portfolio, tradingMode]);

  // market tick
  useEffect(() => {
    const id = setInterval(() => setMarket(p => tickMarket(p)), 2000);
    return () => clearInterval(id);
  }, []);

  // bot scan visual
  useEffect(() => {
    if (!autoTrade) { setBotScanMap({}); setBotStatus("IDLE"); return; }
    setBotStatus("SCANNING");
    const id = setInterval(() => {
      const syms = Object.keys(stateRef.current.market);
      const s = {};
      for (let i = 0; i < 5; i++) s[syms[Math.floor(Math.random() * syms.length)]] = "SCAN";
      setBotScanMap(s);
      setTimeout(() => setBotScanMap({}), 1400);
    }, 3000);
    return () => clearInterval(id);
  }, [autoTrade]);

  // bot trading
  useEffect(() => {
    if (!autoTrade) return;
    const id = setInterval(() => {
      const { market: mkt, capital: cap, portfolio: port, tradingMode: mode } = stateRef.current;
      let nc = cap; const np = { ...port }, nt = [], na = [], sh = {};
      setBotStatus("TRADING");
      Object.entries(np).filter(([sym]) => { const a = mkt[sym]; return a && (getModeSignal(mode, a) === "SELL" || a.rsi > 74); }).slice(0, 2)
        .forEach(([sym, h]) => {
          const a = mkt[sym]; if (!a) return;
          const proceeds = h.qty * a.price, pnl = proceeds - h.qty * h.avgPrice;
          nc += proceeds; sh[sym] = "SELL";
          nt.push({ id: Date.now() + Math.random(), symbol: sym, type: "SELL", qty: h.qty, price: a.price, pnl, time: new Date().toLocaleTimeString(), auto: true, mode });
          na.push({ id: Date.now() + Math.random(), symbol: sym, type: "SELL", mode, fade: false });
          delete np[sym];
        });
      Object.values(mkt).filter(a => getModeSignal(mode, a) === "BUY" && !np[a.symbol]).sort((a, b) => b.change - a.change).slice(0, 3)
        .forEach(a => {
          if (nc < 2000) return;
          const budget = Math.min(nc * 0.06, nc - 500);
          const bq = a.exchange === "CRYPTO" ? +(budget / a.price).toFixed(a.basePrice < 1 ? 2 : 4) : Math.floor(budget / a.price);
          if (bq <= 0) return;
          nc -= bq * a.price; np[a.symbol] = { qty: bq, avgPrice: a.price, mode, targets: calcTargets(a.price, mode) }; sh[a.symbol] = "BUY";
          nt.push({ id: Date.now() + Math.random(), symbol: a.symbol, type: "BUY", qty: bq, price: a.price, pnl: null, time: new Date().toLocaleTimeString(), auto: true, mode });
          na.push({ id: Date.now() + Math.random(), symbol: a.symbol, type: "BUY", mode, fade: false });
        });
      if (nt.length > 0) {
        setCapital(nc); setPortfolio(np); setTrades(p => [...nt, ...p]);
        setBotScanMap(sh); setBotItems(p => [...na, ...p].slice(0, 5));
        setTimeout(() => { setBotScanMap({}); setBotStatus("SCANNING"); setBotItems(p => p.map(a => ({ ...a, fade: true }))); setTimeout(() => setBotItems(p => p.filter(a => !a.fade)), 1500); }, 2500);
      } else setBotStatus("SCANNING");
    }, 8000);
    return () => clearInterval(id);
  }, [autoTrade]);

  const showToast = (msg, type = "success") => { setToast({ msg, type }); setTimeout(() => setToast(null), 2800); };

  // ── Derived quantities ────────────────────────────────────────────
  const asset = market[selectedSym];
  const derivedQtyFromAmt = asset && amtVal && parseFloat(amtVal) > 0
    ? (asset.exchange === "CRYPTO"
        ? Math.max(0.0001, +(parseFloat(amtVal) / asset.price).toFixed(asset.basePrice < 1 ? 2 : 4))
        : Math.floor(parseFloat(amtVal) / asset.price))
    : 0;
  const derivedQtyFromPct = asset
    ? (asset.exchange === "CRYPTO"
        ? Math.max(0.0001, +((capital * pctVal / 100) / asset.price).toFixed(asset.basePrice < 1 ? 2 : 4))
        : Math.floor((capital * pctVal / 100) / asset.price))
    : 0;
  const activeQty =
    iMode === "qty"    ? (parseFloat(qtyVal) || 0) :
    iMode === "amount" ? derivedQtyFromAmt :
    derivedQtyFromPct;
  const activeCost = activeQty > 0 && asset ? activeQty * asset.price : 0;

  const portfolioValue = Object.entries(portfolio).reduce((s, [sym, h]) => s + (market[sym] ? h.qty * market[sym].price : 0), 0);
  const totalValue  = capital + portfolioValue;
  const totalPnL    = totalValue - initialCapital;
  const totalPnLPct = ((totalPnL / initialCapital) * 100).toFixed(2);

  const filteredAssets = Object.values(market).filter(a => {
    const ex = filter === "ALL" || a.exchange === filter;
    const sr = !searchQ || a.symbol.toLowerCase().includes(searchQ.toLowerCase()) || a.name.toLowerCase().includes(searchQ.toLowerCase());
    return ex && sr;
  });

  const modeTargets = asset ? calcTargets(asset.price, tradingMode) : null;
  const modeSignal  = asset ? getModeSignal(tradingMode, asset) : "HOLD";
  const modeCfg     = TRADING_MODES[tradingMode];
  const sigColor    = { BUY:"#22c55e", SELL:"#ef4444", HOLD:"#f59e0b", WATCH:"#38bdf8" };
  const sigBg       = { BUY:"#052e16", SELL:"#2d1515", HOLD:"#1c1107", WATCH:"#0c1a2e" };

  // ── Execute manual trade ──────────────────────────────────────────
  const handleTrade = () => {
    const a = market[selectedSym]; const q = activeQty;
    if (!q || q <= 0) return showToast("Qty is 0 — enter amount or quantity", "error");
    if (orderType === "BUY") {
      const cost = q * a.price;
      if (cost > capital) return showToast("Insufficient balance!", "error");
      setCapital(c => c - cost);
      setPortfolio(p => {
        const ex = p[selectedSym];
        if (ex) { const tq = ex.qty + q; return { ...p, [selectedSym]: { qty: tq, avgPrice: (ex.qty * ex.avgPrice + cost) / tq, mode: tradingMode, targets: calcTargets(a.price, tradingMode) } }; }
        return { ...p, [selectedSym]: { qty: q, avgPrice: a.price, mode: tradingMode, targets: calcTargets(a.price, tradingMode) } };
      });
      setTrades(t => [{ id: Date.now(), symbol: selectedSym, type: "BUY", qty: q, price: a.price, pnl: null, time: new Date().toLocaleTimeString(), auto: false, mode: tradingMode }, ...t]);
      showToast(`${modeCfg.icon} Bought ${q} ${selectedSym} @ ₹${fmtP(a.price)} [${modeCfg.label}]`);
    } else {
      const h = portfolio[selectedSym];
      if (!h || h.qty < q) return showToast("Insufficient holdings!", "error");
      const proceeds = q * a.price, pnl = proceeds - q * h.avgPrice;
      setCapital(c => c + proceeds);
      setPortfolio(p => { const rem = h.qty - q; if (rem < 0.00001) { const np = { ...p }; delete np[selectedSym]; return np; } return { ...p, [selectedSym]: { ...h, qty: rem } }; });
      setTrades(t => [{ id: Date.now(), symbol: selectedSym, type: "SELL", qty: q, price: a.price, pnl, time: new Date().toLocaleTimeString(), auto: false, mode: tradingMode }, ...t]);
      showToast(`Sold ${q} ${selectedSym} — P&L: ${pnl >= 0 ? "+" : ""}₹${pnl.toFixed(2)}`);
    }
    setQtyVal(""); setAmtVal("");
  };

  // ── Setup screen ──────────────────────────────────────────────────
  if (!started) return <CapitalSetup onStart={amt => { setInitialCapital(amt); setCapital(amt); setStarted(true); }} />;

  return (
    <>
      {showWallet && (
        <WalletModal
          capital={capital}
          initialCapital={initialCapital}
          onAddFunds={v => { setCapital(c => c + v); showToast(`Added ₹${v.toLocaleString("en-IN")} to wallet`); }}
          onReset={v => { setCapital(v); setInitialCapital(v); setPortfolio({}); setTrades([]); showToast(`Reset to ₹${v.toLocaleString("en-IN")}`); }}
          onClose={() => setShowWallet(false)}
        />
      )}

      <div style={{ fontFamily: "'JetBrains Mono', monospace", background: "#020617", color: "#e2e8f0", minHeight: "100vh", fontSize: 12 }}>
        <style>{`
          @import url('https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;600;700&display=swap');
          *{box-sizing:border-box;margin:0;padding:0;}
          ::-webkit-scrollbar{width:4px;height:4px;}
          ::-webkit-scrollbar-track{background:#0a0f1e;}
          ::-webkit-scrollbar-thumb{background:#1e293b;border-radius:4px;}
          .tb{background:none;border:none;color:#334155;cursor:pointer;padding:7px 11px;font-family:inherit;font-size:10px;font-weight:700;letter-spacing:.1em;text-transform:uppercase;border-bottom:2px solid transparent;transition:all .15s;}
          .tb.on{color:#38bdf8;border-bottom-color:#38bdf8;}
          .ar{display:flex;align-items:center;gap:7px;padding:5px 9px;cursor:pointer;border-bottom:1px solid #040810;transition:all .1s;}
          .ar:hover,.ar.sel{background:#0a1628;}
          .pill{padding:1px 5px;border-radius:99px;font-size:9px;font-weight:700;}
          .bdg{display:inline-block;padding:1px 5px;border-radius:3px;font-size:9px;font-weight:700;}
          .ifield{background:#0a1628;border:1px solid #0f172a;border-radius:6px;color:#e2e8f0;font-family:inherit;font-size:12px;padding:7px 10px;width:100%;outline:none;transition:border .15s;}
          .ifield:focus{border-color:#38bdf8;}
          .sc{background:#0a1628;border-radius:6px;padding:8px 11px;border:1px solid #0f172a;}
          .toast{position:fixed;bottom:20px;left:50%;transform:translateX(-50%);padding:8px 16px;border-radius:8px;font-size:11px;font-weight:600;z-index:9999;animation:su .2s ease;box-shadow:0 8px 24px rgba(0,0,0,.6);white-space:nowrap;max-width:360px;text-align:center;}
          @keyframes su{from{opacity:0;transform:translateX(-50%) translateY(8px)}to{opacity:1;transform:translateX(-50%) translateY(0)}}
          @keyframes pulse{0%,100%{opacity:1}50%{opacity:.4}}
          @keyframes botpop{from{opacity:0;transform:translateX(10px)}to{opacity:1;transform:translateX(0)}}
          .ob{padding:7px;border-radius:6px;background:#0a1628;font-family:inherit;font-size:11px;font-weight:700;cursor:pointer;letter-spacing:.05em;flex:1;transition:all .15s;}
          .abtn{border:none;border-radius:7px;padding:11px;font-family:inherit;font-size:12px;font-weight:700;cursor:pointer;width:100%;letter-spacing:.04em;transition:all .15s;}
          .fb{background:#0a1628;border:1px solid #0f172a;color:#334155;padding:2px 8px;border-radius:4px;cursor:pointer;font-family:inherit;font-size:10px;font-weight:600;}
          .fb.on{background:#1e293b;color:#e2e8f0;border-color:#334155;}
          .bpulse{animation:pulse 1.2s infinite;}
          .scan-row{outline:1px solid #1d4ed880;}
          .buy-row{outline:1px solid #16a34a80;background:#020c05 !important;}
          .sell-row{outline:1px solid #dc262680;background:#0f0404 !important;}
          .mode-strip{display:flex;gap:5px;padding:7px 12px;background:#030a18;border-bottom:1px solid #06091a;overflow-x:auto;align-items:center;}
          .mode-btn{padding:5px 9px;border-radius:6px;border:1.5px solid #0f172a;background:#030a18;cursor:pointer;font-family:inherit;font-size:10px;font-weight:700;letter-spacing:.05em;transition:all .15s;white-space:nowrap;display:flex;align-items:center;gap:4px;}
          .imode-btn{padding:5px 9px;border-radius:5px;border:1px solid #0f172a;background:#0a1628;cursor:pointer;font-family:inherit;font-size:10px;font-weight:700;transition:all .15s;flex:1;text-align:center;}
          .imode-btn.active{border-color:#38bdf8;background:#0c1a2e;color:#38bdf8;}
          .pct-btn{padding:4px 6px;border-radius:4px;border:1px solid #0f172a;background:#0a1628;color:#334155;cursor:pointer;font-family:inherit;font-size:10px;font-weight:700;flex:1;transition:all .12s;}
          .pct-btn:hover{border-color:#38bdf8;color:#38bdf8;}
        `}</style>

        {/* ── HEADER ── */}
        <div style={{ background: "#030a18", borderBottom: "1px solid #06091a", padding: "8px 14px", display: "flex", alignItems: "center", justifyContent: "space-between", flexWrap: "wrap", gap: 8 }}>
          <div style={{ display: "flex", alignItems: "center", gap: 10 }}>
            <div style={{ width: 6, height: 6, borderRadius: "50%", background: "#22c55e", boxShadow: "0 0 8px #22c55e" }} />
            <span style={{ fontWeight: 700, fontSize: 13, color: "#f8fafc", letterSpacing: ".07em" }}>TRADELAB</span>
            <span style={{ color: "#0f172a", fontSize: 10 }}>|</span>
            <span style={{ color: "#1e293b", fontSize: 10 }}>{ALL_ASSETS.length} ASSETS</span>
          </div>
          {autoTrade && (
            <div style={{ display: "flex", alignItems: "center", gap: 6, background: botStatus === "TRADING" ? "#052e16" : "#0c1a2e", border: `1px solid ${botStatus === "TRADING" ? "#16a34a" : "#1d4ed8"}`, borderRadius: 6, padding: "3px 10px" }}>
              <div className="bpulse" style={{ width: 5, height: 5, borderRadius: "50%", background: botStatus === "TRADING" ? "#22c55e" : "#60a5fa" }} />
              <span style={{ fontSize: 9, fontWeight: 700, color: botStatus === "TRADING" ? "#4ade80" : "#93c5fd" }}>BOT {botStatus} · {modeCfg.short}</span>
            </div>
          )}
          <div style={{ display: "flex", gap: 12, alignItems: "center" }}>
            {/* Wallet button */}
            <button onClick={() => setShowWallet(true)} style={{ background: "#0a1628", border: "1px solid #0f172a", color: "#38bdf8", borderRadius: 6, padding: "4px 10px", fontFamily: "inherit", fontSize: 10, fontWeight: 700, cursor: "pointer", display: "flex", alignItems: "center", gap: 5 }}>
              💼 <span>₹{fmtN(capital)}</span>
            </button>
            <div style={{ textAlign: "right" }}>
              <div style={{ color: "#0f172a", fontSize: 9 }}>TOTAL</div>
              <div style={{ color: "#f8fafc", fontWeight: 700, fontSize: 13 }}>₹{fmtN(totalValue)}</div>
            </div>
            <div style={{ textAlign: "right" }}>
              <div style={{ color: "#0f172a", fontSize: 9 }}>P&L</div>
              <div style={{ color: totalPnL >= 0 ? "#22c55e" : "#ef4444", fontWeight: 700, fontSize: 13 }}>
                {totalPnL >= 0 ? "+" : ""}₹{fmtN(totalPnL)} <span style={{ fontSize: 9, opacity: .6 }}>({totalPnLPct}%)</span>
              </div>
            </div>
            <div style={{ display: "flex", alignItems: "center", gap: 5, cursor: "pointer" }} onClick={() => setAutoTrade(v => !v)}>
              <div style={{ width: 28, height: 15, borderRadius: 99, background: autoTrade ? "#16a34a" : "#1e293b", position: "relative", transition: "background .2s" }}>
                <div style={{ width: 9, height: 9, borderRadius: "50%", background: "#fff", position: "absolute", top: 3, left: autoTrade ? 16 : 3, transition: "left .2s" }} />
              </div>
              <span style={{ fontSize: 10, fontWeight: 700, color: autoTrade ? "#22c55e" : "#334155" }}>AUTO</span>
            </div>
          </div>
        </div>

        {/* ── MODE STRIP ── */}
        <div className="mode-strip">
          <span style={{ color: "#1e293b", fontSize: 9, fontWeight: 700, letterSpacing: ".1em", whiteSpace: "nowrap", marginRight: 4 }}>MODE:</span>
          {Object.keys(TRADING_MODES).map(k => {
            const m = TRADING_MODES[k], active = tradingMode === k;
            return (
              <button key={k} className="mode-btn" onClick={() => setTradingMode(k)}
                style={{ borderColor: active ? m.color : "#0f172a", background: active ? m.bg : "#030a18", color: active ? m.color : "#334155" }}>
                {m.icon}{m.label}
              </button>
            );
          })}
          <div style={{ marginLeft: 8, color: modeCfg.color, fontSize: 9, borderLeft: "1px solid #0f172a", paddingLeft: 10, whiteSpace: "nowrap" }}>
            SL {modeCfg.stopLossPct}% · T1 {modeCfg.target1}% · T2 {modeCfg.target2}% · T3 {modeCfg.target3}% <span style={{ color: "#1e293b" }}>· {modeCfg.desc}</span>
          </div>
        </div>

        {/* ── TABS ── */}
        <div style={{ display: "flex", borderBottom: "1px solid #06091a", padding: "0 14px", background: "#030a18", alignItems: "center" }}>
          {["market", "trade", "breakout", "portfolio", "history"].map(t => (
            <button key={t} className={`tb ${tab === t ? "on" : ""}`} onClick={() => setTab(t)}>
              {t === "breakout" ? "🚀 breakout" : t}
            </button>
          ))}
          <div style={{ marginLeft: "auto", paddingRight: 4 }}>
            <input className="ifield" style={{ width: 130, padding: "3px 8px", fontSize: 11 }} placeholder="Search…" value={searchQ} onChange={e => setSearchQ(e.target.value)} />
          </div>
        </div>

        <div style={{ display: "grid", gridTemplateColumns: "1fr 270px", height: "calc(100vh - 107px)" }}>

          {/* ── LEFT PANEL ── */}
          <div style={{ overflowY: "auto", padding: "10px 12px" }}>

            {/* MARKET */}
            {tab === "market" && (
              <div>
                <div style={{ display: "flex", gap: 5, marginBottom: 8, flexWrap: "wrap", alignItems: "center" }}>
                  {["ALL", "NSE", "BSE", "CRYPTO"].map(f => <button key={f} className={`fb ${filter === f ? "on" : ""}`} onClick={() => setFilter(f)}>{f}</button>)}
                  <span style={{ color: "#1e293b", fontSize: 9, marginLeft: 4 }}>{filteredAssets.length} assets · <span style={{ color: modeCfg.color }}>{modeCfg.label}</span></span>
                </div>
                <div style={{ display: "flex", gap: 6, padding: "2px 9px", color: "#0f172a", fontSize: 9, fontWeight: 700, letterSpacing: ".08em", borderBottom: "1px solid #040810", marginBottom: 1 }}>
                  <div style={{ width: 86 }}>SYMBOL</div><div style={{ width: 44 }}>EXCH</div><div style={{ flex: 1 }}>CHART</div>
                  <div style={{ width: 96, textAlign: "right" }}>PRICE</div><div style={{ width: 72, textAlign: "center" }}>SIG</div><div style={{ width: 56, textAlign: "center" }}>BOT</div>
                </div>
                {filteredAssets.map(a => {
                  const ba = botScanMap[a.symbol], ms = getModeSignal(tradingMode, a);
                  const rc = ba === "BUY" ? "buy-row" : ba === "SELL" ? "sell-row" : ba === "SCAN" ? "scan-row" : "";
                  return (
                    <div key={a.symbol} className={`ar ${selectedSym === a.symbol ? "sel" : ""} ${rc}`} onClick={() => { setSelectedSym(a.symbol); setTab("trade"); }}>
                      <div style={{ width: 86 }}>
                        <div style={{ fontWeight: 700, fontSize: 11, color: "#f1f5f9", display: "flex", alignItems: "center", gap: 3 }}>{a.symbol}{a.brk && <span style={{ fontSize: 8, color: "#f43f5e" }}>🚀</span>}</div>
                        <div style={{ color: "#0f172a", fontSize: 9, overflow: "hidden", textOverflow: "ellipsis", whiteSpace: "nowrap", maxWidth: 86 }}>{a.sector}</div>
                      </div>
                      <span className="pill" style={{ width: 44, textAlign: "center", background: a.exchange === "CRYPTO" ? "#1e1b4b" : a.exchange === "BSE" ? "#1a0f2e" : "#0c1a2e", color: a.exchange === "CRYPTO" ? "#818cf8" : a.exchange === "BSE" ? "#c084fc" : "#38bdf8" }}>{a.exchange}</span>
                      <div style={{ flex: 1 }}><MiniChart history={a.history} color={a.change >= 0 ? "#22c55e" : "#ef4444"} /></div>
                      <div style={{ width: 96, textAlign: "right" }}>
                        <div style={{ fontWeight: 700, color: "#f1f5f9", fontSize: 11 }}>₹{fmtP(a.price)}</div>
                        <div style={{ color: a.change >= 0 ? "#22c55e" : "#ef4444", fontSize: 9, fontWeight: 600 }}>{a.change >= 0 ? "▲" : "▼"} {Math.abs(a.change)}%</div>
                      </div>
                      <div style={{ width: 72, textAlign: "center" }}>
                        <span className="bdg" style={{ background: sigBg[ms] || "#111", color: sigColor[ms] || "#94a3b8" }}>
                          {ms === "BUY" ? "▲ BUY" : ms === "SELL" ? "▼ SELL" : ms === "WATCH" ? "◎ WTCH" : "● HOLD"}
                        </span>
                      </div>
                      <div style={{ width: 56, textAlign: "center", fontSize: 9, fontWeight: 700, animation: ba ? "pulse 1s infinite" : "none", color: ba === "BUY" ? "#4ade80" : ba === "SELL" ? "#f87171" : ba === "SCAN" ? "#93c5fd" : "transparent" }}>
                        {ba === "BUY" ? "● BUY" : ba === "SELL" ? "● SELL" : ba === "SCAN" ? "◌ SCAN" : "·"}
                      </div>
                    </div>
                  );
                })}
              </div>
            )}

            {/* TRADE */}
            {tab === "trade" && asset && (
              <div>
                <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start", marginBottom: 10 }}>
                  <div>
                    <div style={{ fontSize: 15, fontWeight: 700, color: "#f8fafc" }}>{asset.symbol}<span style={{ marginLeft: 6, fontSize: 10, color: "#334155", fontWeight: 400 }}>{asset.name}</span></div>
                    <div style={{ display: "flex", gap: 4, marginTop: 4, flexWrap: "wrap" }}>
                      <span className="pill" style={{ background: asset.exchange === "CRYPTO" ? "#1e1b4b" : asset.exchange === "BSE" ? "#1a0f2e" : "#0c1a2e", color: asset.exchange === "CRYPTO" ? "#818cf8" : asset.exchange === "BSE" ? "#c084fc" : "#38bdf8" }}>{asset.exchange}</span>
                      <span className="bdg" style={{ background: sigBg[modeSignal], color: sigColor[modeSignal] }}>{modeSignal === "BUY" ? "▲ BUY" : modeSignal === "SELL" ? "▼ SELL" : modeSignal === "WATCH" ? "◎ WATCH" : "● HOLD"}</span>
                      <span className="bdg" style={{ background: asset.trend === "BULLISH" ? "#052e16" : "#2d1515", color: asset.trend === "BULLISH" ? "#4ade80" : "#f87171" }}>{asset.trend}</span>
                      {asset.brk && <span className="bdg" style={{ background: "#1f0712", color: "#f43f5e" }}>🚀 BREAKOUT</span>}
                    </div>
                  </div>
                  <div style={{ textAlign: "right" }}>
                    <div style={{ fontSize: 17, fontWeight: 700, color: "#f8fafc" }}>₹{fmtP(asset.price)}</div>
                    <div style={{ color: asset.change >= 0 ? "#22c55e" : "#ef4444", fontWeight: 600, fontSize: 11 }}>{asset.change >= 0 ? "▲ +" : "▼ "}{asset.change}%</div>
                  </div>
                </div>
                <div style={{ borderRadius: 8, overflow: "hidden", border: "1px solid #0a1628", marginBottom: 10 }}>
                  <CandleChart history={asset.history} modeColor={modeCfg.color} />
                </div>
                <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr 1fr 1fr", gap: 5, marginBottom: 10 }}>
                  {[["RSI", asset.rsi?.toFixed(1), asset.rsi > 70 ? "#ef4444" : asset.rsi < 30 ? "#22c55e" : "#94a3b8"],["MACD", asset.macd?.toFixed(2), asset.macd > 0 ? "#22c55e" : "#ef4444"],["EMA10", fmtP(asset.e10), asset.price > asset.e10 ? "#22c55e" : "#ef4444"],["EMA20", fmtP(asset.e20), asset.price > asset.e20 ? "#22c55e" : "#ef4444"]].map(([l,v,c]) => (
                    <div key={l} className="sc"><div style={{ color: "#0f172a", fontSize: 9 }}>{l}</div><div style={{ color: c, fontWeight: 700, fontSize: 11, marginTop: 2 }}>{v}</div></div>
                  ))}
                </div>
                <div style={{ background: modeCfg.bg, border: `1px solid ${modeCfg.color}33`, borderRadius: 8, padding: "10px 14px", marginBottom: 10 }}>
                  <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 7 }}>
                    <span style={{ color: modeCfg.color, fontWeight: 700, fontSize: 11 }}>{modeCfg.icon} {modeCfg.label}</span>
                    <span style={{ fontSize: 9, color: modeCfg.color + "88" }}>{modeCfg.holdTime} · {modeCfg.riskLevel}</span>
                  </div>
                  <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr 1fr 1fr", gap: 5 }}>
                    {[["SL", `₹${fmtP(modeTargets.sl)}`, "#ef4444", `-${modeCfg.stopLossPct}%`],["T1", `₹${fmtP(modeTargets.t1)}`, "#4ade80", `+${modeCfg.target1}%`],["T2", `₹${fmtP(modeTargets.t2)}`, "#22c55e", `+${modeCfg.target2}%`],["T3", `₹${fmtP(modeTargets.t3)}`, "#16a34a", `+${modeCfg.target3}%`]].map(([l,v,c,p]) => (
                      <div key={l} style={{ background: "#020617", borderRadius: 5, padding: "6px 8px", border: "1px solid #0f172a" }}>
                        <div style={{ color: "#334155", fontSize: 8 }}>{l}</div>
                        <div style={{ color: c, fontWeight: 700, fontSize: 10, marginTop: 2 }}>{v}</div>
                        <div style={{ color: c + "88", fontSize: 8 }}>{p}</div>
                      </div>
                    ))}
                  </div>
                </div>
                {portfolio[selectedSym] && (
                  <div className="sc">
                    <span style={{ color: "#1e293b", fontSize: 9 }}>POSITION · </span>
                    <span style={{ color: "#64748b", fontSize: 10 }}>Qty: {portfolio[selectedSym].qty} · Avg: ₹{fmtP(portfolio[selectedSym].avgPrice)}</span>
                    {portfolio[selectedSym].mode && <span style={{ marginLeft: 6, fontSize: 9, color: TRADING_MODES[portfolio[selectedSym].mode]?.color }}>[{TRADING_MODES[portfolio[selectedSym].mode]?.label}]</span>}
                    <span style={{ color: asset.price > portfolio[selectedSym].avgPrice ? "#22c55e" : "#ef4444", fontSize: 10, marginLeft: 8, fontWeight: 600 }}>
                      {asset.price > portfolio[selectedSym].avgPrice ? "▲" : "▼"} {(((asset.price - portfolio[selectedSym].avgPrice) / portfolio[selectedSym].avgPrice) * 100).toFixed(2)}%
                    </span>
                  </div>
                )}
              </div>
            )}

            {/* BREAKOUT */}
            {tab === "breakout" && <BreakoutScanner market={market} onSelect={sym => { setSelectedSym(sym); setTradingMode("BREAKOUT"); setTab("trade"); }} />}

            {/* PORTFOLIO */}
            {tab === "portfolio" && (
              <div>
                <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 7, marginBottom: 12 }}>
                  {[["Cash", `₹${fmtN(capital)}`, "#38bdf8"],["Invested", `₹${fmtN(portfolioValue)}`, "#a78bfa"],["Total", `₹${fmtN(totalValue)}`, "#f8fafc"],["P&L", `${totalPnL >= 0 ? "+" : ""}₹${fmtN(totalPnL)}`, totalPnL >= 0 ? "#22c55e" : "#ef4444"]].map(([l,v,c]) => (
                    <div key={l} className="sc"><div style={{ color: "#0f172a", fontSize: 9 }}>{l}</div><div style={{ color: c, fontWeight: 700, fontSize: 13, marginTop: 2 }}>{v}</div></div>
                  ))}
                </div>
                <div style={{ color: "#0f172a", fontSize: 9, letterSpacing: ".1em", marginBottom: 6 }}>POSITIONS ({Object.keys(portfolio).length})</div>
                {Object.keys(portfolio).length === 0 ? <div style={{ color: "#0f172a", textAlign: "center", padding: "24px 0" }}>No open positions</div>
                  : Object.entries(portfolio).map(([sym, h]) => {
                    const a = market[sym]; if (!a) return null;
                    const cv = h.qty * a.price, cost = h.qty * h.avgPrice, pnl = cv - cost, pct = ((pnl / cost) * 100).toFixed(2);
                    const hm = h.mode ? TRADING_MODES[h.mode] : null;
                    return (
                      <div key={sym} className="sc" style={{ marginBottom: 4, cursor: "pointer" }} onClick={() => { setSelectedSym(sym); setTab("trade"); }}>
                        <div style={{ display: "flex", justifyContent: "space-between" }}>
                          <div><span style={{ fontWeight: 700, color: "#f1f5f9", fontSize: 12 }}>{sym}</span>{hm && <span style={{ marginLeft: 5, fontSize: 9, color: hm.color, background: hm.bg, padding: "1px 5px", borderRadius: 3, fontWeight: 700 }}>{hm.icon}{hm.label}</span>}</div>
                          <div style={{ textAlign: "right" }}><div style={{ fontWeight: 700, fontSize: 12 }}>₹{fmtN(cv)}</div><div style={{ color: pnl >= 0 ? "#22c55e" : "#ef4444", fontSize: 10, fontWeight: 600 }}>{pnl >= 0 ? "+" : ""}₹{fmtN(pnl)} ({pct}%)</div></div>
                        </div>
                        <div style={{ color: "#0f172a", fontSize: 9, marginTop: 3 }}>Qty: {h.qty} · Avg: ₹{fmtP(h.avgPrice)}</div>
                        {h.targets && (
                          <div style={{ display: "flex", gap: 4, marginTop: 5 }}>
                            {[["SL", h.targets.sl, "#f87171","#2d1515"],["T1", h.targets.t1, "#4ade80","#052e16"],["T2", h.targets.t2, "#22c55e","#052e16"],["T3", h.targets.t3, "#16a34a","#052e16"]].map(([l,v,c,bg]) => (
                              <span key={l} style={{ fontSize: 8, color: c, background: bg, padding: "1px 4px", borderRadius: 2 }}>{l} ₹{fmtP(v)}</span>
                            ))}
                          </div>
                        )}
                      </div>
                    );
                  })
                }
              </div>
            )}

            {/* HISTORY */}
            {tab === "history" && (
              <div>
                <div style={{ color: "#0f172a", fontSize: 9, letterSpacing: ".1em", marginBottom: 6 }}>TRADE HISTORY ({trades.length})</div>
                {trades.length === 0 ? <div style={{ color: "#0f172a", textAlign: "center", padding: "24px 0" }}>No trades yet</div>
                  : trades.map(t => {
                    const hm = t.mode ? TRADING_MODES[t.mode] : null;
                    return (
                      <div key={t.id} className="sc" style={{ marginBottom: 3, display: "flex", justifyContent: "space-between", alignItems: "center" }}>
                        <div style={{ display: "flex", gap: 5, alignItems: "center", flexWrap: "wrap" }}>
                          <span className="bdg" style={{ background: t.type === "BUY" ? "#052e16" : "#2d1515", color: t.type === "BUY" ? "#4ade80" : "#f87171" }}>{t.type}</span>
                          <span style={{ fontWeight: 700, color: "#f1f5f9", fontSize: 11 }}>{t.symbol}</span>
                          <span style={{ color: "#1e293b", fontSize: 10 }}>×{t.qty}</span>
                          {hm && <span style={{ fontSize: 8, color: hm.color, background: hm.bg, padding: "1px 4px", borderRadius: 2, fontWeight: 700 }}>{hm.icon}{hm.short}</span>}
                          {t.auto && <span className="bdg" style={{ background: "#1e1b4b", color: "#818cf8" }}>BOT</span>}
                        </div>
                        <div style={{ textAlign: "right" }}>
                          <div style={{ color: "#475569", fontSize: 10 }}>₹{fmtP(t.price)}</div>
                          {t.pnl !== null && <div style={{ color: t.pnl >= 0 ? "#22c55e" : "#ef4444", fontSize: 10, fontWeight: 600 }}>{t.pnl >= 0 ? "+" : ""}₹{fmtN(t.pnl)}</div>}
                          <div style={{ color: "#0f172a", fontSize: 9 }}>{t.time}</div>
                        </div>
                      </div>
                    );
                  })
                }
              </div>
            )}
          </div>

          {/* ── RIGHT: ORDER PANEL ── */}
          <div style={{ background: "#030a18", borderLeft: "1px solid #06091a", padding: "10px 12px", display: "flex", flexDirection: "column", gap: 8, overflowY: "auto" }}>

            {/* Active mode badge */}
            <div style={{ background: modeCfg.bg, border: `1px solid ${modeCfg.color}44`, borderRadius: 7, padding: "7px 10px" }}>
              <div style={{ display: "flex", justifyContent: "space-between" }}>
                <span style={{ color: modeCfg.color, fontWeight: 700, fontSize: 11 }}>{modeCfg.icon} {modeCfg.label}</span>
                <span style={{ fontSize: 9, color: modeCfg.color + "77" }}>{modeCfg.riskLevel}</span>
              </div>
              <div style={{ display: "flex", gap: 6, marginTop: 4 }}>
                <span style={{ fontSize: 8, color: "#ef4444" }}>SL {modeCfg.stopLossPct}%</span>
                <span style={{ fontSize: 8, color: "#4ade80" }}>T1 {modeCfg.target1}%</span>
                <span style={{ fontSize: 8, color: "#22c55e" }}>T2 {modeCfg.target2}%</span>
                <span style={{ fontSize: 8, color: "#16a34a" }}>T3 {modeCfg.target3}%</span>
              </div>
            </div>

            {/* Asset selector */}
            <div>
              <div style={{ color: "#0f172a", fontSize: 9, marginBottom: 3 }}>ASSET</div>
              <select className="ifield" value={selectedSym} onChange={e => setSelectedSym(e.target.value)}
                style={{ background: "#0a1628", border: "1px solid #0f172a", borderRadius: 6, color: "#e2e8f0", fontFamily: "inherit", fontSize: 11, padding: "6px 8px", width: "100%", outline: "none" }}>
                <optgroup label="── NSE">{NSE_STOCKS.map(a => <option key={a.symbol} value={a.symbol}>{a.symbol} · {a.sector}</option>)}</optgroup>
                <optgroup label="── BSE">{BSE_STOCKS.map(a => <option key={a.symbol} value={a.symbol}>{a.symbol} · {a.sector}</option>)}</optgroup>
                <optgroup label="── CRYPTO">{CRYPTO_ASSETS.map(a => <option key={a.symbol} value={a.symbol}>{a.symbol} · {a.name}</option>)}</optgroup>
              </select>
            </div>

            {/* Live price */}
            {asset && (
              <div className="sc" style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 4 }}>
                {[["LTP", `₹${fmtP(asset.price)}`, "#f8fafc"],["Change", `${asset.change >= 0 ? "+" : ""}${asset.change}%`, asset.change >= 0 ? "#22c55e" : "#ef4444"],["Signal", modeSignal, sigColor[modeSignal]],["RSI", asset.rsi?.toFixed(1), asset.rsi > 70 ? "#ef4444" : asset.rsi < 30 ? "#22c55e" : "#94a3b8"]].map(([l,v,c]) => (
                  <div key={l}><div style={{ color: "#0f172a", fontSize: 9 }}>{l}</div><div style={{ color: c, fontWeight: 700, fontSize: 11 }}>{v}</div></div>
                ))}
              </div>
            )}

            {/* BUY / SELL toggle */}
            <div style={{ display: "flex", gap: 4 }}>
              {["BUY", "SELL"].map(t => (
                <button key={t} className="ob" onClick={() => setOrderType(t)}
                  style={{ border: `1.5px solid ${orderType === t ? (t === "BUY" ? "#22c55e" : "#ef4444") : "#0f172a"}`, color: orderType === t ? (t === "BUY" ? "#4ade80" : "#f87171") : "#334155", background: orderType === t ? (t === "BUY" ? "#052e16" : "#2d1515") : "#0a1628" }}>
                  {t}
                </button>
              ))}
            </div>

            {/* ── INVESTMENT INPUT MODE SWITCHER ── */}
            <div>
              <div style={{ color: "#0f172a", fontSize: 9, letterSpacing: ".1em", marginBottom: 5 }}>INVEST BY</div>
              <div style={{ display: "flex", gap: 4, marginBottom: 8 }}>
                {[["qty", "QTY"], ["amount", "₹ AMOUNT"], ["pct", "% CAPITAL"]].map(([k, lbl]) => (
                  <button key={k} className={`imode-btn ${iMode === k ? "active" : ""}`} onClick={() => setIMode(k)}
                    style={{ color: iMode === k ? "#38bdf8" : "#334155" }}>{lbl}</button>
                ))}
              </div>

              {/* QTY mode */}
              {iMode === "qty" && (
                <div>
                  <input className="ifield" type="number" value={qtyVal} onChange={e => setQtyVal(e.target.value)}
                    placeholder={asset?.exchange === "CRYPTO" ? "0.001" : "10"} min="0" step={asset?.exchange === "CRYPTO" ? "0.001" : "1"} />
                </div>
              )}

              {/* AMOUNT mode */}
              {iMode === "amount" && (
                <div>
                  <div style={{ position: "relative", marginBottom: 6 }}>
                    <span style={{ position: "absolute", left: 10, top: "50%", transform: "translateY(-50%)", color: "#475569", fontWeight: 700, fontSize: 13 }}>₹</span>
                    <input className="ifield" type="number" value={amtVal} onChange={e => setAmtVal(e.target.value)}
                      style={{ paddingLeft: 24 }} placeholder="e.g. 5000" min="0" />
                  </div>
                  {/* Quick amount presets */}
                  <div style={{ display: "grid", gridTemplateColumns: "repeat(4, 1fr)", gap: 4, marginBottom: 4 }}>
                    {[1000, 2000, 5000, 10000, 25000, 50000, 100000, 250000].map(v => (
                      <button key={v} className="pct-btn" onClick={() => setAmtVal(String(v))}
                        style={{ borderColor: amtVal === String(v) ? "#38bdf8" : "#0f172a", color: amtVal === String(v) ? "#38bdf8" : "#334155", background: amtVal === String(v) ? "#0c1a2e" : "#0a1628" }}>
                        {fmtC(v)}
                      </button>
                    ))}
                  </div>
                  {derivedQtyFromAmt > 0 && asset && (
                    <div style={{ color: "#38bdf8", fontSize: 9, marginTop: 2 }}>→ {derivedQtyFromAmt} units @ ₹{fmtP(asset.price)}</div>
                  )}
                </div>
              )}

              {/* % CAPITAL mode */}
              {iMode === "pct" && (
                <div>
                  <div style={{ display: "grid", gridTemplateColumns: "repeat(4, 1fr)", gap: 4, marginBottom: 6 }}>
                    {[5, 10, 15, 20, 25, 50, 75, 100].map(p => (
                      <button key={p} className="pct-btn" onClick={() => setPctVal(p)}
                        style={{ borderColor: pctVal === p ? "#38bdf8" : "#0f172a", color: pctVal === p ? "#38bdf8" : "#334155", background: pctVal === p ? "#0c1a2e" : "#0a1628" }}>
                        {p}%
                      </button>
                    ))}
                  </div>
                  <div style={{ background: "#0a1628", borderRadius: 5, padding: "6px 10px", fontSize: 10, display: "flex", justifyContent: "space-between" }}>
                    <span style={{ color: "#334155" }}>{pctVal}% of ₹{fmtN(capital)}</span>
                    <span style={{ color: "#38bdf8", fontWeight: 700 }}>= ₹{fmtN(capital * pctVal / 100)}</span>
                  </div>
                  {derivedQtyFromPct > 0 && asset && (
                    <div style={{ color: "#38bdf8", fontSize: 9, marginTop: 4 }}>→ {derivedQtyFromPct} units @ ₹{fmtP(asset.price)}</div>
                  )}
                </div>
              )}
            </div>

            {/* Order summary */}
            {activeQty > 0 && asset && (
              <div className="sc" style={{ fontSize: 10 }}>
                <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 2 }}>
                  <span style={{ color: "#334155" }}>Qty</span>
                  <span style={{ fontWeight: 700 }}>{activeQty} units</span>
                </div>
                <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 2 }}>
                  <span style={{ color: "#334155" }}>{orderType === "BUY" ? "Total Cost" : "Proceeds"}</span>
                  <span style={{ fontWeight: 700, color: "#f8fafc" }}>₹{fmtN(activeCost)}</span>
                </div>
                {orderType === "BUY" && modeTargets && (
                  <>
                    <div style={{ height: 1, background: "#0f172a", margin: "5px 0" }} />
                    <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 2 }}>
                      <span style={{ color: "#ef4444", fontSize: 9 }}>Stop Loss</span>
                      <span style={{ color: "#ef4444", fontWeight: 600 }}>₹{fmtP(modeTargets.sl)} (-{modeCfg.stopLossPct}%)</span>
                    </div>
                    <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 2 }}>
                      <span style={{ color: "#4ade80", fontSize: 9 }}>Target 1</span>
                      <span style={{ color: "#4ade80", fontWeight: 600 }}>₹{fmtP(modeTargets.t1)} (+{modeCfg.target1}%)</span>
                    </div>
                    <div style={{ display: "flex", justifyContent: "space-between" }}>
                      <span style={{ color: "#22c55e", fontSize: 9 }}>Target 2</span>
                      <span style={{ color: "#22c55e", fontWeight: 600 }}>₹{fmtP(modeTargets.t2)} (+{modeCfg.target2}%)</span>
                    </div>
                  </>
                )}
                {orderType === "SELL" && portfolio[selectedSym] && (
                  <div style={{ display: "flex", justifyContent: "space-between", marginTop: 4 }}>
                    <span style={{ color: "#334155", fontSize: 9 }}>Est. P&L</span>
                    <span style={{ color: (asset.price - portfolio[selectedSym].avgPrice) >= 0 ? "#22c55e" : "#ef4444", fontWeight: 600 }}>
                      {((asset.price - portfolio[selectedSym].avgPrice) * activeQty >= 0 ? "+" : "")}₹{fmtN((asset.price - portfolio[selectedSym].avgPrice) * activeQty)}
                    </span>
                  </div>
                )}
                {orderType === "BUY" && activeCost > capital && (
                  <div style={{ color: "#ef4444", fontSize: 9, marginTop: 4, fontWeight: 700 }}>⚠ Exceeds available cash</div>
                )}
              </div>
            )}

            <button className="abtn" onClick={handleTrade}
              style={{ background: orderType === "BUY" ? "#16a34a" : "#dc2626", color: "#fff", opacity: activeQty > 0 ? 1 : 0.5 }}>
              {modeCfg.icon} {orderType} {selectedSym} [{modeCfg.short}]
            </button>

            {/* Wallet summary */}
            <div style={{ borderTop: "1px solid #06091a", paddingTop: 8 }}>
              <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 6 }}>
                <div style={{ color: "#0f172a", fontSize: 9, letterSpacing: ".1em" }}>WALLET</div>
                <button onClick={() => setShowWallet(true)} style={{ background: "#0a1628", border: "1px solid #0f172a", color: "#38bdf8", borderRadius: 4, padding: "2px 7px", fontFamily: "inherit", fontSize: 9, fontWeight: 700, cursor: "pointer" }}>MANAGE ↗</button>
              </div>
              {[["Cash", `₹${fmtN(capital)}`, "#38bdf8"],["Invested", `₹${fmtN(portfolioValue)}`, "#a78bfa"],["P&L", `${totalPnL >= 0 ? "+" : ""}₹${fmtN(totalPnL)}`, totalPnL >= 0 ? "#22c55e" : "#ef4444"]].map(([k,v,c]) => (
                <div key={k} style={{ display: "flex", justifyContent: "space-between", marginBottom: 4 }}>
                  <span style={{ color: "#334155", fontSize: 10 }}>{k}</span>
                  <span style={{ color: c, fontWeight: 600, fontSize: 11 }}>{v}</span>
                </div>
              ))}
            </div>

            {/* Bot status */}
            {autoTrade && (
              <div style={{ background: "#020a03", border: "1px solid #052e16", borderRadius: 6, padding: "7px 9px" }}>
                <div style={{ display: "flex", alignItems: "center", gap: 5, marginBottom: 4 }}>
                  <div className="bpulse" style={{ width: 5, height: 5, borderRadius: "50%", background: botStatus === "TRADING" ? "#22c55e" : "#60a5fa" }} />
                  <span style={{ fontSize: 9, fontWeight: 700, color: botStatus === "TRADING" ? "#4ade80" : "#93c5fd" }}>BOT {botStatus}</span>
                </div>
                <div style={{ fontSize: 9, color: "#1e293b", lineHeight: 1.7 }}>
                  <div>Mode: <span style={{ color: modeCfg.color }}>{modeCfg.label}</span></div>
                  <div>Scanning: {ALL_ASSETS.length} assets</div>
                  <div>Positions: {Object.keys(portfolio).length} · Trades: {trades.filter(t => t.auto).length}</div>
                </div>
              </div>
            )}
          </div>
        </div>

        <BotActivity items={botItems} />

        {toast && (
          <div className="toast" style={{ background: toast.type === "error" ? "#2d1515" : "#052e16", border: `1px solid ${toast.type === "error" ? "#ef4444" : "#22c55e"}`, color: toast.type === "error" ? "#f87171" : "#4ade80" }}>
            {toast.msg}
          </div>
        )}
      </div>
    </>
  );
}
