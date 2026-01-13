# ESPHome HA Display

## Setup (Windows)

```powershell
# Create venv with Python 3.11 (required - Python 3.14 not yet supported)
py -3.11 -m venv venv

# Activate venv
.\venv\Scripts\Activate.ps1

# Install ESPHome
pip install esphome
```

## Run

```powershell
# Activate venv first
.\venv\Scripts\Activate.ps1

# Compile and upload
esphome run index.yaml

# Or use the web dashboard
esphome dashboard .
```

## macOS/Linux

```bash
python3 -m venv venv
source venv/bin/activate
pip install esphome
esphome run index.yaml
```
