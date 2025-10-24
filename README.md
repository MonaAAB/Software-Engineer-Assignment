# Full Stack Developer Assignment - StudyMate

> **Note:** The primary objective of this task is to evaluate your creativity and attention to detail when building such a system. We expect you to demonstrate a strong approach, as it is more important to us than the completion of the assignment.

---

## Overview

**StudyMate** is an AI-powered learning resource platform that allows students to browse through educational courses and materials, with AI assistance to help them understand and explore content.

---

## Requirements

### General
- Build a responsive web app
- The web app should contain two main tabs: **Home** and **AI Assistant**

### Home Tab

#### Search & Filter
- **Search functionality**: When a user types "Python", show courses whose `name` or `description` contain the word "python" (case-insensitive)
- **Filter by category**: Programming, Design, Business, Data Science
- **Category count cards**: Right below the search and filter bar, display cards showing the number of items in each category

#### Course Display
- Display courses as arranged cards in a responsive grid
- Each course card should include:
  - Course image
  - Title and description
  - Price, duration, and difficulty level
  - Instructor name
  - Rating and enrollment count
  - Category badge
  - **Interest counter** with increment/decrement buttons

#### Course Details
- On card click, show a modal/popup with full course details
- Modal should include all course information and larger image

### AI Assistant Tab

#### Chat Interface
- Create a chat interface where users can ask questions about courses
- Clear distinction between user and AI messages
- Display chat history with proper message styling

#### AI Capabilities
The AI should have access to the course catalog and be able to:
- Recommend courses based on user interests
- Answer questions about course content, difficulty, and duration
- Compare different courses
- Suggest learning paths

#### Features
- Message input field with send button
- Loading indicator for AI responses
- Scrollable chat history

### Optional Features
These are truly optional and will NOT negatively impact your evaluation if skipped:
- Dark mode toggle
- Voice input for AI chat
- Course bookmarking/favorites
- Chat history persistence
- Streaming AI responses
- Advanced filtering options
- Course context selector in AI chat

### Bonus Question
Develop a backend API endpoint to retrieve the **second-highest rated course per category** based on rating or enrollment count.

---

## 🛠️ Technical Stack

### Frontend
- **Framework**: React.js
- **Language**: TypeScript or JavaScript
- **Styling**: Your choice (CSS Modules, Styled Components, Tailwind, etc.)
- **Requirements**:
  - Responsive design for mobile, tablet, and desktop
  - Clean component structure
  - Proper state management

### Backend
- **Framework**: FastAPI or Flask (Python)
- **Requirements**:
  - RESTful API design
  - Proper error handling and validation
  - Request/response models with Pydantic
  - CORS configuration for frontend integration

### Database
- **Database**: PostgreSQL
- **Requirements**:
  - Efficient schema design
  - Proper indexes for performance
  - Foreign key relationships

### LLM Integration

You must integrate an LLM for the AI Assistant functionality. Choose the option that best fits your resources:

#### Option 1: Cloud-based LLM API (Recommended)
- OpenAI, Anthropic Claude, Google Gemini, or any similar service
- Configure via environment variables
- Simpler setup, faster development

#### Option 2: Local LLM with Ollama (Optional - Bonus Points)
- If you choose Ollama, include it as a separate Docker service in your `docker-compose.yml`
- Use a lightweight model (e.g., `llama3.2:3b`)
- Document setup instructions and model choice
- **Note**: Requires adequate RAM (8GB+) and model downloads (~5GB)

### DevOps

#### Docker Compose Requirements
The entire project must run using Docker Compose with the following services:
- **PostgreSQL** (database)
- **FastAPI** (backend API)
- **React** (frontend)
- **Ollama** (optional - only if using local LLM)

#### Environment Variables
- Use `.env` file for configuration
- Provide `.env.example` with all required variables
- Never commit sensitive data (API keys, passwords)

#### Setup Requirements
- Single command startup: `docker-compose up`
- Automatic database initialization with sample data
- Clear README with setup instructions

---

