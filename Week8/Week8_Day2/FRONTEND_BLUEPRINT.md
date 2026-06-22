# Frontend Implementation Blueprint

## Enterprise RAG Platform – React + TypeScript + Tailwind CSS Frontend

---

## 1. Project Structure

```
frontend/
├── public/
│   ├── index.html
│   └── favicon.ico
├── src/
│   ├── main.tsx                        # App entry point
│   ├── App.tsx                         # Root component + router
│   ├── vite-env.d.ts                   # Vite type declarations
│   │
│   ├── assets/
│   │   └── logo.svg
│   │
│   ├── components/
│   │   ├── common/
│   │   │   ├── Button.tsx              # Reusable button
│   │   │   ├── Card.tsx                # Card wrapper
│   │   │   ├── Modal.tsx               # Modal dialog
│   │   │   ├── Input.tsx               # Form input
│   │   │   ├── Select.tsx              # Dropdown select
│   │   │   ├── Badge.tsx               # Status badge
│   │   │   ├── Spinner.tsx             # Loading spinner
│   │   │   ├── EmptyState.tsx          # No data placeholder
│   │   │   ├── Pagination.tsx          # Pagination controls
│   │   │   └── FileUpload.tsx          # Drag & drop file upload
│   │   │
│   │   ├── layout/
│   │   │   ├── AppLayout.tsx           # Main layout wrapper
│   │   │   ├── Sidebar.tsx             # Navigation sidebar
│   │   │   ├── Header.tsx              # Top header bar
│   │   │   └── ProtectedRoute.tsx      # Auth guard wrapper
│   │   │
│   │   ├── charts/
│   │   │   ├── BarChart.tsx            # Bar chart component
│   │   │   ├── LineChart.tsx           # Line chart component
│   │   │   ├── PieChart.tsx            # Pie chart component
│   │   │   └── MetricCard.tsx          # KPI metric card
│   │   │
│   │   ├── documents/
│   │   │   ├── DocumentList.tsx        # Document table/grid
│   │   │   ├── DocumentCard.tsx        # Single document card
│   │   │   ├── DocumentDetail.tsx      # Document detail view
│   │   │   └── UploadModal.tsx         # Upload dialog
│   │   │
│   │   ├── chunking/
│   │   │   ├── ChunkViewer.tsx         # Chunk content display
│   │   │   ├── ChunkStats.tsx          # Token counts, metadata
│   │   │   ├── StrategySelector.tsx    # Chunking strategy picker
│   │   │   └── ChunkComparison.tsx     # Side-by-side comparison
│   │   │
│   │   ├── search/
│   │   │   ├── SearchBar.tsx           # Search input with options
│   │   │   ├── SearchResults.tsx       # Results list
│   │   │   ├── ResultCard.tsx          # Single result item
│   │   │   └── SearchFilters.tsx       # Filter controls
│   │   │
│   │   └── rag/
│   │       ├── ChatInterface.tsx       # Q&A chat UI
│   │       ├── ChatMessage.tsx         # Single message bubble
│   │       ├── SourceCitation.tsx      # Source reference card
│   │       └── AgentSteps.tsx          # Agentic workflow steps
│   │
│   ├── pages/
│   │   ├── Dashboard/
│   │   │   └── DashboardPage.tsx       # Main analytics dashboard
│   │   ├── Auth/
│   │   │   ├── LoginPage.tsx           # Login form
│   │   │   └── RegisterPage.tsx        # Registration form
│   │   ├── Ingestion/
│   │   │   ├── IngestionPage.tsx       # Document upload & management
│   │   │   └── WebCrawlPage.tsx        # Web crawl interface
│   │   ├── ChunkingStudio/
│   │   │   └── ChunkingStudioPage.tsx  # Chunking config & visualization
│   │   ├── SearchStudio/
│   │   │   └── SearchStudioPage.tsx    # Search interface
│   │   ├── RAG/
│   │   │   └── RAGPage.tsx             # Question answering
│   │   ├── Comparison/
│   │   │   └── ComparisonPage.tsx      # Document comparison
│   │   └── Admin/
│   │       ├── UsersPage.tsx           # User management
│   │       └── AuditPage.tsx           # Audit logs
│   │
│   ├── services/
│   │   ├── api.ts                      # Axios instance + interceptors
│   │   ├── authService.ts             # Auth API calls
│   │   ├── documentService.ts         # Document API calls
│   │   ├── chunkingService.ts         # Chunking API calls
│   │   ├── searchService.ts           # Search API calls
│   │   ├── ragService.ts             # RAG API calls
│   │   ├── comparisonService.ts       # Comparison API calls
│   │   └── analyticsService.ts        # Analytics API calls
│   │
│   ├── store/
│   │   ├── authStore.ts               # Auth state (Zustand)
│   │   ├── documentStore.ts           # Document state
│   │   ├── searchStore.ts             # Search state
│   │   └── uiStore.ts                 # UI state (sidebar, theme)
│   │
│   ├── hooks/
│   │   ├── useAuth.ts                 # Auth hook
│   │   ├── useDocuments.ts            # Documents data hook
│   │   ├── useSearch.ts               # Search hook
│   │   └── useDebounce.ts             # Debounce utility hook
│   │
│   ├── types/
│   │   ├── auth.ts                    # Auth types
│   │   ├── document.ts                # Document types
│   │   ├── chunk.ts                   # Chunk types
│   │   ├── search.ts                  # Search types
│   │   ├── rag.ts                     # RAG types
│   │   └── analytics.ts              # Analytics types
│   │
│   └── utils/
│       ├── constants.ts               # App constants
│       ├── formatters.ts              # Date, number formatters
│       └── validators.ts              # Form validation rules
│
├── tailwind.config.js
├── postcss.config.js
├── tsconfig.json
├── vite.config.ts
├── package.json
├── Dockerfile
└── .env.example
```

---

## 2. Core Setup Files

### 2.1 Package Configuration (`package.json`)

```json
{
  "name": "enterprise-rag-frontend",
  "private": true,
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "lint": "eslint . --ext ts,tsx"
  },
  "dependencies": {
    "react": "^18.3.1",
    "react-dom": "^18.3.1",
    "react-router-dom": "^6.23.0",
    "axios": "^1.6.8",
    "zustand": "^4.5.2",
    "react-hook-form": "^7.51.3",
    "recharts": "^2.12.6",
    "lucide-react": "^0.372.0",
    "sonner": "^1.4.41",
    "clsx": "^2.1.1",
    "date-fns": "^3.6.0"
  },
  "devDependencies": {
    "@types/react": "^18.3.1",
    "@types/react-dom": "^18.3.0",
    "@vitejs/plugin-react": "^4.2.1",
    "autoprefixer": "^10.4.19",
    "eslint": "^9.2.0",
    "postcss": "^8.4.38",
    "tailwindcss": "^3.4.3",
    "typescript": "^5.4.5",
    "vite": "^5.2.11"
  }
}
```

### 2.2 Tailwind Configuration (`tailwind.config.js`)

