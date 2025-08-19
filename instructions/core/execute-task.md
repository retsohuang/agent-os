---
description: Rules to execute a task and its sub-tasks using Agent OS
globs:
alwaysApply: false
version: 1.0
encoding: UTF-8
allowed-tools: mcp__filesystem__read_text_file, mcp__filesystem__write_file, mcp__filesystem__edit_file, mcp__filesystem__list_directory, mcp__filesystem__search_files, Task, Bash, Glob, Grep
---

# Task Execution Rules

## Overview

Execute a specific task along with its sub-tasks systematically following a TDD development workflow.

<pre_flight_check>
  EXECUTE: @~/.agent-os/instructions/meta/pre-flight.md (use mcp__filesystem__read_text_file)
</pre_flight_check>


<process_flow>

<step number="1" name="task_selection">

### Step 1: Task Selection and Scope Analysis

Analyze the user's request to determine task scope and identify which specific tasks or sub-tasks to execute.

<scope_analysis>
  <default_behavior>
    By default, complete one parent task and all its sub-tasks
  </default_behavior>
  
  <scope_variations>
    <more_work>
      Pattern: "Complete tasks X and Y with all sub-tasks"
      Action: Execute multiple specified parent tasks completely
    </more_work>
    
    <less_work>
      Pattern: "Just do task X.Y and X.Z"
      Action: Execute only specified sub-tasks
    </less_work>
    
    <specific_task>
      Pattern: "Work on task X: [description]"
      Action: Execute the specified parent task with all sub-tasks
    </specific_task>
    
    <next_task>
      Pattern: "execute next task"
      Action: Find and execute the next uncompleted parent task
    </next_task>
  </scope_variations>
</scope_analysis>

<task_identification>
  <explicit_requests>
    IF user specifies exact task numbers or descriptions:
      PARSE: Extract task identifiers from user request
      VALIDATE: Ensure specified tasks exist in tasks.md
      SCOPE: Execute only the specified tasks/sub-tasks
  </explicit_requests>
  
  <next_task_logic>
    IF user requests "next task" or no specific task:
      SEARCH: Find first uncompleted parent task in tasks.md
      SCOPE: Execute that parent task and all its sub-tasks
      DEFAULT: If all tasks complete, inform user
  </next_task_logic>
</task_identification>

<instructions>
  ACTION: Parse user request to determine task scope
  IDENTIFY: Specific tasks or use "next uncompleted" logic
  VALIDATE: Ensure target tasks exist and are actionable
  SCOPE: Determine exact sub-tasks to execute
  INFORM: Clearly state what will be executed before proceeding
</instructions>

</step>

<step number="2" name="task_understanding">

### Step 2: Task Understanding

Use filesystem tools to read and analyze the selected task(s) and their sub-tasks from tasks.md to gain complete understanding of what needs to be built.

<task_analysis>
  <read_from_tasks_md>
    - Selected parent task description(s)
    - Relevant sub-task descriptions
    - Task dependencies
    - Expected outcomes
  </read_from_tasks_md>
</task_analysis>

<instructions>
  ACTION: Use mcp__filesystem__read_text_file to read tasks.md and analyze selected task(s)
  ANALYZE: Full scope of implementation required for selected items
  UNDERSTAND: Dependencies and expected deliverables
  NOTE: Test requirements for each selected sub-task
</instructions>

</step>

<step number="3" name="technical_spec_review">

### Step 3: Technical Specification Review

Search and extract relevant sections from technical-spec.md to understand the technical implementation approach for this task.

<selective_reading>
  <search_technical_spec>
    FIND sections in technical-spec.md related to:
    - Current task functionality
    - Implementation approach for this feature
    - Integration requirements
    - Performance criteria
  </search_technical_spec>
</selective_reading>

<instructions>
  ACTION: Search technical-spec.md for task-relevant sections
  EXTRACT: Only implementation details for current task
  SKIP: Unrelated technical specifications
  FOCUS: Technical approach for this specific feature
</instructions>

</step>

<step number="4" subagent="context-fetcher" name="best_practices_review">

### Step 4: Best Practices Review

Use the context-fetcher subagent to retrieve relevant sections from @~/.agent-os/standards/best-practices.md (using mcp__filesystem__read_text_file) that apply to the current task's technology stack and feature type.

<selective_reading>
  <search_best_practices>
    FIND sections relevant to:
    - Task's technology stack
    - Feature type being implemented
    - Testing approaches needed
    - Code organization patterns
  </search_best_practices>
</selective_reading>

<instructions>
  ACTION: Use context-fetcher subagent
  REQUEST: "Find best practices sections relevant to:
            - Task's technology stack: [CURRENT_TECH]
            - Feature type: [CURRENT_FEATURE_TYPE]
            - Testing approaches needed
            - Code organization patterns"
  PROCESS: Returned best practices
  APPLY: Relevant patterns to implementation
</instructions>

</step>

<step number="5" subagent="context-fetcher" name="code_style_review">

### Step 5: Code Style Review

Use the context-fetcher subagent to retrieve relevant code style rules from @~/.agent-os/standards/code-style.md (using mcp__filesystem__read_text_file) for the languages and file types being used in this task.

<selective_reading>
  <search_code_style>
    FIND style rules for:
    - Languages used in this task
    - File types being modified
    - Component patterns being implemented
    - Testing style guidelines
  </search_code_style>
