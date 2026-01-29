# Help Desk System - Test Project

## Business Story

Your company has an internal help desk. Employees submit simple support tickets (IT / Facilities / General). Support agents review them, add comments, and close them when resolved. You need a lightweight web app to manage this.

## Tech Constraints

- **Frontend**: React
- **Backend**: ASP.NET Core Web API
- **Database**: PostgreSQL or SQL Server (candidate chooses)
- **Repo**: Push to GitHub with clear commit history
- **Timebox**: Designed to be solvable in ~3 days

## Functional Requirements

### 1) Tickets

Each ticket must have:

- `id` (int or guid)
- `title` (required, 5–100 chars)
- `description` (required, 10–2000 chars)
- `category` (enum or lookup): IT, Facilities, General
- `priority` (enum): Low, Medium, High
- `status` (enum): Open, InProgress, Closed
- `createdAt` (server-generated)
- `updatedAt` (server-generated)
- `createdBy` (string, required) — simple "name" entered on UI is enough

#### Rules:

- New ticket always starts as **Open**
- Ticket can be moved:
  - `Open -> InProgress -> Closed`
  - also allow `InProgress -> Open` (reopened)
  - do not allow `Closed -> InProgress` (must "Reopen" to Open first)
- Closing rule: a ticket can only be **Closed** if it has at least one comment

#### Required actions:

- Create ticket
- View ticket details
- Update ticket status (with rules above)
- Delete ticket (allowed; optional soft delete is fine)

### 2) Comments

Each comment must have:

- `id`
- `ticketId`
- `author` (required)
- `message` (required, 2–1000 chars)
- `createdAt` (server-generated)

#### Rules:

- Comments can be added to **Open** and **InProgress**
- Comments cannot be added to **Closed**

#### Required actions:

- Add comment to a ticket
- List comments for a ticket (ordered oldest → newest)

### 3) Ticket list + filtering (important)

Ticket list screen must support:

- Filter by status
- Filter by priority
- Filter by category
- Search by keyword (title/description contains)
- Sort by createdAt (newest first)

#### Nice-to-have (bonus):

- Pagination (page + pageSize) on backend + frontend

## UI Requirements (React)

### Pages

#### Ticket List
- table or cards view
- filters (status/priority/category) + search box
- clicking ticket opens details page

#### Create Ticket
- form with validation messages
- on success, redirect to details page

#### Ticket Details
- show ticket fields
- show status badge
- buttons to change status (Open/InProgress/Closed as allowed)
- comments section:
  - list comments
  - add comment form (disabled if ticket closed)

### UX expectations (junior-friendly)

- Basic form validation (required fields, length)
- Clear error handling when API returns errors
- Loading states (simple "Loading…" is fine)

## Backend Requirements (ASP.NET Core)

### Suggested endpoints (minimal + clean)

#### Tickets
- `GET /api/tickets?status=&priority=&category=&q=&sort=createdAt_desc&page=&pageSize=`
- `GET /api/tickets/{id}`
- `POST /api/tickets`
- `PATCH /api/tickets/{id}/status` (body: `{ "status": "InProgress" }`)
- `DELETE /api/tickets/{id}`

#### Comments
- `GET /api/tickets/{id}/comments`
- `POST /api/tickets/{id}/comments`

### Backend behavior

- Use DTOs (don't expose EF entities directly)
- Validate inputs and return sensible status codes:
  - `400` validation errors
  - `404` not found
  - `409` conflict for invalid transitions (optional) or `400` acceptable
- Use EF Core + migrations
- Seed data: at least 5 tickets, some with comments

### Data model (minimum)

#### Tables:
- Tickets
- Comments

#### Relationships:
- One ticket has many comments

#### Indexes (bonus but simple):
- index on `Tickets(status)`
- index on `Tickets(createdAt)`
- optional full-text/search approach (simple LIKE is OK)

## Acceptance Criteria Checklist

A submission is "complete" if:

- ✅ App runs locally following README
- ✅ Can create and view tickets
- ✅ Ticket list filters + search work
- ✅ Can add comments (Open/InProgress only)
- ✅ Cannot close ticket without at least one comment
- ✅ Status transitions enforce the rules
- ✅ Database migrations exist and work

## Deliverables

In GitHub repo:

- `/backend` ASP.NET Core API
- `/frontend` React app
- `README.md` with:
  - prerequisites
  - how to run backend + frontend
  - how to run migrations / connection string example
  - seeded test data
- Swagger enabled (or Postman collection)

### Bonus deliverables:

- Docker Compose for DB + backend
- A short `NOTES.md` with tradeoffs and "what I'd improve next"

## Grading Rubric

### Correctness (40%)
Rules enforced, filters work, comments/close logic correct

### Code quality (25%)
Structure, naming, separation, DTOs

### Data + API design (20%)
Clean endpoints, migrations, validations

### Frontend quality (15%)
Readable components, basic UX, error handling
