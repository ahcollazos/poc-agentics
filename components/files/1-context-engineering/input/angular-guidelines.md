# Modern Angular Development Guidelines

## 1. Project Structure

Our Angular applications use a feature-focused structure with standalone components.

```plaintext
project-root/
├── src/
│   ├── app/
│   │   ├── core/                          # App-wide services and utilities
│   │   │   ├── auth/                      # Authentication-related code
│   │   │   ├── http/                      # HTTP-related code
│   │   │   ├── services/                  # App-wide singleton services
│   │   │   ├── utils/                     # Utility functions
│   │   │   └── core.providers.ts          # Core providers for bootstrapping
│   │   ├── features/                      # Feature-specific components and logic
│   │   │   ├── dashboard/                 # Dashboard feature
│   │   │   │   ├── components/            # Feature-specific components
│   │   │   │   ├── pages/                 # Routed page components
│   │   │   │   ├── services/              # Feature-specific services
│   │   │   │   ├── models/                # Feature-specific models
│   │   │   │   └── dashboard.routes.ts    # Feature routes
│   │   │   └── ...                        # Other features
│   │   ├── shared/                        # Shared UI components and directives
│   │   │   ├── components/                # Reusable UI components
│   │   │   ├── directives/                # Custom directives
│   │   │   └── pipes/                     # Custom pipes
│   │   ├── layout/                        # App-wide layout components
│   │   │   ├── header/                    # Header component
│   │   │   ├── footer/                    # Footer component
│   │   │   └── shell/                     # App shell component
│   │   ├── app.config.ts                  # App configuration and providers
│   │   ├── app.routes.ts                  # Main application routes
│   │   └── app.ts                         # Root app component
│   ├── assets/                            # Static assets
│   ├── environments/                      # Environment configuration
│   ├── styles/                            # Global styles
│   ├── index.html                         # Main HTML file
│   └── main.ts                            # Main entry point
└── ...                                    # Configuration files
```

### Key Structural Guidelines

* **Standalone Components**: Use standalone components instead of NgModules.
* **Feature-First Organization**: Organize by domain/feature rather than technical role.
* **Core Directory**: Contains app-wide services, interceptors, guards, and utilities.
* **Shared Directory**: Houses reusable UI components, directives, and pipes.
* **Lazy Loading**: Implement lazy loading for feature routes to improve initial load time.

## 2. Component Architecture

Signal-based, component-driven architecture.

### Component Types

1. **Presentational Components**:
   * Focus on UI presentation with minimal business logic.
   * Receive data via inputs and emit events via outputs.
   * Highly reusable across different features.

2. **Container Components**:
   * Manage state, coordinate data flow, and contain business logic.
   * Use services for data fetching and state management.
   * Compose multiple presentational components.

3. **Page Components**:
   * Represent specific routes in the application.
   * Orchestrate container and presentational components for a complete view.

4. **Layout Components**:
   * Define the overall structure of the application or sections.
   * Examples: app shell, navigation, sidebars, headers, footers.

5. **UI Components**:
   * Generic, reusable UI elements (buttons, inputs, cards, etc.).
   * Should leverage UI libraries when possible.

### Component Guidelines

* **Signals for State**: Use Angular Signals for reactive, local component state.

  ```typescript
  count = signal(0);
  doubled = computed(() => this.count() * 2);
  
  increment() {
    this.count.update(value => value + 1);
  }
  ```

* **Functional Injection**: Prefer the `inject()` function over constructor-based dependency injection.

  ```typescript
  private userService = inject(UserService);
  ```

* **OnPush Change Detection**: Use `ChangeDetectionStrategy.OnPush` for better performance.

  ```typescript
  import { ChangeDetectionStrategy } from '@angular/core';
  
  @Component({
    selector: 'app-user-card',
    standalone: true,
    templateUrl: './user-card.component.html',
    changeDetection: ChangeDetectionStrategy.OnPush
  })
  export class UserCardComponent { }
  ```

* **Standalone Components**: Use the standalone approach for all components. Always set `standalone: true` explicitly in the decorator.

  ```typescript
  @Component({
    selector: 'app-feature',
    standalone: true,
    imports: [CommonModule, RouterModule],
    templateUrl: './feature.component.html',
    styleUrl: './feature.component.css'
  })
  export class FeatureComponent { }
  ```

* **Input/Output API**: Use the modern `input()` and `output()` functions instead of decorators.

  ```typescript
  title = input<string>('Default Title');
  valueChange = output<number>();
  ```

## 3. Routing Structure

* **Main Routes**: Define application-level routes in `app.routes.ts`.

  ```typescript
  export const routes: Routes = [
    {
      path: '',
      component: ShellComponent,
      children: [
        {
          path: '',
          loadChildren: () => import('./features/dashboard/dashboard.routes')
            .then(r => r.DASHBOARD_ROUTES)
        },
        // More routes...
      ]
    }
  ];
  ```

* **Feature Routes**: Each feature has its own routes file.

  ```typescript
  // features/dashboard/dashboard.routes.ts
  export const DASHBOARD_ROUTES: Routes = [
    {
      path: '',
      component: DashboardComponent
    },
    // More dashboard routes...
  ];
  ```

* **Route Guards**: Use functional route guards for protection.

  ```typescript
  export const authGuard = () => {
    const authService = inject(AuthService);
    const router = inject(Router);
    
    return authService.isAuthenticated() ? 
      true : 
      router.createUrlTree(['/auth/login']);
  };
  ```

## 4. State Management

* **Component State**: Use Angular Signals for local component state.

  ```typescript
  // Local component state with signals
  selectedItem = signal<Item | null>(null);
  items = signal<Item[]>([]);
  
  filteredItems = computed(() => {
    const search = this.searchTerm();
    return this.items().filter(item => item.name.includes(search));
  });
  ```

* **Feature State**: For feature-specific state, use NgRx SignalStore.

  ```typescript
  // Example NgRx SignalStore
  export const TodosStore = signalStore(
    withState<TodosState>({ todos: [], loading: false }),
    withMethods((store) => ({
      addTodo: (todo: Todo) => {
        store.update(state => ({
          ...state,
          todos: [...state.todos, todo]
        }));
      }
    }))
  );
  ```

* **Best Practices**:
  * Only use dedicated state management for complex, cross-cutting concerns.
  * Keep state as local as possible.
  * Use RxJS sparingly, primarily for complex async flows and HTTP.

## 5. Styling Approach

* **Angular Material**: Use utility classes for rapid UI development.
* **Component Styles**: Use component-scoped styles.
* **CSS Variables**: Define design tokens as CSS variables for theming.
* **Global Styles**: Keep global styles minimal; prefer component encapsulation.
* **Responsive Design**: Mobile-first approach using responsive utility classes.

## 6. TypeScript Best Practices

* **Type Definitions**: Explicitly type variables, parameters, return types, and component inputs/outputs.
* **Interfaces and Models**: Define clear interfaces for data models.
* **Best Practices**:
  * Enable strict type checking in `tsconfig.json`.
  * Avoid `any`; use `unknown` when type is truly unknown.
  * Prefer type inference when the type is obvious.
  * Use type guards and discriminated unions for type narrowing.
  * Leverage TypeScript utility types (Partial, Pick, Omit, etc.).

```typescript
// Example: Type-safe enum pattern
export const UserRole = {
  ADMIN: 'admin',
  USER: 'user',
} as const;
export type UserRole = typeof UserRole[keyof typeof UserRole];
```

## 7. Services and Dependency Injection

* **Service Provisioning**: Register services at the appropriate level.

  ```typescript
  // App-wide singleton service
  @Injectable({
    providedIn: 'root'
  })
  export class GlobalService { }
  
  // Feature-scoped service
  @Injectable()
  export class FeatureService { }
  
  // In feature routes file:
  export const FEATURE_ROUTES: Routes = [
    {
      path: '',
      providers: [FeatureService],
      // ...
    }
  ];
  ```

* **HTTP Services**: Create domain-specific services for API calls.

  ```typescript
  @Injectable({
    providedIn: 'root'
  })
  export class UserService {
    private http = inject(HttpClient);
    private apiUrl = 'api/users';
  
    getUsers(): Observable<User[]> {
      return this.http.get<User[]>(this.apiUrl);
    }
  }
  ```

## 8. Testing Strategy

* **Testing Stack**:
  * Unit Tests: Jasmine/Karma or Jest
  * End-to-End Tests: Playwright or Cypress
  * Component Tests: Angular Testing Library

