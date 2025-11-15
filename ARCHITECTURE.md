# Job Application AI Agent System - V2 Architecture

## Project Overview

**Monorepo Strategy**: Building AI agent system (V2) on top of existing human agent system (V1)

**Core Objective**: End-to-end automated job application system with AI agents that can:
- Analyze resumes and understand candidates
- Search and match relevant jobs
- Generate tailored resumes and cover letters
- Submit applications (Easy Apply, forms, with human fallback)
- Track applications and provide feedback loops
- Generate reports and send notifications

---

## Tech Stack

### Backend
- **Framework**: Django REST Framework (DRF)
- **Database**: PostgreSQL
- **Task Queue**: Celery + Redis
- **AI/LLM**: Claude API (Anthropic)
- **Web Scraping**: Playwright (JS-heavy sites) + BeautifulSoup (static HTML)
- **Authentication**: JWT (djangorestframework-simplejwt)
- **File Processing**: PyPDF2, python-docx
- **Notifications**: SendGrid (email), Twilio (SMS)

### Frontend
- **Framework**: Next.js 14+ (App Router)
- **Language**: TypeScript
- **HTTP Client**: Axios
- **Data Fetching**: SWR
- **Styling**: Tailwind CSS
- **Forms**: Formik + Yup
- **Icons**: React Icons

### Infrastructure
- **Containerization**: Docker + Docker Compose
- **Web Server**: Nginx (reverse proxy)
- **WSGI Server**: Gunicorn
- **Process Manager**: Supervisor (for Celery workers)

---

## Project Structure

