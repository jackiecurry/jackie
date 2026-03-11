# UI Engineering Patterns

React, TypeScript, and component architecture patterns using the shadcn/ui approach. Read when building, scaffolding, or reviewing UI implementation.

This file covers the *how*. For the *what* — component specs, token scales, and visual standards — read `component-library.md` and `design-tokens.md`.

---

## Stack Conventions

**Core:**
- React 18+ with TypeScript
- Tailwind CSS for styling
- CSS variables for theming (maps to design token scale)
- Radix UI primitives for interactive components
- CVA (Class Variance Authority) for variant management
- `cn()` utility (clsx + tailwind-merge) for conditional classes

**Install:**
```bash
npm install tailwindcss class-variance-authority clsx tailwind-merge
npm install @radix-ui/react-[primitive] # as needed per component
```

**The `cn()` utility — required in every project:**
```ts
import { clsx, type ClassValue } from 'clsx'
import { twMerge } from 'tailwind-merge'

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```

Use `cn()` everywhere class names are assembled. Never concatenate Tailwind strings manually — merge conflicts are silent and produce wrong output.

---

## Component Anatomy

Every component follows the same file structure:

```
/components/ui/
├── button.tsx          ← component + variants
├── input.tsx
├── card.tsx
└── index.ts            ← barrel export
```

**Barrel export pattern:**
```ts
// components/ui/index.ts
export { Button, type ButtonProps } from './button'
export { Input, type InputProps } from './input'
export { Card, CardHeader, CardContent, CardFooter } from './card'
```

Always co-export the Props type. Consumers should not have to import from the component file directly to get the type.

---

## TypeScript Patterns

### Props interface conventions

```ts
// Always extend HTML element props for native attribute passthrough
interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'default' | 'destructive' | 'outline' | 'ghost' | 'link'
  size?: 'sm' | 'md' | 'lg' | 'icon'
  isLoading?: boolean
}
```

Rules:
- Extend the native HTML element interface (`ButtonHTMLAttributes`, `InputHTMLAttributes`, etc.)
- Use `?` for all optional props — required props should be minimal
- Name boolean props `is*` or `has*` (`isLoading`, `isDisabled`, `hasError`)
- Never use `any`. Use `unknown` when the type is genuinely unknown, then narrow it.

### Discriminated unions for state variants

When a component has mutually exclusive states, use a discriminated union rather than a loose combination of optional booleans:

```ts
// Avoid — allows impossible states (isLoading + isError = true)
interface BadProps {
  isLoading?: boolean
  isError?: boolean
  errorMessage?: string
}

// Prefer — impossible states are impossible
type InputState =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'error'; message: string }
  | { status: 'success' }

interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  state?: InputState
}
```

### Generic components

Use generics when a component wraps arbitrary data:

```ts
interface SelectProps<T> {
  options: T[]
  value: T | null
  onChange: (value: T) => void
  getLabel: (option: T) => string
  getValue: (option: T) => string | number
}

function Select<T>({ options, value, onChange, getLabel, getValue }: SelectProps<T>) {
  // ...
}
```

### forwardRef — required for all leaf components

Any component that renders a single native element must forward its ref:

```ts
const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant = 'default', size = 'md', isLoading, children, ...props }, ref) => {
    return (
      <button
        ref={ref}
        className={cn(buttonVariants({ variant, size }), className)}
        disabled={isLoading || props.disabled}
        {...props}
      >
        {isLoading ? <Spinner /> : children}
      </button>
    )
  }
)
Button.displayName = 'Button'
```

Rules:
- Always set `displayName` — required for React DevTools and error messages
- Spread `...props` after explicit props so callers can override `data-*`, `aria-*`, event handlers
- `className` should be mergeable — always accept it and pass it through `cn()`

---

## CVA — Class Variance Authority

CVA is how variants are defined. It replaces manual string conditionals and makes variant logic readable and type-safe.

### Basic pattern

