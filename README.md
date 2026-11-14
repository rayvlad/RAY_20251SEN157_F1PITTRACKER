# RAY_20251SEN157_F1PITTRACKER
# F1 Race Results and Pit-Stop Strategy Tracker

**Student:** Shimwa Ray vladan 20251SEN157
**Course:** INSY 8311, Database Development with PL/SQL
**Instructor:** Eric Maniraguha

## Problem Statement
Formula 1 generates large volumes of race data every season: results, lap times and pit stops for every Grand Prix since 1950. This data is usually published as separate files and web pages, with no single structured database that enforces rules on it. As a result:

Data errors go unnoticed. Two drivers can be recorded in the same finishing position, or a pit stop can be logged with an impossible duration or on a lap beyond the race distance.
Team performance is hard to compare. Pit-stop efficiency is a major factor in race outcomes, but there is no easy, consistent way to compare it across teams and seasons.
Standings are slow and error-prone to produce. Without a database, championship points and race standings must be recalculated by hand or with ad hoc spreadsheets, which causes inconsistencies.

## Objectives
**Main objective**
To design and implement a reliable Oracle database for Formula 1 race data, using PL/SQL to validate the data and to produce race standings and pit-stop performance statistics.

**Specific objectives**

Normalize the data. Convert the raw Kaggle F1 files (drivers, constructors, circuits, races, results, pit stops, lap times) into 3NF tables with primary and foreign keys, and document them in an ERD and data dictionary.
Load and validate real data. Load the cleaned data into an Oracle pluggable database and check row counts and values against the source files.
Build PL/SQL logic. Implement procedures (record_result, record_pit_stop), functions (driver_season_points, avg_pit_duration), cursors for race standings, and a package (f1_pkg) with exception handling.
Enforce rules and audit changes. Use triggers to block duplicate finishing positions and invalid pit stops, and an audit trigger to log every correction to race results.
Add an interactive layer. Connect a user interface (APEX or Streamlit) to the Oracle database so users can view standings and pit-stop statistics, and record results through the PL/SQL procedures.

## Data Source
Kaggle, Formula 1 World Championship (1950-2024) by Vopani.
Link: https://www.kaggle.com/datasets/rohanrao/formula-1-world-championship-1950-2020
Licence: CC0: Public Domain (as stated on the Kaggle dataset page)

## Innovation
Innovation: Interactive F1 Race Data Application (Oracle APEX)
An Oracle APEX web application connected directly to the F1 Oracle database. Users can select a season and race to view standings and pit-stop statistics, and compare average pit-stop times between constructors. A race steward can record a new race result or pit stop through a form that calls the PL/SQL procedures record_result and record_pit_stop. All business rules stay in the database: if the data breaks a rule, such as a duplicate finishing position or an invalid pit-stop duration, the trigger or procedure rejects it and the app shows the error message. The app only displays and submits data; it does not enforce rules itself.

## Planned Tables
DRIVER, CONSTRUCTOR, CIRCUIT, RACE, RESULT_STATUS, RESULT, PIT_STOP, LAP_TIME, AUDIT_LOG

## Planned PL/SQL Features
**Functions**
- driver_season_points(driver_id, year) totals a driver’s points for a season.
- constructor_season_points(constructor_id, year) totals a team’s points, giving the constructors’ championship.
- driver_championship_rank(driver_id, year) returns a driver’s standings position for a season. It builds on driver_season_points.
- avg_pit_duration(constructor_id, year) returns a team’s average pit-stop time, to compare pit-stop efficiency.
- fastest_pit_stop(race_id) returns the quickest pit stop of a race.
- pit_stop_count(driver_id, race_id) returns how many stops a driver made in a race.
- dnf_rate(constructor_id, year) gives the percentage of a team’s race entries that did not finish, using the status table.
- driver_exists(driver_id) and race_exists(race_id) return TRUE or FALSE, so procedures can raise clean exceptions.

**Procedures**

record_result uses driver_exists and race_exists, then validates the constructor and status and inserts the result.
record_pit_stop uses the same checks, validates lap number and duration, then inserts the stop.

**Cursor**

An explicit cursor loops through a race’s results in finishing order to build the standings.

**Triggers**

Duplicate position trigger: blocks two drivers sharing a finishing position in one race, ignoring unclassified (retired) drivers.
Pit-stop validation trigger: rejects zero or negative durations and laps beyond the race distance.
Audit trigger: logs every correction to RESULT in AUDIT_LOG, with old value, new value, user and timestamp.

**Package**

f1_pkg groups the functions, procedures and cursor logic.

**Exceptions**

Custom exceptions for unknown driver, unknown race, duplicate position and invalid pit-stop data.

## References
