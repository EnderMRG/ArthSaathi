# ArthSaathi: Technical Design Document

## 1. System Overview
ArthSaathi is a serverless, event-driven application built on AWS. It uses a React-based frontend for user interaction and an AWS Lambda backend to orchestrate calls between Amazon Bedrock (for AI reasoning) and Amazon Textract (for document processing). The system is designed to be stateless, scalable, and cost-effective.

## 2. High-Level Architecture
The architecture follows the standard AWS Serverless pattern:
1. **Client**: Next.js Web App hosting on Vercel or AWS Amplify.
2. **Entry Point**: API Gateway (REST API) to route requests.
3. **Compute**: AWS Lambda (Node.js/Python) to handle business logic.
4. **AI/ML Layer**: 
   - **Amazon Bedrock**: Access to Claude 3 Sonnet / Haiku for reasoning.
   - **Amazon Textract**: For extracting text from uploaded PDFs/Images.
5. **Storage**:
   - **S3**: Ephemeral storage for uploaded documents.
   - **DynamoDB**: Storing session history and user context.

## 3. Component Breakdown

### 3.1 Frontend (React / Next.js)
- **Framework**: Next.js (App Router).
- **Styling**: Tailwind CSS for rapid UI development.
- **State Management**: React Context or Zustand for chat history.
- **Key Components**:
  - `ChatInterface`: Main conversational UI.
  - `FileUpload`: Drag-and-drop zone for policies.
  - `ResponseRenderer`: Markdown renderer for AI answers.

### 3.2 API Layer (AWS API Gateway)
- **Type**: REST API.
- **Auth**: API Keys (for demo) or Cognito Authorizer (production).
- **Routes**:
  - `POST /chat`: Send user message, get AI response.
  - `POST /upload`: Get pre-signed S3 URL for file upload.
  - `POST /analyze`: Trigger document analysis after upload.

### 3.3 AI Layer (Amazon Bedrock)
- **Model**: Anthropic Claude 3 Sonnet (for complex reasoning) and Haiku (for simple chats).
- **Role**:
  - Intent classification.
  - Summarization of Textract output.
  - Generation of structured financial guidance.

### 3.4 Document Processing (Amazon Textract)
- **Feature**: `DetectDocumentText` API.
- **Flow**: Triggered by Lambda when a file is mapped to a chat session.
- **Post-Processing**: Lambda sanitizes raw OCR text before sending to Bedrock.

### 3.5 Data Storage
- **Amazon S3**: 
  - Bucket: `arthsaathi-verified-uploads`.
  - Lifecycle Policy: Delete objects after 1 hour (Privacy).
- **Amazon DynamoDB**:
  - Table: `ArthSaathiSessions`.
  - PK: `SessionID`, SK: `Timestamp`.
  - Attributes: `UserQuery`, `AIResponse`, `ContextVector` (optional).

## 4. Detailed Data Flow

### Scenario: Policy Analysis
1. **User** uploads `policy.pdf` via Frontend.
2. **Frontend** requests pre-signed URL from `POST /upload`.
3. **Frontend** uploads file directly to S3.
4. **Frontend** calls `POST /analyze` with `fileKey`.
5. **Lambda** receives request, calls **Textract** to read `fileKey`.
6. **Textract** returns raw text strings.
7. **Lambda** constructs a prompt: *"Here is the raw text of an insurance policy. Summarize exclusions and coverage... [Raw Text]"*.
8. **Bedrock** processes prompt and returns simplified markdown.
9. **Lambda** sends markdown to Frontend.
10. **Frontend** renders the explanation.

## 5. AI Reasoning Flow

### 5.1 Intent Extraction
- **Input**: "How do I invest 5000 rupees?"
- **Classifier Prompt**: "Classify intent: [Investment, Insurance, Loan, Process, General]. If uncertain, ask clarifying question."
- **Output**: Intent: `Investment`.

