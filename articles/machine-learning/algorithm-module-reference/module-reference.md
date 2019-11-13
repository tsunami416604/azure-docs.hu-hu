---
title: Algoritmusokra és modulokra vonatkozó referenciák
description: A Azure Machine Learning Designerben elérhető modulok ismertetése (előzetes verzió)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 11/11/2019
ms.openlocfilehash: 938286f0dafdeb11473bef1b88f876d7918e76ca
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012753"
---
# <a name="algorithm--module-reference"></a>Algoritmusokra és modulokra vonatkozó referenciák

Ez a hivatkozási tartalom a Azure Machine Learning Designer (előzetes verzió) szolgáltatásban elérhető gépi tanulási algoritmusok és modulok technikai hátterét ismerteti.

Minden modul a kód egy készletét jelöli, amely egymástól függetlenül futtatható, és gépi tanulási feladatot hajt végre, a szükséges bemenetek miatt. A modulok tartalmazhatnak egy adott algoritmust, vagy elvégezhetnek egy olyan feladatot, amely fontos a gépi tanulásban, például a hiányzó érték cseréje vagy a statisztikai elemzés.

> [!TIP]
> A tervező bármely folyamatában információt kaphat egy adott modulról. Válassza ki a modult, majd kattintson a **további Súgó** hivatkozásra a **gyors Súgó** ablaktáblán.

## <a name="modules"></a>Modulok

A modulok a funkciók szerint vannak rendszerezve:

| Funkció | Leírás | Modul |
| --- |--- | ---- |
| Adatok bevitele és kimenete | A Felhőbeli forrásokból származó adatok áthelyezése a folyamatba. Megírhatja az eredményeket vagy köztes adatait az Azure Storage-ba, egy SQL-adatbázisba vagy-struktúrába, egy folyamat futtatása közben, vagy a Felhőbeli tároló használatával átválthatja az adatcsatornák közötti adatcserét.  | [Adatimportálás](import-data.md) <br/> [Az adatbevitel manuálisan](enter-data-manually.md) <br/>[Adatexportálás](export-data.md) |
| Adatátalakítás | A gépi tanuláshoz egyedi adatokon alapuló műveletek, például a normalizálás vagy a dobozolási, a dimenzióját csökkentése és az adatok konvertálása különböző fájlformátumok között.| [Oszlopok hozzáadása](add-columns.md) <br/> [Sorok hozzáadása](add-rows.md) <br/> [Hiányzó adatértékek törlése](clean-missing-data.md) <br/> [Konvertálás CSV-re](convert-to-csv.md) <br/> [Átalakítás adatkészletbe](convert-to-dataset.md) <br/> [Metaadatok szerkesztése](edit-metadata.md) <br/> [Csatlakozás az adatszolgáltatáshoz](join-data.md) <br/> [Az adatnormalizálás](normalize-data.md) <br/> [Ismétlődő sorok eltávolítása](remove-duplicate-rows.md) <br/> [Oszlopok átalakításának kiválasztása](select-columns-transform.md) <br/> [Oszlopok kiválasztása az adatkészletben](select-columns-in-dataset.md) |
| Mintavételezés | A gépi tanulási modellek betanítására és tesztelésére egy vagy több részhalmaz részhalmazra oszthatja adatait.  | [Kereszt-ellenőrzési modell](cross-validate-model.md) <br/> [Partíció és minta](partition-and-sample.md) <br/> [ARCUL ütötte](smote.md) <br/> [Adatfelosztás](split-data.md) |
| Szolgáltatás kiválasztása | Válassza ki a megfelelő, hasznos funkciók egy részhalmazát, amelyet az analitikai modell létrehozásához kíván használni. | [Szűrésen alapuló funkció kiválasztása](filter-based-feature-selection.md) <br/> [A permutáció funkció fontossága](permutation-feature-importance.md) |
| Python és R | Kód írása és beágyazása modulba a Python és az R integrálása a folyamattal. | [Python-modell létrehozása](create-python-model.md) <br/> [Python-szkript végrehajtása](execute-python-script.md)   <br/>  [R-szkript végrehajtása](execute-r-script.md)
| Szövegelemzés | Speciális számítási eszközöket biztosít a strukturált és strukturálatlan szöveggel való munkához. | [N grammos funkciók kinyerése szövegből](extract-n-gram-features-from-text.md) <br/> [Szolgáltatások kivonatolása](feature-hashing.md) <br/> [Szöveg előfeldolgozása](preprocess-text.md) |
|  | **Gépi tanulási algoritmusok**: | |
| Osztályozás | Egy osztály előrejelzése.  Válasszon a bináris (kétosztályos) vagy a többosztályos algoritmusok közül.| [Többosztályos döntési erdő](multiclass-decision-forest.md) <br/> [Többosztályos növelt döntési fa](multiclass-boosted-decision-tree.md) <br/> [Többosztályos logisztikai regresszió](multiclass-logistic-regression.md)  <br/> [Többosztályos neurális hálózat](multiclass-neural-network.md) <br/> [Egy vagy minden többosztályos](one-vs-all-multiclass.md) <br/>  [Kétosztályos logisztikai regresszió](two-class-logistic-regression.md)  <br/>[Kétosztályos átlagú Perceptron](two-class-averaged-perceptron.md) <br/> [Kétosztályos, megnövelt döntési fa](two-class-boosted-decision-tree.md)  <br/> [Kétosztályos döntési erdő](two-class-decision-forest.md)  <br/> [Kétosztályos neurális hálózat](two-class-neural-network.md) <br/> [Kétosztályos támogatási vektoros gép](two-class-support-vector-machine.md) | 
| Fürtszolgáltatás | Csoportosítsa az adathalmazokat.| [K – fürtözés](k-means-clustering.md)
| Regresszió | Egy érték előrejelzése. | [A döntési fa regressziójának fokozása](boosted-decision-tree-regression.md) <br/> [Döntési erdő regressziója](decision-forest-regression.md) <br/> [Lineáris regresszió](linear-regression.md)  <br/> [Neurális hálózat regressziója](neural-network-regression.md)  <br/> |
| Ajánló | Build-javaslatok modelljei. | [Az ajánló kiértékelése](evaluate-recommender.md) <br/> [Pontszám SVD ajánló](score-svd-recommender.md) <br/> [SVD-ajánló](train-SVD-recommender.md) |
|  | **Modellek létrehozása és kiértékelése**: | |
| Betanítás   | Adatfuttatás az algoritmuson keresztül. | [Betanítási modell](train-model.md)  <br/> [Fürtözési modell betanítása](train-clustering-model.md) <br/>  [Modell Hiperparaméterek beállítása hangolása](tune-model-hyperparameters.md) |
| Modell értékelése | Mérje fel a betanított modell pontosságát. |  [Modell kiértékelése](evaluate-model.md) |
| Pontszám | Szerezze be a most betanított modellből származó előrejelzéseket. | [Átalakítás alkalmazása](apply-transformation.md)<br/>[Az adatfürthöz rendelése](assign-data-to-clusters.md) <br/>[Pontszám modell](score-model.md) |
| Statisztikai függvények | Az adatelemzéshez kapcsolódó statisztikai módszerek széles skáláját adja meg. | [Matematikai művelet alkalmazása](apply-math-operation.md) <br/> [Az adatösszesítés](summarize-data.md)|

## <a name="error-messages"></a>Hibaüzenetek

Ismerje meg, hogy milyen [hibaüzenetek és kivétel-kódok](machine-learning-module-error-codes.md) merülhetnek fel a Azure Machine learning Designer moduljaival.