* **Testing Guidelines**:
  * Test business logic in services thoroughly.
  * For components, focus on behavior, not implementation details.
  * Use shallow component tests unless integration testing is needed.
  * Mock external dependencies and services.

* **Test Organization**: Colocate test files with implementation using `.spec.ts` suffix.

## 9. Performance Considerations

* **Change Detection**: Use OnPush change detection strategy.
* **Lazy Loading**: Implement lazy loading for feature routes.
* **Virtual Scrolling**: Use `@angular/cdk/scrolling` for long lists.
* **Memoization**: Use `computed()` to derive values from signals.
* **Tree-Shakable Providers**: Use `providedIn: 'root'` for services.
* **Images**: Use `NgOptimizedImage` for all static images.
* **Host Bindings**: Use the `host` object instead of `@HostBinding` and `@HostListener` decorators.
* **Templates**: Use native control flow (`@if`, `@for`, `@switch`) instead of structural directives.
* **Class/Style Bindings**: Use `class` and `style` bindings instead of `ngClass` and `ngStyle`.
* **Signal Updates**: Use `update()` or `set()` instead of `mutate()` on signals.
* **Forms**: Prefer Reactive forms instead of Template-driven ones.

## 10. Accessibility (A11y)

* **Requirements**: Aim for WCAG 2.1 AA compliance.
* **Implementation**:
  * Use semantic HTML elements.
  * Ensure keyboard navigation works properly.
  * Add appropriate ARIA attributes when needed.
  * Maintain sufficient color contrast.
  * Provide text alternatives for non-text content.

## 11. Application Configuration

```typescript
// app.config.ts
import { ApplicationConfig } from '@angular/core';
import { provideRouter } from '@angular/router';
import { provideHttpClient, withInterceptors } from '@angular/common/http';

import { routes } from './app.routes';
import { authInterceptor } from './core/auth/interceptors/auth.interceptor';

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    provideHttpClient(
      withInterceptors([authInterceptor])
    ),
    // Other app-wide providers
  ]
};
```

## 12. Example Component Implementation

```typescript
// features/dashboard/components/stats-card/stats-card.component.ts
import { Component, signal, computed, input, ChangeDetectionStrategy } from '@angular/core';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-stats-card',
  imports: [CommonModule],
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <div class="card">
      <h3>{{ '{{ title() }}' }}</h3>
      <div class="value">{{ '{{ formattedValue() }}' }}</div>
      <div class="change" [class.positive]="isPositive()">
        {{ '{{ changePercentage() }}' }}%
      </div>
    </div>
  `,
  styleUrl: './stats-card.component.css'
})
export class StatsCardComponent {
  title = input<string>('');
  value = input<number>(0);
  change = input<number>(0);
  
  formattedValue = computed(() => {
    return this.value().toLocaleString();
  });
  
  changePercentage = computed(() => {
    return this.change().toFixed(1);
  });
  
  isPositive = computed(() => {
    return this.change() > 0;
  });
}
```

## 13. Code Generation Templates

To accelerate development, use these standardized templates for common patterns:

### Feature Component Template

```typescript
import { Component, signal, computed, inject, effect } from '@angular/core';
import { CommonModule } from '@angular/common';
import { Router } from '@angular/router';
import { ReactiveFormsModule, FormBuilder, FormGroup } from '@angular/forms';

// Material imports (verify availability)
import { MatCardModule } from '@angular/material/card';
import { MatButtonModule } from '@angular/material/button';
import { MatIconModule } from '@angular/material/icon';
import { MatProgressBarModule } from '@angular/material/progress-bar';

// Custom components (uncomment only when verified to exist)
// import { BreadcrumbComponent } from '../../shared/components/breadcrumb/breadcrumb.component';

@Component({
  selector: 'app-[FEATURE_NAME]',
  standalone: true,
  imports: [
    CommonModule,
    ReactiveFormsModule,
    MatCardModule,
    MatButtonModule,
    MatIconModule,
    MatProgressBarModule,
    // BreadcrumbComponent, // Uncomment when component exists
  ],
  template: `
    <div class="[FEATURE_NAME]-container">
      <!-- Breadcrumb (conditional) -->
      @if (showBreadcrumb) {
        <!-- <app-breadcrumb></app-breadcrumb> -->
        <nav class="breadcrumb-placeholder">
          <a routerLink="/[parent-route]">[Parent Section]</a> > [Current Page]
        </nav>
      }
      
      <h1 class="page-title">[FEATURE_TITLE]</h1>
      
      <!-- Loading state -->
      @if (isLoading()) {
        <mat-progress-bar mode="indeterminate"></mat-progress-bar>
        <div class="loading">Carregando...</div>
      }
      
      <!-- Error state -->
      @if (error()) {
        <mat-card class="error-card">
          <mat-card-content>
            <mat-icon>error</mat-icon>
            <span>{{ error() }}</span>
          </mat-card-content>
        </mat-card>
      }
      
      <!-- Main content -->
      @if (data() && !isLoading()) {
        <div class="content">
          @for (item of data(); track item.id) {
            <mat-card class="item-card">
              <mat-card-content>
                <h3>{{ item.name || 'Untitled' }}</h3>
                <p>{{ item.description || 'No description available' }}</p>
              </mat-card-content>
              <mat-card-actions>
                <button mat-button (click)="onItemAction(item)" [disabled]="!canPerformAction()">
                  Action
                </button>
              </mat-card-actions>
            </mat-card>
          }
        </div>
      }
      
      <!-- Action buttons -->
      <div class="actions">
        <button mat-flat-button color="primary" 
                (click)="onPrimaryAction()" 
                [disabled]="!canPerformAction()">
          [PRIMARY_ACTION]
        </button>
        <button mat-stroked-button (click)="onSecondaryAction()">
          [SECONDARY_ACTION]
        </button>
        <button mat-stroked-button (click)="goBack()">
          VOLTAR
        </button>
      </div>
    </div>
  `,
  styleUrl: './[FEATURE_NAME].component.css'
})
export class [FEATURE_NAME_PASCAL]Component {
  private readonly router = inject(Router);
  private readonly fb = inject(FormBuilder);
  private readonly [SERVICE_NAME] = inject([SERVICE_NAME_PASCAL]);

  // Core data signals
  data = signal<[DATA_TYPE][]>([]);
  selectedItem = signal<[DATA_TYPE] | null>(null);
  
  // State management signals
  isLoading = signal(true);
  loading = signal(false); // Alias for template compatibility
  error = signal<string | null>(null);
  
  // UI control signals
  showBreadcrumb = signal(true);
  
  // Form management (if needed)
  form!: FormGroup;
  
  // Computed values
  canPerformAction = computed(() => 
    this.data().length > 0 && !this.isLoading() && !this.error()
  );
  
  hasData = computed(() => this.data().length > 0);
  
  ngOnInit() {
    this.initializeForm();
    this.loadData();
    this.setupSignalSynchronization();
  }
  
  private initializeForm(): void {
    this.form = this.fb.group({
      // Add form controls here
    });
  }
  
  private setupSignalSynchronization(): void {
    // Sync related signals for template compatibility
    effect(() => {
      this.loading.set(this.isLoading());
    });
  }
  
  private loadData(): void {
    this.isLoading.set(true);
    this.error.set(null);
    
    this.[SERVICE_NAME].getData()
      .pipe(takeUntilDestroyed())
      .subscribe({
        next: (data) => {
          this.data.set(data);
          this.isLoading.set(false);
        },
        error: (err) => {
          this.error.set('Erro ao carregar dados. Tente novamente.');
          this.isLoading.set(false);
          console.error('Error loading data:', err);
        }
      });
  }
  
  // Event handlers with proper typing
  onInputEvent(event: Event): void {
    const target = event.target as HTMLInputElement;
    // Handle input change
  }
  
  onTextareaInput(event: Event): void {
    const target = event.target as HTMLTextAreaElement;
    // Handle textarea change
  }
  
  onSelectChange(event: Event): void {
    const target = event.target as HTMLSelectElement;
    // Handle select change
  }
  
  // Action methods
  onPrimaryAction(): void {
    if (!this.canPerformAction()) return;
    
    // Navigate to next step or perform main action
    this.router.navigate(['/[NEXT_ROUTE]']);
  }
  
  onSecondaryAction(): void {
    // Handle secondary action
  }
  
  onItemAction(item: [DATA_TYPE]): void {
    this.selectedItem.set(item);
    // Perform item-specific action
  }
  
  goBack(): void {
    this.router.navigate(['/[PARENT_ROUTE]']);
  }
  
