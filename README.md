import React, { useState, useEffect, useMemo, useCallback, useRef } from "react";
import {
  AlertTriangle,
  Bell,
  Package,
  TrendingDown,
  TrendingUp,
  ShoppingCart,
  MinusCircle,
  PlusCircle,
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
  Undo2,
  Check,
  Save,
  Copy,
  ImagePlus,
  ImageOff,
  Home,
  XCircle,
  MessageSquareText,
  CalendarClock,
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

const SEED_PRODUCTS_BASE = [
  { id: "milk", name: "Milk (นมสด)", category: "raw", unit: "ml", packSize: 2000, packCost: 86, leadTimeDays: 1, shelfLifeDays: 7, supplier: "ฟาร์มนมสด", onHand: 6000, avgMonthlyUsage: 40000, abcOverride: null, orderGroup: "milk", customOrderMessage: "" },
  { id: "yolida", name: "Yolida (หัวเชื้อโยเกิร์ต)", category: "raw", unit: "g", packSize: 225, packCost: 55, leadTimeDays: 2, shelfLifeDays: 14, supplier: "ผู้จำหน่ายหัวเชื้อ", onHand: 900, avgMonthlyUsage: 3375, abcOverride: null, orderGroup: "yolida", customOrderMessage: "" },
  { id: "biscoff", name: "Biscoff", category: "raw", unit: "g", packSize: 750, packCost: 285, leadTimeDays: 3, shelfLifeDays: 365, supplier: "ซัพพลายเออร์ทอปปิ้ง", onHand: 1500, avgMonthlyUsage: 1500, abcOverride: null, orderGroup: "shopee_tiktok", customOrderMessage: "" },
  { id: "cornflake", name: "Cornflake", category: "raw", unit: "g", packSize: 500, packCost: 145, leadTimeDays: 3, shelfLifeDays: 180, supplier: "ซัพพลายเออร์ทอปปิ้ง", onHand: 250, avgMonthlyUsage: 1000, abcOverride: null, orderGroup: "shopee_tiktok", customOrderMessage: "" },
  { id: "granola", name: "Granola", category: "raw", unit: "g", packSize: 500, packCost: 242, leadTimeDays: 3, shelfLifeDays: 180, supplier: "ซัพพลายเออร์ทอปปิ้ง", onHand: 1000, avgMonthlyUsage: 750, abcOverride: null, orderGroup: "shopee_tiktok", customOrderMessage: "" },
  { id: "almond", name: "Almond", category: "raw", unit: "g", packSize: 1000, packCost: 384, leadTimeDays: 5, shelfLifeDays: 365, supplier: "ซัพพลายเออร์ทอปปิ้ง", onHand: 500, avgMonthlyUsage: 600, abcOverride: null, orderGroup: "shopee_tiktok", customOrderMessage: "" },
  { id: "oreo", name: "Oreo", category: "raw", unit: "g", packSize: 454, packCost: 99, leadTimeDays: 3, shelfLifeDays: 270, supplier: "ซัพพลายเออร์ทอปปิ้ง", onHand: 908, avgMonthlyUsage: 908, abcOverride: null, orderGroup: "shopee_tiktok", customOrderMessage: "" },
  { id: "spoon", name: "Spoon (ช้อน)", category: "packaging", unit: "pcs", packSize: 100, packCost: 48, leadTimeDays: 7, shelfLifeDays: null, supplier: "ผู้ผลิตบรรจุภัณฑ์", onHand: 150, avgMonthlyUsage: 1200, abcOverride: null, orderGroup: "shopee_tiktok", customOrderMessage: "" },
  { id: "cup_pet", name: "Cup (PET)", category: "packaging", unit: "pcs", packSize: 50, packCost: 159, leadTimeDays: 7, shelfLifeDays: null, supplier: "ผู้ผลิตบรรจุภัณฑ์", onHand: 100, avgMonthlyUsage: 900, abcOverride: null, orderGroup: "shopee_tiktok", customOrderMessage: "" },
  { id: "sticker_ty", name: "Sticker (Thank you)", category: "packaging", unit: "pcs", packSize: 100, packCost: 43.78, leadTimeDays: 5, shelfLifeDays: null, supplier: "ร้านสติกเกอร์", onHand: 300, avgMonthlyUsage: 900, abcOverride: null, orderGroup: "sticker_print", customOrderMessage: "" },
  { id: "sauce_cup", name: "Sauce Cup", category: "packaging", unit: "pcs", packSize: 50, packCost: 27, leadTimeDays: 5, shelfLifeDays: null, supplier: "ผู้ผลิตบรรจุภัณฑ์", onHand: 200, avgMonthlyUsage: 400, abcOverride: null, orderGroup: "shopee_tiktok", customOrderMessage: "" },
  { id: "cup", name: "Cup", category: "packaging", unit: "pcs", packSize: 50, packCost: 50, leadTimeDays: 5, shelfLifeDays: null, supplier: "ผู้ผลิตบรรจุภัณฑ์", onHand: 150, avgMonthlyUsage: 600, abcOverride: null, orderGroup: "shopee_tiktok", customOrderMessage: "" },
  { id: "cooler_bag", name: "Cooler Bag", category: "packaging", unit: "pcs", packSize: 300, packCost: 3060, leadTimeDays: 10, shelfLifeDays: null, supplier: "ผู้ผลิตบรรจุภัณฑ์", onHand: 60, avgMonthlyUsage: 90, abcOverride: null, orderGroup: "shopee_tiktok", customOrderMessage: "" },
  { id: "delivery_bag", name: "Delivery Bag (craft paper)", category: "packaging", unit: "pcs", packSize: 25, packCost: 84, leadTimeDays: 5, shelfLifeDays: null, supplier: "ร้านกระดาษคราฟท์", onHand: 50, avgMonthlyUsage: 150, abcOverride: null, orderGroup: "shopee_tiktok", customOrderMessage: "" },
  { id: "bucket", name: "Bucket", category: "packaging", unit: "pcs", packSize: 1, packCost: 24.58, leadTimeDays: 7, shelfLifeDays: null, supplier: "ผู้ผลิตบรรจุภัณฑ์", onHand: 10, avgMonthlyUsage: 20, abcOverride: null, orderGroup: "shopee_tiktok", customOrderMessage: "" },
  { id: "bottle_pet", name: "Bottle PET", category: "packaging", unit: "pcs", packSize: 50, packCost: 120, leadTimeDays: 7, shelfLifeDays: null, supplier: "ผู้ผลิตบรรจุภัณฑ์", onHand: 100, avgMonthlyUsage: 300, abcOverride: null, orderGroup: "shopee_tiktok", customOrderMessage: "" },
  { id: "bottle_label", name: "Bottle Label", category: "packaging", unit: "pcs", packSize: 48, packCost: 102.5, leadTimeDays: 5, shelfLifeDays: null, supplier: "ร้านสติกเกอร์", onHand: 96, avgMonthlyUsage: 288, abcOverride: null, orderGroup: "sticker_print", customOrderMessage: "" },
  { id: "flavor_sticker", name: "Flavor Stickers", category: "packaging", unit: "pcs", packSize: 588, packCost: 102.5, leadTimeDays: 5, shelfLifeDays: null, supplier: "ร้านสติกเกอร์", onHand: 1176, avgMonthlyUsage: 1764, abcOverride: null, orderGroup: "sticker_print", customOrderMessage: "" },
  { id: "cup_takeaway", name: "Cup (Take away)", category: "packaging", unit: "pcs", packSize: 10, packCost: 125, leadTimeDays: 5, shelfLifeDays: null, supplier: "ผู้ผลิตบรรจุภัณฑ์", onHand: 20, avgMonthlyUsage: 60, abcOverride: null, orderGroup: "shopee_tiktok", customOrderMessage: "" },
  { id: "sticker_takeaway", name: "Sticker (Take away)", category: "packaging", unit: "pcs", packSize: 96, packCost: 59, leadTimeDays: 5, shelfLifeDays: null, supplier: "ร้านสติกเกอร์", onHand: 192, avgMonthlyUsage: 288, abcOverride: null, orderGroup: "sticker_print", customOrderMessage: "" },
];

const SEED_PRODUCTS = SEED_PRODUCTS_BASE.map((p, i) => {
  const expiry = p.shelfLifeDays ? addDaysToStr(todayStr(), p.shelfLifeDays) : null;
  return {
    sku: `${p.category === "raw" ? "RM" : "PK"}-${String(i + 1).padStart(3, "0")}`,
    physicalCount: p.onHand,
    ropOverride: null,
    safetyStockOverride: null,
    eoqOverride: null,
    imageUrl: null,
    lots: p.onHand > 0 ? [{ id: `${p.id}-lot-1`, qty: p.onHand, receivedDate: todayStr(), expiryDate: expiry, note: "สต็อกเริ่มต้น" }] : [],
    ...p,
  };
});

const MOVEMENT_TYPES = {
  received: { label: "รับเข้า (Received)", direction: "in" },
  transfer_in: { label: "โอนย้ายเข้า (Transfer In)", direction: "in" },
  transfer_out: { label: "โอนย้ายออก (Transfer Out)", direction: "out" },
  mark_out: { label: "ตัดจำหน่าย/เสียหาย (Mark out)", direction: "out" },
  sampling: { label: "แจกชิม/ตัวอย่าง (Sampling)", direction: "out" },
  expense_others: { label: "เบิกใช้อื่นๆ (Expense others)", direction: "out" },
  sales: { label: "ขาย (Sales)", direction: "out" },
};

const ORDER_GROUP_LABELS = {
  sticker_print: "สติกเกอร์ / กระดาษที่ต้องปริ้น",
  milk: "นม (โอนเงินหลังรับสินค้า)",
  yolida: "Yolida หัวเชื้อโยเกิร์ต (Makro)",
  shopee_tiktok: "ค่าเริ่มต้น — Shopee / TikTok Shop",
  custom: "กำหนดข้อความเอง",
};

const DEFAULT_ORDER_TEMPLATES = {
  sticker_print: "ปริ้นสติกเกอร์ PP มัน กันน้ำ A3+ ขนาด 2×1 ซม. (งานเดิม) 52 ชิ้น\nhttps://url.in.th/ZfKRc",
  milk: "สั่งซื้อนม ปริมาณ 2 ลิตร จำนวน 6 แกลลอน\nรอบสั่งซื้อ 00/00/00\nชื่อบัญชี บจก.ปโยสินพันธมิตรร่วมค้า 999\nเลขที่บัญชี 386-2-42097-7\nธนาคารทหารไทย สาขาสิงห์บุรี\n(หลังรับสินค้า โอนเงินเข้าบัญชีบริษัทฯ)",
  yolida: "สั่งซื้อใน Macro",
  shopee_tiktok: "สั่งซื้อใน Shopee app และ TikTok shop",
};

const DEFAULT_SETTINGS = {
  orderCost: 100,
  holdingRate: 0.2,
  serviceZ: 1.65,
  demandCv: 0.25,
  orderTemplates: DEFAULT_ORDER_TEMPLATES,
  lineWebhookUrl: "https://script.google.com/macros/s/AKfycbznT6_tkFRGyN4PR7lWsXhK3LF7CrEuwxhYTZd32njL2DxTZw6rS_CtWUWtMZcX0ozx0A/exec",
  lineUserId: "U24130dda7a1503e0d43974c559391ec4",
  lineAutoNotify: true,
  smsPhone: "0622659733",
  smsWebhookUrl: "",
  smsAutoNotify: false,
  pushNotifyEnabled: false,
  expiryWarnDays: 7,
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
  orderGroup: "shopee_tiktok",
  customOrderMessage: "",
  sku: "",
  physicalCount: 0,
  ropOverride: null,
  safetyStockOverride: null,
  eoqOverride: null,
  imageUrl: "",
  lots: [],
};

function getOrderMessage(product, settings) {
  if (product.orderGroup === "custom") return product.customOrderMessage || "(ยังไม่ได้กำหนดข้อความ)";
  return (settings.orderTemplates && settings.orderTemplates[product.orderGroup]) || DEFAULT_ORDER_TEMPLATES[product.orderGroup] || DEFAULT_ORDER_TEMPLATES.shopee_tiktok;
}

// สร้างข้อความสำหรับคัดลอก: ชื่อสินค้า, ซื้อที่ไหน, ปริมาณที่แนะนำสั่ง, ราคา ของแต่ละรายการในกลุ่ม ต่อท้ายด้วยข้อความสั่งซื้อของกลุ่มนั้น
function buildOrderCopyText(items, settings) {
  const lines = items.map((p) => {
    const m = computeMetrics(p, settings);
    const where = p.supplier?.trim() ? p.supplier : (ORDER_GROUP_LABELS[p.orderGroup] || "-");
    return [
      `ชื่อสินค้า: ${p.name}`,
      `ซื้อที่: ${where}`,
      `ปริมาณที่แนะนำสั่ง: ${fmt(m.eoq)} ${p.unit}`,
      `ราคา: ฿${fmt(m.costPerUnit, 2)}/${p.unit} (รวมประมาณ ฿${fmt(m.eoq * m.costPerUnit)})`,
    ].join("\n");
  }).join("\n\n");
  const msg = getOrderMessage(items[0], settings);
  return `${lines}\n\n${msg}`;
}

// รวมสินค้าที่ต้องสั่งซื้อทั้งหมด จัดกลุ่มตามช่องทาง แล้วต่อเป็นข้อความเดียวสำหรับส่ง SMS ทีเดียว
function buildAllOrdersText(items, settings) {
  if (items.length === 0) return "";
  const groups = {};
  items.forEach((p) => {
    const key = p.orderGroup || "shopee_tiktok";
    if (!groups[key]) groups[key] = [];
    groups[key].push(p);
  });
  return Object.entries(groups)
    .map(([key, groupItems], idx) => `${idx + 1}. ${ORDER_GROUP_LABELS[key] || key}\n${buildOrderCopyText(groupItems, settings)}`)
    .join("\n\n----------\n\n");
}

const DEFAULT_SMS_PHONE = "0622659733";

// เปิดแอปส่งข้อความของเครื่องพร้อมเบอร์และข้อความที่กรอกไว้ล่วงหน้า (ไม่ต้องมีเซิร์ฟเวอร์กลาง)
function buildSmsLink(phone, text) {
  const isIOS = typeof navigator !== "undefined" && /iPad|iPhone|iPod/.test(navigator.userAgent || "");
  const sep = isIOS ? "&" : "?";
  return `sms:${phone}${sep}body=${encodeURIComponent(text)}`;
}

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

function todayStr() {
  return new Date().toISOString().slice(0, 10);
}

function addDaysToStr(dateStr, days) {
  const d = new Date(dateStr);
  d.setDate(d.getDate() + days);
  return d.toISOString().slice(0, 10);
}

// วันจนถึงวันหมดอายุ (ลบได้ถ้าหมดอายุแล้ว) — null ถ้าไม่มีวันหมดอายุ
function daysUntil(dateStr) {
  if (!dateStr) return null;
  const today = new Date(); today.setHours(0, 0, 0, 0);
  const d = new Date(dateStr); d.setHours(0, 0, 0, 0);
  return Math.round((d - today) / 86400000);
}

// อายุสินค้าที่ค้างในสต็อก (วันตั้งแต่รับเข้า)
function daysSince(dateStr) {
  if (!dateStr) return null;
  const today = new Date(); today.setHours(0, 0, 0, 0);
  const d = new Date(dateStr); d.setHours(0, 0, 0, 0);
  return Math.round((today - d) / 86400000);
}

// เรียง Lot ตามหลัก FEFO: หมดอายุก่อนอยู่หน้าสุด, ไม่มีวันหมดอายุไปอยู่ท้ายสุด
function sortLotsFefo(lots) {
  return [...lots].sort((a, b) => {
    if (!a.expiryDate && !b.expiryDate) return new Date(a.receivedDate) - new Date(b.receivedDate);
    if (!a.expiryDate) return 1;
    if (!b.expiryDate) return -1;
    return new Date(a.expiryDate) - new Date(b.expiryDate);
  });
}

function totalLotQty(lots) {
  return (lots || []).reduce((s, l) => s + l.qty, 0);
}

// ตัดสต็อกออกตามหลัก FEFO (ของหมดอายุก่อนถูกใช้ก่อน) คืน lot ที่เหลือ + รายการที่ถูกใช้ + ส่วนที่ขาด (ถ้า lot ไม่พอ)
function deductFefo(lots, qty) {
  let remaining = qty;
  const sorted = sortLotsFefo(lots || []);
  const usedLots = [];
  const newLots = [];
  for (const lot of sorted) {
    if (remaining <= 0) { newLots.push(lot); continue; }
    if (lot.qty <= remaining + 1e-9) {
      remaining -= lot.qty;
      usedLots.push({ ...lot, usedQty: lot.qty });
    } else {
      usedLots.push({ ...lot, usedQty: remaining });
      newLots.push({ ...lot, qty: lot.qty - remaining });
      remaining = 0;
    }
  }
  return { newLots, usedLots, shortfall: Math.max(0, remaining) };
}

function addLotToList(lots, qty, expiryDate, note) {
  const id = `lot-${Date.now()}-${Math.random().toString(36).slice(2, 5)}`;
  return [...(lots || []), { id, qty, receivedDate: todayStr(), expiryDate: expiryDate || null, note: note || "" }];
}

// ย่อ/บีบอัดรูปก่อนเก็บ เพื่อให้ไฟล์เล็กพอสำหรับบันทึกลง storage
function compressImage(file, maxDim = 480, quality = 0.6) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onerror = () => reject(new Error("อ่านไฟล์ไม่สำเร็จ"));
    reader.onload = () => {
      const img = new Image();
      img.onerror = () => reject(new Error("โหลดรูปไม่สำเร็จ"));
      img.onload = () => {
        let { width, height } = img;
        if (width > height && width > maxDim) { height = Math.round((height * maxDim) / width); width = maxDim; }
        else if (height >= width && height > maxDim) { width = Math.round((width * maxDim) / height); height = maxDim; }
        const canvas = document.createElement("canvas");
        canvas.width = width; canvas.height = height;
        const ctx = canvas.getContext("2d");
        ctx.drawImage(img, 0, 0, width, height);
        resolve(canvas.toDataURL("image/jpeg", quality));
      };
      img.src = reader.result;
    };
    reader.readAsDataURL(file);
  });
}

