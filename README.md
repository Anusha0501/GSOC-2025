# GSoC 2025 – Normalization and Interpretation Enhancement of `drivedb.h` in smartmontools

**Organization:** Ceph (IBM)

**Contributor:** Anusha Singh

---

## 📌 Project Overview

The goal of this project was to **standardize and normalize SSD SMART endurance, temperature, and write-related attributes** in `smartmontools`, particularly in `drivedb.h` and the JSON reporting layer.

SSD vendors expose endurance and health metrics inconsistently, with variations such as `Percent_Life_Used`, `SSD_Life_Left`, or `Media_Wearout_Indicator`. This inconsistency complicates monitoring at scale (e.g., Prometheus/Grafana).

The project was executed in two phases:

* **Phase 1:** Enhance smartctl to normalize vendor-specific SSD lifetime attributes into a unified JSON field (`endurance_used`).
* **Phase 2:** Define a standard specification for SMART attribute labels (`ATTRIBUTE_LABEL_STANDARD.md`) for long-term upstream consistency.

---

## 🚀 Key Contributions

### 🔹 Phase 1 – Runtime Normalization

* Expanded regex coverage in `ataprtint.cpp` to capture multiple vendor-specific endurance attributes.
* Implemented **normalization logic**:

  * “Remaining life” metrics inverted (`100 - value`).
  * “Used life” metrics reported directly.
* Added new JSON key:

  ```json
  "endurance_used": {
    "current_percent": 73
  }
  ```
* Extended parsing to **temperature** and **LBAs written** metrics.
* Tested on real hardware (Apple SSD SM0128G).

📄 Reports:

* [Phase 1 Report (PDF)](phase1.pdf)
* [Phase 1 Commented Draft](phase1_commented.pdf)
* [Endurance Attributes Classification Table](endurance_attributes_classification_table.pdf)

---

### 🔹 Phase 2 – Defining Standards

* Authored a Markdown specification (`ATTRIBUTE_LABEL_STANDARD.md`) for SMART attribute naming.
* Standardized key metrics with clear polarity and units:

  * `SSD_Lifetime_Used_Pct`
  * `SSD_Lifetime_Remain_Pct`
  * `Temperature_Celsius`
  * `Total_LBAs_Written`
* Ensures future entries in `drivedb.h` follow uniform naming, reducing regex complexity over time.

📄 Reports:

* [Phase 2 Report (PDF)](phase2.pdf)

---

## 📂 Repository Contents

```
.
├── phase1.pdf                           # Phase 1 detailed report
├── phase1_commented.pdf                 # Commented Phase 1 draft
├── endurance_attributes_classification_table.pdf   # Attribute classification reference
├── phase2.pdf                           # Phase 2 final report
├── smartctl-linux-x64.zip               # Build/test binaries
└── README.md                            # This file
```

---

## 🧪 Testing & Validation

* Built and tested modified `smartctl` binary on macOS.
* Verified extraction of:

  * `endurance_used`
  * `temperature_celsius`
  * `lbas_written`
* Example output:

  ```bash
  $ sudo smartctl -a --json=c /dev/disk0 | jq '.endurance_used'
  {
    "current_percent": 168
  }
  ```

---

## 📊 Expected Impact

* Enables **consistent fleet-wide monitoring** of SSD health.
* Simplifies integration with observability tools like **Prometheus/Grafana**.
* Provides a **foundation spec** for future contributions to smartmontools.

---

## 🔗 References

* Upstream PR discussion (WIP): [smartmontools PR #378](https://github.com/smartmontools/smartmontools/pull/378)
* Project idea: Ceph GSoC 2025 – Smartmontools Enhancements

---

## 🙌 Acknowledgements

Huge thanks to my mentors and the Ceph community for their continuous guidance, reviews, and technical support.

