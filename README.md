# Topcoder Resources API – Automated Test Suite

This project provides a full set of **automated test cases** to validate the behavior of the `/resources` and `/resource-roles` endpoints in Topcoder's API. The test suite compares responses between the **local environment** (branch `PM-437`) and the **deployed production environment**.

---

## ⚙️ Local Setup (`resources-api`)

### 1. Install dependencies

```bash
npm install
source env.sh
```

### 2. Start required services

```bash
cd local
docker-compose up -d
```

### 3. Create database tables

```bash
npm run create-tables
```

---

## 🛠️ Data Migration

Use the \[`resources-migration-tool-fixed`] to populate the local DB with valid data from Topcoder-exported JSON files.

```bash
npm install
node src/migrateAll.js
```

---

## 🚀 Launch API and Mock Server

Start the local server in one terminal:

```bash
npm start
```

In a second terminal:

```bash
source env.sh
npm run mock-challenge-api
```

---

## 🔐 Token Simulation for Local Testing

### 1. Install JWT dependency

You must install the required package to enable token generation:

```bash
npm install jsonwebtoken
```

### 2. Generate tokens

The local API requires valid JWT tokens with specific roles. Generate tokens with:

```bash
node generate-token.js "<role>" <handle> [userId]
```

### Accepted roles:

* "administrator"
* "copilot"
* "Connect Manager"
* "Topcoder User"

### Examples:

```bash
node generate-token.js "administrator" mess
node generate-token.js "copilot" TCConnCopilot
node generate-token.js "Topcoder User" Ghostar
```

Tokens are valid if `AUTH_SECRET` is set to `"secret"`.

![Screenshot from 2025-05-20 20-53-26](https://github.com/user-attachments/assets/d59997be-74a2-4e28-9184-b15238a3f53f)

Copy and paste into `.env` as:

```env
TOKEN_ADMIN_LOCAL=<admin-token>
TOKEN_USER_LOCAL=<user-token>
TOKEN_USER_TEST_LOCAL=<test-user-token>
```

Respectively.

For deployed tokens:

```env
TOKEN_ADMIN_DEPLOYED=<admin-token>
TOKEN_USER_DEPLOYED=<user-token>
```

Use the following credentials for [https://api.topcoder-dev.com](https://api.topcoder-dev.com):

* **Admin**: `mess` / `appirio123`
* **User**: `jgasperMobile22` / `kong1cuct4BROG!priy`

🚫 **Do NOT change the passwords!** Anyone who changes passwords will be banned from future challenges.

### How to get the deployed tokens:

1. Go to [https://challenges.topcoder-dev.com](https://challenges.topcoder-dev.com) and log in with the credentials above.
2. Open browser developer tools and inspect the network requests.
3. Look for a request to `https://api.topcoder-dev.com/v5` (e.g., `/challenge-types`).
4. Copy the Bearer token from the request headers.

Use the token from `mess` for `TOKEN_ADMIN_DEPLOYED`, and the token from `jgasperMobile22` for `TOKEN_USER_DEPLOYED`.

### Usage in manual API calls:

```bash
curl http://localhost:3000/v5/resources/count \
  -H "Authorization: Bearer <PASTE_TOKEN_HERE>"
```

---

## ✅ Test Execution

The test suite compares API behavior across:

* Deployed API (`https://api.topcoder-dev.com/v5`)
* Local API (`http://localhost:3000/v5`)

Each test logs:

* Request method, endpoint, and sample data
* Status code and response comparison
* Pass/Fail based on expected behavior

---

## 📂 Output

Test results can be exported to an `.xlsx` file for submission or review.

---

## 📁 Suggested Folder Structure

```
resources-api-tests/
├── run_all.py                  # main test runner
├── request_helper.py           # generic make_request() function
├── test_data.py                # shared constants (IDs, handles, etc.)
├── test_cases/
│   └── test_resources.py       # full test suite
├── output/
│   └── test_results.xlsx       # optional export
```

---

## 📌 Notes

* Tests that mutate data (POST, DELETE) should be run in a clean environment.
* Ensure role and phase-dependency IDs are unique or resettable.
