# CreditGuide AI – System Design Document

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
