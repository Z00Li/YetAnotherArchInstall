## Applying the Configuration

Applying this configuration is simple: **just copy the provided config file into your Ghostty config directory**. That's it — no additional steps are required.

Run the following command to copy the config file into place:

```bash
sudo cp -f YetAnotherArchInstall/tastyConfigFiles/ghostty/config.ghostty /home/YOUR_USERNAME/.config/ghostty
```

> Replace `YOUR_USERNAME` with your actual username.
> 
Restart Ghostty (or reload its configuration, with `Shift+Ctrl+,`) for the changes to take effect.

## Notes

- If you have an existing Ghostty configuration you want to keep, back it up first:
  ```bash
  sudo cp /home/YOUR_USERNAME/.config/ghostty /home/liminal/.config/ghostty.bak
  ```
  > Replace `YOUR_USERNAME` with your actual username.
  
- Restart Ghostty (or reload its configuration, with `Shift+Ctrl+,`) for the changes to take effect.