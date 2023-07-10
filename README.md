<h2> Desafio </h2>
No cooperativismo, cada associado possui um voto e as decisões são tomadas em assembleias, por votação. A partir disso foi criada a seguinte solução back-end para gerenciar essas sessões de votação.
<hr>
<h3>A solução foi dividida da seguinte maneira:</h3>

![fluxo-da-solucao](/assets/system-flow.png)

A solução foi divida em dois projetos java: o <b>assembly-service</b> e o <b>vote-service</b>.<br><br>
Links dos projetos: <br>
- assembly-service: https://github.com/ThomasPeruch/scd-assembly-service/<br>
- vote-service: https://github.com/ThomasPeruch/scd-vote-service/<br>
<br>
O <b>assembly-service</b> tem como responsabilidade gerenciar as pautas e as sessão de votações, já o <b>vote-service</b> é responsável por registrar os votos e exibir o resultado da votação.<br>
O assembly-service tem um job que roda no segundo 30 de cada minuto, verificando no banco de dados se uma sessão já expirou (voting_end < now()), caso tenha expirado envia uma mensagem(via RabbitMq) para fila session-status-queue. O vote-service consome dados dessa fila. Após isso o vote-service não permitirá mais votos na sessão especifica.<br>
O vote-service além de retornar o resultado da sessão em seu endpoint específico publicara uma mensagem na fila session-result-queue.<br><br>
<strong>IMPORTANTE:</strong>Executar primeiro o docker-compose.yml do projeto assembly pois contenha as configurações do rabbit que ambos os projetos se conectarão.


<h3>Links de acesso</h3>

A interface do rabbit pode ser acessada no link:<br>
<i>http://127.0.0.0:15672/#/</i><br>
usuario : <i>guest</i><br>
senha : <i>guest</i><br>

credencias de acesso do banco de dados PostgreSQL do assembly-service:<br>
url: <i>jdbc:postgresql://localhost:5432/assembly</i><br>
usuario: <i>postgres</i><br>
senha: <i>assembly</i><br>

credencias de acesso do banco de dados PostgreSQL do vote-service:<br>
url: <i>jdbc:postgresql://localhost:5433/vote</i><br>
usuario: <i>postgres</i><br>
senha: <i>vote</i><br>
