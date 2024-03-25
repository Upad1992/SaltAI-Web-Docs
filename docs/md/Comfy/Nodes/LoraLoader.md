# Load LoRA
## Documentation
- Class name: `LoraLoader`
- Category: `loaders`
- Output node: `False`

The LoraLoader node is designed to dynamically load and apply LoRA (Low-Rank Adaptation) adjustments to models and CLIP instances based on specified strengths and LoRA file names. It facilitates the customization of pre-trained models by applying fine-tuned adjustments without altering the original model weights directly, enabling more flexible and targeted model behavior modifications.
## Input types
### Required
- **`model`**
    - Comfy dtype: `MODEL`
    - The model to which LoRA adjustments will be applied. It's crucial for customizing the model's behavior without changing its original structure. The choice of model directly influences the effectiveness and applicability of the LoRA adjustments, as different models may respond differently to the same set of adjustments.
    - Python dtype: `torch.nn.Module`
- **`clip`**
    - Comfy dtype: `CLIP`
    - The CLIP instance to which LoRA adjustments will be applied, allowing for customized behavior in processing visual and textual data. The adjustments can significantly alter how the CLIP model processes and interprets visual and textual inputs, thereby affecting the outcomes of tasks like image captioning or text-to-image generation.
    - Python dtype: `torch.nn.Module`
- **`lora_name`**
    - Comfy dtype: `COMBO[STRING]`
    - The name of the LoRA file containing the adjustments to be applied. This enables the selection of specific fine-tuning adjustments for the model and CLIP instance. The specific LoRA file chosen dictates the nature of the adjustments and can lead to varied enhancements or modifications in model performance.
    - Python dtype: `str`
- **`strength_model`**
    - Comfy dtype: `FLOAT`
    - Determines the intensity of the LoRA adjustments applied to the model. This allows for fine-grained control over the extent of model customization. Higher strengths mean more pronounced adjustments, which can lead to significant changes in model behavior, potentially improving performance on specific tasks.
    - Python dtype: `float`
- **`strength_clip`**
    - Comfy dtype: `FLOAT`
    - Determines the intensity of the LoRA adjustments applied to the CLIP instance. This allows for fine-grained control over the extent of CLIP customization. Similar to the model, higher strengths result in more noticeable changes, affecting how the CLIP model processes data.
    - Python dtype: `float`
## Output types
- **`model`**
    - Comfy dtype: `MODEL`
    - The model with LoRA adjustments applied, reflecting the specified customization. The adjustments can enhance the model's performance on specific tasks or alter its behavior to better suit particular applications.
    - Python dtype: `torch.nn.Module`
- **`clip`**
    - Comfy dtype: `CLIP`
    - The CLIP instance with LoRA adjustments applied, reflecting the specified customization. These adjustments can lead to improved or altered performance in tasks involving visual and textual data processing.
    - Python dtype: `torch.nn.Module`
## Usage tips
- Infra type: `GPU`
- Common nodes: `LoraLoader,CLIPTextEncode,Reroute,VideoLinearCFGGuidance,KSampler,FaceDetailer,ModelSamplingDiscrete,ADE_AnimateDiffLoaderWithContext,KSampler //Inspire,ToBasicPipe`


## Source code
```python
class LoraLoader:
    def __init__(self):
        self.loaded_lora = None

    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                              "clip": ("CLIP", ),
                              "lora_name": (folder_paths.get_filename_list("loras"), ),
                              "strength_model": ("FLOAT", {"default": 1.0, "min": -20.0, "max": 20.0, "step": 0.01}),
                              "strength_clip": ("FLOAT", {"default": 1.0, "min": -20.0, "max": 20.0, "step": 0.01}),
                              }}
    RETURN_TYPES = ("MODEL", "CLIP")
    FUNCTION = "load_lora"

    CATEGORY = "loaders"

    def load_lora(self, model, clip, lora_name, strength_model, strength_clip):
        if strength_model == 0 and strength_clip == 0:
            return (model, clip)

        lora_path = folder_paths.get_full_path("loras", lora_name)
        lora = None
        if self.loaded_lora is not None:
            if self.loaded_lora[0] == lora_path:
                lora = self.loaded_lora[1]
            else:
                temp = self.loaded_lora
                self.loaded_lora = None
                del temp

        if lora is None:
            lora = comfy.utils.load_torch_file(lora_path, safe_load=True)
            self.loaded_lora = (lora_path, lora)

        model_lora, clip_lora = comfy.sd.load_lora_for_models(model, clip, lora, strength_model, strength_clip)
        return (model_lora, clip_lora)

```