```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: ["./index.html", "./src/**/*.{js,ts,jsx,tsx}"],
  theme: {
    extend: {
      colors: {
        primary: {
          50: "#eff6ff",
          100: "#dbeafe",
          200: "#bfdbfe",
          300: "#93c5fd",
          400: "#60a5fa",
          500: "#3b82f6",
          600: "#2563eb",
          700: "#1d4ed8",
          800: "#1e40af",
          900: "#1e3a8a",
        },
        sidebar: {
          bg: "#1e293b",
          hover: "#334155",
          active: "#3b82f6",
        },
      },
      fontFamily: {
        sans: ["Inter", "system-ui", "sans-serif"],
        mono: ["JetBrains Mono", "monospace"],
      },
    },
  },
  plugins: [],
};
```

### 2.3 Vite Configuration (`vite.config.ts`)

```typescript
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import path from "path";

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
  server: {
    port: 3000,
    proxy: {
      "/api": {
        target: "http://localhost:8000",
        changeOrigin: true,
      },
    },
  },
});
```

---

## 3. Phase 1: Foundation Components

### 3.1 API Service (`src/services/api.ts`)

```typescript
import axios, { AxiosError, InternalAxiosRequestConfig } from "axios";

const API_BASE_URL = import.meta.env.VITE_API_URL || "http://localhost:8000/api/v1";

const api = axios.create({
  baseURL: API_BASE_URL,
  headers: {
    "Content-Type": "application/json",
  },
});

// Request interceptor – attach JWT token
api.interceptors.request.use((config: InternalAxiosRequestConfig) => {
  const token = localStorage.getItem("access_token");
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Response interceptor – handle 401, refresh token
api.interceptors.response.use(
  (response) => response,
  async (error: AxiosError) => {
    const originalRequest = error.config;

    if (error.response?.status === 401 && originalRequest && !originalRequest._retry) {
      originalRequest._retry = true;

      try {
        const refreshToken = localStorage.getItem("refresh_token");
        const res = await axios.post(`${API_BASE_URL}/auth/refresh`, {
          refresh_token: refreshToken,
        });

        const { access_token } = res.data;
        localStorage.setItem("access_token", access_token);
        originalRequest.headers.Authorization = `Bearer ${access_token}`;
        return api(originalRequest);
      } catch {
        // Refresh failed – logout
        localStorage.removeItem("access_token");
        localStorage.removeItem("refresh_token");
        window.location.href = "/login";
      }
    }

    return Promise.reject(error);
  }
);

export default api;
```

### 3.2 Auth Store (`src/store/authStore.ts`)

```typescript
import { create } from "zustand";
import { persist } from "zustand/middleware";

interface User {
  id: string;
  email: string;
  name: string;
  role: "admin" | "editor" | "viewer" | "partner";
}

interface AuthState {
  user: User | null;
  isAuthenticated: boolean;
  setUser: (user: User) => void;
  logout: () => void;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      isAuthenticated: false,
      setUser: (user) => set({ user, isAuthenticated: true }),
      logout: () => {
        localStorage.removeItem("access_token");
        localStorage.removeItem("refresh_token");
        set({ user: null, isAuthenticated: false });
      },
    }),
    {
      name: "auth-storage",
      partialize: (state) => ({ user: state.user, isAuthenticated: state.isAuthenticated }),
    }
  )
);
```

### 3.3 Auth Service (`src/services/authService.ts`)

```typescript
import api from "./api";
import { LoginRequest, RegisterRequest, TokenResponse, UserResponse } from "@/types/auth";

export const authService = {
  async login(data: LoginRequest): Promise<TokenResponse> {
    const res = await api.post("/auth/login", data);
    const tokens = res.data;
    localStorage.setItem("access_token", tokens.access_token);
    localStorage.setItem("refresh_token", tokens.refresh_token);
    return tokens;
  },

  async register(data: RegisterRequest): Promise<UserResponse> {
    const res = await api.post("/auth/register", data);
    return res.data;
  },

  async getMe(): Promise<UserResponse> {
    const res = await api.get("/auth/me");
    return res.data;
  },

  logout() {
    localStorage.removeItem("access_token");
    localStorage.removeItem("refresh_token");
  },
};
```

### 3.4 Types (`src/types/auth.ts`)

```typescript
export interface LoginRequest {
  email: string;
  password: string;
}

export interface RegisterRequest {
  email: string;
  password: string;
  name: string;
  consent_gdpr: boolean;
}

export interface TokenResponse {
  access_token: string;
  refresh_token: string;
  token_type: string;
}

export interface UserResponse {
  id: string;
  email: string;
  name: string;
  role: "admin" | "editor" | "viewer" | "partner";
}
```

### 3.5 Types (`src/types/document.ts`)

```typescript
export interface Document {
  id: string;
  title: string;
  source_type: "pdf" | "docx" | "txt" | "csv" | "web" | "confluence";
  status: "uploading" | "processing" | "chunked" | "embedded" | "ready" | "error";
  metadata: DocumentMetadata;
  created_at: string;
}

export interface DocumentMetadata {
  file_size_bytes: number;
  page_count?: number;
  word_count: number;
  char_count: number;
  language?: string;
  mime_type?: string;
  author?: string;
  tags?: string[];
}

export interface DocumentListResponse {
  documents: Document[];
  total: number;
  skip: number;
  limit: number;
}
```

### 3.6 Types (`src/types/chunk.ts`)

```typescript
export interface Chunk {
  content: string;
  chunk_index: number;
  start_char: number;
  end_char: number;
  token_count: number;
  metadata: Record<string, any>;
}

export interface ChunkRequest {
  document_id: string;
  strategy: ChunkingStrategy;
  params?: Record<string, any>;
}

export interface ChunkResponse {
  document_id: string;
  strategy: string;
  total_chunks: number;
  chunks: Chunk[];
}

export type ChunkingStrategy =
  | "fixed"
  | "recursive"
  | "semantic"
  | "structure"
  | "query_aware"
  | "metadata_aware"
  | "llm_based"
  | "agentic";

export interface StrategyInfo {
  id: ChunkingStrategy;
  name: string;
  description: string;
}
```

### 3.7 Types (`src/types/search.ts`)

```typescript
export interface SearchRequest {
  query: string;
  search_type: "semantic" | "keyword" | "hybrid";
  top_k?: number;
  filters?: SearchFilters;
}

export interface SearchFilters {
  document_id?: string;
  strategy?: string;
  source_type?: string;
}

export interface SearchResult {
  chunk_id: string;
  content: string;
  document_id: string;
  chunk_index: number;
  score: number;
  hybrid_score?: number;
  metadata: Record<string, any>;
}
```

### 3.8 Types (`src/types/rag.ts`)

```typescript
export interface RAGRequest {
  question: string;
  filters?: Record<string, any>;
}

export interface RAGResponse {
  answer: string;
  sources: RAGSource[];
  confidence: number;
}

export interface RAGSource {
  chunk_id: string;
  document_id: string;
  score: number;
  preview: string;
}

export interface AgentResponse {
  answer: string;
  sub_queries: string[];
  sources: { chunk_id: string; document_id: string }[];
  iterations: number;
}
```

---

## 4. Layout Components

### 4.1 App Layout (`src/components/layout/AppLayout.tsx`)

```tsx
import { Outlet } from "react-router-dom";
import { Sidebar } from "./Sidebar";
import { Header } from "./Header";

export function AppLayout() {
  return (
    <div className="flex h-screen bg-gray-50">
      <Sidebar />
      <div className="flex flex-1 flex-col overflow-hidden">
        <Header />
        <main className="flex-1 overflow-y-auto p-6">
          <Outlet />
        </main>
      </div>
    </div>
  );
}
```

