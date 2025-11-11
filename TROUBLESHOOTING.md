# LEAN Troubleshooting Guide

Having issues running LEAN? This guide covers common problems and their solutions.

## Build Issues

### "SDK not found" or ".NET SDK version mismatch"

**Problem**: You see errors about missing .NET SDK or wrong version.

**Solution**:
1. Install [.NET 9 SDK](https://dotnet.microsoft.com/download/dotnet/9.0)
2. Verify installation: `dotnet --version`
3. Restart your terminal/IDE after installation

### "Build failed with errors"

**Problem**: Build command fails with compilation errors.

**Solution**:
```bash
# Clean and rebuild
dotnet clean QuantConnect.Lean.sln
dotnet restore QuantConnect.Lean.sln
dotnet build QuantConnect.Lean.sln
```

If errors persist, check that you're on a compatible branch/commit.

---

## Running Issues

### "Algorithm type not found" or "Could not load algorithm"

**Problem**: LEAN cannot find your algorithm class.

**Solution**:

**For C# algorithms:**
1. Ensure the algorithm is compiled: `dotnet build`
2. Check `config.json`:
   ```json
   {
     "algorithm-type-name": "YourAlgorithmClassName",
     "algorithm-language": "CSharp",
     "algorithm-location": "QuantConnect.Algorithm.CSharp.dll"
   }
   ```
3. The class name must match exactly (case-sensitive)

**For Python algorithms:**
1. Check `config.json`:
   ```json
   {
     "algorithm-type-name": "YourAlgorithmClassName",
     "algorithm-language": "Python",
     "algorithm-location": "../../../Algorithm.Python/YourFile.py"
   }
   ```
2. Verify the path is correct relative to the Launcher/bin/Debug directory

### "No data found" or "Data file not found"

**Problem**: LEAN cannot find market data files.

**Solution**:

**Option 1: Use LEAN CLI** (easiest)
```bash
lean data download
```

**Option 2: Manual setup**
1. The `data-folder` in `config.json` must point to valid data
2. Default location: `../../../Data/` (relative to Launcher/bin/Debug)
3. Ensure the directory structure matches LEAN's expected format
4. For testing, you can use the `force-exchange-always-open` setting:
   ```json
   {
     "force-exchange-always-open": true
   }
   ```

### "Python.Runtime.PythonException" or Python errors

**Problem**: Python-related errors when running Python algorithms.

**Solution**:
1. Install Python 3.11.11 (exactly this version recommended)
2. Follow the [Python setup guide](https://github.com/QuantConnect/Lean/tree/master/Algorithm.Python)
3. On Linux/Mac, ensure Python is in your PATH:
   ```bash
   which python3.11
   ```
4. Check `config.json` for Python virtual environment settings:
   ```json
   {
     "python-venv": "/path/to/venv"  // Optional
   }
   ```

### "Address already in use" or Port conflicts

**Problem**: Cannot start live trading - port already in use.

**Solution**:
1. Check if another LEAN instance is running
2. Kill the process using the port (default 8020):
   ```bash
   # Linux/Mac
   lsof -ti:8020 | xargs kill -9
   
   # Windows
   netstat -ano | findstr :8020
   taskkill /PID <PID> /F
   ```

---

## Configuration Issues

### "Invalid configuration file"

**Problem**: LEAN reports configuration errors.

**Solution**:
1. Validate your JSON syntax (comments are allowed in LEAN's config)
2. Compare with `config.example.json`
3. Common issues:
   - Missing commas between properties
   - Invalid escape characters in paths
   - Incorrect value types (string vs number)

### "Insufficient permissions"

**Problem**: Permission errors when writing results or logs.

**Solution**:
```bash
# Linux/Mac - ensure write permissions
chmod -R 755 /home/runner/work/Lean/Lean

# Windows - run as administrator or check folder permissions
```

---

## Docker Issues

### "Docker daemon not running"

**Problem**: Docker commands fail.

**Solution**:
1. Start Docker Desktop (Windows/Mac)
2. Start Docker service (Linux):
   ```bash
   sudo systemctl start docker
   ```

### "Cannot pull Docker image"

**Problem**: `docker pull` fails.

**Solution**:
1. Check your internet connection
2. Try with sudo (Linux):
   ```bash
   sudo docker pull quantconnect/lean:latest
   ```
3. Check Docker Hub is accessible: https://hub.docker.com

### "Container immediately exits"

**Problem**: Docker container starts and stops immediately.

**Solution**:
1. Check the logs:
   ```bash
   docker logs <container_id>
   ```
2. Verify volume mounts are correct
3. Ensure config.json is properly mounted

---

## LEAN CLI Issues

### "lean: command not found"

**Problem**: CLI not in PATH after installation.

**Solution**:
```bash
# Verify installation
pip show lean

# If installed but not in PATH, use python -m
python -m lean --help

# Or reinstall with user flag
pip install --user lean

# Add to PATH (Linux/Mac)
export PATH="$HOME/.local/bin:$PATH"
```

### "CLI not pulling Docker image"

**Problem**: CLI commands fail to download required images.

**Solution**:
1. Manually pull the image:
   ```bash
   docker pull quantconnect/lean:latest
   ```
2. Check Docker is running
3. Try with `--verbose` flag for more info:
   ```bash
   lean backtest "ProjectName" --verbose
   ```

---

## Performance Issues

### "Backtest running very slowly"

**Problem**: Backtest takes too long to complete.

**Solutions**:
1. Reduce data resolution (use minute data instead of tick data)
2. Reduce date range in your algorithm
3. Optimize your algorithm code (reduce indicator count, simplify logic)
4. Check system resources:
   ```bash
   # Linux/Mac
   top
   
   # Windows
   Task Manager
   ```

### "High memory usage"

**Problem**: LEAN consuming too much RAM.

**Solutions**:
1. Reduce the number of securities in your universe
2. Adjust cache settings in config.json:
   ```json
   {
     "zip-data-cache-provider": "5"  // Reduce from default 10
   }
   ```
3. Use coarse universe selection instead of fine

---

## IDE-Specific Issues

### Visual Studio Code

**Problem**: IntelliSense not working.

**Solution**:
1. Install C# Dev Kit extension
2. Restart OmniSharp: `Ctrl+Shift+P` → "OmniSharp: Restart OmniSharp"
3. Check `.vscode/settings.json` exists

### Visual Studio

**Problem**: Cannot set QuantConnect.Lean.Launcher as startup project.

**Solution**:
1. Right-click on QuantConnect.Lean.Launcher project
2. Select "Set as Startup Project"
3. Press F5 to run

---

## Debugging

### Enable Verbose Logging

Add to `config.json`:
```json
{
  "debug-mode": true,
  "log-handler": "QuantConnect.Logging.CompositeLogHandler"
}
```

### Debug Python Algorithms

1. Set in `config.json`:
   ```json
   {
     "debugging": true,
     "debugging-method": "DebugPy"
   }
   ```
2. LEAN will wait for debugger on port 5678
3. Attach your IDE's Python debugger

### Debug C# Algorithms

1. Open in Visual Studio or VS Code
2. Set breakpoints in your algorithm code
3. Press F5 to start debugging

---

## Getting Help

If you're still stuck:

1. **Search the Forum**: [QuantConnect Forum](https://www.quantconnect.com/forum)
2. **Check GitHub Issues**: [GitHub Issues](https://github.com/QuantConnect/Lean/issues)
3. **Join Discord**: [Discord Community](https://www.quantconnect.com/discord)
4. **Read Documentation**: [Official Docs](https://www.lean.io/docs)

When asking for help, include:
- Your operating system
- .NET and Python versions
- LEAN version or commit hash
- Full error message
- Relevant configuration
- Steps to reproduce

---

## Quick Diagnostics Checklist

Run through this list when troubleshooting:

```bash
# Check .NET
dotnet --version  # Should be 9.x

# Check Python (if using Python algorithms)
python3 --version  # Should be 3.11.x

# Check Docker (if using CLI/Docker)
docker --version

# Verify LEAN builds
cd /path/to/Lean
dotnet build QuantConnect.Lean.sln

# Check config file
cat Launcher/config.json | head -20

# Check data folder exists
ls -la Data/

# Try running with verbose output
cd Launcher/bin/Debug
dotnet QuantConnect.Lean.Launcher.dll
```

---

## Still Need Help?

Create a new issue on GitHub with the troubleshooting information:
[New Issue](https://github.com/QuantConnect/Lean/issues/new)

Be sure to follow the issue template and provide as much detail as possible!
