````markdown
---
name: application-compliance
...
---

# MSCop Application Compliance Analysis

Analyze only the application path supplied by the user.

The current application filesystem is the authoritative source for application
code and XML data.

---

# 0. Execution Contract — Follow This Order Exactly

For every compliance request, follow this execution order exactly:

1. Read and follow this ApplicationCompliance SKILL.md.
2. Determine which rule(s) the user requested.
3. Use RULE_SUMMARY.md as instructed by this Skill to resolve the requested rule ID(s).
4. Open and read the complete corresponding AgentRule YAML file(s).
5. Use only the selected AgentRule(s) to determine what must be validated.
6. Analyze only the application path supplied by the user.
7. During normal filesystem traversal, apply FileFolderExclusions.yaml:
   - matching folder/file → skip it completely
   - non-matching folder/file → continue traversal
8. Apply the selected AgentRule validation procedure to the remaining applicable files.
9. For every candidate produced by the selected AgentRule, evaluate AgentRules/Exclusions.yaml.
10. Remove candidates excluded by AgentRules/Exclusions.yaml.
11. Verify the remaining findings against the current application filesystem.
12. Return only confirmed violations.

The execution order is:

User Request
    ↓
ApplicationCompliance SKILL.md
    ↓
RULE_SUMMARY.md
    ↓
Selected AgentRule YAML
    ↓
Current application path
    ↓
FileFolderExclusions.yaml during traversal
    ↓
Selected AgentRule validation
    ↓
AgentRules/Exclusions.yaml
    ↓
Evidence verification
    ↓
Final result

Do not introduce any additional execution stage, validator, filtered application,
alternate filesystem, secondary rule-selection mechanism, or separate exclusion
pipeline.

The application path supplied by the user remains the same throughout execution.

FileFolderExclusions.yaml is only for deciding whether a file or folder should
be analyzed.

AgentRules/Exclusions.yaml is only for deciding whether an already-produced
candidate should be excluded.

Do not search for, reconstruct, or apply legacy runtime exclusion mechanisms
such as CustomExclusions or BaseExclusions unless the selected AgentRule
explicitly requires them as part of its current executable validation procedure.

Do not use previous scan results, generated reports, caches, logs, memory, or
other historical results as application evidence.

# 1. User Request Contract

The user prompt is intentionally lightweight.

The user provides only:

1. The requested rule selection.
2. The application/codebase path.

The user does NOT need to provide the detailed execution instructions
contained in this Skill or in the AgentRule YAML.

The user may request:

### A. One specific rule

Example:

```text
Run HCP on:

C:\Users\Om Annasaheb\Desktop\MSCop\Policy\ManuScripts
````

### B. Multiple explicitly named rules

Example:

```text
Run HCP, DID and MFC on:

C:\Users\Om Annasaheb\Desktop\MSCop\Policy\ManuScripts
```

This means:

> Execute exactly HCP, DID and MFC.

Do not execute unrelated rules.

### C. All rules

Example:

```text
Run all MSCop rules on:

C:\Users\Om Annasaheb\Desktop\MSCop\Policy\ManuScripts
```

The word `all` means:

> Discover the complete rule catalog and execute every rule that is currently
> executable according to its AgentRule status and available semantics.

Do not interpret `all` as "all rules currently being tested."

---
D. ModuleType/Tag-Based Rule Selection

The user may request all rules belonging to a ModuleType/tag, for example:

`Run all Standards rules on: <path>`

The agent resolves the ModuleType/tag through `RULE_SUMMARY.md`.

After resolution:

- Exactly one matching rule → Specific Rule Mode.
- Multiple matching rules → Multiple Rule Mode.
- No matching rules → No unrelated rules are executed.
---

# 2. Rule Selection

The Skill is responsible for determining which rules the user requested.

The rule selection flow is:

```text
User Request
    ↓
Identify requested rule selection
    ↓
RULE_SUMMARY.md
    ↓
Resolve requested rule ID(s)
    ↓
AgentRules/<Rule>.yaml
```

There are three possible execution modes.

## Specific Rule Mode

If the user requests one rule:

```text
Run HCP
```

identify HCP through RULE_SUMMARY.md and execute only the corresponding
AgentRule.

Do not execute unrelated rules.

---

## Multiple Rule Mode

If the user explicitly names multiple rules:

```text
Run HCP, DID and MFC
```

resolve each requested rule independently.

For example:

```text
HCP → AgentRules/HardCodedPaths.yaml
DID → AgentRules/DuplicateID.yaml
MFC → AgentRules/MissingFormCaption.yaml
```

Execute exactly those rules.

Do not add unrelated rules.

Each rule must be evaluated independently using its own AgentRule.

One rule's semantics must never be applied to another rule.

---

## All-Rules Mode

If the user requests:

```text
Run all MSCop rules
```

or equivalent general compliance wording:

```text
Check this application for MSCop compliance
```

then:

1. Read RULE_SUMMARY.md.
2. Identify the complete available rule catalog.
3. Open the corresponding AgentRule YAML files.
4. Determine which rules are executable.
5. Execute every executable rule.
6. Keep unresolved rules separate.
7. Do not treat unresolved rules as confirmed violations.
8. Do not silently skip an executable rule.

---

### ModuleType / Tag-Based Rule Selection

The user may select rules by requesting all rules belonging to a specified
ModuleType/tag, for example:

- `Run all Standards rules on: <application path>`
- `Run all Performance rules on: <application path>`
- `Run all Error rules on: <application path>`

ModuleType/tag selection is a rule-selection mechanism only. It does not
introduce a new execution pipeline, validation mechanism, exclusion mechanism,
evidence mechanism, or reporting format.

When the user requests rules by ModuleType/tag, the agent MUST:

1. Read `RULE_SUMMARY.md`.
2. Identify every rule whose `ModuleType` matches the requested ModuleType/tag.
3. Resolve each matching rule to its corresponding `AgentRules/<Rule>.yaml`.
4. Determine the number of resolved rules.
5. If exactly one rule is resolved, execute that rule using the existing
   **Specific Rule Mode**.
6. If more than one rule is resolved, execute those rules using the existing
   **Multiple Rule Mode**.
7. If no rules are resolved, do not execute unrelated rules. Report that no
   rules were found for the requested ModuleType/tag.
8. After ModuleType/tag resolution, the ModuleType/tag itself has no further
   role in execution.

The ModuleType/tag MUST NOT be hard-coded to a fixed list of Rule IDs.
The matching rules MUST be discovered dynamically from `RULE_SUMMARY.md`.

Once the matching Rule ID(s) have been resolved, all subsequent processing
MUST be identical to the corresponding existing execution mode:

- Exactly one resolved rule → existing Specific Rule Mode.
- More than one resolved rule → existing Multiple Rule Mode.

This means the selected AgentRule YAML(s), validation procedures,
FileFolderExclusions, candidate-level exclusions, evidence verification,
result states, and HTML reporting remain governed by the existing Skill
rules.
# 3. RULE_SUMMARY.md

Before opening individual AgentRule YAML files, read:

`C:\Users\Om Annasaheb\Desktop\MSCop\AgentRules\RULE_SUMMARY.md`

RULE_SUMMARY.md is the MSCop rule-discovery/index layer.

Use it to determine which AgentRule YAML corresponds to the user's request.

Example:

```text
User:
"Check duplicate IDs."

        ↓

