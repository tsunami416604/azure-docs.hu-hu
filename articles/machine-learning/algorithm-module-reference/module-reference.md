---
title: Algoritmusokra és modulokra vonatkozó referenciák
description: Ismerje meg az Azure Machine Learning designerben elérhető modulokat (előzetes verzió)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 02/22/2020
ms.openlocfilehash: 7fcfc7c15f94d7b569629e53534d731126172260
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399065"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer-preview"></a>Algoritmus & modulhivatkozás az Azure Machine Learning tervezőjéhez (előzetes verzió)

Ez a referenciatartalom biztosítja a technikai hátteret az Azure Machine Learning designerben elérhető gépi tanulási algoritmusok és modulok technikai hátterét.

Minden modul egy kódkészletet jelöl, amely egymástól függetlenül futtatható, és a szükséges bemenetek esetén gépi tanulási feladatot hajt végre. Egy modul tartalmazhat egy adott algoritmust, vagy a gépi tanulásban fontos feladatot, például hiányzó értékcsere vagy statisztikai elemzés.

Az algoritmusok kiválasztásával kapcsolatban további 
* [Algoritmusok kiválasztása](../how-to-select-algorithms.md)
* [Azure Machine Learning algoritmus cheat sheet](../algorithm-cheat-sheet.md)

> [!TIP]
> A tervező bármely folyamatában információt kaphat egy adott modulról. Jelölje ki a modult, majd a **további súgóhivatkozást** a **Gyorssúgó** ablaktáblában.

## <a name="data-preparation-modules"></a>Adat-előkészítési modulok


