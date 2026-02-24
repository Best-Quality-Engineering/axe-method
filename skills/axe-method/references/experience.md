# Experience Spec Guide

Interactive guide for defining and refining Experience specifications. **Experience Defines The Value.** The Experience spec captures why the product exists — the capabilities, features, and novel solutions that give someone a reason to use it — and how those are experienced: behaviors, flows, feedback, states, transitions, product lifecycle, system events, background operations, accessibility, localization, design language, and platform HIG compliance. It does not describe how the experience is rendered. "Consumer" includes end users, but also callers, clients, and dependent systems. User experience is the primary concern; product behaviors that are not directly user-facing (background sync, lifecycle management, system events) also belong here when they shape the observable product.

## When to Use

- Starting a new Experience spec from scratch
- Defining what the product does and why someone would use it
- Expanding an existing Experience spec to cover new capabilities or behaviors
- Unsure what belongs in Experience vs. Engineering
- User says `/axe-method:experience`

## Phase 1: Discover

Before asking questions, use agent tools to find what already exists.

**Use Task (Explore) agents to scan for:**
- Product capabilities — README, marketing copy, onboarding flows, feature flags, top-level navigation that reveals what the product offers
- Views, screens, pages — anything that represents a user-facing destination
- Navigation structures — tab bars, sidebars, routers, navigation stacks
- User actions — button handlers, gesture recognizers, form submissions
- System events — app lifecycle callbacks, background refresh triggers, push notification handlers
- Background operations — sync jobs, scheduled tasks, data migrations, cleanup operations
- State enums or status types — loading, error, empty, populated
- Existing Experience specs or feature documentation
- Accessibility: VoiceOver labels, accessibility identifiers, Dynamic Type support, Reduce Motion checks
- Localization: string catalogs, NSLocalizedString usage, locale-aware formatters, RTL layout constraints
- Design language: color definitions, typography scales, spacing constants, animation duration patterns

**Use AskUserQuestion to present findings:**
```
"I found these product-facing areas in the codebase:
  - [list of views/screens discovered]
  - [list of lifecycle/system behaviors discovered]

Which of these are in scope for this Experience spec?"
  Options: All of them / Select specific areas / These are wrong, let me explain
```

### Domain Context

If `/axe-method:specify` has already established the domain, skip this. Otherwise:

**Use AskUserQuestion:**
- "What domain are we working in?"
- "Who are the users?"
- Present discovered class/model names: "Are any of these part of the ubiquitous language?"

## Phase 2: Structured Exploration

Work through each area using a combination of discovery and structured questions. Use TaskCreate to track which areas have been explored.

### 1. Product Value and Capabilities

This comes first because everything else serves it. The Experience spec must answer: **"Why would someone use this product?"**

**Discover first:** Use Task (Explore) to find README files, marketing copy, app store descriptions, onboarding flows, feature flags, and top-level navigation that reveals what the product offers. Scan for distinct capabilities — the things the product lets users do that they couldn't do before (or couldn't do as well).

**Use AskUserQuestion:**
```
"What does this product make possible? What are the key capabilities?"
  Options: Let me describe what it does / Start from what I found in the codebase / I have a product brief
```

For each capability, ask using AskUserQuestion:
- **"What problem does this solve?"** — What was the user doing before? What was painful or impossible?
- **"What is novel about this solution?"** — Options: New capability (nothing like it) / Better approach (improves on existing solutions) / Integration (connects things that were separate) / Let me describe
- **"Who specifically needs this?"** — Options: All users / Specific user type / Power users / Let me describe

Probe for completeness:
- "Are there capabilities the product offers that we haven't named?"
- "What would users miss most if it were taken away?"
- "What is the single most compelling reason to use this product?"

**Classification Guard for Capabilities:**
- The capability and its value stay in the Experience spec: "Users can discover and join listening sessions with friends in real time", "The product recommends content based on listening patterns"
- How the capability is structured goes to Architecture: bounded contexts, aggregates, seams between components
- How the capability is implemented goes to Engineering: APIs, protocols, UI controls

### 2. Core Behaviors

**Discover first:** Use Grep/Glob to find action handlers, event names, and user-triggered functions in the codebase.

**Use AskUserQuestion:**
```
"I found these user actions in the codebase:
  - [discovered actions]

For each, I need to understand the behavior:"
  Options: Let's go through them one by one / Add missing actions first / Start fresh
```

