# 🩺 AI Health Report Translator

### Medical reports, explained in plain language.

Turn technical laboratory reports into clearer explanations, reviewable
results, doctor-discussion questions, and visual health trends --- all
in one privacy-conscious web application.

```{=html}
<p align="center">
```
`<img src="https://img.shields.io/badge/Status-Academic%20Project-blue?style=for-the-badge" alt="Project status" />`{=html}
`<img src="https://img.shields.io/badge/AI-Responsible%20Use-7c3aed?style=for-the-badge" alt="Responsible AI" />`{=html}
`<img src="https://img.shields.io/badge/Platform-Web%20Application-0f766e?style=for-the-badge" alt="Web application" />`{=html}
```{=html}
</p>
```
**Understand the report. Verify the values. Prepare better questions.**

[Overview](#-overview) · [Features](#-key-features) ·
[Architecture](#-system-architecture) · [Data
Model](#-proposed-database-schema) · [Workflow](#-how-it-works) ·
[Security](#-privacy--safety) · [Roadmap](#-development-roadmap)

------------------------------------------------------------------------

> \[!IMPORTANT\] **Informational use only:** This project is designed to
> help users understand laboratory-report terminology. It does not
> diagnose diseases, predict illness, recommend treatment, or replace a
> qualified healthcare professional. Always discuss medical results with
> your doctor.

## ✨ Overview

Laboratory reports often contain abbreviations, unfamiliar test names,
units, and reference ranges. The **AI Health Report Translator** is a
proposed full-stack web application that makes these reports easier to
review.

Users upload a PDF or image of a laboratory report. The system extracts
the available test details, lets the user verify them, compares numeric
values against the reference ranges printed on the report, and uses an
AI service to produce plain-language explanations, a concise summary,
and suggested questions for a medical consultation. Saved reports can be
revisited and used to visualise changes in health parameters over time.

### 🎯 Project goals

-   Extract test names, results, units, and reference ranges from PDF
    and image reports.
-   Classify numeric results as **Normal**, **High**, or **Low** using
    the report's own reference range.
-   Explain results in simple, non-diagnostic language.
-   Generate useful questions users can take to a doctor.
-   Maintain report history and visualise comparable results over time.
-   Protect user information through authentication, access control, and
    data-minimisation practices.

## 🚀 Key Features

    <details>

    <summary>

`<strong>`{=html}📄 Upload and process reports`</strong>`{=html}

    </summary>

-   Upload PDF, JPG, and PNG laboratory reports.
-   Extract text directly from digital PDFs.
-   Use OCR for scanned PDFs and image-based reports.
-   Parse test names, values, units, and reference ranges.
-   Review and correct extracted data before analysis.

```{=html}
<!-- -->
```
    </details>

    <details>

    <summary>

`<strong>`{=html}🔎 Review result status`</strong>`{=html}

    </summary>

Numeric results are compared with the reference range supplied in the
report. The classification is performed by deterministic application
logic rather than delegated to the language model.

-   **Normal:** within the supplied range.
-   **High:** above the supplied range.
-   **Low:** below the supplied range.
-   **Qualitative results:** displayed as reported; not graded as
    Normal, High, or Low.

The system does not independently determine whether a result is
medically concerning.

    </details>

    <details>

    <summary>

`<strong>`{=html}🤖 AI explanations and report summary`</strong>`{=html}

    </summary>

The AI service is intended to generate: - Plain-language explanations of
individual tests. - A concise overall report summary. - Suggested
questions to discuss with a qualified healthcare professional.

AI output should be validated and constrained to the provided report
data. It must not present a diagnosis or recommend medication or
treatment.

    </details>

    <details>

    <summary>

`<strong>`{=html}📈 Report history and health trends`</strong>`{=html}

    </summary>

-   View previously uploaded reports.
-   Compare the same parameter across report dates.
-   Display trends with charts and reference-range context.
-   Delete reports and, where implemented, the user's account.

Trend comparisons are meaningful only when test names and units are
consistent or have been safely normalised.

    </details>

    <details>

    <summary>

`<strong>`{=html}🔐 Account and privacy controls`</strong>`{=html}

    </summary>

-   Registration and login using JWT-based authentication.
-   Password hashing (for example, bcrypt).
-   Protected API routes and per-user data access.
-   File-type, file-size, and input validation.
-   Keep API keys and database credentials in environment variables.
-   Minimise personal identifiers sent to an external AI service.

```{=html}
<!-- -->
```
    </details>

## 🧱 Technology Stack

  -----------------------------------------------------------------------
  Layer                   Planned technology      Purpose
  ----------------------- ----------------------- -----------------------
  Frontend                React.js, Vite,         Responsive interface,
                          JavaScript, HTML, CSS   routing, and state
                                                  handling

  UI and charts           Lucide React, Recharts  Icons, graphs, and
                          (or similar)            trend visualisation

  Backend                 Node.js, Express.js,    Application logic and
                          REST APIs               API endpoints

  Authentication          JWT, password hashing   Authentication and
                          such as bcrypt          protected routes

  File uploads            Multer                  Multipart uploads and
                                                  file validation

  PDF processing          PDF text-extraction     Read text from digital
                          library                 PDFs

  OCR                     Tesseract or equivalent Read
                          OCR engine              scanned/image-based
                                                  reports

  Database                MongoDB                 Store users, reports,
                                                  extracted results, and
                                                  analyses

  AI integration          LLM/AI API --- provider Explanations, summary,
                          to be finalised         and doctor-question
                                                  generation

  Testing and development Git, GitHub, Postman,   Version control and
                          browser-based testing   API/workflow testing
  -----------------------------------------------------------------------

> **Implementation note:** This stack reflects the project synopsis.
> Replace planned tools with the exact libraries and providers used in
> the implementation.

## 🏛️ System Architecture

The application follows a **three-tier architecture**, supported by
document-processing and AI-service modules.

``` mermaid
flowchart TB
    U["👤 User"]
    subgraph PRESENTATION["1. Presentation Layer"]
        UI["React + Vite Web App"]
        DASH["Dashboard · Upload · Review · Charts"]
    end

    subgraph APPLICATION["2. Application Layer — Node.js + Express"]
        AUTH["JWT Authentication & Access Control"]
        UPLOAD["Upload API + Multer Validation"]
        EXTRACT["PDF Text Extraction / OCR"]
        PARSE["Structured Test Data Parser"]
        VERIFY["User Verification Step"]
        RULES["Reference-Range Classification"]
        AI["AI Service Adapter"]
        VALIDATE["AI Response Validation"]
    end

    subgraph DATA["3. Data Layer"]
        DB[("MongoDB")]
        FILES["Report File Storage"]
    end

    LLM["External LLM / AI API"]

    U --> UI
    UI --> DASH
    DASH <--> AUTH
    DASH --> UPLOAD
    UPLOAD --> FILES
    UPLOAD --> EXTRACT
    EXTRACT --> PARSE
    PARSE --> VERIFY
    VERIFY --> RULES
    VERIFY --> AI
    AI <--> LLM
    AI --> VALIDATE
    RULES --> DB
    VALIDATE --> DB
    AUTH <--> DB
    DB --> DASH
```

### Architecture responsibilities

  ---------------------------------------------------------------------
  Component                          Responsibility
  ---------------------------------- ----------------------------------
  React frontend                     Upload reports, show extracted
                                     fields for confirmation, present
                                     explanations and charts

  Express REST API                   Validate requests, enforce access
                                     control, coordinate processing,
                                     and return results

  PDF/OCR pipeline                   Extract readable text from
                                     supported documents

  Structured parser                  Identify test names, result
                                     values, units, and reference
                                     ranges

  Rule-based classifier              Compare numeric values against the
                                     supplied reference range

  AI service adapter                 Request plain-language
                                     explanations, summary, and doctor
                                     questions

  MongoDB                            Persist user-linked report
                                     metadata, results, analysis, and
                                     dates

  Trend module                       Group comparable parameters and
                                     provide chart-ready time-series
                                     data
  ---------------------------------------------------------------------

## 🔄 How It Works

``` mermaid
flowchart TD
    A["Register / Log in"] --> B["Open Dashboard"]
    B --> C["Upload PDF or Image"]
    C --> D{"Text layer available?"}
    D -- "Yes" --> E["Extract PDF Text"]
    D -- "No" --> F["OCR Processing"]
    E --> G["Parse Test Details"]
    F --> G
    G --> H["User Reviews and Corrects Values"]
    H --> I["Classify Numeric Results"]
    H --> J["Request AI Explanations"]
    J --> K["Validate Structured AI Response"]
    I --> L["Save Verified Results"]
    K --> L
    L --> M["Summary + Doctor Questions"]
    L --> N["Report History + Trend Charts"]
```

### Processing pipeline

1.  **Authenticate:** the user signs in and receives a time-limited JWT.
2.  **Upload:** the API validates the file type and size.
3.  **Extract:** text is read from a digital PDF or OCR is used for
    scanned/image-based content.
4.  **Parse:** the system identifies test names, result values, units,
    and reference ranges.
5.  **Verify:** the user checks and corrects extracted fields before
    analysis.
6.  **Classify:** application logic labels numeric values Normal, High,
    or Low against the supplied range.
7.  **Explain:** the AI service receives verified data and a
    non-diagnostic prompt.
8.  **Validate and save:** the response is checked before being stored
    with the report.
9.  **Review over time:** users revisit saved reports and view
    comparable parameters on charts.

## 🗃️ Proposed Database Schema

The following is a **proposed logical schema** based on the synopsis,
not a claim about an already implemented Mongoose schema. MongoDB is
document-oriented, so these entities may be represented as separate
collections or embedded documents depending on implementation choices.

``` mermaid
erDiagram
    USER ||--o{ REPORT : owns
    REPORT ||--o{ TEST_RESULT : contains
    REPORT ||--o| REPORT_ANALYSIS : has

    USER {
        ObjectId _id PK
        string name
        string email UK
        string passwordHash
        date createdAt
        date updatedAt
    }

    REPORT {
        ObjectId _id PK
        ObjectId userId FK
        string originalFileName
        string fileType
        string fileReference
        date reportDate
        date uploadedAt
        string processingStatus
    }

    TEST_RESULT {
        ObjectId _id PK
        ObjectId reportId FK
        string testName
        string normalizedTestName
        number value
        string qualitativeValue
        string unit
        number referenceLow
        number referenceHigh
        string referenceText
        string status
        boolean userVerified
    }

    REPORT_ANALYSIS {
        ObjectId _id PK
        ObjectId reportId FK
        string summary
        array explanations
        array doctorQuestions
        string disclaimer
        date generatedAt
    }
```

### Collection design notes

    <details>

    <summary>

`<strong>`{=html}👤 User`</strong>`{=html}

    </summary>

Stores account information.

-   `_id`: MongoDB document identifier.
-   `name`: display name, if needed.
-   `email`: login identifier; enforce a unique index.
-   `passwordHash`: salted password hash --- never store a plaintext
    password.
-   `createdAt`, `updatedAt`: audit timestamps.

```{=html}
<!-- -->
```
    </details>

    <details>

    <summary>

`<strong>`{=html}📁 Report`</strong>`{=html}

    </summary>

Represents one uploaded laboratory report.

-   `userId`: owner of the report.
-   `originalFileName`, `fileType`: upload metadata.
-   `fileReference`: controlled storage reference; avoid exposing raw
    filesystem paths.
-   `reportDate`: date printed on the report, when available.
-   `uploadedAt`: upload timestamp.
-   `processingStatus`: for example, `uploaded`, `processing`,
    `needs_review`, `completed`, or `failed`.

```{=html}
<!-- -->
```
    </details>

    <details>

    <summary>

`<strong>`{=html}🧪 TestResult`</strong>`{=html}

    </summary>

Represents one test entry extracted from a report.

-   `testName`, `normalizedTestName`: original and normalised names for
    matching across reports.
-   `value`, `unit`: numeric value and unit when applicable.
-   `qualitativeValue`: reported text for values such as Positive or
    Negative.
-   `referenceLow`, `referenceHigh`, `referenceText`: parsed and
    original reference range.
-   `status`: Normal, High, Low, or Not graded.
-   `userVerified`: whether the user confirmed the extracted fields.

```{=html}
<!-- -->
```
    </details>

    <details>

    <summary>

`<strong>`{=html}📝 ReportAnalysis`</strong>`{=html}

    </summary>

Stores the generated educational output.

-   `summary`: short report overview.
-   `explanations`: per-test plain-language explanations.
-   `doctorQuestions`: suggested questions for consultation.
-   `disclaimer`: non-diagnostic notice displayed with results.
-   `generatedAt`: analysis timestamp.

```{=html}
<!-- -->
```
    </details>

### Important data-model rules

-   Every report query must be scoped to the authenticated `userId`.
-   Validate and normalise values before comparing or plotting them.
-   Preserve the original reference-range text in case parsing needs
    review.
-   Do not compare values across incompatible units without a validated
    conversion.
-   Consider embedding test results inside the report document if that
    better suits the implementation; choose one approach consistently.
-   Add indexes for common queries, such as
    `{ userId: 1, uploadedAt: -1 }` and report/result lookup fields, as
    appropriate.

## 🧮 Example: Rule-Based Classification

Suppose a report contains:

  Field             Example
  ----------------- ---------------
  Test              WBC Count
  Reported value    11,200
  Reference range   4,000--11,000
  Classification    **High**

The value is classified as **High** because it is above the reference
range printed on the report. A cautious explanation might say:

> "Your WBC count is slightly above the reference range provided on this
> report. There can be several reasons for a result like this. Discuss
> it with a qualified healthcare professional."

This example illustrates range comparison only. It does **not**
establish a diagnosis.

### Classification logic (illustrative pseudocode)

``` text
if result is qualitative:
    display the reported result without grading
else if the reference range cannot be parsed or verified:
    mark the result for review
else if value < referenceLow:
    status = "Low"
else if value > referenceHigh:
    status = "High"
else:
    status = "Normal"
```

> Some reports use one-sided ranges, text-based ranges, or special
> interpretation rules. Handle those explicitly instead of forcing every
> result into a two-bound numeric range.

## 🔌 Proposed REST API Surface

These are **suggested endpoint contracts for planning**. They are not a
list of verified, implemented routes.

  ----------------------------------------------------------------------------
  Method               Suggested endpoint                 Purpose
  -------------------- ---------------------------------- --------------------
  `POST`               `/api/auth/register`               Create an account

  `POST`               `/api/auth/login`                  Authenticate and
                                                          issue a JWT

  `GET`                `/api/auth/me`                     Return the
                                                          authenticated user's
                                                          profile

  `POST`               `/api/reports`                     Upload a report

  `GET`                `/api/reports`                     List the current
                                                          user's report
                                                          history

  `GET`                `/api/reports/:reportId`           Retrieve a report
                                                          and its results

  `PATCH`              `/api/reports/:reportId/results`   Save user
                                                          corrections to
                                                          extracted values

  `POST`               `/api/reports/:reportId/analyze`   Generate and
                                                          validate educational
                                                          analysis

  `DELETE`             `/api/reports/:reportId`           Delete a user's
                                                          report

  `GET`                `/api/trends/:testName`            Retrieve comparable
                                                          results over time

  `DELETE`             `/api/auth/me`                     Request account
                                                          deletion, if
                                                          supported
  ----------------------------------------------------------------------------

**API design checklist**

-   Apply authentication middleware to protected routes.
-   Confirm ownership for every report read, update, analysis, and
    delete request.
-   Validate request bodies, identifiers, upload types, and upload
    sizes.
-   Return consistent error responses without exposing secrets or
    internal stack traces.
-   Rate-limit sensitive endpoints and protect against repeated
    expensive AI requests.
-   Never accept a client-supplied `userId` as proof of ownership.

## 🔐 Privacy & Safety

Medical reports may contain sensitive personal information. Privacy and
non-diagnostic behaviour are core design requirements, not optional
extras.

-   **Authentication:** store salted password hashes and use
    time-limited JWTs.
-   **Authorisation:** restrict report access to the authenticated owner
    on every request.
-   **Upload validation:** allow only supported formats, enforce size
    limits, and validate content rather than trusting file extensions
    alone.
-   **Secrets management:** keep database credentials and AI API keys in
    environment variables; never commit them.
-   **Data minimisation:** avoid sending names, addresses, contact
    details, or other unnecessary identifiers to the external AI
    provider.
-   **Transport security:** use HTTPS in deployment.
-   **User control:** support report deletion and account deletion where
    implemented.
-   **AI safeguards:** validate the AI response, keep explanations
    grounded in supplied values, and do not provide diagnoses or
    treatment instructions.
-   **Transparency:** show a clear disclaimer on every analysis screen.

> **Important limitations:** OCR and parsing can misread values; unusual
> layouts and poor-quality scans may reduce accuracy. Classification
> relies on the range printed on the report, and trend tracking requires
> consistent test names and units. AI explanations can be incomplete or
> inaccurate. Users should verify extracted data and discuss results
> with a qualified doctor.

## 🧪 Testing Strategy

  ---------------------------------------------------------------------
  Test level                         What to verify
  ---------------------------------- ----------------------------------
  Unit tests                         Range comparison, parser
                                     behaviour, missing values,
                                     boundary values, and qualitative
                                     results

  API tests                          Authentication, upload validation,
                                     ownership checks, response codes,
                                     and error handling

  Integration tests                  PDF/OCR → parsing → user review →
                                     classification → AI validation →
                                     persistence

  End-to-end tests                   Full upload and review workflow,
                                     report history, deletion, and
                                     trend charts

  Security tests                     Cross-user access attempts,
                                     invalid tokens, oversized files,
                                     invalid inputs, and secret
                                     handling

  AI output checks                   Structured response validation,
                                     grounded explanations, absence of
                                     diagnoses/treatment
                                     recommendations

  Usability checks                   Clarity of labels, correction
                                     workflow, disclaimers, and chart
                                     readability
  ---------------------------------------------------------------------

Use synthetic or properly anonymised sample reports for development and
testing. Do not use real patient data without appropriate permission and
safeguards.

## 🗺️ Development Roadmap

The synopsis proposes a 16-week plan. Adjust these milestones to reflect
actual progress.

-   [ ] **Weeks 1--2:** Requirements, literature survey, stack and
    AI-provider selection
-   [ ] **Weeks 3--4:** Architecture, database schema, API design,
    wireframes, prompt guidelines
-   [ ] **Weeks 5--6:** Project setup, authentication, dashboard, report
    upload
-   [ ] **Weeks 7--9:** PDF/OCR extraction, parsing, review screen,
    status classification
-   [ ] **Weeks 10--11:** AI integration, explanations, summary, doctor
    questions, response validation
-   [ ] **Weeks 12--13:** Report history, test-name normalisation, trend
    charts
-   [ ] **Weeks 14--15:** Security hardening, testing, bug fixing, UI
    refinement
-   [ ] **Week 16:** Deployment, user documentation, final report,
    presentation

## 👥 Team

  ---------------------------------------------------------------------
  Team member                        Responsibility
  ---------------------------------- ----------------------------------
  **Kritika Yadav**                  Team Leader; Backend and API
                                     development

  **Anubhavi Sinha**                 Frontend development and data
                                     visualisation

  **Kumari Bhavya Singh**            Report processing (PDF/OCR) and AI
                                     integration

  **Aditya Mishra**                  Database design, testing, and
                                     documentation
  ---------------------------------------------------------------------

**Project supervisor:** Meghna M --- Technical Trainer, CEA, GLA
University, Mathura.

## 🛠️ Local Setup

The final repository structure, scripts, environment variable names, and
AI provider are not specified in the synopsis. The following is a
**generic setup outline**; adapt it to the actual codebase.

### Prerequisites

-   Node.js LTS and npm
-   MongoDB local instance or MongoDB Atlas database
-   Git
-   An AI API account and key, once the provider is finalised
-   PDF extraction and OCR dependencies used by the implementation

### Steps

``` bash
# 1. Clone your repository
git clone <YOUR_REPOSITORY_URL>
cd <YOUR_PROJECT_DIRECTORY>

# 2. Install dependencies in the frontend and backend folders
cd backend
npm install

# If your frontend is in a separate folder:
cd ../frontend
npm install
```

Create environment files based on the variables your application
actually reads. **Do not commit `.env` files or real credentials.**

Example variable names to adapt to your code:

``` env
# Backend example — use the exact names expected by your code
PORT=5000
MONGODB_URI=your_mongodb_connection_string
JWT_SECRET=replace_with_a_long_random_secret
AI_API_KEY=your_ai_provider_key
```

Start commands depend on the scripts defined in each `package.json`.
Check those scripts before running the app. Never put real keys in this
README.

## 📁 Suggested Repository Structure

``` text
ai-health-report-translator/
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── services/
│   │   └── charts/
│   └── package.json
├── backend/
│   ├── src/
│   │   ├── config/
│   │   ├── middleware/
│   │   ├── routes/
│   │   ├── controllers/
│   │   ├── services/
│   │   │   ├── pdf/
│   │   │   ├── ocr/
│   │   │   └── ai/
│   │   ├── models/
│   │   └── utils/
│   └── package.json
├── tests/
├── .env.example
├── .gitignore
└── README.md
```

This is a suggested layout, not a description of the current repository.

## ⚖️ Scope & Limitations

### In scope

-   Authentication and protected access.
-   PDF/image upload and text extraction.
-   Structured extraction with a user verification step.
-   Reference-range-based numeric classification.
-   AI-generated educational explanations and doctor questions.
-   Report history and trend visualisation.

### Out of scope

-   Diagnosing diseases or predicting illness.
-   Prescribing medicines, dosages, or treatments.
-   Replacing a doctor or emergency medical services.
-   Interpreting X-rays, MRI/CT scans, handwritten prescriptions, or
    clinical notes.
-   Hospital/EHR integration.
-   Claiming formal clinical validation, regulatory approval, or
    medical-device certification.

## 📚 References

1.  React Documentation --- https://react.dev/
2.  Vite Guide --- https://vite.dev/guide/
3.  Node.js Documentation --- https://nodejs.org/docs/latest/api/
4.  Express.js --- https://expressjs.com/
5.  MongoDB Manual --- https://www.mongodb.com/docs/manual/
6.  Multer --- https://github.com/expressjs/multer
7.  Recharts --- https://recharts.org/
8.  Lucide React --- https://lucide.dev/guide/packages/lucide-react
9.  JSON Web Token (JWT), RFC 7519 ---
    https://www.rfc-editor.org/rfc/rfc7519
10. Tesseract OCR --- https://github.com/tesseract-ocr/tesseract
11. PDF.js --- https://mozilla.github.io/pdf.js/
12. OWASP Top Ten --- https://owasp.org/www-project-top-ten/
13. Digital Personal Data Protection Act, 2023 --- Ministry of
    Electronics and Information Technology, Government of India.

## 🤝 Contributing

Contributions, suggestions, and bug reports are welcome. For changes
involving report parsing, classification, or AI output, include tests
and document limitations clearly. Do not include real patient reports or
personal medical data in issues, pull requests, screenshots, or test
fixtures.

## 📄 License

No license was specified in the project synopsis. Add a `LICENSE` file
and name the license here before distributing or accepting external
contributions.

------------------------------------------------------------------------

**Built as an academic project at GLA University, Mathura.**

*Making laboratory reports easier to understand --- without replacing
professional medical advice.* :::
