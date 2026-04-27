# Process: Consolidating Invalid 2024 Roll Test Case Files

This document describes the two-step process for identifying and consolidating invalid 2024 validation logs, instance files, and taxonomy archives into the `invalid/2024` directory.

**Roll output source:**   `tests/output/roll*2024*.xml`
**Instance input source:** `tests/input/testfiles/2024-roll-2025/{TICKER}_instance_2024_2025.xml`
**Destination:**           `tests/input/testfiles/testcase-tools/invalid/2024/`

---

## Step 1 — Identify Invalid 2024 Validation Logs

### What
Evaluate each `roll*2024*.xml` output file in `tests/output/` to confirm it contains at least one DQC rule finding. A valid roll output file must contain at least one occurrence of:

```
<entry code="DQC.US
```

Any roll file that lacks this string produced no DQC.US findings and is treated as an invalid test case.

### How

```python
import os, re, shutil

output_dir  = "tests/output"
invalid_dir = "tests/input/testfiles/testcase-tools/invalid/2024"
search_str  = '<entry code="DQC.US'

os.makedirs(invalid_dir, exist_ok=True)

roll_all = sorted(f for f in os.listdir(output_dir)
                  if re.match(r'roll.*2024.*\.xml$', f))

for fname in roll_all:
    path = os.path.join(output_dir, fname)
    with open(path, encoding='utf-8', errors='replace') as fh:
        content = fh.read()
    if search_str not in content:
        new_name = 'invalid-' + fname
        new_path = os.path.join(output_dir, new_name)
        os.rename(path, new_path)
        shutil.move(new_path, os.path.join(invalid_dir, new_name))
        print(f'Moved: {new_name}')
```

### Result (last run: 2026-04-27)
- Total 2024 roll files evaluated: **291**
- Invalid (no `<entry code="DQC.US`) — renamed and moved: **84**
- Valid (retained in `tests/output`): **207**

Invalid logs moved to `invalid/2024/`:

