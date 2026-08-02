import React, { useState, useEffect, useMemo, useCallback, useRef } from "react";
import {
  AlertTriangle,
  Bell,
  Package,
  TrendingDown,
  TrendingUp,
  ShoppingCart,
  MinusCircle,
  Settings,
  X,
  ChevronDown,
  ChevronRight,
  Send,
  Info,
  Milk,
  Boxes,
  ClipboardList,
  RotateCcw,
  Plus,
  Pencil,
  Trash2,
  FileText,
  Printer,
  ArrowLeft,
} from "lucide-react";
import {
  ComposedChart,
  Bar,
  Line,
  XAxis,
  YAxis,
  Tooltip,
  ResponsiveContainer,
  CartesianGrid,
} from "recharts";

/* ---------------------------------------------------------------
   Fresh Yogurt — ระบบจัดการสต็อกวัตถุดิบ & บรรจุภัณฑ์
   ธีม: ครีมโยเกิร์ต (อุ่น) + เบอร์รี่เข้ม (โครม) + สถานะสัญญาณไฟ
--------------------------------------------------------------- */

const LINE_LINK = "https://line.me/ti/p/P40ZLb2bk5";
const BRAND = "Fresh Yogurt";

const SEED_PRODUCTS = [
  { id: "milk", name: "Milk (นมสด)", category: "raw", unit: "ml", packSize: 2000, packCost: 86, leadTimeDays: 1, shelfLifeDays: 7, supplier: "ฟาร์มนมสด", onHand: 6000, avgMonthlyUsage: 40000, abcOverride: null },
  { id: "yolida", name: "Yolida (หัวเชื้อโยเกิร์ต)", category: "raw", unit: "g", packSize: 225, packCost: 55, leadTimeDays: 2, shelfLifeDays: 14, supplier: "ผู้จำหน่ายหัวเชื้อ", onHand: 900, avgMonthlyUsage: 3375, abcOverride: null },
  { id: "biscoff", name: "Biscoff", category: "raw", unit: "g", packSize: 750, packCost: 285, leadTimeDays: 3, shelfLifeDays: 365, supplier: "ซัพพลายเออร์ทอปปิ้ง", onHand: 1500, avgMonthlyUsage: 1500, abcOverride: null },
  { id: "cornflake", name: "Cornflake", category: "raw", unit: "g", packSize: 500, packCost: 145, leadTimeDays: 3, shelfLifeDays: 180, supplier: "ซัพพลายเออร์ทอปปิ้ง", onHand: 250, avgMonthlyUsage: 1000, abcOverride: null },
  { id: "granola", name: "Granola", category: "raw", unit: "g", packSize: 500, packCost: 242, leadTimeDays: 3, shelfLifeDays: 180, supplier: "ซัพพลายเออร์ทอปปิ้ง", onHand: 1000, avgMonthlyUsage: 750, abcOverride: null },
  { id: "almond", name: "Almond", category: "raw", unit: "g", packSize: 1000, packCost: 384, leadTimeDays: 5, shelfLifeDays: 365, supplier: "ซัพพลายเออร์ทอปปิ้ง", onHand: 500, avgMonthlyUsage: 600, abcOverride: null },
  { id: "oreo", name: "Oreo", category: "raw", unit: "g", packSize: 454, packCost: 99, leadTimeDays: 3, shelfLifeDays: 270, supplier: "ซัพพลายเออร์ทอปปิ้ง", onHand: 908, avgMonthlyUsage: 908, abcOverride: null },
  { id: "spoon", name: "Spoon (ช้อน)", category: "packaging", unit: "pcs", packSize: 100, packCost: 48, leadTimeDays: 7, shelfLifeDays: null, supplier: "ผู้ผลิตบรรจุภัณฑ์", onHand: 150, avgMonthlyUsage: 1200, abcOverride: null },
  { id: "cup_pet", name: "Cup (PET)", category: "packaging", unit: "pcs", packSize: 50, packCost: 159, leadTimeDays: 7, shelfLifeDays: null, supplier: "ผู้ผลิตบรรจุภัณฑ์", onHand: 100, avgMonthlyUsage: 900, abcOverride: null },
  { id: "sticker_ty", name: "Sticker (Thank you)", category: "packaging", unit: "pcs", packSize: 100, packCost: 43.78, leadTimeDays: 5, shelfLifeDays: null, supplier: "ร้านสติกเกอร์", onHand: 300, avgMonthlyUsage: 900, abcOverride: null },
  { id: "sauce_cup", name: "Sauce Cup", category: "packaging", unit: "pcs", packSize: 50, packCost: 27, leadTimeDays: 5, shelfLifeDays: null, supplier: "ผู้ผลิตบรรจุภัณฑ์", onHand: 200, avgMonthlyUsage: 400, abcOverride: null },
  { id: "cup", name: "Cup", category: "packaging", unit: "pcs", packSize: 50, packCost: 50, leadTimeDays: 5, shelfLifeDays: null, supplier: "ผู้ผลิตบรรจุภัณฑ์", onHand: 150, avgMonthlyUsage: 600, abcOverride: null },
  { id: "cooler_bag", name: "Cooler Bag", category: "packaging", unit: "pcs", packSize: 300, packCost: 3060, leadTimeDays: 10, shelfLifeDays: null, supplier: "ผู้ผลิตบรรจุภัณฑ์", onHand: 60, avgMonthlyUsage: 90, abcOverride: null },
  { id: "delivery_bag", name: "Delivery Bag (craft paper)", category: "packaging", unit: "pcs", packSize: 25, packCost: 84, leadTimeDays: 5, shelfLifeDays: null, supplier: "ร้านกระดาษคราฟท์", onHand: 50, avgMonthlyUsage: 150, abcOverride: null },
  { id: "bucket", name: "Bucket", category: "packaging", unit: "pcs", packSize: 1, packCost: 24.58, leadTimeDays: 7, shelfLifeDays: null, supplier: "ผู้ผลิตบรรจุภัณฑ์", onHand: 10, avgMonthlyUsage: 20, abcOverride: null },
  { id: "bottle_pet", name: "Bottle PET", category: "packaging", unit: "pcs", packSize: 50, packCost: 120, leadTimeDays: 7, shelfLifeDays: null, supplier: "ผู้ผลิตบรรจุภัณฑ์", onHand: 100, avgMonthlyUsage: 300, abcOverride: null },
  { id: "bottle_label", name: "Bottle Label", category: "packaging", unit: "pcs", packSize: 48, packCost: 102.5, leadTimeDays: 5, shelfLifeDays: null, supplier: "ร้านสติกเกอร์", onHand: 96, avgMonthlyUsage: 288, abcOverride: null },
  { id: "flavor_sticker", name: "Flavor Stickers", category: "packaging", unit: "pcs", packSize: 588, packCost: 102.5, leadTimeDays: 5, shelfLifeDays: null, supplier: "ร้านสติกเกอร์", onHand: 1176, avgMonthlyUsage: 1764, abcOverride: null },
  { id: "cup_takeaway", name: "Cup (Take away)", category: "packaging", unit: "pcs", packSize: 10, packCost: 125, leadTimeDays: 5, shelfLifeDays: null, supplier: "ผู้ผลิตบรรจุภัณฑ์", onHand: 20, avgMonthlyUsage: 60, abcOverride: null },
  { id: "sticker_takeaway", name: "Sticker (Take away)", category: "packaging", unit: "pcs", packSize: 96, packCost: 59, leadTimeDays: 5, shelfLifeDays: null, supplier: "ร้านสติกเกอร์", onHand: 192, avgMonthlyUsage: 288, abcOverride: null },
];

