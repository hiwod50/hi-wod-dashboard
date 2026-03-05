// =======================================================
// HI-WOD DASHBOARD — Vite + React + TS + Tailwind + Recharts
// =======================================================
//
// INSTRUCCIONES IMPORTANTES:
// 1) Crea una carpeta: hi-wod-dashboard
// 2) Dentro, crea cada archivo con el contenido correspondiente.
//
// -------------------------------------------------------
// FILE: package.json
// -------------------------------------------------------
{
  "name": "hi-wod-dashboard",
  "private": true,
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc -b && vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "clsx": "^2.1.1",
    "react": "^18.3.1",
    "react-dom": "^18.3.1",
    "recharts": "^2.12.7"
  },
  "devDependencies": {
    "@types/react": "^18.3.12",
    "@types/react-dom": "^18.3.1",
    "@vitejs/plugin-react": "^4.3.2",
    "autoprefixer": "^10.4.20",
    "postcss": "^8.4.47",
    "tailwindcss": "^3.4.13",
    "typescript": "^5.6.3",
    "vite": "^5.4.10"
  }
}

// -------------------------------------------------------
// FILE: vite.config.ts
// -------------------------------------------------------
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()]
});

// -------------------------------------------------------
// FILE: tsconfig.json
// -------------------------------------------------------
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "jsx": "react-jsx",
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "strict": true,
    "skipLibCheck": true,
    "noEmit": true,
    "types": ["vite/client"]
  },
  "include": ["src"]
}

// -------------------------------------------------------
// FILE: tailwind.config.js
// -------------------------------------------------------
/** @type {import('tailwindcss').Config} */
export default {
  content: ["./index.html", "./src/**/*.{ts,tsx}"],
  theme: {
    extend: {
      borderRadius: {
        xl: "1rem",
        "2xl": "1.25rem"
      }
    }
  },
  plugins: []
};

// -------------------------------------------------------
// FILE: postcss.config.js
// -------------------------------------------------------
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {}
  }
};

// -------------------------------------------------------
// FILE: index.html
// -------------------------------------------------------
<!doctype html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/logo.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>HI-WOD Dashboard</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>

// -------------------------------------------------------
// FILE: public/logo.svg   (placeholder; sustitúyelo por tu logo real si quieres)
// -------------------------------------------------------
<svg xmlns="http://www.w3.org/2000/svg" width="320" height="96" viewBox="0 0 320 96">
  <rect width="320" height="96" rx="20" fill="#0b0f14"/>
  <text x="22" y="60" font-family="Arial, Helvetica, sans-serif" font-size="42" fill="#40E0D0" font-weight="800">
    HI-WOD
  </text>
  <text x="22" y="84" font-family="Arial, Helvetica, sans-serif" font-size="14" fill="#A7B3C3" font-weight="600">
    Entrena. Registra. Mejora.
  </text>
</svg>

// -------------------------------------------------------
// FILE: src/styles.css
// -------------------------------------------------------
@tailwind base;
@tailwind components;
@tailwind utilities;

:root{
  --bg: #070A0E;
  --panel: #0C1118;
  --panel2:#0F1620;
  --text: #E7EEF7;
  --muted:#A7B3C3;
  --border:#1C2633;
  --accent:#40E0D0;
  --danger:#FF5A7A;
  --warn:#FFC857;
}

html, body { height: 100%; }
body{
  margin:0;
  background: var(--bg);
  color: var(--text);
  font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial, "Apple Color Emoji","Segoe UI Emoji";
}

::selection { background: rgba(64,224,208,0.25); }

a { color: inherit; }

// -------------------------------------------------------
// FILE: src/main.tsx
// -------------------------------------------------------
import React from "react";
import ReactDOM from "react-dom/client";
import "./styles.css";
import { App } from "./App";

ReactDOM.createRoot(document.getElementById("root")!).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

// -------------------------------------------------------
// FILE: src/lib/types.ts
// -------------------------------------------------------
export type Level = "Beginner" | "Intermediate" | "Advanced";
export type ScoreType = "Tiempo" | "Reps" | "Carga";
export type Tag = "HYROX" | "CrossFit" | "Fuerza" | "Carrera";

export type Workout = {
  id: string;
  name: string;
  objective: string;
  durationMin: number;
  equipment: string[];
  tags: Tag[];
  details: {
    warmup: string[];
    main: string[];
    accessory?: string[];
    cooldown?: string[];
  };
};

export type ProgramDay = {
  id: string;
  dow: number; // 0 Domingo - 6 Sábado
  workoutId: string;
  phase: "Base" | "Build" | "Peak" | "Taper";
};

export type LoadEntry = { movement: string; kg: number; reps: number };

export type CompletedWorkout = {
  id: string;
  dateISO: string;
  workoutId: string;
  scoreType: ScoreType;
  scoreValue: number; // Tiempo=minutos, Reps=reps, Carga=kg
  loads: LoadEntry[];
  notes?: string;
  rpe: number; // 1-10
};

export type UserProfile = {
  name: string;
  level: Level;
  goal: "HYROX" | "Hybrid" | "CrossFit Performance" | "Salud";
  daysPerWeek: number;
  typicalDurationMin: number;
  equipmentOwned: Record<string, boolean>;
  planId: string;
};

export type PR = {
  movement: string;
  bestKg: number;
  dateISO: string;
};

// -------------------------------------------------------
// FILE: src/lib/date.ts
// -------------------------------------------------------
export function isoToday(): string {
  const d = new Date();
  return d.toISOString().slice(0, 10);
}

export function addDaysISO(iso: string, days: number): string {
  const d = new Date(iso + "T00:00:00");
  d.setDate(d.getDate() + days);
  return d.toISOString().slice(0, 10);
}

export function formatShort(iso: string): string {
  const d = new Date(iso + "T00:00:00");
  const dd = String(d.getDate()).padStart(2, "0");
  const mm = String(d.getMonth() + 1).padStart(2, "0");
  return `${dd}/${mm}`;
}

export function dayNameES(dow: number): string {
  return ["Dom", "Lun", "Mar", "Mié", "Jue", "Vie", "Sáb"][dow] ?? "—";
}

export function dowFromISO(iso: string): number {
  const d = new Date(iso + "T00:00:00");
  return d.getDay();
}

// -------------------------------------------------------
// FILE: src/lib/storage.ts
// -------------------------------------------------------
const KEY = "hiwod_v1_state";

export type PersistedState = {
  user: any;
  completed: any[];
};

export function loadState<T>(fallback: T): T {
  try {
    const raw = localStorage.getItem(KEY);
    if (!raw) return fallback;
    const parsed = JSON.parse(raw);
    return parsed as T;
  } catch {
    return fallback;
  }
}

export function saveState(state: PersistedState) {
  try {
    localStorage.setItem(KEY, JSON.stringify(state));
  } catch {
    // ignore
  }
}

export function resetState() {
  try {
    localStorage.removeItem(KEY);
  } catch {
    // ignore
  }
}

// -------------------------------------------------------
// FILE: src/lib/insights.ts
// -------------------------------------------------------
import { CompletedWorkout, ScoreType, Workout } from "./types";

function median(values: number[]): number {
  const v = [...values].sort((a, b) => a - b);
  const mid = Math.floor(v.length / 2);
  return v.length % 2 ? v[mid] : (v[mid - 1] + v[mid]) / 2;
}

function isWorse(scoreType: ScoreType, current: number, baseline: number): boolean {
  // Para Tiempo: más alto = peor. Para Reps/Carga: más bajo = peor.
  if (scoreType === "Tiempo") return current > baseline * 1.1; // +10% peor
  return current < baseline * 0.9; // -10% peor
}

export function quickInsight(rpe: number, scoreType: ScoreType, scoreValue: number): string {
  if (rpe <= 5) return "Sesión controlada. Buen día para consolidar técnica y consistencia.";
  if (rpe <= 7) return "Intensidad bien calibrada. Mantén el volumen y cuida el pacing.";
  if (rpe <= 8) return "Alta demanda. Prioriza recuperación (sueño/hidratación) y mantén calidad de movimiento.";
  return "Muy exigente. Considera reducir volumen 20–30% si se repite y revisa recuperación.";
}

