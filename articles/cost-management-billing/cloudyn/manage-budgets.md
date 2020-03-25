---
title: Cloudyn-költségvetések kezelése az Azure-ban
description: Ennek a cikknek a segítségével gyorsan létrehozhat költségvetéseket a Cloudynben, és megkezdheti a kezelésüket.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: vitavor
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: f2f8fdbf2b470e9f1eb0d82c4c6de230c2a9e78f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79464155"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>Azure-költségvetések kezelése a Cloudynnel

A költségvetések és költségvetés-alapú riasztások beállítása hozzájárul a felhőszabályozás és az elszámoltathatóság javításához. Ennek a cikknek a segítségével gyorsan létrehozhat költségvetéseket a Cloudynben, és megkezdheti a kezelésüket.

Ha nagyvállalati vagy MSP-fiókkal rendelkezik, a hierarchikus költségentitás-struktúrával havi költségvetési kvótákat rendelhet a különböző üzleti egységekhez, részlegekhez vagy más költségentitásokhoz. Ha prémium szintű fiókkal rendelkezik, használhatja a költségvetés-kezelési funkciót, amelyet utána a teljes felhőbeli költésre alkalmaz a rendszer. Minden költségvetés hozzárendelése manuálisan történik.

A hozzárendelt költségvetésekhez kapcsolódóan meghatározhat küszöbérték-riasztásokat a felhasznált költségvetés százalékos aránya alapján, és meghatározhatja az egyes küszöbértékek súlyosságát.

A költségvetési jelentések a hozzárendelt költségvetést jelenítik meg. A felhasználók megtekinthetik, hogy a kiadásaik egy adott időszakban mikor lépik túl a felhasználást, mikor vannak alatta, vagy mikor egyeznek meg azzal. A költségvetési jelentések tetején található **Show/Hide Fields** (Mezők megjelenítése/elrejtése) elemmel tekintheti meg a költségeket, a költségvetést, a halmozott költségeket vagy a teljes költségvetést.

Az Azure Cost Management a Cloudynhez hasonló funkcionalitást kínál. Az Azure Cost Management egy natív Azure költségkezelő megoldás. Segít kezelni a költségvetéseket, exportálni az adatokat, valamint áttekinteni és végrehajtani az optimalizálási javaslatokat pénzmegtakarítás céljából. A Cost Management költségvetéseivel kapcsolatos további információkért tekintse meg a [költségvetések létrehozását és kezelését](../costs/tutorial-acm-create-budgets.md) ismertető cikket.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="create-budgets"></a>Költségvetések létrehozása

Amikor létrehoz egy költségvetést, azt a pénzügyi évre állítja be, és egy adott entitásra vonatkozik.

Költségvetés létrehozása és entitáshoz rendelése:

1. Lépjen a **Costs** &gt; **Cost Management** &gt; **Budget** (Költségek > Költségkezelés > Költségvetés) részre.
2. A Budget Management (Költségvetés-kezelés) oldal **Entities** (Entitások) területén válassza ki az entitást, amelyben a költségvetést létre szeretné hozni.
3. A költségvetési évben válassza ki az évet, amelyhez létre szeretné hozni a költségvetést.
4. Állítson be egy költségvetési értéket minden hónap számára. Ha végzett, kattintson a **Save** (Mentés) gombra.
Ebben a példában 2018 júniusának havi költségvetése 135 000 dollárra lett beállítva. Az év teljes költségvetése 1 615 000,00 dollár.
![Költségvetési oldal létrehozása, amelyen minden hónap számára beállíthatja a költségvetést](./media/manage-budgets/set-budget.png)


Az éves költségvetés fájljának importálása:

1. Az **Actions** (Műveletek) területen válassza az **Export** (Exportálás) lehetőséget egy üres CSV-sablon letöltéséhez, amelyet a költségvetés alapjaként használhat.
2. Töltse ki a CVS-fájlt a költségvetés elemeivel, és mentse a fájlt helyileg.
3. Az **Actions** (Műveletek) területen válassza az **Import** (Importálás) elemet.
4. Jelölje ki a mentett fájlt, majd kattintson az **Ok** gombra.

