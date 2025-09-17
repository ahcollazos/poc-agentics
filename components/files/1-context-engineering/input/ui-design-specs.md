# UI Design Specification: EduContent AI Course Creator (Angular)

## 1. Introduction

This document provides a comprehensive design and technical specification for the **EduContent AI Course Creator**, an intelligent educational content generation platform. The system enables educators and content creators to design, generate, and manage comprehensive educational courses with AI assistance. The application is built as a responsive single-page application (SPA) using the **Angular framework** with custom components and Material Design principles.

**Project Overview:** This specification defines the visual language, component architecture, and implementation guidelines for a modern AI-powered course creation system that includes:

- Course configuration and structuring interface
- Real-time AI content generation with progress tracking
- Multi-stage review and approval workflow
- Integrated quality assurance dashboard
- Navigation sidebar and header system

## 2. Application Architecture & Workflow

### 2.1 Core User Journey

The application implements a complete course creation and management workflow:

```
Course Dashboard → Configuration → AI Generation → Review & Approval → Publication
    (Sidebar)        (Forms)        (Progress)       (Queue)          (Complete)
```

1. **Course Navigation**: Sidebar provides access to all content generation tools
2. **Course Configuration**: Users define course parameters, structure, and AI settings
3. **AI Generation**: Real-time progress tracking as AI generates course content
4. **Review Process**: Module-by-module review with approve/edit/regenerate options
5. **Quality Assurance**: Automated checks for plagiarism, facts, citations, and accessibility

### 2.2 Key Features Implemented

- ✅ **Sidebar Navigation**: Categorized menu system with active state management
- ✅ **Course Configuration Forms**: Multi-section forms with validation
- ✅ **Real-time Progress Tracking**: Visual progress bars with time estimates
- ✅ **Review Queue System**: Status-based workflow for generated content
- ✅ **Quality Checks Dashboard**: Integrated validation status indicators
- ✅ **Responsive Layout**: Two-column design optimized for desktop and tablet

## 3. Design Principles

### 3.1 Core Principles

1. **AI-First Interface**: Design around AI generation workflows with real-time feedback and progress visualization.
2. **Progressive Disclosure**: Show complexity only when needed, keeping the initial interface clean and approachable.
3. **Status-Driven Design**: Visual indicators for every state - generating, reviewing, approved, failed.
4. **Guided Workflow**: Clear progression from configuration to generation to review to publication.
5. **Information Hierarchy**: Two-column layout separating configuration (left) from status/progress (right).
6. **Accessibility**: WCAG 2.1 AA compliance with proper color contrast, keyboard navigation, and screen reader support.

## 4. Visual Language & Theming

The visual design follows a modern, clean aesthetic optimized for educational content creation workflows.

### 4.1 Color Palette

#### Primary Colors (Core Interface)

- **Primary Dark**: `#111827`
  - **Usage**: Primary buttons, active states, sidebar navigation, header elements
- **Pure White**: `#ffffff`
  - **Usage**: Main backgrounds, cards, content areas, text on dark surfaces
- **Background Gray**: `#f9fafb`
  - **Usage**: Sidebar background, subtle backgrounds, alternate sections

#### Text Colors

- **Primary Text**: `#111827`
  - **Usage**: Headings, primary content, important labels
- **Secondary Text**: `#4b5563`
  - **Usage**: Descriptions, secondary information, muted content
- **Tertiary Text**: `#9ca3af`
  - **Usage**: Placeholder text, disabled states, very subtle text
- **Light Text**: `#e5e7eb`
  - **Usage**: Dividers, borders, very light backgrounds

#### Semantic Colors

- **Success Green**: `#10b981`
  - **Usage**: Completed states, approved items, success messages
- **Warning Yellow**: `#f59e0b`
  - **Usage**: Pending states, warnings, in-progress items
- **Error Red**: `#ef4444`
  - **Usage**: Error states, failed checks, destructive actions
- **Info Blue**: `#3b82f6`
  - **Usage**: Information badges, links, informational states

#### Status-Specific Colors

- **Review Badge**: `#fbbf24` (Yellow)
  - **Usage**: Items awaiting review
