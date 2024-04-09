# Détection et Identification Automatiques de Pièces de Monnaie

## 1.Introduction

La détection et l'identification automatiques de pièces de monnaie constituent un défi important dans de nombreuses applications, telles que la gestion financière automatisée et le contrôle de la production industrielle. Dans le cadre de ce projet, nous avons entrepris de concevoir un système capable de compter le montant total en euros représenté par des pièces à partir d'une image donnée en entrée.

Pour atteindre cet objectif, nous avons exploré deux approches différentes : une approche basée sur la segmentation régionale avec une méthode de binarisation d'Otsu et une approche basée sur la détection de contours à l'aide de la transformée de Hough pour les cercles. Ces deux méthodes ont été mises en œuvre et évaluées pour leur capacité à détecter et à identifier les pièces de monnaie en euros dans des images.

Dans cette étude, nous présenterons en détail ces deux approches, en décrivant leur mise en œuvre, leurs performances et leurs limites. Nous discuterons également des implications de nos résultats et des pistes d'amélioration possibles pour des systèmes futurs de détection et d'identification de pièces de monnaie.

## 2.Base de données

Les images ont été prises de différentes manières pour refléter les conditions réelles auxquelles le système pourrait être confronté. Certaines ne contiennent qu'une seule pièce de monnaie, tandis que d'autres en contiennent plusieurs (collés entre eux ou séparés), ce qui permet de tester la capacité du système à gérer des situations où les pièces se chevauchent ou sont très proches les unes des autres.

De plus, les images ont été prises dans des conditions d'éclairage variables, ce qui représente un défi pour la robustesse du système face aux variations de luminosité. Nous avons constaté cela notamment avec les images prises avec flash, ce qui a créé des ombres indésirables, du glare ou des reflets sur les pièces, affectant la qualité de l'image et la précision de la détection.

Enfin, le fond de certaines images n'est pas uniforme, ce qui simule des situations où les pièces sont posées sur des surfaces avec des motifs ou des textures diverses.

En conséquence, certains prétraitements sont nécessaires pour corriger ces imperfections et améliorer la qualité des images avant l'analyse par le système de détection.
<center>

| Image 1 | Image 2 |
|---------|---------|
|![01](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/d01ec29d-9df2-41f7-9340-7484b6be00c8) | ![02](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/50ca245a-a20d-4e17-9558-53a8ef2aece7)|
| Image 3 | Image 4 |
| ![03](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/9bda39ca-c4b0-4160-b710-bd29eb7bec0a) | ![04](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/4df7d170-9e68-4fa4-8d45-8eb6eb56e278)|

</center>

## 3.Méthodes d'Évaluation

### 3.1 Évaluation pour les méthodologies de comptage de pièces

L'évaluation des méthodologies de comptage de pièces peut être réalisée en utilisant plusieurs mesures de performance. Les mesures utilisées incluent :

#### 3.1.1. Erreur Absolue Moyenne (MAE)

La MAE mesure la moyenne des écarts absolus entre les valeurs prédites (le nombre de pièces détecté pour une seule image) et les valeurs réelles (le vrai nombre de pièces pour une seule image).

Formule :
```MAE = (1 / n) * Σ|i=1 à n|(|y_pred[i] - y_true[i]|)```

#### 3.1.2. Erreur Quadratique Moyenne (MSE)

Le MSE est la moyenne des carrés des écarts entre les valeurs prédites (le nombre de pièces détecté pour une seule image) et les valeurs réelles (le vrai nombre de pièces pour une seule image).

Formule :
```MSE = (1 / n) * Σ|i=1 à n|(y_pred[i] - y_true[i])²```

#### 3.1.3. Racine de l'Erreur Quadratique Moyenne (RMSE)

Le RMSE est la racine carrée du MSE, ce qui donne une mesure de l'écart moyen entre les valeurs prédites (le nombre de pièces détecté pour une seule image) et les valeurs réelles (le vrai nombre de pièces pour une seule image).

Formule :
```RMSE = √MSE```

#### 3.1.4. Précision (ACC)

La précision est trouvé en calculant la proportion des images avec le nombre de pièces correctement prédit sur le nombre total d'images.

Formule :
```ACC = (Nombre d'images avec le nombre de pièces correctement prédit) / (Nombre nombre total d'image)```

### 3.2 Méthodologie d'évaluation de la somme en euros

L'évaluation de la somme en euros comptée peut également être effectuée à l'aide de plusieurs mesures de performance similaires à celles utilisées pour le comptage de pièces:

#### 3.2.1. Erreur Absolue Moyenne (MAE)

Formule :
```MAE = (1 / n) * Σ|i=1 à n|(|somme_pred[i] - somme_true[i]|)```

#### 3.2.2. Erreur Quadratique Moyenne (MSE)

Formule :
```MSE = (1 / n) * Σ|i=1 à n|(somme_pred[i] - somme_true[i])²```

#### 3.2.3. Racine de l'Erreur Quadratique Moyenne (RMSE)

Formule :
```RMSE = √MSE```


## 4. Méthodes de comptage des pièces de monnaie

Dans cette étude, nous avons adopté deux approches distinctes :

  ### 4.1. Approche segmentation régionale avec la technique de binarisation d'Otsu

Pour commencer, nos images sont soumises à un processus de prétraitement pour améliorer leur qualité. 
L'image est chargée, puis les reflets de flash potentiellement présents sont réduits pour améliorer la qualité de l'image. Ensuite, l'image est redimensionnée en conservant son rapport d'aspect pour garantir une manipulation appropriée lors des étapes suivantes (afin de prévenir toute altération de la forme de la pièce).

Après cela, la luminosité de l'image est ajustée (en utilisant LUT Tables) pour corriger les variations d'éclairage et garantir une visualisation optimale. En parallèle, les détails indésirables de l'image sont réduits à l'aide de techniques de flou gaussien.

Une fois ces étapes de prétraitement effectuées, l'image est convertie en niveaux de gris. Ensuite, l'algorithme d'Otsu est appliqué pour déterminer automatiquement le seuil optimal de binarisation. Cela permet de segmenter l'image en deux classes distinctes : les pixels représentant les pièces de monnaie et ceux représentant l'arrière-plan.

Pour garantir une binarisation correcte, la couleur majoritaire de l'image seuillée est vérifiée. Si nécessaire, l'image est inversée pour que le fond soit noir, facilitant ainsi la détection des pièces de monnaie dans des conditions variées.

Après la binarisation, l'image est soumise à une détection de contours à l'aide de l'opérateur de Canny pour identifier les bords distincts des objets présents dans l'image. La détection précise des contours est essentielle pour délimiter correctement les pièces dans l'image. Une fois les contours détectés, des ellipses sont ajustées aux contours des pièces de monnaie à l'aide de la fonction ```fitEllipse```. Et ce, pour modéliser les formes des pièces dans l'image, permettant ainsi de les caractériser et de les identifier efficacement.