export function fatigueAlert(completed: CompletedWorkout[]): { show: boolean; message: string } {
  if (completed.length < 5) return { show: false, message: "" };

  const last = completed.slice(-3);
  const avgRPE = last.reduce((a, x) => a + x.rpe, 0) / last.length;

  if (avgRPE < 8) return { show: false, message: "" };

  // Detectar empeoramiento comparando la última sesión contra baseline histórico por mismo tipo de score
  const lastOne = completed[completed.length - 1];
  const sameTypeHistory = completed
    .slice(0, -1)
    .filter((c) => c.scoreType === lastOne.scoreType && c.workoutId === lastOne.workoutId)
    .map((c) => c.scoreValue);

  // Si no hay histórico del mismo workout, usar baseline global por scoreType
  const fallbackHistory = completed
    .slice(0, -1)
    .filter((c) => c.scoreType === lastOne.scoreType)
    .map((c) => c.scoreValue);

  const baseArray = sameTypeHistory.length >= 3 ? sameTypeHistory : fallbackHistory;
  if (baseArray.length < 5) return { show: true, message: "RPE alto sostenido. Señales de fatiga: ajusta recuperación y reduce volumen 20–30% si se mantiene." };

  const base = median(baseArray);
  if (isWorse(lastOne.scoreType, lastOne.scoreValue, base)) {
    return {
      show: true,
      message: "Señales de fatiga: RPE alto sostenido + rendimiento empeorando. Recomendación: recorta volumen 20–30% y prioriza recuperación."
    };
  }

  return { show: true, message: "RPE alto sostenido. Ajusta sueño/estrés y mantén la técnica: si se repite, recorta volumen 20–30%." };
}

export function workoutLabel(w: Workout | undefined): string {
  if (!w) return "Sesión";
  return w.name;
}

// -------------------------------------------------------
// FILE: src/data/plans.ts
// -------------------------------------------------------
export type Plan = {
  id: string;
  name: string;
  priceEUR: number;
  note?: string;
  bullets: string[];
  highlight?: boolean;
};

export const PLANS: Plan[] = [
  {
    id: "starter",
    name: "Starter",
    priceEUR: 99,
    bullets: ["Base estructurada", "Registro de progreso", "Acceso esencial"]
  },
  {
    id: "build",
    name: "Build",
    priceEUR: 119,
    bullets: ["Bloques por objetivo", "Mejor control de carga", "Más volumen útil"]
  },
  {
    id: "performance-179",
    name: "Performance",
    priceEUR: 179,
    note: "Opción 179€",
    bullets: ["Progresión intensiva", "Analíticas completas", "Enfoque performance"],
    highlight: true
  },
  {
    id: "performance-199",
    name: "Performance",
    priceEUR: 199,
    note: "Opción 199€",
    bullets: ["Más personalización", "Bloques extendidos", "Mayor control de intensidad"]
  },
  {
    id: "competition-299",
    name: "Competition",
    priceEUR: 299,
    note: "Revisiones Q2W",
    bullets: ["Preparación competición", "Revisiones cada 2 semanas (Q2W)", "KPIs y tests"]
  },
  {
    id: "competition-399",
    name: "Competition+",
    priceEUR: 399,
    note: "Revisiones Q2W",
    bullets: ["Soporte ampliado", "Ajustes finos por objetivo", "Revisiones Q2W"]
  }
];

// -------------------------------------------------------
// FILE: src/data/mock.ts
// -------------------------------------------------------
import { addDaysISO, isoToday } from "../lib/date";
import { CompletedWorkout, ProgramDay, UserProfile, Workout } from "../lib/types";

export const EQUIPMENT_CATALOG = [
  "AirBike",
  "Cinta / espacio 20m",
  "Kettlebells",
  "Mancuernas",
  "Barra y discos",
  "Cajón",
  "Comba",
  "Anillas",
  "Saco (sandbag)",
  "Trineo (sled)"
];

export const WORKOUTS: Workout[] = [
  {
    id: "wod-engine",
    name: "HI-WOD | Engine + Carry",
    objective: "Motor sostenido + tolerancia al lactato (perfil HYROX)",
    durationMin: 60,
    equipment: ["AirBike", "Kettlebells", "Mancuernas", "Cinta / espacio 20m"],
    tags: ["HYROX", "Carrera"],
    details: {
      warmup: [
        "8' Z2 AirBike",
        "Movilidad tobillo/cadera 4'",
        "2 rondas: 10 air squats + 10 KB deadlift + 8 burpees suaves"
      ],
      main: [
        "4 rondas (RPE objetivo 7–8):",
        "• 600m run (shuttle 20m) o 2' carrera",
        "• 20 KB swings",
        "• 20 DB walking lunges",
        "• 12 burpees",
        "Descanso 2' entre rondas"
      ],
      cooldown: ["5' Z1", "Respiración 2' + estiramientos 6'"]
    }
  },
  {
    id: "wod-strength",
    name: "HI-WOD | Strength Day",
    objective: "Fuerza base + técnica (sin quemar)",
    durationMin: 60,
    equipment: ["Barra y discos", "Cajón"],
    tags: ["Fuerza", "CrossFit"],
    details: {
      warmup: ["RAMP 10': bisagra + core + hombro", "2 series técnicas de sentadilla"],
      main: ["Back Squat 5x5 (RPE 7)", "Superset: 5x8 row + 5x10 box step-ups"],
      cooldown: ["Movilidad + 5' caminata suave"]
    }
  },
  {
    id: "wod-mixed",
    name: "HI-WOD | Mixed Modal",
    objective: "Capacidad mixta + pacing",
    durationMin: 55,
    equipment: ["Mancuernas", "Comba", "Cajón"],
    tags: ["CrossFit"],
    details: {
      warmup: ["6' comba suave", "2 rondas técnica DB: 8 thrusters + 8 snatch alterno"],
      main: ["AMRAP 18': 12 DB thrusters + 30 DU + 12 box jumps"],
      cooldown: ["Enfriar 6' + movilidad"]
    }
  }
];

export const PROGRAM_DAYS: ProgramDay[] = [
  { id: "pd-1", dow: 1, workoutId: "wod-engine", phase: "Build" },   // Lunes
  { id: "pd-2", dow: 3, workoutId: "wod-strength", phase: "Build" }, // Miércoles
  { id: "pd-3", dow: 5, workoutId: "wod-mixed", phase: "Build" }     // Viernes
];

export const DEFAULT_USER: UserProfile = {
  name: "Atleta",
  level: "Intermediate",
  goal: "HYROX",
  daysPerWeek: 5,
  typicalDurationMin: 60,
  equipmentOwned: Object.fromEntries(EQUIPMENT_CATALOG.map((e) => [e, true])),
  planId: "performance-179"
};

function clamp(n: number, a: number, b: number) {
  return Math.max(a, Math.min(b, n));
}

