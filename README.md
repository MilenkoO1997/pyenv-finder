# pyenv-finder: Manual

## 1. What it is

`pyenv-finder` is a small terminal program that **finds every Python on this computer** and sorts them into groups.
It looks for system Python, conda (Miniconda, Anaconda, Miniforge, mamba and micromamba), venvs, pipx, uv, pyenv, Flatpak apps, and Pythons bundled inside other programs.
For each one it shows:

- **where it is** (full path)
- **which Python version** it has
- **how much disk space** it uses
- **how many packages** are installed in it, plus a short list of notable ones (jupyter, torch, tensorflow, numpy, pandas...)
- **which program it belongs to** (the "owner program", see section 6)
- whether it is the `python` / `python3` you get when you type it in a terminal (**on PATH**)
- whether it is **broken** (for example, its interpreter was deleted)
- **how to use or activate it**

> **Safety: pyenv-finder is READ-ONLY.** It never modifies, deletes or installs anything.
> It only reads folders and files. It runs only harmless commands: `<python> --version`, with a 5-second time limit, and `conda env list --json`.
> The only files it ever writes are the two report files, and only when you ask for an export (`e` key or `--export`).

## 2. Where it is installed

| What | Where |
|---|---|
| The program (a single Python 3 file, standard library only) | `~/.local/bin/pyenv-finder` |
| This manual | `~/Documents/pyenv-finder-MANUAL.md` |
| Exported reports (only when you export) | `~/pyenv-finder-report.txt` and `~/pyenv-finder-report.json` |

`~/.local/bin` is already on your PATH, so you can type `pyenv-finder` from any folder. No configuration files were changed.
To uninstall it, delete the file `~/.local/bin/pyenv-finder`.

## 3. How to run it

```bash
pyenv-finder              # interactive screen (arrow keys)
pyenv-finder --list       # plain text list, grouped by category
pyenv-finder --json       # everything as JSON (for scripts)
pyenv-finder --export     # scan and save ~/pyenv-finder-report.txt + .json
pyenv-finder --no-size    # skip measuring disk size (faster; works with any mode)
pyenv-finder --no-run     # never run any python; read versions from files only
pyenv-finder --help       # short help
pyenv-finder --version
```

You can combine options, for example `pyenv-finder --list --no-size` or `pyenv-finder --json > envs.json`.
A full scan takes about 10 seconds on this computer, and about 2 seconds with `--no-size`.
While it scans, it shows a progress line with the folder it is looking at.
If there is no real terminal (for example, output piped into a file), it automatically prints the `--list` output instead of the interactive screen.

## 4. The interactive screen

```
pyenv-finder  26 envs  sort:name  filter:-   python3 -> ~/.var/app/org.jupyter.JupyterLab/.../jlab_server/bin/python3
   NAME                         VERSION         SIZE  PKGS  OWNER PROGRAM [notable]
▸ Conda · Miniconda · ~/miniconda3  (2)
   base                         3.12.7      433.1 MB    36  Miniconda - base (root) environment
   python36                     3.6.13      182.4 MB     4  Miniconda - conda env 'python36'
...
conda 'python36' prefix: /home/milenko/miniconda3/envs/python36
↑↓/jk move Enter details / filter s sort r rescan e export q quit | READ-ONLY: never modifies, deletes or installs anything
```

The bottom line always reminds you that the program is read-only. The line above it shows the full path of the selected item; for conda items it shows the conda name and prefix.

| Key | What it does |
|---|---|
| `↑` `↓` or `k` `j` | move up / down |
| `PgUp` `PgDn`, `Home`/`g`, `End`/`G` | jump |
| `Enter` (or `→`, `l`) | open the **details panel** for the selected item |
| `Esc`, `Enter`, `q` or `←` (in details) | go back to the list |
| `/` | **filter**: type a word (for example `conda`, `torch`, `3.6`, `jupyter`) and press Enter. An empty filter shows everything again. Esc cancels. |
| `s` | change the **sort order** inside each group: name → size (largest first) → version (newest first) |
| `r` | **rescan** the computer |
| `e` | **export** what is currently shown (with the filter applied) to `~/pyenv-finder-report.txt` and `.json` |
| `q` | quit |

Markers and colours: `*` (green) means this is the `python`/`python3` you get on PATH. `!` (red) means broken.