function computeMetrics(p, settings) {
  const costPerUnit = p.packSize > 0 ? p.packCost / p.packSize : 0;
  const dailyUsage = p.avgMonthlyUsage / 30;
  const annualDemand = p.avgMonthlyUsage * 12;
  const annualValue = costPerUnit * annualDemand;
  const holdingCostPerUnit = costPerUnit * settings.holdingRate;
  const eoqComputed =
    holdingCostPerUnit > 0 && annualDemand > 0
      ? Math.sqrt((2 * annualDemand * settings.orderCost) / holdingCostPerUnit)
      : 0;
  const leadTimeDemand = dailyUsage * (p.leadTimeDays || 0);
  const demandStdDev = dailyUsage * settings.demandCv;
  const safetyStockComputed = settings.serviceZ * demandStdDev * Math.sqrt(p.leadTimeDays || 1);
  const ropComputed = leadTimeDemand + safetyStockComputed;

  const eoq = p.eoqOverride ?? eoqComputed;
  const safetyStock = p.safetyStockOverride ?? safetyStockComputed;
  const rop = p.ropOverride ?? ropComputed;

  const daysRemaining = dailyUsage > 0 ? p.onHand / dailyUsage : Infinity;
  const packsUsedThisMonth = p.packSize > 0 ? p.avgMonthlyUsage / p.packSize : 0;
  const stockValue = costPerUnit * p.onHand;

  let status = "green";
  if (p.onHand <= 0) status = "out";
  else if (p.onHand <= safetyStock) status = "red";
  else if (p.onHand <= rop) status = "yellow";

  return {
    costPerUnit, dailyUsage, annualDemand, annualValue, eoq, eoqComputed,
    leadTimeDemand, safetyStock, safetyStockComputed, rop, ropComputed,
    daysRemaining, status, packsUsedThisMonth, stockValue,
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
  out: { label: "หมดแล้ว", dot: "bg-[#7A140A]", text: "text-[#7A140A]", bg: "bg-[#7A140A]/10", ring: "ring-[#7A140A]/40" },
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
  const [reqDirection, setReqDirection] = useState("out"); // "out" = เบิก/ลด, "in" = รับเข้า/เพิ่ม
  const [reqMovementType, setReqMovementType] = useState("sales");
  const [reqProductId, setReqProductId] = useState(SEED_PRODUCTS[0].id);
  const [reqQty, setReqQty] = useState(1);
  const [reqNote, setReqNote] = useState("");
  const [reqImage, setReqImage] = useState("");
  const [reqExpiryDate, setReqExpiryDate] = useState("");
  const [settingsOpen, setSettingsOpen] = useState(false);
  const [toast, setToast] = useState(null);
  const [toastUndo, setToastUndo] = useState(null); // fn to undo the last toasted action
  const [editorOpen, setEditorOpen] = useState(false);
  const [editorMode, setEditorMode] = useState("add");
  const [draft, setDraft] = useState(EMPTY_DRAFT);
  const [hasUpdates, setHasUpdates] = useState(false);
  const [showSummary, setShowSummary] = useState(false);
  const [undoStack, setUndoStack] = useState([]); // [{id, label, products, log}]
  const [editingStockId, setEditingStockId] = useState(null);
  const [editingStockValue, setEditingStockValue] = useState("");
  const [saveStatus, setSaveStatus] = useState("idle"); // idle | saving | saved | error
  const [notifiedCriticalIds, setNotifiedCriticalIds] = useState([]); // productId ที่แจ้งเตือนสต็อกหมด/ใกล้หมดไปแล้ว (จนกว่าจะกลับมาปกติ)
  const [notifiedLotIds, setNotifiedLotIds] = useState([]); // lot id ที่แจ้งเตือนวันหมดอายุไปแล้ว
  const [notifPermission, setNotifPermission] = useState(typeof Notification !== "undefined" ? Notification.permission : "unsupported");
  const [lastSavedAt, setLastSavedAt] = useState(null);

  async function withRetry(fn, retries = 2) {
    let lastErr;
    for (let i = 0; i <= retries; i++) {
      try {
        return await fn();
      } catch (e) {
        lastErr = e;
        await new Promise((r) => setTimeout(r, 300 * (i + 1)));
      }
    }
    throw lastErr;
  }

  const loadAll = useCallback(async () => {
    try {
      const p = await withRetry(() => window.storage.get("fy-products", false));
      if (p) setProducts(JSON.parse(p.value));
    } catch (e) { /* no saved data yet — using default items */ }
    try {
      const s = await withRetry(() => window.storage.get("fy-settings", false));
      if (s) setSettings(JSON.parse(s.value));
    } catch (e) { /* no saved settings yet */ }
    try {
      const l = await withRetry(() => window.storage.get("fy-log", false));
      if (l) setLog(JSON.parse(l.value));
    } catch (e) { /* no saved log yet */ }
    try {
      const n = await withRetry(() => window.storage.get("fy-notified-lots", false));
      if (n) setNotifiedLotIds(JSON.parse(n.value));
    } catch (e) { /* none yet */ }
    setLoaded(true);
  }, []);

  useEffect(() => { loadAll(); }, [loadAll]);

  // บันทึกลง storage ทันทีที่ข้อมูลเปลี่ยน พร้อมลองใหม่อัตโนมัติหากล้มเหลว และแสดงสถานะให้เห็นชัดเจน
  const persist = useCallback(async (key, value) => {
    setSaveStatus("saving");
    try {
      await withRetry(() => window.storage.set(key, JSON.stringify(value), false));
      setSaveStatus("saved");
      setLastSavedAt(new Date());
      return true;
    } catch (e) {
      setSaveStatus("error");
      return false;
    }
  }, []);

  useEffect(() => { if (loaded) persist("fy-products", products); }, [products, loaded, persist]);
  useEffect(() => { if (loaded) persist("fy-settings", settings); }, [settings, loaded, persist]);
  useEffect(() => { if (loaded) persist("fy-log", log); }, [log, loaded, persist]);
  useEffect(() => { if (loaded) persist("fy-notified-lots", notifiedLotIds); }, [notifiedLotIds, loaded, persist]);

  function retryPersistNow() {
    persist("fy-products", products);
    persist("fy-settings", settings);
    persist("fy-log", log);
  }

  useEffect(() => {
    if (!toast) return;
    const t = setTimeout(() => { setToast(null); setToastUndo(null); }, 4500);
    return () => clearTimeout(t);
  }, [toast]);

  const metricsById = useMemo(() => {
    const m = {};
    products.forEach((p) => { m[p.id] = computeMetrics(p, settings); });
    return m;
  }, [products, settings]);

  const abc = useMemo(() => classifyAbc(products, settings), [products, settings]);

  const counts = useMemo(() => {
    const c = { out: 0, red: 0, yellow: 0, green: 0 };
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

  // เก็บสถานะก่อนหน้าไว้ในสแตก เพื่อให้กด "ย้อนกลับ" ได้ทีละขั้น (สูงสุด 30 ขั้น)
  function pushUndo(label) {
    setUndoStack((st) => [{ id: `${Date.now()}-${Math.random().toString(36).slice(2, 5)}`, label, products, log }, ...st].slice(0, 30));
  }

  function undoLast() {
    setUndoStack((st) => {
      if (st.length === 0) return st;
      const [top, ...rest] = st;
      setProducts(top.products);
      setLog(top.log);
      setToast(`ย้อนกลับแล้ว: ${top.label}`);
      setToastUndo(null);
      setHasUpdates(true);
      return rest;
    });
  }

  function submitStockAdjust() {
    const p = products.find((x) => x.id === reqProductId);
    if (!p || reqQty <= 0) return;
    const typeLabel = MOVEMENT_TYPES[reqMovementType]?.label || (reqDirection === "out" ? "เบิก/ลด" : "เพิ่ม/รับเข้า");
    const label = `${typeLabel} ${p.name} ${fmt(reqQty)} ${p.unit}`;
    pushUndo(label);
    const before = metricsById[p.id];
    const newOnHand = reqDirection === "out" ? Math.max(0, p.onHand - reqQty) : p.onHand + reqQty;

    let newLots = p.lots || [];
    let fefoNote = "";
    if (reqDirection === "out") {
      const { newLots: nl, usedLots, shortfall } = deductFefo(p.lots, reqQty);
      newLots = nl;
      if (usedLots.length > 0) {
        fefoNote = " — ตัดสต็อกตาม FEFO จาก " + usedLots.map((l) => `Lot ${l.expiryDate ? `หมดอายุ ${l.expiryDate}` : "ไม่ระบุวันหมดอายุ"} (${fmt(l.usedQty)} ${p.unit})`).join(", ");
      }
      if (shortfall > 0) fefoNote += ` (ไม่มีข้อมูล Lot ครอบคลุมอีก ${fmt(shortfall)} ${p.unit})`;
    } else {
      newLots = addLotToList(p.lots, reqQty, reqExpiryDate || null, typeLabel);
    }

    setProducts((ps) => ps.map((x) => (x.id === p.id ? { ...x, onHand: newOnHand, lots: newLots } : x)));
    const afterMetrics = computeMetrics({ ...p, onHand: newOnHand }, settings);
    addLog({
      type: "stock_adjust", direction: reqDirection, movementType: reqMovementType, productId: p.id, productName: p.name, qty: reqQty, unit: p.unit,
      note: reqNote + fefoNote, onHandAfter: newOnHand, statusBefore: before.status, statusAfter: afterMetrics.status, imageUrl: reqImage || null,
    });
    if (reqDirection === "out" && afterMetrics.status !== "green") {
      const orderMsg = getOrderMessage(p, settings);
      const alertMsg = `${p.name} เหลือ ${fmt(newOnHand)} ${p.unit} (${STATUS_META[afterMetrics.status].label}, ถึงจุดสั่งซื้อ ROP แล้ว) — ${orderMsg}`;
      addLog({
        type: "line_alert", productId: p.id, productName: p.name,
        message: alertMsg,
        status: afterMetrics.status,
      });
      sendLineNotify(alertMsg);
    }
    setToast(`${reqDirection === "out" ? "เบิก" : "เพิ่ม"} ${p.name} จำนวน ${reqQty} ${p.unit} แล้ว — คงเหลือ ${fmt(newOnHand)} ${p.unit}`);
    setToastUndo(() => undoLast);
    setReqOpen(false); setReqQty(1); setReqNote(""); setReqImage(""); setReqExpiryDate(""); setHasUpdates(true);
  }

  function startInlineEdit(p) {
    setEditingStockId(p.id);
    setEditingStockValue(String(p.onHand));
  }

  function saveInlineEdit(p) {
    const newVal = Number(editingStockValue);
    if (Number.isNaN(newVal) || newVal < 0) { setToast("กรุณาใส่จำนวนที่ถูกต้อง"); return; }
    if (newVal === p.onHand) { setEditingStockId(null); return; }
    pushUndo(`แก้ไขยอดคงเหลือ ${p.name} จาก ${fmt(p.onHand)} เป็น ${fmt(newVal)} ${p.unit}`);
    const newLots = newVal > p.onHand
      ? addLotToList(p.lots, newVal - p.onHand, null, "ปรับยอดคงเหลือโดยตรง")
      : deductFefo(p.lots, p.onHand - newVal).newLots;
    setProducts((ps) => ps.map((x) => (x.id === p.id ? { ...x, onHand: newVal, lots: newLots } : x)));
    addLog({
      type: "stock_adjust", direction: newVal > p.onHand ? "in" : "out", productId: p.id, productName: p.name,
      qty: Math.abs(newVal - p.onHand), unit: p.unit, note: "แก้ไขยอดคงเหลือโดยตรง", onHandAfter: newVal,
    });
    const afterMetrics = computeMetrics({ ...p, onHand: newVal }, settings);
    if (afterMetrics.status !== "green") {
      const orderMsg = getOrderMessage(p, settings);
      const alertMsg = `${p.name} เหลือ ${fmt(newVal)} ${p.unit} (${STATUS_META[afterMetrics.status].label}, ถึงจุดสั่งซื้อ ROP แล้ว) — ${orderMsg}`;
      addLog({
        type: "line_alert", productId: p.id, productName: p.name,
        message: alertMsg,
        status: afterMetrics.status,
      });
      sendLineNotify(alertMsg);
    }
    setToast(`บันทึกยอดคงเหลือ ${p.name} เป็น ${fmt(newVal)} ${p.unit} แล้ว`);
    setToastUndo(() => undoLast);
    setEditingStockId(null); setHasUpdates(true);
  }

  // ใช้แก้ไขฟิลด์ใดก็ได้ของสินค้าแบบอินไลน์ (ROP, Safety Stock, EOQ, Lead Time, ยอดใช้, ต้นทุน, ผู้ขาย ฯลฯ)
  // พร้อมย้อนกลับได้เสมอ
  function updateProductField(p, patch, label) {
    pushUndo(label);
    setProducts((ps) => ps.map((x) => (x.id === p.id ? { ...x, ...patch } : x)));
    addLog({ type: "edit", productId: p.id, productName: p.name, message: label });
    setToast(`${label} แล้ว`);
    setToastUndo(() => undoLast);
    setHasUpdates(true);
  }

  // ส่งข้อความแจ้งเตือนเข้า LINE จริงผ่าน Google Apps Script webhook ที่ผู้ใช้ตั้งค่าไว้
  async function sendLineNotify(message) {
    if (!settings.lineAutoNotify || !settings.lineWebhookUrl || !settings.lineUserId) return;
    try {
      await fetch(settings.lineWebhookUrl, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ userId: settings.lineUserId, message }),
      });
    } catch (e) {
      // ส่งไม่สำเร็จ (เช่น เน็ตขัดข้อง) — ข้อความยังถูกบันทึกไว้ในประวัติในแอปตามปกติ
    }
  }

  // ส่ง SMS จริงผ่าน webhook ของผู้ให้บริการ SMS ที่ผู้ใช้ตั้งค่าไว้ (ถ้ามี) — ไม่งั้น sms: link ยังใช้เป็นทางเลือกสำรองได้
  async function sendSmsNotify(message) {
    if (!settings.smsAutoNotify || !settings.smsWebhookUrl || !settings.smsPhone) return false;
    try {
      const res = await fetch(settings.smsWebhookUrl, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ phone: settings.smsPhone, message }),
      });
      return res.ok;
    } catch (e) {
      return false;
    }
  }

  // แจ้งเตือนแบบ Push ผ่านเบราว์เซอร์ (Web Notification API) — ใช้ได้เมื่อเปิดแท็บนี้ทิ้งไว้และได้รับอนุญาตแล้วเท่านั้น
  // บน iPad/iPhone (Safari) หรือเมื่อเปิดผ่านหน้าต่างแชทที่ฝังอยู่ มักใช้ไม่ได้เลยเพราะข้อจำกัดของแพลตฟอร์ม — ดูคำอธิบายในหน้าตั้งค่า
  function notifyBrowser(title, body) {
    try {
      if (typeof Notification === "undefined") return false;
      if (Notification.permission !== "granted") return false;
      new Notification(title, { body, icon: undefined });
      return true;
    } catch (e) {
      return false;
    }
  }

  function copyMessage(text) {
    if (navigator.clipboard && navigator.clipboard.writeText) {
      navigator.clipboard.writeText(text).then(
        () => setToast("คัดลอกข้อความสั่งซื้อแล้ว — วางในแชทหรือเอกสารได้เลย"),
        () => setToast("คัดลอกไม่สำเร็จ ลองเลือกข้อความด้วยตนเอง")
      );
    } else {
      setToast("อุปกรณ์นี้ไม่รองรับการคัดลอกอัตโนมัติ");
    }
  }

  function resetData() {
    setProducts(SEED_PRODUCTS); setSettings(DEFAULT_SETTINGS); setLog([]); setUndoStack([]);
    setToast("รีเซ็ตข้อมูลตัวอย่างเรียบร้อย"); setToastUndo(null); setHasUpdates(false);
  }

  function openAddEditor() {
    setDraft({ ...EMPTY_DRAFT }); setEditorMode("add"); setEditorOpen(true);
  }
  function openEditEditor(p) {
    setDraft({ ...p, shelfLifeDays: p.shelfLifeDays ?? "" }); setEditorMode("edit"); setEditorOpen(true);
  }
  function deleteProduct(p) {
    if (!window.confirm(`ลบ "${p.name}" ออกจากระบบ?`)) return;
    pushUndo(`ลบสินค้า "${p.name}"`);
    setProducts((ps) => ps.filter((x) => x.id !== p.id));
    addLog({ type: "edit", productId: p.id, productName: p.name, message: `ลบสินค้า "${p.name}" ออกจากระบบ` });
    setToast(`ลบ "${p.name}" แล้ว`);
    setToastUndo(() => undoLast);
    setExpanded(null); setHasUpdates(true);
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
      physicalCount: draft.physicalCount === "" || draft.physicalCount === undefined ? Number(draft.onHand) || 0 : Number(draft.physicalCount) || 0,
    };
    if (editorMode === "add") {
      pushUndo(`เพิ่มสินค้า "${cleaned.name}"`);
      const id = slugify(cleaned.name) + "-" + Math.random().toString(36).slice(2, 5);
      if (!cleaned.sku.trim()) cleaned.sku = `${cleaned.category === "raw" ? "RM" : "PK"}-${String(products.length + 1).padStart(3, "0")}`;
      setProducts((ps) => [...ps, { ...cleaned, id }]);
      addLog({ type: "edit", productId: id, productName: cleaned.name, message: `เพิ่มสินค้าใหม่ "${cleaned.name}"` });
      setToast(`เพิ่ม "${cleaned.name}" แล้ว`);
    } else {
      pushUndo(`แก้ไขข้อมูล "${cleaned.name}"`);
      setProducts((ps) => ps.map((x) => (x.id === cleaned.id ? cleaned : x)));
      addLog({ type: "edit", productId: cleaned.id, productName: cleaned.name, message: `แก้ไขข้อมูล "${cleaned.name}"` });
      setToast(`บันทึกการแก้ไข "${cleaned.name}" แล้ว`);
    }
    setToastUndo(() => undoLast);
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

  const criticalItems = products.filter((p) => metricsById[p.id].status === "red" || metricsById[p.id].status === "out");
  const outOfStockItems = products.filter((p) => metricsById[p.id].status === "out");
  const warningItems = products.filter((p) => metricsById[p.id].status === "yellow");
  const alertLog = log.filter((l) => l.type === "line_alert");

  const movementTotals = useMemo(() => {
    const totals = {};
    products.forEach((p) => {
      totals[p.id] = { received: 0, transfer_in: 0, transfer_out: 0, mark_out: 0, sampling: 0, expense_others: 0, sales: 0 };
    });
    log.forEach((entry) => {
      if (entry.type === "stock_adjust" && entry.movementType && totals[entry.productId]) {
        totals[entry.productId][entry.movementType] = (totals[entry.productId][entry.movementType] || 0) + (entry.qty || 0);
      }
    });
    return totals;
  }, [log, products]);

  const allLots = useMemo(() => {
    const rows = [];
    products.forEach((p) => {
      (p.lots || []).forEach((lot) => {
        if (lot.qty <= 0) return;
        rows.push({ ...lot, product: p });
      });
    });
    return sortLotsFefo(rows);
  }, [products]);

  const expiringSoonCount = useMemo(
    () => allLots.filter((l) => l.expiryDate && daysUntil(l.expiryDate) !== null && daysUntil(l.expiryDate) <= (settings.expiryWarnDays ?? 7)).length,
    [allLots, settings.expiryWarnDays]
  );

  function requestPushPermission() {
    if (typeof Notification === "undefined") { setNotifPermission("unsupported"); return; }
    Notification.requestPermission().then((perm) => setNotifPermission(perm));
  }

  // ปุ่ม "แจ้งเตือนตอนนี้" — สรุปสต็อกหมด/ใกล้หมด + ล็อตใกล้หมดอายุ ส่งทั้ง Push (ถ้าได้รับอนุญาต) และ LINE (ช่องทางที่ใช้งานได้จริงบน iPad วันนี้)
  function notifyNow() {
    const expiringLots = allLots.filter((l) => l.expiryDate && daysUntil(l.expiryDate) !== null && daysUntil(l.expiryDate) <= (settings.expiryWarnDays ?? 7));
    if (criticalItems.length === 0 && expiringLots.length === 0) {
      setToast("ตอนนี้ไม่มีสินค้าหมด/ใกล้หมด หรือใกล้หมดอายุ");
      return;
    }
    const parts = [];
    if (criticalItems.length > 0) parts.push(`สต็อกหมด/ใกล้หมด ${criticalItems.length} รายการ: ${criticalItems.map((p) => p.name).join(", ")}`);
    if (expiringLots.length > 0) parts.push(`ใกล้หมดอายุ ${expiringLots.length} ล็อต: ${expiringLots.map((l) => `${l.product.name} (${l.expiryDate})`).join(", ")}`);
    const body = parts.join("\n");
    const pushed = notifyBrowser(`${BRAND} — แจ้งเตือนสต็อก`, body);
    sendLineNotify(`🔔 แจ้งเตือนสต็อก\n${body}`);
    addLog({ type: "line_alert", message: `🔔 แจ้งเตือนสต็อกด้วยตนเอง — ${body}`, status: "order" });
    setToast(pushed ? "ส่งแจ้งเตือนแล้ว (Push + LINE)" : "ส่งแจ้งเตือนผ่าน LINE แล้ว (Push บนอุปกรณ์นี้ใช้ไม่ได้ — ดูวิธีตั้งค่าที่หน้าตั้งค่า)");
    setHasUpdates(true);
  }

  // แจ้งเตือนอัตโนมัติเมื่อมีสินค้ากลายเป็นสถานะวิกฤต (หมด/ใกล้หมด) รายการใหม่ที่ยังไม่เคยแจ้ง
  useEffect(() => {
    if (!loaded) return;
    const currentIds = criticalItems.map((p) => p.id);
    const newOnes = criticalItems.filter((p) => !notifiedCriticalIds.includes(p.id));
    if (newOnes.length > 0) {
      const body = newOnes.map((p) => `${p.name} (${STATUS_META[metricsById[p.id].status].label})`).join(", ");
      notifyBrowser(`${BRAND} — สต็อกหมด/ใกล้หมด`, body);
      if (settings.pushNotifyEnabled) sendLineNotify(`🔔 สต็อกหมด/ใกล้หมด: ${body}`);
    }
    // เก็บเฉพาะ id ที่ยังวิกฤตอยู่ + ของใหม่ที่เพิ่งแจ้ง (ของที่กลับมาปกติแล้วจะถูกลบออก ทำให้แจ้งซ้ำได้ใหม่ถ้าวิกฤตอีกครั้ง)
    setNotifiedCriticalIds(currentIds);
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [criticalItems.map((p) => p.id).join(","), loaded]);

  // แจ้งเตือนอัตโนมัติเมื่อมีล็อตใกล้หมดอายุ/หมดอายุแล้วที่ยังไม่เคยแจ้ง
  useEffect(() => {
    if (!loaded) return;
    const expiring = allLots.filter((l) => l.expiryDate && daysUntil(l.expiryDate) !== null && daysUntil(l.expiryDate) <= (settings.expiryWarnDays ?? 7));
    const newOnes = expiring.filter((l) => !notifiedLotIds.includes(l.id));
    if (newOnes.length > 0) {
      const body = newOnes.map((l) => {
        const d = daysUntil(l.expiryDate);
        return `${l.product.name} ${d < 0 ? `หมดอายุแล้ว ${Math.abs(d)} วัน` : `เหลืออีก ${d} วัน`}`;
      }).join(", ");
      notifyBrowser(`${BRAND} — สินค้าใกล้หมดอายุ`, body);
      if (settings.pushNotifyEnabled) sendLineNotify(`⏰ สินค้าใกล้หมดอายุ: ${body}`);
      setNotifiedLotIds((ids) => [...new Set([...ids, ...newOnes.map((l) => l.id)])]);
    }
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [allLots.map((l) => l.id).join(","), loaded]);

  function goHome() {
    setShowSummary(false);
    setTab("dashboard");
    setReqOpen(false);
    setEditorOpen(false);
    setSettingsOpen(false);
    setExpanded(null);
    setStatusFilter("all");
    setCategoryFilter("all");
  }

  function quickAdjustFromSummary(productId, direction) {
    setReqProductId(productId);
    setReqDirection(direction);
    setReqMovementType(direction === "out" ? "sales" : "received");
    setShowSummary(false);
    setTab("dashboard");
    setReqOpen(true);
  }

  if (showSummary) {
    return (
      <SummaryView
        brand={BRAND}
        products={products}
        settings={settings}
        metricsById={metricsById}
        abc={abc}
        counts={counts}
        totalValue={totalValue}
        criticalItems={criticalItems}
        warningItems={warningItems}
        alertLog={alertLog}
        movementTotals={movementTotals}
        undoStack={undoStack}
        undoLast={undoLast}
        saveStatus={saveStatus}
        lastSavedAt={lastSavedAt}
        onRetrySave={retryPersistNow}
        onQuickAdjust={quickAdjustFromSummary}
        onBack={() => { setShowSummary(false); setTab("dashboard"); }}
      />
    );
  }

  return (
    <div className="min-h-screen bg-[#F6F3EF] font-body text-black">
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Prompt:wght@500;600;700&family=IBM+Plex+Sans+Thai:wght@400;500;600;700&display=swap');
        .font-display { font-family: 'Prompt', sans-serif; }
        .font-body { font-family: 'IBM Plex Sans Thai', sans-serif; }
        .tabular { font-variant-numeric: tabular-nums; }
      `}</style>

      <header className="sticky top-0 z-30 border-b border-[#E4DCD1] bg-[#2B1E2A] text-[#F6F3EF]">
        <div className="mx-auto max-w-6xl px-4 py-4 flex items-center justify-between">
          <h1 className="font-display text-lg font-semibold leading-tight">{BRAND}</h1>
          <div className="flex items-center gap-2">
            <button
              onClick={goHome}
              title="กลับหน้าแรก"
              className="flex items-center justify-center rounded-full border border-[#4A3549] text-[#D9C9DA] w-8 h-8"
            >
              <Home size={14} />
            </button>
            {criticalItems.length > 0 ? (
              <a
                href={buildSmsLink(settings.smsPhone || DEFAULT_SMS_PHONE, buildAllOrdersText(criticalItems, settings))}
                onClick={async () => {
                  const text = buildAllOrdersText(criticalItems, settings);
                  addLog({ type: "line_alert", message: `เตรียมข้อความสั่งซื้อสินค้าที่หมด/ใกล้หมด ${criticalItems.length} รายการ ส่งผ่าน SMS`, status: "order" });
                  setHasUpdates(true);
                  if (settings.smsAutoNotify && settings.smsWebhookUrl) {
                    const ok = await sendSmsNotify(text);
                    setToast(ok ? `ส่ง SMS สำเร็จผ่าน Webhook แล้ว` : `ส่งผ่าน Webhook ไม่สำเร็จ — เปิดแอปข้อความให้แทน กดส่งเองได้เลย`);
                  } else {
                    setToast(`เปิดข้อความ SMS พร้อมสั่งซื้อ ${criticalItems.length} รายการแล้ว — ต้องกด "ส่ง" เองในแอปข้อความ`);
                  }
                }}
                title={`สั่งซื้อด่วน ${criticalItems.length} รายการ — แตะเพื่อเปิด SMS พร้อมส่ง`}
                className="relative flex items-center justify-center rounded-full border border-[#4F9D8D] bg-[#4F9D8D] text-white w-9 h-9"
              >
                <MessageSquareText size={16} />
                <span className="absolute -top-1.5 -right-1.5 flex h-4 min-w-4 items-center justify-center rounded-full bg-[#D6432C] px-1 text-[9px] font-bold text-white tabular ring-2 ring-[#2B1E2A]">
                  {criticalItems.length}
                </span>
              </a>
            ) : (
              <button
                disabled
                title="ยังไม่มีสินค้าที่ต้องสั่งซื้อตอนนี้"
                className="flex items-center justify-center rounded-full border border-[#4A3549] text-[#6B5B69] opacity-50 w-9 h-9"
              >
                <MessageSquareText size={16} />
              </button>
            )}
            <button
              onClick={() => { notifyNow(); setTab("log"); }}
              title={criticalItems.length > 0 ? `แตะเพื่อส่งแจ้งเตือนตอนนี้ — ${criticalItems.length} รายการต้องสั่งซื้อด่วน` : "แตะเพื่อส่งแจ้งเตือนตอนนี้"}
              className={`relative flex items-center justify-center rounded-full border w-8 h-8 ${
                criticalItems.length > 0 || expiringSoonCount > 0 ? "border-[#D6432C] bg-[#D6432C] text-white" : "border-[#4A3549] text-[#D9C9DA]"
              }`}
            >
              <Bell size={14} />
              {(alertLog.length > 0 || expiringSoonCount > 0) && (
                <span className="absolute -top-1.5 -right-1.5 flex h-4 min-w-4 items-center justify-center rounded-full bg-[#D6432C] px-1 text-[9px] font-bold text-white tabular ring-2 ring-[#2B1E2A]">
                  {criticalItems.length + expiringSoonCount}
                </span>
              )}
            </button>
            <button
              onClick={undoLast}
              disabled={undoStack.length === 0}
              title={undoStack.length > 0 ? `ย้อนกลับ: ${undoStack[0].label}` : "ไม่มีรายการให้ย้อนกลับ"}
              className={`flex items-center gap-1.5 rounded-full border px-3 py-1.5 text-xs font-medium ${
                undoStack.length === 0 ? "border-[#4A3549] text-[#6B5B69] opacity-50" : "border-[#4A3549] text-[#D9C9DA] hover:bg-[#3A2839]"
              }`}
            >
              <Undo2 size={14} /> ย้อนกลับ{undoStack.length > 0 ? ` (${undoStack.length})` : ""}
            </button>
            <button
              onClick={() => { setShowSummary(true); setHasUpdates(false); }}
              className="relative flex items-center gap-1.5 rounded-full bg-[#E8A33D] px-3 py-1.5 text-xs font-medium text-[#2B1E2A]"
            >
              <FileText size={14} /> สรุป PDF
              {hasUpdates && (
                <span className="absolute -top-1 -right-1 h-2.5 w-2.5 rounded-full bg-[#D6432C] ring-2 ring-[#2B1E2A]" />
              )}
            </button>
            <button
              onClick={() => setSettingsOpen(true)}
              className="flex items-center gap-1.5 rounded-full border border-[#4A3549] px-3 py-1.5 text-xs font-medium text-[#D9C9DA] hover:bg-[#3A2839]"
            >
              <Settings size={14} /> ตั้งค่า
            </button>
          </div>
        </div>
        <div className="mx-auto max-w-6xl px-4 pb-2">
          <SaveStatusBadge status={saveStatus} lastSavedAt={lastSavedAt} onRetry={retryPersistNow} />
        </div>
        <div className="mx-auto max-w-6xl px-4 pb-4 grid grid-cols-2 sm:grid-cols-4 gap-2.5">
          {[
            { key: "out", label: "หมดแล้ว", icon: XCircle },
            { key: "red", label: "ใกล้หมด", icon: AlertTriangle },
            { key: "yellow", label: "เริ่มน้อย", icon: TrendingDown },
            { key: "green", label: "ปกติ", icon: Package },
          ].map(({ key, label, icon: Icon }) => (
            <button
              key={key}
              onClick={() => { setTab("dashboard"); setStatusFilter(statusFilter === key ? "all" : key); }}
              className={`rounded-xl px-3 py-2.5 text-left transition ${
                statusFilter === key ? "bg-[#E8A33D] text-[#2B1E2A]" : key === "out" ? "bg-[#7A140A] text-white" : "bg-[#3A2839] text-[#F6F3EF]"
              }`}
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
          { id: "aging", label: "Lot & วันหมดอายุ", icon: CalendarClock },
          { id: "report", label: "รายงานการใช้", icon: ClipboardList },
          { id: "orders", label: "สรุปคำสั่งซื้อ", icon: ShoppingCart },
          { id: "log", label: "ประวัติ / LINE", icon: Bell },
        ].map(({ id, label, icon: Icon }) => (
          <button
            key={id}
            onClick={() => setTab(id)}
            className={`flex items-center gap-1.5 whitespace-nowrap rounded-full px-3.5 py-2 text-sm font-medium transition ${
              tab === id ? "bg-[#2B1E2A] text-[#F6F3EF]" : id === "log" && criticalItems.length > 0 ? "bg-white text-[#D6432C] border border-[#D6432C]/40" : "bg-white text-[#6B5B69] border border-[#E4DCD1]"
            }`}
          >
            <Icon size={14} className={id === "log" && criticalItems.length > 0 && tab !== id ? "text-[#D6432C]" : ""} /> {label}
            {id === "log" && alertLog.length > 0 && (
              <span className="ml-0.5 rounded-full bg-[#D6432C] px-1.5 text-[10px] text-white tabular">{alertLog.length}</span>
            )}
            {id === "orders" && (criticalItems.length + warningItems.length) > 0 && (
              <span className="ml-0.5 rounded-full bg-[#D6432C] px-1.5 text-[10px] text-white tabular">{criticalItems.length + warningItems.length}</span>
            )}
            {id === "aging" && expiringSoonCount > 0 && (
              <span className="ml-0.5 rounded-full bg-[#D6432C] px-1.5 text-[10px] text-white tabular">{expiringSoonCount}</span>
            )}
          </button>
        ))}
      </nav>

      <main className="mx-auto max-w-6xl px-4 py-5 pb-16">
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
                const nextLot = sortLotsFefo((p.lots || []).filter((l) => l.expiryDate))[0];
                const expDays = nextLot ? daysUntil(nextLot.expiryDate) : null;
                return (
                  <div key={p.id} className="rounded-2xl border border-[#E4DCD1] bg-white overflow-hidden">
                    <button onClick={() => setExpanded(isOpen ? null : p.id)} className="w-full flex items-center gap-3 px-4 py-3 text-left">
                      <span className={`h-9 w-1.5 rounded-full ${STATUS_META[m.status].dot} shrink-0`} />
                      {p.imageUrl && (
                        <img src={p.imageUrl} alt="" className="h-10 w-10 rounded-lg object-cover border border-[#E4DCD1] shrink-0" />
                      )}
                      <div className="flex-1 min-w-0">
                        <div className="flex items-center gap-2 flex-wrap">
                          <span className="font-medium text-sm truncate">{p.name}</span>
                          <span className="rounded-md bg-[#EFE8E0] px-1.5 py-0.5 text-[10px] font-semibold text-[#6B5B69]">{abc[p.id]?.cls || "-"}</span>
                          {expDays !== null && (
                            <span className={`rounded-md px-1.5 py-0.5 text-[10px] font-semibold flex items-center gap-0.5 ${
                              expDays < 0 ? "bg-[#7A140A]/10 text-[#7A140A]" : expDays <= 3 ? "bg-[#FBEAE7] text-[#D6432C]" : expDays <= 7 ? "bg-[#FBF1DF] text-[#C98A22]" : "bg-[#EFE8E0] text-[#6B5B69]"
                            }`}>
                              <CalendarClock size={10} /> {expDays < 0 ? `หมดอายุแล้ว ${Math.abs(expDays)}ว.` : `หมดอายุอีก ${expDays}ว.`}
                            </span>
                          )}
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
                        {/* แก้ไขยอดคงเหลือได้โดยตรง */}
                        <div className="mb-3.5 rounded-xl border border-[#E4DCD1] bg-white px-3 py-2.5 flex items-center gap-2">
                          <span className="text-xs text-[#6B5B69] shrink-0">คงเหลือ:</span>
                          {editingStockId === p.id ? (
                            <>
                              <input
                                type="number"
                                autoFocus
                                value={editingStockValue}
                                onChange={(e) => setEditingStockValue(e.target.value)}
                                onKeyDown={(e) => e.key === "Enter" && saveInlineEdit(p)}
                                className="w-24 rounded-lg border border-[#E4DCD1] px-2 py-1.5 text-sm tabular"
                              />
                              <span className="text-xs text-[#8A7A87]">{p.unit}</span>
                              <button onClick={() => saveInlineEdit(p)} className="ml-auto flex items-center gap-1 rounded-full bg-[#3F8A5E] px-3 py-1.5 text-xs font-medium text-white">
                                <Check size={13} /> บันทึก
                              </button>
                              <button onClick={() => setEditingStockId(null)} className="rounded-full border border-[#E4DCD1] px-2.5 py-1.5 text-xs text-[#6B5B69]">ยกเลิก</button>
                            </>
                          ) : (
                            <>
                              <span className="font-semibold tabular">{fmt(p.onHand)} {p.unit}</span>
                              <button onClick={() => startInlineEdit(p)} className="ml-auto flex items-center gap-1 rounded-full border border-[#E4DCD1] px-2.5 py-1.5 text-xs text-[#6B5B69]">
                                <Pencil size={12} /> แก้ไขจำนวน
                              </button>
                            </>
                          )}
                        </div>

                        <div className="grid grid-cols-2 sm:grid-cols-4 gap-3 text-xs">
                          <InlineMetric
                            label="จุดสั่งซื้อ (ROP)" unit={p.unit}
                            value={m.rop} display={`${fmt(m.rop)} ${p.unit}`}
                            isOverridden={p.ropOverride !== null && p.ropOverride !== undefined}
                            onSave={(v) => updateProductField(p, { ropOverride: v }, `แก้ไข ROP ของ ${p.name} เป็น ${fmt(v)} ${p.unit}`)}
                            onClear={() => updateProductField(p, { ropOverride: null }, `รีเซ็ต ROP ของ ${p.name} เป็นค่าคำนวณอัตโนมัติ`)}
                          />
                          <InlineMetric
                            label="Safety Stock" unit={p.unit}
                            value={m.safetyStock} display={`${fmt(m.safetyStock)} ${p.unit}`}
                            isOverridden={p.safetyStockOverride !== null && p.safetyStockOverride !== undefined}
                            onSave={(v) => updateProductField(p, { safetyStockOverride: v }, `แก้ไข Safety Stock ของ ${p.name} เป็น ${fmt(v)} ${p.unit}`)}
                            onClear={() => updateProductField(p, { safetyStockOverride: null }, `รีเซ็ต Safety Stock ของ ${p.name} เป็นค่าคำนวณอัตโนมัติ`)}
                          />
                          <InlineMetric
                            label="EOQ แนะนำ" unit={p.unit}
                            value={m.eoq} display={`${fmt(m.eoq)} ${p.unit}`}
                            isOverridden={p.eoqOverride !== null && p.eoqOverride !== undefined}
                            onSave={(v) => updateProductField(p, { eoqOverride: v }, `แก้ไข EOQ ของ ${p.name} เป็น ${fmt(v)} ${p.unit}`)}
                            onClear={() => updateProductField(p, { eoqOverride: null }, `รีเซ็ต EOQ ของ ${p.name} เป็นค่าคำนวณอัตโนมัติ`)}
                          />
                          <InlineMetric
                            label="Lead Time" unit="วัน"
                            value={p.leadTimeDays} display={`${p.leadTimeDays} วัน`}
                            onSave={(v) => updateProductField(p, { leadTimeDays: v }, `แก้ไข Lead Time ของ ${p.name} เป็น ${v} วัน`)}
                          />
                          <InlineMetric
                            label="ใช้เฉลี่ย/วัน" unit={p.unit}
                            value={Math.round(m.dailyUsage * 10) / 10} display={`${fmt(m.dailyUsage, 1)} ${p.unit}`}
                            onSave={(v) => updateProductField(p, { avgMonthlyUsage: v * 30 }, `แก้ไขยอดใช้เฉลี่ย/วันของ ${p.name} เป็น ${v} ${p.unit}`)}
                          />
                          <InlineMetric
                            label="ใช้เดือนนี้ (ประมาณ)" unit={p.unit}
                            value={p.avgMonthlyUsage} display={`${fmt(p.avgMonthlyUsage)} ${p.unit}`}
                            onSave={(v) => updateProductField(p, { avgMonthlyUsage: v }, `แก้ไขยอดใช้ต่อเดือนของ ${p.name} เป็น ${fmt(v)} ${p.unit}`)}
                          />
                          <InlineMetric
                            label="ต้นทุน/หน่วย" unit="บาท" prefix="฿"
                            value={Math.round(m.costPerUnit * 100) / 100} display={`฿${fmt(m.costPerUnit, 2)}`}
                            onSave={(v) => updateProductField(p, { packCost: v * p.packSize }, `แก้ไขต้นทุน/หน่วยของ ${p.name} เป็น ฿${v}`)}
                          />
                          <InlineMetric
                            label="ผู้ขาย" type="text"
                            value={p.supplier} display={p.supplier || "-"}
                            onSave={(v) => updateProductField(p, { supplier: v }, `แก้ไขผู้ขายของ ${p.name} เป็น "${v}"`)}
                          />
                        </div>
                        <div className="mt-3.5 flex flex-wrap gap-2">
                          <button onClick={() => { setReqProductId(p.id); setReqDirection("out"); setReqOpen(true); }} className="flex items-center gap-1.5 rounded-full bg-[#2B1E2A] px-3.5 py-2 text-xs font-medium text-white">
                            <MinusCircle size={14} /> เบิก / ลด
                          </button>
                          <button onClick={() => { setReqProductId(p.id); setReqDirection("in"); setReqOpen(true); }} className="flex items-center gap-1.5 rounded-full bg-[#4F9D8D] px-3.5 py-2 text-xs font-medium text-white">
                            <PlusCircle size={14} /> รับเข้า / เพิ่ม
                          </button>
                          <a
                            href={buildSmsLink(settings.smsPhone || DEFAULT_SMS_PHONE, buildOrderCopyText([p], settings))}
                            className="flex items-center gap-1.5 rounded-full bg-[#4F9D8D] px-3.5 py-2 text-xs font-medium text-white"
                          >
                            <MessageSquareText size={14} /> ส่ง SMS
                          </a>
                          <button onClick={() => openEditEditor(p)} className="flex items-center gap-1.5 rounded-full border border-[#E4DCD1] px-3.5 py-2 text-xs font-medium text-[#6B5B69]">
                            <Pencil size={14} /> แก้ไขข้อมูล
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

        {tab === "aging" && (
          <div>
            <div className="rounded-2xl border border-[#E4DCD1] bg-white p-4 mb-4">
              <h2 className="font-display text-base font-semibold mb-1">Lot & วันหมดอายุ (FEFO / Aging)</h2>
              <p className="text-xs text-[#8A7A87]">เรียงตามหลัก FEFO — ล็อตที่หมดอายุก่อนอยู่บนสุด ควรถูกใช้ก่อน ดูอายุที่ค้างในสต็อก (Aging) ได้ในคอลัมน์ขวาสุด</p>
            </div>
            <div className="space-y-2">
              {allLots.length === 0 && (
                <div className="rounded-2xl border border-dashed border-[#E4DCD1] py-10 text-center text-sm text-[#8A7A87]">ยังไม่มีข้อมูล Lot — เพิ่มได้ในหน้าแก้ไขสินค้า หรือตอน "รับเข้า/เพิ่ม" สต็อก</div>
              )}
              {allLots.map((lot) => {
                const expDays = daysUntil(lot.expiryDate);
                const ageDays = daysSince(lot.receivedDate);
                const isExpired = expDays !== null && expDays < 0;
                const isSoon = expDays !== null && expDays >= 0 && expDays <= (settings.expiryWarnDays ?? 7);
                return (
                  <div key={lot.id} className={`rounded-xl border px-4 py-3 flex items-center justify-between gap-3 ${
                    isExpired ? "border-[#7A140A]/40 bg-[#7A140A]/5" : isSoon ? "border-[#D6432C]/30 bg-[#FBEAE7]" : "border-[#E4DCD1] bg-white"
                  }`}>
                    <div className="min-w-0">
                      <div className="text-sm font-medium truncate">{lot.product.name}</div>
                      <div className="text-[11px] text-[#8A7A87] tabular mt-0.5">
                        คงเหลือ {fmt(lot.qty)} {lot.product.unit} • รับเข้า {lot.receivedDate || "-"} {ageDays !== null && `(ค้าง ${ageDays} วัน)`}
                      </div>
                    </div>
                    <div className="text-right shrink-0">
                      {lot.expiryDate ? (
                        <span className={`text-xs font-semibold ${isExpired ? "text-[#7A140A]" : isSoon ? "text-[#D6432C]" : "text-[#3F8A5E]"}`}>
                          {isExpired ? `หมดอายุแล้ว ${Math.abs(expDays)} วัน` : `เหลืออีก ${expDays} วัน`}
                        </span>
                      ) : (
                        <span className="text-xs text-[#8A7A87]">ไม่ระบุวันหมดอายุ</span>
                      )}
                      <div className="text-[10px] text-[#8A7A87] tabular">{lot.expiryDate || ""}</div>
                    </div>
                  </div>
                );
              })}
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

        {tab === "orders" && (
          <div>
            <div className="rounded-2xl border border-[#E4DCD1] bg-white p-4 mb-4">
              <div className="flex items-start justify-between gap-3">
                <div>
                  <h2 className="font-display text-base font-semibold mb-1">สรุปคำสั่งซื้อ — ข้อความที่ต้องส่ง</h2>
                  <p className="text-xs text-[#8A7A87]">รวมสินค้าที่ถึงจุด ROP แล้ว จัดกลุ่มตามช่องทางสั่งซื้อ กดคัดลอกข้อความไปวางในแชทได้เลย</p>
                </div>
                {criticalItems.length > 0 && (
                  <a
                    href={buildSmsLink(settings.smsPhone || DEFAULT_SMS_PHONE, buildAllOrdersText(criticalItems, settings))}
                    onClick={async () => {
                      const text = buildAllOrdersText(criticalItems, settings);
                      setHasUpdates(true);
                      if (settings.smsAutoNotify && settings.smsWebhookUrl) {
                        const ok = await sendSmsNotify(text);
                        setToast(ok ? "ส่ง SMS สำเร็จผ่าน Webhook แล้ว" : "ส่งผ่าน Webhook ไม่สำเร็จ — เปิดแอปข้อความให้แทน กดส่งเองได้เลย");
                      } else {
                        setToast('เปิดข้อความ SMS พร้อมสั่งซื้อทั้งหมดแล้ว — ต้องกด "ส่ง" เองในแอปข้อความ');
                      }
                    }}
                    className="shrink-0 flex items-center gap-1.5 rounded-full bg-[#4F9D8D] px-3.5 py-2 text-xs font-medium text-white"
                  >
                    <MessageSquareText size={14} /> ส่ง SMS ทั้งหมด
                  </a>
                )}
              </div>
            </div>
            {(() => {
              const ropItems = [...criticalItems, ...warningItems];
              const groups = {};
              ropItems.forEach((p) => {
                const key = p.orderGroup || "shopee_tiktok";
                if (!groups[key]) groups[key] = [];
                groups[key].push(p);
              });
              const entries = Object.entries(groups);
              if (entries.length === 0) {
                return (
                  <div className="rounded-2xl border border-dashed border-[#E4DCD1] py-10 text-center text-sm text-[#8A7A87]">
                    ยังไม่มีสินค้าที่ถึงจุดสั่งซื้อตอนนี้
                  </div>
                );
              }
              return (
                <div className="space-y-2.5">
                  {entries.map(([groupKey, items], idx) => {
                    const msg = getOrderMessage(items[0], settings);
                    return (
                      <div key={groupKey} className="rounded-2xl border-2 border-[#D6432C]/40 bg-[#FBEAE7] px-4 py-3.5">
                        <div className="flex items-start justify-between gap-2 mb-2">
                          <span className="text-sm font-bold text-[#D6432C]">{idx + 1}. {ORDER_GROUP_LABELS[groupKey] || groupKey}</span>
                          <div className="flex items-center gap-1.5 shrink-0">
                            <button
                              onClick={() => copyMessage(buildOrderCopyText(items, settings))}
                              className="flex items-center gap-1 rounded-full bg-white px-3 py-1.5 text-xs font-medium text-black border border-[#D6432C]/30"
                            >
                              <Copy size={12} /> คัดลอก
                            </button>
                            <a
                              href={buildSmsLink(settings.smsPhone || DEFAULT_SMS_PHONE, buildOrderCopyText(items, settings))}
                              className="flex items-center gap-1 rounded-full bg-[#4F9D8D] px-3 py-1.5 text-xs font-medium text-white"
                            >
                              <MessageSquareText size={12} /> ส่ง SMS
                            </a>
                          </div>
                        </div>
                        <div className="space-y-2 mb-2.5">
                          {items.map((p) => {
                            const m = metricsById[p.id];
                            const where = p.supplier?.trim() ? p.supplier : (ORDER_GROUP_LABELS[p.orderGroup] || "-");
                            return (
                              <div key={p.id} className="rounded-lg bg-white/70 px-3 py-2 text-xs text-black">
                                <div className="font-semibold">{p.name}</div>
                                <div className="grid grid-cols-2 gap-x-3 text-[11px] text-black mt-1">
                                  <span>ซื้อที่: {where}</span>
                                  <span>ปริมาณ: {fmt(m.eoq)} {p.unit}</span>
                                  <span className="col-span-2">ราคา: ฿{fmt(m.costPerUnit, 2)}/{p.unit} (รวม ~฿{fmt(m.eoq * m.costPerUnit)})</span>
                                </div>
                              </div>
                            );
                          })}
                        </div>
                        <p className="text-sm whitespace-pre-wrap text-black font-medium border-t border-[#D6432C]/20 pt-2">{msg}</p>
                      </div>
                    );
                  })}
                </div>
              );
            })()}
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
                <div key={entry.id} className={`rounded-xl border bg-white px-4 py-3 flex items-start gap-3 ${
                  entry.type === "line_alert" && entry.status === "red" ? "border-[#D6432C]/50 bg-[#FBEAE7]/40" : "border-[#E4DCD1]"
                }`}>
                  <div className={`mt-0.5 flex h-7 w-7 shrink-0 items-center justify-center rounded-full ${
                    entry.type === "line_alert" ? "bg-[#FBEAE7] text-[#D6432C]" : entry.type === "edit" ? "bg-[#EFE8E0] text-[#6B5B69]" : entry.direction === "in" ? "bg-[#E9F4EC] text-[#3F8A5E]" : "bg-[#FBF1DF] text-[#C98A22]"
                  }`}>
                    {entry.type === "line_alert" ? <Bell size={13} /> : entry.type === "edit" ? <Pencil size={13} /> : entry.direction === "in" ? <PlusCircle size={13} /> : <MinusCircle size={13} />}
                  </div>
                  <div className="flex-1 min-w-0">
                    {entry.type === "stock_adjust" || entry.type === "requisition" ? (
                      <p className="text-sm">
                        {MOVEMENT_TYPES[entry.movementType]?.label || (entry.direction === "in" ? "รับเข้า" : "เบิก/ลด")} <span className="font-medium">{entry.productName}</span> จำนวน {fmt(entry.qty)} {entry.unit}
                        {entry.onHandAfter !== undefined && <span className="text-[#8A7A87]"> (คงเหลือ {fmt(entry.onHandAfter)} {entry.unit})</span>}
                        {entry.note && <span className="text-[#8A7A87]"> — {entry.note}</span>}
                      </p>
                    ) : (
                      <p className={`text-sm whitespace-pre-wrap ${entry.type === "line_alert" && entry.status === "red" ? "font-medium text-[#D6432C]" : ""}`}>{entry.message}</p>
                    )}
                    {entry.imageUrl && (
                      <img src={entry.imageUrl} alt="" className="mt-1.5 h-14 w-14 rounded-lg object-cover border border-[#E4DCD1]" />
                    )}
                    <p className="text-[11px] text-[#8A7A87] mt-0.5 tabular">{new Date(entry.ts).toLocaleString("th-TH")}</p>
                  </div>
                  {entry.type === "line_alert" && (
                    <div className="shrink-0 flex flex-col gap-1.5">
                      <button
                        onClick={() => {
                          const prod = products.find((x) => x.id === entry.productId);
                          copyMessage(prod ? buildOrderCopyText([prod], settings) : entry.message);
                        }}
                        className="flex items-center gap-1 rounded-full bg-[#EFE8E0] px-2.5 py-1 text-[11px] text-[#2B1E2A]"
                      >
                        <Copy size={11} /> คัดลอก
                      </button>
                      <button onClick={() => window.open(LINE_LINK, "_blank")} className="flex items-center gap-1 rounded-full bg-[#EFE8E0] px-2.5 py-1 text-[11px] text-[#2B1E2A]">
                        <Send size={11} /> LINE
                      </button>
                    </div>
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
              <h3 className="font-display text-base font-semibold">ปรับสต็อก</h3>
              <div className="flex items-center gap-2">
                <button onClick={goHome} title="กลับหน้าแรก" className="flex items-center gap-1 rounded-full border border-[#E4DCD1] px-2.5 py-1 text-[11px] text-[#6B5B69]">
                  <Home size={12} /> หน้าแรก
                </button>
                <button
                  onClick={undoLast}
                  disabled={undoStack.length === 0}
                  title="ย้อนกลับการเปลี่ยนแปลงล่าสุด"
                  className={`flex items-center gap-1 rounded-full border border-[#E4DCD1] px-2.5 py-1 text-[11px] ${undoStack.length === 0 ? "text-[#C9BFC7] opacity-50" : "text-[#6B5B69]"}`}
                >
                  <Undo2 size={12} /> ย้อนกลับ
                </button>
                <button onClick={() => setReqOpen(false)}><X size={18} className="text-[#8A7A87]" /></button>
              </div>
            </div>
            <div className="grid grid-cols-2 gap-2 mb-3.5">
              <button
                onClick={() => { setReqDirection("out"); setReqMovementType("sales"); }}
                className={`flex items-center justify-center gap-1.5 rounded-lg py-2.5 text-sm font-medium ${reqDirection === "out" ? "bg-[#2B1E2A] text-white" : "bg-[#EFE8E0] text-[#6B5B69]"}`}
              >
                <MinusCircle size={14} /> เบิก / ลด
              </button>
              <button
                onClick={() => { setReqDirection("in"); setReqMovementType("received"); }}
                className={`flex items-center justify-center gap-1.5 rounded-lg py-2.5 text-sm font-medium ${reqDirection === "in" ? "bg-[#4F9D8D] text-white" : "bg-[#EFE8E0] text-[#6B5B69]"}`}
              >
                <PlusCircle size={14} /> รับเข้า / เพิ่ม
              </button>
            </div>
            <Field label="ประเภทการเคลื่อนไหว">
              <select value={reqMovementType} onChange={(e) => setReqMovementType(e.target.value)} className={inputCls}>
                {Object.entries(MOVEMENT_TYPES).filter(([, v]) => v.direction === reqDirection).map(([k, v]) => (
                  <option key={k} value={k}>{v.label}</option>
                ))}
              </select>
            </Field>
            <Field label="รายการ">
              <select value={reqProductId} onChange={(e) => setReqProductId(e.target.value)} className={inputCls}>
                {products.map((p) => (<option key={p.id} value={p.id}>{p.name} (คงเหลือ {fmt(p.onHand)} {p.unit})</option>))}
              </select>
            </Field>
            <Field label={`จำนวนที่${reqDirection === "out" ? "เบิกออก" : "รับเข้า"} (${products.find((p) => p.id === reqProductId)?.unit || ""})`}>
              <input type="number" min={1} value={reqQty} onChange={(e) => setReqQty(Number(e.target.value))} className={inputCls} />
            </Field>
            {reqDirection === "in" && (
              <Field label="วันหมดอายุของล็อตนี้ (ไม่บังคับ)">
                <input type="date" value={reqExpiryDate} onChange={(e) => setReqExpiryDate(e.target.value)} className={inputCls} />
                <p className="text-[10px] text-[#8A7A87] mt-1">ระบบจะบันทึกเป็น Lot ใหม่ และตัดออกตามหลัก FEFO (ของหมดอายุก่อนถูกใช้ก่อน) เมื่อมีการเบิกครั้งถัดไป</p>
              </Field>
            )}
            {reqDirection === "out" && (() => {
              const p = products.find((x) => x.id === reqProductId);
              const sorted = p ? sortLotsFefo(p.lots || []) : [];
              if (sorted.length === 0) return null;
              const next = sorted[0];
              return (
                <div className="mb-3.5 rounded-lg bg-[#FBF1DF] border border-[#C98A22]/30 px-3 py-2 text-[11px] text-[#6B5B69]">
                  ระบบจะตัดสต็อกตาม FEFO — ล็อตถัดไปที่จะถูกใช้: {next.expiryDate ? `หมดอายุ ${next.expiryDate}` : "ไม่ระบุวันหมดอายุ"} (คงเหลือ {fmt(next.qty)} {p.unit})
                </div>
              );
            })()}
            <Field label="หมายเหตุ (ไม่บังคับ)">
              <input type="text" value={reqNote} onChange={(e) => setReqNote(e.target.value)} placeholder={reqDirection === "out" ? "เช่น ใช้สำหรับออเดอร์เดลิเวอรี" : "เช่น รับของจากผู้ขาย"} className={inputCls} />
            </Field>
            <Field label="แนบรูปภาพ (ไม่บังคับ, เช่น รูปใบเสร็จ/สินค้าที่รับเข้า)">
              <div className="flex items-center gap-3">
                {reqImage ? (
                  <div className="relative shrink-0">
                    <img src={reqImage} alt="" className="h-14 w-14 rounded-lg object-cover border border-[#E4DCD1]" />
                    <button onClick={() => setReqImage("")} className="absolute -top-1.5 -right-1.5 flex h-5 w-5 items-center justify-center rounded-full bg-[#D6432C] text-white">
                      <X size={11} />
                    </button>
                  </div>
                ) : (
                  <label className="flex h-14 w-14 shrink-0 cursor-pointer items-center justify-center rounded-lg border border-dashed border-[#E4DCD1] text-[#C9BFC7]">
                    <ImagePlus size={16} />
                    <input
                      type="file" accept="image/*" className="hidden"
                      onChange={async (e) => {
                        const file = e.target.files?.[0];
                        if (!file) return;
                        try {
                          const dataUrl = await compressImage(file, 480, 0.55);
                          setReqImage(dataUrl);
                        } catch (err) { setToast("แนบรูปไม่สำเร็จ ลองไฟล์อื่น"); }
                        e.target.value = "";
                      }}
                    />
                  </label>
                )}
                <span className="text-[11px] text-[#8A7A87]">แนบรูปเพื่อเก็บเป็นหลักฐานการเคลื่อนไหวสต็อกนี้</span>
              </div>
            </Field>
            <div className="flex gap-2 mt-1">
              <button onClick={() => { setReqOpen(false); setReqImage(""); setReqExpiryDate(""); }} className="flex-1 rounded-full border border-[#E4DCD1] py-3 text-sm font-medium text-[#6B5B69]">
                ยกเลิก
              </button>
              <button onClick={submitStockAdjust} className={`flex-1 rounded-full py-3 text-sm font-medium text-white ${reqDirection === "out" ? "bg-[#2B1E2A]" : "bg-[#4F9D8D]"}`}>
                {reqDirection === "out" ? "ยืนยันการเบิก" : "ยืนยันการรับเข้า"}
              </button>
            </div>
          </div>
        </div>
      )}

      {editorOpen && (
        <div className="fixed inset-0 z-40 flex items-end sm:items-center justify-center bg-black/40" onClick={() => setEditorOpen(false)}>
          <div className="w-full sm:max-w-md max-h-[90vh] overflow-y-auto rounded-t-2xl sm:rounded-2xl bg-white p-5" onClick={(e) => e.stopPropagation()}>
            <div className="flex items-center justify-between mb-4">
              <h3 className="font-display text-base font-semibold">{editorMode === "add" ? "เพิ่มสินค้าใหม่" : `แก้ไข "${draft.name}"`}</h3>
              <div className="flex items-center gap-2">
                <button onClick={goHome} title="กลับหน้าแรก" className="flex items-center gap-1 rounded-full border border-[#E4DCD1] px-2.5 py-1 text-[11px] text-[#6B5B69]">
                  <Home size={12} /> หน้าแรก
                </button>
                <button
                  onClick={undoLast}
                  disabled={undoStack.length === 0}
                  title="ย้อนกลับการเปลี่ยนแปลงล่าสุด"
                  className={`flex items-center gap-1 rounded-full border border-[#E4DCD1] px-2.5 py-1 text-[11px] ${undoStack.length === 0 ? "text-[#C9BFC7] opacity-50" : "text-[#6B5B69]"}`}
                >
                  <Undo2 size={12} /> ย้อนกลับ
                </button>
                <button onClick={() => setEditorOpen(false)}><X size={18} className="text-[#8A7A87]" /></button>
              </div>
            </div>
            <Field label="ชื่อสินค้า">
              <input type="text" value={draft.name} onChange={(e) => setDraft((d) => ({ ...d, name: e.target.value }))} className={inputCls} />
            </Field>
            <Field label="รูปภาพสินค้า (ไม่บังคับ)">
              <div className="flex items-center gap-3">
                {draft.imageUrl ? (
                  <div className="relative shrink-0">
                    <img src={draft.imageUrl} alt="" className="h-16 w-16 rounded-lg object-cover border border-[#E4DCD1]" />
                    <button
                      onClick={() => setDraft((d) => ({ ...d, imageUrl: "" }))}
                      className="absolute -top-1.5 -right-1.5 flex h-5 w-5 items-center justify-center rounded-full bg-[#D6432C] text-white"
                    >
                      <X size={11} />
                    </button>
                  </div>
                ) : (
                  <div className="flex h-16 w-16 shrink-0 items-center justify-center rounded-lg border border-dashed border-[#E4DCD1] text-[#C9BFC7]">
                    <ImageOff size={18} />
                  </div>
                )}
                <label className="flex items-center gap-1.5 rounded-full border border-[#E4DCD1] px-3 py-2 text-xs font-medium text-[#6B5B69] cursor-pointer">
                  <ImagePlus size={14} /> {draft.imageUrl ? "เปลี่ยนรูป" : "แนบรูป"}
                  <input
                    type="file" accept="image/*" className="hidden"
                    onChange={async (e) => {
                      const file = e.target.files?.[0];
                      if (!file) return;
                      try {
                        const dataUrl = await compressImage(file, 480, 0.6);
                        setDraft((d) => ({ ...d, imageUrl: dataUrl }));
                      } catch (err) { setToast("แนบรูปไม่สำเร็จ ลองไฟล์อื่น"); }
                      e.target.value = "";
                    }}
                  />
                </label>
              </div>
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
            <div className="grid grid-cols-2 gap-3">
              <Field label="SKU no.">
                <input type="text" value={draft.sku} onChange={(e) => setDraft((d) => ({ ...d, sku: e.target.value }))} className={inputCls} placeholder="เช่น RM-001" />
              </Field>
              <Field label="นับสต็อกจริง (Count)">
                <input type="number" value={draft.physicalCount} onChange={(e) => setDraft((d) => ({ ...d, physicalCount: e.target.value }))} className={inputCls} />
              </Field>
            </div>
            <Field label="ผู้ขาย / Supplier">
              <input type="text" value={draft.supplier} onChange={(e) => setDraft((d) => ({ ...d, supplier: e.target.value }))} className={inputCls} />
            </Field>
            <Field label={`Lot & วันหมดอายุ (รวม ${fmt(totalLotQty(draft.lots))} ${draft.unit} — คงเหลือระบบ ${fmt(Number(draft.onHand) || 0)} ${draft.unit})`}>
              <div className="space-y-2">
                {(draft.lots || []).map((lot, idx) => (
                  <div key={lot.id} className="rounded-lg border border-[#E4DCD1] bg-[#FBF9F6] px-2.5 py-2 grid grid-cols-12 gap-1.5 items-center">
                    <input
                      type="number" value={lot.qty}
                      onChange={(e) => setDraft((d) => ({ ...d, lots: d.lots.map((l, i) => (i === idx ? { ...l, qty: Number(e.target.value) } : l)) }))}
                      className="col-span-3 rounded border border-[#E4DCD1] px-1.5 py-1.5 text-xs tabular"
                      placeholder="จำนวน"
                    />
                    <input
                      type="date" value={lot.receivedDate || ""}
                      onChange={(e) => setDraft((d) => ({ ...d, lots: d.lots.map((l, i) => (i === idx ? { ...l, receivedDate: e.target.value } : l)) }))}
                      className="col-span-4 rounded border border-[#E4DCD1] px-1.5 py-1.5 text-[11px]"
                      title="วันที่รับเข้า"
                    />
                    <input
                      type="date" value={lot.expiryDate || ""}
                      onChange={(e) => setDraft((d) => ({ ...d, lots: d.lots.map((l, i) => (i === idx ? { ...l, expiryDate: e.target.value } : l)) }))}
                      className="col-span-4 rounded border border-[#E4DCD1] px-1.5 py-1.5 text-[11px]"
                      title="วันหมดอายุ"
                    />
                    <button
                      onClick={() => setDraft((d) => ({ ...d, lots: d.lots.filter((_, i) => i !== idx) }))}
                      className="col-span-1 flex items-center justify-center text-[#D6432C]"
                    >
                      <Trash2 size={14} />
                    </button>
                  </div>
                ))}
                <button
                  onClick={() => setDraft((d) => ({ ...d, lots: addLotToList(d.lots, 0, null, "เพิ่มด้วยตนเอง") }))}
                  className="flex items-center gap-1 rounded-full border border-[#E4DCD1] px-2.5 py-1.5 text-[11px] text-[#6B5B69]"
                >
                  <Plus size={12} /> เพิ่ม Lot
                </button>
                <p className="text-[9px] text-[#8A7A87]">ช่อง: จำนวน · วันที่รับเข้า · วันหมดอายุ — ระบบจะตัดสต็อกออกจาก Lot ที่หมดอายุก่อนโดยอัตโนมัติเมื่อเบิกใช้ (FEFO)</p>
              </div>
            </Field>
            <Field label="ช่องทาง/ข้อความสั่งซื้อ เมื่อถึงจุด ROP">
              <select value={draft.orderGroup} onChange={(e) => setDraft((d) => ({ ...d, orderGroup: e.target.value }))} className={inputCls}>
                {Object.entries(ORDER_GROUP_LABELS).map(([k, label]) => (<option key={k} value={k}>{label}</option>))}
              </select>
            </Field>
            {draft.orderGroup === "custom" && (
              <Field label="ข้อความสั่งซื้อ (กำหนดเอง)">
                <textarea
                  value={draft.customOrderMessage}
                  onChange={(e) => setDraft((d) => ({ ...d, customOrderMessage: e.target.value }))}
                  rows={4}
                  className={inputCls}
                  placeholder="พิมพ์ข้อความที่จะใช้แจ้งเตือน/สั่งซื้อสินค้านี้"
                />
              </Field>
            )}
            {draft.orderGroup !== "custom" && (
              <div className="mb-3.5 rounded-lg bg-[#FBF9F6] border border-[#E4DCD1] px-3 py-2.5 text-[11px] text-[#6B5B69] whitespace-pre-wrap">
                {getOrderMessage(draft, settings)}
              </div>
            )}
            <div className="flex gap-2 mt-2">
              <button onClick={() => setEditorOpen(false)} className="flex-1 rounded-full border border-[#E4DCD1] py-3 text-sm font-medium text-[#6B5B69]">
                ยกเลิก
              </button>
              <button onClick={saveDraft} className="flex-1 rounded-full bg-[#2B1E2A] py-3 text-sm font-medium text-white">
                {editorMode === "add" ? "ยืนยัน & เพิ่มสินค้า" : "ยืนยันการแก้ไข"}
              </button>
            </div>
          </div>
        </div>
      )}

      {settingsOpen && (
        <SettingsModal
          settings={settings}
          setSettings={setSettings}
          onClose={() => setSettingsOpen(false)}
          onReset={resetData}
          onHome={goHome}
          undoStack={undoStack}
          undoLast={undoLast}
          pushUndo={pushUndo}
          notifPermission={notifPermission}
          onRequestPushPermission={requestPushPermission}
          onTestPush={() => notifyBrowser(`${BRAND} — ทดสอบการแจ้งเตือน`, "ถ้าเห็นข้อความนี้ แปลว่า Push Notification ใช้งานได้บนอุปกรณ์นี้")}
        />
      )}

      {toast && (
        <div className="fixed bottom-6 left-1/2 -translate-x-1/2 z-50 flex items-center gap-3 max-w-[92%] rounded-full bg-[#2B1E2A] px-4 py-2.5 text-xs text-white shadow-lg">
          <span>{toast}</span>
          {toastUndo && (
            <button onClick={() => toastUndo()} className="shrink-0 flex items-center gap-1 rounded-full bg-white/15 px-2.5 py-1 font-medium">
              <Undo2 size={12} /> ย้อนกลับ
            </button>
          )}
        </div>
      )}
    </div>
  );
}

