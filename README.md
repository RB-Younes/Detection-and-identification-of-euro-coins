# Détection et Identification Automatiques de Pièces de Monnaie

## Introduction

La détection et l'identification automatiques de pièces de monnaie constituent un défi important dans de nombreuses applications, telles que la gestion financière automatisée et le contrôle de la production industrielle. Dans le cadre de ce projet, nous avons entrepris de concevoir un système capable de compter le montant total en euros représenté par des pièces à partir d'une image donnée en entrée.

Pour atteindre cet objectif, nous avons exploré deux approches différentes : une approche basée sur la segmentation régionale avec une méthode de binarisation d'Otsu et une approche basée sur la détection de contours à l'aide de la transformée de Hough pour les cercles. Ces deux méthodes ont été mises en œuvre et évaluées pour leur capacité à détecter et à identifier les pièces de monnaie en euros dans des images.

Dans cette étude, nous présenterons en détail ces deux approches, en décrivant leur mise en œuvre, leurs performances et leurs limites. Nous discuterons également des implications de nos résultats et des pistes d'amélioration possibles pour des systèmes futurs de détection et d'identification de pièces de monnaie.

## Base de données

Les images ont été prises de différentes manières pour refléter les conditions réelles auxquelles le système pourrait être confronté. Certaines ne contiennent qu'une seule pièce de monnaie, tandis que d'autres en contiennent plusieurs (collés entre eux ou séparés), ce qui permet de tester la capacité du système à gérer des situations où les pièces se chevauchent ou sont très proches les unes des autres.

De plus, les images ont été prises dans des conditions d'éclairage variables, ce qui représente un défi pour la robustesse du système face aux variations de luminosité. Nous avons constaté cela notamment avec les images prises avec flash, ce qui a créé des ombres indésirables, du glare ou des reflets sur les pièces, affectant la qualité de l'image et la précision de la détection.

Enfin, le fond de certaines images n'est pas uniforme, ce qui simule des situations où les pièces sont posées sur des surfaces avec des motifs ou des textures diverses.

En conséquence, certains prétraitements sont nécessaires pour corriger ces imperfections et améliorer la qualité des images avant l'analyse par le système de détection.
<center>

| Image 1 | Image 2 |
|---------|---------|
| ![Description de l'image 1](chemin/vers/image1.jpg) | ![Description de l'image 2](chemin/vers/image2.jpg) |
| Image 3 | Image 4 |
| ![Description de l'image 3](chemin/vers/image3.jpg) | ![Description de l'image 4](chemin/vers/image4.jpg) |

</center>

## Méthodes d'Évaluation

### Évaluation pour les méthodologies de comptage de pièces

L'évaluation des méthodologies de comptage de pièces peut être réalisée en utilisant plusieurs mesures de performance. Les mesures utilisées incluent :

#### 1. Erreur Absolue Moyenne (MAE)

La MAE mesure la moyenne des écarts absolus entre les valeurs prédites (le nombre de pièces détecté pour une seule image) et les valeurs réelles (le vrai nombre de pièces pour une seule image).

Formule :
```MAE = (1 / n) * Σ|i=1 à n|(|y_pred[i] - y_true[i]|)```

#### 2. Erreur Quadratique Moyenne (MSE)

Le MSE est la moyenne des carrés des écarts entre les valeurs prédites (le nombre de pièces détecté pour une seule image) et les valeurs réelles (le vrai nombre de pièces pour une seule image).

Formule :
```MSE = (1 / n) * Σ|i=1 à n|(y_pred[i] - y_true[i])²```

#### 3. Racine de l'Erreur Quadratique Moyenne (RMSE)

Le RMSE est la racine carrée du MSE, ce qui donne une mesure de l'écart moyen entre les valeurs prédites (le nombre de pièces détecté pour une seule image) et les valeurs réelles (le vrai nombre de pièces pour une seule image).

Formule :
```RMSE = √MSE```

