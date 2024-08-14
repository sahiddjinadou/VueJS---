# Vue ++

## les comptenus de slot
Nous avons appris que les composants peuvent accepter des props, qui peuvent être des valeurs JavaScript de n'importe quel type. Mais qu'en est-il du contenu du template ? Dans certains cas, nous pouvons vouloir transmettre un fragment de template à un composant enfant et laisser le composant enfant afficher le fragment dans son propre template.

#### Exemple
```js
//le composant accepte un template en son sein
<FancyButton>
  Click me! <!-- contenu du slot -->
</FancyButton>

//le composant ressemble à ceci
<button class="fancy-btn">
  <slot></slot> <!-- emplacement du slot -->
</button>
```

L'élément <slot> est un emplacement du slot qui indique où le contenu du slot fourni par le parent doit être affiché.

### Portée du rendu
Le contenu du slot a accès à la portée des données du composant parent, car il est défini dans le parent. Par exemple :
```js
<span>{{ message }}</span>
<FancyButton>{{ message }}</FancyButton>
```
Le contenu du slot n'a pas accès aux données du composant enfant. Les expressions dans les templates Vue ne peuvent accéder qu'à la portée de déclaration dans laquelle elles sont définies, conformément à la portée lexicale de JavaScript.
### Contenu par défaut
Il existe des cas où il est utile de spécifier un contenu par défaut pour un slot, à rendre uniquement lorsqu'aucun contenu n'est fourni.
Nous pourrions souhaiter que le texte "Submit" soit rendu à l'intérieur du  `<button>` si le parent n'a fourni aucun contenu pour le slot. Pour faire de "Submit" le contenu par défaut, nous pouvons le placer entre les balises  `<slot>`
```js
<button type="submit">
  <slot>
    Submit <!-- contenu par défaut -->
  </slot>
</button>
```
### Slots nommés
Prenons ce code ci .
```js
<div class="container">
  <header>
    <!-- Nous voulons le contenu du header ici -->
  </header>
  <main>
    <!-- Nous voulons le contenu principal ici -->
  </main>
  <footer>
    <!-- Nous voulons le contenu du footer ici -->
  </footer>
</div>
```

Dans ces cas, l'élément <slot> a un attribut spécial, name, qui peut être utilisé pour attribuer un ID unique à différents slots afin que vous puissiez déterminer où le contenu doit être affiché :
```js
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

Pour passer un slot nommé, nous devons utiliser un élément `<template>` avec la directive `v-slot`, puis passer le nom du slot comme argument à `v-slot` :

```js
//très astucieux de savoir  que les deux synthaxes s'équivalent
<BaseLayout>
  <template v-slot:header>
    <!-- contenu pour le slot header -->
  </template>
  <template #footer>
    <!-- contenu pour le slot header -->
  </template>
</BaseLayout>
```
n'oublions pas les valeurs par defaut ont aussi leut sens ici.

### Slots conditionnels
Il arrive que l'on veuille rendre quelque chose en fonction de la présence ou non d'un slot.
Vous pouvez utiliser la propriété `$slots` en combinaison avec un v-if pour y parvenir.

```js
<template>
  <div class="card">
    <div v-if="$slots.header" class="card-header">
      <slot name="header" />
    </div>

    <div v-if="$slots.default" class="card-content">
      <slot />
    </div>

    <div v-if="$slots.footer" class="card-footer">
      <slot name="footer" />
    </div>
  </div>
</template>
```

### Noms de slot dynamiques
Les arguments de directive dynamique fonctionnent également sur `v-slot`, permettant la définition de noms de slots dynamiques :

```js
<base-layout>
  <template v-slot:[dynamicSlotName]>
    ...
  </template>

  <!-- avec syntaxe abrégée -->
  <template #[dynamicSlotName]>
    ...
  </template>
</base-layout>
```

### Scoped Slots
Des fois, il existe des cas où il peut être utile que le contenu d'un slot puisse utiliser des données provenant à la fois de la portée du parent et de la portée de l'enfant. Pour y parvenir, nous avons besoin d'un moyen pour l'enfant de transmettre des données à un slot pour son affichage.
En fait, nous pouvons faire exactement cela - nous pouvons transmettre des attributs à un emplacement de slot comme on transmettrait des props à un composant :


```ts
//sans avoir à emettre un evenement la donnée de l'enfant passe au parent
<!-- template de <MyComponent> -->
<div>
  <slot :text="greetingMessage" :count="1"></slot>
</div>
```
### Scoped slots nommés
Les Scoped slots nommés fonctionnent de la même manière - les props du slot sont accessibles en tant que valeur de la directive `v-slot` : `v-slot:name="slotProps"`. Lorsque vous utilisez le raccourci, cela ressemble à ceci :

```js
<MyComponent>
  <template #header="headerProps">
    {{ headerProps }}
  </template>

  <template #default="defaultProps">
    {{ defaultProps }}
  </template>

  <template #footer="footerProps">
    {{ footerProps }}
  </template>
</MyComponent>
```

## Provide / Inject
### Passage de props en profondeur (Prop Drilling)
Habituellement, lorsque nous devons transmettre des données du parent à un composant enfant, nous utilisons des props.Cependant, imaginez le cas où nous avons un arbre de composants important, et qu'un composant profondément imbriqué aurait besoin d'accéder à des informations d'un de ses composants parents. Avec seulement des props, nous devrions passer la même prop sur toute la chaîne composants parents :
![alt text](prop-drilling.XJXa8UE-.png)

Notez que bien que le composant `<Footer>` n'utilise pas du tout ces props, il doit malgré tout les déclarer et les transmettre uniquement afin que `<DeepChild>` puisse y accéder. S'il y a une chaîne de composants parents plus longue, encore plus de composants seront affectés par le problème. C'est ce qu'on appelle le `"props drilling"` et ce n'est certainement pas amusant à gérer.

Nous pouvons résoudre le "props drilling" avec `provide` and `inject`. Un composant parent peut servir de fournisseur de dépendances pour tous ses descendants. Tout composant enfant de l'arborescence, quelle que soit sa profondeur, peut injecter des dépendances fournies par des composants présent dans sa chaîne de composants parents.
![alt text](provide-inject.C0gAIfVn.png)

### Provide
Pour fournir des données aux descendants d'un composant, utilisez la fonction provide() :

```js
<script setup>
import { provide } from 'vue'

provide(/* clé */ 'message', /* valeur */ 'hello!')
</script>
```

Si vous n'utilisez pas`<script setup>`, assurez-vous que `provide()` est appelé de manière synchrone dans `setup()` :

### Provide au niveau de l'application
En plus de fournir des données dans un composant, nous pouvons également fournir des données au niveau de l'application :
```js
import { createApp } from 'vue'

const app = createApp({})

app.provide(/* clé */ 'message', /* valeur */ 'hello!')
```

Les données fournies au niveau de l'application sont disponibles pour tous les composants rendus dans l'application

## Inject
Pour injecter des données fournies par un composant parent, utilisez la fonction `inject()` :

```js
<script setup>
import { inject } from 'vue'

const message = inject('message')
</script>
```

#### Valeurs par défaut lors de l'injection
Par défaut, inject suppose que la clé injectée est fournie quelque part dans la chaîne de composants parents. Dans le cas où la clé n'est pas fournie, il y aura un message d'avertissement lors de l'exécution.

Si nous voulons faire fonctionner une propriété injectée avec des fournisseurs facultatifs, nous devons déclarer une valeur par défaut, de la même manière qu'avec les props :
```js
// `value` sera "default value"
// si aucune donnée correspondant à la clé "message" n'a été fournie
const value = inject('message', 'default value')
```
