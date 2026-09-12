___

## Pipe Command (`|`) — Complete Technical & Cybersecurity Analysis

> **Topic:** Unix/Linux pipe operator (`|`)  
> **Category:** Shell feature / Inter-process communication (IPC) mechanism  
> **Common environments:** Linux, Unix, macOS, BSD, Windows PowerShell (similar concept)

### What is the pipe command (`|`)?
The **pipe (`|`)** is a shell operator that connects the **standard output (stdout)** of one command to the **standard input (stdin)** of another command.

In simple terms:
```
Command A | Command B
```

means:
```
Output produced by Command A
        ↓
Input received by Command B
```

Example:
```bash
ls -la | grep ".txt"
```

**Workflow:**
1. `ls -la` lists files.
2. Its output is not displayed directly.
3. The output is sent to `grep`.
4. `grep` filters only filenames containing `.txt`.

### Why was the pipe created?
Before pipes, users had to:
1. Save command output into temporary files.
2. Run another command on that file.

Example without pipe:
```bash
ls > files.txt
grep ".txt" files.txt
```

This created unnecessary temporary files.

The pipe concept introduced a cleaner model:
```bash
ls | grep ".txt"
```

Benefits:
- Faster processing
- Less disk usage
- Modular command design
- Ability to combine small tools into powerful workflows

### Unix philosophy
The pipe represents the Unix philosophy:

> "Write programs that do one thing and do it well."

Examples:
```
cat
grep
sort
uniq
awk
sed
cut
wc
```

Each tool performs one task.

Pipes combine them.
Example:
```bash
cat access.log | grep "FAILED" | sort | uniq -c
```

___

## Pipe ( | ) Usage
### Beginner Level
| Purpose       | Command / Syntax          | Example                 | What It Does                    |
| ------------- | ------------------------- | ----------------------- | ------------------------------- |
| Basic pipe    | `cmd1 \| cmd2`            | `ls \| grep txt`        | Sends output to another command |
| View files    | `cat file \| less`        | `cat log.txt \| less`   | Scroll large output             |
| Search output | `command \| grep pattern` | `ps aux \| grep nginx`  | Filter results                  |
| Count output  | `command \| wc`           | `ls \| wc -l`           | Count lines                     |
| Sort output   | `command \| sort`         | `cat names.txt \| sort` | Sort data                       |

### Intermediate Level
| Purpose            | Command / Syntax  | Example                       | What It Does         |
| ------------------ | ----------------- | ----------------------------- | -------------------- |
| Network filtering  | `nmap \| grep`    | `nmap -sV host \| grep open`  | Extract services     |
| Log analysis       | `cat log \| grep` | `cat auth.log \| grep failed` | Find security events |
| Duplicate removal  | `sort \| uniq`    | `cat users \| sort \| uniq`   | Remove duplicates    |
| Column extraction  | `awk` pipeline    | `cat log \| awk '{print $1}'` | Extract fields       |
| Output duplication | `tee`             | `nmap host \| tee scan.txt`   | Save and display     |

### Advanced Level
| Purpose             | Command / Syntax     | Example                                      | What It Does               |
| ------------------- | -------------------- | -------------------------------------------- | -------------------------- |
| Automated scanning  | `tool1 \| tool2`     | `subfinder \| httpx`                         | Chain security tools       |
| Data processing     | `grep \| awk \| sed` | `cat logs \| grep ERROR \| awk '{print $2}'` | Advanced parsing           |
| Parallel processing | `xargs`              | `cat hosts \| xargs -P10 ping`               | Run multiple jobs          |
| Error handling      | `cmd \|& tee`        | `scan \|& tee output.txt`                    | Capture errors             |
| Complex workflows   | Multiple pipes       | `nmap \| grep \| awk`                        | Build automation pipelines |

### Important Pipe-Related Commands Quick Reference
| Command | Purpose                   |
| ------- | ------------------------- |
| `grep`  | Search/filter text        |
| `awk`   | Extract/process columns   |
| `sed`   | Modify text streams       |
| `cut`   | Extract fields            |
| `sort`  | Sort data                 |
| `uniq`  | Remove duplicates         |
| `wc`    | Count data                |
| `tee`   | Save + display output     |
| `xargs` | Convert input to commands |
| `jq`    | Parse JSON                |