  // Utility methods
  private handleError(error: any, message: string): void {
    this.error.set(message);
    this.isLoading.set(false);
    console.error(error);
  }
}
```

### Service Template

```typescript
import { Injectable, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { environment } from '../../../environments/environment';

@Injectable({
  providedIn: 'root'
})
export class [SERVICE_NAME_PASCAL]Service {
  private readonly http = inject(HttpClient);
  private readonly apiUrl = `${environment.apiUrl}/[ENDPOINT]`;

  get[ENTITY_PLURAL](): Observable<[ENTITY_TYPE][]> {
    return this.http.get<[ENTITY_TYPE][]>(this.apiUrl);
  }

  get[ENTITY_SINGULAR](id: string): Observable<[ENTITY_TYPE]> {
    return this.http.get<[ENTITY_TYPE]>(`${this.apiUrl}/${id}`);
  }

  create[ENTITY_SINGULAR](entity: Partial<[ENTITY_TYPE]>): Observable<[ENTITY_TYPE]> {
    return this.http.post<[ENTITY_TYPE]>(this.apiUrl, entity);
  }

  update[ENTITY_SINGULAR](id: string, entity: Partial<[ENTITY_TYPE]>): Observable<[ENTITY_TYPE]> {
    return this.http.put<[ENTITY_TYPE]>(`${this.apiUrl}/${id}`, entity);
  }

  delete[ENTITY_SINGULAR](id: string): Observable<void> {
    return this.http.delete<void>(`${this.apiUrl}/${id}`);
  }
}
```

### Model Interface Template

```typescript
export interface [ENTITY_TYPE] {
  id: string;
  name: string;
  description?: string;
  createdAt: Date;
  updatedAt: Date;
  status: [ENTITY_STATUS];
  // Add specific properties here
}

export type [ENTITY_STATUS] = 'active' | 'inactive' | 'pending';

export interface [ENTITY_TYPE]Filter {
  search?: string;
  status?: [ENTITY_STATUS];
  category?: string;
  // Add filter properties here
}

export interface [ENTITY_TYPE]Request {
  // Properties for API requests
}

export interface [ENTITY_TYPE]Response {
  // Properties for API responses
  data: [ENTITY_TYPE][];
  total: number;
  page: number;
  pageSize: number;
}
```

### Routes Template

```typescript
import { Routes } from '@angular/router';

export const [FEATURE_NAME_UPPER]_ROUTES: Routes = [
  {
    path: '',
    loadComponent: () => import('./[FEATURE_NAME].component')
      .then(m => m.[FEATURE_NAME_PASCAL]Component),
    data: { breadcrumb: '[FEATURE_TITLE]' }
  },
  {
    path: 'create',
    loadComponent: () => import('./create-[ENTITY]/create-[ENTITY].component')
      .then(m => m.Create[ENTITY_PASCAL]Component),
    data: { breadcrumb: 'Criar [ENTITY_NAME]' }
  },
  {
    path: 'edit/:id',
    loadComponent: () => import('./edit-[ENTITY]/edit-[ENTITY].component')
      .then(m => m.Edit[ENTITY_PASCAL]Component),
    canActivate: [entityExistsGuard],
    data: { breadcrumb: 'Editar [ENTITY_NAME]' }
  },
  {
    path: 'view/:id',
    loadComponent: () => import('./view-[ENTITY]/view-[ENTITY].component')
      .then(m => m.View[ENTITY_PASCAL]Component),
    canActivate: [entityExistsGuard],
    data: { breadcrumb: 'Visualizar [ENTITY_NAME]' }
  },
  {
    path: 'publish/:id',
    loadComponent: () => import('./publish-[ENTITY]/publish-[ENTITY].component')
      .then(m => m.Publish[ENTITY_PASCAL]Component),
    canActivate: [entityExistsGuard],
    data: { breadcrumb: 'Publicar [ENTITY_NAME]' }
  }
];
```

### SSR Configuration Template

```typescript
// app.routes.server.ts
import { RenderMode, ServerRoute } from '@angular/ssr';

export const serverRoutes: ServerRoute[] = [
  // Static routes - prerender for better SEO and performance
  {
    path: '',
    renderMode: RenderMode.Prerender
  },
  {
    path: '[feature]',
    renderMode: RenderMode.Prerender
  },
  
  // Parametric routes - use server rendering to avoid prerender issues
  {
    path: '[feature]/edit/**',
    renderMode: RenderMode.Server
  },
  {
    path: '[feature]/view/**',
    renderMode: RenderMode.Server
  },
  {
    path: '[feature]/publish/**',
    renderMode: RenderMode.Server
  },
  {
    path: '[feature]/create/**',
    renderMode: RenderMode.Server
  },
  
  // Catch-all for other routes - prerender by default
  {
    path: '**',
    renderMode: RenderMode.Prerender
  }
];
```

### Guard Template

```typescript
// guards/entity-exists.guard.ts
import { inject } from '@angular/core';
import { Router, ActivatedRouteSnapshot } from '@angular/router';
import { map, catchError } from 'rxjs/operators';
import { of } from 'rxjs';

export const entityExistsGuard = (route: ActivatedRouteSnapshot) => {
  const service = inject([ENTITY_NAME_PASCAL]Service);
  const router = inject(Router);
  const id = route.paramMap.get('id');
  
  if (!id) {
    return router.createUrlTree(['/[feature]']);
  }
  
  return service.get[ENTITY_NAME](id).pipe(
    map(() => true),
    catchError(() => {
      router.navigate(['/[feature]'], { 
        queryParams: { error: 'Entity not found' } 
      });
      return of(false);
    })
  );
};
```

### CSS Template

```css
/* [FEATURE_NAME].component.css */

/* =================
   CONTAINER LAYOUT
   ================= */
.{{ '[FEATURE_NAME]' }}-container {
  padding: 24px;
  max-width: 1200px;
  margin: 0 auto;
  min-height: calc(100vh - 120px);
  box-sizing: border-box;
}

/* =================
   TYPOGRAPHY
   ================= */
.page-title {
  font-size: 2rem;
  font-weight: 600;
  margin-bottom: 24px;
  color: var(--primary-color);
  line-height: 1.2;
}

.section-title {
  font-size: 1.5rem;
  font-weight: 500;
  margin-bottom: 16px;
  color: var(--text-color);
}

.subsection-title {
  font-size: 1.25rem;
  font-weight: 500;
  margin-bottom: 12px;
  color: var(--text-color-secondary);
}

/* =================
   STATE INDICATORS
   ================= */
.loading {
  text-align: center;
  padding: 48px 24px;
  color: var(--text-color-muted);
}

.loading .spinner {
  margin-bottom: 16px;
}

.error {
  color: var(--error-color);
  background-color: var(--error-bg);
  border-radius: 8px;
  border: 1px solid var(--error-border);
  padding: 16px;
  margin-bottom: 24px;
  display: flex;
  align-items: center;
  gap: 12px;
}

.error-icon {
  color: var(--error-color);
  flex-shrink: 0;
}

.success {
  color: var(--success-color);
  background-color: var(--success-bg);
  border-radius: 8px;
  border: 1px solid var(--success-border);
  padding: 16px;
  margin-bottom: 24px;
  display: flex;
  align-items: center;
  gap: 12px;
}

.warning {
  color: var(--warning-color);
  background-color: var(--warning-bg);
  border-radius: 8px;
  border: 1px solid var(--warning-border);
  padding: 16px;
  margin-bottom: 24px;
  display: flex;
  align-items: center;
  gap: 12px;
}

/* =================
   CONTENT LAYOUT
   ================= */
.content {
  display: grid;
  gap: 16px;
  margin-bottom: 24px;
}

.content-section {
  background-color: var(--surface-color);
  border-radius: 8px;
  padding: 20px;
  border: 1px solid var(--border-color);
}

.content-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 16px;
}

