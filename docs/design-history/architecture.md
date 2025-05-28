# GenPres2 Architecture Overview

This document describes the architecture of the GenPres2 application, a clinical decision support system (CDSS) for medication prescribing. The system is written entirely in F# and built with the SAFE Stack (Saturn, Azure, Fable, Elmish).

---

## 1. High-Level Architecture

GenPres2 is a **client-server web application**:

- **Server**: Runs in .NET (F#), can be hosted in a Docker container. Exposes a web API.
- **Client**: F# (Fable) compiled to JavaScript, runs in the browser.
- **Configuration**: All configuration and medication rules are maintained in Google Spreadsheets.
- **Local Drug Repository**: Drug data is cached locally in text files for performance and offline access.

---

## 2. Server

### 2.1. Technologies

- **F#** (.NET 9.0)
- **Giraffe** for web server
- **Saturn** for application composition
- **Fable.Remoting** for type-safe API communication with the client

### 2.2. Structure

- **Entry Point**: `src/Server/Server.fs`
- **API Implementation**: `src/Server/ServerApi.fs`
    - Implements protocol in `Shared.Api.IServerApi`
    - Processes commands from the client, performs calculations/validations, and returns results
- **Agents & Domain Logic**: e.g., `src/Server/Scripts/OrderAgent.fsx`
    - Uses F# MailboxProcessor (actor model) for concurrent/isolated processing (e.g., medication order calculations)

### 2.3. Docker Hosting

- **Dockerfile** builds the server, bundles the client, and sets up the environment.
- Environment variables (e.g., `GENPRES_URL_ID`, `GENPRES_PROD`) configure which Google Spreadsheet is used for configuration.
- Entry point: runs `dotnet Server.dll` and exposes port 8085.

**Example Docker Usage:**
```bash
docker build --build-arg GENPRES_URL_ARG="your_secret_url_id" -t halcwb/genpres .
docker run -it -p 8080:8085 halcwb/genpres
```

---

## 3. Client

### 3.1. Technologies

- **F# (Fable)**: Compiles to JavaScript
- **Elmish**: Model-View-Update architecture
- **React**: UI rendering
- **Vite**: Dev/bundle tooling (see `src/Client/vite.config.js`)

### 3.2. Structure

- **Entry Point**: `src/Client/App.fs` and `src/Client/index.html`
- Communicates with the server using Fable.Remoting proxies (`Api.IServerApi`)
- Handles application state, dispatches commands, and updates UI reactively

---

## 4. Configuration via Google Spreadsheets

- **All rules, constraints, and medication data** (except local drug cache) are stored in Google Spreadsheets.
    - URLs are constructed dynamically and downloaded as CSV.
    - Example: `https://docs.google.com/spreadsheets/d/{id}/gviz/tq?tqx=out:csv&sheet={sheet}`
- **F# Modules** (e.g., `Informedica.Utils.Lib.Web.GoogleSheets`) handle fetching and parsing of spreadsheet data.
- **Which spreadsheet to use** is controlled by the `GENPRES_URL_ID` environment variable.

---

## 5. Local Medication Drug Repository

- **Data Cache**: Proprietary cache files (not distributed publicly) containing medication product information.
    - Used for fast lookup/calculation and offline use.
    - Demo cache files are included for development.
    - Path: `src/Server/data/cache/README.md` explains the folder usage.

- **Drug Data Types and Logic**:
    - Main types are defined in `src/Informedica.KinderFormularium.Lib/Drug.fs` and `src/Informedica.GenOrder.Lib/Types.fs`
    - Includes types for Drug, Dose, Route, Schedule, and DrugOrder.
    - Drug data can be loaded from local cache or generated from Google Sheets.

---

## 6. Build and Development

- **Development**: `dotnet run` for full stack, or `dotnet run list` for targets.
- **Client**: Open browser at `http://localhost:5173`.
- **Production**: Deploy via Docker.
- **Environmental Variables**:
    - `GENPRES_URL_ID`: Google Spreadsheet ID for config/data
    - `GENPRES_LOG`, `GENPRES_PROD`, `GENPRES_DEBUG`: Control logging, production/demo mode, etc.

---

## 7. Key Data Flow

1. **Startup**:
    - Server loads configuration from Google Spreadsheets (as CSV).
    - Loads or generates local drug cache.
2. **Runtime**:
    - Client sends commands (e.g., calculate dose) via API.
    - Server processes, applies rules, validates, and returns results.
    - Drug lookup/calculation uses both spreadsheet config and local cache as needed.
3. **Updates**:
    - To update rules or configuration, edit the Google Spreadsheets referenced by the server.

---

## 8. References

- [SAFE Stack Documentation](https://safe-stack.github.io/docs/)
- [Saturn](https://saturnframework.org/)
- [Fable](https://fable.io/docs/)
- [Elmish](https://elmish.github.io/elmish/)
- More background: [GenPres2 README](https://github.com/halcwb/GenPres2/blob/master/README.md)

---

## 9. Notes & Limitations

- Some medication data is proprietary and not included in the public repo.
- Only demo cache files are distributed for development and testing.
- The drug repository logic and calculation code is modular and can be extended for new data sources.

---

## 10. File References

- **See also**:
    - [README.md](https://github.com/halcwb/GenPres2/blob/master/README.md)
    - [Dockerfile](https://github.com/halcwb/GenPres2/blob/master/Dockerfile)
    - [src/Server/ServerApi.fs](https://github.com/halcwb/GenPres2/blob/master/src/Server/ServerApi.fs)
    - [src/Informedica.KinderFormularium.Lib/Drug.fs](https://github.com/halcwb/GenPres2/blob/master/src/Informedica.KinderFormularium.Lib/Drug.fs)

---

> **This is a summary based on code and documentation found in the repository. For more in-depth details, see the full [codebase on GitHub](https://github.com/halcwb/GenPres2) and linked files above. Results are based on a search limited to the top 10 results per query.**