export const MOCK_COMPLETED: CompletedWorkout[] = (() => {
  const today = isoToday();
  const start = addDaysISO(today, -42); // 6 semanas atrás
  const out: CompletedWorkout[] = [];
  let id = 1;

  // Patrón: 3 sesiones/semana (engine, strength, mixed)
  // Vamos mejorando un poco, pero forzamos una semana con RPE alto y peor rendimiento para mostrar alerta.
  for (let w = 0; w < 6; w++) {
    const baseDate = addDaysISO(start, w * 7);

    // Engine (Tiempo: cuanto menos, mejor)
    const engineTime = clamp(34 - w * 0.8 + (w === 5 ? 4.0 : 0), 26, 60); // última semana peor
    const engineRPE = clamp(6 + w * 0.3 + (w >= 4 ? 1.5 : 0), 4, 10);

    out.push({
      id: `cw-${id++}`,
      dateISO: addDaysISO(baseDate, 1),
      workoutId: "wod-engine",
      scoreType: "Tiempo",
      scoreValue: Number(engineTime.toFixed(1)),
      loads: [
        { movement: "KB Swing", kg: 24 + (w >= 3 ? 4 : 0), reps: 20 }
      ],
      notes: w >= 4 ? "Acumulación alta. Dormí peor." : "Buen pacing.",
      rpe: Math.round(engineRPE)
    });

    // Strength (Carga: más es mejor)
    const squat = clamp(90 + w * 2.5 + (w === 5 ? -4 : 0), 60, 140);
    const strengthRPE = clamp(6 + w * 0.25 + (w >= 4 ? 1.2 : 0), 4, 10);

    out.push({
      id: `cw-${id++}`,
      dateISO: addDaysISO(baseDate, 3),
      workoutId: "wod-strength",
      scoreType: "Carga",
      scoreValue: Number(squat.toFixed(0)),
      loads: [
        { movement: "Back Squat", kg: Number(squat.toFixed(0)), reps: 5 },
        { movement: "Row", kg: 0, reps: 8 }
      ],
      notes: w >= 4 ? "RPE alto. Técnica sólida." : "Progresión estable.",
      rpe: Math.round(strengthRPE)
    });

    // Mixed (Reps: más es mejor)
    const reps = clamp(220 + w * 6 + (w === 5 ? -18 : 0), 120, 400); // última semana cae
    const mixedRPE = clamp(6 + w * 0.35 + (w >= 4 ? 1.2 : 0), 4, 10);

    out.push({
      id: `cw-${id++}`,
      dateISO: addDaysISO(baseDate, 5),
      workoutId: "wod-mixed",
      scoreType: "Reps",
      scoreValue: Number(reps.toFixed(0)),
      loads: [
        { movement: "DB Thruster", kg: 2 * (20 + (w >= 3 ? 2.5 : 0)), reps: 12 }
      ],
      notes: w >= 4 ? "Fatiga acumulada." : "Buen ritmo.",
      rpe: Math.round(mixedRPE)
    });
  }

  // Orden cronológico
  out.sort((a, b) => a.dateISO.localeCompare(b.dateISO));
  return out;
})();

// -------------------------------------------------------
// FILE: src/App.tsx
// -------------------------------------------------------
import React, { useEffect, useMemo, useState } from "react";
import clsx from "clsx";
import {
  Area,
  AreaChart,
  Bar,
  BarChart,
  CartesianGrid,
  Legend,
  ResponsiveContainer,
  Tooltip,
  XAxis,
  YAxis
} from "recharts";

import { DEFAULT_USER, EQUIPMENT_CATALOG, MOCK_COMPLETED, PROGRAM_DAYS, WORKOUTS } from "./data/mock";
import { PLANS } from "./data/plans";
import { addDaysISO, dayNameES, dowFromISO, formatShort, isoToday } from "./lib/date";
import { fatigueAlert, quickInsight } from "./lib/insights";
import { loadState, resetState, saveState } from "./lib/storage";
import { CompletedWorkout, LoadEntry, PR, ScoreType, UserProfile, Workout } from "./lib/types";

// ---------------------------
// UI primitives
// ---------------------------
function Card(props: { children: React.ReactNode; className?: string }) {
  return (
    <div className={clsx("rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel)] p-4 shadow-sm", props.className)}>
      {props.children}
    </div>
  );
}

function Button(props: {
  children: React.ReactNode;
  onClick?: () => void;
  variant?: "primary" | "secondary" | "ghost";
  className?: string;
  disabled?: boolean;
  type?: "button" | "submit";
}) {
  const v = props.variant ?? "secondary";
  const base =
    "inline-flex items-center justify-center gap-2 rounded-2xl px-4 py-3 text-sm font-semibold transition focus:outline-none focus:ring-2 focus:ring-[color:var(--accent)] disabled:opacity-60 disabled:cursor-not-allowed";
  const styles =
    v === "primary"
      ? "bg-[color:var(--accent)] text-black hover:opacity-90"
      : v === "ghost"
      ? "bg-transparent text-[color:var(--text)] hover:bg-[color:var(--panel2)]"
      : "bg-[color:var(--panel2)] text-[color:var(--text)] hover:opacity-90 border border-[color:var(--border)]";
  return (
    <button type={props.type ?? "button"} disabled={props.disabled} onClick={props.onClick} className={clsx(base, styles, props.className)}>
      {props.children}
    </button>
  );
}

function Chip(props: { children: React.ReactNode; active?: boolean; onClick?: () => void }) {
  return (
    <button
      onClick={props.onClick}
      className={clsx(
        "rounded-full border px-3 py-1 text-xs font-semibold transition",
        props.active
          ? "border-transparent bg-[color:var(--accent)] text-black"
          : "border-[color:var(--border)] bg-[color:var(--panel2)] text-[color:var(--muted)] hover:text-[color:var(--text)]"
      )}
    >
      {props.children}
    </button>
  );
}

function Modal(props: { open: boolean; title: string; children: React.ReactNode; onClose: () => void }) {
  if (!props.open) return null;
  return (
    <div className="fixed inset-0 z-50 flex items-end justify-center bg-black/60 p-4 md:items-center">
      <div className="w-full max-w-xl rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel)] shadow-xl">
        <div className="flex items-center justify-between border-b border-[color:var(--border)] p-4">
          <div className="text-base font800 font-extrabold">{props.title}</div>
          <Button variant="ghost" onClick={props.onClose} className="py-2">
            Cerrar
          </Button>
        </div>
        <div className="p-4">{props.children}</div>
      </div>
    </div>
  );
}

// ---------------------------
// Helpers: domain
// ---------------------------
function getWorkoutById(id: string): Workout | undefined {
  return WORKOUTS.find((w) => w.id === id);
}

function sortByDateISO(a: CompletedWorkout, b: CompletedWorkout) {
  return a.dateISO.localeCompare(b.dateISO);
}

function computePRs(completed: CompletedWorkout[]): PR[] {
  const best: Record<string, PR> = {};
  for (const c of completed) {
    for (const l of c.loads) {
      if (!l.movement) continue;
      const movement = l.movement.trim();
      const kg = Number(l.kg || 0);
      const prev = best[movement];
      if (!prev || kg > prev.bestKg) {
        best[movement] = { movement, bestKg: kg, dateISO: c.dateISO };
      }
    }
  }
  return Object.values(best).sort((a, b) => b.bestKg - a.bestKg).slice(0, 8);
}

function withinRange(iso: string, range: "7d" | "30d" | "90d" | "all"): boolean {
  if (range === "all") return true;
  const today = isoToday();
  const min = range === "7d" ? addDaysISO(today, -7) : range === "30d" ? addDaysISO(today, -30) : addDaysISO(today, -90);
  return iso >= min && iso <= today;
}

function scorePretty(scoreType: ScoreType, v: number): string {
  if (scoreType === "Tiempo") return `${v.toFixed(1)} min`;
  if (scoreType === "Reps") return `${Math.round(v)} reps`;
  return `${Math.round(v)} kg`;
}

// ---------------------------
// App
// ---------------------------
type TabKey = "Hoy" | "Programa" | "Registrar" | "Progreso" | "Perfil";

type AppState = {
  user: UserProfile;
  completed: CompletedWorkout[];
};