function SaveStatusBadge({ status, lastSavedAt, onRetry, onLight }) {
  if (status === "error") {
    return (
      <button onClick={onRetry} className={`flex items-center gap-1.5 rounded-full px-2.5 py-1 text-[11px] font-medium ${onLight ? "bg-[#FBEAE7] text-[#D6432C] border border-[#D6432C]/30" : "bg-[#D6432C]/20 text-[#F6BDB2]"}`}>
        <Save size={12} /> <AlertTriangle size={11} /> บันทึกไม่สำเร็จ — ข้อมูลยังอยู่ในเครื่อง กดเพื่อลองบันทึกใหม่
      </button>
    );
  }
  if (status === "saving") {
    return (
      <span className={`flex items-center gap-1.5 text-[11px] ${onLight ? "text-black" : "text-[#D9C9DA]"}`}>
        <Save size={12} className="animate-pulse" /> กำลังบันทึก...
      </span>
    );
  }
  if (status === "saved") {
    return (
      <span className={`flex items-center gap-1.5 text-[11px] font-medium ${onLight ? "text-[#3F8A5E]" : "text-[#9FD9B8]"}`}>
        <Save size={12} /> <Check size={12} /> บันทึกอัตโนมัติแล้ว{lastSavedAt ? ` • ${lastSavedAt.toLocaleTimeString("th-TH")}` : ""}
      </span>
    );
  }
  return (
    <span className={`flex items-center gap-1.5 text-[11px] ${onLight ? "text-black" : "text-[#8A7A87]"}`}>
      <Save size={12} /> พร้อมบันทึกอัตโนมัติ
    </span>
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

// เมตริกที่แก้ไขได้แบบอินไลน์ — ใช้กับทั้งฟิลด์จริง (Lead Time, ผู้ขาย ฯลฯ)
// และค่าที่คำนวณอัตโนมัติซึ่งกำหนดเอง (override) ได้ เช่น ROP, Safety Stock, EOQ
function InlineMetric({ label, value, display, unit, prefix = "", type = "number", isOverridden, onSave, onClear }) {
  const [editing, setEditing] = useState(false);
  const [val, setVal] = useState(value);

  function startEdit() {
    setVal(value);
    setEditing(true);
  }
  function save() {
    const v = type === "number" ? Number(val) : val;
    if (type === "number" && Number.isNaN(v)) return;
    onSave(v);
    setEditing(false);
  }

  if (editing) {
    return (
      <div>
        <div className="text-[10px] text-[#8A7A87] mb-0.5">{label}</div>
        <div className="flex items-center gap-1">
          <input
            type={type}
            autoFocus
            value={val}
            onChange={(e) => setVal(e.target.value)}
            onKeyDown={(e) => e.key === "Enter" && save()}
            className="w-full min-w-0 rounded border border-[#4F9D8D] px-1.5 py-1 text-xs tabular"
          />
          <button onClick={save} className="shrink-0 text-[#3F8A5E]"><Check size={14} /></button>
          <button onClick={() => setEditing(false)} className="shrink-0 text-[#8A7A87]"><X size={14} /></button>
        </div>
      </div>
    );
  }

  return (
    <div className="group/metric">
      <div className="text-[10px] text-[#8A7A87] mb-0.5 flex items-center gap-1">
        {label}
        {isOverridden && <span className="text-[9px] rounded bg-[#EFE8E0] px-1 text-[#6B5B69]">กำหนดเอง</span>}
      </div>
      <button onClick={startEdit} className="font-medium tabular flex items-center gap-1 hover:text-[#4F9D8D] text-left">
        {display}
        <Pencil size={10} className="opacity-30 group-hover/metric:opacity-100 shrink-0" />
      </button>
      {isOverridden && onClear && (
        <button onClick={onClear} className="block text-[9px] text-[#4F9D8D] underline mt-0.5">ใช้ค่าอัตโนมัติ</button>
      )}
    </div>
  );
}

function TestLineButton({ settings }) {
  const [state, setState] = useState("idle"); // idle | sending | ok | error

  async function test() {
    if (!settings.lineWebhookUrl || !settings.lineUserId) { setState("error"); return; }
    setState("sending");
    try {
      await fetch(settings.lineWebhookUrl, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ userId: settings.lineUserId, message: `ทดสอบการแจ้งเตือนจาก ${BRAND} Stock Manager ✅` }),
      });
      setState("ok");
    } catch (e) {
      setState("error");
    }
  }

  return (
    <div className="flex items-center gap-2">
      <button
        type="button"
        onClick={test}
        disabled={state === "sending"}
        className="flex items-center gap-1.5 rounded-full border border-[#4F9D8D] px-3 py-2 text-xs font-medium text-[#4F9D8D]"
      >
        <Send size={13} /> {state === "sending" ? "กำลังส่ง..." : "ทดสอบส่งแจ้งเตือน"}
      </button>
      {state === "ok" && <span className="text-[11px] text-[#3F8A5E] flex items-center gap-1"><Check size={12} /> ส่งสำเร็จ ตรวจสอบใน LINE</span>}
      {state === "error" && <span className="text-[11px] text-[#D6432C] flex items-center gap-1"><AlertTriangle size={12} /> ส่งไม่สำเร็จ ตรวจสอบ URL/ID</span>}
    </div>
  );
}

