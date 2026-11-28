# Reducing firmware size

The component compiles a few features that not every panel needs. They can be removed at build time; no fork patch is required.

## Disable the TFT uploader

If the HMI firmware is already installed (version `52` or `53`), add this under `esphome:`:

```yaml
esphome:
  build_flags:
    - -DDISABLE_NSPANEL_TFT_UPLOAD
```

This suppresses `-DUSE_NSPANEL_TFT_UPLOAD`, so the uploader code, the `esp_http_client` IDF component and its TLS options are not compiled. CI covers this variant (`codegen-no-tft`); see [Development and CI](development.md).

Delete any API action whose lambda calls `id(nspanel).upload_tft(...)` from the panel YAML — commonly named `upload_tft` and `upload_tft_hard`. The C++ method does not exist in this build, so keeping such an action fails at link time.

The tradeoff: if the display later goes blank, recovery requires flashing the TFT physically; the HTTP upload path is gone. See [TFT firmware and upload](tft-upload.md) for the firmware version and normal recovery procedure.

## Optional: drop native weather

`screensaver.weather` pulls in the native weather code and its Home Assistant service-call actions. Removing it changes the screensaver: daily forecast data will no longer be shown. See [Screensaver weather](screensaver-weather.md).

There are no switches for the icon catalog or translations — those are always compiled. Entity attributes are held in PSRAM.

[← Back to README](../README.md#install)