#### 4. Précision (ACC)

La précision est trouvé en calculant la proportion des images avec le nombre de pièces correctement prédit sur le nombre total d'images.

Formule :
```ACC = (Nombre d'images avec le nombre de pièces correctement prédit) / (Nombre nombre total d'image)```

### Méthodologie d'évaluation de la somme en euros

L'évaluation de la somme en euros comptée peut également être effectuée à l'aide de plusieurs mesures de performance similaires à celles utilisées pour le comptage de pièces:

#### 1. Erreur Absolue Moyenne (MAE)

Formule :
```MAE = (1 / n) * Σ|i=1 à n|(|somme_pred[i] - somme_true[i]|)```

#### 2. Erreur Quadratique Moyenne (MSE)

Formule :
```MSE = (1 / n) * Σ|i=1 à n|(somme_pred[i] - somme_true[i])²```

#### 3. Racine de l'Erreur Quadratique Moyenne (RMSE)

Formule :
```RMSE = √MSE```


## Méthodologie

Dans cette étude, nous avons adopté deux approches distinctes :

  ### 1. Approche segmentation régionale avec la technique de binarisation d'Otsu

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
  ### 2. Approche basée détection de contours avec la transformée de Hough pour les cercles

Tout d'abord, l'image est redimensionnée pour garantir qu'elle ait une taille maximale de 500 pixels dans la plupart des cas. Cependant, si l'image semble être en mode plein écran 16:9, elle est redimensionnée à une taille maximale de 800 pixels. Cette étape est cruciale pour éviter la compression de l'image, ce qui pourrait compliquer la détection des cercles. Garder des images originales avec une taille initiale grande peut conduire à des erreurs de détection dues à une complexité accrue dans le traitement et à une augmentation des temps de calcul, surtout si les détails superflus ne sont pas pertinents pour la tâche de détection des cercles.
Il est également important de noter, afin de garantir une certaine uniformité dans les données, si la forme initiale de l'image est égale à ```(3024, 4032, 3)```, alors l'image est inversée à l'aide de la fonction ```flip_image``` avant de passer au redimensionnement.

L'image redimensionnée est convertie en niveaux de gris pour simplifier le traitement. Ensuite, un flou gaussien est appliqué pour réduire le bruit et améliorer la qualité de l'image.

La fonction ```cv2.HoughCircle```s est utilisée pour détecter les cercles dans l'image en niveaux de gris. Des paramètres spécifiques sont ajustés pour contrôler la sensibilité de la détection et les tailles de cercles à rechercher. Les coordonnées des cercles détectés sont ensuite renvoyées après avoir été ajustées pour correspondre à l'échelle de l'image d'origine.

Les coordonnées des cercles détectés, représentant les centres des pièces de monnaie, sont récupérées et utilisées pour estimer le nombre de pièces dans l'image.

Les coordonnées et les rayons retournés par la fonction ```cv2.HoughCircle``` sont ensuite utilisés pour délimiter les pièces de monnaie.

## Résultats expérimentaux 
### 1. Approche segmentation régionale avec la technique de binarisation d'Otsu
Après avoir appliqué l'algorithme d'Otsu pour la binarisation de l'image, notre méthode a obtenu une précision de détection de pièces de monnaie de 60,8%. Cette précision a été évaluée en comparant les résultats de notre algorithme avec les annotations manuelles fournies dans le fichier CSV. Voici les résultats de l'évaluation obtenus :

| Métrique                          | Valeur   |
|-----------------------------------|----------|
| Mean Absolute Error (MAE)         | 2.402    |
| Mean Squared Error (MSE)          | 34.424   |
| Root Mean Squared Error (RMSE)    | 5.867    |
| Accuracy                          | 63.043 % |

Ces valeurs fournissent une mesure quantitative de la performance de notre méthode de détection de pièces de monnaie après l'application de l'algorithme d'Otsu.


