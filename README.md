# @harperdb/nextjs

A HarperDB Component for running (and developing) Next.js apps.

Most Next.js features are supported as we rely on the Next.js Server provided by Next.js to run your application.

## Usage

1. Install:

```sh
npm install @harperdb/nextjs
```

2. Add to `config.yaml`:

```yaml
'@harperdb/nextjs':
  package: '@harperdb/nextjs'
  files: '/*'
```

3. Run your app with HarperDB:

```sh
harperdb run nextjs-app
```

Alternatively, you can use the included `harperdb-nextjs` CLI:

```sh
harperdb-nextjs build | dev | start
```

4. Within any server side code paths, you can use [HarperDB Globals]() after importing the HarperDB package:

```js
// app/actions.js
'use server';

import('harperdb');

export async function listDogs() {
	const dogs = [];
	for await (const dog of tables.Dog.search()) {
		dogs.push({ id: dog.id, name: dog.name });
	}
	return dogs;
}

export async function getDog(id) {
	return tables.Dog.get(id);
}

```

```js
// app/dogs/[id]/page.jsx
import { getDog, listDogs } from '@/app/actions';

export async function generateStaticParams() {
	const dogs = await listDogs();

	return dogs;
}

export default async function Dog({ params }) {
	const dog = await getDog(params.id);

	return (
		<section>
			<h1>{dog.name}</h1>
			<p>Breed: {dog.get('breed')}</p>
			<p>Woof!</p>
		</section>
	);
}

```

> Check out the [HarperDB-Add-Ons/nextjs-example](https://github.com/HarperDB-Add-Ons/nextjs-example) for a complete example.

## Options

> All configuration options are optional

### `buildCommand: string`

Specify a custom build command. Defaults to `next build`.

> Note: the extension will skip building if the `prebuilt` option is set to `true`

### `buildOnly: boolean`

Build the Next.js application and then exit (including shutting down HarperDB). Defaults to `false`.

### `dev: boolean`

Enables Next.js dev mode. Defaults to `false`.

### `installCommand: string`

Specify an install command. Defaults to `npm install`.

> Note: the extension will skip installing dependencies if it detects a `node_modules` folder in the application component.

### `port: number`

Specify a port for the Next.js server. Defaults to `3000`.

### `prebuilt: boolean`

When enabled, the extension will look for a `.next` directory in the root of the component and skip executing the `buildCommand`. Defaults to `false`.


## CLI

This package includes a CLI (`harperdb-nextjs`) that is meant to replace certain functions of the Next.js CLI. It will launch HarperDB and set sensible configuration values.

Available commands include:

### `dev`

Launches the application in Next.js development mode, and enables HMR for instantaneous updates when modifying application code.

### `build`

Builds the application and then exits the process.

### `start`

Launches the application in Next.js production mode.

### `help`

Lists available CLI commands.
