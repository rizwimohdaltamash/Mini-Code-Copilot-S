# Mini Code Copilot

AI-powered code generation application with a modern React frontend and Node.js backend. Features multi-language support, light/dark themes, favorites system, and smart search functionality.

## 🚀 Features

### Core Functionality
- **🤖 AI Code Generation**: Powered by Groq AI API
- **💻 Multi-Language Support**: 12+ programming languages
- **⭐ Favorites System**: Star important code generations
- **🔍 Smart Search**: Filter history by prompt or code content
- **📋 Code Management**: Copy, view, and organize generated code

### UI/UX
- **🌓 Light/Dark Theme**: Toggle with persistent preference
- **🎨 Modern Design**: Clean, professional interface with smooth animations
- **📱 Responsive**: Works seamlessly on all screen sizes
- **⚠️ Input Validation**: Warns about non-coding questions
- **⌨️ Keyboard Shortcuts**: Ctrl+Enter to generate code

## 📁 Project Structure

```
mini-copilot/
├── backend/                    # Node.js + Express API
│   ├── prisma/
│   │   ├── schema.prisma      # Database schema
│   │   ├── migrations/        # Database migrations
│   │   └── seed.js            # Seed data
│   ├── index.js               # Main server file
│   ├── vercel.json            # Vercel deployment config
│   └── package.json
│
├── frontend/                   # React + TypeScript
│   ├── src/
│   │   ├── components/        # Reusable components
│   │   ├── context/           # React contexts (Theme)
│   │   ├── pages/             # Generator & History pages
│   │   ├── types/             # TypeScript interfaces
│   │   ├── api.ts             # API client
│   │   └── index.css          # Global styles & themes
│   ├── vercel.json            # Vercel deployment config
│   └── package.json
│
└── README.md                   # This file
```

## 🗄️ Database Schema

### Entity-Relationship Model

```
┌─────────────────┐         ┌──────────────────────┐
│   Language      │         │    Generation        │
├─────────────────┤         ├──────────────────────┤
│ 🔑 id (PK)      │────┐    │ 🔑 id (PK)           │
│ name (UNIQUE)   │    └───→│ 🔗 languageId (FK)   │
└─────────────────┘         │ prompt               │
        1                   │ code                 │
                            │ createdAt            │
                            │ starred              │
                            └──────────────────────┘
                                      N
```

### Schema Details

**Language Table:**
- `id` (INT, PK, Auto-increment): Unique identifier
- `name` (VARCHAR, UNIQUE): Programming language name

**Generation Table:**
- `id` (INT, PK, Auto-increment): Unique identifier
- `prompt` (TEXT, NOT NULL): User's coding question
- `code` (TEXT, NOT NULL): Generated code
- `createdAt` (TIMESTAMP, DEFAULT NOW): Creation timestamp
- `starred` (BOOLEAN, DEFAULT FALSE): Favorite status
- `languageId` (INT, FK, NOT NULL): References Language.id

**Normalization:** 3rd Normal Form (3NF)
- Eliminates data redundancy
- Prevents update anomalies
- Maintains referential integrity

### ER Diagram (dbdiagram.io format)

```dbml
Table Language {
  id int [pk, increment]
  name varchar [unique, not null]
  Note: 'Stores programming language information'
}

Table Generation {
  id int [pk, increment]
  prompt text [not null]
  code text [not null]
  createdAt timestamp [default: `now()`, not null]
  starred boolean [default: false, not null]
  languageId int [not null]
  Note: 'Stores generated code with metadata'
}

Ref: Generation.languageId > Language.id [delete: cascade]
```

## ⚡ Performance Analysis

### Time Complexity of Paginated Retrieval

**Current Query:**
```javascript
await prisma.generation.findMany({
  where: { /* search filters */ },
  skip: (page - 1) * limit,
  take: limit,
  orderBy: [
    { starred: "desc" },
    { id: "desc" }
  ],
  include: { language: true }
});
```

