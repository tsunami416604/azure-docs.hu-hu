---
title: Az Azure Monitor riasztási szabályainak áttelepítése
description: Ismerje meg, hogyan használhatja az önkéntes áttelepítési eszközt a klasszikus riasztási szabályok áttelepítéséhez.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: ab5c16995a2d2bad6e44f0f9d1187ca3d66be1b6
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114253"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>A klasszikus riasztási szabályok áttelepítéséhez használja az önkéntes áttelepítési eszközt

Ahogy [azt korábban bejelentették](monitoring-classic-retirement.md), az Azure Monitor klasszikus riasztásai 2019 szeptemberében (eredetileg 2019 júliusa) megszűnnek. Az Azure Portalon elérhető egy áttelepítési eszköz azoknak az ügyfeleknek, akik klasszikus riasztási szabályokat használnak, és akik maguk szeretnék elindítani az áttelepítést. Ebből a cikkből megtudhatja, hogy az áttelepítési eszközzel miként telepítheti önkéntesen a klasszikus riasztási szabályokat, mielőtt az automatikus áttelepítés 2019 szeptemberében megkezdődik.

> [!NOTE]
> A migrációs eszköz bevezetésének késedelmi ideje miatt a klasszikus riasztások áttelepítésének nyugdíjazási dátumát 2019. június 30-án, az eredetileg bejelentett időponttól [2019. augusztus 31-ig meghosszabbították.](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)

## <a name="benefits-of-new-alerts"></a>Az új figyelmeztető jelzések előnyei

A klasszikus riasztásokat új, egységes riasztás váltja fel az Azure Monitorban. Az új riasztási platform a következő előnyökkel jár:

- Számos többdimenziós metrikáról riasztást adhat meg [sokkal több Azure-szolgáltatásesetében.](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)
- Az új metrikariasztások támogatják a [több erőforrásra vonatkozó riasztási szabályokat,](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) amelyek jelentősen csökkentik a számos szabály kezelésének többletterhelését.
- Az egységes értesítési mechanizmus, amely támogatja:
  - [Műveletcsoportok](action-groups.md), egy moduláris értesítési mechanizmus, amely együttműködik az összes új riasztási típussal (metrika, napló és tevékenységnapló).
  - Új értesítési mechanizmusok, például SMS, hang és ITSM-csatlakozó.
- Az [egyesített riasztási felület](alerts-overview.md) a különböző jelek (metrika, napló és tevékenységnapló) összes riasztását egy helyre hozza.

## <a name="before-you-migrate"></a>Az áttelepítés előtt

Az áttelepítési folyamat a klasszikus riasztási szabályokat új, egyenértékű riasztási szabályokká alakítja, és műveletcsoportokat hoz létre. Az előkészítés során vegye figyelembe a következő pontokat:

- Az értesítési hasznos adatformátum és az új riasztási szabályok létrehozásához és kezeléséhez szükséges API-k eltérnek a klasszikus riasztási szabályoktól, mivel több funkciót támogatnak. [További információ az áttelepítésre való felkészülésről.](alerts-prepare-migration.md)

- Néhány klasszikus riasztási szabály nem telepíthető át az eszköz használatával. [Megtudhatja, hogy mely szabályok nem telepíthetők át, és mi a teendő velük.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)

    > [!NOTE]
    > Az áttelepítési folyamat nem befolyásolja a klasszikus riasztási szabályok kiértékelését. Továbbra is futni fognak, és riasztásokat küldenek, amíg át nem telepítik őket, és az új riasztási szabályok érvénybe nem lépnek.

## <a name="how-to-use-the-migration-tool"></a>A migrálási eszköz használata

A klasszikus riasztási szabályok áttelepítésének elindításához az Azure Portalon kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com)válassza a **Figyelő**lehetőséget.

1. Válassza **a Riasztások**lehetőséget, majd a **Riasztási szabályok kezelése** vagy a Klasszikus riasztások **megtekintése**lehetőséget.

1. Válassza **az Áttelepítés új szabályokra** lehetőséget az áttelepítés céllapjára való ugráshoz. Ezen a lapon az összes előfizetés és az áttelepítési állapot uk listája látható:

    ![migráció-kirakodás](media/alerts-migration/migration-landing.png "Szabályok áttelepítése")

    Az eszköz használatával áttelepíthető összes előfizetés **áttelepítésre készként**van megjelölve.

    > [!NOTE]
    > Az áttelepítési eszköz fokozatosan vezet be a klasszikus riasztási szabályokat használó összes előfizetéshez. A bevezetés korai fázisaiban előfordulhat, hogy egyes előfizetések megjelölt nem készen áll az áttelepítésre.

