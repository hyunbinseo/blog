# Disable modal backdrop scroll

When a modal is open, the background content should not scroll. This behavior has to be manually implemented, even when using the `<dialog>` element.

```typescript
export const disableBodyScroll = (disable: boolean) => {
  if (disable) document.body.style.overflowY = 'hidden';
  if (!disable) document.body.style.overflowY = '';
};
```

```svelte
<script lang="ts">
  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  const handleDialog = (el: HTMLDialogElement) => ({
    // e.g. goto() caused the dialog to unmount.
    destroy: () => disableBodyScroll(false),
  });
</script>

<button
  on:click={() => {
    disableBodyScroll(true);
    dialog.showModal();
  }}
>
  Show Modal
</button>
<dialog use:handleDialog on:close={() => disableBodyScroll(false)}>
  <!-- Dialog Content -->
</dialog>
```

Note that there is no open event for `<dialog>` elements — only [cancel] and [close] are provided.

[cancel]: https://developer.mozilla.org/en-US/docs/Web/API/HTMLDialogElement/cancel_event
[close]: https://developer.mozilla.org/en-US/docs/Web/API/HTMLDialogElement/close_event

## Safari (iOS, iPadOS)

However, hiding the `<body>` element's over-scroll is not sufficient in Safari. The dialog backdrop's [touch-action] or panning gestures should also be disabled.

[touch-action]: https://developer.mozilla.org/en-US/docs/Web/CSS/touch-action

```css
/* Works in iOS Safari 16.6. */
dialog[open]::backdrop {
  touch-action: none;
}
```

For non-`<dialog>` usage, setting `<body>` to `fixed` and giving a `top` value works. [Reference](https://slog.website/post/16)
