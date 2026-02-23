# Sports Equipment Rental & Repair Shop - React Frontend

A single-page application built with **React 18** that serves as the frontend for a sports equipment rental and repair shop. Communicates with the [Express.js backend](https://github.com/karolpela/internet-technologies-express) via a RESTful API, featuring JWT authentication, role-based access control, client-side form validation, and full internationalization (Polish/English).

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [Getting Started](#getting-started)
- [Authentication & Authorization](#authentication--authorization)
- [Internationalization](#internationalization)
- [Project Structure](#project-structure)
- [Related Projects](#related-projects)

## Features

- **Equipment Browsing** - View, add, edit, and delete sports equipment (public listing, employee-restricted modifications)
- **Rental Management** - Full CRUD for equipment rentals with date validation and role-restricted access
- **Service & Repair Tracking** - Admin-managed services and employee-managed repairs, each with detail views and forms
- **Customer Management** - Employee-accessible customer listing with add/edit/delete operations
- **JWT Authentication** - Login via phone number and password, with token stored in `localStorage` and attached to all API requests
- **Role-Based Route Protection** - Three tiers of access (Customer, Employee, Admin) enforced through route guard components
- **Client-Side Form Validation** - Real-time field validation with translated error messages (required fields, text length, phone format, date ranges, shoe sizes)
- **Internationalization** - Full Polish and English language support, switchable at runtime via the navigation bar

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | React 18 (class components) |
| Routing | React Router v6 |
| i18n | i18next, react-i18next |
| HTTP Client | Fetch API |
| Dev Tools | ESLint, Prettier |
| Build Tool | Create React App |

## Architecture

The application follows a component-based architecture with a clear separation between UI, data fetching, and validation logic:

```
App (state: user)
 ├── Header / Footer
 ├── Navigation (language switch, login/logout)
 └── Routes
      ├── ProtectedRoute / EmployeeOnlyRoute / AdminOnlyRoute  (route guards)
      └── Resource modules (Equipment, Rental, Service, Repair, Customer)
           ├── List  ->  ListTable  ->  ListTableRow
           ├── Details  ->  DetailsData
           ├── Form (add/edit with validation)
           └── DeleteResource (generic delete handler)
```

**Key patterns:**

- **Class components with `withTranslation()` HOC** for i18n access throughout the component tree
- **API call modules** (`src/apiCalls/`) encapsulate all `fetch` calls per resource, keeping components free of HTTP logic
- **Reusable form components** (`FormInput`, `FormSelect`, `FormButtons`) provide consistent form rendering with integrated validation error display
- **Validation helpers** (`src/helpers/validationCommon.js`) provide pure functions for field validation (required, text length, phone number, date, number range)
- **Generic `DeleteResource` component** handles deletion for any resource type via a passed-in API call function

## Getting Started

### Prerequisites

- **Node.js** (v14+)
- The [Express.js backend](https://github.com/karolpela/internet-technologies-express) running on `http://localhost:3000`

### Installation

```bash
# Clone the repository
git clone https://github.com/karolpela/internet-technologies-react.git
cd internet-technologies-react

# Install dependencies
npm install
```

### Running the Application

```bash
npm start
```

The app starts on [http://localhost:8000](http://localhost:8000) and expects the backend API at `http://localhost:3000`.

### Default Accounts

See the [backend README](https://github.com/karolpela/internet-technologies-express#default-accounts) for test credentials. Authentication uses phone number and password.

## Authentication & Authorization

The app authenticates against the backend's JWT endpoint (`POST /api/auth/login`). On successful login, the full user object (including token and role) is stored in `localStorage` and attached as a `Bearer` token to all subsequent API requests.

**Route Guards:**

| Component | Access Level | Usage |
|---|---|---|
| `ProtectedRoute` | Any authenticated user | Rentals list, rental details |
| `EmployeeOnlyRoute` | Employee or Admin | Customers, equipment edit, rentals edit, repairs |
| `AdminOnlyRoute` | Admin only | Services management |

Unauthenticated users attempting to access protected routes are redirected to `/login`. Equipment browsing is publicly accessible without authentication.

## Internationalization

The application supports **Polish** (default) and **English**, switchable instantly via PL/EN buttons in the navigation bar. Translations are managed through i18next with JSON resource files.

Translation files in `src/locales/` cover:
- Navigation labels and page titles
- Form field labels, placeholders, and validation messages
- Entity-specific terminology (equipment types, service categories, repair statuses)
- Role names and authentication messages

## Project Structure

```
src/
├── apiCalls/               # API integration layer (one file per resource)
│   ├── authApiCalls.js     # Login endpoint
│   ├── customerApiCalls.js # Customer CRUD
│   ├── equipmentApiCalls.js
│   ├── rentalApiCalls.js
│   ├── repairApiCalls.js
│   └── serviceApiCalls.js
├── components/
│   ├── customer/           # Customer list, details, form
│   ├── equipment/          # Equipment list, details, form
│   ├── rental/             # Rental list, details, form
│   ├── repair/             # Repair list, details, form
│   ├── service/            # Service list, details, form
│   ├── form/               # Reusable form components (FormInput, FormSelect, FormButtons)
│   ├── fragments/          # Layout components (Header, Footer, Navigation)
│   └── other/              # Route guards, LoginForm, MainContent, DeleteResource
├── helpers/
│   ├── authHelper.js       # JWT token management and role checks
│   ├── dateHelper.js       # Date formatting utilities
│   ├── formHelper.js       # Form mode constants and validation key mapping
│   ├── routerHelper.js     # withRouter HOC for class components
│   └── validationCommon.js # Reusable validation functions
├── locales/                # i18n translation files (en.json, pl.json)
├── i18n.js                 # i18next configuration
├── App.js                  # Root component with routing table
└── index.js                # Application entry point
```

## Related Projects

- **Backend:** [internet-technologies-express](https://github.com/karolpela/internet-technologies-express) - Express.js + Sequelize REST API that this frontend consumes
