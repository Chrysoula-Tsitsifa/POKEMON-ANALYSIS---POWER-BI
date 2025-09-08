# POKEMON ANALYTICS

This project demonstrates the full Power BI workflow :

- **Data cleaning & transformation** in Power Query  
- **Data modeling** with star schema (including bridge table for dual types)  
- **DAX measures** for KPIs and advanced calculations  
- **Interactive dashboards** with tables, slicers, cards, and charts  

It showcases both **high-level insights** (KPIs, averages, Top 20 strongest Pokémon) and **detailed exploration** (full dataset filtering by type and generation).

<br>

Workflow : **Cleaning** → **Modeling** → **DAX** → **Visuals**

------------------------------------------------------------------------

## Dataset

Original dataset from [Kaggle : Pokémon with stats Dataset](https://www.kaggle.com/datasets/abcsds/pokemon)


- Raw data stored in : [pokemon.csv](https://github.com/Chrysoula-Tsitsifa/POKEMON-ANALYSIS---POWER-BI/blob/main/DATA/RAW_DATA/Pokemon.csv)


- Transformed into BI - ready schema :

  - [FactPokemon.csv](https://github.com/Chrysoula-Tsitsifa/POKEMON-ANALYSIS---POWER-BI/blob/main/DATA/PROCESSED_DATA/FactPokemon.csv)
  - [DimType.csv](https://github.com/Chrysoula-Tsitsifa/POKEMON-ANALYSIS---POWER-BI/blob/main/DATA/PROCESSED_DATA/DimType.csv)
  - [DimGeneration.csv](https://github.com/Chrysoula-Tsitsifa/POKEMON-ANALYSIS---POWER-BI/blob/main/DATA/PROCESSED_DATA/DimGeneration.csv)
  - [BridgePokemonType.csv](https://github.com/Chrysoula-Tsitsifa/POKEMON-ANALYSIS---POWER-BI/blob/main/DATA/PROCESSED_DATA/BridgePokemonType.csv)

------------------------------------------------------------------------

## Full Power BI Report

👉 [Download Pokemon Analytics.pbix](https://github.com/Chrysoula-Tsitsifa/POKEMON-ANALYSIS---POWER-BI/blob/main/Pokemon%20Analytics.pbix)

------------------------------------------------------------------------

## 1) Data & Tables

**FactPokemon**

- Columns : `PokemonID`, `Name`, `BaseName`, `Type1`, `Type2`, `HP`,`Attack`, `Defense`, `SpAtk`, `SpDef`, `Speed`, `Generation`,`Legendary`

  helpers : `BaseName`, `IsDualType`, `SurrogatePokemonID`

- Contains 800 rows (including Mega forms)

<br>

**DimType**

- Columns : `TypeID`, `TypeName`
  
- Contains all unique Pokémon types (Fire, Water, Electric, ...)

<br>

**BridgePokemonType**

- Columns : `PokemonID`, `SurrogatePokemonID`, `TypeID`, `TypeSlot`
  
- 1 row per Pokémon – type
  
<br>

**DimGeneration**

- Columns : `Generation`, `GenerationName`

------------------------------------------------------------------------

## 2) Cleaning (Power Query)

- Used first row as headers (promoted headers)
  
- Added custom columns: 

  - `BaseName`   → Removed “Mega ” prefix and parentheses
  - `IsDualType` → TRUE if Type2 exists, otherwise FALSE

- Added Index column → used as `SurrogatePokemonID`

- Reordered columns for logical grouping

- Renamed columns (e.g. `Sp. Atk → SpAtk`, `Sp. Def → SpDef`)

- Changed data types (text, whole number, decimal, boolean)

- Replaced values where needed (e.g. nulls with `nan`)

- Applied `Trimmed Text` and `Cleaned Text` to ensure consistency

------------------------------------------------------------------------

## 3) Modeling (Relationships)

- DimGeneration[Generation]       **(1)** → **(∞)** FactPokemon[Generation]
  
- DimType[TypeID]                 **(1)** → **(∞)** BridgePokemonType[TypeID]

- FactPokemon[SurrogatePokemonID] **(1)** → **(∞)** BridgePokemonType[SurrogatePokemonID]

<br>

All relationships are active. Fact ↔ Bridge is set to cross filter *Both* so that the DimType slicer works.

------------------------------------------------------------------------

## 4) DAX Measures

Explicit measures created in **FactPokemon** :

```
CountPokemon = COUNTROWS(FactPokemon)
```

```
AvgAttack = AVERAGE(FactPokemon[Attack])
```

```
TotalAttack = SUM(FactPokemon[Attack])
```

```
CountDualType =
CALCULATE(
    COUNTROWS(FactPokemon),
    FactPokemon[IsDualType] = TRUE()
)
```

```
PctDualType = DIVIDE([CountDualType], [CountPokemon])
```

------------------------------------------------------------------------

## 5) Visuals (Report Layout)

**Slicer :**

- Field : `DimType[TypeName]`

- Positioned on the left, containing all Pokémon types

<br>

**Table (Full List) :**

- Columns     : `BaseName`, `Attack`
  
- Aggregation : Sum of Attack
  
- Displays the entire dataset or is filtered by the slicer

<br>

**Table (Top 20 Strongest) :**

- Columns : `BaseName`, `Attack`

- Filter  : Top N = 20 by Attack

- Title   : "TOTAL ATTACK PER POKEMON (TOP 20 STRONGEST)"

<br>

**Bar Chart (Average Attack by Generation) :**

- Axis   : `DimGeneration[Generation]`
  
- Values : `[AvgAttack]`
  
- Title  : "Average of Attack and CountPokemon by Generation"

<br>

**Cards (KPIs) :**

- `[CountPokemon]`  → «Sum of Pokemon» (800)

- `[AvgAttack]`     → «Average of Attack» (79)

- `[TotalAttack]`   → «Total Attack» (63K)

- `[CountDualType]` → «Sum of Dual Type» (414)

- `[PctDualType]`   → «% of Dual Type» (51.8%)

------------------------------------------------------------------------

## 6) Validation

- CountPokemon shows 800 (entire dataset)

- AvgAttack ≈ 79

- TotalAttack ≈ 63K

- PctDualType = 51.8% (414 από 800)

- Values update dynamically when the Type slicer is used

- ------------------------------------------------------------------------

## 7) Visuals Preview

**Slicer (Pokémon Types)**

![Slicer](https://github.com/Chrysoula-Tsitsifa/POKEMON-ANALYSIS---POWER-BI/blob/main/SCREENSHOTS/REPORT%20VIEW/Slicer.png)

<br>

**Table – Total Attack per Pokémon**

![Full List](https://github.com/Chrysoula-Tsitsifa/POKEMON-ANALYSIS---POWER-BI/blob/main/SCREENSHOTS/REPORT%20VIEW/Total%20Attack%20per%20Pokemon.png)

<br>

**Table – Total Attack per Pokémon (Top 20 Strongest)**

![Top 20](https://github.com/Chrysoula-Tsitsifa/POKEMON-ANALYSIS---POWER-BI/blob/main/SCREENSHOTS/REPORT%20VIEW/Total%20Attack%20per%20Pokemon%20(Top%2020%20Strongest).png)

<br>

**Bar Chart – Average of Attack & CountPokemon by GenerationName**

![Avg Attack by Gen](https://github.com/Chrysoula-Tsitsifa/POKEMON-ANALYSIS---POWER-BI/blob/main/SCREENSHOTS/REPORT%20VIEW/Average%20Attack%20and%20CountPokemon%20by%20GenerationName.png)

<br>
<br>

**KPI – AvgAttack**

![KPIs](https://github.com/Chrysoula-Tsitsifa/POKEMON-ANALYSIS---POWER-BI/blob/main/SCREENSHOTS/MEASURES/AvgAttack.png)

<br>

**KPI – CountPokemon**

![KPIs](https://github.com/Chrysoula-Tsitsifa/POKEMON-ANALYSIS---POWER-BI/blob/main/SCREENSHOTS/MEASURES/CountPokemon.png)

<br>

**KPI – TotalAttack**

![KPIs](https://github.com/Chrysoula-Tsitsifa/POKEMON-ANALYSIS---POWER-BI/blob/main/SCREENSHOTS/MEASURES/TotalAttack.png)

<br>

**KPI – CountDualType**

![KPIs](https://github.com/Chrysoula-Tsitsifa/POKEMON-ANALYSIS---POWER-BI/blob/main/SCREENSHOTS/MEASURES/CountDualType.png)

<br>

**KPI – PctDualType**

![KPIs](https://github.com/Chrysoula-Tsitsifa/POKEMON-ANALYSIS---POWER-BI/blob/main/SCREENSHOTS/MEASURES/PctDualType.png)
