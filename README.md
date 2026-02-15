# FinClarity - Your AI Financial Navigator 🇮🇳

> **"The Google Translate for Finance"**

FinClarity is an AI-powered platform designed to help ordinary citizens navigate the complex Indian financial ecosystem. It acts as a clarity layer, translating jargon-heavy documents and confusing processes into simple, actionable guidance in plain language.

## 🚀 Project Overview

Financial literacy in India is low, and financial products are often sold with opaque terms. FinClarity solves this by:
1.  **Decoding Insurance Policies**: Upload a PDF policy, and get a simple summary of what's covered and what's not.
2.  **Simplifying Processes**: Step-by-step guides for tasks like "Death Claim Settlement" or "Closing a Loan".
3.  **Goal-Based Guidance**: Ask questions like "How do I save for a car?" and get a personalized roadmap.

**Note**: This platform does **NOT** sell products or execute transactions. It is purely an informational guide.

## 📚 Documentation

- **[Product Requirements](./requirements.md)**: Detailed breakdown of user personas, stories, and functional specs.
- **[Technical Design](./design.md)**: Architecture, data flow, and AWS serverless implementation details.

## 🛠 Tech Stack

- **Frontend**: Next.js (React), Tailwind CSS
- **Backend**: AWS Lambda (Node.js/Python)
- **AI Engine**: Amazon Bedrock (Claude 3 Sonnet & Haiku)
- **OCR/Document Processing**: Amazon Textract
- **Storage**: Amazon S3 (Documents), DynamoDB (Sessions)
- **Infrastructure**: AWS SAM / CDK

## ✨ Key Features

- **Document Scanner**: Drag & drop insurance/loan documents for instant simplification.
- **Visual Process Maps**: Timelines and checklists for bureaucratic procedures.
- **Private & Secure**: Documents are processed ephemerally and auto-deleted after 1 hour.
- **Verified Guidance**: AI responses are grounded in regulatory facts (RBI/IRDAI guidelines).


## 📄 License
MIT License. See [LICENSE](LICENSE) for more information.
