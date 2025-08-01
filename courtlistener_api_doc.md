---
# CourtListener API Integration Reference for Claude Code

This document unifies the relevant documentation from CourtListener’s API, webhooks, and integration pages to assist in building or debugging your application that interfaces with CourtListener’s REST API.

---

## 1. API Authentication

**Token Access:**  
To use the CourtListener API, you must obtain an API token from:
- [https://www.courtlistener.com/profile/api-token/](https://www.courtlistener.com/profile/api-token/)

**Usage:**  
Include the token as a header in your requests:
```http
Authorization: Token YOUR_API_TOKEN
```

---

## 2. REST API Overview

API Root: [https://www.courtlistener.com/api/rest/v4/](https://www.courtlistener.com/api/rest/v4/)

Documentation: [https://www.courtlistener.com/help/api/rest/](https://www.courtlistener.com/help/api/rest/)

**Available Endpoints Include:**
- `/opinions/`
- `/dockets/`
- `/citations/`
- `/people/`
- `/courts/`
- `/jurisdictions/`
- `/recap-documents/`

### Common Parameters
- `format=json` (default)
- `ordering`: `dateFiled`, `dateModified`, etc.
- `search`: full-text keyword search (depends on endpoint)
- `court`: use Court IDs (from `/courts/`)
- `jurisdiction`: use `/jurisdictions/` to lookup IDs

### Pagination
- Responses are paginated (default: 20 items/page)
- Use `?page=n` to navigate

---

## 3. Webhooks

Webhook configuration UI: [https://www.courtlistener.com/profile/webhooks/](https://www.courtlistener.com/profile/webhooks/)

Docs: [https://www.courtlistener.com/help/api/webhooks/](https://www.courtlistener.com/help/api/webhooks/)

### Purpose
Webhooks allow CourtListener to notify your app when certain events occur, such as updates to dockets or opinions.

### Creating a Webhook
- Create via the profile page or API (TBD)
- Must point to a publicly accessible HTTPS endpoint on your server

### Example Payload
```json
{
  "event": "docket-update",
  "docket_id": "123456",
  "timestamp": "2025-08-01T13:45:00Z"
}
```

### Event Types
- `docket-update`
- `opinion-update`

### Security
- All requests are POST
- Include an `X-CourtListener-Signature` header (HMAC-SHA256 using your API token)

Verify signature server-side:
```python
import hmac, hashlib
signature = hmac.new(api_token, payload_body, hashlib.sha256).hexdigest()
```

---

## 4. API Usage Tips & Common Issues

### Rate Limiting
- API is rate-limited. Heavy scraping may result in bans.
- Limit concurrent requests, respect pagination.

### Error Codes
- 400: Bad request
- 401: Unauthorized (check API token)
- 403: Forbidden (check webhook permission)
- 404: Not found
- 429: Too Many Requests

### Headers to Always Include
```http
Accept: application/json
Authorization: Token YOUR_API_TOKEN
```

### Debugging Webhooks
- Inspect request headers and payloads using ngrok or webhook.site
- Log `X-CourtListener-Signature` and raw body for debugging signature issues

### Common Pitfalls
- Forgetting to validate webhook signatures
- Using HTTP instead of HTTPS for webhook URLs
- Failing to paginate responses
- Not filtering by court/jurisdiction when needed

---

## 5. Development Checklist

- [ ] API Token stored securely
- [ ] REST API endpoints integrated with pagination
- [ ] Webhook URL publicly accessible and SSL-enabled
- [ ] Signature validation using `X-CourtListener-Signature`
- [ ] Rate-limiting handled with backoff
- [ ] Unit tests for endpoint consumers and webhook handlers

---

## 6. Useful Links

- API Token: [https://www.courtlistener.com/profile/api-token/](https://www.courtlistener.com/profile/api-token/)
- Webhooks Dashboard: [https://www.courtlistener.com/profile/webhooks/](https://www.courtlistener.com/profile/webhooks/)
- Webhooks Guide: [https://www.courtlistener.com/help/api/webhooks/](https://www.courtlistener.com/help/api/webhooks/)
- REST API Docs: [https://www.courtlistener.com/help/api/rest/](https://www.courtlistener.com/help/api/rest/)
- REST v4 Root: [https://www.courtlistener.com/api/rest/v4/](https://www.courtlistener.com/api/rest/v4/)

---

