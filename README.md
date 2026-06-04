# lens_harvest

A Zemax OpticStudio analysis toolkit. It connects to OpticStudio via the ZOS-API,
runs a full battery of analyses across every `.zmx` file in a folder, and lets you
browse the results in a web-based viewer.

This repository distributes the pre-built wheel (`lens_harvest-0.1.9-py3-none-any.whl`)
plus a sample archive of Zemax files (`zemax_files.zip`).

---

## Requirements

- **Windows** with **Zemax OpticStudio** installed (the `parse` command drives OpticStudio
  through the ZOS-API).
- **Python 3.9–3.13.** Python 3.14+ is *not* supported (the PyVista dependency requires
  `<3.14`). If your system Python is 3.14, install a 3.13 interpreter and use that.

---

## Installation

It is recommended to install into a clean virtual environment.

```bash
# 1. Create and activate a virtual environment (use a 3.9–3.13 interpreter)
python -m venv venv
venv\Scripts\activate          # Windows
# source venv/bin/activate     # macOS/Linux

# 2. Install the wheel (dependencies are pulled in automatically)
pip install lens_harvest-0.1.9-py3-none-any.whl
```

To upgrade later, reinstall with `--force-reinstall`:

```bash
pip install lens_harvest-0.1.9-py3-none-any.whl --force-reinstall
```

---

## Usage

The package is run as a module with one of two subcommands: **`parse`** or **`viewer`**.

```bash
python -m lens_harvest <command> [options]
```

### 1. `parse` — run analyses on a folder of `.zmx` files

```bash
python -m lens_harvest parse "C:\path\to\zmx\files"
```

What it does:

- Finds every `.zmx` file in the given directory (non-recursive).
- Connects to OpticStudio and runs the full set of analyses on each file
  (LDE, layout, spot diagrams, MTF, wavefront, Seidel, Zernike, ray fans, and more).
- Writes the results next to each lens as a `.pkl` file.
- Prints per-file progress with elapsed time and an ETA, and reports any files
  that failed at the end.

The last directory you parsed is remembered (stored in
`~/.lens_harvest/config.json`) so the viewer can pick it up automatically.

> **Note:** OpticStudio must be installed and licensed. Parsing a large folder can
> take a while — roughly a few seconds per lens file.

### 2. `viewer` — browse results in the browser

```bash
python -m lens_harvest viewer
```

What it does:

- Starts a local web server (FastAPI/uvicorn) and opens your browser to it.
- If no directory is given, it loads the **last folder you parsed**.
- You can also pass a directory of `.pkl` files explicitly, or pick one from the UI.

```bash
# View a specific folder of results
python -m lens_harvest viewer "C:\path\to\pkl\files"

# Change the port (default 8000)
python -m lens_harvest viewer --port 9000

# Bind to all interfaces so other machines on the network can connect
python -m lens_harvest viewer --host 0.0.0.0 --port 8080
```

| Option     | Default     | Description                          |
|------------|-------------|--------------------------------------|
| `directory`| last parsed | Folder of `.pkl` files to display    |
| `--host`   | `127.0.0.1` | Host/interface to bind the server to |
| `--port`   | `8000`      | Port to bind the server to           |

Once running, open the printed URL (e.g. `http://127.0.0.1:8000`) in your browser.
Press **Ctrl+C** in the terminal to stop the server.

---

## Typical workflow

```bash
# 1. Parse a folder of lenses → produces .pkl result files
python -m lens_harvest parse "C:\zemax\my_lenses"

# 2. Launch the viewer (auto-loads the folder you just parsed)
python -m lens_harvest viewer
```

---

## Sample data

`zemax_files.zip` in this repository contains a set of `.zmx` lens files you can
extract and point `parse` at to try the tool out.

---

## License

MIT License. See the license headers in the source distribution.
