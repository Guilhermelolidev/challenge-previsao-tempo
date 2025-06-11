🔁 Como rodar localmente

1. Importar o fluxo no n8n
* Acesse seu n8n local em http://localhost:5678
* Clique em "Import" no canto superior direito
* Importe o arquivo:
./n8n/fluxo-n8n-export.json
* Atualize as credenciais da API Weatherstack e do seu Email SMTP

2. Rodar API strapi
* Inicializar projeto strapi
* Copiar a pasta ./strapi/api/ na raiz do projeto
* Executar yarn develop ou npm run develop

📘 Documentação – Desafio Strapi + n8n
Projeto: Acompanhando o tempo em São Paulo e Rio de Janeiro

🧠 Visão Geral
Monitorar a previsão do tempo duas vezes ao dia (às 08h e às 18h) nas cidades de São Paulo e Rio de Janeiro, armazenar os dados no Strapi e gerar notificações automatizadas com um resumo das condições climáticas e se é um bom final de semana para ir à praia no RJ.

🛠️ Tecnologias Utilizadas
* Strapi – CMS headless usado como backend para armazenar e servir dados das previsões.
* n8n – Orquestrador para automação dos fluxos de coleta, processamento e notificação.
* Weatherstack API – API usada para buscar as previsões climáticas em tempo real.
* Email (SMTP) – Canal de notificação escolhido para envio do resumo.

🧱 Modelagem de Dados (Strapi)

1. Cidade
* id (integer, PK)
* nome (string)
* uf (string)
  
2. Previsao
* cidade (relation many-to-one → Cidade)
* temperatura (float)
* condicao (string)
* data_coleta (datetime)
* fim_de_semana (boolean|null)
* bom_para_praia (boolean|null)

🔄 Automação com n8n

Disparador
* Trigger: Agendamento programado para 08:00 e 18:00 diariamente.

Etapa 1 – Verificação e Cadastro das Cidades
1. Busca Cidade SP no Strapi GET /cidades?nome=Sao paulo
2. Verifica se SP já está cadastrado
3. Se não existir, executa:
    * Cadastrar cidade SP
4. Transforma os dados com nó "Nova cidade"
(Repete o mesmo processo para RJ)

Etapa 2 – Consolida as duas cidades
1. Junta os dados das cidades no nó Id e nome
   
Etapa 3 – Consulta da Previsão
1. Aguarda com Intervalo 1s (evita overload)
2. Busca Previsão via Weatherstack (duas vezes, uma por cidade)
3. Formata Dados Cadastro
    * Calcula se é fim de semana
    * Define se é "bom para praia" (RJ apenas)
    * Converte timezone/data
4. Cadastra Previsão no Strapi

Etapa 4 – Notificação por Email
1. Formata Dados Email
    * Monta mensagem com condição e temperatura
    * Se for RJ no fim de semana, mostra se é bom para praia
2. Send Email
    * Envia resumo das duas cidades

![file-NtmjTV9gcidtq3cUvZxX83](https://github.com/user-attachments/assets/38e64657-4a52-4c18-8437-16cd5c47803b)

Exemplo de Notificação
Olá! ☀️ Aqui está a previsão do tempo de hoje: São Paulo: Ensolarado, 27°C Rio de Janeiro: Parcialmente nublado, 30°C
Fim de semana: Bom para praia no RJ? ✅ Sim

Ideias de funcionalidades futuras que fariam sentido: (não deu pra implementar devido a limitação do tempo)
- Envio de notificações personalizadas via WhatsApp (canal de comunicação mais utilizado)
- Painel visual em tempo real com gráficos de temperatura e probabilidade de chuva (via Chart.js no frontend).
