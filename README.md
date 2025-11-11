# 🧠 Cortex - Your Personal AI Operating System

> A minimalist second brain inspired by Kortex. Capture ideas, synthesize notes, and write with AI-powered assistance.

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Next.js](https://img.shields.io/badge/Next.js-14-black)
![TypeScript](https://img.shields.io/badge/TypeScript-5.0-blue)

## ✨ Features

- **🤖 AI-Powered Chat**: Integrated with Claude 3.5, GPT-4, and Mistral via OpenRouter
- **📝 Smart Note-Taking**: Capture and synthesize ideas with AI assistance
- **💡 Idea Synthesis**: Transform scattered thoughts into structured notes
- **🎯 Goal Tracking**: Set and monitor personal/professional goals
- **🌙 Dark Mode UI**: Minimalist glassmorphic design (#0D0D0D)
- **🔒 Privacy First**: Self-hosted with Supabase, your data stays yours
- **📱 Responsive**: Works seamlessly on desktop and mobile

## 🚀 Quick Start

### Prerequisites

- Node.js 18+ 
- Supabase account (free tier works)
- OpenRouter API key

### Installation

```bash
# Clone the repository
git clone https://github.com/Synthrealai/cortex.git
cd cortex

# Install dependencies
npm install

# Copy environment variables
cp .env.example .env.local
```

### Environment Setup

Create `.env.local`:

```env
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key
OPENROUTER_API_KEY=sk-or-v1-your_key
```

**Get your keys:**
- Supabase: [https://supabase.com/dashboard](https://supabase.com/dashboard) → Project Settings → API
- OpenRouter: [https://openrouter.ai/keys](https://openrouter.ai/keys)

### Database Setup

Run this SQL in your Supabase SQL Editor:

```sql
-- Profiles table
CREATE TABLE profiles (
  id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
  full_name TEXT,
  avatar_url TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Notes table
CREATE TABLE notes (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
  title TEXT NOT NULL DEFAULT 'Untitled',
  content TEXT DEFAULT '',
  tags TEXT[] DEFAULT '{}',
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Chat sessions
CREATE TABLE chat_sessions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
  title TEXT DEFAULT 'New Chat',
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Chat messages  
CREATE TABLE chat_messages (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  session_id UUID REFERENCES chat_sessions(id) ON DELETE CASCADE,
  role TEXT CHECK (role IN ('user', 'assistant', 'system')),
  content TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Goals
CREATE TABLE goals (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
  title TEXT NOT NULL,
  description TEXT,
  status TEXT DEFAULT 'active',
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Enable RLS
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE notes ENABLE ROW LEVEL SECURITY;
ALTER TABLE chat_sessions ENABLE ROW LEVEL SECURITY;
ALTER TABLE chat_messages ENABLE ROW LEVEL SECURITY;
ALTER TABLE goals ENABLE ROW LEVEL SECURITY;

-- Policies
CREATE POLICY "Users can view own profile" ON profiles FOR SELECT USING (auth.uid() = id);
CREATE POLICY "Users can view own notes" ON notes FOR SELECT USING (auth.uid() = user_id);
CREATE POLICY "Users can create own notes" ON notes FOR INSERT WITH CHECK (auth.uid() = user_id);
```

### Run Development Server

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000)

## 📂 Project Structure

```
cortex/
├── app/
│   ├── (auth)/          # Auth routes
│   ├── dashboard/       # Main dashboard
│   ├── chat/           # Chat interface
│   ├── notes/          # Notes manager
│   ├── api/
│   │   └── chat/       # AI chat endpoint
│   └── layout.tsx
├── components/
│   ├── chat-interface.tsx
│   ├── note-editor.tsx
│   ├── sidebar.tsx
│   └── ui/            # shadcn components
├── lib/
│   ├── supabase.ts    # Database client
│   ├── openrouter.ts  # AI integration  
│   └── store.ts       # State management
└── types/
    └── index.ts
```

## 🛠️ Tech Stack

- **Framework**: Next.js 14 (App Router)
- **Language**: TypeScript
- **Styling**: Tailwind CSS + shadcn/ui
- **Database**: Supabase (PostgreSQL)
- **Auth**: Supabase Auth
- **AI**: OpenRouter (Claude, GPT-4, Mistral)
- **State**: Zustand
- **Animation**: Framer Motion

## 📋 Roadmap

- [x] Basic chat interface
- [x] Note-taking system
- [ ] Markdown editor with AI suggestions
- [ ] Vector memory (Pinecone/Chroma)
- [ ] Weekly digest & reflections
- [ ] Templates & outlines system
- [ ] Web clipper extension
- [ ] Readwise integration
- [ ] Mobile app (React Native)

## 🤝 Contributing

Contributions welcome! Please read [CONTRIBUTING.md](CONTRIBUTING.md) first.

## 📄 License

MIT License - see [LICENSE](LICENSE) for details.

## 🙏 Acknowledgments

- Inspired by [Kortex](https://kortex.co) by Dan Koe
- Built with [Vercel AI SDK](https://sdk.vercel.ai)
- UI components from [shadcn/ui](https://ui.shadcn.com)

---

**Made with ❤️ by Synthreal AI**

Star ⭐ this repo if you find it helpful!
