# Master Documentation & Architecture Workflow with Validation

You are executing a comprehensive documentation workflow that generates, validates, and improves project documentation through automated accuracy verification, then creates architecture diagrams.

## Workflow Overview

This workflow creates a **self-improving documentation system** that:
1. Applies agent rules and context
2. Generates comprehensive documentation from source code using **generic prompt**
3. Validates documentation accuracy against the actual codebase (results displayed in chat)
4. Captures **project-specific learnings** and updates documentation using **composite context**
5. Creates architecture diagrams based on validated documentation
6. Creates a feedback loop for continuous improvement **without polluting generic prompts**

## Execution Sequence

### Phase 1: Agent Context Setup
**Execute**: Read and apply agent rules from `.cursor/rules/rules.md`

**Goal**: Define your role as a principal software engineer and set the boundaries for your analysis.

**Output**: Agent context and role established

### Phase 2: Documentation Generation
**Execute**: `.cursor/prompts/documentation-generation-prompt.md`

**Goal**: Generate comprehensive project documentation using the **generic, technology-agnostic prompt** with universal validation requirements.

**Important**: The documentation-generation-prompt.md must remain **generic and reusable** across all project types (frontend, backend, different tech stacks).

**Output**: `docs/PROJECT_DOCUMENTATION.md`

### Phase 3: Documentation Validation & Correction  
**Execute**: `.cursor/prompts/documentation-validation-prompt.md`

**Goal**: Systematically validate the generated documentation against the source code to identify inaccuracies.

**Inputs**: 
- Generated documentation from Phase 2
- Access to complete source codebase
- The **generic** documentation generation prompt

**Outputs**:
- Display validation findings in chat window
- Create `.cursor/learning/workflow-learnings.md` with **project-specific validation insights, patterns, and technology-specific validation rules**
- **DO NOT** update the generic documentation-generation-prompt.md

### Phase 4: Improvement Application
**Goal**: Apply corrections and improvements to documentation using **composite context**.

**Actions**:
1. **Composite Context Application**: Update `docs/PROJECT_DOCUMENTATION.md` using BOTH:
   - The generic `.cursor/prompts/documentation-generation-prompt.md` 
   - The project-specific `.cursor/learning/workflow-learnings.md`
2. **Project-Specific Learning Enhancement**: Update `.cursor/learning/workflow-learnings.md` with additional execution metrics, patterns, and insights
3. **Generic Prompt Preservation**: Keep `.cursor/prompts/documentation-generation-prompt.md` unchanged and reusable

### Phase 5: Architecture Diagram Generation
**Execute**: `.cursor/prompts/diagram-generation-prompt.md`

**Goal**: Create architecture diagram using the validated documentation as additional context.

**Inputs**:
- Validated and corrected documentation from Phase 4
- Codebase analysis
- Component mapping information

**Output**: `docs/architecture_diagram.html`

## Execution Instructions

**Important**: Maintain clean separation between generic (reusable) and project-specific (learning) components. Display validation results in chat while capturing learnings for future workflow improvements.

**Final Deliverables**:
- `docs/PROJECT_DOCUMENTATION.md` - Comprehensive validated documentation (updated with composite context)
- `docs/architecture_diagram.html` - Interactive architecture diagram
- `.cursor/learning/workflow-learnings.md` - Project-specific validation insights and technology-specific rules

### Step 1: Apply Agent Rules
```
Read .cursor/rules/rules.md and acknowledge the agent role as a principal software engineer with expertise in frontend web applications and backend REST services.
```

### Step 2: Generate Initial Documentation
```
Apply the GENERIC documentation generation prompt to analyze the current codebase and produce comprehensive documentation with universal verification requirements.
```

### Step 3: Validate Documentation Accuracy
```
Apply the documentation validation prompt to systematically verify every claim in the generated documentation against the actual source code. Display all validation findings in chat and CREATE `.cursor/learning/workflow-learnings.md` with PROJECT-SPECIFIC insights, patterns, technology-specific validation rules, and metrics for this project type.
```

