# CreditGuide AI – System Design Document- Manish Bansal & Atul Bansal

## 1. Overview
CreditGuide AI is a dual‑side platform that improves MSME loan readiness and supports bankers in credit assessment. It uses **industry‑aware, explainable AI** to provide guidance without automating credit decisions. The system is built on AWS cloud for scalability, security, and cost‑efficiency.

## 2. High‑Level Architecture
![Architecture Diagram](docs/images/architecture.png)  
*Placeholder: actual diagram should be added to `docs/images/architecture.png`.*

The platform follows a **serverless microservices architecture**:



User (Web/Mobile) → AWS Amplify (Frontend) → Amazon API Gateway → AWS Lambda → AI Layer (SageMaker/Bedrock) → Amazon RDS / S3



All services are secured via **Amazon Cognito**, **IAM roles**, and **AWS KMS** for encryption.

## 3. Component Details

### 3.1 Frontend
- **Technology:** React.js, Tailwind CSS, AWS Amplify (hosting, auth, CI/CD)
- **Responsibilities:**
  - Guided forms for MSMEs (business details, cash flow, etc.)
  - Dashboard for bankers (risk flags, mitigation options, report generation)
  - Responsive design for mobile & desktop
  - Progressive Web App (PWA) capabilities for offline access

### 3.2 API Layer
- **Technology:** Amazon API Gateway (RESTful APIs) + AWS Lambda (Python)
- **Responsibilities:**
  - Handle user requests from frontend
  - Validate input, route to appropriate microservices
  - Trigger AI processing
  - Return explainable outputs

### 3.3 AI/ML Layer
- **Services:**
  - **Amazon SageMaker:** Train/fine‑tune industry‑specific models using retired bankers’ expertise.
  - **AWS Bedrock:** Integrate with foundation models (e.g., Llama 2, Mistral) for natural language explanations.
  - **Amazon Comprehend:** Extract key entities from MSME descriptions.
  - **Amazon Translate:** Multi‑language support (Hindi, Tamil, etc.)
- **Responsibilities:**
  - Analyze MSME inputs against industry‑specific patterns
  - Identify risks, strengths, and mitigation pathways
  - Generate plain‑language explanations for both MSMEs and bankers

### 3.4 Database Layer
- **Amazon RDS (PostgreSQL):** Store user profiles, assessment history, anonymized MSME data.
- **Amazon DynamoDB:** Cache session data, real‑time progress tracking.
- **Amazon S3:** Store uploaded documents (GST returns, financial statements), generated reports.

### 3.5 Security & Compliance
- **Amazon Cognito:** User sign‑up/sign‑in, multi‑factor authentication.
- **AWS IAM:** Fine‑grained permissions for services.
- **AWS KMS:** Encryption at rest for sensitive data.
- **AWS WAF:** Protect against common web exploits.
- **Data anonymization:** All training data is anonymized; PII is never stored with business data.

## 4. Data Flow (End‑to‑End)

1. **MSME User** opens the app → logs in via Cognito.
2. **Fills guided form** (industry, turnover, cash flow, etc.) → frontend sends data to API Gateway.
3. **API Gateway** triggers a **Lambda function** (e.g., `process_msme_input`).
4. **Lambda** pre‑processes data, calls **SageMaker endpoint** for pattern analysis and **Bedrock** for explanation generation.
5. **Results** (strengths, gaps, suggestions) are stored in **RDS** and returned to frontend.
6. **Banker** logs in, views assigned assessments → similar flow to fetch and update data.
7. **Final report** can be generated (PDF) and stored in **S3**, with a link sent back to the user.

## 5. Technology Stack Summary

| Layer          | Technologies                                                                 |
|----------------|------------------------------------------------------------------------------|
| Frontend       | React.js, AWS Amplify, Tailwind CSS, PWA                                     |
| Backend        | AWS Lambda (Python/FastAPI), API Gateway, Node.js (optional)                 |
| AI/ML          | Amazon SageMaker, AWS Bedrock, Hugging Face Transformers, Amazon Comprehend  |
| Database       | Amazon RDS (PostgreSQL), DynamoDB, Amazon S3                                 |
| Security       | Amazon Cognito, IAM, KMS, WAF                                                |
| DevOps         | AWS CloudFormation, GitHub Actions, CloudWatch, X-Ray                        |

