---
title: Az Azure-ban a Cloudyn költségvetések kezeléséhez |} A Microsoft Docs
description: Ez a cikk segít hozhat létre és kezelhet a Cloudyn költségvetése.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: ''
ms.openlocfilehash: 92236547e2100ab57f4ec6b4953f6c61ec87892d
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275504"
---
# <a name="manage-budgets"></a>Költségvetések kezelése

Költségvetés létrehozása és a költségvetés-alapú súgó a felhő cégirányítási és felelősségre vonhatóság riasztást küld. Ez a cikk segítséget nyújt a létre gyorsan költségvetéshez, és azokat a Cloudyn kezelésének megkezdéséhez.

Ha egy vállalati vagy MSP-fiókkal rendelkezik, használhatja a hierarchikus költség entitásstruktúrát havi költségvetés kvótákat rendelhet hozzá a különböző üzleti egységek, részlegek vagy bármely más költségentitás. Ha egy prémium szintű fiókkal rendelkezik, használhatja a költségvetés management szolgáltatásra, a rendszer ezután alkalmazza a teljes felhőalapú kiadások. Az összes költségvetés manuálisan hozzá vannak rendelve.

Hozzárendelt költségvetése alapján, a költségvetés felhasznált aránya alapján küszöbérték riasztásokat állíthat be, és minden egyes küszöbérték súlyosságát határozza meg.

Költségvetés szóló jelentésekben megtekintheti a hozzárendelt költségvetést. Felhasználók megtekinthetik, ha a költségkeret-beállítási keresztül, alatt, vagy a használat idővel rendelkező névértéken. Ha bejelöli **mezők megjelenítése/elrejtése** költség, a költségvetés, a összesített költségeit vagy a teljes fennmaradó is megtekintheti a költségvetés jelentés tetején.

Az Azure Cost Management a Cloudynhez hasonló funkcionalitást kínál. Az Azure Cost Management egy natív Azure költségkezelő megoldás. Segít kezelni a költségvetéseket, exportálni az adatokat, valamint áttekinteni és végrehajtani az optimalizálási javaslatokat pénzmegtakarítás céljából. A Cost Management költségvetése kapcsolatos további információkért lásd: [létrehozása és kezelése költségvetése](tutorial-acm-create-budgets.md).

## <a name="create-budgets"></a>Hozzon létre költségvetése

Költségvetési létrehozásakor állítja be, a pénzügyi évben, és a egy adott entitásra vonatkozik.

Költségvetés létrehozása, és rendelje hozzá egy entitás:

1. Navigáljon a **költségek** &gt; **Cost Management** &gt; **költségvetés**.
2. A költségvetés felügyelet lapon alatt **entitások**, válassza ki az entitást, ahol szeretné létrehozni a költségvetést.
3. A költségvetési évben válassza ki az év, ahol szeretné létrehozni a költségvetést.
4. Minden hónapban a költségvetés érték beállítása. Ha elkészült, kattintson a **mentése**.
Ebben a példában a 2018 június havi költségvetését $135,000 értéke. A teljes költségvetés az év $1,615,000.00.
![Költségvetés létrehozása](./media/manage-budgets/set-budget.png)


Az éves költségvetés-fájl importálása:

1. Alatt **műveletek**válassza **exportálása** letölteni egy üres CSV-sablont a költségvetés a alapjaként használni.
2. Töltse ki a költségvetés és a CSV-fájlt, és mentse helyileg.
3. A **műveletek**válassza **importálás**.
4. Válassza ki a mentett fájlt, és kattintson a **OK**.

Az elkészült költségvetés alatt exportálhatja egy CSV-fájl **műveletek**válassza **exportálása** letölteni a fájlt.

## <a name="view-budget-in-reports"></a>Költségvetés-jelentések megtekintése

Amikor elkészült, a költségvetés alapján a legtöbb költségjelentésben látható **költségek** &gt; **költségelemzés** és a Cost vs. Költségvetés időalapú jelentést. Jelentések használata költségvetési küszöbértékek alapján is ütemezheti **műveletek**.

Íme egy példa a Költségelemzési jelentést. A teljes költségvetés és a számítási feladatok és a használati típusok költséget mutatja az év kezdete óta.

![Példa Költségelemzési jelentést a költségvetés](./media/manage-budgets/cost-analysis-budget-example.png)

Ebben a példában feltételeztük az aktuális dátum későbbi, június 22. 2018 június költsége $71,611.28 $135,000 havi költségvetését képest. A költségek továbbra is vannak a hónap végéig kiadások követő nyolc azért sokkal alacsonyabb, mint a havi költségvetést.

A jelentés megtekintéséhez másik módja, és összesített költségeit és tekintse meg a költségvetést. Összesített költségek alatt megtekintéséhez **mezők megjelenítése/elrejtése**válassza **összesített költségeit** és **teljes költségvetés**. Íme egy példa az összesített költségeit az év kezdete óta.

![Halmozott költségvetés](./media/manage-budgets/accumulated-budget.png)

Valamikor a jövőben az összesített költségeit meghaladhatja a költségvetést. Könnyebben láthatja, hogy ha módosítja a diagram nézet a _sor_ típusa.

![Vonaldiagram látható költségvetés](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Szűrő költségvetés riasztások létrehozása

Az előző példában láthatja, hogy a összesített költségeit a költségvetés válaszadásra. Automatikus költségvetés riasztásokat hozhat létre, hogy Ön értesítést megközelítések költségeit, vagy meghaladja a költségvetést. Alapvetően a riasztás az ütemezett jelentések egy küszöbértékkel. Költségvetés riasztási küszöbérték metrikák a következők:

- Fennmaradó költség és költségvetés – a pénznem érték küszöbértéket megadása
- Költségszázalék vagy költségvetési – egy érték arányának küszöbértéke megadása

Lássunk erre egy példát.

A a Cost VS-ben. Idő a jelentés felett költségvetési, kattintson a **műveletek** majd **jelentés ütemezése**. A küszöbérték lapon válassza ki egy küszöbértéket metrikát. Ha például **költségvetés százalékos vs**. Válassza ki a riasztási típusát, és adja meg a költségvetés százalékos érték. Ha szeretne kaphat, csak egyszer, válassza ki a **egymást követő riasztások számát** és írja be _1_. Kattintson a **Save** (Mentés) gombra.

![Költségvetési figyelmeztetés](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>További lépések

- Ha a Cloudyn már még nem végezte el az első oktatóanyaga, olvassa el, a [tekintse át a használat és költségek](tutorial-review-usage.md).
- Tudjon meg többet a [elérhető a Cloudyn-jelentések](use-reports.md).
