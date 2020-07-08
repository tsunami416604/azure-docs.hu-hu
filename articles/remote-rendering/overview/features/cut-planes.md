---
title: Síkok kivágása
description: Ismerteti a kivágott síkokat és azok használatát
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 7adf9a9701eb2492f0b13a26af1dbaf8de631373
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84021364"
---
# <a name="cut-planes"></a>Síkok kivágása

A *kivágott sík* olyan vizualizációs funkció, amely egy virtuális sík egyik oldalán lévő képpontokat jelenít meg, és kifedi a [Rácsvonalak](../../concepts/meshes.md)belsejét.
Az alábbi képen a hatás látható. A bal oldalon az eredeti rácsvonal látható, amely a jobb oldalon a Hálón belül látható:

![Sík kivágása](./media/cutplane-1.png)

## <a name="limitations"></a>Korlátozások

* Az Azure Remote rendering jelenleg **legfeljebb nyolc aktív kivágású gépet**támogat. Több kivágási sík-összetevőt is létrehozhat, de ha több párhuzamosan próbálkozik, a rendszer figyelmen kívül hagyja az aktiválást. Ha azt szeretné, hogy az adott összetevő milyen hatással legyen a jelenetre, tiltsa le a többi gépet.
* Minden egyes kivágott sík hatással van az összes távolról kiolvasztott objektumra. Jelenleg nincs lehetőség bizonyos objektumok vagy rácsvonalak kizárására.
* A kivágott síkok csupán vizualizációs funkciók, nem befolyásolják a [térbeli lekérdezések](spatial-queries.md)eredményét. Ha azt szeretné, hogy a Ray kivágású nyitott rácsvonalba kerüljön, beállíthatja, hogy a Ray kiindulási pontja a kivágási síkon legyen. Így a fénysugár csak a látható részeket tudja megütni.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Az aktív kivágott síkok kis költségekkel járnak a renderelés során. A kivágott síkok letiltása vagy törlése, ha nem szükségesek.

## <a name="cutplanecomponent"></a>CutPlaneComponent

Egy *CutPlaneComponent*létrehozásával hozzáadhat egy kivágási síkot a jelenethez. A sík helyét és tájolását az összetevő tulajdonosi [entitása](../../concepts/entities.md)határozza meg.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<AzureSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Actions()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->Normal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->FadeColor(fadeColor);
    cutPlane->FadeLength(0.05f); // gradient width: 5cm
}
```


### <a name="cutplanecomponent-properties"></a>CutPlaneComponent tulajdonságai

A következő tulajdonságok vannak kitéve egy kivágott sík összetevőn:

* `Enabled`: Ideiglenesen kikapcsolhatja a kivágott síkokat az összetevő letiltásával. A letiltott síkok nem terhelik a renderelési terhelést, és nem számítanak bele a globálisan kivágott sík korlátba.

* `Normal`: Megadja, hogy a rendszer melyik irányt (+ X,-X, + Y,-Y, + Z,-Z) használja a normál síkon. Ez az irány a tulajdonos entitás tájolásához képest relatív. Helyezze át és forgassa el a tulajdonos entitást a pontos elhelyezéshez.

* `FadeColor`és `FadeLength` :

  Ha a *FadeColor* alfa értéke nem nulla, a kivágási sík közelébe mutató képpontok a FadeColor RGB-részéhez fognak elhalványulni. Az alfa-csatorna erőssége határozza meg, hogy teljes mértékben elhalványul-e az elhalványulás színe, vagy csak részben. A *FadeLength* határozza meg, hogy a Halványítás milyen távolságra legyen végrehajtva.

## <a name="next-steps"></a>További lépések

* [Egyoldalas megjelenítés](single-sided-rendering.md)
* [Térbeli lekérdezések](spatial-queries.md)
