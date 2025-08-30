# GitHub Issue: Enhance UnknownDependenciesException to detect and provide specific guidance for `import type` issues

**Title:** Enhance UnknownDependenciesException to detect and provide specific guidance for `import type` issues

## 🐛 Bug Report / 🚀 Feature Request

### Current behavior

When a developer accidentally uses `import type` instead of `import` for a service that needs to be injected, the error message is generic and doesn't hint at the root cause:

```
[Nest] 52937  - 08/29/2025, 6:33:11 PM   ERROR [ExceptionHandler] UnknownDependenciesException [Error]: Nest can't resolve dependencies of the ResourceController (ResourceService, ?). Please make sure that the argument Object at index [1] is available in the ResourceModule context.

Potential solutions:
- Is ResourceModule a valid NestJS module?
- If Object is a provider, is it part of the current ResourceModule?
- If Object is exported from a separate @Module, is that module imported within ResourceModule?
  @Module({
    imports: [ /* the Module containing Object */ ]
  })
```

### Expected behavior

The error message should detect this common TypeScript mistake and provide specific guidance:

```
[Nest] 52937  - 08/29/2025, 6:33:11 PM   ERROR [ExceptionHandler] UnknownDependenciesException [Error]: Nest can't resolve dependencies of the ResourceController (ResourceService, ?). Please make sure that the argument dependency at index [1] is available in the ResourceModule context.

Potential solutions:
- The dependency at index [1] appears to be undefined at runtime
- This commonly occurs when using 'import type' instead of 'import' for a class that needs to be injected
- Check your imports: change 'import type { SomeService }' to 'import { SomeService }'
- Verify the dependency is properly imported for runtime use, not just for typing
```

### Minimal reproduction of the problem

```typescript
// Wrong - causes the error
import type { SomeService } from './some.service';

@Controller()
export class ResourceController {
  constructor(
    private resourceService: ResourceService,
    private someService: SomeService, // undefined at runtime
  ) {}
}
```

**Correct approach:**
```typescript
// Correct - runtime import
import { SomeService } from './some.service';

@Controller()
export class ResourceController {
  constructor(
    private resourceService: ResourceService,
    private someService: SomeService, // properly injected
  ) {}
}
```

### What is the motivation / use case for changing the behavior?

- **Extremely common mistake**: This is one of the most frequent issues developers encounter, especially when:
  - Transitioning to NestJS from other frameworks
  - Working with strict TypeScript configurations
  - Using IDE auto-import features that sometimes default to `import type`

- **Poor developer experience**: The current error message provides no hint about the `import type` vs `import` distinction, leading to:
  - Hours of debugging time wasted
  - Developers questioning their module setup when the issue is just an import statement
  - Increased support burden on forums/Discord

- **Precise detection possible**: The error pattern is very specific and detectable:
  - `name === undefined` (TypeScript erases `import type` at runtime)
  - `index !== undefined` (parameter position is known)
  - `dependencies[index] === undefined` (dependency becomes undefined)

- **Targeted solution**: Instead of generic module advice, provide specific actionable guidance that immediately points to the root cause.

### Environment

- **NestJS version**: All versions affected
- **Related to**: TypeScript compilation behavior and dependency injection system
- **Impact**: High - affects many developers, especially newcomers

### Additional Context

This enhancement would:
1. **Preserve existing functionality** - only shows new message when the specific pattern is detected
2. **Improve developer experience** significantly by providing targeted guidance
3. **Reduce support burden** by helping developers self-diagnose this common issue
4. **Maintain backward compatibility** - no breaking changes

The detection logic is precise and won't interfere with other legitimate dependency resolution errors.