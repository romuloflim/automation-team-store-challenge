# GERENCIADOR DE PRODUTOS - DAFITI CHLLNG


O Dafiti Group abrange um grupo de empresas que aliam moda à inovação, e no momento em que vivemos, não dá para pensar em inovação se não tiver lado a lado com a tecnologia e, mais especificamente, a Tecnologia da Informação e seu papel fundamental nas interações entre pessoas, empresas, produtos e serviços, garantindo cada vez mais agilidade e aproximação entre os extremos. Nesta perspectiva, o grupo conta com um time de profissionais de TI que utilizam as ferramentas mais recentes e produtivas, almejando bons resultados nos bastidores das atividades virtuais desempenhadas pela empresa.

Para integrar mais profissionais ao time, o grupo de automação lançou um problema, que na verdade é um desafio, para ser feita a implementação de um projeto que represente miminamente algumas das regras de negócio dos serviços da Dafiti, que consiste em fazer uma aplicação capaz de criar dados, visualizá-los, alterálos e excluí-los, tarefas conhecidas como CRUD pelos desenvolvedores. Os dados são relacionados aos negócios da empresa: produtos de vestuário, e é preciso que haja uma estrutura que faça a persistência dos dados e os torne acessíveis e manipuláveis por uma interface para o usuário, havendo então uma ponte para comunicação entre as duas partes. E para implementar o projeto, nada mais justo que utilizar as ferramentas que já são usadas pelo time de TI da empresa.
Sendo assim, para a implementação desse projeto, foram utilizadas as tecnologias que serão discutidas junto à estrutura do projeto logo adiante.


## Desvendando o projeto

Abaixo estão listadas as tecnologias utilizadas no projeto, e que são necessárias, dentre outras dependências, para ser executado. O passo a passo da instalação estará no tópico sobre a execução do projeto!

- Python 3.9.2: Linguagem utilizada para construir a aplicação
- Django 3.1.7: Framework Python para a construção do serviço que tem os objetos e métodos que farão parte da arquitetura de manipulação dos dados
- Django REST Framework 3.12: para a construção de uma API REST que é responsável pela comunicação entre a interface e o serviço de gerenciamento dos dados
- PostgreSQL 13.2: Sistema Gerenciador de Banco de Dados (SGBD) que fará a persistência dos dados do projeto
- React 17.0.2: Biblioteca em JavaScript que oferecerá a interface para que o usuário seja capaz de visualizar e manipular os dados por meio de um website, que contará com o Axios para fazer as chamadas à API e Bootsrap 5 na estilização das páginas.


### Back-End: Serviço, API e Banco de Dados
Para a construção do serviço que conta com as regras de negócio do projeto, direcionando os dados de acordo com as chamadas à API, a arquitetura utilizada será a oferecida pelo Django, que conta com a seguinte estrutura: há um diretório-base com o nome do projeto, que, dentre outros componentes, conta com o arquivo `settings.py`, onde são feitas as configurações do projeto e `urls.py`, onde definimos as rotas para cada funcionalidade da aplicação. Compondo a estrutura, existe o arquivo `manage.py` que executa ações importantes da aplicação.
O `settings.py` tem grande importância nas definições do projeto. É nele que definimos o tão importante banco de dados, na variável `DATABASES`. Nela é especificado qual SGBD que utilizaremos, neste caso, o PostgreSQL, e as credenciais de acesso ao banco. Segue o exemplo:
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'productmanager_dafiti',
        'USER': 'postgres',
        'PASSWORD': 'root',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
```
O app products, que é um diretório com os componentes que toda aplicação do Django conta, está adicionado à lista de outros apps (`INSTALED_APPS`) no `settings.py`. No diretório deste projeto, podemos destacar os seguintes arquivos: `models.py`, `views.py` e `serializers.py`.

**Models** _- models.py_
Classes que extendem o `Model` são representações das tabelas nas formas de objeto. Elas contam com os atributos e suas especificações que definirão as colunas na tabela. O projeto conta com três:

_Product_: é o modelo principal desse projeto. Ele armazena as representações dos produtos, como o nome, tipo de produto, categoria e  tamanho. Abaixo, temos um trecho do objeto junto aos seus atributos, que podem ser conferidos com mais detalhes explorando o arquivo `models.py` no diretório `products`.
```
class Product(models.Model):

    TYPES=[...]

    name = models.CharField("Nome", max_length=255)
    category = models.ForeignKey(ProductCategory, on_delete=models.SET_NULL, null=True)
    product_type = models.SmallIntegerField("Tipo", choices=TYPES)
    ...
