---
title: 'Metaadatok szerkesztése: Segédanyagok a modulokhoz'
titleSuffix: Azure Machine Learning service
description: Útmutató az Azure Machine Learning szolgáltatás a metaadatok szerkesztéséhez modul segítségével módosíthatja az oszlopok egy adatkészlet társított metaadatokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 282652adb917450c262e08bf10c3c6e537b829e7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072719"
---
# <a name="edit-metadata-module"></a>Metaadatok modul szerkesztése

Ez a cikk ismerteti a visual felület (előzetes verzió) az Azure Machine Learning szolgáltatás egy modul.

Az adatok szerkesztése modul segítségével módosíthatja az oszlopok egy adatkészlet társított metaadatokat. A metaadatok szerkesztéséhez modul használata után az adatkészlet értékét, és az adatok típusát változik.

Jellemző metaadat-módosítások a következők lehetnek:
  
+ Kategorikus értékek logikai vagy numerikus oszlopok kezelni.
  
+ Jelzi, hogy melyik oszlop tartalmazza a **osztály** címkét vagy kategorizálása vagy előre jelezni kívánt értékeket tartalmazza.
  
+ Oszlopok megjelölése funkciókat.
  
+ Megváltoztatja a dátum/idő értékek, numerikus érték, vagy fordítva.
  
+ Oszlopok átnevezése.
  
 Bármikor módosítania egy oszlop definíciója általában egy alsóbb rétegbeli modul igényeinek megfelelően, használja a metaadatok szerkesztéséhez. Például bizonyos modulok csak meghatározott adattípusok használata vagy jelzőket a oszlop, például a szükséges `IsFeature` vagy `IsCategorical`.  
  
 A szükséges műveletet hajt végre, miután a metaadatok visszaállíthatja az eredeti állapotba.
  
## <a name="configure-edit-metadata"></a>Szerkesztés metaadatok konfigurálása
  
1. Az Azure Machine Learning a metaadatok szerkesztéséhez modul hozzáadása is futtathatja a kísérletet, és csatlakozzon a frissíteni kívánt adatkészlet. Az adatkészlet mellett található **adatátalakítás** a a **kezelése** kategória.
  
1. Válassza ki **az Oszlopválasztó elindítása** , és válassza ki az oszlop vagy oszlopkészlet dolgozhat. Oszlopok külön-külön neve vagy indexe választhat, vagy választhat egy típus szerint oszlopok csoportja.  
  
1. Válassza ki a **adattípus** beállítást, ha szüksége más hozzárendelése a kijelölt oszlopokban. Szüksége lehet módosíthatja az adattípust, bizonyos műveletek. Például ha a forrás adatkészlet kezeli a szöveg, számok, módosítania kell azokat egy numerikus adattípusúra matematikai műveletek használata előtt.

    + A támogatott típusok a következők **karakterlánc**, **egész**, **dupla**, **logikai**, és **DateTime**.

    + Ha több oszlopot, telepítenie kell a metaadat-módosítások *összes* kijelölt oszlopok. Például tegyük fel kettő vagy három numerikus oszlopokat választja. Minden karakterlánccá adattípusúra, és nevezze át őket egy művelettel módosíthatja őket. Azonban nem módosítható egy oszlop karakteres adattípusúnak, és a egy másik oszlopot egy lebegőpontos az egész.
  
    + Ha nem ad meg egy új típusú adatokat, az az oszlop metaadatai nem változik.

    + Az oszlop típusa és az értékek módosul a Szerkesztés metaadat-művelet végrehajtása után. Alaphelyzetbe állítja az oszlop adattípusához szerkesztése metaadatok használatával állíthatja helyre az eredeti adattípus bármikor.  

    > [!NOTE]
    > Ha módosítja a bármilyen típusú és szám a **dátum és idő** írja be, hagyja a **dátum és idő formátumban** mező üres. Jelenleg nem lehetséges a cél-adatformátum megadásához.  

