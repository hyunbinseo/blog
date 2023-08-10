# JavaScript object property order

Check if `Object.keys()` matches insertion order. Limited to string keys.

## Works in

```js
Object.keys({ z: 0, a: 1 }).join('') === 'za';
```

| OS    | Platform | Version                           |
| ----- | -------- | --------------------------------- |
| macOS | Chrome   | 115.0.5790.114(공식 빌드) (arm64) |
| macOS | Firefox  | 116.0.2 (64-비트)                 |
| macOS | Safari   | 16.6(18615.3.12.11.2)             |
| macOS | Node.js  | 18.16.1                           |

## Reference

- https://github.com/tc39/proposal-for-in-order
- https://stackoverflow.com/a/23202095/12817553
