
Applications often load images using certain APIs to contact the directory where the req info is stored.

To return an image, the application appends the requested filename to this base directory and uses a filesystem API to read the contents of the file.

## Common obstacles to exploiting path traversal vulnerabilities

Many applications that place user input into file paths implement defences against path traversal attacks. These can often be bypassed.

If an application strips or blocks directory traversal sequences from the user-supplied filename, it might be possible to bypass the defence using a variety of techniques.

You might be able to use an absolute path from the filesystem root, such as `filename=/etc/passwd`, to directly reference a file without using any traversal sequences.

Also, You might be able to use nested traversal sequences, such as `....//` or `....\/`. These revert to simple traversal sequences when the inner sequence is stripped.

> **"....//"**: The ".." represents moving up one level in the hierarchy (similar to how it's used in directory navigation), and "//" represents traversing down the hierarchy. So, "....//" means moving up four levels and then traversing down. This is useful in scenarios where you want to access a resource that is located deeper in the hierarchy, but you're not exactly sure of the exact path.

# NOTE:
Add as many ....//....//....// as you need, because once the root directory is reached, the system ignores the extra ....// without throwing an error. (In UNIX like systems). Although, whenever possible, use multiple ../../../ instead; they're more robust and seem to work more generally.


# URL Encoding to Bypass Stripping

In some contexts, such as in a URL path or the `filename` parameter of a `multipart/form-data` request, web servers may strip any directory traversal sequences before passing your input to the application. You can sometimes bypass this kind of sanitization by URL encoding, or even double URL encoding, the `../` characters. This results in `%2e%2e%2f` and `%252e%252e%252f` respectively. Various non-standard encodings, such as `..%c0%af` or `..%ef%bc%8f`, may also work.

# Using Null Byte characters to fake file extensions

An application may require the user-supplied filename to end with an expected file extension, such as `.png`. In this case, it might be possible to use a null byte to effectively terminate the file path before the required extension. For example: `filename=../../../etc/passwd%00.png`.

# Prevention from a Path Traversal Attack

> The most effective way to prevent path traversal vulnerabilities is to avoid passing user-supplied input to filesystem APIs altogether. Many application functions that do this can be rewritten to deliver the same behavior in a safer way.

If you can't avoid passing user-supplied input to filesystem APIs, we recommend using two layers of defense to prevent attacks:

- Validate the user input before processing it. Ideally, compare the user input with a whitelist of permitted values. If that isn't possible, verify that the input contains only permitted content, such as alphanumeric characters only.
- After validating the supplied input, append the input to the base directory and use a platform filesystem API to canonicalize the path. Verify that the canonicalized path starts with the expected base directory.

Below is an example of some simple Java code to validate the canonical path of a file based on user input:

`File file = new File(BASE_DIRECTORY, userInput); if (file.getCanonicalPath().startsWith(BASE_DIRECTORY)) { // process file }`


> Canonicalizing a path refers to the process of converting a given file or directory path to its simplest form or its standard representation. This process often involves resolving any symbolic links, eliminating redundant components, and ensuring consistent formatting.

Here are some common steps involved in canonicalizing a path:

1. **Resolve symbolic links:** If the path contains symbolic links (shortcuts to other files or directories), they are resolved to their actual target paths.
    
2. **Eliminate redundant components:** Redundant components such as "." (current directory) and ".." (parent directory) are removed. For example, "/path/to/dir/./file" would be simplified to "/path/to/dir/file".

