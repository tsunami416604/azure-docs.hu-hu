---
title: Kvóták és korlátozások az Azure Data Lake Analytics beállítása
description: Megtudhatja, hogyan módosíthatja, vagy növelje a kvóták és korlátok a fiókok az Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonwhowell
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 4629b52f3b2c9e351ddc2a68a40c5178a9a73950
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048255"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Kvóták és korlátozások az Azure Data Lake Analytics beállítása

Megtudhatja, hogyan módosíthatja, vagy növelje a kvóták és -korlátok a fiókok az Azure Data Lake Analytics (ADLA). Ezeket a korlátokat, hogy segíthetik az U-SQL-feladat működésének megértését. Az összes kvótakorlát helyreállítható, így növelheti a maximális korlát az Azure támogatási szolgálatával.

## <a name="azure-subscriptions-limits"></a>Az Azure-előfizetések korlátai

**ADLA maximális száma előfizetésenként és régiónként fiókok:** 5

Ha a hatodik ADLA-fiók létrehozásához, megjelenik egy hibaüzenet "Elérte a maximális Data Lake Analytics fiókjainak engedélyezett számát (5) régióban előfizetés neve alatt található". 

Ha nem ezt a korlátot, megpróbálhatja ezeket a beállításokat:
* Ha a megfelelő, válassza ki egy másik régióba
* Lépjen kapcsolatba az Azure-támogatás által [támogatási jegy megnyitása](#increase-maximum-quota-limits) kérje egy kvótájának növelését.

## <a name="default-adla-account-limits"></a>Alapértelmezett ADLA korlátai

**Elemzési egység (AU-k) maximális száma fiókonként:** 32

Ez az egyidejűleg futtatható a fiók au-k maximális száma. Ha a futó összes feladat au-k teljes száma meghaladja ezt a korlátot, újabb feladatok automatikusan várólistára kerülnek. Példa:

* Ha csak egy feladat és a 32 fut au-k, amikor egy második küld feladat, a feladat-várólistában várakozik az első feladat befejezéséig.
* Ha már rendelkezik futó négy feladatot, és 8 tárolófürtök au-k, a 8 igénylő ötödik feladat elküldésekor az AU-k a feladat-várólistában lévő, megvárja, amíg nincsenek 8 au-k érhető el.

**Elemzési egység (AU-k) maximális száma feladatonként:** 32

Ez az alapértelmezett maximális számának au-k minden egyes feladat rendelhető a fiókjában. Ezt a határt több rendelt feladatok rendszer elutasítja, kivéve, ha a küldő érintett egy számítási házirend (feladat beküldése limit), amely több au-k száma alapján történik. Ez az érték felső határértéke a fiók a foglalási egység korlátja.

**Egyidejű U-SQL feladatok száma fiókonként maximális számát:** 20

Ez a fiókjában található egyidejűleg futtatható feladatok maximális számát. Ez az érték feletti újabb feladatok várólistára kerülnek automatikusan.

## <a name="adjust-adla-account-limits"></a>ADLA korlátozások beállítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válasszon ki egy meglévő ADLA-fiókot.
3. Kattintson a **Tulajdonságok** elemre.
4. Az értékek az **foglalási egység maximális mérete**, **futó feladatok maximális száma**, és **feladat küldésére vonatkozó korlátok** az igényeinek.

## <a name="increase-maximum-quota-limits"></a>Kvóta felső határát korlátok növelése

Azure korlátairól további információt talál a a [szolgáltatásspecifikus Azure korlátairól dokumentáció](../azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Nyisson egy támogatási kérést az Azure Portalon.

    ![Az Azure Data Lake Analytics-portálon](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Az Azure Data Lake Analytics-portálon](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. A probléma típusaként válassza **kvóta**.
3. Válassza ki a **előfizetés** (Győződjön meg arról, hogy ez ne a "próbaverziós" előfizetés).
4. Válassza ki a kvóta típusa **Data Lake Analytics**.

    ![Az Azure Data Lake Analytics-portálon](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. A probléma oldalon ismertetik a kért növelés mértéke korlátját; **részletek** , miért érdemes az extra kapacitásra van szüksége.

    ![Az Azure Data Lake Analytics-portálon](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Ellenőrizze a kapcsolattartási adatokat, és a támogatási kérelem létrehozása.

A Microsoft ellenőrzi kérelmét, és megpróbálja a lehető leghamarabb megfeleljen az üzleti igényeknek megfelelően.

## <a name="next-steps"></a>További lépések

* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Az Azure Data Lake Analytics kezelése az Azure PowerShell-lel](data-lake-analytics-manage-use-powershell.md)
* [Az Azure Data Lake Analytics-feladatok figyelése és hibaelhárítása az Azure Portal használatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
