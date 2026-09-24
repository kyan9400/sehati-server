# Sehati Server

Backend API for **Sehati**, a clinic management platform. This service is a small Node.js / Express 5 server backed by MongoDB (Mongoose) that currently provides JWT-based login for the Sehati front-ends:

- [sehati-client](https://github.com/kyan9400/sehati-client) - the clinic dashboard (React + TypeScript)
- [sehati-landing-client](https://github.com/kyan9400/sehati-landing-client) - the marketing landing page

## Stack

- Node.js, Express 5
- MongoDB via Mongoose 8
- `jsonwebtoken` for access tokens, `bcryptjs` for password hashing
- `cors`, `dotenv`
- `nodemon` for local development

## Getting started

```bash
git clone https://github.com/kyan9400/sehati-server.git
cd sehati-server
npm install
cp .env.example .env   # then fill in the values
npm run dev            # development (nodemon, auto-restart)
npm start              # production
```

The server listens on `http://localhost:5000` by default.

## Environment variables

Copy `.env.example` to `.env` and set:

| Variable     | Required | Description                                                        |
| ------------ | -------- | ------------------------------------------------------------------ |
| `MONGO_URI`  | yes      | MongoDB connection string (for example a MongoDB Atlas URI).       |
| `JWT_SECRET` | yes      | Secret used to sign JWTs. Use a long, random string.               |
| `PORT`       | no       | Port to listen on. Defaults to `5000`.                             |

Never commit `.env`; it is ignored by `.gitignore`.

## API

| Method | Path              | Description                                  |
| ------ | ----------------- | -------------------------------------------- |
| `GET`  | `/`               | Health/welcome message.                      |
| `POST` | `/api/auth/login` | Log in with email and password, returns JWT. |

### `POST /api/auth/login`

Request body:

```json
{ "email": "user@example.com", "password": "secret" }
```

Successful response (`200`):

```json
{
  "token": "<jwt, expires in 1h>",
  "user": { "id": "...", "name": "...", "email": "...", "role": "patient" }
}
```

Invalid credentials return `400` with `{ "message": "Invalid credentials" }`.
The token payload contains `userId` and `role`.

There is no registration endpoint yet: users are created directly in the database with a bcrypt-hashed `password`.

## Data model

`User` (`models/User.js`):

| Field       | Type   | Notes                                                |
| ----------- | ------ | ---------------------------------------------------- |
| `name`      | String | required, trimmed                                    |
| `email`     | String | required, unique, stored lowercase                   |
| `password`  | String | required, bcrypt hash                                |
| `role`      | String | defaults to `"patient"` (`"doctor"`, `"admin"` also used) |
| timestamps  |        | `createdAt` / `updatedAt` added automatically         |

## Project structure

```
server.js        # Express app, MongoDB connection, route mounting
routes/auth.js   # POST /api/auth/login
models/User.js   # Mongoose User schema
.env.example     # environment variable template
```

## License

MIT - see [LICENSE](LICENSE).