```
job-application-platform/
├── backend/
│   ├── apps/
│   │   ├── agents/                     # 🆕 AI Agent System (V2)
│   │   │   ├── models.py               # AgentTask, AgentLog, AgentConfig
│   │   │   ├── orchestrator.py         # Main coordinator
│   │   │   ├── resume_analyzer.py      # Agent 1: Resume analysis with Claude
│   │   │   ├── job_searcher.py         # Agent 2: Job searching (scraping)
│   │   │   ├── job_matcher.py          # Agent 3: Job matching with Claude
│   │   │   ├── doc_customizer.py       # Agent 4: Resume/Cover letter generator
│   │   │   ├── applicator.py           # Agent 5: Application submission
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   ├── urls.py
│   │   │   └── utils/
│   │   │       ├── claude_client.py    # Claude API wrapper
│   │   │       ├── easy_apply.py       # LinkedIn/Indeed Easy Apply
│   │   │       ├── form_filler.py      # Complex form automation
│   │   │       └── human_handoff.py    # Escalation logic
│   │   │
│   │   ├── scrapers/                   # 🆕 Web Scraping (V2)
│   │   │   ├── base_scraper.py         # Abstract base class
│   │   │   ├── linkedin_scraper.py     # LinkedIn job scraper
│   │   │   ├── indeed_scraper.py       # Indeed job scraper
│   │   │   ├── glassdoor_scraper.py    # Glassdoor job scraper
│   │   │   ├── company_scraper.py      # Direct company career pages
│   │   │   └── scraper_factory.py      # Factory pattern
│   │   │
│   │   ├── candidates/                 # ✅ Extended (V1 + V2)
│   │   │   ├── models.py               # User, Resume, JobPreferences
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   ├── validators.py           # 🆕 Validate complete info
│   │   │   └── urls.py
│   │   │
│   │   ├── jobs/                       # ✅ Extended (V1 + V2)
│   │   │   ├── models.py               # Job, Application, ApplicationLog
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   └── urls.py
│   │   │
│   │   ├── human_agents/               # ✅ Existing (V1)
│   │   │   ├── models.py               # HumanAgent, Assignment
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   ├── urls.py
│   │   │   └── escalation.py           # 🆕 Enhanced for AI handoff
│   │   │
│   │   ├── notifications/              # 🆕 New (V2)
│   │   │   ├── models.py               # Notification, NotificationPreference
│   │   │   ├── services/
│   │   │   │   ├── email_service.py    # SendGrid integration
│   │   │   │   ├── sms_service.py      # Twilio integration
│   │   │   │   └── push_service.py     # Push notifications
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   └── urls.py
│   │   │
│   │   ├── reports/                    # 🆕 New (V2)
│   │   │   ├── models.py               # WeeklyReport
│   │   │   ├── generator.py            # Report generation logic
│   │   │   ├── scheduler.py            # Celery periodic tasks
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   └── urls.py
│   │   │
│   │   ├── feedback/                   # 🆕 New (V2)
│   │   │   ├── models.py               # UserFeedback, PreferenceUpdate
│   │   │   ├── collector.py            # Feedback collection logic
│   │   │   ├── optimizer.py            # Update preferences with Claude
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   └── urls.py
│   │   │
│   │   ├── subscriptions/              # 🆕 New (V2)
│   │   │   ├── models.py               # Subscription, Plan
│   │   │   ├── middleware.py           # Check subscription status
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   └── urls.py
│   │   │
│   │   └── authentication/             # ✅ Existing (V1)
│   │       ├── models.py               # User, Role
│   │       ├── serializers.py
│   │       ├── views.py
│   │       └── urls.py
│   │
│   ├── core/
│   │   ├── settings/
│   │   │   ├── base.py                 # Base settings
│   │   │   ├── development.py          # Dev settings
│   │   │   └── production.py           # Prod settings
│   │   ├── feature_flags.py            # 🆕 Feature flag config
│   │   ├── urls.py                     # Main URL config
│   │   ├── wsgi.py
│   │   └── celery.py                   # Celery config
│   │
│   ├── tasks.py                        # Celery tasks
│   ├── manage.py
│   ├── requirements.txt
│   └── Dockerfile
│
├── frontend/
│   ├── src/
│   │   ├── app/                        # Next.js App Router
│   │   │   ├── layout.tsx              # Root layout
│   │   │   ├── page.tsx                # Landing page
│   │   │   ├── globals.css             # Tailwind imports
│   │   │   │
│   │   │   ├── (auth)/                 # Auth group
│   │   │   │   ├── login/
│   │   │   │   │   └── page.tsx
│   │   │   │   ├── register/
│   │   │   │   │   └── page.tsx
│   │   │   │   └── forgot-password/
│   │   │   │       └── page.tsx
│   │   │   │
│   │   │   ├── (candidate)/            # Candidate dashboard group
│   │   │   │   ├── layout.tsx          # Candidate layout
│   │   │   │   ├── dashboard/
│   │   │   │   │   └── page.tsx
│   │   │   │   ├── profile/
│   │   │   │   │   └── page.tsx
│   │   │   │   ├── applications/
│   │   │   │   │   ├── page.tsx
│   │   │   │   │   └── [id]/
│   │   │   │   │       └── page.tsx
│   │   │   │   ├── feedback/
│   │   │   │   │   └── page.tsx
│   │   │   │   ├── reports/
│   │   │   │   │   └── page.tsx
│   │   │   │   ├── notifications/
│   │   │   │   │   └── page.tsx
│   │   │   │   └── subscription/
│   │   │   │       └── page.tsx
│   │   │   │
│   │   │   └── (admin)/                # Admin dashboard group
│   │   │       ├── layout.tsx          # Admin layout
│   │   │       ├── dashboard/
│   │   │       │   └── page.tsx
│   │   │       ├── assignments/
│   │   │       │   └── page.tsx
│   │   │       ├── candidates/
│   │   │       │   └── page.tsx
│   │   │       └── ai-agents/
│   │   │           └── page.tsx
│   │   │
│   │   ├── components/
│   │   │   ├── layout/
│   │   │   │   ├── Navbar.tsx
│   │   │   │   ├── Sidebar.tsx
│   │   │   │   ├── Footer.tsx
│   │   │   │   └── DashboardLayout.tsx
│   │   │   │
│   │   │   ├── forms/                  # Formik forms
│   │   │   │   ├── LoginForm.tsx
│   │   │   │   ├── RegisterForm.tsx
│   │   │   │   ├── ProfileForm.tsx
│   │   │   │   ├── PreferencesForm.tsx
│   │   │   │   └── FeedbackForm.tsx
│   │   │   │
│   │   │   ├── dashboard/
│   │   │   │   ├── StatsCard.tsx
│   │   │   │   ├── ApplicationCard.tsx
│   │   │   │   ├── JobMatchCard.tsx
│   │   │   │   ├── ActivityFeed.tsx
│   │   │   │   └── AgentStatusCard.tsx
│   │   │   │
│   │   │   ├── notifications/
│   │   │   │   ├── NotificationBell.tsx
│   │   │   │   └── NotificationItem.tsx
│   │   │   │
│   │   │   └── common/
│   │   │       ├── Button.tsx
│   │   │       ├── Modal.tsx
│   │   │       ├── Spinner.tsx
│   │   │       ├── Alert.tsx
│   │   │       ├── Card.tsx
│   │   │       └── Table.tsx
│   │   │
│   │   ├── lib/
│   │   │   ├── axios.ts                # Axios instance config
│   │   │   ├── swr.ts                  # SWR config
│   │   │   └── validations/            # Yup schemas
│   │   │       ├── authSchemas.ts
│   │   │       ├── profileSchemas.ts
│   │   │       └── preferencesSchemas.ts
│   │   │
│   │   ├── hooks/
│   │   │   ├── useAuth.ts              # Auth hook with SWR
│   │   │   ├── useApplications.ts      # Fetch applications
│   │   │   ├── useJobs.ts              # Fetch job matches
│   │   │   ├── useNotifications.ts     # Real-time notifications
│   │   │   ├── useSubscription.ts      # Subscription status
│   │   │   └── useAgents.ts            # AI agent monitoring
│   │   │
│   │   ├── types/
│   │   │   ├── auth.ts
│   │   │   ├── candidate.ts
│   │   │   ├── job.ts
│   │   │   ├── application.ts
│   │   │   ├── agent.ts
│   │   │   └── index.ts
│   │   │
│   │   └── utils/
│   │       ├── constants.ts
│   │       ├── helpers.ts
│   │       └── formatters.ts
│   │
│   ├── public/
│   │   ├── images/
│   │   └── icons/
│   │
│   ├── tailwind.config.ts
│   ├── postcss.config.js
│   ├── next.config.js
│   ├── tsconfig.json
│   ├── package.json
│   └── Dockerfile
│
├── docker-compose.yml
├── .env.example
├── .gitignore
└── README.md
```