RULE_SUMMARY.md

        ↓

DID — Duplicate ID

        ↓

AgentRules/DuplicateID.yaml
```

RULE_SUMMARY.md is used for:

* rule discovery

* rule-name matching

* rule-ID resolution

* ModuleType/tag matching

* mapping a user request to an AgentRule file

RULE_SUMMARY.md is NOT the authoritative source for detailed rule semantics.

Do not infer rule behavior from the summary when the detailed AgentRule is
available.

---

# 4. Detailed AgentRule

After identifying the requested rule(s), open the corresponding AgentRule
YAML file.

Read the complete selected AgentRule before analyzing the application.

The detailed AgentRule is authoritative for rule-specific behavior, including:

* target
* target elements
* target attributes
* scope
* locations
* conditions
* Boolean logic
* comparison semantics
* case sensitivity
* whitespace behavior
* normalization
* patterns
* exclusions
* expected behavior
* violation condition
* evidence requirements
* validation procedures
* execution instructions
* original XSL traceability

Do not infer detailed rule semantics from:

* RULE_SUMMARY.md
* user assumptions
* previous compliance results
* memory
* unrelated AgentRules
* generic XML conventions

The Skill defines HOW the agent executes a rule.

The AgentRule defines WHAT that rule means.

---

# 5. Separation of Responsibilities

Maintain the following separation.

## Skill

The Skill defines:

* workflow
* rule discovery
* rule selection
* filesystem handling
* validation execution
* freshness
* evidence verification
* error handling
* unresolved-rule handling
* output states
* multi-rule orchestration

## RULE_SUMMARY.md

RULE_SUMMARY.md defines:

* which rule exists
* rule ID
* rule name
* short description
* keywords
* mapping to AgentRule



## AgentRule YAML

The AgentRule defines:

- exact rule semantics
- target
- conditions
- patterns
- comparisons
- validation procedure
- evidence
- expected condition
- violation condition
- source traceability
- rule-specific exclusion semantics, when explicitly defined

The AgentRule does NOT define the repository-wide configurable exclusion
registry.

The repository-wide configurable exclusion registry is defined by:

AgentRules/Exclusions.yaml

## Application

The application filesystem provides:

* actual XML/code
* actual values
* actual structures
* actual evidence

Do not move rule-specific semantics into this Skill.

---

# 6. Application Path Boundary

The path supplied by the user is the application-analysis boundary.

If the user supplies a directory:

```text
C:\Users\Om Annasaheb\Desktop\MSCop\Policy\ManuScripts
```

recursively analyze the application files under that directory according to
the selected AgentRule(s).

If the user supplies a specific XML file, analyze only that file.

Do not require the user to explicitly state:

* recursive scanning
* XML parsing
* current filesystem usage
* evidence verification
* cache invalidation
* output requirements

These are Skill responsibilities.

Do not expand the application boundary to unrelated application directories.

The selected AgentRule may require an external source only when that source is
explicitly defined as part of its current executable validation procedure.

Do not search for, reconstruct, or introduce additional rule sources, exclusion
sources, legacy exclusion mechanisms, reports, or historical implementations.

The runtime exclusion mechanisms are limited to:

1. AgentRules/FileFolderExclusions.yaml
   → determines whether a file or folder is analyzed.

2. AgentRules/Exclusions.yaml
   → determines whether an already-produced candidate is excluded.

No additional exclusion mechanism may be introduced unless the selected
AgentRule explicitly defines it as a required current runtime dependency.

---
 # 6.1 File/Folder Exclusion During Traversal

During analysis of the application path, the agent MUST apply the
file/folder exclusion rules while traversing the filesystem.

The file/folder exclusion configuration is:

C:\Users\Om Annasaheb\Desktop\MSCop\AgentRules\FileFolderExclusions.yaml

This file defines files and folders that must be skipped during normal
filesystem traversal.

The file/folder exclusion mechanism is a filesystem traversal rule and is
independent of the individual AgentRule semantics.

It does not create a separate analysis stage, preprocessing stage,
filtered application, or alternate execution path.

## Required Traversal Behavior

When analyzing a directory:

1. Begin traversal from the application path supplied by the user.

2. Before entering a directory, evaluate that directory's name against the
   configured file/folder exclusion list.

3. If the directory matches a configured exclusion:
   - skip the entire directory
   - do not enter the directory
   - do not inspect files inside the directory
   - do not execute any AgentRule against files inside the directory
   - do not generate candidates from files inside the directory

4. If the directory does not match, continue traversing its contents.

5. Before analyzing an individual file, evaluate the file name/path against
   the configured file/folder exclusion list.

6. If the file matches a configured exclusion:
   - skip the entire file
   - do not execute the selected AgentRule against the file
   - do not generate candidates from the file
   - do not apply candidate-level exclusions to the file

7. If neither the containing directory nor the file matches an exclusion,
   continue with the selected AgentRule's normal validation procedure.

## Matching Semantics

File/folder exclusion matching MUST be determined from the actual
structure and values of:

AgentRules/FileFolderExclusions.yaml

The agent MUST:

1. Read the complete FileFolderExclusions.yaml.
2. Read its `matching` configuration.
3. Read its configured `exclusions` entries.
4. Respect each exclusion entry's:
   - `type`
   - `name`
   - `matching` semantics
   - configured reason
5. Apply the configured matching semantics exactly as defined.

For the current configuration, this means:

- comparison: contains
- case_sensitive: false
- whitespace_sensitive: true
- normalization: none

Do NOT extract arbitrary `name:` values from the YAML and treat them
as exclusions.

Do NOT hard-code DCT or any other exclusion.

Do NOT create a separate exclusion list from the YAML.

The YAML configuration itself is the single source of truth.

## Mandatory Failure Condition

If FileFolderExclusions.yaml cannot be loaded, parsed, or evaluated
according to its configured structure and matching semantics:

STOP the compliance analysis.

Do NOT continue with an unfiltered filesystem scan.

Return an incomplete analysis explaining that the file/folder exclusion
configuration could not be applied.

## Evaluation Order

The file/folder exclusion check MUST happen before AgentRule validation.

The required execution order is:

Application Path
    ↓
Filesystem Traversal
    ↓
Check Folder Against FileFolderExclusions.yaml
    ↓
MATCH → Skip Entire Folder
    ↓
NO MATCH
    ↓
Continue Into Folder
    ↓
Check File Against FileFolderExclusions.yaml
    ↓
MATCH → Skip Entire File
    ↓
NO MATCH
    ↓
Selected AgentRule
    ↓
Deterministic Validation Procedure
    ↓
Candidate(s)
    ↓
AgentRules/Exclusions.yaml
    ↓
Candidate-Level Exclusion Evaluation
    ↓
Evidence Verification
    ↓
Confirmed Violations

## Separation From Candidate-Level Exclusions

File/folder exclusions and candidate-level exclusions are separate mechanisms.

`FileFolderExclusions.yaml` answers:

"Should this file or folder be analyzed at all?"

`AgentRules/Exclusions.yaml` answers:

"A candidate was found. Should this particular candidate be excluded?"

A file/folder exclusion MUST be evaluated before the selected AgentRule.

A candidate-level exclusion MUST be evaluated only after the selected
AgentRule has produced a candidate.

A file that was skipped by `FileFolderExclusions.yaml` MUST NOT reach
candidate-level exclusion evaluation.

## No Semantic Interpretation

The agent MUST NOT use AgentRule semantics to decide whether a file or folder
should be skipped.

The agent MUST NOT inspect XML content to determine whether a file should be
skipped when its path or containing folder already matches a configured
file/folder exclusion.

The purpose of this pre-check is to avoid unnecessary validation of files and
folders that are explicitly excluded from analysis.

# 7. Mandatory Execution Flow

For every compliance request, follow:

```text
User Request

    ↓

