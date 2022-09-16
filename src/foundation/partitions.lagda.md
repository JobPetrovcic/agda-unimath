---
title: Partitions
---

```agda
module foundation.partitions where

open import foundation.cartesian-product-types
open import foundation.contractible-types
open import foundation.dependent-pair-types
open import foundation.embeddings
open import foundation.equational-reasoning
open import foundation.equivalences
open import foundation.existential-quantification
open import foundation.fibers-of-maps
open import foundation.functions
open import foundation.functoriality-dependent-pair-types
open import foundation.fundamental-theorem-of-identity-types
open import foundation.homotopies
open import foundation.identity-types
open import foundation.inhabited-subtypes
open import foundation.locally-small-types
open import foundation.propositional-truncations
open import foundation.propositions
open import foundation.sets
open import foundation.small-types
open import foundation.subtype-identity-principle
open import foundation.subtypes
open import foundation.surjective-maps
open import foundation.universe-levels
```

## Idea

A partition of a type `A` is a subset `P` of the type of inhabited subsets of `A` such that for each `a : A` the type

```md
  Σ (Q : inhabited-subtype (A)), P(Q) × Q(a)
```

is contractible. In other words, it is a collection `P` of inhabited subtypes of `A` such that each element of `A` is in a unique inhabited subtype in `P`.

## Definition

```agda
is-partition-Prop :
  {l1 l2 l3 : Level} {A : UU l1} (P : subtype l3 (inhabited-subtype l2 A)) →
  UU-Prop (l1 ⊔ lsuc l2 ⊔ l3)
is-partition-Prop {l1} {l2} {l3} {A} P =
  Π-Prop A
    ( λ x →
      is-contr-Prop
        ( Σ ( type-subtype P)
            ( λ Q → is-in-inhabited-subtype (inclusion-subtype P Q) x)))

is-partition :
  {l1 l2 l3 : Level} {A : UU l1} (P : subtype l3 (inhabited-subtype l2 A)) →
  UU (l1 ⊔ lsuc l2 ⊔ l3)
is-partition P = type-Prop (is-partition-Prop P)

partition :
  {l1 : Level} (l2 l3 : Level) → UU l1 → UU (l1 ⊔ lsuc l2 ⊔ lsuc l3)
partition {l1} l2 l3 A = type-subtype (is-partition-Prop {l1} {l2} {l3} {A})

module _
  {l1 l2 l3 : Level} {A : UU l1} (P : partition l2 l3 A)
  where

  subtype-partition : subtype l3 (inhabited-subtype l2 A)
  subtype-partition = pr1 P

  is-partition-subtype-partition : is-partition subtype-partition
  is-partition-subtype-partition = pr2 P

  is-block-partition : inhabited-subtype l2 A → UU l3
  is-block-partition = is-in-subtype subtype-partition

  is-prop-is-block-partition :
    (Q : inhabited-subtype l2 A) → is-prop (is-block-partition Q)
  is-prop-is-block-partition = is-prop-is-in-subtype subtype-partition
```

We introduce the type of blocks of a partition. However, we will soon be able to reduce the universe level of this type. Therefore we call this type of blocks `large`.

