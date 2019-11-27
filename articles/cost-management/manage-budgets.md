---
title: Cloudyn-költségvetések kezelése az Azure-ban
description: Ez a cikk segítséget nyújt a létre gyorsan költségvetéshez, és azokat a Cloudyn kezelésének megkezdéséhez.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 9d7d0e049d3c35aab56145beb94c8e41e56c5785
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219103"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>A Cloudyn Azure költségvetése kezelése

Költségvetés létrehozása és a költségvetés-alapú súgó a felhő cégirányítási és felelősségre vonhatóság riasztást küld. Ez a cikk segítséget nyújt a létre gyorsan költségvetéshez, és azokat a Cloudyn kezelésének megkezdéséhez.

Ha egy vállalati vagy MSP-fiókkal rendelkezik, használhatja a hierarchikus költség entitásstruktúrát havi költségvetés kvótákat rendelhet hozzá a különböző üzleti egységek, részlegek vagy bármely más költségentitás. Ha egy prémium szintű fiókkal rendelkezik, használhatja a költségvetés management szolgáltatásra, a rendszer ezután alkalmazza a teljes felhőalapú kiadások. Az összes költségvetés manuálisan hozzá vannak rendelve.

Hozzárendelt költségvetése alapján, a költségvetés felhasznált aránya alapján küszöbérték riasztásokat állíthat be, és minden egyes küszöbérték súlyosságát határozza meg.

Költségvetés szóló jelentésekben megtekintheti a hozzárendelt költségvetést. Felhasználók megtekinthetik, ha a költségkeret-beállítási keresztül, alatt, vagy a használat idővel rendelkező névértéken. Ha a költségvetési jelentés tetején a **mezők megjelenítése/elrejtése** lehetőséget választja, megtekintheti a költségeket, a költségvetést, a halmozott költségeket vagy a teljes költségvetést.

Az Azure Cost Management a Cloudynhez hasonló funkcionalitást kínál. Az Azure Cost Management egy natív Azure költségkezelő megoldás. Segít kezelni a költségvetéseket, exportálni az adatokat, valamint áttekinteni és végrehajtani az optimalizálási javaslatokat pénzmegtakarítás céljából. A Cost Management költségvetésével kapcsolatos további információkért lásd: [költségvetések létrehozása és kezelése](tutorial-acm-create-budgets.md).

## <a name="create-budgets"></a>Hozzon létre költségvetése

Költségvetési létrehozásakor állítja be, a pénzügyi évben, és a egy adott entitásra vonatkozik.

Költségvetés létrehozása, és rendelje hozzá egy entitás:

1. Navigáljon a **költségek** &gt; **Cost Management** &gt; **költségvetést**.
2. A költségvetés kezelése lap **entitások**területén válassza ki azt az entitást, amelyben létre kívánja hozni a költségvetést.
3. A költségvetési évben válassza ki az év, ahol szeretné létrehozni a költségvetést.
4. Minden hónapban a költségvetés érték beállítása. Ha elkészült, kattintson a **Mentés**gombra.
Ebben a példában a 2018 június havi költségvetését $135,000 értéke. A teljes költségvetés az év $1,615,000.00.
![hozzon létre egy költségvetési oldalt, amelyben minden hónapra vonatkozóan költségvetést állít be](./media/manage-budgets/set-budget.png)


Az éves költségvetés-fájl importálása:

1. A **műveletek**területen válassza az **Exportálás** lehetőséget egy üres CSV-sablon letöltéséhez, amelyet a költségvetés alapjaként kíván használni.
2. Töltse ki a költségvetés és a CSV-fájlt, és mentse helyileg.
3. A **műveletek**területen válassza az **Importálás**lehetőséget.
4. Válassza ki a mentett fájlt, majd kattintson **az OK**gombra.

Ha a kész költségvetést CSV-fájlként szeretné exportálni, a **műveletek**területen válassza az **Exportálás** lehetőséget a fájl letöltéséhez.

## <a name="view-budget-in-reports"></a>Költségvetés-jelentések megtekintése

Ha elkészült, a költségkeret a legtöbb Cost-jelentésben a **költségek** &gt; a **Cost Analysis** és a Cost és a költségkeret időbeli változása című jelentésben látható. A jelentések a költségvetési küszöbértékek alapján is ütemezhetők a **műveletek**használatával.

Íme egy példa a Költségelemzési jelentést. A teljes költségvetés és a számítási feladatok és a használati típusok költséget mutatja az év kezdete óta.

![Példa Költségelemzési jelentést a költségvetés](./media/manage-budgets/cost-analysis-budget-example.png)

Ebben a példában feltételeztük az aktuális dátum későbbi, június 22. 2018 június költsége $71,611.28 $135,000 havi költségvetését képest. A költségek továbbra is vannak a hónap végéig kiadások követő nyolc azért sokkal alacsonyabb, mint a havi költségvetést.

A jelentés megtekintéséhez másik módja, és összesített költségeit és tekintse meg a költségvetést. A halmozott költségek megtekintéséhez a **mezők megjelenítése/elrejtése**területen válassza a **halmozott költség** és a **teljes költségvetés**lehetőséget. Íme egy példa az összesített költségeit az év kezdete óta.

![Példa a költségek és a költségkeret összesített költségére és a költségvetés időbeli alakulására – jelentés](./media/manage-budgets/accumulated-budget.png)

Valamikor a jövőben az összesített költségeit meghaladhatja a költségvetést. Könnyebben láthatja, hogy ha megváltoztatja a diagram nézetet a _sor_ típusára.

![Költségvetés hónapok jelentés díja vonaldiagram látható](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Szűrő költségvetés riasztások létrehozása

Az előző példában láthatja, hogy a összesített költségeit a költségvetés válaszadásra. Automatikus költségvetés riasztásokat hozhat létre, hogy Ön értesítést megközelítések költségeit, vagy meghaladja a költségvetést. Alapvetően a riasztás az ütemezett jelentések egy küszöbértékkel. Költségvetés riasztási küszöbérték metrikák a következők:

- Fennmaradó költség és költségvetés – a pénznem érték küszöbértéket megadása
- Költségszázalék vagy költségvetési – egy érték arányának küszöbértéke megadása

Lássunk erre egy példát.

A költség és a költségkeret időbeli alakulása jelentésben kattintson a **műveletek** , majd az **ütemezett jelentés**elemre. A küszöbérték lapon válassza ki egy küszöbértéket metrikát. Például a **Cost százalék vs költségvetés**. Válassza ki a riasztási típusát, és adja meg a költségvetés százalékos érték. Ha csak egyszer szeretne értesítést kapni, válassza **az egymást követő riasztások száma** lehetőséget, majd írja be az _1_értéket. Kattintson a **Save** (Mentés) gombra.

![A Mentés vagy a jelentés be ütemezést a költségvetés riasztás létrehozása](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>További lépések

- Ha még nem végezte el az első oktatóanyagot a Cloudyn, olvassa el a [használat és a költségek áttekintése](tutorial-review-usage.md)című cikk lépéseit.
- További információ a [Cloudyn-ben elérhető jelentésekről](use-reports.md).