1. Jelöljön ki egy vagy több előfizetést, majd válassza **az Áttelepítés előnézete**lehetőséget.

    Az eredményül kapott oldal a klasszikus riasztási szabályok részleteit jeleníti meg, amelyek egyszerre egy előfizetésre lesznek áttelepítve. Az előfizetés **áttelepítési részleteinek letöltése** lehetőséget is választhatja, hogy csv formátumban kapja meg a részleteket.

    ![áttelepítés-előnézet](media/alerts-migration/migration-preview.png "Áttelepítés előnézetének megtekintése")

1. Adjon meg egy vagy több e-mail címet, amely értesítést kap az áttelepítés állapotáról. E-mailben kapja meg, ha az áttelepítés befejeződött, vagy ha bármilyen műveletre van szükség.

1. Válassza **az Áttelepítés indítása**lehetőséget. Olvassa el a megerősítést kérő párbeszédpanelen megjelenő információkat, és ellenőrizze, hogy készen áll-e az áttelepítési folyamat elindítására.

    > [!IMPORTANT]
    > Miután kezdeményezte az áttelepítést egy előfizetéshez, nem fogja tudni megtekinteni vagy létrehozni a klasszikus riasztási szabályokat az adott előfizetéshez. Ez a korlátozás biztosítja, hogy a klasszikus riasztási szabályok módosításai ne vesszenek el az új szabályokra való áttelepítés során. Bár a klasszikus riasztási szabályok nem módosíthatók, azok továbbra is futni fognak, és riasztásokat biztosítanak, amíg át nem telepítették őket. Miután az előfizetés áttelepítése befejeződött, már nem használhatja a klasszikus riasztási szabályokat.

    ![áttelepítés-megerősítés](media/alerts-migration/migration-confirm.png "Áttelepítés indításának megerősítése")

1. Ha az áttelepítés befejeződött, vagy ha beavatkozásra van szükség, e-mailt kap a korábban megadott címeken. Az állapotát a portál áttelepítési céllapján is ellenőrizheti.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Miért nem áll készen az előfizetésem az áttelepítésre?

Az áttelepítési eszköz fázisokban kerül bevezetésre az ügyfelek számára. A korai fázisokban előfordulhat, hogy a legtöbb vagy az összes előfizetés **nem áll készen az áttelepítésre.** 

Amikor egy előfizetés készen áll az áttelepítésre, az előfizetés tulajdonosa e-mailben értesítést kap arról, hogy az eszköz elérhető. Tartsd szemmel ezt az üzenetet.

### <a name="who-can-trigger-the-migration"></a>Ki indíthatja el az áttelepítést?

Azok a felhasználók, akik az előfizetés szintjén hozzárendelt figyelő közreműködői szerepkört rendelik hozzájuk, elindíthatják az áttelepítést. [További információ az áttelepítési folyamat szerepköralapú hozzáférés-vezérléséről.](alerts-understand-migration.md#who-can-trigger-the-migration)

### <a name="how-long-will-the-migration-take"></a>Mennyi ideig tart az áttelepítés?

Az áttelepítés a legtöbb előfizetés esetében egy órán belül befejeződik. Az áttelepítés előrehaladását az áttelepítés céloldalán követheti nyomon. Az áttelepítés során győződjön meg arról, hogy a riasztások továbbra is futnak a klasszikus riasztási rendszerben vagy az újban.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Mi a teendő, ha problémába ütközöm az áttelepítés során?

Az áttelepítés során felmerülő problémákkal kapcsolatos segítségért tekintse meg a [hibaelhárítási útmutatót.](alerts-understand-migration.md#common-problems-and-remedies) Ha az áttelepítés befejezéséhez bármilyen műveletre van szükség, értesítést kap az eszköz beállításakor megadott e-mail címeken.

## <a name="next-steps"></a>További lépések

- [Előkészületek a migráláshoz](alerts-prepare-migration.md)
- [A migrálási eszköz működésének ismertetése](alerts-understand-migration.md)