const DEFAULT_SETTINGS = {
  orderCost: 100,
  holdingRate: 0.2,
  serviceZ: 1.65,
  demandCv: 0.25,
};

const EMPTY_DRAFT = {
  id: "",
  name: "",
  category: "raw",
  unit: "g",
  packSize: 1,
  packCost: 0,
  leadTimeDays: 3,
  shelfLifeDays: "",
  supplier: "",
  onHand: 0,
  avgMonthlyUsage: 0,
  abcOverride: null,
};

function fmt(n, d = 0) {
  if (n === null || n === undefined || Number.isNaN(n)) return "-";
  return n.toLocaleString("th-TH", { minimumFractionDigits: d, maximumFractionDigits: d });
}

function slugify(name) {
  return (
    name
      .toLowerCase()
      .trim()
      .replace(/[^a-z0-9ก-๙]+/g, "-")
      .replace(/(^-|-$)/g, "") || `item-${Date.now()}`
  );
}

function computeMetrics(p, settings) {
  const costPerUnit = p.packSize > 0 ? p.packCost / p.packSize : 0;
  const dailyUsage = p.avgMonthlyUsage / 30;
  const annualDemand = p.avgMonthlyUsage * 12;
  const annualValue = costPerUnit * annualDemand;
  const holdingCostPerUnit = costPerUnit * settings.holdingRate;
  const eoq =
    holdingCostPerUnit > 0 && annualDemand > 0
      ? Math.sqrt((2 * annualDemand * settings.orderCost) / holdingCostPerUnit)
      : 0;
  const leadTimeDemand = dailyUsage * (p.leadTimeDays || 0);
  const demandStdDev = dailyUsage * settings.demandCv;
  const safetyStock = settings.serviceZ * demandStdDev * Math.sqrt(p.leadTimeDays || 1);
  const rop = leadTimeDemand + safetyStock;
  const daysRemaining = dailyUsage > 0 ? p.onHand / dailyUsage : Infinity;
  const packsUsedThisMonth = p.packSize > 0 ? p.avgMonthlyUsage / p.packSize : 0;
  const stockValue = costPerUnit * p.onHand;

  let status = "green";
  if (p.onHand <= safetyStock) status = "red";
  else if (p.onHand <= rop) status = "yellow";

  return {
    costPerUnit, dailyUsage, annualDemand, annualValue, eoq,
    leadTimeDemand, safetyStock, rop, daysRemaining, status, packsUsedThisMonth, stockValue,
  };
}

function classifyAbc(products, settings) {
  const withValue = products.map((p) => ({ p, value: computeMetrics(p, settings).annualValue }));
  withValue.sort((a, b) => b.value - a.value);
  const total = withValue.reduce((s, x) => s + x.value, 0) || 1;
  let cum = 0;
  const result = {};
  withValue.forEach(({ p, value }) => {
    cum += value;
    const cumPct = (cum / total) * 100;
    let computedCls = "C";
    if (cumPct <= 80) computedCls = "A";
    else if (cumPct <= 95) computedCls = "B";
    const cls = p.abcOverride || computedCls;
    result[p.id] = { value, cumPct, cls, computedCls, overridden: !!p.abcOverride, pct: (value / total) * 100 };
  });
  return result;
}

const STATUS_META = {
  red: { label: "ใกล้หมด", dot: "bg-[#D6432C]", text: "text-[#D6432C]", bg: "bg-[#FBEAE7]", ring: "ring-[#D6432C]/30" },
  yellow: { label: "เริ่มน้อย", dot: "bg-[#C98A22]", text: "text-[#C98A22]", bg: "bg-[#FBF1DF]", ring: "ring-[#C98A22]/30" },
  green: { label: "ปกติ", dot: "bg-[#3F8A5E]", text: "text-[#3F8A5E]", bg: "bg-[#E9F4EC]", ring: "ring-[#3F8A5E]/30" },
};

function StatusPill({ status }) {
  const m = STATUS_META[status];
  return (
    <span className={`inline-flex items-center gap-1.5 rounded-full px-2.5 py-1 text-xs font-semibold ${m.bg} ${m.text} ring-1 ${m.ring}`}>
      <span className={`h-1.5 w-1.5 rounded-full ${m.dot}`} />
      {m.label}
    </span>
  );
}

function Field({ label, children }) {
  return (
    <div className="mb-3.5">
      <label className="block text-xs font-medium text-[#6B5B69] mb-1.5">{label}</label>
      {children}
    </div>
  );
}

const inputCls = "w-full rounded-lg border border-[#E4DCD1] px-3 py-2.5 text-sm tabular bg-white";

