---
title: Az Azure monitorban az önkéntes áttelepítési eszköz használata a klasszikus riasztások áttelepítése
description: Ismerje meg, hogyan önkéntes áttelepítési eszköz használata a klasszikus riasztási szabályok áttelepíteni.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 58c664beee942fe7115c7fff38a039c23bed6ac3
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632263"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Az önkéntes áttelepítési eszköz használata a klasszikus riasztási szabályok áttelepítése

Mint [azt korábban bejelentettük](monitoring-classic-retirement.md), klasszikus riasztások az Azure monitorban vannak vezetve a július 2019. Az áttelepítési eszköz való áttelepítés önkéntesen érhető el az Azure Portalon, és ügyfeleink, akik klasszikus riasztási szabályok jelennek meg. Ez a cikk végigvezeti az áttelepítési eszköz használatával önkéntesen áttelepíteni a klasszikus riasztási szabályok, az automatikus áttelepítése a július 2019 megkezdése előtt.

## <a name="benefits-of-new-alerts"></a>Új riasztások előnyei

Klasszikus riasztások cserélik új egyesített riasztások kezelése az Azure monitorban. Az új riasztások platformra a következő előnyökkel jár:

- A többdimenziós metrikák a különböző riasztási [számos további Azure-szolgáltatások](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)
- Új metrika figyelmezteti az támogatási [több erőforrás riasztási szabályok](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) amely nagy mértékben csökkentheti számos szabályok kezelése járó többletterhelést.
- Egységes értesítési mechanizmus
  - [Műveletcsoportok](action-groups.md) moduláris értesítési mechanizmusok, amelyek minden tárolóhelytípussal működik új riasztás (mérőszám, a napló és a tevékenységnaplóhoz.)
  - Akkor is képes kihasználni az új értesítési mechanizmusok, például SMS-, hang- és ITSM-összekötő
- A [egységes riasztási kezelőfelület](alerts-overview.md) számos lehetőséget kínál az összes riasztás a különböző jel (metrika, tevékenység napló és a naplófájlok) adatait egy helyen

## <a name="before-you-migrate"></a>Az áttelepítés előtt

A migrálás részeként a klasszikus riasztási szabályok egyenértékű új riasztási szabályok alakítja, és Műveletcsoportok jönnek létre.

- Az értesítési adattartalom formátuma, valamint az API-k létrehozásához és kezeléséhez új riasztási szabályok eltér a klasszikus riasztási szabályok az módon támogatják a több funkciót. Ismerje meg, [az áttelepítés előkészítése](alerts-prepare-migration.md).

- Néhány klasszikus riasztási szabályok nem telepíthetők át, hogy az eszköz használatával. [Ismerje meg, mely szabályok nem migrálható, és megtudhatja, hogyan telepítheti át őket a](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated).

    > [!NOTE]
    > Áttelepítési folyamat nem befolyásolja a klasszikus riasztási szabályok értékelését. Továbbra is futtassa, és küldje a riasztásokat, amíg az áttelepítésük és megkezdtem az Új riasztási szabályok.


## <a name="how-to-use-the-migration-tool"></a>Az áttelepítési eszköz használata

Az alábbi eljárás ismerteti, hogyan lehet elindítani a migrálás a klasszikus riasztási szabályok az Azure Portalon:

