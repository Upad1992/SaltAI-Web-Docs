# Prepare Image For Clip Vision
## Documentation
- Class name: `PrepImageForClipVision`
- Category: `ipadapter`
- Output node: `False`

The PrepImageForClipVision node is designed to prepare images for processing by CLIP vision models. It adjusts images according to specified parameters such as interpolation, crop position, and sharpening to ensure they are in the optimal format for feature extraction by CLIP models.
## Input types
### Required
- **`image`**
    - Comfy dtype: `IMAGE`
    - The 'image' parameter represents the input image to be processed. It is crucial for the node's operation as it is the subject of the preparation process.
    - Python dtype: `torch.Tensor`
- **`interpolation`**
    - Comfy dtype: `COMBO[STRING]`
    - The 'interpolation' parameter specifies the method used to resize the image, affecting the quality and the way pixels are interpolated.
    - Python dtype: `str`
- **`crop_position`**
    - Comfy dtype: `COMBO[STRING]`
    - The 'crop_position' parameter determines the area of the image to focus on or how the image is cropped, influencing the portion of the image that will be analyzed by the CLIP model.
    - Python dtype: `str`
- **`sharpening`**
    - Comfy dtype: `FLOAT`
    - The 'sharpening' parameter adjusts the sharpness of the image, enhancing edge definition and detail visibility, which can affect the model's ability to extract features.
    - Python dtype: `float`
## Output types
- **`image`**
    - Comfy dtype: `IMAGE`
    - The output is a processed image, optimized for feature extraction by CLIP vision models, ensuring that the image is in the best possible state for analysis.
    - Python dtype: `torch.Tensor`
## Usage tips
- Infra type: `CPU`
- Common nodes: `IDGenerationNode,IPAdapterApply,AIO_Preprocessor,ImageBatch,CLIPVisionEncode,PrepImageForInsightFace,SetNode,IPAdapterApplyFaceID,PreviewImage,IPAdapterEncoder`


## Source code
```python
class PrepImageForClipVision:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "image": ("IMAGE",),
            "interpolation": (["LANCZOS", "BICUBIC", "HAMMING", "BILINEAR", "BOX", "NEAREST"],),
            "crop_position": (["top", "bottom", "left", "right", "center", "pad"],),
            "sharpening": ("FLOAT", {"default": 0.0, "min": 0, "max": 1, "step": 0.05}),
            },
        }

    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "prep_image"

    CATEGORY = "ipadapter"

    def prep_image(self, image, interpolation="LANCZOS", crop_position="center", sharpening=0.0):
        size = (224, 224)
        output = prepImage(image, interpolation, crop_position, size, sharpening, 0)
        return (output, )

```