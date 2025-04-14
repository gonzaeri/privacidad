# Topcoder Resources Data Migration Tool

This tool is designed to **migrate data from DynamoDB (JSON format) to PostgreSQL** using **Prisma ORM**. It covers five key models of the Topcoder Resources API:

- `MemberProfile`
- `MemberStats`
- `ResourceRole`
- `Resource`
- `ResourceRolePhaseDependency`

## 📦 Technologies Used
- **Node.js** (backend scripting)
- **Prisma ORM** (PostgreSQL schema management)
- **PostgreSQL 16.3** (Dockerized database)
- **Docker & Docker Compose** (for DB setup)
- **stream-json / readline** (for streaming JSON migration)
- **Jest** (unit testing framework)

## ⚙️ Environment Configuration
Create a `.env` file in the root directory:

```env
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/resourcesdb"
CREATED_BY="resources-api-db-migration"
```

> The `CREATED_BY` field can be overridden at runtime:
```bash
CREATED_BY=eduardo node src/index.js member-stats ./data/MemberStats_test.json
```

## 🚀 How to Run
```bash
npm install
npm test                     # To run tests
npm run db:up                # To start PostgreSQL via docker-compose
npx prisma db push           # To apply schema
node src/index.js <step>     # To run a specific migration
```

Optionally, provide a custom path:
```bash
node src/index.js member-profiles ./data/MemberProfile_test.json
```

## 🧩 Available Migration Steps

| Step                                | Auto Strategy | Description                                                                                       |
|-------------------------------------|---------------|---------------------------------------------------------------------------------------------------|
| `member-profiles`                  | ✅            | Auto strategy: uses `stream-json` (batch) for files larger than 3MB, and `loadJSON` (simple) otherwise |
| `member-stats`                     | ✅            | Auto strategy: uses `stream-json` (batch) for files larger than 3MB, and `loadJSON` (simple) otherwise |
| `resource-roles`                   | ❌            | Simple in-memory migration using `loadJSON`, not expected to be large                             |
| `resource-role-phase-dependencies` | ❌            | Simple in-memory migration using `loadJSON`, not expected to be large                             |
| `resources`                        | ✅            | Auto strategy for NDJSON files: uses `readline` + batch for files > 3 MB, otherwise simple line-by-line       |

### 📁 Default Input Files per Migration Step

The following files are used by default for each step, unless a custom path is provided via the CLI:

| Step                                | Default File Path                                             |
|-------------------------------------|----------------------------------------------------------------|
| `member-profiles`                  | `./data/MemberProfile_dynamo_data.json`                       |
| `member-stats`                     | `./data/MemberStats_dynamo_data.json`                         |
| `resource-roles`                   | `./data/ResourceRole_dynamo_data.json`                        |
| `resource-role-phase-dependencies` | `./data/ResourceRolePhaseDependency_dynamo_data.json`         |
| `resources`                        | `./data/Resource_data.json`                                   |

💡 **Note:** If you're using the original file provided in the forum (`challenge-api.resources.json` from [this link](https://drive.google.com/file/d/1F8YW-fnKjn8tt5a0_Z-QenZIHPiP3RK7/view?usp=sharing)), you must run the `resources` step explicitly with its path:

```bash
node src/index.js resources ./data/challenge-api.resources.json

## 📒 Error Logs
All failed migrations are logged under the `logs/` folder by model:
- `logs/memberprofile_errors.log`
- `logs/memberstats_errors.log`
- `logs/resourcerole_errors.log`
- `logs/resource_errors.log`
- `logs/resourcerolephasedependency_errors.log`

## ✅ Verification
You can verify successful migration with simple SQL queries, for example:
```sql
SELECT COUNT(*) FROM "MemberProfile";
SELECT COUNT(*) FROM "Resource";
```
To connect:
```bash
docker exec -it resources_postgres psql -U postgres -d resourcesdb
```

## 📸 Screenshots
See `/docs/` for evidence of a fully mounted database.
![Screenshot from 2025-04-14 16-58-20](https://github.com/user-attachments/assets/8fb66fb8-3db1-4b51-bb29-c1db7b207689)

## 📝 Notes
- The `resources` data was exported from ElasticSearch and uses NDJSON format. Each line must be a valid JSON object.
- Its expected path is `./data/Resource_data.json`, not the original `challenge-api.resources.json`.
- The `Auto` suffix in migrators means they **automatically choose** between batch (streamed) or simple (in-memory) strategy based on file size.

---

✅ All requirements of the challenge have been implemented, including logs, unit tests, schema adherence, and configurability.