## 🗄️ Database Schema & Sample Data

### Overview

The database consists of two main tables:
- **courses** - Stores course catalog (20 sample courses included)
- **chat_history** - Stores AI chat conversations (optional)

### Schema Definition

**Table: courses**
```sql
CREATE TABLE courses (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    description TEXT NOT NULL,
    price NUMERIC(10, 2) NOT NULL,
    image VARCHAR(500) NOT NULL,
    duration INTEGER NOT NULL,              -- in hours
    difficulty VARCHAR(50) NOT NULL,        -- Beginner/Intermediate/Advanced
    category VARCHAR(100) NOT NULL,         -- Programming/Design/Business/Data Science
    instructor VARCHAR(255),
    enrollment_count INTEGER DEFAULT 0,
    rating NUMERIC(3, 2) DEFAULT 0.0,      -- 0.00 to 5.00
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Indexes for performance
CREATE INDEX idx_courses_category ON courses(category);
CREATE INDEX idx_courses_difficulty ON courses(difficulty);
CREATE INDEX idx_courses_rating ON courses(rating DESC);
CREATE INDEX idx_courses_enrollment ON courses(enrollment_count DESC);
```

**Table: chat_history** (Optional - for AI conversation persistence)
```sql
CREATE TABLE chat_history (
    id SERIAL PRIMARY KEY,
    user_id VARCHAR(255),
    message TEXT NOT NULL,
    role VARCHAR(20) NOT NULL CHECK (role IN ('user', 'assistant')),
    course_context INTEGER REFERENCES courses(id) ON DELETE SET NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Indexes
CREATE INDEX idx_chat_history_user ON chat_history(user_id);
CREATE INDEX idx_chat_history_created ON chat_history(created_at DESC);
```

### Sample Data

The assignment includes 20 pre-populated courses:

**Categories:**
- **Programming** (6 courses): Python, React, JavaScript, TypeScript, SQL, CSS
- **Design** (4 courses): UI/UX, Figma, Motion Graphics, Graphic Design
- **Data Science** (4 courses): Data Science, Machine Learning, Deep Learning, D3.js
- **Business** (4 courses): Marketing, Strategy, Entrepreneurship, Finance

**Example course:**
```json
{
  "name": "Python for Beginners",
  "description": "Learn Python programming from scratch. Cover fundamentals including variables, loops, functions, and basic data structures.",
  "price": 49.99,
  "duration": 20,
  "difficulty": "Beginner",
  "category": "Programming",
  "instructor": "Dr. Sarah Johnson",
  "enrollment_count": 1250,
  "rating": 4.7
}
```

Configure your `docker-compose.yml`:
```yaml
services:
  postgres:
    image: postgres:15-alpine
    container_name: studymate-postgres
    environment:
      POSTGRES_DB: studymate
      POSTGRES_USER: studymate
      POSTGRES_PASSWORD: studymate_pass
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./database/init.sql:/docker-entrypoint-initdb.d/init.sql
    ports:
      - "5432:5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U studymate"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:
```

### Verification

After setup, verify the database:
```sql
SELECT category, COUNT(*) as course_count, 
       ROUND(AVG(rating)::numeric, 2) as avg_rating
FROM courses 
GROUP BY category 
ORDER BY course_count DESC;
```

Expected output:
```
   category    | course_count | avg_rating 
---------------+--------------+------------
 Programming   |            6 |       4.72
 Design        |            4 |       4.60
 Data Science  |            4 |       4.88
 Business      |            4 |       4.53
```

---

## 🎨 UI/UX Design Reference

### Figma Design Files

