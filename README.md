# App

GymPass style app.

## RFs (requsitos funcionais)

- [ ] Deve ser possível se cadastrar (sistema de cadastro, login, criptografia);;
- [ ] Deve ser possível se autenticar;
- [ ] Deve ser possível obter o perfil de um usuário logado;
- [ ] Deve ser possível obter o número de chek-ins realizados pelo usuário logado;
- [ ] Deve ser possível o usuário obter seu histórico de check-ins;
- [ ] Deve ser possível o usuário buscar academias próximas;
- [ ] Deve ser possível o usuário buscar academias pelo nome;
- [ ] Deve ser possível o usuário realizar check-in em uma academia;
- [ ] Deve ser possível validar o check-in de um usuário;
- [ ] Deve ser possível cadastar uma academia;


## RNs (regras de negócio)

- [ ] O Usuário não deve poder se cadastrar com um e-mail duplicado;
- [ ] O usuário não pode fazer 2 check-ins no mesmo dia;
- [ ] O usuário não pode fazer check-in se não estiver perto (100m) da academia;
- [ ] O check-in só pode ser validado até 20 minutos após criado;
- [ ] O check-in só pode ser validado por administradores;
- [ ] A academia só pode ser cadastrada por administradores;

## RNFs (Requisitos não-funcionais)

- [ ] A senha do usuário precisa estar criptografada;
- [ ] Os dados da aplicação precisam estar persistidos em um banco PostegreSQL;
- [ ] Todas as listas de dados precisam estar paginas com 20 itens por página;
- [ ] O usuário deve ser identificado por um JWT (JSON Web Token);"


Comandos aula 1: 

1. Criou o src, criou o server.ts
2. npm init -y
3. npm i typescript @types/node tsx tsup -D
4. npx tsc --init
5. Com o comando 4. temos criado o arquivo tsconfig.json.
6. neste arquivo trocar o es2016 por es2020
7.  Antes de Trabalhar em server instalar: npm i fastify
8. Dividir server em server.ts e app.ts
9. em app.ts foi adicionado:

import fastify from "fastify"

export const app = fastify()

10. em server.ts foi adiconado:

import {app} from './app'

app.listen({
    host: '0.0.0.0',
    port: 3333,
}).then(() => {
    console.log('HTTP Server Running!')
})

11. Criar um arquivo gitignore 

12. em Packege.json vamos adicionar alguns scripts:

"scripts": {
    "start:dev": tsx watch src/server.ts
}

13. rodar npm run start:dev vai mostar HTTP Server Runnig

14. Criar o script:

"scripts": {
    "start:dev": tsx watch src/server.ts,
    "build": "tsup src --out-dir build"
}

15. Executar 'npm run build'. Gera uma pasta build com código JavaScript

16. 
    Criar o script:
    "scripts": {
    "start:dev": "tsx watch src/server.ts",
    "build": "tsup src --out-dir build",
    "start": "node build/server.js" 
}


17. Rodar 'npm run start'. Vai executar o HTTP server running com o node e não com o tsx.


aula 2: Configurações de dependências antes de iniciar o projeto:

1. criar o arquivo .npmrc na raiz do projeto
2. Configurar este arquivo adicionando o comando: save-exact=true
4. Isso é configuração para questões de dependencias.
5. rodar o comando: npm i typescript @types/node tsx tsup -D
6. rodar o coamando: npm i fastify
7. Isso vai reinstalar os pacotes, já com as configurações de dependências aplicadas.


aula 3: Configurar variáveis de ambiente:

1. criar um aruivo .env: NODE_ENV=dev
2. Criar o .env.exemple: a pessoa que baixar o projeto saiba quais variaveis ambiente precisa ter.
2. Adicionar o 
3. Carregar as variáveis para dentro do projeto. para isso precisa  instalar npm dotenv.
4. Criar em src uma pasta env. Criar o arquivo index.ts
5. adicionar o import 'dotenv/config'
6. instalar npm i zod. Isso faz a validação das variáveis.
7. adicionar import {z} from 'zod' em index.ts.
8. Criar uma variavel envSchema que contem um z.object;

const envSchema = z.object({
    NODE_ENV:  z.enum(['dev', 'test', 'production'])
})

9. adicoinar chamar default('dev') em z.enum().

const envSchema = z.object({
    NODE_ENV:  z.enum(['dev', 'test', 'production']).default('dev')
})

10. Criar mais uma variável ambiente: algumas hospedagens setam essa 
 de maneira automatizada. Por isso precisa dela para que os meios externos
 informem qual a porta utilizada.
 
  const envSchema = z.object({
    NODE_ENV:  z.enum(['dev', 'test', 'production']).default('dev')
    PORT: z.coerce.number().default(3333)
})

11. Agora é necessario fazer a validação:

criar const _env = envSchema.safeParse(process.env)

12. Daí faz a verificação de sucesso ou não:

if (_env.success === false) {
    console.error('Invalid enviroment variables', _env.error.format())

    // derruba a aplicação caso tenha problemas nas variáveis ambiente
    throw new Error('Invalid enviroment variables')
}

