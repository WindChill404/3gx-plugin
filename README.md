# ALBW AP on a 3DS

## If you are here from Caroline's upstream apworld, you do not need to install any custom apworlds or libs, only the 3DS plugin and generated patch must be copied to your 3DS's SD card.

1. Download the `plugin.3gx` [releases](https://github.com/LittlestCube/albw-ap-plugin/releases/latest).

2. Copy `plugin.3gx` to `/luma/plugins/00040000000EC300/` on your SD card.

3. Once you generate a zip from the .apalbw patch, copy the folder inside to `/luma/titles/` on your SD card.

4. Re-insert your SD card into your 3DS and power it on.

5. On the Luma3DS configuration screen after power-up (if this screen does not show up, hold SELECT during power-up):
	1. _Make sure_ that `Enable loading external FIRMs and modules` does **NOT** have an x next to it. If so, disable it.
	2. Turn `Enable game patching` on and make sure it **DOES** have an x next to it.
	3. Press Start or choose `Save and exit`.

6. Press L+DPadDown+Select to open the Rosalina menu, and make sure that `Plugin loader` is set to `[Enabled]`.

7. Run A Link Between Worlds.

8. At the end of the red 3DS loading screen, you should see a blue flash. This means the plugin has loaded successfully.

9. Run the command on-screen into your ALBW APWorld client.

10. Profit.