- **Approved Badge**: `#34d399` (Green)
  - **Usage**: Approved modules
- **Generating Badge**: `#60a5fa` (Blue)
  - **Usage**: Content being generated
- **Needs Review**: `#f87171` (Light Red)
  - **Usage**: Items requiring attention

#### Interactive States

- **Hover State**: `#f3f4f6`
  - **Usage**: Hover backgrounds for clickable elements
- **Active State**: `#e5e7eb`
  - **Usage**: Active/pressed states
- **Focus Ring**: `#2563eb`
  - **Usage**: Focus indicators for accessibility
- **Disabled**: `#d1d5db`
  - **Usage**: Disabled controls and text

### 4.2 Typography

The application uses **Inter** as the primary font family for a clean, modern appearance.

- **Primary Font Family**:
  - `Inter, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif`
  
- **Type Scale**:
  - **Page Title**: `24px`, `400` (Regular), `32px` line-height
  - **Section Header**: `20px`, `400` (Regular), `28px` line-height
  - **Panel Title**: `18px`, `400` (Regular), `24px` line-height
  - **Body Text**: `16px`, `400` (Regular), `24px` line-height
  - **Label Text**: `14px`, `400` (Regular), `20px` line-height
  - **Small Text**: `12px`, `400` (Regular), `16px` line-height
  - **Button Text**: `16px`, `400` (Regular), `24px` line-height
  - **Badge Text**: `12px`, `500` (Medium), `16px` line-height
  
- **Text Hierarchy**:
  - **Primary Headings**: Color `#111827`, Weight `400`
  - **Secondary Text**: Color `#4b5563`, Weight `400`
  - **Muted Text**: Color `#9ca3af`, Weight `400`
  - **Interactive Text**: Color `#3b82f6`, Weight `400`
  - **Error Text**: Color `#ef4444`, Weight `400`

### 4.3 Spacing & Layout Grid

#### Grid System

- **Base Unit**: 8px grid for consistent spacing
- **Container Width**: 1440px maximum
- **Sidebar Width**: 256px fixed
- **Main Content**: Flexible with 32px padding
- **Right Panel**: 352px fixed width

#### Spacing Scale

```css
:root {
  --space-1: 4px;   /* Micro spacing */
  --space-2: 8px;   /* Tight spacing */
  --space-3: 12px;  /* Small spacing */
  --space-4: 16px;  /* Default spacing */
  --space-5: 24px;  /* Medium spacing */
  --space-6: 32px;  /* Large spacing */
  --space-7: 48px;  /* Extra large spacing */
  --space-8: 64px;  /* Jumbo spacing */
}
```

#### Component Spacing

- **Card Padding**: 24px internal padding
- **Form Field Spacing**: 16px between fields
- **Button Spacing**: 12px between buttons
- **Section Spacing**: 24px between major sections
- **Panel Spacing**: 32px between panels

### 4.4 Component Dimensions

- **Buttons**:
  - Height: 42px (standard), 32px (small)
  - Padding: 12px 16px
  - Border radius: 8px
  
- **Form Inputs**:
  - Height: 48px (standard), 42px (compact)
  - Padding: 10px 12px
  - Border radius: 8px
  
- **Cards**:
  - Border radius: 8px
  - Border: 1px solid `#e5e7eb`
  - Shadow: `0 1px 2px 0 rgba(0, 0, 0, 0.05)`
  
- **Progress Bars**:
  - Height: 8px
  - Border radius: 9999px (full)
  - Background: `#e5e7eb`
  - Fill: Context-dependent (green, blue, etc.)

## 5. Component Architecture

### 5.1 Core Layout Components

#### 1. **Main Application Shell**

- **Header Navigation**: Fixed top bar with app branding and user menu
- **Sidebar Navigation**: Fixed left panel with feature navigation
- **Content Area**: Scrollable main content with responsive grid

#### 2. **Header Component**

```
┌─────────────────────────────────────────────────────────────────────┐
│ [Logo] EduContent AI Assistant    Projects Library Analytics Settings│ [🔔] [Avatar]
└─────────────────────────────────────────────────────────────────────┘
```