.content-flex {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

/* =================
   CARDS AND ITEMS
   ================= */
.item-card {
  background-color: var(--surface-color);
  border: 1px solid var(--border-color);
  border-radius: 8px;
  padding: 16px;
  transition: all 0.2s ease;
  cursor: pointer;
}

.item-card:hover {
  border-color: var(--primary-color);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.item-card:focus-within {
  outline: 2px solid var(--focus-color);
  outline-offset: 2px;
}

.item-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 12px;
}

.item-title {
  font-weight: 500;
  color: var(--text-color);
  margin: 0;
}

.item-description {
  color: var(--text-color-secondary);
  margin: 8px 0;
  line-height: 1.4;
}

.item-meta {
  display: flex;
  gap: 12px;
  font-size: 0.875rem;
  color: var(--text-color-muted);
  margin-top: 12px;
}

.item-status {
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 0.75rem;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.item-status.success {
  background-color: var(--success-bg);
  color: var(--success-color);
  border: 1px solid var(--success-border);
}

.item-status.warning {
  background-color: var(--warning-bg);
  color: var(--warning-color);
  border: 1px solid var(--warning-border);
}

.item-status.error {
  background-color: var(--error-bg);
  color: var(--error-color);
  border: 1px solid var(--error-border);
}

/* =================
   FORMS
   ================= */
.form-container {
  background-color: var(--surface-color);
  border-radius: 8px;
  padding: 24px;
  border: 1px solid var(--border-color);
}

.form-group {
  margin-bottom: 20px;
}

.form-label {
  display: block;
  margin-bottom: 6px;
  font-weight: 500;
  color: var(--text-color);
}

.form-control {
  width: 100%;
  padding: 12px;
  border: 1px solid var(--border-color);
  border-radius: 6px;
  font-size: 1rem;
  transition: border-color 0.2s ease, box-shadow 0.2s ease;
  box-sizing: border-box;
}

.form-control:focus {
  outline: none;
  border-color: var(--primary-color);
  box-shadow: 0 0 0 3px rgba(var(--primary-rgb), 0.1);
}

.form-control:invalid {
  border-color: var(--error-color);
}

.form-control:disabled {
  background-color: var(--disabled-bg);
  color: var(--disabled-color);
  cursor: not-allowed;
}

.form-error {
  color: var(--error-color);
  font-size: 0.875rem;
  margin-top: 4px;
}

.form-help {
  color: var(--text-color-muted);
  font-size: 0.875rem;
  margin-top: 4px;
}

/* =================
   BUTTONS
   ================= */
.actions {
  display: flex;
  gap: 12px;
  justify-content: flex-start;
  align-items: center;
  margin-top: 24px;
}

.btn {
  padding: 12px 24px;
  border-radius: 6px;
  border: none;
  font-weight: 500;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.2s ease;
  display: inline-flex;
  align-items: center;
  gap: 8px;
  text-decoration: none;
  box-sizing: border-box;
  min-height: 44px; /* Accessibility: minimum touch target */
}

.btn:focus {
  outline: 2px solid var(--focus-color);
  outline-offset: 2px;
}

.btn:disabled {
  background-color: var(--disabled-bg);
  color: var(--disabled-color);
  cursor: not-allowed;
  opacity: 0.6;
}

.btn-primary {
  background-color: var(--primary-color);
  color: white;
}

.btn-primary:hover:not(:disabled) {
  background-color: var(--primary-hover);
}

.btn-secondary {
  background-color: var(--secondary-color);
  color: var(--text-color);
  border: 1px solid var(--border-color);
}

.btn-secondary:hover:not(:disabled) {
  background-color: var(--secondary-hover);
}

.btn-danger {
  background-color: var(--error-color);
  color: white;
}

.btn-danger:hover:not(:disabled) {
  background-color: var(--error-hover);
}

.btn-ghost {
  background-color: transparent;
  color: var(--primary-color);
  border: 1px solid var(--primary-color);
}

.btn-ghost:hover:not(:disabled) {
  background-color: var(--primary-color);
  color: white;
}

.btn-sm {
  padding: 8px 16px;
  font-size: 0.875rem;
  min-height: 36px;
}

.btn-lg {
  padding: 16px 32px;
  font-size: 1.125rem;
  min-height: 52px;
}

/* =================
   NAVIGATION
   ================= */
.breadcrumb-placeholder {
  margin-bottom: 24px;
  padding: 12px 0;
  border-bottom: 1px solid var(--border-color);
}

.breadcrumb-placeholder a {
  color: var(--primary-color);
  text-decoration: none;
}

.breadcrumb-placeholder a:hover {
  text-decoration: underline;
}

.navigation-tabs {
  display: flex;
  border-bottom: 2px solid var(--border-color);
  margin-bottom: 24px;
}

.nav-tab {
  padding: 12px 24px;
  background: none;
  border: none;
  color: var(--text-color-secondary);
  cursor: pointer;
  border-bottom: 2px solid transparent;
  transition: all 0.2s ease;
}

.nav-tab:hover,
.nav-tab.active {
  color: var(--primary-color);
  border-bottom-color: var(--primary-color);
}

/* =================
   TABLES
   ================= */
.table-container {
  overflow-x: auto;
  border-radius: 8px;
  border: 1px solid var(--border-color);
}

.table {
  width: 100%;
  border-collapse: collapse;
  background-color: var(--surface-color);
}

.table th,
.table td {
  padding: 12px 16px;
  text-align: left;
  border-bottom: 1px solid var(--border-color);
}

.table th {
  background-color: var(--surface-secondary);
  font-weight: 600;
  color: var(--text-color);
}

.table tr:hover {
  background-color: var(--hover-bg);
}

.table tr:last-child td {
  border-bottom: none;
}

/* =================
   LISTS
   ================= */
.list-container {
  background-color: var(--surface-color);
  border-radius: 8px;
  border: 1px solid var(--border-color);
  overflow: hidden;
}

.list-item {
  padding: 16px;
  border-bottom: 1px solid var(--border-color);
  display: flex;
  align-items: center;
  gap: 12px;
  transition: background-color 0.2s ease;
}

.list-item:hover {
  background-color: var(--hover-bg);
}

.list-item:last-child {
  border-bottom: none;
}

.list-item-content {
  flex: 1;
}

.list-item-actions {
  display: flex;
  gap: 8px;
}

/* =================
   MODALS AND OVERLAYS
   ================= */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-content {
  background-color: var(--surface-color);
  border-radius: 8px;
  padding: 24px;
  max-width: 500px;
  width: 90%;
  max-height: 90vh;
  overflow-y: auto;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.modal-title {
  margin: 0;
  font-size: 1.25rem;
  font-weight: 600;
}

.modal-close {
  background: none;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  color: var(--text-color-secondary);
  padding: 0;
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 4px;
}

.modal-close:hover {
  background-color: var(--hover-bg);
  color: var(--text-color);
}

/* =================
   SEARCH AND FILTERS
   ================= */
.search-container {
  position: relative;
  margin-bottom: 24px;
}

.search-input {
  width: 100%;
  padding: 12px 16px 12px 40px;
  border: 1px solid var(--border-color);
  border-radius: 6px;
  font-size: 1rem;
  box-sizing: border-box;
}

.search-icon {
  position: absolute;
  left: 12px;
  top: 50%;
  transform: translateY(-50%);
  color: var(--text-color-muted);
}

.filter-container {
  display: flex;
  gap: 12px;
  margin-bottom: 24px;
  flex-wrap: wrap;
}

.filter-select {
  min-width: 150px;
  padding: 8px 12px;
  border: 1px solid var(--border-color);
  border-radius: 6px;
  background-color: var(--surface-color);
}

/* =================
   EMPTY STATES
   ================= */
.empty-state {
  text-align: center;
  padding: 64px 24px;
  color: var(--text-color-muted);
}

.empty-state-icon {
  font-size: 4rem;
  margin-bottom: 16px;
  color: var(--text-color-muted);
}

.empty-state-title {
  font-size: 1.25rem;
  font-weight: 500;
  margin-bottom: 8px;
  color: var(--text-color-secondary);
}

.empty-state-description {
  margin-bottom: 24px;
  line-height: 1.5;
}

/* =================
   PROGRESS INDICATORS
   ================= */
.progress-container {
  margin-bottom: 24px;
}

.progress-bar {
  width: 100%;
  height: 8px;
  background-color: var(--border-color);
  border-radius: 4px;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background-color: var(--primary-color);
  transition: width 0.3s ease;
}

.progress-steps {
  display: flex;
  justify-content: space-between;
  margin-bottom: 24px;
}

.progress-step {
  display: flex;
  flex-direction: column;
  align-items: center;
  flex: 1;
  position: relative;
}

.progress-step::after {
  content: '';
  position: absolute;
  top: 16px;
  left: 50%;
  width: 100%;
  height: 2px;
  background-color: var(--border-color);
  z-index: -1;
}

.progress-step:last-child::after {
  display: none;
}

.progress-step.completed::after {
  background-color: var(--primary-color);
}

.step-indicator {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  background-color: var(--border-color);
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 500;
  margin-bottom: 8px;
}

.progress-step.completed .step-indicator {
  background-color: var(--primary-color);
}

.progress-step.active .step-indicator {
  background-color: var(--primary-color);
  box-shadow: 0 0 0 4px rgba(var(--primary-rgb), 0.2);
}

/* =================
   TOOLTIPS
   ================= */
.tooltip-container {
  position: relative;
  display: inline-block;
}

.tooltip {
  position: absolute;
  bottom: 100%;
  left: 50%;
  transform: translateX(-50%);
  background-color: var(--tooltip-bg);
  color: var(--tooltip-color);
  padding: 8px 12px;
  border-radius: 4px;
  font-size: 0.875rem;
  white-space: nowrap;
  opacity: 0;
  visibility: hidden;
  transition: opacity 0.2s ease, visibility 0.2s ease;
  z-index: 1000;
  margin-bottom: 8px;
}

.tooltip::after {
  content: '';
  position: absolute;
  top: 100%;
  left: 50%;
  transform: translateX(-50%);
  border: 4px solid transparent;
  border-top-color: var(--tooltip-bg);
}

.tooltip-container:hover .tooltip {
  opacity: 1;
  visibility: visible;
}

/* =================
   FILE COVER PLACEHOLDERS
   ================= */
.file-cover-placeholder {
  margin-bottom: 16px;
}

.file-cover-card {
  background: linear-gradient(135deg, var(--primary-color) 0%, var(--primary-hover) 100%);
  color: white;
  margin-bottom: 16px;
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.file-info {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 20px;
}

.file-info .file-icon {
  font-size: 48px;
  width: 48px;
  height: 48px;
  opacity: 0.9;
}

.file-details h4 {
  margin: 0 0 8px 0;
  font-size: 1.25rem;
  font-weight: 500;
}

.file-details p {
  margin: 0 0 12px 0;
  opacity: 0.9;
  font-size: 0.95rem;
}

/* =================
   RESPONSIVE DESIGN
   ================= */
@media (max-width: 1024px) {
  .{{ '[FEATURE_NAME]' }}-container {
    padding: 20px;
  }
  
  .content-grid {
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  }
}

@media (max-width: 768px) {
  .{{ '[FEATURE_NAME]' }}-container {
    padding: 16px;
  }
  
  .page-title {
    font-size: 1.75rem;
  }
  
  .actions {
    flex-direction: column;
    align-items: stretch;
  }
  
  .content-grid {
    grid-template-columns: 1fr;
  }
  
  .filter-container {
    flex-direction: column;
  }
  
  .filter-select {
    min-width: unset;
  }
  
  .table-container {
    font-size: 0.875rem;
  }
  
  .modal-content {
    width: 95%;
    padding: 20px;
  }
  
  .progress-steps {
    font-size: 0.875rem;
  }
  
  .btn {
    padding: 12px 20px;
  }
  
  .file-info {
    flex-direction: column;
    text-align: center;
    padding: 16px;
  }
}

@media (max-width: 480px) {
  .{{ '[FEATURE_NAME]' }}-container {
    padding: 12px;
  }
  
  .page-title {
    font-size: 1.5rem;
  }
  
  .btn {
    padding: 10px 16px;
    font-size: 0.875rem;
  }
  
  .form-control {
    padding: 10px;
  }
  
  .table th,
  .table td {
    padding: 8px 12px;
  }
}

/* =================
   ACCESSIBILITY
   ================= */
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}

.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}

.skip-link {
  position: absolute;
  top: -40px;
  left: 6px;
  background-color: var(--primary-color);
  color: white;
  padding: 8px 16px;
  text-decoration: none;
  border-radius: 4px;
  z-index: 1000;
}

.skip-link:focus {
  top: 6px;
}

/* Focus indicators for keyboard navigation */
*:focus:not(:focus-visible) {
  outline: none;
}

*:focus-visible {
  outline: 2px solid var(--focus-color);
  outline-offset: 2px;
}

/* High contrast mode support */
@media (prefers-contrast: high) {
  .btn {
    border: 2px solid currentColor;
  }
  
  .form-control {
    border: 2px solid currentColor;
  }
  
  .item-card {
    border: 2px solid currentColor;
  }
}

/* =================
   CSS VARIABLES
   ================= */
:root {
  /* Primary colors */
  --primary-color: #1976d2;
  --primary-hover: #1565c0;
  --primary-rgb: 25, 118, 210;
  
  /* Secondary colors */
  --secondary-color: #f5f5f5;
  --secondary-hover: #eeeeee;
  
  /* Text colors */
  --text-color: #212121;
  --text-color-secondary: #757575;
  --text-color-muted: #9e9e9e;
  
  /* Surface colors */
  --surface-color: #ffffff;
  --surface-secondary: #fafafa;
  --background-color: #f5f5f5;
  
  /* Border colors */
  --border-color: #e0e0e0;
  --focus-color: #2196f3;
  
  /* State colors */
  --success-color: #4caf50;
  --success-bg: #e8f5e8;
  --success-border: #c8e6c9;
  --success-hover: #45a049;
  
  --warning-color: #ff9800;
  --warning-bg: #fff3e0;
  --warning-border: #ffcc02;
  
  --error-color: #f44336;
  --error-bg: #ffebee;
  --error-border: #ef9a9a;
  --error-hover: #d32f2f;
  
  /* Interactive states */
  --hover-bg: #f5f5f5;
  --disabled-bg: #f5f5f5;
  --disabled-color: #bdbdbd;
  
  /* Tooltip */
  --tooltip-bg: #424242;
  --tooltip-color: #ffffff;
  
  /* Shadows */
  --shadow-sm: 0 1px 3px rgba(0, 0, 0, 0.12);
  --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.12);
  --shadow-lg: 0 10px 25px rgba(0, 0, 0, 0.15);
}

/* Dark theme variables (optional) */
@media (prefers-color-scheme: dark) {
  :root {
    --primary-color: #90caf9;
    --primary-hover: #64b5f6;
    
    --text-color: #ffffff;
    --text-color-secondary: #b0b0b0;
    --text-color-muted: #808080;
    
    --surface-color: #121212;
    --surface-secondary: #1e1e1e;
    --background-color: #000000;
    
    --border-color: #404040;
    --hover-bg: #2c2c2c;
    
    --success-bg: #1b5e20;
    --warning-bg: #e65100;
    --error-bg: #b71c1c;
    
    --disabled-bg: #2c2c2c;
    --disabled-color: #606060;
  }
}

/* =================
   UTILITY CLASSES
   ================= */
.text-center { text-align: center; }
.text-left { text-align: left; }
.text-right { text-align: right; }

.d-flex { display: flex; }
.d-block { display: block; }
.d-inline { display: inline; }
.d-inline-block { display: inline-block; }
.d-none { display: none; }

.justify-center { justify-content: center; }
.justify-between { justify-content: space-between; }
.justify-end { justify-content: flex-end; }

.align-center { align-items: center; }
.align-start { align-items: flex-start; }
.align-end { align-items: flex-end; }

.flex-column { flex-direction: column; }
.flex-wrap { flex-wrap: wrap; }

.w-full { width: 100%; }
.h-full { height: 100%; }

.mb-0 { margin-bottom: 0; }
.mb-1 { margin-bottom: 8px; }
.mb-2 { margin-bottom: 16px; }
.mb-3 { margin-bottom: 24px; }
.mb-4 { margin-bottom: 32px; }

.mt-0 { margin-top: 0; }
.mt-1 { margin-top: 8px; }
.mt-2 { margin-top: 16px; }
.mt-3 { margin-top: 24px; }
.mt-4 { margin-top: 32px; }

.p-0 { padding: 0; }
.p-1 { padding: 8px; }
.p-2 { padding: 16px; }
.p-3 { padding: 24px; }
.p-4 { padding: 32px; }

.rounded { border-radius: 4px; }
.rounded-lg { border-radius: 8px; }
.rounded-full { border-radius: 50%; }

.shadow-sm { box-shadow: var(--shadow-sm); }
.shadow-md { box-shadow: var(--shadow-md); }
.shadow-lg { box-shadow: var(--shadow-lg); }
````

## 14. Workflow Automation Patterns

### Feature Generation Checklist

When creating a new feature, follow this systematic approach:

**1. Define the Domain Model**

```typescript
// Define your entities first
interface Template {
  id: string;
  name: string;
  description: string;
  areas: TemplateArea[];
  status: 'draft' | 'published' | 'archived';
  createdAt: Date;
  updatedAt: Date;
}
```

**2. Create the Service Layer**

```bash
# Generate service with CRUD operations
ng generate service features/[feature]/services/[entity]
```

**3. Build the Component Hierarchy**

```
features/template-management/
├── template-management.component.ts     # Main container
├── components/
│   ├── template-list/                   # List presentation
│   ├── template-card/                   # Item presentation
│   └── template-filters/                # Filter controls
└── pages/
    ├── edit-template/                   # Edit page
    └── publish-template/                # Publish page
```

**4. Implement Navigation Flow**

```typescript
// Define clear navigation paths
Management → Edit → Publish → Management
     ↑                           ↓
     ←────────── Cancel ←────────┘
```

### Common Component Patterns

**List + Detail Pattern**

```typescript
// 1. List Component (Container)
@Component({
  selector: 'app-template-list',
  template: `
    <app-template-filters (filterChange)="onFilterChange($event)" />
    
    @for (template of filteredTemplates(); track template.id) {
      <app-template-card 
        [template]="template"
        (edit)="onEdit($event)"
        (delete)="onDelete($event)"
      />
    }
  `
})

// 2. Card Component (Presentation)
@Component({
  selector: 'app-template-card',
  template: `
    <div class="card">
      <h3>{{ template().name }}</h3>
      <p>{{ template().description }}</p>
      <div class="actions">
        <button (click)="edit.emit(template().id)">Edit</button>
        <button (click)="delete.emit(template().id)">Delete</button>
      </div>
    </div>
  `
})
```

**Form Pattern**

```typescript
// 1. Form Container
@Component({
  selector: 'app-edit-template',
  template: `
    <form [formGroup]="templateForm" (ngSubmit)="onSubmit()">
      <app-template-form-fields [formGroup]="templateForm" />
      <app-form-actions 
        [canSave]="templateForm.valid"
        (save)="onSubmit()"
        (cancel)="onCancel()"
      />
    </form>
  `
})

// 2. Form Fields (Presentation)
@Component({
  selector: 'app-template-form-fields',
  template: `
    <div [formGroup]="formGroup()">
      <input formControlName="name" placeholder="Template Name" />
      <textarea formControlName="description" placeholder="Description"></textarea>
    </div>
  `
})
```

### State Management Patterns

**Local State (Simple)**

```typescript
export class TemplateListComponent {
  // Simple reactive state
  templates = signal<Template[]>([]);
  searchTerm = signal('');
  
  filteredTemplates = computed(() => 
    this.templates().filter(t => 
      t.name.toLowerCase().includes(this.searchTerm().toLowerCase())
    )
  );
}
```

**Feature State (Complex)**

```typescript
// Use NgRx SignalStore for complex state
export const TemplateStore = signalStore(
  { providedIn: 'root' },
  withState<TemplateState>({
    templates: [],
    selectedTemplate: null,
    loading: false,
    error: null
  }),
  withMethods((store) => ({
    loadTemplates: rxMethod<void>(
      pipe(
        tap(() => store.patchState({ loading: true })),
        switchMap(() => inject(TemplateService).getTemplates()),
        tapResponse({
          next: (templates) => store.patchState({ templates, loading: false }),
          error: (error) => store.patchState({ error: error.message, loading: false })
        })
      )
    )
  }))
);
```

### Error Handling Pattern

```typescript
// 1. Centralized error handling
@Injectable({ providedIn: 'root' })
export class ErrorHandlerService {
  private errorSubject = new Subject<AppError>();
  
  error$ = this.errorSubject.asObservable();
  
  handleError(error: unknown): AppError {
    const appError = this.mapToAppError(error);
    this.errorSubject.next(appError);
    return appError;
  }
  
  private mapToAppError(error: unknown): AppError {
    if (error instanceof HttpErrorResponse) {
      return {
        message: error.error?.message || 'Server error',
        code: error.status,
        type: 'server'
      };
    }
    return {
      message: 'An unexpected error occurred',
      code: 0,
      type: 'client'
    };
  }
}

// 2. Component-specific error handling
@Component({
  selector: 'app-template-detail',
  template: `
    <div *ngIf="errorMessage" class="error-message">
      {{ errorMessage }}
    </div>
    
    <!-- Component content -->
  `
})
export class TemplateDetailComponent {
  errorMessage = signal('');
  
  constructor(private errorHandler: ErrorHandlerService) { }
  
  ngOnInit() {
    this.loadData().catch(error => {
      this.errorMessage.set(this.errorHandler.handleError(error).message);
    });
  }
}
```

## 15. Padrões Avançados de Desenvolvimento

### Arquitetura Baseada em Features

Organize o código por domínio de negócio em vez de tipo técnico:

```plaintext
src/app/
├── core/
│   ├── guards/
│   ├── interceptors/
│   └── services/
├── shared/
│   ├── components/
│   ├── pipes/
│   └── directives/
└── features/
    ├── user-management/
    │   ├── components/
    │   ├── services/
    │   ├── models/
    │   └── user-management.module.ts
    └── template-management/
        ├── components/
        ├── services/
        ├── models/
        └── template-management.module.ts
```

### State Management com Signals

Para gerenciamento de estado local complexo:

```typescript
@Injectable()
export class TemplateStateService {
  // State signals
  private readonly _templates = signal<Template[]>([]);
  private readonly _selectedTemplate = signal<Template | null>(null);
  private readonly _loading = signal(false);
  private readonly _error = signal<string | null>(null);

  // Computed values
  readonly templates = this._templates.asReadonly();
  readonly selectedTemplate = this._selectedTemplate.asReadonly();
  readonly loading = this._loading.asReadonly();
  readonly error = this._error.asReadonly();

  readonly filteredTemplates = computed(() => {
    const templates = this._templates();
    const filter = this._filter();
    return templates.filter(t => t.name.includes(filter));
  });

  // Actions
  loadTemplates(): void {
    this._loading.set(true);
    this._error.set(null);
    
    this.templateService.getTemplates().subscribe({
      next: (templates) => {
        this._templates.set(templates);
        this._loading.set(false);
      },
      error: (error) => {
        this._error.set(error.message);
        this._loading.set(false);
      }
    });
  }

  selectTemplate(template: Template): void {
    this._selectedTemplate.set(template);
  }

  updateTemplate(updatedTemplate: Template): void {
    this._templates.update(templates => 
      templates.map(t => t.id === updatedTemplate.id ? updatedTemplate : t)
    );
  }
}
```

### Reactive Forms com Validação Avançada

```typescript
export class AdvancedFormComponent {
  private readonly fb = inject(FormBuilder);
  
  // Definir o form com tipagem forte
  form = this.fb.group({
    basicInfo: this.fb.group({
      name: ['', [Validators.required, Validators.minLength(3)]],
      description: ['', Validators.maxLength(500)]
    }),
    configuration: this.fb.group({
      type: ['', Validators.required],
      settings: this.fb.array([])
    })
  });

  // Getters tipados
  get basicInfo() {
    return this.form.get('basicInfo') as FormGroup;
  }

  get configuration() {
    return this.form.get('configuration') as FormGroup;
  }

  get settings() {
    return this.configuration.get('settings') as FormArray;
  }

  // Validadores customizados
  static uniqueNameValidator(control: AbstractControl): ValidationErrors | null {
    // Implementar validação assíncrona se necessário
    return null;
  }

  // Manipulação de arrays dinâmicos
  addSetting(): void {
    const settingGroup = this.fb.group({
      key: ['', Validators.required],
      value: ['', Validators.required],
      type: ['string', Validators.required]
    });
    
    this.settings.push(settingGroup);
  }

  removeSetting(index: number): void {
    this.settings.removeAt(index);
  }

  // Submit com tratamento de erros
  onSubmit(): void {
    if (this.form.valid) {
      const formValue = this.form.getRawValue();
      this.saveData(formValue);
    } else {
      this.markAllFieldsAsTouched();
      this.scrollToFirstError();
    }
  }

  private markAllFieldsAsTouched(): void {
    Object.keys(this.form.controls).forEach(key => {
      const control = this.form.get(key);
      control?.markAsTouched();
      
      if (control instanceof FormGroup) {
        this.markGroupAsTouched(control);
      }
    });
  }

  private markGroupAsTouched(group: FormGroup): void {
    Object.keys(group.controls).forEach(key => {
      const control = group.get(key);
      control?.markAsTouched();
      
      if (control instanceof FormGroup) {
        this.markGroupAsTouched(control);
      } else if (control instanceof FormArray) {
        control.controls.forEach(c => {
          if (c instanceof FormGroup) {
            this.markGroupAsTouched(c);
          } else {
            c.markAsTouched();
          }
        });
      }
    });
  }

  private scrollToFirstError(): void {
    const firstError = document.querySelector('.ng-invalid');
    firstError?.scrollIntoView({ behavior: 'smooth', block: 'center' });
  }
}
```

### Performance Optimization Patterns

```typescript
@Component({
  selector: 'app-optimized-list',
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <div class="search-container">
      <input 
        #searchInput
        [value]="searchTerm()"
        (input)="updateSearch($event)"
        placeholder="Buscar..."
      />
    </div>

    <div class="list-container">
      @for (item of visibleItems(); track item.id) {
        <app-list-item 
          [item]="item"
          (itemClick)="onItemClick($event)"
        />
      }
    </div>

    <app-virtual-scroll
      [items]="filteredItems()"
      [itemHeight]="80"
      (visibleRangeChange)="updateVisibleRange($event)"
    />
  `
})
export class OptimizedListComponent {
  private readonly destroyRef = inject(DestroyRef);
  