function TestSmsButton({ settings }) {
  const [state, setState] = useState("idle"); // idle | sending | ok | error | nowebhook

  async function test() {
    if (!settings.smsWebhookUrl) { setState("nowebhook"); return; }
    if (!settings.smsPhone) { setState("error"); return; }
    setState("sending");
    try {
      const res = await fetch(settings.smsWebhookUrl, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ phone: settings.smsPhone, message: `ทดสอบการแจ้งเตือน SMS จาก ${BRAND} Stock Manager` }),
      });
      setState(res.ok ? "ok" : "error");
    } catch (e) {
      setState("error");
    }
  }

  return (
    <div className="flex items-center gap-2 flex-wrap">
      <button
        type="button"
        onClick={test}
        disabled={state === "sending"}
        className="flex items-center gap-1.5 rounded-full border border-[#4F9D8D] px-3 py-2 text-xs font-medium text-[#4F9D8D]"
      >
        <Send size={13} /> {state === "sending" ? "กำลังส่ง..." : "ทดสอบส่ง SMS ผ่าน Webhook"}
      </button>
      {state === "ok" && <span className="text-[11px] text-[#3F8A5E] flex items-center gap-1"><Check size={12} /> ส่งสำเร็จ ตรวจสอบที่เบอร์ปลายทาง</span>}
      {state === "error" && <span className="text-[11px] text-[#D6432C] flex items-center gap-1"><AlertTriangle size={12} /> ส่งไม่สำเร็จ ตรวจสอบ Webhook URL</span>}
      {state === "nowebhook" && <span className="text-[11px] text-[#C98A22] flex items-center gap-1"><AlertTriangle size={12} /> ยังไม่ได้กรอก SMS Webhook URL</span>}
    </div>
  );
}

