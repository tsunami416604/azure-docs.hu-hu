---
title: Erőforrás zárolása ismertetése
description: Ismerje meg az erőforrások védelme a tervezet hozzárendelésekor zárolási beállítások.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 799e496fd9dd8a405e5fc356e13cf6c05883e1ae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57855406"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Erőforrás zárolása az Azure-tervek ismertetése

A nagy mennyiségű egységes környezetet létrehozása csak akkor valóban a legértékesebb, ha olyan mechanizmus, amellyel a konzisztencia fenntartása. Ez a cikk ismerteti, hogyan erőforrás zárolása az Azure-tervek. Lásd például erőforrás zárolása és az alkalmazás _hozzárendelések megtagadása_, tekintse meg a [új erőforrások védelme](../tutorials/protect-new-resources.md) oktatóanyag.

## <a name="locking-modes-and-states"></a>Zárolási mód és állapotok

Zárolási mód vonatkozik a tervezet-hozzárendelést, és azt a három pontot tartalmaz: **Ne zárolja**, **csak olvasható**, vagy **ne törölje a**. A zárolási mód összetevő üzembe helyezése során a tervezet-hozzárendelés során van konfigurálva. A tervezet-hozzárendelés frissítése egy másik zárolási mód is beállítható.
Zárolás módok, azonban nem módosítható tervezetek kívül.

A tervezet-hozzárendelést az összetevők által létrehozott erőforrások négy állapota van: **Sok mindent megváltoztathat**, **csak olvasható**, **nem Szerkesztés / Törlés**, vagy **nem lehet törölni**. Minden egyes összetevő típusa lehet a **nincs zárolva** állapota. Erőforrás állapotának megállapítása a következő táblázat használható:

|Mód|Összetevő erőforrás típusa|Állapot|Leírás|
|-|-|-|-|
|Nincs zárolás|*|Nincs zárolva|Tervezetek nem védett erőforrásokat. Ebben az állapotban is szolgál az erőforrások hozzáadott egy **csak olvasható** vagy **ne törölje a** erőforrás csoport összetevő a tervezet-hozzárendelést kívül.|
|Csak olvasási engedély|Erőforráscsoport|Cannot Edit / Delete|Az erőforráscsoport csak olvasható, és címkéket az erőforráscsoport nem módosítható. **Sok mindent megváltoztathat** erőforrások hozzáadva, áthelyezték, módosítható, vagy ez az erőforráscsoport törölve.|
|Csak olvasási engedély|Nem-erőforráscsoport|Csak olvasási engedély|Az erőforrás nem módosítható bármilyen módon – nem végez módosítást, és nem törölhető.|
|Do Not Delete|*|Cannot Delete|Az erőforrások is módosítható, de nem lehet törölni. **Sok mindent megváltoztathat** erőforrások hozzáadva, áthelyezték, módosítható, vagy ez az erőforráscsoport törölve.|

## <a name="overriding-locking-states"></a>Zárolási állapotok felülbírálása

Általában lehetséges, ha valaki megfelelő [szerepköralapú hozzáférés-vezérlés](../../../role-based-access-control/overview.md) (RBAC) az előfizetésben, például a "Tulajdonos" szerepkört kell lehetővé tenni, hogy megváltoztatni vagy bármely erőforrás törlése. Ez a hozzáférés nem ez a helyzet, ha tervezetek érvényes részeként üzembe helyezett hozzárendelés zárolását. Ha a hozzárendelés állították be az a **csak olvasható** vagy **ne törölje** lehetőséget, még az előfizetés tulajdonosa a letiltott művelet hajtható végre, a védett erőforrás.

Ez a biztonsági intézkedés védi a konzisztencia, a megadott tervezet és a környezet létrehozása a véletlen vagy programozott törlés vagy az megváltoztatására úgy lett kialakítva.

## <a name="removing-locking-states"></a>Zárolási állapotok eltávolítása

Ha módosítja vagy törli a hozzárendelés által védett erőforrás szükségessé válik, két módon ennek a végrehajtására.

- A tervezet-hozzárendelés frissítése a zárolási mód **nem zárolása**
- A tervezet-hozzárendelés törlése

A hozzárendelés eltávolítása után a rendszer eltávolítja a tervek szerint létrehozott zárolása. Azonban az erőforrás marad, és normál módon törölni kell.

## <a name="how-blueprint-locks-work"></a>Hogyan tervezet zárolja a munka

Az RBAC [hozzárendelések megtagadása](../../../role-based-access-control/deny-assignments.md) megtagadási művelet során mikor lesz alkalmazva összetevő erőforrások a tervezet-hozzárendelést a hozzárendelés választásakor a **csak olvasható** vagy **ne törölje a** a beállítás. A megtagadási művelet által felügyelt identitását a tervezet-hozzárendelést kerülnek, és csak eltávolíthatók az összetevő-erőforrások által a azonos felügyelt identitás. Ez biztonsági okokból a zárolási mechanizmus kikényszeríti, és megakadályozza, hogy a tervek kívül a tervezet zárolás eltávolítása.

> [!IMPORTANT]
> Az Azure Resource Manager akár 30 percig gyorsítótárazza a szerepkör-hozzárendelés részletei. Ennek eredményeképpen a megtagadási hozzárendelések megtagadási művelet a tervezet-erőforrások nem azonnal el teljes körű érvénybe. Ez idő alatt, előfordulhat, hogy lehet törölni a tervezet zárolások fogja védeni kívánt erőforrás.

## <a name="next-steps"></a>További lépések

- Kövesse a [új erőforrások védelmét](../tutorials/protect-new-resources.md) oktatóanyag.
- Tudnivalók a [tervek életciklusáról](lifecycle.md).
- A [statikus és dinamikus paraméterek](parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](sequencing-order.md) testreszabásának elsajátítása.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).