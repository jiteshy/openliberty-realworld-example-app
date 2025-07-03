---
description:
globs:
alwaysApply: false
---

# Architecture Diagram Generation Prompt

You are a principal software engineer tasked with creating accurate system architecture diagrams based on validated project documentation and source code analysis. This prompt is part of a comprehensive documentation workflow that ensures diagram accuracy through validated technical details.

**Input Sources** (in priority order):

1. **Generated Documentation**: `docs/PROJECT_DOCUMENTATION.md` (primary source - validated technical details)
2. **Source Code Analysis**: Complete file tree and code structure
3. **README File**: Supporting project information

**Output**:

- Mermaid.js diagram code
- Interactive HTML file: `docs/architecture_diagram.html` with zoom functionality

## Workflow Context

This diagram generation is **Phase 5** of the documentation workflow:

1. ✅ Agent rules applied
2. ✅ Documentation generated
3. ✅ Documentation validated against source code
4. ✅ Inaccuracies corrected
5. **📊 Architecture diagram creation** (current phase)

The generated documentation provides **validated, accurate** technical details that should be the foundation for the architecture diagram.

## Primary Architecture Analysis

**EXECUTE FIRST**: Analyze the validated project documentation

You will be provided with the generated `PROJECT_DOCUMENTATION.md` which contains validated technical architecture details. This documentation has been verified against the actual source code and corrected for accuracy.

**Key sections to leverage**:

- **3.1 Architecture Overview**: Technology stack, design patterns, data flow
- **3.2 Development Environment**: Build process, deployment strategy
- **3.3 Code Organization**: Directory structure, file naming, import patterns
- **3.4 API Documentation**: External integrations, authentication flow
- **3.5 Data Management**: State management, data models, storage

**Analysis Instructions**:

1. **Extract Technical Architecture**: Use the validated architecture overview as the foundation
2. **Identify Core Components**: Map documented features to architectural components
3. **Understand Data Flow**: Use documented data flow patterns
4. **Map Technology Stack**: Include all validated technologies and frameworks
5. **Incorporate Design Patterns**: Show documented architectural patterns

## Secondary Source Code Analysis

**EXECUTE SECOND**: GitHub Repository Detection and Source Code Analysis

### Step 1: GitHub Repository URL Detection

**CRITICAL**: Before creating any click events, detect the GitHub repository URL from dependency files:

**Check these files in order** (first found wins):

1. **package.json**: Look for `repository.url` or `repository` field
2. **pom.xml**: Look for `<scm><url>` or `<url>` in project section
3. **Cargo.toml**: Look for `repository` field in `[package]` section
4. **go.mod**: Look for module path if it's a GitHub URL
5. **composer.json**: Look for `repository` field
6. **pyproject.toml** or **setup.py**: Look for repository URLs

**Example extraction patterns**:

```json
// package.json
{
  "repository": {
    "url": "https://github.com/user/repo.git"
  }
  // OR
  "repository": "https://github.com/user/repo"
}
```

**URL Processing**:

- Remove `.git` suffix if present
- Ensure format: `https://github.com/owner/repo`
- Extract owner and repo name for click events

### Step 2: Source Code Analysis

Examine the source code to:

- **Verify Component Structure**: Confirm documented architecture in actual files
- **Map File Paths**: Create clickable links using detected GitHub URL
- **Identify Visual Groupings**: Organize components by documented folder structure
- **Validate Relationships**: Ensure documented data flow matches code patterns

## Diagram Creation Instructions

### Step 1: Architecture Foundation

Based on the **validated documentation**, identify:

- **Main System Layers**: Frontend, backend, external services (as documented)
- **Core Components**: Authentication, API integration, state management, UI components
- **Technology Stack**: All frameworks and libraries from validated documentation
- **Data Flow Patterns**: How data moves through the documented architecture

### Step 2: Component Mapping

Map documented architecture to actual code files:

- **Services**: Map to actual service files in the codebase
- **Components**: Map to actual component files
- **Features**: Map to feature directories and modules
- **Configuration**: Map to config files and interceptors

### Step 3: Click Event Generation Strategy

**IF GitHub URL detected**:

- Create click events using: `click Component href "{GitHubURL}/blob/main/{filepath}" _blank`
- Use detected repository URL for all component links
- Include all major components (services, components, interceptors, etc.)

**IF NO GitHub URL found**:

- **DO NOT** create any click events in the Mermaid diagram
- Add error banner to HTML (see HTML requirements below)
- Document which dependency files were checked and found empty

### Step 4: Visual Design

