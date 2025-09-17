# User Story: Course Creator Initial Setup and Generation

## 1. User Story

**As a** Course Creator,
**I want to** configure the initial parameters of a new course, including its metadata, structure, and AI settings,
**So that I can** initiate the AI generation process and monitor its real-time progress.

## 2. Description

This user story covers the primary user flow for creating a new educational course using the EduContent AI Assistant. The user will land on the "Course Creator" screen, which serves as the central hub. The screen is divided into two main columns. The left column contains forms for defining the course's fundamental attributes (title, subject), its structure (number of modules and lessons), and the desired AI generation style. The right column provides dynamic feedback, displaying the real-time progress of the content generation, a queue of modules ready for review, and a summary of automated quality checks. The goal is to provide a seamless and guided experience from initial configuration to the start of the AI-powered content creation workflow.

## 3. Acceptance Criteria

### 3.1. Main Layout & Navigation

-   **AC1.1:** The screen must display a main application shell consisting of a fixed top Header, a fixed left Sidebar, and a main content area.
-   **AC1.2:** The Header must contain the "EduContent AI Assistant" logo, navigation links ("Projects", "Library", "Analytics", "Settings"), and a user profile section.
-   **AC1.3:** The Sidebar must have a width of `256px`, a background color of `#F9FAFB`, and a `1px` right border (`#e5e7eb`).
-   **AC1.4:** The Sidebar must display a primary "New Project" button and a categorized list of navigation links. The "Course Creator" link under "Content Generation" must be in an active state (background `#FFFFFF`, text `#111827`).
-   **AC1.5:** The main content area must have a two-column layout with a `32px` gap. The left column is for configuration, and the right column (`352px` fixed-width) is for status panels.
-   **AC1.6:** The page title "Course Creator" must be displayed at the top of the content area with `24px Inter, Regular` font.

### 3.2. Left Column: Course Configuration

-   **AC2.1: Course Configuration Panel**
    -   A panel with a `24px` padding and `8px` border-radius must contain the course metadata form.
    -   **Course Title:** Must be a required text input (`<input type="text">`) with a `48px` height and must enforce a character limit of 5-200 characters.
    -   **Subject Area:** Must be a required dropdown (`<select>`) with options: "Mathematics", "Science", "Engineering", etc.
    -   **Difficulty Level:** Must be a required dropdown (`<select>`) with options: "Beginner", "Intermediate", "Advanced", "Expert".
    -   **Target Audience:** Must be a required textarea (`<textarea>`) and must enforce a character limit of 50-500 characters.
    -   **Learning Objectives:** Must be a required textarea (`<textarea>`) and must enforce a character limit of 100-1000 characters.
    -   All form fields must have labels with `14px Inter, Regular` font and a `16px` vertical spacing between them.

-   **AC2.2: Content Structure Panel**
    -   A panel must contain controls for defining the course structure.
    -   **Number of Modules:** Must be a number input (`<input type="number">`) with a default value of `8` and must be constrained between 1 and 20.
    -   **Lessons per Module:** Must be a number input (`<input type="number">`) with a default value of `4` and must be constrained between 1 and 10.
    -   **Include Assessments:** Must be an iOS-style toggle switch, enabled by default.
    -   **Include Interactive Elements:** Must be an iOS-style toggle switch, enabled by default.

-   **AC2.3: AI Generation Settings Panel**
    -   A panel must contain settings for the AI generation process.
    -   **Content Style:** Must be a dropdown (`<select>`) with options: "Academic", "Conversational", "Professional", "Technical".
    -   **Language:** Must be a dropdown (`<select>`) with options: "English", "Spanish", "French", etc.
    -   **Include Citations:** Must be an iOS-style toggle switch.

-   **AC2.4: Action Buttons**
    -   A "Save Draft" button (secondary style: white background, `#e5e7eb` border) must be present to save the form data without starting generation.
    -   A "Generate" button (primary style: `#111827` background, `#FFFFFF` text) must be present. This button should be disabled until all required fields in the "Course Configuration" panel are valid.
    -   Clicking the "Generate" button must initiate the AI content generation process and trigger updates in the right-hand panels.

