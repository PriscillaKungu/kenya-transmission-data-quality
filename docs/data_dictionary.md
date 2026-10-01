# Data Dictionary & Source Register

## Source register
| Item | Detail |
|---|---|
| Dataset | Kenya transmission stations (GeoJSON, 83 point features) |
| Publisher | ENERGYDATA.INFO, World Bank Group / ESMAP |
| Data provider | Kenya Power and Lighting Company (KPLC), https://www.kplc.co.ke/ |
| URL | https://energydata.info/dataset/kenya-transmission-stations |
| Release year / last updated | 2020 / 24 November 2025 (per dataset page) |
| Date downloaded | [add date] |
| Licence | CC0 1.0 (public domain dedication); redistribution permitted |
| File used | `transmission_stations.json` (GeoJSON, 38.2 kB); a SHP ZIP version is also published |
| CRS | EPSG:32737 (WGS 84 / UTM zone 37S) |
| Note | The dataset page credits KPLC, but the file's `Source` field also contains 27 `KETRACO` records; provenance of those records is not documented on the page |
| Sources inside file | `KPLC FDB` (56 records, full attributes), `KETRACO` (27 records, coordinates only) |

## Fields (original names are truncated shapefile-style names)
| Original field | Meaning | Notes |
|---|---|---|
| FID, OBJECTID | Row identifiers | |
| RCC1 | Regional control centre area | 10 areas among named stations |
| County2 | County / KPLC region | Contains NAIROBI NORTH and NAIROBI SOUTH, which are utility regions rather than counties |
| Branch3 | Utility branch | |
| Name_IMS11 | Station name with voltage in parentheses | Voltage notation inconsistent |
| FDB_Last18 | Last update timestamp in source | Day-first; `01/01/2000` is a placeholder |
| Ownershi23 | Owner | KPLC, KENGEN, KPLC & KENGEN, KPLC & KPC, PRIVATE, UNKNOWN |
| Street40 | Street / road location | |
| Type_Acc42 | Access road type | TARMAC / MURRAM |
| Manned50 | Staffed station | YES / NO |
| VHF_Radi55 | VHF radio available | YES / NO |
| Earthing56 | Earthing present | YES / NO |
| Remote_T57 | Remote monitoring/telemetry available [confirm exact meaning] | YES / NO |
| Programm58 | Programmable equipment available [confirm exact meaning] | YES / NO |
| Ripple_C59 | Ripple control available | YES / NO |
| Source | Originating dataset | KPLC FDB / KETRACO |
| x, y | Easting / northing (metres) | EPSG:32737 |

## Derived fields (added in the notebook)
| Field | Definition |
|---|---|
| last_update_date | Parsed `FDB_Last18`; placeholder date set to null |
| date_is_placeholder | True where the source date was 2000-01-01 |
| station_name | Text before the parenthesis in `Name_IMS11` |
| max_voltage_kv | Highest voltage parsed from the parenthesis (voltage class) |
| n_voltage_levels | Count of voltages parsed |
| base_name | Name with voltage and facility words stripped (duplicate detection) |
| non_missing_attrs, completeness_score | Non-missing count of 14 descriptive attributes and its percentage |
| completeness_category | Highly complete (90-100), Moderately complete (70-89), Low (<70) |
| missing_pct_station | Percent of the 14 attributes blank |

## Cleaning log
| # | Rule | Reason |
|---|---|---|
| 1 | Whitespace-only strings to NaN | Blank-string missingness invisible to `isnull()` |
| 2 | Strip whitespace, uppercase text fields | Case/whitespace drift |
| 3 | `VHF_Radi55 "0"` to `"NO"` | Inconsistent encoding |
| 4 | `Ownershi23 "NONE"` to `"UNKNOWN"` | "None" means unknown owner, not no owner |
| 5 | Parse dates day-first; null the 2000-01-01 placeholder | Placeholder is not a real update |
| 6 | `22OKV` to `220KV` in one station name | Typo (letter O) |
| 7 | Keep KAMBURU and LESSOS pairs | Distinct multi-voltage facilities |
