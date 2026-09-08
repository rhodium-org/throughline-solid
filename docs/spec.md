# SOLID principles — throughline source

This document is **generated from the graph** by `tl docs`; `tl docs --check` gates it in CI. The prose headings are hand-owned — everything between `tl:*` markers is injected from the YAML items, so the published spec can never drift from the graph.

This source expresses the five **SOLID** principles of object-oriented design, and the guidance on applying them together, as a grounded IDD graph. Each principle is a `user_requirement`; every concrete, checkable rule is a `system_requirement` that `implements` its principle. Each rule states what to do and how to check it, so that a consumer citing `solid:SR-0007` from its own requirement inherits a test it can run against its code. The publication a rule is drawn from lives in `attrs.source_ref`; the throughline UIDs are this source's own and immutable.

It carries
<!-- tl:count type == 'user_requirement' -->
6
<!-- tl:end --> principles and
<!-- tl:count type == 'system_requirement' -->
43
<!-- tl:end --> rules.

## Purpose

<!-- tl:item INT-0001 -->
**INT-0001 — Software stays cheap and safe to change as it grows** — `intent`, status `proposed`

> Most of the cost of software is the cost of changing it after it first works.
> The SOLID principles exist to keep that cost low and predictable. They do this by
> controlling how modules divide responsibility and how they depend on one another,
> so that a change to one part of a system does not force changes, rebuilds, retests
> or redeployments across parts that had no reason to move.
>
> A system built to these principles has modules that each answer to one reason to
> change, that grow by adding code rather than editing working code, whose parts can
> be swapped without the rest noticing, whose interfaces are no wider than their
> clients need, and whose high-level policy does not depend on low-level detail.

*Rationale:* The principles are means, not ends. The end is a system that can absorb the changes its owners will actually ask for. A rule in this graph is applied because a real axis of change or a real second client calls for it, never for its own sake.

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 7 — What Is Agile Design?; Martin, Clean Architecture (2017), Part III — Design Principles · **principle**: APPLY · **origin**: ai
<!-- tl:end -->

## Single Responsibility Principle

<!-- tl:item UR-0001 -->
**UR-0001 — Single Responsibility Principle — a module has one reason to change** — `user_requirement`, status `proposed`

> A module answers to one, and only one, actor. An actor is a group of people or
> systems that ask for changes for the same reason. When two actors would each
> cause the same module to change, the module has two responsibilities and must be
> split so that each actor's logic lives in its own module.

*Rationale:* When one module serves several actors, a change made for one of them breaks or redeploys the others. Separating by reason to change keeps each change local.

*Derives from:* INT-0001

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 8 — SRP; Martin, Clean Architecture (2017), ch. 7 — SRP · **principle**: SRP · **origin**: ai
<!-- tl:end -->

<!-- tl:table type == 'system_requirement' and attrs.get('principle') == 'SRP' -->
| UID | Type | Status | Title |
|---|---|---|---|
| SR-0001 | system_requirement | proposed | Name the single actor each module serves before writing it |
| SR-0002 | system_requirement | proposed | Split a module whose methods would change for different actors |
| SR-0003 | system_requirement | proposed | Do not share a private helper between methods that serve different actors |
| SR-0004 | system_requirement | proposed | Keep each module's public methods cohesive around the same data |
| SR-0005 | system_requirement | proposed | Keep business rules, persistence, presentation and transport in separate modules |
| SR-0006 | system_requirement | proposed | Keep together what always changes together |
| SR-0007 | system_requirement | proposed | Treat a module whose honest name needs 'and', 'manager', 'util' or 'helper' as a smell |
| SR-0008 | system_requirement | proposed | Present a facade when a split would otherwise expose several modules to one caller |
| SR-0009 | system_requirement | proposed | Make each function do one thing at one level of abstraction |
<!-- tl:end -->

<!-- tl:item SR-0001 -->
**SR-0001 — Name the single actor each module serves before writing it** — `system_requirement`, status `proposed`

> Before creating or extending a module, name the actor it serves. An actor is the
> group of people or systems whose requests would change it, for example finance,
> operations, the database team, a partner API. Record the actor where the module is
> defined, in its documentation comment or module description.
>
> Check. If the honest answer names two actors, the module has two responsibilities.
> Split it before adding code.

*Rationale:* A responsibility is a reason to change, and reasons to change come from people. Naming the actor makes the single responsibility a testable fact rather than a feeling about cohesion.

*Implements:* UR-0001

**source_ref**: Martin, Clean Architecture (2017), ch. 7 — SRP · **principle**: SRP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0002 -->
**SR-0002 — Split a module whose methods would change for different actors** — `system_requirement`, status `proposed`

> When one module holds methods that different actors would ask to change, move each
> actor's methods into a separate module. The classic case is one class that
> calculates pay for finance, reports hours for operations, and saves itself for the
> database team. Those become three modules that share the data they need through a
> simple data structure, not through each other's logic.
>
> Check. Ask, for each public method, who would request a change to it. Methods with
> different answers belong in different modules.

*Rationale:* Methods that change for different reasons but live together get coupled by accident. A fix for one actor then breaks another actor's behaviour, and both must be retested and redeployed for a change only one of them wanted.

*Implements:* UR-0001

**source_ref**: Martin, Clean Architecture (2017), ch. 7 — SRP (the Employee example); Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 8 — SRP · **principle**: SRP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0003 -->
**SR-0003 — Do not share a private helper between methods that serve different actors** — `system_requirement`, status `proposed`

> Two methods that serve different actors must not call the same private helper when
> that helper encodes a rule either actor could change. Duplicate the helper, or lift
> the shared part into a module that is clearly owned by one actor and consumed by
> the other through a stable interface.
>
> Check. Look for a private function called from methods that belong to different
> actors. If changing it for one actor would silently change the result for the
> other, it is a shared-algorithm defect.

