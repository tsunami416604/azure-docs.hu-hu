---
title: Az Azure Data Lake Analytics-szabályzatok kezelése
description: Ismerje meg, hogyan házirendek használata a Data Lake Analytics-fiók használatát szabályozza.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 64095f6706bb978cd33b8fe7833fe4e65fc3b0f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813425"
---
# <a name="manage-azure-data-lake-analytics-using-policies"></a>Házirendek használata Azure Data Lake Analytics kezelése

A fiók-szabályzatokkal szabályozható hogyan erőforrások egy Azure Data Lake Analytics-fiókot használja. Ezek a házirendek lehetővé teszik a költségek az Azure Data Lake Analytics használatával. Például ezek a szabályzatok a megakadályozhatja a költségek váratlan adatforgalmi csúcsokhoz, korlátozza a fiók használhatják egyszerre hány AU-t.

## <a name="account-level-policies"></a>Fiókszintű házirendek

A Data Lake Analytics-fiók összes feladat ezeket a szabályzatokat alkalmazni.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>A Data Lake Analytics-fiók au-k maximális száma
A szabályzatok vezérlik az elemzési egység (AU-k), használhatja a Data Lake Analytics-fiók teljes száma. Alapértelmezés szerint az értéke 250. Például, ha az értéke 250 au-k, és a 250 fut egy feladat rendelkezhet az AU-k vagy a 25-ös futó 10 feladatok hozzárendelve au-k minden. További elküldött feladatok várólistára kerülnek, a futó feladatok befejezéséig. Futó feladatok befejezésekor az AU-k felszabadítását regisztrálásához az aszinkron feladatok futtatását.

A Data Lake Analytics-fiók au-k számának módosítása:

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiók.
2. Kattintson a **Tulajdonságok** elemre.
3. A **foglalási egység maximális mérete**, a csúszka segítségével válassza ki a egy értéke vagy a szövegmezőbe írja be az értéket. 
4. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> Ha több, mint az alapértelmezett (250) van szüksége az AU-k, kattintson a portál, **súgó + támogatás** egy támogatási kérést szeretne beküldeni. AU-k a Data Lake Analytics-fiókban rendelkezésre álló növelhető.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Egyidejűleg futtatható feladatok maximális száma
Egy házirend szabályozza, hogy egyszerre hány feladatokat futtathat. Alapértelmezés szerint ez az érték értéke 20. Ha a Data Lake Analytics au-k érhető el, új típusú feladatok vannak ütemezve, addig, amíg a futó feladatok teljes száma eléri a házirend értékét. Ha egyenlege eléri a egyidejűleg futtatható feladatok maximális számát, az egymást követő feladatok várólistára kerülnek prioritási sorrendben mindaddig, amíg egy vagy több futó feladatok elvégzése (attól függően, Ausztrália elérhetőségét).

Egyidejűleg futtatható feladatok számának módosításához:

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiók.
2. Kattintson a **Tulajdonságok** elemre.
3. A **maximális száma a futó feladatok**, a csúszka segítségével válassza ki a egy értéke vagy a szövegmezőbe írja be az értéket. 
4. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> Ha a több alapértelmezett (20) feladatok, a portál futtatásához szükséges, kattintson a **súgó + támogatás** egy támogatási kérést szeretne beküldeni. A Data Lake Analytics-fiók egyidejűleg futtatható feladatok száma növelhető.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Mennyi ideig tartsa feladat-metaadatok és az erőforrásokhoz való 
Amikor a felhasználók a U-SQL-feladatok futtatásához, a Data Lake Analytics szolgáltatás megőrzi az összes kapcsolódó fájlokat. Kapcsolódó fájlok közé tartoznak a U-SQL parancsfájl, a DLL-fájlokat a U-SQL parancsfájl, a lefordított erőforrások és a statisztikák hivatkozik. A fájlok az alapértelmezett Azure Data Lake Storage-fiók /system/ mappájában. Ez az irányelv szabályozza, hogy mennyi ideig tárolja ezeket az erőforrásokat automatikusan törlés előtt (az alapértelmezett érték 30 nap). Ezeket a fájlokat a hibakereséshez és a teljesítmény finomhangolásának feladatok, amely futtatja a későbbiekben lesz újra használható.