---

## Database Models (PostgreSQL)

### 1. Authentication & Users

```python
# apps/authentication/models.py
class User(AbstractUser):
    """Extended user model"""
    role = models.CharField(max_length=20, choices=[
        ('candidate', 'Candidate'),
        ('admin', 'Admin'),
        ('human_agent', 'Human Agent')
    ])
    phone = models.CharField(max_length=20, null=True, blank=True)
    avatar = models.ImageField(upload_to='avatars/', null=True, blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
```

### 2. Candidates

```python
# apps/candidates/models.py
class Resume(models.Model):
    """Candidate resume"""
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    file = models.FileField(upload_to='resumes/')
    parsed_data = models.JSONField(default=dict)  # Extracted by Agent 1
    skills = models.JSONField(default=list)
    experience = models.JSONField(default=list)
    education = models.JSONField(default=list)
    summary = models.TextField(blank=True)
    analyzed_at = models.DateTimeField(null=True, blank=True)
    completeness_score = models.IntegerField(default=0)  # 0-100
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

class JobPreferences(models.Model):
    """User job search preferences"""
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    job_titles = models.JSONField(default=list)  # ['Software Engineer', 'Backend Developer']
    locations = models.JSONField(default=list)  # ['Remote', 'New York', 'San Francisco']
    salary_min = models.IntegerField(null=True, blank=True)
    salary_max = models.IntegerField(null=True, blank=True)
    employment_types = models.JSONField(default=list)  # ['Full-time', 'Contract']
    experience_level = models.CharField(max_length=50, blank=True)  # 'Mid-level', 'Senior'
    industries = models.JSONField(default=list)
    company_size = models.JSONField(default=list)  # ['Startup', 'Enterprise']
    remote_preference = models.CharField(max_length=20, default='no_preference')
    willing_to_relocate = models.BooleanField(default=False)
    work_authorization = models.CharField(max_length=50, blank=True)
    optimized_preferences = models.JSONField(default=dict)  # Updated by feedback
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
```

### 3. Jobs & Applications

