<h2> Desafio </h2>
No cooperativismo, cada associado possui um voto e as decisões são tomadas em assembleias, por votação. A partir disso foi criada a seguinte solução back-end para gerenciar essas sessões de votação.
<hr>
<h3>A solução foi dividida da seguinte maneira:</h3>

![fluxo-da-solucao](/assets/flow-system.png)

A solução foi divida em dois projetos java: o <b>assembly-service</b> e o <b>vote-service</b>.<br><br>
Links dos projetos: <br>
- assembly-service: https://github.com/ThomasPeruch/scd-assembly-service/<br>
- vote-service: https://github.com/ThomasPeruch/scd-vote-service/<br>
<br>
O <b>assembly-service</b> tem como responsabilidade gerenciar as pautas e as sessão de votações, já o <b>vote-service</b> é responsável por registrar os votos e exibir o resultado da votação.<br>
O assembly-service tem um job que roda no segundo 10 de cada minuto, verificando no banco de dados se uma sessão já expirou (voting_end < now()), caso tenha expirado envia uma mensagem(via RabbitMq) para fila session-status-queue. O vote-service consome dados dessa fila. Após isso o vote-service não permitirá mais votos na sessão especifica.<br>
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

<h3>Postman collection de ambos projetos</h3> 

Para importar a collection a baixo no seu postman, siga os seguintes passos:
- Abra o postman
- File > Import
- Clique em Raw Text
- Cole o código abaixo
- Clique em Import
````
{
	"info": {
		"_postman_id": "da6fdbfd-9924-42cd-b013-ce795c5fc9cf",
		"name": "nt / sicredi",
		"schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
	},
	"item": [
		{
			"name": "ASSEMBLY-SERVICE",
			"item": [
				{
					"name": "SESSION",
					"item": [
						{
							"name": "open topic voting session",
							"request": {
								"method": "POST",
								"header": [],
								"body": {
									"mode": "raw",
									"raw": "{\n    \"topicId\":4,\n    \"sessionTimeInMinutes\":20\n}",
									"options": {
										"raw": {
											"language": "json"
										}
									}
								},
								"url": {
									"raw": "http://localhost:8080/session",
									"protocol": "http",
									"host": [
										"localhost"
									],
									"port": "8080",
									"path": [
										"session"
									]
								}
							},
							"response": []
						},
						{
							"name": "find all sessions",
							"protocolProfileBehavior": {
								"disableBodyPruning": true
							},
							"request": {
								"method": "GET",
								"header": [],
								"body": {
									"mode": "raw",
									"raw": "{\n    \"topicId\":2,\n    \"sessionTimeInMinutes\":null\n}",
									"options": {
										"raw": {
											"language": "json"
										}
									}
								},
								"url": {
									"raw": "http://localhost:8080/session",
									"protocol": "http",
									"host": [
										"localhost"
									],
									"port": "8080",
									"path": [
										"session"
									]
								}
							},
							"response": []
						}
					]
				},
				{
					"name": "TOPIC",
					"item": [
						{
							"name": "create topic",
							"request": {
								"method": "POST",
								"header": [],
								"body": {
									"mode": "raw",
									"raw": "{\n    \"name\":\"pauta 4\"\n}",
									"options": {
										"raw": {
											"language": "json"
										}
									}
								},
								"url": {
									"raw": "http://localhost:8080/topic",
									"protocol": "http",
									"host": [
										"localhost"
									],
									"port": "8080",
									"path": [
										"topic"
									]
								}
							},
							"response": []
						},
						{
							"name": "find all topics",
							"protocolProfileBehavior": {
								"disableBodyPruning": true
							},
							"request": {
								"method": "GET",
								"header": [],
								"body": {
									"mode": "raw",
									"raw": "{\n    \"topicId\":3,\n    \"sessionTimeInMinutes\":30\n}",
									"options": {
										"raw": {
											"language": "json"
										}
									}
								},
								"url": {
									"raw": "http://localhost:8080/topic",
									"protocol": "http",
									"host": [
										"localhost"
									],
									"port": "8080",
									"path": [
										"topic"
									]
								}
							},
							"response": []
						}
					]
				}
			]
		},
		{
			"name": "VOTE-SERVICE",
			"item": [
				{
					"name": "vote",
					"item": [
						{
							"name": "vote",
							"request": {
								"method": "POST",
								"header": [],
								"body": {
									"mode": "raw",
									"raw": "{\n    \"idSession\":1,\n    \"vote\":true,\n    \"idAssociate\":1\n\n}",
									"options": {
										"raw": {
											"language": "json"
										}
									}
								},
								"url": {
									"raw": "http://localhost:8081/vote",
									"protocol": "http",
									"host": [
										"localhost"
									],
									"port": "8081",
									"path": [
										"vote"
									]
								}
							},
							"response": []
						},
						{
							"name": "count votes and get session result",
							"request": {
								"method": "GET",
								"header": [],
								"url": {
									"raw": "http://localhost:8081/vote/result/1",
									"protocol": "http",
									"host": [
										"localhost"
									],
									"port": "8081",
									"path": [
										"vote",
										"result",
										"1"
									]
								}
							},
							"response": []
						}
					]
				},
				{
					"name": "session status",
					"item": [
						{
							"name": "get all sessions status",
							"request": {
								"method": "GET",
								"header": [],
								"url": {
									"raw": "http://localhost:8081/session-status",
									"protocol": "http",
									"host": [
										"localhost"
									],
									"port": "8081",
									"path": [
										"session-status"
									]
								}
							},
							"response": []
						}
					]
				}
			]
		}
	]
}
````
