---
title: Azure Data Lake Analytics fiók-házirendek kezelése
description: Ismerje meg, hogyan használhatja a fiók-házirendeket egy Data Lake Analytics-fiók használatának vezérléséhez, például a maximális au-és a maximális feladatokhoz.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: how-to
ms.date: 04/30/2018
ms.openlocfilehash: cf64424d1d422e599585b76fc068c940f9311b05
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87127706"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>Azure Data Lake Analytics kezelése a fiókházirend használatával

A fiókházirend segítségével szabályozhatja a Azure Data Lake Analytics-fiókok erőforrásainak használatát. Ezek a házirendek lehetővé teszik a Azure Data Lake Analytics használatának költségeit. Ezekkel a szabályzatokkal például megakadályozhatja a váratlan költségeket, ha korlátozza, hogy a fiók hány au-t képes egyidejűleg használni. # # fiók szintű házirendek

Ezek a házirendek a Data Lake Analytics-fiókban lévő összes feladatra vonatkoznak. # # # az AU-t egy Data Lake Analytics-fiókban a szabályzat szabályozza a Data Lake Analytics-fiók által használható elemzési egységek teljes számát. Alapértelmezés szerint az érték 250-re van állítva. Ha például ez az érték 250 au-ra van állítva, akkor egy, a 250 AUs-t futtató feladat, illetve 10, egyenként 25 au-t futtató feladat is lehet. Az elküldött további feladatok várólistára kerülnek, amíg a futó feladatok be nem fejeződik. A feladatok futtatásakor az AUs felszabadul a várólistára helyezett feladatok futtatására.

A Data Lake Analytics-fiókhoz tartozó AUs számának módosítása:

1. A Azure Portal lépjen a Data Lake Analytics-fiókra.
2. Kattintson **a korlátozások és házirendek**elemre.
3. Az **AUs maximális**értéke alatt mozgassa a csúszkát egy érték kiválasztásához, vagy írja be az értéket a szövegmezőbe. 
4. Kattintson a **Mentés** gombra.

   > [!NOTE]
   > Ha az alapértelmezett (250) AUs-nál többre van szüksége, a portálon kattintson a **Súgó + támogatás** lehetőségre a támogatási kérelem elküldéséhez. A Data Lake Analytics-fiókban rendelkezésre álló AUs-szám növelhető.

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Egyszerre futtatható feladatok maximális száma
Ez a szabályzat korlátozza, hogy hány feladat futhat egyszerre. Alapértelmezés szerint ez az érték 20. Ha a Data Lake Analytics rendelkezik az AUs-vel, az új feladatok azonnal futnak, amíg a futó feladatok teljes száma eléri a szabályzat értékét. Ha eléri a párhuzamosan futtatható feladatok maximális számát, a következő feladatok a prioritási sorrendben lesznek várólistán, amíg egy vagy több futó feladat be nem fejeződik (a rendelkezésre álló AUs-tól függően).

A párhuzamosan futtatható feladatok számának módosítása:

1. A Azure Portal lépjen a Data Lake Analytics-fiókra.
2. Kattintson **a korlátozások és házirendek**elemre.
3. A **futó feladatok maximális száma**alatt mozgassa a csúszkát egy érték kiválasztásához, vagy írja be az értéket a szövegmezőbe. 
4. Kattintson a **Mentés** gombra.

   > [!NOTE]
   > Ha az alapértelmezett (20) számú feladatot többet kell futtatnia, a portálon kattintson a **Súgó + támogatás** lehetőségre a támogatási kérelem elküldéséhez. A Data Lake Analytics-fiókban egyszerre futtatható feladatok száma növelhető.

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Mennyi ideig tart a feladatok metaadatainak és erőforrásainak megőrzése 
Ha a felhasználók a U-SQL-feladatokat futtatják, a Data Lake Analytics szolgáltatás megtartja az összes kapcsolódó fájlt. Ezek a fájlok tartalmazzák a U-SQL-parancsfájlt, a U-SQL-parancsfájlban hivatkozott DLL-fájlokat, a lefordított erőforrásokat és a statisztikát. A fájlok az alapértelmezett Azure Data Lake Storage fiók/System/mappájában találhatók. Ez a házirend azt szabályozza, hogy a rendszer mennyi ideig tárolja ezeket az erőforrásokat az automatikus Törlésük előtt (az alapértelmezett érték 30 nap). Ezeket a fájlokat hibakereséshez használhatja, valamint a jövőben újrafuttatott feladatok teljesítményének finomhangolásához.

