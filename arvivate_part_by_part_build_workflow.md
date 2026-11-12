# ARVIVATE: Step-by-Step Build Workflow ("Engine First" Strategy)

**Locked Decision (Question 10):** Option A — Engine First (Backend Lego Blocks $\rightarrow$ Manager Orchestrator $\rightarrow$ Next.js UI Last)

**Core Rule:** Har ek hisse ko pehle akele banaya jayega, real data par test kiya jayega, aur jab wo 100% pass ho jayega tabhi agle part par badha jayega.

## 1. Yeh "Part-by-Part" Workflow Kyun Zaroori Hai? (Simple Explanation)

Agar aap AI (Claude Code / Codex / DeepSeek) ko ek hi baar mein poora Arvivate app banane ko bolenge, toh wo 15–20 files ek saath bana dega. Ek beginner ke liye usmein aane wale errors ko pakadna namumkin ho jayega, aur aapke free API tokens aur 12GB RAM dono waste honge.

Isliye hum **Lego Blocks** ki tarah kaam karenge:

* **Step-by-Step Control:** Aap har ek block ko chala kar dekh sakte hain ki wo aapke `arvivate_complete_blueprint.md` ke rules maanta hai ya nahi.

* **Zero UI Rework:** Pehle peeche ka poora Python engine ready hoga, aur sabse aakhir mein uske upar sundar Next.js website lagayi jayegi—taaki UI ka code sirf ek hi baar likhna pade.

* **Low RAM Load:** Ek time par sirf ek chhoti file chalegi, jisse aapke Intel i3 + 12GB RAM machine par kabhi hang hone ka khatra nahi rahega.

## 2. Folder Setup (Sabse Pehla Kadam)

Apne computer mein `Arvivate_Project` naam ka ek main folder banayein aur usmein yeh do rulebook files sabse pehle rakhein:

1. `arvivate_complete_blueprint.md` (Aapka master architecture aur 10 locked decisions).

2. `CLAUDE.md` (Jisme likha ho ki Claude Code koi bhi kaam karne se pehle `arvivate_complete_blueprint.md` zaroor padhe).

## 3. The 6-Part Build Sequence (Kya, Kab, Aur Kaise Banega)

### Part 1: The AI Brain & Fallback Router (`llm_router.py`)

* **Kya Hai Yeh:** Yeh aapke system ka "Telephone Exchange" hai. Jab bhi kisi agent ko AI se kuch poochhna hoga, wo isi file ke paas aayega.

* **Kaise Kaam Karta Hai:** Yeh pehle **Google Gemini Free Tier** ko call bhejega. Agar Gemini busy hai ya rate-limit (`HTTP 429`) aa gaya, toh yeh khud-ba-khud **Groq ya OpenRouter** ke free model par switch ho jayega.

* **Testing & Pass Rule:** Terminal mein ek chhota test chalega. Jab screen par AI ka clean jawab print ho jayega, tab Part 1 complete mana jayega.

### Part 2: Unit Normalizer & Pure Python Math Engine (`math_engine.py`)

* **Kya Hai Yeh:** Yeh aapka "100% Accurate CA (Calculator)" hai.

* **Kaise Kaam Karta Hai:**

  1. **Unit Normalizer:** Pehle check karta hai ki numbers `₹ in Lakhs` mein hain ya `₹ in Crores` mein. Agar Lakhs mein hain, toh `100` se divide karke sabko `Crores` mein barabar karta hai.

  2. **Pure Python Calculator:** Uske baad pure Python formulas se **Debt-to-Equity, ROE, Profit Margin, aur YoY Growth** calculate karta hai (AI ko math karne ki bilkul permission nahi hoti).

* **Testing & Pass Rule:** Hum isko fake mixed numbers denge (jaise Debt `500 Lakhs` aur Equity `20 Crores`). Jab yeh `0.25x` (exact sahi jawab) dikhayega, tab Part 2 lock hoga.

### Part 3: Local PDF Chunker & `"Not Disclosed"` Guard (`pdf_extractor.py`)

* **Kya Hai Yeh:** Yeh aapka "Smart Document Reader" hai jo 300-page ki Annual Report ko bina RAM full kiye padhta hai.

