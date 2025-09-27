# DSN-CLI

DSN-CLI is an open-source command-line tool designed to streamline JavaScript project initialization and package management, with a focus on optimizing dependency installation in environments with limited or slow network connectivity. It supports creating projects with React, Next.js, Angular, or Server frameworks, installing packages using npm or pnpm with local caching and peer-to-peer sharing, running project scripts, cleaning `package.json`, sharing package caches across a LAN, and managing code snippets.

DSN-CLI is ideal for developers working offline, in teams with shared package caches, or in scenarios requiring efficient dependency management. It is built with TypeScript and leverages modern Node.js modules for robust performance.

## Features

*   **Project Initialization**: Bootstrap React, Next.js, Angular, or Server projects with a single command using interactive prompts or command-line flags.
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

Bootstrap a new project with React, Next.js, Angular, or Server frameworks, including a DevSync configuration. You can use interactive prompts or command-line flags for quick project creation.

#### Command

```bash
dsn init [options]
```

#### Options

*   `--server`: Create a server project (defaults to Express if no server type specified)
*   `--express`: Use Express framework for server projects
*   `--nestjs`: Use NestJS framework for server projects
*   `--node`: Use Pure Node.js for server projects
*   `--react`: Create a React project
*   `--next`: Create a Next.js project
*   `--angular`: Create an Angular project
*   `--name <name>`: Specify project name (defaults to current directory if omitted)

#### Description

*   **Interactive Mode**: When no flags are provided, prompts for framework selection, server type (if applicable), and project name.
*   **Flag Mode**: Use command-line flags to skip interactive prompts and create projects directly.
*   **Smart Defaults**: If `--server` is specified without a server type, prompts for server type selection.
*   **Mixed Mode**: You can combine flags with prompts - if some information is provided via flags, only missing information will be prompted.
*   **Port Management**: Automatically detects port conflicts and prompts for alternative ports when creating server projects.
*   **Project Name Validation**: Enforces lowercase letters, numbers, and dashes for project names.
*   Scaffolds the project using `create-vite`, `create-next-app`, `@angular/cli`, or custom server templates.
*   Creates a `dsn.json` configuration file.

#### Examples

**Interactive mode (original behavior):**
```bash
dsn init
```
Prompts for framework, server type (if server), and project name.

**Quick server creation:**
```bash
dsn init --server --express --name my-api
```
Creates an Express server project named `my-api` without prompts.

**Server with default Express:**
```bash
dsn init --server --name my-backend
```
Creates a server project and prompts for server type selection (Express, NestJS, or Pure Node.js).

**Frontend projects:**
```bash
dsn init --react --name my-app
dsn init --next --name my-next-app
dsn init --angular --name my-angular-app
```

**Mixed mode:**
```bash
dsn init --server
```
Prompts only for server type and project name (framework is already specified).

**Current directory:**
```bash
dsn init --server --express
```
Creates Express server in current directory (no project name needed).

#### Server Project Features

When creating server projects, DSN-CLI generates:

**Express Projects:**
*   Complete TypeScript setup with strict configuration
*   Express server with CORS, Helmet, and JSON parsing middleware
*   Organized folder structure: `controllers/`, `routes/`, `middleware/`, `models/`, `utils/`
*   Sample User API with CRUD operations
*   Health check endpoint at `/health`
*   Development scripts with `ts-node` and `nodemon`
*   Automatic port conflict detection and resolution

**NestJS Projects:**
*   Full NestJS application with TypeScript
*   Standard NestJS project structure
*   Built-in dependency injection and decorators
*   Development server with hot reload
*   Support for creating projects in current directory

**Pure Node.js Projects:**
*   Native Node.js HTTP server
*   Manual routing and middleware implementation
*   CORS handling and request parsing
*   Same folder structure as Express projects
*   Automatic port conflict detection and resolution

### Install Packages (`install`)

Install a single package or all `package.json` dependencies, prioritizing local cache or LAN peers.

#### Command

```bash
dsn install [name] [options]
```

#### Arguments

*   `[name]`: Package name (e.g., `react`). Omit to install all `package.json` dependencies.

#### Options

*   `-v, --version <version>`: Specify a version (e.g., `19.1.1`, `^19.0.0`, `latest`).
*   `--silent`: Suppress non-error logs for faster execution.
*   `--verify`: Verify cached package integrity (slower but safer).

#### Description