**Time Complexity: O(n log n + k)**
- **O(n log n)**: Sorting all generations by `starred` and `id` without indexes
- **O(k)**: Fetching k items per page (k = 10)
- **With indexes**: O(log n + k) - significantly faster

**With Search Filter:**
- **Without indexes**: O(n) - full table scan
- **With GIN indexes**: O(log n) - logarithmic search

### Schema Impact on Performance

**Strengths:**
- ✅ Foreign key on `languageId` (implicit index)
- ✅ Primary keys auto-indexed
- ✅ Unique constraint on `Language.name` (indexed)
- ✅ Normalized design prevents data duplication

**Bottlenecks:**
- ❌ No index on `starred` column (sorting requires full scan)
- ❌ No index on `createdAt` (secondary sort is inefficient)
- ❌ No full-text search indexes (search scans entire columns)

**Flexibility:**
- ✅ Easy to extend with new fields
- ✅ Normalized design supports complex queries
- ✅ One-to-many relationship is efficient
- ⚠️ Case-insensitive search requires full scan

### Index Recommendations

**When indexes are useful:**
1. Columns in `WHERE` clauses (frequent filtering)
2. Columns in `ORDER BY` clauses (sorting)
3. Columns in `JOIN` operations
4. Tables with >1000 rows
5. Read-heavy operations

**Recommended Indexes:**

```prisma
model Generation {
  // ... existing fields ...
  
  @@index([starred, id])        // Composite for sorting
  @@index([createdAt])          // Date-based queries
  @@index([prompt], type: Gin)  // Full-text search
}
```

**Performance Impact:**

| Query Type | Without Index | With Index | Improvement |
|------------|--------------|------------|-------------|
| Paginated retrieval | O(n log n) | O(log n + k) | ~100x faster |
| Search by starred | O(n) | O(log n) | ~1000x faster |
| Text search | O(n) | O(log n) | ~100x faster |
| Date range queries | O(n) | O(log n) | ~1000x faster |

**Current Status:**
- ❌ No custom indexes implemented
- ✅ Only default PK and FK indexes
- ⚠️ Performance degrades with >1000 records

## 🛠️ Installation & Setup

### Prerequisites
- Node.js (v16+)
- PostgreSQL database
- Groq API key

### Backend Setup

1. **Navigate to backend directory**
   ```bash
   cd backend
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Configure environment variables**
   
   Create `.env` file:
   ```env
   DATABASE_URL="postgresql://username:password@host:port/database"
   GROQ_API_KEY="your_groq_api_key"
   PORT=5000
   ```

4. **Run database migrations**
   ```bash
   npx prisma migrate dev
   npx prisma generate
   ```

5. **Seed the database (optional)**
   ```bash
   node prisma/seed.js
   ```

6. **Start the server**
   ```bash
   npm start
   # or for development
   nodemon index.js
   ```

   Server runs on `http://localhost:5000`

### Frontend Setup

1. **Navigate to frontend directory**
   ```bash
   cd frontend
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Configure API endpoint**
   
   Edit `src/api.ts`:
   ```typescript
   const API_BASE = "http://localhost:5000";
   // or for production:
   // const API_BASE = "https://your-backend.onrender.com";
   ```

4. **Start development server**
   ```bash
   npm run dev
   ```

   App runs on `http://localhost:5173`

## 📡 API Endpoints

### POST /api/generate
Generate code based on prompt and language.

**Request:**
```json
{
  "prompt": "Create a function to reverse a string",
  "language": "javascript"
}
```

**Response:**
```json
{
  "id": 1,
  "prompt": "Create a function to reverse a string",
  "code": "function reverseString(str) { ... }",
  "createdAt": "2025-11-22T10:30:00.000Z",
  "starred": false,
  "languageId": 1,
  "language": {
    "id": 1,
    "name": "javascript"
  }
}
```

### GET /api/history
Retrieve paginated history with optional search.

**Query Parameters:**
- `page` (optional): Page number (default: 1)
- `search` (optional): Search query

