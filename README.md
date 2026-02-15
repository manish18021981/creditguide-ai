# creditguide-ai
AI-powered platform for MSME loan readiness and banker assesment support
*AI-Powered Industry-Aware Credit Readiness & Assessment Support Platform*

[![AWS](https://img.shields.io/badge/Powered%20by-AWS-orange)](https://aws.amazon.com)
[![Hackathon](https://img.shields.io/badge/AI%20for%20Bharat-Hackathon-blue)](https://aiforbharat.com)

## 📌 Problem Statement
MSMEs contribute 30% to India's GDP but face high loan rejection rates due to poor proposal clarity. Junior bankers lack structured, industry-aware guidance for credit assessment.

## 🚀 Solution
CreditGuide AI is a dual-sided platform that:
- Prepares MSMEs with guided inputs and gap analysis
- Empowers bankers with structured prompts and explainable outputs
- Uses *industry-aware AI* trained on retired bankers' expertise

## 🛠️ Tech Stack
- *Frontend:* React.js, AWS Amplify
- *Backend:* AWS Lambda, API Gateway, Python (FastAPI)
- *AI/ML:* Amazon SageMaker, AWS Bedrock, Hugging Face
- *Database:* Amazon RDS (PostgreSQL), S3
- *Security:* Amazon Cognito, AWS KMS

## 📂 Repository Structure
- *Frontend:* React.js, AWS Amplify
- *Backend:* AWS Lambda, API Gateway, Python (FastAPI)

- ## 1. User Stories

### MSME Users

**US-1: Business Profile Creation**
WHEN a new MSME user registers on the platform
THE SYSTEM SHALL guide them through a step-by-step business profile creation process
- Acceptance Criteria:
  - Collects business name, industry type, years in operation
  - Collects annual turnover, employee count, GST status
  - Saves progress automatically after each step

**US-2: Loan Readiness Assessment**
WHEN an MSME user submits their business details
THE SYSTEM SHALL analyze their profile against industry-specific banking criteria
- Acceptance Criteria:
  - Identifies strengths (stable operations, GST compliance)
  - Flags potential gaps (seasonal cash flow, buyer concentration)
  - Provides plain-language improvement suggestions
  - Response time < 5 seconds

**US-3: Industry-Specific Guidance**
WHEN an MSME user selects their industry (manufacturing/trading/services/agri)
THE SYSTEM SHALL tailor all recommendations to that industry
- Acceptance Criteria:
  - Manufacturing: focuses on machinery utilization, raw material cycles
  - Trading: highlights receivables concentration, thin margins
  - Services: emphasizes recurring revenue, client concentration
  - Agri-based: accounts for seasonal crop cycles, weather dependencies

### Banker Users

**US-4: Credit Assessment Dashboard**
WHEN a banker logs in and selects an MSME application
THE SYSTEM SHALL display a structured assessment view
- Acceptance Criteria:
  - Shows MSME summary (business details, loan amount)
  - Lists risk flags with explanations
  - Provides mitigation suggestions
  - Auto-generates banker's note for loan file

**US-5: Risk Mitigation Guidance**
WHEN a banker identifies a risk factor in an application
THE SYSTEM SHALL suggest appropriate mitigation strategies
- Acceptance Criteria:
  - For high buyer concentration: suggests requesting diversification plan
  - For thin margins: suggests collateral or guarantor options
  - For seasonal cash flow: suggests repayment structuring
  - All suggestions are based on real banking practices

## 2. Functional Requirements

**FR-1: User Authentication**
WHEN a user attempts to access the platform
THE SYSTEM SHALL authenticate them via secure login
- Support email/password and Google login
- Implement multi-factor authentication for bankers
- Session timeout after 30 minutes of inactivity

**FR-2: Industry-Aware AI Engine**
WHEN the platform receives business data
THE SYSTEM SHALL apply industry-specific analysis rules
- Maintain separate logic for 5+ industry verticals
- Update rules based on retired banker expertise
- Log all analysis decisions for explainability

**FR-3: Report Generation**
WHEN a user requests a loan readiness report
THE SYSTEM SHALL generate a downloadable PDF
- Include strengths, gaps, and recommendations
- Format for bank submission readiness
- Available in English (expandable to regional languages)

## 3. Non-Functional Requirements

**NFR-1: Performance**
WHEN 100 concurrent users access the platform
THE SYSTEM SHALL maintain response times under 3 seconds
- API response time < 200ms
- AI analysis completes within 5 seconds
- 99.9% uptime during business hours

**NFR-2: Security**
WHEN storing user data
THE SYSTEM SHALL encrypt all sensitive information
- PII encrypted at rest using AWS KMS
- All API traffic over TLS 1.2+
- Regular security audits

**NFR-3: Scalability**
WHEN user base grows to 10,000+
THE SYSTEM SHALL scale horizontally without degradation
- Auto-scaling Lambda functions
- RDS read replicas for reporting
- CloudFront CDN for static assets

**NFR-4: Explainability**
WHEN presenting AI-generated recommendations
THE SYSTEM SHALL provide clear reasoning for each suggestion
- No black-box decisions
- All outputs traceable to banking logic
- Human judgment always remains final


