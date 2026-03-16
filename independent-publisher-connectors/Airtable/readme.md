# Airtable

Airtable is a cloud-based platform that combines the simplicity of spreadsheets with the power of databases, enabling teams to manage projects, CRM, inventory, content calendars, and more. This connector lets you discover bases, read and write records with powerful filtering and sorting, manage record comments, and react to new data via a polling trigger — all from your automated workflows.

## Publisher

### Aaron Mah

## Prerequisites

You need an Airtable account (Free, Team, Business, or Enterprise plan). To use this connector, create a **Personal Access Token (PAT)**:

1. Go to [https://airtable.com/create/tokens](https://airtable.com/create/tokens)
2. Click **"Create new token"**
3. Name it (e.g., `Power Automate`)
4. Add the following scopes:
   - `data.records:read` — Read records
   - `data.records:write` — Create, update, and delete records
   - `data.recordComments:read` — Read record comments
   - `data.recordComments:write` — Create comments
   - `schema.bases:read` — List bases and get table schemas
   - `user.email:read` — Get user info (connection validation)
5. Under **Access**, click **"Add a base"** and select the bases you want to use (or choose **"All current and future bases in all current and future workspaces"**)
6. Click **"Create token"** and copy the token immediately (it won't be shown again)
7. When creating a connection in your flow, enter: `Bearer patYOUR_TOKEN_HERE`

**Note:** Free-plan accounts are limited to 1,000 API calls per month. Team plans allow 100,000 calls/month. Business and Enterprise plans have no monthly API limit.

## Supported Operations

### List Bases
Lists all Airtable bases accessible to the authenticated token. Use this to discover base IDs before reading or writing records.

### Get Base Schema
Returns the full schema of an Airtable base including all tables, fields (with types and options), and views. Use this to discover field names for building filter formulas or record payloads.

### List Records
Lists records in an Airtable table with optional filtering, sorting, and view scoping. Supports Airtable's `filterByFormula` syntax for powerful queries (e.g., `{Status}='Active'`, `AND({Priority}='High', {Due Date}<TODAY())`). Returns up to 100 records per page with offset-based pagination.

### When a Record Is Created (Polling Trigger)
Polls for new records in an Airtable table or view. When a new record is created — or when a record newly enters a filtered view due to a status change — the trigger fires and runs the flow for each new record. To detect status changes, create an Airtable view filtered to the target status (e.g., "Blocked Items") and point the trigger's **View** parameter at it. Records are sorted by creation time (newest first) by default.

### Get Record
Retrieves a single Airtable record by its ID. Use this after a trigger fires to get full record details, or to verify a record exists before updating it.

### Create Record
Creates a new record in an Airtable table. Pass field values as key-value pairs in the request body. Enable **Typecast** to auto-convert string values to the appropriate cell type (e.g., "5" becomes a number).

### Update Record
Updates an existing record's fields using a partial update (PATCH). Only the specified fields are changed; unspecified fields are left intact. Use this to update statuses, add notes, or set assignees.

### Delete Record
Deletes a record from an Airtable table by record ID. The response confirms deletion with a `deleted: true` flag.

### List Comments
Lists comments on a specific Airtable record, newest first. Use this to pull discussion history into reports or Teams messages. Supports offset-based pagination.

### Create Comment
Adds a text comment to an Airtable record. Use this for automated audit notes — log approval decisions, sync activity, or status change reasons as comments.

### Get User Info
Returns the authenticated user's ID and email address. Useful for verifying the connection is working and identifying the token owner.

## API Documentation

Visit the [Airtable Web API Reference](https://airtable.com/developers/web/api) for full endpoint documentation, formula syntax, and field type details.

## Known Issues and Limitations

- **Dynamic field schema:** Airtable tables have user-defined fields, so the `fields` object in record responses is dynamic. You may need to use the **Parse JSON** action to extract typed field values in your flows.
- **Rate limits:** Airtable allows 5 requests per second per base and 50 requests per second per user. If rate-limited (HTTP 429), wait 30 seconds before retrying.
- **Free plan API limit:** Free-plan accounts are blocked from API access after exceeding 1,000 calls per month (enforced since December 2024).
- **Pagination:** List operations return up to 100 records per page. Pass the `offset` token from the response to get the next page. When `offset` is absent, all records have been returned.
- **Attachment URLs are temporary:** Attachment field URLs expire after a few hours. Download or process attachments promptly.
- **Field name changes:** If a field is renamed in Airtable, existing flows using the old field name will break. Use the **Get Base Schema** operation to discover current field names.
- **Single record delete:** The Delete Record operation deletes one record per call. For bulk deletion, use an **Apply to Each** loop.

## License

Distributed under the MIT License.
