---
title: Erőforrás zárolása az Azure-tervek ismertetése
description: Ismerje meg az erőforrások védelme a tervezet hozzárendelésekor zárolási beállítások.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 4e71797837927fe5f5233bcf88d35fef98f504e9
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139442"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Erőforrás zárolása az Azure-tervek ismertetése

A nagy mennyiségű egységes környezetet létrehozása csak akkor valóban a legértékesebb, ha olyan mechanizmus, amellyel a konzisztencia fenntartása. Ez a cikk ismerteti, hogyan erőforrás zárolása az Azure-tervek.

## <a name="locking-modes-and-states"></a>Zárolási mód és állapotok

Zárolási mód vonatkozik a tervezet-hozzárendelést, és csak két pontot tartalmaz: **nincs** vagy **összes erőforrás**. A zárolási mód tervezet-hozzárendelés során van konfigurálva, és nem módosítható, miután a hozzárendelés sikerült alkalmazni az előfizetéshez.

A tervezet-hozzárendelést az összetevők által létrehozott erőforrásokat három állapota van: **nincs zárolva**, **csak olvasható**, vagy **nem Szerkesztés / Törlés**. Minden egyes összetevő lehet a **nincs zárolva** állapota. Van azonban, hogy nem erőforráscsoport összetevők **csak olvasható** és erőforráscsoportok **nem módosítása / törlése** állapotok. Ez a különbség fontos különbség az, hogy hogyan kezeli az ezekhez az erőforrásokhoz.

A **csak olvasható** állapota pontosan a meghatározott: az erőforrás nem módosítható bármilyen módon – nem végez módosítást, és nem törölhető. A **nem Szerkesztés / Törlés** erőforráscsoportok "container" jellege miatt több árnyalt. Az erőforrás-csoport objektum csak olvasható, de módosításokat nem zárolt erőforrások az erőforráscsoporton belül lehetséges.

## <a name="overriding-locking-states"></a>Zárolási állapotok felülbírálása

Általában lehetséges, ha valaki megfelelő [szerepköralapú hozzáférés-vezérlés](../../../role-based-access-control/overview.md) (RBAC) az előfizetésben, például a "Tulajdonos" szerepkört kell lehetővé tenni, hogy megváltoztatni vagy bármely erőforrás törlése. Ez a hozzáférés nem ez a helyzet, ha tervezetek érvényes részeként üzembe helyezett hozzárendelés zárolását. Ha a hozzárendelés állították be az a **zárolási** lehetőséget, még az előfizetés tulajdonosa tudja módosítani a bennefoglalt erőforrások.

Ez a biztonsági intézkedés védi a konzisztencia, a megadott tervezet és a környezet létrehozása a véletlen vagy programozott törlés vagy az megváltoztatására úgy lett kialakítva.

## <a name="removing-locking-states"></a>Zárolási állapotok eltávolítása

Válik, a hozzárendelés által létrehozott erőforrások törléséhez szükséges, ha a törölheti őket módja először távolítsa el a hozzárendelést. A hozzárendelés eltávolítása után a rendszer eltávolítja a tervek szerint létrehozott zárolása. Azonban az erőforrás marad, és normál módon törölni kell.

## <a name="how-blueprint-locks-work"></a>Hogyan tervezet zárolja a munka

Az RBAC szerepkör `denyAssignments` összetevő erőforrások során alkalmazzák a tervezet-hozzárendelést a hozzárendelés kijelölésével a **zárolási** lehetőséget. A szerepkör által kezelt identitását a tervezet-hozzárendelést kerülnek, és csak eltávolíthatók az összetevő-erőforrások által a azonos felügyelt identitás. Ez biztonsági okokból a zárolási mechanizmus kikényszeríti, és megakadályozza, hogy a tervek kívül a tervezet zárolás eltávolítása. A szerepkör és a Zárolás eltávolítása csak akkor lehetséges, a tervezet-hozzárendelést, amely megfelelő jogosultsággal rendelkező egyének csak végezhető eltávolításával.

> [!IMPORTANT]
> Az Azure Resource Manager akár 30 percig gyorsítótárazza a szerepkör-hozzárendelés részletei. Ennek eredményeképpen `denyAssignments` a tervezet erőforrás nem azonnal el teljes körű érvénybe. Ez idő alatt, előfordulhat, hogy lehet törölni a tervezet zárolások fogja védeni kívánt erőforrás.

## <a name="next-steps"></a>További lépések

- Tudnivalók a [tervek életciklusáról](lifecycle.md)
- A [statikus és dinamikus paraméterek](parameters.md) használatának elsajátítása
- A [tervekkel kapcsolatos műveleti sorrend](sequencing-order.md) testreszabásának elsajátítása
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md)