*Rationale:* Two things that look the same today but change for different reasons are accidental duplicates, not true duplicates. Merging them is the most common way a single-responsibility module silently acquires a second one.

*Implements:* UR-0001

**source_ref**: Martin, Clean Architecture (2017), ch. 7 — SRP (accidental duplication) · **principle**: SRP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0004 -->
**SR-0004 — Keep each module's public methods cohesive around the same data** — `system_requirement`, status `proposed`

> The public methods of a class or module all operate on most of the same fields or
> state. A method that touches only a subset of the state, which no other method
> touches, is a candidate for its own module.
>
> Check. Sketch which methods use which fields. Two or more disjoint clusters mean
> two or more modules are hiding in one.

*Rationale:* Low cohesion is the structural signature of more than one responsibility. Clusters of methods around separate data are separate reasons to change that happen to share a file.

*Implements:* UR-0001

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 8 — SRP (cohesion) · **principle**: SRP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0005 -->
**SR-0005 — Keep business rules, persistence, presentation and transport in separate modules** — `system_requirement`, status `proposed`

> A module that decides what the software does (business rules) does not also decide
> how the result is stored, how it is shown, or how it travels over a network. Each of
> those is a separate concern owned by a separate actor and lives in its own module.
> A business-rule module has no import of a database driver, a web framework, a
> serialisation library or a user-interface toolkit.
>
> Check. Read the module's imports. Imports from more than one of these categories,
> or a business module importing any of them, mark a violation.

*Rationale:* Storage, display and transport technologies each change on their own schedule and at the request of different people. Mixing them into the rules means every technology decision reopens the rules for edit.

*Implements:* UR-0001

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 8 — SRP (separating coupled responsibilities); Martin, Clean Architecture (2017), ch. 7 — SRP · **principle**: SRP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0006 -->
**SR-0006 — Keep together what always changes together** — `system_requirement`, status `proposed`

> Do not split a module along a line that has never moved and is not expected to. A
> responsibility is an axis of change only if changes actually arrive along it. Two
> behaviours that are always changed in the same commit, by the same actor, belong in
> the same module.
>
> Check. Before separating two behaviours, name the actor and the concrete change
> that would move one without the other. If you cannot, leave them together.

*Rationale:* Needless separation is its own cost. It spreads one change across several modules and forces the reader to reassemble the responsibility in their head.

*Implements:* UR-0001

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 8 — SRP (an axis of change is only an axis of change if the changes actually occur) · **principle**: SRP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0007 -->
**SR-0007 — Treat a module whose honest name needs 'and', 'manager', 'util' or 'helper' as a smell** — `system_requirement`, status `proposed`

> Name a module for the one thing it does. If the accurate name would contain 'and',
> or falls back to a catch-all like Manager, Processor, Utils or Helper, the module
> is a bag of unrelated responsibilities. Split it and name each part for its actor
> and purpose.
>
> Check. Try to write a one-sentence description of the module without the word
> 'and'. Failure is the signal.

*Rationale:* Vague names are where second responsibilities hide. A precise name is a cheap, constant test of single responsibility that every reader runs unconsciously.

*Implements:* UR-0001

**source_ref**: Martin, Clean Code (2008), ch. 10 — Classes (class organisation and the SRP) · **principle**: SRP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0008 -->
**SR-0008 — Present a facade when a split would otherwise expose several modules to one caller** — `system_requirement`, status `proposed`

> When splitting a module by actor leaves a caller needing all the parts, add a thin
> facade that instantiates and delegates to them. The facade holds no logic of its
> own. Callers see one entry point; each responsibility still lives in its own
> module.
>
> Check. A facade method body is a delegation and nothing else. Logic in a facade is
> a responsibility that escaped the split.

*Rationale:* Without a facade, every caller must know how the responsibilities were divided, which couples the callers to the split and defeats the purpose of making it.

*Implements:* UR-0001

**source_ref**: Martin, Clean Architecture (2017), ch. 7 — SRP (the Facade solution) · **principle**: SRP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0009 -->
**SR-0009 — Make each function do one thing at one level of abstraction** — `system_requirement`, status `proposed`

> Apply single responsibility inside a module too. A function does one thing, and
> all its statements sit at the same level of abstraction. A function that mixes
> high-level steps with low-level detail, or that contains sections separated by
> blank lines and comments, is several functions.
>
> Check. If you can extract another function from it with a name that is not merely
> a restatement of its body, it does more than one thing.

*Rationale:* Functions that do one thing are the units from which single-responsibility modules are assembled. A function with several jobs cannot be moved to the module that owns any one of them.

*Implements:* UR-0001

**source_ref**: Martin, Clean Code (2008), ch. 3 — Functions (do one thing; one level of abstraction per function) · **principle**: SRP · **origin**: ai
<!-- tl:end -->

## Open-Closed Principle

<!-- tl:item UR-0002 -->
**UR-0002 — Open-Closed Principle — extend behaviour without modifying working code** — `user_requirement`, status `proposed`

> A module is open for extension and closed for modification. New behaviour of a
> kind the design anticipates is added by writing new code, not by editing the
> code that already works. The module reaches this state by depending on an
> abstraction at the point where variation occurs, so that a new variant is a new
> implementation of that abstraction.

*Rationale:* Code that is edited for every new case accumulates risk with every edit and must be retested each time. Code that is extended stays stable, and the new case is tested alone.

*Derives from:* INT-0001

**source_ref**: Meyer, Object-Oriented Software Construction (1988) — the Open-Closed principle; Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 9 — OCP; Martin, Clean Architecture (2017), ch. 8 — OCP · **principle**: OCP · **origin**: ai
<!-- tl:end -->