```
_ProductCatrgory_: modelo que conta com as diferentes categorias que um produto pode ter. Como não há uma quantidade certa de categorias, elas serão adicionadas de acordo com a necessidade do usuário e fará parte do produto por meio de um relacionamento.

_Brand_: este modelo também se relaciona com o produto e definirá a marca dele. Existe uma infinidade de marcas e também serão adicionadas de acordo com a necessidade do usuário.

**Serializers** _- serializers.py_
Os serializadores fazem o tratamento dos dados que são guardados no banco por meio dos modelos fazendo a validação dos campos, e prepara os dados que serão enviados pela API para o front, normalmente por meio de um arquivo de formato leve (JSON) contendo cada campo que será necessário na montagem dos valores na interface, e também variáveis necessárias para realizar ações no front-end e posteriores requisições à API. Os serializadores deste serviço extendem o serializador oferecido pelo _Django Rest Framework_ para a construção da API REST. No projeto, há três - um para cada model.

**Views** _- views.py_
As views recebem e tratam as requisições feitas pelo cliente no front-end (Axios neste caso) e tratam de acordo com o que é pedido na requisição por meio da URL e operações vindas na requisição, como os métodos HTTP que serão utilizados (GET, POST, PUT, DELETE). Após receber a requisição e fazer o que é pedido, um retorno é enviado ao cliente sobre sua solicitação. Aqui podemos destacar os diferentes status que indicam se a requisição foi bem sucedida ou não, muito utilizada pelo front-end, e também os dados vindos do banco de dados solicitados na requisição. As views do projeto também estão intimamente ligadas ao Django REST Framework, que monitora os chamados através da `api_view` e direciona o chamado ao método correto.

> Nota: para ter uma maior noção dos elementos citados, é interessante que o leitor esteja visualizando os códigos do projeto :)

**Endpoints da API**
Abaixo estão listados os acessos utilizados pelo cliente para fazer as requisições à API, os chamados _endpoints_,  que vêm após a URL base do serviço. Suas rotas são definidas no arquivo urls.py que se encontra no diretório base do projeto.

```[GET] /api/products/```
Retorna uma lista com todos os produtos adicionados no banco. Há um outro método GET em que a URL é definida pela API e traz os produtos paginados.

```[GET] /api/products/{pk}```
Retorna a lista de dados de um produto especifico por meio do fornecimento de seu ID (ou primary key - pk) na URL.

```[DELETE] /api/products/{pk}```
Deleta um produto por meio de seu ID no endpoint.

```[POST] /api/products/```
Cria um produto, fornecendo na requisição um objeto (JSON) com os dados necessários para preencher os campos do produto.

```[PUT] /api/products/{pk}```
Altera as informações de um produto, especificando sua primary key e enviando na requisição um objeto com os dados que substituirão os dados atuais cadastrados.

```[GET] /api/categories/```
Retorna uma lista de todas as categorias, que será utilizada para popular o campo de seleção do formulário que cria e altera produtos.

```[POST] /api/categories/```
Cria uma nova categoria no banco de dados, enviando na requisição os dados da categoria.

```[GET] /api/brands/```
Retorna uma lista de todas as marcas, que será utilizada para popular o campo de seleção do formulário que cria e altera produtos.

```[POST] /api/brands/```
Cria uma nova marca no banco de dados, enviando na requisição os dados da marca.


## Front-end: Interface por meio de páginas web
A interface do projeto, implementada utilizando o React e estilizada com Bootstrap, foi feita de forma básica e objetiva para atender a API por completo. A estrutura básica e a facilidade de construção de um projeto React é bastante útil. Além dos componentes que formam a base (como o `App.js` e `index.js`), foram criados  três outros, que são os que interessam para o back-end. Abaixo estão listados:

O `ProductsList.js`, renderiza a página inicial da interface e, como o prórpio nome indica, exibe os produtos em uma tabela contento os dados da de cada produto, juntamente às opções de excluir e editar um produto. Há também a paginação da tabela com uma navegação básica.

O `ProductCreateUpdate.js` é responsável por fornecer um formulário para a criação de um produto novo ou mesmo editar os dados de um produto específico. Os dois componentes citados utilizam a mágica dos _React Hooks_ para o gerenciamento dos estados da aplicação e a construção do formulário.

O `ProductsService.js`, apesar de simples, é fundamental para a comunicação com a API. Ela faz o uso das funcionalidades do _Axios_, que é o pacote instalado para ser o cliente que faz as requisições ao serviço. A classe `ProductsService` tem um método para cada um dos endpoints citados anteriormente, especificando os dados e os métodos HTTP necessários. Os dois componentes mencionados utilizam este componente sempre que é preciso fazer uma chamada à API.
```
createProduct(product){
        const url = `${API_URL}/api/products/`;
        return axios.post(url, product);
    }