The **details panel** shows:

- the full path and the interpreter, with its other names (aliases)
- the version, and where the version came from
- conda name, prefix and install (for conda items)
- size on disk
- package counts
- owner program
- on PATH or not
- problems and extra info
- **how to use or activate it**
- notable packages and the packages folder
- what `which python/python3/pip/conda/jupyter` point to
- the full list of installed packages

## 5. Categories explained

| Category | What it is |
|---|---|
| **System Python (/usr/bin, from apt)** | The Python that comes with Linux Mint (3.12 on Mint 22.3). The system itself uses it. Don't `pip install` into it; make a venv instead. Extra `pythonX.Y` versions from apt or the deadsnakes PPA also go here. |
| **Locally installed (/usr/local)** | Pythons you built yourself with `make install`. |
| **Conda · Miniconda · <folder>** | A Miniconda install, for example `~/miniconda3`. The `base` entry is the install itself. Every env in its `envs/` folder is listed under it, with its **conda name** and **prefix** (full folder). |
| **Conda · Anaconda · <folder>** | Same as above, for an Anaconda install (`~/anaconda3`, `/opt/anaconda3`...). It is recognised by the folder name or by the `anaconda` metapackage. |
| **Conda · Miniforge / Micromamba · <folder>** | Miniforge or Mambaforge (conda-forge), and micromamba or mamba root folders. |
| **Conda · JupyterLab Desktop (bundled conda)** | JupyterLab Desktop ships its own small conda, called `jlab_server`, and can create more envs. These belong to the app. |
| **pipx app environments** | Each program installed with `pipx install X` gets its own private venv. Example: `gptme`. The entry `shared` is pipx's own helper. |
| **uv-managed Pythons** | Python versions downloaded by `uv` into `~/.local/share/uv/python`. |
| **uv tool environments** | Programs installed with `uv tool install`. |
| **pyenv versions** | Pythons installed by pyenv (`~/.pyenv/versions`). |
| **Virtual environments (venv / virtualenv / uv)** | Any folder with a `pyvenv.cfg` file: project `.venv` folders, virtualenvs, pipenv and hatch venvs. |
| **Flatpak apps & runtimes (bundled Python)** | Flatpak apps and the shared Flatpak runtimes (GNOME, KDE, freedesktop) that contain a Python. They only run inside their sandbox. You can ignore them. |
| **Snap packages** | Same idea, for snaps. |
| **Application-bundled Pythons** | A Python that is part of another program (in `/opt` or elsewhere). |
| **Game runtimes (Steam / Proton / umu / Wine - ignorable)** | Pythons inside game runtimes. You can safely ignore them. |
| **Broken / missing** | Dead links, or envs that conda still lists but whose folder no longer exists. |

How conda is searched:

- **Install folders:** it checks the usual places in `~`, `/opt` and `/usr/local`: `miniconda3`, `anaconda3`, `miniforge3`, `mambaforge`, `micromamba` and others. It also checks `$CONDA_PREFIX`, `$MAMBA_ROOT_PREFIX`, and the `root_prefix` setting in `~/.condarc` or `~/.mambarc`.
- **Conda's own lists:** it reads `~/.conda/environments.txt` and asks every conda it finds for `conda env list --json`.
- **The whole-disk search:** any folder that contains `conda-meta/` is also counted as a conda env.

The list header always says which conda installs were found, and which kinds were **not** found (for example "Not installed: Anaconda, Miniforge, Micromamba").

## 6. What "owner program" means

Many Pythons on a computer were not installed by you directly. They are **parts of other programs**. The owner program tells you who put a Python there and who uses it. Examples:

- `JupyterLab Desktop (flatpak org.jupyter.JupyterLab) - its built-in Python server environment (jlab_server)`: JupyterLab Desktop needs this one. Don't remove it unless you remove the app.
- `pipx app 'gptme' 0.31.0`: the private venv of the gptme command.
- `Miniconda - conda env 'python36'`: a conda env that you created.
- `project folder ~/Downloads/InvokeAI`: a project's own `.venv`.
- `Mycroft voice assistant (see also /opt/mycroft)`: the Mycroft venv.
- `Flatpak runtime org.gnome.Platform`: part of a Flatpak runtime.
- `Linux Mint / Ubuntu (apt package python3.12)`: the operating system.

How it is detected:

