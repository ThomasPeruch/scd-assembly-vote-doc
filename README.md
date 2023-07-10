<h2> Desafio </h2>
No cooperativismo, cada associado possui um voto e as decisões são tomadas em assembleias, por votação. A partir disso foi criada a seguinte solução back-end para gerenciar essas sessões de votação.
<hr>
<h3>A solução foi dividida da seguinte maneira:</h3>

![fluxo-da-solucao](/assets/system-flow.png)

A solução foi divida em dois projetos java: o assembly-service e o vote-service.<br>

O assembly-service tem como responsabilidade gerenciar as pautas e as sessão de votações, já o vote-service é responsável por registrar os votos e exibir o resultado da votação.<bt>
O assembly-service tem um job que roda no segundo 30 de cada segundo, verificando no banco de dados se uma sessão já expirou (dataFinal < agora), caso tenha expirado envia uma mensagem(via RabbitMq) para fila session-status-queue. O vote-service consome dados dessa fila. Após isso o vote-service não permitirá mais votos na sessão especifica.
O vote-service além de retornar o resultado da sessão em seu endpoint específico publicara uma mensagem na fila session-result-queue.  
