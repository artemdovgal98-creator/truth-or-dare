"use client";


import { useEffect, useState, useMemo, useCallback, useRef } from "react";
import { toast } from "sonner";
import { api } from "@/lib/api";
import { categories, categoryMeta, getAnonCreate } from "@/lib/pravda";
import { BADGES, MAX_POLL_OPTIONS } from "@/lib/story-format";
import { StoryCategory } from "@/types/StoryCategory";
import { ProfileData, ProfileSheet } from "@/pravda/ProfileSheet";
import { Tab, Story, Category } from "@/types/db";
import { DuelArena } from "@/components/DuelArena";
import { Roulette } from "@/components/Roulette";
import { MarketService } from "@/components/MarketService";
import { AdminPanel } from "@/pravda/AdminPanel";
import { VoiceRecorder } from "@/components/VoiceRecorder";
import { CommentsSheet } from "@/components/CommentsSheet";


type ActiveTab = "feed" | "add" | "battle" | "roulette" | "market";


const TABS: { id: ActiveTab; label: string }[] = [
  { id: "feed", label: "Лента" },
  { id: "add", label: "Добавить" },
  { id: "battle", label: "Битва" },
  { id: "roulette", label: "Рулетка" },
  { id: "market", label: "Рынок" },
];


const LIKED_KEY = "pravda_liked_stories";
const PENDING_KEY = "pravda_pending_purchase";
const CODE_KEY = "pravda_anon_code";
const MAX_PHOTOS = 10;
const MAX_LINKS = 5;


const SORTS: { id: "new" | "top"; label: string }[] = [
  { id: "new", label: "Свежее" },
  { id: "top", label: "Топ" },
];


const PERIODS: { id: "today" | "week" | "all"; label: string }[] = [
  { id: "today", label: "Сегодня" },
  { id: "week", label: "Неделя" },
  { id: "all", label: "За все время" },
];


interface CrystalPackage {
  crystals: number;
  usd: number;
  tag: string;
}


interface Costs {
  vip_boost_on_publish: number;
  erase_story: number;
  vip_status: number;
  comment: number;
  reaction: number;
  badge_important: number;
  badge_inside: number;
  pin_top: number;
  roulette_spin: number;
  incognito: number;
  min_tip: number;
  min_duel_stake: number;
  min_bet: number;
}


const DEFAULT_COSTS: Costs = {
  vip_boost_on_publish: 5,
  erase_story: 20,
  vip_status: 10,
  comment: 1,
  reaction: 2,
  badge_important: 3,
  badge_inside: 6,
  pin_top: 25,
  roulette_spin: 2,
  incognito: 15,
  min_tip: 1,
  min_duel_stake: 5,
  min_bet: 1,
};


interface MarketConfig {
  packages: CrystalPackage[];
  costs: Costs;
  payments_configured: boolean;
  nowpayments_label: string;
  ipn_label: string;
  bot_label: string;
}


interface PendingPurchase {
  purchase_id: string;
  invoice_url: string;
  crystals: number;
}


export default function Main() {
  const [tab, setTab] = useState<ActiveTab>("feed");
  const [period, setPeriod] = useState<"today" | "week" | "all">("today");
  const [tag, setTag] = useState<string>("none");
  const [pollQuestion, setPollQuestion] = useState("");
  const [pollOptions, setPollOptions] = useState<string[]>([]);
  const [vipBoost, setVipBoost] = useState(false);
  const [publishing, setPublishing] = useState(false);
  const fileInputRef = useRef<HTMLInputElement>(null);


  const [checkingPayment, setCheckingPayment] = useState(false);
  const [commentsStory, setCommentsStory] = useState<Story | null>(null);
  const [tipStory, setTipStory] = useState<Story | null>(null);
  const [tipAmount, setTipAmount] = useState<number>(3);
  const [tipBusy, setTipBusy] = useState<Story | null>(null);
  const [liked, setLiked] = useState<string[]>([]);
  const [pending, setPending] = useState<PendingPurchase | null>(null);
  
  const costs = DEFAULT_COSTS;


  useEffect(() => {
    const wanted = new URLSearchParams(window.location.search).get("tab");
    const tabs: ActiveTab[] = ["feed", "add", "battle", "roulette", "market"];
    if (wanted && tabs.includes(wanted as ActiveTab)) {
      setTab(wanted as ActiveTab);
    }
  }, []);


  useEffect(() => {
    try {
      const storedLiked = localStorage.getItem(LIKED_KEY);
      if (storedLiked) setLiked(JSON.parse(storedLiked));
      const rawPending = localStorage.getItem(PENDING_KEY);
      if (rawPending) setPending(JSON.parse(rawPending));
    } catch (err) {
      console.error("[pravda] failed to read localStorage", err);
    }
  }, []);


  return null;
}