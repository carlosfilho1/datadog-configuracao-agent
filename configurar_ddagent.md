# Configurando Datadog Agent para Monitorar Docker

## 1. Pré-requisito.
- Datadog Agent instsalado no servidor (No exemplo em questão, instância EC2).
- Permissão para usuário <b>"dd-agent"</b> acessar o socket do docker.

## 2. Permisões para o socket do docker.
> [!IMPORTANT]
> <i>sudo usermode -a -G docker dd-agent </i><br>
> <i>sudo systemctl restart datadog-agent # Ou o comando apropriado para reiniciar o Agent</i><br>