```ts
import { cva, type VariantProps } from 'class-variance-authority'

const buttonVariants = cva(
  // Base classes — always applied
  'inline-flex items-center justify-center rounded-md font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50',
  {
    variants: {
      variant: {
        default:     'bg-primary text-primary-foreground hover:bg-primary/90',
        destructive: 'bg-destructive text-destructive-foreground hover:bg-destructive/90',
        outline:     'border border-input bg-background hover:bg-accent hover:text-accent-foreground',
        ghost:       'hover:bg-accent hover:text-accent-foreground',
        link:        'text-primary underline-offset-4 hover:underline',
      },
      size: {
        sm:   'h-8 px-3 text-xs',
        md:   'h-10 px-4 py-2',
        lg:   'h-11 px-8',
        icon: 'h-10 w-10',
      },
    },
    defaultVariants: {
      variant: 'default',
      size: 'md',
    },
  }
)

// Extract the variant prop types for use in the component interface
interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {}
```

**Rules:**
- Base classes go in the first argument — these apply unconditionally
- One CVA definition per component — do not split variants across multiple CVA calls
- `VariantProps<typeof buttonVariants>` gives you the typed variant props for free — don't redefine them manually
- `defaultVariants` must be set — never leave a variant without a default

### Compound variants

Use `compoundVariants` when a combination of two variants produces a unique style:

```ts
const badgeVariants = cva('inline-flex items-center rounded-full px-2.5 py-0.5 text-xs font-semibold', {
  variants: {
    variant: { default: 'bg-primary', outline: 'border border-current' },
    size: { sm: 'text-xs', lg: 'text-sm' },
  },
  compoundVariants: [
    {
      variant: 'outline',
      size: 'lg',
      class: 'px-4 py-1', // larger padding only for outline + large
    },
  ],
})
```

---

## Radix UI Primitives

Use Radix for any component with complex interactive behavior: dialogs, dropdowns, tooltips, popovers, selects, checkboxes, radio groups, tabs, accordions, sliders.

Do not build these from scratch. Radix handles:
- ARIA roles and attributes
- Keyboard navigation
- Focus management and focus trapping
- Screen reader announcements
- Controlled and uncontrolled state

### Anatomy pattern

Radix components are composed from named parts. Wrap each part in a styled component:

```ts
import * as DialogPrimitive from '@radix-ui/react-dialog'

const Dialog = DialogPrimitive.Root
const DialogTrigger = DialogPrimitive.Trigger

const DialogOverlay = React.forwardRef<
  React.ElementRef<typeof DialogPrimitive.Overlay>,
  React.ComponentPropsWithoutRef<typeof DialogPrimitive.Overlay>
>(({ className, ...props }, ref) => (
  <DialogPrimitive.Overlay
    ref={ref}
    className={cn(
      'fixed inset-0 z-50 bg-black/80 data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0',
      className
    )}
    {...props}
  />
))
DialogOverlay.displayName = DialogPrimitive.Overlay.displayName
```

**Key conventions:**
- Use `React.ElementRef<typeof Primitive.Part>` for the ref type
- Use `React.ComponentPropsWithoutRef<typeof Primitive.Part>` for the props type — this gives you all Radix props plus HTML attributes
- Always pass `className` through `cn()` so callers can override
- Set `displayName` from the Radix primitive's own `displayName`

### data-[state] attributes

Radix exposes component state via `data-state` attributes (`open`, `closed`, `checked`, `unchecked`). Use Tailwind's `data-*` variant to style based on state:

```ts
// Animate based on Radix state
'data-[state=open]:animate-in data-[state=closed]:animate-out'
'data-[state=checked]:bg-primary data-[state=unchecked]:bg-input'
'data-[disabled]:opacity-50 data-[disabled]:cursor-not-allowed'
```

Never add your own `isOpen` class — use the `data-state` Radix already provides.

---

## Theming with CSS Variables

shadcn/ui uses CSS variables for all color tokens. This enables dark mode, theming, and token-level overrides without Tailwind config changes.

### Token structure