*   **Package Manager Selection**: Prompts for package manager (npm or pnpm, defaults to pnpm) when online.
*   **Installation Priority**: Tries to install from:
    1.   Local cache (`~/.devsync-cache` or `DEVSYNC_CACHE_DIR`)
    2.   LAN peers (if `expose` is running on other machines)
    3.   npm registry (caches the result for future use)
*   **Version Resolution**: Automatically resolves version ranges and finds compatible versions.
*   **Cache Management**: Automatically caches packages downloaded from npm registry.
*   **Package.json Fixes**: Updates `package.json` to avoid `file:` references.
*   **Progress Tracking**: Displays progress for multiple packages with spinner indicators.

#### Examples

**Install a single package:**
```bash
dsn install react --version 19.1.1
```

**Install all dependencies silently:**
```bash
dsn install --silent
```

**Install with cache verification:**
```bash
dsn install --verify
```

**Install specific package with silent mode:**
```bash
dsn install axios --version ^1.0.0 --silent
```

#### Subcommands

**Clear Cache:**
```bash
dsn install clear
```
Deletes all cached packages after confirmation.

**Delete Specific Package:**
```bash
dsn install delete <pkg> [--all]
```
*   `<pkg>`: Package name in format `name@version` (e.g., `react@19.1.1`)
*   `--all`: Delete all cached versions of the package

**List Cached Packages:**
```bash
dsn install list
```
Shows all packages available in the local cache with size and date information.

### Run Scripts (`run`)

Execute a script defined in `package.json`.

#### Command

```bash
dsn run <script> [args...]
```

#### Description

*   Runs the specified script from `package.json` (e.g., `dev`, `build`) with optional arguments.
*   Adds `node_modules/.bin` to the PATH for local binaries.

#### Examples

**Start a development server:**
```bash
dsn run dev
```

**Build a project with custom arguments:**
```bash
dsn run build -- --prod
```

**Run tests:**
```bash
dsn run test
```

### Update Packages (`update`)

Update project dependencies or a specific package using npm or pnpm.

#### Command

```bash
dsn update [name] [--verify] [--silent] [--scan]
```

#### Options

*   `[name]`: Optional package name to update. Omit to update all `package.json` dependencies.
*   `--verify`: Verify package integrity after update.
*   `--silent`: Suppress non-error logs for faster execution.
*   `--scan`: Run a security scan after updating.

#### Description

*   **Package Manager Selection**: Prompts for package manager (npm or pnpm, defaults to pnpm).
*   **Version Updates**: Uses `npm-check-updates` to find and apply the latest compatible versions.
*   **Installation Priority**: Prioritizes installation from local cache or LAN peers (if available).
*   **Automatic Caching**: Caches newly installed packages from the npm registry.
*   **Package.json Maintenance**: Fixes `package.json` to avoid `file:` references.
*   **Conflict Resolution**: Automatically handles peer dependency conflicts with npm.
*   **Progress Tracking**: Displays progress for multiple packages.

#### Examples

**Update all dependencies:**
```bash
dsn update
```

**Update a specific package:**
```bash
dsn update axios
```

**Update with security scan:**
```bash
dsn update --scan
```

**Silent update:**
```bash
dsn update --silent --verify
```

### Clean Package.json (`clean`)

Remove `file:` references from `package.json`, replacing them with version ranges.

#### Command

```bash
dsn clean
```

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

#### Options

*   `-p, --port <port>`: Port for peer advertisement (default: 3000).

#### Description

*   Starts a background server to share the `.devsync-cache` with other DSN-CLI users on the LAN.
*   Updates `devsync.json` with the server's PID and port.
*   Discovers other peers in real-time.

#### Examples

**Share cache on default port:**
```bash
dsn expose
```

**Share on custom port:**
```bash
dsn expose --port 5000
```

### Stop Peer Server (`close`)

Stop the peer-sharing server.

#### Command

```bash
dsn close
```

#### Description

*   Terminates the peer server started by `dsn expose`.
*   Updates `devsync.json` to remove server details.

#### Example

```bash
dsn close
```

### Security Scan (`scan`)

Perform a comprehensive security scan on project dependencies, files, and system resources to detect malware, threats, and vulnerabilities.

#### Command

```bash
dsn scan [-a|--all] [-p|--package <name>] [--deep] [--json] [-o|--output <file>] [--no-network]
```

#### Options