Determine rule selection

    ↓

Read RULE_SUMMARY.md

    ↓

Resolve requested rule(s)

    ↓

If ModuleType/tag selection was requested:
    ↓
    Identify every rule whose ModuleType matches the requested tag
    ↓
    Determine the number of matching rules
    ↓
    ├── Exactly 1 matching rule
    │       ↓
    │   Continue using Specific Rule Mode
    │
    └── More than 1 matching rule
            ↓
        Continue using Multiple Rule Mode

    ↓

Open selected AgentRule YAML file(s)
    ↓
Read complete AgentRule(s)
    ↓
Determine validation procedure
    ↓
Analyze supplied application/codebase
    ↓
During filesystem traversal:
    ↓
Check current folder/file against FileFolderExclusions.yaml
    ↓
MATCH → skip folder/file
    ↓
NO MATCH → continue normal traversal
    ↓
Apply selected AgentRule to applicable files
    ↓
Apply rule conditions
    ↓
Apply AgentRules/Exclusions.yaml to candidates
    ↓
Verify evidence
    ↓
Determine result
    ↓
Return result
```
### File/Folder Exclusion Is Part of Normal Traversal

File/folder exclusions do not constitute a separate analysis pipeline,
validator, preprocessing stage, or alternate application path.

The application path supplied by the user remains the same throughout
the analysis.

While the selected AgentRule analyzes that application path, the agent
must check `AgentRules/FileFolderExclusions.yaml` whenever it encounters
a directory or file.

A matching directory is skipped without entering it.

A matching file is skipped without applying the selected AgentRule to it.

A non-matching directory or file continues through the normal execution
flow of the selected AgentRule.

Do not create a separate filtered application, filtered file list,
filesystem copy, validator, wrapper, or alternate analysis pipeline
solely for file/folder exclusions.

Do not skip rule discovery.

Do not skip reading the selected AgentRule.

Do not substitute generic assumptions for AgentRule semantics.

---

# 8. Rule Execution

For every selected rule:

1. Read the complete AgentRule.
2. Identify its target and scope.
3. Identify its validation/evaluation procedure.
4. Apply that procedure to the current application filesystem.
5. Preserve the exact semantics defined by the AgentRule.
6. Evaluate the complete rule conditions.
7. Evaluate applicable exclusion/context requirements according to the
   AgentRule.
8. Verify every reported finding against the current filesystem.
9. Report only confirmed violations.

Preserve:

* target
* scope
* Boolean grouping
* comparison semantics
* case sensitivity
* whitespace behavior
* normalization behavior
* required context
* exclusions
* evidence requirements

Do not invent requirements.

Do not broaden a rule beyond its AgentRule or validated original MSCop
semantics.

---
# Deterministic Validation Procedures
# Deterministic Validation Procedures

When an AgentRule provides a deterministic validation or evaluation procedure, that procedure is the authoritative execution procedure for that rule.

If the AgentRule contains a `validation_procedure.command_template`, the agent MUST use that command template as the authoritative validation logic.

The existing `validation_procedure.command_template` is the complete validation implementation. The agent MUST NOT recreate, rewrite, simplify, translate, reinterpret, or replace its validation logic.
Semantic equivalence is NOT sufficient.

An independently implemented procedure is prohibited even when the agent believes that it produces the same results as the command_template.

The authoritative command_template itself must be executed.

The command_template is the complete validation implementation.

Only the transport or invocation mechanism may be adapted.

Transport or invocation adaptation MUST NOT change, recreate, rewrite,
simplify, translate, reinterpret, or replace the command_template or any
validation logic contained within it.

Transport adaptation means only changing how the unchanged command_template
is delivered to or invoked by the execution environment.

The following are NOT transport adaptations:

- rewriting XPath;
- changing XPath predicates or selectors;
- changing regex;
- changing comparers;
- changing grouping or filtering;
- changing thresholds or conditions;
- changing XML traversal logic;
- implementing equivalent logic in another language;
- replacing the command_template with manual inspection;
- replacing the command_template with LLM reasoning;
- constructing a new validator that is intended to produce equivalent results.

If the unchanged command_template cannot be executed using the available
permitted transport or invocation mechanisms, the rule MUST be reported as
Incomplete.

Transport difficulty is never permission to create an alternative
validation implementation.
## Direct Execution

The execution strategy is fixed by the AgentRule's validation_procedure.

If validation_procedure.per_file is true, the existing command_template MUST be invoked once for every applicable file.

If validation_procedure is application-level, the existing command_template MUST be invoked according to its declared application-level inputs.

The agent MUST NOT select a different validation strategy based on convenience, performance, output size, shell complexity, or implementation difficulty.

### Command Transport Integrity

When invoking an existing command_template, the agent MUST preserve the
command_template's exact quoting, escaping, XPath, regex, selectors,
comparers, grouping, and other validation syntax.

The execution transport MUST NOT strip, reinterpret, or rewrite quoting or
escaping contained in the command_template.

If the command_template requires runtime substitution such as an XML file
path, only the explicitly required runtime value may be supplied.

The agent MUST NOT reconstruct the command as a new shell string in a way
that changes the command_template's quoting or escaping semantics.

If an initial invocation or transport attempt fails because of shell quoting,
escaping, argument transport, command construction, invocation mechanics, or
other runtime transport issues, this MUST NOT by itself cause the affected
rule to be marked Incomplete.

The agent MUST distinguish command transport/invocation failure from failure
of the command_template's validation logic.

The agent MUST retry the exact same command_template without modifying its
text or validation semantics.

A retry MAY change only the external mechanism used to deliver, invoke, or
transport the unchanged command_template and to supply its explicitly
required runtime values.

A retry MUST NOT modify, rewrite, simplify, translate, reinterpret,
reconstruct, inline, extract, or replace any part of the command_template's
validation logic.

In particular, a retry MUST NOT convert the command_template into an
alternative PowerShell command, XPath expression, regex, parser, traversal,
comparison, grouping, filtering operation, or other implementation that is
intended to produce equivalent results.

If the unchanged command_template cannot be successfully executed after
permitted transport or invocation retries, the affected rule MUST be marked
Incomplete.

Transport failure is not permission to implement the rule by another method.

The agent MUST distinguish command transport/invocation failure from failure
of the command_template's validation logic.

The agent MUST retry the existing command_template using permitted
in-memory/runtime orchestration that preserves the command_template itself
unchanged.

A retry MAY change only the mechanism used to transport or invoke the existing
command_template and to supply its explicitly required runtime values.

A retry MUST NOT modify, rewrite, simplify, reinterpret, reconstruct, or
replace any validation logic contained in the command_template.

Only if the existing command_template still cannot be executed after
permitted runtime transport/invocation attempts may the affected rule be
marked Incomplete.

The agent MAY provide only the runtime values required by the existing command template, such as:

- the supplied application path, when the command template operates on an application root;
- an applicable XML/file path, when the command template requires an individual file;
- other runtime parameters explicitly required by the existing command template.

The agent MUST NOT modify the command template itself.

The agent MUST NOT:

- create a new PowerShell implementation of the rule;
- recreate the rule's XPath, regex, selectors, comparers, grouping logic, thresholds, or conditions;
- replace the command with LLM-based inspection;
- substitute another comparer, XPath, regex, parser, or grouping implementation;
- simplify or reinterpret the deterministic validation logic;
- write the command template into a new file and execute that file;
- create or persist a new `.ps1`, validator, generated source file, executable artifact, or other persistent execution artifact solely for AgentRule validation.

### Transport Failure and Retry

The agent MUST NOT treat a failed shell invocation, quoting error, escaping
error, continuation state, argument-passing error, or equivalent transport
failure as a failure of the AgentRule itself.

When such a transport failure occurs:

1. Preserve the original command_template exactly.
2. Identify that the failure occurred during command transport or invocation,
   rather than during validation logic.
3. Retry the same command_template using permitted runtime-only orchestration.
4. Supply only the runtime values explicitly required by the command_template.
5. Preserve all XPath, regex, selectors, conditions, comparisons, grouping,
   thresholds, quoting, and escaping semantics of the original command.
6. Continue execution for all applicable inputs after successful invocation.

The agent MAY use an execution mechanism that safely transports the existing
command_template without reconstructing its validation logic.

For example, if embedding a command_template inside another shell command
causes quoting to be reinterpreted, the agent MUST use a permitted runtime
invocation approach that preserves the original command text and semantics,
rather than immediately declaring the rule Incomplete.

The agent MUST NOT create a substitute validator merely to avoid a transport
problem.

A rule may be marked Incomplete only when the authoritative command_template
itself cannot be successfully executed after permitted transport and
invocation handling, or when another Skill-defined completeness requirement
cannot be satisfied.
## Runtime Orchestration

Runtime orchestration is permitted when it is required to invoke an existing AgentRule command template with the runtime inputs it requires.

Such orchestration is NOT a new validation procedure.

Its purpose is limited to:

1. determining the applicable files using the normal filesystem traversal and FileFolderExclusions rules;
2. supplying the required runtime value(s) to the existing command template;
3. invoking the existing command template;
4. collecting the raw result;
5. continuing until all applicable inputs have been evaluated.

Runtime orchestration MUST:

- remain in-memory/runtime-only;
- contain no recreated validation logic;
- invoke the existing command template without changing its semantics;
- not modify the application filesystem;
- not create a persistent script, validator, generated source file, or executable artifact.

### Required Per-File Orchestration

When an AgentRule is marked `per_file: true` and the user supplies an application directory/root rather than an individual file, the agent MUST use minimal in-memory/runtime orchestration to execute the existing command template once for each applicable file.

The required flow is:

```text
Supplied application root
        ↓
