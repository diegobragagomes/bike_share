# Projeto - Compartilhamento de Bicicleta

O projeto se trata sobre a <b> Análise de Dados de um case de aluguel de bicicleta pela empresa Divvy em Chicago entre os anos de 2014 e 2017. </b>

O principal objetivo desse projeto é entender melhor o perfil situacional da utilização das bicicletas, o perfil do usuário e um pouco mais sobre as principais estações que são utilizadas por esses usuários.

Ao todo, o projeto foi separado em <b> 3 fases: </b>
<li> Arquitetura dos dados </li>
<li> Entendimento e Processamento dos Dados </li>
<li> Análise dos Dados</li>

## Arquitetura dos Dados

Nessa etapa, o objetivo foi desenhar a arquitetura, isto é, o caminho pelo qual os dados partiriam desde o computador até a disponibilização para a ferramenta de visualização de dados. Abaixo, pode-se observar o desenho dessa arquitetura:

<img src = "./Images/Credit_Card_Project.drawio.png" alt = "Arquitetura de Dados">

<li> Tudo se inicia com o download dos dados do case <a href="https://www.kaggle.com/datasets/yingwurenjian/chicago-divvy-bicycle-sharing-data">Chicago Divvy</a> no Kaggle. </li><br>

<li>Após isso, os dados são inseridos em um bucket, chamado de Raw, no <b>S3</b> dentro da AWS. Esses dados chegarão brutos, em .csv, e serão processados pelo <b>EMR</b> (Elastic Map Reduce) e serão salvos em .parquet em outro bucket no <b>S3</b>, chamado de Curated.</li><br>

<li>A partir desse dado em .parquet, haverá um acionamento de um crawler no <b>Glue</b>, responsável por entender a estrutura e o tipo dos dados, para catalogar como esses dados estão dispostos no arquivo e qual é o formato de cada uma das colunas. </li><br>

<li>Seguindo o caminho dos dados até a visualização, faz-se necessária a utilização do <b>Redshift Spectrum</b>, que permite a leitura de arquivos no <b>S3</b> pelo <b>Redshift</b> ao se criarem schemas e tabelas externas, com o auxílio do catálogo prévio do <b>Glue</b>. A partir desse processo, os dados que foram catalogados no <b>Glue</b> e estão presentes no <b>S3</b>, podem ser lidos e disponibilizados no <b>RedShift</b>.</li><br>

<li>Por último, a ferramenta de visualização escolhida foi o <b>Metabase</b>, pelo fato de ser open source e trabalhar com o <b>SQL</b>, que é uma ferramenta que também buscava desenvolver e expor. Para que o <b>Metabase</b> pudesse funcionar, foi utilizado o <b>Docker</b> e a partir da imagem metabase/metabase rodando na porta 3000 do localhost, houve a possibilidade de utilização dele. A conexão no <b>Metabase</b> é feita de maneira bastante simples, conectando ao database do <b>Redshift</b> e fazendo queries a partir dele.</li><br>

Essa é uma arquitetura simples, mas cumpre o objetivo de se trabalhar quase inteiramente na nuvem, passando pelos processos de storage, processamento e disponibilização como um DW, pelo <b>Redshift</b>, até a conexão com a ferramenta de visualização, esta que poderia estar sendo utilizada num EC2, como um app no Elastic BeanStalk, numa app no Heruko, ou simplesmente na própria nuvem do <b>Metabase</b>, como seria o caso de uma versão online paga para o <b>Power BI</b>, <b>Tableau</b> e demais.

## Entendimento e Processamento dos Dados

Esse case consiste em uma tabela única com 23 colunas, todas do tipo string, sendo:<br>
    <li> trip_id : o ID da viagem </li>
    <li> year : ano que ocorreu a determinada viagem </li>
    <li> month : mês que ocorreu a determinada viagem </li>
    <li> week: semana que ocorreu a determinada viagem </li>
    <li> day: dia que ocorreu a determinada viagem </li>
    <li> hour : hora que ocorreu a determinada viagem </li>
    <li> user_type: tipo de cadastro do usuário </li>
    <li> gender: gênero do usuário </li>
    <li> starttime: horário de início da viagem </li>
    <li> stoptime: horário de finalização da viagem </li>
    <li> tripduration: duração da viagem </li>
    <li> temperature: temperatura, em Fahrenheit, no momento da viagem </li>
    <li> events: tempo (clima) no momento da viagem </li>
    <li> from_station_id: ID da estação de origem </li>
    <li> from_station_name: nome da estação de origem</li>
    <li> latitude_start: latitude de origem </li>
    <li> longitude_start: longitude de origem </li>
    <li> dpcapacity_start: número de espaços para as bicicletas em cada estação de origem </li>
    <li> to_station_id: ID da estação de chegada </li>
    <li> to_station_name: nome da estação de chegada</li>
    <li> latitude_end: latitude de chegada </li>
    <li> longitude_end: longitude de chegada </li>
    <li> dpcapacity_end: número de espaços para as bicicletas em cada estação de chegada</li><br>

Sabendo-se disso, a arquivo .csv, localizado no bucket Raw, foi processado no EMR, utilizando Pyspark, no Jupyter Notebook.<br>

Buscou-se apenas o mínimo para a possível visualização posterior, sendo verificada a existência de nulos, que comprovou que não havia nulos nessa tabela.<br>

Depois, houve a alteração dos tipos de dados das colunas, já que algumas deveriam ser numéricas e outras no formato de timestamp, como no caso do starttime e stoptime. <br>

Ao final dessa etapa, foi gerado um novo arquivo, nesse caso .parquet e ele foi salvo em outro bucket, chamado Curated.

##Análise dos Dados


  
  

  


