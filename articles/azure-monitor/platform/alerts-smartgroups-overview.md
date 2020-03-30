---
title: Intelligens csoportok
description: Az intelligens csoportok olyan riasztások összesítései, amelyek segítenek csökkenteni a riasztási zajt
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/15/2018
ms.openlocfilehash: 05b05f8bc079bb3768ac2f1a03593bc9260b41aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665493"
---
# <a name="smart-groups"></a>Intelligens csoportok

A figyelmeztető jelzések kezelése során felmerülő közös kihívás a zaj átszitálása, hogy kiderítsük, mi számít valójában - az intelligens csoportok célja, hogy megoldást találjanak erre a problémára.  

Az intelligens csoportok automatikusan létrejönnek gépi tanulási algoritmusok használatával az egyetlen problémát képviselő kapcsolódó riasztások kombinálására.  Riasztás létrehozásakor az algoritmus hozzáadja azt egy új intelligens csoporthoz vagy egy meglévő intelligens csoporthoz olyan információk alapján, mint például a korábbi minták, hasonló tulajdonságok és hasonló struktúra. Ha például az előfizetésben lévő több virtuális gép processzorának %-a egyszerre számos egyedi riasztáshoz vezet, és ha ilyen riasztások a múltban bármikor előfordultak együtt, akkor ezek a riasztások valószínűleg egyetlen intelligens csoportba lesznek csoportosítva, ami egy lehetséges gyakori kiváltó ok. Ez azt jelenti, hogy valaki hibaelhárítási riasztások, intelligens csoportok nem csak lehetővé teszi számukra, hogy csökkentsék a zajkezelése kapcsolódó riasztások egyetlen összesített egységként, ez is irányítja őket a lehetséges gyakori kiváltó okait a riasztások.

Jelenleg az algoritmus csak az előfizetésen belül ugyanattól a figyelőszolgáltatástól származó riasztásokat veszi figyelembe. Az intelligens csoportok a konszolidáció révén akár 99%-kal is csökkenthetik a riasztási zajt. Megtekintheti az oka annak, hogy a riasztások szerepelnek egy csoportban az intelligens csoport részleteit oldalon.

Megtekintheti az intelligens csoportok részleteit, és beállíthatja az állapothasonlóan, hogyan lehet a riasztások. Minden riasztás egy és egyetlen intelligens csoport tagja. 

## <a name="smart-group-state"></a>Intelligens csoport állapota

Az intelligens csoport állapota hasonló fogalom, mint a riasztási állapot, amely lehetővé teszi a megoldási folyamat intelligens csoport szintjén való kezelését. A riasztási állapothoz hasonlóan, intelligens csoport létrehozásakor az **Új** állapot, amely **nyugtázott** vagy **lezárt**állapotra módosítható.

A következő intelligens csoport állapotok támogatottak.

| Állapot | Leírás |
|:---|:---|
| Új | A problémát épp most észlelték, és még nem vizsgálták felül. |
| Nyugtázva | Egy rendszergazda áttekintette az intelligens csoportot, és elkezdett dolgozni rajta. |
| Lezárt | A probléma megoldódott. Miután egy intelligens csoportot bezárt, újra megnyithatja, ha másik állapotra módosítja. |

[További információ az intelligens csoport állapotának módosításáról.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  Az intelligens csoport állapotának módosítása nem változtatja meg az egyes tagi riasztások állapotát.

## <a name="smart-group-details-page"></a>Intelligens csoport részleteinek lapja

Intelligens csoport részletezési lapja intelligens csoport kiválasztásakor jelenik meg. Az intelligens csoport részleteit tartalmazza, beleértve a csoport létrehozásához használt érvelést, és lehetővé teszi az állapotának módosítását.
 
![Intelligens csoport részletei](media/alerts-smartgroups-overview/smart-group-detail.png)


Az intelligens csoport információs lapja a következő szakaszokat tartalmazza.

| Section | Leírás |
|:---|:---|
| Riasztások | Az intelligens csoportban szereplő egyes riasztások listája. Jelöljön ki egy riasztást a riasztás részleteit tartalmazó oldal megnyitásához. |
| Előzmények | Felsorolja az intelligens csoport által végrehajtott minden műveletet és a végrehajtott módosításokat. Ez jelenleg az állapotmódosításokra és a tagsági értesítések változásaira korlátozódik. |

## <a name="smart-group-taxonomy"></a>Intelligens csoporttaxonómia

Az intelligens csoport neve az első riasztás neve. Intelligens csoportot nem lehet létrehozni vagy átnevezni.

## <a name="next-steps"></a>További lépések

- [Intelligens csoportok kezelése](https://aka.ms/managing-smart-groups)
- [A riasztás és az intelligens csoport állapotának módosítása](https://aka.ms/managing-alert-smart-group-states)


