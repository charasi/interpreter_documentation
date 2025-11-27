This document outlines the testing strategy for the **Statement Evaluator**. Since `AlgoWB` is a visual debugger, tests must verify both **Language Logic** (did it run correctly?) and **Debugger State** (is the visualization data correct?).

## 1. SourceFile (The Root)
**Goal:** Verify the interpreter correctly processes a sequence of statements from top to bottom.
* **Positive Scenarios:**
    * **Sequential Execution:** Verify statement A executes before B.
    * **Global Scope:** Verify variables defined at the top level persist after execution.
    * **Empty File:** Verify handling a file with 0 statements (no crash).
* **Negative / Edge Cases:**
    * **Early Termination:** Verify that if an error occurs in line 1, line 2 is never executed.

## 2. Block (`{ ... }`)
**Goal:** Verify scope isolation and nesting.
* **Positive Scenarios:**
    * **Scope Hierarchy:** Verify inner code can read outer variables.
    * **Variable Shadowing:** Verify `let x` inside a block doesn't overwrite outer `x`.
    * **Nested Blocks:** Verify deep nesting (`{{}}`) maintains the scope chain.
* **Debugger State Verification:**
    * **Scope ID:** Verify that entering a block pushes a new `ScopeID` to the scope stack visualization.

## 3. VariableStatement (`let x = 1;`)
**Goal:** Verify memory allocation and initialization.
* **Positive Scenarios:**
    * **Initialization:** Verify variable is created with correct value.
    * **No Init:** Verify `let x;` results in `undefined`.
    * **Multiple Declarations:** Verify `let x = 1, y = 2;` works.
* **Negative / Edge Cases:**
    * **Redeclaration:** Verify error when redeclaring the same name in the same scope.

## 4. IfStatement (`if (...)`)
**Goal:** Verify conditional branching.
* **Positive Scenarios:**
    * **True Branch:** Verify "Consequent" executes.
    * **False Branch:** Verify "Alternate" (else) executes.
* **Debugger State Verification:**
    * **Highlighting:** Verify the debugger highlights the line of the `if` statement *before* jumping to the body.

## 5. SwitchStatement (`switch (...)`) [NEW]
**Goal:** Verify multi-way branching and fallthrough logic.
* **Positive Scenarios:**
    * **Match Found:** Verify execution jumps to the matching `case`.
    * **Default Case:** Verify `default` executes if no match is found.
    * **Fallthrough:** Verify that without a `break`, execution continues into the next case (standard JS behavior).
* **Negative / Edge Cases:**
    * **No Match/No Default:** Verify nothing happens and execution continues after the switch.

## 6. Loops (`while`, `do...while`, `for`)
**Goal:** Verify iteration, conditions, and updating.

### WhileStatement
* **Positive Scenarios:**
    * **Standard Loop:** Runs N times.
    * **Zero Iterations:** Body never runs if condition starts false.
* **Debugger State Verification:**
    * **Step-Over:** Verify "stepping" at the end of the loop body jumps back to the loop header (condition check).

### ForStatement
* **Positive Scenarios:**
    * **Sequence:** Verify Init -> Test -> Body -> Update order.
    * **Scope:** Verify loop variable (`let i`) is not accessible outside.
* **Negative / Edge Cases:**
    * **Infinite Loop:** (Optional) Verify debugger creates a "max iteration" safeguard.

## 7. Control Flow (`break`, `continue`, `return`)
**Goal:** Verify "jumps" in execution flow.
* **Positive Scenarios:**
    * **Break:** Immediately exits nearest loop/switch.
    * **Continue:** Skips remaining body and jumps to loop update/condition.
    * **Return:** Exits function immediately, unwinding all active scopes/blocks.
* **Negative / Edge Cases:**
    * **Orphan Jumps:** Verify specific error if `break` or `return` is found outside a loop/function.

## 8. TryStatement (`try ... catch`) [NEW]
**Goal:** Verify error handling and recovery.
* **Positive Scenarios:**
    * **Success Path:** `try` block runs, `catch` is skipped.
    * **Error Path:** Exception in `try` jumps immediately to `catch`.
    * **Finally:** Verify `finally` block runs regardless of success or error.
* **Debugger State Verification:**
    * **Exception Visualization:** Verify the "Error" state is captured and displayed in the UI before the crash occurs.

## 9. ExpressionStatement
**Goal:** Verify side effects.
* **Positive Scenarios:**
    * **Assignment:** `x = 5` updates the variable.
    * **Function Call:** `func()` executes the function body.
