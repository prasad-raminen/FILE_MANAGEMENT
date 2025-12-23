# Distributed Network File System (NFS)

This project is a **Distributed Network File System** designed to handle concurrent file operations across multiple storage nodes. It breaks away from traditional file-level locking by implementing **Sentence-Level Locking**, allowing multiple users to edit the same file simultaneously as long as they are working on different sentences.

---

## 🏗️ System Architecture

The system follows a hub-and-spoke model consisting of three primary entities:

1.  Naming Server (The Brain):** The central coordinator. It stores the directory structure, manages file-to-server mappings, and handles access control.
2.  Storage Servers (The Muscle):** Distributed nodes that physically store text files. They handle reading, writing, and streaming data directly to clients.
3.  Clients (The Interface):** Users who interact with the system via a CLI to perform file operations.



---

## 🛠️ Key Features

### 1. Granular Concurrency
Instead of locking an entire file, the system identifies sentence delimiters (`.`, `!`, `?`). 
* **Parallel Editing:** User A can edit sentence 1 while User B edits sentence 5 simultaneously.
* **Word-Level Updates:** Modify specific words within a sentence using index-based commands.

### 2. Smart Communication
* **Direct Access:** For **Reading, Writing, and Streaming**, the Naming Server provides the client with the Storage Server's IP for direct communication to reduce latency.
* **Mediated Tasks:** For administrative tasks (Metadata, Access Control, Deleting), the Naming Server acts as the intermediary.

### 3. Resilience & Efficiency
* **Redundancy:** Supports data replication. If a Storage Server fails, the Naming Server reroutes to a replica.
* **Search Optimization:** Uses Tries or Hashmaps to ensure file lookups are faster than $O(N)$.
* **Logging:** Every packet and request is timestamped for full system traceability.

---

## 🚀 Command Reference

| Command | Action |
| :--- | :--- |
| `VIEW [-a] [-l]` | List files (all files or detailed view). |
| `CREATE <file>` | Initialize a new text file. |
| `READ <file>` | Retrieve the full content of a file. |
| `WRITE <file> <idx>` | Enter interactive mode to modify a specific sentence. |
| `STREAM <file>` | Fetch content word-by-word with a 0.1s delay. |
| `EXEC <file>` | Run file content as shell commands on the Naming Server. |
| `UNDO <file>` | Revert the last modification made to the file. |
| `ADDACCESS <R/W>` | Grant permissions to another user. |

---

## ⚙️ Technical Constraints
* **Text Only:** Optimized specifically for ASCII text data.
* **SPOF:** The Naming Server is a single point of failure; if it fails, the system must restart.
* **Persistence:** All metadata and files survive Storage Server reboots.

---

## 🛠️ Getting Started

1.  **Start the Naming Server:** Initialize the central hub first.
2.  **Spin up Storage Servers:** Connect nodes to the Naming Server.
3.  **Connect Clients:** Users log in with a unique username to start managing files.
