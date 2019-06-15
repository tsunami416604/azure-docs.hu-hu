---
title: Algoritmusokra és modulokra vonatkozó referenciák
titleSuffix: Azure Machine Learning service
description: További tudnivalók az Azure Machine Learning vizuális felületen érhető el a modulok
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 8825f1dc3b66a5c4981ba25a90813aec63975b1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65145137"
---
# <a name="algorithm--module-reference-overview"></a>Algoritmusokra és modulokra referencia – áttekintés

Ez a referencia-tartalom gépi tanulási algoritmusok és a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás elérhető modulok minden egyes technikai tudással biztosít.

Minden modul mutatnak, amelyek egymástól függetlenül futtathatja, és hajtsa végre a machine learning-feladatban szükséges bemeneti adatok halmazai. Egy modul előfordulhat, hogy egy adott algoritmus tartalmaz, vagy hajtsa végre egy feladatot, amely fontos a Machine learning, például hiányzó érték lecseréléséről, vagy a statisztikai elemzése.

> [!TIP]
> Bármilyen kísérlet a vizuális felületen, az eszközspecifikus modul kapcsolatos információkat szerezhet a. Jelölje ki a modult, majd válassza ki a **további segítséget itt találhat** hivatkozásra a **gyors súgó** ablaktáblán.

## <a name="modules"></a>Modulok

Modulok funkciók szerint vannak rendszerezve:

| Funkció | Leírás | Modul |
| --- |--- | ---- |
| Adatformátum átalakítása | Konvertálja az adatokat a machine learning, a használt különböző fájlformátumok között | [Átalakítás fürt megosztott Kötetévé](convert-to-csv.md) |
| Adatok bemeneti és kimeneti | Felhőbeli forrásokból származó adatok áthelyezése a kísérletbe. Az eredmények vagy a köztes adatokat írni az Azure Storage, SQL-adatbázis vagy a Hive, kísérlet futtatása során, vagy használhat felhőtárhelyet kísérletek közötti cseréjéhez.  | [Adatok importálása](import-data.md)<br/>[Adatok exportálása](export-data.md)<br/>[Adja meg manuálisan az adatok](enter-data-manually.md) |
| Adatátalakítás | Machine learning, például normalizálása vagy adatok, a szolgáltatás kiválasztása és dimenziócsökkentést dobozolás egyedi adatokon műveleteket.| [Oszlopok kiválasztása az adathalmaz](select-columns-in-dataset.md) <br/> [Metaadatok szerkesztése](edit-metadata.md) <br/> [Hiányzó adatok törlése](clean-missing-data.md) <br/> [Oszlopok hozzáadása](add-columns.md) <br/> [Sorok hozzáadása](add-rows.md) <br/> [Ismétlődő sorok eltávolítása](remove-duplicate-rows.md) <br/> [Adatok felosztása](split-data.md) <br/> [Adatok optimalizálása](normalize-data.md) <br/> [Partíció és minta](partition-and-sample.md) |
| Python modul | Kód írása, és beágyazza azt egy modulban Python integrálását is futtathatja a kísérletet. | [Python-szkript végrehajtása](execute-python-script.md)   <br/> [Python-modell létrehozása](create-python-model.md)
|  | **Machine learning-algoritmusok**: | |
| Besorolás | Előre jelezni egy osztályt.  Válassza ki a bináris (két osztályú) vagy a multiclass algoritmusokat.| [Osztályú döntési erdő](multiclass-decision-forest.md) <br/> [Multiclass logisztikai regresszió](multiclass-logistic-regression.md)  <br/> [Multiclass Neurális hálózat](multiclass-neural-network.md)  <br/>  [Kétosztályos logisztikai regresszió](two-class-logistic-regression.md)  <br/>[Kétosztályos Perceptron átlagoláshoz használni.](two-class-averaged-perceptron.md) <br/> [Kétosztályos&nbsp;súlyozott&nbsp;döntési&nbsp;fa](two-class-boosted-decision-tree.md)  <br/> [Kétosztályos döntési erdő](two-class-decision-forest.md)  <br/> [Kétosztályos Neurális hálózat](two-class-neural-network.md)  <br/> [Két&#8209;osztály&nbsp;támogatási&nbsp;vektor&nbsp;gép](two-class-support-vector-machine.md) 
| Fürtszolgáltatás | Adatok egy csoportba.| [K-Means Clustering](k-means-clustering.md)
| Regresszió | Előre jelzett egy érték. | [Lineáris regresszió](linear-regression.md)  <br/> [Neurális hálózat regresszió](neural-network-regression.md)  <br/> [Regressziós döntési erdő](decision-forest-regression.md)  <br/> [Súlyozott&nbsp;döntési&nbsp;fa&nbsp;regresszió](boosted-decision-tree-regression.md)
|  | **Hozhat létre, és a modellek kiértékelése**: | |
| Betanítás   | Futtassa az adatok az algoritmus keresztül. | [Tanítási modell](train-model.md)  <br/> [A fürtözési modell betanítása](train-clustering-model.md)    |
| Modell értékelése | Mérheti a betanított modell pontosságát. |  [Modell értékelése](evaluate-model.md)
| Pontszám | Kaphatnak előjelzéseket a korábban csak betanított modellből. | [Átalakítás alkalmazása](apply-transformation.md)<br/>[Rendelje hozzá&nbsp;adatok&nbsp;való&nbsp;fürtök](assign-data-to-clusters.md) <br/>[Pontszám modell](score-model.md)

## <a name="error-messages"></a>Hibaüzenetek

További információ a [hibaüzenetek és a kivétel kódok](machine-learning-module-error-codes.md) találkozhat modulok használata az Azure Machine Learning szolgáltatás a vizuális felhasználói felületet.
