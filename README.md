# API Consulta CPF <span id="trialSpan"></span>

API para acesso ao Cadastro de Pessoas Físicas diretamente das bases da Receita Federal do Brasil.

A plataforma APIGOV (Plataforma que contempla todas as API's disponibilizadas e comercializadas pelo SERPRO) utiliza o protocolo Oauth2 - Client Credential Grant ([https://tools.ietf.org/html/rfc6749#section-4.4](https://tools.ietf.org/html/rfc6749#section-4.4)) para realizar a autenticação e autorização de acesso para consumo das API's contratadas, conforme figura abaixo:

<img title="Processo de autenticação e autorização APIS" src="https://raw.githubusercontent.com/devserpro/consulta-cpf/master/img/oauth.png" style="width=50%;" />

## Como fazer consultas a API CPF

Para consumir a API Consulta CPF, você deverá utilizar os dois códigos (Consumer Key e Consumer Secret) disponibilizados na Área do Cliente. Esses códigos servem para identificar o contrato e deverão ser informados sempre que uma consulta for realizada.
Exemplos de códigos:

**Consumer Key**: djaR21PGoYp1iyK2n2ACOH9REdUb

**Consumer Secret**: ObRsAJWOL4fv2Tp27D1vd8fB3Ote

### 1 – Como solicitar o Token de Acesso (Bearer)
Para consultar a API, é necessário obter um token de acesso temporário (Bearer). Esse token possui um tempo de validade e sempre que expirado, este passo de requisição de um novo token de acesso deve ser repetido. 

Para solicitar o token temporário é necessário realizar uma requisição HTTP POST para o endpoint Token https://apigateway.serpro.gov.br/token, informando as credenciais de acesso(consumerKey:consumerSecret) no HTTP Header Authorization, no formato base64, conforme exemplo abaixo. As credenciais de acesso devem ser obtidas a partir do portal do cliente Serpro - https://minhaconta.serpro.gov.br

```
[POST] grant_type=client_credentials
[HEAD] Authorization: Basic base64(Consumer Key:Consumer Secret)
```

Abaixo segue um exemplo de chamada via cUrl:

```curl
curl -k -d "grant_type=client_credentials" -H "Authorization: Basic ZGphUjIxUEdvWXAxaXlLMm4yQUNPSDlSRWRVYjpPYlJzQUpXT0w0ZnYyVHAyN0QxdmQ4ZkIzT3RlCg==" https://apigateway.serpro.gov.br/token
```

A chave informada no exemplo acima "ZGphUjIxUEdvWXAxaXlLMm4yQUNPSD
lSRWRVYjpPYlJzQUpXT0w0ZnYyVHAyN0QxdmQ4ZkIzT3RlCg==" é resultado do BASE64 dos códigos Consumer Key e Consumer Secret separados pelo caracter “:”, conforme exemplo a seguir como linha de comando do Linux:

```curl
echo -n "djaR21PGoYp1iyK2n2ACOH9REdUb:ObRsAJWOL4fv2Tp27D1vd8fB3Ote" | base64
```

**Receba o Token**

Como resultado, o endpoint informará o token de acesso a API, no campo access_token da mensagem json de retorno. Este token deve ser informado nos próximos passos.

```json
{"scope":"am_application_scope default","token_type":"Bearer","expires_in":3295,"access_token":"c66a7def1c96f7008a0c397dc588b6d7"}
```

**Renovação do Token de Acesso**

Atentar que sempre que o token de acesso temporário expirar, o gateway vai retornar um _HTTP CODE 401_ após realizar uma requisição para uma API. Neste caso, deve ser repetido o passo anterior (**Como solicitar o Token de Acesso (Bearer)**) para geração de um novo token de acesso temporário.


### 2 – Como realizar a consulta à API

De posse do Token de Acesso (**Bearer**), faça uma requisição via GET ao gateway informando os parâmetros da API. Exemplo:

```curlBearer
curl -X GET --header "Accept: application/json" --header "Authorization: Bearer c66a7de41c96f7008a0c397dc588b6d7" "https://apigateway.serpro.gov.br/consulta-cpf/v1/cpf/99999999999"
```

No exemplo acima foram utilizados os seguintes parametros:

**[HEADER] Accept: application/json** - Informamos o tipo de dados que estamos requerendo, nesse caso JSON

**[HEADER] Authorization: Bearer <span class="bearer">c66a7de41c96f7008a0c397dc588b6d7</span>** - Informamos o token de acesso recebido

**[GET] https://apigateway.serpro.gov.br/consulta-cpf<span id="trialSpanUrl"></span><span id="trialSpanVersao"></span>/cpf/v1/99999999999**: chamamos a url da API informando o CPF. No caso a url é "consulta-cpf<span id="trialSpanUrl2"></span>/<span id="trialSpanVersao2"></span>/cpf/{numero do CPF}"

Nesse caso, espera-se que a resposta seja a seguinte:

```json
{
  "ni": "91708635203", 
  "nome": "Nome do CPF 917.086.352-03", 
  "nascimento": "01011975", 
  "situacao": {
    "codigo" : "0", 
    "descricao" : "Regular"
  }
}
```

```trialSpanCPFs
CPF's para ambiente TRIAL:

1) 40442820135
2) 63017285995
3) 91708635203
4) 47856021985
5) 23479805647
6) 40532176871
7) 47123586964
8) 49852036700
9) 28769150494
10) 07691852312
11) 10975384600
12) 01648527949
13) 47893062592
14) 98302514705
15) 18025346790
16) 64913872591
17) 52389071686
18) 05137518743
19) 08849979878
20) 58136053391

Atenção: Os CPFs acima foram gerados aleatoriamente contendo apenas dados fictícios.
```