- **Dimensions**: Full width × 68px height
- **Background**: White with bottom shadow
- **Elements**:
  - Logo: 36×36px with 6px border radius
  - Navigation links: 16px font, `#4b5563` color
  - User avatar: 32×32px circular
  - Notification icon: 14×16px with badge

#### 3. **Sidebar Navigation**

```
┌────────────────┐
│ [+] New Project│
├────────────────┤
│ CONTENT GEN.   │
│ ✓ Course Creator│
│   Text Content │
│   Video Scripts│
│   Image Assets │
├────────────────┤
│ RESEARCH & VAL.│
│   Bibliography │
│   Plagiarism   │
│   Fact Check   │
├────────────────┤
│ ASSESSMENT     │
│   Quiz Builder │
│   Exam Gen.    │
├────────────────┤
│ PERSONALIZATION│
│   Adaptive     │
│   Micro-Learn  │
└────────────────┘
```

- **Dimensions**: 256px width × full height
- **Background**: `#f9fafb`
- **Border**: 1px right border `#e5e7eb`
- **Category Headers**: 12px uppercase, `#6b7280`
- **Menu Items**: 16px regular, 12px padding, 8px border radius

### 5.2 Course Creator Components

#### 1. **Course Configuration Panel**

- **Container**: White background, 8px border radius, 1px border
- **Sections**:
  - Course Title (text input)
  - Subject Area & Difficulty (dropdowns side-by-side)
  - Target Audience (textarea)
  - Learning Objectives (textarea)
- **Field Spacing**: 20px vertical between fields

#### 2. **Content Structure Panel**

- **Number Controls**: 80px wide inputs with increment/decrement
- **Toggle Switches**: iOS-style 44×24px switches
- **Labels**: Left-aligned with controls right-aligned

#### 3. **AI Generation Settings Panel**

- **Dropdowns**: Full width with chevron indicator
- **Toggle**: Include Citations switch

### 5.3 Progress & Status Components

#### 1. **Generation Progress Panel**

```
┌─────────────────────────────────────┐
│ Generation Progress                  │
├─────────────────────────────────────┤
│ Course Outline          Complete    │
│ ████████████████████████ 100%       │
│                                      │
│ Module Content              3/8      │
│ ████████░░░░░░░░░░░░░░░░ 37.5%      │
│                                      │
│ Assessments            Pending       │
│ ░░░░░░░░░░░░░░░░░░░░░░░░ 0%         │
├─────────────────────────────────────┤
│ ⏱ Estimated completion: 12 minutes   │
│ AI is currently generating Module 4  │
└─────────────────────────────────────┘
```

- **Progress Bars**: 8px height, full rounded
- **Status Text**: Right-aligned, 14px
- **Estimation Box**: Gray background, info icon

#### 2. **Review Queue Panel**

- **Module Cards**: 84px height with status badge
- **Status Badges**: Rounded 4px, 12px font
- **Action Buttons**: 26px height, 4px border radius
- **ETA Display**: 12px text, `#6b7280` color

#### 3. **Quality Checks Panel**

- **Check Items**: Icon + Label + Status format
- **Icons**: 16×16px with status-based colors
- **Status Text**: Right-aligned, 12px font
- **Spacing**: 8px between check items

## 6. Implementation Guidelines (Angular)

### 6.1 Component Structure

```typescript
// app.routes.ts
export const routes: Routes = [
  { 
    path: '', 
    redirectTo: '/course-creator', 
    pathMatch: 'full' 
  },
  { 
    path: 'course-creator',
    loadComponent: () => import('./features/course-creator/course-creator.component')
      .then(m => m.CourseCreatorComponent)
  },
  {
    path: 'text-content',
    loadComponent: () => import('./features/text-content/text-content.component')
      .then(m => m.TextContentComponent)
  }
];
```

### 6.2 Course Creator Component

