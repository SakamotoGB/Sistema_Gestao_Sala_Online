# Arquitetura

## Estilo arquitetural: Híbrido (Monolito Modular + Publish-Subscribe)
Justificativa: O monolito modular foi escolhido por ser um padrão de arquitetura que organiza o código em módulos independentes dentro de uma única base de código e unidade de implantação. Unindo a simplicidade dos sistemas monolíticos tradicionais com a organização e os limites bem definidos típicos dos microsserviços. Nesse modelo de arquitetura, as camadas internas garantem que as regras de negócio cruciais fiquem isoladas da camada de apresentação. 
O ecossistema de sala de aula deverá utilizar de gatilhos de comunicação que funcionarão em forma de publicações no sistema (avisos, atividades, avaliações, novas notas, entregas atrasadas, etc). Assim, a inclusão do padrão Publish-Subscribe permite que o sistema funcione por meio de publicações assíncronas.

## Decisões arquiteturais

- Estilo arquitetural: Híbrido (Monolito Modular + Publish-Subscribe)
- Diferenciação de Acesso por Tipo de Usuário (Professor/Aluno): O sistema deve registrar o tipo de perfil do usuário (professor, estudante, etc) na tabela de cadastro do banco de dados e deverá validar essa informação para realizar determinadas funções no sistema. O sistema, então, respeitará as regras estritas sobre quem pode fazer o quê. Verificando a autorização antes de liberar as páginas ou permitir a criação de turmas e prazos. 
- Uso de Banco de Dados Relacional Único: Deverá ser utilizado um banco de dados relacional como o MySQL para registrar todas as informações do programa em um único local. Assim, os dados de uma sala de aula são totalmente interligados e um banco relacional garante de forma simples que essas ligações nunca se percam. Garante um banco seguro por meio do ACID.
- Notificações por Disparo de Avisos (utilizando Publish-Subscribe): Utilizar um sistema de avisos, publicação de atividades e notas onde uma ação principal (como o professor postar uma nota, “publish”) publica um alerta e o sistema envia as notificações para os alunos inscritos nas turmas corretas (“subscribe”). Se o sistema enviasse todos os alertas simultaneamente, a tela do professor ficaria carregando por muito tempo. Com o padrão Publish-Subscribe, o professor clica em enviar e o sistema cuida do envio dos avisos em segundo plano. 
