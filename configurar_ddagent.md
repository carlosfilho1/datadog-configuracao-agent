# Configurando Datadog Agent para Monitorar Docker

## 1. Pré-requisito.
- Datadog Agent instsalado no servidor (No exemplo em questão, instância EC2).
- Permissão para usuário <b>"dd-agent"</b> acessar o socket do docker.

## 2. Permisões para o socket do docker.
> [!IMPORTANT]
> <i>sudo usermode -a -G docker dd-agent </i><br>
> <i>sudo systemctl restart datadog-agent # Ou o comando apropriado para reiniciar o Agent</i><br>

## 3. Configuração da Integração Docker:
> [!IMPORTANT]
> A configuração da integração Docker é feita através de arquivos YAML no diretório de configuração do Datadog Agent. <br>
> Localização dos arquivos de configuração: Geralmente em <b>/etc/datadog-agent/conf.d/.</b> Dentro desta pasta, você pode ter um diretório <b>docker.d/</b> ou criar um arquivo <b>docker.d/conf.yaml</b>.<br>

[!NOTE]

    # /etc/datadog-agent/conf.d/docker.d/conf.yaml -> Caminho do arquivo

    init_config:

    instances:
        - url: "unix://var/run/docker.sock"

    # Codigos abaixo é opcional para alguns casos espercifico.

    # collect_container_size: false # Defina como true para coletar o tamanho dos contêineres (pode ser intensivo)
    # collect_exit_codes: true      # Coleta códigos de saída de contêineres parados
    # collect_images_stats: false   # Coleta estatísticas sobre imagens (pode ser intensivo)
    # collect_disk_stats: false     # Coleta estatísticas de disco por contêiner (pode ser intensivo)
    # collect_volume_stats: true    # Coleta estatísticas de contagem de volumes

    # (Opcional) Filtragem de contêineres:
    # Se você quiser incluir ou excluir contêineres específicos da monitoração.
    # exclude:
    #   - "image:datadog/agent.*" # Exclui o próprio Datadog Agent da monitoração detalhada
    # include:
    #   - "image:nginx.*"
    #   - "name:portfolio_web"

    # (Opcional) Custom tags para todos os contêineres monitorados
    # tags:
    #   - "environment:producao"
    #   - "application:portfolio"

    # (Opcional) Coleta de eventos do Docker
    # collect_events: true

    # (Opcional) Coleta de logs de contêineres
    # Para isso, você também precisa habilitar a coleta de logs no datadog.yaml principal
    # e configurar a origem (source) e serviço (service) para seus logs.
    # logs:
    #   - type: docker
    #     source: docker # Nome da origem para seus logs Docker no Datadog
    #     service: portfolio-app # Nome do serviço para seus logs Docker no Datadog
    #     # (Opcional) process_ Παντού (process all logs)
    #     # (Opcional) include_labels: ["com.docker.compose.service"] # Inclui labels do Docker como tags

> [!CAUTION]
> <b>A coleta de logs precisa estar habilitada globalmente no arquivo principal de configuração do Datadog Agent (/etc/datadog-agent/datadog.yaml)</b>

    # /etc/datadog-agent/datadog.yaml
    logs_enabled: true

## 4. Valide a Configuração e Reinicie o Agent
### Após criar ou modificar o arquivo docker.d/conf.yaml, valide a configuração do Agent

    sudo datadog-agent configcheck
    # Ou, se o Agent estiver rodando como um contêiner Docker:
    # docker exec -it <datadog_agent_container_name_or_id> agent configcheck
        Procure pela seção docker no output e veja se ela aparece como OK ou se há erros.

### Reinicie o Datadog Agent para aplicar as novas configurações:
    sudo systemctl restart datadog-agent
    # Ou o comando apropriado para o seu sistema.

> [!WARNING]
> Caso apresente erro de permissao no socket, no caminho <b><i>unix:///var/run/docker.sock</i></b><br>
   
    - sudo usermod -a -G docker dd-agent

    usermod: Comando para modificar uma conta de usuário.
    -a: "Append" – adiciona o usuário a grupos suplementares sem removê-lo de outros.
    -G docker: Especifica o grupo docker.
    dd-agent: O nome de usuário do Datadog Agent.

    Reinicie o Datadog Agent:

    - sudo systemctl restart datadog-agent

    Verifique o Status:
    pós reiniciar, verifique o status do Datadog Agent e da integração Docker:

    - sudo datadog-agent status