### 4.2 Sidebar (`src/components/layout/Sidebar.tsx`)

```tsx
import { NavLink } from "react-router-dom";
import {
  LayoutDashboard,
  Upload,
  Scissors,
  Search,
  MessageSquare,
  GitCompare,
  Users,
  Shield,
} from "lucide-react";
import { useAuthStore } from "@/store/authStore";
import { clsx } from "clsx";

const navItems = [
  { to: "/dashboard", icon: LayoutDashboard, label: "Dashboard" },
  { to: "/ingestion", icon: Upload, label: "Ingestion" },
  { to: "/chunking-studio", icon: Scissors, label: "Chunking Studio" },
  { to: "/search-studio", icon: Search, label: "Search Studio" },
  { to: "/rag", icon: MessageSquare, label: "AI Q&A" },
  { to: "/comparison", icon: GitCompare, label: "Comparison" },
];

const adminItems = [
  { to: "/admin/users", icon: Users, label: "Users" },
  { to: "/admin/audit", icon: Shield, label: "Audit Logs" },
];

export function Sidebar() {
  const { user } = useAuthStore();

  return (
    <aside className="flex w-64 flex-col bg-sidebar-bg text-white">
      {/* Logo */}
      <div className="flex h-16 items-center px-6">
        <h1 className="text-lg font-bold">RAG Platform</h1>
      </div>

      {/* Navigation */}
      <nav className="flex-1 space-y-1 px-3 py-4">
        {navItems.map((item) => (
          <NavLink
            key={item.to}
            to={item.to}
            className={({ isActive }) =>
              clsx(
                "flex items-center gap-3 rounded-lg px-3 py-2 text-sm transition-colors",
                isActive
                  ? "bg-sidebar-active text-white"
                  : "text-gray-300 hover:bg-sidebar-hover hover:text-white"
              )
            }
          >
            <item.icon className="h-5 w-5" />
            {item.label}
          </NavLink>
        ))}

        {/* Admin section */}
        {user?.role === "admin" && (
          <>
            <div className="my-4 border-t border-gray-600" />
            <p className="px-3 text-xs uppercase text-gray-400">Admin</p>
            {adminItems.map((item) => (
              <NavLink
                key={item.to}
                to={item.to}
                className={({ isActive }) =>
                  clsx(
                    "flex items-center gap-3 rounded-lg px-3 py-2 text-sm transition-colors",
                    isActive
                      ? "bg-sidebar-active text-white"
                      : "text-gray-300 hover:bg-sidebar-hover hover:text-white"
                  )
                }
              >
                <item.icon className="h-5 w-5" />
                {item.label}
              </NavLink>
            ))}
          </>
        )}
      </nav>
    </aside>
  );
}
```

### 4.3 Header (`src/components/layout/Header.tsx`)

```tsx
import { LogOut, User } from "lucide-react";
import { useAuthStore } from "@/store/authStore";
import { useNavigate } from "react-router-dom";

export function Header() {
  const { user, logout } = useAuthStore();
  const navigate = useNavigate();

  const handleLogout = () => {
    logout();
    navigate("/login");
  };

  return (
    <header className="flex h-16 items-center justify-between border-b bg-white px-6">
      <div />
      <div className="flex items-center gap-4">
        <div className="flex items-center gap-2 text-sm text-gray-600">
          <User className="h-4 w-4" />
          <span>{user?.name}</span>
          <span className="rounded bg-primary-100 px-2 py-0.5 text-xs text-primary-700">
            {user?.role}
          </span>
        </div>
        <button
          onClick={handleLogout}
          className="rounded-lg p-2 text-gray-400 hover:bg-gray-100 hover:text-gray-600"
        >
          <LogOut className="h-5 w-5" />
        </button>
      </div>
    </header>
  );
}
```

### 4.4 Protected Route (`src/components/layout/ProtectedRoute.tsx`)

```tsx
import { Navigate } from "react-router-dom";
import { useAuthStore } from "@/store/authStore";

interface Props {
  children: React.ReactNode;
  allowedRoles?: string[];
}

export function ProtectedRoute({ children, allowedRoles }: Props) {
  const { isAuthenticated, user } = useAuthStore();

  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }

  if (allowedRoles && user && !allowedRoles.includes(user.role)) {
    return <Navigate to="/dashboard" replace />;
  }

  return <>{children}</>;
}
```

---

## 5. Page Implementations

### 5.1 Login Page (`src/pages/Auth/LoginPage.tsx`)

```tsx
import { useState } from "react";
import { useForm } from "react-hook-form";
import { useNavigate, Link } from "react-router-dom";
import { authService } from "@/services/authService";
import { useAuthStore } from "@/store/authStore";
import { LoginRequest } from "@/types/auth";
import { toast } from "sonner";

export function LoginPage() {
  const [loading, setLoading] = useState(false);
  const navigate = useNavigate();
  const { setUser } = useAuthStore();
  const { register, handleSubmit, formState: { errors } } = useForm<LoginRequest>();

  const onSubmit = async (data: LoginRequest) => {
    setLoading(true);
    try {
      await authService.login(data);
      const user = await authService.getMe();
      setUser(user);
      toast.success("Login successful");
      navigate("/dashboard");
    } catch (err: any) {
      toast.error(err.response?.data?.detail || "Login failed");
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="flex min-h-screen items-center justify-center bg-gray-50">
      <div className="w-full max-w-md rounded-xl bg-white p-8 shadow-lg">
        <h2 className="mb-6 text-center text-2xl font-bold text-gray-900">
          Enterprise RAG Platform
        </h2>

        <form onSubmit={handleSubmit(onSubmit)} className="space-y-4">
          <div>
            <label className="block text-sm font-medium text-gray-700">Email</label>
            <input
              type="email"
              {...register("email", { required: "Email is required" })}
              className="mt-1 w-full rounded-lg border px-3 py-2 focus:border-primary-500 focus:outline-none focus:ring-1 focus:ring-primary-500"
            />
            {errors.email && (
              <p className="mt-1 text-sm text-red-500">{errors.email.message}</p>
            )}
          </div>

          <div>
            <label className="block text-sm font-medium text-gray-700">Password</label>
            <input
              type="password"
              {...register("password", { required: "Password is required", minLength: 8 })}
              className="mt-1 w-full rounded-lg border px-3 py-2 focus:border-primary-500 focus:outline-none focus:ring-1 focus:ring-primary-500"
            />
            {errors.password && (
              <p className="mt-1 text-sm text-red-500">{errors.password.message}</p>
            )}
          </div>

          <button
            type="submit"
            disabled={loading}
            className="w-full rounded-lg bg-primary-600 py-2 text-white hover:bg-primary-700 disabled:opacity-50"
          >
            {loading ? "Signing in..." : "Sign In"}
          </button>
        </form>

        <p className="mt-4 text-center text-sm text-gray-500">
          Don't have an account?{" "}
          <Link to="/register" className="text-primary-600 hover:underline">
            Register
          </Link>
        </p>
      </div>
    </div>
  );
}
```

