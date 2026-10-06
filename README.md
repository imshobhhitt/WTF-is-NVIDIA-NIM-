# Generative AI on AWS Cloud

A collection of Generative AI projects demonstrating large language model (LLM) deployment and inference on AWS cloud infrastructure, covering Amazon SageMaker and AWS Bedrock.

---

## Projects

### 1. AWS SageMaker — Falcon-40B-Instruct Deployment

**Directory:** `AWS sagemaker/`

Deploys the open-source [Falcon-40B-Instruct](https://huggingface.co/tiiuae/falcon-40b-instruct) model on Amazon SageMaker using the Hugging Face LLM Inference Container (backed by [Text Generation Inference / TGI](https://github.com/huggingface/text-generation-inference)).

**Key capabilities:**
- Provisions a SageMaker endpoint on a `ml.g5.12xlarge` instance (4× NVIDIA A10G GPUs, 96 GB GPU memory)
- Automatic model sharding across all GPUs via TGI
- Demonstrates zero-shot, one-shot, and few-shot prompt engineering patterns
- Configurable generation parameters: `temperature`, `top_p`, `max_new_tokens`, `repetition_penalty`, etc.
- Includes endpoint teardown to avoid lingering AWS costs

**Files:**
| File | Description |
|------|-------------|
| `falcon40B-instruct-notebook-full.ipynb` | End-to-end notebook: environment setup → model deployment → inference → cleanup |
| `Untitled (2).ipynb` | Supplementary/scratch notebook |
| `test.py` | Minimal test script |

**Prerequisites:**
- An active AWS account with SageMaker access
- IAM role with `AmazonSageMakerFullAccess` permissions
- Sufficient service quota for GPU instances (`ml.g5.12xlarge`)

**Quickstart (inside SageMaker Studio or a notebook instance):**
```bash
pip install --upgrade boto3 sagemaker
# Then run the cells in falcon40B-instruct-notebook-full.ipynb sequentially
```

---

### 2. Blog Generation — AWS Lambda + Bedrock (LLaMA 2)

**Directory:** `Blog generation in aws/`

A serverless blog-generation backend built as an AWS Lambda function. Given a topic, it invokes Meta's **LLaMA 2-13B Chat** model via **AWS Bedrock** to produce a ~200-word blog post, then persists the output to an **Amazon S3** bucket.

**Architecture:**
```
API Gateway → Lambda (app.py) → AWS Bedrock (meta.llama2-13b-chat-v1) → S3
```

**Key capabilities:**
- Stateless Lambda handler accepting a JSON event with a `blog_topic` field
- Bedrock invocation with configurable generation settings (`max_gen_len`, `temperature`, `top_p`)
- Timestamp-keyed S3 persistence under `blog-output/` prefix
- Error handling for both Bedrock inference and S3 upload failures

**Files:**
| File | Description |
|------|-------------|
| `app.py` | Lambda handler with Bedrock inference and S3 persistence logic |
| `requirements.txt` | Python dependencies (`boto3`) |

**Prerequisites:**
- AWS account with Bedrock access and the `meta.llama2-13b-chat-v1` model enabled in `us-east-1`
- An S3 bucket named `aws_bedrock_course1` (or update the bucket name in `app.py`)
- Lambda execution role with `bedrock:InvokeModel` and `s3:PutObject` permissions

**Deploying the Lambda:**
```bash
pip install -r requirements.txt -t ./package
# Zip app.py + package/ contents and upload to AWS Lambda
```

**Sample event payload:**
```json
{
  "body": "{\"blog_topic\": \"The future of serverless AI\"}"
}
```

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| LLM Hosting | Amazon SageMaker + HuggingFace TGI |
| LLM Inference (serverless) | AWS Bedrock (Meta LLaMA 2) |
| Serverless Compute | AWS Lambda |
| Storage | Amazon S3 |
| SDK | `boto3`, `sagemaker` (Python) |

---

## Repository Structure

```
NVIDIA-NIM/
├── AWS sagemaker/
│   ├── falcon40B-instruct-notebook-full.ipynb   # SageMaker deployment notebook
│   ├── Untitled (2).ipynb
│   └── test.py
├── Blog generation in aws/
│   ├── app.py                                   # Lambda handler
│   └── requirements.txt
└── README.md
```

---

## License

This project is licensed under the [GNU General Public License v3.0](LICENSE).
