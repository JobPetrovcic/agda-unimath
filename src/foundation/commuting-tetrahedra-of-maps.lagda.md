# Commuting tetrahedra of maps

```agda
module foundation.commuting-tetrahedra-of-maps where
```

<details><summary>Imports</summary>

```agda
open import foundation.universe-levels

open import foundation-core.commuting-triangles-of-maps
open import foundation-core.homotopies
open import foundation-core.whiskering-homotopies
```

</details>

## Idea

A {{#concept "commuting tetrahedron of maps" Agda=coherence-tetrahedron-maps}}
is a commuting diagram of the form

```text
  A ----------> B
  |  \       ^  |
  |    \   /    |
  |      /      |
  |    /   \    |
  V  /       v  V
  X ----------> Y.
```

## Definition

```agda
module _
  {l1 l2 l3 l4 : Level} {A : UU l1} {B : UU l2} {X : UU l3} {Y : UU l4}
  (top : A → B) (left : A → X) (right : B → Y) (bottom : X → Y)
  (diagonal-up : X → B) (diagonal-down : A → Y)
  (upper-left : coherence-triangle-maps top diagonal-up left)
  (lower-right : coherence-triangle-maps bottom right diagonal-up)
  (upper-right : coherence-triangle-maps diagonal-down right top)
  (lower-left : coherence-triangle-maps diagonal-down bottom left)
  where

  coherence-tetrahedron-maps : UU (l1 ⊔ l4)
  coherence-tetrahedron-maps =
    ( upper-right ∙h (right ·l upper-left)) ~
    ( lower-left ∙h (lower-right ·r left))

  coherence-tetrahedron-maps' : UU (l1 ⊔ l4)
  coherence-tetrahedron-maps' =
    ( lower-left ∙h (lower-right ·r left)) ~
    ( upper-right ∙h (right ·l upper-left))
```