### 5.2 Dashboard Page (`src/pages/Dashboard/DashboardPage.tsx`)

```tsx
import { useEffect, useState } from "react";
import { FileText, Layers, Database, Search } from "lucide-react";
import { MetricCard } from "@/components/charts/MetricCard";
import { BarChart } from "@/components/charts/BarChart";
import api from "@/services/api";

interface DashboardData {
  overview: {
    total_documents: number;
    total_chunks: number;
    total_embeddings: number;
    total_searches: number;
    total_users: number;
  };
  recent_activity: {
    searches_last_7_days: number;
    uploads_last_7_days: number;
  };
}

export function DashboardPage() {
  const [data, setData] = useState<DashboardData | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchDashboard = async () => {
      try {
        const res = await api.get("/analytics/dashboard");
        setData(res.data);
      } catch (err) {
        console.error("Failed to fetch dashboard", err);
      } finally {
        setLoading(false);
      }
    };
    fetchDashboard();
  }, []);

  if (loading) {
    return <div className="flex h-64 items-center justify-center">Loading...</div>;
  }

  return (
    <div className="space-y-6">
      <h1 className="text-2xl font-bold text-gray-900">Dashboard</h1>

      {/* Metrics Grid */}
      <div className="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-4">
        <MetricCard
          title="Total Documents"
          value={data?.overview.total_documents || 0}
          icon={FileText}
          color="blue"
        />
        <MetricCard
          title="Total Chunks"
          value={data?.overview.total_chunks || 0}
          icon={Layers}
          color="green"
        />
        <MetricCard
          title="Embeddings"
          value={data?.overview.total_embeddings || 0}
          icon={Database}
          color="purple"
        />
        <MetricCard
          title="Total Searches"
          value={data?.overview.total_searches || 0}
          icon={Search}
          color="orange"
        />
      </div>

      {/* Recent Activity */}
      <div className="grid grid-cols-1 gap-6 lg:grid-cols-2">
        <div className="rounded-xl bg-white p-6 shadow-sm">
          <h3 className="mb-4 text-lg font-semibold">Recent Activity (7 days)</h3>
          <div className="space-y-3">
            <div className="flex justify-between">
              <span className="text-gray-600">Searches</span>
              <span className="font-medium">{data?.recent_activity.searches_last_7_days}</span>
            </div>
            <div className="flex justify-between">
              <span className="text-gray-600">Uploads</span>
              <span className="font-medium">{data?.recent_activity.uploads_last_7_days}</span>
            </div>
          </div>
        </div>

        <div className="rounded-xl bg-white p-6 shadow-sm">
          <h3 className="mb-4 text-lg font-semibold">System Health</h3>
          <div className="space-y-3">
            <div className="flex items-center justify-between">
              <span className="text-gray-600">API Status</span>
              <span className="rounded-full bg-green-100 px-3 py-1 text-xs font-medium text-green-700">
                Healthy
              </span>
            </div>
            <div className="flex items-center justify-between">
              <span className="text-gray-600">Database</span>
              <span className="rounded-full bg-green-100 px-3 py-1 text-xs font-medium text-green-700">
                Connected
              </span>
            </div>
          </div>
        </div>
      </div>
    </div>
  );
}
```

### 5.3 Ingestion Page (`src/pages/Ingestion/IngestionPage.tsx`)

```tsx
import { useState, useEffect, useCallback } from "react";
import { Upload, Globe, BookOpen, Trash2 } from "lucide-react";
import { documentService } from "@/services/documentService";
import { Document } from "@/types/document";
import { toast } from "sonner";

export function IngestionPage() {
  const [documents, setDocuments] = useState<Document[]>([]);
  const [loading, setLoading] = useState(true);
  const [uploading, setUploading] = useState(false);

  const fetchDocuments = useCallback(async () => {
    try {
      const res = await documentService.list();
      setDocuments(res.documents);
    } finally {
      setLoading(false);
    }
  }, []);

  useEffect(() => {
    fetchDocuments();
  }, [fetchDocuments]);

  const handleFileUpload = async (files: FileList) => {
    setUploading(true);
    try {
      for (const file of Array.from(files)) {
        await documentService.upload(file);
        toast.success(`Uploaded: ${file.name}`);
      }
      fetchDocuments();
    } catch (err: any) {
      toast.error(err.response?.data?.detail || "Upload failed");
    } finally {
      setUploading(false);
    }
  };

  const handleDrop = (e: React.DragEvent) => {
    e.preventDefault();
    if (e.dataTransfer.files.length) {
      handleFileUpload(e.dataTransfer.files);
    }
  };

  return (
    <div className="space-y-6">
      <h1 className="text-2xl font-bold text-gray-900">Knowledge Ingestion</h1>

      {/* Upload Zone */}
      <div
        onDrop={handleDrop}
        onDragOver={(e) => e.preventDefault()}
        className="flex flex-col items-center justify-center rounded-xl border-2 border-dashed border-gray-300 bg-white p-12 transition-colors hover:border-primary-400"
      >
        <Upload className="mb-4 h-12 w-12 text-gray-400" />
        <p className="mb-2 text-lg font-medium text-gray-700">
          Drag & drop files here
        </p>
        <p className="mb-4 text-sm text-gray-500">
          Supports PDF, DOCX, TXT, CSV (max 50MB)
        </p>
        <label className="cursor-pointer rounded-lg bg-primary-600 px-4 py-2 text-white hover:bg-primary-700">
          {uploading ? "Uploading..." : "Browse Files"}
          <input
            type="file"
            multiple
            accept=".pdf,.docx,.txt,.csv"
            className="hidden"
            onChange={(e) => e.target.files && handleFileUpload(e.target.files)}
            disabled={uploading}
          />
        </label>
      </div>

      {/* Source Tabs */}
      <div className="flex gap-4">
        <button className="flex items-center gap-2 rounded-lg bg-primary-50 px-4 py-2 text-primary-700">
          <Upload className="h-4 w-4" /> Files
        </button>
        <button className="flex items-center gap-2 rounded-lg px-4 py-2 text-gray-600 hover:bg-gray-100">
          <Globe className="h-4 w-4" /> Web Crawl
        </button>
        <button className="flex items-center gap-2 rounded-lg px-4 py-2 text-gray-600 hover:bg-gray-100">
          <BookOpen className="h-4 w-4" /> Confluence
        </button>
      </div>

      {/* Document List */}
      <div className="rounded-xl bg-white shadow-sm">
        <div className="border-b px-6 py-4">
          <h3 className="font-semibold">Documents ({documents.length})</h3>
        </div>
        <div className="divide-y">
          {documents.map((doc) => (
            <div key={doc.id} className="flex items-center justify-between px-6 py-4">
              <div>
                <p className="font-medium text-gray-900">{doc.title}</p>
                <p className="text-sm text-gray-500">
                  {doc.source_type.toUpperCase()} • {doc.metadata.word_count} words •{" "}
                  {new Date(doc.created_at).toLocaleDateString()}
                </p>
              </div>
              <div className="flex items-center gap-3">
                <span
                  className={`rounded-full px-3 py-1 text-xs font-medium ${
                    doc.status === "ready"
                      ? "bg-green-100 text-green-700"
                      : doc.status === "error"
                      ? "bg-red-100 text-red-700"
                      : "bg-yellow-100 text-yellow-700"
                  }`}
                >
                  {doc.status}
                </span>
              </div>
            </div>
          ))}
          {!loading && documents.length === 0 && (
            <div className="py-12 text-center text-gray-400">
              No documents uploaded yet. Upload your first document above.
            </div>
          )}
        </div>
      </div>
    </div>
  );
}
```