| Funkció | Leírás | Modul |
| --- |--- | --- |
| Adatbemenet és -kimenet | A felhőforrásokból származó adatok áthelyezése a folyamatba. Írja az eredményeket vagy köztes adatokat az Azure Storage-ba, egy SQL-adatbázisba vagy egy Hive-be, miközben egy folyamatot futtat, vagy használja a felhőalapú tárhelyet a folyamatok közötti adatcseréhez.  | [Manuális adatbevitel](enter-data-manually.md) <br/> [Adatok exportálása](export-data.md) <br/> [Adatok importálása](import-data.md) |
| Adatátalakítás | A gépi tanulásra vonatkozó egyedi adatokon végzett műveletek, például az adatok normalizálása vagy kibinningje, a méretcsökkentés és az adatok különböző fájlformátumok közötti átalakítása.| [Oszlopok hozzáadása](add-columns.md) <br/> [Sorok hozzáadása](add-rows.md) <br/> [Matematikai művelet alkalmazása](apply-math-operation.md) <br/> [SQL-átalakítás alkalmazása](apply-sql-transformation.md) <br/> [Hiányzó adatok törlése](clean-missing-data.md) <br/> [Értékek levágása](clip-values.md) <br/> [Konvertálás CSV formátumba](convert-to-csv.md) <br/> [Átalakítás adathalmazzá](convert-to-dataset.md) <br/> [Átalakítás mutatóértékekké](convert-to-indicator-values.md) <br/> [Metaadatok szerkesztése](edit-metadata.md) <br/> [Adatok összekapcsolása](join-data.md) <br/> [Adatok normalizálása](normalize-data.md) <br/> [Partíció és minta](partition-and-sample.md)  <br/> [Ismétlődő sorok eltávolítása](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Oszlopok kiválasztása átalakításhoz](select-columns-transform.md) <br/> [Oszlopok kiválasztása az adathalmazban](select-columns-in-dataset.md) <br/> [Adatok felosztása](split-data.md) |
| Szolgáltatás kijelölése | Válassza ki a releváns, hasznos funkciók egy részét, amelyet egy analitikai modell létrehozásához használni szeretne. | [Szűrőalapú szolgáltatásválasztás](filter-based-feature-selection.md) <br/> [A permutáció funkció fontossága](permutation-feature-importance.md) |
| Statisztikai függvények | Az adatelemzéssel kapcsolatos statisztikai módszerek széles skáláját biztosítja. | [Adatok összegzése](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Gépi tanulási algoritmusok

| Funkció | Leírás | Modul |
| --- |--- | --- |
| Regresszió | Érték előrejelzése. | [Súlyozott döntési fa típusú regresszió](boosted-decision-tree-regression.md) <br/> [Döntési erdő típusú regresszió](decision-forest-regression.md) <br/> [Lineáris regresszió](linear-regression.md)  <br/> [Neurális hálózat típusú regresszió](neural-network-regression.md)  <br/> |
| Fürtözés | Csoportosítsa az adatokat.| [K-közép csoportosítás](k-means-clustering.md)
| Osztályozás | Megjósolni egy osztályt.  Bináris (kétosztályos) vagy többosztályos algoritmusok közül választhat.| [Többosztályos súlyozott döntési fa](multiclass-boosted-decision-tree.md) <br/> [Többosztályos döntési erdő](multiclass-decision-forest.md) <br/> [Többosztályos logisztikai regresszió](multiclass-logistic-regression.md)  <br/> [Többosztályos neurális hálózat](multiclass-neural-network.md) <br/> [Egy ellenfél minden többosztályos](one-vs-all-multiclass.md) <br/> [Kétosztályos átlagolt perceptron](two-class-averaged-perceptron.md) <br/>  [Kétosztályos súlyozott döntési fa](two-class-boosted-decision-tree.md)  <br/> [Kétosztályos döntési erdő](two-class-decision-forest.md) <br/>  [Kétosztályos logisztikai regresszió](two-class-logistic-regression.md) <br/> [Kétosztályos neurális hálózat](two-class-neural-network.md) <br/> [Kétosztályos támogató vektorgép](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Modulok modellek készítéséhez és értékeléséhez

| Funkció | Leírás | Modul |
| --- |--- | --- |
| A modell betanítása | Futtassa az adatokat az algoritmuson keresztül. |  [Csoportosítási modell betanítása](train-clustering-model.md) <br/> [Vonat modell](train-model.md)  <br/> [Modell-hiperparaméterek hangolása](tune-model-hyperparameters.md) |
| Modellpontozás és -értékelés | Mérje meg a betanított modell pontosságát. | [Átalakítás alkalmazása](apply-transformation.md) <br/> [Adatok hozzárendelése fürtökhöz](assign-data-to-clusters.md) <br/> [Modell keresztellenőrzése](cross-validate-model.md) <br/> [Modell kiértékelése](evaluate-model.md) <br/> [Pontszám modell](score-model.md) |
| Python nyelv | Írja meg a kódot, és ágyazza be egy modulba, hogy integrálja a Pythont a folyamattal. | [Python-modell létrehozása](create-python-model.md) <br/> [Python-szkript futtatása](execute-python-script.md) |
| R nyelv | Írja meg a kódot, és ágyazza be egy modulba, hogy integrálja az R-t a folyamattal. | [R-szkript végrehajtása](execute-r-script.md) |
| Szövegelemzés | Speciális számítási eszközöket biztosít a strukturált és strukturálatlan szövegekkel való munkához. | [N-Gram-funkciók kinyerése szövegből](extract-n-gram-features-from-text.md) <br/> [Funkciókivonatolás](feature-hashing.md) <br/> [Szöveg előfeldolgozása](preprocess-text.md) <br/> [Látens Dirichlet-allokáció](latent-dirichlet-allocation.md) |
| Ajánlás | Javaslati modellek létrehozása. | [Ajánló értékelése](evaluate-recommender.md) <br/> [SVD-ajánló pontozása](score-svd-recommender.md) <br/> [SVD-ajánló betanítása](train-SVD-recommender.md) |
| Rendellenességek észlelése | Anomáliadetektálási modellek létrehozása. | [PCA-alapú anomáliadetektálás](pca-based-anomaly-detection.md) <br/> [Anomáliadetektálási modell betanítása](train-anomaly-detection-model.md) |


## <a name="error-messages"></a>Hibaüzenetek

Ismerje meg a [hibaüzenetek és a kivétel kódok](designer-error-codes.md) előfordulhat, hogy az Azure Machine Learning tervezőmodulok használatával találkozhat.

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: Építsen modellt tervezőben az automatikus árak előrejelzéséhez](../tutorial-designer-automobile-price-train-score.md)