<!-- tl:table type == 'system_requirement' and attrs.get('principle') == 'OCP' -->
| UID | Type | Status | Title |
|---|---|---|---|
| SR-0010 | system_requirement | proposed | Add a new variant by adding code, not by editing working code |
| SR-0011 | system_requirement | proposed | Replace a repeated switch on a type tag with polymorphism |
| SR-0012 | system_requirement | proposed | Do not branch on the concrete type of an object |
| SR-0013 | system_requirement | proposed | Close a module against a kind of change after that change has happened once |
| SR-0014 | system_requirement | proposed | Do not build abstractions for variation that has not occurred and is not known to be coming |
| SR-0015 | system_requirement | proposed | Protect higher-level policy from changes in lower-level detail |
| SR-0016 | system_requirement | proposed | Expose behaviour, not fields, from a module that others depend on |
<!-- tl:end -->

<!-- tl:item SR-0010 -->
**SR-0010 — Add a new variant by adding code, not by editing working code** — `system_requirement`, status `proposed`

> When a new case arrives of a kind the design already varies on, for example a new
> payment method, export format, notification channel, pricing rule or shape, add it
> as a new module that implements the existing abstraction. Existing modules are not
> edited, except for the one place that registers or composes implementations.
>
> Check. Diff the change. If files other than the new implementation and the
> composition root were modified, the design is not closed against that kind of
> change.

*Rationale:* Every edit to a working module risks breaking it and forces its retest. When a new case is pure addition, the old code stays proven and only the new code needs proving.

*Implements:* UR-0002

**source_ref**: Meyer, Object-Oriented Software Construction (1988) — the Open-Closed principle; Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 9 — OCP; Martin, Clean Architecture (2017), ch. 8 — OCP · **principle**: OCP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0011 -->
**SR-0011 — Replace a repeated switch on a type tag with polymorphism** — `system_requirement`, status `proposed`

> A chain of if, else-if or switch statements that branches on a type code, an enum
> of kinds, a string discriminator or the concrete class of an object, and that
> appears in more than one place, becomes a family of types behind one abstraction.
> Each branch becomes an implementation. A single such switch, in one place, that
> only creates the right implementation (a factory) is acceptable.
>
> Check. Search for the discriminator. If the same set of cases is switched on in two
> or more places, apply this rule.

*Rationale:* Each repeated switch is a place that must be found and edited for every new case. Missing one is a silent defect. Polymorphism turns the set of cases into a set of modules that the compiler or runtime dispatches without anyone remembering.

*Implements:* UR-0002

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 9 — OCP (the Shape example); Martin, Clean Code (2008), ch. 3 — Functions (switch statements) · **principle**: OCP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0012 -->
**SR-0012 — Do not branch on the concrete type of an object** — `system_requirement`, status `proposed`

> Code that receives an object through an abstraction does not inspect its concrete
> type with instanceof, type checks, casts, reflection on class names or a
> hand-rolled kind field, in order to choose behaviour. The behaviour that differs
> per type belongs in the type, reached through a method on the abstraction.
>
> Check. Every occurrence of a type test on an abstraction-typed value is a
> violation unless it is confined to the composition root or a deserialisation
> boundary.

*Rationale:* A type test is a hidden switch. It couples the caller to every concrete type, so adding a type means finding and editing every caller that tests for one.

*Implements:* UR-0002

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 9 — OCP (heuristics; run-time type information is dangerous) · **principle**: OCP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0013 -->
**SR-0013 — Close a module against a kind of change after that change has happened once** — `system_requirement`, status `proposed`

> Do not guess which changes will come. Write the simplest code that works. When the
> first change of a given kind arrives, refactor so that the module is closed against
> further changes of that kind, then implement the change as an extension. Do not
> build the abstraction before the first change has shown that this axis is real.
>
> Check. Every abstraction introduced in the name of this principle can point to the
> change that motivated it.

*Rationale:* Abstractions built against imagined change are usually wrong about which change comes, and they cost the reader indirection for no protection. The first real change tells you where the seam actually is.

*Implements:* UR-0002

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 9 — OCP (anticipation and natural structure; taking the first bullet) · **principle**: OCP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0014 -->
**SR-0014 — Do not build abstractions for variation that has not occurred and is not known to be coming** — `system_requirement`, status `proposed`

> Do not add an interface, a strategy, a plug-in point or a configuration switch for
> a variation that exists only in speculation. One implementation with no second
> implementation and no concrete plan for one is a concrete class, not an
> abstraction.
>
> Check. An interface with exactly one implementation and no named second
> implementation, whether real, planned or a test double that a test genuinely
> needs, is needless complexity. Remove it or justify it in writing.

*Rationale:* Needless complexity is a design smell in its own right. It makes the design harder to read and change today for a benefit that may never be claimed.

*Implements:* UR-0002

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 7 — What Is Agile Design? (needless complexity); Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 9 — OCP · **principle**: OCP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0015 -->
**SR-0015 — Protect higher-level policy from changes in lower-level detail** — `system_requirement`, status `proposed`

> Arrange dependencies so that a change to a low-level component, for example the
> presenter, the database or a view, cannot force a change to a higher-level one,
> such as the business rules. The higher the level of a component, the more
> protected it is from change in components below it. The dependency arrows point
> from detail toward policy.
>
> Check. For each pair of components, ask whether a change to the lower one would
> require editing the higher one. Any yes marks an arrow that points the wrong way.

*Rationale:* This is the Open-Closed Principle at architectural scale. Policy is the part the business cares about most and changes least; it must be the part that is closed.

*Implements:* UR-0002

**source_ref**: Martin, Clean Architecture (2017), ch. 8 — OCP (directional control; information hiding) · **principle**: OCP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0016 -->
**SR-0016 — Expose behaviour, not fields, from a module that others depend on** — `system_requirement`, status `proposed`

> Fields of a class or module that other modules depend on are private, and clients
> reach state only through methods that express behaviour. Global mutable variables
> are not used. This keeps the representation free to change without touching
> clients.
>
> Check. Any public mutable field, or any module-level mutable state read by other
> modules, is a modification hazard.

*Rationale:* A public field is part of the module's interface. Changing its type or meaning is a modification that ripples to every client that touches it.

