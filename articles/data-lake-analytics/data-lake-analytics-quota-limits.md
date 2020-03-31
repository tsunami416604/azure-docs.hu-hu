---
title: Kvóták és korlátok módosítása az Azure Data Lake Analyticsben
description: Ismerje meg, hogyan módosíthatja és növelheti a kvótákat és a korlátokat az Azure Data Lake Analytics (ADLA) fiókokban.
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonwhowell
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c1d7c8cc4b50682a74ac88b2113f0d44ebc55199
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644715"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Kvóták és korlátok módosítása az Azure Data Lake Analyticsben

Ismerje meg, hogyan módosíthatja és növelheti a kvótát és a korlátokat az Azure Data Lake Analytics (ADLA) fiókokban. Ezeknek a korlátoknak a ismerete segít megérteni az U-SQL-feladat viselkedését. Minden kvótakorlát puha, így növelheti a maximális korlátokat az Azure-támogatással való kapcsolatfelvételsel.

## <a name="azure-subscriptions-limits"></a>Az Azure-előfizetések korlátai

**Az ADLA-fiókok maximális száma előfizetésenként régiónként:** 5

Ha megpróbál létrehozni egy hatodik ADLA-fiókot, akkor a következő hibaüzenet jelenik meg: "Elérte a régióban engedélyezett Data Lake Analytics-fiókok maximális számát (5) az előfizetés neve alatt".

Ha túl szeretne lépni ezen a korláton, próbálkozzon az alábbi lehetőségekkel:
* válasszon másik régiót, ha az alkalmas
* lépjen kapcsolatba az Azure-támogatással [egy támogatási jegy megnyitásával,](#increase-maximum-quota-limits) és kérjen kvótanövelést.

## <a name="default-adla-account-limits"></a>Alapértelmezett ADLA-fiókkorlátok

**Az Analytics-egységek maximális száma fiókonként:** 250, alapértelmezett 32

Ez a fiókban egyidejűleg futtatható AUs-ok maximális száma. Ha az összes feladatban futó AUs-ok teljes száma meghaladja ezt a korlátot, az újabb feladatok automatikusan várólistára kerülnek. Példa:

* Ha csak egy feladat fut 32 AUs-szal, amikor egy második feladatot küld, akkor a feladatvárólistában fog várni, amíg az első feladat befejeződik.
* Ha már négy feladat fut, és mindegyik 8 AUs-t használ, amikor egy ötödik, 8 AUs-t igényel, a feladatvárólistában várakozik, amíg 8 AUs elérhető.

    ![Az Azure Data Lake Analytics korlátai és kvótaoldala](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Az Analytics-egységek maximális száma feladatonként:** 250, alapértelmezett 32

Ez az egyes feladatokkal a fiókjában hozzárendelhető a maximális számú AT.This is the maximum number of AUs that each individual job can be assigned in your account. Az ezt a korlátnál nagyobb hozadékokat a rendszer elutasítja, kivéve, ha a beküldőre hatással van egy számítási szabályzat (feladatbenyújtási korlát), amely feladatonként több AUs-t biztosít számukra. Ennek az értéknek a felső határa a számla AU-korlátja.

**Az egyidejű U-SQL feladatok maximális száma fiókonként:** 20

Ez a fiókban egyidejűleg futtatható feladatok maximális száma. Ezen érték felett az újabb feladatok automatikusan várólistára kerülnek.

## <a name="adjust-adla-account-limits"></a>ADLA-fiókkorlátainak módosítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válasszon egy meglévő ADLA-fiókot.
3. Kattintson a **Tulajdonságok** elemre.
4. Állítsa be az **AUs maximális**értékét, **a futó feladatok maximális számát**és a **feladatbeküldési korlátokat** az igényeinek megfelelően.

## <a name="increase-maximum-quota-limits"></a>Maximális kvótakorlátok növelése

Az Azure-korlátozásokkal kapcsolatos további információkért az [Azure-szolgáltatásspecifikus korlátozások dokumentációjában](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits)talál.

1. Nyisson meg egy támogatási kérelmet az Azure Portalon.

    ![Az Azure Data Lake Analytics portáloldala](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Az Azure Data Lake Analytics portáloldala](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Válassza ki a Kvóta típusú **problématípust.**
3. Válassza ki az **Előfizetést** (győződjön meg róla, hogy nem "próbaverziós" előfizetés).
4. Válassza ki a **Data Lake Analytics kvótatípust.**

    ![Az Azure Data Lake Analytics portáloldala](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. A probléma oldalon ismertesse a kért emelési korlátot **a Részletek** segítségével, hogy miért van szüksége erre az extra kapacitásra.

    ![Az Azure Data Lake Analytics portáloldala](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Ellenőrizze a kapcsolattartási adatokat, és hozza létre a támogatási kérelmet.

A Microsoft áttekinti a kérését, és a lehető leghamarabb megpróbálja kielégíteni üzleti igényeit.

## <a name="next-steps"></a>További lépések

* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Az Azure Data Lake Analytics kezelése az Azure PowerShell-lel](data-lake-analytics-manage-use-powershell.md)
* [Az Azure Data Lake Analytics-feladatok figyelése és hibaelhárítása az Azure Portal használatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