### 5.4 Chunking Studio Page (`src/pages/ChunkingStudio/ChunkingStudioPage.tsx`)

```tsx
import { useState, useEffect } from "react";
import { chunkingService } from "@/services/chunkingService";
import { documentService } from "@/services/documentService";
import { Document } from "@/types/document";
import { Chunk, ChunkingStrategy, StrategyInfo } from "@/types/chunk";
import { toast } from "sonner";

export function ChunkingStudioPage() {
  const [documents, setDocuments] = useState<Document[]>([]);
  const [selectedDoc, setSelectedDoc] = useState<string>("");
  const [selectedStrategy, setSelectedStrategy] = useState<ChunkingStrategy>("recursive");
  const [chunks, setChunks] = useState<Chunk[]>([]);
  const [processing, setProcessing] = useState(false);
  const [strategies, setStrategies] = useState<StrategyInfo[]>([]);

  useEffect(() => {
    documentService.list().then((res) => setDocuments(res.documents));
    chunkingService.getStrategies().then((res) => setStrategies(res.strategies));
  }, []);

  const handleProcess = async () => {
    if (!selectedDoc) {
      toast.error("Select a document first");
      return;
    }
    setProcessing(true);
    try {
      const result = await chunkingService.process({
        document_id: selectedDoc,
        strategy: selectedStrategy,
      });
      setChunks(result.chunks);
      toast.success(`Generated ${result.total_chunks} chunks`);
    } catch (err: any) {
      toast.error(err.response?.data?.detail || "Chunking failed");
    } finally {
      setProcessing(false);
    }
  };

  return (
    <div className="space-y-6">
      <h1 className="text-2xl font-bold text-gray-900">Chunking Studio</h1>

      {/* Controls */}
      <div className="flex flex-wrap items-end gap-4 rounded-xl bg-white p-6 shadow-sm">
        <div className="flex-1">
          <label className="block text-sm font-medium text-gray-700">Document</label>
          <select
            value={selectedDoc}
            onChange={(e) => setSelectedDoc(e.target.value)}
            className="mt-1 w-full rounded-lg border px-3 py-2"
          >
            <option value="">Select a document...</option>
            {documents.map((doc) => (
              <option key={doc.id} value={doc.id}>
                {doc.title}
              </option>
            ))}
          </select>
        </div>

        <div className="flex-1">
          <label className="block text-sm font-medium text-gray-700">Strategy</label>
          <select
            value={selectedStrategy}
            onChange={(e) => setSelectedStrategy(e.target.value as ChunkingStrategy)}
            className="mt-1 w-full rounded-lg border px-3 py-2"
          >
            {strategies.map((s) => (
              <option key={s.id} value={s.id}>
                {s.name}
              </option>
            ))}
          </select>
        </div>

        <button
          onClick={handleProcess}
          disabled={processing || !selectedDoc}
          className="rounded-lg bg-primary-600 px-6 py-2 text-white hover:bg-primary-700 disabled:opacity-50"
        >
          {processing ? "Processing..." : "Generate Chunks"}
        </button>
      </div>

      {/* Chunk Visualization */}
      {chunks.length > 0 && (
        <div className="space-y-4">
          <div className="flex items-center justify-between">
            <h3 className="text-lg font-semibold">
              Results: {chunks.length} chunks ({selectedStrategy})
            </h3>
          </div>

          <div className="grid grid-cols-1 gap-4 lg:grid-cols-2">
            {chunks.map((chunk, idx) => (
              <div key={idx} className="rounded-xl border bg-white p-4 shadow-sm">
                <div className="mb-2 flex items-center justify-between">
                  <span className="text-sm font-medium text-primary-600">
                    Chunk #{chunk.chunk_index + 1}
                  </span>
                  <span className="rounded bg-gray-100 px-2 py-0.5 text-xs text-gray-600">
                    {chunk.token_count} tokens
                  </span>
                </div>
                <p className="line-clamp-4 text-sm text-gray-700 font-mono">
                  {chunk.content}
                </p>
                <div className="mt-3 flex gap-2 text-xs text-gray-400">
                  <span>Chars: {chunk.start_char}-{chunk.end_char}</span>
                </div>
              </div>
            ))}
          </div>
        </div>
      )}
    </div>
  );
}
```

### 5.5 Search Studio Page (`src/pages/SearchStudio/SearchStudioPage.tsx`)

```tsx
import { useState } from "react";
import { Search as SearchIcon } from "lucide-react";
import { searchService } from "@/services/searchService";
import { SearchResult } from "@/types/search";
import { toast } from "sonner";

export function SearchStudioPage() {
  const [query, setQuery] = useState("");
  const [searchType, setSearchType] = useState<"semantic" | "keyword" | "hybrid">("hybrid");
  const [results, setResults] = useState<SearchResult[]>([]);
  const [searching, setSearching] = useState(false);
  const [responseTime, setResponseTime] = useState<number | null>(null);

  const handleSearch = async () => {
    if (!query.trim()) return;
    setSearching(true);
    const start = performance.now();

    try {
      const res = await searchService.search({
        query: query.trim(),
        search_type: searchType,
        top_k: 10,
      });
      setResults(res);
      setResponseTime(Math.round(performance.now() - start));
    } catch (err: any) {
      toast.error("Search failed");
    } finally {
      setSearching(false);
    }
  };

  return (
    <div className="space-y-6">
      <h1 className="text-2xl font-bold text-gray-900">Search Studio</h1>

      {/* Search Bar */}
      <div className="rounded-xl bg-white p-6 shadow-sm">
        <div className="flex gap-3">
          <div className="relative flex-1">
            <SearchIcon className="absolute left-3 top-3 h-5 w-5 text-gray-400" />
            <input
              type="text"
              value={query}
              onChange={(e) => setQuery(e.target.value)}
              onKeyDown={(e) => e.key === "Enter" && handleSearch()}
              placeholder="Ask a question or search for information..."
              className="w-full rounded-lg border py-2.5 pl-10 pr-4 focus:border-primary-500 focus:outline-none focus:ring-1 focus:ring-primary-500"
            />
          </div>
          <select
            value={searchType}
            onChange={(e) => setSearchType(e.target.value as any)}
            className="rounded-lg border px-3 py-2"
          >
            <option value="hybrid">Hybrid</option>
            <option value="semantic">Semantic</option>
            <option value="keyword">Keyword</option>
          </select>
          <button
            onClick={handleSearch}
            disabled={searching}
            className="rounded-lg bg-primary-600 px-6 py-2 text-white hover:bg-primary-700 disabled:opacity-50"
          >
            {searching ? "Searching..." : "Search"}
          </button>
        </div>

        {responseTime !== null && (
          <p className="mt-2 text-sm text-gray-500">
            {results.length} results in {responseTime}ms
          </p>
        )}
      </div>

      {/* Results */}
      <div className="space-y-3">
        {results.map((result, idx) => (
          <div key={result.chunk_id} className="rounded-xl border bg-white p-5 shadow-sm">
            <div className="mb-2 flex items-center justify-between">
              <span className="text-sm font-medium text-gray-500">
                Result #{idx + 1}
              </span>
              <span className="rounded bg-primary-50 px-2 py-0.5 text-xs font-medium text-primary-700">
                Score: {(result.score * 100).toFixed(1)}%
              </span>
            </div>
            <p className="text-gray-800">{result.content}</p>
            <div className="mt-3 flex gap-4 text-xs text-gray-400">
              <span>Doc: {result.document_id.slice(0, 8)}...</span>
              <span>Chunk #{result.chunk_index}</span>
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}
```