Normal filesystem traversal
        ↓
Apply FileFolderExclusions
        ↓
Determine applicable files
        ↓
For each applicable file:
        ↓
Provide that file as the command template's required runtime value
        ↓
Execute the EXISTING command_template unchanged
        ↓
Capture the raw result
        ↓
Continue to next applicable file
        ↓
All applicable files completed
        ↓
Combine raw results for this AgentRule
        ↓
Apply AgentRule conditions/exclusions
        ↓
Verify evidence against current filesystem
        ↓
Confirmed violations
```

For example, if a command template requires `<XML_FILE>`, the agent MUST invoke that exact command template separately for every applicable XML file:

```text
Applicable XML file 1 → existing command_template(XML_FILE=file1)
Applicable XML file 2 → existing command_template(XML_FILE=file2)
Applicable XML file 3 → existing command_template(XML_FILE=file3)
...
Applicable XML file N → existing command_template(XML_FILE=fileN)
```

The agent MUST NOT replace these individual invocations with a newly constructed validator that implements the rule itself.

The orchestration MAY contain ordinary control flow required to perform the repeated invocation, such as iteration over applicable files and collection of returned results. Such control flow is orchestration only and MUST NOT contain the AgentRule's validation semantics.

The agent MUST NOT interpret the prohibition on wrappers as a prohibition on this required runtime orchestration.

A runtime loop is permitted only when its operation is limited to supplying
the required runtime value and invoking the unchanged existing
`command_template`.

The runtime loop MUST NOT implement, reproduce, translate, simplify,
reinterpret, or substitute any part of the AgentRule's validation logic.

If the loop contains or reproduces the rule's XPath, regex, selectors,
comparisons, grouping, filtering, thresholds, conditions, or violation logic,
it is not runtime orchestration and MUST NOT be used as a substitute for the
existing `command_template`.

## Multiple Rules

When multiple AgentRules are selected, each AgentRule MUST be executed independently.

For each selected AgentRule:

1. read the complete AgentRule;
2. determine whether its procedure is application-level or `per_file`;
3. determine the applicable files using the normal traversal/exclusion process;
4. invoke its existing `command_template`;
5. capture the complete result for that rule;
6. apply that rule's conditions and exclusions;
7. verify that rule's evidence;
8. finalize that rule's confirmed violations.

Only after every selected AgentRule has produced a complete verified result may the agent aggregate the results for the final compliance response and HTML report.

One rule MUST NOT replace another rule's command template or validation semantics.

No selector, XPath, regex, comparer, scope, condition, exclusion, or evidence rule may leak from one AgentRule into another.

If five rules are selected, the intended execution is:

```text
Rule 1 → independently execute → complete result
Rule 2 → independently execute → complete result
Rule 3 → independently execute → complete result
Rule 4 → independently execute → complete result
Rule 5 → independently execute → complete result
                         ↓
                all five complete
                         ↓
                 aggregate results
                         ↓
                 generate HTML
                         ↓
             write + verify report
