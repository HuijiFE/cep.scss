# CEP.scss

> CEP: Component, Element and Property, a methodology that help you styling single file component.

## Installation

```bash
yarn add -D cep.scss

# or use npm

npm i -D cep.scss
```

And import it into the index scss file in your project.

```scss
@import 'cep.scss';
```

## Usage / Core

The CEP provides a series of mixins and functions to help you writing stylesheet for components, these tools can auto generate specified selector blocks and convert different units.

- core mixin： `comp()`, `elem()`, `prop()`, `when()`
- core function: `rem()`

---

### Namespace `<ns>-*`

The css class name prefix for components, controlled by global variable `$namespace`.

```scss
@import 'cep.scss';

// there is a ui library 'huiji-ui', its namespace is `hj`
$namespace: 'hj';
@import 'src/huiji-ui/button.scss';
@import 'src/huiji-ui/list.scss';
@import 'src/huiji-ui/card.scss';
@import 'src/huiji-ui/layout.scss';

// for the common service components, you can use `c` as their namespace.
$namespace: 'c';
@import 'src/components/header.scss';
@import 'src/components/footer.scss';
@import 'src/components/sidebar.scss';
@import 'src/components/navbar.scss';

// for the views components, you can use `v` as their namespace.
$namespace: 'v';
@import 'src/views/home.scss';
@import 'src/views/user.scss';
@import 'src/views/about.scss';
@import 'src/views/article.scss';
```

---

### Component `<ns>-<component>`

A component has its own css class name.

```html
<button class="hj-button"><!----></button>
```

```scss
@include comp('button') {
  // style for .hj-button
}
```

The mixin `comp($name)` will auto generate a selector at root:

- `.hj-button`.

---

### Element `<ns>-<component>_<element>`

The CEP will combine the component name and the element name with single underline as css class name for elements.

```html
<button class="hj-button">
  <i class="hj-button_icon"></i>
  <span class="hj-button_content">{{content}}</span>
</button>
```

```scss
@include comp('button') {
  // style for .hj-button

  @include elem('icon', 'content') {
    // shared style for the element icon and content
  }

  @include elem('icon') {
    // style for the element icon
  }

  @include elem('content') {
    // style for the element content
  }
}
```

The mixin `elem($name-list...)` will auto generate selectors:

- `.hj-button .hj-button_icon, .hj-button .hj-button_content`
- `.hj-button .hj-button_icon`
- `.hj-button .hj-button_content`.

> Note: don't nest elements block in scss if not necessary.

---

### Property `<ns>p-<name>_<value>`

For a component, control style by properties of the component, use namespace with a letter 'p' as prefix, combine the property name and the value with single underline.

```html
<button class="hj-button hjp-theme_light"><!----></button>
<button class="hj-button hjp-theme_dark hjp-size_large"><!----></button>
```

```scss
$foreground-color-map: (
  light: black,
  dark: white
);
$background-color-map: (
  light: white,
  dark: black
);

@include comp('button') {
  // style for .hj-button

  @each $theme in ('light', 'dark') {
    @include prop(
      (
        'theme': $theme
      )
    ) {
      color: map-get($map: $foreground-color-map, $key: $theme);
      background-color: map-get($map: $background-color-map, $key: $theme);

      @include elem('icon') {
        // style for the element icon under the different theme.
      }
    }

    @include prop(
      (
        'theme': $theme,
        'size': 'large'
      )
    ) {
      // style when the the theme is specified and the size is 'large'
    }
  }
}
```

The mixin `prop($name-value-map-list...)` will auto generate selectors:

- `.hj-button.hjp-theme_light`
- `.hj-button.hjp-theme_dark`
- `.hj-button.hjp-theme_light.hjp-size_large`
- `.hj-button.hjp-theme_dark.hjp-size_large`

> Note: the `prop` is only for components, not for elements, usually used with `@each` and `map`.

---

### status `is-<status>`

For components or elements, control style by boolean value.

```html
<button class="hj-button is-raise"><!----></button>
<button class="hj-button is-raise is-active"><!----></button>
```

```scss
@include comp('button') {
  // style for .hj-button

  @include when('raise') {
    // ...
  }

  @include when('raise', 'active') {
    // ...
  }

  @include when-or('raise', 'active') {
    // ...
  }
}
```

The mixin `when($status-list...)` will auto generate selectors:

- `.hj-button.is-raise`
- `.hj-button.is-raise.is-active`

Them mixin `when-or($status-list...)` will auto generate selectors:

- `.hj-button.is-raise, .hj-button.is-active`

> `when` and `when-or` can be used under both of components and elements.

---

### `rem($pixel)`

The function `rem($pixel)` will convert a pixel number to rem number according to the global variable `$root-font-size`, append the unit `rem` to the number .

```scss
$root-font-size: 100;

:root {
  font-size: $root-font-size;
}

@include comp('foo') {
  font-size: rem(16); // will be convert to 0.16rem
}
```

Mobile devices

```scss
$root-font-size: 320;

:root {
  font-size: 100vw;
}

@include comp('bar') {
  // In any different size devices
  // the width of `bar` will always be 10% of the screen width
  width: rem(32);
}
```

> Note: you need write the `font-size` in the `:root` selector yourself

---

### Others

More mixins and functions, like `atom`, please checkout the [source code](./cep.scss).

Snippets for VSCode: [scss.json](./scss.json)

## Projects using CEP

- [void-ui](https://github.com/HuijiFE/void-ui)