```python
# apps/jobs/models.py
class Job(models.Model):
    """Job posting"""
    title = models.CharField(max_length=255)
    company = models.CharField(max_length=255)
    description = models.TextField()
    requirements = models.TextField()
    location = models.CharField(max_length=255)
    salary_range = models.CharField(max_length=100, blank=True)
    employment_type = models.CharField(max_length=50)  # Full-time, Contract, etc.
    application_url = models.URLField()
    application_type = models.CharField(max_length=20, choices=[
        ('easy_apply', 'Easy Apply'),
        ('form', 'Form-based'),
        ('complex', 'Complex')
    ])
    source = models.CharField(max_length=50)  # LinkedIn, Indeed, etc.
    posted_date = models.DateField(null=True, blank=True)
    scraped_at = models.DateTimeField(auto_now_add=True)
    is_active = models.BooleanField(default=True)
    raw_data = models.JSONField(default=dict)  # Original scraped data

class Application(models.Model):
    """Job application"""
    candidate = models.ForeignKey(User, on_delete=models.CASCADE)
    job = models.ForeignKey(Job, on_delete=models.CASCADE)

    # Application method
    application_method = models.CharField(max_length=20, choices=[
        ('ai', 'AI Agent'),
        ('human', 'Human Agent'),
        ('manual', 'Manual')
    ], default='ai')

    # Status tracking
    status = models.CharField(max_length=50, choices=[
        ('pending', 'Pending'),
        ('searching', 'Searching Jobs'),
        ('matched', 'Job Matched'),
        ('preparing', 'Preparing Documents'),
        ('applying', 'Applying'),
        ('applied', 'Applied'),
        ('escalated', 'Escalated to Human'),
        ('interview', 'Interview Scheduled'),
        ('offer', 'Offer Received'),
        ('rejected', 'Rejected'),
        ('withdrawn', 'Withdrawn')
    ], default='pending')

    # AI agent tracking
    ai_agent_id = models.CharField(max_length=100, blank=True)
    match_score = models.FloatField(null=True, blank=True)  # 0-100

    # Human agent (if escalated)
    escalated_to_human = models.BooleanField(default=False)
    escalation_reason = models.TextField(blank=True)
    assigned_human_agent = models.ForeignKey(
        User,
        on_delete=models.SET_NULL,
        null=True,
        blank=True,
        related_name='assigned_applications'
    )

    # Documents
    customized_resume = models.FileField(upload_to='applications/resumes/', null=True)
    cover_letter = models.FileField(upload_to='applications/covers/', null=True)

    # Tracking
    applied_at = models.DateTimeField(null=True, blank=True)
    response_received_at = models.DateTimeField(null=True, blank=True)
    interview_date = models.DateTimeField(null=True, blank=True)

    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        unique_together = ('candidate', 'job')

class ApplicationLog(models.Model):
    """Detailed logging of application process"""
    application = models.ForeignKey(Application, on_delete=models.CASCADE, related_name='logs')
    timestamp = models.DateTimeField(auto_now_add=True)
    action = models.CharField(max_length=100)  # 'job_matched', 'resume_generated', etc.
    agent = models.CharField(max_length=100, blank=True)  # Which agent performed action
    details = models.JSONField(default=dict)
    success = models.BooleanField(default=True)
    error_message = models.TextField(blank=True)
```

### 4. AI Agents

```python
# apps/agents/models.py
class AgentTask(models.Model):
    """Tracks AI agent tasks"""
    task_id = models.UUIDField(default=uuid.uuid4, unique=True)
    candidate = models.ForeignKey(User, on_delete=models.CASCADE)
    agent_type = models.CharField(max_length=50, choices=[
        ('resume_analyzer', 'Resume Analyzer'),
        ('job_searcher', 'Job Searcher'),
        ('job_matcher', 'Job Matcher'),
        ('doc_customizer', 'Document Customizer'),
        ('applicator', 'Applicator')
    ])
    status = models.CharField(max_length=20, choices=[
        ('queued', 'Queued'),
        ('running', 'Running'),
        ('completed', 'Completed'),
        ('failed', 'Failed')
    ], default='queued')
    input_data = models.JSONField(default=dict)
    output_data = models.JSONField(default=dict)
    error_message = models.TextField(blank=True)
    started_at = models.DateTimeField(null=True, blank=True)
    completed_at = models.DateTimeField(null=True, blank=True)
    created_at = models.DateTimeField(auto_now_add=True)

class AgentConfig(models.Model):
    """Agent configuration"""
    agent_type = models.CharField(max_length=50, unique=True)
    is_enabled = models.BooleanField(default=True)
    config = models.JSONField(default=dict)  # Agent-specific settings
    updated_at = models.DateTimeField(auto_now=True)
```

### 5. Human Agents (Existing - V1)

```python
# apps/human_agents/models.py
class HumanAgent(models.Model):
    """Human agent profile"""
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    is_available = models.BooleanField(default=True)
    max_assignments = models.IntegerField(default=10)
    current_assignments = models.IntegerField(default=0)
    expertise = models.JSONField(default=list)  # Industries/roles they specialize in
    created_at = models.DateTimeField(auto_now_add=True)

class Assignment(models.Model):
    """Human agent assignment"""
    application = models.OneToOneField(Application, on_delete=models.CASCADE)
    human_agent = models.ForeignKey(HumanAgent, on_delete=models.CASCADE)
    assigned_at = models.DateTimeField(auto_now_add=True)
    completed_at = models.DateTimeField(null=True, blank=True)
    status = models.CharField(max_length=20, choices=[
        ('pending', 'Pending'),
        ('in_progress', 'In Progress'),
        ('completed', 'Completed')
    ], default='pending')
    notes = models.TextField(blank=True)
```

### 6. Feedback