*Implements:* UR-0002

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 9 — OCP (heuristics; make all member variables private, no global variables) · **principle**: OCP · **origin**: ai
<!-- tl:end -->

## Liskov Substitution Principle

<!-- tl:item UR-0003 -->
**UR-0003 — Liskov Substitution Principle — subtypes are usable wherever the supertype is expected** — `user_requirement`, status `proposed`

> Any object of a subtype can stand in for an object of its supertype without the
> caller knowing the difference and without the program's correctness changing.
> Substitutability is about behaviour, not shape. A type that shares a supertype's
> method signatures but not its contract is not a subtype, however natural the
> real-world hierarchy looks.

*Rationale:* Every caller of an abstraction relies on its contract. A subtype that bends the contract forces callers to test for the concrete type, which destroys the abstraction and reintroduces the coupling it was meant to remove.

*Derives from:* INT-0001

**source_ref**: Liskov, Data Abstraction and Hierarchy (OOPSLA 1987 keynote); Liskov and Wing, A Behavioral Notion of Subtyping (ACM TOPLAS 16(6), 1994); Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 10 — LSP; Martin, Clean Architecture (2017), ch. 9 — LSP · **principle**: LSP · **origin**: ai
<!-- tl:end -->

<!-- tl:table type == 'system_requirement' and attrs.get('principle') == 'LSP' -->
| UID | Type | Status | Title |
|---|---|---|---|
| SR-0017 | system_requirement | proposed | Design subtypes so callers of the supertype never need to know the concrete type |
| SR-0018 | system_requirement | proposed | Never strengthen a precondition or weaken a postcondition in a subtype |
| SR-0019 | system_requirement | proposed | Preserve the supertype's invariants and its constraints on state history |
| SR-0020 | system_requirement | proposed | Do not throw from a subtype for inputs the supertype's contract accepts |
| SR-0021 | system_requirement | proposed | Never override a method to do nothing or to report that it is unsupported |
| SR-0022 | system_requirement | proposed | Do not test the concrete type of an object to decide how to treat it |
| SR-0023 | system_requirement | proposed | Run the supertype's tests against every implementation |
| SR-0024 | system_requirement | proposed | Apply substitutability to every implementer of an interface, not only to subclasses |
| SR-0025 | system_requirement | proposed | Use composition, not inheritance, when the relationship is reuse rather than substitution |
<!-- tl:end -->

<!-- tl:item SR-0017 -->
**SR-0017 — Design subtypes so callers of the supertype never need to know the concrete type** — `system_requirement`, status `proposed`

> A subtype is usable in any place the supertype is declared, without the caller
> checking which subtype it holds and without the result being wrong. Model IS-A on
> behaviour, not on the real-world taxonomy. A square is a rectangle in geometry, but
> a Square class whose setWidth also changes its height is not a substitute for a
> Rectangle whose callers set width and height independently.
>
> Check. Take each public use of the supertype and run it, in your head or in a test,
> with every subtype. Any use that would need a special case fails this rule.

*Rationale:* The value of an abstraction is that its callers do not care which implementation they hold. A subtype that makes them care removes that value and reintroduces the coupling the abstraction was meant to remove.

*Implements:* UR-0003

**source_ref**: Liskov, Data Abstraction and Hierarchy (OOPSLA 1987 keynote); Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 10 — LSP (the Square and Rectangle example); Martin, Clean Architecture (2017), ch. 9 — LSP · **principle**: LSP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0018 -->
**SR-0018 — Never strengthen a precondition or weaken a postcondition in a subtype** — `system_requirement`, status `proposed`

> A subtype accepts at least every input the supertype accepts, and guarantees at
> least everything the supertype guarantees. It may accept more and promise more; it
> may not demand more or deliver less. Write the supertype's preconditions and
> postconditions down, in documentation or in assertions, so that this can be
> checked.
>
> Check. For each overridden method, compare the conditions. A new argument check
> that rejects a value the supertype allowed is a strengthened precondition. A result
> that can violate a property the supertype promised is a weakened postcondition.

*Rationale:* Callers are written against the supertype's contract. A subtype that demands more breaks callers that supply less; one that delivers less breaks callers that relied on more.

*Implements:* UR-0003

**source_ref**: Liskov and Wing, A Behavioral Notion of Subtyping (ACM TOPLAS 16(6), 1994); Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 10 — LSP (design by contract) · **principle**: LSP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0019 -->
**SR-0019 — Preserve the supertype's invariants and its constraints on state history** — `system_requirement`, status `proposed`

> Every invariant that holds for all objects of the supertype holds for all objects
> of the subtype. A subtype does not introduce a way to change state that the
> supertype rules out. In particular, a mutable subtype of a type that its callers
> treat as immutable violates this rule even if no method signature changes.
>
> Check. List the supertype's invariants and the state transitions it allows. Any
> subtype method that can break an invariant or perform a forbidden transition
> fails.

*Rationale:* Callers reason about objects over time as well as per call. A subtype that can reach states the supertype never could invalidates that reasoning silently.

*Implements:* UR-0003

**source_ref**: Liskov and Wing, A Behavioral Notion of Subtyping (ACM TOPLAS 16(6), 1994) (invariant rule and history constraint) · **principle**: LSP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0020 -->
**SR-0020 — Do not throw from a subtype for inputs the supertype's contract accepts** — `system_requirement`, status `proposed`

> A subtype does not raise an exception, error or failure result for a call the
> supertype's contract accepts, and raises no exception type that the supertype's
> contract does not name. If a subtype cannot honour a method, it is not a subtype of
> that supertype; segregate the interface instead.
>
> Check. For each overridden method, list the error cases. Any new one that a caller
> of the supertype would not be prepared to handle is a violation.

*Rationale:* An unexpected exception is a weakened postcondition delivered at run time. It surfaces as a failure in a caller that was correct against the supertype.

*Implements:* UR-0003

