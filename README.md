<h1 align="center">L’espérance de vie dans le monde 
et ses différents facteurs
</h1>

## Introduction
L'espérance de vie n'est pas seulement un chiffre ou même un âge qu'on peut espérer atteindre en fonction de notre position géographique. C'est l'un des indicateurs les plus fiables et parlant en ce qui concerne les conditions de vie au sein d'un pays. 

## Choix des sous sujets et perspective pour le traitement :
Les facteurs influant un sujet aussi complexe que l'espérance de vie étant nombreux, nous avons fait le choix de traiter le sujet à travers des sous questions spécifiques auxquels nous pourrons répondre en détails à travers des analyses profondes. 
Le traitement de ses sous questions nous mènera à la déduction d'une conclusion globale plus générale. Cependant, nous nous sommes fixé une plage temporelle commune, les analyses porteront majoritairement sur la période de 2000 à 2021 mais certaines études nécessiteront une plage plus large (1950 - 2021).

## Les sous questions :

### 1 - Qu'est ce qui creuse l'écart d’espérance de vie entre les hommes et les femmes ?
#### Introduction :
Il est connu de tous que les femmes vivent plus longtemps que les hommes. Il est également su par tous que cela résulte de multiples facteurs, les principaux étant biologiques et comportements. Mais en effectuant mes recherches, une donnée atypique à attirer mon attention : l'écart entre l'espérance de vie des hommes et des femmes est très creusé en fonction des pays.
Une analyse simpliste de la question suggèrerait que cela concernerait les pays appartenant au tiers monde ou les pays en guerre (les hommes seraient donc les plus exposés).
Mais en consultant la liste issue du groupe mondiale, nous constatons rapidement que la réponse est en réalité complexe. 

#### Liste des pays par espérance de vie avec différent tri :

<p align="">
  <img width="300" height="200" alt="image1" src="https://github.com/user-attachments/assets/ba0b5c2d-8bf5-4842-be1c-396d617c066f" />
  <img width="300" height="200" alt="image2" src="https://github.com/user-attachments/assets/baf2a2dd-3d6b-407e-807b-bde546352dc1" />
</p> 
<i> Source : World Bank Group</i>
La première images est le tri de l'espérance de vie dasn l'ordre décroissant et la seconde dans l'ordre croissant

On constate donc que cela ne dépend pas simplement du niveau de richesses du pays car nous avons là plusieurs exemples démontrant le contraire. Les niveaux de vie des populations sélectionné étant extrêmement hétérogène.
C’est pourquoi c’est un axe qui nous semble intéressante.

<i> La question sera traitée par Marie-AnneRandria </i>

## 1 - Qu’est ce qui explique le faussé de l’espérance de vie entre l’Afrique et le reste du monde ? (1950 - 2020)
### 1) Qu’est ce qui a ralenti l’Afrique dans son évolution ? 
### 2) Quel monde sépare l’Afrique du Nord et l’Afrique du Sud ? 

#### Introduction :
En visionnant les cartes du monde de l’espérance de vie par état (à différente période), le constat est sans appel : l’Afrique a une évolution beaucoup plus singulière que les autres continents. Une évolution plus lente (1), une évolution hétérogène (2).

#### En effet, voici des cartes du monde de l’espérance de vie par état :
<p align="">
  <img width="300" height="200" alt="image1" src="https://github.com/user-attachments/assets/e1cd7aca-1c5e-406d-909d-9168eaf347e5" />
  <img width="300" height="200" alt="image2" src="https://github.com/user-attachments/assets/c637c526-db95-4ca3-b82d-861e5d1e4c36" />
</p> 
<p align="">
  <img width="300" height="200" alt="image1" src="https://github.com/user-attachments/assets/68937f57-49f1-4aa6-9aa2-0ce174c1af2c" />
  <img width="300" height="200" alt="image2" src="https://github.com/user-attachments/assets/571d19b5-5ad4-4445-88ca-6f437c755de3" />
</p> 

<i> Source : World Population Prospects, Population Division, Department of Economic and Social Affairs, United Nations. </i>

En 20 ans (entre 2000 et 2020), on constate une évolution globale positive de 5-10 de l’espérance de vie dans le monde.
Cependant, si on recule davantage dans le temps, on constate que l’Afrique entière avait une espérance de vie de moins de 50 ans. Dans ce cas, on pourrait s'interroger uniquement sur l'écart entre l'Afrique et le reste du monde (1). Mais à partir de 1980, l’Afrique du Nord commence a mieux évolué (d’où la sous-question n°2).
L’Afrique a presque toujours été le continent avec l’espérance de vie la plus basse dans le monde. Et dans une étude comme celle-ci, il semble inévitable qu'il faille s'interroger sur les raisons.

<i> La question sera traitée par Ju-456 </i>

### 3 - 
#### Introduction :

<i> La question sera traitée par ManeleHssini </i>

### 3 - Quels sont les facteurs qui influencent le plus l'espérance de vie, et peut-on la prédire ?

#### Introduction :

Les deux premières questions regardent des écarts bien précis : entre les hommes et les
femmes, et entre l'Afrique et le reste du monde. Moi, j'ai eu envie de prendre le problème
dans l'autre sens. Au lieu de partir d'un facteur en particulier, je me suis posé la
question : si on met tous les facteurs ensemble, lesquels comptent vraiment ?

On entend souvent que tout est une question de richesse du pays, ou d'accès aux soins, ou
encore de vaccination. Mais au fond ce sont surtout des intuitions. Ce que je veux faire ici,
c'est laisser les données répondre à ma place plutôt que de deviner.

Pour ça je m'appuie sur le jeu de données "Life Expectancy (WHO)" de Kaggle. Il est
intéressant parce qu'il rassemble, pour 193 pays sur la période 2000 - 2015, une vingtaine
de facteurs très variés : le statut du pays (développé / en développement), le PIB, les
années de scolarisation, la consommation d'alcool, l'IMC moyen, les dépenses de santé, la
couverture vaccinale (hépatite B, polio, diphtérie), le VIH/SIDA, ou encore les taux de
mortalité. C'est assez complet pour pouvoir comparer les facteurs entre eux et voir
lesquels pèsent vraiment.

Ma démarche se fait en deux temps :

1. Une partie exploration : je regarde les corrélations entre chaque facteur et l'espérance
   de vie, et je nettoie les données (il y a pas mal de valeurs manquantes à gérer).
2. Une partie modélisation : j'entraîne un modèle pour prédire l'espérance de vie à partir
   des facteurs. Je commence par une régression linéaire, simple à interpréter, puis un
   random forest qui est plus performant et qui permet surtout de sortir un classement des
   facteurs les plus importants.

Le but n'est pas seulement d'avoir un modèle qui prédit bien, mais de comprendre pourquoi il
prédit comme ça : quels sont les 3-4 facteurs qui pèsent le plus, et est-ce que ça confirme
ou non les idées reçues.

La question sera traitée par {ton pseudo GitHub}




## Liens vers les informations du jeu de donnée
Life expectancy : https://www.kaggle.com/datasets/kumarajarshi/life-expectancy-who

All Natural disasters 1900-2021 : https://www.kaggle.com/datasets/brsdincer/all-natural-disasters-19002021-eosdis

Demographic Indicators(1900 - 2100) : https://population.un.org/wpp/downloads?folder=Standard%20Projections&group=CSV%20format

Conflict dataset : 

<img width="862" height="562" alt="image" src="https://github.com/user-attachments/assets/4f37dafe-801e-4cdc-a1ca-b0ddbabad51e" />