```python
# apps/feedback/models.py
class UserFeedback(models.Model):
    """User feedback on job matches"""
    candidate = models.ForeignKey(User, on_delete=models.CASCADE)
    job = models.ForeignKey(Job, on_delete=models.CASCADE)
    application = models.ForeignKey(Application, on_delete=models.CASCADE, null=True)

    rating = models.IntegerField(choices=[(1, '👎'), (2, '👍')])  # Simple thumbs up/down
    feedback_type = models.CharField(max_length=50, choices=[
        ('too_junior', 'Too Junior'),
        ('too_senior', 'Too Senior'),
        ('wrong_location', 'Wrong Location'),
        ('low_salary', 'Salary Too Low'),
        ('wrong_industry', 'Wrong Industry'),
        ('good_match', 'Good Match'),
        ('other', 'Other')
    ])
    comment = models.TextField(blank=True)
    created_at = models.DateTimeField(auto_now_add=True)

class PreferenceUpdate(models.Model):
    """Track preference optimization"""
    candidate = models.ForeignKey(User, on_delete=models.CASCADE)
    old_preferences = models.JSONField()
    new_preferences = models.JSONField()
    feedback_analyzed = models.JSONField(default=list)  # Which feedback items were used
    updated_by = models.CharField(max_length=50, default='ai_optimizer')
    created_at = models.DateTimeField(auto_now_add=True)
```

### 7. Reports

```python
# apps/reports/models.py
class WeeklyReport(models.Model):
    """Weekly summary report"""
    candidate = models.ForeignKey(User, on_delete=models.CASCADE)
    week_start = models.DateField()
    week_end = models.DateField()

    # Stats
    applications_submitted = models.IntegerField(default=0)
    interviews_scheduled = models.IntegerField(default=0)
    offers_received = models.IntegerField(default=0)
    rejections = models.IntegerField(default=0)

    # Data
    top_matches = models.JSONField(default=list)
    upcoming_interviews = models.JSONField(default=list)
    next_steps = models.TextField(blank=True)

    generated_at = models.DateTimeField(auto_now_add=True)
    sent_at = models.DateTimeField(null=True, blank=True)
```

### 8. Notifications

```python
# apps/notifications/models.py
class Notification(models.Model):
    """User notification"""
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    type = models.CharField(max_length=50, choices=[
        ('interview', 'Interview Scheduled'),
        ('offer', 'Offer Received'),
        ('application_update', 'Application Update'),
        ('weekly_report', 'Weekly Report Ready'),
        ('feedback_request', 'Feedback Request'),
        ('subscription_expiring', 'Subscription Expiring')
    ])
    title = models.CharField(max_length=255)
    message = models.TextField()
    link = models.URLField(blank=True)

    # Channels
    sent_via_email = models.BooleanField(default=False)
    sent_via_sms = models.BooleanField(default=False)
    sent_via_push = models.BooleanField(default=False)

    is_read = models.BooleanField(default=False)
    read_at = models.DateTimeField(null=True, blank=True)
    created_at = models.DateTimeField(auto_now_add=True)

class NotificationPreference(models.Model):
    """User notification preferences"""
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    email_enabled = models.BooleanField(default=True)
    sms_enabled = models.BooleanField(default=False)
    push_enabled = models.BooleanField(default=True)
    notification_types = models.JSONField(default=dict)  # Per-type preferences
```

### 9. Subscriptions

```python
# apps/subscriptions/models.py
class Plan(models.Model):
    """Subscription plan"""
    name = models.CharField(max_length=50, unique=True)  # Free, Pro, Enterprise
    applications_per_month = models.IntegerField()  # -1 for unlimited
    price = models.DecimalField(max_digits=10, decimal_places=2)
    features = models.JSONField(default=list)
    is_active = models.BooleanField(default=True)

class Subscription(models.Model):
    """User subscription"""
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    plan = models.ForeignKey(Plan, on_delete=models.PROTECT)
    status = models.CharField(max_length=20, choices=[
        ('active', 'Active'),
        ('expired', 'Expired'),
        ('cancelled', 'Cancelled'),
        ('trial', 'Trial')
    ], default='active')
    applications_used = models.IntegerField(default=0)
    start_date = models.DateField()
    end_date = models.DateField()
    auto_renew = models.BooleanField(default=True)
    created_at = models.DateTimeField(auto_now_add=True)
```

---

## AI Agent Workflow

### Phase 1: Onboarding & Validation
1. User registers → Uploads resume + sets preferences
2. System validates completeness (validator checks all required fields)
3. If incomplete → Prompt user, block agent
4. If complete → Trigger Agent 1

### Phase 2: Resume Analysis (Agent 1)
**Resume Analyzer Agent** (`resume_analyzer.py`)
- Extract text from PDF/DOCX
- Parse with Claude API:
  - Skills (technical, soft)
  - Experience (companies, roles, years)
  - Education
  - Key achievements
- Store structured data in `Resume` model
- Calculate completeness score

**Claude Prompt Example:**
```
Analyze this resume and extract structured information:
- Skills (categorized: technical, soft, domain)
- Work experience (company, role, duration, achievements)
- Education
- Summary/objective

Resume text: {resume_text}
```

### Phase 3: Job Search & Matching (Agent 2 + 3)

