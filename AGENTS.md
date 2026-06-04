# AGENTS.md

## Cursor Cloud specific instructions

### What this repo is

QuantConnect **LEAN** — a .NET 9 algorithmic trading engine (backtest, live, optimizer, report, research). Primary workflow for agents: **build the solution**, **run tests**, **run the Launcher** for a C# backtest. Bundled `Data/` on disk is required (no database).

### Toolchain (already on the VM after setup)

| Tool | Location / version |
|------|-------------------|
| .NET SDK 9 | `$HOME/.dotnet` — add `export DOTNET_ROOT=$HOME/.dotnet` and `export PATH=$HOME/.dotnet:$PATH` in every shell |
| Python 3.11 (LEAN) | Conda env `qc_lean` at `$HOME/miniconda3/envs/qc_lean` |
| Python.NET | `export PYTHONNET_PYDLL="$HOME/miniconda3/envs/qc_lean/lib/libpython3.11.so"` before `dotnet test` or Report |

System `python3` (3.12) is **not** used by LEAN; always set `PYTHONNET_PYDLL` when running tests or Report.

### NuGet

The devcontainer adds a local feed once. If restore fails on internal packages:

```bash
dotnet nuget add source /workspace/LocalPackages --name LocalPackages
```

### Build (Release matches CI)

```bash
cd /workspace
dotnet build /p:Configuration=Release /v:minimal /p:WarningLevel=1 QuantConnect.Lean.sln
```

First build is ~2 minutes; outputs land under `*/bin/Release/`.

### Tests

CI filter (see `.github/workflows/gh-actions.yml`):

```bash
export PYTHONNET_PYDLL="$HOME/miniconda3/envs/qc_lean/lib/libpython3.11.so"
dotnet test ./Tests/bin/Release/QuantConnect.Tests.dll \
  --filter "TestCategory!=TravisExclude&TestCategory!=ResearchRegressionTests" \
  -- TestRunParameters.Parameter\(name=\"log-handler\", value=\"ConsoleErrorLogHandler\"\)
```

Full suite is large (~15k+ tests in Common alone). Syntax lint (Python): `pip install quantconnect-stubs mypy==1.15.0` then `python3 run_syntax_check.py` (slow; parallel workers).

**Gotcha:** Without `PYTHONNET_PYDLL`, the test host can crash with `GIL must always be released`.

### Run Launcher (default hello-world backtest)

Config: `Launcher/config.json` — default `BasicTemplateFrameworkAlgorithm` (C#), `data-folder` `../../../Data/`.

```bash
cd /workspace/Launcher/bin/Release
dotnet QuantConnect.Lean.Launcher.dll --close-automatically true
```

Use `--close-automatically true` in automation; otherwise the process waits for a keypress.

### Report (optional)

After a backtest, JSON is written next to the Launcher binary. Report needs matplotlib in `qc_lean`:

```bash
conda run -n qc_lean pip install matplotlib
cd /workspace/Report/bin/Release
dotnet QuantConnect.Report.dll \
  --backtest-data-source-file ../../../Launcher/bin/Release/BasicTemplateFrameworkAlgorithm.json \
  --close-automatically true
```

### Docker / Lean CLI (optional)

Official CI uses `quantconnect/lean:foundation`. End users often use `pip install lean` + Docker; not required for native C# backtest on this VM.

### Services summary

| Service | Required for agent work |
|---------|-------------------------|
| .NET 9 SDK | Yes |
| `Data/` directory | Yes |
| `qc_lean` + `PYTHONNET_PYDLL` | Yes for `dotnet test` and Report |
| Docker / Lean CLI / cloud API | No |
