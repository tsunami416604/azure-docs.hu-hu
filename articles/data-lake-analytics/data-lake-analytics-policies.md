---
title: Azure Data Lake Analytics házirendjeinek kezelése
description: Tudnivalók a házirendek használata a Data Lake Analytics-fiók használatát szabályozza.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 2c074b1a75c5bfef07ffb90e719bd3247a474e63
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623944"
---
# <a name="manage-azure-data-lake-analytics-using-policies"></a>Azure Data Lake Analytics-szabályzatok kezelése

Fiók házirendekkel szabályozható hogyan erőforrásokat egy Azure Data Lake Analytics-fiókot használni. Ezek a házirendek lehetővé teszik az Azure Data Lake Analytics használatának költségét. Például ezekkel a szabályzatokkal megakadályozhatja váratlan költség teljesítményt, ha a fiók egyidejűleg használhatják hány ausztráliai korlátozza.

## <a name="account-level-policies"></a>Fiók szintű szabályzatok

Ezek a házirendek a Data Lake Analytics-fiók levő összes feladatnak vonatkozik.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>A Data Lake Analytics-fiók ausztráliai maximális száma
A szabályzatok vezérlik az Analytics-egységek (ausztráliai) használható a Data Lake Analytics-fiók teljes száma. Alapértelmezés szerint a értéke 250. Például, ha a változó értéke 250 ausztráliai, 250 fut egy feladat lehet ausztráliai vagy 25 futtató 10 feladatok rendelt minden ausztráliai. További feladatok tartalmazó várólistára kerülnek, a futó feladatok befejezéséig. Futó feladat befejeződése után ausztráliai felszabadítását feliratkozott az aszinkron feladatok futtatását.

A Data Lake Analytics-fiók ausztráliai számának módosításához:

1. Az Azure-portálon lépjen a Data Lake Analytics-fiókhoz.
2. Kattintson a **Tulajdonságok** elemre.
3. A **maximális ausztráliai**, a csúszkával kiválaszthat egy értéket, vagy a mezőben adja meg az értéket. 
4. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> Ha több, mint az alapértelmezett (250) kell ausztráliai, a portálon kattintson **súgó + támogatás** támogatási kérelmet küldeni. A Data Lake Analytics-fiókja elérhető ausztráliai száma növelhető.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Az egyidejűleg futtatható feladatok maximális számát
A házirend szabályozza, hogy hány feladatok futtathatók egyszerre. Ez az érték alapértelmezés szerint 20 értéke. Ha a Data Lake Analytics ausztráliai elérhető, új feladatok ütemezésére addig, amíg a futó feladatok teljes száma eléri ezt a házirendet az érték. Elérte az egyidejűleg futtatható feladatok maximális száma, amikor további feladat várólistára prioritási sorrendben mindaddig, amíg legalább egy futó feladat befejeződik (attól függően, hogy AU rendelkezésre állás).

Az egyidejűleg futtatható feladatok számát módosítása:

1. Az Azure-portálon lépjen a Data Lake Analytics-fiókhoz.
2. Kattintson a **Tulajdonságok** elemre.
3. A **maximális számát a futó feladatok**, a csúszkával kiválaszthat egy értéket, vagy a mezőben adja meg az értéket. 
4. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> Ha a több alapértelmezett (20) feladatok, a portál futtatásához szükséges, kattintson a **súgó + támogatás** támogatási kérelmet küldeni. A Data Lake Analytics-fiók egyidejűleg futtatható feladatok száma növelhető.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Mennyi ideig tartsa feladat metaadatok és az erőforrásokhoz való 
Amikor a felhasználók a U-SQL feladatok futtatása, a Data Lake Analytics szolgáltatás megőrzi az összes kapcsolódó fájlt. Kapcsolódó fájlok közé tartoznak a U-SQL parancsfájl, a DLL-fájlok a U-SQL-parancsfájl, a lefordított erőforrások és a statisztika hivatkozik. A fájlok az alapértelmezett Azure Data Lake-tárfiókra /system/ mappájában. Ez az irányelv szabályozza, hogy mennyi ideig tárolja ezeket az erőforrásokat automatikusan törlés előtt (az alapértelmezett érték 30 nap). Ezeket a fájlokat is használhatja, a hibakeresés, valamint a teljesítményének hangolása, amely a későbbiekben lesz Újrafuttatja feladatok.