**Job Searcher Agent** (`job_searcher.py`)
- Check subscription status (has quota?)
- Scrape job boards based on preferences:
  - LinkedIn: Playwright (requires login simulation)
  - Indeed: BeautifulSoup (simpler HTML)
  - Glassdoor: Hybrid approach
- For each job:
  - Extract details (title, company, description, requirements, URL)
  - Identify application type (Easy Apply / Form / Complex)
  - Store in `Job` model

**Job Matcher Agent** (`job_matcher.py`)
- Fetch candidate profile + preferences
- Use Claude API to score each job (0-100 match score)
- Apply feedback optimization (adjust weights based on previous feedback)
- Rank jobs by score
- Return top N matches (configurable, e.g., top 10)

**Claude Prompt Example:**
```
Score this job match for the candidate (0-100):

Candidate:
- Skills: {skills}
- Experience: {experience}
- Preferences: {preferences}
- Feedback history: {feedback_summary}

Job:
- Title: {job_title}
- Description: {job_description}
- Requirements: {job_requirements}

Provide:
1. Match score (0-100)
2. Reasoning
3. Pros/cons for candidate
```

### Phase 4: Document Customization (Agent 4)

**Document Customization Agent** (`doc_customizer.py`)
- For each matched job:
  - Generate tailored resume:
    - Highlight relevant skills
    - Emphasize matching experience
    - Reorder sections for relevance
  - Write custom cover letter:
    - Address specific requirements
    - Show enthusiasm for company/role
    - Explain why candidate is a fit
  - Export to PDF/DOCX
  - Store files in `Application` model

**Claude Prompt Example:**
```
Generate a tailored cover letter for this job application:

Candidate background: {resume_summary}
Job title: {job_title}
Company: {company}
Job requirements: {requirements}
Why candidate is interested: {user_notes}

Write a professional, enthusiastic cover letter (250-300 words) that:
1. Addresses key requirements
2. Highlights relevant experience
3. Shows genuine interest
```

### Phase 5: Application Submission (Agent 5)

**Applicator Agent** (`applicator.py`)

**Easy Apply** (LinkedIn, Indeed):
- Use Playwright to automate:
  - Navigate to job page
  - Click "Easy Apply"
  - Fill basic info (pulled from profile)
  - Upload resume
  - Submit
- Success → Mark as "applied"

**Form-based**:
- Playwright automation:
  - Fill form fields (name, email, phone, etc.)
  - Upload resume + cover letter
  - Answer standard questions (parsed from user data)
  - Submit
- Success → Mark as "applied"

**Complex** (Captcha, custom questions, multi-stage):
- Escalate to human agent:
  - Set `escalated_to_human = True`
  - Set `escalation_reason`
  - Notify admin via notification system
  - Wait for human completion

**Handoff Logic:**
```python
def should_escalate(application):
    if has_captcha(application.job.application_url):
        return True, "Captcha detected"
    if has_custom_questions(application.job):
        return True, "Custom questions require human input"
    if form_complexity_score(application.job) > 8:
        return True, "Form too complex"
    return False, None
```

### Phase 6: Tracking & Logging
- Every action logged to `ApplicationLog`
- Status updates in real-time
- Visible on candidate dashboard

### Phase 7: Monitoring & Feedback Loop

**Feedback Collection** (`feedback/collector.py`)
- After job match, prompt user for feedback (thumbs up/down)
- Collect reasons (too junior, wrong location, etc.)
- Store in `UserFeedback`

**Preference Optimizer** (`feedback/optimizer.py`)
- Periodic task (daily or weekly)
- Analyze feedback patterns with Claude:
  - If user thumbs down jobs in X location → reduce weight for X
  - If user thumbs down "too junior" → increase seniority filter
  - If user likes jobs at startups → boost startup preference
- Update `JobPreferences.optimized_preferences`
- Apply in future searches

**Claude Prompt Example:**
```
Analyze user feedback and optimize job preferences:

Current preferences: {current_preferences}
Feedback history (last 30 days): {feedback_list}

Based on patterns (e.g., user dislikes remote jobs, likes startups),
suggest updated preferences as JSON.
```

### Phase 8: Reporting & Notifications

**Weekly Report Generator** (`reports/generator.py`)
- Celery periodic task (every Monday 9am)
- Generate report:
  - Applications submitted (with links)
  - Interviews scheduled
  - Offers received
  - Next steps/recommendations
- Store in `WeeklyReport`
- Email to user

**Notification System** (`notifications/`)
- Real-time notifications for:
  - Interview scheduled → Immediate email + SMS
  - Offer received → Immediate email + SMS
  - Application update → In-app notification
  - Weekly report ready → Email
- Multi-channel support (email, SMS, push)

---

