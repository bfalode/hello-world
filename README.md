# hello-world




  frontend:
    image: ai/chat-demo-frontend:latest
    build: ./frontend
    ports:
      - "3000:3000"
    environment:
      - PORT=3000
      - HOST=0.0.0.0
    command: npm run start
    depends_on:
      - backend

  ollama:
    image: ai/chat-demo-model:latest
    build: ./ollama
    ports:
      - "11434:11434"
    volumes:
      - ollama_data:/root/.ollama
    environment: 
      - MODEL=${MODEL:-mistral:latest} # Default to mistral:latest if MODEL is not set
    healthcheck:
      test: ["CMD-SHELL", "curl -s http://localhost:11434/api/tags | jq -e \".models[] | select(.name == \\\"${MODEL:-mistral:latest}\\\")\" > /dev/null"]
      interval: 10s
      timeout: 5s
      retries: 50
      start_period: 600s
    deploy:
      resources:
        limits:
          memory: 8G

volumes:
  ollama_data:
    name: ollama_data