function SettingsModal({ settings, setSettings, onClose, onReset, onHome, undoStack, undoLast, pushUndo, notifPermission, onRequestPushPermission, onTestPush }) {
  const [draft, setDraft] = useState({ ...settings, orderTemplates: { ...settings.orderTemplates } });

  function confirm() {
    pushUndo("แก้ไขค่าตั้งต้นการคำนวณ / ข้อความสั่งซื้อ");
    setSettings(draft);
    onClose();
  }

  return (
    <div className="fixed inset-0 z-40 flex items-end sm:items-center justify-center bg-black/40" onClick={onClose}>
      <div className="w-full sm:max-w-sm max-h-[90vh] overflow-y-auto rounded-t-2xl sm:rounded-2xl bg-white p-5" onClick={(e) => e.stopPropagation()}>
        <div className="flex items-center justify-between mb-4">
          <h3 className="font-display text-base font-semibold">ค่าตั้งต้นการคำนวณ</h3>
          <div className="flex items-center gap-2">
            <button onClick={onHome} title="กลับหน้าแรก" className="flex items-center gap-1 rounded-full border border-[#E4DCD1] px-2.5 py-1 text-[11px] text-[#6B5B69]">
              <Home size={12} /> หน้าแรก
            </button>
            <button
              onClick={undoLast}
              disabled={undoStack.length === 0}
              title="ย้อนกลับการเปลี่ยนแปลงล่าสุด"
              className={`flex items-center gap-1 rounded-full border border-[#E4DCD1] px-2.5 py-1 text-[11px] ${undoStack.length === 0 ? "text-[#C9BFC7] opacity-50" : "text-[#6B5B69]"}`}
            >
              <Undo2 size={12} /> ย้อนกลับ
            </button>
            <button onClick={onClose}><X size={18} className="text-[#8A7A87]" /></button>
          </div>
        </div>
        {[
          { key: "orderCost", label: "ค่าใช้จ่ายต่อการสั่งซื้อ 1 ครั้ง (บาท)", step: 1 },
          { key: "holdingRate", label: "อัตราต้นทุนถือครองสต็อกต่อปี (0-1)", step: 0.01 },
          { key: "serviceZ", label: "Z-score ระดับบริการ (เช่น 1.65 = 95%)", step: 0.01 },
          { key: "demandCv", label: "ความแปรผันของยอดใช้ (0-1)", step: 0.01 },
        ].map((f) => (
          <Field key={f.key} label={f.label}>
            <input type="number" step={f.step} value={draft[f.key]} onChange={(e) => setDraft((d) => ({ ...d, [f.key]: Number(e.target.value) }))} className={inputCls} />
          </Field>
        ))}

        <div className="mt-1 mb-2 border-t border-[#EFE8E0] pt-3.5">
          <p className="text-xs font-medium text-[#2B1E2A] mb-2.5 flex items-center gap-1.5">
            <Bell size={13} /> การแจ้งเตือนแบบ Push (สต็อกหมด/ใกล้หมด + ใกล้หมดอายุ)
          </p>
          <div className="rounded-lg bg-[#FBF1DF] border border-[#C98A22]/30 px-3 py-2.5 mb-3">
            <p className="text-[10px] text-[#6B5B69] leading-relaxed">
              ⚠️ Push Notification ของเบราว์เซอร์ทำงานได้เฉพาะตอนเปิดหน้านี้ทิ้งไว้ในเบราว์เซอร์ (ไม่ใช่ปิดแอปแล้วเด้งขึ้นมาได้เอง) และ<b>บน iPad/iPhone (Safari) มักใช้ไม่ได้เลย</b>ถ้าเปิดผ่านหน้าต่างแชทที่ฝังแอปนี้ไว้ — วิธีที่การันตีว่าจะมีข้อความเด้งขึ้นจริงบน iPad วันนี้คือผ่าน<b>แอป LINE</b> (ตั้งค่าไว้ด้านล่างแล้ว) เพราะ LINE เป็นแอปจริงที่ส่ง Push ของตัวเองได้ปกติ
            </p>
          </div>
          <div className="flex items-center gap-2 mb-3 flex-wrap">
            <button
              type="button"
              onClick={onRequestPushPermission}
              disabled={notifPermission === "granted"}
              className="flex items-center gap-1.5 rounded-full border border-[#4F9D8D] px-3 py-2 text-xs font-medium text-[#4F9D8D]"
            >
              <Bell size={13} /> {notifPermission === "granted" ? "อนุญาตแล้ว" : notifPermission === "unsupported" ? "อุปกรณ์นี้ไม่รองรับ" : "ขออนุญาตแจ้งเตือน"}
            </button>
            {notifPermission === "granted" && (
              <button type="button" onClick={onTestPush} className="flex items-center gap-1.5 rounded-full border border-[#E4DCD1] px-3 py-2 text-xs font-medium text-[#6B5B69]">
                <Send size={13} /> ทดสอบ Push
              </button>
            )}
          </div>
          <Field label="แจ้งเตือนล่วงหน้าก่อนหมดอายุ (วัน)">
            <input type="number" min={0} value={draft.expiryWarnDays} onChange={(e) => setDraft((d) => ({ ...d, expiryWarnDays: Number(e.target.value) }))} className={inputCls} />
          </Field>
          <label className="flex items-center gap-2 mb-1 text-xs text-[#6B5B69]">
            <input
              type="checkbox"
              checked={draft.pushNotifyEnabled}
              onChange={(e) => setDraft((d) => ({ ...d, pushNotifyEnabled: e.target.checked }))}
              className="h-4 w-4 rounded border-[#E4DCD1]"
            />
            ส่งแจ้งเตือนสต็อกหมด/ใกล้หมด และใกล้หมดอายุ เข้า LINE อัตโนมัติด้วย (นอกเหนือจากตอนถึงจุด ROP)
          </label>
        </div>

        <div className="mt-1 mb-2 border-t border-[#EFE8E0] pt-3.5">
          <p className="text-xs font-medium text-[#2B1E2A] mb-2.5 flex items-center gap-1.5">
            <Bell size={13} /> การแจ้งเตือนเข้า LINE อัตโนมัติ
          </p>
          <label className="flex items-center gap-2 mb-3 text-xs text-[#6B5B69]">
            <input
              type="checkbox"
              checked={draft.lineAutoNotify}
              onChange={(e) => setDraft((d) => ({ ...d, lineAutoNotify: e.target.checked }))}
              className="h-4 w-4 rounded border-[#E4DCD1]"
            />
            เปิดใช้งานส่งแจ้งเตือนเข้า LINE อัตโนมัติเมื่อสินค้าถึงจุด ROP
          </label>
          <Field label="Webhook URL (Google Apps Script)">
            <input type="text" value={draft.lineWebhookUrl} onChange={(e) => setDraft((d) => ({ ...d, lineWebhookUrl: e.target.value }))} className={inputCls} placeholder="https://script.google.com/macros/s/.../exec" />
          </Field>
          <Field label="LINE User ID ปลายทาง">
            <input type="text" value={draft.lineUserId} onChange={(e) => setDraft((d) => ({ ...d, lineUserId: e.target.value }))} className={inputCls} placeholder="U..." />
          </Field>
          <TestLineButton settings={draft} />
        </div>

        <div className="mt-1 mb-2 border-t border-[#EFE8E0] pt-3.5">
          <p className="text-xs font-medium text-[#2B1E2A] mb-2.5 flex items-center gap-1.5">
            <MessageSquareText size={13} /> แจ้งเตือนผ่าน SMS
          </p>
          <Field label="เบอร์โทรศัพท์ปลายทาง">
            <input type="text" value={draft.smsPhone} onChange={(e) => setDraft((d) => ({ ...d, smsPhone: e.target.value }))} className={inputCls} placeholder="08xxxxxxxx" />
          </Field>
          <div className="rounded-lg bg-[#FBF1DF] border border-[#C98A22]/30 px-3 py-2.5 mb-3">
            <p className="text-[10px] text-[#6B5B69] leading-relaxed">
              ⚠️ ปุ่ม "ส่ง SMS" ในแอปเป็นการเปิดแอปส่งข้อความของเครื่องพร้อมกรอกข้อความไว้ล่วงหน้าเท่านั้น — <b>ไม่ได้ส่งอัตโนมัติ</b> และจะไม่แจ้งเตือนที่ปลายทางจนกว่าจะกด "ส่ง" เองในแอปข้อความ นอกจากนี้บนคอมพิวเตอร์/บางเบราว์เซอร์อาจไม่รองรับเลย ถ้าต้องการให้ระบบส่ง SMS อัตโนมัติจริง (แจ้งเตือนที่ปลายทางทันทีโดยไม่ต้องกดอะไรเพิ่ม) ต้องต่อกับผู้ให้บริการ SMS Gateway ที่มี API เช่น Twilio หรือผู้ให้บริการ SMS ในไทย แล้วกรอก Webhook URL ด้านล่าง (รูปแบบเดียวกับที่ตั้งค่า LINE)
            </p>
          </div>
          <label className="flex items-center gap-2 mb-3 text-xs text-[#6B5B69]">
            <input
              type="checkbox"
              checked={draft.smsAutoNotify}
              onChange={(e) => setDraft((d) => ({ ...d, smsAutoNotify: e.target.checked }))}
              className="h-4 w-4 rounded border-[#E4DCD1]"
            />
            เปิดใช้งานส่ง SMS อัตโนมัติผ่าน Webhook เมื่อสินค้าถึงจุด ROP
          </label>
          <Field label="SMS Webhook URL (ไม่บังคับ — ต้องมี SMS Gateway ของตัวเอง)">
            <input type="text" value={draft.smsWebhookUrl} onChange={(e) => setDraft((d) => ({ ...d, smsWebhookUrl: e.target.value }))} className={inputCls} placeholder="https://script.google.com/macros/s/.../exec" />
          </Field>
          <TestSmsButton settings={draft} />
        </div>

        <div className="mt-1 mb-2 border-t border-[#EFE8E0] pt-3.5">
          <p className="text-xs font-medium text-[#2B1E2A] mb-2.5">ข้อความสั่งซื้ออัตโนมัติเมื่อถึงจุด ROP (แก้ไขได้)</p>
          {Object.entries(ORDER_GROUP_LABELS).filter(([k]) => k !== "custom").map(([k, label]) => (
            <Field key={k} label={label}>
              <textarea
                rows={3}
                value={draft.orderTemplates[k] || ""}
                onChange={(e) => setDraft((d) => ({ ...d, orderTemplates: { ...d.orderTemplates, [k]: e.target.value } }))}
                className={inputCls}
              />
            </Field>
          ))}
        </div>

        <div className="flex gap-2 mb-2">
          <button onClick={onClose} className="flex-1 rounded-full border border-[#E4DCD1] py-3 text-sm font-medium text-[#6B5B69]">ยกเลิก</button>
          <button onClick={confirm} className="flex-1 rounded-full bg-[#2B1E2A] py-3 text-sm font-medium text-white">ยืนยัน</button>
        </div>
        <button onClick={onReset} className="w-full flex items-center justify-center gap-1.5 rounded-full border border-[#E4DCD1] py-2.5 text-xs font-medium text-[#6B5B69]">
          <RotateCcw size={13} /> รีเซ็ตข้อมูลตัวอย่าง
        </button>
      </div>
    </div>
  );
}

