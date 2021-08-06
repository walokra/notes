# CSS

##  CSS System Colors 

See: <https://blog.jim-nielsen.com/2021/css-system-colors/>

```
/* First, declare your dark mode colors */
:root {
  --c-bg: #fff;
  --c-text: #000;
}
@media (prefers-color-scheme: dark) {
  :root {
    --c-bg: #000;
    --c-text: #fff;
  }
}

/* For browsers that don’t support `color-scheme` and therefore
   don't handle system dark mode for you automatically 
   (Firefox), handle it for them. */
@supports not (color-scheme: light dark) {
  html {
    background: var(--c-bg);
    color: var(--c-text);
  }
}

/* For browsers that support automatic dark/light mode
   As well as system colors, set those */
@supports (color-scheme: light dark)
  and (background-color: Canvas)
  and (color: CanvasText) {
  :root {
    --c-bg: Canvas;
    --c-text: CanvasText;
  }
}

/* For Safari on iOS. Hacky, but it works. */
@supports (background-color: -apple-system-control-background)
  and (color: text) {
  :root {
    --c-bg: -apple-system-control-background;
    --c-text: text;
  }
}
```
