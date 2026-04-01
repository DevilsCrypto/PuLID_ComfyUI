# PuLID ComfyUI

[PuLID](https://github.com/ToTheBeginning/PuLID) ComfyUI native implementation.

![basic workflow](examples/pulid_wf.jpg)

> [!IMPORTANT]  
> **2025.04.14** - I do not use ComfyUI as my main way to interact with Gen AI anymore as a result I'm setting the repository in "maintenance only" mode. If there are crucial updates or PRs I might still consider merging them but I do not plan any consistent work on this repo.

## Notes

The code can be considered beta, things may change in the coming days. In the `examples` directory you'll find some basic workflows.

The loader now detects both PuLID `v1` and `v1.1` checkpoints automatically. When you use a `v1.1` checkpoint, image batches passed into the `image` input are combined using the official multi-reference `IDFormer` path instead of averaging per-image embeddings.

The original implementation makes use of a [4-step lighting UNet](https://huggingface.co/ByteDance/SDXL-Lightning). I made a few comparisons with the official Gradio demo using the same model in ComfyUI and I can't see any noticeable difference, meaning that this code should be faithful to the orignal. The Lightning lora doesn't work as well.

Testing other models though I noticed some quality degradation. You may need to experiment with CFG and various samplers/schedulers (try `sgm_uniform`).

**The quality of the reference image is very important**. Maybe this is because of the Eva CLIP that gets more details. Be sure to use a clean and sharp picture!

**For IPAdapter compatibility you need to update the IPAdapter extension!**

PuLID `v1.1` does not perform as well as `v1` on the SDXL lightning 4-step base model. If that is your main base model, `v1` is still the safer choice.

## The 'method' parameter

`method` applies the weights in different ways. `Fidelity` is closer to the reference ID, `Style` leaves more freedom to the checkpoint. Sometimes the difference is minimal. I've added `neutral` that doesn't do any normalization, if you use this option with the standard Apply node be sure to lower the weight. With the Advanced node you can simply increase the `fidelity` value.

The Advanced node has a `fidelity` slider and a `projection` option. `ortho_v2` with `fidelity: 8` is the same as `fidelity` method in the standard node. Projection `ortho` and `fidelity: 16` is the same as method `style`.

**Lower `fidelity` values grant higher resemblance to the reference image.**

## Installation

- [PuLID pre-trained model](https://huggingface.co/huchenlei/ipadapter_pulid/resolve/main/ip-adapter_pulid_sdxl_fp16.safetensors?download=true) goes in `ComfyUI/models/pulid/` (thanks to [Chenlei Hu](https://github.com/huchenlei) for converting them into IPAdapter format)
- [PuLID v1.1 converted weights](https://huggingface.co/Runzy/ip-adapter_pulid/tree/main) can also be placed in `ComfyUI/models/pulid/`
- The EVA CLIP is EVA02-CLIP-L-14-336, but should be downloaded automatically (will be located in the huggingface directory).
- `facexlib` dependency needs to be installed, the models are downloaded at first use
- Finally you need InsightFace with [AntelopeV2](https://huggingface.co/MonsterMMORPG/tools/tree/main), the unzipped models should be placed in `ComfyUI/models/insightface/models/antelopev2`.
