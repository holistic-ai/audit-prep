---
name: holistic-audit-prep
description: Write the Holistic AI audit preparation pack (audit-prep/ with audit-prep.json and the API documentation) for a bias audit of a hiring or promotion system, from this repo. Use when the person asks to prepare a Holistic AI audit or LL144 bias audit.
---

# Audit preparation pack: instructions for a coding agent

Holistic AI is preparing a bias audit of a hiring or promotion system. You read this repo and
write an audit preparation pack. A person in the client's company uploads the pack to Holistic AI
and confirms each answer. Nothing in the pack is used until that person accepts it.

The pack is one folder. The person drops the whole folder on the Holistic AI intake, under
"Let a coding agent draft it". Each file has a fixed name:

```
audit-prep/
  audit-prep.json            the proposed intake answers
  api-documentation.<ext>    the API documentation, only when the system has an API
```

The folder holds no other file. The check refuses a file with another name.

Work only in this repo, and in another repo or file only if the person names it. Write nothing
outside the `audit-prep/` folder. Send nothing over the network.

## Never include

- A credential value: an API key, token, password, private key or connection string. Do not open
  `.env` files, secret stores, key files or CI secrets. Name a credential, never copy its value.
- Personal data: names, email addresses or phone numbers of candidates, employees or users. If a
  file seems to hold real candidate or employee records, leave it out and add a warning that names
  the file.
- Team members or commit authors. A person types the audit team. Do not read `git log` for names.
- A dataset. The person uploads the audit data separately.

## One system per pack

A pack describes one system and its one API. First find out which systems this repo holds. If it
holds more than one, for example several products, models or scoring APIs, stop before you write
anything. List the systems you found, with one line and the main files for each, and ask the
person which one the audit covers. Write the pack only for the system the person names.

## What to read

- The documents the person names in the prompt, after `Your documents:`, such as a model card,
  a validation study or a product description. Read each one first and draft answers from it.
  Give the document and its page or section as the evidence. A document often describes the
  system in its own terms: answer our question, not the document's. Leave a question `null`
  when the documents and the repo do not answer it.

- The README, docs, and any product or model description.
- The API: OpenAPI or Swagger files, route and handler code, gateway or proxy configuration,
  client SDKs, API tests and example requests.
- The scoring code: what the system outputs (a score, a rank, a fit label), the score names and
  ranges, and the methods (machine learning, statistics, rules).
- Dependency files, to see which methods the system uses.

## Write the pack

Write `audit-prep/audit-prep.json`. It must match `audit-prep.schema.json`, which comes with this
file. Each question in the schema carries its text and its allowed option ids.

- `packVersion`: 3.
- `answers`: one entry per question you can answer, with:
  - `value`: the proposed answer, or `null` when the repo does not answer it. Use the option ids
    from the schema. Write dates as `YYYY-MM-DD`.
  - `evidence`: every file you read for it, with the lines, for example
    `{ "path": "src/score.ts", "lines": "10-42" }`. An answer with no evidence is refused.
  - `confidence`: `high`, `medium` or `low`.
  - `needsPerson`: `true` when a person must give or check the answer.
  - `note`: what the person should check.
- The schema leaves out the answers that only a person gives: the regulations, who qualifies, the
  California scope, the team and the signatory. Do not add them.
- The company description and the ten parts of the system description reach the audit report.
  Write them as they should appear in public: plain language, third person, full sentences. For a
  part that does not apply, such as pilot data for a system that is not an assessment, write "Not
  applicable" and the reason in one line. Use `null` only when the repo does not say.
- `scores` has one row for each score: its name, the dataset column that holds it, its range
  ("0 to 100", or "0 or 1" for a yes or no outcome), and its scale, direction and use.
- Only fill a business answer (dates, the company, the uses, the data choice, the region) when a
  file states it. A guess is worse than `null`. These answers always go to a person.
- The API answers, when Holistic AI calls the system's API (`testing_method` `api`):
  - `api_base_url` and `api_sandbox_url`: the production address and any test environment.
    Give them without a query string (`?...`) or a fragment (`#...`), because a query string
    can hold a key.
  - `api_auth_method`, `api_auth_header` and `api_auth_prefix`: how the API checks who is
    calling, the header that carries the key, and the text before the key (for example `Token `
    in `Authorization: Token <key>`). `oauth_client_credentials` is an OAuth token service that
    takes a client ID and a client secret. `api_auth_scope` is its scope, only when the token
    request needs one. The person types the token service address, the client ID and the secret
    in the platform, never in the pack.
  - `api_headers`: other headers on every request, such as `Content-Type`. Never a key. When
    the API wants another credential in its own header next to the sign-in, such as a
    subscription key, name that header in `warnings`, never its value: the person adds it in the
    platform with "Add another credential".
  - `api_steps`: one row for each call, in the order Holistic AI makes them: what it does, the
    method, the path, how the result comes back (`sync`, `polling` or `callback`), and an
    example request and response, each as JSON text. Put the call that returns the score first
    when the flow has one call.
  - `api_score_field`: the response field that holds the score, and its step, for example
    `match_score, in step 1`.
  - `api_rate_limits`, `api_determinism`, `api_guardrails`, `api_errors` and
    `api_test_data` when the code or a document states them.
  - Fill them from the repo: OpenAPI or Swagger files, route handlers, API tests, client SDKs. If
    the prompt names an API guide (`API guide: <path>`), read it first: a PDF, Word or wiki guide
    often holds the steps and the examples.
  - Never write a credential value in an answer or an example. Use a placeholder such as
    `<SCORING_API_KEY>`. The person types the key in the platform's encrypted key field.
- `apiDocumentationFile`: write `audit-prep/api-documentation.md`, or copy the OpenAPI file as
  `audit-prep/api-documentation.json`, `.yaml`, `.yml` or `.txt`. Give
  each endpoint, its request and response with an example, the scores it returns and their ranges,
  the sign-in steps, and the limits. Use placeholders such as `<SCORING_API_KEY>` for credentials,
  in headers (`Authorization: Token <SCORING_API_KEY>`), in request bodies and in query
  parameters (`?code=<FUNCTION_KEY>`). `${NAME}` and `{{NAME}}` also work as placeholders.
  Give its file name as `path`, and list the files it comes from.
- `warnings`: anything the person must know, such as a file with personal data that you left out.

## Before you finish

1. Check the pack against `audit-prep.schema.json` if a JSON Schema validator is available.
2. Search the `audit-prep/` folder for anything that looks like a key, a token or a password,
   including in file names, notes and warnings. Replace it with a placeholder. The upload warns
   about text that looks like a credential, and refuses a key the schema does not list.
3. Make sure `audit-prep/` holds only the files listed above.
4. Tell the person which answers need them. They open the audit in the Holistic AI client app,
   choose "Let a coding agent draft it" and drop the `audit-prep` folder there.
