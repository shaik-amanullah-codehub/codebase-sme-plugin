# Knowledge File Schema: `.sme/codebase-knowledge.json`

This is the single source of truth for the structure the `/sme-scan` command produces
and that every other command reads. It is intentionally language-agnostic — it works
for .NET, Java, Node, Python, etc. Field values adapt to the stack detected.

Every factual field must be backed by something actually observed in the code. Where a
value is inferred rather than confirmed, it must be marked (see `confidence` fields).

```json
{
  "schema_version": "1.0",
  "project": {
    "name": "string",
    "scanned_at": "ISO-8601 timestamp",
    "root_path": "string",
    "primary_language": "string",
    "stack": {
      "framework": "e.g. ASP.NET Core 8, Spring Boot 3, Express",
      "orm_or_data_access": "e.g. EF Core, Dapper, Hibernate, Prisma, raw SQL",
      "database": "e.g. SQL Server, PostgreSQL — or 'unknown' if not determinable",
      "build_tool": "e.g. dotnet, maven, npm",
      "detected_from": "how the stack was identified, e.g. 'csproj + Startup.cs'"
    },
    "stats": {
      "total_source_files": 0,
      "total_lines_estimate": 0,
      "files_scanned": 0,
      "files_skipped": 0,
      "skip_reason": "e.g. generated code, vendored libraries, migrations"
    }
  },

  "modules": [
    {
      "id": "stable-slug-like-order-module",
      "name": "OrderModule",
      "responsibility": "one-sentence description of what this module does",
      "layer": "e.g. api | application | domain | infrastructure | ui | shared",
      "key_files": [
        { "path": "relative/path/File.cs", "role": "controller | service | repository | model | config | other" }
      ],
      "entry_points": [
        { "type": "http_endpoint | cli_command | background_job | event_handler | scheduled",
          "signature": "e.g. POST /api/orders",
          "handler": "OrderController.SubmitOrder",
          "file": "relative/path/OrderController.cs" }
      ],
      "public_surface": [
        "the classes/methods other modules are expected to call into"
      ],
      "depends_on_modules": ["ids of modules this one calls"],
      "external_dependencies": [
        { "name": "Stripe", "kind": "payment_gateway | http_api | queue | cache | email | filestore | other",
          "used_in": "relative/path/PaymentGateway.cs", "confidence": "confirmed | inferred" }
      ],
      "database_access": [
        { "entity_or_table": "Orders",
          "operations": ["create", "read", "update", "delete"],
          "access_via": "OrderRepository",
          "confidence": "confirmed | inferred" }
      ],
      "patterns_observed": ["Repository", "Dependency Injection", "CQRS", "Unit of Work"],
      "risk": {
        "level": "low | medium | high",
        "reasons": ["no tests found", "high fan-in", "raw SQL string concatenation", "legacy — last touched long ago"]
      },
      "notes": "anything a senior engineer would flag to a newcomer about this module"
    }
  ],

  "module_interactions": [
    { "from": "order-module", "to": "payment-module",
      "via": "OrderService.Checkout -> PaymentService.Charge",
      "interaction_type": "direct_call | event | queue | http | shared_db",
      "confidence": "confirmed | inferred" }
  ],

  "data_model": {
    "tables_or_entities": [
      { "name": "Orders",
        "defined_in": "relative/path/Order.cs",
        "key_fields": ["Id", "CustomerId", "Status"],
        "related_to": ["OrderItems", "Payments"],
        "accessed_by_modules": ["order-module", "reporting-module"],
        "confidence": "confirmed | inferred" }
    ],
    "notes": "migrations location, seeding, multi-db, etc."
  },

  "cross_cutting": {
    "auth": "how authn/authz is handled and where",
    "logging": "logging approach and where configured",
    "error_handling": "global handlers, middleware, filters",
    "configuration": "where config/secrets are read from (name the mechanism, never the secret values)",
    "validation": "where/how input validation happens"
  },

  "conventions": [
    "Services are suffixed with 'Service' and registered in DI",
    "Controllers inherit from BaseApiController",
    "Repositories implement IRepository<T>"
  ],

  "test_coverage_signals": {
    "test_projects_found": ["path/to/Tests"],
    "modules_with_tests": ["order-module"],
    "modules_without_tests": ["legacy-reporting-module"],
    "note": "this is a heuristic signal from file presence, NOT a coverage measurement"
  },

  "open_questions": [
    "Things the scan could not determine and a human should clarify — e.g. 'PaymentGateway target environment not found in config'"
  ],

  "glossary_candidates": [
    { "term": "Commitment", "guessed_meaning": "appears to mean a confirmed order", "confidence": "inferred" }
  ]
}
```

## Rules for whatever writes this file

- Every `confidence` field must be honestly set. Default to `inferred` unless the code directly proves it.
- The `open_questions` array must not be empty for a real codebase — there are always ambiguities. An empty array is a red flag that the scan overclaimed certainty.
- Never write secrets, tokens, passwords, or connection strings into this file. Reference their location only.
- Keep it compact: this file is meant to be cheap to read on every query. Prefer stable ids and short strings over prose.