```typescript
@Component({
  selector: 'app-course-creator',
  standalone: true,
  imports: [
    CommonModule,
    ReactiveFormsModule,
    SidebarNavigationComponent,
    HeaderNavigationComponent,
    CourseConfigurationComponent,
    GenerationProgressComponent,
    ReviewQueueComponent,
    QualityChecksComponent
  ],
  template: `
    <div class="app-container">
      <app-header-navigation></app-header-navigation>
      
      <div class="main-layout">
        <app-sidebar-navigation 
          [activeItem]="'course-creator'"
          (navigate)="onNavigate($event)">
        </app-sidebar-navigation>
        
        <main class="content-area">
          <div class="content-wrapper">
            <div class="page-header">
              <h1 class="page-title">Course Creator</h1>
              <p class="page-description">
                Create comprehensive educational courses with AI assistance
              </p>
            </div>
            
            <div class="content-grid">
              <div class="left-column">
                <app-course-configuration 
                  [formGroup]="courseForm">
                </app-course-configuration>
                
                <app-content-structure
                  [formGroup]="structureForm">
                </app-content-structure>
                
                <app-ai-settings
                  [formGroup]="aiSettingsForm">
                </app-ai-settings>
              </div>
              
              <div class="right-column">
                <app-generation-progress
                  [progress]="generationProgress$ | async">
                </app-generation-progress>
                
                <app-review-queue
                  [modules]="reviewQueue$ | async"
                  (approve)="onApprove($event)"
                  (edit)="onEdit($event)">
                </app-review-queue>
                
                <app-quality-checks
                  [checks]="qualityChecks$ | async">
                </app-quality-checks>
              </div>
            </div>
          </div>
        </main>
      </div>
    </div>
  `
})
export class CourseCreatorComponent implements OnInit {
  courseForm!: FormGroup;
  structureForm!: FormGroup;
  aiSettingsForm!: FormGroup;
  
  generationProgress$ = this.courseService.generationProgress$;
  reviewQueue$ = this.courseService.reviewQueue$;
  qualityChecks$ = this.courseService.qualityChecks$;

  constructor(
    private fb: FormBuilder,
    private courseService: CourseService,
    private router: Router
  ) {}

  ngOnInit() {
    this.initializeForms();
  }

  initializeForms() {
    this.courseForm = this.fb.group({
      title: ['', Validators.required],
      subjectArea: ['', Validators.required],
      difficultyLevel: ['', Validators.required],
      targetAudience: ['', Validators.required],
      learningObjectives: ['', Validators.required]
    });
    
    this.structureForm = this.fb.group({
      numberOfModules: [8, [Validators.min(1), Validators.max(20)]],
      lessonsPerModule: [4, [Validators.min(1), Validators.max(10)]],
      includeAssessments: [true],
      includeInteractiveElements: [true]
    });
    
    this.aiSettingsForm = this.fb.group({
      contentStyle: ['Academic'],
      language: ['English'],
      includeCitations: [true]
    });
  }

  onGenerate() {
    const formData = {
      ...this.courseForm.value,
      ...this.structureForm.value,
      ...this.aiSettingsForm.value
    };
    
    this.courseService.generateCourse(formData).subscribe();
  }
}
```

### 6.3 Sidebar Navigation Component

