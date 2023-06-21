# Otimização na Logistica com Análise Exploratória de Dados Geolocalizados

# **Tópicos**

<ol type="1">
  <li>Contexto;</li>
  <li>Pacotes e Bibiliotecas;</li>
  <li>Exploração de dados;</li>
  <li>Manipulação;</li>
  <li>Visualização;</li>
  <li>Insigths.</li>
</ol>

## 1\. Contexto

Os dados correspondem a uma empresa de logística Loggi, onde há três bases em Brasília e a distribuição de pacotes em diferentes regiões da cidade, o objetivo é obter insights para aumentar a eficiência na entrega dos pacotes.

![Loggi simbolo](https://github.com/PhD-Anibal/Logistica-usando-EDA/blob/main/img_loggi.png)

* *A Loggi é uma startup unicórnio brasileira de tecnologia focada em logística. A Loggi começou entregando apenas documentos entre 2013 e 2014. Dois anos depois, entrou no segmento de e-commerce. E, desde 2017, tem atuado nas entregas de alimentos também* *

São utilizados os dados disponíveis para carregá-los em um dataframe do Pandas. Isso  permitirá explorar, compreender e manipular a estrutura dos dados, tornando-os mais organizados e facilitando análises posteriores.

É realizada a manipulação dos dados para verificar a qualidade das informações e utilizando os dados de geolocalização, serão identificados os hubs e os destinos de cada hub em um mapa. Essa visualização enriquecerá a base de dados, permitindo obter insights adicionais e avaliar a qualidade dos dados.

Dessa forma, os dados estarão preparados de forma adequada para extrair informações relevantes e ajudar a empresa Loggi a aprimorar sua eficiência na entrega de pacotes.

Arquivos com os códigos em Python:
loggi_1ra_parte.ipynb
loggi_2da_parte.ipynb

## 2\. Pacotes e bibliotecas

### Pacotes e bibliotecas de manipulação de dados:

json: fornece funcionalidades para trabalhar com dados no formato JSON (JavaScript Object Notation). Ele permite a leitura e gravação de dados JSON, bem como a manipulação e extração de informações desses dados.

pandas: biblioteca que fornece estruturas de dados eficientes, como o DataFrame, que facilitam a limpeza, transformação e análise de conjuntos de dados tabulares. O pandas é amplamente utilizado em tarefas de análise de dados e ciência de dados.

### Pacotes e bibliotecas de geolocalização:

geopy: biblioteca que fornece uma interface simples para realizar operações relacionadas à geolocalização. Ele permite a obtenção de coordenadas geográficas a partir de endereços, cálculo de distâncias entre pontos e outras funcionalidades relacionadas à localização geográfica.

Nominatim: Serviço de geocodificação fornecido pelo OpenStreetMap. A biblioteca geopy utiliza o Nominatim para converter endereços em coordenadas geográficas (latitude e longitude) e vice-versa.

RateLimiter: Biblioteca que permite controlar a taxa de acesso a APIs ou serviços externos. Ele pode ser usado junto com outras bibliotecas, como o geopy, para garantir que não sejam feitas muitas solicitações em um curto período de tempo, evitando bloqueios ou restrições.

### Pacotes e bibliotecas numéricas e de visualização:

numpy: Biblioteca que fornece suporte para arrays multidimensionais, operações matemáticas eficientes, funções de álgebra linear, geração de números aleatórios e muito mais. O numpy é amplamente utilizado em tarefas que envolvem manipulação e processamento de dados numéricos.

geopandas: É uma extensão da biblioteca pandas, projetada especificamente para trabalhar com dados geoespaciais. Ela permite a manipulação de dados geográficos, como geometrias de pontos, linhas e polígonos, e fornece ferramentas para realizar operações espaciais e análises.

### Pacotes e bibliotecas de visualização de dados:

matplotlib: Biblioteca de visualização de dados amplamente utilizada em Python. Ela oferece uma ampla gama de funcionalidades para criar gráficos e visualizações, incluindo gráficos de linhas, de barras, de dispersão, histogramas, gráficos 3D, entre outros. O matplotlib é altamente personalizável e oferece controle detalhado sobre todos os aspectos visuais dos gráficos.

seaborn: Biblioteca de visualização de dados baseada no matplotlib. Ela fornece uma interface de alto nível para criar visualizações estatísticas atraentes e informativas. O seaborn possui recursos avançados para plotagem de gráficos estatísticos, como gráficos de distribuição, gráficos de dispersão, gráficos de barras categorizadas e muito mais. Além disso, o seaborn oferece opções de personalização e estilos pré-definidos que facilitam a criação de gráficos visualmente agradáveis.


## 3\. Exploração de dados
os dados são obtidos do GitHub:
!wget -q https://raw.githubusercontent.com/andre-marcos-perez/ebac-course-utils/main/dataset/deliveries.json

Os dados em bruto é um arquivo do tipo JSON com uma lista de instâncias de entregas. Cada instância representa um conjunto de entregas que devem ser realizadas pelos veículos do hub regional. Exemplo:

json [ { "name": "cvrp-0-df-0", "region": "df-0", "origin": {"lng": -47.802664728268745, "lat": -15.657013854445248}, "vehicle_capacity": 180, "deliveries": [ { "id": "ed0993f8cc70d998342f38ee827176dc", "point": {"lng": -47.7496622016347, "lat": -15.65879313293694}, "size": 10 }, { "id": "c7220154adc7a3def8f0b2b8a42677a9", "point": {"lng": -47.75887552060412, "lat": -15.651440380492554}, "size": 10 }, ... ] } ] ...

Onde:
- name: uma string com o nome único da instância;
- region: uma string com o nome único da região do hub;
- origin: um dict com a latitude e longitude da região do hub;
- vehicle_capacity: um int com a soma da capacidade de carga dos veículos do hub;
- deliveries: uma list de dict com as entregas que devem ser realizadas.

Sendo que:
- id: uma string com o id único da entrega;
- point: um dict com a latitude e longitude da entrega;
- size: um int com o tamanho ou a carga que a entrega ocupa no veículo.

Na exploração:
São normalizadas as colunas com uma operação conhecida como flatten ou achatamento que transforma cada chave do JSON em uma nova coluna.

A localização das bases (Hubs) estão com a latitude e longitude no mesmo campo, estes são separados em duas colunas.

O mesmo é feito para cada localização das entregas que cada base teria que fazer.

É Verificado quais colunas possuem dados faltantes: Vazios ( "" ), Nulos ( None ), Não disponíveis ou aplicaveis ( na , NA , etc.), Não numérico ( nan , NaN , NAN , etc).

Finalmente é feito um breve análise dos tipos de dados e estatística.


## 4\. Manipulação
Geodecodificação reversa, pegamos a lista dos Hub e as coordenadas para obter seus endereços.

O mesmo é feito com as coordenadas de cada entrega mantendo de esta base de dados unicamente os dados da região do Hub, bairro e endereço da entrega.

Logo depois misturamos com a base de dados incluindo aos dados da entrega os dados dos HUb obtendo um datafreme deliberies_df. Em este datafreme cada linha uma entrega indicando a capacidade, localização e nome do Hub resonsavel por essa entrega.

## 5\. Visualização

### 5\.1. Mapa de entregas por região
Utilizando o pacote Python GeopPandas para visualizar as coordenadas dos hubs e das entregas no mapa do Distrito Federal do site oficial do IBGE que pode ser obtido através do seguinte [link](https://www.ibge.gov.br/geociencias/cartas-e-mapas/bases-cartograficas-continuas) para criar o DataFrame mapa. Logo é gerado o seguinte mapa:

![Geodata](https://github.com/PhD-Anibal/Logistica-usando-EDA/blob/main/img_geo.png)


Com o seaborn é gerado o seguinte grafico de barras de entregas por região:

![Gráfico de barras da proporção de entregas por Hub](https://github.com/PhD-Anibal/Logistica-usando-EDA/blob/main/img_proporcao.png)

## 6\. Insigths

O hub df-0 realiza entregas em grandes distâncias e tem um volume de entregas significativamente menor em comparação aos outros hubs. É importante entender se a baixa quantidade de entregas se deve ao fato dos veículos estarem ocupados durante o percurso até o destino ou se eles ficam disponíveis para realizar mais entregas. Caso estejam disponíveis, poderiam ser úteis em outros hubs ou poderia ser considerada a possibilidade de expandir a área de atuação desse hub.

Já o hub df-1 é o que concentra a maior parte das entregas. Seria útil compreender o motivo pelo qual a sua área de atuação ocupa uma região que aparentemente ficaria mais próxima do hub df-2, que realiza um menor volume de entregas. Se a região de atuação fosse ajustada, seria possível equilibrar melhor a proporção de entregas entre os hubs.