- **From the location:** pipx, uv, flatpak IDs, `/opt/<app>`, `jupyterlab-desktop`, conda env names, VS Code extensions, Steam/umu runtimes.
- **From the installed packages:** for example, `open-webui` installed means **Open WebUI**, and `jupyterlab` installed means JupyterLab. These are listed in the details as "Programs in it".

## 7. Exporting

- Press `e` in the interactive screen, or run `pyenv-finder --export`.
- It writes `~/pyenv-finder-report.txt` (the same as `--list`) and `~/pyenv-finder-report.json` (the same as `--json`).
- In the interactive screen, only the items that match the current filter are exported.
- Each export overwrites the previous report files. Nothing else is ever written.

## 8. Notes on the numbers

- **Size** is real disk usage, like `du -s`. Symlinks are not followed.
  - For conda `base`, the `envs/` and `pkgs/` folders are not included. The `pkgs` package cache is shown separately in the details.
  - Conda envs share files with `pkgs/` through hard links, so adding all sizes together counts some files twice.
  - For system Python, the size is the interpreter plus `/usr/lib/python3.X`.
  - For Flatpak and app-bundled Pythons, the size is the whole app or runtime that contains it.
  - Measuring one item stops after 25 seconds. In that case the size is shown as `≥ ...`.
- **Packages** counts the `*.dist-info` and `*.egg-info` entries in `site-packages`. Nothing is executed for this. Conda items also show the number of conda packages (`conda-meta`).
- **Version** comes from conda-meta, pyvenv.cfg, or `python --version`. If that is impossible, it is guessed from the `lib/pythonX.Y` folder name.

## 9. Troubleshooting

| Problem | What to do |
|---|---|
| `pyenv-finder: command not found` | Run it with its full path: `~/.local/bin/pyenv-finder`. Or open a new terminal; `~/.local/bin` is added to PATH at login. |
| The screen looks garbled or the terminal is tiny | Make the window bigger and press any key, or use `pyenv-finder --list`. |
| "terminal UI failed..." | The program falls back to `--list` by itself. Check that `TERM` is not `dumb`. |
| It is slow | Use `--no-size`. Measuring big environments (torch, CUDA) takes the most time. |
| A Python is missing from the list | Folders such as `~/.cache`, `node_modules`, `.git`, Steam, Wine and browser caches are skipped on purpose. Venvs that `uv run` or poetry create inside `~/.cache` are not listed. AppImages are compressed files, so their internal Python can't be seen without running them. Other disks under `/media` and `/mnt` are not searched. |
| Some folders are unreadable | They are skipped silently. The count is shown in the JSON as `permission_errors`. No sudo is needed or used. |
| Version is "?" | The interpreter could not be run, and no version file was found. Try running it without `--no-run`. |
| An item is marked BROKEN | Its interpreter was removed or a link points nowhere. You may delete that folder yourself if you no longer need it. pyenv-finder will never delete it. |

## 10. Example outputs (real run on this computer, 27 Oct 2026)

### Example 1: `pyenv-finder --list` (beginning)

