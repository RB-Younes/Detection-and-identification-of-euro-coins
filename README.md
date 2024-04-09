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



