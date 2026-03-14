# Projeto-Cloud---DevOps
Este projeto consiste em uma aplicação web moderna hospedada na AWS, focada em alta disponibilidade e baixo custo. A infraestrutura é gerenciada como código (IaC) e o deploy é totalmente automatizado.

O objetivo é um site que registra e exibe o número de visitantes em tempo real. A arquitetura é Serverless, o que significa que não há servidores ligados 24h; os recursos só funcionam quando há demanda, otimizando custos e escalabilidade.

🏗️ Como foi feito
Infraestrutura (IaC): Utilização do Terraform para definir toda a infraestrutura da AWS via código, garantindo que o ambiente seja replicável.

Frontend: Um arquivo index.html hospedado no Amazon S3, utilizando JavaScript para interagir com a API de backend.

Backend (Serverless): Uma função AWS Lambda em Python que processa a lógica de contagem e interage com o banco de dados.

Banco de Dados: Amazon DynamoDB para armazenamento rápido e escalável do número de visitas.

Esteira DevOps (CI/CD): Configuração do GitHub Actions para automatizar a criação da infraestrutura e o deploy do código a cada atualização no repositório.

💻 Códigos Chave
1. Manual de Construção (main.tf)
Este arquivo utiliza o Terraform para criar os recursos necessários na conta AWS.

Terraform

provider "aws" {
  region = "us-east-1"
}

# Tabela para salvar a contagem
resource "aws_dynamodb_table" "contador" {
  name         = "tabela-contador"
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "id"
  attribute { name = "id", type = "S" }
}

# Bucket S3 para hospedar os arquivos do site
resource "aws_s3_bucket" "meu_site" {
  bucket = "meu-projeto-unico-2026" # Nome único do bucket
}
2. A Lógica de Backend (lambda_function.py)
Script em Python que executa a função de soma no banco de dados.

Python

import json
import boto3

def lambda_handler(event, context):
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('tabela-contador')

    # Incrementa o contador de visitas
    resposta = table.update_item(
        Key={'id': 'geral'},
        UpdateExpression='ADD visitas :inc',
        ExpressionAttributeValues={':inc': 1},
        ReturnValues="UPDATED_NEW"
    )

    return {
        'statusCode': 200,
        'headers': { 'Access-Control-Allow-Origin': '*' },
        'body': json.dumps(str(resposta['Attributes']['visitas']))
    }
3. Interface do Usuário (index.html)
Código HTML e JavaScript que o visitante acessa no navegador.

HTML

<h1> Você está vendo um site 100% na Nuvem!</h1>
<p>Total de pessoas que já passaram por aqui: <span id="valor">...</span></p>

<script>
  // URL gerada pelo API Gateway após o deploy
  fetch('SUA_URL_DA_API')
    .then(res => res.json())
    .then(data => {
      document.getElementById('valor').innerText = data;
    });
</script>
🛠️ Próximos Passos
Para colocar este projeto em execução:

Configurar as credenciais da AWS.

Instalar o Terraform localmente para o deploy inicial.

Adicionar as chaves de acesso (AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY) nos Secrets deste repositório no GitHub para ativar a automação.
