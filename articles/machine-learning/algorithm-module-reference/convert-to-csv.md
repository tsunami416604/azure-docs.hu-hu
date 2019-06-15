---
title: 'Átalakítás fürt megosztott Kötetévé: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, az átalakítás fürt megosztott kötetei szolgáltatás modul használata az Azure Machine Learning szolgáltatás egy adatkészlet alakítható át egy CSV-formátumba is letöltött, exportálásának vagy megosztásának R vagy Python-szkript modulokkal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 8b8b6758cc2df7a092ce36e9507f84ac534d0e3d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028725"
---
# <a name="convert-to-csv-module"></a>Átalakítás fürt megosztott kötetei szolgáltatás modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használja egy adatkészlet alakítható át egy CSV-formátumba is letöltött, exportálásának vagy megosztásának R vagy Python-szkript modulokkal.

### <a name="more-about-the-csv-format"></a>További információ a CSV formátum 

A CSV formátum, amely a "vesszővel tagolt értékek" rövidítése, számos külső gépi tanulási eszközkészlet által használt fájlformátum. Fürt megosztott kötetei szolgáltatás nem egy közös adatcsere-formátumot, ha a nyílt forráskódú nyelvek, például az R vagy Python.

Akkor is, ha ezt teszi, hogy a legtöbb feladatot az Azure Machine Learning, nincsenek alkalommal, amikor találhatja, az adatkészlet átalakítása a fürt megosztott kötetei szolgáltatás a külső eszközök használatával hasznos. Példa:

+ Töltse le a CSV-fájl megnyitható az Excel használatával, vagy importálja egy relációs adatbázisban.  
+ Mentés a CSV-fájlt a felhőbeli tárhelyére, és kapcsolódjon a Power BI-Vizualizációk létrehozásához.  
+ A CSV formátum használatával előkészíti az adatokat az R és Python. Kattintson a jobb gombbal a kódot közvetlenül a Python, vagy egy Jupyter notebookot az adatok eléréséhez szükséges a modul kimenete. 

Amikor egy adatkészlet átalakítás fürt megosztott Kötetévé, a fájl kerül az Azure ML-munkaterület. Nyissa meg és használhatják a fájlt közvetlenül az Azure storage segédprogramot használhatja, vagy kattintson a jobb gombbal a modul kimeneti és a CSV-fájl letöltése a számítógépre, vagy használhatja az R vagy Python nyelvű programkódjával.  

## <a name="how-to-configure-convert-to-csv"></a>Átalakítás fürt megosztott kötetei szolgáltatás konfigurálása

1.  Adja hozzá a [átalakítás fürt megosztott Kötetévé](./convert-to-csv.md) modult a kísérletvászonra. Ez a modul a annak a **formátum Adatátalakítókat** csoporthoz a felület a. 

2. Csatlakoztassa azt minden olyan modul, amely egy adatkészletet.   
  
3.  Futtassa a kísérletet.

### <a name="results"></a>Results (Eredmények)
  

Kattintson duplán a kimenetét [átalakítás fürt megosztott Kötetévé](./convert-to-csv.md), és válassza ki az alábbi lehetőségek egyikét.  

 + **Eredmény adatkészlet-letöltés >** : Azonnal megnyitja az adatok másolatát egy helyi mappába menthető CSV formátumban. Ha nem ad meg egy mappát, a alkalmazni egy alapértelmezett név, és a CSV-fájlt a rendszer menti a helyi **letölti** könyvtár.


 + **Eredmény adatkészlet -> Mentés adatkészletként**: A CSV-fájlt ment vissza az Azure ML-munkaterület külön adatkészletként.

 + **Adat-hozzáférési kód készítése**: Az Azure Machine Learning állít elő, hogy hozzáférhessen az adatokat, Python vagy R. használatával a kód két készletnyi Az adatok eléréséhez, másolja a kódrészletet az alkalmazásba. (*Adatok hozzáférési kód előállítása hamarosan fognak érkezni.* )

## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 