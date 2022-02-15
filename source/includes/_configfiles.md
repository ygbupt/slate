# Config Files

VanceAI currently has 12 AI features for image enhancement, each one has one config file at least. And we are continue to add more AI features which will cover the fields of image, video and music.

Generally, all the config files have a fixed structure. let's take an example by using AI Enlarger.

## Structure of config file

Configuration is required when calling transform endpoint. VanceAI supports single feature mode and workflow (combination of multiple steps) mode.

### Single Feature

> Single Feature Configuration

```json
{
	"job": "enlarge",
	"config": {
		"module": "enlarge",
		"module_params": {
			"model_name": "EnlargeStable",
			"suppress_noise": 26,
			"remove_blur": 26,
			"scale": "2x"
		},
		"out_params": {
		  "compress": {
		    "quality": 95
		  },
		  "dpi": 300,
		  "format": "png",
		  "bg_composed": {
			"bg_color": {
			  "r": 0,
			  "g": 255,
			  "b": 255
			}
		  }
		}
	}
}
```

Single feature means to process image with only one feature, like enlarge, denoise or colorise.

To process an image, you will need to send a job to the cloud server. We use `job` to tell the server what kind of job is it. In this case, the job is `enlarge`.

In the `config`, we config which `module` to use and the related `module_params`. And in the `out_params`, it defines some parameters of the output file, such as the format, quality and dpi.

### Workflow

> Workflow Configuration

```json
{
	"job": "workflow",
	"config": [
		{
			"name": "enlarge",
			"config": {
				"module": "enlarge",
				"module_params": {
					"model_name": "EnlargeStable",
					"autoparams": false,
					"suppress_noise": 65,
					"remove_blur": 65,
					"scale": "1x"
				}
			}
		},
		{
			"name": "matting",
			"config": {
				"module": "matting",
				"module_params": {
					"model_name": "MattingStable",
					"rescale": 532
				},
				"out_params": {
					"compress": {
						"quality": 100
					},
					"bg_composed": {
						"bg_color": {
							"r": 132,
							"g": 142,
							"b": 109
						}
					},
					"dpi": 72,
					"format": "jpg"
				}
			}
		}
	]
}
```

You can also add multiple features in the configuration, the server will process your image in the order of the added features.

There are some differences between the structure of the Workflow configuration file and the single feature. For details, you can check the demo code.

The demo code provides a <strong>two-step</strong> workflow, the first step is enlarge, the second step is background remove.

