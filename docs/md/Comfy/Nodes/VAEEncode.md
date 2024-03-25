# VAE Encode
## Documentation
- Class name: `VAEEncode`
- Category: `latent`
- Output node: `False`

This node is designed for encoding images into a latent space representation using a specified VAE model. It abstracts the complexity of the encoding process, providing a straightforward way to transform images into their latent representations.
## Input types
### Required
- **`pixels`**
    - Comfy dtype: `IMAGE`
    - The 'pixels' parameter represents the image data to be encoded into the latent space. It plays a crucial role in determining the output latent representation by serving as the direct input for the encoding process.
    - Python dtype: `torch.Tensor`
- **`vae`**
    - Comfy dtype: `VAE`
    - The 'vae' parameter specifies the Variational Autoencoder model to be used for encoding the image data into latent space. It is essential for defining the encoding mechanism and characteristics of the generated latent representation.
    - Python dtype: `comfy.sd.VAE`
## Output types
- **`latent`**
    - Comfy dtype: `LATENT`
    - The output is a latent space representation of the input image, encapsulating its essential features in a compressed form.
    - Python dtype: `Dict[str, torch.Tensor]`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,KSamplerAdvanced,SetLatentNoiseMask,ImpactKSamplerBasicPipe,KSampler (Efficient),BNK_Unsampler,LatentUpscale,KSampler //Inspire,DZ_Face_Detailer,LatentUpscaleBy`


## Source code
```python
class VAEEncode:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "pixels": ("IMAGE", ), "vae": ("VAE", )}}
    RETURN_TYPES = ("LATENT",)
    FUNCTION = "encode"

    CATEGORY = "latent"

    def encode(self, vae, pixels):
        t = vae.encode(pixels[:,:,:,:3])
        return ({"samples":t}, )

```