**Example:**
```
GET /api/history?page=1&search=function
```

### PATCH /api/generation/:id/star
Toggle star/favorite status.

**Request:**
```json
{
  "starred": true
}
```

## 🚀 Deployment

### Backend Deployment (Render)

1. **Push to GitHub**
   ```bash
   git add .
   git commit -m "Deploy backend"
   git push origin main
   ```

2. **Create Web Service on Render**
   - Connect GitHub repository
   - Build Command: `npm install`
   - Start Command: `node index.js`

3. **Set Environment Variables**
   - `DATABASE_URL`: PostgreSQL connection string
   - `GROQ_API_KEY`: Your Groq API key

4. **Deploy**
   - Render auto-deploys on push to main

**Live Backend:** `https://mini-code-copilot-backend-1.onrender.com`

### Frontend Deployment (Vercel)

1. **Update API URL**
   
   Edit `frontend/src/api.ts`:
   ```typescript
   const API_BASE = "https://mini-code-copilot-backend-1.onrender.com";
   ```

2. **Deploy to Vercel**
   ```bash
   cd frontend
   vercel --prod
   ```

3. **Or use Vercel Dashboard**
   - Import GitHub repository
   - Build Command: `npm run build`
   - Output Directory: `dist`

## 🔧 Technologies Used

### Backend
- **Node.js** - Runtime environment
- **Express.js** - Web framework
- **Prisma** - ORM for database operations
- **PostgreSQL** - Relational database
- **Groq SDK** - AI code generation
- **dotenv** - Environment variable management
- **CORS** - Cross-origin resource sharing

### Frontend
- **React 18** - UI library
- **TypeScript** - Type safety
- **Vite** - Build tool and dev server
- **React Router** - Client-side routing
- **Tailwind CSS** - Utility-first CSS framework
- **CSS Custom Properties** - Dynamic theming

## 🎯 Usage

### Generating Code

1. Select a programming language from the dropdown
2. Enter your coding prompt
3. Click "✨ Generate Code" or press `Ctrl+Enter`
4. View the generated code
5. Click "☆ Star" to mark as favorite (optional)
6. Click "📋 Copy" to copy code to clipboard

### Viewing History

1. Navigate to the **History** page
2. Use the search bar to filter by prompt or code
3. Click on any card to view full details
4. Toggle stars to mark/unmark favorites
5. Starred items automatically appear at the top

### Theme Switching

- Click the sun/moon icon in the navbar
- Theme preference is saved automatically
- All pages adapt to the selected theme

## 🐛 Troubleshooting

### Backend Issues

**Database Connection Fails:**
```bash
# Test connection
npx prisma db pull

# Ensure DATABASE_URL includes ?sslmode=require for production
```

**Migration Errors:**
```bash
# Reset database (WARNING: deletes all data)
npx prisma migrate reset

# Or push schema without migration
npx prisma db push
```

### Frontend Issues

**API Connection Errors:**
- Check if backend is running
- Verify API_BASE URL in `src/api.ts`
- Check CORS configuration in backend

**Build Errors:**
```bash
# Clear cache and reinstall
rm -rf node_modules package-lock.json
npm install
```

## 📊 Key Features Explained

### Star/Favorite System
- Mark important code generations with a star
- Starred items automatically appear at the top of history
- Star status persists in the database
- Visual feedback with yellow star icon

### Search Functionality
- Search through both prompts and code content
- Case-insensitive search
- Real-time filtering
- Resets to page 1 on new search

### Theme System
- Light and dark mode support
- Persistent preference in localStorage
- Smooth transitions between themes
- Theme-aware components throughout

## 📝 License

MIT

## 👨‍💻 Author

Mohd. Altamash Rizwi

---

**Live Demo:**
- Backend: https://mini-code-copilot-backend-1.onrender.com
- Frontend: Deploy to see your live app!

For detailed deployment guides, see `DEPLOYMENT.md` and `RENDER_DEPLOYMENT.md`.
#
