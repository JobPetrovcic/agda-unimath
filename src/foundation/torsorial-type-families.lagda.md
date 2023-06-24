# Torsorial type families

```agda
module foundation.torsorial-type-families where
```

<details><summary>Imports</summary>

```agda
open import foundation.contractible-types
open import foundation.dependent-pair-types
open import foundation.equivalences
open import foundation.fundamental-theorem-of-identity-types
open import foundation.identity-types
open import foundation.logical-equivalences
open import foundation.propositional-maps
open import foundation.propositions
open import foundation.type-theoretic-principle-of-choice
open import foundation.universal-property-identity-types
open import foundation.universe-levels
```

</details>

## Idea

A type family `E` over `B` is said to be **torsorial** if its
[total space](foundation.dependent-pair-types.md) is
[contractible](foundation.contractible-types.md). By the
[fundamental theorem of identity types](foundation.fundamental-theorem-of-identity-types.md)
it follows that a type family `E` is torsorial if and only if it is in the
[image](foundation.images.md) of `Id : B → (B → 𝒰)`.

## Definition

### The predicate of being a torsorial type family over `B`

```agda
is-torsorial-Prop :
  {l1 l2 : Level} {B : UU l1} → (B → UU l2) → Prop (l1 ⊔ l2)
is-torsorial-Prop E = is-contr-Prop (Σ _ E)

is-torsorial :
  {l1 l2 : Level} {B : UU l1} → (B → UU l2) → UU (l1 ⊔ l2)
is-torsorial E = type-Prop (is-torsorial-Prop E)

is-prop-is-torsorial :
  {l1 l2 : Level} {B : UU l1} (E : B → UU l2) → is-prop (is-torsorial E)
is-prop-is-torsorial E = is-prop-type-Prop (is-torsorial-Prop E)
```

### The type of torsorial type families over `B`

```agda
torsorial-family-of-types :
  {l1 : Level} (l2 : Level) → UU l1 → UU (l1 ⊔ lsuc l2)
torsorial-family-of-types l2 B = Σ (B → UU l2) is-torsorial

module _
  {l1 l2 : Level} {B : UU l1} (T : torsorial-family-of-types l2 B)
  where

  type-torsorial-family-of-types : B → UU l2
  type-torsorial-family-of-types = pr1 T

  is-torsorial-torsorial-family-of-types :
    is-torsorial type-torsorial-family-of-types
  is-torsorial-torsorial-family-of-types = pr2 T
```

## Properties

#### `fib Id B ≃ is-contr (Σ A B)` for any type family `B` over `A`

In other words, a type family `B` over `A` is in the
[image](foundation.images.md) of `Id : A → (A → 𝒰)` if and only if `B` is
torsorial. Since being contractible is a
[proposition](foundation.propositions.md), this observation leads to an
alternative proof of the above claim that `Id : A → (A → 𝒰)` is an
[embedding](foundation.embeddings.md). Our previous proof of the fact that
`Id : A → (A → 𝒰)` is an embedding can be found in
[`foundation.universal-property-identity-types`](foundation.universal-property-identity-types.md).

```agda
module _
  {l1 l2 : Level} {A : UU l1} {B : A → UU l2}
  where

  is-torsorial-fib-Id :
    {a : A} → ((x : A) → (a ＝ x) ≃ B x) → is-torsorial B
  is-torsorial-fib-Id H =
    fundamental-theorem-id'
      ( λ x → map-equiv (H x))
      ( λ x → is-equiv-map-equiv (H x))

  fib-Id-is-torsorial :
    is-torsorial B → Σ A (λ a → (x : A) → (a ＝ x) ≃ B x)
  pr1 (fib-Id-is-torsorial ((a , b) , H)) = a
  pr2 (fib-Id-is-torsorial ((a , b) , H)) =
    map-inv-distributive-Π-Σ (f , fundamental-theorem-id ((a , b) , H) f)
    where
    f : (x : A) → (a ＝ x) → B x
    f x refl = b

  compute-fib-Id :
    (Σ A (λ a → (x : A) → (a ＝ x) ≃ B x)) ≃ is-contr (Σ A B)
  compute-fib-Id =
    equiv-iff
      ( Σ A (λ a → (x : A) → (a ＝ x) ≃ B x) ,
        is-prop-total-family-of-equivalences-Id)
      ( is-contr-Prop (Σ A B))
      ( λ u → is-torsorial-fib-Id (pr2 u))
      ( fib-Id-is-torsorial)
```