  // Inputs como signals
  readonly items = input.required<ListItem[]>();
  readonly searchTerm = signal('');
  readonly visibleRange = signal({ start: 0, end: 50 });

  // Computed values com memoização
  readonly filteredItems = computed(() => {
    const items = this.items();
    const term = this.searchTerm().toLowerCase();
    
    if (!term) return items;
    
    return items.filter(item => 
      item.name.toLowerCase().includes(term) ||
      item.description.toLowerCase().includes(term)
    );
  });

  readonly visibleItems = computed(() => {
    const items = this.filteredItems();
    const range = this.visibleRange();
    return items.slice(range.start, range.end);
  });

  // Debounced search
  updateSearch(event: Event): void {
    const value = (event.target as HTMLInputElement).value;
    
    // Debounce a busca para evitar muitas chamadas
    setTimeout(() => {
      this.searchTerm.set(value);
    }, 300);
  }

  updateVisibleRange(range: { start: number; end: number }): void {
    this.visibleRange.set(range);
  }

  onItemClick(item: ListItem): void {
    // Usar signal para comunicação com componente pai
    this.itemSelected.emit(item);
  }

  @Output() itemSelected = new EventEmitter<ListItem>();
}
```

### Testing Patterns Avançados

```typescript
describe('TemplateManagementComponent', () => {
  let component: TemplateManagementComponent;
  let fixture: ComponentFixture<TemplateManagementComponent>;
  let mockTemplateService: jasmine.SpyObj<TemplateService>;
  let mockNotificationService: jasmine.SpyObj<NotificationService>;

  beforeEach(async () => {
    // Criar spies para services
    const templateServiceSpy = jasmine.createSpyObj('TemplateService', [
      'getTemplates', 'createTemplate', 'updateTemplate', 'deleteTemplate'
    ]);
    
    const notificationServiceSpy = jasmine.createSpyObj('NotificationService', [
      'showSuccess', 'showError'
    ]);

    await TestBed.configureTestingModule({
      imports: [TemplateManagementComponent],
      providers: [
        { provide: TemplateService, useValue: templateServiceSpy },
        { provide: NotificationService, useValue: notificationServiceSpy },
        provideRouter([]),
        provideHttpClientTesting()
      ]
    }).compileComponents();

    fixture = TestBed.createComponent(TemplateManagementComponent);
    component = fixture.componentInstance;
    mockTemplateService = TestBed.inject(TemplateService) as jasmine.SpyObj<TemplateService>;
    mockNotificationService = TestBed.inject(NotificationService) as jasmine.SpyObj<NotificationService>;
  });

  describe('Component Initialization', () => {
    it('should load templates on init', fakeAsync(() => {
      // Arrange
      const mockTemplates = [
        { id: '1', name: 'Template 1', description: 'Test template' },
        { id: '2', name: 'Template 2', description: 'Another template' }
      ];
      mockTemplateService.getTemplates.and.returnValue(of(mockTemplates));

      // Act
      component.ngOnInit();
      tick();
      fixture.detectChanges();

      // Assert
      expect(mockTemplateService.getTemplates).toHaveBeenCalled();
      expect(component.templates()).toEqual(mockTemplates);
      expect(component.loading()).toBeFalse();
    }));

    it('should handle loading error', fakeAsync(() => {
      // Arrange
      const errorMessage = 'Failed to load templates';
      mockTemplateService.getTemplates.and.returnValue(
        throwError(() => new Error(errorMessage))
      );

      // Act
      component.ngOnInit();
      tick();
      fixture.detectChanges();

      // Assert
      expect(component.error()).toBe(errorMessage);
      expect(component.loading()).toBeFalse();
    }));
  });

  describe('Template Operations', () => {
    it('should create template successfully', fakeAsync(() => {
      // Arrange
      const newTemplate = { name: 'New Template', description: 'Description' };
      const createdTemplate = { id: '3', ...newTemplate };
      mockTemplateService.createTemplate.and.returnValue(of(createdTemplate));

      // Act
      component.createTemplate(newTemplate);
      tick();

      // Assert
      expect(mockTemplateService.createTemplate).toHaveBeenCalledWith(newTemplate);
      expect(mockNotificationService.showSuccess).toHaveBeenCalledWith('Template criado com sucesso');
    }));

    it('should handle template creation error', fakeAsync(() => {
      // Arrange
      const newTemplate = { name: 'New Template', description: 'Description' };
      const errorMessage = 'Failed to create template';
      mockTemplateService.createTemplate.and.returnValue(
        throwError(() => new Error(errorMessage))
      );

      // Act
      component.createTemplate(newTemplate);
      tick();

      // Assert
      expect(mockNotificationService.showError).toHaveBeenCalledWith(
        'Erro ao criar template: ' + errorMessage
      );
    }));
  });

  describe('User Interactions', () => {
    it('should filter templates when search term changes', () => {
      // Arrange
      const templates = [
        { id: '1', name: 'Angular Template', description: 'For Angular projects' },
        { id: '2', name: 'React Template', description: 'For React projects' }
      ];
      component.templates.set(templates);

      // Act
      component.searchTerm.set('Angular');
      fixture.detectChanges();

      // Assert
      const filteredTemplates = component.filteredTemplates();
      expect(filteredTemplates).toHaveLength(1);
      expect(filteredTemplates[0].name).toBe('Angular Template');
    });

    it('should emit template selection', () => {
      // Arrange
      spyOn(component.templateSelected, 'emit');
      const template = { id: '1', name: 'Test Template', description: 'Test' };

      // Act
      component.selectTemplate(template);

      // Assert
      expect(component.templateSelected.emit).toHaveBeenCalledWith(template);
      expect(component.selectedTemplate()).toBe(template);
    });
  });

  describe('Integration Tests', () => {
    it('should handle complete workflow', fakeAsync(() => {
      // Arrange
      const initialTemplates = [
        { id: '1', name: 'Template 1', description: 'Test template' }
      ];
      const newTemplate = { name: 'New Template', description: 'New description' };
      const createdTemplate = { id: '2', ...newTemplate };

      mockTemplateService.getTemplates.and.returnValue(of(initialTemplates));
      mockTemplateService.createTemplate.and.returnValue(of(createdTemplate));

      // Act - Load initial data
      component.ngOnInit();
      tick();
      fixture.detectChanges();

      // Assert - Initial state
      expect(component.templates()).toEqual(initialTemplates);

      // Act - Create new template
      component.createTemplate(newTemplate);
      tick();

      // Assert - Template added
      expect(component.templates()).toContain(createdTemplate);
      expect(mockNotificationService.showSuccess).toHaveBeenCalled();
    }));
  });
});
```

### Error Handling Global

```typescript
@Injectable()
export class GlobalErrorHandler implements ErrorHandler {
  private readonly notificationService = inject(NotificationService);
  private readonly router = inject(Router);