## 6. Key Design Decisions

| Decision | Rationale |
|----------|-----------|
| **Serverless first** | Pay‑per‑use, auto‑scaling, reduced operational overhead. |
| **Separate AI layer** | Decouples business logic from AI; easier to update models independently. |
| **Industry‑specific templates** | Instead of one‑size‑fits‑all, we embed banking expertise per sector. |
| **Explainability over automation** | AI never approves/rejects; it only provides reasoning (compliance, trust). |
| **Multi‑language ready** | To serve Bharat’s diverse MSMEs; uses Amazon Translate for quick integration. |
| **Data anonymization** | Required for using real banker insights while protecting privacy. |

## 7. Scalability & Performance

- **Lambda concurrency** handles spikes automatically.
- **RDS read replicas** for heavy reporting queries.
- **SageMaker multi‑model endpoints** to serve multiple industry models efficiently.
- **CloudFront CDN** for static assets (frontend).
- **ElastiCache** (Redis) for session caching (planned for future).

## 8. Security Considerations

- All API endpoints are authenticated via Cognito JWT tokens.
- Sensitive data encrypted at rest (KMS) and in transit (TLS).
- Least‑privilege IAM roles for each Lambda function.
- Regular security audits via AWS Inspector.

## 9. Future Enhancements

- Integration with **Account Aggregator** framework for direct financial data fetch.
- WhatsApp/Telegram chatbot interface for low‑literacy MSMEs.
- Voice‑based input (using Amazon Transcribe) for vernacular users.
- Dashboard for banks to track portfolio‑level trends.
# CreditGuide AI - System Design Document

## 1. System Overview

CreditGuide AI is a dual-sided platform that helps MSMEs prepare loan proposals and supports bankers with structured credit assessment. The system uses industry-aware AI models trained on retired banker expertise, with all decisions being explainable and assistive (not automated).

## 2. High-Level Architecture



┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Client Layer  │────▶│   API Layer     │────▶│   Service Layer │
│  React on AWS   │     │  API Gateway +  │     │   Microservices │
│    Amplify      │     │    Lambda       │     │                 │
└─────────────────┘     └─────────────────┘     └────────┬────────┘
│                        │                        │
▼                        ▼                        ▼
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Auth Layer    │     │   AI Layer      │     │   Data Layer    │
│  Amazon Cognito │     │ SageMaker +     │     │  RDS + S3       │
│                 │     │   Bedrock       │     │                 │
└─────────────────┘     └─────────────────┘     └─────────────────┘



## 3. Component Details

### 3.1 Frontend Components

| Component | Technology | Responsibility |
|-----------|------------|----------------|
| MSME Onboarding | React + Tailwind | Step-by-step business data collection |
| Readiness Dashboard | React + Chart.js | Visual display of strengths/gaps |
| Banker Assessment View | React | Risk flags + mitigation suggestions |
| Report Generator | React + PDF library | Generate bank-ready summaries |

**State Management:** React Context API + LocalStorage for offline capability [citation:3]

### 3.2 Backend Services (AWS Lambda)

**Service: User Management**
- Handles registration, profile updates
- Integrates with Cognito for auth
- Environment: Python 3.10+

**Service: Business Analysis**
- Processes MSME business data
- Calls industry-specific SageMaker endpoints
- Response time target: < 3 seconds

**Service: Banker Assessment**
- Retrieves MSME data and risk analysis
- Formats for banker dashboard
- Generates report drafts

**Service: Report Generation**
- Creates PDF summaries
- Stores in S3 with presigned URLs
- Email delivery via SES

### 3.3 AI/ML Components

**Industry-Aware Models (Amazon SageMaker)**
- Separate fine-tuned models per industry:
  - Manufacturing model
  - Trading model
  - Services model
  - Agri-based model
- Each trained on anonymized banking assessment data
- Regular retraining with feedback loop

**Explanation Engine (AWS Bedrock)**
- Uses foundation models (Llama 2, Mistral) to generate plain-language explanations
- Prompt template:


You are a senior banker explaining credit assessment to:

· A small business owner (for MSME view)
· A junior banker (for banker view)

Based on this analysis: {analysis_results}

Provide clear, actionable guidance in simple language.



**Pattern Recognition**
- Identifies common risk patterns (buyer concentration, seasonality)
- Maps to mitigation strategies from banking knowledge base