**source_ref**: Liskov and Wing, A Behavioral Notion of Subtyping (ACM TOPLAS 16(6), 1994) (exception rule); Martin, Clean Architecture (2017), ch. 9 — LSP · **principle**: LSP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0021 -->
**SR-0021 — Never override a method to do nothing or to report that it is unsupported** — `system_requirement`, status `proposed`

> A subtype does not override a method with an empty body, a return of a default
> value that ignores the request, or a raised not-supported error. Such a degenerate
> method means the type is being made to pass as something it is not. Fix the type
> hierarchy, by segregating the interface or by using composition, rather than
> silencing the method.
>
> Check. Search overriding methods for empty bodies, unconditional default returns
> and unsupported-operation errors.

*Rationale:* A degenerate override is the most common concrete form of a substitution violation. It hides at compile time and fails in the one caller that relied on the method doing its job.

*Implements:* UR-0003

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 10 — LSP (degenerate functions in derivatives); Martin, Clean Architecture (2017), ch. 9 — LSP · **principle**: LSP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0022 -->
**SR-0022 — Do not test the concrete type of an object to decide how to treat it** — `system_requirement`, status `proposed`

> A caller holding a supertype does not use instanceof, type switches, casts or
> class-name comparisons to give one subtype different treatment. When such a test
> appears to be needed, the hierarchy is wrong. Move the differing behaviour into the
> types, or split the abstraction.
>
> Check. A type test on a value received through an abstraction marks a substitution
> failure to be fixed at the type, not a caller to be patched.

*Rationale:* Each type test couples the caller to the concrete types and must be revisited for every new one. It is the caller compensating for a subtype that does not substitute.

*Implements:* UR-0003

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 10 — LSP; Martin, Clean Architecture (2017), ch. 9 — LSP (the taxi dispatch example) · **principle**: LSP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0023 -->
**SR-0023 — Run the supertype's tests against every implementation** — `system_requirement`, status `proposed`

> The tests that express a supertype's contract run, unchanged, against every
> subtype and every implementation of the interface. Write contract tests once,
> parameterised by implementation, and add each new implementation to the list. A
> subtype that needs the shared tests weakened is not substitutable.
>
> Check. Every implementation of an abstraction appears in that abstraction's
> contract-test run.

*Rationale:* Substitutability is a behavioural property, and behaviour is what tests measure. A shared contract test is the cheapest mechanical proof that a subtype honours the contract its callers rely on.

*Implements:* UR-0003

**source_ref**: Liskov and Wing, A Behavioral Notion of Subtyping (ACM TOPLAS 16(6), 1994); Martin, Clean Architecture (2017), ch. 9 — LSP · **principle**: LSP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0024 -->
**SR-0024 — Apply substitutability to every implementer of an interface, not only to subclasses** — `system_requirement`, status `proposed`

> The rule covers anything that stands in for an abstraction, including plug-ins,
> drivers, adapters, service endpoints behind a common client, and implementations
> of a protocol or schema. An implementer that varies a convention the abstraction
> fixes, such as an argument format or an ordering guarantee, breaks every consumer
> that trusted the abstraction, and the fix will otherwise be a special case in each
> consumer.
>
> Check. For a shared interface across services or modules, confirm that every
> implementer honours the same conventions, not just the same signatures.

*Rationale:* Substitution failures at architectural boundaries are the expensive ones. They turn a clean abstraction into a growing table of special cases that every consumer must maintain.

*Implements:* UR-0003

**source_ref**: Martin, Clean Architecture (2017), ch. 9 — LSP (LSP and architecture; the taxi dispatch example) · **principle**: LSP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0025 -->
**SR-0025 — Use composition, not inheritance, when the relationship is reuse rather than substitution** — `system_requirement`, status `proposed`

> Inherit only when every object of the subtype can be used as an object of the
> supertype. When the goal is to reuse an implementation, or the relationship is
> has-a or uses-a, hold the other object as a field and delegate. A line segment is
> not a substitute for an infinite line, even though it shares most of the code.
>
> Check. Ask whether every caller of the base type would be correct when handed the
> derived type. If not, the derived type wraps the base rather than extending it.

*Rationale:* Inheritance used for reuse produces subtypes that fail substitution, and the failure is discovered by the callers, not by the author.

*Implements:* UR-0003

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 10 — LSP (the Line and LineSegment example) · **principle**: LSP · **origin**: ai
<!-- tl:end -->

## Interface Segregation Principle

<!-- tl:item UR-0004 -->
**UR-0004 — Interface Segregation Principle — no client depends on methods it does not use** — `user_requirement`, status `proposed`

> Clients are not forced to depend on methods they do not call. An interface is
> shaped for the client that uses it, not for the class that implements it. A class
> that serves several kinds of client implements several narrow interfaces, one per
> role, rather than one wide interface that every client must accept whole.

*Rationale:* A client that depends on a wide interface is coupled to every method in it. When a method it never calls changes, the client still has to be rebuilt, retested and redeployed. Narrow interfaces confine each change to the clients that care.

*Derives from:* INT-0001

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 12 — ISP; Martin, Clean Architecture (2017), ch. 10 — ISP · **principle**: ISP · **origin**: ai
<!-- tl:end -->

<!-- tl:table type == 'system_requirement' and attrs.get('principle') == 'ISP' -->
| UID | Type | Status | Title |
|---|---|---|---|
| SR-0026 | system_requirement | proposed | Split an interface so that each client depends only on the methods it calls |
| SR-0027 | system_requirement | proposed | Shape and name each interface from the client's point of view |
| SR-0028 | system_requirement | proposed | Treat a method an implementer cannot honour as a sign the interface is too wide |
| SR-0029 | system_requirement | proposed | Do not let a change for one client force unrelated clients to rebuild or redeploy |
| SR-0030 | system_requirement | proposed | Depend on a narrow adapter rather than on a wide module for a small need |
| SR-0031 | system_requirement | proposed | Combine narrow interfaces by composition when a client needs more than one role |
<!-- tl:end -->

