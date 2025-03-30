# Demonstration of MD5 Hash for a File and Integrity Verification in Ubuntu

This guide explains how to use the `md5sum` command to:

- Generate an MD5 hash for a file.
- Display the MD5 hash in the console.
- Store the hash in a `.md5` file.
- Verify the file's integrity automatically.

## Step 1: Open the Terminal

Press `Ctrl + Alt + T` to open the terminal in Ubuntu.

## Step 2: Create a Sample File

First, create a sample text file for demonstration:

```bash
echo "Hello, this is a test file." > file.txt
```

You can verify the file's content using:

```bash
cat file.txt
```

## Step 3: Generate MD5 Hash for the File

To compute the MD5 hash for `file.txt` and display it in the console, run:

```bash
md5sum file.txt
```

### Example Output:
```
098f6bcd4621d373cade4e832627b4f6  file.txt
```

This 32-character hash is unique to the file. If the file changes, the hash will also change.

## Step 4: Store the Hash in a .md5 File

To store the MD5 hash in a separate file (`checksum.md5`), use:

```bash
md5sum file.txt > checksum.md5
```

This command creates a `checksum.md5` file that contains:

```
098f6bcd4621d373cade4e832627b4f6  file.txt
```

You can check its content using:

```bash
cat checksum.md5
```

## Step 5: Verify File Integrity Automatically

To verify if `file.txt` is unchanged, run:

```bash
md5sum -c checksum.md5
```

### Expected Output:
- If the file is intact:
  ```
  file.txt: OK
  ```
- If the file is modified:
  ```
  file.txt: FAILED
  ```

## Step 6: Test What Happens If the File Changes

To check if MD5 can detect changes, modify `file.txt`:

```bash
echo "This is a modified version." > file.txt
```

Now, re-run the verification:

```bash
md5sum -c checksum.md5
```

### New Output (If the file is altered):
```
file.txt: FAILED
md5sum: WARNING: 1 computed checksum did NOT match
```

This indicates that `file.txt` has been modified or corrupted.

## Step 7: Recalculate the MD5 Hash After Modification

Since the file changed, you must recalculate its MD5 hash:

```bash
md5sum file.txt > checksum.md5
```

Now, run the verification again:

```bash
md5sum -c checksum.md5
```

If no further modifications were made, it should return:

```
file.txt: OK
```

## Conclusion

The `md5sum` command provides a simple way to verify file integrity by generating and comparing MD5 hashes. By storing the checksum in a `.md5` file, users can quickly check if a file has been altered. However, since MD5 is vulnerable to collision attacks, it is recommended to use stronger hashing algorithms like SHA-256 for security-critical applications.