  handleError(error: any): void {
    console.error('Global error:', error);

    // Categorizar tipos de erro
    if (error instanceof HttpErrorResponse) {
      this.handleHttpError(error);
    } else if (error instanceof TypeError) {
      this.handleTypeError(error);
    } else {
      this.handleGenericError(error);
    }
  }

  private handleHttpError(error: HttpErrorResponse): void {
    switch (error.status) {
      case 401:
        this.notificationService.showError('Sessão expirada. Faça login novamente.');
        this.router.navigate(['/login']);
        break;
      case 403:
        this.notificationService.showError('Acesso negado.');
        break;
      case 404:
        this.notificationService.showError('Recurso não encontrado.');
        break;
      case 500:
        this.notificationService.showError('Erro interno do servidor.');
        break;
      default:
        this.notificationService.showError('Erro de comunicação com o servidor.');
    }
  }

  private handleTypeError(error: TypeError): void {
    this.notificationService.showError('Erro de aplicação. Tente recarregar a página.');
  }

  private handleGenericError(error: any): void {
    this.notificationService.showError('Ocorreu um erro inesperado.');
  }
}

// Registrar no app.config.ts
export const appConfig: ApplicationConfig = {
  providers: [
    // ... outros providers
    { provide: ErrorHandler, useClass: GlobalErrorHandler }
  ]
};
```

### Deployment e Build Optimization

```typescript
// angular.json - Configurações de produção otimizadas
{
  "build": {
    "builder": "@angular-devkit/build-angular:browser",
    "options": {
      "outputPath": "dist/app",
      "index": "src/index.html",
      "main": "src/main.ts",
      "polyfills": "src/polyfills.ts",
      "tsConfig": "tsconfig.app.json",
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
        },
        {
          "type": "anyComponentStyle",
          "maximumWarning": "6kb",
          "maximumError": "10kb"
        }
      ]
    }
  }
}
```

### Environment Configuration

```typescript
// environment.interface.ts
export interface Environment {
  production: boolean;
  apiUrl: string;
  features: {
    enableDebug: boolean;
    enableAnalytics: boolean;
    enableMockData: boolean;
  };
  auth: {
    clientId: string;
    authority: string;
  };
}