## API Endpoints (Django REST Framework)

### Authentication
- `POST /api/auth/register/` - Register new user
- `POST /api/auth/login/` - Login (returns JWT)
- `POST /api/auth/logout/` - Logout
- `POST /api/auth/refresh/` - Refresh JWT token
- `POST /api/auth/forgot-password/` - Password reset

### Candidates
- `GET /api/candidates/me/` - Get current user profile
- `PUT /api/candidates/me/` - Update profile
- `POST /api/candidates/resume/upload/` - Upload resume
- `GET /api/candidates/resume/` - Get resume details
- `GET /api/candidates/preferences/` - Get job preferences
- `PUT /api/candidates/preferences/` - Update job preferences

### Jobs
- `GET /api/jobs/` - List available jobs (paginated)
- `GET /api/jobs/{id}/` - Get job details
- `GET /api/jobs/search/` - Search jobs (filters: title, location, salary, etc.)

### Applications
- `GET /api/applications/` - List user's applications
- `GET /api/applications/{id}/` - Get application details
- `POST /api/applications/` - Create application (triggers agents)
- `PUT /api/applications/{id}/` - Update application
- `GET /api/applications/{id}/logs/` - Get application logs

### Agents
- `POST /api/agents/start/` - Start AI agent workflow for user
- `GET /api/agents/status/` - Get agent task status
- `GET /api/agents/tasks/` - List agent tasks
- `POST /api/agents/stop/` - Stop running agent task

### Feedback
- `POST /api/feedback/` - Submit feedback on job match
- `GET /api/feedback/` - Get user's feedback history

### Reports
- `GET /api/reports/` - List user's weekly reports
- `GET /api/reports/{id}/` - Get specific report
- `GET /api/reports/latest/` - Get latest report

### Notifications
- `GET /api/notifications/` - List notifications
- `PUT /api/notifications/{id}/read/` - Mark as read
- `PUT /api/notifications/read-all/` - Mark all as read
- `GET /api/notifications/preferences/` - Get preferences
- `PUT /api/notifications/preferences/` - Update preferences

### Subscriptions
- `GET /api/subscriptions/me/` - Get user's subscription
- `GET /api/subscriptions/plans/` - List available plans
- `POST /api/subscriptions/upgrade/` - Upgrade plan
- `POST /api/subscriptions/cancel/` - Cancel subscription

### Admin (Human Agents)
- `GET /api/admin/assignments/` - List all assignments
- `POST /api/admin/assignments/` - Assign application to human agent
- `GET /api/admin/agents/` - List human agents
- `GET /api/admin/candidates/` - List all candidates
- `GET /api/admin/ai-agents/status/` - Monitor AI agent performance

---

## Feature Flags

```python
# core/feature_flags.py
FEATURE_FLAGS = {
    'AI_AGENTS_ENABLED': True,
    'HUMAN_AGENTS_ENABLED': True,
    'EASY_APPLY_ENABLED': True,
    'FORM_FILL_ENABLED': True,
    'LINKEDIN_SCRAPER_ENABLED': True,
    'INDEED_SCRAPER_ENABLED': True,
    'GLASSDOOR_SCRAPER_ENABLED': True,
    'WEEKLY_REPORTS_ENABLED': True,
    'SMS_NOTIFICATIONS_ENABLED': False,  # Disabled until Twilio setup
    'FEEDBACK_OPTIMIZATION_ENABLED': True,
}
```

---

## Environment Variables

```bash
# .env.example

# Django
SECRET_KEY=your-secret-key
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/job_app_db

# Redis
REDIS_URL=redis://localhost:6379/0

# Celery
CELERY_BROKER_URL=redis://localhost:6379/0
CELERY_RESULT_BACKEND=redis://localhost:6379/0

# Claude API
ANTHROPIC_API_KEY=your-claude-api-key

# Email (SendGrid)
SENDGRID_API_KEY=your-sendgrid-key
FROM_EMAIL=noreply@yourapp.com

# SMS (Twilio)
TWILIO_ACCOUNT_SID=your-twilio-sid
TWILIO_AUTH_TOKEN=your-twilio-token
TWILIO_PHONE_NUMBER=your-twilio-number

# AWS S3 (for file storage)
AWS_ACCESS_KEY_ID=your-aws-key
AWS_SECRET_ACCESS_KEY=your-aws-secret
AWS_STORAGE_BUCKET_NAME=your-bucket-name
AWS_S3_REGION_NAME=us-east-1

# Frontend
NEXT_PUBLIC_API_URL=http://localhost:8000/api
```

---

## Implementation Phases