### Step 4: Apply Corrections and Improvements with Composite Context
```
Based on validation findings:
1. Correct inaccurate statements in the documentation using COMPOSITE CONTEXT:
   - Generic documentation generation guidelines
   - Project-specific validation learnings and technology-specific rules
2. Update `.cursor/learning/workflow-learnings.md` with execution results, accuracy metrics, successful patterns, and technology-specific insights
3. PRESERVE the generic documentation-generation-prompt.md for reuse across different project types
```

### Step 5: Generate Architecture Diagram
```
Apply the architecture diagram prompt using both the validated documentation and direct codebase analysis to create accurate architectural representations.
```

## Success Criteria

**Phase 1 Success**:
- [ ] Agent rules applied and acknowledged
- [ ] Role and expertise boundaries established

**Phase 2 Success**:
- [ ] Comprehensive documentation generated using generic prompt
- [ ] All major project aspects covered
- [ ] Documentation follows the specified structure

**Phase 3 Success**:
- [ ] Every factual claim verified against source code
- [ ] All inaccuracies identified with evidence in chat
- [ ] Project-specific patterns of errors analyzed and documented in learning file
- [ ] Technology-specific validation insights captured for this project type

**Phase 4 Success**:
- [ ] Documentation corrected using composite context (generic + project-specific)
- [ ] Generic prompt preserved for reuse across projects
- [ ] Learning file contains technology-specific validation rules and patterns
- [ ] Project-specific improvement insights captured

**Phase 5 Success**:
- [ ] Architecture diagram created with clickable components
- [ ] Diagram accurately represents validated documentation
- [ ] Visual representation matches actual codebase structure

## Quality Assurance

**Final Documentation Must**:
- [ ] Contain only verified, accurate information
- [ ] Use consistent package manager throughout
- [ ] Document only features that actually exist
- [ ] Provide working commands and setup instructions
- [ ] Match actual project structure and patterns
- [ ] Enable successful project onboarding

**Generic Prompt Must**:
- [ ] Remain technology-agnostic and reusable
- [ ] Include only universal validation principles
- [ ] Work for frontend, backend, and different tech stacks
- [ ] Not contain project-specific validation rules

**Project Learning File Must**:
- [ ] Contain technology-specific validation insights
- [ ] Include project-type-specific patterns and rules
- [ ] Capture execution metrics for this project type
- [ ] Be ready for composite context application

**Architecture Diagram Must**:
- [ ] Accurately represent the validated system architecture
- [ ] Include clickable components linking to actual files
- [ ] Be based on verified documentation rather than assumptions
- [ ] Provide clear visual understanding of the system

## Continuous Improvement Architecture

This workflow creates a **clean learning system** where:
- **Generic Prompt**: Remains reusable across all project types (frontend React, backend Java, Python APIs, etc.)
- **Project Learnings**: Technology-specific validation rules and patterns are captured per project
- **Composite Context**: Final documentation updates use both generic principles and project-specific learnings
- **Scalability**: Each project type builds its own learning base without polluting generic prompts
- **Reusability**: The same generic prompt works for diverse technology stacks

## Execution Command

**Run the complete workflow:**
```
Execute the master documentation workflow: Apply agent rules, generate documentation using generic prompt, validate accuracy, capture project-specific learnings, apply corrections using composite context, and create architecture diagrams.
```

This will automatically chain all five phases and provide a comprehensive, validated, and continuously improving documentation system with accurate architectural visualization while maintaining clean separation between generic and project-specific components.

## Expected Timeline
- **Phase 1**: 2-3 minutes (rules application)
- **Phase 2**: 10-15 minutes (documentation generation with generic prompt)
- **Phase 3**: 15-20 minutes (validation and project-specific learning creation)  
- **Phase 4**: 5-10 minutes (applying corrections with composite context)
- **Phase 5**: 10-15 minutes (architecture diagram generation)
- **Total**: 42-63 minutes for complete workflow

Execute this workflow to create accurate, validated documentation with architectural diagrams while building project-specific learnings without polluting generic, reusable prompts.