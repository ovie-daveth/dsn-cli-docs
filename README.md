# DSN-CLI

DSN-CLI is an open-source command-line tool designed to streamline JavaScript project initialization and package management, with a focus on optimizing dependency installation in environments with limited or slow network connectivity. It supports creating projects with React, Next.js, or Angular, installing packages using npm or pnpm with local caching and peer-to-peer sharing, running project scripts, cleaning `package.json`, sharing package caches across a LAN, and managing code snippets.

DSN-CLI is ideal for developers working offline, in teams with shared package caches, or in scenarios requiring efficient dependency management. It is built with TypeScript and leverages modern Node.js modules for robust performance.

## Features

*   **Project Initialization**: Bootstrap React, Next.js, or Angular projects with a single command.
*   **Optimized Package Installation**: Install packages from a local cache, LAN peers, or the npm registry, with automatic caching for future use.
*   **Script Execution**: Run `package.json` scripts seamlessly.
*   **Package.json Cleanup**: Remove `file:` references from `package.json` to ensure compatibility.
*   **Peer-to-Peer Cache Sharing**: Share and fetch package caches across a LAN.
*   **Snippet Management**: Save, search, list, and share code snippets via a CLI or web interface.
*   **Customizable Cache**: Store packages in a user-defined cache directory to avoid sync issues (e.g., with OneDrive).

## Installation

DSN-CLI can be installed globally via npm or built from source using GitHub. It requires Node.js 18.x or later and npm or pnpm.

### Prerequisites