```

The number of selected rules does not authorize construction of a combined replacement validator.

## Current Filesystem and Exclusions

The deterministic procedure must operate on the current filesystem and the application path supplied by the user.

The deterministic procedure must operate only on files that remain applicable after normal filesystem traversal and FileFolderExclusions checks.

File/folder exclusions remain in force for the entire analysis.

A `per_file` command template MUST NOT independently rediscover excluded files or bypass the normal traversal/exclusion process.

The presence of an application root path does not authorize bypassing the required traversal and exclusion process.

## Authoritative Execution Flow

The complete execution flow is:

```text
Selected AgentRule
        ↓
Read complete AgentRule
        ↓
Determine validation_procedure
        ↓
Determine per_file/application scope
        ↓
Normal filesystem traversal
        ↓
Apply FileFolderExclusions
        ↓
Determine applicable runtime inputs
        ↓
Invoke EXISTING command_template
        ↓
For per_file rules:
    invoke once per applicable file
        ↓
Capture complete raw execution results
        ↓
Apply AgentRule conditions
        ↓
Apply applicable/evaluable exclusions
        ↓
Verify evidence against current filesystem
        ↓
Confirmed violations
```

If the AgentRule provides a deterministic procedure but that procedure cannot be executed, the agent MUST NOT silently replace it with an independently implemented procedure.

The correct result in that situation is `Incomplete`.

The agent MUST NOT claim `Clean` merely because execution returned zero results if the required applicable files were not actually evaluated.

The agent MUST distinguish:

- zero applicable files after valid traversal/exclusions;
- applicable files successfully evaluated with zero violations;
- execution failure;
- incomplete/unverified result collection.

Only successfully completed deterministic execution over the required applicable inputs may produce a final `Clean` or `Violations` result.

### No Generated Validation Scripts

AgentRule execution is not an instruction to generate implementation code.

When an AgentRule contains a PowerShell, XPath, regex, SQL, or other deterministic `command_template`, the agent MUST treat that content as the authoritative executable validation procedure.

The agent MUST NOT create a persistent implementation of the AgentRule's validation logic.

The agent MUST NOT:

- create a `.ps1` file containing or reproducing the AgentRule validation logic;
- create a temporary validator containing or reproducing the AgentRule validation logic;
- copy the `command_template` into another executable file;
- translate the `command_template` into different PowerShell, Python, JavaScript, SQL, XPath, regex, or other implementation;
- modify the selectors, XPath, regex, comparers, grouping, filtering, thresholds, or other validation semantics;
- replace the deterministic procedure with LLM reasoning or manual source inspection.

This prohibition does NOT prohibit the minimal in-memory/runtime orchestration explicitly permitted by this Skill.

For a `per_file: true` AgentRule, runtime orchestration MAY:

- enumerate applicable files after normal traversal and exclusions;
- iterate over those files;
- provide each file path as the runtime input required by the existing `command_template`;
- invoke the existing `command_template`;
- capture its result;
- continue to the next applicable file.

Such orchestration is permitted only because it invokes the existing deterministic procedure. It MUST NOT reproduce any validation logic from that procedure.

The following distinction is mandatory:

```text
ALLOWED:

Runtime orchestration
    ↓
file1 → existing command_template(file1)
file2 → existing command_template(file2)
file3 → existing command_template(file3)

NOT ALLOWED:

New wrapper
    ↓
reimplements XPath/regex/comparison/grouping/validation
    ↓
