# MoneyMap for macOS

A complete, production-intent personal finance application for macOS — native SwiftUI, SQLite, Decimal money math, offline-first, free, no account, no ads, no telemetry.

- **Bundle ID:** `com.moneymap.desktop`
- **Platform:** macOS 14.0+, Apple Silicon (arm64)
- **Default currency:** BDT (configurable)
- **Stack:** Swift 6, SwiftUI, AppKit where needed, SQLite via GRDB.swift, Swift Charts, MVVM, PDFKit, XCTest

## What is implemented
All 11 sidebar sections are fully implemented (no placeholder screens):
Overview (dashboard, charts, date filters), Transactions (add/edit/delete, search/sort/filter, bulk delete, transfer/refund/adjustment), Accounts (Cash/Bank/Savings/Credit Card/Digital Wallet/Other, opening balance + computed balance, archive/restore), Budgets (monthly/weekly/custom, 50/75/90/100% alerts), Categories (20 built-in + custom, safe delete), Recurring Payments (daily/weekly/monthly/quarterly/yearly/custom, occurrence ledger, mark-as-paid/skip/pause — never auto-deducts), Financial Goals (contributions tracked separately), Reports & Analytics (10 report kinds, PDF/CSV/JSON export), Insights (deterministic, local, from real data), Import & Export (CSV preview/mapping/duplicate detection, atomic import), Settings (currency, appearance, notifications, privacy, data, advanced).

Financial rules are in `MoneyMap/Documentation/FINANCIAL_RULES.md` and `MoneyMap/Core/FinancialRules.swift` — Decimal only, transfers never count as income/expense, refunds reduce expense, balances are always computed.

Persistence is real SQLite via GRDB: `MoneyMap/Database/GRDBRecords.swift` maps every model
(Decimal stored as TEXT, dates as REAL, UUIDs as TEXT), and all six repositories
read/write through `DatabaseService`. `InMemoryStore` is a test double only and is
not used by the app. Backup/restore is an atomic SQLite replacement. PDF reports
draw a real totals table with AppKit/CoreGraphics. The app icon is a generated
1024x1024 master with all sizes exported (see `MoneyMap/Resources/Assets.xcassets/`).

## ⚠️ IMPORTANT — read before building
**This project was authored on a Linux VM with no Swift toolchain and no Xcode/macOS** (`swift` not found, no `/Applications`). Therefore:
- No `.app` or `.dmg` was built or verified here. **Do not claim one exists.**
- No tests were run here. Tests are written and must be run on macOS — see `TESTING_REPORT.md`.
- The GRDB glue, PDF table and app icon are implemented in this snapshot, but they
  have **not been compiled anywhere yet**. The first GitHub Actions run (below) is
  the first verification. Expect the first run to surface compile issues that
  could not be caught on Linux; fix them in the workflow run, do not disable tests.

## Next step — build it on a cloud Mac (no Xcode needed on your Mac)
1. Push this repo to GitHub (intended target: `Quantam96/moneymap`).
2. GitHub Actions runs `.github/workflows/build-macos.yml` automatically on every
   push to `main`: it builds Release for arm64, runs the tests, archives the app,
   verifies the bundle (identifier `com.moneymap.desktop`, arm64), creates and
   verifies `MoneyMap.dmg`, and uploads `MoneyMap.dmg`, `MoneyMap.app.zip` and the
   test results as artifacts.
3. Download `MoneyMap.dmg` from the workflow run's Artifacts, double-click it,
   drag `MoneyMap.app` to Applications, and launch. The build is unsigned, so on
   first launch right-click the app and choose Open once.
4. See `BUILD.md` for the full workflow description and the local-Xcode alternative.

## Project layout
See `MoneyMap/` — `App/`, `Core/`, `Models/`, `Database/`, `Repositories/`, `Services/`, `Features/`, `DesignSystem/`, `Resources/`, `Tests/`, `Documentation/`.