1. A [az Azure portal](https://portal.azure.com), kattintson a **figyelő**.

2. Kattintson a **riasztások** majd kattintson a **riasztási szabályok kezelése** vagy **klasszikus riasztások**.

3. Kattintson a **áttelepítése új szabályok** , nyissa meg a migrálási kezdőlapját. Ezen a lapon látható az összes előfizetés és az áttelepítési azok állapotának listája.

    ![áttelepítési-alkotóelemeit](media/alerts-migration/migration-landing.png "szabályok áttelepítése")

4. Összes olyan előfizetést, amely az eszköz használatával telepíthetők át lesznek megjelölve **migrálásra kész**.

    > [!NOTE]
    > Az áttelepítési eszköz bevezetéséről szakaszában, az összes olyan előfizetést, amely a klasszikus riasztási szabályok használata. Bevezetés a korai szakaszában, az egyes előfizetéseket, nem áll készen a migrálásra jelenhet meg.

5. Válassza ki egy vagy több előfizetést, majd kattintson a **áttelepítési előzetes verzió**

6. Ezen a lapon láthatja a klasszikus riasztási szabályok egy előfizetéshez egyszerre áttelepített részleteit. Emellett **ennél az előfizetésnél az áttelepítés részleteinek letöltése** egy .csv formátumban.

    ![migrálás – előzetes verzió](media/alerts-migration/migration-preview.png "áttelepítési előzetes verzió")

7. Adjon meg egy vagy több **e-mail-címek** értesíti a migrálás állapota. E-mailt küldünk, amikor a migrálás befejezése vagy művelet van szükség.

8. Kattintson a **indítsa el áttelepítési**. Olvassa el a megerősítő párbeszédpanelen megjelenő információkat, és győződjön meg arról, ha készen áll az áttelepítési folyamat elindításához.

    >[!IMPORTANT]
    > Miután elindította az áttelepítési folyamat-előfizetéssel, nem lesz az előfizetéshez tartozó klasszikus riasztási szabályok Szerkesztés/létrehozás. Azonban a klasszikus riasztási szabályok továbbra is fut, és a riasztással, amíg az áttelepítésük során. Ez a klasszikus riasztási szabályok és az áttelepítés során létrehozott új szabályok között a minőség biztosítása érdekében. Az áttelepítés befejezése után az előfizetéshez tartozó, többé nem használható klasszikus riasztási szabályok.

    ![migrálás megerősítéséhez](media/alerts-migration/migration-confirm.png "megerősítése a migrálás megkezdése")

9. Mint azt a migrálás befejezésének vagy van szükség, a művelet, kapni fog egy e-mailt a 8. lépésben megadott e-mail-címek. Rendszeres időközönként ellenőrizheti a migrálás kezdőlapja a portálon az állapot.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="why-is-my-subscriptions-listed-as-not-ready-for-migration"></a>**Miért érdemes az előfizetés(ek) szerepel a listán nem áll készen a migrálás?**

Az áttelepítési eszköz bevezetéséről fázisban történik minden ügyfél számára. A korai szakaszában, a legtöbb vagy összes az előfizetések jelöléssel **nem áll készen a migrálás**. Azonban mid április, az összes előfizetés kell tenni, migrálásra kész.

Előfizetés válásakor készen áll az áttelepítéshez, előfizetés-tulajdonost az eszköz rendelkezésre állásának értesítése e-mailt fog kapni. Tartsa szemmel erre az értesítésre.

### <a name="who-can-trigger-the-migration"></a>**Ki is aktiválhatja a migrálás?**

Az az előfizetés szintjén hozzájuk rendelt figyelési közreműködő szerepkörrel rendelkező felhasználók tudják az áttelepítés indításához. Tudjon meg többet [szerepköralapú hozzáférés-vezérlés az áttelepítési folyamat](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-is-the-migration-going-to-take"></a>**Mennyi az áttelepítés lesz?**

A legtöbb előfizetésekhez áttelepítés általában befejezi egy óra alatt. Azt is nyomon követheti, az áttelepítési folyamat a migrálás kezdőlapja.  Ebben az időszakban meg kell biztosítani, hogy a riasztások továbbra is futnak a klasszikus riasztások rendszer vagy az újat.

### <a name="what-can-i-do-if-i-run-into-an-issue-during-migration"></a>**Mi a teendő, ha az áttelepítés során problémába futtatási?**

Kérjük, kövesse a [hibaelhárítási útmutató megtekintéséhez javítási lépések között előfordulhat, hogy migráláskor problémákkal](alerts-understand-migration.md#common-issues-and-remediations). Ha az áttelepítés elvégzéséhez, nincs szükség semmilyen műveletre, értesíteni fogjuk az áttelepítés során megadott e-mail-címeit.

## <a name="next-steps"></a>További lépések

- [Az áttelepítés előkészítése](alerts-prepare-migration.md)
- [Az áttelepítési eszköz működésének megismerése](alerts-understand-migration.md)
