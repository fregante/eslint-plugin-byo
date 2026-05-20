# eslint-plugin-byo

> Like `no-restricted-syntax` but composable, shareable, and individually togglable.

ESLint comes with a customizable [`no-restricted-syntax`](https://eslint.org/docs/latest/rules/no-restricted-syntax) rule that lets you create rules on the fly. Unfortunately this is _all or nothing_, you cannot set certain rules as "warning" or limit others to certain folders.

`eslint-plugin-byo` (Bring Your Own) solves this issue and more:

- each rule has its own name
- each rule can be set as a warning
- each rule can be subsequently disabled via config or `eslint-disable` directives

The last point comes particularly handy because `// eslint-disable no-restricted-syntax` would disable _all_ of your rules at once, whereas now you can use `// eslint-disable byo/your-rule-name,byo/your-other-rule` as needed. And the comment now mentions _which rule_ was disabled, rather than a generic `no-restricted-syntax`

## Install

```sh
npm install --save-dev eslint-plugin-byo
```

## Usage

```js
import byo from 'eslint-plugin-byo';

export default [{
	plugins: {
		byo,
	},
	rules: {
		'byo/pick-any-name': ['error', {
			selector: "MemberExpression[object.name='window']",
			message: 'Use the shared abstraction instead of window directly',
		}],
	},
}];
```

### More examples

```js
import byo from 'eslint-plugin-byo';

export default [
	{
		plugins: {
			byo,
		},
		rules: {
			// Specify a custom rule name, set it as a warning. The config object matches `no-restricted-syntax`
			'byo/no-direct-window-access': ['warn', {
				selector: "MemberExpression[object.name='window']",
				message: 'Use the shared abstraction instead of window directly',
			}],

			// Rules can have multiple selectors under one name
			// Set just one rule as an error, impossible with `no-restricted-syntax` (all "warn" or all "error")
			'byo/no-console-log': ['error', {
				selector: "CallExpression[callee.object.name='console'][callee.property.name='log']",
				message: 'Do not ship console.log',
			}, {
				selector: "CallExpression[callee.object.name='console'][callee.property.name='warn']",
				message: 'Do not ship console.warn',
			}],
		},
	},
	{
		files: ['tests/**'],
		rules: {
			// Disable individual rules in specific folders. Impossible with `no-restricted-syntax`
			'byo/no-console-log': 'off',
		},
	},
	{
		files: ['src/legacy/**'],
		rules: {
			// Create rules for specific folders. Impossible/verbose/error-prone with `no-restricted-syntax`
			'byo/no-direct-window-access': ['warn', {
				selector: "MemberExpression[object.name='window']",
				message: 'Avoid new usages of window',
			}],
		},
	},
];
```
