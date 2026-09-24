# Handover

- Name: Somesh R
- Email used for this application:somesh1715@gmail.com
- Chosen track: Track A — Repair the register
- Why this track (one or two sentences): I chose Track A to investigate and repair defects in the invoice register, verify the fixes with tests and changed-input cases, and make one small usability improvement.

## Run and verify

Prerequisite: Python 3.10+.

Run the application:

    python app.py

Open:

    http://127.0.0.1:8787

I ran:

    python -m unittest discover -s tests -v

The final test run completed successfully. The test suite verifies invoice import, payment import, seed repeatability, invoice status filtering, export output, and payment matching.

Reset the demo data when needed:

    python app.py reset-demo

No third-party dependencies or credentials are required.

## What I delivered

I investigated and repaired the seeded defects in the invoice register, including:

- Invalid CSV rows no longer stop valid rows from being imported.
- Duplicate invoices with the same identity are skipped, while conflicting details are rejected.
- Invoice/payment matching uses customer ID and invoice number rather than payment amount.
- Open and paid invoice filters return the correct records.
- CSV export preserves monetary values to two decimal places.
- Browser import handling now distinguishes successful and failed HTTP responses.

I also added a stronger regression test for payment matching and improved the import feedback so rejected CSV rows display their line number and validation reason.

Relevant areas:
- `ledger/importing.py`
- `ledger/storage.py`
- `ledger/matching.py`
- `ledger/reporting.py`
- `ledger/validation.py`
- `web/app.js`
- `tests/test_smoke.py`

## Evidence and limits

I ran:

    python -m unittest discover -s tests -v

The test suite verifies invoice import, payment import, seed repeatability, invoice status filtering, export output, and payment matching.

Failing-before/passing-after reproduction:
The original payment matching logic searched for an invoice by payment amount before checking the invoice identity. I changed it to match using `customer_id + invoice_number` and added a regression test where the payment amount differs from the invoice amount.

For the CSV validation defect, a mixed-input CSV containing two valid rows and one invalid amount was used. Expected behaviour was that the two valid rows would import while the invalid row would be rejected with its line and reason.

I also checked the existing demo register after reset to verify that the seeded records remain available.

Known limits: I did not perform a large-volume or concurrent-user test. In a real project, I would investigate database-level uniqueness constraints for invoice identity and broader end-to-end testing.

The separate improvement is clearer display of rejected CSV rows and their validation reasons after a partial import.

## Tools and judgment

- AI-assisted investigation suggested checking the import flow first -> I inspected the relevant modules and changed only the affected code -> I verified the behaviour with the test suite.
- Amount-based payment matching was identified as unreliable -> I changed the matching logic to use the documented invoice identity -> I added a regression test where payment and invoice amounts differ.
- Import feedback initially displayed only counts -> I kept the existing API result and exposed its rejection details in the UI -> I verified that multiple rejected rows can be displayed separately.

AI tools used: Claude Sonnet 5 and ChatGPT (GPT-5.6 Luna).

Claude Sonnet 5 was used for AI-assisted investigation, code review, defect analysis, and suggesting focused fixes.

ChatGPT (GPT-5.6 Luna) was used for reviewing the implementation, reasoning about the business rules, refining regression tests, and checking the handover/evidence.

I reviewed the AI-suggested changes against the existing code and business rules and verified the relevant behaviour using tests and changed-input cases. AI suggestions were not accepted blindly; changes were inspected and corrected where necessary.