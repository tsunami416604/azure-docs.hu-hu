---
title: Erőforrás zárolása az Azure-tervek ismertetése
description: Ismerje meg az erőforrások védelme a tervezet hozzárendelésekor zárolási beállítások.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 718c23b806da5058c042b961077e51ba0d4b6245
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973252"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Erőforrás zárolása az Azure-tervek ismertetése

Ipari méretekben egységes környezetet létrehozása csak a valóban a legértékesebb, ha olyan mechanizmus, amellyel győződjön meg arról, hogy a rendszer megőrzi konzisztencia. Ez a cikk ismerteti, hogyan erőforrás zárolása az Azure-tervek.

## <a name="locking-modes-and-states"></a>Zárolási mód és állapotok

Zárolási mód vonatkozik a tervezet-hozzárendelést, és csak két pontot tartalmaz: **nincs** vagy **összes erőforrás**. Ez a tervezet-hozzárendelés során van konfigurálva, és nem módosítható, miután a hozzárendelés sikerült alkalmazni az előfizetéshez.

Összetevő-definíciókat a tervezet az előfizetéshez rendelt belül létrehozott erőforrást három állapota van: **nincs zárolva**, **csak olvasható**, vagy **nem Szerkesztés / Törlés**. Bármilyen típusú összetevő lehet a **nincs zárolva** állapota. Azonban nem erőforrás-csoport összetevőket is **csak olvasható** és erőforrás-csoportok a következők **nem Szerkesztés / Törlés**. Ez fontos különbség az, hogy hogyan kezeli az ezekhez az erőforrásokhoz.

A **csak olvasható** állapota pontosan a meghatározott: az erőforrás nem módosítható bármilyen módon – nem végez módosítást, és nem törölhető. A **nem Szerkesztés / Törlés** erőforráscsoportok "container" jellege miatt több árnyalt. Az erőforrás-csoport objektum csak olvasható, de lehet létrehozni, update és delete-erőforrásához kapcsolódóan az erőforrás csoport – mindaddig, amíg azok bármely tervezet-hozzárendelést a részét képezik a **csak olvasható** zárolási állapot.

## <a name="overriding-locking-states"></a>Zárolási állapotok felülbírálása

Bár általában lehetséges, ha valaki megfelelő [szerepköralapú hozzáférés-vezérlés](../../../role-based-access-control/overview.md) (RBAC) az előfizetésben, például a "Tulajdonos" szerepkör módosítása vagy törlése bármely erőforrás képes ez nem amikor tervek érvényes részeként üzembe helyezett hozzárendelés zárolását. Ha a hozzárendelés állították be az a **zárolási** lehetőséget, még az előfizetés tulajdonosa tudja módosítani a bennefoglalt erőforrások.

Ez védelmet biztosít a megadott tervezet és a környezet létrehozása a véletlen vagy programozott törlése vagy módosítása arra készült konzisztenciáját.

## <a name="removing-locking-states"></a>Zárolási állapotok eltávolítása

Ha válik szükségessé, törölheti a hozzárendelés által létrehozott erőforrásokat, majd csak úgy törölheti őket, hogy először távolítsa el a hozzárendelést. A hozzárendelés eltávolítása után a rendszer eltávolítja a tervek szerint létrehozott zárolása. Az erőforrás azonban marad, és ezután keresztül szokásos azt jelenti, hogy megfelelő engedélyekkel rendelkező bármely személy törölni kell.

## <a name="how-blueprint-locks-work"></a>Hogyan tervezet zárolja a munka

Az RBAC szerepkör `denyAssignments` összetevő erőforrások során alkalmazzák a tervezet-hozzárendelést a hozzárendelés kijelölésével a **zárolási** lehetőséget. A szerepkör által kezelt identitását a tervezet-hozzárendelést kerülnek, és csak eltávolíthatók az összetevő-erőforrások által a azonos felügyelt identitás. Ez kényszeríti a zárolási mechanizmus, és távolítsa el a tervezet zárolást kívül tervezetek megakadályozza. A szerepkör és a Zárolás eltávolítása csak akkor lehetséges, a tervezet-hozzárendelést, amely megfelelő jogosultsággal rendelkező egyének csak végezhető eltávolításával.

## <a name="next-steps"></a>További lépések

- További információ a [tervezetet életciklus](lifecycle.md)
- Megtudhatja, hogyan használja [statikus és dinamikus paraméterek](parameters.md)
- Ismerje meg, szabhatja testre a [tervezetet alkalmazás-előkészítés sorrend](sequencing-order.md)
- Ismerje meg, hogyan [meglévő hozzárendelések frissítése](../how-to/update-existing-assignments.md)
- A tervrajz hozzárendelésének során felmerülő problémák megoldása [általános hibaelhárítási](../troubleshoot/general.md)