### 5.6 RAG Q&A Page (`src/pages/RAG/RAGPage.tsx`)

```tsx
import { useState, useRef, useEffect } from "react";
import { Send, Bot, User, Sparkles } from "lucide-react";
import { ragService } from "@/services/ragService";
import { RAGResponse } from "@/types/rag";

interface Message {
  id: string;
  role: "user" | "assistant";
  content: string;
  sources?: RAGResponse["sources"];
  timestamp: Date;
}

export function RAGPage() {
  const [messages, setMessages] = useState<Message[]>([]);
  const [input, setInput] = useState("");
  const [loading, setLoading] = useState(false);
  const [mode, setMode] = useState<"rag" | "agent">("rag");
  const messagesEndRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    messagesEndRef.current?.scrollIntoView({ behavior: "smooth" });
  }, [messages]);

  const handleSend = async () => {
    if (!input.trim() || loading) return;

    const userMessage: Message = {
      id: crypto.randomUUID(),
      role: "user",
      content: input.trim(),
      timestamp: new Date(),
    };
    setMessages((prev) => [...prev, userMessage]);
    setInput("");
    setLoading(true);

    try {
      const response =
        mode === "agent"
          ? await ragService.agentQuery(userMessage.content)
          : await ragService.ask(userMessage.content);

      const assistantMessage: Message = {
        id: crypto.randomUUID(),
        role: "assistant",
        content: response.answer,
        sources: response.sources,
        timestamp: new Date(),
      };
      setMessages((prev) => [...prev, assistantMessage]);
    } catch {
      const errorMessage: Message = {
        id: crypto.randomUUID(),
        role: "assistant",
        content: "Sorry, I encountered an error processing your question.",
        timestamp: new Date(),
      };
      setMessages((prev) => [...prev, errorMessage]);
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="flex h-[calc(100vh-8rem)] flex-col">
      {/* Header */}
      <div className="flex items-center justify-between pb-4">
        <h1 className="text-2xl font-bold text-gray-900">AI Question & Answer</h1>
        <div className="flex rounded-lg border p-1">
          <button
            onClick={() => setMode("rag")}
            className={`rounded px-3 py-1 text-sm ${
              mode === "rag" ? "bg-primary-600 text-white" : "text-gray-600"
            }`}
          >
            RAG Mode
          </button>
          <button
            onClick={() => setMode("agent")}
            className={`rounded px-3 py-1 text-sm ${
              mode === "agent" ? "bg-primary-600 text-white" : "text-gray-600"
            }`}
          >
            <Sparkles className="mr-1 inline h-3 w-3" />
            Agent Mode
          </button>
        </div>
      </div>

      {/* Messages */}
      <div className="flex-1 overflow-y-auto rounded-xl bg-white p-6 shadow-sm">
        {messages.length === 0 && (
          <div className="flex h-full items-center justify-center text-gray-400">
            <div className="text-center">
              <Bot className="mx-auto mb-4 h-12 w-12" />
              <p>Ask me anything about your knowledge base</p>
            </div>
          </div>
        )}

        {messages.map((msg) => (
          <div
            key={msg.id}
            className={`mb-4 flex ${msg.role === "user" ? "justify-end" : "justify-start"}`}
          >
            <div
              className={`max-w-[70%] rounded-xl px-4 py-3 ${
                msg.role === "user"
                  ? "bg-primary-600 text-white"
                  : "bg-gray-100 text-gray-800"
              }`}
            >
              <p className="whitespace-pre-wrap">{msg.content}</p>
              {msg.sources && msg.sources.length > 0 && (
                <div className="mt-3 border-t border-gray-200 pt-2">
                  <p className="text-xs font-medium text-gray-500">Sources:</p>
                  {msg.sources.map((source, i) => (
                    <p key={i} className="mt-1 text-xs text-gray-400">
                      [{i + 1}] {source.preview?.slice(0, 80)}...
                    </p>
                  ))}
                </div>
              )}
            </div>
          </div>
        ))}
        {loading && (
          <div className="flex justify-start">
            <div className="rounded-xl bg-gray-100 px-4 py-3 text-gray-500">
              Thinking...
            </div>
          </div>
        )}
        <div ref={messagesEndRef} />
      </div>

      {/* Input */}
      <div className="mt-4 flex gap-3">
        <input
          type="text"
          value={input}
          onChange={(e) => setInput(e.target.value)}
          onKeyDown={(e) => e.key === "Enter" && handleSend()}
          placeholder="Type your question..."
          className="flex-1 rounded-lg border px-4 py-3 focus:border-primary-500 focus:outline-none focus:ring-1 focus:ring-primary-500"
          disabled={loading}
        />
        <button
          onClick={handleSend}
          disabled={loading || !input.trim()}
          className="rounded-lg bg-primary-600 px-4 py-3 text-white hover:bg-primary-700 disabled:opacity-50"
        >
          <Send className="h-5 w-5" />
        </button>
      </div>
    </div>
  );
}
```

### 5.7 Document Comparison Page (`src/pages/Comparison/ComparisonPage.tsx`)