13. O arquivo index.ts fica com a seguinte configuração após estes passos:

import 'dotenv/config'
import {z} from 'zod'

const envSchema = z.object({
    NODE_ENV:  z.enum(['dev', 'test', 'production']).default('dev'),
    PORT: z.coerce.number().default(3333)
})

const _env = envSchema.safeParse(process.env)

if (_env.success === false) {
    console.error('Invalid enviroment variables', _env.error.format())

    // derruba a aplicação caso tenha problemas nas variáveis ambiente
    throw new Error('Invalid enviroment variables')
}

14. Caso a aplicação execute, vamos exportar uma variável env em index.ts. 

export const env = _env.data

15. No server: adicoinar import {env} from "./env" e adiconar port: env.PORT:

import {app} from './app'
import {env} from "./env"

app.listen({
    host: '0.0.0.0',
    port: env.PORT,
}).then(() => {
    console.log('HTTP Server Running!')
})

16. Executar a aplicação com npm run start:dev.


aula 4: Configurar o eslint

1. configurar o eslint:
2. É possível instalar o eslint e configurar manualmente 
(não precisa seguir do 3 ao 8):
3. npm i eslint -D
4. npx eslint --init
5. Ao executar o último comando, temos algumas configurações:
6. aperte y para proceder.
7. to check syntax, find problems, and enforce code style...
8. ele vai fazer algumas outras pergutas: Responda de acordo com seu projeto:

Vamos utilizar as configurações automáticas da rocketseat:

9. npm i @rocketseat/eslint-config -D
10. Essas são as configurações padrão para o Eslint da rocketseat.


11. criar o arquivo .eslintrc.json na raiz do projeto. Adcionar o seguinte códgio:

{
    "extends": [
        "@rocketseat/eslint-config/node"
    ]
}

12. A formatação da estrutura do projeto é automatica. Caso não dê, basta dar um reload window.

13. Caso não dê certo, digite ctrl shift p e encontre: user settings.json. adicone:
"editor.codeActionsOnSave": {

        "source.fixAll.eslint": "explicit"
        
        },

Meu arquivo ficou assim:

{
    "files.autoSave": "afterDelay",
    "editor.codeActionsOnSave": {

        "source.fixAll.eslint": "explicit"
        
        },
}

14. Caso ainda não dê certo, em output do terminal, veja as configurações do eslint.

15. Criar um arquivo .eslintignore na raiz do projeto. Daí vai ignorar alguns arquivos que eu não quero
que sejam automaticamente corrigidos pelo eslint. no caso, vamos ignorar: 

node_modulees
build


Aula 5: Criar atalhos de caminhos para importações entre arquivos.
Conforme vamos adicionando uma pasta dentro da outra, e supondo que em um arquivo
queremos fazer uma nova importação, a configuração dessa importação pode ficar estranha '../../../arquivo'.

Para encurtar os caminhos da importação, podemos fazer o seguinte:

1. Em tsconfig.json encontrar baseUrl: descomentar e adiconar o seguinte path:

   "baseUrl": "./",                                  
     "paths": {
      "@/*": ["./src/*"]
     }, 

 toda vez que eu importar em qualquer arquivo, a importação que vem com @/ vem direto de src.

2. Para testar se está funcionando, vá em server.ts e troque o ./ por @/ em import { env } from '@/env'. Daí, rode o comando, npm run start:dev. O servidor deve rodar normalmente.


Aula 6: Banco de dados: Há várias estratégias para trabalhar com BD em uma aplicação BackEnd. é possível usar o driver nativo node postgress. Com isso fazemos queryes diretamente. Existe um nível maior de abstração como o knex.js. Ele converte uma sintaxe javascript para uma query em baixo nível. Um nível maior ainda de abstração: ORM. São ferramentas com alto nível de abtração para trabalhar com banco de dados. Objetc Relational Mapper. A ideia é mapear as tabelas campos e relacionamento em objetos. por exemplo uma classe user

class User {
    name: string
    email: string
}

isso se parece com uma tabela no BD. Daí é possível traduzir cada tabela do BD como se fosse uma classe.

Cada ORM tem sua própria sintaxe. Três dos mais famosos são o TypeORM, Sequelize e o Prisma.

O Prisma tem uma grande integração com o typescript. Por exemplo, uma tabela users com 10 campos, o prisma entende essa tabela automaticamente e infere automaticamente
as tabelas os campos. Daí na hora de inserir um campo, ele já indica se vamos encontrar um erro de sintaxe ou não. 

Também faz a parte de migrations automatizada. Não é necessário escrever os arquivos de migrations. Basta alterar o arquivo do BD e ele cria as migrations.




1. Instar npm i prisma -D. 

É instalado em ambiente de desenvolvimento pois, ele faz o acesso ao banco. É uma interface de linha de comando em que é possível fazer operções no BD diretamente no banco.

2. Digite npx prisma -h

