---
title: Azure költség felügyeleti költségvetések kezeléséhez |} Microsoft Docs
description: Ez a cikk segít hozhat létre és kezelhet költség felügyeleti költségek teszik ki.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/25/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 9d6bf29909393846ec17a1bcc210fb989efd7f99
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938406"
---
# <a name="manage-budgets"></a>Költségvetések kezeléséhez

Költségvetések létrehozása és a keret-alapú súgó a felhő irányítás és elszámolási kötelezettségéről szóló javításához riasztást küld. Ez a cikk segít gyorsan hozzon létre költségvetések, és azokat a felügyeleti költségek kezelésének megkezdéséhez.

Ha egy vállalati vagy MSP fiókkal rendelkezik, használhatja a hierarchikus költség entitás struktúra havi költségvetési kvóták hozzárendelése a különböző üzleti egységek, szervezeti egységek vagy bármely más költség entitás. Prémium szintű fiókkal rendelkezik, ha alkalmazva lesz a teljes körű felhőalapú kiadások költségvetési felügyeleti funkciót is használhatja. Minden költségvetések manuálisan hozzá vannak rendelve.

Hozzárendelt költségvetések alapján, a felhasznált költségvetést százaléka alapján küszöbérték riasztások beállítását, és adja meg a küszöbértékek súlyossága.

Jelentésekben a hozzárendelt keret megjelenítése. Felhasználók megtekinthetik, ha a kiadásokat keresztül, vagy a saját használat idővel rendelkező névértéken. Ha bejelöli **megjelenítése mezők** költségvetési jelentés tetején, megtekintheti a költség, a költségvetési, a halmozott költség vagy a teljes költségvetést.

## <a name="create-budgets"></a>Költségvetések létrehozása

Költségvetés létrehozása, amikor a pénzügyi év meg, és egy adott entitás vonatkozik.

A keret létrehozása, és rendelje hozzá egy entitás:

1. Navigáljon a **költségek** &gt; **Költségkezelésére** &gt; **költségvetési**.
2. A keret kezelése lap alatt **entitások**, válassza ki az entitást, ha szeretne létrehozni a keret.
3. Válassza ki az év, ha szeretne létrehozni a költségvetési keret év.
4. Havonta a költségvetési érték beállítása. Amikor elkészült, kattintson a **mentése**.
Ebben a példában a havi keretét. június 2018 $135,000 van beállítva. A teljes költségvetést, az évre $1,615,000.00.
![Költségvetés létrehozása](./media/manage-budgets/set-budget.png)


Az éves költségvetés fájl importálása:

1. A **műveletek**, jelölje be **exportálása** egy üres, a költségvetési a alapjául használandó CSV sablon letöltése.
2. Adja meg a CSV-fájl, a költségvetési megadásával, és mentse helyileg.
3. A **műveletek**, jelölje be **importálási**.
4. Válassza ki a mentett fájlt, és kattintson a **OK**.

A befejezett költségvetési exportálása CSV-fájlként, a **műveletek**, jelölje be **exportálása** letölteni a fájlt.

## <a name="view-budget-in-reports"></a>A jelentések költségvetés megtekintése

Befejezése után a költségvetést a legtöbb költség jelentésekben látható a **költségek** &gt; **költség Analysis** és a költségek vs. Keret felett idő jelentés. Azt is beállíthatja a jelentések használatával költségvetési küszöbértékek alapján **műveletek**.

Íme egy példa az költség elemzést. Azt illusztrálja, a teljes költségvetést és munkaterhelés-és használati által az év kezdete óta.

![Példa költség elemző jelentés költségvetés](./media/manage-budgets/cost-analysis-budget-example.png)

Ebben a példában fel, hogy az aktuális dátum: június 22. 2018. június költsége $71,611.28 $135,000 a havi költségvetési képest. A költség nem sokkal alacsonyabb, mint a havi költségvetést, mert még vannak nyolc követő hónap végéig kiadásokat.

A jelentés megtekintéséhez egy másik módja, ha halmozott költség vs tekintse meg a költségvetést. Összesített költségek a megjelenítéséhez **megjelenítése mezők**, jelölje be **halmozott költség** és **teljes keret**. Itt látható egy példa a halmozott költség megjelenítő év kezdete óta.

![Halmozott költségvetés](./media/manage-budgets/accumulated-budget.png)

Némi várakozás a jövőben a halmozott költség meghaladhatja a költségvetést. Könnyebben láthatja, hogy a diagram nézet módosításakor a _sor_ típusa.

![A keret vonaldiagram látható](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Hozzon létre egy szűrő költségvetési riasztásokat

Az előző példában láthatja, hogy a halmozott költség válaszadásra a keret. Automatikus költségvetési riasztásokat hozhat létre, így még értesíti, ha a kiadásokat megközelítések, vagy meghaladja a költségvetést. A riasztás alapvetően, amelyek küszöbértéke ütemezett jelentés. Keret riasztási küszöbérték mérőszámok közé tartozik:

- Fennmaradó költség és költségvetés – a pénznem értékének küszöbérték megadása
- Költség százalékos és költségvetés – a százalékos érték küszöbérték megadása

Nézzük például.

A költségek vs. Idő jelentés keresztül költségvetési, kattintson a **műveletek** majd **ütemezni a jelentést**. A küszöbérték lapján jelöljön ki egy küszöbértéket metrikát. Például **százalékos vs költségvetés**. Válasszon egy riasztási típus, és írja be a költségvetés százalékos érték. Ha csak egyszer tájékoztatást kaphat, jelölje be **egymást követő riasztások száma** és írja be _1_. Kattintson a **Save** (Mentés) gombra.

![Keret riasztás](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>További lépések

- Ha még nem fejezte első oktatóanyaga, amely költség-kezelésre, olvassa el a [tekintse át a használati és költségek](https://docs.microsoft.com/en-us/azure/cost-management/tutorial-review-usage).
- További információ a [költség felügyeleti-ban rendelkezésre álló](use-reports.md).
