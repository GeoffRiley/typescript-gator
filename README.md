# typescript-gator

A command-line RSS feed aggregator. Register an account, subscribe to feeds, run the aggregator in the background, and browse the latest posts — all from your terminal.

## Prerequisites

- **Node.js** v22.15.0 (use [nvm](https://github.com/nvm-sh/nvm) and run `nvm use` in the project root)
- **npm** (comes with Node.js)
- **PostgreSQL** database (running and accessible via a connection URL)

## Installation

1. Clone the repository and install dependencies:

   ```bash
   git clone https://github.com/GeoffRiley/typescript-gator.git
   cd typescript-gator
   nvm use          # switches to the correct Node.js version
   npm install
   ```

2. Apply the database migrations (requires the config file below to be set up first):

   ```bash
   npx drizzle-kit migrate
   ```

## Configuration

The CLI reads its configuration from `~/.gatorconfig.json`. Create this file in your home directory before running any commands:

```json
{
  "db_url": "postgres://user:password@localhost:5432/gator",
  "current_user_name": ""
}
```

| Field               | Description                                                   |
| ------------------- | ------------------------------------------------------------- |
| `db_url`            | **Required.** PostgreSQL connection URL for your database.    |
| `current_user_name` | The currently logged-in user. Managed automatically by the CLI. |

## Running the CLI

All commands are run through `npm start`:

```bash
npm run start -- <command> [args...]
```

For example:

```bash
npm run start -- register alice
```

## Commands

### User management

| Command | Usage | Description |
|---------|-------|-------------|
| `register` | `register <name>` | Create a new user account and log in as that user. |
| `login` | `login <name>` | Switch the active user to an existing account. |
| `users` | `users` | List all registered users; the current user is marked with `(current)`. |
| `reset` | `reset` | **Destructive.** Delete all users and their data from the database. |

### Feed management

| Command | Usage | Description |
|---------|-------|-------------|
| `addfeed` | `addfeed <name> <url>` | Add a new RSS feed by name and URL, and automatically follow it. Requires login. |
| `feeds` | `feeds` | List all feeds known to the system. |
| `follow` | `follow <url>` | Follow an existing feed by its URL. Requires login. |
| `following` | `following` | List all feeds you are currently following. Requires login. |
| `unfollow` | `unfollow <url>` | Stop following a feed by its URL. Requires login. |

### Aggregation & browsing

| Command | Usage | Description |
|---------|-------|-------------|
| `agg` | `agg <interval>` | Start the feed aggregator, polling for new posts at the given interval. Press `Ctrl+C` to stop. Requires login. |
| `browse` | `browse [limit]` | Show the most recent posts from feeds you follow. Defaults to 2 posts; pass a number to show more. Requires login. |

#### `agg` interval format

The interval argument accepts the following suffixes:

| Suffix | Meaning | Example |
|--------|---------|---------|
| `h`    | hours   | `1h`    |
| `m`    | minutes | `30m`   |
| `s`    | seconds | `15s`   |
| `ms`   | milliseconds | `3500ms` |

## Example workflow

```bash
# 1. Register a user
npm run start -- register alice

# 2. Add a feed (automatically followed)
npm run start -- addfeed "Boot.dev Blog" https://blog.boot.dev/index.xml

# 3. Start the aggregator (fetch new posts every 30 seconds)
npm run start -- agg 30s

# 4. In another terminal, browse the latest posts
npm run start -- browse 5
```
