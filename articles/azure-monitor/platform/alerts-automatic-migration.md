---
title: Ismerje meg, hogyan működik a Azure Monitor klasszikus riasztások automatikus áttelepítési folyamata
description: Ismerje meg, hogyan működik az automatikus áttelepítési folyamat.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: fbc024b6ae6e3ebfed6f64067158352e8201aef5
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642564"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>A klasszikus riasztási szabályok automatikus áttelepítési folyamatának megismerése

Amint azt [korábban bejelentettük](monitoring-classic-retirement.md), a Azure monitor klasszikus riasztásai 2019 szeptemberében megszűnnek (eredetileg 2019. július). A nyugdíjazási folyamat részeként [egy áttelepítési eszköz](alerts-using-migration-tool.md) is elérhető a Azure Portal az ügyfelek számára az áttelepítés elindításához. Ha még nem használta az áttelepítési eszközt a 2019-es augusztus 31-ig, Azure Monitor elindítja a klasszikus riasztások automatikus áttelepítésének folyamatát, amely az 2019. szeptember 1-től kezdődik.
Ez a cikk végigvezeti az automatikus áttelepítési folyamaton, és segít megoldani az esetlegesen futtatott problémákat.

  > [!NOTE]
  > Ez a cikk csak az Azure nyilvános felhőre vonatkozik. A Azure Government Cloud és az Azure China 21Vianet Azure Monitor klasszikus riasztások kivonási folyamata jövőbeli időpontban lesz bejelentve.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>Mi fog történni az automatikus áttelepítési folyamat során?

- **2019. szeptember 1-től**az ügyfelek nem tudnak új klasszikus riasztási szabályokat létrehozni, kivéve [bizonyos mérőszámokat](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).
  - A kivételek miatt az ügyfél továbbra is új klasszikus riasztási szabályokat hozhat létre, és a klasszikus riasztásokat 2020-ig használhatja.
- **2019. szeptember 1-től**kezdődően a klasszikus riasztások áttelepítése a klasszikus riasztásokkal rendelkező ügyfelek kötegében aktiválódik.
- Hasonlóan az önkéntes áttelepítési eszközhöz, bizonyos klasszikus riasztási szabályok, amelyek nem telepíthetők át, maradnak. A klasszikus riasztási szabályokat a 2020-ig továbbra is támogatja a rendszer. Azonban minden érvénytelen klasszikus riasztási szabály törlődik, mivel azok nem működőképesek.
A törölt cél erőforrásokat vagy a [már nem támogatott metrikákat](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) figyelő klasszikus riasztási szabályok érvénytelennek tekintendők.
- Ha az előfizetés áttelepítése megkezdődik, hacsak nincs probléma, az áttelepítés egy órán belül befejeződik. Az áttelepítés állapotát a [Azure monitor](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)áttelepítési paneljén is figyelheti.
- Az előfizetés tulajdonosai e-mailt kapnak az áttelepítés sikeres befejezéséről.
- Ha az áttelepítés során problémák merülnek fel, az előfizetések tulajdonosai is kapnak egy e-mailt, amely arról tájékoztatja őket. Az ügyfelek az áttelepítési panelen tekinthetik meg a probléma részletes adatait.
- Ha az ügyfélnek olyan beavatkozásra van szüksége, mint például egy erőforrás zárolásának ideiglenes letiltása vagy egy szabályzat-hozzárendelés módosítása, az ügyfeleknek az 2019. október 31-ig kell megszüntetniük az esetleges problémákat. Ha a problémákat nem oldja meg, a klasszikus riasztások sikeres áttelepítése nem garantálható.

    > [!NOTE]
    > Ha nem szeretné, hogy az automatikus áttelepítési folyamat elinduljon, továbbra is aktiválhatja az áttelepítést az áttelepítési eszköz használatával.

## <a name="important-things-to-note"></a>Fontos Tudnivaló

Az áttelepítési folyamat a klasszikus riasztási szabályokat új, egyenértékű riasztási szabályokra konvertálja, és műveleti csoportokat hoz létre. Az előkészítés során vegye figyelembe a következő szempontokat:

- Az új riasztási szabályokhoz tartozó értesítési hasznos formátumok eltérnek a klasszikus riasztási szabályoktól, mert több funkciót támogatnak. Ha olyan logikai alkalmazásokkal, runbookok vagy webhookokkal rendelkezik, amelyeket klasszikus riasztási szabály indít el, akkor előfordulhat, hogy az áttelepítést követően az áttelepítés befejeződése után leáll a várt működés. [Ismerje meg, hogyan készítheti elő az](alerts-prepare-migration.md)áttelepítést.

- Néhány klasszikus riasztási szabályt nem lehet áttelepíteni az eszköz használatával. Megtudhatja, [hogy mely szabályok nem telepíthetők át, és mire használhatók](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > Az áttelepítési folyamat nem befolyásolja a klasszikus riasztási szabályok kiértékelését. A rendszer továbbra is futtatja és riasztásokat küld, amíg át nem telepíti őket, és az új riasztási szabályok érvénybe lépnek.

## <a name="what-if-the-automatic-migration-fails"></a>Mi a teendő, ha az automatikus áttelepítés meghiúsul?

Ha az automatikus áttelepítési folyamat meghiúsul, az előfizetés tulajdonosai e-mailben értesítik a problémát. A probléma részletes ismertetését az Azure Monitor áttelepítési paneljén végezheti el.

Az áttelepítés során esetlegesen felmerülő problémákkal kapcsolatos segítségért tekintse meg a [hibaelhárítási útmutatót](alerts-understand-migration.md#common-problems-and-remedies) .

  > [!NOTE]
  > Ha az ügyfélnek olyan beavatkozásra van szüksége, mint például egy erőforrás zárolásának ideiglenes letiltása vagy egy szabályzat-hozzárendelés módosítása, az ügyfeleknek az 2019. október 31-ig kell megszüntetniük az esetleges problémákat. Ha a problémákat nem oldja meg, a klasszikus riasztások sikeres áttelepítése nem garantálható.

## <a name="next-steps"></a>További lépések

- [Felkészülés az áttelepítésre](alerts-prepare-migration.md)
- [A migrálási eszköz működésének ismertetése](alerts-understand-migration.md)