### 5.2 Clarification Layer
- If intent is vague (e.g., "I want a loan"), AI asks:
  - "Personal or Home loan?"
  - "What is your income range?"
- This "Clarification Loop" continues until required 3-4 data points are gathered.

### 5.3 Structured Prompting (Example)
```text
System: You are ArthSaathi, a helpful financial guide.
Context: User wants to open a PPF account.
Task: Provide a step-by-step guide.
Tone: Simple, encouraging, 8th-grade reading level.
Output Format: Markdown list with 'Key Documents' section.
```

## 6. Prompt Engineering Strategy
- **Chain of Thought (CoT)**: Ask the model to "Explain the financial concept first, then apply it to the user's case."
- **Role-Playing**: "Act as a kind grandmother explaining finance."
- **Constraint Enforcement**: "Do not mention specific brand names (e.g., don't say 'Buy HDFC Life', say 'Look for Term Insurance')."

## 7. Error Handling Strategy
- **Textract Failure**: If PDF is blurry, return "I couldn't read the document clearly. Please upload a structured digital PDF."
- **Bedrock Timeout**: Implement exponential backoff in Lambda.
- **Content Policy Violation**: If user asks for illegal financial advice (e.g., tax evasion), AI responds with a hardcoded refusal message.

## 8. Security Architecture
- **In-Transit**: HTTPS (TLS 1.2) for all API calls.
- **At-Rest**: S3 and DynamoDB encrypted with AWS KMS.
- **Input Validation**: Joi/Zod validation in Lambda to prevent SQLi/Injection.
- **Least Privilege**: Lambda execution roles have specific permissions (e.g., `s3:GetObject` only on specific bucket).

## 9. Scalability Strategy
- **Serverless**: Lambda and API Gateway auto-scale with traffic.
- **Quotas**: Request limit increases for Bedrock (TPM/RPM) before demo.
- **Throttling**: Set API Gateway usage plans to prevent abuse.

## 10. Data Privacy Strategy
- **No Persistence of PII**: Documents are transient.
- **Anonymization**: User sessions are identified by random SessionIDs, not email/phone numbers (unless logged in).
- **Consent**: Explicit "I agree" checkbox before document upload explaining text analysis.

## 11. API Endpoints Structure (Proposed)
| Method | Endpoint | Description | Payload |
|--------|----------|-------------|---------|
| GET | `/health` | Health check | - |
| POST | `/session/new` | Start new chat | `{ userId: "guest" }` |
| POST | `/chat/message` | Send query | `{ sessionId, text }` |
| POST | `/document/sign` | Get S3 URL | `{ fileName, fileType }` |
| POST | `/document/analyze` | Process file | `{ sessionId, s3Key }` |

## 12. Folder Structure
```
arthsaathi-backend/
├── src/
│   ├── handlers/       # Lambda function handlers
│   │   ├── chat.js
│   │   ├── upload.js
│   │   └── analyze.js
│   ├── services/       # Business logic
│   │   ├── bedrock.js
│   │   └── textract.js
│   ├── utils/          # Helpers (validation, formatting)
│   └── prompts/        # System prompts for Bedrock
├── template.yaml       # AWS SAM template
└── package.json
```

## 13. Deployment Strategy
- **Tool**: AWS SAM (Serverless Application Model) or CDK.
- **CI/CD**: GitHub Actions pipeline.
  1. `npm test`
  2. `sam build`
  3. `sam deploy`
- **Environment Variables**: API keys and Bucket names stored in AWS Systems Manager (SSM) Parameter Store.

## 14. Future Enhancements
- **Voice Interface**: Audio-to-Text (Transcribe) and Text-to-Speech (Polly) for rural accessibility.
- **Vernacular UI**: Full UI translation into Hindi, Tamil, Telugu using AWS Translate.
- **Agentic Workflow**: Use Amazon Bedrock Agents to actually fetch real-time interest rates from bank APIs.
