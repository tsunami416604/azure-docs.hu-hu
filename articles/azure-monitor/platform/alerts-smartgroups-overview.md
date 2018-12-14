---
title: Intelligens csoportok
description: Az intelligens csoportok olyan riasztásokat, amelyek segítséget nyújtanak az összesítések riasztási zaj csökkentésére
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 960dc8f2908a20620a84113b27e474b553cd45d4
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346095"
---
# <a name="smart-groups"></a>Intelligens csoportok
A riasztások kezelésével kapcsolatosan van átnéznie a zaj ismerje meg, hogy ténylegesen lényegre – intelligens csoportok szánt a megoldást a problémára, ha megmérkőzött közös kihívást.  

Intelligens csoportokat úgy, hogy egyetlen problémára képviselő kapcsolódó riasztások gépi tanulási algoritmusok használatával automatikusan jönnek létre.  Ha riasztás jön létre, az algoritmus hozzáad egy új intelligens csoportot vagy egy meglévő, például a korábbi minták, hasonló tulajdonságok és hasonló szerkezet információi alapján intelligens csoporthoz. Ha egy adott előfizetéshez több virtuális gépen % CPU egyidejűleg hirtelen megugró kihasználtság, és az egyes riasztások, és ha az ilyen riasztások együtt történt bármikor múltbeli időpont, ezek a riasztások valószínűleg csoportosítja a rendszer egyetlen intelligens csoportba, amely arra utal, például egy lehetséges leggyakoribb okát. Ez azt jelenti, hogy valaki hibaelhárítási figyelmeztetések, intelligens csoportok nem csak lehetővé teszi őket a zaj csökkentésére, mivel kezeli az kapcsolódó riasztások összesített egyetlen egységként, azt is segítséget nyújt számukra az felé gyakori kiváltó okok felderítése érdekében, a riasztások esetén lehetséges.

Az algoritmus jelenleg csak egy előfizetésen belül ugyanaz a figyelő a szolgáltatás riasztásai figyelembe veszi. Intelligens csoportok akár 99 %-a-összevonás keresztül riasztási zaj csökkentéséhez. Az oka, hogy a riasztás egy csoportot az intelligens csoport részleteit megjelenítő oldalon a Rendszeríró tekintheti meg.

Intelligens csoportok adatait tekintheti meg, és az állapot beállítása hasonlóan történik, hogyan zajlik a riasztások. Minden riasztás pontosan egy intelligens csoport tagja. 

## <a name="smart-group-state"></a>Az intelligens csoport állapota
Az intelligens csoport állapota a riasztás állapotát, amely lehetővé teszi, hogy az intelligens csoport szintjén a feloldási folyamat kezeléséhez egy hasonló fogalma. Hasonlóan a riasztás állapota, ha egy intelligens csoport jön létre, azt a **új** állapotát, amely egyaránt módosítható **Visszaigazolva** vagy **lezárva**.

A következő intelligens csoport állapotok támogatottak.

| Állapot | Leírás |
|:---|:---|
| Új | A probléma csak észlelt, és még nem lett felülvizsgálva. |
| Visszaigazolva | A rendszergazda tekintse át az intelligens csoport és a munka megkezdése. |
| Lezárva | A probléma megoldódott. Egy intelligens csoport lezárása után nyissa meg újra egy másik állapotának módosításával. |

[Megtudhatja, hogyan módosíthatja az intelligens csoport állapotát.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  Az intelligens csoport állapotának módosítása nem módosítja a tagokhoz riasztások állapotát.

## <a name="smart-group-details-page"></a>Az intelligens csoport adatai lap

Az intelligens Részletek lapján jelenik meg, válasszon ki egy intelligens csoportot. Az intelligens csoport, beleértve az indoklást, amely a csoport létrehozásához használt, és lehetővé teszi, hogy az állapotváltozáshoz. részleteket biztosít.
 
![Az intelligens csoport részletei](media/alerts-smartgroups-overview/smart-group-detail.png)


Az intelligens Részletek lapján az alábbi szakaszokat tartalmazza.

| Section | Leírás |
|:---|:---|
| Riasztások | Az intelligens csoportban levő egyes riasztások listája. Válasszon ki egy riasztást a riasztás részletei lap megnyitásához. |
| Előzmények | Felsorolja az egyes műveletet, amelyet az intelligens csoport és a hozzá végrehajtott módosításokat. Ez az állapot és riasztási tagsági változás jelenleg korlátozott. |

## <a name="smart-group-taxonomy"></a>Az intelligens csoport besorolás

Egy intelligens csoport nevét az első riasztás neve. Nem hozható létre, vagy intelligens csoport átnevezése.

## <a name="next-steps"></a>További lépések

- [Az intelligens csoportok kezelése](https://aka.ms/managing-smart-groups)
- [A riasztási és intelligens állapot módosítása](https://aka.ms/managing-alert-smart-group-states)

