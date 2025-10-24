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

#### AI Capabilities
The AI should have access to the course catalog and be able to:
- Recommend courses based on user interests
- Answer questions about course content, difficulty, and duration
- Compare different courses
- Suggest learning paths

#### Features
- Message input field with send button
- Loading indicator for AI responses

### Optional Features
These are truly optional and will NOT negatively impact your evaluation if skipped:
- Dark mode toggle
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
- We can provide test API keys if needed

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

### Sample Data Insert Commands

```sql
-- Sample course data inserts for StudyMate platform
-- 20 courses across 4 categories: Programming, Design, Data Science, Business

INSERT INTO courses (name, description, price, image, duration, difficulty, category, instructor, enrollment_count, rating) VALUES
('Python for Beginners', 'Learn Python programming from scratch. Cover fundamentals including variables, loops, functions, and basic data structures. Perfect for those new to programming.', 49.99, 'https://images.unsplash.com/photo-1526379095098-d400fd0bf935?w=400', 20, 'Beginner', 'Programming', 'Dr. Sarah Johnson', 1250, 4.7),

('Advanced React Patterns', 'Master advanced React concepts including hooks, context, performance optimization, and modern design patterns. Build scalable applications with confidence.', 89.99, 'https://images.unsplash.com/photo-1633356122544-f134324a6cee?w=400', 35, 'Advanced', 'Programming', 'Michael Chen', 890, 4.8),

('UI/UX Design Fundamentals', 'Comprehensive introduction to user interface and experience design. Learn design principles, wireframing, prototyping, and user research methodologies.', 69.99, 'https://images.unsplash.com/photo-1561070791-2526d30994b5?w=400', 25, 'Beginner', 'Design', 'Emma Rodriguez', 2100, 4.6),

('Data Science with Python', 'Explore data analysis, visualization, and machine learning using Python. Work with pandas, numpy, matplotlib, and scikit-learn on real-world datasets.', 99.99, 'https://images.unsplash.com/photo-1551288049-bebda4e38f71?w=400', 45, 'Intermediate', 'Data Science', 'Prof. James Wilson', 1580, 4.9),

('Digital Marketing Mastery', 'Complete guide to digital marketing including SEO, social media marketing, content strategy, email marketing, and analytics. Grow your online presence.', 79.99, 'https://images.unsplash.com/photo-1460925895917-afdab827c52f?w=400', 30, 'Beginner', 'Business', 'Lisa Anderson', 3200, 4.5),

('JavaScript Deep Dive', 'Go beyond basics to understand JavaScript at a fundamental level. Explore closures, prototypes, async programming, and modern ES6+ features.', 74.99, 'https://images.unsplash.com/photo-1627398242454-45a1465c2479?w=400', 28, 'Intermediate', 'Programming', 'Alex Thompson', 1670, 4.7),

('Figma for Product Design', 'Master Figma from basics to advanced techniques. Create professional designs, build component systems, prototype interactions, and collaborate effectively.', 59.99, 'https://images.unsplash.com/photo-1609921212029-bb5a28e60960?w=400', 22, 'Intermediate', 'Design', 'Sophie Turner', 1940, 4.8),

('SQL Database Design', 'Learn relational database design, normalization, complex queries, performance optimization, and best practices for building efficient database systems.', 64.99, 'https://images.unsplash.com/photo-1544383835-bda2bc66a55d?w=400', 26, 'Intermediate', 'Programming', 'Dr. Robert Martinez', 1120, 4.6),

('Machine Learning A-Z', 'Comprehensive machine learning course covering supervised and unsupervised learning, neural networks, natural language processing, and computer vision.', 129.99, 'https://images.unsplash.com/photo-1555255707-c07966088b7b?w=400', 60, 'Advanced', 'Data Science', 'Dr. Amanda Foster', 2300, 4.9),

('Business Strategy & Planning', 'Develop strategic thinking skills. Learn market analysis, competitive strategy, business model innovation, and strategic planning frameworks.', 84.99, 'https://images.unsplash.com/photo-1454165804606-c3d57bc86b40?w=400', 32, 'Intermediate', 'Business', 'John Davidson', 1450, 4.4),

('CSS Grid & Flexbox', 'Master modern CSS layout techniques. Build responsive layouts with Grid and Flexbox. Learn best practices for maintainable stylesheets.', 44.99, 'https://images.unsplash.com/photo-1523437113738-bbd3cc89fb19?w=400', 15, 'Beginner', 'Programming', 'Maria Garcia', 2800, 4.7),

('Motion Graphics in After Effects', 'Create stunning animations and motion graphics. Learn keyframing, expressions, effects, and professional workflow for video production.', 94.99, 'https://images.unsplash.com/photo-1574717024653-61fd2cf4d44d?w=400', 38, 'Intermediate', 'Design', 'Chris Williams', 1230, 4.6),

('Data Visualization with D3.js', 'Create interactive, dynamic data visualizations for the web. Master D3.js fundamentals, SVG manipulation, and advanced charting techniques.', 79.99, 'https://images.unsplash.com/photo-1551288049-bebda4e38f71?w=400', 30, 'Advanced', 'Data Science', 'Dr. Nina Patel', 890, 4.8),

('Entrepreneurship Essentials', 'Start your business journey. Learn ideation, validation, business planning, fundraising, and launch strategies from successful entrepreneurs.', 69.99, 'https://images.unsplash.com/photo-1507679799987-c73779587ccf?w=400', 28, 'Beginner', 'Business', 'Mark Stevens', 2650, 4.5),

('Node.js Backend Development', 'Build scalable backend applications with Node.js. Cover Express, databases, authentication, RESTful APIs, and deployment strategies.', 89.99, 'https://images.unsplash.com/photo-1558494949-ef010cbdcc31?w=400', 40, 'Intermediate', 'Programming', 'David Kim', 1890, 4.8),

('Graphic Design Principles', 'Foundations of visual communication. Learn typography, color theory, composition, branding, and create professional designs from concept to completion.', 64.99, 'https://images.unsplash.com/photo-1626785774573-4b799315345d?w=400', 24, 'Beginner', 'Design', 'Rachel Green', 3100, 4.6),

('Deep Learning with TensorFlow', 'Advanced neural networks with TensorFlow. Build CNNs, RNNs, GANs, and transformers. Deploy production-ready deep learning models.', 139.99, 'https://images.unsplash.com/photo-1677442136019-21780ecad995?w=400', 55, 'Advanced', 'Data Science', 'Prof. Li Wei', 1340, 4.9),

('Financial Analysis for Managers', 'Essential financial skills for decision-makers. Understand financial statements, budgeting, forecasting, and investment analysis.', 94.99, 'https://images.unsplash.com/photo-1554224311-beee4ece6c1f?w=400', 35, 'Intermediate', 'Business', 'Jennifer Walsh', 1760, 4.7),

('TypeScript Masterclass', 'Complete TypeScript guide from basics to advanced types. Build type-safe applications with confidence and leverage TypeScript full power.', 69.99, 'https://images.unsplash.com/photo-1587620962725-abab7fe55159?w=400', 26, 'Intermediate', 'Programming', 'Thomas Anderson', 2150, 4.8),

('3D Design with Blender', 'Create stunning 3D models and animations. Learn modeling, texturing, lighting, and rendering. Perfect for beginners and intermediate users.', 84.99, 'https://images.unsplash.com/photo-1618556450991-2f1af64e8191?w=400', 42, 'Beginner', 'Design', 'Marcus Johnson', 1420, 4.5);
```

**Verification Query:**
```sql
SELECT 
    category, 
    COUNT(*) as course_count,
    ROUND(AVG(rating)::numeric, 2) as avg_rating,
    ROUND(AVG(price)::numeric, 2) as avg_price
FROM courses 
GROUP BY category 
ORDER BY course_count DESC;
```

**Expected Output:**
```
   category    | course_count | avg_rating | avg_price 
---------------+--------------+------------+-----------
 Programming   |            6 |       4.72 |     71.16
 Design        |            5 |       4.62 |     74.79
 Data Science  |            4 |       4.88 |    102.49
 Business      |            4 |       4.53 |     82.24
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