function DownloadMenu({ onDownload, floating }) {
  const [open, setOpen] = useState(false);
  const options = [
    { scope: "all", label: "สรุปทั้งหมด" },
    { scope: "raw", label: "เฉพาะวัตถุดิบ" },
    { scope: "packaging", label: "เฉพาะบรรจุภัณฑ์" },
  ];
  return (
    <div className="relative">
      <button
        onClick={() => setOpen((o) => !o)}
        className={`flex items-center gap-1.5 rounded-full bg-[#E8A33D] text-black font-medium ${floating ? "px-4 py-3 text-xs shadow-lg" : "px-4 py-2 text-xs"}`}
      >
        <FileText size={14} /> ดาวน์โหลดไฟล์ <ChevronDown size={13} />
      </button>
      {open && (
        <>
          <div className="fixed inset-0 z-40" onClick={() => setOpen(false)} />
          <div className={`absolute z-50 ${floating ? "bottom-full mb-2 right-0" : "top-full mt-2 right-0"} w-48 rounded-xl border border-[#E4DCD1] bg-white shadow-lg overflow-hidden`}>
            {options.map((o) => (
              <button
                key={o.scope}
                onClick={() => { onDownload(o.scope); setOpen(false); }}
                className="w-full text-left px-3.5 py-2.5 text-xs text-black hover:bg-[#FBF9F6]"
              >
                {o.label}
              </button>
            ))}
          </div>
        </>
      )}
    </div>
  );
}

