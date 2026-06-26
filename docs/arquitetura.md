# Arquitetura

## Estilo Arquitetural: Híbrido (Monolito Modular + Publish-Subscribe)
**Justificativa:** O monolito modular foi escolhido por ser um padrão de arquitetura que organiza o código em módulos independentes dentro de uma única base de código e unidade de implantação. Unindo a simplicidade dos sistemas monolíticos tradicionais com a organização e os limites bem definidos típicos dos microsserviços. Nesse modelo de arquitetura, as camadas internas garantem que as regras de negócio cruciais fiquem isoladas da camada de apresentação. 

O ecossistema de sala de aula deverá utilizar de gatilhos de comunicação que funcionarão em forma de publicações no sistema (avisos, atividades, avaliações, novas notas, entregas atrasadas, etc). Assim, a inclusão do padrão Publish-Subscribe permite que o sistema funcione por meio de publicações assíncronas.

## Decisões Arquiteturais

- **Estilo arquitetural:** Híbrido (Monolito Modular + Publish-Subscribe)
- **Diferenciação de Acesso por Tipo de Usuário (Professor/Aluno):** O sistema deve registrar o tipo de perfil do usuário (professor, estudante, etc) na tabela de cadastro do banco de dados e deverá validar essa informação para realizar determinadas funções no sistema. O sistema, então, respeitará as regras estritas sobre quem pode fazer o quê. Verificando a autorização antes de liberar as páginas ou permitir a criação de turmas e prazos. 
- **Uso de Banco de Dados Relacional Único:** Deverá ser utilizado um banco de dados relacional como o MySQL para registrar todas as informações do programa em um único local. Assim, os dados de uma sala de aula são totalmente interligados e um banco relacional garante de forma simples que essas ligações nunca se percam. Garante um banco seguro por meio do ACID.
- **Notificações por Disparo de Avisos (utilizando Publish-Subscribe):** Utilizar um sistema de avisos, publicação de atividades e notas onde uma ação principal (como o professor postar uma nota, “publish”) publica um alerta e o sistema envia as notificações para os alunos inscritos nas turmas corretas (“subscribe”). Se o sistema enviasse todos os alertas simultaneamente, a tela do professor ficaria carregando por muito tempo. Com o padrão Publish-Subscribe, o professor clica em enviar e o sistema cuida do envio dos avisos em segundo plano. 

## Desafios Arquiteturais

- **Desafio 1:** Risco de Lentidão no Servidor com Disparo de Avisos

**Dificuldade:** O ecossistema de sala de aula exige muitas notificações simultâneas. Se um professor postar uma nota, o sistema precisaria atualizar o banco de dados e disparar alertas para todos os alunos da turma ao mesmo tempo. 

**Solução Arquitetural:** Adoção do padrão Publish-Subscribe (Event-Driven) para a camada de notificações. O sistema principal do monolito processa a nota, salva no banco e apenas "publica" o evento. Uma rotina em segundo plano (assíncrona) captura esse evento e envia os avisos aos alunos aos poucos, liberando a tela do professor instantaneamente.
- **Desafio 2:** Evitar Fraudes e Alterações Indevidas de Notas
Dificuldade: Garantir a imutabilidade após a correção. Se essa verificação dependesse apenas da tela do navegador (interface), um usuário mal-intencionado poderia burlar o código visual e reenviar o arquivo.
Solução Arquitetural: Implementação de Isolamento em Camadas Internas. A lógica de validação foi blindada dentro da camada de serviço. Mesmo que a tela sofra alguma tentativa de modificação, quando o pedido chega no "cérebro" do servidor, o sistema checa o banco de dados, vê que o campo da nota já está preenchido e barra qualquer tentativa de reenvio da entrega.
Desafio 3: Garantir o Controle de Acesso Rigoroso (Professor vs. Aluno)
Dificuldade: O sistema possui regras de negócio muito estritas baseadas no papel de cada usuário. O desafio arquitetural se torna garantir que um aluno mal-intencionado (ou que entendesse de programação) não consiga burlar a interface do sistema se passando por professor para alterar a sua própria nota ou criar prazos falsos.
Solução Arquitetural: Implementação de um mecanismo de Controle de Acesso Baseado em Perfis (RBAC - Role-Based Access Control) integrado nativamente nas camadas internas do servidor. Quando o usuário faz login, o sistema identifica seu perfil ("Professor" ou "Aluno"). Assim, mesmo que um aluno consiga alterar o código visual da tela para mostrar o botão "Lançar Nota", ao clicar nele, a camada de serviço do servidor intercepta o pedido, valida que o perfil dele é de "Estudante" e bloqueia a ação imediatamente, retornando um erro de "Não Autorizado".