export function App() {
  const initial = loadState<AppState>({ user: DEFAULT_USER, completed: MOCK_COMPLETED });

  const [tab, setTab] = useState<TabKey>("Hoy");
  const [user, setUser] = useState<UserProfile>(initial.user);
  const [completed, setCompleted] = useState<CompletedWorkout[]>([...initial.completed].sort(sortByDateISO));

  // Persist
  useEffect(() => {
    saveState({ user, completed });
  }, [user, completed]);

  const todaysWorkout = useMemo(() => {
    const dow = new Date().getDay();
    const pd = PROGRAM_DAYS.find((p) => p.dow === dow);
    return getWorkoutById(pd?.workoutId ?? PROGRAM_DAYS[0].workoutId);
  }, []);

  const fatigue = useMemo(() => fatigueAlert(completed), [completed]);
  const prs = useMemo(() => computePRs(completed), [completed]);

  return (
    <div className="min-h-screen">
      <Header onLogoClick={() => setTab("Hoy")} />

      <div className="mx-auto grid max-w-6xl grid-cols-1 gap-4 p-4 md:grid-cols-[240px_1fr] md:gap-6">
        <aside className="hidden md:block">
          <Sidebar tab={tab} setTab={setTab} />
          <div className="mt-4 space-y-3">
            <Card>
              <div className="text-xs font-semibold text-[color:var(--muted)]">Plan actual</div>
              <div className="mt-1 text-sm font-extrabold">{planName(user.planId)}</div>
              <div className="mt-2 text-xs text-[color:var(--muted)]">Objetivo</div>
              <div className="text-sm font-semibold">{user.goal}</div>
            </Card>

            {fatigue.show && (
              <AlertCard title="Alerta" tone="warn">
                {fatigue.message}
              </AlertCard>
            )}
          </div>
        </aside>

        <main className="space-y-4">
          {tab === "Hoy" && (
            <TodayPage
              user={user}
              setUser={setUser}
              workout={todaysWorkout}
              completed={completed}
              setCompleted={setCompleted}
              fatigue={fatigue}
            />
          )}
          {tab === "Programa" && <ProgramPage />}
          {tab === "Registrar" && <LogPage workouts={WORKOUTS} completed={completed} setCompleted={setCompleted} />}
          {tab === "Progreso" && <ProgressPage workouts={WORKOUTS} completed={completed} prs={prs} />}
          {tab === "Perfil" && <ProfilePage user={user} setUser={setUser} prs={prs} />}
        </main>
      </div>

      <BottomTabs tab={tab} setTab={setTab} />

      <footer className="mx-auto max-w-6xl px-4 pb-8 pt-3 text-center text-xs text-[color:var(--muted)]">
        HI-WOD • Entrena. Registra. Mejora.
      </footer>
    </div>
  );
}

// ---------------------------
// Header / Nav
// ---------------------------
function Header(props: { onLogoClick: () => void }) {
  return (
    <div className="sticky top-0 z-40 border-b border-[color:var(--border)] bg-black/30 backdrop-blur">
      <div className="mx-auto flex max-w-6xl items-center justify-between p-3 md:p-4">
        <button onClick={props.onLogoClick} className="flex items-center gap-3">
          {/* Logo grande y visible */}
          <div className="rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel)] px-3 py-2">
            <img
              src="/logo.svg"
              alt="HI-WOD"
              className="h-[56px] w-auto md:h-[72px]"
              draggable={false}
            />
          </div>
        </button>

        <div className="hidden items-center gap-2 md:flex">
          <span className="rounded-full border border-[color:var(--border)] bg-[color:var(--panel)] px-3 py-2 text-xs font-semibold text-[color:var(--muted)]">
            Dark UI • Accent #40E0D0
          </span>
        </div>
      </div>
    </div>
  );
}

function Sidebar(props: { tab: TabKey; setTab: (t: TabKey) => void }) {
  const items: TabKey[] = ["Hoy", "Programa", "Registrar", "Progreso", "Perfil"];
  return (
    <Card>
      <div className="text-xs font-semibold text-[color:var(--muted)]">Navegación</div>
      <div className="mt-3 flex flex-col gap-2">
        {items.map((k) => (
          <button
            key={k}
            onClick={() => props.setTab(k)}
            className={clsx(
              "rounded-2xl border px-3 py-3 text-left text-sm font-semibold transition",
              props.tab === k
                ? "border-transparent bg-[color:var(--accent)] text-black"
                : "border-[color:var(--border)] bg-[color:var(--panel2)] text-[color:var(--text)] hover:opacity-90"
            )}
          >
            {k}
          </button>
        ))}
      </div>
    </Card>
  );
}

function BottomTabs(props: { tab: TabKey; setTab: (t: TabKey) => void }) {
  const items: TabKey[] = ["Hoy", "Programa", "Registrar", "Progreso", "Perfil"];
  return (
    <div className="fixed bottom-0 left-0 right-0 z-40 border-t border-[color:var(--border)] bg-black/40 backdrop-blur md:hidden">
      <div className="mx-auto grid max-w-6xl grid-cols-5 gap-1 p-2">
        {items.map((k) => (
          <button
            key={k}
            onClick={() => props.setTab(k)}
            className={clsx(
              "rounded-2xl px-2 py-3 text-xs font-extrabold transition",
              props.tab === k ? "bg-[color:var(--accent)] text-black" : "bg-[color:var(--panel)] text-[color:var(--muted)]"
            )}
          >
            {k}
          </button>
        ))}
      </div>
    </div>
  );
}

function AlertCard(props: { title: string; tone: "warn" | "danger"; children: React.ReactNode }) {
  const color = props.tone === "warn" ? "var(--warn)" : "var(--danger)";
  return (
    <div className="rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel)] p-4">
      <div className="flex items-center justify-between">
        <div className="text-sm font-extrabold">{props.title}</div>
        <span className="h-2 w-2 rounded-full" style={{ background: color }} />
      </div>
      <div className="mt-2 text-sm text-[color:var(--muted)]">{props.children}</div>
    </div>
  );
}

function planName(planId: string): string {
  const p = PLANS.find((x) => x.id === planId);
  if (!p) return "—";
  return `${p.name} · ${p.priceEUR}€${p.note ? ` · ${p.note}` : ""}`;
}