<!-- tl:item SR-0026 -->
**SR-0026 — Split an interface so that each client depends only on the methods it calls** — `system_requirement`, status `proposed`

> When different clients use different subsets of an interface, split it into one
> interface per subset. A class that serves all of them implements all of the
> narrow interfaces. No client is handed methods it does not call.
>
> Check. For each client of an interface, list the methods it calls. If two clients'
> lists differ, the interface is a candidate for segregation along that difference.

*Rationale:* A client depends on the whole of whatever type it names. A change to a method it never calls still forces it to be rebuilt, retested and redeployed.

*Implements:* UR-0004

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 12 — ISP; Martin, Clean Architecture (2017), ch. 10 — ISP · **principle**: ISP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0027 -->
**SR-0027 — Shape and name each interface from the client's point of view** — `system_requirement`, status `proposed`

> Design an interface for the client that uses it, not for the class that implements
> it. Name it for the role the client needs, such as a Reader, a Notifier, a
> PriceSource, rather than for the implementer. Put in it only what that client
> needs to do its job.
>
> Check. Each interface can be explained by pointing at the client that requires it
> and the job that client does with it.

*Rationale:* An interface designed from the implementer's side is a mirror of the implementer and grows with it. One designed from the client's side stays as small as the client's need.

*Implements:* UR-0004

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 12 — ISP (client-specific interfaces); Martin, Clean Architecture (2017), ch. 10 — ISP · **principle**: ISP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0028 -->
**SR-0028 — Treat a method an implementer cannot honour as a sign the interface is too wide** — `system_requirement`, status `proposed`

> When a class implementing an interface must leave a method empty, return a
> meaningless default or raise not-supported, the interface has combined roles that
> do not belong together. Split the interface so that this class implements only the
> roles it can honour.
>
> Check. Search implementations for empty, default-returning or not-supported
> methods. Each is a segregation to make.

*Rationale:* This is where the Interface Segregation and Liskov Substitution principles meet. A fat interface forces implementers to lie about what they support, and the lie is caught by a caller at run time.

*Implements:* UR-0004

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 12 — ISP (interface pollution); Martin, Clean Architecture (2017), ch. 10 — ISP · **principle**: ISP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0029 -->
**SR-0029 — Do not let a change for one client force unrelated clients to rebuild or redeploy** — `system_requirement`, status `proposed`

> Adding or changing a method that one client needs does not touch the interface
> that other clients depend on. If it would, put the new method on a separate
> interface for the client that needs it, or on a new interface that extends the old
> one for that client alone.
>
> Check. Before changing an interface, list its clients. Any client that does not
> need the change but would be affected by it identifies an interface to split.

*Rationale:* Interface pollution spreads: one client's need becomes every client's rebuild, and over time every client is coupled to every other through the interface they share.

*Implements:* UR-0004

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 12 — ISP (interface pollution) · **principle**: ISP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0030 -->
**SR-0030 — Depend on a narrow adapter rather than on a wide module for a small need** — `system_requirement`, status `proposed`

> When a module needs a small part of a library, framework, service or class that
> carries much more, define the small interface the module actually needs and
> implement it with an adapter over the wide dependency. The module names only its
> own narrow interface. This applies to source-level dependencies, build-level
> dependencies and deployment-level dependencies alike.
>
> Check. A module that imports a large dependency and calls one or two operations on
> it should own a narrow interface for those operations instead.

*Rationale:* Depending on something that carries baggage you do not use exposes you to changes, failures and transitive dependencies you did not choose.

*Implements:* UR-0004

**source_ref**: Martin, Clean Architecture (2017), ch. 10 — ISP (ISP and architecture); Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 12 — ISP (the Adapter separation) · **principle**: ISP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0031 -->
**SR-0031 — Combine narrow interfaces by composition when a client needs more than one role** — `system_requirement`, status `proposed`

> A client that needs two roles depends on two interfaces, or on a small interface
> that extends both, rather than on a union interface handed to every other client
> as well. Implementers keep implementing the narrow roles.
>
> Check. Any interface created for one client's convenience that other clients are
> now required to accept has undone a segregation.

*Rationale:* Multiple inheritance of interfaces lets a class present several roles without forcing any single client to see all of them.

*Implements:* UR-0004

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 12 — ISP (separation through multiple inheritance) · **principle**: ISP · **origin**: ai
<!-- tl:end -->

## Dependency Inversion Principle

<!-- tl:item UR-0005 -->
**UR-0005 — Dependency Inversion Principle — depend on abstractions, not on concrete details** — `user_requirement`, status `proposed`

> High-level policy does not depend on low-level detail; both depend on
> abstractions. Abstractions do not depend on details; details depend on
> abstractions. The interface a policy needs is owned by the policy and is
> implemented by the detail, so that the source-code dependency points from the
> detail toward the policy even though control flows the other way.

*Rationale:* Details such as databases, frameworks, transports and file formats change more often than the business rules that use them. When policy names a detail in its source, every change to the detail ripples into the policy. Inverting the dependency makes the detail a plug-in to the policy.

*Derives from:* INT-0001

**source_ref**: Martin, Design Principles and Design Patterns (2000); Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 11 — DIP; Martin, Clean Architecture (2017), ch. 11 — DIP · **principle**: DIP · **origin**: ai
<!-- tl:end -->

<!-- tl:table type == 'system_requirement' and attrs.get('principle') == 'DIP' -->
| UID | Type | Status | Title |
|---|---|---|---|
| SR-0032 | system_requirement | proposed | Keep high-level policy free of imports from low-level detail |
| SR-0033 | system_requirement | proposed | Let the policy own the interface that the detail implements |
| SR-0034 | system_requirement | proposed | Refer to abstractions, not to volatile concrete classes, in source |
| SR-0035 | system_requirement | proposed | Do not construct volatile concrete objects inside policy code |
| SR-0036 | system_requirement | proposed | Do not inherit from or override methods of volatile concrete classes |
| SR-0037 | system_requirement | proposed | Confine knowledge of concrete classes to one composition root |
| SR-0038 | system_requirement | proposed | Point every dependency that crosses an architectural boundary toward the more abstract side |
<!-- tl:end -->