Mennyi ideig átállítása feladat metaadatok és erőforrások:

1. Az Azure-portálon lépjen a Data Lake Analytics-fiókhoz.
2. Kattintson a **Tulajdonságok** elemre.
3. A **Feladatlekérdezéseit megőrizni kívánt napok**, a csúszkával kiválaszthat egy értéket, vagy a mezőben adja meg az értéket.  
4. Kattintson a **Save** (Mentés) gombra.

## <a name="job-level-policies"></a>Feladat szintű szabályzatok

A feladat szintű házirendek szabályozhatja a maximális ausztráliai és a legnagyobb prioritású virtuális gép, amely az egyes felhasználók (vagy a meghatározott biztonsági csoportok tagjai) feladatok, ugyanúgy lehet beállítani. Ez a házirend lehetővé teszi a felhasználók felmerült kapcsolatos költségek szabályozását. Ezenkívül lehetővé teszi a hatást, amelyet az ütemezett feladatok lehet magas prioritású termelési feladatokhoz ugyanazt a Data Lake Analytics-fiókot a futó vezérlő.

A Data Lake Analytics van két házirend, amely a feladat szinten állíthatja be:

* **AU felső határ az egyes feladatok**: csak a küldje el, hogy ez a szám ausztráliai kell feladatok. Alapértelmezés szerint ez a korlátozás megegyezik a AU maximális száma a fiókot.
* **Prioritás**: csak a küldje el, amely kisebb vagy egyenlő az érték a prioritású feladatok. A nagyobb szám azt jelzi, hogy a kisebb prioritással. Alapértelmezés szerint ezt a határt értéke 1, amely a lehető legmagasabb prioritású.

Nincs minden fiókhoz beállított alapértelmezett házirend. Az alapértelmezett házirendet a fiók minden felhasználójára vonatkoznak. További házirendjeinek beállítása meghatározott felhasználókhoz és csoportokhoz. 

> [!NOTE]
> Fiók szintű és a feladat szintű házirendeket alkalmazza egyszerre.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Egy szabályzat egy adott felhasználó vagy csoport hozzáadása

1. Az Azure-portálon lépjen a Data Lake Analytics-fiókhoz.
2. Kattintson a **Tulajdonságok** elemre.
3. A **feladat elküldése korlátok**, kattintson a **házirend hozzáadása** gombra. Ezután válassza ki vagy adja meg a következő beállításokat:
    1. **Házirend neve számítási**: Adja meg a házirend nevét, a házirend céljának arra az esetre.
    2. **Válassza ki a felhasználó vagy csoport**: válassza ki a felhasználó vagy csoport a házirend vonatkozik.
    3. **A feladat AU-korlátjának beállítása**: állítsa be a kijelölt felhasználót vagy csoportot a AU korlátozás.
    4. **A prioritás-korlátjának beállítása**: állítsa be a kijelölt felhasználót vagy csoportot a prioritás korlátozás.

4. Kattintson az **OK** gombra.

5. Az új házirend megjelenik a **alapértelmezett** házirend a tábla **feladat elküldése korlátok**. 

### <a name="delete-or-edit-an-existing-policy"></a>Törölje vagy egy meglévő házirend szerkesztése

1. Az Azure-portálon lépjen a Data Lake Analytics-fiókhoz.
2. Kattintson a **Tulajdonságok** elemre.
3. A **feladat elküldése korlátok**, keresse meg a szerkeszteni kívánt házirendet.
4.  Hogy a **törlése** és **szerkesztése** beállítások, a tábla a jobb oldali oszlopban kattintson `...`.

## <a name="additional-resources-for-job-policies"></a>További erőforrásokat a feladat házirendek
* [Házirend által írt blogbejegyzés áttekintése](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Fiók szintű szabályzatok által írt blogbejegyzés](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Feladat szintű szabályzatok által írt blogbejegyzés](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Ismerkedés a Data Lake Analytics az Azure portál használatával](data-lake-analytics-get-started-portal.md)
* [Azure Data Lake Analytics kezelése az Azure PowerShell használatával](data-lake-analytics-manage-use-powershell.md)