```
  block-partition-Large-Type : UU (l1 ⊔ lsuc l2 ⊔ l3)
  block-partition-Large-Type = type-subtype subtype-partition

  inhabited-subtype-block-partition-Large-Type :
    block-partition-Large-Type → inhabited-subtype l2 A
  inhabited-subtype-block-partition-Large-Type =
    inclusion-subtype subtype-partition

  is-emb-inhabited-subtype-block-partition-Large-Type :
    is-emb inhabited-subtype-block-partition-Large-Type
  is-emb-inhabited-subtype-block-partition-Large-Type =
    is-emb-inclusion-subtype subtype-partition

  emb-inhabited-subtype-block-partition-Large-Type :
    block-partition-Large-Type ↪ inhabited-subtype l2 A
  emb-inhabited-subtype-block-partition-Large-Type =
    emb-subtype subtype-partition

  is-block-inhabited-subtype-block-partition-Large-Type :
    (B : block-partition-Large-Type) →
    is-block-partition (inhabited-subtype-block-partition-Large-Type B)
  is-block-inhabited-subtype-block-partition-Large-Type =
    is-in-subtype-inclusion-subtype subtype-partition

  subtype-block-partition-Large-Type :
    block-partition-Large-Type → subtype l2 A
  subtype-block-partition-Large-Type =
    subtype-inhabited-subtype ∘ inhabited-subtype-block-partition-Large-Type

  is-inhabited-subtype-block-partition-Large-Type :
    (B : block-partition-Large-Type) →
    is-inhabited-subtype (subtype-block-partition-Large-Type B)
  is-inhabited-subtype-block-partition-Large-Type B =
    is-inhabited-subtype-inhabited-subtype
      ( inhabited-subtype-block-partition-Large-Type B)

  type-block-partition-Large-Type : block-partition-Large-Type → UU (l1 ⊔ l2)
  type-block-partition-Large-Type Q =
    type-inhabited-subtype (inhabited-subtype-block-partition-Large-Type Q)

  is-in-block-partition-Large-Type : block-partition-Large-Type → A → UU l2
  is-in-block-partition-Large-Type Q =
    is-in-inhabited-subtype (inhabited-subtype-block-partition-Large-Type Q)

  is-prop-is-in-block-partition-Large-Type :
    (Q : block-partition-Large-Type) (a : A) →
    is-prop (is-in-block-partition-Large-Type Q a)
  is-prop-is-in-block-partition-Large-Type Q =
    is-prop-is-in-inhabited-subtype
      ( inhabited-subtype-block-partition-Large-Type Q)

  large-block-element-partition : A → block-partition-Large-Type
  large-block-element-partition a =
    pr1 (center (is-partition-subtype-partition a))

  is-surjective-large-block-element-partition :
    is-surjective large-block-element-partition
  is-surjective-large-block-element-partition B =
    apply-universal-property-trunc-Prop
      ( is-inhabited-subtype-block-partition-Large-Type B)
      ( trunc-Prop (fib large-block-element-partition B))
      ( λ (a , u) →
        unit-trunc-Prop
          ( pair a
            ( eq-type-subtype
              ( subtype-partition)
              ( ap pr1
                ( ap
                  ( inclusion-subtype
                    ( λ Q → subtype-inhabited-subtype (pr1 Q) a))
                  ( contraction
                    ( is-partition-subtype-partition a)
                    ( pair B u)))))))

  is-locally-small-block-partition-Large-Type :
    is-locally-small (l1 ⊔ l2) block-partition-Large-Type
  is-locally-small-block-partition-Large-Type =
    is-locally-small-type-subtype
      ( subtype-partition)
      ( is-locally-small-inhabited-subtype is-small')
  
  is-small-block-partition-Large-Type :
    is-small (l1 ⊔ l2) block-partition-Large-Type
  is-small-block-partition-Large-Type =
    is-small-is-surjective
      ( is-surjective-large-block-element-partition)
      ( is-small-lmax l2 A)
      ( is-locally-small-block-partition-Large-Type)
```

### The (small) type of blocks of a partition

We will now introduce the type of blocks of a partition, and show that the type of blocks containing a fixed element `a` is contractible. Once this is done, we will have no more use for the large type of blocks of a partition.

