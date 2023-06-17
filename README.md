# Projeto - Compartilhamento de Bicicleta

O projeto se trata sobre a <b> Análise de Dados de um case de aluguel de bicicleta pela empresa Divvy em Chicago entre os anos de 2014 e 2017. </b>

O principal objetivo desse projeto é entender melhor o perfil situacional da utilização das bicicletas, o perfil do usuário e um pouco mais sobre as principais estações que são utilizadas por esses usuários.

Ao todo, o projeto foi separado em <b> 3 fases: </b>
<li> Arquitetura dos dados </li>
<li> Entendimento e Processamento dos Dados </li>
<li> Análise dos Dados</li><br>

Principais Ferramentas:
<li><b>AWS</b></li>
<li> <b>Pyspark</b></li>
<li><b>SQL</b></li>
<li><b>Metabase</b></li><br>

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

Sabendo-se disso, a arquivo .csv, localizado no bucket Raw, foi processado no <b>EMR</b>, utilizando <b>Pyspark</b>, no Jupyter Notebook.<br>

Buscou-se apenas o mínimo para a possível visualização posterior, sendo verificada a existência de nulos, que comprovou que não havia nulos nessa tabela.<br>

Depois, houve a alteração dos tipos de dados das colunas, já que algumas deveriam ser numéricas e outras no formato de timestamp, como no caso do starttime e stoptime. <br>

Ao final dessa etapa, foi gerado um novo arquivo, nesse caso .parquet e ele foi salvo em outro bucket, chamado Curated.

## Análise dos Dados

Com os dados disponíveis no <b> RedShift </b>, conecta-se ao <b> Metabase </b> e o foco se vira para a análise em si. Com isso, alguns cruzamentos entre os dados foram realizados para bucar extrair informações relevantes, entre eles: <br>

<b>1 - Quantidade de utilizações por ano</b>

<br><img src = "./Images/1 -Quantidade_de_utilizações_por_ano.png" alt = "Gráfico 1"><br>

O gráfico demonstra a variação da quantidade de utilização do serviço ao longo dos anos. A partir dele, pode-se notar um aumento gradativo ao longo dos anos, sendo 2017 correspondente a quase o dobre de utilizações de 2014. <br><br><br>

<b>2 - Quantidade de utilizações por mês e pela média da temperatura</b>

<br><img src = "./Images/2 - Quantidade_de_utilizações_pelo_mês_e_pela_média_e_temperatura.png" alt = "Gráfico 2"><br>

Pode-se perceber ao observar o gráfico que conforme a média de temperatura aumenta, o que parece ser ali o final da primavera até o final do verão, as pessoas tendem a utilizar mais o serviço. Isso pode se perceber empiricamente em locais frios como o caso de Chicago, já que com as baixas temperaturas, atividades ao ar livre se tornam mais raras, retirando da lista atividades que dependam do frio. 

Logo, é intuito que a empresa que coordena o serviço foque mais sua estratégia nesses meses, já que a menos que ela não conseguirá com o fator externo, que no caso é o climático.<br><br><br>

<b>3 - Quantidade de utilizações por mês, pela média de temperatura e pela média da duração da viagem</b>

<br><img src = "./Images/3 - Quantidade_de utilizações_pelo mês_pela_média_de_temperatura_e_pela_média_de_duração_da_viagem.png" alt = "Gráfico 3"><br>

Seguindo com a análise da utilização pela temperatura, agora há a adição de mais um fator, que é a média da duração da viagem. Cruzando os dados das 3 colunas conjuntamente, é possível perceber que assim como a quantidade de utilizações, o tempo média da duração da viagem também é aumentado no período de maiores médias de temperatura. Sendo, cerca de quase 30% maior em períodos mais quente quando comparado aos períodos mais frios.

Isso reforça o foco nesse período mais quente, que se estende desde maio até setembro, aproximadamente.<br><br><br>

<b>4 - Quantidade de utilizações pelo dia da semana</b> 

<br><img src = "./Images/4 - Quantidade_de_utilizações_pelo_dia_da_semana.png" alt = "Gráfico 4"><br>

Esse e o próximo gráficos destacarão a relação da utilização das utilizações pelos dias e horas específicos para tentar entender um perfil. Nesse primeiro, pode-se notar uma característica interessante, as utilizações são cerca de 50% superiores em dias de semana que em finais de semana. O que poderia sugerir um efeito de utilização maior com a finalidade de deslocamento para o trabalho ou eventos sociais durante a semana. <br><br><br>

<b>5 - Quantidade de utilizações pela hora do dia</b>

