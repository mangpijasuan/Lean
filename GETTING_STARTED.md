# Getting Started with LEAN

Welcome to LEAN! This guide will walk you through running the LEAN algorithmic trading engine step by step.

## What is LEAN?

LEAN is an open-source algorithmic trading engine built by QuantConnect. It allows you to develop, backtest, and deploy trading algorithms for stocks, forex, futures, options, and cryptocurrencies.

## Prerequisites

Before you begin, ensure you have the following installed:

### Required
- **Git**: [Download here](https://git-scm.com/downloads)

### Choose Your Method

You can run LEAN in three different ways. Choose the method that works best for you:

---

## Method 1: LEAN CLI (Recommended for Most Users)

This is the easiest and fastest way to get started. The CLI handles Docker, data management, and deployment automatically.

### Step 1: Install Python

Make sure you have Python 3.6+ installed:
```bash
python3 --version
```

### Step 2: Install LEAN CLI

```bash
pip install lean
```

### Step 3: Create a New Project

```bash
lean project-create "MyFirstStrategy"
cd MyFirstStrategy
```

### Step 4: Run a Backtest

```bash
lean backtest "MyFirstStrategy"
```

That's it! The CLI will handle downloading Docker images and running your algorithm.

### Next Steps with CLI

- **Research**: Run Jupyter Lab for algorithm research
  ```bash
  lean research "MyFirstStrategy"
  ```

- **Optimize**: Find optimal parameters for your strategy
  ```bash
  lean optimize "MyFirstStrategy"
  ```

- **Live Trading**: Deploy your algorithm for live paper trading
  ```bash
  lean live "MyFirstStrategy"
  ```

📘 **Learn More**: [CLI Documentation](https://www.lean.io/docs/v2/lean-cli/key-concepts/getting-started)

---

## Method 2: Docker (Good for Quick Testing)

If you want to run LEAN without installing development tools, use Docker.

### Step 1: Install Docker

- **Windows/Mac**: [Docker Desktop](https://www.docker.com/products/docker-desktop)
- **Linux**: [Docker Engine](https://docs.docker.com/engine/install/)

### Step 2: Clone the Repository

```bash
git clone https://github.com/QuantConnect/Lean.git
cd Lean
```

### Step 3: Pull the LEAN Docker Image

```bash
docker pull quantconnect/lean:latest
```

### Step 4: Run a Backtest with Docker

```bash
docker run --rm \
  -v "$(pwd)/Data:/Lean/Data" \
  -v "$(pwd)/Launcher/config.json:/Lean/Launcher/config.json" \
  -v "$(pwd)/Algorithm.CSharp:/Lean/Algorithm.CSharp" \
  quantconnect/lean:latest
```

---

## Method 3: Local Development (For Contributors & Advanced Users)

This method is best if you want to:
- Modify LEAN's source code
- Debug the engine
- Contribute to the project

### Step 1: Install .NET SDK

Download and install [.NET 9 SDK](https://dotnet.microsoft.com/en-us/download/dotnet/9.0)

Verify installation:
```bash
dotnet --version
```

### Step 2: (Optional) Install Python for Python Algorithms

If you want to run Python algorithms, install [Python 3.11.11](https://www.python.org/downloads/release/python-31111/)

Follow the [Python setup instructions](https://github.com/QuantConnect/Lean/tree/master/Algorithm.Python#quantconnect-python-algorithm-project) for your platform.

### Step 3: Clone the Repository

```bash
git clone https://github.com/QuantConnect/Lean.git
cd Lean
```

### Step 4: Build the Solution

```bash
dotnet build QuantConnect.Lean.sln
```

This will take a few minutes the first time as it downloads dependencies.

### Step 5: Configure Your Algorithm

Edit the configuration file at `Launcher/config.json`:

**For C# algorithms:**
```json
{
  "algorithm-type-name": "BasicTemplateFrameworkAlgorithm",
  "algorithm-language": "CSharp",
  "algorithm-location": "QuantConnect.Algorithm.CSharp.dll"
}
```

**For Python algorithms:**
```json
{
  "algorithm-type-name": "BasicTemplateAlgorithm",
  "algorithm-language": "Python",
  "algorithm-location": "../../../Algorithm.Python/BasicTemplateAlgorithm.py"
}
```

### Step 6: Run LEAN

```bash
cd Launcher/bin/Debug
dotnet QuantConnect.Lean.Launcher.dll
```

Your algorithm will run and output results to the console!

---

## Understanding the Output

When you run a backtest, LEAN will output:

1. **Algorithm Statistics**: Total returns, Sharpe ratio, drawdown, etc.
2. **Trade Log**: List of all orders and fills
3. **Charts**: Performance charts (saved to the output folder)
4. **Log Messages**: Debug messages from your algorithm

---

## What's Next?

### Learn to Write Algorithms

- 📖 [Algorithm Documentation](https://www.lean.io/docs/v2/writing-algorithms)
- 💡 [Example Algorithms in C#](Algorithm.CSharp/)
- 🐍 [Example Algorithms in Python](Algorithm.Python/)

### IDE Setup

For a better development experience:
- **VS Code**: See [.vscode/readme.md](.vscode/readme.md)
- **Visual Studio**: See [.vs/readme.md](.vs/readme.md)

### Data

LEAN comes with free sample data. For full market data:
- 💾 [Data Library](https://www.quantconnect.com/data)
- 📥 Download data using the CLI: `lean data download`

### Community & Support

- 💬 [Forum](https://www.quantconnect.com/forum)
- 🎮 [Discord](https://www.quantconnect.com/discord)
- 🐛 [Issue Tracker](https://github.com/QuantConnect/Lean/issues)

---

## Common Issues

### "Algorithm location not found"
- Check that your `algorithm-location` path in `config.json` is correct
- For C# algorithms, make sure you've built the solution first

### "No data found"
- The `data-folder` in `config.json` must point to valid market data
- Use the CLI to download sample data: `lean data download`

### Build errors
- Ensure you have .NET 9 SDK installed
- Try cleaning and rebuilding: `dotnet clean && dotnet build`

### Python algorithm not running
- Verify Python 3.11.11 is installed
- Check the [Python setup guide](https://github.com/QuantConnect/Lean/tree/master/Algorithm.Python)

---

## Quick Reference

| Task | Command/Action |
|------|----------------|
| Build solution | `dotnet build QuantConnect.Lean.sln` |
| Run LEAN locally | `cd Launcher/bin/Debug && dotnet QuantConnect.Lean.Launcher.dll` |
| Run with CLI | `lean backtest "ProjectName"` |
| Create new project | `lean project-create "ProjectName"` |
| Run tests | `dotnet test Tests/Tests.csproj` |

---

Happy Trading! 🚀