```agda
  block-partition : UU (l1 ⊔ l2)
  block-partition = type-is-small is-small-block-partition-Large-Type

  compute-block-partition : block-partition-Large-Type ≃ block-partition
  compute-block-partition = equiv-is-small is-small-block-partition-Large-Type

  map-compute-block-partition : block-partition-Large-Type → block-partition
  map-compute-block-partition = map-equiv compute-block-partition

  make-block-partition :
    (Q : inhabited-subtype l2 A) → is-block-partition Q → block-partition
  make-block-partition Q H = map-compute-block-partition (pair Q H)

  map-inv-compute-block-partition : block-partition → block-partition-Large-Type
  map-inv-compute-block-partition =
    map-inv-equiv compute-block-partition

  issec-map-inv-compute-block-partition :
    ( map-compute-block-partition ∘ map-inv-compute-block-partition) ~ id
  issec-map-inv-compute-block-partition =
    issec-map-inv-equiv compute-block-partition

  isretr-map-inv-compute-block-partition :
    ( map-inv-compute-block-partition ∘ map-compute-block-partition) ~ id
  isretr-map-inv-compute-block-partition =
    isretr-map-inv-equiv compute-block-partition

  inhabited-subtype-block-partition : block-partition → inhabited-subtype l2 A
  inhabited-subtype-block-partition =
    inhabited-subtype-block-partition-Large-Type ∘
    map-inv-compute-block-partition

  is-emb-inhabited-subtype-block-partition :
    is-emb inhabited-subtype-block-partition
  is-emb-inhabited-subtype-block-partition =
    is-emb-comp'
      ( inhabited-subtype-block-partition-Large-Type)
      ( map-inv-compute-block-partition)
      ( is-emb-inhabited-subtype-block-partition-Large-Type)
      ( is-emb-is-equiv (is-equiv-map-inv-equiv compute-block-partition))

  emb-inhabited-subtype-block-partition :
    block-partition ↪ inhabited-subtype l2 A
  pr1 emb-inhabited-subtype-block-partition = inhabited-subtype-block-partition
  pr2 emb-inhabited-subtype-block-partition =
    is-emb-inhabited-subtype-block-partition

  is-block-inhabited-subtype-block-partition :
    (B : block-partition) →
    is-block-partition (inhabited-subtype-block-partition B)
  is-block-inhabited-subtype-block-partition B =
    is-block-inhabited-subtype-block-partition-Large-Type
      ( map-inv-compute-block-partition B)

  subtype-block-partition : block-partition → subtype l2 A
  subtype-block-partition =
    subtype-block-partition-Large-Type ∘ map-inv-compute-block-partition

  is-inhabited-subtype-block-partition :
    (B : block-partition) → is-inhabited-subtype (subtype-block-partition B)
  is-inhabited-subtype-block-partition B =
    is-inhabited-subtype-block-partition-Large-Type
      ( map-inv-compute-block-partition B)

  type-block-partition : block-partition → UU (l1 ⊔ l2)
  type-block-partition B =
    type-block-partition-Large-Type (map-inv-compute-block-partition B)

  is-in-block-partition : (B : block-partition) → A → UU l2
  is-in-block-partition B =
    is-in-block-partition-Large-Type (map-inv-compute-block-partition B)

  is-prop-is-in-block-partition :
    (B : block-partition) (a : A) → is-prop (is-in-block-partition B a)
  is-prop-is-in-block-partition B =
    is-prop-is-in-block-partition-Large-Type
      ( map-inv-compute-block-partition B)

  compute-is-in-block-partition :
    (B : inhabited-subtype l2 A) (H : is-block-partition B) (x : A) →
    is-in-inhabited-subtype B x ≃
    is-in-block-partition (make-block-partition B H) x
  compute-is-in-block-partition B H x =
    equiv-tr
      ( λ C → is-in-block-partition-Large-Type C x)
      ( inv (isretr-map-inv-compute-block-partition (B , H)))

  make-is-in-block-partition :
    (B : inhabited-subtype l2 A) (H : is-block-partition B) (x : A) →
    is-in-inhabited-subtype B x →
    is-in-block-partition (make-block-partition B H) x
  make-is-in-block-partition B H x K =
    map-equiv (compute-is-in-block-partition B H x) K

  block-containing-element-partition : A → UU (l1 ⊔ l2)
  block-containing-element-partition a =
    Σ block-partition (λ B → is-in-block-partition B a)

  is-contr-block-containing-element-partition :
    (a : A) → is-contr (block-containing-element-partition a)
  is-contr-block-containing-element-partition a =
    is-contr-equiv'
      ( Σ block-partition-Large-Type
        ( λ B → is-in-block-partition-Large-Type B a))
      ( equiv-Σ
        ( λ B → is-in-block-partition B a)
        ( compute-block-partition)
        ( λ B →
          equiv-tr
            ( λ C → is-in-block-partition-Large-Type C a)
            ( inv (isretr-map-inv-compute-block-partition B))))
      ( is-partition-subtype-partition a)

  center-block-containing-element-partition :
    (a : A) → block-containing-element-partition a
  center-block-containing-element-partition a =
    center (is-contr-block-containing-element-partition a)

  class-partition : A → block-partition
  class-partition a =
    pr1 (center-block-containing-element-partition a)

  is-block-class-partition :
    (a : A) →
    is-block-partition (inhabited-subtype-block-partition (class-partition a))
  is-block-class-partition a =
    is-block-inhabited-subtype-block-partition (class-partition a)

  is-in-block-class-partition :
    (a : A) → is-in-block-partition (class-partition a) a
  is-in-block-class-partition a =
    pr2 (center-block-containing-element-partition a)
```

## Properties

### Characterizing equality of partitions

```agda
module _
  {l1 l2 l3 : Level} {A : UU l1} (P : partition l2 l3 A)
  where

  has-same-blocks-partition :
    {l4 : Level} (Q : partition l2 l4 A) → UU (l1 ⊔ lsuc l2 ⊔ l3 ⊔ l4)
  has-same-blocks-partition Q =
    has-same-elements-subtype (subtype-partition P) (subtype-partition Q)

  refl-has-same-blocks-partition : has-same-blocks-partition P
  refl-has-same-blocks-partition =
    refl-has-same-elements-subtype (subtype-partition P)

  extensionality-partition :
    (Q : partition l2 l3 A) → (P ＝ Q) ≃ has-same-blocks-partition Q
  extensionality-partition =
    extensionality-type-subtype
      ( is-partition-Prop)
      ( is-partition-subtype-partition P)
      ( refl-has-same-elements-subtype (subtype-partition P))
      ( extensionality-subtype (subtype-partition P))

  eq-has-same-blocks-partition :
    (Q : partition l2 l3 A) → has-same-blocks-partition Q → P ＝ Q
  eq-has-same-blocks-partition Q =
    map-inv-equiv (extensionality-partition Q)
```

### Characterizing equality of blocks of partitions