**Access the complete UI mockups here:**
[Figma Design Link - https://www.figma.com/make/kgVJFGdew88v3CKWZrbChh/StudyMate-Platform-Design?node-id=0-4&t=4jX4ei640WmNhoBo-1]

### UI Design Overview

The following guidelines show the expected look and feel of the application. Your implementation should match the general layout and user experience while maintaining code quality and functionality.

<div align="center">
  <img src="https://github.com/user-attachments/assets/6b26022d-0361-4fee-b4bb-eaa26cfa3eaa" alt="Home Page - Course Catalog" width="975"/>
  <p><em>Figure 1: Home Page - Dark Mode</em></p>
</div>

<div align="center">
  <img src="https://github.com/user-attachments/assets/53e09fed-54b5-4dfb-98d9-811281fa4e35" alt="Home Page - Course Catalog" width="975"/>
  <p><em>Figure 2: Home Page - Light Mode</em></p>
</div>

<div align="center">
  <img src="https://github.com/user-attachments/assets/32cf4051-3adf-4330-a9c5-73aa6c6c3499" alt="Home Page - Course Catalog" width="975"/>
  <p><em>Figure 3: AI Assistant Page</em></p>
</div>

<div align="center">
  <img src="https://github.com/user-attachments/assets/d2b33a47-06bf-48d3-bc9a-d4dd5a647030" alt="Home Page - Course Catalog" width="975"/>
  <p><em>Figure 4: Course Detail Modal</em></p>
</div>
---

## 🔌 API Endpoints

Your backend should implement the following RESTful API endpoints:

### Courses

#### `GET /api/courses`
Get all courses with optional filtering and search
```
Query Parameters:
- search (optional): Search term for name/description
- category (optional): Filter by category
- difficulty (optional): Filter by difficulty level
- sort_by (optional): Sort field (rating, price, enrollment)
- order (optional): asc or desc

Response: 200 OK
[
  {
    "id": 1,
    "name": "Python for Beginners",
    "description": "Learn Python...",
    "price": 49.99,
    "image": "https://...",
    "duration": 20,
    "difficulty": "Beginner",
    "category": "Programming",
    "instructor": "Dr. Sarah Johnson",
    "enrollment_count": 1250,
    "rating": 4.7,
    "created_at": "2024-01-01T00:00:00Z"
  },
  ...
]
```

#### `GET /api/courses/{id}`
Get specific course details
```
Response: 200 OK
{
  "id": 1,
  "name": "Python for Beginners",
  ...
}

Response: 404 Not Found
{
  "detail": "Course not found"
}
```

#### `GET /api/courses/categories`
Get all categories with course counts
```
Response: 200 OK
{
  "categories": [
    {
      "name": "Programming",
      "count": 6,
      "avg_rating": 4.72
    },
    ...
  ]
}
```

#### `GET /api/courses/categories/{category}/second-highest` (Bonus)
Get second-highest rated course in a category
```
Response: 200 OK
{
  "id": 2,
  "name": "Advanced React Patterns",
  "rating": 4.8,
  ...
}
```

### AI Assistant

#### `POST /api/ai/chat`
Send message to AI assistant
```
Request Body:
{
  "message": "What programming courses do you recommend for beginners?",
  "user_id": "user123",  // optional
  "course_context": null  // optional: course ID for context
}

Response: 200 OK
{
  "message": "I recommend starting with 'Python for Beginners'...",
  "timestamp": "2024-01-01T12:00:00Z"
}

Response: 400 Bad Request
{
  "detail": "Message is required"
}

Response: 500 Internal Server Error
{
  "detail": "AI service unavailable"
}
```

#### `GET /api/ai/history/{user_id}` (Optional)
Get chat history for a user
```
Response: 200 OK
[
  {
    "id": 1,
    "message": "What programming courses...",
    "role": "user",
    "created_at": "2024-01-01T12:00:00Z"
  },
  {
    "id": 2,
    "message": "I recommend...",
    "role": "assistant",
    "created_at": "2024-01-01T12:00:05Z"
  }
]
```

### Course Interactions (Optional)

#### `POST /api/courses/{id}/increment-interest`
Increment interest counter
```
Response: 200 OK
{
  "id": 1,
  "interest_count": 5
}
```

#### `POST /api/courses/{id}/decrement-interest`
Decrement interest counter
```
Response: 200 OK
{
  "id": 1,
  "interest_count": 4
}
```
