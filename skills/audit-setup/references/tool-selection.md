# Tool usage

## 4a. Tool selection correctness

Beyond efficiency, check whether the right tool is used at all. The Claude Code harness provides specialized tools that are preferred over Bash equivalents:

| Task | Correct tool | Wrong (Bash) |
|---|---|---|
| Read a file | Read | `cat`, `head`, `tail` |
| Find files by pattern | Glob | `find`, `ls -R` |
| Search content | Grep | `grep`, `rg` |
| Write a new file | Write | `echo >`, heredoc redirect |
| Modify a file | Edit | `sed`, `awk` |
| Output text to user | direct text response | `echo`, `printf` |

Flag agents, skills, or workflows that:

- use Bash `cat` to read files instead of the Read tool
- use heredoc syntax (`cat << EOF > /tmp/file`) to write files instead of the Write tool
- use `find` or `ls -R` instead of Glob
- use `sed` or `awk` to modify files instead of the Edit tool
- write to `/tmp/` instead of project paths
- echo file contents to the terminal as a way of "passing" content downstream
- construct long inline Bash strings (>200 characters) to pass prompts to nested `claude` calls — pass a file path instead

## 4b. Tool usage efficiency

Flag:

- broad `Read`, `Glob`, `Search`, `Bash`, or equivalent patterns
- reading whole files when sections are enough
- listing large directories
- dumping full command output into context
- reading logs, manifests, lockfiles, generated files, or JSON blobs unnecessarily
- allowing tools that the agent does not need
- missing truncation rules for logs and command output
- commands that produce huge outputs
- commands that print long prompts or generated shell invocations into the transcript
- unnecessary writes to session files
- unnecessary rereads of recently written files

Recommend:

- prefer specialized tools (Read, Glob, Grep, Edit, Write) over Bash equivalents
- read only named files
- restrict allowed tools per agent
- use output truncation
- summarize large artifacts once
- use compact handoff files
- avoid raw logs unless needed
- move deterministic logic into scripts
- use shell scripts or Node/Python scripts instead of generating long Bash commands
- avoid printing full generated commands into Claude Code context
