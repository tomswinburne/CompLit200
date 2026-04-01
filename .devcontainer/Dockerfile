FROM mcr.microsoft.com/devcontainers/python:3.11-bookworm

# Install zstd (needed by Ollama installer) and curl
RUN apt-get update && apt-get install -y --no-install-recommends zstd curl \
    && rm -rf /var/lib/apt/lists/*

# Deps for both notebooks
RUN pip install --no-cache-dir torch --index-url https://download.pytorch.org/whl/cpu
RUN pip install --no-cache-dir requests jupyter ipykernel transformers scikit-learn matplotlib

# Pre-download HuggingFace models so students don't wait
RUN python -c "\
from transformers import AutoTokenizer, AutoModel; \
AutoTokenizer.from_pretrained('bert-base-multilingual-cased'); \
AutoModel.from_pretrained('bert-base-multilingual-cased'); \
AutoTokenizer.from_pretrained('sentence-transformers/LaBSE'); \
AutoModel.from_pretrained('sentence-transformers/LaBSE')"

# Install Ollama
RUN curl -fsSL https://ollama.com/install.sh | sh

# Pre-pull models so students don't wait
# Start Ollama temporarily just for pulling, then stop it
RUN ollama serve & SERVER_PID=$! \
    && sleep 3 \
    && ollama pull llama3 \
    && ollama pull mistral \
    && kill $SERVER_PID