function CopyButton({ text }) {
  const [copied, setCopied] = useState(false);
  function doCopy() {
    if (navigator.clipboard && navigator.clipboard.writeText) {
      navigator.clipboard.writeText(text).then(() => {
        setCopied(true);
        setTimeout(() => setCopied(false), 2000);
      });
    }
  }
  return (
    <button onClick={doCopy} className="no-print flex items-center gap-1 rounded-full bg-white/70 px-2 py-1 text-[10px] font-medium text-[#2B1E2A] border border-[#D6432C]/30">
      {copied ? <Check size={11} /> : <Copy size={11} />} {copied ? "คัดลอกแล้ว" : "คัดลอก"}
    </button>
  );
}

function MovementTable({ title, items, movementTotals, onQuickAdjust }) {
  if (items.length === 0) return null;
  return (
    <div className="mb-4">
      <p className="text-xs font-semibold text-[#6B5B69] mb-1.5">{title}</p>
      <div className="overflow-x-auto">
        <table className="w-full text-[9.5px] border border-[#E4DCD1] rounded-lg overflow-hidden whitespace-nowrap">
          <thead>
            <tr className="bg-[#2B1E2A] text-white text-left">
              <th className="px-1.5 py-1.5 font-semibold">SKU no</th>
              <th className="px-1.5 py-1.5 font-semibold">SKU name</th>
              <th className="px-1.5 py-1.5 font-semibold text-right">Begin INV</th>
              <th className="px-1.5 py-1.5 font-semibold text-right">Received</th>
              <th className="px-1.5 py-1.5 font-semibold text-right">Transfer In</th>
              <th className="px-1.5 py-1.5 font-semibold text-right">Transfer out</th>
              <th className="px-1.5 py-1.5 font-semibold text-right">Mark out</th>
              <th className="px-1.5 py-1.5 font-semibold text-right">Sampling</th>
              <th className="px-1.5 py-1.5 font-semibold text-right">Expense others</th>
              <th className="px-1.5 py-1.5 font-semibold text-right">Sales</th>
              <th className="px-1.5 py-1.5 font-semibold text-right">On hand</th>
              <th className="px-1.5 py-1.5 font-semibold text-right">Count</th>
              {onQuickAdjust && <th className="no-print px-1.5 py-1.5 font-semibold text-center">ปรับสต็อก</th>}
            </tr>
          </thead>
          <tbody>
            {items.map((p) => {
              const t = movementTotals[p.id] || {};
              const netIn = (t.received || 0) + (t.transfer_in || 0);
              const netOut = (t.transfer_out || 0) + (t.mark_out || 0) + (t.sampling || 0) + (t.expense_others || 0) + (t.sales || 0);
              const beginInv = p.onHand - netIn + netOut;
              const countDiff = (p.physicalCount ?? p.onHand) - p.onHand;
              return (
                <tr key={p.id} className="border-t border-[#EFE8E0]">
                  <td className="px-1.5 py-1.5">{p.sku || "-"}</td>
                  <td className="px-1.5 py-1.5 font-medium">{p.name}</td>
                  <td className="px-1.5 py-1.5 text-right tabular">{fmt(beginInv, 1)}</td>
                  <td className="px-1.5 py-1.5 text-right tabular">{fmt(t.received || 0, 1)}</td>
                  <td className="px-1.5 py-1.5 text-right tabular">{fmt(t.transfer_in || 0, 1)}</td>
                  <td className="px-1.5 py-1.5 text-right tabular">{fmt(t.transfer_out || 0, 1)}</td>
                  <td className="px-1.5 py-1.5 text-right tabular">{fmt(t.mark_out || 0, 1)}</td>
                  <td className="px-1.5 py-1.5 text-right tabular">{fmt(t.sampling || 0, 1)}</td>
                  <td className="px-1.5 py-1.5 text-right tabular">{fmt(t.expense_others || 0, 1)}</td>
                  <td className="px-1.5 py-1.5 text-right tabular">{fmt(t.sales || 0, 1)}</td>
                  <td className="px-1.5 py-1.5 text-right tabular font-semibold">{fmt(p.onHand, 1)}</td>
                  <td className={`px-1.5 py-1.5 text-right tabular ${countDiff !== 0 ? "font-bold text-[#D6432C]" : ""}`}>{fmt(p.physicalCount ?? p.onHand, 1)}</td>
                  {onQuickAdjust && (
                    <td className="no-print px-1.5 py-1.5">
                      <div className="flex items-center justify-center gap-1">
                        <button onClick={() => onQuickAdjust(p.id, "out")} title="เบิก/ลด" className="flex h-5 w-5 items-center justify-center rounded-full bg-[#FBEAE7] text-[#D6432C]">
                          <MinusCircle size={12} />
                        </button>
                        <button onClick={() => onQuickAdjust(p.id, "in")} title="รับเข้า/เพิ่ม" className="flex h-5 w-5 items-center justify-center rounded-full bg-[#E9F4EC] text-[#3F8A5E]">
                          <PlusCircle size={12} />
                        </button>
                      </div>
                    </td>
                  )}
                </tr>
              );
            })}
          </tbody>
        </table>
      </div>
    </div>
  );
}

