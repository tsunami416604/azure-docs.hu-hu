---
title: "Adatok importálása a Machine Learning Studio |} Microsoft Docs"
description: "Az adatok importálása az Azure Machine Learning Studio a különféle adatforrásokból származó módjáról. Ismerje meg, milyen típusú adatokat és az adatok formátumok támogat."
keywords: "adatok, adatformátum, adattípusok, adatforrások, betanítási adatok importálása"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: c194ee3b-838c-4efe-bb2a-c1d052326216
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: garye;bradsev
ms.openlocfilehash: 98a8fa15922ef100a7f8cadd92fb7ffef0d3bcde
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>A betanítási adatok importálása az Azure Machine Learning Studióba különböző adatforrásokból
A saját adatok használata a Machine Learning Studióban fejlesztésére és egy prediktív elemzési megoldások betanítását, a következőket teheti: 

* az adatok feltöltése egy **helyi fájl** időben a merevlemez-meghajtóról hozzon létre egy adatkészlet-modult a munkaterületen
* adatelérés több egyikéből **online adatforrások** kísérletbe használatával futása közben a [és adatokat importálhat] [ import-data] modul 
* Használjon egy másik Azure Machine learning adatait **kísérletezhet** adatkészletként mentése
* a helyszíni adatok felhasználásával **SQL Server-adatbázis**

Ezen lehetőségek ismertetett a témaköröket a lenti menüben. Ezek a témakörök bemutatják a Machine Learning Studio-ban történő használatáról a különféle adatforrásokból származó adatok importálása. 

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

> [!NOTE]
> Nincsenek elérhető a betanítási adatok használható a Machine Learning Studio számos mintaként használható adathalmazt. Ezek az információk: [a mintaként használható adathalmazt használja az Azure Machine Learning Studióban](use-sample-datasets.md)).
> 
> 

Ez a témakör bevezető is ismerteti, hogy hogyan adatok használatra kész állapotba hozásához a Machine Learning Studióban, és írja le, mely adatokat formátumok és adattípusok használata támogatott. 

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

## <a name="get-data-ready-for-use-in-azure-machine-learning-studio"></a>Adatok használatra kész állapotba hozásához az Azure Machine Learning Studióban
A Machine Learning Studio téglalap vagy táblázatos adatok, például egy adatbázis strukturált bizonyos körülmények között nem téglalap használhatja, ha vagy tagolt szöveges adat tervezték.

Az ajánlott, ha az adatok viszonylag tiszta. Ez azt jelenti, hogy érdemes például nem jegyzett karakterláncok gondoskodunk az adatok kísérletbe való feltöltés előtt.

Van azonban modulok érhető el a Machine Learning Studióban, amelyek lehetővé teszik az adatok kísérletbe belül néhány kezeléséhez. Attól függően gépi tanulási algoritmusok fogja használni, szükség lehet annak eldöntése, hogyan fogja kezelni adatok strukturális problémák, például a hiányzó értékeket, és a ritka adatokhoz, így nincsenek a modulokat, amelyek segíthetnek, hogy a. Keresse meg a **Data Transformation** modulok végre ezeket a funkciókat, a modulpalettán szakasza.

A kísérletben bármikor megtekintheti, és töltse le az adatokat, kattintson a kimeneti portra modul által létrehozott. Attól függően, hogy a modul nincs másik letöltési beállítások érhetők el, vagy esetleg a Machine Learning Studióban webböngészőből adatok megjelenítéséhez.

## <a name="data-formats-and-data-types-supported"></a>Támogatott formátumok és adattípusok
Adattípusok számos importálhatja a kísérletet, attól függően, hogy milyen mechanizmus használatával importálja az adatokat, és ahol adatforrásból származó:

* Egyszerű szöveges (.txt)
* Vesszővel tagolt (CSV) a fejléc (.csv) vagy anélkül (. nh.csv)
* A lapon elválasztott értékeket (TSV) fejléc (.tsv) vagy anélkül (. nh.tsv)
* Excel-fájl
* Azure-tábla
* Hive tábla
* SQL-adatbázistáblában szereplő
* Az OData-értékek
* SVMLight adatok (.svmlight) (lásd a [SVMLight definition](http://svmlight.joachims.org/) formátum információt)
* Attribútum-kapcsolat fájlformátumra (ARFF) adatokat (.arff) (lásd: a [ARFF definition](http://weka.wikispaces.com/ARFF) formátum információt)
* Zip-fájl (.zip)
* R-objektum vagy munkaterület fájl (. RData)

Ha például a metaadatokat tartalmazó ARFF formátumú adatokat importál, a Machine Learning Studio ezeket a metaadatokat használja adható meg a címsor és minden oszlop adattípusát.

Ha importálja az adatokat, például TSV vagy CSV formátum, amely nem tartalmazza ezeket a metaadatokat, a Machine Learning Studio által az adatok mintavétele arra következtet minden oszlop adattípusát. Ha az adatok nem rendelkezik oszlopának fejlécére kattintva rendezhető, a Machine Learning Studio biztosít alapértelmezett nevét.

Explicit módon adja meg vagy módosítsa a használatával oszlopok fejlécére kattintva rendezhető és adattípusok a [szerkesztése metaadatok][edit-metadata].

A következő **adattípusok** felismeri a Machine Learning Studio:

* Karakterlánc
* Egész szám
* Dupla
* Logikai érték
* Dátum és idő
* A TimeSpan

A Machine Learning Studio nevű belső adatok típust használ ***adattábla*** mechanizmusok adatok átadására modulok között. Explicit módon az adatokat átalakíthatja adattábla formátum használatával az [átalakítása Dataset] [ convert-to-dataset] modul.

Bármely modul, amely fogadja a formátumok adattábla nem lesz adatok átalakítása adattábla beavatkozás nélkül átadja azt a következő modul előtt.

Ha szükséges, konvertálhatja adattábla CSV, TSV, ARFF programba, vagy SVMLight formátumban más átalakítás modulok használata.
Keresse meg a **adatok formátuma átalakítások** modulok végre ezeket a funkciókat, a modulpalettán szakasza.

<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