* **Kaise Kaam Karta Hai:**

  1. **Local Chunking:** Poori PDF cloud par bhejne ke bajaye, yeh aapke PC par hi sirf kaam ke panne (*Balance Sheet, Profit & Loss, Auditor Report*) chhant kar nikalta hai.

  2. **Sanitizer & Hallucination Lock:** AI se raw numbers nikalwata hai, toote hue JSON ko regex se saaf karta hai, aur agar koi number PDF mein nahi milta toh ek micro-search maarta hai—warna strictly `"Not Disclosed"` lock kar deta hai.

* **Testing & Pass Rule:** Ek asli Indian company ki PDF daal kar check karenge ki yeh sahi table nikal raha hai aur missing data par tukka nahi maar raha.

### Part 4: Legal Scout & Target-Locked Browser Sniper (`legal_scout.py`)

* **Kya Hai Yeh:** Yeh aapka "Detective" hai jo company par chal rahe NCLT cases, SEBI orders, ya court disputes dhoondhta hai.

* **Kaise Kaam Karta Hai:**

  1. **Lightweight Search First:** Pehle 0MB RAM wali simple search se news aur court order ke links dhoondhta hai.

  2. **The Sniper Trigger:** Jab koi exact court/NCLT link milta hai jo CAPTCHA ya heavy page ke peeche hai, sirf tab aapka **GitHub Browser Tool** ek single tab mein khulta hai, text nikalta hai, aur turant close ho jata hai.

* **Testing & Pass Rule:** Ek company ka naam daal kar verify karenge ki browser sirf target URL par hi khule aur kaam hote hi RAM se hat jaye.

### Part 5: The Master Manager, Checkpoints & Database (`manager.py`)

* **Kya Hai Yeh:** Yeh aapke poore backend ka "Captain" hai jo Parts 1, 2, 3, aur 4 ko ek saath jodta hai.

* **Kaise Kaam Karta Hai:**

  1. **Identity & Cache Check:** Pehle company ka **CIN** verify karta hai aur **Supabase** database mein dekhta hai ki purani report hai ya nahi.

  2. **The Traffic Light (Batched Async):** Pehle Batch 1 (News + Legal) chalata hai $\rightarrow$ chhota pause leta hai $\rightarrow$ phir Batch 2 (PDF + Math + Judge) chalata hai.

  3. **Checkpoints (`temp_checkpoint.json`):** Har step ke baad local file mein progress save karta hai taaki crash hone par wahin se resume ho sake, aur aakhir mein final JSON Supabase cloud par save karke temp file delete kar deta hai.

* **Testing & Pass Rule:** Terminal mein ek asli company ka CIN daal kar poori end-to-end report generate hote aur Supabase mein save hote dekhna.

### Part 6: Next.js Dashboard & Live Step Tracker UI (Frontend — Sabse Aakhir Mein)

* **Kya Hai Yeh:** Yeh aapka "Showroom" hai jo B2B investors ko screen par dikhega.

* **Kaise Kaam Karta Hai:**

  1. **Two-Step Search:** User company search karega $\rightarrow$ dropdown se exact **CIN** select karega.

  2. **Dynamic Refresh & Tracker:** Agar purani report hai toh `0.1s` mein khulegi aur upar live alert check chalega; agar nayi company hai toh screen par live checklist (`✅ Verified` $\rightarrow$ `⏳ Reading PDF` $\rightarrow$ `⬜ Ratios`) har 2 second mein update hogi.

* **Testing & Pass Rule:** Browser mein website khol kar ek company search karna aur poori report ko clean tables aur risk badges ke saath screen par dekhna.

## 4. Har Part Banwane Ka Standard Rule (Golden Loop)

Jab bhi aap Claude Code se koi bhi Part banwayein, toh hamesha is **3-Step Loop** ko follow karein:

1. **Order Dein (With Blueprint Reference):** Claude ko bolein ki pehle `@arvivate_complete_blueprint.md` padhe aur sirf aaj ka ek Part banaye.

2. **Test Karein (Verify Output):** Code banne ke baad usko terminal mein chala kar dekhein. Agar koi error aaye, toh error copy karke Claude ko dein aur fix karwayein.

3. **Lock Karein (Move Next):** Jab ek Part bina error ke sahi result de de, tabhi agle Part ki shuruat karein.