veja os possíveis comandos.

Lembre que: 
-- npm instala os pacotes de dependecia do node.
-- npx executa um comando específco que está na pasta bin do node_modules.

3. digitar npx prisma init
 
Ele cria uma pasta prisma e um arquivo schema.prisma.

4. Instalar a extensão do vscode do prisma 'prisma.io'.

5. Nas configurações do editor ctrl shift P (settings.json) confirme que tenha

"prisma": {
    "editor.formatOnSave": true
} 

Meu arquivo ficou assim:

 {
    "files.autoSave": "afterDelay",
    "editor.codeActionsOnSave": {

        "source.fixAll.eslint": "explicit"
        
        },
        "prisma.showPrismaDataPlatformNotification": false,
        
        "prisma": {
            "editor.formatOnSave": true
        }
}

Isso pois quero que quando eu salvo o arquivo ele faça uma formatação no arquivo do schema automaticamente.

O prisma suporta vários BDs. Para criar uma tabela, criamos um 'model'.

Em schema.prisma digitamos o seguinte comando:

model User {
  id String @id @default(uuid())
  name String
  email String @unique

  @@map("users")
}

6. Executar npx prisma generate

Esse comando vai criar automaticamente a tipagem do schema. A tipagem é a integração com o typescript para que o código consiga identificar automaticamente.

Para testar essa funcionalidade, vamos em node_modules. Vamos em prisma, depois em client. Lá abrimos o arquivo index.d.ts

Ao pesquisa por name: string temos a tipagem criar automaticamente. Ele converte a tabela user em um tipo do typescritp. Ele criou todas as operações possíveis em User.
Há no arquivo métodos como findUnique para encontrar um usuário único. Tudo é criado automaticamente.

7. Instalar npm i @prisma/client

Em app vamos importar o PrismaClient e digitar alguns comandos. O arquivo ficará assim:

import fastify from 'fastify'
import { PrismaClient } from '@prisma/client'

export const app = fastify()

const prisma = new PrismaClient()

prisma.user.create({
  data: {
    name: 'Diego Fernandes',
    email: 'diego@rocketseat.com.br',
  },
})

- const prisma = new PrismaClient() -- Cria uma conexão com o banco de dados.

- ao digitar 'prisma.user.' podemos observar que temos todos os métodos já criados automaticamente.

- Com ctrl + espaço em cima do método o vscode informa os tipos de valores aceitos.

- Se eu tentar informa um campo de um tipo diferente do definido, ele emite um alerta.


8. A conexação com o bd ainda não está feita. Vamos fazer isso na próxima aula.


Aula 7: Docker: Vamos subir serviços como postgress, de uma maneira mais simples e que funcione em todos os SO's sem comprometer o ambiente de desenvolviemnto. Esses serviços instalados geram muita bagunça, lixo, resíduos. Por isso como o docker teremos um container isolado, virtualizado e independente.


1. Instalar o docker.

2. Vereifique a instalação com docker -v no terminal.

3. Acessar o docker hub. É um repositório de imagens do docker. Vá em bitnami/postgresql. Eles implementam uma versão com travas de segurança. Por exemplo, eles não
deixam criar uma senha para o usuário root.



4. Para rodar docker run --name api-solid-pg <imagem>

5. docker run --name api-solid-pg -e POSTGRESQL_USERNAME=docker -e POSTGRESQL_PASSWORD=docker -e POSTGRESQL_DATABASE=apisolid -p 5432:5432 bitnami/postgresql

6. é chato ter que digitar esse comando toda vez que preciso iniciar essa aplicação. Caso dê um stop ele para o container, mas mantem ele em cache.
ao digitar docker ps -a ele mostra todos os containers já criados.

dai digiter docker start + id, ou docker start + nome (api-solid-pg)

para parar o contanier docker stop + nome, ou docker stop + id.

para ver logs: dokcer logs api-solid-pg

em .env, veja que o prisma já criou uma varialve de ambiente. faça as alterações:

DATA

rodar npx pridsma migrate dev

ele vai no schema .prsima, verifica as tabelas e campos e que ainda não foi refletido no bd. Daí, para a tabela users, vai detectar, vai criar um novo usuario,
 
create users para criar, 

npx prisma studio


aula:

docker compose. E um arquivo criado na reiz do projeto.

1. criar o arqiuvo docker-compose.yml na raiz do projeto.
2. adicionar o script:

version: '3'

services:
  api-solid-pg:
    image: bitnami/postgresql
    ports: 
      - 5432:5432
    environment: 
      - POSTGRESQL_USERNAME=docker
      - POSTGRESQL_PASSWORD=doccker
      - POSTRESQL_DATABASE=apisolid

3. daí, para subir o bd, digite docker compose up -d


4. docker compose stop para o de rodar o container.


aula:

criar as outras tabelas. 

em schmae.prisma criar as tabelas model e checkin

npx prisma studio


aula: Relacionamento entre tabelas.

1-1 poderia estar tudo na mesma tabela.
1-N 
N-N