```css
:root {
  --background: 0 0% 100%;
  --foreground: 222.2 84% 4.9%;
  --primary: 222.2 47.4% 11.2%;
  --primary-foreground: 210 40% 98%;
  --secondary: 210 40% 96.1%;
  --secondary-foreground: 222.2 47.4% 11.2%;
  --muted: 210 40% 96.1%;
  --muted-foreground: 215.4 16.3% 46.9%;
  --accent: 210 40% 96.1%;
  --accent-foreground: 222.2 47.4% 11.2%;
  --destructive: 0 84.2% 60.2%;
  --destructive-foreground: 210 40% 98%;
  --border: 214.3 31.8% 91.4%;
  --input: 214.3 31.8% 91.4%;
  --ring: 222.2 84% 4.9%;
  --radius: 0.5rem;
}

.dark {
  --background: 222.2 84% 4.9%;
  --foreground: 210 40% 98%;
  /* ... dark overrides */
}
```

Values are HSL without the `hsl()` wrapper — Tailwind uses them as `hsl(var(--primary))`.

**In Tailwind config:**
```ts
theme: {
  extend: {
    colors: {
      background: 'hsl(var(--background))',
      foreground: 'hsl(var(--foreground))',
      primary: {
        DEFAULT: 'hsl(var(--primary))',
        foreground: 'hsl(var(--primary-foreground))',
      },
      // ...
    },
    borderRadius: {
      lg: 'var(--radius)',
      md: 'calc(var(--radius) - 2px)',
      sm: 'calc(var(--radius) - 4px)',
    },
  },
}
```

Then in components: `bg-primary text-primary-foreground` — not hardcoded colors.

---

## Compound Components

Use compound components when a parent and its children share state. The pattern uses React Context internally and exports named sub-components.

```ts
// Context
interface CardContextValue {
  variant: 'default' | 'bordered'
}
const CardContext = React.createContext<CardContextValue>({ variant: 'default' })

// Parent
interface CardProps extends React.HTMLAttributes<HTMLDivElement> {
  variant?: 'default' | 'bordered'
}

const Card = React.forwardRef<HTMLDivElement, CardProps>(
  ({ variant = 'default', className, children, ...props }, ref) => (
    <CardContext.Provider value={{ variant }}>
      <div
        ref={ref}
        className={cn(cardVariants({ variant }), className)}
        {...props}
      >
        {children}
      </div>
    </CardContext.Provider>
  )
)
Card.displayName = 'Card'

// Sub-components consume context
const CardHeader = React.forwardRef<HTMLDivElement, React.HTMLAttributes<HTMLDivElement>>(
  ({ className, ...props }, ref) => {
    const { variant } = React.useContext(CardContext)
    return (
      <div
        ref={ref}
        className={cn('flex flex-col space-y-1.5 p-6', variant === 'bordered' && 'border-b', className)}
        {...props}
      />
    )
  }
)
CardHeader.displayName = 'CardHeader'
```

**When to use compound components:**
- Parent and children share state that shouldn't be in the caller's scope
- Sub-components need to know about parent variant/configuration
- The composition API is the public surface (e.g. `<Card><CardHeader>…`)

**When not to use them:**
- Simple components with no shared state — use props directly
- When the sub-components are only ever used together — consider a single component with slots instead

---

## Controlled vs. Uncontrolled

Always support both patterns. Use the same approach Radix does internally.

```ts
interface CheckboxProps {
  checked?: boolean               // controlled
  defaultChecked?: boolean        // uncontrolled
  onCheckedChange?: (checked: boolean) => void
}

function Checkbox({ checked, defaultChecked, onCheckedChange, ...props }: CheckboxProps) {
  const [internalChecked, setInternalChecked] = React.useState(defaultChecked ?? false)
  const isControlled = checked !== undefined
  const currentChecked = isControlled ? checked : internalChecked

  const handleChange = (next: boolean) => {
    if (!isControlled) setInternalChecked(next)
    onCheckedChange?.(next)
  }

  // ...
}
```

**Rule:** If your component has internal state, expose both `value`/`defaultValue` and `onChange`. Do not force callers to manage state if they don't need to.

---

## Accessibility in React