Mennyi ideig módosítása, így a feladat-metaadatok és az erőforrások:

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiók.
2. Kattintson a **Tulajdonságok** elemre.
3. A **Feladatlekérdezéseit megőrzése (nap)** , a csúszka segítségével válassza ki a egy értéke vagy a szövegmezőbe írja be az értéket.  
4. Kattintson a **Save** (Mentés) gombra.

## <a name="job-level-policies"></a>Feladat-szintű szabályzatok

Feladat-szintű szabályzatok szabályozhatja a maximális au-k és a maximális prioritás, amely az egyes felhasználók (vagy adott biztonsági csoportok tagjai) állíthatók be a feladatokat, ezek küldenek. Ez a szabályzat lehetővé teszi, hogy vezérelheti a felhasználók által felmerült költségek. Ezenkívül lehetővé teszi, hogy az ütemezett feladatok milyen hatással lehet a ugyanazt a Data Lake Analytics-fiókban futó feladatok magas prioritású éles vezérlő.

A Data Lake Analytics van két szabályzat, amely a feladatok szintjén is megadhatja:

* **Foglalási egység korlátja feladatonként**: Csak a felhasználók elküldhetik a au-k számát kell feladatokat. Alapértelmezés szerint ez a korlátozás megegyezik a fiók AU maximális száma.
* **Prioritás**: Csak a felhasználók elküldhetik feladatok prioritása kisebb vagy egyenlő, erre az értékre. Ha nagyobb prioritást jelent alacsonyabb. Alapértelmezés szerint ez a korlátozás értéke 1, amely a lehető legmagasabb prioritású.

Nincs beállítva minden fiók alapértelmezett szabályzat. Az alapértelmezett szabályzat vonatkozik, a fiók összes felhasználója számára. További házirendek beállíthatja adott felhasználók és csoportok számára. 

> [!NOTE]
> Fiókszintű és feladat-szintű házirendeket alkalmazza egyszerre.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Egy szabályzat egy adott felhasználó vagy csoport hozzáadása

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiók.
2. Kattintson a **Tulajdonságok** elemre.
3. A **feladat küldésére vonatkozó korlátok**, kattintson a **házirend hozzáadása** gombra. Válassza ki, vagy adja meg a következő beállításokat:
    1. **Számítási szabályzat neve**: Adja meg a szabályzat nevét, amely emlékezteti a házirend céljának.
    2. **Felhasználó vagy csoport kiválasztása**: Válassza ki a felhasználót vagy csoportot a házirend vonatkozik.
    3. **A feladat AU-korlátjának beállítása**: Állítsa be a foglalási egység korlátja, amely a kiválasztott felhasználó vagy csoport vonatkozik.
    4. **A prioritási korlátjának beállítása**: Állítsa be a kiválasztott felhasználó vagy csoport a prioritású korlátozás.

4. Kattintson az **OK** gombra.

5. Az új házirend megjelenik a **alapértelmezett** házirend tábla alatt **feladat küldésére vonatkozó korlátok**. 

### <a name="delete-or-edit-an-existing-policy"></a>Törölheti vagy szerkesztheti a meglévő házirend

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiók.
2. Kattintson a **Tulajdonságok** elemre.
3. A **feladat küldésére vonatkozó korlátok**, keresse meg a szerkeszteni kívánt szabályzatot.
4.  Megtekintheti a **törlése** és **szerkesztése** lehetőségek tábla, a jobb szélső oszlopban kattintson `...`.

## <a name="additional-resources-for-job-policies"></a>További források a feladat-szabályzatok
* [Csoportházirend áttekintése blogbejegyzés](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Fiókszintű házirendek blogbejegyzés](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Feladat-szintű szabályzatok blogbejegyzés](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Data Lake Analytics használatának első lépései az Azure portal használatával](data-lake-analytics-get-started-portal.md)
* [Az Azure Data Lake Analytics kezelése az Azure PowerShell használatával](data-lake-analytics-manage-use-powershell.md)

