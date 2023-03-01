# -Workfacilit-vaga-php-dev2023.1

	Passo 1: Criação do ambiente de testes Após a leitura de todas as situações apresentadas criei uma base de testes utilizando as ferramentas XAMPP e HeidiSQL.

	Situação 1: Excluindo as possibilidades de contato com banco e a intermediadora poderíamos entrar em contato com o cliente e solicitar que ele fizesse o pagamento previsto em contrato novamente, tendo em vista o reembolso assim que o primeiro pagamento fosse constatado. Caso não houvesse possibilidade ainda poderíamos consultar nas plataformas online para checar se ha instabilidade nas redes do banco ou da intermediadora oque tornaria a informação mais confiável, mas acredito que o mais correto a se fazer seria dar prosseguimento ao atendimento.

	Situação 2: Analisando a função encontrada no arquivo SendGridEmail.php linha 19 notei que é necessário que a consulta não reproduza resultados para que a função retorne nulo e passe nas demais validações (linhas 42,58,109). Portanto, a formanda raphaella9@gmail.com não está recebendo os e-mails porque existe um registro no banco: crmerp_legacy_base> history_emails_blacklist> id 272

    	Situação 3: Ao analisar os bancos notei que a associação era feita pelas seguintes tabelas e colunas crmerp_legacy_base> users_portaldocliente> database crmerp_legacy_company_1> clientes crmerp_legacy_company_2> clientes

Portanto o erro poderia ser facilmente corrigido realizando um backup dos atuais dados da cliente e executando as respectivas consultas:

crmerp_legacy_base:
	UPDATE `crmerp_legacy_base`.`users_portaldocliente` SET `database`='crmerp_legacy_company_2', `empresa_id`=699 WHERE  `id`=1 AND `username`='carla.meres@legacy.com.br';
    
crmerp_legacy_company_1:
	DELETE FROM `crmerp_legacy_company_1`.`clientes` WHERE  `id`=3 AND `nid`='3';
    
crmerp_legacy_company_2:
    	INSERT INTO `clientes` VALUES (3, '3', 1, 'Carla Méres', NULL, NULL, NULL, '1988-09-19', NULL, 'PE', NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, 'carla.meres@legacy.com.br', NULL, NULL, NULL, NULL, NULL, NULL, NULL, 30, NULL, NULL, NULL, NULL, NULL, NULL, 68, 1, '2019-12-13 19:51:20', '1', NULL, NULL, NULL, NULL, '2019-12-13 19:47:34', '2022-01-18 14:19:06');

Em uma situação real consultas também poderiam ser realizadas nos LOGs para realoca-los caso existissem(dependendo de como a Workfacilit trata os LOGs).

	Situação 4: Filtros de Tabela principal, Usuário, IP, Ação, Data, os JSONs tambem poderiam ser tratados em tela para não sobrecarregar o banco com grandes consultas e novos filtros seriam gerados. (Esboço: imagem 1)

	SELECT main_table, username, action, json_changed, ip, user_agent, datecreate
FROM logs
WHERE main_table = "projeto_planos" AND user_id = 1 AND ip = "172.18.0.1" AND action = "update" AND datecreate BETWEEN '2022-12-20 21:33:00' AND '2022-12-28 10:46:00';

	Situação 5: Ao realizar a análise não encontrei melhorias consideráveis, ao primeiro olhar parece completo e seguro, alguns trechos de código poderiam ser simplificados ao atribuir valores de objetos a Arrays e uma implementação de chaves de criptografia por sessão poderia ser interessante.. 

	Situação 6: Iria verificar na base de dados se existe correlação entre a tabela de lead e vendedor responsável e se os vendedores já possuem números cadastrados.
    	Caso sim: Será necessário implementar uma API de envio de SMS no sistema que receberá uma chamada sempre que um novo lead for cadastrado (Normalmente será necessário o contrato de uma empresa terceira que ficará responsável pelo pacote de SMS ex: 1000SMS´s custarão R$100,00) uma solução alternativa para redução de custos seria uma API ou um Script de envio de mensagens por WhatsApp caso os vendedores já possuam plano de dados móvel.
	Caso nao: Será necessário adicionar o campo de número ao cadastro de cliente, e o campo de vendedor ao cadastro de lead antes de realizar os passos anteriores.

    	Bônus: Na tabela users_portaldocliente os projetos "1-1" não tem um projeto_id associados já as "1-n" sim.
