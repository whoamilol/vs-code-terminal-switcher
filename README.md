# 🐚 VS Code Terminal Shell Switcher (Flatpak Version - No sudo needed)

> Easily switch between a **zsh terminal for aliases** and a **pure bash terminal for debugging** in VS Code — even on locked-down school machines using Flatpak.

⚠️ **Tested on Ubuntu-based 42 school machines using Flatpak-installed VS Code.**

---

## ✨ What this gives you

- 🧑‍💻 ZSH terminal with aliases, autocompletions, themes, and personal configs
- 🐞 BASH terminal that works with the VS Code debugger (which zsh often breaks)
- 🔁 Quick commands to toggle between the two — no sudo needed

---

## 📦 Step 1: Set up shell configuration files

Go to your VS Code settings directory:

```bash
cd ~/.var/app/com.visualstudio.code/config/Code/User/
```

### 1. Save your current setup as zsh mode:

```bash
cp settings.json settings.zsh.json
```

### 2. Create a bash-only config for debugging:

Start by copying your current `settings.json` to a separate file so you can safely edit it:

```bash
cp settings.json settings.debug.json
```


Open your current `settings.json`, copy its contents to a new file called `settings.debug.json`, and then **replace the terminal section** (if it already exists) with this:


📥 *Copy the following block into your `settings.debug.json` file:*
```json

  "terminal.integrated.defaultProfile.linux": "bash",
  "terminal.integrated.profiles.linux": {
    "bash": {
      "path": "/bin/bash",
      "args": []
    }
  }
```

### 3. Create a zsh config (for daily use with aliases):

Open your current `settings.json`, copy it to a new file called `settings.zsh.json`, and **replace the terminal section** (if one exists) with this:

```

✅ *Then add these lines, if they dont already exist into the settings.zsh.json*:

{
  "terminal.integrated.defaultProfile.linux": "zsh",
  "terminal.integrated.profiles.linux": {
    "zsh": {
      "path": "host-spawn",
      "args": ["bash", "-c", "zsh"]
    },
    "bash": {
      "path": "/bin/bash",
      "args": []
    }
  }
}
```

Make sure to remove or overwrite any existing `terminal.integrated.profiles.linux` and `terminal.integrated.defaultProfile.linux` fields so they don't conflict. while adding zsh support.

> 💡 Tip: You can customize these files further later — just keep them separate!

---

## ⚙️ Step 2: Add functions to switch modes

Edit **both** your `~/.bashrc` and `~/.zshrc`, and paste the following:

```bash
vdebug() {
  local config_dir="$HOME/.var/app/com.visualstudio.code/config/Code/User"
  local debug="$config_dir/settings.debug.json"
  local current="$config_dir/settings.json"

  if cmp -s "$current" "$debug"; then
    echo "Already in debugger (bash) mode."
  else
    cp "$debug" "$current"
    echo "✅ Switched to debugger (bash) mode."
  fi

  echo "🛠️  Please close and reopen the VS Code terminal (Ctrl+Shift+\`) to apply the change."
}

vzsh() {
  local config_dir="$HOME/.var/app/com.visualstudio.code/config/Code/User"
  local zsh="$config_dir/settings.zsh.json"
  local current="$config_dir/settings.json"

  if cmp -s "$current" "$zsh"; then
    echo "Already in zsh dev mode."
  else
    cp "$zsh" "$current"
    echo "✅ Switched to zsh dev mode."
  fi

  echo "🛠️  Please close and reopen the VS Code terminal (Ctrl+Shift+\`) to apply the change."
}
```

### Then reload your shell config:

```bash
# If you're currently in zsh:
source ~/.zshrc

# If you're in bash (after switching to debugger mode):
source ~/.bashrc
```

> 🧠 Tip: Remember that bash does not automatically load your `.bashrc` in all environments. If you're in a new terminal and your vdebug/vzsh commands aren't found, just run `source ~/.bashrc` to load them.

---

## 🧪 Step 3: Use it

```bash
vdebug   # Switches to bash mode for debugging
vzsh     # Switches back to zsh dev mode with aliases
```

> ⚠️ After running either command, **you must close and reopen your VS Code terminal**:
>
> - Click the trash icon 🗑️ in the terminal tab
> - Then click ➕ "New Terminal"
> - Or press `Ctrl+Shift+\`` (the backtick key, usually below Esc) to toggle it
>
> This is necessary because Flatpak VS Code does **not automatically reload terminal config changes**.

---

## ✅ Done!

You now have a reliable way to switch between:

- 🧑‍💻 **ZSH dev shell** (with aliases, functions, and themes)
- 🐞 **BASH debugger shell** (required for working with VS Code debugger)

This works **even in sandboxed Flatpak VS Code** with **no sudo access**.

---

## 💡 Optional bonus

### 🪧 Add a visual reminder of the current shell mode

Add this line to your `.bashrc`:

```bash
echo "💪 You are in debugger (bash) mode — type 'vzsh' to switch back to zsh."
```

And this to your `.zshrc`:

```bash
echo "🔍 You are in zsh dev mode — type 'vdebug' to use the debugger."
```

---

## 🧠 Gotchas & FAQ

**Q: Why doesn’t the terminal switch instantly after running vdebug or vzsh?**  
**A:** Because VS Code loads shell settings *only when opening a new terminal*. You must close and reopen the terminal for the change to apply.

**Q: Can I make this automatic?**  
**A:** Not with Flatpak VS Code, since it blocks terminal control sequences that would normally restart the terminal. This is a limitation of the sandbox.

**Q: What if I mess up one of the config files?**  
**A:** No worries! Just copy a working `settings.json` and save it again as `settings.zsh.json` or `settings.debug.json`.

---

