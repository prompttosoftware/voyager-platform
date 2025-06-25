# voyager-platform

Voyager is an innovative travel planning platform designed to empower users to discover and book unique travel experiences. It aims to provide a visually stunning, intuitive interface, leveraging AI for personalized recommendations, and offering seamless integration with various travel providers.

## Table of Contents

1.  [Features](#features)
2.  [Architecture](#architecture)
3.  [Technology Stack](#technology-stack)
4.  [Getting Started](#getting-started)
    *   [Prerequisites](#prerequisites)
    *   [Installation](#installation)
    *   [Running Locally](#running-locally)
5.  [Testing Strategy](#testing-strategy)
6.  [Contributing](#contributing)
7.  [License](#license)
8.  [Support](#support)

## 1. Features

Voyager provides a comprehensive suite of features to enhance the travel planning experience:

*   **User Authentication & Profile Management:** Secure registration, login, and personalized user profiles.
*   **Advanced Travel Discovery & Search:** Intuitive search, filtering, and sorting capabilities for unique travel experiences by keyword, location, dates, categories, and more.
*   **AI-Powered Personalized Recommendations:** Intelligent recommendations based on user history, preferences, and behavior.
*   **Detailed Experience Pages:** Rich media, descriptions, itineraries, and user reviews for each experience.
*   **Booking & Reservation Management:** Secure booking workflows with real-time availability checks and payment processing.
*   **Integration with Third-Party Travel Providers:** Seamless connection to external APIs for flights, accommodations, and activities.
*   **User Reviews & Ratings:** Empower users to share their experiences and help others.
*   **Content Management System (Admin-facing):** Tools for administrators and providers to create, manage, and publish unique travel content.

## 2. Architecture

Voyager adopts a **microservices-oriented architecture** to ensure scalability, maintainability, and independent deployability of services. A monorepo approach is used for streamlined development and deployment.

**High-Level Components:**

*   **Client Applications:** Web-based Single-Page Application (SPA) providing the user interface.
*   **API Gateway:** Routes requests to appropriate backend services, handles authentication and authorization.
*   **Backend Services (Microservices):**
    *   `User Service`: Manages user authentication, profiles, and preferences.
    *   `Discovery Service`: Handles search, filtering, and retrieval of travel experiences.
    *   `AI Recommendation Service`: Generates personalized travel recommendations.
    *   `Booking Service`: Manages booking transactions, availability, and payment processing.
    *   `Provider Integration Service`: Acts as an intermediary for external travel provider APIs.
    *   `Content Management Service`: Enables CRUD operations for travel experiences (admin/provider facing).
*   **Database Layer:** Polyglot persistence for optimal data handling (relational for transactions, NoSQL for content).
*   **Caching Layer:** Enhances performance by storing frequently accessed data.
*   **Message Broker:** Facilitates asynchronous inter-service communication and background tasks.
*   **File Storage:** For rich media content associated with experiences.

![Voyager High-Level Architecture Diagram (Conceptual)](./docs/architecture.png)
*(Note: A detailed architecture diagram will be provided in the `docs` folder.)*

## 3. Technology Stack

The platform leverages modern and robust technologies:

*   **Frontend:**
    *   **Framework:** React with Next.js (for SSR/SSG)
    *   **Language:** TypeScript
    *   **Styling:** Tailwind CSS, DaisyUI
    *   **State Management:** React Context API, Zustand/Jotai
    *   **Data Fetching:** React Query/SWR
*   **Backend:**
    *   **Framework:** Node.js with NestJS
    *   **Language:** TypeScript
    *   **AI/ML:** Python with FastAPI (for prediction serving), TensorFlow/PyTorch, Scikit-learn
*   **Databases:**
    *   PostgreSQL (primary relational database)
    *   MongoDB (for unstructured content/data)
    *   Redis (caching)
    *   Elasticsearch (for search and discovery)
*   **Message Broker:** Kafka or RabbitMQ
*   **Cloud Provider:** AWS (preferred for its comprehensive suite of services)
*   **CI/CD:** GitHub Actions
*   **Monitoring & Logging:** Prometheus, Grafana, ELK Stack / AWS CloudWatch/X-Ray
*   **Security:** AWS Secrets Manager for sensitive credentials

## 4. Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.

### Prerequisites

Before you begin, ensure you have the following installed:

*   **Git:** For cloning the repository.
*   **Node.js (LTS):** 18.x or higher.
*   **Yarn:** Or npm, depending on your preference. We recommend Yarn.
*   **Python (3.9+):** With `pip` for the AI service.
*   **Docker & Docker Compose:** For running local database instances and services.
*   **AWS CLI (Optional):** If you plan to interact with AWS services directly.

### Installation

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/your-org/voyager-platform.git
    cd voyager-platform
    ```

2.  **Install dependencies for all services (monorepo setup):**
    ```bash
    yarn install
    ```
    or
    ```bash
    npm install
    ```

3.  **Set up environment variables:**
    Copy `.env.example` files from each service directory (e.g., `services/user-service/.env.example`) to `.env` and configure them based on your local setup (e.g., database connection strings, external API keys).

    ```bash
    cp services/user-service/.env.example services/user-service/.env
    cp services/discovery-service/.env.example services/discovery-service/.env
    # ... and so on for other services and the frontend
    ```

4.  **Start local infrastructure using Docker Compose:**
    This will spin up PostgreSQL, MongoDB, Redis, and Elasticsearch containers.
    ```bash
    docker-compose -f docker-compose.local.yml up -d
    ```

5.  **Run database migrations (for PostgreSQL):**
    Navigate to each Node.js backend service that uses PostgreSQL (e.g., `user-service`, `booking-service`) and run its migrations.
    ```bash
    # Example for user-service
    cd services/user-service
    yarn typeorm -- dataSource src/config/data-source.ts migration:run
    cd ../..
    ```
    *(Note: Specific migration commands will be detailed in each service's individual README.)*

### Running Locally

To run the entire platform locally, you'll need to start each service.

1.  **Start Backend Services:**
    From the root directory, you can start each service in a separate terminal or use a tool like `concurrently` (if configured in `package.json`).

    ```bash
    # Example for User Service
    cd services/user-service
    yarn start:dev
    # Open new terminal for Discovery Service, etc.
    cd ../discovery-service
    yarn start:dev
    # ... and so on for other Node.js services
    ```

    For the AI Recommendation Service (Python/FastAPI):
    ```bash
    cd services/ai-recommendation-service
    pip install -r requirements.txt
    uvicorn main:app --reload --port 8001 # Or specified port in .env
    ```

2.  **Start Frontend Application:**
    ```bash
    cd apps/web
    yarn dev
    ```

Once all services are running, you can access the frontend application, typically at `http://localhost:3000`.

## 5. Testing Strategy

Voyager employs a robust, multi-layered testing strategy to ensure high quality and reliability. We follow the **Test Pyramid** principle, prioritizing fast and isolated tests.

### 5.1. Testing Hierarchy

1.  **Unit Tests (Fastest, Most Isolated):**
    *   **Focus:** Individual functions, methods, pure components, and business logic.
    *   **Tools:** Jest (Backend - Node.js/NestJS), Pytest (Backend - Python/FastAPI), Jest + React Testing Library (Frontend).
    *   **Mocking:** Minimal mocking, primarily for immediate external dependencies (e.g., database repository methods in a service unit test). Prefer simple stubs or fakes.

2.  **Integration Tests (Medium Speed & Scope):**
    *   **Focus:** Interactions between components, services, and their primary data stores or mocked external systems.
    *   **Tools:** Supertest (HTTP API testing), Testcontainers (for real database instances like PostgreSQL, MongoDB), MSW (Mock Service Worker) / Nock (for mocking external HTTP calls).
    *   **Mocking:** Use real test databases (via Testcontainers). **Crucially, mock calls to actual external third-party APIs (e.g., payment gateways, travel provider APIs) to ensure deterministic and efficient tests.** For message queues, use in-memory queues or lightweight test containers.

3.  **End-to-End (E2E) Tests (Slowest, Broadest Scope):**
    *   **Focus:** Entire user journeys through the deployed system (UI to backend services).
    *   **Tools:** Playwright or Cypress.
    *   **Mocking:** Minimal, primarily for extremely complex or unavailable external systems. Ideally, run against dedicated staging environments with real services.

### 5.2. Mocking Strategy

*   **Prefer Dependency Injection (DI):** Design components to receive their dependencies, making them easily replaceable with mocks/stubs in tests.
*   **Simple Fakes for Internal Dependencies:** For internal modules (e.g., a custom `UserRepository`), simple in-memory fakes can be more readable and maintainable than complex mocks.
*   **Mock at System Boundaries:** Only use dedicated mocking libraries when interacting with:
    *   External third-party APIs (Stripe, Amadeus, Google OAuth)
    *   Databases (at the repository level for unit tests; use real test DBs for integration tests)
    *   Message Brokers (for unit tests of publishing logic; for integration tests, verify message sent to a local test broker)
    *   File Storage (S3, etc.)

---

## 6. Contributing

We welcome contributions to the Voyager platform! Please refer to our [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on how to submit pull requests, report issues, and contribute to the development process.

## 7. License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 8. Support

If you encounter any issues or have questions, please open an issue on the GitHub repository. For more direct communication, reach out to the core development team.