### 3.3. Right Column: Status & Progress

-   **AC3.1: Generation Progress Panel**
    -   The panel must display real-time progress of the course generation.
    -   An "Course Outline" progress bar (`8px` height, rounded) must be displayed, showing 0-100% completion.
    -   A "Module Content" progress bar must be displayed, including a counter (e.g., "3/8") and a percentage.
    -   An "Assessments" progress bar must be displayed.
    -   An estimated completion time must be displayed (e.g., "⏱ Estimated completion: 12 minutes").
    -   A current status message must be shown (e.g., "AI is currently generating Module 4").

-   **AC3.2: Review Queue Panel**
    -   The panel must list modules as they are generated.
    -   Each module in the list must display its title and a status badge.
    -   **Status Badges:**
        -   `Generating`: Blue background (`#60a5fa` text, `rgba(96, 165, 250, 0.1)` bg).
        -   `Review`: Yellow background (`#f59e0b` text, `rgba(251, 191, 36, 0.1)` bg).
        -   `Approved`: Green background (`#10b981` text, `rgba(52, 211, 153, 0.1)` bg).
    -   Modules with "Review" status must have "Approve" and "Edit" action buttons.
    -   Modules with "Approved" status must have "View" and "Revise" action buttons.

-   **AC3.3: Quality Checks Panel**
    -   The panel must display a checklist of automated quality assurance tasks.
    -   Each item must have a status icon and a label.
    -   **Plagiarism Check:** Must show an icon indicating status (e.g., ✓ Passed, ⏳ In Progress).
    -   **Fact Verification:** Must show an icon indicating status.
    -   **Citation Check:** Must show an icon indicating status.
    -   **Accessibility:** Must show an icon indicating status (e.g., ⚠ Needs Review).

## 4. Out of Scope

-   The detailed implementation of the "Module Review & Edit Screen". Clicking "Edit" or "Revise" is not part of this story.
-   The functionality of the "Assessment Builder Screen".
-   User interactions within the Header and other Sidebar navigation links (e.g., "Projects", "Library", "Text Content").
-   Collaborative features or version history.
-   The actual backend API integration for AI generation and quality checks (this story focuses on the frontend UI and state management hooks).

## 5. Technical Notes

-   **Framework:** Angular 17+
-   **State Management:** Use Angular Signals for local component state and simple cross-component communication. Consider NgRx for managing complex global state like generation progress and review queues.
-   **Forms:** Implement using Angular's Reactive Forms (`FormGroup`, `FormControl`) to handle validation and state.
-   **Component Structure:** Break down the UI into standalone components as suggested in the UI Spec:
    -   `CourseCreatorComponent` (Main container)
    -   `SidebarNavigationComponent`
    -   `CourseConfigurationComponent`
    -   `ContentStructureComponent`
    -   `AiSettingsComponent`
    -   `GenerationProgressComponent`
    -   `ReviewQueueComponent`
    -   `QualityChecksComponent`
-   **Styling:** Use SCSS with CSS variables for theming as defined in the UI Spec (e.g., `--primary-dark: #111827`). Adhere to the specified `8px` grid system for spacing.
-   **Real-time Updates:** The right-column panels should be connected to a service that receives real-time updates from the backend, potentially via WebSockets or Server-Sent Events (SSE).

## 6. Design & Documentation References

-   **Figma Design:** [Figma Design - Course Creator](https://www.figma.com/design/DBLOzIMGLV4TJYopgJhTze/Feynman?node-id=1-384&m=dev)
-   **Product Requirements:** PRD Section 4.1 "Course Creator Main Screen"
-   **UI Specifications:** UI Design Spec Sections 4 (Visual Language), 5 (Component Architecture), and 7 (Styling).
