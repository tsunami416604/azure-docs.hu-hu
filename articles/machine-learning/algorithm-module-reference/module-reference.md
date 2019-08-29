---
title: Algoritmusokra és modulokra vonatkozó referenciák
titleSuffix: Azure Machine Learning service
description: Az Azure Machine Learning Visual Interface-ben elérhető modulok ismertetése
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 065931140894478caee9d4ea49dac49f2415716b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128658"
---
# <a name="algorithm--module-reference-overview"></a>Algoritmus & modul hivatkozásának áttekintése

Ez a hivatkozási tartalom a Azure Machine Learning szolgáltatás vizuális felületén (előzetes verzió) elérhető gépi tanulási algoritmusok és modulok technikai hátterét ismerteti.

Minden modul a kód egy készletét jelöli, amely egymástól függetlenül futtatható, és gépi tanulási feladatot hajt végre, a szükséges bemenetek miatt. A modulok tartalmazhatnak egy adott algoritmust, vagy elvégezhetnek egy olyan feladatot, amely fontos a gépi tanulásban, például a hiányzó érték cseréje vagy a statisztikai elemzés.

> [!TIP]
> A vizualizációs felületen található bármely kísérlet során információt kaphat egy adott modulról. Válassza ki a modult, majd kattintson a **további Súgó** hivatkozásra a **gyors Súgó** ablaktáblán.

## <a name="modules"></a>Modulok

A modulok a funkciók szerint vannak rendszerezve:

| Funkció | Leírás | Modul |
| --- |--- | ---- |
| Adatformátum átalakítása | A gépi tanulásban használt különböző fájlformátumok között alakítsa át az adatformátumot, | [Konvertálás CSV-re](convert-to-csv.md) |
| Adatok bevitele és kimenete | Felhőbeli forrásokból származó adatok áthelyezése a kísérletbe. Megírhatja az eredményeket vagy a köztes adatait az Azure Storage-ba, egy SQL-adatbázisba vagy-struktúrába egy kísérlet futtatása közben, vagy a felhőalapú tárolás használatával adatcserét végez a kísérletek között.  | [Adatimportálás](import-data.md)<br/>[Adatexportálás](export-data.md)<br/>[Az adatbevitel manuálisan](enter-data-manually.md) |
| Adatok átalakítása | A gépi tanulással kapcsolatos adatok, például az adatok normalizálása vagy dobozolási, a funkciók kiválasztása és a dimenzióját csökkentése.| [Oszlopok kiválasztása az adatkészletben](select-columns-in-dataset.md) <br/> [Metaadatok szerkesztése](edit-metadata.md) <br/> [Hiányzó adatértékek törlése](clean-missing-data.md) <br/> [Oszlopok hozzáadása](add-columns.md) <br/> [Sorok hozzáadása](add-rows.md) <br/> [Ismétlődő sorok eltávolítása](remove-duplicate-rows.md) <br/> [Csatlakozás az adatszolgáltatáshoz](join-data.md) <br/> [Adatfelosztás](split-data.md) <br/> [Az adatnormalizálás](normalize-data.md) <br/> [Partíció és minta](partition-and-sample.md) |
| Python és R modulok | Kód írása és beágyazása egy modulba a Python és az R integrálásához a kísérlettel. | [Python-szkript végrehajtása](execute-python-script.md)   <br/> [Python-modell létrehozása](create-python-model.md) <br/> [R-szkript végrehajtása](execute-r-script.md)
|  | **Gépi tanulási algoritmusok**: | |
| Besorolás | Egy osztály előrejelzése.  Válasszon a bináris (kétosztályos) vagy a többosztályos algoritmusok közül.| [Többosztályos döntési erdő](multiclass-decision-forest.md) <br/> [Többosztályos növelt döntési fa](multiclass-boosted-decision-tree.md) <br/> [Többosztályos logisztikai regresszió](multiclass-logistic-regression.md)  <br/> [Többosztályos neurális hálózat](multiclass-neural-network.md)  <br/>  [Kétosztályos logisztikai regresszió](two-class-logistic-regression.md)  <br/>[Kétosztályos átlagú Perceptron](two-class-averaged-perceptron.md) <br/> [Kétosztályos&nbsp;,&nbsp;megnövelt döntési&nbsp;fa](two-class-boosted-decision-tree.md)  <br/> [Kétosztályos döntési erdő](two-class-decision-forest.md)  <br/> [Kétosztályos neurális hálózat](two-class-neural-network.md)  <br/> [Kétosztályos&nbsp;támogatási&nbsp;vektoros&nbsp;gép&#8209;](two-class-support-vector-machine.md) 
| Fürtözés | Csoportosítsa az adathalmazokat.| [K – fürtözés](k-means-clustering.md)
| Regresszió | Egy érték előrejelzése. | [Lineáris regresszió](linear-regression.md)  <br/> [Neurális hálózat regressziója](neural-network-regression.md)  <br/> [Döntési erdő regressziója](decision-forest-regression.md)  <br/> [A döntési&nbsp;fa&nbsp;regressziójánakfokozása&nbsp;](boosted-decision-tree-regression.md)
|  | **Modellek létrehozása és**kiértékelése: | |
| Betanítás   | Adatfuttatás az algoritmuson keresztül. | [Betanítási modell](train-model.md)  <br/> [Fürtözési modell betanítása](train-clustering-model.md)    |
| Modell értékelése | Mérje fel a betanított modell pontosságát. |  [Modell kiértékelése](evaluate-model.md)
| Pontszám | Szerezze be a most betanított modellből származó előrejelzéseket. | [Átalakítás alkalmazása](apply-transformation.md)<br/>[Az&nbsp;adatfürthöz&nbsp;rendelése&nbsp;](assign-data-to-clusters.md) <br/>[Pontszám modell](score-model.md)

## <a name="error-messages"></a>Hibaüzenetek

Ismerje meg azokat a [hibaüzeneteket és kivételi kódokat](machine-learning-module-error-codes.md) , amelyek a Azure Machine learning szolgáltatás vizuális felületén található modulok használatával találkozhatnak.
