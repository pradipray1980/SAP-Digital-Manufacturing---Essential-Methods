# SAP-Digital-Manufacturing---Essential-Methods

# SAP DMC POD Plugin – **Get Essential Methods Inspector**

A developer utility plugin for **SAP Digital Manufacturing Cloud (DMC)** POD that discovers and safely invokes zero‑argument `get*` methods on common targets (Controller, POD Controller, Selection Model, Plant Settings, PodUtility). It also lets you optionally list non‑function attributes, copy fully‑qualified call paths, and export results to CSV.

> Built with SAPUI5 and the DMC POD foundation controller APIs.

---

## ✨ Features
- **Target chooser**: Inspect `This Controller`, `POD Controller`, `POD Selection Model`, `Plant Setting`, or `Pod Utility`.
- **Method discovery**: Walks the **full prototype chain** to list unique `get*` methods; shows accurate arity (argument count). 
- **Safe execution**: Only calls **zero‑argument** `get*` methods; async results supported with a 5s timeout guard.
- **Attributes toggle**: Optionally include current values of non‑function attributes on the target.
- **Filtering & copy**: Live filter by name/path and **copy** a ready‑to‑paste call expression (e.g., `this.getPodController().getPodSelectionModel()`).
- **CSV export**: Export **all** or **selected** rows with columns: Target, Name, Full Path, Type/Arity, Sync/Async, Value.
- **Configurable**: Back/Close button visibility, header title, and body text via the Property Editor.

## 🚀 Getting Started (DMC POD Plugin)

### Prerequisites
- SAP DMC tenant with permissions to deploy POD plugins

### Install / Deploy
1. Package the plugin resources under the namespace `br.custom.plugins.getessentialmethods` keeping the same file names.
2. Import **POD plugin** from POD designer.
3. In the POD Designer, add the plugin to an order POD.

> **Namespace**: `br.custom.plugins.getessentialmethods`

### Runtime Permissions
The plugin reads from in‑memory controller objects and utilities; no external calls or write actions are performed by default.

---

## 🧩 UI Overview
The view renders a vertical layout with a **Toolbar** (Back/Title/Close), a short **Text** area, and an **Inspector Panel**:
- **Target** selector and action buttons: `List (get*)`, `Call All (Zero‑arg)`, `Call Selected (Zero‑arg)`, `Clear Results`, `Export CSV (All)`, `Export CSV (Selected)`
- **Show attributes** toggle (off by default)
- **Filter** search field
- **Methods & Attributes table** with columns: Name, Full Path, Copy, Type/Arity, Value/Error

> You can select multiple rows in the table before invoking or exporting.

---

## ⚙️ Configuration (Builder Property Editor)
Defined in **`PropertyEditor.js`** with defaults:

| Property            | Type    | Default              | Notes |
|---------------------|---------|----------------------|-------|
| `backButtonVisible` | boolean | `true`               | Show/hide the Back button |
| `closeButtonVisible`| boolean | `true`               | Show/hide the Close button |
| `title`             | string  | `"getessentialmethods"` | Header title |
| `text`              | string  | `"getessentialmethods"` | Body text under the toolbar |

The editor also wires i18n bundles via:
- `this.setResourceBundleName("br.custom.plugins.getessentialmethods.i18n.builder")`
- `this.setPluginResourceBundleName("br.custom.plugins.getessentialmethods.i18n.i18n")`
- Prefix: `customComponentListConfig.`

> Adjust these properties in the POD Designer (Builder) to brand the plugin in each POD.

---

## 🔍 How It Works

### Discovery
- `collectGetMethodNamesFullChain(obj)` walks the **entire prototype chain** (excluding `Object.prototype`) to collect unique function properties whose names start with `get`.
- Each row shows the **computed arity** by locating the defining descriptor and reading `fn.length`.
- A **copyable** call expression is generated based on the selected target (e.g., `this.getPodController().getPodSelectionModel()`).

### Invocation
- Only `get*` methods are candidates; **non‑get** rows and attributes are skipped.
- Methods with **arguments (`fn.length > 0`)** are **not executed** and are annotated as *Skipped*.
- If a method returns a **Promise**, it is awaited with a **5‑second timeout**; result or error is stringified via a circular‑safe serializer.

### Attributes (Optional)
- When **Show attributes** is ON, own non‑function properties of the target are listed with their current values.

### Filtering & Copy
- Live filter applies to `name` and `fullPath`.
- **Copy** uses `navigator.clipboard` with a fallback to a hidden `<textarea>`.

### CSV Export
- Two actions: **All** rows or **Selected** rows.
- File name pattern: `methods_<TargetLabel>_<ISO-timestamp>.csv`.

---

## 🧪 Targets Supported
- `This Controller` → the plugin controller instance (`this`)
- `POD Controller` → `this.getPodController()`
- `POD Selection Model` → `this.getPodController().getPodSelectionModel()`
- `Plant Setting` → `PlantSettings` module (or its singleton if available)
- `Pod Utility` → `PodUtility`

> Targets resolve defensively; if unavailable, a placeholder row explains that the target is null/undefined.

---

## 📸 Screenshots (optional)
Add screenshots/GIFs here to document common workflows:
- Listing methods on the POD Controller
  <img width="1298" height="666" alt="image" src="https://github.com/user-attachments/assets/0977f56b-66f7-4780-96c7-d231aed8ff03" />

- Invoking selected zero‑arg getters
  <img width="1337" height="678" alt="image" src="https://github.com/user-attachments/assets/0996a8ab-6b24-4d26-b0a6-de94797f5196" />

- Exporting CSV
<img width="1342" height="287" alt="image" src="https://github.com/user-attachments/assets/645dc0ff-0df4-4c6b-9c91-4034b788411a" />

---

## 🛡️ Notes & Limitations
- The inspector is **read‑only** and avoids side‑effects by design (no arguments passed, only `get*`).
- Long‑running async getters are capped by a timeout (5s) to keep the UI responsive.
- Attribute values are stringified; circular references are marked as `[Circular]`.

---

## 🔧 Development
- Controller extends `sap/dm/dme/podfoundation/controller/PluginViewController`.
- View uses `sap.m` controls and `sap.ui.layout.form.SimpleForm`.
- Model: `sap/ui/model/json/JSONModel` under the view model name `"inspector"`.

### Local i18n
Hook up the two i18n bundles in your project to replace hardcoded strings if desired.

### Linting & Formatting
Adopt your team’s ESLint/Prettier config. The code contains defensive `/* eslint-disable no-console */` in one diagnostic log.

---

## 📝 License
Choose a license (e.g., MIT) and include it as `LICENSE` in the repository root.

---

## 🙌 Credits
Created by Pradip Ray for SAP DMC POD developers who need quick insight into available getters on runtime objects.