You can use our [workflow configuration generator](https://vanceai.com/config-generator/?source=api_docs) to build your json file.

<aside class="notice">
Note:
  <ul>
    <li>The <code>out_params</code> should be added to the last step.</li>
    <li>Enlarge and Background remove can only be the last step of workflow.</li>
    <li>Anime Upscaler currently can not be used in workflow. But we will add support in the future.</li>
  </ul>
</aside>

### Supported output parameters

Settings | Type | Example | Note
-------- | ---- | ------- | ----
Quality | int | {"compress": {"quality": 95}} | The range is 0-100, the higher the number, the compress the less.
DPI | int | {"dpi": 300} | The range is 0-2000 (suggested). Generally, 300 or 350 is suitable for print.
Format | string | {"format": "png"} | Now we only accept `jpg` and `png`.
Background Color | int | {"bg_composed": {"bg_color": {"r": 0, "g": 255, "b": 255}}} | This can be used to set a solid background color to the background removed photo.

## Description of config file

### AI Image Enlarger

> AI Image Enlarger

```json
{
	"job": "enlarge",
	"config": {
		"module": "enlarge",
		"module_params": {
			"model_name": "EnlargeStable",
			"auto_params": true,
			"suppress_noise": 26,
			"remove_blur": 26,
			"scale": "2x"
		}
	}
}
```

Enlarge image up to 800% while keeping high quality.

Key | Value | Description
--- | ----- | -----------
job | enlarge | 
module | enlarge | 
model_name | EnlargeStable | 
auto_params | true or false | If true, then `suppress_noise` and `remove_blur` will be ignored. AI will select property values automatically according to the training.
suppress_noise | 0 - 100 | 
remove_blur | 0 - 100 | 
scale | 2x, 4x, 6x, 8x | 

### AI Image Denoiser 

> AI Image Denoiser

```json
{
	"job": "denoise",
	"config": {
		"module": "denoise",
		"module_params": {
			"model_name": "DenoiseStable",
			"auto_params": true,
			"remove_noise": 75,
			"sharpen" : 75
		}
	}
}
```

It removes grain and noise from photo effortlessly through AI-powered denoise algorithms.

Key | Value | Description
--- | ----- | -----------
job | denoise | 
module | denoise | 
model_name | DenoiseStable | 
auto_params | true or false | If true, then `remove_noise` and `sharpen` will be ignored. AI will select property values automatically according to the training.
remove_noise | 0 - 100 | 
sharpen | 0 - 100 | 

### AI Image Sharpener 

> AI Image Sharpener

```json
{
	"job": "sharpen",
	"config": {
		"module": "sharpen",
		"module_params": {
			"model_name": "SharpenStable",
			"auto_params": true,
			"sharpness": 75,
			"suppress_noise": 75
		}
	}
}
```

Key | Value | Description
--- | ----- | -----------
job | sharpen | 
module | sharpen, debouncing | Two modules available, `sharpen` for softness images and  `debouncing` for motion blur images.
model_name | SharpenStable, DebouncingStable | 
auto_params | true or false | If true, then `sharpeness` and `suppress_noise` will be ignored. AI will select property values automatically according to the training.
sharpeness | 0 - 100 | 
suppress_noise | 0 - 100 | 

### AI Background Remover 

> AI Background Remover

```json
{
	"job": "matting",
	"config": {
		"module": "matting",
		"module_params": {
			"model_name": "MattingStable",
			"rescale": 532
		}
	}
}
```

Key | Value | Description
--- | ----- | -----------
job | matting | 
module | matting | 
model_name | MattingStable | 
rescale | 532 | The internal code of the recognition accuracy.

### AI Anime Upscaler 

> AI Anime Upscaler - waifu

```json
{
  "job": "waifu",
  "config": {
    "module": "waifu",
    "module_params": {
      "model_name": "models-cunet",
      "noise_level": 2,
      "scale": "2x"
    }
  }
}
```

<strong>Model of waifu</strong>

Key | Value | Description
--- | ----- | -----------
job | waifu | 
module | waifu | 
model_name | models-cunet, models-upconv_7_anime_style_art_rgb | 2 models for waifu
noise_level | 0, 1, 2, 3 | The level of reduce noise. 0: No, 1: Little, 2: Medium, 3: Much
scale | 2x, 4x, 8x, 16x | 

> AI Anime Upscaler - real_esrgan

```json
{
	"job":"real_esrgan",
	"config": {
		"module":"real_esrgan",
		"module_params": {
			"model_name": "RealEsrganStable",
			"scale": "2x"
		}
	}
}
```

<strong>Model of RealEsrgan</strong>

Key | Value | Description
--- | ----- | -----------
job | real_esrgan | 
module | real_esrgan | 
model_name | RealEsrganStable | 
scale | 1x, 2x, 4x, 6x, 8x | If selected 1x, it will only enhance the quality without enlargement.

### AI JPEG Artifacts Remover

> AI JPEG Artifacts Remover

```json
{
	"job":"jpeg_artifact",
	"config": {
		"module": "hinet_jpeg",
		"module_params": {
			"model_name": "HinetStable",
			"deblur": true
		}
	}
}
```

Key | Value | Description
--- | ----- | -----------
job | jpeg_artifact | 
module | hinet_jpeg | 
model_name | HinetStable | 
deblur | true or false | An option for if you want to deblur the image.

### AI Photo Dehaze

> AI Photo Dehaze

```json
{
	"job":"se-dehazing",
	"config":[
		{
			"name":"dehazing",
			"config":{
				"module":"dehazing",
				"module_params":{
					"model_name":"DehazingStable",
					"contrast":1,
					"brightness":20
				}
			}
		},
		{
			"name":"jpeg_artifact",
			"config":{ 
				"module":"hinet_jpeg",
				"module_params":{
					"model_name":"HinetStable",
					"deblur":true
				}
			}
		}
	]
}
```

For a better result, this feature is a 2-step workflow by default. The table below only lists the parameters in `dehazing`.

Key | Value | Description
--- | ----- | -----------
job | se-dehazing | 
module | dehazing | 
model_name | DehazingStable | 
contrast | 0.0 - 2.0 | Set the contrast of output image. Default: `1`
brightness | 0 - 100 | Set the brightness of output image. Default: `20`

### AI Photo Restorer

> AI Photo Restorer

```json
{
	"job": "repair",
	"config": {
		"module": "repair",
		"module_params": {
			"model_name": "RepairStable",
			"with_scratch": true,
			"inpainting": true
		}
	}
}
```

Key | Value | Description
--- | ----- | -----------
job | repair | 
module | repair | 
model_name | RepairStable | 
with_scratch | true or false | Set to `true` to remove scratches.
inpainting | true or false | Set to `true` to inpaint damaged area.

### Toongineer Cartoonizer

> Toongineer Cartoonizer

```json
{
	"job": "cartoonize",
	"config": {
		"module": "cartoonize",
		"module_params": {
			"model_name": "CartoonizeStable"
		}
	}
}
```

Key | Value | Description
--- | ----- | -----------
job | cartoonize | 
module | cartoonize | 
model_name | CartoonizeStable | 

### VancePortrait

> VancePortrait - AnimeGANv2

```json
{
	"job": "animegan",
	"config": {
		"module": "animegan2",
		"module_params": {
			"model_name": "Animegan2Stable",
			"single_face": true
		}
	}
}
```

<strong>Model of AnimeGANv2</strong>

This model will convert portrait to anime style.

![Anime Style](https://c.vanceai.com/assets/images/portraits/compare_02.jpg)

Key | Value | Description
--- | ----- | -----------
job | animegan | 
module | animegan2 | 
model_name | Animegan2Stable | 
single_face | true or false | Set to `true` to only convert human face. Default: `true`

> VancePortrait - Sketch

```json
{
	"job": "sketch",
	"config": {
		"module": "sketch",
		"module_params": {
			"model_name": "SketchStable",
			"single_face": true,
			"composite": true,
			"sigma": 0,
			"alpha": 0
		}
	}
}
```

<strong>Model of AnimeGANv2</strong>

This model will convert image to sketch style.

![Sketch Style](https://c.vanceai.com/assets/images/portraits/compare_01.jpg)

Key | Value | Description
--- | ----- | -----------
job | sketch | 
module | sketch | 
model_name | SketchStable | 
single_face | true or false | Set to `true` to only convert human face. Default: `true`
composite | true or false | Set to `true` to enable `sigma` and `alpha`.
sigma | 0 - 100 | Set the softness of the result.
alpha | 0 - 100 | Set the saturation of the result.

You can use the preset value below:

<img src="/images/sketch-9.png" alt="Sketch Preset Value" width="280" />

  0  |   1  |   2  |   3  
---- | ---- | ---- | -----
1 | Sigma: 0, Alpha: 0 | Sigma: 2, Alpha: 0.35 | Sigma: 20, Alpha: 0.35
2 | Sigma: 2, Alpha: 0.5 | Sigma: 10, Alpha: 0.5 | Sigma: 20, Alpha: 0.5
3 | Sigma: 2, Alpha: 0.75 | Sigma: 10, Alpha: 0.75 | Sigma: 20, Alpha: 0.75

### AI Photo Colorizer

> AI Photo Colorizer

```json
{
	"job": "colorize",
	"config": {
		"module": "deoldify",
		"module_params": {
			"model_name": "ColorizeStable_pth",
			"render_factor": 30
		}
	}
}
```

Key | Value | Description
--- | ----- | -----------
job | colorize | 
module | colorize or deoldify | `colorize` is fast but `deoldify` has a better output. 
model_name | ColorizeStable_pth or ColorizeStable | deoldify -> ColorizeStable_pth, colorize -> ColorizeStable
render_factor | 0 - 40 | Default: 30 for `deoldify` and 10 for `colorize`

### AI Photo Retoucher

> AI Photo Retoucher

```json
{
	"job": "perfect",
	"config": {
		"module": "perfect",
		"module_params": {
			"model_name": "perfect_2k"
		}
	}
}
```

Key | Value | Description
--- | ----- | -----------
job | perfect | 
module | perfect |  
model_name | perfect_2k | 

### Download Config Files

Download the [Config Files](https://c.vanceai.com/config/infer_api/API_JSON_CONFIG.zip) here.
