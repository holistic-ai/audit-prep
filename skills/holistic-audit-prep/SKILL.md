---
name: holistic-audit-prep
description: Write the Holistic AI audit preparation pack (audit-prep/audit-prep.json and the API documentation) for a bias audit of a hiring or promotion system, from this repo. Use when the person asks to prepare a Holistic AI audit or LL144 bias audit.
---

# Audit preparation pack: instructions for a coding agent

Holistic AI is preparing a bias audit of a hiring or promotion system. You read this repo and
write an audit preparation pack. A person in the client's company uploads the pack to Holistic AI
and confirms each answer. Nothing in the pack is used until that person accepts it.

Work only in this repo, and in another repo only if the person names it. Write nothing outside
the `audit-prep/` folder. Send nothing over the network.

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

- The README, docs, and any product or model description.
- The API: OpenAPI or Swagger files, route and handler code, gateway or proxy configuration,
  client SDKs, API tests and example requests.
- The scoring code: what the system outputs (a score, a rank, a fit label), the score names and
  ranges, and the methods (machine learning, statistics, rules).
- Dependency files, to see which methods the system uses.

## Write the pack

Write `audit-prep/audit-prep.json`. It must match `audit-prep.schema.json`, which comes with this
file. Each question in the schema carries its text and its allowed option ids.

- `packVersion`: 1.
- `answers`: one entry per question you can answer, with:
  - `value`: the proposed answer, or `null` when the repo does not answer it. Use the option ids
    from the schema. Write dates as `YYYY-MM-DD`.
  - `evidence`: every file you read for it, with the lines, for example
    `{ "path": "src/score.ts", "lines": "10-42" }`. An answer with no evidence is refused.
  - `confidence`: `high`, `medium` or `low`.
  - `needsPerson`: `true` when a person must give or check the answer.
  - `note`: what the person should check.
- The schema leaves out the answers that only a person gives: the regulations, who qualifies, the
  California scope and the team. Do not add them.
- Only fill a business answer (dates, the company, the uses, the data choice, the region) when a
  file states it. A guess is worse than `null`. These answers always go to a person.
- `api`: the base URL you propose (Holistic AI confirms it), its environment, the endpoints, the
  sign-in kind, the parameter that carries the credential, the text before it in a header (for
  example `Token ` in `Authorization: Token <key>`), the credential NAMES in capital letters
  (for example `SCORING_API_KEY`), the request limits, and any other host the flow calls. Use
  `null` when there is no API. Give the base URL and the endpoint paths without a query string
  (`?...`) or a fragment (`#...`), because a query string can hold a key. If the API takes the
  credential as a query parameter, put the parameter name in `parameterName`.
- `apiDocumentationFile`: write `audit-prep/api-documentation.md` (or copy the OpenAPI file). Give
  each endpoint, its request and response with an example, the scores it returns and their ranges,
  the sign-in steps, and the limits. Use placeholders such as `<SCORING_API_KEY>` for credentials,
  in headers (`Authorization: Token <SCORING_API_KEY>`), in request bodies and in query
  parameters (`?code=<FUNCTION_KEY>`). `${NAME}` and `{{NAME}}` also work as placeholders.
  Point to it with a path relative to `audit-prep/`, and list the files it comes from.
- `warnings`: anything the person must know, such as a file with personal data that you left out.

## Before you finish

1. Check the pack against `audit-prep.schema.json` if a JSON Schema validator is available.
2. Search the `audit-prep/` folder for anything that looks like a key, a token, a password or an
   email address, including in file names, notes and warnings. Replace it with a placeholder.
   The check refuses a pack that has one anywhere, or that has a key the schema does not list.
3. Tell the person which answers need them, and that they upload the folder in the Holistic AI
   client app.
