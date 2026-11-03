# Bio-Bank API

REST API for Bio-Bank, a lab sample management application used at King Hussein Cancer Center. It follows biological samples from collection to freezer storage and covers research studies, patients, freezers, reports and an audit log.

The React frontend is in [7aaj/KHCC](https://github.com/7aaj/KHCC).

## How it works

1. **Accounts:** new users register with a `pending` role, and a super admin gives them a nursing, technician or super admin role.
2. **Studies:** staff submit a research study with its files, and a super admin approves it.
3. **Collection:** nurses add patients and their samples to a study.
4. **Review:** technicians mark patients as seen, then approve or reject each sample. Rejections keep a reason.
5. **Storage:** technicians place approved samples in a freezer by main box, sub box and cell.
6. **Extraction and reports:** technicians remove samples from freezers and export Excel reports. A backup record keeps extracted samples available for reporting, and the freezer report includes each sample's storage price at 1 JD per day.

Super admins can also view freezer and study statistics and the audit log. Super admins and technicians can search across studies, patients and samples.

## Tech stack

- Node.js and Express
- Sequelize with a SQL database, set through environment variables (the `mssql` and `mysql2` drivers are included)
- JWT authentication with 24-hour tokens, plus role checks on protected routes
- bcrypt for password hashing
- Joi for request validation
- xlsx for Excel reports

## API overview

All routes start with `/api/v1`. The API has 34 endpoints:

| Area | Path | Endpoints | Main access |
| --- | --- | --- | --- |
| Auth | `/auth` | 2 | Public |
| Public | `/public` | 1 | Public |
| Nursing | `/nursing` | 5 | Nursing |
| Technician | `/technician` | 13 | Technician |
| Super admin | `/superAdmin` | 13 | Super admin |

Some routes are shared between roles. For example, technicians can also list freezers and use search.

## Getting started

You need Node.js, npm and a SQL database.

```bash
git clone https://github.com/7aaj/KHCCBE.git
cd KHCCBE
npm install
mkdir StudiesFiles
```

Create a `.env.local` file:

```env
PORT=5000
JWT_SECRET=your-secret
LOCAL_DB_NAME=
LOCAL_DB_USER=
LOCAL_DB_PASSWORD=
LOCAL_DB_HOST=localhost
LOCAL_DB_DIALECT=mysql
LOCAL_FILE_BASE_URL=http://localhost
```

Tables are created on startup with `sequelize.sync()`.

| Command | Use |
| --- | --- |
| `npm run devStart` | Local development with nodemon |
| `npm start` | Local run without nodemon |
| `npm run prodStart` | Production, using `.env.production` |

Production uses the same `PORT` and `JWT_SECRET` names. The database and file URL values use `PROD_` in place of `LOCAL_`.

## Project structure

```text
Controllers/       Request handlers, grouped by role
DataBase/          Sequelize connection
models/            Sequelize models
routes/            API routes, grouped by role
Utils/             JWT, file upload, audit log and sample type helpers
validation/        Validation and role middleware
validationSchema/  Joi request schemas
index.js           App entry point
```

## Contributors

Built by [Mustafa Mahmood](https://github.com/Mustafa-kl5) and [Mohammad Al-Haj](https://github.com/7aaj). This repository is Mohammad's fork of [Mustafa-kl5/KHCCBE](https://github.com/Mustafa-kl5/KHCCBE).

Mohammad's work on this API includes:

- Freezer endpoints to add, list and delete freezers
- The technician patient list and mark-as-seen flow
- Patient deletion with a recorded reason
- Search and filters for patients, samples, studies, users and logs
