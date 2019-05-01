---
title: A klasszikus riasztások az Azure monitorban áttelepítése a önkéntes migrálást eszközzel
description: Ismerje meg, hogyan kell áttelepíteni a klasszikus riasztási szabályok az önkéntes áttelepítési eszköz használata.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 55e6b515328df635a423e0ecf736b03e17a90b8f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698424"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Az önkéntes áttelepítési eszköz használata a klasszikus riasztási szabályok áttelepítése

Mint [azt korábban bejelentettük](monitoring-classic-retirement.md), klasszikus riasztások az Azure monitorban vannak vezetve a július 2019. A migrálási eszköz érhető el az ügyfelek számára, akik klasszikus riasztási szabályok és ki szeretne aktiválása áttelepítési magukat az Azure Portalon. Ez a cikk bemutatja, hogyan használható az áttelepítési eszköz önkéntesen áttelepíteni a klasszikus riasztási szabályok, az automatikus áttelepítése a július 2019 megkezdése előtt.

## <a name="benefits-of-new-alerts"></a>Új riasztások előnyei

Klasszikus riasztások cserélik új, egyesített az Azure monitorban riasztásokat. Az új riasztások platformra a következő előnyökkel jár:

- A többdimenziós metrikák különböző figyelmeztető [számos további Azure-szolgáltatások](alerts-metric-near-real-time.md#metrics-and-dimensions-supported).
- Az új metrika figyelmezteti az támogatási [több erőforrás riasztási szabályok](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) , amely nagy mértékben csökkentheti számos szabályok kezelése járó többletterhelést.
- Az egységes értesítési mechanizmust, amely támogatja:
  - [Műveletcsoportok](action-groups.md), a moduláris értesítési mechanizmus, amely az összes új riasztástípusok (a mérőszám, a napló és a tevékenységnaplóhoz) együttműködve.
  - Új értesítési mechanizmusok, például SMS, hang és ITSM-összekötő.
- A [egységes riasztási kezelőfelület](alerts-overview.md) számos lehetőséget kínál az összes riasztás a különböző jel (a mérőszám, a napló és a tevékenységnaplóhoz) adatait egy helyen.

## <a name="before-you-migrate"></a>Az áttelepítés előtt

Az áttelepítési folyamat alakítja át klasszikus riasztási szabályok új, egyenértékű riasztási szabályok és művelet csoportokat hoz létre. Előkészítés vegye figyelembe a következőket:

- Az értesítési adattartalom formátuma és a az API-k létrehozásához és kezeléséhez új riasztási szabályok eltérnek a klasszikus riasztási szabályok mert támogatják a több funkciót. [Ismerje meg, hogyan készíti elő az áttelepítés](alerts-prepare-migration.md).

- Néhány klasszikus riasztási szabályok nem telepíthetők át, az eszköz használatával. [Ismerje meg, mely szabályok nem telepíthető át, és mi a teendő velük](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated).

    > [!NOTE]
    > Az áttelepítési folyamat nem érinti a klasszikus riasztási szabályok értékelését. Ezek továbbra is futtassa, és értesítés küldése, amíg ezek már át, és az Új riasztási szabályok érvénybe léptetéséhez.

## <a name="how-to-use-the-migration-tool"></a>Az áttelepítési eszköz használata

Az áttelepítés az Azure Portalon a klasszikus riasztási szabályok aktiválásához kövesse az alábbi lépéseket:

1. A [az Azure portal](https://portal.azure.com)válassza **figyelő**.

1. Válassza ki **riasztások**, majd válassza ki **riasztási szabályok kezelése** vagy **klasszikus riasztások**.

1. Válassza ki **áttelepítése új szabályok** , nyissa meg a migrálási kezdőlapját. Ezen a lapon látható az összes előfizetés és a migrálási állapot listáját:

    ![áttelepítési-alkotóelemeit](media/alerts-migration/migration-landing.png "szabályok áttelepítése")

    Az eszköz használatával telepíthetők át minden előfizetés jelölt **migrálásra kész**.

    > [!NOTE]
    > Az áttelepítési eszköz bevezetéséről szakaszában, az összes olyan előfizetést, amely a klasszikus riasztási szabályok használata. A bevezetés korai szakaszaiban néhány előfizetés nem áll készen a migrálás megjelölve jelenhet meg.

1. Egy vagy több előfizetést, majd válassza ki és **megtekintheti, migrálási**.

    Az eredményül kapott oldal a klasszikus riasztási szabályok egy előfizetéshez egyszerre áttelepített részleteit jeleníti meg. Lehetőség kiválasztásával **ennél az előfizetésnél az áttelepítés részleteinek letöltése** részletes információkkal szolgál egy CSV formátumú.

    ![migrálás – előzetes verzió](media/alerts-migration/migration-preview.png "áttelepítési előzetes verzió")

1. Adjon meg egy vagy több e-mail cím értesíti a migrálás állapota. E-mailt fog kapni, az áttelepítés befejezésekor, vagy ha minden művelet van szükség.

1. Válassza ki **indítsa el áttelepítési**. Olvassa el a művelet megerősítését kérő párbeszédpanelen megjelenő információkat, és győződjön meg arról, hogy készen áll az áttelepítési folyamat elindítása.

    > [!IMPORTANT]
    > Miután elindította egy előfizetés áttelepítése, nem tudja szerkeszteni vagy létrehozni az adott előfizetéshez tartozó klasszikus riasztási szabályok. Ez a korlátozás biztosítja, hogy a klasszikus riasztási szabályok módosítása nélkül elveszett új szabályainak migráláskor. Bár nem tudják módosítani a klasszikus riasztási szabályok, azok továbbra is továbbra is futtatni, és adja meg a riasztások mindaddig, amíg már migrálták. Az áttelepítés befejezése után az előfizetéshez tartozó, klasszikus riasztási szabályok többé nem használható.

    ![migrálás megerősítéséhez](media/alerts-migration/migration-confirm.png "megerősítése a migrálás megkezdése")

1. Ha az áttelepítés akkor fejeződött be, vagy ha a teendője, kap egy e-mailt a korábban megadott címek. Ellenőrizheti az állapotát a portál kezdőlapjának alkotóelemeit áttelepítési rendszeres időközönként.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Miért jelenik meg nem áll készen a migrálás előfizetésemet?

Az áttelepítési eszköz bevezetéséről ügyfeleknek fázisban történik. A korai szakaszában, a legtöbb vagy az összes előfizetés jelöléssel **nem áll készen a migrálás**. Mid – április azonban az összes előfizetés kell tenni, migrálásra kész.

Előfizetés válik, migrálásra kész, az előfizetés tulajdonosa kap egy e-mail üzenet szerint, hogy az eszköz érhető el. Tartsa szemmel ehhez az üzenethez.

### <a name="who-can-trigger-the-migration"></a>Ki is aktiválhatja a migrálás?

Az az előfizetés szintjén hozzájuk rendelt figyelési közreműködő szerepkörrel rendelkező felhasználók képesek az áttelepítés indításához. [További információ a szerepköralapú hozzáférés-vezérlés az áttelepítési folyamat](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Mennyi ideig az áttelepítés tart?

Áttelepítés a legtöbb előfizetések egy óra alatt nem fejeződik. Azt is nyomon követheti, az áttelepítés lefolyásának áttelepítési kezdőlapján. Az áttelepítés során biztos lehet abban, hogy a riasztások továbbra is futnak a klasszikus riasztások rendszer vagy az újat.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Mi a teendő, ha a futtatási hiba az áttelepítés során?

Tekintse meg a [hibaelhárítási útmutató](alerts-understand-migration.md#common-problems-and-remedies) kapcsolatos áttelepítés során előfordulhat, hogy között. Az áttelepítés elvégzéséhez, nincs szükség semmilyen műveletre, ha értesítést is küld az eszköz beállításakor megadott e-mail címre.

## <a name="next-steps"></a>További lépések

- [Az áttelepítés előkészítése](alerts-prepare-migration.md)
- [Az áttelepítési eszköz működésének megismerése](alerts-understand-migration.md)
