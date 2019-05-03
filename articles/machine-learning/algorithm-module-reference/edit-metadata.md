---
title: 'Metaadatok szerkesztése: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Útmutató az Azure Machine Learning szolgáltatás a metaadatok szerkesztéséhez modul segítségével módosíthatja egy adatkészlet oszlopok társított metaadatokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: cfee607aca155b6cf68e5bddc40eb9c752df5e34
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028815"
---
# <a name="edit-metadata-module"></a>Metaadatok modul szerkesztése

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul segítségével módosíthatja egy adatkészlet oszlopok társított metaadatokat. Az adatkészlet típusa érték és az adatok módosulnak használata után a **metaadatainak szerkesztése** modul. 
 
Jellemző metaadat-módosítások a következők lehetnek:
  
+ Kategorikus értékek logikai vagy numerikus oszlopok kezelni  
  
+ Jelzi, hogy melyik oszlop tartalmazza a *osztály* címkét, vagy a kategorizálását, vagy előre jelezni kívánt értékeket  
  
+ Oszlopok megjelölése funkciók
  
+ Módosítása a dátum/idő értékei egy numerikus érték, vagy fordítva  
  
+ Oszlopok átnevezése
  
 [Szerkesztése metaadatok bármikor módosítania egy oszlop definíciója általában egy alsóbb rétegbeli modul igényeinek megfelelően. Például bizonyos modulok csak meghatározott adattípusok használata, vagy jelzőket a oszlop, például a szükséges `IsFeature` vagy `IsCategorical`.  
  
 A szükséges műveletet hajt végre, miután a metaadatok visszaállíthatja az eredeti állapotba. 
  
## <a name="configure-edit-metadata"></a>Szerkesztés metaadatok konfigurálása
  
1.  Adja hozzá az Azure Machine Learning [metaadatainak szerkesztése](./edit-metadata.md) modult a kísérletvászonra, és csatlakozzon a frissíteni kívánt adatkészlet. Annak a **adatátalakítás**, a a **kezelése** kategória.
  
2.  Kattintson a **az Oszlopválasztó elindítása** , és válassza ki az oszlop vagy oszlopkészlet dolgozhat. Szerint is választhat oszlopok külön-külön, neve vagy indexe, vagy választhat egy csoportot az oszlopok, típus szerint.  
  
3.  Válassza ki a **adattípus** beállítást, ha szüksége más hozzárendelése a kijelölt oszlopokban. Az adatok módosítása típus szükség lehet bizonyos műveletek: például, ha a forrás adatkészlet kezeli a szöveg, számok, módosítania kell azokat egy numerikus adattípusúra matematikai műveletek használata előtt. 

    + A támogatott adattípusok a következők `String`, `Integer`, `Double`, `Boolean`, `DateTime`. 

    + Ha több oszlop van kiválasztva, telepítenie kell a metaadat-módosítások **összes** kijelölt oszlopok. Például tegyük fel, a 2-3 numerikus oszlopok kiválasztása. Módosítsa őket az összes kifejezésnek karakteres adattípusúnak, és nevezze át őket egy művelettel. Azonban nem módosítható egy oszlop karakteres adattípusúnak, és a egy másik oszlopot egy lebegőpontos az egész.
  
    + Ha nem ad meg egy új típusú adatokat, az az oszlop metaadatai nem változik. 
    
    + Az oszlop típusa és az értékek módosulnak után hajtsa végre a [metaadatainak szerkesztése](./edit-metadata.md) műveletet. Akkor is használatával állíthatja helyre az eredeti adattípus bármikor [metaadatainak szerkesztése](./edit-metadata.md) alaphelyzetbe állítja az oszlop adattípusa.  

    > [!NOTE]
    > Ha módosítja a bármilyen típusú és szám a **dátum és idő** írja be, hagyja a **dátum és idő formátumban** mező üres. Jelenleg nincs lehetőség a cél-adatformátum megadásához.  

      
4.  Válassza ki a **Kategorikus** beállítással azt adhatja meg, hogy a kijelölt oszlopokban szereplő értékek kategóriák kell kezelni. 

    Például akkor lehet, hogy 0,1. és 2 számokat tartalmazó oszlop, de tudja, hogy számok ténylegesen jelenti azt, hogy "Smoker", "Nem-smoker" és "Ismeretlen". Ebben az esetben az oszlopot a kategorikus megjelölése biztosíthatja, hogy az értékek nem használ a numerikus számításokban, csak az adatok csoportosításához. 
  
5.  Használja a **mezők** beállítást, ha meg szeretné változtatni a módon, hogy az Azure Machine Learning a modellek az adatokat használja.

    + **A szolgáltatás**: Ez a beállítás használatával Ez a jelző azt egy olyan oszlop szolgáltatásként, a modulok, amely csak a szolgáltatás oszlopok segítségével. Alapértelmezés szerint az összes oszlop szolgáltatások kezdetben kezeli.  
  
    + **Címke**: Ez a beállítás segítségével jelölje meg a címke (más néven az előre jelezhető attribútumot, vagy célváltozó). Számos modulok megkövetelése, hogy legalább egy (és csak egy) címke oszlop található az adatkészletben lévő lehet. 
    
        Sok esetben az Azure Machine Learning is következtet, hogy egy oszlop osztály címkét tartalmaz, de a metaadatok beállításával biztosíthatja, hogy az oszlop megfelelően azonosítja. Ezt a beállítást nem változik adatértékek, csak a módon, hogy néhány gépi tanulási algoritmusok kezelni az adatok.
  

  
    > [!TIP]
    >  Adatok, amelyek nem fér el a következő kategóriákba vannak?  Előfordulhat, hogy például, hogy az adatkészlet egyedi azonosítók, amelyek nem hasznos változókat, például értékeket tartalmaznak. Egyes esetekben a azonosítók modell használatakor problémákat okozhat. 
    >   
    >  Szerencsére "valójában" Azure Machine Learning tartja az adatokat, így nem kell törölni az ilyen oszlopokat az adatkészletből. Amikor szüksége van néhány speciális oszlopkészleteket műveletek végrehajtásához, csak távolítsa el az összes többi oszlop ideiglenesen használatával a [Select Columns in Dataset](./select-columns-in-dataset.md) modul. Később is egyesíthet az oszlopok vissza az adatkészlet használatával a [oszlopok hozzáadása](./add-columns.md) modul.  
  
6. A következő beállítások segítségével törölje az előző beállításokat, és vissza a metaadatokat az alapértelmezett értékeket.  
  
    + **Törölje a szolgáltatás**: Ez a beállítás használatával távolítsa el a szolgáltatás jelzőt.  
  
         Mivel az összes oszlop kezdetben számít funkciói, hajtsa végre a matematikai műveletek modulok szüksége lehet a beállítás megakadályozza, hogy a numerikus oszlopokat, amelyeket az változók használatához.
  
    + **Egyértelmű címke**: Ez a beállítás segítségével távolítsa el a **címke** a megadott oszlop metaadataiból.  
  
    + **Törölje a pontszám**: Ez a beállítás segítségével távolítsa el a **pontszám** a megadott oszlop metaadataiból.  
  
         Lehetővé teszi egy pontszám egy oszlopot explicit módon megjelölése jelenleg az Azure Machine Learning nem érhető el. Egyes műveletek azonban egy pontszám belső használatra megjelölt alatt álló oszlop eredményez. Emellett egy egyéni R modult előfordulhat, hogy kimeneti pontszám értékeket.
  
  
7.  A **új oszlopnevek**, írja be a kijelölt oszlopot vagy oszlopokat új nevét.  
  
    + Oszlopnevek használható csak az UTF-8 által támogatott karakterek kódolása. Üres karakterláncok, a NULL értékek vagy a tárolóhelyek álló nevek nem engedélyezettek.  
  
    + Több oszlop átnevezéséhez írja be a neveket vesszővel tagolt listaként az oszlop indexek sorrendje.  
  
    + Az összes kijelölt oszlopok kell nevezhető át. Nem hagyja el vagy hagyja ki az oszlopot.  
  
  
8.  Futtassa a kísérletet.  

## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 