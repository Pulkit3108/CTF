# OverTheWire Bandit Notes

Historical solution notes for the [OverTheWire Bandit](https://overthewire.org/wargames/bandit/) command-line wargame.

> [!WARNING]
> This page contains solution approaches and commands. It intentionally omits every password and private key in accordance with the [OverTheWire rules](https://overthewire.org/rules/). Use the commands only inside the Bandit environment.

The notes use these placeholders:

- `<CURRENT_LEVEL_PASSWORD>`: the password obtained by completing the previous level.
- `<GENERATED_PATH>`: a challenge-specific temporary path or derived filename.
- `<PRIVATE_KEY_FROM_CHALLENGE>`: key material returned by the challenge, intentionally not published here.

Current hostnames, ports, and starting credentials should always be taken from the official level pages.

## Level 0 to 1

List the starting directory and read the provided file.

```bash
ls
cat readme
```

## Level 1 to 2

Use an explicit relative path for a filename beginning with `-`.

```bash
cat ./-
```

## Level 2 to 3

Escape spaces in the filename.

```bash
cat spaces\ in\ this\ filename
```

## Level 3 to 4

Include hidden entries in the directory listing.

```bash
cd inhere
ls -la
cat .hidden
```

## Level 4 to 5

Inspect the files and read the human-readable one identified during the exercise.

```bash
cd inhere
ls -la
file ./*
cat ./-file07
```

## Level 5 to 6

Find a readable, non-executable file with the required exact size.

```bash
cd inhere
find . -type f -readable ! -executable -size 1033c
cat ./maybehere07/.file2
```

## Level 6 to 7

Search the filesystem by owner, group, and exact size while suppressing permission errors.

```bash
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
cat /var/lib/dpkg/info/bandit7.password
```

## Level 7 to 8

Search for the line associated with the required word.

```bash
grep "millionth" data.txt
```

## Level 8 to 9

Sort the data so `uniq` can identify the only non-repeated line.

```bash
sort data.txt | uniq -u
```

## Level 9 to 10

Extract printable strings and filter for the expected marker.

```bash
strings data.txt | grep "="
```

## Level 10 to 11

Decode Base64 text.

```bash
base64 --decode data.txt
```

## Level 11 to 12

Apply ROT13 with `tr`.

```bash
tr '[A-Za-z]' '[N-ZA-Mn-za-m]' < data.txt
```

## Level 12 to 13

Reverse the hex dump, identify each resulting file type, and unpack the nested compression layers. Use a private temporary directory instead of modifying the challenge file in place.

```bash
work_dir="$(mktemp -d)"
chmod 700 "$work_dir"
cp data.txt "$work_dir/"
cd "$work_dir"

xxd -r data.txt > data.bin
file data.bin
gzip -dc data.bin > layer-1.bz2
file layer-1.bz2
bzip2 -dc layer-1.bz2 > layer-2.gz
file layer-2.gz
gzip -dc layer-2.gz > layer-3.tar
file layer-3.tar
tar -xf layer-3.tar

file data5.bin
tar -xf data5.bin
file data6.bin
bzip2 -dc data6.bin > layer-6.tar
tar -xf layer-6.tar

file data8.bin
gzip -dc data8.bin > final.txt
cat final.txt
```

## Level 13 to 14

Use the private key supplied by the challenge to connect to the next local account.

```bash
ls
file sshkey.private
chmod 600 sshkey.private
ssh -i sshkey.private -p 2220 bandit14@localhost
cat /etc/bandit_pass/bandit14
```

## Level 14 to 15

Send the current password to the local service using Netcat.

```bash
nc localhost 30000
# Enter <CURRENT_LEVEL_PASSWORD> when connected.
```

## Level 15 to 16

Connect to the TLS-protected service with OpenSSL.

```bash
openssl s_client -connect localhost:30001 -ign_eof
# Enter <CURRENT_LEVEL_PASSWORD> when connected.
```

## Level 16 to 17

Scan the permitted local port range, connect to the discovered TLS service, and save the returned private key locally. The key body is intentionally omitted.

```bash
nmap -sT -A -p 31000-32000 localhost
openssl s_client -connect localhost:<DISCOVERED_TLS_PORT> -ign_eof
# Enter <CURRENT_LEVEL_PASSWORD>.
# Save <PRIVATE_KEY_FROM_CHALLENGE> as sshkey.private.
chmod 600 sshkey.private
ssh -i sshkey.private -p 2220 bandit17@localhost
```

## Level 17 to 18

Inspect the two password files and compare them to find the changed line.

```bash
ls
file ./*
diff passwords.old passwords.new
```

## Level 18 to 19

Run a command through SSH because the account's login shell immediately exits.

```bash
ssh -p 2220 bandit18@bandit.labs.overthewire.org ls
ssh -p 2220 bandit18@bandit.labs.overthewire.org cat readme
```

## Level 19 to 20

Inspect and use the supplied setuid helper inside the challenge environment.

```bash
ls -la bandit20-do
file bandit20-do
./bandit20-do id
./bandit20-do whoami
./bandit20-do cat /etc/bandit_pass/bandit20
```

## Level 20 to 21

Run a local listener in one terminal and connect the supplied helper from another.

Terminal one:

```bash
nc -l 1234
# Enter <CURRENT_LEVEL_PASSWORD> after the helper connects.
```

Terminal two:

```bash
ls -la suconnect
./suconnect 1234
```

## Level 21 to 22

Inspect the cron configuration and follow the referenced script to its generated output file.

```bash
cd /etc/cron.d
ls -l
cat cronjob_bandit22
cat /usr/bin/cronjob_bandit22.sh
cat /tmp/<GENERATED_PATH>
```

## Level 22 to 23

Reproduce the cron script's deterministic filename calculation.

```bash
cd /etc/cron.d
cat cronjob_bandit23
cat /usr/bin/cronjob_bandit23.sh
whoami
echo I am user bandit23 | md5sum | cut -d ' ' -f 1
cat /tmp/<DERIVED_HASH>
```

## Level 23 to 24

Inspect the cron job, prepare a script in a temporary directory, and copy it to the challenge spool directory.

> [!CAUTION]
> The permissions and spool paths below are challenge-specific. Do not reproduce this world-writable setup on a real system.

```bash
cd /etc/cron.d
cat cronjob_bandit24
cat /usr/bin/cronjob_bandit24.sh

mkdir /tmp/<YOUR_UNIQUE_DIRECTORY>
# CHALLENGE SANDBOX ONLY:
# chmod 777 /tmp/<YOUR_UNIQUE_DIRECTORY>

# Create a challenge script with <YOUR_UNIQUE_DIRECTORY> replaced first.
cat > /tmp/<YOUR_UNIQUE_DIRECTORY>/bandit24.sh <<'SCRIPT'
#!/bin/sh
cat /etc/bandit_pass/bandit24 > /tmp/<YOUR_UNIQUE_DIRECTORY>/level24
SCRIPT

# The challenge cron user must be able to enter the directory and run the script.
# chmod 777 /tmp/<YOUR_UNIQUE_DIRECTORY>/bandit24.sh
cp /tmp/<YOUR_UNIQUE_DIRECTORY>/bandit24.sh /var/spool/bandit24/

cat /tmp/<YOUR_UNIQUE_DIRECTORY>/level24
```

## Level 24 to 25

Generate the permitted four-digit PIN candidates and submit them to the local service with the current password.

```bash
for pin in $(seq -w 0000 9999); do
    printf '%s %s\n' '<CURRENT_LEVEL_PASSWORD>' "$pin"
done > attempts.txt

nc localhost 30002 < attempts.txt
```

## Level 25 to 26

Connect with the challenge-provided key, force the pager to open by reducing the terminal height, then use the pager's editor escape to start a shell.

```bash
ls
file bandit26.sshkey
chmod 600 bandit26.sshkey
ssh -i bandit26.sshkey -p 2220 bandit26@localhost
```

From the pager/editor used by the challenge:

```text
:set shell=/bin/bash
:shell
```

## Level 26 to 27

Use the supplied helper to run a command as the next challenge user.

```bash
ls -la bandit27-do
./bandit27-do whoami
./bandit27-do cat /etc/bandit_pass/bandit27
```

## Level 27 to 28

Clone the local challenge repository and inspect its README.

```bash
git clone ssh://bandit27-git@localhost/home/bandit27-git/repo
cd repo
ls
cat README
```

## Level 28 to 29

Inspect the repository history for information removed from the current file.

```bash
git clone ssh://bandit28-git@localhost/home/bandit28-git/repo
cd repo
cat README.md
git log --oneline
git show
```

## Level 29 to 30

Inspect all branches and switch to the branch containing the required information.

```bash
git clone ssh://bandit29-git@localhost/home/bandit29-git/repo
cd repo
git branch -a
git checkout dev
cat README.md
```

## Level 30 to 31

Inspect repository references and show the challenge's `secret` tag or reference.

```bash
git clone ssh://bandit30-git@localhost/home/bandit30-git/repo
cd repo
git tag
git show secret
```

## Level 31 to 32

Create the requested file, force-add it when required by ignore rules, commit it, and push it to the challenge repository.

> [!CAUTION]
> Push only to the disposable repository supplied for this Bandit level.

```bash
git clone ssh://bandit31-git@localhost/home/bandit31-git/repo
cd repo
printf '%s\n' '<CHALLENGE_REQUESTED_CONTENT>' > key.txt
git add -f key.txt
git commit -m "Add challenge key"
# CHALLENGE REPOSITORY ONLY:
# git push origin master
```

## Level 32 to 33

Escape the uppercase shell by invoking the current shell through `$0`.

```bash
$0
ls -al
cat /etc/bandit_pass/bandit33
```

## Level 33

Inspect the final README supplied by the game.

```bash
ls
cat README.md
```