```tsx
import { useState, useEffect } from "react";
import { documentService } from "@/services/documentService";
import { comparisonService } from "@/services/comparisonService";
import { Document } from "@/types/document";
import { toast } from "sonner";

export function ComparisonPage() {
  const [documents, setDocuments] = useState<Document[]>([]);
  const [selectedDocs, setSelectedDocs] = useState<string[]>(["", ""]);
  const [result, setResult] = useState<any>(null);
  const [comparing, setComparing] = useState(false);

  useEffect(() => {
    documentService.list().then((res) => setDocuments(res.documents));
  }, []);

  const handleCompare = async () => {
    const validIds = selectedDocs.filter(Boolean);
    if (validIds.length < 2) {
      toast.error("Select at least 2 documents to compare");
      return;
    }
    setComparing(true);
    try {
      const res = await comparisonService.compare(validIds);
      setResult(res);
    } catch {
      toast.error("Comparison failed");
    } finally {
      setComparing(false);
    }
  };

  return (
    <div className="space-y-6">
      <h1 className="text-2xl font-bold text-gray-900">Document Comparison</h1>

      {/* Document Selection */}
      <div className="rounded-xl bg-white p-6 shadow-sm">
        <div className="grid grid-cols-1 gap-4 md:grid-cols-2">
          {selectedDocs.map((docId, idx) => (
            <div key={idx}>
              <label className="block text-sm font-medium text-gray-700">
                Document {idx + 1}
              </label>
              <select
                value={docId}
                onChange={(e) => {
                  const updated = [...selectedDocs];
                  updated[idx] = e.target.value;
                  setSelectedDocs(updated);
                }}
                className="mt-1 w-full rounded-lg border px-3 py-2"
              >
                <option value="">Select document...</option>
                {documents.map((doc) => (
                  <option key={doc.id} value={doc.id}>
                    {doc.title}
                  </option>
                ))}
              </select>
            </div>
          ))}
        </div>
        <button
          onClick={handleCompare}
          disabled={comparing}
          className="mt-4 rounded-lg bg-primary-600 px-6 py-2 text-white hover:bg-primary-700 disabled:opacity-50"
        >
          {comparing ? "Comparing..." : "Compare Documents"}
        </button>
      </div>

      {/* Results */}
      {result && (
        <div className="space-y-4">
          {Object.entries(result.similarities || {}).map(([pair, data]: [string, any]) => (
            <div key={pair} className="rounded-xl bg-white p-6 shadow-sm">
              <h3 className="mb-4 text-lg font-semibold">Similarity Analysis</h3>
              <div className="grid grid-cols-2 gap-4">
                <div className="rounded-lg bg-blue-50 p-4 text-center">
                  <p className="text-3xl font-bold text-blue-700">
                    {(data.text_similarity * 100).toFixed(1)}%
                  </p>
                  <p className="text-sm text-blue-600">Text Similarity</p>
                </div>
                <div className="rounded-lg bg-purple-50 p-4 text-center">
                  <p className="text-3xl font-bold text-purple-700">
                    {(data.semantic_similarity * 100).toFixed(1)}%
                  </p>
                  <p className="text-sm text-purple-600">Semantic Similarity</p>
                </div>
              </div>
            </div>
          ))}

          {Object.entries(result.differences || {}).map(([pair, data]: [string, any]) => (
            <div key={pair} className="rounded-xl bg-white p-6 shadow-sm">
              <h3 className="mb-4 text-lg font-semibold">Differences</h3>
              <div className="grid grid-cols-1 gap-4 md:grid-cols-2">
                <div>
                  <h4 className="mb-2 text-sm font-medium text-red-600">
                    Only in Document 1 ({data.only_in_first?.length || 0} sections)
                  </h4>
                  <ul className="space-y-1">
                    {data.only_in_first?.slice(0, 5).map((s: string, i: number) => (
                      <li key={i} className="text-sm text-gray-600 truncate">
                        • {s}
                      </li>
                    ))}
                  </ul>
                </div>
                <div>
                  <h4 className="mb-2 text-sm font-medium text-green-600">
                    Only in Document 2 ({data.only_in_second?.length || 0} sections)
                  </h4>
                  <ul className="space-y-1">
                    {data.only_in_second?.slice(0, 5).map((s: string, i: number) => (
                      <li key={i} className="text-sm text-gray-600 truncate">
                        • {s}
                      </li>
                    ))}
                  </ul>
                </div>
              </div>
            </div>
          ))}
        </div>
      )}
    </div>
  );
}
```

---

## 6. Services Layer

### 6.1 Document Service (`src/services/documentService.ts`)

```typescript
import api from "./api";
import { DocumentListResponse } from "@/types/document";

export const documentService = {
  async list(skip = 0, limit = 20): Promise<DocumentListResponse> {
    const res = await api.get("/documents", { params: { skip, limit } });
    return res.data;
  },

  async getById(id: string) {
    const res = await api.get(`/documents/${id}`);
    return res.data;
  },

  async upload(file: File) {
    const formData = new FormData();
    formData.append("file", file);
    const res = await api.post("/documents/upload", formData, {
      headers: { "Content-Type": "multipart/form-data" },
    });
    return res.data;
  },

  async delete(id: string) {
    await api.delete(`/documents/${id}`);
  },

  async crawlWeb(url: string) {
    const res = await api.post("/documents/crawl", { url });
    return res.data;
  },
};
```

### 6.2 Chunking Service (`src/services/chunkingService.ts`)

```typescript
import api from "./api";
import { ChunkRequest, ChunkResponse, StrategyInfo } from "@/types/chunk";

export const chunkingService = {
  async process(request: ChunkRequest): Promise<ChunkResponse> {
    const res = await api.post("/chunking/process", request);
    return res.data;
  },

  async getStrategies(): Promise<{ strategies: StrategyInfo[] }> {
    const res = await api.get("/chunking/strategies");
    return res.data;
  },

  async getChunks(documentId: string) {
    const res = await api.get(`/chunks/${documentId}`);
    return res.data;
  },

  async compareStrategies(documentId: string, strategies: string[]) {
    const res = await api.post("/chunking/compare", { document_id: documentId, strategies });
    return res.data;
  },
};
```

### 6.3 Search Service (`src/services/searchService.ts`)

```typescript
import api from "./api";
import { SearchRequest, SearchResult } from "@/types/search";

export const searchService = {
  async search(request: SearchRequest): Promise<SearchResult[]> {
    const endpoint = `/search/${request.search_type}`;
    const res = await api.post(endpoint, request);
    return res.data;
  },

  async submitFeedback(chunkId: string, relevance: number) {
    await api.post("/search/feedback", { chunk_id: chunkId, relevance });
  },
};
```

### 6.4 RAG Service (`src/services/ragService.ts`)

```typescript
import api from "./api";
import { RAGResponse, AgentResponse } from "@/types/rag";

export const ragService = {
  async ask(question: string, filters?: Record<string, any>): Promise<RAGResponse> {
    const res = await api.post("/rag/ask", { question, filters });
    return res.data;
  },

  async agentQuery(question: string): Promise<AgentResponse> {
    const res = await api.post("/rag/agent", { question });
    return res.data;
  },

  async summarize(documentId: string): Promise<{ summary: string }> {
    const res = await api.post("/rag/summarize", { document_id: documentId });
    return res.data;
  },
};
```

### 6.5 Comparison Service (`src/services/comparisonService.ts`)

```typescript
import api from "./api";

export const comparisonService = {
  async compare(documentIds: string[]) {
    const res = await api.post("/comparison/compare", { document_ids: documentIds });
    return res.data;
  },
};
```

---

## 7. Router Configuration (`src/App.tsx`)

```tsx
import { BrowserRouter, Routes, Route, Navigate } from "react-router-dom";
import { Toaster } from "sonner";
import { AppLayout } from "./components/layout/AppLayout";
import { ProtectedRoute } from "./components/layout/ProtectedRoute";
import { LoginPage } from "./pages/Auth/LoginPage";
import { RegisterPage } from "./pages/Auth/RegisterPage";
import { DashboardPage } from "./pages/Dashboard/DashboardPage";
import { IngestionPage } from "./pages/Ingestion/IngestionPage";
import { ChunkingStudioPage } from "./pages/ChunkingStudio/ChunkingStudioPage";
import { SearchStudioPage } from "./pages/SearchStudio/SearchStudioPage";
import { RAGPage } from "./pages/RAG/RAGPage";
import { ComparisonPage } from "./pages/Comparison/ComparisonPage";

export default function App() {
  return (
    <BrowserRouter>
      <Toaster position="top-right" richColors />
      <Routes>
        {/* Public routes */}
        <Route path="/login" element={<LoginPage />} />
        <Route path="/register" element={<RegisterPage />} />

        {/* Protected routes */}
        <Route
          element={
            <ProtectedRoute>
              <AppLayout />
            </ProtectedRoute>
          }
        >
          <Route path="/dashboard" element={<DashboardPage />} />
          <Route path="/ingestion" element={<IngestionPage />} />
          <Route path="/chunking-studio" element={<ChunkingStudioPage />} />
          <Route path="/search-studio" element={<SearchStudioPage />} />
          <Route path="/rag" element={<RAGPage />} />
          <Route path="/comparison" element={<ComparisonPage />} />
        </Route>

        {/* Redirect */}
        <Route path="/" element={<Navigate to="/dashboard" replace />} />
      </Routes>
    </BrowserRouter>
  );
}
```