produces an independent result
```

The agent MUST NOT interpret "no generated validation scripts" or "no wrapper" as prohibiting the minimal in-memory/runtime orchestration required to invoke an existing `per_file` command template.

Only the existing AgentRule `command_template` defines the validation logic.
# Exclusion Evaluation

After a validation procedure identifies a candidate violation, the agent MUST evaluate the candidate against the configured exclusion file before reporting it as a confirmed violation.

The exclusion file is the authoritative source for configurable inclusion/exclusion rules.

For every candidate:

1. Extract the candidate attributes required by the applicable exclusion rules.
2. Load the exclusion configuration.
3. Select exclusion entries applicable to the candidate's rule.
4. Evaluate the candidate against each applicable exclusion entry using the comparison logic defined by that entry.
5. If an exclusion entry matches all of its required conditions, exclude the candidate from the final violation report.
6. If no exclusion entry matches, continue with normal confirmation and evidence validation.
7. Do not infer, invent, or broaden exclusion conditions.
8. Do not treat a partial match as an exclusion.
9. If an exclusion condition cannot be evaluated because required candidate information is unavailable, do not assume that it matches.

The final output must contain only confirmed violations that were not excluded.

# Result Collection and Output Limits

When multiple compliance rules are executed together, collect and validate each rule's results independently before producing the final compliance response.

The agent MUST NOT treat a truncated, incomplete, or partially captured result set as complete.

For each selected rule:

1. Execute the rule's deterministic validation procedure.
2. Collect the complete result set internally.
3. Apply the rule's conditions, exclusions, and evidence requirements.
4. Verify that the complete confirmed-violation result set has been captured.
5. Only then include that rule's confirmed violations in the final response.

When multiple rules are executed together, process them independently before combining their confirmed results.

If the combined result would exceed the available output capacity, the agent MUST use a bounded evidence representation rather than allowing the output to be silently truncated.

The agent MUST NOT:
- omit confirmed violations because of output limits,
- claim the scan is complete when the result set was truncated,
- infer missing violations from partial output,
- treat partially captured evidence as verified evidence.

If complete confirmed results cannot be captured or represented reliably, return:

MSCop Compliance Analysis

Analysis incomplete.

Explain which rule(s) could not be reliably captured and why.

If any requested rule cannot produce a complete verified result, the overall multi-rule compliance run MUST be reported as Incomplete.

Successfully completed rule results MAY be retained internally for diagnostic purposes, but they MUST NOT be presented as the final combined compliance conclusion.

The agent MUST NOT aggregate partial rule results into a Clean or Violations final state.
# 10. Candidate vs Confirmed Violation

A search result, text match, XPath result, regex match, or command result is
not automatically a confirmed violation.

A candidate becomes a confirmed violation only after the complete applicable
rule semantics have been evaluated.

Evaluate, as applicable:

1. target
2. scope
3. Boolean conditions
4. comparison semantics
5. required context
6. exclusions
7. expected condition
8. evidence requirements
9. current filesystem evidence

The exact requirements depend on the selected AgentRule.

Do not impose requirements from one rule onto another rule.

---

# 11. Candidate-Level Exclusion Handling

Candidate-level exclusions are evaluated using:

AgentRules/Exclusions.yaml

The selected AgentRule defines the candidate attributes and rule semantics
needed for evaluating the candidate, while AgentRules/Exclusions.yaml defines
the configured candidate-level exclusions.

## Implemented Exclusions

If the AgentRule defines an exclusion as implemented and provides sufficient
information to evaluate it:

* evaluate it
* apply it before confirming the violation

## Deferred Exclusions

If an AgentRule explicitly marks an exclusion as:

* DEFERRED
* NOT_IMPLEMENTED
* UNSUPPORTED
* PENDING

do not invent or infer the missing exclusion behavior.

Do not fabricate exclusion data.

Do not silently apply an exclusion that cannot actually be evaluated.

The selected AgentRule determines whether the current rule validation can
produce a confirmed result with the exclusion deferred.

If the AgentRule explicitly defines the current validation scope as detection
without the deferred exclusion, follow that defined scope.

If the AgentRule states that the missing exclusion prevents a confirmed
compliance conclusion, mark the analysis incomplete instead.

---

# 12. Source of Truth and Freshness

The current application filesystem is authoritative.

Do not use any of the following as a substitute for the current application
files:

* previous chat answers
* previous compliance reports
* `_compliance_output`
* logs
* caches
* memory
* stale generated results
* previous scan output

Always analyze the current filesystem.

Before reporting evidence, verify it against the current source file.

If the application has changed since a previous scan, perform the validation
again against the current filesystem.

Never assume a previous result is still valid.

---

# 13. Application Inventory

For directory-based analysis, recursively inventory the supplied application
root sufficiently for the selected rule(s).

Record material analysis limitations such as:

* unreadable files
* malformed XML
* parse failures
* unsupported files
* inaccessible paths
* incomplete traversal

Never silently treat a partial scan as clean.

A clean result requires that the requested rule(s) were sufficiently evaluated
over the relevant application scope.

---

# 14. Parse Failures

If an XML file cannot be parsed:

1. Record the file as a parse failure.
2. Do not invent compliance results for that file.
3. Continue analyzing other applicable files.
4. Include the limitation in the final result when it materially affects the
   requested compliance conclusion.

Do not classify an unscanned or unparseable file as clean.

---

# 15. Evidence Verification

Before reporting a confirmed violation, verify it against the current source
file.

Verify, as applicable:

* file path
* XML/code location
* element
* attribute
* ID
* field name
* value
* surrounding XML/code
* exact rule condition
* matched pattern
* relevant contextual information

Do not reconstruct evidence from:

* memory
* previous output
* cached results
* search snippets alone
* generated reports

Evidence must reflect the actual current filesystem.

If the evidence cannot be verified, do not report the candidate as a confirmed
violation.

---

# 16. Multiple Rule Execution

When multiple rules are explicitly requested:

```text
Run HCP, DID and MFC
```

execute exactly those rules.

Each rule is independent.

For example:

```text
HCP → HCP AgentRule
DID → DID AgentRule
MFC → MFC AgentRule
```

Do not allow one rule's:

* selectors
* XPath
* regex
* comparison semantics
* scope
* exclusions
* evidence requirements

to leak into another rule.

The filesystem traversal may be shared across selected rules for efficiency,
provided that the same FileFolderExclusions semantics are preserved.

However, shared traversal MUST NOT become shared validation.

Each selected AgentRule MUST independently invoke its own existing
validation_procedure.command_template, independently collect its raw results,
independently evaluate its conditions and exclusions, and independently verify
its evidence.

The agent MUST NOT construct one combined validation command containing the
logic of multiple AgentRules.

Shared traversal is permitted.
Shared validation logic is prohibited.

Each reported violation must identify its Rule ID.

For final HTML presentation, confirmed violations from multiple rules MUST be
grouped by source file first.

The required presentation hierarchy is:

Source File
    ↓
Rule
    ↓
Confirmed Violations

Each source file MUST appear only once in the Violations section, even when
multiple selected rules produce violations for that file.

Different rules MUST remain separate under that source file.

The logical evaluation of each rule remains independent. This requirement only
defines the final presentation order in the HTML report.

---

# 17. All-Rules Execution

When the user requests:

```text
Run all MSCop rules
```

do not assume a fixed subset of rules.

Instead:

```text
RULE_SUMMARY.md
      ↓
Complete rule catalog
      ↓
Read AgentRule YAML files
      ↓
Determine executable rules
      ↓
Execute every executable rule
      ↓
Identify unresolved rules
      ↓
Return aggregate result
```

A rule marked unresolved, pending, or requiring review must not be treated as a
confirmed executable rule unless its AgentRule explicitly defines a usable
current validation scope.

Do not silently omit an executable rule.

---

# 18. Rule Status and Unresolved Rules

A rule's status is defined by its AgentRule.

For example:

```yaml
status: REQUIRES_REVIEW
```

or:

```yaml
validation: PENDING_VALIDATION
```

may indicate that the rule is not yet sufficiently defined for confirmed
compliance findings.

However, do not override an AgentRule's explicit current validation scope.

Determine executability from the complete AgentRule.

If the selected rule cannot reliably establish a finding:

* do not guess
* do not fabricate semantics
* do not report candidates as violations
* mark the analysis incomplete when appropriate
* explain the concrete limitation

---

# 19. Confirmed-Violation Gate

Report a confirmed violation only when all requirements applicable to the
selected AgentRule have been satisfied.

At minimum:

1. The target exists.
2. The target is within the rule's defined scope.
3. Required conditions are satisfied.
4. Required contextual conditions have been evaluated.
5. Applicable implemented/evaluable exclusions have been evaluated.
6. Deferred exclusions have been handled according to the AgentRule.
7. Evidence comes from the current filesystem.
8. Evidence corresponds to the exact reported location.
9. The actual observed value has been verified.
10. The finding can be traced to the selected AgentRule.

A search result alone is never sufficient.

An XPath result alone is never sufficient unless the selected AgentRule
explicitly defines that XPath as the complete violation predicate and all
associated requirements have been satisfied.

---

# 20. No Semantic Drift

Do not allow user wording, assumptions, examples, or natural-language
interpretation to change the actual rule semantics.

The hierarchy is:

The execution hierarchy for an AgentRule is:

```text
Detailed AgentRule
        ↓
