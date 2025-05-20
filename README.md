## Prototype Development for Image Generation Using the Stable Diffusion Model and Gradio Framework
## AIM
To develop a prototype that utilizes the Stable Diffusion model for generating images from text prompts and integrate it with an interactive Gradio-based user interface.

## PROBLEM STATEMENT
The task involves creating a user-friendly interface where users can input text descriptions (prompts) to generate high-quality, realistic images using the Stable Diffusion model. The prototype aims to demonstrate the capabilities of the model while ensuring ease of interaction through Gradio.

##  STEPS
### Step 1: Set Up Environment
 - Install the required libraries (diffusers, transformers, torch, gradio).
 - Verify GPU support for running Stable Diffusion.
### Step 2: Load the Stable Diffusion Model
 - Use the diffusers library to load the Stable Diffusion pipeline.
### Step 3: Define the Gradio Interface
 - Design the user interface to accept text prompts and generate corresponding images.
 - Add parameters for customization (e.g., number of inference steps, guidance scale).
### Step 4: Integrate the Model with the Interface
 - Connect the Stable Diffusion model to the Gradio interface for real-time inference.
### Step 5: Deploy and Test
 - Launch the Gradio application locally or on a server.
 - Test with diverse prompts to ensure functionality.
## PROGRAM
```python
import os
import io
import IPython.display
from PIL import Image
import base64 
from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv()) # read local .env file
hf_api_key = os.environ['HF_API_KEY']

# Helper function
import requests, json

#Text-to-image endpoint
def get_completion(inputs, parameters=None, ENDPOINT_URL=os.environ['HF_API_TTI_BASE']):
    headers = {
      "Authorization": f"Bearer {hf_api_key}",
      "Content-Type": "application/json"
    }   
    data = { "inputs": inputs }
    if parameters is not None:
        data.update({"parameters": parameters})
    response = requests.request("POST",
                                ENDPOINT_URL,
                                headers=headers,
                                data=json.dumps(data))
    return json.loads(response.content.decode("utf-8"))

import gradio as gr 

#A helper function to convert the PIL image to base64
#so you can send it to the API
def base64_to_pil(img_base64):
    base64_decoded = base64.b64decode(img_base64)
    byte_stream = io.BytesIO(base64_decoded)
    pil_image = Image.open(byte_stream)
    return pil_image

def generate(prompt):
    output = get_completion(prompt)
    result_image = base64_to_pil(output)
    return result_image

gr.close_all()
demo = gr.Interface(fn=generate,
                    inputs=[gr.Textbox(label="Your prompt")],
                    outputs=[gr.Image(label="Result")],
                    title="Image Generation with Stable Diffusion",
                    description="Generate any image with Stable Diffusion",
                    allow_flagging="never",
                    examples=["the spirit of a tamagotchi wandering in the city of Vienna","a mecha robot in a favela"])

demo.launch(share=True, server_port=int(os.environ['PORT1']))

```
## OUTPUT
![Screenshot 2025-05-20 190532](https://github.com/user-attachments/assets/e9b55542-1f81-4f04-a3ff-14b5e39aef9f)

## RESULT
The prototype successfully demonstrates text-to-image generation using Stable Diffusion, providing an interactive and user-friendly interface. Users can modify parameters to influence the output quality and style.

