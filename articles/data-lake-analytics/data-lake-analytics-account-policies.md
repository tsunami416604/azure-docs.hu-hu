---
title: Az Azure Data Lake Analytics-fiókházirendek kezelése
description: Ismerje meg, hogyan használhatja a fiókházirendeket a Data Lake Analytics-fiók, például a maximális AUs és a maximális feladatok használatának szabályozására.
services: data-lake-analytics
ms.service: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 4689714073047e383a53a04bd0069a8a27afdf9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72966436"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>Az Azure Data Lake Analytics kezelése fiókházirendekkel

A fiókszabályzatok segítségével szabályozhatja, hogyan használják az Azure Data Lake Analytics-fiók erőforrásait. Ezek a szabályzatok lehetővé teszik az Azure Data Lake Analytics használatának költségeit. Például ezekkel a szabályzatokkal megakadályozhatja a váratlan költségkiugrásokat, ha korlátozza, hogy a fiók egyszerre hány Atus-t használhat egyidejűleg.

## <a name="account-level-policies"></a>Fiókszintű házirendek

Ezek a szabályzatok a Data Lake Analytics-fiók összes feladatára vonatkoznak.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>A Data Lake Analytics-fiókban lévő atusok maximális száma
A szabályzat szabályozza a Data Lake Analytics-fiók által használható Elemzési egységek (AUs) teljes számát. Alapértelmezés szerint az érték 250. Ha például ez az érték 250 AUs értékre van állítva, akkor egy feladat fut, amelyhez 250 AUs van hozzárendelve, vagy 10 feladat fut 25 AUs-szal. A további elküldött feladatok várólistára kerülnek, amíg a futó feladatok be nem fejeződnek. A feladatok futtatása után az AUs felszabadul a várólistán lévő feladatok futtatásához.

A Data Lake Analytics-fiókhoz az AUs-ok számának módosítása:

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiókját.
2. Kattintson **a Korlátok és házirendek**elemre.
3. A **Maximális AUs csoportban**mozgassa a csúszkát egy érték kijelöléséhez, vagy írja be az értéket a szövegmezőbe. 
4. Kattintson a **Mentés** gombra.

> [!NOTE]
> Ha az alapértelmezett (250) AUs-nál többre van szüksége, a portálon kattintson a **Súgó+támogatás** gombra támogatási kérelem benyújtásához. A Data Lake Analytics-fiókban elérhető bizonyos számú atus növelhető.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Az egyidejűleg futtatható feladatok maximális száma
Ez a házirend korlátozza, hogy hány feladat futhat egyszerre. Alapértelmezés szerint ez az érték 20.Default, this value is set to 20. Ha a Data Lake Analytics rendelkezik a rendelkezésre álló, új feladatok az ütemezve azonnal fut, amíg a futó feladatok száma eléri a jelen szabályzat értékét. Ha eléri az egyidejűleg futtatható feladatok maximális számát, a további feladatok várólistára kerülnek prioritási sorrendben, amíg egy vagy több futó feladat be nem fejeződik (a rendelkezésre álló AUs-tól függően).

Az egyidejűleg futtatható feladatok számának módosítása:

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiókját.
2. Kattintson **a Korlátok és házirendek**elemre.
3. A **Futó feladatok maximális száma**csoportban mozgassa a csúszkát egy érték kijelöléséhez, vagy írja be az értéket a szövegmezőbe. 
4. Kattintson a **Mentés** gombra.

> [!NOTE]
> Ha az alapértelmezett (20) számú feladatnál többet kell futtatnia, a portálon kattintson a **Súgó+támogatás** gombra támogatási kérelem benyújtásához. A Data Lake Analytics-fiókban egyidejűleg futtatható feladatok száma növelhető.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Mennyi ideig tart meg a feladat metaadatai és erőforrásai? 
Amikor a felhasználók U-SQL-feladatokat futtatnak, a Data Lake Analytics szolgáltatás megőrzi az összes kapcsolódó fájlt. Ezek a fájlok közé tartozik az U-SQL script, a DLL fájlok hivatkozott az U-SQL script, lefordított erőforrások, és a statisztikák. A fájlok az alapértelmezett Azure Data Lake Storage-fiók /system/mappájában találhatók. Ez a házirend határozza meg, hogy mennyi ideig tárolja ezeket az erőforrásokat, mielőtt azok automatikusan törlődnek (az alapértelmezett 30 nap). Ezeket a fájlokat használhatja a hibakereséshez és a jövőbeli újrafuttatott feladatok teljesítményének beállításához.

