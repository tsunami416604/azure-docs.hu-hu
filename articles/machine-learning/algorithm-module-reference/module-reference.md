---
title: Algoritmusokra és modulokra vonatkozó referenciák
description: A Azure Machine Learning Designerben elérhető modulok ismertetése
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 12/17/2019
ms.openlocfilehash: c265c2386b31fd02a5aa635c343be5d3cb227722
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76312176"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Algoritmus & Azure Machine Learning Designer modul-hivatkozása

Ez a hivatkozási tartalom a Azure Machine Learning Designerben elérhető gépi tanulási algoritmusok és modulok technikai hátterét ismerteti.

Minden modul a kód egy készletét jelöli, amely egymástól függetlenül futtatható, és gépi tanulási feladatot hajt végre, a szükséges bemenetek miatt. A modulok tartalmazhatnak egy adott algoritmust, vagy elvégezhetnek egy olyan feladatot, amely fontos a gépi tanulásban, például a hiányzó érték cseréje vagy a statisztikai elemzés.

> [!TIP]
> A tervező bármely folyamatában információt kaphat egy adott modulról. Válassza ki a modult, majd kattintson a **további Súgó** hivatkozásra a **gyors Súgó** ablaktáblán.

## <a name="modules"></a>Modulok

A modulok a funkciók szerint vannak rendszerezve:

| Funkció | Leírás | Modul |
| --- |--- | --- |
|  | **Adatelőkészítés**: | |
| Adatbemenet és -kimenet | A Felhőbeli forrásokból származó adatok áthelyezése a folyamatba. Megírhatja az eredményeket vagy köztes adatait az Azure Storage-ba, egy SQL-adatbázisba vagy-struktúrába egy folyamat futtatása közben, vagy a Felhőbeli tároló használatával átválthatja az adatátviteli folyamatokat.  | [Az adatbevitel manuálisan](enter-data-manually.md) <br/> [Adatexportálás](export-data.md) <br/> [Adatimportálás](import-data.md) |
| Adatátalakítás | A gépi tanuláshoz egyedi adatokon alapuló műveletek, például a normalizálás vagy a dobozolási, a dimenzióját csökkentése és az adatok konvertálása különböző fájlformátumok között.| [Oszlopok hozzáadása](add-columns.md) <br/> [Sorok hozzáadása](add-rows.md) <br/> [Matematikai művelet alkalmazása](apply-math-operation.md) <br/> [SQL-transzformáció alkalmazása](apply-sql-transformation.md) <br/> [Hiányzó adatértékek törlése](clean-missing-data.md) <br/> [Klip értékei](clip-values.md) <br/> [Konvertálás CSV-re](convert-to-csv.md) <br/> [Átalakítás adatkészletbe](convert-to-dataset.md) <br/> [Metaadatok szerkesztése](edit-metadata.md) <br/> [Csatlakozás az adatszolgáltatáshoz](join-data.md) <br/> [Az adatnormalizálás](normalize-data.md) <br/> [Partíció és minta](partition-and-sample.md)  <br/> [Ismétlődő sorok eltávolítása](remove-duplicate-rows.md) <br/> [ARCUL ütötte](smote.md) <br/> [Oszlopok átalakításának kiválasztása](select-columns-transform.md) <br/> [Oszlopok kiválasztása az adatkészletben](select-columns-in-dataset.md) <br/> [Adatfelosztás](split-data.md) |
| Szolgáltatás kiválasztása | Válassza ki a megfelelő, hasznos funkciók egy részhalmazát, amelyet az analitikai modell létrehozásához kíván használni. | [Szűrésen alapuló funkció kiválasztása](filter-based-feature-selection.md) <br/> [A permutáció funkció fontossága](permutation-feature-importance.md) |
| Statisztikai függvények | Az adatelemzéshez kapcsolódó statisztikai módszerek széles skáláját adja meg. | [Az adatösszesítés](summarize-data.md)|
|  | **Gépi tanulási algoritmusok**: | |
| Regresszió | Egy érték előrejelzése. | [A döntési fa regressziójának fokozása](boosted-decision-tree-regression.md) <br/> [Döntési erdő regressziója](decision-forest-regression.md) <br/> [Lineáris regresszió](linear-regression.md)  <br/> [Neurális hálózat regressziója](neural-network-regression.md)  <br/> |
| Fürtözés | Csoportosítsa az adathalmazokat.| [K – fürtözés](k-means-clustering.md)
| Osztályozás | Egy osztály előrejelzése.  Válasszon a bináris (kétosztályos) vagy a többosztályos algoritmusok közül.| [Többosztályos növelt döntési fa](multiclass-boosted-decision-tree.md) <br/> [Többosztályos döntési erdő](multiclass-decision-forest.md) <br/> [Többosztályos logisztikai regresszió](multiclass-logistic-regression.md)  <br/> [Többosztályos neurális hálózat](multiclass-neural-network.md) <br/> [Egy vagy minden többosztályos](one-vs-all-multiclass.md) <br/> [Kétosztályos átlagú Perceptron](two-class-averaged-perceptron.md) <br/>  [Kétosztályos, megnövelt döntési fa](two-class-boosted-decision-tree.md)  <br/> [Kétosztályos döntési erdő](two-class-decision-forest.md) <br/>  [Kétosztályos logisztikai regresszió](two-class-logistic-regression.md) <br/> [Kétosztályos neurális hálózat](two-class-neural-network.md) <br/> [Kétosztályos támogatási vektoros gép](two-class-support-vector-machine.md) | 
|  | **Modellek létrehozása és kiértékelése**: | |
| A modell betanítása | Adatfuttatás az algoritmuson keresztül. |  [Fürtözési modell betanítása](train-clustering-model.md) <br/> [Betanítási modell](train-model.md)  <br/> [Modell Hiperparaméterek beállítása hangolása](tune-model-hyperparameters.md) |
| Modell pontozása és kiértékelése | Mérje fel a betanított modell pontosságát. | [Átalakítás alkalmazása](apply-transformation.md) <br/> [Az adatfürthöz rendelése](assign-data-to-clusters.md) <br/> [Kereszt-ellenőrzési modell](cross-validate-model.md) <br/> [Modell kiértékelése](evaluate-model.md) <br/> [Pontszám modell](score-model.md) |
| Python nyelv | Kód írása és beágyazása modulba a Python és a folyamat integrálásához. | [Python-modell létrehozása](create-python-model.md) <br/> [Python-szkript végrehajtása](execute-python-script.md) |
| R nyelv | Kód írása és beágyazása egy modulba az R és a folyamat integrálásához. | [R-szkript végrehajtása](execute-r-script.md) |
| Text Analytics | Speciális számítási eszközöket biztosít a strukturált és strukturálatlan szöveggel való munkához. | [N grammos funkciók kinyerése szövegből](extract-n-gram-features-from-text.md) <br/> [Szolgáltatások kivonatolása](feature-hashing.md) <br/> [Szöveg előfeldolgozása](preprocess-text.md) |
| Ajánlás | Build-javaslatok modelljei. | [Az ajánló kiértékelése](evaluate-recommender.md) <br/> [Pontszám SVD ajánló](score-svd-recommender.md) <br/> [SVD-ajánló](train-SVD-recommender.md) |

## <a name="error-messages"></a>Hibaüzenetek

Ismerje meg, hogy milyen [hibaüzenetek és kivétel-kódok](designer-error-codes.md) merülhetnek fel a Azure Machine learning Designer moduljaival.