<br><img src = "./Images/5 - Quantidade_de_utilizações_pela_hora_do_dia.png" alt = "Gráfico 5"><br>

Continuando a análise do gráfico anterior, a premissa da utilização com a finalidade do trabalho é reforçada por esse gráfico, já que os horários de maior pico de utilização são entre 8-10 h  e 16 - 18 h. 

Analisando essas possibilidades, uma possível campanha de marketing ou descontos para aumentar a quantidade em horários de menores incidências poderiam ser interessantes.<br><br><br>

<b>6 - Quantidade de utilizações de acordo com o tempo (clima) do dia</b>

<br><img src = "./Images/6 - Quantidade_de_utilizações_por_como_estava_o_tempo_no_dia.png" alt = "Gráfico 6"><br>

O gráfico demonstra que a esmagadora maioria dos dias de utilização são dias nublados, o que ajuda a dedução de que a influência na utilização por conta de o tempo estar bonito ou não estar o mais apropriado é pequena ou nula. <br><br><br>

<b>7 - Quantidade de utilizações por gênero</b>

<br><img src = "./Images/7 - Quantidade_de_utilizações_pelo_gênero.png" alt = "Gráfico 7"><br>

Nota-se que a grande maioria das utilizações é realizada por homens. Seguindo-se a premissa que as utilizações tem seu foco no deslocamento para o trabalho, poderia se pensar, hipoteticamente, na possibilidade da mobilidade pela diferença na vestimenta entre homens e mulheres, principalmente em um ambiente formal. Ou ainda no fator segurança, por se tratar de uma cidade grande. Nesse caso, apenas hipóteses, que deveriam ser avaliadas para buscar soluções afim de aproximar o público feminino. <br><br><br>

<b>8 - Quantidade de utilizações por tipo de usuário</b>

<br><img src = "./Images/8 - Quantidade_de_utilizações_por_tipo_de_usuário.png" alt = "Gráfico 8"><br>

O gráfico implica que mais de 90% dos usuários são inscritos, isso pode implicar que a política de inscritos está funcionando e poderia ser melhor explorada e/ou as outras opções poderiam ser melhores exploradas ou descartadas. Os casos teriam que ser melhor abordados para se chegar na melhor política possível. <br><br><br>

<b>9 - 10 estações de partida com maior movimento</b>

<br><img src = "./Images/9 - 10_estações_de_partida_com_maior_movimento.png" alt = "Gráfico 9"><br><br>

<b>10 - 10 estações de chegada com maior movimento</b>

<br><img src = "./Images/10 - 10_estações_de_chegada_com_maior_movimento.png" alt = "Gráfico 10"><br>

O cruzamento dos dados geram, nesses gráficos, as 10 melhores possibilidades de investimento em uma oferta maior de bicicletas, visto que elas possuem maior volume de utilizações. Ainda mais, as 4 estações com maior volume de utilizações são as mesmas tanto para partida como chegada, o que poderia se explicar pela proximidade de grandes centros corporativos. 

Mas, o foco deve se dar no quesito que por apresentarem um grande volume, uma maior oferta pode gerar maior número de utilizações e com isso maior retorno. <br><br>

## Conclusão

O projeto foi elaborado tendo a premissa de avaliar, a partir dos dados, a relação entre as utilizações, perfil e as estações, buscando entender melhor sobre possíveis melhoras em estratégias e ofertas de serviço. Para isso, foram utilizadas ferramentas, tais como <b>Pyspark, SQL, AWS, Metabase</b> para auxiliar na resposta desses questionamentos.

Após o trabalho com os dados e a análise, nota-se que o principal perfil dos usuários é de homens, inscritos, provavelmente utilizando para trabalho, entre 8-10 e 16-18, com utilizações maiores em períodos quentes (como final da primavera e verão), focados principalmente em 5 grandes estações, seja para partida ou para chegada. 

Isso ajuda a entender o usuário atual, gerar personas melhores para o marketing e pensar em possíveis políticas e estratégicas de marketing, como a decisão de focar em ganhar um volume maior de usuárias femininas ou de tentar aumentar o público em horários de menor incidência de utilização através de campanhas de marketing mais efetivas ou descontos. Por final, mas não menos importantes, uma possível estratégia de um aumento de oferta nas principais estações de partida e chegada, dados os volumes apresentados.

Com o intuito de avançar com a ideia do projeto, poderia se gerar uma discussão e avaliar alguns indicadores (KPIs) para que fossem observados ao longo do tempo e que pudessem estar disponíveis em um dashboard, que poderia ser gerado no próprio Metabase (ou outra ferramenta de visualiação).