All interactive components must meet these requirements. These are not optional.

### Focus management

```ts
// Visible focus ring — never remove outline without replacing it
'focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2'

// Programmatic focus — use after modal open, after action completes
const triggerRef = React.useRef<HTMLButtonElement>(null)
// On close:
triggerRef.current?.focus()
```

### ARIA patterns

```ts
// Loading state
<button aria-busy={isLoading} aria-label={isLoading ? 'Saving...' : 'Save'}>
  {isLoading ? <Spinner aria-hidden="true" /> : 'Save'}
</button>

// Error association
<input
  id="email"
  aria-describedby={hasError ? 'email-error' : undefined}
  aria-invalid={hasError}
/>
{hasError && <p id="email-error" role="alert">{errorMessage}</p>}

// Icon-only buttons
<button aria-label="Close dialog">
  <XIcon aria-hidden="true" />
</button>
```

### Keyboard navigation

- `Tab` / `Shift+Tab` — move between interactive elements
- `Enter` / `Space` — activate buttons, checkboxes
- `Escape` — close overlays, cancel operations
- Arrow keys — navigate within composite widgets (menus, tabs, radio groups)

If you're using Radix, keyboard navigation is handled. If you're building a custom composite widget, implement `onKeyDown` with the correct key bindings.

---

## Component Architecture

### File naming
- Component files: `PascalCase.tsx` (`Button.tsx`, `DataTable.tsx`)
- Utility files: `camelCase.ts` (`cn.ts`, `formatDate.ts`)
- Hook files: `useHookName.ts` (`useMediaQuery.ts`, `useDebounce.ts`)

### Directory structure for a component library

```
/components
├── ui/                   ← primitives (button, input, card, badge)
│   ├── Button.tsx
│   ├── Input.tsx
│   └── index.ts
├── patterns/             ← compositions (form, data table, nav)
│   ├── DataTable.tsx
│   ├── FormField.tsx
│   └── index.ts
└── [feature]/            ← product-specific (UserCard, InvoiceRow)
    ├── UserCard.tsx
    └── index.ts
```

**Rule:** `ui/` components have no product knowledge. `patterns/` components compose `ui/` components. Feature components compose patterns and contain product logic.

### Props discipline

```ts
// Avoid: too many props, growing without bound
interface ButtonProps {
  label: string
  icon?: React.ReactNode
  iconPosition?: 'left' | 'right'
  isFullWidth?: boolean
  isUppercase?: boolean  // ← start of a problem
  textSize?: 'sm' | 'md' // ← still growing
}

// Prefer: className escape hatch + CVA variants for intentional options
interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement>,
  VariantProps<typeof buttonVariants> {
  // Only props that change behavior, not appearance
  isLoading?: boolean
  leftIcon?: React.ReactNode
  rightIcon?: React.ReactNode
}
```

Accept `className` for one-off overrides rather than adding a new prop for every visual tweak. If you find yourself adding the same one-off repeatedly, that's when it becomes a variant.

---

## Patterns to Avoid

**Prop drilling beyond two levels.** Use Context or lift state to a shared ancestor.

**Boolean prop explosion.** `isLarge`, `isBold`, `isFullWidth`, `isRounded` on the same component is a variant system waiting to be written with CVA.

**Hardcoded colors.** Use CSS variables and Tailwind semantic tokens (`text-foreground`, `bg-muted`). Never `text-gray-700` in a component that needs to support dark mode.

**Conditional `className` with string concatenation:**
```ts
// Wrong
className={`button ${isLoading ? 'button--loading' : ''} ${variant === 'primary' ? 'button--primary' : ''}`}

// Right
className={cn(buttonVariants({ variant }), isLoading && 'opacity-70 cursor-wait', className)}
```

**Missing `displayName`.** Always set it on forwardRef components. React DevTools and error boundaries depend on it.

**Swallowing the `ref`.** If a component wraps a native element, forward the ref. If it doesn't, document why.

**Uncontrolled-only inputs.** Any input component that manages internal state must also accept a controlled `value` + `onChange` interface.
