# WORKLOG.md

## 2025-11-04 - Neural Network Blocked, SVM Abandoned (Team)

**Context**: Continuing advanced modeling attempts while waiting for enhanced data integration.

**Work Completed**:
- (Team) Abandoned SVM approach (M4.T4) - training time was prohibitive with 50K+ blocks
- (Behr) Neural network with block embeddings showing overfitting on sparse data
- Decision: Need to complete enhanced data integration (M5.*) before continuing neural network work

**Impact**: M4.T6 is blocked until we have more features to prevent overfitting.

**Next Steps**: Focus on completing weather and census data integration first.

---

## 2025-11-03 - Weather and Census Data Progress (Carmen, Abishek)

**Context**: Integrating external data sources to improve model performance beyond baseline.

**Work Completed**:
- (Carmen) Found NOAA weather station coordinates; using nearest station assignment for each block
- (Abishek) Downloaded Census ACS 5-year estimates: population density, median age, income by tract
- Both data sources require spatial joining to our block-level crime data

**Impact**: M5.T1 and M5.T2 progressing well; spatial alignment (M5.T5) will be the challenging step.

---

## 2025-11-01 - External Data API Challenges (Carmen, Abishek)

**Context**: Starting the Enhanced Data Integration milestone.

**Problems Identified**:
- (Carmen) NOAA weather API changed since last year; needed to register for new API key
- (Abishek) Census API returns data by tract, not block; need tract-to-block crosswalk file

**Solution Implemented**:
- (Carmen) Obtained new NOAA API access; data is by weather station, need to map to blocks
- (Abishek) Found HUD USPS crosswalk files for tract-to-block mapping

**Impact**: Both tasks are unblocked but require additional spatial processing.

---

## 2025-10-27 - Added Enhanced Data Milestone (Team)

**Context**: XGBoost with spatial features still only achieving 0.42 F1 score.

**Problem Identified**: Simple features (temporal + basic spatial) are insufficient for meaningful predictions. Need richer feature set.

**Solution Implemented**:
- Added entire Milestone 5 "Enhanced Data Integration" with tasks for weather, census, economic, and POI data
- Added M3.T5 for spatial adjacency matrix to support future spatial models
- Added Milestone 6 "Advanced Modeling" for graph neural networks and ensemble approaches

**Impact**: Major scope expansion, but necessary to achieve meaningful prediction accuracy.

---

## 2025-10-26 - XGBoost Hyperparameter Tuning (Behr)

**Context**: Random Forest only marginally better than logistic regression; trying XGBoost with careful tuning.

**Work Completed**:
- Grid search over: max_depth [3,6,9,12], learning_rate [0.01,0.05,0.1,0.3]
- Best parameters: depth=6, lr=0.1
- F1 score: 0.42 (improvement from 0.33 but still not great)

**Impact**: XGBoost is our best model so far, but clearly need more/better features.

---

## 2025-10-24 - Abandoned Location Interpolation (Team)

**Context**: About 15% of crime records have missing or ungeocodable locations.

**Problem Identified**: M3.T4 attempted kernel density-based spatial interpolation to fill in missing locations.

**Solution Implemented**: Abandoned this approach - introduces too much noise and artificial patterns. Will exclude records with missing locations (acceptable data loss at 15%).

**Impact**: M3.T4 marked as abandoned (🚫). Simpler approach is more defensible.

---

## 2025-10-22 - Crimes Mapped to Census Blocks (Abishek)

**Context**: Need spatial unit for predictions; chose census blocks as finest available granularity.

**Work Completed**:
- Downloaded NYC census block shapefiles from Census TIGER
- Used geopandas for spatial join of crime coordinates to blocks
- Edge case: crimes on block boundaries assigned to nearest centroid

**Files Modified**:
- `work/geocoding/map_to_blocks.py`
- `data/crimes_with_blocks.csv`

**Impact**: M3.T3 complete. Crime data now has spatial resolution for modeling.

---

## 2025-10-17 - Geocoding Strategy Revised (Abishek, Carmen)

**Context**: Address geocoding blocked by API limits and data quality issues.

**Problems Identified**:
- Google Maps API rate limits (2500/day free tier) - would take months to geocode all records
- NYC GeoClient API rejecting 30% of addresses due to formatting issues

**Solution Implemented**:
- (Carmen) Added M2.T7 to parse cross-street information from freeform text before geocoding
- (Abishek) Combined NYC GeoClient with OSM Nominatim for better coverage
- Result: 85% geocoding success rate (acceptable)

**Impact**: Unblocked M3.T1; geocoding now viable path forward.

---

## 2025-10-12 - Crime Type Pivot (Team)

**Context**: Initial model attempts with all crime types showed poor performance.

**Problem Identified**: Dataset includes 60+ crime types with vastly different patterns:
- "HARASSMENT 2" has different spatial patterns than "ROBBERY"
- Many indoor crimes (domestic violence, fraud) have poor location data
- Too heterogeneous for a single prediction model

**Solution Implemented**:
- Pivoted to violent public crimes only (assault, robbery, car-jacking)
- Added M2.T6 to filter dataset accordingly
- Revised M1.T5 to reflect this decision

**Impact**: Dataset reduced from 500K to ~80K records, but much more homogeneous target variable.

---

## 2025-10-05 - Spatial Information Critical Finding (Team)

**Context**: Initial modeling attempts showed poor performance.

**Problem Identified**:
- Logistic regression with temporal features only: 0.31 F1 (barely better than random)
- Random Forest with same features: 0.33 F1 (no real improvement)
- Realized we cannot predict crime location without encoding location!

**Solution Implemented**:
- Added entire Milestone 3 "Geocoding & Spatial Resolution" (not in original plan)
- Need to convert text addresses ("CORNER OF 42 ST and 8 AVE") to coordinates
- Then map coordinates to census blocks for spatial features

**Impact**: Major gap in original plan identified and addressed. Spatial features are essential.

---

## 2025-09-28 - Initial Feature Engineering (Behr)

**Context**: Creating baseline features from raw crime data.

**Work Completed**:
- Created temporal features: hour of day, day of week, month, holiday indicator
- Explored location field - it's freeform text, not coordinates
- Examples: "CORNER OF 42 ST and 8 AVE", "FRONT OF 123 MAIN STREET"

**Impact**: Temporal features ready; location data needs significant preprocessing (see Milestone 3).

---

## 2025-09-23 - Initial EDA Complete (Behr)

**Context**: First look at NYC Open Data crime dataset.

**Work Completed**:
- Dataset has 35 columns, many with unclear codes (CMPLNT_NUM, KY_CD, OFNS_DESC)
- Found official data dictionary on NYC Open Data portal
- ~500K records over 5 years
- Key fields identified: date/time, location text, offense description, precinct

**Files Created**:
- `work/eda/initial_exploration.ipynb`

**Impact**: M1.T2 complete. Data is workable but needs significant cleaning.

---

## 2025-09-22 - Project Kickoff (Team)

**Context**: First team meeting to set up project infrastructure.

**Work Completed**:
- (Carmen) Downloaded NYC crime dataset from NYC Open Data portal
- Set up repository structure (admin/, work/, data/)
- Created initial VISION.md and WORKPLAN.md
- Assigned initial task ownership

**Impact**: M1.T1 complete. Project infrastructure in place.