<!-- tl:item SR-0032 -->
**SR-0032 — Keep high-level policy free of imports from low-level detail** — `system_requirement`, status `proposed`

> A module that holds business rules or application policy does not import, name or
> call a database driver, an object-relational mapper, a web framework, a message
> broker client, a file system API, a clock, or any other volatile detail. The policy
> module declares the interfaces it needs, and detail modules implement them.
>
> Check. Read the import list of every policy module. An import of a detail is a
> violation. Framework annotations on policy classes count as imports.

*Rationale:* Details change more often than rules and at other people's request. When the rules import a detail, every change in the detail reopens the rules, and the rules cannot be tested without the detail present.

*Implements:* UR-0005

**source_ref**: Martin, Design Principles and Design Patterns (2000); Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 11 — DIP; Martin, Clean Architecture (2017), ch. 11 — DIP · **principle**: DIP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0033 -->
**SR-0033 — Let the policy own the interface that the detail implements** — `system_requirement`, status `proposed`

> The abstraction a policy depends on is declared in the policy's own module or
> package, named in the policy's terms, and implemented by the detail. The detail
> therefore depends on the policy, and the source-code dependency points opposite to
> the flow of control. Do not have the policy depend on an interface that ships with
> or is shaped by the detail.
>
> Check. Find the interface's file. It lives with the policy that calls it, not with
> the detail that implements it, and it uses the policy's vocabulary.

*Rationale:* An interface owned by the detail changes when the detail changes, so depending on it is still depending on the detail. Ownership by the client is what makes the inversion real.

*Implements:* UR-0005

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 11 — DIP (ownership of the abstraction; the Button and Lamp example); Martin, Clean Architecture (2017), ch. 11 — DIP · **principle**: DIP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0034 -->
**SR-0034 — Refer to abstractions, not to volatile concrete classes, in source** — `system_requirement`, status `proposed`

> In a policy module, no field, parameter, local variable, return type or
> inheritance clause names a volatile concrete class. A volatile class is one under
> active development, one likely to be replaced, or one owned by another team. Stable
> platform types such as strings, collections and numbers are exempt.
>
> Check. Scan declarations in policy modules for concrete class names. Each one that
> is volatile is a dependency to invert.

*Rationale:* Every mention of a concrete class in source is a compile-time dependency on it. Confining those mentions to abstractions is what lets the concrete class change without the policy noticing.

*Implements:* UR-0005

**source_ref**: Martin, Clean Architecture (2017), ch. 11 — DIP (stable abstractions; do not refer to volatile concrete classes) · **principle**: DIP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0035 -->
**SR-0035 — Do not construct volatile concrete objects inside policy code** — `system_requirement`, status `proposed`

> Policy code does not use new, a static create call or a service locator to obtain
> an instance of a volatile concrete class. Instances arrive through constructor
> parameters, through an abstract factory the policy declares, or through the
> composition root. Creating an object couples the creator to the concrete class as
> firmly as calling it.
>
> Check. Search policy modules for construction of concrete classes. Each hit is a
> dependency to move to a factory or the composition root.

*Rationale:* Construction is the hidden dependency. A module that never names a concrete type in a declaration but creates one in a method body still cannot change or test without it.

*Implements:* UR-0005

**source_ref**: Martin, Clean Architecture (2017), ch. 11 — DIP (factories); Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 11 — DIP · **principle**: DIP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0036 -->
**SR-0036 — Do not inherit from or override methods of volatile concrete classes** — `system_requirement`, status `proposed`

> A policy module does not extend a volatile concrete class and does not override its
> concrete methods. Inheritance is the strongest source-code dependency there is, and
> an overridden concrete method inherits the base method's dependencies with it. Make
> the base abstract, or hold the other object and delegate.
>
> Check. Any extends or inherits clause in a policy module that names a concrete,
> volatile class is a violation.

*Rationale:* Overriding a concrete function does not remove the dependency on it; it deepens the coupling by tying the subtype to the base's implementation choices.

*Implements:* UR-0005

**source_ref**: Martin, Clean Architecture (2017), ch. 11 — DIP (do not derive from volatile concrete classes; do not override concrete functions) · **principle**: DIP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0037 -->
**SR-0037 — Confine knowledge of concrete classes to one composition root** — `system_requirement`, status `proposed`

> One component, the composition root or main, knows which concrete classes exist
> and wires them to the abstractions the policy declares. It depends on everything;
> nothing depends on it. It holds no policy. Dependency-injection containers, if
> used, are configured here and nowhere else.
>
> Check. Concrete detail classes are referenced from the composition root and from
> their own tests, and from no other module.

*Rationale:* Concrete dependencies cannot vanish; they can only be collected. Collecting them in one place that nothing depends on means that changing a detail changes that one place and the detail itself.

*Implements:* UR-0005

**source_ref**: Martin, Clean Architecture (2017), ch. 11 — DIP (the concrete component; main) · **principle**: DIP · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0038 -->
**SR-0038 — Point every dependency that crosses an architectural boundary toward the more abstract side** — `system_requirement`, status `proposed`

> Where a boundary separates a higher-level component from a lower-level one, the
> source-code dependency crosses it from the lower-level side toward the higher-level
> side. When control must flow from policy to detail, the policy calls an interface it
> owns and the detail implements it on the other side. No dependency crosses a
> boundary from policy toward detail.
>
> Check. Draw the components and the arrows. An arrow leaving a policy component
> and landing on a detail component is the one to invert.

*Rationale:* A boundary is only as good as the direction of the dependencies across it. One arrow pointing from policy to detail is enough to make the detail unreplaceable.

