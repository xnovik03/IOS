# XTF – Log Processing Script

A shell script that processes financial transaction logs for specific users and currencies. It supports multiple filters (by date and currency) and commands to display account status, calculate profit, or list records.

## Features

- Filter transactions by:
  - **Start date** (`-a`)
  - **End date** (`-b`)
  - **Currency** (`-c`)
- Commands:
  - `list`: display all matching entries for a user
  - `list-currency`: list used currencies for the user
  - `status`: show total balance per currency
  - `profit`: calculate balance with virtual profit applied
- Supports multiple input files: plain text and `.gz`-compressed logs
- Reads from standard input if no files are given

## Requirements

- POSIX-compatible shell
- `awk`, `gzip`, `sort`, `printf`, `cat`

## Environment Variables

- `XTF_PROFIT` – percentage used in `profit` command (default: 20%)

## Log Format

Each transaction is a semicolon-separated record in the form:

```text
user;timestamp;currency;amount
```

Example:
```text
alice;2024-01-10T10:00:00;USD;150.00
bob;2024-02-12T12:00:00;EUR;-50.00
```

## Usage

```bash
./xtf [FILTERS] COMMAND USER FILE1 [FILE2 ...]
```

### Filters

| Flag         | Description                      |
|--------------|----------------------------------|
| `-a DATE`    | Only include entries **after** this date (e.g. `2024-01-01`) |
| `-b DATE`    | Only include entries **before** this date |
| `-c CURRENCY`| Only include entries for this currency (e.g. `USD`) |

### Commands

| Command         | Description                                |
|------------------|--------------------------------------------|
| `list`           | List raw transactions for a given user     |
| `list-currency`  | List distinct currencies in user’s logs    |
| `status`         | Print account balance per currency         |
| `profit`         | Print balance with XTF_PROFIT % bonus on positive values |

## Examples

### List entries for user "alice" from log files:

```bash
./xtf list alice log1.txt log2.txt
```

### Show balance for "bob" in EUR before 2024-02-01:

```bash
./xtf -b "2024-02-01" -c EUR status bob logs.txt
```

### Calculate profit with a custom percentage:

```bash
XTF_PROFIT=25 ./xtf profit alice logs.gz
```

### Using input from a pipe:

```bash
cat logs.txt | ./xtf -c USD list alice
```

## Error Handling

- Returns an error if required files are not found or readable
- Handles invalid argument counts with descriptive messages