Authoritative validation_procedure.command_template
        ↓
Current application
        ↓
Raw validation result
        ↓
AgentRule conditions and exclusions
        ↓
Verified result

RULE_SUMMARY.md is only for discovering the appropriate rule.

It does not override the detailed AgentRule.

The detailed AgentRule does not override actual evidence in the current
application.

The Skill does not redefine the semantics of an individual rule.

---

# 21. Output States

The result must use one of these states:

1. Clean
2. Violations
3. Incomplete

---
## 21.0 Pre-existing Report Handling
# 21. Output States

The result must use one of these states:

1. Clean
2. Violations
3. Incomplete

## 21.0 Pre-existing Report Handling

The fixed HTML report may already exist before a compliance run.

A pre-existing report, regardless of its timestamp or contents, MUST NOT be treated as evidence for the current run.

However, the existence of a pre-existing or stale report MUST NOT cause the compliance execution to stop or become Incomplete.

The agent MUST continue with the requested rule execution, generate/overwrite the fixed HTML report using the current-run results, and then verify the newly written report.

A stale or pre-existing report is an output-state condition, not an execution blocker.

The agent MUST NOT use the pre-existing report's contents, timestamp, size, or metadata as a substitute for executing the requested compliance rules.

Only the current run's execution results may determine the final compliance state and current-run report.

If the current-run report cannot subsequently be written or verified, then the existing report-handling rules for Incomplete apply.
**---**
**## 21.1 Clean**

Use the Clean state only when:

\- all requested rule(s) were evaluated  
\- the relevant application files were successfully evaluated  
\- no material scan limitation exists  
\- all applicable requirements for the selected rule(s) were satisfied  
\- no confirmed violations exist  

When the Clean state is reached, generate the same fixed HTML report defined  
by the Deterministic Report Format.

The report MUST be written to:

```text
<MSCop repository root>\_compliance_output\MSCop_Compliance_Report.html
```

The same current-run write and read-back verification requirements defined below for the Violations state apply to Clean results.

A Clean result MUST NOT be reported unless the current-run HTML report was successfully written to the fixed path and verified.

A pre-existing report MUST NOT be treated as the current Clean report.


## 21.2 Violations

When confirmed violations exist, generate exactly ONE self-contained HTML
report for the current compliance run.

The HTML report is an OUTPUT ARTIFACT ONLY.

Do not read, search, parse, summarize, ingest, or use the generated HTML
report as input to the compliance analysis.

After generating the report, the agent MUST successfully write the current
run's HTML content to the fixed report path.

A pre-existing report MUST NOT be treated as the output of the current run.

The agent MUST NOT claim that the report was generated unless the write
operation completed successfully.

After writing, the agent MUST verify that the report corresponds to the
current run.

At minimum, verification MUST establish:
- the report exists at the required path
- the write operation completed successfully
- the resulting file metadata reflects the current write
- the resulting file contains the current run's selected rule(s) and
  execution summary

If the write operation fails, is not captured, or cannot be verified, the
compliance run MUST be reported as Incomplete.

The agent MUST NOT use the previous HTML report as evidence of successful
report generation.

The agent MUST NOT report "report generated and verified" merely because
the pre-existing file exists, has the expected path, or has a readable size.

### Mandatory HTML Structure

Use exactly these sections, in exactly this order:

1. MSCop Compliance Analysis
2. Rule
3. Application
4. File/Folder Exclusions
5. Candidate-Level Exclusions
6. Violations
7. Execution Summary

Do not add other sections.

### File/Folder Exclusions

Show one entry for each configured exclusion rule.

For each entry show only:

- exclusion name
- folders skipped
- files skipped directly
- reason

Do NOT list individual skipped paths.

Example:

File/Folder Exclusions

1. DCT
   - 33 folders skipped
   - 0 files skipped directly
   - Reason: Folder/file path matched the configured DCT exclusion.

If no configured exclusion matched:

No file/folder exclusions matched.

### Candidate-Level Exclusions

Show one entry for each applicable rule.

For each entry show only:

- rule ID/name
- candidates skipped
- short status/reason

Do NOT list individual excluded candidates.

Example:

Candidate-Level Exclusions

1. MML
   - 0 candidates skipped
   - No MML candidate-level exclusions configured.
### Violations

Group ALL confirmed violations by source file.

The mandatory hierarchy is:

File
  ↓
Rule
  ↓
Violations

For every source file with one or more confirmed violations:

1. Show the file name.
2. Show the complete file path once.
3. Under the file, group confirmed violations by Rule ID/name.
4. Under each Rule, list only the confirmed violation identifier/name produced by that AgentRule.
5. A file may contain violations from multiple rules.
6. Different rules MUST remain separate under the same file.
7. Do NOT merge violations from different rules into one list.
8. Do NOT repeat the file path for every violation.
9. Show only rules that have confirmed violations for that file.
10. Do NOT create empty Rule sections.

The required structure is:

