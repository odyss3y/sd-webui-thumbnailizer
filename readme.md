# About Thumbnailizer

Thumbnailizer is an extension for managing and generating multiple sets of thumbnails for checkpoints in Automatic1111.

Easily switch between different sets of thumbnails to see how your checkpoints handle your preset prompts.

It's like an X/Y/Z-grid for checkpoints that you can easily update and always re-visit.

# Fork Development Target

This fork is developed and tested primarily against [Haoming02/sd-webui-forge-classic `neo`](https://github.com/Haoming02/sd-webui-forge-classic/tree/neo), the Forge Neo branch used in the local install.

Compatibility with upstream Automatic1111 and older Forge builds should be preserved where practical, but new testing and compatibility fixes are expected to prioritize Forge Neo behavior first.

https://github.com/MNeMoNiCuZ/sd-webui-thumbnailizer/assets/60541708/dd04d7b6-6506-4847-8405-b71925e2e6d9

# Features
## Thumbnail Set Gallery
Switch between different sets of thumbnails for your checkpoints. Compare how all your checkpoints handle the same prompt.

![image](https://github.com/MNeMoNiCuZ/sd-webui-thumbnailizer/assets/60541708/20cf66b0-b2d1-4d8f-805c-268b25cc6df2)

## Customizable Sets
Edit thumbnail generation presets directly in the Thumbnailizer tab, including prompt, negative prompt, sampler, scheduler when available, steps, size, CFG, seed, and advanced prompt affix fields.

Preset edits are saved to `scripts/sets_user.json`. The bundled `scripts/sets_template.json` is only used as the first-run seed file and is not rewritten by the editor.

You can still edit `scripts/sets_user.json` manually if you need to audit or repair the persisted preset data.

### Preset field meanings

`Display Name` is the human-readable name shown in the Set List. It does not control the output filename.

`Thumbnail Filename Suffix` is the thumbnail variant key. Thumbnailizer writes files beside each checkpoint, so an empty suffix targets the default thumbnail name such as `model.png`, while `environment` targets `model.environment.png`. Use a short unique suffix for every non-default preset. It does not have to match the display name, though matching it as a lowercase slug can make the files easier to audit.

`Prompt Prefix`, `Prompt`, and `Prompt Suffix` are saved as separate static fields but are combined at generation time as `Prompt Prefix + Prompt + Prompt Suffix`. The negative prompt fields work the same way. Prefix/suffix fields are useful when you want to reuse a broad wrapper, such as model trigger words, quality tags, camera/style endings, or default negative terms, without burying that wrapper inside every main prompt. They live behind the advanced prompt affix expander in the UI by default.

These fields are not dynamic variables yet. Explicit wildcard expansion and richer prompt composition are tracked separately for future sd-dynamic-prompts support.

![image](https://github.com/MNeMoNiCuZ/sd-webui-thumbnailizer/assets/60541708/58d3c44c-bef0-425a-80fb-860774070559)


## Thumbnail Set Generation
Batch generate thumbnails for explicitly selected checkpoint targets based on the selected preset. The target selector lists relative checkpoint paths so duplicate model filenames in different folders stay distinguishable.

![image](https://github.com/MNeMoNiCuZ/sd-webui-thumbnailizer/assets/60541708/40930bd2-6232-4e4e-803e-0b1f268731df)

_Use Select Missing to target only checkpoints without thumbnails for the selected preset. Use Select Visible to target every checkpoint in the current folder filter. Enable "Regenerate thumbnails that already exist" only when existing files should be replaced._

## Generate for All Sets
![image](https://github.com/user-attachments/assets/79e3b0dc-245b-47a7-81cb-ef941f488be2)

_Use this button to generate every saved preset for the currently selected checkpoint targets. Select the targets first; the batch action does not silently expand to the whole checkpoint library._

## Use Override Settings
![image](https://github.com/user-attachments/assets/6970f0f7-28be-41d9-8315-5028d7915fb9)

_Use this setting to override generation settings from the override_settings_user.txt-file. This will be created during a first launch if it doesn't exist. This is checked when generation starts and does not require a restart of the webui._

This is meant to let you re-use the same set-prompts but change settings like CFG Scale or add some specific triggers as a prefix for the prompts (useful for Pony-models).

## Customizable Blocklist
Use the drop-down model selector to select some models to ignore.

![image](https://github.com/MNeMoNiCuZ/sd-webui-thumbnailizer/assets/60541708/13f5708a-d59f-4191-bd76-3cd61807bdef)
_Use this list disable thumbnail generation for SDXL models if you want to generate them with other settings. You can manually swap between different blocklist files for now._

## Blocked Folders / Paths
![image](https://github.com/user-attachments/assets/cd5a53bd-5dc6-4966-8d8d-35e78bd660c6)
_You can select entire folders that should be ignored by the tool. Useful if you have models that need specific settings, or want to switch between generating thumbnails for SDXL / SD1.5._

## Supports Civitai Helper Thumbnails
If you are already using the [Civitai Helper-extension](https://github.com/zixaphir/Stable-Diffusion-Webui-Civitai-Helper/) (forked from [this one](https://github.com/butaixianran/Stable-Diffusion-Webui-Civitai-Helper)), to download thumbnails and model info, their thumbnails are saved as modelname.preview.png. This is added to the set-list as one of the types, so you can easily switch to the original model thumbnails to view them, even if you are customizing your own sets.
The Preview set is display-only in Thumbnailizer: it shows those Civitai Helper files but cannot be edited, deleted, duplicated, or used as a generation preset.

![image](https://github.com/MNeMoNiCuZ/sd-webui-thumbnailizer/assets/60541708/5732cc16-972f-4259-b875-d47da4f190c5)


# Installation
Make sure to disable the `Add number to filename when saving` option in `Settings` for A1111 as this may interfere with the generated thumbnails.

You can use the official extension-list and just search for "thumbnailizer":
![image](https://github.com/MNeMoNiCuZ/sd-webui-thumbnailizer/assets/60541708/7266414e-199a-4276-bea4-880a6ed29ac1)

Alternatively you can go to the Extensions-tab in A1111 and select the "Install from URL" sub-tab, and paste the URL to this extension there `https://github.com/MNeMoNiCuZ/sd-webui-thumbnailizer`. Do not forget to restart your A1111 after.

![image](https://github.com/MNeMoNiCuZ/sd-webui-thumbnailizer/assets/60541708/d7c188ef-40c7-415e-a984-191cf52f0c51)

You can manually install the extension by downloading this space and placing it in your /stable-diffusion-webui/extensions-folder. To verify the folder structure afterwards, you should have a path like this: `\stable-diffusion-webui\extensions\sd-webui-thumbnailizer\scripts\`

# Known Issues
>IndexError: list index out of range

The script failed to count the number of images correctly. Refresh the gallery and reselect checkpoint targets so the selected relative paths match the currently visible checkpoint list.

>AttributeError: 'NoneType' object has no attribute 'get'

Likely an initialization issue. Try force-refreshing your A1111, or restarting the A1111 server completely. Let it finish loading everything before using Thumbnailizer. It's sensitive.

>Thumbnails keep getting numbers added to their names (00001- or 00003 etc).

Make sure you disable the `Add number to filename when saving` option in `Settings` for A1111 as this interferes with the generated thumbnails.


# Changelog
* 0.29 - Initial release
* 0.30 - Include metadata in generated thumbnails
* 0.31 - Generate a sets_user.json
* 0.32 - Generate a blocklist_user.json
* 0.33 - Added [Blocked Paths](https://github.com/MNeMoNiCuZ/sd-webui-thumbnailizer/blob/main/readme.md#blocked-folders--paths).
* 0.34 - Added [Override Settings](https://github.com/MNeMoNiCuZ/sd-webui-thumbnailizer/blob/main/readme.md#use-override-settings).
* 0.35 - Optimized multi-set generation, fixed bugs with the wrong set being generated.
* 0.36 - Fixed bugs with . or _ in names and Overwrite setting was ignored for set generation.
