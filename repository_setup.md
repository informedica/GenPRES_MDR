# Repository Structure

```
/docs/                        # Regulatory documentation
├── README.md                 # Overview of the documentation structure
├── design-history/           # Design History File (DHF)
│   ├── architecture.md
│   ├── ui-wireframes.md
│   └── change-log.md
├── requirements/             # Functional and regulatory requirements
│   ├── user-requirements.md
│   ├── software-requirements.md
│   └── traceability-matrix.xlsx
├── risk-analysis/            # ISO 14971 Risk Management
│   ├── risk-management-plan.md
│   ├── hazard-analysis.xlsx
│   ├── risk-control-table.xlsx
│   └── risk-management-report.md
├── validation/               # IEC 62304 Software Verification & Validation
│   ├── test-strategy.md
│   ├── unit-test-report.md
│   ├── integration-test-report.md
│   └── usability-validation-report.md
├── usability/                # IEC 62366 Usability Engineering
│   ├── user-profile.md
│   ├── critical-tasks.md
│   ├── formative-testing.md
│   └── summative-testing.md
└── post-market/              # Surveillance and maintenance
    ├── feedback-log.md
    ├── update-plan.md
    └── known-issues.md

/src/                         # Source code
├── main/                     # Production code
└── config/                   # Configuration files

/tests/                       # Unit and integration tests
├── unit/
├── integration/
└── regression/

/.github/                     # CI/CD and GitHub workflows
└── workflows/
    ├── ci.yml                # Run linting, tests, and validation on push
    └── build.yml             # Build/package software

/ci-scripts/                  # Optional custom scripts

CODEOWNERS
README.md                     # Overview of the system
LICENSE
.gitignore
```