*   `-a, --all`: Scan all dependencies listed in package.json (dependencies, devDependencies, peerDependencies).
*   `-p, --package <name>`: Scan a specific package (e.g., react).
*   `--deep`: Enable deep analysis for more thorough scanning (slower).
*   `--json`: Output results in JSON format instead of formatted text.
*   `-o, --output <file>`: Save scan results to a specified file (JSON format).
*   `--no-network`: Skip network-based checks (e.g., DNS lookups, npm registry queries).

#### Description

Scans for malware, vulnerabilities, and suspicious patterns in:
*   Project dependencies (via package.json or specific packages).
*   JavaScript and TypeScript files for malicious code patterns (e.g., eval, document.write, crypto mining).
*   System resources (processes, network connections, sensitive files).

**Checks for:**
*   Known malicious packages (e.g., compromised versions of event-stream, eslint-scope).
*   Typosquatting (package names similar to popular packages like react, lodash).
*   Suspicious authors, high-entropy code (possible obfuscation), and hardcoded IPs or URLs.
*   Sensitive file modifications (e.g., .npmrc, .env) and exposed secrets (e.g., API keys).

**Features:**
*   Generates a report with a risk level (LOW, MEDIUM, HIGH, CRITICAL), threats, warnings, and recommendations.
*   Supports JSON output for integration with CI/CD pipelines.
*   Skips whitelisted packages (e.g., react, lodash) for efficiency.

#### Examples

**Scan all project dependencies:**
```bash
dsn scan --all
```

**Scan specific package with deep analysis:**
```bash
dsn scan --package react --deep
```

**Save scan results to JSON file:**
```bash
dsn scan --all --json --output scan-results.json
```

**Scan without network checks:**
```bash
dsn scan --no-network
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
*   **open**: Starts a web server to browse or save snippets at `http://localhost:<port>`.

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

**Save a snippet:**
```bash
dsn snippet save my-component.tsx --version 1.0.0
```

**Retrieve a specific version:**
```bash
dsn snippet get my-component 1.0.0
```

**Search snippets:**
```bash
dsn snippet search component
```

**List all snippets:**
```bash
dsn snippet list
```

**Delete a specific snippet:**
```bash
dsn snippet delete my-component 1.0.0
```

**Delete all snippets:**
```bash
dsn snippet delete --all
```

**Open web interface:**
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

**List all cached packages:**
```bash
dsn cache list
```

**Delete a specific package version:**
```bash
dsn cache delete react --version 19.1.1
```

**Delete all versions of a package:**
```bash
dsn cache delete react --all
```

**Clear the entire cache:**
```bash
dsn cache clear
```

## Configuration

DSN-CLI uses a `dsn.json` file in the project root to store metadata (e.g., project name, framework, peer server details). It is created by `dsn init` and updated by `expose` and `close`.

### Cache Directory

*   Defaults to `~/.devsync-cache`.
*   Override with the `DEVSYNC_CACHE_DIR` environment variable:

    ```bash
    export DEVSYNC_CACHE_DIR=/path/to/cache
    ```
    <br/>
    Or in PowerShell:
    ```powershell
    $env:DEVSYNC_CACHE_DIR = "C:\Custom\Cache"
    ```

## Performance Tips

*   **Use pnpm**: `pnpm` is significantly faster than `npm` (e.g., 13s vs. 101s for 11 packages in tests). Select it during `dsn install`.
*   **Avoid Cloud Sync**: Place projects outside OneDrive or similar services to prevent sync-related delays.
*   **Use SSD**: An SSD significantly speeds up file operations.
*   **Exclude from Antivirus**: Exclude the cache directory and project from antivirus scans:
    ```powershell
    # Windows PowerShell (run as admin)
    Add-MpPreference -ExclusionPath "$HOME\.devsync-cache"
    Add-MpPreference -ExclusionPath "$(pwd)"
    ```

## Contributing

DSN-CLI is open-source! To contribute:

*   Fork the repository: [https://github.com/ovie-daveth/DevSync-CLI.git](https://github.com/ovie-daveth/DevSync-CLI.git)
*   Create a feature branch: `git checkout -b my-feature`
*   Commit changes: `git commit -m "Add my feature"`
*   Push to your fork: `git push origin my-feature`
*   Open a pull request.

Please follow the Code of Conduct and include tests for new features.

## License

DSN-CLI is licensed under the [MIT License](https://opensource.org/licenses/MIT).

## Support

For issues or feature requests, open a ticket on GitHub: [https://github.com/ovie-daveth/dsn-cli-docs.git/issues](https://github.com/ovie-daveth/dsn-cli-docs.git/issues)
