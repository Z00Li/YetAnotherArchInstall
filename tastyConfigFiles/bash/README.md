## Applying the Configuration

Applying this configuration is simple: **just copy the provided `.bashrc` file into your `/home` directory**, replacing the existing one. That's it — no additional steps are required.

Run the following command to copy and replace the existing `.bashrc` file:

```bash
sudo cp -f YetAnotherArchInstall/tastyConfigFiles/Bash/.bashrc /home
```

## Notes

- This will **overwrite** your current `.bashrc` file. If you have custom settings in your existing `.bashrc` that you want to keep, back it up first:
  ```bash
  cp /home/.bashrc /home/.bashrc.bak
  ```
- After copying, reload your shell configuration for the changes to take effect:
  ```bash
  source /home/.bashrc
  ```
- As with the other configuration files in `tastyConfigFiles`, this `.bashrc` is a starting point — feel free to further customize it to your liking.