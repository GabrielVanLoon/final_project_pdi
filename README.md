# Geração de Imagens para Pintura Terapeutica

![Pintura do ICMC](./images/output/icmc_recolorized.jpg)

## Resumo do Projeto

Geração de imagens para pintura terapeutica a partir de imagens com diferentes propriedades recebidas pelo usuário (eg: imagens fotorealistas, selfies, quadros, pinturas, desenhos animados etc).  

## Objetivo

Livros de colorir são uma uma categoria bastante comum em livrarias e bancas e possuem como público alvo desde crianças até jovens e adultos, mas nem sempre é possível encontrar um livro cujas ilustrações sejam de nosso interesse.

Pensando nisso criamos esse projeto que permite gerar, a partir de uma imagem real de qualquer natureza, uma imagem de **pintura terapeutica** que possa ser impressa e colorida com o mapeamento de cores indicado pelo grupo.

Por fim, para garantir um resultado satisfatório do projeto e que o output possua qualidade tentaremos cumprir os seguintes requisitos:

- Limitar o número de cores a serem utilizadas, pois em um cenário real uma pessoa não teria todo o espectro de cores.
- Garantir que as áreas pintáveis foram geradas de forma razoável e de fácil interpretação.

## Estrutura do Projeto e Como Executar

Para executar o projeto, recomendamos o uso do script `main.py` que irá solicitar alguns parâmetros de configuração para o usuário e salvar os resultados obtidos na pasta `./images/output/`. Recomendamos a leitura dos arquivos em `./case` para garantir que os inputs estão sendo inseridos nos formatos corretos.

```
/
| -- main.py            Script com pipeline final p/ geração das imagens de preview e pintura.
| -- dependencies.txt   Lista de dependências do projeto.
| -- /src               Pasta com funções utilizadas ao longo do projeto;.
| -- /exploratory       Notebooks utilizados para estudo e exploração de técnicas de PDI.
| -- /cases             Entradas de exemplo para o arquivo main.py
| -- /images            Pasta com algumas das imagens utilizadas como input do projeto
```

Recomendamos o uso do Python 3 e a instalação das seguintes dependências listadas em `dependencies.txt`.

## Descrição do Pipeline de Processamento

O pipeline encontrado possui uma primeira etapa de pré-processamento e extração de paleta em comum mas consta com uma etapa de segmentação opcional utilizando o método SLIC (motivo discutido logo em seguida)

- **Pré-processamento**
  - **Leitura da Imagem** no formato `png` ou `jpg`
  - **Image Downsampling** para dimensões máximas de 1000x1000
  - **Image Enhance** utilizando Gaussian Filter (`sigma=2`)
  - ~~Quantitização da imagem para B bits para remover possíveis ruídos~~
- **Extração de Paleta de Cores (Quantização)**
  - **Kmeans Clustering** para extração das N Cores
  - **Recolorização** da imagem utilizando apenas cores da paleta
- **Quebra da imagem em Superpixels (Opcional)**
  - **SLIC Segmentation** para encontrar clusters de regiões de cores semelhantes
  - **Recolorização SLIC** da imagem utilizando a intensidade média de cada cluster
  - **Recolorização dos Clusters** aplicaçando a paleta de cores
- **Geração da Imagem Colorível e seu Preview**
  - **Edge Detection** por meio do mapa de labels/cores 
  - **Export das imagems** recolorizadas e de bordas na pasta `./images/output/`

### *Quando utilizar o método SLIC?*

Caso a imagem final possua regiões muito estreitas para serem de fato coloridas ou queira ter um maior controle no nível de detalhes final com maior precisão. 

### *Quando (não) utilizar o método SLIC?*

Recomendamos o uso do método SLIC em imagens complexas e com bordas pouco definidas. Um exemplo de quando não utizar o método seria em imagens artificiais como imagens de quadrinhos ou desenhos de animes.

## Imagens de Teste

Para realização dos testes utilizamos imagens com diferentes propriedades de uma base de imagens com licença copyleft: [https://unsplash.com/](https://unsplash.com/) e podem ser encontradas na pasta `images/raw/`.

Buscamos imagens com diferentes propriedades propositalmente para buscar cenários cujo tipo de imagem gerasse alguma limitação ou dificuldade adicional (Ex: retratos foto realistas, desenhos artificiais bem definidos, pinturas abstratas, pinturas monocromáticas).

## Resultados Obtidos

![Imagem Colorível - Grafitti (2 Cores)](images/output/blackandwhite_graffiti_N2_slic_edges.jpg)

Na pasta `./exploratory` encontram-se alguns dos notebooks (ordenados aproximadamente baseado na ordem de progressão do projeto) utilizados pelo grupo para explorar os métodos utilizados (ou não) no pipeline final. Cada documento possui (geralmente) seu objetivo e comentários dos resultados obtidos no decorrer de cada documento.

No geral, o uso do pipeline de extração de paleta de cores em conjunto com a segmentação SLIC e uma etapa simples de pré-processamento se mostraram satisfatórios para geração das imagens coloríveis após um refinamento manual simples dos parâmetros de entrada para cada caso (ver `./images/output`).

Como exemplo das opções levantadas e não utilizadas citamos a de utilizar quantização utilizando MSB ou Luminance para tentar realizar um mapeamento dos canais RGB em um canal único para serem utilizadas as técnicas de segmentação vistas em aula, mas que a utilização do SLIC permitiu a obtenção de uma segmentação melhor e respeitando as variações de pequenas nuances de cores na imagem mesmo em pinturas monocromáticas.

Quanto aos 3 métodos de extração de paleta de cores levantados (citados com mais calma no notebook de `Color Palette Extraction`) escolheu-se o Kmeans devido a sua simplicidade e ao fato de grande parte dos artigos consultados utilizarem técnicas baseadas no método para essa tarefa (geralmente com alguma melhoria como otimização dos clusters inicias) apesar de o método ter algumas desvantagens como: custo de tempo (cerca de 1~2 min para imagens de 1000x1000) e seleção de cores menos saturadas.

Como pontos de melhoria levantados podemos citar a possibilidade de implementar algumas das técnicas de otimazação do Kmeans para extração de uma paleta de cores que favoreça pontos de destaque da imagem, assim como a possibilidade de inserir no pipeline etapas que permitam extrair features da imagem para predição dos parâmetros a serem utilizados na quantidade de cores da paleta (N) e nos parâmetros de Nº de Segmentos e Compacidade do SLIC.

## Participantes

Gabriel Van Loon

Giovani Decico Lucafó

Tamiris Fernandes Tinelli

Projeto para Disciplina de Processamento de Imagens (2021.1)