1. Válassza ki a **Kategorikus** beállítással azt adhatja meg, hogy a kijelölt oszlopokban szereplő értékek kategóriák kell kezelni.

    Például akkor lehet, hogy 0, 1 és 2 számokat tartalmazó oszlop, de tudja, hogy számok ténylegesen jelenti azt, hogy "Smoker," "Nem-smoker" és "Ismeretlen". Ebben az esetben az oszlopot a kategorikus megjelölése biztosíthatja, hogy a értékeket fogja használni, csak az adatok csoportosításához, és nem numerikus számításokban.
  
1. Használja a **mezők** beállítást, ha meg szeretné változtatni a módon, hogy az Azure Machine Learning a modellek az adatokat használja.

    + **A szolgáltatás**: Ez a beállítás használatával Ez a jelző azt egy olyan oszlop, amely a szolgáltatás oszlopok csak a modulok egyik funkciója. Alapértelmezés szerint az összes oszlop szolgáltatások kezdetben kezeli.  
  
    + **Címke**: Ez a beállítás segítségével jelölje meg a címke, amely más néven az előre jelezhető attribútumot, vagy a cél változó. Számos modulok van szükség, hogy pontosan egy címke oszlop szerepel az adatkészletet.

        Sok esetben az Azure Machine Learning is következtet, hogy egy oszlop osztály címkét tartalmaz. A metaadatok beállításával biztosíthatja, hogy az oszlop megfelelően azonosítja. Ezt a beállítást nem módosítja az adatok értékeit. Csak az, hogy egyes gépi tanulási algoritmusok kezelni az adatok úgy változik.
  
    > [!TIP]
    > Adatok, amelyek nem fér el a következő kategóriákba vannak? Előfordulhat, hogy például, hogy az adatkészlet egyedi azonosítók, amelyeket nem hasznos változókat, például értékeket tartalmaznak. Néha az ilyen azonosítókat az modell használatakor problémákat okozhat.
    >
    > Szerencsére az Azure Machine Learning biztosítja, hogy az adatok, így nem kell törölni az ilyen oszlopokat az adatkészletből. Amikor szüksége van néhány speciális oszlopkészleteket műveletek végrehajtásához, csak távolítsa el az összes többi oszlop ideiglenesen használatával a [Select Columns in Dataset](select-columns-in-dataset.md) modul. Később is egyesíthet az oszlopok vissza az adatkészlet használatával a [oszlopok hozzáadása](add-columns.md) modul.  
  
1. A következő beállítások segítségével törölje az előző beállításokat, és vissza a metaadatokat az alapértelmezett értékeket.  
  
    + **Törölje a szolgáltatás**: Ez a beállítás használatával távolítsa el a szolgáltatás jelzőt.  
  
         Az összes oszlop szolgáltatások kezdetben kezeli. Matematikai műveletek végrehajtása modulok előfordulhat, hogy kell ezt a beállítást használja annak érdekében, hogy megakadályozza, hogy a numerikus oszlopokat, amelyeket az változókat.
  
    + **Egyértelmű címke**: Ez a beállítás segítségével távolítsa el a **címke** a megadott oszlop metaadataiból.  
  
    + **Törölje a pontszám**: Ez a beállítás segítségével távolítsa el a **pontszám** a megadott oszlop metaadataiból.  
  
         Jelenleg nem lehet explicit módon megjelöl egy oszlop az Azure Machine Learning pontszámot. Egyes műveletek azonban egy pontszám belső használatra megjelölt alatt álló oszlop eredményez. Emellett egy egyéni R modult előfordulhat, hogy kimeneti pontszám értékeket.

1. A **új oszlopnevek**, adja meg a kijelölt oszlopot vagy oszlopokat új nevét.  
  
    + Oszlopnevek használható csak az UTF-8 által támogatott karakterek kódolása. Üres karakterláncok, a NULL értékek vagy állhatnak kizárólag tárolóhelyek nevek nem engedélyezett.  
  
    + Több oszlop átnevezéséhez írja be a nevét, az oszlop az indexek sorrendje a vesszővel elválasztott listáját.  
  
    + Az összes kijelölt oszlopok kell nevezhető át. Nem hagyja el vagy hagyja ki az oszlopot.  
  
1. Futtassa a kísérletet.  

## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) az Azure Machine Learning szolgáltatáshoz.
