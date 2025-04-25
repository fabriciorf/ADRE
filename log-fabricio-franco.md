 <!-- Documentacao: Configuracao de Servidor rsyslog Centralizado - Projeto RedesBR -->

 <!-- Enderecamento da Rede -->

Servidor de Log (192.168.1.10);
Cliente 1 (192.168.1.11);
Cliente 2(192.168.1.12);
Cliente 3(192.168.1.13).      

 <!-- Etapas de Configuracao -->

<!-- 1. Configuracao do Servidor rsyslog -->

<!-- Editar o arquivo de configuracao principal: -->
```bash
sudo nano /etc/rsyslog.conf
```

 <!-- Habilitar os modulos de recepcao por rede: -->
Adicione ou descomente as seguintes linhas para permitir entrada via UDP e TCP na porta 514:

```conf
module(load="imudp")
input(type="imudp" port="514")

module(load="imtcp")
input(type="imtcp" port="514")
```

<!--  Reiniciar o servico rsyslog para aplicar as mudancas: -->
```bash
sudo systemctl restart rsyslog
```

<!--  (Opcional) Liberar as portas no firewall: -->
```bash
sudo ufw allow 514/udp
sudo ufw allow 514/tcp
```


<!-- 2. Configuracao dos Clientes -->

 <!-- Criar um novo arquivo de configuracao para envio remoto: -->
```bash
sudo nano /etc/rsyslog.d/remote.conf
```

<!-- Inserir a seguinte linha para encaminhar todos os logs para o servidor: -->
```conf
*.* @@192.168.1.10:514
```
> Obs: `@@` indica envio via **TCP**. Use `@` para UDP, se preferir.

 <!-- Reiniciar o servico rsyslog: -->
```bash
sudo systemctl restart rsyslog
```


<!-- 3. Teste de Funcionamento -->

<!-- Em um cliente, execute: -->
```bash
logger "Teste de log enviado para o servidor rsyslog"
```

<!-- No servidor, verifique se o log foi recebido: -->
```bash
sudo tail -f /var/log/syslog
```

Uma linha com a mensagem enviada deve aparecer, indicando que a configuracao esta funcionando corretamente.


<!-- Resultado Esperado -->
Todos os logs dos clientes estao sendo encaminhados e recebidos no servidor central.
E possivel acompanhar os eventos em tempo real usando `tail -f` ou `journalctl`.

<!-- Consideracoes Finais -->
Essa configuracao permite que a empresa "RedesBR" tenha um repositorio unificado de logs para facilitar o monitoramento da rede, aumentar a seguranca e atender requisitos de auditoria. Essa arquitetura tambem torna mais simples a identificacao de falhas e atividades suspeitas nos clientes.

<!-- Status do Projeto -->
Configuracao realizada com sucesso e logs recebidos corretamente no servidor central.

**Alunos: [Jose Augusto e Fabricio Franco]**
