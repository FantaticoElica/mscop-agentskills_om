# MSCop Rule Summary

This document provides a short description of each MSCop rule to help identify which rule matches a user's compliance request. The detailed rule definition remains in the corresponding YAML file.

---

## AE — Always Execute

**Rule File:** `AlwaysExecute.yaml`
**ModuleType:** `Performance`

**Description:** Identifies fields configured to always execute instead of using cached values. The setting should be limited to cases where it is necessary.

**Useful when the user asks about:**
- fields that always execute
- `alwaysRun` settings
- unnecessary recalculation
- field caching and performance

---

## BIF — Big Int Fields

**Rule File:** `BigIntFields.yaml`
**ModuleType:** `Error`

**Description:** Identifies integer fields and related integer references that may hold values large enough to exceed the integer limit and need review for a more suitable type.

**Useful when the user asks about:**
- integer overflow risk
- large integer fields
- BigInt or integer type issues
- IDs that may exceed integer limits

---

## BO — Hard-coded BlankOption

**Rule File:** `BlankOption.yaml`
**ModuleType:** `Standards`
**Description:** Lists repeated hard-coded `blankOption` format-mask values. The rule is deprecated by Hard-coded Values and recommends using a field reference instead of repeating the same value.

**Useful when the user asks about:**
- hard-coded `blankOption`
- repeated blank-option values
- duplicate format masks
- deprecated BlankOption checks

---

## CI — Coverage Indicators

**Rule File:** `CoverageIndicator.yaml`
**ModuleType:** `Standards`
**Description:** Checks whether coverage nodes have a standard Indicator field used to turn coverage on or off, using the expected `Indicator` or `Indicators[Type='XXX']/bValue` path.

**Useful when the user asks about:**
- missing coverage indicators
- coverage on/off fields
- coverage Indicator paths
- coverage structure standards

---

## DF — Duplicate Form

**Rule File:** `DuplicateForm.yaml`
**ModuleType:** `Error`
**Description:** Reports document sets whose names are not unique within the current XML document.

**Useful when the user asks about:**
- duplicate forms
- repeated form names
- non-unique document sets
- duplicate documentSet entries

---

## DID — Duplicate ID

**Rule File:** `DuplicateID.yaml`
**ModuleType:** `Error`
**Description:** Detects duplicate `id` attributes on public, private, and object elements within the same ManuScript XML document.

**Useful when the user asks about:**
- duplicate IDs
- repeated IDs
- duplicate identifiers
- the same ID appearing multiple times

---

## FE — Form Exists

**Rule File:** `FormExists.yaml`
**ModuleType:** `Standards`
**Description:** Reports `printDoc` references whose named document-set form does not exist in the same XML document.

**Useful when the user asks about:**
- missing forms referenced by printDoc
- invalid form references
- forms that do not exist
- broken document-set references

---

## FNOJ — Form Not On Job

**Rule File:** `FormNotOnJob.yaml`
**ModuleType:** `Standards`
**Description:** Lists forms that exist in the ManuScript but are not associated with any print jobs.

**Useful when the user asks about:**
- forms not on a job
- unused forms
- forms with no print-job association
- unassigned forms

---

## HCP — Hard-coded Paths

**Rule File:** `HardCodedPaths.yaml`
**ModuleType:** `Standards`
**Description:** Detects hard-coded file paths or URLs in Value or Default elements that may need to be externalized for environment-specific configuration.

**Useful when the user asks about:**
- hard-coded paths
- embedded URLs
- environment-specific file locations
- paths in Value or Default elements

---

## HCV — Hard-coded Values

**Rule File:** `HardCodedValues.yaml`
**ModuleType:** `Standards`
**Description:** Reports exact hard-coded value parameters on non-excluded public fields.

**Useful when the user asks about:**
- hard-coded field values
- literal values in public fields
- values that should be configurable
- hard-coded parameters

---

## HCVR — Hard-coded Values Rules

**Rule File:** `HardCodedValuesRules.yaml`
**ModuleType:** `Standards`
**Description:** Lists hard-coded values in the Then and Else Boolean Results of conditional value and miscellaneous rules on private fields, where table lookups may be preferred.

**Useful when the user asks about:**
- hard-coded rule values
- Then/Else Boolean Results
- hard-coded conditional values
- values in private-field rules

---

## IFS — Incorrect Field Scope

**Rule File:** `IncorrectFieldScope.yaml`
**ModuleType:** `Standards`
**Description:** Finds public fields under groups whose names include `Private`, and private fields under groups whose names include `Input` or `Output`, indicating a possible scope mismatch.

**Useful when the user asks about:**
- incorrect field scope
- public fields in private groups
- private fields in input/output groups
- field placement by scope

---

## IP — Incorrect Parent

**Rule File:** `IncorrectParent.yaml`
**ModuleType:** `Error`
**Description:** Identifies fields or groups that do not have the required group as their immediate parent, excluding groups directly under the model element.

**Useful when the user asks about:**
- incorrect parent elements
- fields outside groups
- invalid group nesting
- ManuScript hierarchy problems

---

## IM — Inheritance Maps

**Rule File:** `InheritanceMaps.yaml`
**ModuleType:** `Standards`
**Description:** Reports inheritance-map items defined under `properties/inheritance` and identifies the local name of each relevant child element.

**Useful when the user asks about:**
- inheritance maps
- inheritance properties
- inheritance-map entries
- fields or elements using inheritance

---

## IWR — Iterators With Restrictions