A feladat metaadatainak és erőforrásainak megtartása:

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiókját.
2. Kattintson **a Korlátok és házirendek**elemre.
3. A **Feladatlekérdezések megtartása napjai**csoportban mozgassa a csúszkát egy érték kijelöléséhez, vagy írja be az értéket a szövegmezőbe.  
4. Kattintson a **Mentés** gombra.

## <a name="job-level-policies"></a>Feladatszintű házirendek

A feladatszintű házirendek lehetővé teszik, hogy szabályozhassa az egyes felhasználók (vagy adott biztonsági csoportok tagjai) által az általuk beküldött feladatokhoz beállított maximális ANUs-t és maximális prioritást. Ez a házirend lehetővé teszi a felhasználók költségeinek szabályozását. Azt is lehetővé teszi, hogy szabályozhatja, hogy az ütemezett feladatok milyen hatással lehetnek az azonos Data Lake Analytics-fiókban futó, magas prioritású termelési feladatokra.

A Data Lake Analytics két szabályzatot kínál, amelyeket a feladat szintjén állíthat be:

* **AU korlát feladatonként:** A felhasználók csak olyan feladatokat küldhetnek el, amelyek ennyi AU-val rendelkeznek. Alapértelmezés szerint ez a korlát megegyezik a fiók maximális AU-korlátjával.
* **Prioritás**: A felhasználók csak olyan feladatokat küldhetnek el, amelyek prioritása kisebb vagy egyenlő ezzel az értékkel. A magasabb szám alacsonyabb prioritást jelez. Alapértelmezés szerint ez a korlát 1-re van állítva, ami a lehető legmagasabb prioritás.

Minden fiókhoz alapértelmezett házirend van beállítva. Az alapértelmezett házirend a fiók összes felhasználójára vonatkozik. További házirendeket hozhat létre adott felhasználókhoz és csoportokhoz. 

> [!NOTE]
> A fiókszintű és a feladatszintű házirendek egyidejűleg érvényesek.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Házirend hozzáadása egy adott felhasználóhoz vagy csoporthoz

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiókját.
2. Kattintson **a Korlátok és házirendek**elemre.
3. A **Feladatbeküldéses korlátok csoportban**kattintson a **Házirend hozzáadása** gombra. Ezután válassza vagy adja meg a következő beállításokat:
    1. **Számítási házirend neve:** Adjon meg egy házirend nevet, hogy emlékeztesse a házirend céljára.
    2. **Felhasználó vagy csoport kiválasztása:** Válassza ki azt a felhasználót vagy csoportot, akire a házirend vonatkozik.
    3. **Állítsa be az AU feladat korlátját:** Állítsa be a kijelölt felhasználóra vagy csoportra vonatkozó AU-korlátot.
    4. **A prioritási korlát beállítása:** Állítsa be a kijelölt felhasználóra vagy csoportra vonatkozó prioritási korlátot.

4. Kattintson az **OK** gombra.

5. Az új házirend az **Alapértelmezett** házirend táblázatban, az **Állásbeküldéses korlátok csoportban**található. 

### <a name="delete-or-edit-an-existing-policy"></a>Meglévő házirend törlése vagy szerkesztése

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiókját.
2. Kattintson **a Korlátok és házirendek**elemre.
3. Az **Állásbeküldéses korlátok csoportban**keresse meg a szerkesztendő szabályzatot.
4.  A **Törlés** és **szerkesztés** beállítások megtekintéséhez kattintson a táblázat `...`jobb szélső oszlopában a gombra.

## <a name="additional-resources-for-job-policies"></a>További források a munkaköri házirendekhez
* [Politika áttekintő blogbejegyzése](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Fiókszintű irányelvek blogbejegyzése](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Feladatszintű irányelvek blogbejegyzése](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Ismerkedés a Data Lake Analytics szolgáltatással az Azure Portal használatával](data-lake-analytics-get-started-portal.md)
* [Az Azure Data Lake Analytics kezelése az Azure PowerShell használatával](data-lake-analytics-manage-use-powershell.md)