### 3.4 Database Design

**Amazon RDS PostgreSQL Schema**

sql
-- Users table (anonymized)
CREATE TABLE users (
    user_id UUID PRIMARY KEY,
    user_type VARCHAR(20), -- 'msme' or 'banker'
    created_at TIMESTAMP,
    region VARCHAR(50)
);

-- MSME profiles
CREATE TABLE msme_profiles (
    profile_id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(user_id),
    business_name VARCHAR(200),
    industry VARCHAR(50),
    years_operation INTEGER,
    annual_turnover DECIMAL,
    employee_count INTEGER,
    gst_status VARCHAR(20),
    created_at TIMESTAMP,
    updated_at TIMESTAMP
);

-- Assessment results
CREATE TABLE assessments (
    assessment_id UUID PRIMARY KEY,
    profile_id UUID REFERENCES msme_profiles(profile_id),
    strengths JSONB,
    gaps JSONB,
    suggestions JSONB,
    created_at TIMESTAMP
);

-- Banker notes
CREATE TABLE banker_notes (
    note_id UUID PRIMARY KEY,
    assessment_id UUID REFERENCES assessments(assessment_id),
    banker_id UUID REFERENCES users(user_id),
    note_text TEXT,
    created_at TIMESTAMP
);


Amazon S3 Storage

· Uploaded documents (GST returns, financial statements)
· Generated reports (PDF)
· Training data (anonymized)

3.5 Security Architecture


┌─────────────────────────────────────┐
│         Internet                    │
└───────────────┬─────────────────────┘
                │
┌───────────────▼─────────────────────┐
│      AWS WAF + CloudFront           │
└───────────────┬─────────────────────┘
                │
┌───────────────▼─────────────────────┐
│      Application Load Balancer      │
└───────────────┬─────────────────────┘
                │
┌───────────────▼─────────────────────┐
│    API Gateway (with Auth)          │
│    - JWT validation via Cognito     │
│    - Rate limiting                  │
└───────────────┬─────────────────────┘
                │
┌───────────────▼─────────────────────┐
│    Lambda Functions (IAM Roles)     │
│    - Least privilege permissions    │
│    - Environment variables encrypted│
└─────────────────────────────────────┘


4. Data Flow Diagrams

4.1 MSME Assessment Flow


┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  MSME    │───▶│  Lambda  │───▶│SageMaker │───▶│   RDS   │
│  Input   │    │ Process  │    │  Analyze │    │  Store   │
└──────────┘    └──────────┘    └──────────┘    └────┬─────┘
                                                       │
┌──────────┐    ┌──────────┐    ┌──────────┐           │
│  Display │◀───│  Lambda  │◀───│  Bedrock │◀────────┘
│   UI     │    │  Format  │    │ Explain  │
└──────────┘    └──────────┘    └──────────┘

Sequence:

1. MSME submits business details via React form
2. API Gateway triggers process-msme-input Lambda
3. Lambda calls appropriate SageMaker endpoint (based on industry)
4. SageMaker returns pattern analysis (strengths, gaps)
5. Lambda stores results in RDS
6. Lambda calls Bedrock with analysis to generate explanations
7. Bedrock returns plain-language guidance
8. Lambda formats response and returns to frontend
9. UI displays results with color coding (green/orange/blue)

4.2 Banker Assessment Flow


┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│ Banker   │───▶│  Lambda  │───▶│   RDS    │───▶│  Format  │
│ Selects  │    │  Fetch   │    │  Query   │    │ Response │
│ MSME     │    │          │    │          │    │          │
└──────────┘    └──────────┘    └──────────┘    └────┬─────┘
                                                      │
┌──────────┐    ┌──────────┐    ┌──────────┐         │
│  Display │◀───│  Lambda  │◀───│  Bedrock │◀────────┘
│ Dashboard│    │ Generate │    │  Create  │
│          │    │   Note   │    │ Narrative│
└──────────┘    └──────────┘    └──────────┘


5. API Specifications

5.1 MSME Endpoints

Endpoint Method Description Request Body Response
/api/msme/profile POST Create MSME profile Business details Profile ID
/api/msme/assess POST Run loan readiness assessment Profile ID Assessment results
/api/msme/report GET Download readiness report Assessment ID PDF download URL

5.2 Banker Endpoints