For each behavior, ask using AskUserQuestion:
- **"What triggers this behavior?"** — Options: User gesture / System event / Time-based / Other
- **"What is the expected outcome?"** — Open-ended
- **"What happens when it fails?"** — Options: Error message / Retry / Silent fallback / Not applicable

Probe for completeness:
- "What happens when [behavior] is already in progress?"
- "Can the user undo [behavior]?"

### 3. States and Transitions

**Discover first:** Use Grep to find state enums, loading indicators, empty states, error handling patterns.

**Use AskUserQuestion:**
```
"I found these states in the codebase:
  - [discovered states]

Are these the right states? What's missing?"
  Options: These are correct / Add more states / Let me explain the state model
```

For each state, confirm:
- **What causes the transition?** — Options: User action / System event / Timeout / Data arrival
- **What does the user experience in this state?** — Not the UI (that's Engineering), but the experience

### 4. Navigation and Flows

**Discover first:** Use Task (Explore) to map the navigation structure — find router configs, tab definitions, navigation controllers, segues.

**Use AskUserQuestion:**
```
"I found this navigation structure:
  - [discovered navigation]

Is this the intended navigation model?"
  Options: Yes, that's right / It needs changes / Let me describe the navigation
```

Cover:
- **Major destinations** — The places the user can navigate to
- **Entry point** — Where the user lands first
- **Multi-step flows** — Wizards, onboarding, setup sequences

### 5. Feedback

**Discover first:** Use Grep to find toast/alert/notification patterns, progress indicators, success/error handling.

**Use AskUserQuestion for each behavior:**
- **"How does the user know [action] succeeded?"** — Options: State change / Confirmation message / Animation / Navigation / Other
- **"How does the user know [action] failed?"** — Options: Error message / Retry option / Fallback behavior / Other
- **"Is there ongoing status the user sees?"** — Options: Connection state / Progress / Sync status / None

### 6. Affordances

**Use AskUserQuestion:**
```
"For the behaviors we've identified, how does the user discover they're available?"
  Options: Always visible / Context-dependent / Hidden (gesture/long-press) / Let me explain per behavior
```

### 7. Platform Experience Adaptations

**Discover first:** Use Glob to find platform-specific files (#if os(), platform targets, platform-specific directories).

**Use AskUserQuestion:**
```
"I found these platform targets: [iOS, macOS, tvOS, etc.]

How does the experience adapt per platform?"
  Options: Same experience everywhere / Capabilities vary by platform / Let me describe each platform
```

If capabilities vary, use AskUserQuestion with a matrix:
- For each capability x platform: **Full / Partial / Excluded**

### 8. Accessibility

**Discover first:** Use Grep/Glob to scan for accessibility labels, accessibility traits, accessibilityIdentifier, Dynamic Type support (@ScaledMetric, UIFontMetrics), Reduce Motion preferences (accessibilityReduceMotion), color contrast definitions, focus management (accessibilityFocusedElement, focusable modifiers).

**Use AskUserQuestion:**
```
"I found these accessibility patterns in the codebase:
  - VoiceOver: [labels, traits, custom actions discovered]
  - Dynamic Type: [scaled metrics, font metrics discovered]
  - Reduce Motion: [motion preference checks discovered]
  - Focus management: [focus patterns discovered]

What accessibility standards apply?"
  Options: WCAG AA / WCAG AAA / Platform defaults only / Let me specify
```

For each behavior, ask using AskUserQuestion:
- **"What assistive technologies must be supported?"** — Options: VoiceOver / Switch Control / Voice Control / All / Let me specify
- **"What is the screen reader experience for [behavior]?"** — How should VoiceOver users experience this flow?
- **"Are there custom accessibility actions?"** — Options: Yes (describe) / No / Let me explain per behavior
- **"What dynamic type sizes must be supported?"** — Options: All system sizes / Up to XXXL / Custom range / Let me specify

**Classification Guard for Accessibility:**
- Requirements and principles stay in the Experience spec: "All interactive elements are accessible via VoiceOver", "Content adapts to user's preferred text size", "Animations respect Reduce Motion preference"
- Specific label text, API usage, and identifier naming go to Engineering: "accessibilityLabel = 'Play button'", "Use @ScaledMetric for padding values", "accessibilityIdentifier = 'playback.play'"

### 9. Localization

**Discover first:** Use Grep/Glob to scan for string catalogs (.xcstrings), NSLocalizedString, String(localized:), localized formatters (DateFormatter with locale, NumberFormatter), RTL layout support (leading/trailing constraints, semanticContentAttribute), pluralization rules (stringsdict).

**Use AskUserQuestion:**
```
"I found these localization patterns in the codebase:
  - String catalogs: [files discovered]
  - Localized formatters: [date, number, measurement formatters]
  - RTL support: [leading/trailing usage, layout direction checks]
  - Pluralization: [stringsdict files, plural rules]

What are the localization requirements?"
  Options: Single language only / Multiple languages (list them) / Let me describe
```

For localization scope, ask using AskUserQuestion:
- **"Which languages must be supported?"** — Options: English only / [discovered languages] / Additional languages needed / Let me list
- **"Are there RTL language requirements?"** — Options: Yes (Arabic, Hebrew, etc.) / No / Future consideration
- **"Are there cultural adaptations beyond translation?"** — Options: Date/number formats per locale / Content differences per region / None / Let me explain
- **"How should the app handle locale changes?"** — Options: Restart required / Live update / Per-screen refresh / Let me describe

**Classification Guard for Localization:**
- Language support and cultural rules stay in the Experience spec: "All user-facing strings are localizable", "Date and number formats adapt to the user's locale", "RTL layouts mirror the LTR experience"
- String catalog structure, formatter configurations, and implementation details go to Engineering: "Use .xcstrings catalogs", "NumberFormatter with locale-aware grouping separator", "Leading/trailing constraints instead of left/right"

### 10. Design Language

**Discover first:** Use Grep/Glob to scan for color definitions (Color assets, UIColor extensions, semantic color names), typography scales (font definitions, type styles, text style hierarchies), spacing constants (padding/margin systems, layout guides), animation durations and curves (withAnimation, UIView.animate, CAAnimation patterns), icon patterns (SF Symbol usage, custom icon sets).

**Use AskUserQuestion:**
```
"I found these design language patterns in the codebase:
  - Colors: [semantic names, color assets, themes]
  - Typography: [font definitions, text styles, scales]
  - Spacing: [constants, layout systems]
  - Motion: [animation patterns, duration constants]
  - Icons: [SF Symbols, custom icons]

What design language principles apply?"
  Options: Apple system defaults / Custom design system / Hybrid / Let me describe
```

For design language principles, ask using AskUserQuestion:
- **"What is the color philosophy?"** — Options: Semantic naming (primary, secondary, accent) / Fixed palette / Adaptive (light/dark/high-contrast) / Let me describe
- **"How many typography levels are needed?"** — Options: System text styles only / Custom hierarchy (list levels) / Let me describe
- **"What spacing system applies?"** — Options: 4pt grid / 8pt grid / System defaults / Custom / Let me describe
- **"What is the motion philosophy?"** — Options: Minimal (functional only) / Expressive (personality) / Platform default / Let me describe
- **"What iconography system is used?"** — Options: SF Symbols only / Custom icon set / Mixed / Let me describe

**Classification Guard for Design Language:**
- Principles and hierarchy stay in the Experience spec: "Semantic color naming with a primary accent", "Three-level typography hierarchy: headline, body, caption", "8pt spacing grid", "Functional motion — animations communicate state changes, not decoration"
- Specific values go to Engineering: "Primary color is #007AFF", "Body text is 17pt SF Pro Regular", "Standard padding is 16pt", "State transition duration is 0.3s ease-in-out"

### 11. Platform HIG Compliance

**Discover first:** Use Grep/Glob to scan for platform-specific UI patterns (NavigationStack, UINavigationController, NSToolbar), system integration points (widgets, shortcuts, intents, share extensions, App Clips), platform-specific controls (UIMenu, .contextMenu, NSTouchBar), and Human Interface Guideline conformance patterns.

**Use AskUserQuestion:**
```
"I found these platform integration points:
  - Navigation patterns: [discovered patterns]
  - System integrations: [widgets, shortcuts, intents, share extensions]
  - Platform controls: [discovered platform-specific controls]

Which platform guidelines govern this product?"
  Options: iOS HIG / macOS HIG / Multiple platforms (list) / Let me specify
```

For platform compliance, ask using AskUserQuestion:
- **"Which Human Interface Guidelines apply?"** — Options: iOS / macOS / iPadOS / watchOS / tvOS / visionOS / Multiple (list)
- **"What system integration points are required?"** — Options: Widgets / Shortcuts / Share extensions / App Clips / None / Let me list
- **"How should the app participate in the platform ecosystem?"** — Options: Deep system integration / Standalone experience / Selective integration / Let me describe

**Classification Guard for Platform HIG:**
- Platform principles and system integration requirements stay in the Experience spec: "Navigation follows iOS HIG push/pop patterns", "Key actions are available via Shortcuts", "App supports system-wide dark mode"
- Specific framework APIs and implementation details go to Engineering: "Use NavigationStack with NavigationLink", "Implement AppIntents protocol for Shortcuts", "Override userInterfaceStyle on window scene"

### 12. Product Lifecycle

**Discover first:** Use Grep/Glob to scan for app lifecycle handlers (applicationDidFinishLaunching, scenePhase, onAppear/onDisappear), background task registrations (BGTaskScheduler, BackgroundTasks), push notification handling, data migration code, first-launch detection, and upgrade paths.

**Use AskUserQuestion:**
```
"I found these lifecycle patterns in the codebase:
  - App lifecycle: [launch, background, termination handlers]
  - Background tasks: [sync, refresh, cleanup operations]
  - Migrations: [data upgrade paths, schema changes]
  - First launch: [onboarding triggers, default setup]

What lifecycle behaviors does the product have?"
  Options: Only what I found / There are more / This product has no lifecycle concerns / Let me describe
```

For each lifecycle behavior, ask using AskUserQuestion:
- **"What triggers this behavior?"** — Options: App launch / Background schedule / Push notification / System event / User action / Other
- **"What is the observable outcome?"** — What changes in the product state? What does the user notice (or not notice)?
- **"What happens when it fails?"** — Options: Retry silently / Notify user / Degrade gracefully / Block until resolved

Probe for completeness:
- "What happens on first launch vs. subsequent launches?"
- "What state does the product resume after being backgrounded?"
- "How does the product handle version upgrades with data changes?"

**Classification Guard for Lifecycle:**
- Observable product behaviors stay in the Experience spec: "Data syncs in the background when connectivity is available", "The product resumes where the user left off after relaunch", "First launch presents onboarding before the main experience"
- Implementation mechanics go to Engineering: "Use BGAppRefreshTask with 15-minute minimum interval", "Persist last-viewed state in UserDefaults", "Check hasCompletedOnboarding flag in AppStorage"

### 13. System Events and Background Operations

**Discover first:** Use Grep/Glob to scan for notification observers (NotificationCenter), system event handlers (low memory, significant time change, locale change), connectivity monitoring (NWPathMonitor, Reachability), and resource management patterns.

**Use AskUserQuestion:**
```
"I found these system event patterns:
  - Notifications: [system notifications observed]
  - Connectivity: [network monitoring patterns]
  - Resource management: [memory/storage handling]

What system events affect the product experience?"
  Options: Only what I found / There are more / No system event concerns / Let me describe
```

For each system event, ask using AskUserQuestion:
- **"How does the product respond to this event?"** — Options: Adapt behavior / Notify user / Degrade gracefully / Ignore / Let me describe
- **"Is the user aware this is happening?"** — Options: Always visible / Visible on failure / Invisible unless they look / Never visible

Probe for completeness:
- "What happens when connectivity is lost mid-operation?"
- "How does the product handle low storage conditions?"
- "What happens when the system locale or time zone changes?"

**Classification Guard for System Events:**
- Product behavior in response to events stays in the Experience spec: "The product adapts to connectivity changes without user intervention", "Low storage conditions surface a clear message before data loss", "Time zone changes update all displayed times immediately"
- Event handling implementation goes to Engineering: "Observe .NSSystemTimeZoneDidChange", "Monitor NWPathMonitor for connectivity", "Register for UIApplication.didReceiveMemoryWarningNotification"

## Classification Guard

Throughout the exploration, watch for implementation details leaking into Experience specs. When the user says something like:

| User says... | Guide them to... |
|-------------|-----------------|
| "We need a microservice for recommendations" | That's Architecture (system structure) — here we capture the capability: "The product recommends content based on listening patterns" |
| "The playlist table uses a B-tree index" | That's Engineering (implementation) — here we capture the capability: "Users can search and browse large playlists instantly" |
| "A slider for volume" | "The user can adjust volume smoothly" (Experience) — the slider is Engineering |
| "A red button in the top right" | "The user can cancel the action" (Experience) — the button placement and color is Engineering |
| "A NavigationStack with..." | "The user navigates into detail" (Experience) — NavigationStack is Engineering |
| "44pt touch target" | Note this for the Engineering spec — it's UI implementation |
| "WCAG AA contrast ratio of 4.5:1" | Note this for the Engineering spec — Experience says "Sufficient contrast for readability" |
| "Use NSLocalizedString with key pattern X" | Note this for the Engineering spec — Experience says "All user-facing strings are localizable" |
| "Primary color is #007AFF" | Note this for the Engineering spec — Experience says "Semantic color naming with a primary accent" |
| "Use BGAppRefreshTask" | Note this for the Engineering spec — Experience says "Data syncs in the background" |

Say: *"That's a great detail — let's capture the behavior here and the implementation in the Engineering spec."*

### Ambiguous Cases

Real cases aren't always clear-cut. When you're unsure whether something is Experience or belongs elsewhere, use this decision framework:

1. **"Does this describe what the user experiences — an observable outcome, capability, or requirement?"** → Experience. *"Search results appear instantly"* is Experience (observable outcome). The performance budget that achieves it is Engineering.
2. **"Does this describe how we build it — a specific technique, value, or mechanism?"** → Engineering. *"44pt touch target"* is Engineering. The accessibility requirement it fulfills (*"All interactive elements are accessible"*) is Experience.
3. **"Does this describe what parts exist or how they connect?"** → Architecture. *"Search and playback are independent systems"* is Architecture. What search makes possible is Experience.
4. **"Does this span the boundary?"** → Split it. State the requirement or principle in Experience, the specific value or technique in Engineering, and the structural implication in Architecture. Example: *"Animations respect Reduce Motion preference"* — the requirement is Experience, the preference-checking API is Engineering, and whether motion preferences cross component boundaries is Architecture.

## Cross-References

Experience specs don't exist in isolation. These are the key intersection points with Architecture and Engineering:

| When you specify... | Coordinate with Architecture on... | Coordinate with Engineering on... |
|--------------------|------------------------------------|----------------------------------|
| Product capabilities | Which bounded contexts support each capability; how the system decomposes to deliver it | Which protocols, APIs, and UI controls implement it |
| Accessibility requirements | Whether seams constrain assistive technology support (e.g., can screen reader data cross component boundaries?) | Specific accessibility label text, Dynamic Type implementation, contrast ratio values |
| Localization requirements | Whether bounded contexts need locale-aware boundaries or shared localization infrastructure | String catalog structure, formatter configurations, RTL layout implementation |
| Platform adaptations | Whether architecture differs per platform (shared core vs. platform-specific components) | Platform-specific controls, conditional compilation, framework choices |
| Product lifecycle behaviors | State distribution during lifecycle transitions; which components participate in background operations | App lifecycle API usage, background task registration, migration code |
| System event responses | Which components observe which events; event propagation across seams | Notification observer implementation, connectivity monitoring APIs |

Flag these intersections explicitly when you encounter them. Don't assume the Architecture or Engineering specifier will discover them independently.

## Phase 3: Draft Output

After exploration, produce a draft Experience spec:

1. **Start with value** — state what the product makes possible and why someone would use it
2. Organize by capability or feature (one file per natural grouping)
3. For each capability, describe the behaviors — what happens, what users and consumers experience
4. Include a platform support table if multi-platform
5. Include accessibility requirements per behavior
6. Include localization scope and cultural adaptation rules
7. Include design language principles (not values — those go to Engineering)
8. Include platform HIG compliance requirements
9. Include product lifecycle and system event behaviors where applicable
10. Place in `Documents/Specifications/Experience/{Domain}/v0.1.0/`
11. Flag any cross-cutting concerns that need Architecture or Engineering specs

**Use AskUserQuestion before writing:**
```
"Here's the draft Experience spec structure:
  - [file 1]: covers [behaviors]
  - [file 2]: covers [behaviors]

Ready to write these drafts?"
  Options: Write them / Let me review the content first / Make changes
```
