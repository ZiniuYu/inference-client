# inference-client

Inference Client is a library that provides a simple and efficient way to use Jina AI's Inference, a powerful platform that offers a range of AI models for common tasks such as visual reasoning, question answering, and embedding modalities like texts and images. 
With Inference Client, you can easily select the task and model of your choice and integrate the API call into your workflow with zero technical overhead. 

The current version of Inference Client includes methods to call the following tasks:

📈**Encode**: Encode data into embeddings using various models 

🔍**Rank**: Re-rank cross-modal matches according to their joint likelihood

📷**Caption**: Generate captions for images 

🤔**VQA**: Answer questions related to images 

In addition to these tasks, the client provides the ability to connect to the inference server and user authentication.

## Installation

Please note that Inference Client requires Python 3.8 or higher. Inference Client can be installed via pip by executing:
```bash
pip install inference-client
```

## Getting Started

Before using Inference Client, please create an inference on [Jina AI Cloud](https://cloud.jina.ai/user/inference).

### Client Initialization

To initialize the Client object and connect to the inference server, you can choose to pass a valid personal access token to the token parameter.
A personal access token can be generated at the [Jina AI Cloud](https://cloud.jina.ai/settings/tokens), or via CLI as described in [this guide](https://docs.jina.ai/jina-ai-cloud/login/#create-a-new-pat):
```bash
jina auth token create <name of PAT> -e <expiration days>
```

To pass the token to the client, you can use the following code snippet:

```python
from client import Client

# Initialize client with valid token
client = Client(token='valid token')
```

If you don't provide a token explicitly, Inference Client will look for a `JINA_AUTH_TOKEN` environment variable, otherwise it will try to authenticate via browser.

```python
from client import Client

client = Client()
```

Please note that while it's possible to login via the Jina AI web UI, this method is intended primarily for development and testing purposes. 
For production use, we recommend obtaining a long-lived token via the Jina AI web API and providing it to the Client object explicitly. 
Tokens have a longer lifetime than web sessions and can be securely stored and managed, making them a more suitable choice for production environments.

### Selecting the Model

To select an inference model, you can use the get_model method of the Client object and specify the name of the model as it appears in Jina AI Cloud. 
You can connect to as many inference models as you want once they have been created on Jina AI Cloud, and you can use them for multiple tasks.

Here's an example of how to connect to two models and encode some text using each of them:
    
```python
from client import Client

# Initialize client
inference_client = Client()

# Connect to CLIP model
clip_model = inference_client.get_model('ViT-B-32::openai')
clip_embed = clip_model.encode(text='hello world')[0].embeddings

# Connect to BLIP2 model
blip2_model = inference_client.get_model('Salesforce/blip2-opt-2.7b')
blip2_embed = blip2_model.encode(text='hello jina')[0].embeddings
```

### Encoding

To use the encode method of an inference model, you need to initialize the model and provide input data as DocumentArray, plain text, or an image. 
Here are some examples of how to use the encode method:

1. Encode a `DocumentArray`:

```python
from client import Client
from docarray import Document, DocumentArray

# Initialize client
inference_client = Client()

# Connect to CLIP model
clip_model = inference_client.get_model('ViT-B-32::openai')

# Create a DocumentArray with two documents
docs = DocumentArray(
    [Document(text='hello world'), Document(uri='https://picsum.photos/id/233/100')]
)

# Encode the documents
response = clip_model.encode(docs=docs)

# Access the embeddings
for doc in response:
    print(doc.embedding)
```

2. Encode plain text:

```python
# Encode the documents
response = clip_model.encode(text='hello world')

# Access the embeddings
print(response[0].embedding)
```

3. Encode an image:

```python
# Encode image URL
response = clip_model.encode(image='https://picsum.photos/id/233/100')

# Access the embedding
print(response[0].embedding)

# Encode image binary data
image_bytes = open('my_image.jpg', 'rb').read()
response = clip_model.encode(image=image_bytes)

# Access the embedding
print(response[0].embedding)

# Encode image tensor data
import torch
from PIL import Image
import torchvision.transforms as transforms

image_bytes = Image.open('my_image.jpg')
transform = transforms.ToTensor()
image_tensor = transform(image_bytes)
response = clip_model.encode(image=image_tensor)

# Access the embedding
print(response[0].embedding)
```

## Support

- Join our [Slack community](https://slack.jina.ai) and chat with other community members about ideas.
- Watch our [Engineering All Hands](https://youtube.com/playlist?list=PL3UBBWOUVhFYRUa_gpYYKBqEAkO4sxmne) to learn Jina's new features and stay up-to-date with the latest AI techniques.
- Subscribe to the latest video tutorials on our [YouTube channel](https://youtube.com/c/jina-ai)

## License

Inference Client is backed by [Jina AI](https://jina.ai) and licensed under [Apache-2.0](./LICENSE). 