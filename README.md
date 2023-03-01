# -Workfacilit-vaga-php-dev2023.1


    Passo 1: Criacao do ambiente de testes
    Após a leitura de todas as situacoes apresentadas criei uma base de testes utilizando as ferramentas XAMPP e HeidiSQL.

    Situação 1: o Legacy Enterprise possui integrações com intermediadoras de pagamento. O cliente Edinaldo Carvalho da Company I de evento do dia 14/01/2023, um casamento, alega que pagou a cobrança que estava em aberto em sua grade financeira (extrato) e enviou o comprovante para conferência. Tendo em vista que a resposta e análise do banco e da intermediadora podem demorar e que o evento do cliente se aproxima, qual seria a sua proposta de resolução para esse caso?

    Excluindo as possibilidades de contato com banco e a intermediadora poderiamos entrar em contato com o cliente e solicitar que ele fizesse o pagamento previsto em contrato novamente tendo em vista o reembolso assim que o primeiro pagamento fosse constatado, caso nao houvesse possibilidade ainda poderiamos consultar nas plataformas online para checar se ha instabilidade nas redes do banco ou da intermediadora oque tornaria a informacao mais confiavel, mas acredito que o mais correto a se fazer seria dar prosseguimento ao atendimento.

    Situação 2: a formanda raphaella9@gmail.com não está mais recebendo os e-mails disparados pelo nosso Legacy Enterprise. Qual seria o motivo e por que isso aconteceu?

    Analisando a funcao encontrada no arquivo SendGridEmail.php linha 19 notei que é necessario que a consulta nao reproduza resultados para que a funcao retorne nulo passe nas demais validacoes (linhas 42,58,109). Portanto a formanda raphaella9@gmail.com nao está recebendo os e-mails porque existe um registro no banco:

    crmerp_legacy_base> history_emails_blacklist> id 272

    Situação 3: a formanda Carla Méres foi cadastrada erroneamente na Turma do Legacy da Company 1. Ela pertente ao projeto da Turma da Enterprise da empresa Company 2. De forma lógica, qual a forma de alocarmos a formanda no projeto correto? Consegue nos dizer como podemos ajustar o acesso dela ao Portal do Cliente de forma que não fique referenciando ao projeto antigo?

    Ao análisar os bancos tabelas e colunas criadas notei que a associacao era feita pelas seguintes tabelas e colunas

    crmerp_legacy_base> users_portaldocliente> database
    crmerp_legacy_company_1> clientes
    crmerp_legacy_company_2> clientes

    Portanto o erro poderia ser facilmente corrigido realizando um backup dos atuais dados da cliente e executando as respectivas consultas:

    crmerp_legacy_base:
    UPDATE `crmerp_legacy_base`.`users_portaldocliente` SET `database`='crmerp_legacy_company_2', `empresa_id`=699 WHERE  `id`=1 AND `username`='carla.meres@legacy.com.br';
    
    crmerp_legacy_company_1:
    DELETE FROM `crmerp_legacy_company_1`.`clientes` WHERE  `id`=3 AND `nid`='3';
    
    crmerp_legacy_company_2:
    INSERT INTO `clientes` VALUES (3, '3', 1, 'Carla Méres', NULL, NULL, NULL, '1988-09-19', NULL, 'PE', NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, 'carla.meres@legacy.com.br', NULL, NULL, NULL, NULL, NULL, NULL, NULL, 30, NULL, NULL, NULL, NULL, NULL, NULL, 68, 1, '2019-12-13 19:51:20', '1', NULL, NULL, NULL, NULL, '2019-12-13 19:47:34', '2022-01-18 14:19:06');

    Em uma situacao real consultas tambem poderiam ser realizadas nos logs para realoca-los caso existissem(dependendo de como a workfacilit trata os logs).

    Situação 4: com base na tabela de logs do Legacy Enterprise, qual seria a sua proposta de relatório para que nossas analistas performem uma primeira análise diretamente pelo sistema antes de chegar em nossos técnicos? Quais filtros seriam interessantes para disponibilizar nesse relatório? Consegue desenvolver esboçar uma query base pra gente? (pode ser um esboço)

    Filtros de Tabela principal, Usuario, IP, Acao, Data, os JSONs tambem poderiam ser tratados em tela para nao sobrecarregar o banco com grandes consultas e novos filtros seriam gerados.

    SELECT main_table, username, action, json_changed, ip, user_agent, datecreate
		FROM logs
		WHERE main_table = "projeto_planos" AND user_id = 1 AND ip = "172.18.0.1" AND action = "update" AND datecreate BETWEEN '2022-12-20 21:33:00' AND '2022-12-28 10:46:00';

    Situação 5: olhando nossa biblioteca de autenticação, o que poderia ser melhorado na autenticação dos usuários?

    Ao realizar a análise nao enconntrei melhorias consideraveis, ao primeiro olhar parece completo e seguro, alguns trechos de codigo poderiam ser simplificados ao atribuir valores de objetos a arrays e uma implementacao de chaves de criptografia por sessao poderia ser interessante. 

    Situação 6: um dos nossos usuários (colaborador), solicitou uma feature onde toda vez que um lead é cadastrado no CRM, seja disparado um SMS para o vendedor responsável. Como analisa a solicitação?

    Iria verificar na base de dados se existe correlacao entre a tabela de lead e vendedor responsavel e se os vendedores ja possuem numeros cadastrados.
    Caso sim:
    Será necessario implementar uma API de envio de SMS no sistema que receberá uma chamada sempre que um novo lead for cadastrado (Normalmente será necessario o contrato de uma empresa terceira que ficará responsavel pelo pacote de SMS ex: 1000SMS custarao R$100,00) talvez uma solucao alternativa para reducao de custos seria uma API ou um Script de envio de mensagens por Whatsapp caso os vendedores ja possuam plano de dados movel.

    Caso nao: 
    Será necessario adicionar o campo de numero ao cadastro de cliente, e o campo de vendedor ao cadastro de lead antes de realizar os passos anteriores.

    Bônus: olhando para a tabela clientes, consegue nos dizer como diferenciar um projeto "1-1" de um projeto "1-n"?

    Na tabela users_portaldocliente os projetos "1-1" nao tem um projeto_id associados já as "1-n" sim.