```
invalid-rollDQC.US.0001.69_MTEX-us-2024.xml
invalid-rollDQC.US.0001.70_AEP-us-2024.xml
invalid-rollDQC.US.0001.77_NI-us-2024.xml
invalid-rollDQC.US.0004.9289_TPTW-us-2024.xml
invalid-rollDQC.US.0005.49_BXP-us-2024.xml
invalid-rollDQC.US.0008.6819_BXP-us-2024.xml
invalid-rollDQC.US.0009.15_NONE-us-2024.xml
invalid-rollDQC.US.0009.21_NONE-us-2024.xml
invalid-rollDQC.US.0011_HONG-us-2024.xml
invalid-rollDQC.US.0011_RDI-us-2024.xml
invalid-rollDQC.US.0013.2779_EXC-us-2024.xml
invalid-rollDQC.US.0013.2781_EXC-us-2024.xml
invalid-rollDQC.US.0014_BPOP-us-2024.xml
invalid-rollDQC.US.0018.34_NXT-us-2024.xml
invalid-rollDQC.US.0033.2_CK0001784700-us-2024.xml
invalid-rollDQC.US.0041.73_PEBK-us-2024.xml
invalid-rollDQC.US.0043.7488_NI-us-2024.xml
invalid-rollDQC.US.0044.7503_SCGY-us-2024.xml
invalid-rollDQC.US.0048.7482_SINO-us-2024.xml
invalid-rollDQC.US.0073.7648_BCTF-us-2024.xml
invalid-rollDQC.US.0079.7657_MTEX-us-2024.xml
invalid-rollDQC.US.0081.9278_NXT-us-2024.xml
invalid-rollDQC.US.0085.9362_GDRZF-us-2024.xml
invalid-rollDQC.US.0088.10094_ADTX-us-2024.xml
invalid-rollDQC.US.0091.9376_MTD-us-2024.xml
invalid-rollDQC.US.0108.9564_UAMY-us-2024.xml
invalid-rollDQC.US.0109.9569_BXP-us-2024.xml
invalid-rollDQC.US.0113.9562_AEHR-us-2024.xml
invalid-rollDQC.US.0116.9573_CEI-us-2024.xml
invalid-rollDQC.US.0116.9726_TPTW-us-2024.xml
invalid-rollDQC.US.0121.9581_NXT-us-2024.xml
invalid-rollDQC.US.0123.9583_NXT-us-2024.xml
invalid-rollDQC.US.0124.9585_MTEX-us-2024.xml
invalid-rollDQC.US.0124.9587_BXP-us-2024.xml
invalid-rollDQC.US.0154.10068_MTEX-us-2024.xml
invalid-rollDQC.US.0154.10069_AERT-us-2024.xml
invalid-rollDQC.US.0154.10070_LITE-us-2024.xml
invalid-rollDQC.US.0156.10074_UAMY-us-2024.xml
invalid-rollDQC.US.0162.10084_DSGX-us-2024.xml
invalid-rollDQC.US.0166.10090_NXT-us-2024.xml
invalid-rollDQC.US.0168.10096_WYY-us-2024.xml
invalid-rollDQC.US.0168.10097_UAMY-us-2024.xml
invalid-rollDQC.US.0168.10098_FMCC-us-2024.xml
invalid-rollDQC.US.0170.10103_OCC-us-2024.xml
invalid-rollDQC.US.0170.10129_NOC-us-2024.xml
invalid-rollDQC.US.0171.10104_OCFC-us-2024.xml
invalid-rollDQC.US.0178.10135_LFCR-us-2024.xml
invalid-rollDQC.US.0179.10139_CK0001018164-us-2024.xml
invalid-rollDQC.US.0179.10141_WHR-us-2024.xml
invalid-rollDQC.US.0179.10143_BXP-us-2024.xml
invalid-rollDQC.US.0179.10144_BTCC-us-2024.xml
invalid-rollDQC.US.0179.10151_OLED-us-2024.xml
invalid-rollDQC.US.0181.10180_OMCC-us-2024.xml
invalid-rollDQC.US.0185.10165_VATE-us-2024.xml
invalid-rollDQC.US.0190.10600_ICTSF-us-2024.xml
invalid-rollDQC.US.0193.10153_ICTSF-us-2024.xml
invalid-rollDQC.US.0194.10621_RHE-us-2024.xml
invalid-rollDQC.US.0195.10625_UAMY-us-2024.xml
invalid-rollDQC.US.0199.10653_ARCC-us-2024.xml
invalid-rollDQC.US.0199.10654_ARCC-us-2024.xml
invalid-rollDQC.US.0200.10700_WYY-us-2024.xml
invalid-rollDQC.US.0202.10702_CTHR-us-2024.xml
invalid-rollDQC.US.0204.10704_CDXO_US-2024.xml
invalid-rollDQC.US.0204.10704_WFCF_US-2024.xml
invalid-rollDQC.US.0204.10705_RVNC_US-2024.xml
invalid-rollDQC.US.0204.10705_SRI_US-2024.xml
invalid-rollDQC.US.0204.10707_PFSI_US-2024.xml
invalid-rollDQC.US.0207.10722_GLAD_US-2024.xml
invalid-rollDQC.US.0207.10723_CION_US-2024.xml
invalid-rollDQC.US.0207.10723_MRCC_US-2024.xml
invalid-rollDQC.US.0207.10724_NMFC_US-2024.xml
invalid-rollDQC.US.0207.10724_PNNT_US-2024.xml
invalid-rollDQC.US.0208.10727_REDW-US-2024.xml
invalid-rollDQC.US.0208.10727_SCM-US-2024.xml
invalid-rollDQC.US.0208.10728_REDW-US-2024.xml
invalid-rollDQC.US.0208.10728_SCM-US-2024.xml
invalid-rollDQC.US.0212.10737_HTGC_US-2024.xml
invalid-rollDQC.US.0212.10737_LTUM_US-2024.xml
invalid-rollDQC.US.0212.10738_AEE_US-2024.xml
invalid-rollDQC.US.0212.10738_WY_US-2024.xml
invalid-rollDQC.US.0212.10739_HTGC_US-2024.xml
invalid-rollDQC.US.0212.10739_LTUM_US-2024.xml
invalid-rollDQC.US.0228.10921_AMSF-US-2024.xml
invalid-rollDQC.US.0228.10921_ZEPP-US-2024.xml
```

---

## Step 2 — Identify Instance Files with No Valid Roll Match

### What
After Step 1, compare the remaining valid roll output files against the instance files in `2024-roll-2025/`. Any instance file with no corresponding valid roll file is an invalid test case — its `_instance_2024_2025.xml` and `_taxonomy_2024_2025.zip` are moved to `invalid/2024/`.

Roll filenames encode the company ticker:

```
rollDQC.US.{rule}_{TICKER}-us-2024.xml      (e.g. rollDQC.US.0001.51_OGS-us-2024.xml)
rollDQC.US.{rule}_{TICKER}_US-2024.xml      (e.g. rollDQC.US.0204.10706_PRTH_US-2024.xml)
```

