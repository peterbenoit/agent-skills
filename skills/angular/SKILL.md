---
name: angular
description: Builds and maintains Angular applications. Use when working with Angular components, services, routing, reactive forms, RxJS, signals, or the Angular CLI. Use when the user mentions Angular, NgModule, standalone components, Angular Material, or ng commands.
---

# Angular

## Overview

Angular is an opinionated, full-featured framework with strong conventions around dependency injection, TypeScript, RxJS, and the CLI. Work with the framework's patterns — deviation from them creates code that other Angular developers cannot maintain.

**Current stable version:** Angular 18+  
**Docs:** https://angular.dev (v17+); https://angular.io for older versions  
**CLI:** `ng`

## When to Use

- Building or maintaining Angular applications
- Writing components, services, pipes, or directives
- Setting up routing and lazy loading
- Working with Angular Forms (reactive or template-driven)
- Integrating with an API via `HttpClient`
- Angular-specific performance work (OnPush, signals, lazy loading)

## Project Conventions

Angular 17+ defaults to **standalone components** — no NgModule required. Prefer this pattern for new code.

```bash
ng new my-app --standalone --style=scss --routing
```

## Components

### Standalone component (preferred, Angular 17+)

```ts
import { Component, input, output } from '@angular/core';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-user-card',
  standalone: true,
  imports: [CommonModule],
  template: `
    <div class="card">
      <h2>{{ user().name }}</h2>
      <button (click)="select.emit(user())">Select</button>
    </div>
  `,
  styleUrl: './user-card.component.scss',
})
export class UserCardComponent {
  user = input.required<User>();
  select = output<User>();
}
```

### Change detection

Default change detection checks every component on every event. Use `OnPush` for performance in all components that receive data via inputs:

```ts
import { ChangeDetectionStrategy } from '@angular/core';

@Component({
  changeDetection: ChangeDetectionStrategy.OnPush,
  // ...
})
```

With `OnPush`, the component only re-renders when:
- An `@Input()` reference changes
- An `async`-piped Observable emits
- `markForCheck()` is called explicitly
- A signal it reads changes

## Signals (Angular 16+)

Prefer signals over `BehaviorSubject` for local and shared state in new code:

```ts
import { signal, computed, effect } from '@angular/core';

// In a service
const count = signal(0);
const doubled = computed(() => count() * 2);

// Update
count.set(1);
count.update(n => n + 1);

// Read (in a template or component)
{{ count() }}

// Side effect
effect(() => {
  console.log('Count changed:', count());
});
```

## Services and Dependency Injection

Provide at root unless component-scoped:

```ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class UserService {
  private readonly apiUrl = '/api/users';

  constructor(private http: HttpClient) {}

  getUsers(): Observable<User[]> {
    return this.http.get<User[]>(this.apiUrl);
  }

  getUser(id: string): Observable<User> {
    return this.http.get<User>(`${this.apiUrl}/${id}`);
  }
}
```

## Routing

```ts
// app.routes.ts
import { Routes } from '@angular/router';
import { authGuard } from './guards/auth.guard';

export const routes: Routes = [
  { path: '', component: HomeComponent },
  {
    path: 'dashboard',
    canActivate: [authGuard],
    // Lazy-load feature module
    loadChildren: () => import('./features/dashboard/dashboard.routes').then(m => m.DASHBOARD_ROUTES),
  },
  { path: '**', redirectTo: '' },
];
```

```ts
// Functional guard (Angular 15+)
import { inject } from '@angular/core';
import { CanActivateFn, Router } from '@angular/router';

export const authGuard: CanActivateFn = () => {
  const authService = inject(AuthService);
  const router = inject(Router);
  return authService.isAuthenticated() || router.createUrlTree(['/login']);
};
```

## Reactive Forms

```ts
import { Component, inject } from '@angular/core';
import { FormBuilder, Validators, ReactiveFormsModule } from '@angular/forms';

@Component({
  standalone: true,
  imports: [ReactiveFormsModule],
  template: `
    <form [formGroup]="form" (ngSubmit)="onSubmit()">
      <input formControlName="email" type="email">
      @if (form.controls.email.hasError('required') && form.controls.email.touched) {
        <span>Email is required</span>
      }
      <button type="submit" [disabled]="form.invalid">Submit</button>
    </form>
  `,
})
export class LoginFormComponent {
  private fb = inject(FormBuilder);

  form = this.fb.group({
    email: ['', [Validators.required, Validators.email]],
    password: ['', [Validators.required, Validators.minLength(8)]],
  });

  onSubmit() {
    if (this.form.valid) {
      console.log(this.form.getRawValue());
    }
  }
}
```

## RxJS Patterns

```ts
import { takeUntilDestroyed } from '@angular/core/rxjs-interop';
import { DestroyRef, inject } from '@angular/core';

// Prefer takeUntilDestroyed over manual unsubscribe (Angular 16+)
this.service.data$
  .pipe(takeUntilDestroyed())
  .subscribe(data => { ... });

// Common operators
import { switchMap, catchError, debounceTime, distinctUntilChanged } from 'rxjs/operators';
import { EMPTY } from 'rxjs';

searchInput.valueChanges.pipe(
  debounceTime(300),
  distinctUntilChanged(),
  switchMap(term => this.searchService.search(term).pipe(
    catchError(() => EMPTY),
  )),
).subscribe(results => { ... });
```

Use `switchMap` for search/autocomplete (cancels previous request).  
Use `mergeMap` when all concurrent requests should complete.  
Use `concatMap` when order matters.  
Use `exhaustMap` for login/submit (ignores new events until current completes).

## HTTP Error Handling

Use an HTTP interceptor for global error handling:

```ts
import { HttpInterceptorFn } from '@angular/common/http';
import { catchError, throwError } from 'rxjs';

export const errorInterceptor: HttpInterceptorFn = (req, next) =>
  next(req).pipe(
    catchError(err => {
      if (err.status === 401) {
        // redirect to login
      }
      return throwError(() => err);
    }),
  );

// Register in app.config.ts
provideHttpClient(withInterceptors([errorInterceptor]))
```

## CLI Commands

```bash
ng generate component features/user/user-list --standalone
ng generate service core/services/user
ng generate guard core/guards/auth --functional
ng generate pipe shared/pipes/truncate

ng build --configuration=production
ng test --watch=false --code-coverage
ng lint
```

## Performance Checklist

- [ ] All components use `OnPush` change detection
- [ ] Feature routes are lazy-loaded
- [ ] Large lists use `@for` with `track` expression (replaces `*ngFor trackBy`)
- [ ] Images have `width`/`height` and use `NgOptimizedImage`
- [ ] `takeUntilDestroyed()` or `async` pipe used — no unmanaged subscriptions
- [ ] Bundle analyzed with `ng build --stats-json` + webpack-bundle-analyzer

## Common Mistakes

| Mistake | Fix |
|---|---|
| No `OnPush` on leaf components | Add `ChangeDetectionStrategy.OnPush` |
| `subscribe()` without unsubscribing | Use `async` pipe or `takeUntilDestroyed()` |
| `*ngFor` without `trackBy` / `track` | Always track by unique id |
| Importing entire `FormsModule` for one field | Use `ReactiveFormsModule` or standalone imports |
| Logic in templates | Move to component methods or computed signals |
| Service state as plain properties | Use signals or `BehaviorSubject` |