Endpoint Method Description Request Body Response
/api/banker/assessments GET List pending assessments - Assessment list
/api/banker/assessment/{id} GET Get detailed assessment - Full assessment with risks
/api/banker/note POST Save banker note Note content Success status

6. Implementation Plan

Phase 1: MVP (Weeks 1-2)

· Set up AWS Amplify + Cognito
· Create basic React forms for MSME onboarding
· Implement one industry model (manufacturing) in SageMaker
· Build simple assessment display

Phase 2: Core Features (Weeks 3-4)

· Add all industry models
· Implement Bedrock explanation generation
· Create banker dashboard
· Add report generation (PDF)

Phase 3: Polish (Weeks 5-6)

· Multi-language support (Amazon Translate)
· Performance optimization
· Security hardening
· User testing and feedback integration

7. Deployment Architecture


┌─────────────────────────────────────────────────────┐
│                  AWS Cloud                           │
│                                                      │
│  ┌─────────────┐    ┌─────────────┐                 │
│  │  Amplify    │    │  CloudFront │                 │
│  │  (Frontend) │    │   (CDN)     │                 │
│  └─────────────┘    └─────────────┘                 │
│         │                   │                        │
│         └───────────────────┘                        │
│                                                      │
│  ┌─────────────────────────────────┐                │
│  │      API Gateway                 │                │
│  └─────────────────────────────────┘                │
│              │                                       │
│  ┌───────────┴───────────┐                          │
│  │                      │                           │
│  ▼                      ▼                           │
│  ┌─────────────┐    ┌─────────────┐                 │
│  │   Lambda    │    │   Lambda    │                 │
│  │  (MSME)     │    │  (Banker)   │                 │
│  └─────────────┘    └─────────────┘                 │
│        │                    │                        │
│        └──────────┬─────────┘                        │
│                   │                                  │
│  ┌────────────────▼─────────────────┐               │
│  │         SageMaker + Bedrock       │               │
│  └────────────────┬─────────────────┘               │
│                   │                                  │
│  ┌────────────────▼─────────────────┐               │
│  │         RDS + S3                  │               │
│  └───────────────────────────────────┘               │
│                                                      │
└─────────────────────────────────────────────────────┘


8. Monitoring and Observability

Amazon CloudWatch

· Lambda invocation metrics
· API Gateway latency
· SageMaker inference times
· Custom dashboards for business metrics

AWS X-Ray

· Trace end-to-end requests
· Identify bottlenecks
· Debug errors

CloudWatch Logs

· Structured logging from all services
· Error alerting
· Audit trail for compliance


9. Testing Strategy

Unit Tests

· Jest for React components
· Pytest for Lambda functions
· 80%+ coverage target

Integration Tests

· API endpoint testing
· Database integration
· AWS service mocking

End-to-End Tests

· Cypress for critical user flows
· MSME journey: registration → assessment → report
· Banker journey: login → view → note

User Acceptance Testing

· Pilot with 5-10 MSMEs
· Feedback from 2-3 junior bankers
· Iterate based on feedback

10. Conclusion

This design provides a scalable, secure, and explainable platform for MSME credit guidance. By leveraging AWS managed services, we can build rapidly while ensuring production readiness. The industry-aware AI layer, trained on real banking expertise, is the key differentiator that will make CreditGuide AI valuable for both MSMEs and bankers.
# CreditGuide AI - Implementation Tasks

## Phase 1: Foundation (Week 1)

### Task 1.1: Project Setup
- [ ] Initialize React project with Vite
- [ ] Configure Tailwind CSS
- [ ] Set up folder structure
- [ ] Initialize Git repository

### Task 1.2: AWS Amplify Configuration
- [ ] Create Amplify app
- [ ] Configure build settings
- [ ] Set up CI/CD pipeline
- [ ] Configure custom domain

### Task 1.3: Cognito Authentication
- [ ] Create user pool
- [ ] Configure app client
- [ ] Implement login/signup UI
- [ ] Add multi-factor authentication for bankers


Alternative: Using MCP Server for Spec-Driven Development

If you prefer a more automated approach, you can use the MCP server for spec-driven development :

1. Configure the MCP server in your project:

json
{
    "servers": {
        "spec-driven": {
            "command": "npx",
            "args": [
                "-y",
                "mcp-server-kiro-spec-driven-development@latest"
            ]
        }
    }
}



