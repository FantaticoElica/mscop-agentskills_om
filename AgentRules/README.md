# MSCop AgentRules

## Source of truth

Original XSL files remain authoritative for rule semantics. Curated YAML files define the executable representation of those semantics.

A rule is considered executable only when its YAML provides a faithful and deterministic representation of the source rule, including:

- target and scope
- candidate predicates
- comparisons and validation logic
- exclusions
- evidence and reporting semantics
- deterministic validation procedure
- execution constraints

Generated skeletons produced by `generate_agentrules.ps1` are explicitly marked `REQUIRES_REVIEW` with `validation: PENDING_VALIDATION` and must not be treated as executable rules.

For validated rules, the YAML `command_template` is the authoritative implementation of the validation logic. Runtime transport, quoting, iteration, and result collection may adapt as necessary to execute that command reliably, but must not change its validation semantics.

## Inventory

| Rule ID | Rule Name | Source XSL | Status | Validation |
|---|---|---|---|---|
| AE | Always Execute | AlwaysExecute.xsl | REQUIRES_REVIEW | PENDING_VALIDATION |
| BIF | Big Int Fields | BigIntFields.xsl | REQUIRES_REVIEW | PENDING_VALIDATION |
| BO | Hard-coded BlankOption | BlankOption.xsl | REQUIRES_REVIEW | PENDING_VALIDATION |
| CBC | Cancel Button Companions | PopUpCancelButtonTypes.xslt | READY | VALIDATED |
| CI | Coverage Indicators | CoverageIndicator.xsl | READY | VALIDATED |
| DF | Duplicate Form | DuplicateForm.xsl | READY | VALIDATED |
| DID | Duplicate ID | DuplicateID.xsl | READY | VALIDATED |
| FE | Form Exists | FormExists.xsl | READY | VALIDATED |
| FNOJ | Form Not On Job | FormNotOnJob.xsl | READY | VALIDATED |
| HCP | Hard-coded Paths | HardCodedPaths.xsl | READY | VALIDATED |
| HCV | Hard-coded Values | HardCodedValues.xsl | READY | VALIDATED |
| HCVR | Hard-coded Values Rules | HardCodedValuesRules.xsl | REQUIRES_REVIEW | PENDING_VALIDATION |
| IFS | Incorrect Field Scope | IncorrectFieldScope.xsl | REQUIRES_REVIEW | PENDING_VALIDATION |
| IM | Inheritance Maps | InheritanceMaps.xsl | READY | VALIDATED |
| IP | Incorrect Parent | IncorrectParent.xsl | READY | VALIDATED |
| IWR | Iterators With Restrictions | IteratorsWithRestrictions.xsl | READY | VALIDATED |
| LUD | Incomplete Lookup Details | LookupDetails.xsl | REQUIRES_REVIEW | PENDING_VALIDATION |
| MFC | Missing Form Caption | MissingFormCaption.xsl | READY | VALIDATED |
| MG | Missing Standard Groups | MissingGroups.xsl | READY | VALIDATED |
| MI | Missing Ignore Lookup/Calculation | MissingIgnore.xsl | READY | VALIDATED |
| MIR | Missing Iterator Reference | MissingIteratorReference.xsl | READY | VALIDATED |
| MML | Missing Max Length | MaxLength.xsl | READY | VALIDATED |
| MRD | Incomplete Misc Rule Information | MiscRulesDetails.xsl | READY | VALIDATED |
| NPF | Non-Prepended Fields | NonPrependedFields.xsl | READY | VALIDATED |
| OLF | OptionsList Field Problems | OptionsListFields.xsl | READY | VALIDATED |
| PAO | Page Analysis Overview | PageAnalysisOverview.xsl | READY | VALIDATED |
| SF | Select Field Problems | SelectFields.xsl | READY | VALIDATED |
| TKS | Spaces in Table Keys | TableKeySpaces.xsl | READY | VALIDATED |
| XDS | XPath Double Slash | XPathDoubleSlash.xsl | READY | VALIDATED |

## Execution policy

- `READY` + `validation: VALIDATED`: the rule is eligible for deterministic evaluation when its required semantic fields and runtime inputs are available.
- `REQUIRES_REVIEW` or `validation: PENDING_VALIDATION`: the rule remains unresolved and must not emit confirmed violations.
- `TRANSLATED` is not an executable status and must not be used for new output.
- A search result is a candidate only until all rule conditions, verification requirements, and exclusions have been evaluated.
- Confirmed violations must be supported by the rule's deterministic validation procedure and evidence.
- Transport, quoting, escaping, iteration, and result-collection failures may be handled by runtime orchestration without changing the rule's validation semantics.
- A rule is considered incomplete only when its authoritative validation procedure cannot be executed after permitted runtime handling, or when another required completeness condition is not satisfied.
- Current application files are the evidence source. Prior reports and logs are ignored for current validation results.

## Exclusions and scope

Exclusion membership is runtime-dependent on the selected LOB/configuration.

`AgentRules/Exclusions.yaml` is the authoritative current exclusion configuration when a rule explicitly references it. Legacy `CustomExclusions` and `BaseExclusions` expressions retained from the source XSL are preserved for traceability and source-semantic mapping; they do not automatically override the current runtime exclusion configuration.

The original XSL remains the authoritative source for determining the intended rule semantics.

## Deterministic execution

Each validated rule should expose a deterministic validation procedure that can be executed against the current application files.

The validation procedure must preserve the semantics of the source XSL while making the execution contract explicit. In particular:

1. The rule defines what constitutes a candidate.
2. The rule defines the exact conditions that constitute a violation.
3. The rule defines applicable exclusions.
4. The rule defines how candidates are verified.
5. The rule defines the evidence required for a confirmed violation.
6. The rule defines a deterministic `command_template` or equivalent validation implementation.
7. Runtime orchestration may handle file iteration, command transport, quoting, escaping, invocation, and result collection, but may not substitute a different validator or alter the rule's semantics.

A candidate must therefore remain a candidate until the deterministic validation procedure confirms that it satisfies the rule's violation predicate.

## Run-all readiness

The AgentRules are designed to support deterministic evaluation of multiple rules in a single execution.

For a run-all operation:

- only `READY` / `VALIDATED` rules are eligible for confirmed evaluation;
- unresolved rules remain unresolved rather than producing guessed violations;
- each rule executes using its own authoritative validation procedure;
- results must retain rule identity and evidence;
- failures in one rule must not silently alter the semantics of another rule;
- final reporting should distinguish confirmed violations, candidates, skipped items, and execution failures.

The current rule set should therefore be treated as a collection of independent deterministic validators that can be orchestrated by a common execution layer.

## Coverage

29 standalone rule-like modules are represented in `AgentRules`. Shared configuration, exclusion, documentation, and template files are excluded from the 29-rule count.