```agda
module _
  {l1 l2 l3 : Level} {A : UU l1} (P : partition l2 l3 A) (B : block-partition P)
  where

  has-same-elements-block-partition-Prop :
    block-partition P → UU-Prop (l1 ⊔ l2)
  has-same-elements-block-partition-Prop C =
    has-same-elements-inhabited-subtype-Prop
      ( inhabited-subtype-block-partition P B)
      ( inhabited-subtype-block-partition P C)

  has-same-elements-block-partition :
    block-partition P → UU (l1 ⊔ l2)
  has-same-elements-block-partition C =
    has-same-elements-inhabited-subtype
      ( inhabited-subtype-block-partition P B)
      ( inhabited-subtype-block-partition P C)

  is-prop-has-same-elements-block-partition :
    (C : block-partition P) → is-prop (has-same-elements-block-partition C)
  is-prop-has-same-elements-block-partition C =
    is-prop-has-same-elements-inhabited-subtype
      ( inhabited-subtype-block-partition P B)
      ( inhabited-subtype-block-partition P C)

  refl-has-same-elements-block-partition :
    has-same-elements-block-partition B
  refl-has-same-elements-block-partition =
    refl-has-same-elements-inhabited-subtype
      ( inhabited-subtype-block-partition P B)

  is-contr-total-has-same-elements-block-partition :
    is-contr
      ( Σ (block-partition P) has-same-elements-block-partition)
  is-contr-total-has-same-elements-block-partition =
    is-contr-equiv'
      ( Σ ( block-partition P)
          ( λ C →
            inhabited-subtype-block-partition P B ＝
            inhabited-subtype-block-partition P C))
      ( equiv-tot
        ( λ C →
          extensionality-inhabited-subtype
            ( inhabited-subtype-block-partition P B)
            ( inhabited-subtype-block-partition P C)))
      ( fundamental-theorem-id'
        ( λ C → ap (inhabited-subtype-block-partition P))
        ( is-emb-inhabited-subtype-block-partition P B))

  has-same-elements-eq-block-partition :
    (C : block-partition P) → (B ＝ C) →
    has-same-elements-block-partition C
  has-same-elements-eq-block-partition .B refl =
    refl-has-same-elements-block-partition

  is-equiv-has-same-elements-eq-block-partition :
    (C : block-partition P) →
    is-equiv (has-same-elements-eq-block-partition C)
  is-equiv-has-same-elements-eq-block-partition =
    fundamental-theorem-id
      is-contr-total-has-same-elements-block-partition
      has-same-elements-eq-block-partition

  extensionality-block-partition :
    (C : block-partition P) →
    (B ＝ C) ≃ has-same-elements-block-partition C
  pr1 (extensionality-block-partition C) =
    has-same-elements-eq-block-partition C
  pr2 (extensionality-block-partition C) =
    is-equiv-has-same-elements-eq-block-partition C

  eq-has-same-elements-block-partition :
    (C : block-partition P) →
    has-same-elements-block-partition C → B ＝ C
  eq-has-same-elements-block-partition C =
    map-inv-equiv (extensionality-block-partition C)
```

### The type of blocks of a partition is a set

```agda
module _
  {l1 l2 l3 : Level} {A : UU l1} (P : partition l2 l3 A)
  where

  is-set-block-partition : is-set (block-partition P)
  is-set-block-partition B C =
    is-prop-equiv
      ( extensionality-block-partition P B C)
      ( is-prop-has-same-elements-block-partition P B C)

  block-partition-Set : UU-Set (l1 ⊔ l2)
  pr1 block-partition-Set = block-partition P
  pr2 block-partition-Set = is-set-block-partition
```

### Two blocks of a partition are equal if they share a common element

```agda
module _
  {l1 l2 l3 : Level} {A : UU l1} (P : partition l2 l3 A)
  (B : block-partition P)
  where

  share-common-element-block-partition-Prop :
    (C : block-partition P) → UU-Prop (l1 ⊔ l2)
  share-common-element-block-partition-Prop C =
    ∃-Prop A
      ( λ a →
        is-in-block-partition P B a × is-in-block-partition P C a)

  share-common-element-block-partition :
    (C : block-partition P) → UU (l1 ⊔ l2)
  share-common-element-block-partition C =
    type-Prop (share-common-element-block-partition-Prop C)

  eq-share-common-element-block-partition :
    (C : block-partition P) → share-common-element-block-partition C → B ＝ C
  eq-share-common-element-block-partition C H =
    apply-universal-property-trunc-Prop H
      ( Id-Prop (block-partition-Set P) B C)
      ( λ (a , K , L) →
        ap
          ( pr1)
          ( eq-is-contr
            ( is-contr-block-containing-element-partition P a)
            { B , K}
            { C , L}))
```