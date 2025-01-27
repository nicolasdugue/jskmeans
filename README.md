# TP : Compression d'image par l'algorithme des k-moyennes

## Objectif

L'objectif de ce TP est d'utiliser Javascript pour compresser une image par l'algorithme des K-moyennes. La compression sera réalisée en réduisant le nombre de couleurs utilisées dans l'image à k couleurs représentatives. L'algorithme des k-moyennes est un algorithme d'intelligence artificielle qu'on ne vous demande pas d'implémenter, vous le trouverez implémenté en grande partie : vous allez en terminer quelques morceaux.

## Principe de l'algorithme des k-moyennes

L'algorithme des k-moyennes est une méthode de partitionnement de données qui vise à diviser n données (ici, les pixels) en k groupes (des pixels ayant des couleurs similaires).

Dans le contexte de la compression d'image :

1. Chaque pixel est considéré comme un point dans un espace à 3 dimensions (R,G,B)
2. L'algorithme cherche k couleurs représentatives (centres) qui minimisent la distance entre chaque pixel et la couleur représentative la plus proche
3. L'image compressée est obtenue en remplaçant chaque pixel par la couleur représentative la plus proche

## Travail à réaliser

Vous devez implémenter les fonctions suivantes qui sont essentielles au fonctionnement de l'algorithme :

### 1. Fonction getPixels(table)

Cette fonction doit extraire tous les pixels d'un tableau HTML et les organiser dans une structure de données à deux dimensions. Les rows de la table HTML vous fournissent les lignes de votre structure de données, quand les cells des rows vous fournissent les colonnes.

**Entrée** : Un élément table HTML contenant l'image  
**Sortie** : Un tableau 2D ([] dans lequel on push d'autres []) où chaque élément est un objet pixel avec ses composantes R,G,B

**Indications** :
- Parcourir les lignes et colonnes du tableau HTML (table.rows, table.rows[i].cells)
- Utiliser la fonction getPixel pour chaque cellule
- Retourner une structure de la forme : `[[pixel1_1, pixel1_2, ...], [pixel2_1, pixel2_2, ...], ...]`

### 2. Fonction getPixel(table, x, y)

Cette fonction doit extraire les composantes RGB d'un pixel spécifique du tableau. Vous pouvez vous appuyer sur la fonction *getRGBFromBackgroundColor* déjà implémentée.

**Entrée** :
- table : L'élément table HTML
- x, y : Les coordonnées du pixel à extraire

**Sortie** : Un objet de la forme `{r: valeurR, g: valeurG, b: valeurB}`

**Indications** :
- Accéder à la cellule du tableau aux coordonnées (x,y)
- Extraire la couleur de fond de la cellule
- Convertir la chaîne "rgb(r, g, b)" en objet avec les composantes séparées

### 3. Fonction selectRandomCenters(pixels, k)

Cette fonction doit sélectionner k pixels aléatoires qui serviront de couleurs représentatives.

**Entrée** :
- pixels : Le tableau 2d des pixels de l'image
- k : Le nombre de centres à sélectionner

**Sortie** : Un tableau de k pixels qui serviront de centres initiaux

**Indications** :
- Utiliser la fonction tirerUnEntier(max) fournie pour générer des coordonnées aléatoires
- Veiller à ne pas sélectionner deux fois le même pixel
- Retourner un tableau de k objets pixels

### 4. Fonction calculateDistance(pixel1, pixel2)

Cette fonction doit calculer la distance euclidienne entre deux pixels dans l'espace RGB.

**Entrée** : Deux objets pixels de la forme `{r: valeurR, g: valeurG, b: valeurB}`  
**Sortie** : La distance euclidienne entre les deux pixels

**Indications** :
- Calculer la différence pour chaque composante (R,G,B)
- Appliquer la formule de la distance euclidienne : √((R1-R2)² + (G1-G2)² + (B1-B2)²)

## Tests et validation

### 1. Tests unitaires des fonctions

#### Test de getPixels()
```javascript
// Créez d'abord un petit tableau test avec des couleurs connues
const tableTest = document.createElement('table');
tableTest.innerHTML = `
    <tr>
        <td style="background-color: rgb(255, 0, 0)"></td>
        <td style="background-color: rgb(0, 255, 0)"></td>
    </tr>
    <tr>
        <td style="background-color: rgb(0, 0, 255)"></td>
        <td style="background-color: rgb(255, 255, 255)"></td>
    </tr>
`;

// Testez la fonction getPixels
const pixels = getPixels(tableTest);
console.log('Test getPixels :');
console.log('Nombre de lignes :', pixels.length); // Devrait afficher 2
console.log('Nombre de colonnes :', pixels[0].length); // Devrait afficher 2
console.log('Premier pixel :', pixels[0][0]); // Devrait afficher {r: 255, g: 0, b: 0}
```

#### Test de getPixel()
```javascript
// Utilisez le même tableau test
console.log('Test getPixel :');
console.log('Pixel (0,0) :', getPixel(tableTest, 0, 0)); // Rouge
console.log('Pixel (0,1) :', getPixel(tableTest, 0, 1)); // Vert
console.log('Pixel (1,0) :', getPixel(tableTest, 1, 0)); // Bleu
console.log('Pixel (1,1) :', getPixel(tableTest, 1, 1)); // Blanc
```

#### Test de selectRandomCenters()
```javascript
// Créez une petite matrice de test
const pixelsTest = [
    [{r: 255, g: 0, b: 0}, {r: 0, g: 255, b: 0}],
    [{r: 0, g: 0, b: 255}, {r: 255, g: 255, b: 255}]
];

// Testez la sélection de centres avec différentes valeurs de k
console.log('Test selectRandomCenters :');
console.log('2 centres :', selectRandomCenters(pixelsTest, 2));
console.log('3 centres :', selectRandomCenters(pixelsTest, 3));

// Vérifiez que les centres sont bien dans l'image
const centers = selectRandomCenters(pixelsTest, 2);
centers.forEach((center, i) => {
    console.log(`Centre ${i} est-il valide :`,
        center.r >= 0 && center.r <= 255 &&
        center.g >= 0 && center.g <= 255 &&
        center.b >= 0 && center.b <= 255
    );
});
```

#### Test de calculateDistance()
```javascript
// Créez quelques pixels de test
const pixel1 = {r: 255, g: 0, b: 0}; // Rouge
const pixel2 = {r: 0, g: 255, b: 0}; // Vert
const pixel3 = {r: 255, g: 0, b: 0}; // Rouge (identique à pixel1)

console.log('Test calculateDistance :');
// Distance entre deux pixels identiques (devrait être 0)
console.log('Distance entre pixels identiques :', 
    calculateDistance(pixel1, pixel3));

// Distance entre rouge et vert
console.log('Distance entre rouge et vert :', 
    calculateDistance(pixel1, pixel2));

// Vérification de la symétrie
console.log('Test symétrie :',
    calculateDistance(pixel1, pixel2) === calculateDistance(pixel2, pixel1));
```

### 2. Validation visuelle

Pour valider visuellement le résultat :

1. Testez avec différentes valeurs de k (2, 5, 10, etc.)
2. Vérifiez que :
   - L'image compressée conserve les principales caractéristiques de l'image originale
   - Le nombre de couleurs uniques dans l'image compressée correspond à k
   - Les transitions entre les zones de couleurs sont cohérentes