**Rule File:** `IteratorsWithRestrictions.yaml`
**ModuleType:** `Performance`
**Description:** Lists iterator fields with restrictions that do not use Advanced Query, so they can be reviewed for a potentially better-performing query approach.

**Useful when the user asks about:**
- iterators with restrictions
- iterator performance
- missing Advanced Query
- standard restrictions versus Advanced Query

---

## LUD — Incomplete Lookup Details

**Rule File:** `LookupDetails.yaml`
**ModuleType:** `Error`
**Description:** Lists fields with a lookup that has not supplied all parameters required for the lookup to work and may therefore crash when executed.

**Useful when the user asks about:**
- incomplete lookup configuration
- missing lookup parameters
- broken lookups
- lookup details that may cause crashes

---

## MML — Missing Max Length

**Rule File:** `MaxLength.yaml`
**ModuleType:** `Standards`
**Description:** Finds applicable public string fields without a constant MaxLength, a condition associated with shredding failures.

**Useful when the user asks about:**
- missing MaxLength
- string field length limits
- shredding failures
- fields without a constant maximum length

---

## MRD — Incomplete Misc Rule Information

**Rule File:** `MiscRulesDetails.yaml`
**ModuleType:** `Error`
**Description:** Finds fields with Miscellaneous Rules selected but no rules defined, which can cause a crash when the fields are evaluated.

**Useful when the user asks about:**
- incomplete miscellaneous rules
- missing misc rule definitions
- fields with no configured rules
- misc-rule crashes

---

## MFC — Missing Form Caption

**Rule File:** `MissingFormCaption.yaml`
**ModuleType:** `Standards`
**Description:** Lists forms without a defined form caption; Commercial Templates forms are expected to have captions.

**Useful when the user asks about:**
- missing form captions
- forms without captions
- undefined document captions
- Commercial Templates caption issues

---

## MG — Missing Standard Groups

**Rule File:** `MissingGroups.yaml`
**ModuleType:** `Standards`
**Description:** Finds public groups missing one or more of the standard child groups: Input, Output, or Private.

**Useful when the user asks about:**
- missing standard groups
- missing Input, Output, or Private groups
- public group structure
- incomplete group hierarchy

---

## MI — Missing Ignore Lookup/Calculation

**Rule File:** `MissingIgnore.yaml`
**ModuleType:** `Standards`
**Description:** Finds lookup fields without Ignore Lookup or a Default, and applicable numeric calculation fields without Ignore Calculation.

**Useful when the user asks about:**
- missing Ignore Lookup
- missing Ignore Calculation
- lookup defaults
- unsafe lookup or numeric calculation behavior

---

## MIR — Missing Iterator Reference

**Rule File:** `MissingIteratorReference.yaml`
**ModuleType:** `Error`
**Description:** Finds direct references to fields inside iterative groups where an Iterator field should be used to identify the correct iteration.

**Useful when the user asks about:**
- missing iterator references
- direct references inside iterative groups
- wrong iterator context
- references that default to the first iteration

---

## NPF — Non-Prepended Fields

**Rule File:** `NonPrependedFields.yaml`
**ModuleType:** `Standards`
**Description:** Lists fields whose IDs do not include the parent group ID prefix, except for recognized system-level fields and pure constants.

**Useful when the user asks about:**
- non-prepended field IDs
- missing parent ID prefixes
- field naming conventions
- IDs that do not include group prefixes

---

## OLF — OptionsList Field Problems

**Rule File:** `OptionsListFields.yaml`
**ModuleType:** `Standards`
**Description:** Finds fields with option-list problems, including invalid defaults, empty option sets, apparent data-type mismatches, or spaces in option values.

**Useful when the user asks about:**
- options list problems
- invalid option defaults
- empty option sets
- option data-type mismatches
- spaces in option values

---

## PAO — Page Analysis Overview

**Rule File:** `PageAnalysisOverview.yaml`
**ModuleType:** `Performance`
**Description:** Provides high-level information about all pages in the ManuScript as raw data for further analysis.

**Useful when the user asks about:**
- page analysis
- page overview
- page-level ManuScript information
- page inventory or statistics

---

## CBC — Cancel Button Companions

**Rule File:** `PopUpCancelButtonTypes.yaml`
**ModuleType:** `Standards`
**Description:** Identifies pages whose cancel buttons may need both delete and custom/cancel commands, so the button behavior can be reviewed for the relevant iteration.

**Useful when the user asks about:**
- cancel button types
- cancel button companions
- popup cancel behavior
- missing delete or custom/cancel commands

---

## SF — Select Field Problems

**Rule File:** `SelectFields.yaml`
**ModuleType:** `Standards`
**Description:** Finds fields using Select that may lack a default when no case matches or may contain values whose data type does not match the overall field type.

**Useful when the user asks about:**
- Select field problems
- missing Select defaults
- Select case and otherwise behavior
- Select value data-type mismatches

---

## TKS — Spaces in Table Keys

**Rule File:** `TableKeySpaces.yaml`
**ModuleType:** `Standards`
**Description:** Lists string table keys with spaces in their values or names, which conflicts with the standard against spaces in table keys.

**Useful when the user asks about:**
- spaces in table keys
- table key naming
- string keys containing spaces
- table lookup key standards

---

## XDS — XPath Double Slash

**Rule File:** `XPathDoubleSlash.yaml`
**ModuleType:** `Performance`
**Description:** Finds field parameters containing XPath `//` expressions, which can be expensive and may be replaceable with explicit paths for better performance.

**Useful when the user asks about:**
- XPath double slash
- `//` in XPath
- expensive descendant XPath expressions
- XPath performance or explicit paths

---