### Phase 1: Foundation (Weeks 1-2)
- ✅ Set up Django project structure
- ✅ Configure PostgreSQL database
- ✅ Create base models (User, Resume, Job, Application)
- ✅ Set up authentication (JWT)
- ✅ Configure Celery + Redis
- ✅ Set up Next.js frontend (App Router + TypeScript)
- ✅ Create base components (Layout, Navbar, etc.)

### Phase 2: Resume Analysis (Week 3)
- ✅ Implement Resume Analyzer Agent
- ✅ Integrate Claude API
- ✅ PDF/DOCX parsing
- ✅ API endpoints for resume upload
- ✅ Frontend: Resume upload UI

### Phase 3: Job Search (Weeks 4-5)
- ✅ Implement web scrapers (LinkedIn, Indeed, Glassdoor)
- ✅ Job Searcher Agent
- ✅ Job storage and management
- ✅ API endpoints for jobs
- ✅ Frontend: Job listing UI

### Phase 4: Job Matching (Week 6)
- ✅ Implement Job Matcher Agent
- ✅ Claude API integration for matching
- ✅ Scoring algorithm
- ✅ API endpoints for matches
- ✅ Frontend: Job match cards with scores

### Phase 5: Document Customization (Week 7)
- ✅ Implement Document Customizer Agent
- ✅ Resume tailoring logic
- ✅ Cover letter generation
- ✅ PDF/DOCX export
- ✅ API endpoints for documents
- ✅ Frontend: Document preview/download

### Phase 6: Application Submission (Weeks 8-9)
- ✅ Implement Applicator Agent
- ✅ Easy Apply automation (LinkedIn, Indeed)
- ✅ Form filling automation
- ✅ Human handoff logic
- ✅ API endpoints for application
- ✅ Frontend: Application tracking UI

### Phase 7: Tracking & Notifications (Week 10)
- ✅ Application logging system
- ✅ Notification system (email, SMS, push)
- ✅ Real-time status updates
- ✅ API endpoints for notifications
- ✅ Frontend: Notification center

### Phase 8: Feedback & Optimization (Week 11)
- ✅ Feedback collection system
- ✅ Preference optimizer with Claude
- ✅ API endpoints for feedback
- ✅ Frontend: Feedback UI

### Phase 9: Reporting (Week 12)
- ✅ Weekly report generator
- ✅ Celery periodic tasks
- ✅ Email delivery
- ✅ API endpoints for reports
- ✅ Frontend: Reports dashboard

### Phase 10: Admin & Human Agents (Week 13)
- ✅ Admin dashboard
- ✅ Assignment system
- ✅ Human agent interface
- ✅ API endpoints for admin
- ✅ Frontend: Admin UI

### Phase 11: Subscriptions (Week 14)
- ✅ Subscription system
- ✅ Usage tracking
- ✅ Payment integration (Stripe)
- ✅ API endpoints for subscriptions
- ✅ Frontend: Subscription management

### Phase 12: Testing & Deployment (Weeks 15-16)
- ✅ Unit tests (backend)
- ✅ Integration tests
- ✅ E2E tests (Playwright)
- ✅ Docker containerization
- ✅ CI/CD pipeline
- ✅ Production deployment

---

## Key Dependencies

### Backend (requirements.txt)
```
Django==4.2.7
djangorestframework==3.14.0
djangorestframework-simplejwt==5.3.0
psycopg2-binary==2.9.9
celery==5.3.4
redis==5.0.1
anthropic==0.7.0
playwright==1.40.0
beautifulsoup4==4.12.2
lxml==4.9.3
PyPDF2==3.0.1
python-docx==1.1.0
Pillow==10.1.0
sendgrid==6.11.0
twilio==8.10.0
boto3==1.29.7
gunicorn==21.2.0
python-decouple==3.8
```

### Frontend (package.json)
```json
{
  "dependencies": {
    "next": "^14.0.4",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "typescript": "^5.3.3",
    "@types/react": "^18.2.45",
    "@types/node": "^20.10.5",
    "axios": "^1.6.2",
    "swr": "^2.2.4",
    "formik": "^2.4.5",
    "yup": "^1.3.3",
    "react-icons": "^5.0.1",
    "tailwindcss": "^3.3.6",
    "autoprefixer": "^10.4.16",
    "postcss": "^8.4.32"
  }
}
```

---

## Next Steps

1. **Confirm architecture** - Review and approve
2. **Set up repository** - Initialize git repo
3. **Start Phase 1** - Begin with backend setup
4. **Create first agent** - Resume Analyzer
5. **Build iteratively** - One phase at a time

---

## Notes

- This is a **monorepo** approach building on top of V1
- All new features are **additive** (won't break existing V1)
- Use **feature flags** for gradual rollout
- Both AI and human agents can coexist
- Users can choose or system auto-decides based on complexity
- Eventually merge completely, but V2 can stand alone if needed