```typescript
@Component({
  selector: 'app-sidebar-navigation',
  template: `
    <aside class="sidebar">
      <button class="new-project-btn">
        <mat-icon>add</mat-icon>
        New Project
      </button>
      
      <nav class="nav-menu">
        <div *ngFor="let category of navigationCategories" 
             class="nav-category">
          <h3 class="category-title">{{ category.title }}</h3>
          
          <div class="nav-items">
            <button *ngFor="let item of category.items"
                    class="nav-item"
                    [class.active]="activeItem === item.id"
                    (click)="navigate.emit(item.id)">
              <mat-icon>{{ item.icon }}</mat-icon>
              {{ item.label }}
            </button>
          </div>
        </div>
      </nav>
    </aside>
  `,
  styles: [`
    .sidebar {
      width: 256px;
      height: 100%;
      background: #f9fafb;
      border-right: 1px solid #e5e7eb;
      padding: 24px;
    }
    
    .new-project-btn {
      width: 100%;
      height: 48px;
      background: #111827;
      color: white;
      border: none;
      border-radius: 8px;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 8px;
      font-size: 16px;
      cursor: pointer;
      margin-bottom: 24px;
    }
    
    .nav-category {
      margin-bottom: 32px;
    }
    
    .category-title {
      font-size: 12px;
      font-weight: 400;
      color: #6b7280;
      text-transform: uppercase;
      letter-spacing: 0.3px;
      margin-bottom: 12px;
    }
    
    .nav-item {
      width: 100%;
      padding: 12px;
      background: transparent;
      border: none;
      border-radius: 8px;
      display: flex;
      align-items: center;
      gap: 12px;
      font-size: 16px;
      color: #4b5563;
      cursor: pointer;
      text-align: left;
      margin-bottom: 4px;
    }
    
    .nav-item:hover {
      background: #f3f4f6;
    }
    
    .nav-item.active {
      background: white;
      color: #111827;
      border: 1px solid #e5e7eb;
    }
  `]
})
export class SidebarNavigationComponent {
  @Input() activeItem = '';
  @Output() navigate = new EventEmitter<string>();
  
  navigationCategories = [
    {
      title: 'Content Generation',
      items: [
        { id: 'course-creator', label: 'Course Creator', icon: 'school' },
        { id: 'text-content', label: 'Text Content', icon: 'article' },
        { id: 'video-scripts', label: 'Video Scripts', icon: 'videocam' },
        { id: 'image-assets', label: 'Image Assets', icon: 'image' }
      ]
    },
    // Additional categories...
  ];
}
```

## 7. Styling & CSS Architecture

### 7.1 Global Styles

```scss
// styles.scss
:root {
  // Colors
  --primary-dark: #111827;
  --primary-white: #ffffff;
  --bg-gray: #f9fafb;
  
  --text-primary: #111827;
  --text-secondary: #4b5563;
  --text-muted: #9ca3af;
  
  --border-color: #e5e7eb;
  --hover-bg: #f3f4f6;
  
  --success: #10b981;
  --warning: #f59e0b;
  --error: #ef4444;
  --info: #3b82f6;
  
  // Spacing
  --space-xs: 4px;
  --space-sm: 8px;
  --space-md: 16px;
  --space-lg: 24px;
  --space-xl: 32px;
  
  // Typography
  --font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  
  // Dimensions
  --header-height: 68px;
  --sidebar-width: 256px;
  --right-panel-width: 352px;
  
  // Borders & Shadows
  --border-radius: 8px;
  --border-width: 1px;
  --shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: var(--font-family);
  font-size: 16px;
  line-height: 1.5;
  color: var(--text-primary);
  background: var(--primary-white);
}
```

### 7.2 Layout Styles

```scss
.app-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
}

.main-layout {
  display: flex;
  flex: 1;
  overflow: hidden;
}

.content-area {
  flex: 1;
  overflow-y: auto;
  background: var(--primary-white);
}

.content-wrapper {
  max-width: 1440px;
  margin: 0 auto;
  padding: var(--space-xl);
}

.content-grid {
  display: grid;
  grid-template-columns: 1fr 352px;
  gap: var(--space-xl);
  align-items: start;
}

.left-column {
  display: flex;
  flex-direction: column;
  gap: var(--space-lg);
}

.right-column {
  display: flex;
  flex-direction: column;
  gap: var(--space-lg);
  position: sticky;
  top: var(--space-xl);
}
```

### 7.3 Component Styles

