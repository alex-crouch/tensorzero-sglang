# TensorZero Local Setup

This repository contains a Docker Compose setup for running TensorZero locally with SGLang and the Qwen3-32B-AWQ model.

## Overview

TensorZero is a platform for building reliable LLM applications. This setup includes:

- **SGLang**: Serving the Qwen3-32B-AWQ model locally
- **ClickHouse**: Database for storing inference data and analytics
- **TensorZero Gateway**: HTTP API for interacting with models
- **TensorZero UI**: Web interface for monitoring and analytics

## Prerequisites

- Docker and Docker Compose
- NVIDIA GPU with sufficient VRAM (recommended: 24GB+ for Qwen3-32B-AWQ)
- NVIDIA Container Toolkit installed

## Environment Setup

1. Copy the environment template:
```bash
cp .env.example .env
```

2. Edit `.env` and set your API keys:
```bash
# Required: OpenAI API key for TensorZero services
OPENAI_API_KEY=your_openai_api_key_here

# Required: HuggingFace token for model access
HF_TOKEN=your_huggingface_token_here
```

## Quick Start

1. Clone this repository:
```bash
git clone <your-repo-url>
cd tensorzero
```

2. Set up environment variables (see Environment Setup above)

3. Start the services:
```bash
docker-compose up -d
```

4. Wait for all services to be healthy:
```bash
docker-compose ps
```

5. Access the TensorZero UI at http://localhost:4000

## Services

### SGLang (Model Server)
- **Port**: 30000
- **Model**: Qwen3-32B-AWQ
- **Health Check**: http://localhost:30000/health

### ClickHouse (Database)
- **Port**: 8123
- **Username**: chuser
- **Password**: chpassword
- **Database**: tensorzero

### TensorZero Gateway
- **Port**: 3020
- **API Endpoint**: http://localhost:3020

### TensorZero UI
- **Port**: 4000
- **URL**: http://localhost:4000

## Configuration

The TensorZero configuration is defined in `config/tensorzero.toml`:

- **Model**: `qwen3_local` - Points to the SGLang server
- **Function**: `qwen` - Chat completion function using the local model

## Usage

### Using the Python Client

```python
from tensorzero import TensorZeroGateway

client = TensorZeroGateway("http://localhost:3020")

response = client.inference(
    function_name="qwen",
    input={
        "messages": [
            {"role": "user", "content": "Hello, how are you?"}
        ]
    }
)

print(response.content)
```

### Using OpenAI Client

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:3020/v1",
    api_key="dummy"  # Not required for local setup
)

response = client.chat.completions.create(
    model="qwen",
    messages=[
        {"role": "user", "content": "Hello, how are you?"}
    ]
)

print(response.choices[0].message.content)
```

### Using cURL

```bash
curl -X POST http://localhost:3020/inference \
  -H "Content-Type: application/json" \
  -d '{
    "function_name": "qwen",
    "input": {
      "messages": [
        {"role": "user", "content": "Hello, how are you?"}
      ]
    }
  }'
```

## Monitoring

- **TensorZero UI**: http://localhost:4000 - View inference analytics, model performance, and system metrics
- **SGLang Health**: http://localhost:30000/health - Check model server status
- **ClickHouse**: http://localhost:8123 - Direct database access

## Troubleshooting

### Common Issues

1. **GPU Memory Issues**
   - Ensure you have sufficient VRAM (24GB+ recommended)
   - Consider using a smaller model if memory is limited

2. **Service Startup Failures**
   - Check Docker logs: `docker-compose logs [service-name]`
   - Ensure all environment variables are set correctly

3. **Model Download Issues**
   - Verify your HuggingFace token has access to the model
   - Check internet connectivity for model downloads

### Useful Commands

```bash
# View logs for all services
docker-compose logs -f

# View logs for specific service
docker-compose logs -f sglang

# Restart services
docker-compose restart

# Stop all services
docker-compose down

# Remove all containers and volumes
docker-compose down -v
```

## Development

### Adding New Models

1. Add model configuration to `config/tensorzero.toml`
2. Update the SGLang service in `docker-compose.yml`
3. Restart the services

### Custom Configuration

Modify `config/tensorzero.toml` to add:
- New models and providers
- Custom functions and variants
- Routing configurations

## Security Notes

- This setup is for development/learning purposes
- For production use, refer to [TensorZero deployment docs](https://www.tensorzero.com/docs/gateway/deployment)
- Never commit API keys to version control
- Use proper authentication in production environments

## Resources

- [TensorZero Documentation](https://www.tensorzero.com/docs)
- [SGLang GitHub](https://github.com/sgl-project/sglang)
- [Qwen3 Model](https://huggingface.co/Qwen/Qwen3-32B-AWQ)

## License

This project is provided as-is for educational and development purposes.