*   **Node.js**: Install from [nodejs.org](https://nodejs.org/) (version 18.x or later).
*   **npm or pnpm**: npm is included with Node.js; install pnpm with:
    ```bash
    npm install -g pnpm
    ```
    <br/>
*   **Git** (optional, for cloning): Install from [git-scm.com](https://git-scm.com/).

### Install via npm

Install DSN-CLI globally from the npm registry:

```bash
npm install -g dsn-cli
```
<br/>
Verify installation:

```bash
dsn --version
```

### Install from GitHub

Clone the repository and link it globally using pnpm:

```bash
git clone https://github.com/<your-username>/dsn.git
cd dsn
pnpm install
pnpm link --global
```
<br/>
Verify installation:

```bash
dsn --version
```
<br/>
**Note**: Replace `<your-username>` with your GitHub username. Ensure the repository is published to GitHub.

## Usage

DSN-CLI provides several commands to manage projects and dependencies. Run `dsn --help` to see all commands and options.

### Initialize a New Project (`init`)

Bootstrap a new project with React, Next.js, or Angular, including a dsn configuration.

#### Command

```bash
dsn init
```
<br/>
#### Description

*   Prompts for a project framework (React, Next.js, Angular) and project name.
*   Scaffolds the project using `create-vite`, `create-next-app`, or `@angular/cli`.
*   Creates a `dsn.json` configuration file and a `.dsn-cache` directory.

#### Example

```bash
dsn init
```
<br/>
Select React and enter `my-app` as the project name. Creates `my-app/` with a Vite-based React project, `dsn.json`, and `.dsn-cache`.

### Install Packages (`install`)

Install a single package or all `package.json` dependencies, prioritizing local cache or LAN peers.

#### Command

```bash
dsn install [name] [-v|--version <version>] [--silent] [--verify]
```
<br/>
#### Options

*   `[name]`: Package name (e.g., `react`). Omit to install all `package.json` dependencies.
*   `-v, --version <version>`: Specify a version (e.g., `19.1.1`, `^19.0.0`, `latest`).
*   `--silent`: Suppress non-error logs for faster execution.
*   `--verify`: Verify cached package integrity (slower but safer).

#### Description

*   Prompts for package manager (npm or pnpm, defaults to pnpm).
*   Tries to install from:
    *   Local cache (`~/.dsn-cache` or `dsn_CACHE_DIR`).
    *   LAN peers (if `expose` is running on other machines).
    *   npm registry (caches the result).
*   Fixes `package.json` to avoid `file:` references.
*   Displays progress for multiple packages.

#### Examples

Install a single package:
```bash
dsn install react --version 19.1.1
```
<br/>
Install all `package.json` dependencies silently:
```bash
dsn install --silent
```
<br/>
Install with cache verification:
```bash
dsn install --verify
```

### Run Scripts (`run`)

Execute a script defined in `package.json`.

#### Command

```bash
dsn run <script> [args...]
```
<br/>
#### Description

*   Runs the specified script from `package.json` (e.g., `dev`, `build`) with optional arguments.
*   Adds `node_modules/.bin` to the PATH for local binaries.

#### Examples

Start a development server:
```bash
dsn run dev
```
<br/>
Build a project with custom arguments:
```bash
dsn run build -- --prod
```

### Clean Package.json (`clean`)

Remove `file:` references from `package.json`, replacing them with version ranges.

#### Command

```bash
dsn clean
```
<br/>
#### Description

*   Scans `dependencies` and `devDependencies` for `file:` paths.
*   Extracts versions from filenames (e.g., `file:../react-19.1.1.tgz` → `^19.1.1`).
*   Updates `package.json` if changes are made.

#### Example

```bash
dsn clean
```

### Expose Package Cache (`expose`)

Share the local package cache with LAN peers.

#### Command

```bash
dsn expose [-p|--port <port>]
```
<br/>
#### Options

*   `-p, --port <port>`: Port for peer advertisement (default: 3000).

#### Description

*   Starts a background server to share the `.dsn-cache` with other DSN-CLI users on the LAN.
*   Updates `dsn.json` with the server’s PID and port.
*   Discovers other peers in real-time.

#### Examples

Share cache on port 3000:
```bash
dsn expose
```
<br/>
Share on a custom port:
```bash
dsn expose --port 5000
```

### Stop Peer Server (`close`)

Stop the peer-sharing server.

#### Command

```bash
dsn close
```
<br/>
#### Description

*   Terminates the peer server started by `dsn expose`.
*   Updates `dsn.json` to remove server details.

#### Example

```bash
dsn close
```

### Manage Snippets (`snippet`)

Save, search, list, or access code snippets via a CLI or web interface.

#### Subcommands

*   `save <file>`: Save a file as a snippet. Supports versioning.
*   `search <name>`: Search snippets by name.
*   `list`: List all cached snippets.
*   `get <name> [version]`: Retrieve a snippet by name, optionally specifying a version.
*   `delete [name] [version] [--all]`: Delete a specific snippet by name and optional version, or delete all snippets with the `--all` flag.
*   `open [-p|--port <port>]`: Open a web interface for snippets.

#### Options

*   `-v, --version <version>` (for `save`): Specify snippet version (default: `1.0.0`).
*   `--all` (for `delete`): Delete all snippets after confirmation.
*   `-p, --port <port>` (for `open`): Web interface port (default: `4000`).

#### Description

*   **save**: Stores a file in the cache as a snippet with a name and version. If a snippet with the same name and version already exists, you will be prompted to overwrite, rename, save as a new version, or cancel.
*   **search**: Finds snippets by name substring, including those saved offline and pending sync.
*   **list**: Displays all cached snippets, including those saved offline and pending sync.
*   **get**: Retrieves a snippet by its base name (without the file extension). If no version is specified, the latest version is fetched. If offline, it will also check the local queue. The retrieved file will be saved with its original file extension.
*   **delete**: Deletes a snippet. If `--all` is used, it will delete all snippets from both online and offline storage after a confirmation prompt. Otherwise, it deletes a specific snippet by name and version, requiring you to be the creator. It will check the offline queue if the snippet is not found in the main database.
*   **open**: Starts a web server to browse or save snippets at `http://localhost:<port}`.

#### Versioning Snippets

DSN-CLI allows you to manage different versions of your snippets. When you save a snippet, you can assign it a version number. This is useful for tracking changes and retaining older iterations of your code.

*   **Saving a New Version**: When saving a snippet that already exists with the same name and version, you will now be prompted with an option to 'New Version'. Selecting this will allow you to specify a new version number (e.g., `2.0.0` for `1.0.0`), keeping the old version intact and saving the new content as a distinct version.
    ```bash
    dsn snippet save my-script.js -v 2.0.0
    ```

*   **Retrieving Specific Versions**: You can retrieve a particular version of a snippet by providing the snippet's base name (without the file extension) and the version number with the `get` command.
    ```bash
    # To get version 1.0.0 of a snippet originally saved as my-script.js
    dsn snippet get my-script 1.0.0
    ```

    If no version is specified, `dsn snippet get` will automatically retrieve the latest available version.

#### Examples

Save a snippet:
```bash
dsn snippet save my-component.tsx --version 1.0.0
```
<br/>
Retrieve a specific version:
```bash
dsn snippet get my-component 1.0.0
```
<br/>
Search snippets:
```bash
dsn snippet search component
```
<br/>
List all snippets:
```bash
dsn snippet list
```
<br/>
Delete a specific snippet:
```bash
dsn snippet delete my-component 1.0.0
```
<br/>
Delete all snippets:
```bash
dsn snippet delete --all
```
<br/>
Open web interface:
```bash
dsn snippet open --port 4000
```

### Manage Cache (`cache`)

Manage the local DSN package cache.

#### Subcommands

*   `list`: List all cached packages.
*   `delete <name> [-v|--version <version>] [--all]`: Delete a specific package by name and optional version, or delete all versions of a package with the `--all` flag.
*   `clear`: Clear the entire DSN package cache after confirmation.

#### Examples

List all cached packages:
```bash
dsn cache list
```
<br/>
Delete a specific package version:
```bash
dsn cache delete react --version 19.1.1
```
<br/>
Delete all versions of a package:
```bash
dsn cache delete react --all
```
<br/>
Clear the entire cache:
```bash
dsn cache clear
```

## Configuration


DSN-CLI uses a `dsn.json` file in the project root to store metadata (e.g., project name, framework, peer server details). It is created by `dsn init` and updated by `expose` and `close`.

### Cache Directory

*   Defaults to `~/.dsn-cache`.
*   Override with the `dsn_CACHE_DIR` environment variable:

    ```bash
    export dsn_CACHE_DIR=/path/to/cache
    ```
    <br/>
    Or in PowerShell:
    ```powershell
    $env:dsn_CACHE_DIR = "C:\Custom\Cache"
    ```

## Performance Tips

*   **Use pnpm**: `pnpm` is significantly faster than `npm` (e.g., 13s vs. 101s for 11 packages in tests). Select it during `dsn install`.
*   **Avoid Cloud Sync**: Place projects outside OneDrive or similar services to prevent sync-related delays.
*   **Use SSD**: An SSD significantly speeds up file operations.
*   **Exclude from Antivirus**: Exclude the cache directory and project from antivirus scans:
    ```powershell
    # Windows PowerShell (run as admin)
    Add-MpPreference -ExclusionPath "$HOME\.dsn-cache"
    Add-MpPreference -ExclusionPath "$(pwd)"
    ```

## Contributing

DSN-CLI is open-source! To contribute:

*   Fork the repository: [git+https://github.com/ovie-daveth/dsn-CLI.git](git+https://github.com/ovie-daveth/dsn-CLI.git)
*   Create a feature branch: `git checkout -b my-feature`
*   Commit changes: `git commit -m "Add my feature"`
*   Push to your fork: `git push origin my-feature`
*   Open a pull request.

Please follow the Code of Conduct and include tests for new features.

## License

DSN-CLI is licensed under the [MIT License](https://opensource.org/licenses/MIT).

## Support

For issues or feature requests, open a ticket on GitHub: [https://github.com/ovie-daveth/dsn-cli-docs.git/issues](https://github.com/ovie-daveth/dsn-cli-docs.git/issues)