// environment.ts
export const environment: Environment = {
  production: false,
  apiUrl: 'http://localhost:3000/api',
  features: {
    enableDebug: true,
    enableAnalytics: false,
    enableMockData: true
  },
  auth: {
    clientId: 'dev-client-id',
    authority: 'http://localhost:4200'
  }
};

// environment.prod.ts
export const environment: Environment = {
  production: true,
  apiUrl: 'https://api.production.com',
  features: {
    enableDebug: false,
    enableAnalytics: true,
    enableMockData: false
  },
  auth: {
    clientId: 'prod-client-id',
    authority: 'https://auth.production.com'
  }
};
```

`````markdown
## 16. Recursos e Referências

### Documentação Oficial

- **Angular Docs**: https://angular.io/docs
- **Angular CLI**: https://angular.io/cli
- **Angular Material**: https://material.angular.io/
- **RxJS**: https://rxjs.dev/
- **TypeScript**: https://www.typescriptlang.org/docs/

### Ferramentas de Desenvolvimento

**Extensões VS Code Essenciais:**
- Angular Language Service
- Angular Snippets
- Prettier - Code formatter
- ESLint
- GitLens
- Auto Rename Tag
- Bracket Pair Colorizer
- Thunder Client (para testes de API)

**Ferramentas de Build e Deploy:**
- Angular CLI
- Webpack Bundle Analyzer
- Source Map Explorer
- Lighthouse (performance)
- Chrome DevTools