```scss
// Panel styles
.panel {
  background: var(--primary-white);
  border: var(--border-width) solid var(--border-color);
  border-radius: var(--border-radius);
  padding: var(--space-lg);
}

.panel-title {
  font-size: 18px;
  font-weight: 400;
  color: var(--text-primary);
  margin-bottom: var(--space-lg);
}

// Form styles
.form-group {
  margin-bottom: var(--space-md);
}

.form-label {
  display: block;
  font-size: 14px;
  color: var(--text-secondary);
  margin-bottom: var(--space-sm);
}

.form-control {
  width: 100%;
  height: 48px;
  padding: 0 var(--space-md);
  border: var(--border-width) solid var(--border-color);
  border-radius: var(--border-radius);
  font-size: 16px;
  color: var(--text-primary);
  background: var(--primary-white);
  
  &::placeholder {
    color: var(--text-muted);
  }
  
  &:focus {
    outline: none;
    border-color: #2563eb;
    box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.1);
  }
}

// Button styles
.btn {
  height: 42px;
  padding: 0 var(--space-md);
  border: none;
  border-radius: var(--border-radius);
  font-size: 16px;
  font-weight: 400;
  cursor: pointer;
  display: inline-flex;
  align-items: center;
  gap: var(--space-sm);
  transition: all 0.2s;
  
  &:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
}

.btn-primary {
  background: var(--primary-dark);
  color: var(--primary-white);
  
  &:hover:not(:disabled) {
    background: #1f2937;
  }
}

.btn-secondary {
  background: var(--primary-white);
  color: var(--text-secondary);
  border: var(--border-width) solid var(--border-color);
  
  &:hover:not(:disabled) {
    background: var(--hover-bg);
  }
}

// Status badges
.badge {
  display: inline-block;
  padding: 2px 8px;
  border-radius: 4px;
  font-size: 12px;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.badge-review {
  background: rgba(251, 191, 36, 0.1);
  color: #f59e0b;
}

.badge-approved {
  background: rgba(52, 211, 153, 0.1);
  color: #10b981;
}

.badge-generating {
  background: rgba(96, 165, 250, 0.1);
  color: #3b82f6;
}

// Progress bars
.progress-bar {
  width: 100%;
  height: 8px;
  background: var(--border-color);
  border-radius: 9999px;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background: var(--primary-dark);
  transition: width 0.3s ease;
}
```

## 8. Responsive Design

### 8.1 Breakpoints

```scss
$breakpoints: (
  'mobile': 480px,
  'tablet': 768px,
  'desktop': 1024px,
  'wide': 1440px
);
```

### 8.2 Responsive Layout

```scss
// Tablet (768px - 1023px)
@media (max-width: 1023px) {
  .content-grid {
    grid-template-columns: 1fr;
  }
  
  .right-column {
    position: relative;
    top: 0;
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: var(--space-lg);
  }
}

// Mobile (< 768px)
@media (max-width: 767px) {
  .sidebar {
    position: fixed;
    left: -256px;
    z-index: 100;
    transition: left 0.3s ease;
    
    &.open {
      left: 0;
    }
  }
  
  .content-wrapper {
    padding: var(--space-md);
  }
  
  .page-title {
    font-size: 20px;
  }
  
  .panel {
    padding: var(--space-md);
  }
}
```

## 9. Accessibility Guidelines

### 9.1 Core Requirements

- **Color Contrast**: Minimum 4.5:1 for normal text, 3:1 for large text
- **Keyboard Navigation**: All interactive elements keyboard accessible
- **Focus Indicators**: Clear visible focus states for all controls
- **Screen Reader Support**: Proper ARIA labels and semantic HTML
- **Motion**: Respect prefers-reduced-motion preferences

### 9.2 Implementation

```html
<!-- Form accessibility -->
<div class="form-group">
  <label for="course-title" class="form-label">
    Course Title
    <span class="required" aria-label="required">*</span>
  </label>
  <input 
    id="course-title"
    type="text"
    class="form-control"
    aria-describedby="course-title-error"
    aria-required="true"
    aria-invalid="false">
  <span id="course-title-error" class="error-text" role="alert"></span>
</div>

<!-- Progress accessibility -->
<div class="progress-section" role="region" aria-label="Generation Progress">
  <h3 id="progress-title">Module Content</h3>
  <div 
    class="progress-bar"
    role="progressbar"
    aria-labelledby="progress-title"
    aria-valuenow="37.5"
    aria-valuemin="0"
    aria-valuemax="100">
    <div class="progress-fill" style="width: 37.5%"></div>
  </div>
  <span class="sr-only">37.5% complete, 3 of 8 modules</span>
</div>

<!-- Button accessibility -->
<button 
  class="btn btn-primary"
  aria-label="Generate course content"
  aria-busy="false"
  aria-disabled="false">
  <mat-icon aria-hidden="true">play_arrow</mat-icon>
  Generate
</button>
```

## 10. Performance Optimization