// ---------------------------
// Pages
// ---------------------------
function TodayPage(props: {
  user: UserProfile;
  setUser: (u: UserProfile) => void;
  workout: Workout | undefined;
  completed: CompletedWorkout[];
  setCompleted: (c: CompletedWorkout[]) => void;
  fatigue: { show: boolean; message: string };
}) {
  const w = props.workout;
  const [level, setLevel] = useState(props.user.level);
  const [startOpen, setStartOpen] = useState(false);
  const [logOpen, setLogOpen] = useState(false);
  const [detailOpen, setDetailOpen] = useState(false);

  useEffect(() => {
    if (level !== props.user.level) props.setUser({ ...props.user, level });
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [level]);

  const weekCount = useMemo(() => {
    const today = isoToday();
    const monday = (() => {
      const d = new Date(today + "T00:00:00");
      const dow = d.getDay();
      const diff = (dow + 6) % 7; // lunes=0
      d.setDate(d.getDate() - diff);
      return d.toISOString().slice(0, 10);
    })();
    return props.completed.filter((c) => c.dateISO >= monday && c.dateISO <= today).length;
  }, [props.completed]);

  const last = props.completed[props.completed.length - 1];

  return (
    <div className="space-y-4 pb-20 md:pb-4">
      <div className="flex flex-col gap-3 md:flex-row md:items-center md:justify-between">
        <div>
          <div className="text-xs font-semibold text-[color:var(--muted)]">HOY</div>
          <div className="text-2xl font-extrabold">Dashboard</div>
        </div>

        <div className="flex flex-wrap items-center gap-2">
          <Chip active={level === "Beginner"} onClick={() => setLevel("Beginner")}>Beginner</Chip>
          <Chip active={level === "Intermediate"} onClick={() => setLevel("Intermediate")}>Intermediate</Chip>
          <Chip active={level === "Advanced"} onClick={() => setLevel("Advanced")}>Advanced</Chip>
        </div>
      </div>

      {/* A) Card principal */}
      <Card className="p-5">
        <div className="flex flex-col gap-4 md:flex-row md:items-start md:justify-between">
          <div className="space-y-2">
            <div className="text-sm font-semibold text-[color:var(--muted)]">Entreno de hoy</div>
            <div className="text-xl font-extrabold">{w ? w.name : "Sesión del día"}</div>
            <div className="text-sm text-[color:var(--muted)]">{w ? w.objective : "—"}</div>
            <div className="flex flex-wrap items-center gap-2 pt-2">
              <span className="rounded-full border border-[color:var(--border)] bg-[color:var(--panel2)] px-3 py-1 text-xs font-semibold text-[color:var(--muted)]">
                Duración estimada: {w ? w.durationMin : 60} min
              </span>
              {(w?.tags ?? []).map((t) => (
                <span key={t} className="rounded-full border border-[color:var(--border)] bg-[color:var(--panel2)] px-3 py-1 text-xs font-semibold text-[color:var(--muted)]">
                  {t}
                </span>
              ))}
            </div>

            <div className="pt-2">
              <div className="text-xs font-semibold text-[color:var(--muted)]">Material</div>
              <div className="mt-2 flex flex-wrap gap-2">
                {(w?.equipment ?? []).map((e) => (
                  <span
                    key={e}
                    className={clsx(
                      "rounded-full border px-3 py-1 text-xs font-semibold",
                      props.user.equipmentOwned[e]
                        ? "border-transparent bg-[color:var(--accent)] text-black"
                        : "border-[color:var(--border)] bg-[color:var(--panel2)] text-[color:var(--muted)]"
                    )}
                  >
                    {e}
                  </span>
                ))}
              </div>
            </div>
          </div>

          <div className="flex w-full flex-col gap-2 md:w-[320px]">
            <Button variant="primary" className="w-full py-4 text-base" onClick={() => setStartOpen(true)}>
              EMPEZAR
            </Button>
            <div className="grid grid-cols-2 gap-2">
              <Button variant="secondary" className="w-full" onClick={() => setDetailOpen(true)}>
                VER DETALLE
              </Button>
              <Button variant="secondary" className="w-full" onClick={() => setLogOpen(true)}>
                REGISTRAR
              </Button>
            </div>
            <div className="rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] p-3 text-xs text-[color:var(--muted)]">
              <div className="font-semibold text-[color:var(--text)]">Regla de 5 segundos</div>
              <div>Entrena ahora. Registra al terminar. Progreso medible.</div>
            </div>
          </div>
        </div>
      </Card>

      {/* B) Progreso semanal */}
      <div className="grid grid-cols-1 gap-4 md:grid-cols-2">
        <Card>
          <div className="text-xs font-semibold text-[color:var(--muted)]">Progreso semanal</div>
          <div className="mt-2 flex items-end justify-between">
            <div>
              <div className="text-3xl font-extrabold">{weekCount}</div>
              <div className="text-sm text-[color:var(--muted)]">sesiones registradas esta semana</div>
            </div>
            <div className="rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] px-3 py-2 text-xs font-semibold text-[color:var(--muted)]">
              Objetivo: {props.user.daysPerWeek}/sem
            </div>
          </div>
          <div className="mt-3 h-2 w-full rounded-full bg-[color:var(--border)]">
            <div
              className="h-2 rounded-full bg-[color:var(--accent)]"
              style={{ width: `${Math.min(100, (weekCount / Math.max(1, props.user.daysPerWeek)) * 100)}%` }}
            />
          </div>
          <div className="mt-3 grid grid-cols-2 gap-3">
            <div className="rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] p-3">
              <div className="text-xs text-[color:var(--muted)]">Último RPE</div>
              <div className="text-xl font-extrabold">{last?.rpe ?? "—"}</div>
            </div>
            <div className="rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] p-3">
              <div className="text-xs text-[color:var(--muted)]">Último score</div>
              <div className="text-sm font-extrabold">{last ? scorePretty(last.scoreType, last.scoreValue) : "—"}</div>
            </div>
          </div>
        </Card>

        {/* C + D: Insight + Alert */}
        <Card>
          <div className="text-xs font-semibold text-[color:var(--muted)]">Insight rápido</div>
          <div className="mt-2 text-sm text-[color:var(--muted)]">
            {last ? quickInsight(last.rpe, last.scoreType, last.scoreValue) : "Registra tu primera sesión para ver insights."}
          </div>

          {props.fatigue.show && (
            <div className="mt-4 rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] p-3">
              <div className="text-xs font-extrabold" style={{ color: "var(--warn)" }}>
                ALERTA: fatiga
              </div>
              <div className="mt-1 text-sm text-[color:var(--muted)]">{props.fatigue.message}</div>
            </div>
          )}

          <div className="mt-4 grid grid-cols-2 gap-2">
            <Button variant="secondary" onClick={() => setLogOpen(true)}>Registrar sesión</Button>
            <Button variant="secondary" onClick={() => setDetailOpen(true)}>Ver detalle</Button>
          </div>
        </Card>
      </div>

      {/* Modals */}
      <Modal open={detailOpen} title="Detalle del entrenamiento" onClose={() => setDetailOpen(false)}>
        {w ? (
          <div className="space-y-3">
            <Section title="Warm-up" items={w.details.warmup} />
            <Section title="Main" items={w.details.main} />
            {w.details.accessory?.length ? <Section title="Accessory" items={w.details.accessory} /> : null}
            {w.details.cooldown?.length ? <Section title="Cooldown" items={w.details.cooldown} /> : null}
          </div>
        ) : (
          <div className="text-sm text-[color:var(--muted)]">Sin sesión asignada.</div>
        )}
      </Modal>

      <Modal open={startOpen} title="Sesión en curso" onClose={() => setStartOpen(false)}>
        <div className="space-y-3">
          <div className="rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] p-3 text-sm text-[color:var(--muted)]">
            Modo demo: aquí conectarías timer/intervalos. Al terminar, registra tu sesión.
          </div>
          <Button variant="primary" className="w-full" onClick={() => { setStartOpen(false); setLogOpen(true); }}>
            TERMINAR Y REGISTRAR
          </Button>
        </div>
      </Modal>

      <LogModal
        open={logOpen}
        onClose={() => setLogOpen(false)}
        workouts={WORKOUTS}
        defaultWorkoutId={w?.id}
        onSaved={(cw) => {
          props.setCompleted([...props.completed, cw].sort(sortByDateISO));
          setLogOpen(false);
        }}
      />
    </div>
  );
}

function Section(props: { title: string; items: string[] }) {
  return (
    <div>
      <div className="text-xs font-extrabold text-[color:var(--muted)]">{props.title}</div>
      <ul className="mt-2 list-disc space-y-1 pl-5 text-sm text-[color:var(--text)]">
        {props.items.map((x, i) => (
          <li key={i} className="text-[color:var(--muted)]">
            <span className="text-[color:var(--text)]">{x}</span>
          </li>
        ))}
      </ul>
    </div>
  );
}

function ProgramPage() {
  const today = isoToday();
  const dow = dowFromISO(today);

  const week = [0,1,2,3,4,5,6].map((d) => {
    const pd = PROGRAM_DAYS.find((p) => p.dow === d);
    const w = getWorkoutById(pd?.workoutId ?? "");
    return { dow: d, pd, w };
  });

  return (
    <div className="space-y-4 pb-20 md:pb-4">
      <div>
        <div className="text-xs font-semibold text-[color:var(--muted)]">PROGRAMA</div>
        <div className="text-2xl font-extrabold">Microciclo semanal</div>
      </div>

      <Card>
        <div className="text-xs font-semibold text-[color:var(--muted)]">Semana actual</div>
        <div className="mt-3 grid grid-cols-1 gap-3 md:grid-cols-2">
          {week.map((d) => (
            <div
              key={d.dow}
              className={clsx(
                "rounded-2xl border p-4",
                d.dow === dow ? "border-transparent bg-[color:var(--accent)] text-black" : "border-[color:var(--border)] bg-[color:var(--panel2)]"
              )}
            >
              <div className={clsx("text-xs font-extrabold", d.dow === dow ? "text-black/80" : "text-[color:var(--muted)]")}>
                {dayNameES(d.dow)} • {d.pd?.phase ?? "—"}
              </div>
              <div className={clsx("mt-1 text-sm font-extrabold", d.dow === dow ? "text-black" : "text-[color:var(--text)]")}>
                {d.w?.name ?? "Descanso / movilidad"}
              </div>
              <div className={clsx("mt-1 text-xs", d.dow === dow ? "text-black/80" : "text-[color:var(--muted)]")}>
                {d.w?.objective ?? "Recuperación activa, caminar, movilidad, respiración."}
              </div>
            </div>
          ))}
        </div>
      </Card>
    </div>
  );
}

function LogPage(props: { workouts: Workout[]; completed: CompletedWorkout[]; setCompleted: (c: CompletedWorkout[]) => void }) {
  const [open, setOpen] = useState(false);
  const lastWorkoutId = props.completed[props.completed.length - 1]?.workoutId;

  return (
    <div className="space-y-4 pb-20 md:pb-4">
      <div className="flex items-end justify-between gap-3">
        <div>
          <div className="text-xs font-semibold text-[color:var(--muted)]">REGISTRAR</div>
          <div className="text-2xl font-extrabold">Log rápido</div>
        </div>
        <Button variant="primary" onClick={() => setOpen(true)}>Nuevo registro</Button>
      </div>

      <Card>
        <div className="text-xs font-semibold text-[color:var(--muted)]">Historial (últimas 10)</div>
        <div className="mt-3 space-y-2">
          {props.completed.slice(-10).reverse().map((c) => {
            const w = props.workouts.find((x) => x.id === c.workoutId);
            return (
              <div key={c.id} className="flex items-center justify-between rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] p-3">
                <div>
                  <div className="text-sm font-extrabold">{w?.name ?? "Sesión"}</div>
                  <div className="text-xs text-[color:var(--muted)]">{formatShort(c.dateISO)} • RPE {c.rpe}</div>
                </div>
                <div className="text-sm font-extrabold">{scorePretty(c.scoreType, c.scoreValue)}</div>
              </div>
            );
          })}
        </div>
      </Card>

      <LogModal
        open={open}
        onClose={() => setOpen(false)}
        workouts={props.workouts}
        defaultWorkoutId={lastWorkoutId}
        onSaved={(cw) => {
          props.setCompleted([...props.completed, cw].sort(sortByDateISO));
          setOpen(false);
        }}
      />
    </div>
  );
}

function ProgressPage(props: { workouts: Workout[]; completed: CompletedWorkout[]; prs: PR[] }) {
  const [range, setRange] = useState<"7d" | "30d" | "90d" | "all">("30d");
  const [type, setType] = useState<"all" | "HYROX" | "CrossFit" | "Fuerza" | "Carrera">("all");

  const filtered = useMemo(() => {
    return props.completed.filter((c) => {
      if (!withinRange(c.dateISO, range)) return false;
      if (type === "all") return true;
      const w = props.workouts.find((x) => x.id === c.workoutId);
      return (w?.tags ?? []).includes(type as any);
    });
  }, [props.completed, props.workouts, range, type]);

  const rpeSeries = useMemo(() => {
    return filtered.map((c) => ({ date: formatShort(c.dateISO), rpe: c.rpe }));
  }, [filtered]);

  const strengthSeries = useMemo(() => {
    // Agregamos por fecha el "mejor kg" de Back Squat si existe
    const rows: { date: string; squatKg?: number; thrusterKg?: number }[] = [];
    for (const c of filtered) {
      const date = formatShort(c.dateISO);
      const squat = c.loads.find((l) => l.movement.toLowerCase().includes("squat"))?.kg;
      const thr = c.loads.find((l) => l.movement.toLowerCase().includes("thruster"))?.kg;
      rows.push({ date, squatKg: squat, thrusterKg: thr });
    }
    return rows;
  }, [filtered]);

  const fatigue = useMemo(() => fatigueAlert(props.completed), [props.completed]);

  return (
    <div className="space-y-4 pb-20 md:pb-4">
      <div className="flex flex-col gap-3 md:flex-row md:items-end md:justify-between">
        <div>
          <div className="text-xs font-semibold text-[color:var(--muted)]">PROGRESO</div>
          <div className="text-2xl font-extrabold">Analíticas</div>
        </div>

        <div className="flex flex-wrap items-center gap-2">
          <Chip active={range === "7d"} onClick={() => setRange("7d")}>7d</Chip>
          <Chip active={range === "30d"} onClick={() => setRange("30d")}>30d</Chip>
          <Chip active={range === "90d"} onClick={() => setRange("90d")}>90d</Chip>
          <Chip active={range === "all"} onClick={() => setRange("all")}>Todo</Chip>

          <span className="mx-2 h-5 w-px bg-[color:var(--border)]" />

          <Chip active={type === "all"} onClick={() => setType("all")}>All</Chip>
          <Chip active={type === "HYROX"} onClick={() => setType("HYROX")}>HYROX</Chip>
          <Chip active={type === "CrossFit"} onClick={() => setType("CrossFit")}>CrossFit</Chip>
          <Chip active={type === "Fuerza"} onClick={() => setType("Fuerza")}>Fuerza</Chip>
          <Chip active={type === "Carrera"} onClick={() => setType("Carrera")}>Carrera</Chip>
        </div>
      </div>

      {fatigue.show && (
        <AlertCard title="Alerta de fatiga" tone="warn">
          {fatigue.message}
        </AlertCard>
      )}

      <div className="grid grid-cols-1 gap-4 md:grid-cols-2">
        <Card>
          <div className="text-xs font-semibold text-[color:var(--muted)]">RPE Trend</div>
          <div className="mt-3 h-[260px]">
            <ResponsiveContainer width="100%" height="100%">
              <AreaChart data={rpeSeries}>
                <defs>
                  <linearGradient id="rpeFill" x1="0" y1="0" x2="0" y2="1">
                    <stop offset="5%" stopColor="var(--accent)" stopOpacity={0.35} />
                    <stop offset="95%" stopColor="var(--accent)" stopOpacity={0.02} />
                  </linearGradient>
                </defs>
                <CartesianGrid stroke="rgba(255,255,255,0.06)" />
                <XAxis dataKey="date" tick={{ fill: "rgba(231,238,247,0.65)", fontSize: 12 }} />
                <YAxis domain={[1, 10]} tick={{ fill: "rgba(231,238,247,0.65)", fontSize: 12 }} />
                <Tooltip contentStyle={{ background: "#0C1118", border: "1px solid #1C2633", borderRadius: 14 }} />
                <Area type="monotone" dataKey="rpe" stroke="var(--accent)" fill="url(#rpeFill)" strokeWidth={2} />
              </AreaChart>
            </ResponsiveContainer>
          </div>
          <div className="mt-2 text-xs text-[color:var(--muted)]">RPE como sensor principal de carga interna.</div>
        </Card>

        <Card>
          <div className="text-xs font-semibold text-[color:var(--muted)]">Strength Progress (muestras)</div>
          <div className="mt-3 h-[260px]">
            <ResponsiveContainer width="100%" height="100%">
              <BarChart data={strengthSeries}>
                <CartesianGrid stroke="rgba(255,255,255,0.06)" />
                <XAxis dataKey="date" tick={{ fill: "rgba(231,238,247,0.65)", fontSize: 12 }} />
                <YAxis tick={{ fill: "rgba(231,238,247,0.65)", fontSize: 12 }} />
                <Tooltip contentStyle={{ background: "#0C1118", border: "1px solid #1C2633", borderRadius: 14 }} />
                <Legend />
                <Bar dataKey="squatKg" name="Back Squat (kg)" fill="var(--accent)" />
                <Bar dataKey="thrusterKg" name="DB Thruster (kg totales)" fill="rgba(64,224,208,0.55)" />
              </BarChart>
            </ResponsiveContainer>
          </div>
          <div className="mt-2 text-xs text-[color:var(--muted)]">En producción: top 3 lifts y agregación por semana.</div>
        </Card>
      </div>

      <Card>
        <div className="flex flex-col gap-3 md:flex-row md:items-center md:justify-between">
          <div>
            <div className="text-xs font-semibold text-[color:var(--muted)]">PRs recientes</div>
            <div className="text-sm text-[color:var(--muted)]">Máximos detectados a partir de las cargas registradas.</div>
          </div>
          <div className="text-xs text-[color:var(--muted)]">Filtro: {range} • {type}</div>
        </div>

        <div className="mt-3 grid grid-cols-1 gap-2 md:grid-cols-2">
          {props.prs.map((p) => (
            <div key={p.movement} className="flex items-center justify-between rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] p-3">
              <div>
                <div className="text-sm font-extrabold">{p.movement}</div>
                <div className="text-xs text-[color:var(--muted)]">{formatShort(p.dateISO)}</div>
              </div>
              <div className="text-sm font-extrabold">{p.bestKg} kg</div>
            </div>
          ))}
          {!props.prs.length && (
            <div className="rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] p-3 text-sm text-[color:var(--muted)]">
              Registra cargas para calcular PRs automáticamente.
            </div>
          )}
        </div>
      </Card>
    </div>
  );
}

function ProfilePage(props: { user: UserProfile; setUser: (u: UserProfile) => void; prs: PR[] }) {
  const [name, setName] = useState(props.user.name);
  const [goal, setGoal] = useState(props.user.goal);
  const [days, setDays] = useState(props.user.daysPerWeek);
  const [dur, setDur] = useState(props.user.typicalDurationMin);
  const [planId, setPlanId] = useState(props.user.planId);
  const [savedToast, setSavedToast] = useState(false);

  useEffect(() => {
    props.setUser({ ...props.user, name, goal, daysPerWeek: days, typicalDurationMin: dur, planId });
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [name, goal, days, dur, planId]);

  const toggleEq = (k: string) => {
    props.setUser({ ...props.user, equipmentOwned: { ...props.user.equipmentOwned, [k]: !props.user.equipmentOwned[k] } });
    setSavedToast(true);
    setTimeout(() => setSavedToast(false), 900);
  };

  const doReset = () => {
    resetState();
    window.location.reload();
  };

  return (
    <div className="space-y-4 pb-20 md:pb-4">
      <div className="flex flex-col gap-2 md:flex-row md:items-end md:justify-between">
        <div>
          <div className="text-xs font-semibold text-[color:var(--muted)]">PERFIL</div>
          <div className="text-2xl font-extrabold">Ajustes + Planes</div>
        </div>

        <div className="flex items-center gap-2">
          <Button variant="secondary" onClick={doReset}>Reset demo</Button>
        </div>
      </div>

      <Card>
        <div className="grid grid-cols-1 gap-4 md:grid-cols-2">
          <div className="space-y-3">
            <label className="block">
              <div className="text-xs font-semibold text-[color:var(--muted)]">Nombre</div>
              <input
                value={name}
                onChange={(e) => setName(e.target.value)}
                className="mt-1 w-full rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] px-3 py-3 text-sm outline-none focus:ring-2 focus:ring-[color:var(--accent)]"
              />
            </label>

            <label className="block">
              <div className="text-xs font-semibold text-[color:var(--muted)]">Objetivo</div>
              <select
                value={goal}
                onChange={(e) => setGoal(e.target.value as any)}
                className="mt-1 w-full rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] px-3 py-3 text-sm outline-none focus:ring-2 focus:ring-[color:var(--accent)]"
              >
                <option>HYROX</option>
                <option>Hybrid</option>
                <option>CrossFit Performance</option>
                <option>Salud</option>
              </select>
            </label>

            <div className="grid grid-cols-2 gap-3">
              <label className="block">
                <div className="text-xs font-semibold text-[color:var(--muted)]">Días/sem</div>
                <input
                  type="number"
                  min={1}
                  max={7}
                  value={days}
                  onChange={(e) => setDays(Number(e.target.value))}
                  className="mt-1 w-full rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] px-3 py-3 text-sm outline-none focus:ring-2 focus:ring-[color:var(--accent)]"
                />
              </label>
              <label className="block">
                <div className="text-xs font-semibold text-[color:var(--muted)]">Duración (min)</div>
                <input
                  type="number"
                  min={20}
                  max={120}
                  value={dur}
                  onChange={(e) => setDur(Number(e.target.value))}
                  className="mt-1 w-full rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] px-3 py-3 text-sm outline-none focus:ring-2 focus:ring-[color:var(--accent)]"
                />
              </label>
            </div>

            {savedToast && (
              <div className="rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] p-3 text-xs text-[color:var(--muted)]">
                Guardado.
              </div>
            )}
          </div>

          <div className="space-y-3">
            <div className="text-xs font-semibold text-[color:var(--muted)]">Equipo disponible</div>
            <div className="flex flex-wrap gap-2">
              {EQUIPMENT_CATALOG.map((e) => (
                <Chip key={e} active={props.user.equipmentOwned[e]} onClick={() => toggleEq(e)}>
                  {e}
                </Chip>
              ))}
            </div>

            <div className="pt-2">
              <div className="text-xs font-semibold text-[color:var(--muted)]">Plan seleccionado</div>
              <div className="mt-2 grid grid-cols-1 gap-2">
                {PLANS.map((p) => {
                  const active = planId === p.id;
                  return (
                    <button
                      key={p.id}
                      onClick={() => { setPlanId(p.id); setSavedToast(true); setTimeout(() => setSavedToast(false), 900); }}
                      className={clsx(
                        "rounded-2xl border p-4 text-left transition",
                        active
                          ? "border-transparent bg-[color:var(--accent)] text-black"
                          : "border-[color:var(--border)] bg-[color:var(--panel2)] text-[color:var(--text)] hover:opacity-90",
                        p.highlight ? "ring-1 ring-[color:var(--accent)]/40" : ""
                      )}
                    >
                      <div className={clsx("flex items-start justify-between gap-3", active ? "text-black" : "text-[color:var(--text)]")}>
                        <div>
                          <div className="text-sm font-extrabold">
                            {p.name} {p.note ? <span className={clsx("text-xs font-bold", active ? "text-black/70" : "text-[color:var(--muted)]")}>• {p.note}</span> : null}
                          </div>
                          <div className={clsx("mt-1 text-xs", active ? "text-black/75" : "text-[color:var(--muted)]")}>
                            {p.bullets.join(" · ")}
                          </div>
                        </div>
                        <div className="text-lg font-extrabold">{p.priceEUR}€</div>
                      </div>
                    </button>
                  );
                })}
              </div>

              <div className="mt-3 rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] p-3 text-xs text-[color:var(--muted)]">
                Nota: “Competition” incluye revisiones <b>Q2W</b> (cada 2 semanas). En el MVP esto es UI/placeholder.
              </div>
            </div>
          </div>
        </div>
      </Card>

      <Card>
        <div className="text-xs font-semibold text-[color:var(--muted)]">PRs (resumen)</div>
        <div className="mt-3 grid grid-cols-1 gap-2 md:grid-cols-2">
          {props.prs.map((p) => (
            <div key={p.movement} className="flex items-center justify-between rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] p-3">
              <div>
                <div className="text-sm font-extrabold">{p.movement}</div>
                <div className="text-xs text-[color:var(--muted)]">{formatShort(p.dateISO)}</div>
              </div>
              <div className="text-sm font-extrabold">{p.bestKg} kg</div>
            </div>
          ))}
          {!props.prs.length && (
            <div className="rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] p-3 text-sm text-[color:var(--muted)]">
              Sin PRs detectados todavía.
            </div>
          )}
        </div>
      </Card>
    </div>
  );
}

// ---------------------------
// Log Modal (reusable)
// ---------------------------
function LogModal(props: {
  open: boolean;
  onClose: () => void;
  workouts: Workout[];
  defaultWorkoutId?: string;
  onSaved: (cw: CompletedWorkout) => void;
}) {
  const today = isoToday();

  const [dateISO, setDateISO] = useState(today);
  const [workoutId, setWorkoutId] = useState(props.defaultWorkoutId ?? props.workouts[0]?.id ?? "");
  const [rpe, setRpe] = useState(7);
  const [scoreType, setScoreType] = useState<ScoreType>("Tiempo");
  const [scoreValue, setScoreValue] = useState<number>(30);
  const [notes, setNotes] = useState("");
  const [loads, setLoads] = useState<LoadEntry[]>([{ movement: "Back Squat", kg: 100, reps: 5 }]);
  const [savedInsight, setSavedInsight] = useState<string | null>(null);

  useEffect(() => {
    if (props.open) {
      setDateISO(today);
      setWorkoutId(props.defaultWorkoutId ?? props.workouts[0]?.id ?? "");
      setRpe(7);
      setScoreType("Tiempo");
      setScoreValue(30);
      setNotes("");
      setLoads([{ movement: "Back Squat", kg: 100, reps: 5 }]);
      setSavedInsight(null);
    }
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [props.open]);

  const w = props.workouts.find((x) => x.id === workoutId);

  const addLoad = () => setLoads([...loads, { movement: "Movimiento", kg: 0, reps: 0 }]);
  const removeLoad = (i: number) => setLoads(loads.filter((_, idx) => idx !== i));

  const updateLoad = (i: number, patch: Partial<LoadEntry>) => {
    setLoads(loads.map((l, idx) => (idx === i ? { ...l, ...patch } : l)));
  };

  const canSave = workoutId && rpe >= 1 && rpe <= 10 && Number.isFinite(scoreValue);

  const save = () => {
    if (!canSave) return;
    const cw: CompletedWorkout = {
      id: `cw-${Math.random().toString(16).slice(2)}-${Date.now()}`,
      dateISO,
      workoutId,
      rpe: Math.round(rpe),
      scoreType,
      scoreValue: Number(scoreValue),
      loads: loads.map((l) => ({
        movement: String(l.movement || "").trim(),
        kg: Number(l.kg || 0),
        reps: Number(l.reps || 0)
      })),
      notes: notes.trim() ? notes.trim() : undefined
    };

    // Insight post-save (modal dentro del modal)
    const insight = quickInsight(cw.rpe, cw.scoreType, cw.scoreValue);
    setSavedInsight(insight);

    // Guardar tras un pequeño tick para que el usuario vea el insight
    window.setTimeout(() => props.onSaved(cw), 250);
  };

  return (
    <Modal open={props.open} title="Registrar sesión" onClose={props.onClose}>
      <div className="space-y-4">
        <div className="rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] p-3 text-xs text-[color:var(--muted)]">
          Registro rápido: <b>RPE (obligatorio)</b> + score/carga/tiempo + notas opcionales.
        </div>

        <div className="grid grid-cols-1 gap-3 md:grid-cols-2">
          <label className="block">
            <div className="text-xs font-semibold text-[color:var(--muted)]">Fecha</div>
            <input
              type="date"
              value={dateISO}
              onChange={(e) => setDateISO(e.target.value)}
              className="mt-1 w-full rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] px-3 py-3 text-sm outline-none focus:ring-2 focus:ring-[color:var(--accent)]"
            />
          </label>

          <label className="block">
            <div className="text-xs font-semibold text-[color:var(--muted)]">Sesión</div>
            <select
              value={workoutId}
              onChange={(e) => setWorkoutId(e.target.value)}
              className="mt-1 w-full rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] px-3 py-3 text-sm outline-none focus:ring-2 focus:ring-[color:var(--accent)]"
            >
              {props.workouts.map((x) => (
                <option key={x.id} value={x.id}>
                  {x.name}
                </option>
              ))}
            </select>
            <div className="mt-1 text-xs text-[color:var(--muted)]">{w?.objective ?? "—"}</div>
          </label>
        </div>

        <div className="grid grid-cols-1 gap-3 md:grid-cols-3">
          <label className="block md:col-span-1">
            <div className="text-xs font-semibold text-[color:var(--muted)]">RPE (1–10)</div>
            <input
              type="range"
              min={1}
              max={10}
              value={rpe}
              onChange={(e) => setRpe(Number(e.target.value))}
              className="mt-2 w-full"
            />
            <div className="mt-1 text-sm font-extrabold">{Math.round(rpe)}</div>
          </label>

          <label className="block md:col-span-1">
            <div className="text-xs font-semibold text-[color:var(--muted)]">Tipo de score</div>
            <select
              value={scoreType}
              onChange={(e) => setScoreType(e.target.value as ScoreType)}
              className="mt-1 w-full rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] px-3 py-3 text-sm outline-none focus:ring-2 focus:ring-[color:var(--accent)]"
            >
              <option value="Tiempo">Tiempo</option>
              <option value="Reps">Reps</option>
              <option value="Carga">Carga</option>
            </select>
          </label>

          <label className="block md:col-span-1">
            <div className="text-xs font-semibold text-[color:var(--muted)]">
              Valor ({scoreType === "Tiempo" ? "min" : scoreType === "Reps" ? "reps" : "kg"})
            </div>
            <input
              type="number"
              step={scoreType === "Tiempo" ? 0.1 : 1}
              value={scoreValue}
              onChange={(e) => setScoreValue(Number(e.target.value))}
              className="mt-1 w-full rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] px-3 py-3 text-sm outline-none focus:ring-2 focus:ring-[color:var(--accent)]"
            />
          </label>
        </div>

        <Card className="bg-[color:var(--panel2)]">
          <div className="flex items-center justify-between">
            <div>
              <div className="text-xs font-semibold text-[color:var(--muted)]">Cargas clave (opcional)</div>
              <div className="text-xs text-[color:var(--muted)]">Sirve para PRs y progresión de fuerza.</div>
            </div>
            <Button variant="secondary" onClick={addLoad}>+ Añadir</Button>
          </div>

          <div className="mt-3 space-y-2">
            {loads.map((l, i) => (
              <div key={i} className="grid grid-cols-1 gap-2 rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel)] p-3 md:grid-cols-[1fr_120px_120px_80px] md:items-center">
                <input
                  value={l.movement}
                  onChange={(e) => updateLoad(i, { movement: e.target.value })}
                  className="rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] px-3 py-2 text-sm outline-none focus:ring-2 focus:ring-[color:var(--accent)]"
                  placeholder="Movimiento"
                />
                <input
                  type="number"
                  value={l.kg}
                  onChange={(e) => updateLoad(i, { kg: Number(e.target.value) })}
                  className="rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] px-3 py-2 text-sm outline-none focus:ring-2 focus:ring-[color:var(--accent)]"
                  placeholder="kg"
                />
                <input
                  type="number"
                  value={l.reps}
                  onChange={(e) => updateLoad(i, { reps: Number(e.target.value) })}
                  className="rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] px-3 py-2 text-sm outline-none focus:ring-2 focus:ring-[color:var(--accent)]"
                  placeholder="reps"
                />
                <Button variant="ghost" onClick={() => removeLoad(i)} className="justify-center">
                  ✕
                </Button>
              </div>
            ))}
          </div>
        </Card>

        <label className="block">
          <div className="text-xs font-semibold text-[color:var(--muted)]">Notas (opcional)</div>
          <textarea
            value={notes}
            onChange={(e) => setNotes(e.target.value)}
            rows={3}
            className="mt-1 w-full rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] px-3 py-3 text-sm outline-none focus:ring-2 focus:ring-[color:var(--accent)]"
            placeholder="Sensaciones, sueño, molestias, pacing..."
          />
        </label>

        <Button variant="primary" className="w-full py-4 text-base" disabled={!canSave} onClick={save}>
          GUARDAR
        </Button>

        {savedInsight && (
          <div className="rounded-2xl border border-[color:var(--border)] bg-[color:var(--panel2)] p-3 text-sm text-[color:var(--muted)]">
            <div className="text-xs font-extrabold text-[color:var(--text)]">Insight</div>
            <div className="mt-1">{savedInsight}</div>
          </div>
        )}

        <div className="text-xs text-[color:var(--muted)]">
          // hook API here → conectar a backend (Glide / API) en iteración siguiente.
        </div>
      </div>
    </Modal>
  );
}

// =======================================================
// END OF PROJECT
// =======================================================
