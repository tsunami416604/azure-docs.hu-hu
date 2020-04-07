---
title: Objektum és erőforrás élettartama
description: A különböző típusok élettartamra szóló kezelésének magyarázata
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: d031ff4a6ee86da2843f0f18ac428c50f7cfc121
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681869"
---
# <a name="object-and-resource-lifetime"></a>Objektum és erőforrás élettartama

Az Azure Remote Rendering két típust különböztet meg: **objektumokat** és **erőforrásokat.**

## <a name="object-lifetime"></a>Objektum élettartama

*Az objektumok* olyan dolgoknak minősülnek, amelyeket a felhasználó saját belátása szerint hozhat létre, módosíthat és semmisíthet meg. Az objektumok szabadon duplikálódhatnak, és minden példány idővel mutálódhat. Ennek következtében [az entitások](entities.md) és [az összetevők](components.md) objektumok.

Az objektumok élettartama teljes mértékben a felhasználó ellenőrzése alatt áll. Ez azonban nem kapcsolódik az ügyféloldali képviselet élettartamához. Az osztályok `Entity` `Component` kedvelik `Destroy` és rendelkeznek egy funkcióval, amelyet meg kell hívni az objektum távoli leképezési állomáson történő felszabadításához. Emellett `Entity.Destroy()` elpusztítja az entitást, annak gyermekeit és a hierarchia összes összetevőjét.

## <a name="resource-lifetime"></a>Erőforrás élettartama

*Az erőforrások* olyan dolgok, amelyek élettartamát teljes egészében a távoli renderelési állomás kezeli. Az erőforrások belső számlálása belsőleg van számolva. Felszabadulnak, ha már senki sem hivatkozik rájuk.

A legtöbb erőforrás csak közvetetten hozható létre, általában fájlból történő betöltéssel. Ha ugyanazt a fájlt többször is betölti, az Azure Remote Rendering ugyanazt a hivatkozást adja vissza, és nem tölti be újra az adatokat.

Sok erőforrás nem módosítható, például [a lápok](meshes.md) és [a textúrák](textures.md). Egyes erőforrások azonban módosíthatók, például [anyagok](materials.md). Mivel az erőforrások gyakran meg vannak osztva, az erőforrások módosítása több objektumot is érinthet. Ha például egy anyag színének módosítása megváltoztatja a képének színét, és minden olyan objektum színét megváltoztatja, amely a képre hivatkozik, ami viszont az adott anyagra utal.

### <a name="built-in-resources"></a>Beépített erőforrások

Az Azure remote rendering tartalmaz néhány beépített erőforrásokat, amelyek betölthetők a `AzureSession.Actions.LoadXYZAsync()`megfelelő azonosítót a hívás `builtin://` során. A rendelkezésre álló beépített erőforrások az egyes szolgáltatások dokumentációjában találhatók. Az ég [fejezet](../overview/features/sky.md) például a beépített égbolt textúrákat sorolja fel.

## <a name="general-lifetime"></a>Általános élettartam

Az összes objektum és erőforrás élettartama a kapcsolathoz van kötve. A kapcsolat bontásakor a rendszer mindent eldob. Amikor újra csatlakozik ugyanahhoz a munkamenethez, a jelenetdiagram üres lesz, és az összes erőforrás törlődik.

A gyakorlatban ugyanazt az erőforrást egy munkamenetbe betöltése a kapcsolat bontása után általában gyorsabb, mint az első alkalommal. Ez azért van így, mert a legtöbb erőforrást először kell letölteni az Azure Storage-ból, ami nem szükséges a második alkalommal, így jelentős időt takarít meg.

## <a name="next-steps"></a>További lépések

* [Entitások](entities.md)
* [Összetevők](components.md)
* [Modellek](models.md)