### 10.1 Strategies

- **Lazy Loading**: Load feature modules on demand
- **Virtual Scrolling**: For long lists of modules/content
- **OnPush Change Detection**: Optimize Angular change detection
- **Image Optimization**: Use WebP format, lazy load images
- **Code Splitting**: Separate vendor and application bundles

### 10.2 Implementation

```typescript
// Lazy loading routes
const routes: Routes = [
  {
    path: 'course-creator',
    loadComponent: () => import('./features/course-creator/course-creator.component')
      .then(m => m.CourseCreatorComponent)
  }
];

// OnPush change detection
@Component({
  selector: 'app-generation-progress',
  changeDetection: ChangeDetectionStrategy.OnPush,
  // ...
})

// Virtual scrolling for lists
@Component({
  template: `
    <cdk-virtual-scroll-viewport itemSize="84" class="review-list">
      <div *cdkVirtualFor="let module of modules" class="module-card">
        {{ module.title }}
      </div>
    </cdk-virtual-scroll-viewport>
  `
})
```

## 11. Testing Strategy

### 11.1 Component Testing

```typescript
describe('CourseCreatorComponent', () => {
  let component: CourseCreatorComponent;
  let fixture: ComponentFixture<CourseCreatorComponent>;
  let courseService: jasmine.SpyObj<CourseService>;

  beforeEach(() => {
    const spy = jasmine.createSpyObj('CourseService', ['generateCourse']);
    
    TestBed.configureTestingModule({
      imports: [CourseCreatorComponent],
      providers: [
        { provide: CourseService, useValue: spy }
      ]
    });
    
    fixture = TestBed.createComponent(CourseCreatorComponent);
    component = fixture.componentInstance;
    courseService = TestBed.inject(CourseService) as jasmine.SpyObj<CourseService>;
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should initialize forms on init', () => {
    component.ngOnInit();
    expect(component.courseForm).toBeDefined();
    expect(component.structureForm).toBeDefined();
    expect(component.aiSettingsForm).toBeDefined();
  });

  it('should call service on generate', () => {
    component.ngOnInit();
    component.courseForm.patchValue({
      title: 'Test Course',
      subjectArea: 'Mathematics',
      difficultyLevel: 'Beginner',
      targetAudience: 'Students',
      learningObjectives: 'Learn math'
    });
    
    component.onGenerate();
    expect(courseService.generateCourse).toHaveBeenCalled();
  });
});
```

### 11.2 E2E Testing

```typescript
describe('Course Creator E2E', () => {
  it('should complete course creation flow', async () => {
    await browser.get('/course-creator');
    
    // Fill in course details
    await element(by.id('course-title')).sendKeys('Test Course');
    await element(by.id('subject-area')).click();
    await element(by.cssContainingText('option', 'Mathematics')).click();
    
    // Configure structure
    await element(by.id('num-modules')).clear();
    await element(by.id('num-modules')).sendKeys('10');
    
    // Start generation
    await element(by.buttonText('Generate')).click();
    
    // Verify progress appears
    expect(await element(by.css('.progress-bar')).isPresent()).toBe(true);
  });
});
```

## 12. Deployment Considerations

### 12.1 Build Configuration

```json
// angular.json
{
  "build": {
    "configurations": {
      "production": {
        "optimization": true,
        "outputHashing": "all",
        "sourceMap": false,
        "namedChunks": false,
        "extractLicenses": true,
        "vendorChunk": false,
        "buildOptimizer": true,
        "budgets": [
          {
            "type": "initial",
            "maximumWarning": "2mb",
            "maximumError": "5mb"
          }
        ]
      }
    }
  }
}
```

### 12.2 Environment Configuration

```typescript
// environment.prod.ts
export const environment = {
  production: true,
  apiUrl: 'https://api.educontent.ai',
  wsUrl: 'wss://api.educontent.ai',
  features: {
    aiGeneration: true,
    qualityChecks: true,
    collaboration: false // Coming soon
  }
};
```

This comprehensive specification provides complete design and implementation guidelines for the EduContent AI Course Creator application, ensuring consistency, accessibility, and optimal user experience throughout the platform.