### Libraries Recomendadas

**UI/UX:**
- Angular Material
- ng-bootstrap
- PrimeNG
- Taiga UI

**Utilitários:**
- Lodash
- date-fns
- uuid
- validator.js

**Testing:**
- Jasmine
- Karma
- Protractor (deprecated - usar Cypress ou Playwright)
- Testing Library Angular
- Spectator

**Performance:**
- Angular Service Worker
- ngx-loading
- ngx-skeleton-loader
- virtual-scrolling

### Padrões de Commit

Use Conventional Commits para padronizar mensagens:

```bash
# Tipos de commit
feat: nova funcionalidade
fix: correção de bug
docs: mudanças na documentação
style: formatação, ponto e vírgula, etc
refactor: refatoração de código
test: adição ou correção de testes
chore: mudanças no build, configs, etc

# Exemplos
feat(template-management): adicionar filtro por categoria
fix(auth): corrigir redirecionamento após login
docs(readme): atualizar instruções de instalação
refactor(services): simplificar template service
test(components): adicionar testes para template list
chore(deps): atualizar angular para v17
```

### Scripts de Automação

```json
{
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "build:prod": "ng build --configuration production",
    "test": "ng test",
    "test:headless": "ng test --watch=false --browsers=ChromeHeadless",
    "test:coverage": "ng test --code-coverage --watch=false --browsers=ChromeHeadless",
    "lint": "ng lint",
    "lint:fix": "ng lint --fix",
    "e2e": "ng e2",
    "format": "prettier --write \"src/**/*.{ts,html,scss}\"",
    "format:check": "prettier --check \"src/**/*.{ts,html,scss}\"",
    "analyze": "ng build --stats-json && npx webpack-bundle-analyzer dist/stats.json",
    "precommit": "npm run lint && npm run test:headless",
    "release": "npm run build:prod && npm run test:coverage"
  }
}
```

### Performance Checklist

**Build Time:**
- [ ] OnPush change detection onde possível
- [ ] Lazy loading de módulos/rotas
- [ ] Tree shaking configurado
- [ ] Bundle analyzer executado
- [ ] Chunk splitting otimizado

**Runtime:**
- [ ] Trackby functions em *ngFor
- [ ] Async pipe para subscriptions
- [ ] Signals para state management
- [ ] Virtual scrolling para listas grandes
- [ ] Images com lazy loading

**Network:**
- [ ] HTTP interceptors para cache
- [ ] Service worker configurado
- [ ] Gzip/Brotli compression
- [ ] CDN para assets estáticos
- [ ] API calls otimizadas

### Security Checklist

**XSS Prevention:**
- [ ] Sanitização de inputs do usuário
- [ ] Binding seguro no template
- [ ] CSP headers configurados
- [ ] innerHTML evitado

**Authentication:**
- [ ] JWT tokens seguros
- [ ] Refresh token implementado
- [ ] Route guards configurados
- [ ] Session timeout

**HTTPS:**
- [ ] SSL certificates válidos
- [ ] HSTS headers
- [ ] Secure cookies
- [ ] Mixed content evitado

### Troubleshooting Common Issues

**Build Errors:**
```bash
# Limpar cache
npm ci
ng build --delete-output-path

# Verificar versões
ng version
npm list --depth=0

# Debug build
ng build --verbose
```

**Runtime Errors:**
```typescript
// Debug change detection
import { ChangeDetectorRef } from '@angular/core';

ngAfterViewInit() {
  console.log('Change detection triggered');
  this.cdr.detectChanges();
}

// Debug services
constructor() {
  console.log('Service instantiated:', this.constructor.name);
}
```

**Performance Issues:**
```bash
# Analyze bundle
ng build --stats-json
npx webpack-bundle-analyzer dist/stats.json

# Profile runtime
ng serve --profiling
```

### Recursos de Aprendizado

**Cursos Online:**
- Angular University
- Pluralsight Angular Path
- Udemy Angular Courses
- Angular.io Tutorial

**Blogs e Websites:**
- Angular Blog (blog.angular.io)
- Angular In Depth
- Ninja Squad Blog
- This is Angular

**Podcasts:**
- Adventures in Angular
- Angular Air
- ng-conf Podcasts

**Conferências:**
- ng-conf
- AngularConnect
- Angular Mix
- ng-vikings

### Code Review Checklist

**Estrutura:**
- [ ] Código organizado por features
- [ ] Naming conventions seguidos
- [ ] Interfaces/tipos definidos
- [ ] Imports organizados

**Performance:**
- [ ] OnPush usado quando possível
- [ ] Subscriptions gerenciadas
- [ ] Memory leaks verificados
- [ ] Bundle size considerado

**Qualidade:**
- [ ] Testes adicionados/atualizados
- [ ] Error handling implementado
- [ ] Accessibility considerada
- [ ] Documentation atualizada

**Security:**
- [ ] Inputs sanitizados
- [ ] Permissions verificadas
- [ ] Sensitive data protegida
- [ ] OWASP guidelines seguidas

---

## Conclusão

Este guideline representa as melhores práticas acumuladas para desenvolvimento Angular, baseadas em experiências reais de projeto e lições aprendidas durante a resolução de problemas de build. 

**Principais benefícios de seguir este guideline:**

1. **Redução de Bugs**: Padrões consistentes e validação rigorosa
2. **Melhor Manutenibilidade**: Código organizado e bem documentado
3. **Performance Otimizada**: Práticas de otimização desde o desenvolvimento
4. **Desenvolvimento Eficiente**: Templates e checklist aceleram criação de features
5. **Qualidade Assegurada**: Testes e code review estruturados

**Lembre-se:**
- Mantenha este documento atualizado com novas descobertas
- Adapte os padrões conforme a evolução do Angular
- Compartilhe conhecimento com a equipe
- Revisite periodicamente as decisões arquiteturais

Para dúvidas ou sugestões de melhorias, consulte a equipe de desenvolvimento Angular ou abra uma discussão no repositório do projeto.

---

*Última atualização: [DATA_ATUAL]*
*Versão Angular: 17+*
*Status: Ativo e em evolução contínua*