Instance filenames follow:

```
{TICKER}_instance_2024_2025.xml
{TICKER}_taxonomy_2024_2025.zip
```

A dot-suffix ticker in a roll file (e.g., `AGM.A`) is treated as a match for the base ticker (`AGM`).

### How

```python
import os, re, shutil

output_dir  = "tests/output"
inst_dir    = "tests/input/testfiles/2024-roll-2025"
invalid_dir = "tests/input/testfiles/testcase-tools/invalid/2024"

def extract_ticker(fname):
    name = fname.replace('.xml', '')
    name = re.sub(r'^rollDQC\.(US|IFRS)\.\d+(\.\d+)?_', '', name)
    name = re.sub(r'[-_][Uu][Ss]-2024$', '', name)
    return name.upper()

roll_files   = sorted(f for f in os.listdir(output_dir)
                      if re.match(r'roll.*2024.*\.xml$', f))
roll_tickers = set(extract_ticker(f) for f in roll_files)

inst_files   = [f for f in os.listdir(inst_dir) if f.endswith('_instance_2024_2025.xml')]
inst_tickers = {f.split('_')[0].upper(): f for f in inst_files}

for ticker, inst_fname in sorted(inst_tickers.items()):
    matched = ticker in roll_tickers or any(
        rt == ticker or rt.startswith(ticker + '.') for rt in roll_tickers
    )
    if not matched:
        for suffix in [f'{ticker}_instance_2024_2025.xml', f'{ticker}_taxonomy_2024_2025.zip']:
            src = os.path.join(inst_dir, suffix)
            dst = os.path.join(invalid_dir, suffix)
            if os.path.exists(src):
                shutil.move(src, dst)
                print(f'Moved: {suffix}')
```

### Result (last run: 2026-04-27)
- Instance files evaluated: **171**
- No matching valid roll: **45** (90 files — instance + taxonomy pairs)

Instance and taxonomy pairs moved to `invalid/2024/`:

| Ticker | Reason |
|---|---|
| AEP | Roll file was invalid (Step 1) |
| AERA | No roll file |
| AERT | Roll file was invalid (Step 1) |
| AMSF | Roll file was invalid (Step 1) |
| ARCC | Roll files were invalid (Step 1) |
| ASII | No roll file |
| BDCC | No roll file |
| BINI | No roll file |
| BNKK | No roll file |
| BSAI | No roll file |
| BTCW | No roll file |
| CCH | No roll file |
| CIK0001740742 | No roll file |
| CPKA | No roll file |
| CTHR | Roll file was invalid (Step 1) |
| DMNIF | No roll file (roll ticker was DMN) |
| DPL | No roll file (roll ticker was DPLINCCOM) |
| DSNY | No roll file |
| FIEE | No roll file |
| GWAV | No roll file |
| IMTH | No roll file |
| ITKG | No roll file |
| KPEA | No roll file |
| LITE | Roll file was invalid (Step 1) |
| LTUM | Roll files were invalid (Step 1) |
| NMFC | Roll file was invalid (Step 1) |
| NONE | Roll files were invalid (Step 1) |
| OCFC | Roll file was invalid (Step 1) |
| PAYD | No roll file (roll ticker was PAID) |
| RDI | Roll file was invalid (Step 1) |
| RHEP | No roll file |
| RVNC | Roll files were invalid (Step 1) |
| SCM | Roll files were invalid (Step 1) |
| SDEV | No roll file |
| SFRX | No roll file |
| SKVI | No roll file |
| SRI | Roll files were invalid (Step 1) |
| TEN | No roll file |
| TMB | No roll file |
| TW | No roll file |
| UCC | No roll file |
| VANI | No roll file |
| WFCF | Roll files were invalid (Step 1) |
| WHR | Roll file was invalid (Step 1) |
| WY | Roll file was invalid (Step 1) |

---

## Directory Inventory After Both Steps

The `invalid/2024/` directory contains:

| File type | Naming pattern | Origin |
|---|---|---|
| Invalid validation log | `invalid-roll*2024*.xml` | Step 1 — renamed from `tests/output/` |
| Invalid instance file | `{TICKER}_instance_2024_2025.xml` | Step 2 — moved from `2024-roll-2025/` |
| Invalid taxonomy archive | `{TICKER}_taxonomy_2024_2025.zip` | Step 2 — moved from `2024-roll-2025/` |
