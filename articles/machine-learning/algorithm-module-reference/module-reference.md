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
ms.openlocfilehash: 6602eb4bacdc3b6382c1b6873a465cdfc0632693
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029340"
---
# <a name="algorithm--module-reference-overview"></a>Algoritmusokra és modulokra referencia – áttekintés

Ez a referencia-tartalom gépi tanulási algoritmusok és a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás elérhető modulok minden egyes technikai tudással biztosít. 

Minden modul mutatnak, amelyek egymástól függetlenül futtathatja, és hajtsa végre a machine learning-feladatban szükséges bemeneti adatok halmazai. Egy modul előfordulhat, hogy egy adott algoritmus tartalmaz, vagy hajtsa végre egy feladatot, amely fontos a Machine learning, például hiányzó érték lecseréléséről, vagy a statisztikai elemzése. 

> [!TIP]
> Bármilyen kísérlet a vizuális felületen, az eszközspecifikus modul kapcsolatos információkat szerezhet a. Jelölje ki a modult, majd válassza ki a **további segítséget itt találhat** hivatkozásra a **gyors súgó** ablaktáblán.

Modulok funkciók szerint vannak rendszerezve:

**Adatátalakítókat formátuma**

  + [Átalakítás fürt megosztott Kötetévé ](convert-to-csv.md)

**Bemeneti és kimeneti adatok modulok** ezt a munkát a felhőbeli forrásokból származó adatok áthelyezése a kísérletbe. Az eredmények vagy a köztes adatokat írni az Azure Storage, SQL-adatbázis vagy a Hive, kísérlet futtatása során, vagy használhat felhőtárhelyet kísérletek közötti cseréjéhez.  

  + [Adatok importálása](import-data.md)

  + [Adatok exportálása](export-data.md)

  + [Adja meg manuálisan az adatok](enter-data-manually.md)


**Adatok átalakítása modulok** támogatja a Machine learning, például normalizálása vagy adatok, a szolgáltatás kiválasztása és dimenziócsökkentést dobozolás egyedi adatokon műveleteket.

  + [Oszlopok kiválasztása az adathalmaz](select-columns-in-dataset.md)

  + [Metaadatok szerkesztése](edit-metadata.md)

  + [Hiányzó adatok törlése](clean-missing-data.md)

  + [Oszlopok hozzáadása](add-columns.md)

  + [Sorok hozzáadása](add-rows.md)

  + [Ismétlődő sorok eltávolítása](remove-duplicate-rows.md)

  + [Adatok felosztása](split-data.md)

  + [Adatok optimalizálása](normalize-data.md)

  + [Partíció és minta](partition-and-sample.md)


**Machine learning-algoritmusok** fürtözés, tartóvektor-gép vagy Neurális hálózatok, például egyedi modulok, amelyekkel testre szabhatja a megfelelő paraméterekkel rendelkező gépi tanulási feladat érhető el.  
  + [Pontszám modell](score-model.md)

  + [Adatok hozzárendelése fürtökhöz ](assign-data-to-clusters.md)

  + [Tanítási modell](train-model.md)

  + [A fürtözési modell betanítása](train-clustering-model.md)

  + [Modell értékelése](evaluate-model.md)

  + [Átalakítás alkalmazása](apply-transformation.md)

  + [Lineáris regresszió](linear-regression.md)

  + [Neurális hálózat regresszió](neural-network-regression.md)

  + [Regressziós döntési erdő](decision-forest-regression.md)

  + [Gyorsított regressziós döntési fa](boosted-decision-tree-regression.md)

  + [Kétosztályos gyorsított döntési fa](two-class-boosted-decision-tree.md)

  + [Kétosztályos logisztikai regresszió](two-class-logistic-regression.md)

  + [Multiclass logisztikai regresszió](multiclass-logistic-regression.md)

  + [Multiclass Neurális hálózat](multiclass-neural-network.md)

  + [Osztályú döntési erdő](multiclass-decision-forest.md)

  + [Kétosztályos Perceptron átlagoláshoz használni.](two-class-averaged-perceptron.md)

  + [Kétosztályos döntési erdő](two-class-decision-forest.md)

  + [Kétosztályos Neurális hálózat](two-class-neural-network.md)

  + [Kétosztályos Tartóvektor-gép](two-class-support-vector-machine.md)
  
  + [K-Means Clustering](k-means-clustering.md)


**Python modul** megkönnyíti a egyéni függvény futtatására. A kód írása, és beágyazza azt egy modulban kísérlet szolgáltatással integrálható a Python.
  + [Python-szkript végrehajtása](execute-python-script.md)

  + [Python-modell létrehozása](create-python-model.md)