```
pyenv-finder 1.0 - Python environments on milenko-pavilion  (2026-10-27 12:36:03 CET, 9.3 s)
READ-ONLY: never modifies, deletes or installs anything
  which python   -> ~/.var/app/org.jupyter.JupyterLab/config/jupyterlab-desktop/jlab_server/bin/python  (-> ~/.var/app/org.jupyter.JupyterLab/config/jupyterlab-desktop/jlab_server/bin/python3.12)
  which python3  -> ~/.var/app/org.jupyter.JupyterLab/config/jupyterlab-desktop/jlab_server/bin/python3  (-> ~/.var/app/org.jupyter.JupyterLab/config/jupyterlab-desktop/jlab_server/bin/python3.12)
  which conda    -> ~/.var/app/org.jupyter.JupyterLab/config/jupyterlab-desktop/jlab_server/bin/conda
  Conda installs: JupyterLab Desktop (bundled conda) at ~/.var/app/org.jupyter.JupyterLab/config/jupyterlab-desktop/jlab_server (1 env); Miniconda at ~/miniconda3 (2 envs)
  Not installed : Anaconda, Miniforge, Micromamba
  Found 26 environments/interpreters in 8 categories.

== System Python (/usr/bin, from apt)  (1) ==
   python3.12                         3.12.3       62.9 MB  482 pkgs
      path : /usr
      owner: Linux Mint / Ubuntu (apt package python3.12)
      notable: notebook 6.4.12, ipykernel 6.29.3, numpy 1.26.4, scipy 1.11.4, matplotlib 3.6.3, pyqt5 5.15.10

== Conda · JupyterLab Desktop (bundled conda) · ~/.var/app/org.jupyter.JupyterLab/config/jupyterlab-desktop/jlab_server  (1) ==
 * base                               3.12.3        6.3 GB  235 pkgs (conda 220) ON PATH as python/python3
      path : ~/.var/app/org.jupyter.JupyterLab/config/jupyterlab-desktop/jlab_server
      conda: name 'base', prefix /home/milenko/.var/app/org.jupyter.JupyterLab/config/jupyterlab-desktop/jlab_server
      owner: JupyterLab Desktop (flatpak org.jupyter.JupyterLab) - its built-in Python server environment (jlab_server)
      notable: jupyterlab 4.2.1, ipykernel 6.29.3, torch 2.12.1, numpy 1.26.4, pandas 2.2.2, scipy 1.13.1, matplotlib 3.8.4, transformers 5.13.0

== Conda · Miniconda · ~/miniconda3  (2) ==
   base                               3.12.7      433.1 MB  36 pkgs (conda 76)
      path : ~/miniconda3
      conda: name 'base', prefix /home/milenko/miniconda3
      owner: Miniconda - base (root) environment
   python36                           3.6.13      182.4 MB  4 pkgs (conda 20)
      path : ~/miniconda3/envs/python36
      conda: name 'python36', prefix /home/milenko/miniconda3/envs/python36
      owner: Miniconda - conda env 'python36'

== Virtual environments (venv / virtualenv / uv)  (3) ==
   InvokeAI/.venv                     3.12          8.3 GB  146 pkgs
      path : ~/Downloads/InvokeAI/.venv
      owner: project folder ~/Downloads/InvokeAI
      notable: torch 2.7.1+cu128, jax 0.7.1, numpy 1.26.4, scipy 1.17.1, matplotlib 3.11.0, transformers 5.5.4, fastapi 0.141.1
```

Notice that the `python3` and `conda` commands in your terminal currently come from **JupyterLab Desktop's** `jlab_server`, not from Mint or Miniconda. Its `bin` folder is early in your PATH.

### Example 2: details panel (Enter on `python36`)

```
python36
==========
Category      : Conda · Miniconda · ~/miniconda3
Type          : conda env
Location      : /home/milenko/miniconda3/envs/python36
Interpreter   : /home/milenko/miniconda3/envs/python36/bin/python3
Python version: 3.6.13   (conda-meta)
Conda name    : python36
Conda prefix  : /home/milenko/miniconda3/envs/python36
Conda install : /home/milenko/miniconda3  (Miniconda)
Size on disk  : 182.4 MB
Packages      : 4 pkgs (conda 20)
Owner program : Miniconda - conda env 'python36'
On PATH       : no

How to use it:
  conda activate python36
  or by prefix: conda activate /home/milenko/miniconda3/envs/python36
  run directly without activating: /home/milenko/miniconda3/envs/python36/bin/python3
  this install's conda: ~/miniconda3/condabin/conda
```

### Example 3: one entry from `pyenv-finder --json --no-size` (shortened)

```json
{
  "name": "gptme",
  "category": "pipx app environments",
  "kind": "pipx venv",
  "path": "/home/milenko/.local/share/pipx/venvs/gptme",
  "python": "/home/milenko/.local/share/pipx/venvs/gptme/bin/python3",
  "version": "3.12.3",
  "version_source": "pyvenv.cfg",
  "size_bytes": null,
  "pip_packages": 75,
  "notable": ["gptme 0.31.0", "openai 1.109.1"],
  "owner": "pipx app 'gptme' 0.31.0",
  "how_to_use": [
    "Managed by pipx - use the app's command (e.g. 'gptme').",
    "list its packages: pipx runpip gptme list",
    "upgrade: pipx upgrade gptme   (pyenv-finder itself never runs this)"
  ],
  "on_path": [],
  "broken": "",
  "extra": ["base interpreter (home): /usr/bin/python3.12"]
}
```

`size_bytes` is `null` here because this run used `--no-size`.