</selective_reading>

<instructions>
  ACTION: Use context-fetcher subagent
  REQUEST: "Find code style rules for:
            - Languages: [LANGUAGES_IN_TASK]
            - File types: [FILE_TYPES_BEING_MODIFIED]
            - Component patterns: [PATTERNS_BEING_IMPLEMENTED]
            - Testing style guidelines"
  PROCESS: Returned style rules
  APPLY: Relevant formatting and patterns
</instructions>

</step>

<step number="6" name="task_execution">

### Step 6: Task and Sub-task Execution

Execute the parent task and all sub-tasks in order using test-driven development (TDD) approach.

<typical_task_structure>
  <first_subtask>Write tests for [feature]</first_subtask>
  <middle_subtasks>Implementation steps</middle_subtasks>
  <final_subtask>Verify all tests pass</final_subtask>
</typical_task_structure>

<execution_order>
  <subtask_1_tests>
    IF sub-task 1 is "Write tests for [feature]":
      - Write all tests for the parent feature
      - Include unit tests, integration tests, edge cases
      - Run tests to ensure they fail appropriately
      - Mark sub-task 1 complete
  </subtask_1_tests>

  <middle_subtasks_implementation>
    FOR each implementation sub-task (2 through n-1):
      - Implement the specific functionality
      - Make relevant tests pass
      - Update any adjacent/related tests if needed
      - Refactor while keeping tests green
      - Mark sub-task complete
  </middle_subtasks_implementation>

  <final_subtask_verification>
    IF final sub-task is "Verify all tests pass":
      - Run entire test suite
      - Fix any remaining failures
      - Ensure no regressions
      - Mark final sub-task complete
  </final_subtask_verification>
</execution_order>

<test_management>
  <new_tests>
    - Written in first sub-task
    - Cover all aspects of parent feature
    - Include edge cases and error handling
  </new_tests>
  <test_updates>
    - Made during implementation sub-tasks
    - Update expectations for changed behavior
    - Maintain backward compatibility
  </test_updates>
</test_management>

<instructions>
  ACTION: Execute sub-tasks in their defined order
  RECOGNIZE: First sub-task typically writes all tests
  IMPLEMENT: Middle sub-tasks build functionality
  VERIFY: Final sub-task ensures all tests pass
  UPDATE: Mark each sub-task complete as finished
</instructions>

</step>

<step number="7" subagent="test-runner" name="task_test_verification">

### Step 7: Task-Specific Test Verification

Use the test-runner subagent to run and verify only the tests specific to this parent task (not the full test suite) to ensure the feature is working correctly.

<focused_test_execution>
  <run_only>
    - All new tests written for this parent task
    - All tests updated during this task
    - Tests directly related to this feature
  </run_only>
  <skip>
    - Full test suite (done later in execute-tasks.md)
    - Unrelated test files
  </skip>
</focused_test_execution>

<final_verification>
  IF any test failures:
    - Debug and fix the specific issue
    - Re-run only the failed tests
  ELSE:
    - Confirm all task tests passing
    - Ready to proceed
</final_verification>

<instructions>
  ACTION: Use test-runner subagent
  REQUEST: "Run tests for [this parent task's test files]"
  WAIT: For test-runner analysis
  PROCESS: Returned failure information
  VERIFY: 100% pass rate for task-specific tests
  CONFIRM: This feature's tests are complete
</instructions>

</step>

<step number="8" name="task_status_updates">

### Step 8: Task Status Updates

Update the tasks.md file immediately after completing each task to track progress.

<update_format>
  <completed>- [x] Task description</completed>
  <incomplete>- [ ] Task description</incomplete>
  <blocked>
    - [ ] Task description
    ⚠️ Blocking issue: [DESCRIPTION]
  </blocked>
</update_format>

<blocking_criteria>
  <attempts>maximum 3 different approaches</attempts>
  <action>document blocking issue</action>
  <emoji>⚠️</emoji>
</blocking_criteria>

<instructions>
  ACTION: Update tasks.md after each task completion
  MARK: [x] for completed items immediately
  DOCUMENT: Blocking issues with ⚠️ emoji
  LIMIT: 3 attempts before marking as blocked
</instructions>

</step>

<step number="9" name="task_completion_stop">

### Step 9: Task Completion and Stop

After completing the selected parent task and all its sub-tasks, STOP execution and await further user instructions.

<completion_behavior>
  <single_task_execution>
    - Complete only the ONE parent task that was identified in Step 1
    - Do NOT automatically continue to subsequent tasks
    - Do NOT prepare or set up for additional tasks unless explicitly requested
  </single_task_execution>
  
  <stop_conditions>
    - When the selected parent task and all its sub-tasks are complete
    - When all tests for the task are passing  
    - When task status has been updated in tasks.md
    - When TodoWrite has been marked complete
  </stop_conditions>
  
  <completion_summary>
    - Provide brief summary of what was completed
    - Confirm task completion status
    - STOP and await further user instructions
  </completion_summary>
</completion_behavior>

<instructions>
  ACTION: Summarize completed work for the single selected task
  CONFIRM: Task completion status  
  STOP: Do not continue to other tasks automatically
  AWAIT: User instructions for next steps
</instructions>

</step>

</process_flow>
