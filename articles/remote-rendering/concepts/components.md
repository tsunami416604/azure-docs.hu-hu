---
title: Összetevők
description: Az Azure távoli renderelés hatókörében található összetevők definíciója
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: bd2a6e16d34bf6c5b59ce7fa5c99975d44947770
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84021670"
---
# <a name="components"></a>Összetevők

Az Azure távoli renderelés az [entitás-összetevő](https://en.wikipedia.org/wiki/Entity_component_system) rendszermintáját használja. Míg az [entitások](entities.md) képviselik a pozíciót és az objektumok hierarchikus összetételét, az összetevők a viselkedés végrehajtásáért felelősek.

Az összetevők leggyakrabban használt típusai [:::no-loc text="mesh components":::](meshes.md) , amelyek rácsvonalak hozzáadását teszik elérhetővé a renderelési folyamatban. Hasonlóképpen, a [világos összetevőket](../overview/features/lights.md) a rendszer a világító és a [kivágási sík összetevők](../overview/features/cut-planes.md) hozzáadására használja a nyitott rácsvonalak kivágására.

Ezek az összetevők a hivatkozási pontként, a hozzájuk kapcsolódó entitások átalakítását (pozícióját, elforgatását, méretezését) használják.

## <a name="working-with-components"></a>Összetevők használata

Az összetevők programozott módon egyszerűen hozzáadhatók, eltávolíthatók és kezelhetők:

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

```cpp
// create a point light component
ApiHandle<AzureSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Actions()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```


Az összetevők létrehozáskor egy entitáshoz vannak csatolva. Ezt követően nem helyezhető át másik entitásba. Az összetevők explicit módon törlődnek a `Component.Destroy()` vagy automatikusan, ha az összetevő tulajdonosi entitása megsemmisül.

Egyszerre csak egy példány adható hozzá egy entitáshoz.

## <a name="unity-specific"></a>Unity-specifikus

Az Unity Integration további bővítmény-funkciókat biztosít az összetevőkkel való interakcióhoz. Lásd: [Unity game Objects és Components](../how-tos/unity/objects-components.md).

## <a name="next-steps"></a>További lépések

* [Objektumhatárok](object-bounds.md)
* [Hálók](meshes.md)