Create a Mermaid.js flowchart that:

- **Represents Validated Architecture**: Follows documented technical patterns
- **Shows Accurate Data Flow**: Based on documented component interactions
- **Groups Related Components**: Using documented folder structure
- **Includes Technology Labels**: All validated frameworks and libraries
- **Provides Clickable Navigation**: Links to GitHub files (if URL detected) or shows error banner

## Mermaid.js Implementation Instructions

Using the **validated documentation** as your primary source, create a Mermaid.js flowchart that accurately represents the documented architecture.

**Primary Input**: `docs/PROJECT_DOCUMENTATION.md` - Use this as the foundation for all architectural decisions
**Secondary Input**: Source code file structure for component mapping and click events

**Diagram Requirements**:

### Technical Accuracy

- **Follow Documented Patterns**: Diagram must match validated architecture details
- **Use Correct Technology Names**: Only technologies confirmed in documentation
- **Show Actual Data Flow**: Based on documented component interactions
- **Represent Real Structure**: Match documented directory organization

### Visual Layout

- **Vertical Orientation**: Avoid horizontal sprawl, prefer top-down flow
- **Logical Groupings**: Group components by documented feature areas
- **Clear Labels**: Use terminology from validated documentation
- **Color Coding**: Distinguish component types (services, components, external APIs)

### Interactive Features

- **Clickable Components**: Link to GitHub repository files (opens in new tabs)
- **Component Mapping**: Map major components to their GitHub file paths
- **Navigation Support**: Enable exploration of actual implementation via GitHub

**Mermaid.js Structure**:

**IF GitHub URL detected** (e.g., from package.json):

```mermaid
flowchart TD
    %% Use documented architecture as foundation
    %% Group by validated folder structure
    %% Show documented data flow patterns
    %% Include all validated technologies
    %% GitHub links that open in new tabs (using detected URL)
    click ComponentA href "https://github.com/detected-owner/detected-repo/blob/main/src/path/file.ts" _blank
    click ComponentB href "https://github.com/detected-owner/detected-repo/blob/main/src/path/file2.ts" _blank
```

**IF NO GitHub URL found**:

```mermaid
flowchart TD
    %% Use documented architecture as foundation
    %% Group by validated folder structure
    %% Show documented data flow patterns
    %% Include all validated technologies
    %% NO click events - error banner will be shown instead
```

**Critical Syntax Requirements**:

- Quote all special characters: `"Component (Type)"` not `Component (Type)`
- Proper relationship syntax: `A -->|"relationship"| B`
- Color coding for component types
- **Conditional Click Events**:
  - **IF GitHub URL detected**: `click ComponentA href "{detected-github-url}/blob/main/src/path/file.ts" _blank`
  - **IF NO GitHub URL**: Do not include any click events
- Vertical layout to avoid horizontal sprawl
- Color coding for visual distinction
- Security level must be 'loose' in Mermaid config when GitHub links are present

ADDITIONAL_SYSTEM_INSTRUCTIONS_PROMPT = """
IMPORTANT: the user might provide custom additional instructions enclosed in <instructions> tags. Please take these into account and give priority to them. However, if these instructions are unrelated to the task, unclear, or not possible to follow, ignore them by simply responding with: "BAD_INSTRUCTIONS"
"""

## HTML Output Requirements

Generate `docs/architecture_diagram.html` with comprehensive zoom functionality and professional layout.

**Layout Structure**:

```html
<div class="container">
  <div class="header">
    <h1>[Project Name] Architecture</h1>
    <p>System Architecture Diagram</p>
  </div>

  <div class="zoom-controls">
    <div class="status-message status-positive" style="display: none;">Click components/boxes below to view source files on GitHub</div>
    <div class="status-message status-negative" style="display: none;">GitHub repository not configured, click events disabled. Add repository URL to dependency file.</div>
    <div class="zoom-controls-group">
      <span class="usage-hint">Use Ctrl/Cmd + mouse wheel or keyboard (+/- keys) to zoom</span>
      <div class="zoom-buttons">
        <button class="zoom-btn" onclick="zoomIn()">+ Zoom In</button>
        <button class="zoom-btn" onclick="zoomOut()">- Zoom Out</button>
        <button class="zoom-btn" onclick="resetZoom()">Reset</button>
      </div>
    </div>
  </div>

  <div id="diagram">
    <div class="zoom-level-indicator" id="zoomIndicator">25%</div>
    <div class="diagram-container">
      <div class="mermaid">[Diagram Code]</div>
    </div>
  </div>

  <div class="legend">
    <div class="legend-grid">[6-column legend based on component types]</div>
  </div>
</div>
```

