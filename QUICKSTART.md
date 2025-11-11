# LEAN Quick Start Guide

Get LEAN running in under 5 minutes! ⚡

## The Fastest Way: LEAN CLI

### 1. Install the CLI
```bash
pip install lean
```

### 2. Create Your First Algorithm
```bash
lean project-create "MyFirstStrategy"
```

### 3. Run a Backtest
```bash
lean backtest "MyFirstStrategy"
```

**Done!** 🎉 Your algorithm is running.

---

## Alternative: Run Locally Without CLI

### Prerequisites
- [.NET 9 SDK](https://dotnet.microsoft.com/download/dotnet/9.0)
- [Git](https://git-scm.com/downloads)

### Quick Commands

```bash
# Clone the repository
git clone https://github.com/QuantConnect/Lean.git
cd Lean

# Build
dotnet build QuantConnect.Lean.sln

# Run
cd Launcher/bin/Debug
dotnet QuantConnect.Lean.Launcher.dll
```

**Done!** 🎉 LEAN will run the default algorithm.

---

## What Just Happened?

You just ran a **backtest** of a trading algorithm! LEAN:
1. Loaded historical market data
2. Simulated your algorithm's trades
3. Calculated performance metrics
4. Generated a results report

---

## Next Steps

### 1. Modify the Algorithm
- **CLI**: Edit the Python/C# file in your project folder
- **Local**: Edit files in `Algorithm.CSharp/` or `Algorithm.Python/`

### 2. Choose Your Algorithm
Edit `Launcher/config.json` to select which algorithm to run:

```json
{
  "algorithm-type-name": "BasicTemplateAlgorithm",
  "algorithm-language": "CSharp",
  "algorithm-location": "QuantConnect.Algorithm.CSharp.dll"
}
```

### 3. Learn More
- 📖 [Full Getting Started Guide](GETTING_STARTED.md)
- 💡 [Writing Algorithms](https://www.lean.io/docs/v2/writing-algorithms)
- 🎓 [Video Tutorials](https://www.youtube.com/quantconnect)

---

## Need Help?

- 💬 [Community Forum](https://www.quantconnect.com/forum)
- 🎮 [Discord Chat](https://www.quantconnect.com/discord)
- 📚 [Full Documentation](https://www.lean.io/docs)

Happy Trading! 🚀