function SummaryView({ brand, products, settings, metricsById, abc, counts, totalValue, criticalItems, warningItems, alertLog, movementTotals, undoStack, undoLast, saveStatus, lastSavedAt, onRetrySave, onQuickAdjust, onBack }) {
  const now = new Date();
  const printRef = useRef(null);
  const rawSectionRef = useRef(null);
  const packagingSectionRef = useRef(null);
  const ropItems = [...criticalItems, ...warningItems];
  const orderGroups = {};
  ropItems.forEach((p) => {
    const key = p.orderGroup || "shopee_tiktok";
    if (!orderGroups[key]) orderGroups[key] = [];
    orderGroups[key].push(p);
  });
  const orderGroupEntries = Object.entries(orderGroups);
  const docStamp = `${now.getFullYear()}${String(now.getMonth() + 1).padStart(2, "0")}${String(now.getDate()).padStart(2, "0")}`;

  function wrapHtmlDoc(title, content) {
    return `<!DOCTYPE html>
<html lang="th">
<head>
<meta charset="UTF-8" />
<title>${title}</title>
<script src="https://cdn.tailwindcss.com"></script>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Prompt:wght@500;600;700&family=IBM+Plex+Sans+Thai:wght@400;500;600;700&display=swap');
  body { font-family: 'IBM Plex Sans Thai', sans-serif; background:#E9E4DC; margin:0; }
  .font-display { font-family: 'Prompt', sans-serif; }
  .font-body { font-family: 'IBM Plex Sans Thai', sans-serif; }
  .tabular { font-variant-numeric: tabular-nums; }
  @page { size: A4; margin: 12mm; }
  @media print {
    body { background: white !important; }
    .print-page { box-shadow: none !important; margin: 0 !important; max-width: 100% !important; }
    * { -webkit-print-color-adjust: exact; print-color-adjust: exact; }
    table { page-break-inside: auto; }
    tr { page-break-inside: avoid; page-break-after: auto; }
  }
</style>
</head>
<body>
${content}
<script>window.onload = function () { setTimeout(function () { window.print(); }, 400); };</script>
</body>
</html>`;
  }

  function triggerDownload(filename, html) {
    const blob = new Blob([html], { type: "text/html" });
    const url = URL.createObjectURL(blob);
    const a = document.createElement("a");
    a.href = url;
    a.download = filename;
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
    URL.revokeObjectURL(url);
  }

  function downloadPrintableHtml(scope = "all") {
    if (scope === "raw" || scope === "packaging") {
      const ref = scope === "raw" ? rawSectionRef : packagingSectionRef;
      const label = scope === "raw" ? "วัตถุดิบ (Raw Materials)" : "บรรจุภัณฑ์ (Packaging)";
      const tableHtml = ref.current ? ref.current.outerHTML : "";
      const content = `
        <div class="print-page mx-auto max-w-3xl bg-white my-6 p-8 shadow-sm">
          <div class="flex items-center justify-between border-b-2 border-[#2B1E2A] pb-4 mb-5">
            <div>
              <h1 class="font-display text-2xl font-bold">${brand}</h1>
              <p class="text-xs text-[#8A7A87]">เอกสารสรุปการเคลื่อนไหวสต็อก — ${label}</p>
            </div>
            <div class="text-right text-xs text-[#8A7A87] tabular">
              <div>เลขที่เอกสาร: PO-${docStamp}-${String(now.getHours()).padStart(2, "0")}${String(now.getMinutes()).padStart(2, "0")}</div>
              <div>วันที่ออกเอกสาร: ${now.toLocaleDateString("th-TH", { day: "2-digit", month: "long", year: "numeric" })}</div>
            </div>
          </div>
          ${tableHtml}
        </div>`;
      triggerDownload(`${brand.replace(/\s+/g, "-")}-${scope}-${docStamp}.html`, wrapHtmlDoc(`${brand} — ${label}`, content));
      return;
    }
    const content = printRef.current ? printRef.current.outerHTML : "";
    triggerDownload(`${brand.replace(/\s+/g, "-")}-stock-summary-${docStamp}.html`, wrapHtmlDoc(`${brand} — Stock Summary`, content));
  }

  return (
    <div className="min-h-screen bg-[#E9E4DC] font-body text-black">
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Prompt:wght@500;600;700&family=IBM+Plex+Sans+Thai:wght@400;500;600;700&display=swap');
        .font-display { font-family: 'Prompt', sans-serif; }
        .font-body { font-family: 'IBM Plex Sans Thai', sans-serif; }
        .tabular { font-variant-numeric: tabular-nums; }
        @media print {
          .no-print { display: none !important; }
          body { background: white !important; }
          .print-page { box-shadow: none !important; margin: 0 !important; max-width: 100% !important; }
          * { -webkit-print-color-adjust: exact; print-color-adjust: exact; }
          @page { size: A4; margin: 12mm; }
          table { page-break-inside: auto; }
          tr { page-break-inside: avoid; page-break-after: auto; }
        }
      `}</style>

      <div className="no-print fixed bottom-5 right-5 z-50 sm:hidden">
        <DownloadMenu onDownload={downloadPrintableHtml} floating />
      </div>

      <div className="no-print sticky top-0 z-30 border-b border-[#E4DCD1] bg-white text-black px-4 py-3 flex items-center justify-between gap-2 flex-wrap shadow-sm">
        <div className="flex items-center gap-2">
          <button onClick={onBack} title="กลับหน้าแรก" className="flex items-center justify-center rounded-full border border-[#E4DCD1] text-black w-8 h-8">
            <Home size={14} />
          </button>
          <button onClick={onBack} className="flex items-center gap-1.5 text-xs font-medium text-black">
            <ArrowLeft size={15} /> กลับไปที่แอป
          </button>
        </div>
        <SaveStatusBadge status={saveStatus} lastSavedAt={lastSavedAt} onRetry={onRetrySave} onLight />
        <div className="flex items-center gap-2">
          <button
            onClick={undoLast}
            disabled={undoStack.length === 0}
            title={undoStack.length > 0 ? `ย้อนกลับ: ${undoStack[0].label}` : "ไม่มีรายการให้ย้อนกลับ"}
            className={`flex items-center gap-1.5 rounded-full border border-[#E4DCD1] px-3 py-2 text-xs font-medium ${undoStack.length === 0 ? "text-[#C9BFC7] opacity-70" : "text-black"}`}
          >
            <Undo2 size={13} /> ย้อนกลับ
          </button>
          <DownloadMenu onDownload={downloadPrintableHtml} />
        </div>
      </div>

      <div ref={printRef} className="print-page mx-auto max-w-3xl bg-white my-6 p-8 shadow-sm">
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

        <div className="mb-6">
          <div className="rounded-lg border border-[#2B1E2A] px-4 py-3 mb-3 flex flex-wrap items-center justify-between gap-2">
            <div>
              <h2 className="font-display text-sm font-bold">เอกสารสรุปการเคลื่อนไหวสต็อก (Stock Movement / Purchasing Document)</h2>
              <p className="text-[10px] text-[#6B5B69] mt-0.5">{brand} Stock Manager</p>
            </div>
            <div className="text-[10px] text-[#6B5B69] text-right tabular">
              <div>เลขที่เอกสาร: PO-{now.getFullYear()}{String(now.getMonth() + 1).padStart(2, "0")}{String(now.getDate()).padStart(2, "0")}-{String(now.getHours()).padStart(2, "0")}{String(now.getMinutes()).padStart(2, "0")}</div>
              <div>วันที่ออกเอกสาร: {now.toLocaleDateString("th-TH", { day: "2-digit", month: "long", year: "numeric" })}</div>
            </div>
          </div>
          <div ref={rawSectionRef}>
            <MovementTable title="วัตถุดิบ (Raw Materials)" items={products.filter((p) => p.category === "raw")} movementTotals={movementTotals} onQuickAdjust={onQuickAdjust} />
          </div>
          <div ref={packagingSectionRef}>
            <MovementTable title="บรรจุภัณฑ์ (Packaging)" items={products.filter((p) => p.category === "packaging")} movementTotals={movementTotals} onQuickAdjust={onQuickAdjust} />
          </div>
        </div>

        {orderGroupEntries.length > 0 && (
          <div className="mb-6">
            <h2 className="font-display text-sm font-bold mb-2 flex items-center gap-1.5">
              <FileText size={15} /> สรุปจุดสั่งซื้อ — ข้อความที่ต้องส่ง ({orderGroupEntries.length} รายการ)
            </h2>
            <div className="space-y-2.5">
              {orderGroupEntries.map(([groupKey, items], idx) => (
                <div key={groupKey} className="rounded-lg border-2 border-[#D6432C]/50 bg-[#FBEAE7] px-3.5 py-3">
                  <div className="flex items-center justify-between mb-2 gap-2">
                    <span className="text-[11px] font-bold text-[#D6432C]">{idx + 1}. {ORDER_GROUP_LABELS[groupKey] || groupKey}</span>
                    <div className="no-print flex items-center gap-1.5">
                      <CopyButton text={buildOrderCopyText(items, settings)} />
                      <a
                        href={buildSmsLink(settings.smsPhone || DEFAULT_SMS_PHONE, buildOrderCopyText(items, settings))}
                        className="flex items-center gap-1 rounded-full bg-[#4F9D8D] px-2 py-1 text-[10px] font-medium text-white"
                      >
                        <MessageSquareText size={11} /> SMS
                      </a>
                    </div>
                  </div>
                  <div className="space-y-1.5 mb-2">
                    {items.map((p) => {
                      const m = metricsById[p.id];
                      const where = p.supplier?.trim() ? p.supplier : (ORDER_GROUP_LABELS[p.orderGroup] || "-");
                      return (
                        <div key={p.id} className="text-[10.5px] text-black bg-white/60 rounded px-2 py-1.5">
                          <span className="font-semibold">{p.name}</span>
                          {" — "}ซื้อที่: {where} · ปริมาณ: {fmt(m.eoq)} {p.unit} · ราคา: ฿{fmt(m.costPerUnit, 2)}/{p.unit}
                        </div>
                      );
                    })}
                  </div>
                  <p className="text-xs whitespace-pre-wrap text-black font-medium border-t border-[#D6432C]/20 pt-2">
                    {getOrderMessage(items[0], settings)}
                  </p>
                </div>
              ))}
            </div>
          </div>
        )}

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
              </tr>
            </thead>
            <tbody>
              {[...products].sort((a, b) => metricsById[a.id].daysRemaining - metricsById[b.id].daysRemaining).map((p) => {
                const m = metricsById[p.id];
                const isOut = m.status === "out";
                const isRed = m.status === "red";
                const isYellow = m.status === "yellow";
                return (
                  <tr key={p.id} className={`border-t border-[#EFE8E0] ${isOut ? "bg-[#7A140A]/10" : isRed ? "bg-[#FBEAE7]" : isYellow ? "bg-[#FBF1DF]" : ""}`}>
                    <td className={`px-2 py-1.5 ${isOut ? "font-bold text-[#7A140A]" : isRed ? "font-bold text-[#D6432C]" : ""}`}>{p.name}</td>
                    <td className="px-2 py-1.5 text-[#6B5B69]">{p.category === "raw" ? "วัตถุดิบ" : "บรรจุภัณฑ์"}</td>
                    <td className={`px-2 py-1.5 text-right tabular ${isOut ? "font-bold text-[#7A140A]" : isRed ? "font-bold text-[#D6432C]" : ""}`}>{fmt(p.onHand)} {p.unit}</td>
                    <td className={`px-2 py-1.5 text-right tabular ${isOut ? "font-bold text-[#7A140A]" : isRed ? "font-bold text-[#D6432C]" : ""}`}>{fmt(m.daysRemaining, 1)}</td>
                    <td className="px-2 py-1.5 text-center">
                      <span className={`font-bold ${isOut ? "text-[#7A140A]" : isRed ? "text-[#D6432C]" : isYellow ? "text-[#C98A22]" : "text-[#3F8A5E]"}`}>
                        {isOut ? "หมดแล้ว" : isRed ? "ใกล้หมด" : isYellow ? "เริ่มน้อย" : "ปกติ"}
                      </span>
                    </td>
                  </tr>
                );
              })}
            </tbody>
          </table>
        </div>

        {alertLog.length > 0 && (
          <div className="mb-6">
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
