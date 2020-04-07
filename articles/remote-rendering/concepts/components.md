---
title: Összetevők
description: Az Azure távoli renderelés hatókörében lévő összetevők meghatározása
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681895"
---
# <a name="components"></a>Összetevők

Az Azure távoli renderelés az [entitásösszetevő-rendszer](https://en.wikipedia.org/wiki/Entity_component_system) mintáját használja. Míg [az entitások](entities.md) az objektumok pozícióját és hierarchikus összetételét képviselik, az összetevők felelősek a viselkedés megvalósításáért.

A leggyakrabban használt összetevőtípusok a [hálóösszetevői,](meshes.md)amelyek hálókat adnak a renderelési folyamathoz. Hasonlóképpen, [könnyű alkatrészeket](../overview/features/lights.md) használnak, hogy adjunk világítás és [vágott sík alkatrészek](../overview/features/cut-planes.md) et használnak vágni nyitott csak nem.

Ezek az összetevők referenciapontként annak az entitásnak az átalakítóját (pozíció, elforgatás, lépték) használják, amelyhez kapcsolódnak.

## <a name="working-with-components"></a>Az összetevők kelése

Az összetevőket egyszerűen hozzáadhatja, eltávolíthatja és kezelheti programozott módon:

```cs
// create a point light component
AzureSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Actions.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

Egy összetevő a létrehozás időpontjában kapcsolódik egy entitáshoz. Ezt követően nem helyezhető át egy másik entitásba. Az összetevők explicit módon `Component.Destroy()` törlődnek, vagy automatikusan, ha az összetevő tulajdonosi entitása megsemmisül.

Egyszerre csak egy összetevőtípus tadható hozzá egy entitáshoz.

## <a name="unity-specific"></a>Egységspecifikus

A Unity integráció további bővítményfunkciókkal rendelkezik az összetevőkkel való interakcióhoz. Lásd [Unity játék tárgyak és alkatrészek](../how-tos/unity/objects-components.md).

## <a name="next-steps"></a>További lépések

* [Objektumhatárok](object-bounds.md)
* [Rácsvonalak](meshes.md)
