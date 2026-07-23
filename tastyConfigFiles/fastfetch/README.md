## Applying the Configuration

Applying this configuration is simple: **just copy the provided config file into your Fastfetch config directory**. That's it — no additional steps are required.

Run the following command to copy the config file into place:

```bash
sudo cp -f YetAnotherArchInstall/tastyConfigFiles/fastfetch/config.jsonc /home/YOUR_USERNAME/.config/fastfetch
```

> Replace `YOUR_USERNAME` with your actual username.

## Notes

- If you have an existing Fastfetch configuration you want to keep, back it up first:
  ```bash
  sudo cp /home/YOUR_USERNAME/.config/fastfetch /home/YOUR_USERNAME/.config/fastfetch.bak
  ```
- Restart your terminal (or re-run `fastfetch`) for the changes to take effect.