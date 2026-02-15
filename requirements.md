# ArthSaathi: Project Requirements

## 1. Executive Summary
ArthSaathi is an AI-powered financial clarity assistant designed to democratize financial literacy for the average Indian citizen. By leveraging advanced Large Language Models (LLMs) and Optical Character Recognition (OCR), it translates complex financial documents, policies, and processes into simple, vernacular language. The platform empowers users to make informed decisions without selling products or managing assets, acting purely as an unbiased informational guide.

## 2. Problem Statement
The modern financial ecosystem is opaque and intimidating for the average person. Complex jargon, lengthy policy documents, and convoluted application processes create a barrier to financial inclusion.
- **Information Asymmetry**: Users often buy insurance or loans without understanding the fine print.
- **Process Paralysis**: Complex paperwork for claims, nominations, or account openings leads to procrastination or abandonment.
- **Lack of Unbiased Advice**: Most "advisors" are incentivized salespeople, leaving a gap for trustworthy, neutral guidance.

## 3. Product Vision
To become the "Google Translate for Finance" — a ubiquitous, trusted companion that decodes the financial world for every citizen, ensuring no one signs a document they don't understand or fears a process they need to navigate.

## 4. Scope

### In Scope
- **Goal-Based Guidance**: Conversational interface for financial queries (Investments, Loans, Banking).
- **Document Analysis**: Upload and summarization of insurance policies and financial documents.
- **Process Navigator**: Step-by-step guides for banking and legal financial processes.
- **Multilingual Support**: (Implicit in vision, MVP focuses on English intermixed with common vernacular concepts).
- **Spending Clarity**: Basic analysis of pasted spending data (MVP).

### Out of Scope
- **Transaction Execution**: No money movement or payment processing.
- **Financial Product Sales**: No direct selling of insurance, mutual funds, or loans.
- **Account Management**: No direct integration with bank account APIs (Account Aggregator) for MVP.
- **Legal/Tax Advice**: The AI provides general information, not binding legal or certified tax advice.

## 5. Target Users
- **The Aspiring Middle Class**: Individuals with disposable income but low financial literacy.
- **Senior Citizens**: Users finding it difficult to keep up with digital banking and new financial rules.
- **First-Time Earners**: Young professionals navigating salaries, taxes, and investments for the first time.
- **Rural/Semi-Urban Users**: Users intimidated by formal banking language.

## 6. User Personas

### Persona 1: "Careful Chandra" (45, Small Business Owner)
- **Goal**: Wants to buy health insurance for his family but is terrified of "hidden clauses" and claim rejections.
- **Pain Point**: Cannot understand the 40-page policy document the agent sent him.
- **Needs**: A tool to upload the PDF and get a "Yes/No" on whether specific diseases are covered.

### Persona 2: "Rookie Rahul" (23, Software Engineer)
- **Goal**: Needs to save tax and start investing.
- **Pain Point**: Overwhelmed by jargon like "ELSS", "PPF", "Sharpe Ratio".
- **Needs**: Simple, conversational guidance on where to start without being sold a specific plan.

### Persona 3: "Retired Gita" (68, Retired Teacher)
- **Goal**: Needs to transfer her late husband's bank assets to her name.
- **Pain Point**: Confused by the bank's requirements for "Succession Certificate" vs. "Nominee".
- **Needs**: A clear checklist of documents and a sequence of steps to visit the branch.

## 7. User Stories

| ID | As a... | I want to... | So that... |
|----|---------|--------------|------------|
| US-1 | User | enter a financial goal like "I want to buy a car" | I can get a structured roadmap of financial steps to achieve it. |
| US-2 | User | upload an insurance policy PDF | I can immediately see the "exclusions" and "waiting periods" in simple English. |
| US-3 | User | ask "What happens if I miss a premium?" | I can understand the consequences without reading the whole booklet. |
| US-4 | User | get a checklist for "Death Claim Settlement" | I know exactly what papers to gather before going to the bank. |
| US-5 | User | ask follow-up questions to the AI's advice | I can clear doubts instantly as they arise. |
| US-6 | User | have the AI explain "Compounding" | I can understand the concept with a relatable real-world example. |
| US-7 | User | see a summary of spending "traps" | I can avoid common financial mistakes people like me make. |
| US-8 | User | receive responses in non-technical language | I don't feel intimidated by the advice. |
| US-9 | User | save my chat history | I can refer back to the advice later. |
| US-10 | Mobile User | access the tool on my phone browser | I can use it while waiting at a bank or insurance office. |

## 8. Functional Requirements

### 1. Conversational Interface
- FR-1.1: System shall provide a chat interface for user input.
- FR-1.2: System shall support free-text input and suggested prompt buttons.
- FR-1.3: System shall maintain context within a session.

### 2. Document Processing
- FR-2.1: System shall accept PDF and Image uploads (up to 10MB).
- FR-2.2: System shall extract text from uploaded documents using OCR.
- FR-2.3: System shall identify key sections (Coverage, Exclusions, Terms) automatically.

### 3. Financial Guidance Engine
- FR-3.1: System shall classify user intent into pre-defined categories (Guidance, Explanation, Process).
- FR-3.2: System shall output step-by-step instructions for procedural queries.
- FR-3.3: System shall append a standard financial disclaimer to all advice.

### 4. Authentication (Optional for MVP)
- FR-4.1: System shall allow guest access for basic queries.
- FR-4.2: System shall allow email/OTP login for saving history.

## 9. Non-Functional Requirements

### Performance
- **Latency**: AI responses should be generated within 5 seconds for text queries.
- **OCR Speed**: Document analysis should complete within 30 seconds for a standard 10-page policy.

### Scalability
- **Concurrency**: Must support 100+ concurrent users for the demo.
- **Serverless**: Architecture must scale to zero when not in use to minimize costs.

### Privacy
- **Data retention**: Uploaded documents should be deleted immediately after processing or stored ephemerally (TTL 1 hour).
- **PII Redaction**: AI should attempt to redact names/PAN numbers before sending to LLM (Best effort).

### Reliability
- **Availability**: 99.9% uptime during the hackathon period.
- **Error Handling**: Graceful degradation (e.g., "I cannot read this document") instead of crashing.

## 10. Compliance & Regulatory Considerations
- **Disclaimer Enforcement**: Every response must clearly state "This is not financial advice. Consult a professional."
- **Data Localization**: Preference for AWS India regions (Mumbai) for data processing.
- **No SFD**: We do not store sensitive financial data like credit card numbers or passwords.

## 11. Assumptions
- Users have access to a smartphone with internet/data.
- Scanned documents uploaded are legible (at least 200 DPI).
- AWS Service Quotas for Bedrock/Textract are sufficient for the demo traffic.

## 12. Constraints
- **Budget**: Using AWS Free Tier or Hackathon credits; limited GPU resources.
- **Time**: MVP must be built within the hackathon timeframe (e.g., 24-48 hours).
- **Language**: Primary focus is English; local language support relies on LLM capabilities.

## 13. Risks & Mitigation
- **Risk**: Hallucinations (AI inventing clauses).
  - *Mitigation*: Use high RAG capabilities, strict system prompts, and forcing the AI to cite the document page number.
- **Risk**: Cost Overrun on Bedrock.
  - *Mitigation*: Implement strict token limits and caching for frequent queries.

## 14. Success Metrics (for Hackathon Demo)
- **Analysis Accuracy**: 80%+ accuracy in extracting "Waiting Period" clauses from test policies.
- **User Engagement**: Average session length > 3 minutes.
- **Clarity Score**: User rating of "Helpfulness" > 4/5 in demo feedback.
