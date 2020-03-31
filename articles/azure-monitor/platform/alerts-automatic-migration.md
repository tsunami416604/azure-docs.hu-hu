---
title: Ismerje meg, hogyan működik az Azure Monitor klasszikus riasztásai automatikus áttelepítési folyamata
description: Ismerje meg, hogyan működik az automatikus áttelepítési folyamat.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 8df83439d6754440648688ac1cc36ff66556a4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668247"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>A klasszikus riasztási szabályok automatikus áttelepítési folyamatának megismerése

Ahogy [azt korábban bejelentették](monitoring-classic-retirement.md), az Azure Monitor klasszikus riasztásai 2019 szeptemberében (eredetileg 2019 júliusa) megszűnnek. A kivonási folyamat részeként [egy áttelepítési eszköz](alerts-using-migration-tool.md) érhető el az Azure Portalon az ügyfelek számára, hogy saját maguk is elindíthassanak az áttelepítést. Ha 2019. augusztus 31-ig nem használta az áttelepítési eszközt, az Azure Monitor 2019.
Ez a cikk végigvezeti az automatikus áttelepítési folyamaton, és segít az esetlegesen felmerülő problémák megoldásában.

  > [!NOTE]
  > Ez a cikk csak az Azure nyilvános felhőre vonatkozik. Az Azure Monitor klasszikus riasztásainak kivonási folyamata az Azure Government felhőben és az Azure China 21Vianet jövőbeli időpontban kerül bejelentésre.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>Mi történik az automatikus áttelepítési folyamat során?

- **2019. szeptember 1-től**az ügyfelek bizonyos mutatókon kívül nem hozhatnak létre új klasszikus [riasztási szabályokat.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)
  - A kivételek, az ügyfél továbbra is létrehozhat új klasszikus riasztási szabályokat, és használja a klasszikus riasztások júniusig 2020.
- **2019. szeptember 1-től**a klasszikus riasztások áttelepítése kötegekben aktiválódik minden olyan ügyfél számára, amely klasszikus riasztást kap.
- Az önkéntes áttelepítési eszközhöz úgy, hogy bizonyos klasszikus riasztási szabályok, amelyek nem vándorolnak, ugyanúgy maradnak, ahogy vannak. Ezek a klasszikus riasztási szabályok 2020 júniusáig továbbra is támogatottak. Azonban az érvénytelen klasszikus riasztási szabályok törlődnek, mivel azok nem működnek.
Minden klasszikus riasztási szabályok, amelyek figyelik a törölt célerőforrások vagy [metrikák, amelyek már nem támogatott](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) a már nem támogatott érvénytelennek minősülnek.
- Az előfizetés áttelepítésének megkezdése után, hacsak nincsenek problémák, az áttelepítésnek egy órán belül be kell fejeződnie. Az ügyfelek figyelhetik az áttelepítés állapotát [az Azure Monitor áttelepítési paneljén.](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)
- Az előfizetés-tulajdonosok e-mailt kapnak az áttelepítés sikeres befejezéséről.
- Ha az áttelepítés során bármilyen probléma merül fel, az előfizetés-tulajdonosok e-mailt is kapnak, amelyben tájékoztatják őket arról, hogy azok róluk. Az ügyfelek az áttelepítési panelsegítségével megtekinthetik a probléma teljes részleteit.
- Abban az esetben, ha az ügyféltől olyan műveletre van szükség, mint például egy erőforrászárolás ideiglenes letiltása vagy egy házirend-hozzárendelés módosítása, az ügyfeleknek 2019. Ha a problémák at nem oldja meg addig, a klasszikus riasztások sikeres áttelepítése nem garantálható.

    > [!NOTE]
    > Ha nem szeretné megvárni az automatikus áttelepítési folyamat elindítását, az áttelepítési eszköz használatával továbbra is elindíthatja az áttelepítést.

## <a name="important-things-to-note"></a>Fontos megjegyezni

Az áttelepítési folyamat a klasszikus riasztási szabályokat új, egyenértékű riasztási szabályokká alakítja, és műveletcsoportokat hoz létre. Az előkészítés során vegye figyelembe a következő pontokat:

- Az új riasztási szabályok értesítési hasznos adatformátumai eltérnek a klasszikus riasztási szabályoktól, mivel több funkciót támogatnak. Ha rendelkezik logikai alkalmazásokkal, runbookokkal vagy webhookokkal, amelyeket a klasszikus riasztási szabály vált ki, előfordulhat, hogy az áttelepítés befejezése után leállnak az értesítési hasznos terhelések közötti különbségek miatt. [További információ az áttelepítésre való felkészülésről.](alerts-prepare-migration.md)

- Néhány klasszikus riasztási szabályok nem telepíthető kontami által az eszköz használatával. [Megtudhatja, hogy mely szabályok nem telepíthetők át, és mi a teendő velük.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)

    > [!NOTE]
    > Az áttelepítési folyamat nem befolyásolja a klasszikus riasztási szabályok kiértékelését. Továbbra is futni fognak, és riasztásokat küldenek, amíg át nem telepítik őket, és az új riasztási szabályok érvénybe nem lépnek.

## <a name="what-if-the-automatic-migration-fails"></a>Mi történik, ha az automatikus áttelepítés sikertelen?

Ha az automatikus áttelepítési folyamat sikertelen, az előfizetés-tulajdonosok e-mailt kapnak, amelyben értesítik őket a problémáról. Használhatja a migrálási panel az Azure Monitor a probléma részleteinek megtekintéséhez.

Az áttelepítés során felmerülő problémákkal kapcsolatos segítségért tekintse meg a [hibaelhárítási útmutatót.](alerts-understand-migration.md#common-problems-and-remedies)

  > [!NOTE]
  > Abban az esetben, ha az ügyféltől olyan műveletre van szükség, mint például egy erőforrászárolás ideiglenes letiltása vagy egy házirend-hozzárendelés módosítása, az ügyfeleknek 2019. Ha a problémák at nem oldja meg addig, a klasszikus riasztások sikeres áttelepítése nem garantálható.

## <a name="next-steps"></a>További lépések

- [Előkészületek a migráláshoz](alerts-prepare-migration.md)
- [A migrálási eszköz működésének ismertetése](alerts-understand-migration.md)
