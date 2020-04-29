---
title: Objektumok és erőforrások élettartama
description: A különböző típusú élettartam-kezelés ismertetése
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: d031ff4a6ee86da2843f0f18ac428c50f7cfc121
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681869"
---
# <a name="object-and-resource-lifetime"></a>Objektumok és erőforrások élettartama

Az Azure távoli renderelés két típust különböztet meg: az **objektumokat** és az **erőforrásokat**.

## <a name="object-lifetime"></a>Objektum élettartama

Az *objektumok* olyan dolgoknak tekintendők, amelyeket a felhasználó saját belátása szerint hozhat létre, módosíthat és semmisít meg. Az objektumok szabadon duplikálható, és az egyes példányok időbeli változékonysága is változhat. Ennek következtében az [entitások](entities.md) és az [összetevők](components.md) objektumok.

Az objektumok élettartama teljes mértékben a felhasználói vezérlés alatt áll. Nem kapcsolódik az ügyféloldali ábrázolás élettartamához, azonban. A ( `Entity` z `Component` ) és `Destroy` a (z) függvényt úgy kell meghívni, hogy a távoli renderelési gazdagépen felszabadítsa az objektumot. `Entity.Destroy()` Emellett a megsemmisíti az entitást, annak gyermekeit és a hierarchiában lévő összes összetevőt.

## <a name="resource-lifetime"></a>Erőforrás élettartama

Az *erőforrások* olyan dolgok, amelyek élettartamát teljes mértékben a távoli renderelési gazdagép kezeli. Az erőforrásokra vonatkozó hivatkozás belsőleg van megszámolva. Ha már senki nem hivatkozik rájuk, akkor a lefoglalásra kerül.

A legtöbb erőforrás csak közvetett módon hozható létre, általában egy fájlból való betöltéssel. Ha ugyanaz a fájl többször is be van töltve, az Azure távoli renderelés ugyanezt a hivatkozást fogja visszaadni, és nem tölti be újra az adatfájlokat.

Számos erőforrás nem változtatható, például a [Rácsvonalak](meshes.md) és a [textúrák](textures.md). Bizonyos erőforrások változékonyak, de például [anyagok](materials.md). Mivel az erőforrások gyakran meg vannak osztva, az erőforrások módosítása több objektumra is hatással lehet. Például az anyagok színének módosítása megváltoztatja az összes olyan objektum színét, amely rácsvonalat használ, ami viszont az anyagot is megadja.

### <a name="built-in-resources"></a>Beépített erőforrások

Az Azure Remote rendering tartalmaz néhány beépített erőforrást, amelyek a megfelelő azonosítójuk alapján tölthetők be `builtin://` a hívása során `AzureSession.Actions.LoadXYZAsync()`. Az elérhető beépített erőforrások az egyes szolgáltatások dokumentációjában szerepelnek. Az [égbolt szakasz](../overview/features/sky.md) például felsorolja a beépített Sky-textúrákat.

## <a name="general-lifetime"></a>Általános élettartam

Az összes objektum és erőforrás élettartama a kapcsolódáshoz van kötve. A kapcsolat bontásakor a rendszer elveti az összeset. Amikor újra csatlakozik ugyanahhoz a munkamenethez, a jelenet gráf üres lesz, és az összes erőforrás törlődik.

A gyakorlatban a leválasztást követően ugyanazt az erőforrást egy munkamenetbe tölti be, ami általában gyorsabb, mint az első alkalommal. Ez azért van így, mert a legtöbb erőforrást először le kell tölteni az Azure Storage-ból, ami nem szükséges a második alkalommal, így jelentős mennyiségű időt takaríthat meg.

## <a name="next-steps"></a>További lépések

* [Entitások](entities.md)
* [Összetevők](components.md)
* [Modellek](models.md)