A kész költségvetés CVS-fájlként történő exportálásához az **Actions** (Műveletek) területen válassza az **Export** (Exportálás) lehetőséget, és töltse le a fájlt.

## <a name="view-budget-in-reports"></a>Költségvetés megtekintése jelentésekben

Miután elkészült, a költségvetés a költségjelentések többségében látható a **Costs** &gt; **Cost Analysis** (Költségek > Költségelemzés) részen, valamint a Cost vs. Budget Over Time (Költség és költségvetés összehasonlítása adott időszakban) jelentésben. Az **Actions** (Műveletek) menüvel ütemezhet is jelentéseket a költségvetés-küszöbértékek alapján.

Egy példa a Cost Analysis (Költségelemzés) jelentésre. A jelentés számítási feladatok és használati típusok alapján jeleníti meg a teljes költségvetést és költséget az év elejétől kezdve.

![Példa a Cost Analysis (Költségelemzés) jelentésre költségvetéssel](./media/manage-budgets/cost-analysis-budget-example.png)

Ebben a példában tételezzük fel, hogy a mai dátum június 22. 2018 júniusának a költsége 71 611,28 dollár a 135 000 dolláros havi költségvetéshez képest. Ez a költség sokkal alacsonyabb a havi költségvetésnél, mert még nyolc napnyi kiadás van hátra a hónap végéig.

A jelentés megtekintésének egy másik módja a halmozott költség és a költségvetés összehasonlítása. A halmozott költségek megtekintéséhez a **Show/Hide Fields** (Mezők megjelenítése/elrejtése) területen válassza az **Accumulated Cost** (Halmozott költség) és **Total Budget** (Teljes költségvetés) elemet. Íme egy példa, amely a halmozott költséget jeleníti meg az év elejétől kezdve.

![Példa halmozott költségre és teljes költségvetésre a Cost vs. Budget Over Time (Költség és költségvetés összehasonlítása adott időszakban) jelentésben](./media/manage-budgets/accumulated-budget.png)

Valamikor a jövőben a halmozott költség meghaladhatja a költségvetést. Ezt egyszerűen megállapíthatja, ha a diagramnézetet _vonal_ típusúra módosítja.

![Vonaldiagramon megjelenített költségvetés a Cost by Months (Költség hónapok szerint) jelentésben](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Költségvetési riasztások létrehozása szűrőkhöz

Az előző példában láthatja, hogy a halmozott költség megközelítette a költségvetést. Létrehozhat automatikus költségvetési riasztásokat, így értesülhet arról, ha a kiadások megközelítik vagy meghaladják a költségvetést. A riasztás alapvetően egy küszöbértékkel rendelkező ütemezett jelentés. A költségvetési riasztási küszöbérték-metrikák az alábbiak:

- Remaining cost vs. budget (Fennmaradó költség a költségvetéshez képest) – egy aktuális értékre vonatkozó küszöbérték megadásához
- Cost percentage vs. budget (Költség százalékos aránya a költségvetéshez képest) – egy százalékos értékre vonatkozó küszöbérték megadásához

Lássunk erre egy példát.

A Cost vs. Budget Over Time (Költség és költségvetés összehasonlítása adott időszakban) jelentésben kattintson az **Actions** (Műveletek) gombra, majd válassza a **Schedule report** (Jelentés ütemezése) lehetőséget. A Threshold (Küszöbérték) lapon válasszon ki egy küszöbérték-metrikát. Például: **Cost percentage vs. budget** (Költség százalékos aránya a költségvetéshez képest). Válasszon ki egy riasztástípust, majd adja meg a költségvetés százalékos értékét. Ha csak egyszer szeretne értesítést kapni, válassza a **Number of consecutive alerts** (Egymást követő riasztások száma) lehetőséget, majd írja be az _1_ értéket. Kattintson a **Save** (Mentés) gombra.

![Költségvetési riasztás létrehozása a Save or Schedule this report (A jelentés mentése vagy ütemezése) lapon](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>További lépések

- Ha még nem végezte el a Cloudyn első oktatóanyagát, olvassa el a [használat és költségek áttekintését](tutorial-review-usage.md) ismertető cikkben.
- Tájékozódjon bővebben a [Cloudynben elérhető jelentésekről](use-reports.md).
