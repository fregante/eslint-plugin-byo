# eslint-plugin-byo

Like `no-restricted-syntax` but composable, shareable, and individually togglable.

## Install

```sh
npm install --save-dev eslint-plugin-byo
```

## Usage

```js
import byo from 'eslint-plugin-byo';

export default [
	{
		plugins: {
			byo,
		},
		rules: {
			'byo/no-direct-window-access': ['warn', {
				selector: "MemberExpression[object.name='window']",
				message: 'Use the shared abstraction instead of window directly',
			}],
			'byo/no-console-log': ['error', {
				selector: "CallExpression[callee.object.name='console'][callee.property.name='log']",
				message: 'Do not ship console.log',
			}],
		},
	},
	{
		files: ['tests/**'],
		rules: {
			'byo/no-console-log': 'off',
		},
	},
	{
		files: ['src/legacy/**'],
		rules: {
			'byo/no-direct-window-access': ['warn', {
				selector: "MemberExpression[object.name='window']",
				message: 'Avoid new usages of window',
			}],
		},
	},
];
```