*Implements:* UR-0005

**source_ref**: Martin, Clean Architecture (2017), ch. 11 — DIP (the dependency rule); Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 11 — DIP (layering; the inverted layers) · **principle**: DIP · **origin**: ai
<!-- tl:end -->

## Applying the principles

<!-- tl:item UR-0006 -->
**UR-0006 — Applying the principles — apply them where change is expected, at every scale** — `user_requirement`, status `proposed`

> The principles are applied in response to a real reason to change, at whatever
> scale that reason appears, and never as a blanket rule. Their symptoms of
> absence are the design smells. Their over-application is itself a smell.

*Rationale:* A design that abstracts every seam is as hard to change as one that abstracts none, because the reader must trace through indirection that protects against nothing. The principles pay for themselves only where a change actually arrives.

*Derives from:* INT-0001

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 7 — What Is Agile Design?; Martin, Clean Architecture (2017), Part III — Design Principles · **principle**: APPLY · **origin**: ai
<!-- tl:end -->

<!-- tl:table type == 'system_requirement' and attrs.get('principle') == 'APPLY' -->
| UID | Type | Status | Title |
|---|---|---|---|
| SR-0039 | system_requirement | proposed | Apply a principle in response to a named reason to change, and record that reason |
| SR-0040 | system_requirement | proposed | Take the first change as it comes, then close the design against the next one of its kind |
| SR-0041 | system_requirement | proposed | Read the design smells as symptoms and name the principle each one points to |
| SR-0042 | system_requirement | proposed | Apply the principles to any unit that can change independently, not only to classes |
| SR-0043 | system_requirement | proposed | Remove an abstraction that no longer earns its indirection |
<!-- tl:end -->

<!-- tl:item SR-0039 -->
**SR-0039 — Apply a principle in response to a named reason to change, and record that reason** — `system_requirement`, status `proposed`

> Each abstraction, split or inversion made in the name of a SOLID principle is
> justified by a specific axis of change or a specific second client, and that
> justification is recorded where the abstraction is declared, in its documentation
> comment or an accompanying design note. An abstraction with no recorded reason is
> removed or justified.
>
> Check. Open the interface or the module boundary. The reason it exists is written
> down and names a change or a client, not a principle.

*Rationale:* Writing the reason down forces the author to have one and lets a later reader tell a load-bearing seam from ceremony. It also makes the design's assumptions reviewable when they turn out wrong.

*Implements:* UR-0006

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 7 — What Is Agile Design?; Martin, Clean Architecture (2017), Part III — Design Principles · **principle**: APPLY · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0040 -->
**SR-0040 — Take the first change as it comes, then close the design against the next one of its kind** — `system_requirement`, status `proposed`

> Do not pre-empt change. Implement the first version as simply as it can correctly
> be. When a change arrives, make it, and while making it, restructure so that the
> next change of the same kind will be an extension rather than an edit. Do not
> restructure against kinds of change that have not yet arrived.
>
> Check. Every seam in the design can name the change that opened it.

*Rationale:* Stimulating change early and responding to it is more reliable than predicting it. The first change is evidence; a prediction is a guess that costs indirection whether or not it comes true.

*Implements:* UR-0006

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 9 — OCP (taking the first bullet; stimulating change) · **principle**: APPLY · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0041 -->
**SR-0041 — Read the design smells as symptoms and name the principle each one points to** — `system_requirement`, status `proposed`

> Treat rigidity (one change forces many), fragility (a change breaks unrelated
> parts), immobility (a part cannot be reused elsewhere), viscosity (the hack is
> easier than the right change), needless complexity, needless repetition and opacity
> as signals. For each smell found, name the principle whose violation produces it
> and fix that, rather than treating the symptom.
>
> Check. A review that reports a smell also reports the principle and the module at
> fault.

*Rationale:* The smells are how violations show themselves in practice. Naming the principle behind a smell turns a complaint into a specific, repeatable refactoring.

*Implements:* UR-0006

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 7 — What Is Agile Design? (design smells: the odours of rotting software) · **principle**: APPLY · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0042 -->
**SR-0042 — Apply the principles to any unit that can change independently, not only to classes** — `system_requirement`, status `proposed`

> A module is any cohesive unit of functions and data that can change on its own,
> including a function, a class, a package, a service and a deployable component.
> Single responsibility, closure against modification, substitutability, narrow
> interfaces and inverted dependencies apply at each of these scales, in the same
> terms.
>
> Check. When reviewing a package or a service boundary, ask the same questions asked
> of a class, such as who changes it, whether a new case is an addition, and which
> way the dependencies point.

*Rationale:* The principles describe how things that change should relate to one another. That is not a property of classes; it is a property of anything that can change.

*Implements:* UR-0006

**source_ref**: Martin, Clean Architecture (2017), ch. 7 — SRP (what a module is); Martin, Clean Architecture (2017), Part III — Design Principles · **principle**: APPLY · **origin**: ai
<!-- tl:end -->

<!-- tl:item SR-0043 -->
**SR-0043 — Remove an abstraction that no longer earns its indirection** — `system_requirement`, status `proposed`

> When the second implementation of an interface is removed, when a split module's
> parts are always changed together, or when an inverted dependency guards a detail
> that has become stable, collapse the structure back. Delete the interface, merge
> the modules or depend on the stable class directly. Leave the recorded reason in
> the commit that removes it.
>
> Check. Periodically list interfaces with one implementation and splits whose parts
> always change together, and justify or remove each.

*Rationale:* Design that outlives its reason is needless complexity. The principles are meant to reduce the cost of change; structure that no longer does that is cost without benefit.

*Implements:* UR-0006

**source_ref**: Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 7 — What Is Agile Design? (needless complexity); Martin, Agile Software Development — Principles, Patterns, and Practices (2002), ch. 9 — OCP · **principle**: APPLY · **origin**: ai
<!-- tl:end -->