export default function InventoryApp() {
  const [products, setProducts] = useState(SEED_PRODUCTS);
  const [settings, setSettings] = useState(DEFAULT_SETTINGS);
  const [log, setLog] = useState([]);
  const [loaded, setLoaded] = useState(false);
  const [tab, setTab] = useState("dashboard");
  const [categoryFilter, setCategoryFilter] = useState("all");
  const [statusFilter, setStatusFilter] = useState("all");
  const [expanded, setExpanded] = useState(null);
  const [reqOpen, setReqOpen] = useState(false);
  const [reqProductId, setReqProductId] = useState(SEED_PRODUCTS[0].id);
  const [reqQty, setReqQty] = useState(1);
  const [reqNote, setReqNote] = useState("");
  const [settingsOpen, setSettingsOpen] = useState(false);
  const [toast, setToast] = useState(null);
  const [editorOpen, setEditorOpen] = useState(false);
  const [editorMode, setEditorMode] = useState("add");
  const [draft, setDraft] = useState(EMPTY_DRAFT);
  const [hasUpdates, setHasUpdates] = useState(false);
  const [showSummary, setShowSummary] = useState(false);

  useEffect(() => {
    (async () => {
      try {
        const p = await window.storage.get("fy-products", false);
        if (p) setProducts(JSON.parse(p.value));
      } catch (e) {}
      try {
        const s = await window.storage.get("fy-settings", false);
        if (s) setSettings(JSON.parse(s.value));
      } catch (e) {}
      try {
        const l = await window.storage.get("fy-log", false);
        if (l) setLog(JSON.parse(l.value));
      } catch (e) {}
      setLoaded(true);
    })();
  }, []);

  const persist = useCallback(async (key, value) => {
    try {
      await window.storage.set(key, JSON.stringify(value), false);
    } catch (e) {}
  }, []);

  useEffect(() => { if (loaded) persist("fy-products", products); }, [products, loaded, persist]);
  useEffect(() => { if (loaded) persist("fy-settings", settings); }, [settings, loaded, persist]);
  useEffect(() => { if (loaded) persist("fy-log", log); }, [log, loaded, persist]);

  useEffect(() => {
    if (!toast) return;
    const t = setTimeout(() => setToast(null), 3200);
    return () => clearTimeout(t);
  }, [toast]);

  const metricsById = useMemo(() => {
    const m = {};
    products.forEach((p) => { m[p.id] = computeMetrics(p, settings); });
    return m;
  }, [products, settings]);

  const abc = useMemo(() => classifyAbc(products, settings), [products, settings]);

  const counts = useMemo(() => {
    const c = { red: 0, yellow: 0, green: 0 };
    products.forEach((p) => { c[metricsById[p.id].status]++; });
    return c;
  }, [products, metricsById]);

  const totalValue = useMemo(
    () => products.reduce((s, p) => s + metricsById[p.id].stockValue, 0),
    [products, metricsById]
  );

  const filtered = products
    .filter((p) => {
      if (categoryFilter !== "all" && p.category !== categoryFilter) return false;
      if (statusFilter !== "all" && metricsById[p.id].status !== statusFilter) return false;
      return true;
    })
    .sort((a, b) => metricsById[a.id].daysRemaining - metricsById[b.id].daysRemaining);

  function addLog(entry) {
    setLog((l) => [{ id: `${Date.now()}-${Math.random().toString(36).slice(2, 7)}`, ts: new Date().toISOString(), ...entry }, ...l].slice(0, 200));
  }

  function submitRequisition() {
    const p = products.find((x) => x.id === reqProductId);
    if (!p || reqQty <= 0) return;
    const before = metricsById[p.id];
    const newOnHand = Math.max(0, p.onHand - reqQty);
    setProducts((ps) => ps.map((x) => (x.id === p.id ? { ...x, onHand: newOnHand } : x)));
    const afterMetrics = computeMetrics({ ...p, onHand: newOnHand }, settings);
    addLog({
      type: "requisition", productId: p.id, productName: p.name, qty: reqQty, unit: p.unit,
      note: reqNote, onHandAfter: newOnHand, statusBefore: before.status, statusAfter: afterMetrics.status,
    });
    if (afterMetrics.status !== "green") {
      addLog({
        type: "line_alert", productId: p.id, productName: p.name,
        message: `${p.name} เหลือ ${fmt(newOnHand)} ${p.unit} (${STATUS_META[afterMetrics.status].label}) — เหลือใช้ได้อีกประมาณ ${fmt(afterMetrics.daysRemaining, 1)} วัน`,
        status: afterMetrics.status,
      });
    }
    setToast(`เบิก ${p.name} จำนวน ${reqQty} ${p.unit} แล้ว — คงเหลือ ${fmt(newOnHand)} ${p.unit}`);
    setReqOpen(false); setReqQty(1); setReqNote(""); setHasUpdates(true);
  }

  function openLineForOrder(p) {
    const m = metricsById[p.id];
    addLog({
      type: "line_alert", productId: p.id, productName: p.name,
      message: `ขอสั่งซื้อ ${p.name} — แนะนำสั่ง ${fmt(m.eoq)} ${p.unit} (EOQ) จาก ${p.supplier || "ผู้ขายหลัก"}`,
      status: "order",
    });
    window.open(LINE_LINK, "_blank");
    setToast(`เปิด LINE เพื่อสั่งซื้อ ${p.name} — ระบบบันทึกคำขอไว้ในประวัติแล้ว`);
    setHasUpdates(true);
  }

  function resetData() {
    setProducts(SEED_PRODUCTS); setSettings(DEFAULT_SETTINGS); setLog([]);
    setToast("รีเซ็ตข้อมูลตัวอย่างเรียบร้อย"); setHasUpdates(false);
  }

  function openAddEditor() {
    setDraft({ ...EMPTY_DRAFT }); setEditorMode("add"); setEditorOpen(true);
  }
  function openEditEditor(p) {
    setDraft({ ...p, shelfLifeDays: p.shelfLifeDays ?? "" }); setEditorMode("edit"); setEditorOpen(true);
  }
  function deleteProduct(p) {
    if (!window.confirm(`ลบ "${p.name}" ออกจากระบบ?`)) return;
    setProducts((ps) => ps.filter((x) => x.id !== p.id));
    addLog({ type: "edit", productId: p.id, productName: p.name, message: `ลบสินค้า "${p.name}" ออกจากระบบ` });
    setToast(`ลบ "${p.name}" แล้ว`); setExpanded(null); setHasUpdates(true);
  }
  function saveDraft() {
    if (!draft.name.trim()) { setToast("กรุณาใส่ชื่อสินค้า"); return; }
    const cleaned = {
      ...draft,
      packSize: Number(draft.packSize) || 1,
      packCost: Number(draft.packCost) || 0,
      leadTimeDays: Number(draft.leadTimeDays) || 0,
      shelfLifeDays: draft.shelfLifeDays === "" ? null : Number(draft.shelfLifeDays),
      onHand: Number(draft.onHand) || 0,
      avgMonthlyUsage: Number(draft.avgMonthlyUsage) || 0,
    };
    if (editorMode === "add") {
      const id = slugify(cleaned.name) + "-" + Math.random().toString(36).slice(2, 5);
      setProducts((ps) => [...ps, { ...cleaned, id }]);
      addLog({ type: "edit", productId: id, productName: cleaned.name, message: `เพิ่มสินค้าใหม่ "${cleaned.name}"` });
      setToast(`เพิ่ม "${cleaned.name}" แล้ว`);
    } else {
      setProducts((ps) => ps.map((x) => (x.id === cleaned.id ? cleaned : x)));
      addLog({ type: "edit", productId: cleaned.id, productName: cleaned.name, message: `แก้ไขข้อมูล "${cleaned.name}"` });
      setToast(`บันทึกการแก้ไข "${cleaned.name}" แล้ว`);
    }
    setEditorOpen(false); setHasUpdates(true);
  }

  function setAbcOverride(productId, value) {
    setProducts((ps) => ps.map((x) => (x.id === productId ? { ...x, abcOverride: value || null } : x)));
    setHasUpdates(true);
  }

  const abcRows = useMemo(
    () => [...products].map((p) => ({ p, ...abc[p.id] })).sort((a, b) => b.value - a.value),
    [products, abc]
  );

  const chartData = abcRows.map((r) => ({
    name: r.p.name.length > 10 ? r.p.name.slice(0, 9) + "…" : r.p.name,
    value: Math.round(r.value),
    cumPct: Math.round(r.cumPct),
  }));

  const criticalItems = products.filter((p) => metricsById[p.id].status === "red");
  const warningItems = products.filter((p) => metricsById[p.id].status === "yellow");
  const alertLog = log.filter((l) => l.type === "line_alert");

  function confirmAndViewSummary() {
    setHasUpdates(false);
    setShowSummary(true);
  }

  if (showSummary) {
    return (
      <SummaryView
        brand={BRAND}
        products={products}
        metricsById={metricsById}
        abc={abc}
        counts={counts}
        totalValue={totalValue}
        criticalItems={criticalItems}
        warningItems={warningItems}
        alertLog={alertLog}
        onBack={() => setShowSummary(false)}
      />
    );
  }

  return (
    <div className="min-h-screen bg-[#F6F3EF] font-body text-[#2B1E2A]">
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Prompt:wght@500;600;700&family=IBM+Plex+Sans+Thai:wght@400;500;600;700&display=swap');
        .font-display { font-family: 'Prompt', sans-serif; }
        .font-body { font-family: 'IBM Plex Sans Thai', sans-serif; }
        .tabular { font-variant-numeric: tabular-nums; }
      `}</style>

      <header className="sticky top-0 z-30 border-b border-[#E4DCD1] bg-[#2B1E2A] text-[#F6F3EF]">
        <div className="mx-auto max-w-6xl px-4 py-4 flex items-center justify-between">
          <div className="flex items-center gap-2.5">
            <div className="flex h-9 w-9 items-center justify-center rounded-full bg-[#E8A33D] text-[#2B1E2A]">
              <Milk size={18} strokeWidth={2.5} />
            </div>
            <div>
              <h1 className="font-display text-lg font-semibold leading-tight">{BRAND}</h1>
              <p className="text-[11px] text-[#D9C9DA]">รู้ว่าจะหมดเมื่อไหร่ ก่อนมันจะหมด</p>
            </div>
          </div>
          <div className="flex items-center gap-2">
            <button
              onClick={() => setShowSummary(true)}
              className="flex items-center gap-1.5 rounded-full bg-[#E8A33D] px-3 py-1.5 text-xs font-medium text-[#2B1E2A]"
            >
              <FileText size={14} /> สรุป PDF
            </button>
            <button
              onClick={() => setSettingsOpen(true)}
              className="flex items-center gap-1.5 rounded-full border border-[#4A3549] px-3 py-1.5 text-xs font-medium text-[#D9C9DA] hover:bg-[#3A2839]"
            >
              <Settings size={14} /> ตั้งค่า
            </button>
          </div>
        </div>
        <div className="mx-auto max-w-6xl px-4 pb-4 grid grid-cols-3 gap-2.5">
          {[
            { key: "red", label: "ใกล้หมด", icon: AlertTriangle },
            { key: "yellow", label: "เริ่มน้อย", icon: TrendingDown },
            { key: "green", label: "ปกติ", icon: Package },
          ].map(({ key, label, icon: Icon }) => (
            <button
              key={key}
              onClick={() => { setTab("dashboard"); setStatusFilter(statusFilter === key ? "all" : key); }}
              className={`rounded-xl px-3 py-2.5 text-left transition ${statusFilter === key ? "bg-[#E8A33D] text-[#2B1E2A]" : "bg-[#3A2839] text-[#F6F3EF]"}`}
            >
              <div className="flex items-center justify-between">
                <span className="text-[11px] opacity-80">{label}</span>
                <Icon size={13} />
              </div>
              <div className="font-display text-xl font-semibold tabular">{counts[key]}</div>
            </button>
          ))}
        </div>
      </header>

      <nav className="mx-auto max-w-6xl px-4 mt-4 flex gap-1.5 overflow-x-auto">
        {[
          { id: "dashboard", label: "รายการสต็อก", icon: Boxes },
          { id: "abc", label: "ABC Analysis", icon: TrendingUp },
          { id: "report", label: "รายงานการใช้", icon: ClipboardList },
          { id: "log", label: "ประวัติ / LINE", icon: Bell },
        ].map(({ id, label, icon: Icon }) => (
          <button
            key={id}
            onClick={() => setTab(id)}
            className={`flex items-center gap-1.5 whitespace-nowrap rounded-full px-3.5 py-2 text-sm font-medium transition ${
              tab === id ? "bg-[#2B1E2A] text-[#F6F3EF]" : "bg-white text-[#6B5B69] border border-[#E4DCD1]"
            }`}
          >
            <Icon size={14} /> {label}
            {id === "log" && alertLog.length > 0 && (
              <span className="ml-0.5 rounded-full bg-[#D6432C] px-1.5 text-[10px] text-white tabular">{alertLog.length}</span>
            )}
          </button>
        ))}
      </nav>

      <main className="mx-auto max-w-6xl px-4 py-5 pb-28">
        {tab === "dashboard" && (
          <div>
            <div className="flex items-center gap-2 mb-3 overflow-x-auto">
              {[
                { id: "all", label: "ทั้งหมด" },
                { id: "raw", label: "วัตถุดิบ" },
                { id: "packaging", label: "บรรจุภัณฑ์" },
              ].map((c) => (
                <button
                  key={c.id}
                  onClick={() => setCategoryFilter(c.id)}
                  className={`whitespace-nowrap rounded-full px-3 py-1.5 text-xs font-medium border ${
                    categoryFilter === c.id ? "bg-[#4F9D8D] text-white border-[#4F9D8D]" : "bg-white text-[#6B5B69] border-[#E4DCD1]"
                  }`}
                >
                  {c.label}
                </button>
              ))}
              {statusFilter !== "all" && (
                <button onClick={() => setStatusFilter("all")} className="flex items-center gap-1 whitespace-nowrap rounded-full bg-[#EFE8E0] px-3 py-1.5 text-xs text-[#6B5B69]">
                  ล้างตัวกรองสถานะ <X size={12} />
                </button>
              )}
              <button
                onClick={openAddEditor}
                className="ml-auto flex items-center gap-1 whitespace-nowrap rounded-full bg-[#2B1E2A] px-3 py-1.5 text-xs font-medium text-white"
              >
                <Plus size={13} /> เพิ่มสินค้า
              </button>
            </div>

            <div className="space-y-2.5">
              {filtered.map((p) => {
                const m = metricsById[p.id];
                const isOpen = expanded === p.id;
                return (
                  <div key={p.id} className="rounded-2xl border border-[#E4DCD1] bg-white overflow-hidden">
                    <button onClick={() => setExpanded(isOpen ? null : p.id)} className="w-full flex items-center gap-3 px-4 py-3 text-left">
                      <span className={`h-9 w-1.5 rounded-full ${STATUS_META[m.status].dot} shrink-0`} />
                      <div className="flex-1 min-w-0">
                        <div className="flex items-center gap-2 flex-wrap">
                          <span className="font-medium text-sm truncate">{p.name}</span>
                          <span className="rounded-md bg-[#EFE8E0] px-1.5 py-0.5 text-[10px] font-semibold text-[#6B5B69]">{abc[p.id]?.cls || "-"}</span>
                        </div>
                        <div className="mt-0.5 flex items-center gap-2 text-xs text-[#8A7A87] tabular">
                          <span>คงเหลือ {fmt(p.onHand)} {p.unit}</span>
                          <span>•</span>
                          <span>ใช้ได้อีก ~{fmt(m.daysRemaining, m.daysRemaining < 10 ? 1 : 0)} วัน</span>
                        </div>
                      </div>
                      <StatusPill status={m.status} />
                      {isOpen ? <ChevronDown size={16} className="text-[#8A7A87]" /> : <ChevronRight size={16} className="text-[#8A7A87]" />}
                    </button>

                    {isOpen && (
                      <div className="border-t border-[#EFE8E0] px-4 py-3.5 bg-[#FBF9F6]">
                        <div className="grid grid-cols-2 sm:grid-cols-4 gap-3 text-xs">
                          <Metric label="จุดสั่งซื้อ (ROP)" value={`${fmt(m.rop)} ${p.unit}`} />
                          <Metric label="Safety Stock" value={`${fmt(m.safetyStock)} ${p.unit}`} />
                          <Metric label="EOQ แนะนำ" value={`${fmt(m.eoq)} ${p.unit}`} />
                          <Metric label="Lead Time" value={`${p.leadTimeDays} วัน`} />
                          <Metric label="ใช้เฉลี่ย/วัน" value={`${fmt(m.dailyUsage, 1)} ${p.unit}`} />
                          <Metric label="ใช้เดือนนี้ (ประมาณ)" value={`${fmt(p.avgMonthlyUsage)} ${p.unit}`} />
                          <Metric label="ต้นทุน/หน่วย" value={`฿${fmt(m.costPerUnit, 2)}`} />
                          <Metric label="ผู้ขาย" value={p.supplier || "-"} />
                        </div>
                        <div className="mt-3.5 flex flex-wrap gap-2">
                          <button onClick={() => { setReqProductId(p.id); setReqOpen(true); }} className="flex items-center gap-1.5 rounded-full bg-[#2B1E2A] px-3.5 py-2 text-xs font-medium text-white">
                            <MinusCircle size={14} /> เบิกสินค้า
                          </button>
                          <button onClick={() => openLineForOrder(p)} className="flex items-center gap-1.5 rounded-full bg-[#E8A33D] px-3.5 py-2 text-xs font-medium text-[#2B1E2A]">
                            <ShoppingCart size={14} /> สั่งซื้อผ่าน LINE
                          </button>
                          <button onClick={() => openEditEditor(p)} className="flex items-center gap-1.5 rounded-full border border-[#E4DCD1] px-3.5 py-2 text-xs font-medium text-[#6B5B69]">
                            <Pencil size={14} /> แก้ไข
                          </button>
                          <button onClick={() => deleteProduct(p)} className="flex items-center gap-1.5 rounded-full border border-[#F3D9D4] px-3.5 py-2 text-xs font-medium text-[#D6432C]">
                            <Trash2 size={14} /> ลบ
                          </button>
                        </div>
                      </div>
                    )}
                  </div>
                );
              })}
              {filtered.length === 0 && (
                <div className="rounded-2xl border border-dashed border-[#E4DCD1] py-10 text-center text-sm text-[#8A7A87]">ไม่มีรายการตรงกับตัวกรองนี้</div>
              )}
            </div>
          </div>
        )}

        {tab === "abc" && (
          <div>
            <div className="rounded-2xl border border-[#E4DCD1] bg-white p-4 mb-4">
              <h2 className="font-display text-base font-semibold mb-1">ผังพาเรโต — มูลค่าการใช้ต่อปี</h2>
              <p className="text-xs text-[#8A7A87] mb-3">จัดอันดับตาม ต้นทุน/หน่วย × ปริมาณใช้ต่อปี (Class A = สัดส่วนมูลค่าสะสมถึง 80%) — สามารถแก้ไข Class เองได้รายชิ้น</p>
              <div className="h-64 w-full">
                <ResponsiveContainer width="100%" height="100%">
                  <ComposedChart data={chartData} margin={{ top: 5, right: 10, left: -10, bottom: 40 }}>
                    <CartesianGrid stroke="#EFE8E0" vertical={false} />
                    <XAxis dataKey="name" tick={{ fontSize: 10, fill: "#8A7A87" }} angle={-40} textAnchor="end" interval={0} height={60} />
                    <YAxis yAxisId="left" tick={{ fontSize: 10, fill: "#8A7A87" }} />
                    <YAxis yAxisId="right" orientation="right" domain={[0, 100]} tick={{ fontSize: 10, fill: "#8A7A87" }} />
                    <Tooltip contentStyle={{ fontSize: 12, borderRadius: 8, border: "1px solid #E4DCD1" }} formatter={(v, name) => (name === "cumPct" ? [`${v}%`, "สะสม"] : [`฿${fmt(v)}`, "มูลค่า/ปี"])} />
                    <Bar yAxisId="left" dataKey="value" fill="#4F9D8D" radius={[4, 4, 0, 0]} />
                    <Line yAxisId="right" type="monotone" dataKey="cumPct" stroke="#D6432C" strokeWidth={2} dot={false} />
                  </ComposedChart>
                </ResponsiveContainer>
              </div>
            </div>

            <div className="rounded-2xl border border-[#E4DCD1] bg-white overflow-hidden">
              <table className="w-full text-xs">
                <thead>
                  <tr className="bg-[#FBF9F6] text-[#8A7A87] text-left">
                    <th className="px-3 py-2.5 font-medium">สินค้า</th>
                    <th className="px-3 py-2.5 font-medium text-right">มูลค่า/ปี</th>
                    <th className="px-3 py-2.5 font-medium text-right">สัดส่วน</th>
                    <th className="px-3 py-2.5 font-medium text-right">สะสม</th>
                    <th className="px-3 py-2.5 font-medium text-center">Class</th>
                  </tr>
                </thead>
                <tbody>
                  {abcRows.map(({ p, value, pct, cumPct, cls, computedCls, overridden }) => (
                    <tr key={p.id} className="border-t border-[#EFE8E0]">
                      <td className="px-3 py-2.5">{p.name}</td>
                      <td className="px-3 py-2.5 text-right tabular">฿{fmt(value)}</td>
                      <td className="px-3 py-2.5 text-right tabular">{fmt(pct, 1)}%</td>
                      <td className="px-3 py-2.5 text-right tabular">{fmt(cumPct, 1)}%</td>
                      <td className="px-3 py-2.5 text-center">
                        <select
                          value={p.abcOverride || ""}
                          onChange={(e) => setAbcOverride(p.id, e.target.value)}
                          className={`rounded-md border-0 text-[11px] font-bold px-1.5 py-1 ${
                            cls === "A" ? "bg-[#FBEAE7] text-[#D6432C]" : cls === "B" ? "bg-[#FBF1DF] text-[#C98A22]" : "bg-[#E9F4EC] text-[#3F8A5E]"
                          }`}
                          title={overridden ? `ค่าที่คำนวณได้คือ ${computedCls} (แก้ไขเอง)` : "คำนวณอัตโนมัติ"}
                        >
                          <option value="">{computedCls} (อัตโนมัติ)</option>
                          <option value="A">A</option>
                          <option value="B">B</option>
                          <option value="C">C</option>
                        </select>
                      </td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>
          </div>
        )}

        {tab === "report" && (
          <div>
            <div className="rounded-2xl border border-[#E4DCD1] bg-white p-4 mb-4">
              <h2 className="font-display text-base font-semibold mb-1">สรุปการใช้วัตถุดิบ 30 วันล่าสุด</h2>
              <p className="text-xs text-[#8A7A87]">ตัวเลขนี้อ้างอิงจากยอดใช้เฉลี่ยที่บันทึกไว้ในระบบ (แก้ไขได้ที่ปุ่ม "แก้ไข" ของแต่ละสินค้า) — เมื่อเชื่อมกับ POS จริง ระบบจะคำนวณจากยอดขายจริงอัตโนมัติ</p>
            </div>
            <div className="grid sm:grid-cols-2 gap-2.5">
              {[...products].sort((a, b) => a.category.localeCompare(b.category)).map((p) => {
                const m = metricsById[p.id];
                return (
                  <div key={p.id} className="rounded-xl border border-[#E4DCD1] bg-white px-4 py-3 flex items-center justify-between">
                    <div>
                      <div className="text-sm font-medium">{p.name}</div>
                      <div className="text-[11px] text-[#8A7A87]">{p.category === "raw" ? "วัตถุดิบ" : "บรรจุภัณฑ์"}</div>
                    </div>
                    <div className="text-right">
                      <div className="font-display text-base font-semibold tabular">{fmt(m.packsUsedThisMonth, 1)} แพ็ค</div>
                      <div className="text-[11px] text-[#8A7A87] tabular">{fmt(p.avgMonthlyUsage)} {p.unit}</div>
                    </div>
                  </div>
                );
              })}
            </div>
          </div>
        )}

        {tab === "log" && (
          <div>
            <div className="rounded-2xl border border-[#E4DCD1] bg-white p-4 mb-4 flex gap-3">
              <Info size={16} className="text-[#4F9D8D] shrink-0 mt-0.5" />
              <p className="text-xs text-[#6B5B69] leading-relaxed">
                การส่งข้อความเข้า LINE โดยอัตโนมัติ (Push API) ต้องมีเซิร์ฟเวอร์กลางถือ Channel Access Token ไว้ เพราะเว็บฝั่งผู้ใช้ (browser) เก็บคีย์ลับอย่างปลอดภัยไม่ได้ — แนะนำต่อ Google Apps Script หรือ Cloud Function เป็นตัวกลางรับ webhook จากระบบนี้แล้วยิงเข้า LINE Messaging API อีกที ตอนนี้ปุ่มด้านล่างจะบันทึกประวัติการแจ้งเตือนไว้ในระบบ และเปิดลิงก์ LINE ให้ทันที
              </p>
            </div>
            <div className="space-y-2">
              {log.length === 0 && (
                <div className="rounded-2xl border border-dashed border-[#E4DCD1] py-10 text-center text-sm text-[#8A7A87]">ยังไม่มีประวัติ — ลองเบิกสินค้าหรือกดสั่งซื้อดูได้</div>
              )}
              {log.map((entry) => (
                <div key={entry.id} className="rounded-xl border border-[#E4DCD1] bg-white px-4 py-3 flex items-start gap-3">
                  <div className={`mt-0.5 flex h-7 w-7 shrink-0 items-center justify-center rounded-full ${
                    entry.type === "line_alert" ? "bg-[#FBEAE7] text-[#D6432C]" : entry.type === "edit" ? "bg-[#EFE8E0] text-[#6B5B69]" : "bg-[#E9F4EC] text-[#3F8A5E]"
                  }`}>
                    {entry.type === "line_alert" ? <Bell size={13} /> : entry.type === "edit" ? <Pencil size={13} /> : <MinusCircle size={13} />}
                  </div>
                  <div className="flex-1 min-w-0">
                    {entry.type === "requisition" ? (
                      <p className="text-sm">เบิก <span className="font-medium">{entry.productName}</span> จำนวน {fmt(entry.qty)} {entry.unit}{entry.note && <span className="text-[#8A7A87]"> — {entry.note}</span>}</p>
                    ) : (
                      <p className="text-sm">{entry.message}</p>
                    )}
                    <p className="text-[11px] text-[#8A7A87] mt-0.5 tabular">{new Date(entry.ts).toLocaleString("th-TH")}</p>
                  </div>
                  {entry.type === "line_alert" && (
                    <button onClick={() => window.open(LINE_LINK, "_blank")} className="shrink-0 flex items-center gap-1 rounded-full bg-[#EFE8E0] px-2.5 py-1 text-[11px] text-[#2B1E2A]">
                      <Send size={11} /> LINE
                    </button>
                  )}
                </div>
              ))}
            </div>
          </div>
        )}
      </main>

      {reqOpen && (
        <div className="fixed inset-0 z-40 flex items-end sm:items-center justify-center bg-black/40" onClick={() => setReqOpen(false)}>
          <div className="w-full sm:max-w-sm rounded-t-2xl sm:rounded-2xl bg-white p-5" onClick={(e) => e.stopPropagation()}>
            <div className="flex items-center justify-between mb-4">
              <h3 className="font-display text-base font-semibold">เบิกสินค้า</h3>
              <button onClick={() => setReqOpen(false)}><X size={18} className="text-[#8A7A87]" /></button>
            </div>
            <Field label="รายการ">
              <select value={reqProductId} onChange={(e) => setReqProductId(e.target.value)} className={inputCls}>
                {products.map((p) => (<option key={p.id} value={p.id}>{p.name} (คงเหลือ {fmt(p.onHand)} {p.unit})</option>))}
              </select>
            </Field>
            <Field label={`จำนวนที่เบิก (${products.find((p) => p.id === reqProductId)?.unit || ""})`}>
              <input type="number" min={1} value={reqQty} onChange={(e) => setReqQty(Number(e.target.value))} className={inputCls} />
            </Field>
            <Field label="หมายเหตุ (ไม่บังคับ)">
              <input type="text" value={reqNote} onChange={(e) => setReqNote(e.target.value)} placeholder="เช่น ใช้สำหรับออเดอร์เดลิเวอรี" className={inputCls} />
            </Field>
            <button onClick={submitRequisition} className="w-full rounded-full bg-[#2B1E2A] py-3 text-sm font-medium text-white">บันทึกการเบิก</button>
          </div>
        </div>
      )}

      {editorOpen && (
        <div className="fixed inset-0 z-40 flex items-end sm:items-center justify-center bg-black/40" onClick={() => setEditorOpen(false)}>
          <div className="w-full sm:max-w-md max-h-[90vh] overflow-y-auto rounded-t-2xl sm:rounded-2xl bg-white p-5" onClick={(e) => e.stopPropagation()}>
            <div className="flex items-center justify-between mb-4">
              <h3 className="font-display text-base font-semibold">{editorMode === "add" ? "เพิ่มสินค้าใหม่" : `แก้ไข "${draft.name}"`}</h3>
              <button onClick={() => setEditorOpen(false)}><X size={18} className="text-[#8A7A87]" /></button>
            </div>
            <Field label="ชื่อสินค้า">
              <input type="text" value={draft.name} onChange={(e) => setDraft((d) => ({ ...d, name: e.target.value }))} className={inputCls} />
            </Field>
            <div className="grid grid-cols-2 gap-3">
              <Field label="หมวดหมู่">
                <select value={draft.category} onChange={(e) => setDraft((d) => ({ ...d, category: e.target.value }))} className={inputCls}>
                  <option value="raw">วัตถุดิบ</option>
                  <option value="packaging">บรรจุภัณฑ์</option>
                </select>
              </Field>
              <Field label="หน่วย">
                <input type="text" value={draft.unit} onChange={(e) => setDraft((d) => ({ ...d, unit: e.target.value }))} className={inputCls} placeholder="g / ml / pcs" />
              </Field>
            </div>
            <div className="grid grid-cols-2 gap-3">
              <Field label="คงเหลือ (On hand)">
                <input type="number" value={draft.onHand} onChange={(e) => setDraft((d) => ({ ...d, onHand: e.target.value }))} className={inputCls} />
              </Field>
              <Field label="ใช้เฉลี่ยต่อเดือน">
                <input type="number" value={draft.avgMonthlyUsage} onChange={(e) => setDraft((d) => ({ ...d, avgMonthlyUsage: e.target.value }))} className={inputCls} />
              </Field>
            </div>
            <div className="grid grid-cols-2 gap-3">
              <Field label="ขนาดแพ็ค">
                <input type="number" value={draft.packSize} onChange={(e) => setDraft((d) => ({ ...d, packSize: e.target.value }))} className={inputCls} />
              </Field>
              <Field label="ราคาต่อแพ็ค (บาท)">
                <input type="number" value={draft.packCost} onChange={(e) => setDraft((d) => ({ ...d, packCost: e.target.value }))} className={inputCls} />
              </Field>
            </div>
            <div className="grid grid-cols-2 gap-3">
              <Field label="Lead Time (วัน)">
                <input type="number" value={draft.leadTimeDays} onChange={(e) => setDraft((d) => ({ ...d, leadTimeDays: e.target.value }))} className={inputCls} />
              </Field>
              <Field label="อายุสินค้า (วัน, ไม่บังคับ)">
                <input type="number" value={draft.shelfLifeDays} onChange={(e) => setDraft((d) => ({ ...d, shelfLifeDays: e.target.value }))} className={inputCls} placeholder="เช่น 7" />
              </Field>
            </div>
            <Field label="ผู้ขาย / Supplier">
              <input type="text" value={draft.supplier} onChange={(e) => setDraft((d) => ({ ...d, supplier: e.target.value }))} className={inputCls} />
            </Field>
            <button onClick={saveDraft} className="w-full rounded-full bg-[#2B1E2A] py-3 text-sm font-medium text-white mt-1">
              {editorMode === "add" ? "เพิ่มสินค้า" : "บันทึกการแก้ไข"}
            </button>
          </div>
        </div>
      )}

      {settingsOpen && (
        <div className="fixed inset-0 z-40 flex items-end sm:items-center justify-center bg-black/40" onClick={() => setSettingsOpen(false)}>
          <div className="w-full sm:max-w-sm rounded-t-2xl sm:rounded-2xl bg-white p-5" onClick={(e) => e.stopPropagation()}>
            <div className="flex items-center justify-between mb-4">
              <h3 className="font-display text-base font-semibold">ค่าตั้งต้นการคำนวณ</h3>
              <button onClick={() => setSettingsOpen(false)}><X size={18} className="text-[#8A7A87]" /></button>
            </div>
            {[
              { key: "orderCost", label: "ค่าใช้จ่ายต่อการสั่งซื้อ 1 ครั้ง (บาท)", step: 1 },
              { key: "holdingRate", label: "อัตราต้นทุนถือครองสต็อกต่อปี (0-1)", step: 0.01 },
              { key: "serviceZ", label: "Z-score ระดับบริการ (เช่น 1.65 = 95%)", step: 0.01 },
              { key: "demandCv", label: "ความแปรผันของยอดใช้ (0-1)", step: 0.01 },
            ].map((f) => (
              <Field key={f.key} label={f.label}>
                <input type="number" step={f.step} value={settings[f.key]} onChange={(e) => setSettings((s) => ({ ...s, [f.key]: Number(e.target.value) }))} className={inputCls} />
              </Field>
            ))}
            <button onClick={resetData} className="w-full flex items-center justify-center gap-1.5 rounded-full border border-[#E4DCD1] py-2.5 text-xs font-medium text-[#6B5B69] mt-1">
              <RotateCcw size={13} /> รีเซ็ตข้อมูลตัวอย่าง
            </button>
          </div>
        </div>
      )}

      {hasUpdates && (
        <div className="fixed bottom-0 left-0 right-0 z-40 border-t border-[#E4DCD1] bg-white px-4 py-3 flex items-center justify-between gap-3 shadow-[0_-4px_12px_rgba(0,0,0,0.06)]">
          <p className="text-xs text-[#6B5B69]">มีการอัปเดตข้อมูล — ยืนยันเพื่อดูสรุป PDF ล่าสุด</p>
          <button onClick={confirmAndViewSummary} className="shrink-0 flex items-center gap-1.5 rounded-full bg-[#2B1E2A] px-4 py-2 text-xs font-medium text-white">
            <FileText size={13} /> ยืนยัน & ดูสรุป
          </button>
        </div>
      )}

      {toast && (
        <div className="fixed bottom-20 left-1/2 -translate-x-1/2 z-50 max-w-[90%] rounded-full bg-[#2B1E2A] px-4 py-2.5 text-xs text-white shadow-lg">{toast}</div>
      )}
    </div>
  );
}

function Metric({ label, value }) {
  return (
    <div>
      <div className="text-[10px] text-[#8A7A87] mb-0.5">{label}</div>
      <div className="font-medium tabular">{value}</div>
    </div>
  );
}

function SummaryView({ brand, products, metricsById, abc, counts, totalValue, criticalItems, warningItems, alertLog, onBack }) {
  const now = new Date();
  const abcRows = [...products].map((p) => ({ p, ...abc[p.id] })).sort((a, b) => b.value - a.value);

  return (
    <div className="min-h-screen bg-[#E9E4DC] font-body text-[#2B1E2A]">
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Prompt:wght@500;600;700&family=IBM+Plex+Sans+Thai:wght@400;500;600;700&display=swap');
        .font-display { font-family: 'Prompt', sans-serif; }
        .font-body { font-family: 'IBM Plex Sans Thai', sans-serif; }
        .tabular { font-variant-numeric: tabular-nums; }
        @media print {
          .no-print { display: none !important; }
          body { background: white !important; }
          .print-page { box-shadow: none !important; margin: 0 !important; }
          * { -webkit-print-color-adjust: exact; print-color-adjust: exact; }
        }
      `}</style>

      <div className="no-print sticky top-0 z-30 border-b border-[#E4DCD1] bg-[#2B1E2A] text-white px-4 py-3 flex items-center justify-between">
        <button onClick={onBack} className="flex items-center gap-1.5 text-xs font-medium">
          <ArrowLeft size={15} /> กลับไปที่แอป
        </button>
        <button onClick={() => window.print()} className="flex items-center gap-1.5 rounded-full bg-[#E8A33D] px-4 py-2 text-xs font-medium text-[#2B1E2A]">
          <Printer size={14} /> พิมพ์ / บันทึกเป็น PDF
        </button>
      </div>

      <div className="print-page mx-auto max-w-3xl bg-white my-6 p-8 shadow-sm">
        <div className="flex items-center justify-between border-b-2 border-[#2B1E2A] pb-4 mb-5">
          <div>
            <h1 className="font-display text-2xl font-bold">{brand}</h1>
            <p className="text-xs text-[#8A7A87]">สรุปสต็อกสินค้า (Stock Summary Report)</p>
          </div>
          <div className="text-right text-xs text-[#8A7A87] tabular">
            <div>สร้างเมื่อ {now.toLocaleDateString("th-TH", { day: "2-digit", month: "long", year: "numeric" })}</div>
            <div>{now.toLocaleTimeString("th-TH")}</div>
          </div>
        </div>

        <div className="grid grid-cols-4 gap-3 mb-6">
          <div className="rounded-lg bg-[#FBEAE7] border border-[#D6432C]/30 px-3 py-2.5 text-center">
            <div className="text-2xl font-display font-bold text-[#D6432C] tabular">{counts.red}</div>
            <div className="text-[10px] text-[#D6432C] font-medium">ใกล้หมด</div>
          </div>
          <div className="rounded-lg bg-[#FBF1DF] border border-[#C98A22]/30 px-3 py-2.5 text-center">
            <div className="text-2xl font-display font-bold text-[#C98A22] tabular">{counts.yellow}</div>
            <div className="text-[10px] text-[#C98A22] font-medium">เริ่มน้อย</div>
          </div>
          <div className="rounded-lg bg-[#E9F4EC] border border-[#3F8A5E]/30 px-3 py-2.5 text-center">
            <div className="text-2xl font-display font-bold text-[#3F8A5E] tabular">{counts.green}</div>
            <div className="text-[10px] text-[#3F8A5E] font-medium">ปกติ</div>
          </div>
          <div className="rounded-lg bg-[#EFE8E0] border border-[#E4DCD1] px-3 py-2.5 text-center">
            <div className="text-2xl font-display font-bold tabular">฿{fmt(totalValue)}</div>
            <div className="text-[10px] text-[#6B5B69] font-medium">มูลค่าสต็อกรวม</div>
          </div>
        </div>

        {criticalItems.length > 0 && (
          <div className="mb-6">
            <h2 className="font-display text-sm font-bold text-[#D6432C] mb-2 flex items-center gap-1.5">
              <AlertTriangle size={15} /> รายการที่ต้องเติมด่วน ({criticalItems.length} รายการ)
            </h2>
            <table className="w-full text-xs border border-[#D6432C]/40 rounded-lg overflow-hidden">
              <thead>
                <tr className="bg-[#D6432C] text-white text-left">
                  <th className="px-2.5 py-2 font-semibold">สินค้า</th>
                  <th className="px-2.5 py-2 font-semibold text-right">คงเหลือ</th>
                  <th className="px-2.5 py-2 font-semibold text-right">Safety Stock</th>
                  <th className="px-2.5 py-2 font-semibold text-right">เหลือใช้ได้ (วัน)</th>
                  <th className="px-2.5 py-2 font-semibold">ผู้ขาย</th>
                </tr>
              </thead>
              <tbody>
                {criticalItems.map((p) => {
                  const m = metricsById[p.id];
                  return (
                    <tr key={p.id} className="bg-[#FBEAE7] border-t border-[#D6432C]/20">
                      <td className="px-2.5 py-2 font-bold text-[#D6432C]">{p.name}</td>
                      <td className="px-2.5 py-2 text-right font-bold text-[#D6432C] tabular">{fmt(p.onHand)} {p.unit}</td>
                      <td className="px-2.5 py-2 text-right tabular">{fmt(m.safetyStock)} {p.unit}</td>
                      <td className="px-2.5 py-2 text-right font-bold text-[#D6432C] tabular">{fmt(m.daysRemaining, 1)}</td>
                      <td className="px-2.5 py-2">{p.supplier || "-"}</td>
                    </tr>
                  );
                })}
              </tbody>
            </table>
          </div>
        )}

        {warningItems.length > 0 && (
          <div className="mb-6">
            <h2 className="font-display text-sm font-bold text-[#C98A22] mb-2">รายการเริ่มน้อย — ควรวางแผนสั่งซื้อ ({warningItems.length} รายการ)</h2>
            <table className="w-full text-xs border border-[#C98A22]/40 rounded-lg overflow-hidden">
              <thead>
                <tr className="bg-[#C98A22] text-white text-left">
                  <th className="px-2.5 py-2 font-semibold">สินค้า</th>
                  <th className="px-2.5 py-2 font-semibold text-right">คงเหลือ</th>
                  <th className="px-2.5 py-2 font-semibold text-right">ROP</th>
                  <th className="px-2.5 py-2 font-semibold text-right">เหลือใช้ได้ (วัน)</th>
                </tr>
              </thead>
              <tbody>
                {warningItems.map((p) => {
                  const m = metricsById[p.id];
                  return (
                    <tr key={p.id} className="bg-[#FBF1DF] border-t border-[#C98A22]/20">
                      <td className="px-2.5 py-2 font-medium">{p.name}</td>
                      <td className="px-2.5 py-2 text-right tabular">{fmt(p.onHand)} {p.unit}</td>
                      <td className="px-2.5 py-2 text-right tabular">{fmt(m.rop)} {p.unit}</td>
                      <td className="px-2.5 py-2 text-right tabular">{fmt(m.daysRemaining, 1)}</td>
                    </tr>
                  );
                })}
              </tbody>
            </table>
          </div>
        )}

        <div className="mb-6">
          <h2 className="font-display text-sm font-bold mb-2">รายการสินค้าทั้งหมด</h2>
          <table className="w-full text-[11px] border border-[#E4DCD1] rounded-lg overflow-hidden">
            <thead>
              <tr className="bg-[#2B1E2A] text-white text-left">
                <th className="px-2 py-1.5 font-semibold">สินค้า</th>
                <th className="px-2 py-1.5 font-semibold">หมวด</th>
                <th className="px-2 py-1.5 font-semibold text-right">คงเหลือ</th>
                <th className="px-2 py-1.5 font-semibold text-right">เหลือ (วัน)</th>
                <th className="px-2 py-1.5 font-semibold text-center">สถานะ</th>
                <th className="px-2 py-1.5 font-semibold text-center">ABC</th>
              </tr>
            </thead>
            <tbody>
              {[...products].sort((a, b) => metricsById[a.id].daysRemaining - metricsById[b.id].daysRemaining).map((p) => {
                const m = metricsById[p.id];
                const isRed = m.status === "red";
                const isYellow = m.status === "yellow";
                return (
                  <tr key={p.id} className={`border-t border-[#EFE8E0] ${isRed ? "bg-[#FBEAE7]" : isYellow ? "bg-[#FBF1DF]" : ""}`}>
                    <td className={`px-2 py-1.5 ${isRed ? "font-bold text-[#D6432C]" : ""}`}>{p.name}</td>
                    <td className="px-2 py-1.5 text-[#6B5B69]">{p.category === "raw" ? "วัตถุดิบ" : "บรรจุภัณฑ์"}</td>
                    <td className={`px-2 py-1.5 text-right tabular ${isRed ? "font-bold text-[#D6432C]" : ""}`}>{fmt(p.onHand)} {p.unit}</td>
                    <td className={`px-2 py-1.5 text-right tabular ${isRed ? "font-bold text-[#D6432C]" : ""}`}>{fmt(m.daysRemaining, 1)}</td>
                    <td className="px-2 py-1.5 text-center">
                      <span className={`font-bold ${isRed ? "text-[#D6432C]" : isYellow ? "text-[#C98A22]" : "text-[#3F8A5E]"}`}>
                        {isRed ? "ใกล้หมด" : isYellow ? "เริ่มน้อย" : "ปกติ"}
                      </span>
                    </td>
                    <td className="px-2 py-1.5 text-center">{abc[p.id]?.cls}</td>
                  </tr>
                );
              })}
            </tbody>
          </table>
        </div>

        <div className="mb-6">
          <h2 className="font-display text-sm font-bold mb-2">ABC Analysis</h2>
          <table className="w-full text-[11px] border border-[#E4DCD1] rounded-lg overflow-hidden">
            <thead>
              <tr className="bg-[#EFE8E0] text-[#6B5B69] text-left">
                <th className="px-2 py-1.5 font-semibold">สินค้า</th>
                <th className="px-2 py-1.5 font-semibold text-right">มูลค่า/ปี</th>
                <th className="px-2 py-1.5 font-semibold text-right">สัดส่วน</th>
                <th className="px-2 py-1.5 font-semibold text-right">สะสม</th>
                <th className="px-2 py-1.5 font-semibold text-center">Class</th>
              </tr>
            </thead>
            <tbody>
              {abcRows.map(({ p, value, pct, cumPct, cls }) => (
                <tr key={p.id} className="border-t border-[#EFE8E0]">
                  <td className="px-2 py-1.5">{p.name}</td>
                  <td className="px-2 py-1.5 text-right tabular">฿{fmt(value)}</td>
                  <td className="px-2 py-1.5 text-right tabular">{fmt(pct, 1)}%</td>
                  <td className="px-2 py-1.5 text-right tabular">{fmt(cumPct, 1)}%</td>
                  <td className="px-2 py-1.5 text-center font-bold">{cls}</td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>

        {alertLog.length > 0 && (
          <div className="mb-2">
            <h2 className="font-display text-sm font-bold mb-2">ประวัติการแจ้งเตือนล่าสุด ({alertLog.length} รายการ)</h2>
            <ul className="text-[11px] space-y-1.5">
              {alertLog.slice(0, 10).map((a) => (
                <li key={a.id} className="flex justify-between border-b border-dashed border-[#E4DCD1] pb-1">
                  <span>{a.message}</span>
                  <span className="text-[#8A7A87] tabular whitespace-nowrap ml-2">{new Date(a.ts).toLocaleDateString("th-TH")}</span>
                </li>
              ))}
            </ul>
          </div>
        )}

        <div className="mt-8 pt-3 border-t border-[#E4DCD1] text-center text-[10px] text-[#8A7A87]">
          สร้างโดยระบบ {brand} Stock Manager
        </div>
      </div>
    </div>
  );
}