---

## 8. Reusable Components

### 8.1 MetricCard (`src/components/charts/MetricCard.tsx`)

```tsx
import { LucideIcon } from "lucide-react";
import { clsx } from "clsx";

interface Props {
  title: string;
  value: number;
  icon: LucideIcon;
  color: "blue" | "green" | "purple" | "orange";
  change?: number;
}

const colorMap = {
  blue: "bg-blue-50 text-blue-600",
  green: "bg-green-50 text-green-600",
  purple: "bg-purple-50 text-purple-600",
  orange: "bg-orange-50 text-orange-600",
};

export function MetricCard({ title, value, icon: Icon, color, change }: Props) {
  return (
    <div className="rounded-xl bg-white p-6 shadow-sm">
      <div className="flex items-center justify-between">
        <div>
          <p className="text-sm text-gray-500">{title}</p>
          <p className="mt-1 text-3xl font-bold text-gray-900">
            {value.toLocaleString()}
          </p>
          {change !== undefined && (
            <p className={clsx("mt-1 text-sm", change >= 0 ? "text-green-600" : "text-red-600")}>
              {change >= 0 ? "+" : ""}{change}% from last week
            </p>
          )}
        </div>
        <div className={clsx("rounded-lg p-3", colorMap[color])}>
          <Icon className="h-6 w-6" />
        </div>
      </div>
    </div>
  );
}
```

### 8.2 FileUpload Component (`src/components/common/FileUpload.tsx`)

```tsx
import { useCallback, useState } from "react";
import { Upload, X, File } from "lucide-react";

interface Props {
  onUpload: (files: File[]) => void;
  accept?: string;
  multiple?: boolean;
  maxSize?: number; // MB
}

export function FileUpload({ onUpload, accept = ".pdf,.docx,.txt,.csv", multiple = true, maxSize = 50 }: Props) {
  const [dragActive, setDragActive] = useState(false);
  const [selectedFiles, setSelectedFiles] = useState<File[]>([]);

  const handleDrag = useCallback((e: React.DragEvent) => {
    e.preventDefault();
    e.stopPropagation();
    if (e.type === "dragenter" || e.type === "dragover") {
      setDragActive(true);
    } else if (e.type === "dragleave") {
      setDragActive(false);
    }
  }, []);

  const handleDrop = useCallback((e: React.DragEvent) => {
    e.preventDefault();
    setDragActive(false);
    const files = Array.from(e.dataTransfer.files);
    const valid = files.filter((f) => f.size <= maxSize * 1024 * 1024);
    setSelectedFiles(valid);
    onUpload(valid);
  }, [maxSize, onUpload]);

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    if (e.target.files) {
      const files = Array.from(e.target.files);
      setSelectedFiles(files);
      onUpload(files);
    }
  };

  const removeFile = (index: number) => {
    setSelectedFiles((prev) => prev.filter((_, i) => i !== index));
  };

  return (
    <div>
      <div
        onDragEnter={handleDrag}
        onDragLeave={handleDrag}
        onDragOver={handleDrag}
        onDrop={handleDrop}
        className={`flex flex-col items-center rounded-xl border-2 border-dashed p-8 transition-colors ${
          dragActive ? "border-primary-400 bg-primary-50" : "border-gray-300 bg-gray-50"
        }`}
      >
        <Upload className="mb-3 h-10 w-10 text-gray-400" />
        <p className="text-sm text-gray-600">Drag & drop or click to upload</p>
        <p className="mt-1 text-xs text-gray-400">Max {maxSize}MB per file</p>
        <label className="mt-3 cursor-pointer rounded-lg bg-primary-600 px-4 py-2 text-sm text-white hover:bg-primary-700">
          Browse Files
          <input type="file" className="hidden" accept={accept} multiple={multiple} onChange={handleChange} />
        </label>
      </div>

      {selectedFiles.length > 0 && (
        <div className="mt-3 space-y-2">
          {selectedFiles.map((file, idx) => (
            <div key={idx} className="flex items-center justify-between rounded-lg bg-gray-50 px-3 py-2">
              <div className="flex items-center gap-2">
                <File className="h-4 w-4 text-gray-400" />
                <span className="text-sm">{file.name}</span>
                <span className="text-xs text-gray-400">({(file.size / 1024).toFixed(0)} KB)</span>
              </div>
              <button onClick={() => removeFile(idx)} className="text-gray-400 hover:text-red-500">
                <X className="h-4 w-4" />
              </button>
            </div>
          ))}
        </div>
      )}
    </div>
  );
}
```

---

## 9. Environment Variables (`.env.example`)

```env
VITE_API_URL=http://localhost:8000/api/v1
VITE_APP_NAME=Enterprise RAG Platform
```

---

## 10. Frontend Dockerfile

```dockerfile
# Build stage
FROM node:20-alpine AS build
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production stage
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Nginx Configuration (`nginx.conf`)

```nginx
server {
    listen 80;
    root /usr/share/nginx/html;
    index index.html;

    # SPA routing
    location / {
        try_files $uri $uri/ /index.html;
    }

    # API proxy
    location /api/ {
        proxy_pass http://backend:8000/api/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    # Cache static assets
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

---

## 11. UI Wireframe Summary

```
┌───────────────────────────────────────────────────────────────┐
│  [Logo] RAG Platform          [User Name] [Role] [Logout]     │
├──────────┬────────────────────────────────────────────────────┤
│          │                                                    │
│ Dashboard│    ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐              │
│          │    │Docs │ │Chunks│ │Embed│ │Search│              │
│ Ingestion│    │ 42  │ │1,250│ │1,100│ │ 328 │              │
│          │    └─────┘ └─────┘ └─────┘ └─────┘              │
│ Chunking │                                                    │
│  Studio  │    ┌──────────────────────────────────────┐       │
│          │    │         Activity Chart                │       │
│ Search   │    │         ████ ██ ████ ██              │       │
│  Studio  │    └──────────────────────────────────────┘       │
│          │                                                    │
│ AI Q&A   │    ┌─────────────────┐ ┌─────────────────┐       │
│          │    │  Recent Uploads  │ │  System Health   │       │
│ Compare  │    │  • doc1.pdf     │ │  API: ●Healthy  │       │
│          │    │  • doc2.docx    │ │  DB:  ●Connected│       │
│ ──────── │    └─────────────────┘ └─────────────────┘       │
│ Admin    │                                                    │
│ Users    │                                                    │
│ Audit    │                                                    │
│          │                                                    │
└──────────┴────────────────────────────────────────────────────┘
```
