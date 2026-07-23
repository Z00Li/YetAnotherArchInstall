# How to Configure

> ⚠️ **Linux users only.** The configuration files described in this document, and in `tastyConfigFiles` as a whole, are intended **exclusively for Linux systems**. If you are on Windows or macOS, these instructions and configuration files do not apply to you.

Before going any further: each subfolder inside [`tastyConfigFiles`](https://github.com/Z00Li/YetAnotherArchInstall/tree/main/tastyConfigFiles) contains its **own `.md` file** with specific, step-by-step instructions for installing and configuring that particular application. **Read the `.md` file inside the subfolder you're working with before doing anything else** — this document only covers the general structure and rules that apply across all of them.

## Important: `YetAnotherArchInstall` Path Requirement

The `YetAnotherArchInstall` repository is a special case and requires attention:

- This repo **should be cloned into your `/home` directory**.
- The `.md` instructions inside that subfolder are written assuming this exact path, and the configuration references paths relative to `/home`.
- Feel free to clone it anywhere you want, but then you will need to replace the path each time when running a command from a `.md` file

## These Configs Are a Starting Point, Not a Final Answer

It's important to understand that **every configuration file provided is meant as a starting point**, not a finished, plug-and-play solution:

- Some configs will work out of the box with little to no changes.
- **Some configs require configuration before they will work at all** — particularly anything involving paths specific to your own installation (home directory names, drive mounts, custom install locations, etc.).
- You are encouraged to open these files and adjust values to match your own system and preferences once the basic setup is working.

Do not assume a config file is "done" just because you copied it into place. Always check the subfolder's `.md` file for any required manual edits before expecting it to function correctly.
> 💡 **Tip:** Some applications might use other ways to apply configuration files. Some may require to paste the config file directly into the /home directory, some might make you dig deep through system files, like `.config/...`