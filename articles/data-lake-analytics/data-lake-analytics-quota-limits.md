---
title: "Az Azure Data Lake Analytics kvótakorlát |} Microsoft Docs"
description: "Megtudhatja, hogyan állítsa be, és növelje a kvótakorlát az Azure Data Lake Analytics (ADLA) esetében."
services: data-lake-analytics
keywords: Azure Data Lake Analytics
documentationcenter: 
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: omidm
ms.openlocfilehash: 957f306ea0e80b5830ad64e5ef06c6d122d9eccc
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="azure-data-lake-analytics-quota-limits"></a>Az Azure Data Lake Analytics kvótakorlát

Megtudhatja, hogyan állítsa be, és növelje a kvótakorlát az Azure Data Lake Analytics (ADLA) esetében. Ezek a korlátozások ismerete előfordulhat, hogy segítenek megérteni a U-SQL projekt viselkedését. Minden kvótakorlát letölthető, így növelve a határok által a számunkra elérése.

## <a name="azure-subscriptions-limits"></a>Azure-előfizetések korlátok

**Előfizetésenként fiókok ADLA maximális száma:** 5

 Ez az előfizetésenként hozhat létre ADLA fiókok maximális számát. Hozzon létre egy hatodik ADLA fiókot kísérli meg, ha egy hiba jelenik "Elérte a maximális száma Data Lake Analytics-fiókok engedélyezett [5] terület előfizetési néven". Ebben az esetben törölje az összes nem használt ADLA fiókot, vagy kapcsolatba velünk által [egy támogatási jegy megnyitásakor](#increase-maximum-quota-limits).

## <a name="adla-account-limits"></a>ADLA korlátai

**Fiókonként (ausztráliai) Analytics egységek maximális száma:** 250

Ez az egyidejűleg futtatható fiókjában ausztráliai maximális számát. A teljes ausztráliai futtatásáért az összes feladat túllépi ezt a korlátozást, ha újabb feladatok automatikusan sorba. Példa:

* Ha csak egy feladat még fut 250 ausztráliai, amikor egy másik feladat azt a feladat-várólistán a rendszer megvárja, amíg az első feladat befejeződik.
* Ha már rendelkezik futó feladatokkal öt és a egyes 50 ausztráliai, amikor 20 igénylő hatodik feladat elküldése a feladat-várólistán arra vár, amíg nincsenek 20 ausztráliai ausztráliai érhető el.

**Fiók száma párhuzamos U-SQL feladatok maximális száma:** 20

Ez az a fiók egyidejűleg futtatható feladatok maximális számát. Ezt az értéket, fent újabb feladatok várólistára automatikusan.

## <a name="adjust-adla-quota-limits-per-account"></a>Állítsa be a kvótakorlát ADLA fiókonként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válasszon ki egy meglévő ADLA fiókot.
3. Kattintson a **Tulajdonságok** elemre.
4. Állítsa be **párhuzamossági** és **egyidejűleg futó feladatainak** az igényeinek.

    ![Azure Data Lake Analytics portál panel](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="increase-maximum-quota-limits"></a>Maximális kvótakorlát növelése

1. Nyissa meg a támogatási kérelmet az Azure portálon.

    ![Azure Data Lake Analytics portál panel](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Azure Data Lake Analytics portál panel](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Adja meg a probléma **kvóta**.
3. Válassza ki a **előfizetés** (Ellenőrizze, hogy az nem a "" próbaelőfizetést).
4. Válassza ki a kvótatípus **Data Lake Analytics**.

    ![Azure Data Lake Analytics portál panel](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. A probléma paneljén ismertetik a kért növekedése korlát **részletek** , miért van szükség a további kapacitást.

    ![Azure Data Lake Analytics portál panel](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Ellenőrizze a kapcsolattartási adatokat, és a támogatási kérelem létrehozása.

A Microsoft nem ellenőrzi kérelmét, és megkísérli a lehető leghamarabb megfeleljen az üzleti igényeknek megfelelően.

## <a name="next-steps"></a>Következő lépések

* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Az Azure Data Lake Analytics kezelése az Azure PowerShell-lel](data-lake-analytics-manage-use-powershell.md)
* [Az Azure Data Lake Analytics-feladatok figyelése és hibaelhárítása az Azure Portal használatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