```
O exemplo acima é um método da classe `ProductsService` e serve para criar um novo produto. Para isso, recebe o objeto do produto como parâmetro e o Axios faz o trabalho de enviar a requisição para a URL da API responsável pela adição do produto no banco de dados, definindo-o como POST.

## Executando o projeto

Os passos para executar o projeto em diferentes sistemas operacionais são muito parecidos, mas é importante cumprir o requisitos abaixo:
- Ter o Node.js instalado em uma das versões mais recentes (a partir da 6) e certificar se o yarn está instalado. Caso não esteja, basta executar o comando no terminal do seu sistema: 
```sh
npm install --global yarn
```
- A API está rodando na plataforma Heroku, tornando opcional a execução local desta, mas caso seja de seu interesse, segue os requisitos de máquina para rodar o serviço.
- Ter Python 3 instalado na máquina e certificar se os comandos pip e venv estão sendo executados.
- Ter o PostgreSQL  a partir da versão 11.5 instalado. Ter o PgAdmin 4 instalado facilita a manipulação de bancos porque oferece uma interface para os comandos SQL.

Para iniciarmos a interface React, após clonar este repositório, siga os comandos abaixo:

Via terminal acesse o diretório onde está o front-end da aplicação: `frontend-react`. Vamos instalar as dependências necessárias para o projeto rodar:
```sh
yarn install
```

Por padrão, no arquivo `ProductsService.js` no diretório `src` do front-end, a URL da API está apontada para a que está no Heroku (`const API_URL = URL_HEROKU`), mas caso deseje rodar a API localmente, basta definir a `API_URL` com a constante `URL_LOCAL` que já está instanciada: `const API_URL = URL_LOCAL`.

Com as dependências instaladas e o ajuste da URL da API se necessário, hora de iniciar o front-end via yarn:
```
yarn start
```
Pronto! Nossa interface já está pronta para explorar a API.

Agora, seguiremos os passos para rodar o projeto Django localmente, que é opcional. Para dar início, crie o diretório onde ele será executado e acesse-o via o terminal do seu sistema. Dentro do diretório iremos criar o ambiente virtual Python por meio do comando:
```sh
python3 -m venv ./env
```
Em seguida, podemos ativar nosso ambiente virtual, executando o seguinte comando:
Para Ubuntu e MacOS:
```sh
source env/bin/activate
```
No Windows:
```sh
env\Scripts\activate
```
Com o ambiente criado e ativado, é hora de clonar o projeto através do link do repositório:
```sh
git clone https://github.com/romuloflim/automation-team-store-challenge.git
```
Utilize o editor de código de sua preferência para abrir a pasta do projeto. Lembrando que trabalharemos com as linguagens Python e JavaScript.

Aproveite também para criar o banco de dados PostgreSQL que será utilizado para guardar os dados da aplicação.
No editor de código, abra o arquivo `settings.py`, que está dentro do diretório `productmanager`. Procure a variável `DATABASES` apague suas definições e adicione as informações do seu banco, junto à `ENGINE`. Exemplificando, as configurações estão definidas da seguinte forma:
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'dafiti_db',
        'USER': 'postgres',
        'PASSWORD': 'root',
        'HOST': '',
        'PORT': '5432',
    }
}
```
A variável `SECRET_KEY` precisa receber uma chave String. Você pode definir a chave através de uma funcionalidade no shell do Python. No terminal, inicie o shell digitando `python` e dando enter. Com o shell iniciado, digite os comandos nessa ordem:
```sh
>>> import secrets
>>> secrets.token_hex(24)
```
Uma chave será gerada! Copie-a, procure a `SECRET_KEY` no seu `settings.py` apague o objeto que ela está recebendo e a defina com o valor copiado, entre aspas (String).
```sh
SECRET_KEY = 'chave_que_foi_gerada'
```
Outras constantes do settings que podem ser de interesse do dev definir são a `CORS_ORIGIN_WHITELIST` para passar uma lista de URLS que podem acessar a aplicação, e a `CORS_ORIGIN_ALLOW_ALL` que pode ser definido como verdadeiro ou falso.

Dando prosseguimento às configurações, instalaremos os requisitos do projeto Python, que são os pacotes presentes no arquivo `requirements.txt` do nosso projeto.
```sh
pip install -r requirements.txt
```
Em sistemas MacOS, não há o pacote engine do PostgreSQL `psycopg2`. Então devemos ir no arquivo `requirements.txt` e apagar a linha em que ele aparece (`psycopg2=2.8.....`) para não dar erro na instalação dos requisitos. Ele é necessário para a execução do projeto, então instalaremos o pacote binário disponível:
```sh
pip install psycopg2-binary
```
Após a instalação dos pacotes, a aplicação em Python ganha vida, junto às diversas funcionalidades do framework Django.

Executaremos agora o comando do Django para criar as tabelas necessárias do projeto no banco de dados. As tabelas podem ser visualizadas no `models.py`, no diretório `products`, a única aplicação Django criada para este projeto.
```sh
python manage.py migrate
```
Na tabela migrations, a migration `0002_products.py` tem a função `create_data`. Ela é responsável por criar, após a criação das tabelas, uma linha em cada tabela, com seus respectivos dados.

Se o banco de dados foi adicionado corretamente, assim como todos os requisitos instalados, as migrações do projeto para o banco ocorreram com sucesso, com as alterações aparecendo no terminal com um `OK` no final!
É hora de iniciar o servidor e tornar a API disponível para acesso local. Fazemos isso por meio do seguinte comando:
```sh
python manage.py runserver
```
Servidor iniciado! Hora de fazermos as operações disponíveis no serviço. 