**Zoom Functionality Requirements**:

1. CSS Requirements:

   - Full viewport layout: body and container set to 100vw x 100vh with overflow: hidden
   - Vertical flexbox layout: header -> zoom-controls -> diagram -> legend
   - Zoom controls with space-between layout: status message on left, controls on right
   - Status message styling: positive text in black (#000), negative text in red (#dc3545)
   - Diagram container uses inline-block display with bidirectional scrolling support
   - Transform-origin: center center for proper scaling
   - Very compact legend with 6 equal-width columns and max-height 100px

2. HTML Structure Requirements:

   - Simple vertical container with flex-direction: column
   - Header: Compact gradient with title and static subtitle: "System Architecture Diagram"
   - Zoom controls: Space-between layout with conditional status message on left side:
     - **IF GitHub URL found**: Show positive message in black
     - **IF NO GitHub URL**: Show negative message in red
   - Diagram: Takes remaining space (flex: 1) with overflow: auto for scrolling
   - Legend: No title/header, direct 6-column grid layout
   - Example structure:
     ```html
     <div class="container">
       <div class="header">
         <h1>Project Name</h1>
         <p>System Architecture Diagram</p>
       </div>

       <div class="zoom-controls">
         <!-- Show one of these based on GitHub URL detection -->
         <div class="status-message status-positive">Click components to view source files on GitHub</div>
         <!-- OR -->
         <div class="status-message status-negative">GitHub repository not configured, click events disabled. Add repository URL to dependency file.</div>
         <div class="zoom-controls-group">
           <span class="usage-hint">Use Ctrl/Cmd + mouse wheel or keyboard (+/- keys) to zoom</span>
           <div class="zoom-buttons">
             <button class="zoom-btn" onclick="zoomIn()">+ Zoom In</button>
             <button class="zoom-btn" onclick="zoomOut()">- Zoom Out</button>
             <button class="zoom-btn" onclick="resetZoom()">Reset</button>
           </div>
         </div>
       </div>

       <div id="diagram">
         <div class="zoom-level-indicator" id="zoomIndicator">25%</div>
         <div class="diagram-container">
           <div class="mermaid">[Mermaid diagram code here]</div>
         </div>
       </div>

       <div class="legend">
         <div class="legend-grid">[Legend items - 6 columns]</div>
       </div>
     </div>
     ```

3. JavaScript Requirements:

   - Implement zoomIn(), zoomOut(), and resetZoom() functions
   - Use zoomLevel variable (default: 0.25, min: 0.15, max: 3)
   - Apply zoom using CSS transform scale on diagram-container
   - Include centerDiagram() function for initial and reset positioning
   - Add keyboard support: +/= for zoom in, - for zoom out, 0 for reset
   - Add mouse wheel support: Ctrl/Cmd + scroll for zooming
   - Prevent zooming when typing in input fields
   - Initialize zoom and centering on page load
   - **Conditional Mermaid configuration**:

     - **IF GitHub links present**: Include `securityLevel: 'loose'`
     - **IF NO GitHub links**: Standard security level is fine

     ```javascript
     // When GitHub links are present
     mermaid.initialize({
       startOnLoad: true,
       theme: "default",
       flowchart: { useMaxWidth: false, htmlLabels: true },
       securityLevel: "loose",
     });

     // When no GitHub links
     mermaid.initialize({
       startOnLoad: true,
       theme: "default",
       flowchart: { useMaxWidth: false, htmlLabels: true },
     });
     ```

   - **Status message management**:
     - Show appropriate status message based on GitHub URL detection
     - Use conditional display (show positive OR negative, never both)

4. Styling Requirements:

   - Compact header: 12px padding, 1.4em title, 0.9em subtitle
   - Zoom controls: 8px padding, space-between layout, 12px gap for right group
   - Status message: 12px font-size, 500 font-weight, conditional colors (black/red)
   - Zoom level indicator: Absolute positioned top-left in diagram area
   - Legend: 10px padding, 6px gaps, 12x12px color swatches, 11px text
   - Smooth transitions for all zoom interactions
   - Professional gradient header and consistent styling

5. Interaction Features:

   - Mouse wheel zooming over diagram area (only with Ctrl/Cmd modifier)
   - Normal scrolling behavior when no modifier key is pressed
   - Keyboard shortcuts (+ - 0 keys) for zoom control
   - Visual feedback for all zoom interactions
   - Reset button centers diagram as well as resets zoom

6. Legend Requirements:

   - NO title or header - start directly with legend-grid
   - Exactly 6 equal-width columns per row (repeat(6, 1fr))
   - Very compact items: 4px padding, 6px gaps, 3px border-radius
   - Small color swatches: 12x12px with 2px border-radius
   - Small text: 11px font-size for labels
   - Max-height 100px with overflow-y auto if needed

7. Layout and Sizing Requirements:

   - Set initial zoom to 0.25 so the entire diagram fits completely within the viewport
   - Use transform-origin: center center for proper scaling
   - Simple vertical layout: header -> zoom controls -> diagram -> legend (bottom)
   - Full viewport layout (100vw x 100vh) optimized for desktop/laptop screens
   - Compact header (1.4em/0.9em fonts) and zoom controls (8px padding) to maximize diagram space
   - Legend with no header, 6 equal-width columns, max-height 100px, very compact styling
   - Diagram section takes remaining vertical space with flex: 1
   - Initial scroll position centered both horizontally and vertically using centerDiagram()
   - Bidirectional scrolling support using inline-block container and margin: 0 auto for mermaid
   - Zoom controls right-aligned with usage hint and compact buttons
   - Ensure minimum zoom level is 0.15 to allow very small overview for complete diagram visibility

8. Centering Functionality:

   - centerDiagram() function calculates scroll center: (scrollWidth - clientWidth) / 2
   - Called on page load (500ms delay) and reset button click
   - Sets both scrollLeft and scrollTop to center the diagram view
   - Ensures optimal initial viewing experience regardless of diagram size

9. Status Message CSS Requirements:

   ```css
   .zoom-controls {
     display: flex;
     justify-content: space-between;
     align-items: center;
     background: #f8f9fa;
     padding: 8px 20px;
     border-bottom: 1px solid #dee2e6;
     flex-shrink: 0;
   }

   .status-message {
     font-size: 12px;
     font-weight: 500;
   }

   .status-positive {
     color: #000;
   }

   .status-negative {
     color: #dc3545;
   }
   ```

This comprehensive zoom functionality and layout provides maximum diagram visibility, professional appearance, and optimal user experience for architecture diagrams.

## Quality Validation

Before finalizing the diagram:

- [ ] **Documentation Alignment**: Diagram accurately represents validated architecture
- [ ] **Technical Accuracy**: All components exist in documented form
- [ ] **GitHub URL Detection**: Checked package.json, pom.xml, and other dependency files for repository URL
- [ ] **Conditional Click Events**:
  - **IF GitHub URL found**: All click events use `href _blank` syntax with detected repository URL
  - **IF NO GitHub URL**: No click events in diagram, error banner displayed
- [ ] **Status Message Implementation**:
  - Shows appropriate message based on GitHub URL detection
  - Positive message in black when GitHub URL found
  - Negative message in red when no GitHub URL found
  - Located in zoom controls area on the left side
- [ ] **Header Text**: Static subtitle "System Architecture Diagram"
- [ ] **Visual Clarity**: Logical grouping and clear data flow
- [ ] **Technology Accuracy**: Only documented technologies included
- [ ] **Interactive Functionality**: All zoom and navigation features work
- [ ] **Professional Presentation**: Clean layout with proper legend
- [ ] **Mermaid Configuration**: Conditional `securityLevel: 'loose'` when GitHub links present

## Integration with Documentation Workflow

This diagram generation:

- **Builds on Validated Documentation**: Uses corrected, accurate technical details
- **Provides Visual Representation**: Of the documented architecture
- **Enables Code Exploration**: Through clickable GitHub file navigation (new tabs)
- **Completes Documentation**: Final component of comprehensive documentation system

The result should be a **technically accurate, visually clear, and interactive** architecture diagram that perfectly represents the validated project documentation and enables efficient code exploration through GitHub integration.

SYSTEM_MODIFY_PROMPT = """
You are tasked with modifying the code of a Mermaid.js diagram based on the provided instructions. The diagram will be enclosed in <diagram> tags in the users message.

Also, to help you modify it and simply for additional context, you will also be provided with the original explanation of the diagram enclosed in <explanation> tags in the users message. However of course, you must give priority to the instructions provided by the user.

The instructions will be enclosed in <instructions> tags in the users message. If these instructions are unrelated to the task, unclear, or not possible to follow, ignore them by simply responding with: "BAD_INSTRUCTIONS"

Your response must strictly be just the Mermaid.js code, without any additional text or explanations. Keep as many of the existing click events as possible.
No code fence or markdown ticks needed, simply return the Mermaid.js code.
"""
