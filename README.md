### Tutorial para Usar Docker Compose com Ollama e Open-WebUI

Este tutorial irá guiá-lo na configuração e uso de um ambiente Docker Compose com Ollama e Open-WebUI. Este ambiente permitirá que você utilize grandes modelos de linguagem através do Ollama e interaja com eles através da interface do Open-WebUI.

#### Pré-requisitos

1. **Docker:** Certifique-se de ter o Docker instalado em sua máquina. Siga as instruções de instalação no [site oficial do Docker](https://docs.docker.com/get-docker/).
2. **Docker Compose:** Certifique-se de ter o Docker Compose instalado. As instruções podem ser encontradas no [site oficial do Docker Compose](https://docs.docker.com/compose/install/).

#### Estrutura do Arquivo docker-compose.yml

Aqui está o arquivo `docker-compose.yml` que será utilizado:

```yaml
version: '3.7'
services:
  milvus:
    image: milvusdb/milvus:latest
    container_name: milvus
    ports:
      - "19530:19530"
    volumes:
      - milvus-data:/var/lib/milvus
    environment:
      - TZ=America/Sao_Paulo

  ollama:
    image: ollama/ollama:latest
    container_name: ollama
    ports:
      - "11434:11434"
    volumes:
      - ./ollama/:/root/.ollama
      - ./modelos/:/root/.modelos
    deploy:
      resources:
        limits:
          memory: 4g
        reservations:
          devices:
            - capabilities: [ gpu ]
    environment:
      - TZ=America/Sao_Paulo
    runtime: nvidia

  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    container_name: open-webui
    ports:
      - "8080:8080"
    environment:
      - OLLAMA_BASE_URL=http://ollama:11434
    volumes:
      - webui-volume:/app/backend/data
    deploy:
      resources:
        reservations:
          cpus: "0.5"
          memory: 500m
        limits:
          cpus: "1.0"
          memory: 1g
    tty: true
    depends_on:
      - ollama
      - milvus

volumes:
  milvus-data:
  ollama:
  webui-volume:
```

#### Passo a Passo

1. **Criação do Diretório do Projeto:**

   Crie um diretório para o projeto e navegue até ele:

   ```sh
   mkdir meu_projeto_ollama
   cd meu_projeto_ollama
   ```

2. **Criação do Arquivo docker-compose.yml:**

   No diretório do projeto, crie um arquivo chamado `docker-compose.yml` e copie o conteúdo fornecido acima.

   ```sh
   nano docker-compose.yml
   ```

3. **Configuração das Pastas de Volume:**

   Certifique-se de criar os diretórios locais que serão montados como volumes no contêiner Docker:

   ```sh
   mkdir -p ollama
   mkdir -p modelos
   ```

   Esses diretórios serão usados para armazenar dados persistentes e modelos de linguagem.

4. **Inicialização dos Contêineres:**

   Execute o comando Docker Compose para iniciar os contêineres:

   ```sh
   docker-compose up -d
   ```

   Este comando irá baixar as imagens necessárias e iniciar os serviços definidos no arquivo `docker-compose.yml`.

5. **Verificação dos Contêineres em Execução:**

   Verifique se os contêineres estão em execução:

   ```sh
   docker-compose ps
   ```

   Você deve ver os contêineres `ollama` e `open-webui` em execução.

6. **Acessar o Open-WebUI:**

   Abra o seu navegador e acesse a interface web do Open-WebUI:

   ```
   http://localhost:8080
   ```

   A partir daqui, você pode interagir com os modelos de linguagem fornecidos pelo Ollama.

7. **Interagindo com o Modelo Ollama:**

   Para interagir com um modelo de linguagem através do Open-WebUI, siga os prompts na interface web. O Open-WebUI está configurado para se comunicar com o Ollama no backend, utilizando o URL base configurado em `OLLAMA_BASE_URL`.

### Resolução de Problemas

- **Problemas de Permissão:** Certifique-se de que os diretórios locais (`ollama` e `modelos`) têm as permissões corretas para que o Docker possa ler e gravar neles.
- **Acesso à GPU:** Se você estiver usando a versão com suporte a GPU, certifique-se de ter o Nvidia Docker Toolkit instalado e configurado corretamente.

### Conclusão

Seguindo este tutorial, você configurou um ambiente Docker Compose com Ollama e Open-WebUI. Agora, você pode explorar e utilizar grandes modelos de linguagem através de uma interface web interativa. Esta configuração oferece uma solução robusta para desenvolvimento e testes locais de aplicações baseadas em modelos de linguagem.
