# CMD Challenge Notes

Historical command solutions recorded while working through [CMD Challenge](https://cmdchallenge.com/).

> [!CAUTION]
> These commands were written for disposable challenge sandboxes. Several remove, rename, or overwrite files. Destructive examples are commented out so the blocks cannot be copied and executed accidentally.

The original challenge fixtures are not stored in this repository, so the commands are preserved as learning notes rather than presented as currently verified answers. Some tools, such as `rename`, also differ across operating systems.

## 1. Print text

```bash
echo "hello world"
```

## 2. Show the current directory

```bash
pwd
```

## 3. List directory contents

```bash
ls
```

## 4. Read a file

```bash
cat access.log
```

## 5. Show the final five lines

```bash
tail -n 5 access.log
```

## 6. Run the supplied executable

```bash
./take-the-command-challenge
```

## 7. Create nested directories

```bash
mkdir -p tmp/files
```

## 8. Copy a file

```bash
cp take-the-command-challenge tmp/files/
```

## 9. Move a file

```bash
mv take-the-command-challenge tmp/files/
```

## 10. Create a symbolic link

```bash
ln -s tmp/files/take-the-command-challenge take-the-command-challenge
```

## 11. Remove all challenge files

The original exercise required clearing the sandbox, including hidden entries.

```bash
# DESTRUCTIVE, CHALLENGE SANDBOX ONLY:
# rm -r * .*
```

## 12. Remove Word documents found recursively

```bash
# DESTRUCTIVE, CHALLENGE SANDBOX ONLY:
# rm $(find . -name "*.doc")
```

## 13. Filter log lines containing `GET`

```bash
grep "GET" access.log
```

## 14. Find the access-log filename

```bash
ls | grep "access.log"
```

## 15. Search recursively for status code 500

```bash
grep -rh "500" .
```

## 16. Extract leading IP-address text

```bash
grep -ro '^[0-9.]*' .
```

## 17. Count listed entries

```bash
ls -l | wc -l
```

## 18. Sort a log file

```bash
sort access.log
```

## 19. Count `GET` requests

```bash
grep -c "GET" access.log
```

## 20. Split semicolon-separated text into lines

```bash
tr ';' '\n' < split-me.txt
```

## 21. Print the numbers 1 through 100

```bash
seq 100
```

## 22. Edit matching text files in place

The original note below appears incomplete because it does not include a full `sed` expression. It is preserved as an unverified historical step rather than silently presented as a working solution.

```bash
# MUTATES FILES; ORIGINAL RECORDED COMMAND:
# sed -i 'challenge are difficult' **/*.txt
```

## 23. Sum newline-separated numbers

```bash
paste -sd+ sum-me.txt | bc
```

## 24. Print filenames without their directories

```bash
find . -type f -printf '%f\n'
```

## 25. Remove filename extensions

The `rename` syntax is implementation-specific.

```bash
# MUTATES FILES; CHALLENGE SANDBOX ONLY:
# find . -type f -exec rename 's/\..*//' {} +
```

## 26. Replace spaces with periods

```bash
ls | tr ' ' '.'
```

## 27. List unique Terraform parent directories

```bash
dirname **/*.tf | uniq
```

## 28. Find filenames beginning with a number

```bash
find . -type f -printf '%f\n' | grep '^[0-9]'
```

## 29. Print line 25

```bash
head -n 25 faces.txt | tail -n 1
```

## 30. Reverse line order

```bash
tac reverse-me.txt
```

## 31. Remove duplicate lines while preserving first occurrence

```bash
awk '!seen[$0]++' faces.txt
```

## 32. Count unique prime numbers

```bash
factor < random-numbers.txt | grep -P '\d+: \d+$' | sort -u | wc -l
```

## 33. Find IP addresses shared across sorted logs

```bash
sort access.log.* | grep -o '^[0-9.]*' | uniq -d
```

## 34. Print the line before each 404 result

```bash
grep -rhB1 "404" . | grep -v '404\|--'
```

## 35. Identify binary files that differ from the base

```bash
diff *.bin --to-file base.bin | cut -d ' ' -f 3
```

## 36. Read a deeply nested filename containing spaces

```bash
cat .../*/'. .the flag.txt'
```

## 37. Count files containing tab characters

```bash
grep -P "\t" * | wc -l
```

## 38. Delete files without `.exe` or `.txt` extensions

```bash
# DESTRUCTIVE, CHALLENGE SANDBOX ONLY:
# find . -type f ! -regex '.*\(exe\|txt\)$' -delete
```

## 39. Remove files whose names begin with `-`

```bash
# DESTRUCTIVE, CHALLENGE SANDBOX ONLY:
# rm ./-*
```

## 40. Sort process output by the second field and remove duplicates

```bash
sort -nuk2 ps-ef*
```

## 41. Extract listening IPv4 ports

```bash
grep "LISTEN" netstat.out | awk '{print $4}' | grep '\.' | cut -d: -f2
```