Annak módosítása, hogy mennyi ideig tart a feladatok metaadatainak és erőforrásainak megőrzése:

1. A Azure Portal lépjen a Data Lake Analytics-fiókra.
2. Kattintson **a korlátozások és házirendek**elemre.
3. A **feladatok lekérdezésének megtartásához a napok**alatt mozgassa a csúszkát egy érték kiválasztásához, vagy írja be az értéket a szövegmezőbe.  
4. Kattintson a **Mentés** gombra.

## <a name="job-level-policies"></a>Job szintű házirendek

A feladat szintű házirendek lehetővé teszik az egyes felhasználók (vagy adott biztonsági csoportok tagjai) számára a beküldött feladatok maximális értékének és maximális prioritásának szabályozását. Ez a szabályzat lehetővé teszi a felhasználók által felmerülő költségek szabályozását. Emellett azt is szabályozhatja, hogy az ütemezett feladatok milyen hatással lehetnek a magas prioritású üzemi feladatokra, amelyek ugyanabban a Data Lake Analytics-fiókban futnak.

Data Lake Analytics két házirendet adhat meg, amelyek a feladat szintjén állíthatók be: * **au-korlát/feladat**: a felhasználók csak ennyi AU-val rendelkező feladatokat küldhetnek be. Alapértelmezés szerint ez a korlát megegyezik a fiókhoz tartozó maximális AU-korláttal.
* **Prioritás**: a felhasználók csak olyan feladatokat küldhetnek be, amelyek prioritása ennél az értéknél kisebb vagy egyenlő. A magasabb érték azt jelzi, hogy alacsonyabb prioritású. Alapértelmezés szerint ez a korlát 1 értékre van állítva, amely a lehető legnagyobb prioritás.

Minden fiókhoz alapértelmezett házirend van beállítva. Az alapértelmezett házirend a fiók összes felhasználójára érvényes. Az egyes felhasználókhoz és csoportokhoz további szabályzatokat is létrehozhat. 

> [!NOTE]
> A fiók szintű házirendek és a projektfeladat-szintű házirendek egyszerre érvényesek.

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Házirend hozzáadása egy adott felhasználóhoz vagy csoporthoz

1. A Azure Portal lépjen a Data Lake Analytics-fiókra.
2. Kattintson **a korlátozások és házirendek**elemre.
3. A **feladatok küldési korlátai**területen kattintson a **házirend hozzáadása** gombra. Ezután válassza ki vagy adja meg a következő beállításokat:
    1. **Számítási szabályzat neve**: írja be a szabályzat nevét, hogy emlékeztesse a szabályzat céljára.
    2. **Felhasználó vagy csoport kiválasztása**: válassza ki azt a felhasználót vagy csoportot, amelyre ez a szabályzat vonatkozik.
    3. **A feladatok au-korlátjának beállítása**: állítsa be a kiválasztott felhasználóra vagy csoportra vonatkozó au-korlátot.
    4. **A prioritási korlát beállítása**: állítsa be a kijelölt felhasználóra vagy csoportra érvényes prioritási korlátot.

4. Kattintson az **OK** gombra.

5. Az új házirend megjelenik az **alapértelmezett** házirend táblában a **feladatok beküldési korlátai**területen. # # # Meglévő szabályzat törlése vagy szerkesztése

1. A Azure Portal lépjen a Data Lake Analytics-fiókra.
2. Kattintson **a korlátozások és házirendek**elemre.
3. A **feladatok küldési korlátai**területen keresse meg a szerkeszteni kívánt szabályzatot.
4.  A **törlési** és **szerkesztési** beállítások megjelenítéséhez a tábla jobb oldali oszlopában kattintson a elemre `...` . # # további erőforrások a feladatok házirendjéhez
* [Szabályzat – áttekintés blogbejegyzés](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Fiók szintű házirendek blogbejegyzés](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Job szintű házirendek blogbejegyzés](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)# # következő lépések

* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [A Data Lake Analytics használatának első lépései a Azure Portal](data-lake-analytics-get-started-portal.md)
* [Azure Data Lake Analytics kezelése Azure PowerShell használatával](data-lake-analytics-manage-use-powershell.md)