```text
Violations

File: <file name>
Path: <complete file path>

    Rule: <Rule ID> — <Rule Name>
        - <violation>
        - <violation>

    Rule: <Rule ID> — <Rule Name>
        - <violation>


File: <file name>
Path: <complete file path>

    Rule: <Rule ID> — <Rule Name>
        - <violation>

Do NOT include the following candidate-level metadata in the user-facing violation list:

- Field Type
- MaxLength
- Parameter Type
- Location
- Exclusion Status
- Evidence details
- Internal validation metadata
- Candidate processing information

Candidate-level exclusion processing may still occur internally as required by the execution contract, but exclusion status MUST NOT be rendered for every candidate.

The report should present the candidate name/identifier only, matching the concise style of the legacy MSCop output.

### Execution Summary

Show only:

- Applicable XML files
- Skipped folders
- Skipped files
- Candidates produced
- Candidate-level exclusions
- Confirmed violations
- Parse failures


### Deterministic Report Format

The HTML report is a fixed MSCop output artifact.

Every compliance run MUST use the exact same report format regardless of:

- the number of requested rules
- the number of violations
- the application size
- whether one rule or multiple rules are executed
- whether the result is Clean or Violations

The agent MUST NOT redesign, improvise, or customize the HTML report.

The report MUST use:

- one fixed HTML document structure
- one fixed section order
- one fixed heading hierarchy
- one standard system font
- simple standard HTML
- minimal CSS required only for readability
- consistent spacing and indentation
- consistent presentation across every run

The agent MUST NOT create:

- dashboards
- cards
- charts
- graphs
- colored severity indicators
- themes
- decorative styling
- interactive UI
- unnecessary tables
- JSON reports
- Markdown reports as a replacement for the HTML artifact
- separate HTML reports per rule
- additional report files
- alternate report formats

The report structure MUST NOT change based on the number of rules or violations.

The mandatory HTML section order is:

1. MSCop Compliance Analysis
2. Rule
3. Application
4. File/Folder Exclusions
5. Candidate-Level Exclusions
6. Violations
7. Execution Summary

The Violations section MUST always use:

File
  ↓
Rule
  ↓
Violations

as defined in the Violations subsection.

Only the actual compliance data may change between runs.


### Fixed Report File

Every compliance run MUST write exactly ONE HTML report to:

```text
<MSCop repository root>\_compliance_output\MSCop_Compliance_Report.html
---

## 21.3 Incomplete

Use the Incomplete state when a material unresolved rule or scan limitation
prevents a complete compliance conclusion.

Start with:

```text
MSCop Compliance Analysis

Analysis incomplete.
```

Then list only:

* affected rule IDs/files
* concrete reason for incompleteness

Unresolved candidates are never violations.

---

# 22. Output for Multiple Rules

When multiple rules are explicitly requested:

```text
Run HCP, DID and MFC
```

aggregate the results into one MSCop Compliance Analysis.

Every violation must retain its Rule ID.

Do not merge different rule semantics into one finding.

If all requested rules complete and no violations exist:

```text
MSCop Compliance Analysis

No violations found.
```

If one or more requested rules produce confirmed violations, report those
violations.

If a material limitation prevents one or more requested rules from reaching a
complete conclusion, use the Incomplete state according to the rules above.

---

# 23. User Prompt Should Stay Minimal

The user should not need to repeat the internal validation instructions.

Preferred prompts are:

```text
Run HCP on:

C:\Users\Om Annasaheb\Desktop\MSCop\Policy\ManuScripts
```

```text
Run DID on:

C:\Users\Om Annasaheb\Desktop\MSCop\Policy\ManuScripts
```

```text
Run MFC on:

C:\Users\Om Annasaheb\Desktop\MSCop\Policy\ManuScripts
```

```text
Run HCP, DID and MFC on:

C:\Users\Om Annasaheb\Desktop\MSCop\Policy\ManuScripts
```

```text
Run all MSCop rules on:

C:\Users\Om Annasaheb\Desktop\MSCop\Policy\ManuScripts
```

Do not require the user to repeat:

* AgentRule paths
* XML selectors
* regex patterns
* XPath
* comparison semantics
* case sensitivity
* whitespace behavior
* normalization rules
* validation commands
* evidence requirements
* cache rules
* filesystem rules
* output formatting

Those belong to the Skill and AgentRules.

---

# 24. Completion Requirement

## Specific Rule Request

For a specific-rule request, finish only after:

1. The supplied application path has been analyzed sufficiently for the
   requested rule.
2. RULE_SUMMARY.md has been consulted.
3. The requested AgentRule has been read completely.
4. The AgentRule's validation/evaluation procedure has been applied.
5. The rule's conditions have been evaluated.
6. Applicable exclusions/context have been handled according to the AgentRule.
7. Findings have been verified against the current filesystem.
8. Every confirmed finding has source-backed evidence.

### Fixed Report File

Every compliance run MUST write exactly ONE HTML report to:

```text
<MSCop repository root>\_compliance_output\MSCop_Compliance_Report.html
---

## Multiple Specific Rules Request

For a request such as:

```text
Run HCP, DID and MFC
```

finish only after:

1. RULE_SUMMARY.md has been consulted.
2. Every requested rule has been resolved.
3. Every selected AgentRule has been read completely.
4. Every selected rule's validation procedure has been applied.
5. Each rule has been evaluated independently.
6. Applicable exclusions/context have been handled for each rule.
7. Findings have been verified against the current filesystem.
8. Every confirmed finding has source-backed evidence.

Do not execute unrelated rules.

---

## All-Rules Request

For an all-rules/general compliance request, finish only after:

1. The complete supplied application root has been analyzed.
2. RULE_SUMMARY.md has been consulted.
3. The complete applicable rule catalog has been identified.
4. The applicable AgentRule YAML files have been read.
5. Every executable rule has been evaluated.
6. Unresolved rules have been identified.
7. Applicable exclusions/context have been handled according to each AgentRule.
8. Every confirmed finding has current, source-backed evidence.
9. Material scan limitations have been reported.

Never call the result clean when a material scan limitation or unresolved rule
prevents a complete compliance conclusion.

---

# 25. Core Principle

The Skill is the generic MSCop compliance execution framework.

It must not become a catalog of individual rule semantics.

The architecture is:

```text
               USER
  │
  │ requested rule + application path
  ▼
SKILL.md
  │
  │ HOW to execute
  ▼
RULE_SUMMARY.md
  │
  │ WHICH rule
  ▼
Selected AgentRule.yaml
  │
  │ WHAT + validation procedure
  ▼
Current Application Path
  │
  │ normal filesystem traversal
  ▼
FileFolderExclusions.yaml
  │
  ├── MATCH → SKIP
  │
  └── NO MATCH
          │
          ▼
Selected AgentRule Validation
          │
          ▼
      Candidates
          │
          ▼
AgentRules/Exclusions.yaml
          │
          ├── MATCH → EXCLUDE
          │
          └── NO MATCH
                  │
                  ▼
          Evidence Verification
                  │
                  ▼
             Final Result
```

The Skill orchestrates.

RULE_SUMMARY routes.

AgentRules define rule semantics.

The current application provides evidence.

The final result must be based on the current filesystem.

````

### The key change

Your prompts can now genuinely become:

```text
Run HCP on <path>
````

or

```text
Run HCP, DID and MFC on <path>
```

or

```text
Run all on <path>
```

**without losing the behavior we've already validated.**

And importantly, the Skill does **not** know what HCP, DID, or MFC actually do. That knowledge stays inside their YAMLs. This is the separation I would keep going forward.
