# OSN-cp

## Overview

This project is a small C networking assignment that implements a basic file-sharing system with three components:

- a name server for file creation and access-control requests
- a storage server for file contents and metadata operations
- a client that sends commands to one of the servers

The client connects to the name server on port `9000` and to the storage server on port `9001`.

## Structure

- `src/client.c` starts the interactive client, asks for a username, and routes commands to the right server.
- `src/nameserver.c` handles `CREATE`, `VIEW`, `ADDACCESS`, `REMACCESS`, and `EXEC`.
- `src/storageserver.c` handles `READ`, `WRITE`, `UNDO`, `STREAM`, `DELETE`, `INFO`, and `LIST`.
- `src/utils.c` contains shared helpers for timestamps, locks, and access checks.

## Data files

The program stores state in plain text files under `data/`:

- `data/files/` holds the actual file contents
- `data/metadata.txt` stores file ownership
- `data/access.txt` stores access-control entries
- `data/users.list` stores the usernames seen by the client
- `data/locks/` holds lock files used during writes

Undo snapshots are written as `filename.undo` files in `data/files/`.

## Build

Run:

```bash
make
```

This produces `bin/nm`, `bin/ss`, and `bin/client`.

## Run

Start the servers in separate terminals:

```bash
./bin/nm
./bin/ss
```

Then start the client:

```bash
./bin/client 127.0.0.1 9000 127.0.0.1 9001
```

The client prompts for a username and then accepts commands such as:

```text
CREATE <filename>
VIEW
VIEW -l
VIEW -a
VIEW -al
ADDACCESS -W <filename> <target_user> <your_username>
REMACCESS <filename> <target_user> <your_username>
READ <filename>
WRITE <filename> <sentence_index>
UNDO <filename>
STREAM <filename>
DELETE <filename>
INFO <filename>
LIST
EXIT
```

For `WRITE`, the storage server expects lines of the form:

```text
<word_index> <content>
```

and the session ends when the client sends `ETIRW`.

## Notes

- Access is based on usernames. The owner has full access; other users can be granted read-only or read-write access.
- The `EXEC` command runs the contents of the target file through the shell.
- The implementation is intentionally simple and uses files rather than a database or a more complete permission system.

## License

Not specified.
