<h4>Como versionaria a API da sua aplicação? Que estratégia usar?</h4>
Optaria pelo versionamento de API por rota. Adicionando a versão na rota, ficando desse jeito <i>http://localhost:8080/v2/session</i>, devido sua simplicidade.<br>
Já no código fonte criaria as classes necessárias identificando-as usando como sufixo a versão(também pode ser usado como prefixo, depende da preferência de quem for fazer) em um módulo específico também identificado com a versão.
<br>Exemplo de nome de uma classe no versionada:
<i>TopicControllerV2</i>