Certains critères de filtrage sont ensuite appliqués pour sélectionner les ellipses qui correspondent le mieux aux pièces de monnaie (tels que la taille minimale des ellipses et le rapport d'inertie, qui permettent de distinguer les pièces des autres objets).

Les pièces de monnaie détectées sont filtrées en fonction de leur taille de leur forme et de leur positionnement respective, éliminant ainsi les faux positifs et améliorant la précision de la détection au maximum.

**Remarque:** 
D'autres méthode et prétraitement en étaient testées tel que l'égalisation d'histogramme adaptatif CLAHE, la dilatation circulaire  dans le but de renforcer la détection des contours des pièces de monnaie...etc, mais on n'a gardé que ceux qui donnent le meilleur résultat.

Vous pouvez trouver le code dans le fichier [script.py](script.py).
  ### 4.2. Approche basée détection de contours avec la transformée de Hough pour les cercles

Tout d'abord, l'image est redimensionnée pour garantir qu'elle ait une taille maximale de 500 pixels dans la plupart des cas. Cependant, si l'image semble être en mode plein écran 16:9, elle est redimensionnée à une taille maximale de 800 pixels. Cette étape est cruciale pour éviter la compression de l'image, ce qui pourrait compliquer la détection des cercles. Garder des images originales avec une taille initiale grande peut conduire à des erreurs de détection dues à une complexité accrue dans le traitement et à une augmentation des temps de calcul, surtout si les détails superflus ne sont pas pertinents pour la tâche de détection des cercles.
Il est également important de noter, afin de garantir une certaine uniformité dans les données, si la forme initiale de l'image est égale à ```(3024, 4032, 3)```, alors l'image est inversée à l'aide de la fonction ```flip_image``` avant de passer au redimensionnement.

L'image redimensionnée est convertie en niveaux de gris pour simplifier le traitement. Ensuite, un flou gaussien est appliqué pour réduire le bruit et améliorer la qualité de l'image.

La fonction ```cv2.HoughCircle```s est utilisée pour détecter les cercles dans l'image en niveaux de gris. Des paramètres spécifiques sont ajustés pour contrôler la sensibilité de la détection et les tailles de cercles à rechercher. Les coordonnées des cercles détectés sont ensuite renvoyées après avoir été ajustées pour correspondre à l'échelle de l'image d'origine.

Les coordonnées des cercles détectés, représentant les centres des pièces de monnaie, sont récupérées et utilisées pour estimer le nombre de pièces dans l'image.

Les coordonnées et les rayons retournés par la fonction ```cv2.HoughCircle``` sont ensuite utilisés pour délimiter les pièces de monnaie.

Vous pouvez trouver le code dans le fichier [script.py](script.py).

## 5. Résultats expérimentaux 
### 5.1. Approche segmentation régionale avec la technique de binarisation d'Otsu
Après avoir appliqué l'algorithme d'Otsu pour la binarisation de l'image, notre méthode a obtenu une précision de détection de pièces de monnaie de 60,8%. Cette précision a été évaluée en comparant les résultats de notre algorithme avec les annotations manuelles fournies dans le fichier CSV. Voici les résultats de l'évaluation obtenus :


| Métrique                  | Mean Absolute Error (MAE) | Mean Squared Error (MSE) | Root Mean Squared Error (RMSE) | Accuracy  |
|---------------------------|----------------------------|---------------------------|--------------------------------|-----------|
| Valeur                    | 2.40                       | 34.42                     | 5.87                           | 63.04 %   |


Ces valeurs fournissent une mesure quantitative de la performance de notre méthode de détection de pièces de monnaie après l'application de l'algorithme d'Otsu.

Ci-dessous certains résultats correctement détectés.

| Image 1 | Image 2 |
|---------|---------|
| ![Sans titre](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/d5d184d4-c6e3-4e60-bae0-e77d967e04e3) | ![Sans titre-1](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/1ac28563-5a3b-4177-ab7b-24867fd681f4) |
| Image 3 | Image 4 |
| ![Sans titre](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/5548b278-eb47-4c1e-a95f-39ff2d59160c) | ![Sans titre-1](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/897a1ff1-8225-4b8e-a64b-13da152a3c4b) |

Malheureusement, certaines pièces n’ont pas été correctement détectées. (Voir les figures ci-dessous).

| Image 1 | Image 2 |
|---------|---------|
| ![Sans titre](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/63b04856-344c-49ac-a998-386d82b3d8d1) | ![Sans titre-1](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/d8c20379-452a-41c5-ab6b-31357ae20a82)|


Il convient de noter que malgré cette précision relativement modérée, notre approche a démontré sa capacité à détecter les pièces de monnaie dans des conditions d'éclairage variables.

### 5.2. Approche basée détection de contours avec la transformée de Hough pour les cercles

Après avoir appliqué la transformée de Hough circulaire pour détecter les cercles dans les images, notre méthode a obtenu une précision de détection des pièces de monnaie de 83%. Cette précision a été évaluée en comparant les résultats de notre algorithme avec les annotations manuelles fournies dans le fichier CSV. Les résultats de l'évaluation montrent des performances remarquables, notamment un Mean Absolute Error (MAE) de seulement 0.55, mettant en évidence la précision de la deuxième méthode comparé à la première.

| Métrique                  | Mean Absolute Error (MAE)  | Mean Squared Error (MSE)  | Root Mean Squared Error (RMSE) | Accuracy  |
|---------------------------|----------------------------|---------------------------|--------------------------------|-----------|
| Valeur                    | 0.55                       | 3.90                      | 1.98                           | 83.70 %   |



| Image 1 | Image 2 |
|---------|---------|
| ![Sans titre](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/0278d1bd-882c-4730-ab7b-801811246dd9) | ![Sans titre-1](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/dd7d0b1c-5ac8-4043-ac04-977a9fd68e04)|
| Image 3 | Image 4 |
| ![Sans titre](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/b0e6df0a-c114-41ec-8357-9d7efa124bda) | ![Sans titre-1](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/479bd4b1-a661-41ce-a623-30a8c41ee82d) |

Malgré les bons résultats, il est à noter que certaines erreurs de detection ont été identifiées lors de la comparaison des prédictions avec les valeurs réelles, soulignant ainsi la nécessité d'une analyse approfondie des cas où le système a rencontré des difficultés. Ci-dessous, nous présentons quelques exemples d'images avec erreurs.


| Image 1 | Image 2 | Image 3 |
|---------|---------|---------|
| ![Sans titre-1](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/19014728-3c0f-4aaf-9703-5283cb3194b6) | ![Sans titre-1](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/9883a3e8-8b2f-443b-a8b6-94eb66f49902) |![Sans titre](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/fb8263a6-51cc-4c94-9e18-4b36a13f4e56) |

## 6.Etude comparative des méthodes	
| Méthode                                | Précision | MAE  | MSE  | RMSE  | Avantages                                                                                     | Limitations                                                                                                               |
|----------------------------------------|-----------|------|------|------|-----------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Approche basée sur la méthode d'Otsu   | 63.04%     | 2.402| 34.424| 5.867 | - Méthode rapide et efficace pour la binarisation des images, ce qui permet une séparation claire entre les pièces de monnaie et l'arrière-plan. - Implémentation simple avec détection des contours. | - Précision de 63.04% indique une performance relativement faible. - Méthode sensible aux variations de contraste et d'éclairage dans les images. |
| Approche basée sur la transformée de Hough circulaire | 83.7% | 0.554| 3.902| 1.975| - Précision significativement plus élevée de 83.7%. - La transformée de Hough circulaire est robuste aux variations de contraste et d'éclairage, ce qui améliore la fiabilité de la détection des pièces de monnaie. | - La méthode peut nécessiter des ajustements de paramètres pour optimiser la détection des cercles dans les images. |

La méthode basée sur la transformée de Hough circulaire surpasse clairement la méthode basée sur Otsu en termes de précision de détection des pièces de monnaie avec 83% d’accuracy. Ce qui en fait un choix plus favorable pour les applications nécessitant une détection précise des pièces de monnaie.


## 6.Méthode de reconnaissance de piece afin de calculer la somme en euros

Notre méthode pour compter la somme totale de pièces dans l'image consiste à utiliser la fonction calculate_amount. Cette fonction prend en entrée une liste de cercles détectés dans l'image, obtenue après la réalisation de la détection de pièces à l'aide de la méthode 2 (basée sur HoughCircles) présentée précédemment, ainsi que la classe de la plus grande pièce de monnaie, extraite du fichier CSV contenant des annotations manuelles.

Pour chaque cercle détecté, la fonction calcule d'abord le rapport entre le rayon du cercle et le rayon de la plus grande pièce de monnaie. En utilisant ce rapport, elle détermine ensuite la classe de pièce de monnaie la plus proche en taille pour chaque cercle détecté. Cette étape est réalisée en comparant les rayons des pièces de monnaie réelles avec le rayon estimé de chaque cercle détecté.

Enfin, la fonction totalise la valeur de chaque pièce de monnaie détectée en fonction de sa classe et retourne le montant total en euros arrondi à deux décimales.


| Image 1 | Image 2 | Image 3 |
|---------|---------|---------|
| ![Sans titre](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/c0a0e9a6-7c91-4a96-a0e7-9076facbf9e1) | ![Sans titre-1](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/99ba81ec-1be1-4cb8-9f0c-5f0c15f19259)| ![Sans titre-1](https://github.com/RB-Younes/Detection-and-identification-of-euro-coins/assets/108153787/2f75dca6-5d7a-4134-98df-567ba7293c0d) |


## 7.Résultats expérimentaux 

Pour évaluer notre méthode, nous avons exclu les images ne contenant qu'une seule pièce afin de ne pas biaiser les résultats, car dans ces cas, la pièce unique est toujours identifiée comme la plus grande. Nous avons donc testé notre méthode sur les images contenant plusieurs pièces, soit un total de 64 images sur les 92 initiales. Voici les résultats obtenus :

| Métrique                  | Mean Absolute Error (MAE)  | Mean Squared Error (MSE)  | Root Mean Squared Error (RMSE) |
|---------------------------|----------------------------|---------------------------|--------------------------------|
| Valeur                    | 0.76                       |  1.50                     | 1.22                           |

un MAE de 0.76 signifie en moyenne une erreur absolue de 0.76 unités dans l'estimation de la valeur des pièces de monnaie.

Ces résultats démontrent la précision de notre méthode dans la détection et l'estimation des valeurs des pièces de monnaie dans des images contenant plusieurs pièces.

## 8. Limitations et perspectives futures


# Réalisé par 
* ABED Nada-Fatima Zohra
* REBAI Mohamed Younes
* BENMEHREZ Dima Sabrine

