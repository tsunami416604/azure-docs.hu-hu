---
title: Vágott síkok
description: Bemutatja, hogy mik a vágott síkok, és hogyan kell használni őket
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 8075d9cd4530bafb12a338830baf0fe22eb03bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681024"
---
# <a name="cut-planes"></a>Vágott síkok

A *kivágott sík* olyan vizuális jellemző, amely a virtuális sík egyik oldalán képpontokat kap, és felfedi a [szemképek belsejét.](../../concepts/meshes.md)
Az alábbi kép bemutatja a hatást. A bal oldalon az eredeti háló látható, a jobb oldalon a hálóbelsejében lehet kinézni:

![Kivágási sík](./media/cutplane-1.png)

## <a name="limitations"></a>Korlátozások

* Az Azure Remote Rendering egyelőre **legfeljebb nyolc aktív vágási síkot**támogat. Létrehozhat több kivágott sík összetevőt, de ha egyszerre próbál többet engedélyezni, akkor figyelmen kívül hagyja az aktiválást. Először tiltsa le a többi síkot, ha váltani szeretne, hogy melyik összetevő befolyásolja a jelenetet.
* Minden kivágási sík hatással van az összes távolról renderelt objektumra. Jelenleg nincs mód adott objektumok vagy hálórészek kizárására.
* A kivágott síkok pusztán vizuális funkciók, nem befolyásolják a [térbeli lekérdezések](spatial-queries.md)eredményét. Ha azt szeretnénk, hogy ray öntött egy vágott-nyitott háló, beállíthatja a kiindulási pont a sugár, hogy a vágott síkon. Így a sugár csak látható részeket tud eltalálni.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Minden aktív vágási síknak kis költsége merül fel a renderelés során. Tiltsa le vagy törölje a kivágott síkokat, amikor nincs rájuk szükség.

## <a name="cutplanecomponent"></a>CutPlaneComponent (CutPlaneComponent)

*CutPlaneComponent (CutPlaneComponent )* segítségével vágott síkot adhat a jelenethez. A sík helyét és tájolását az összetevő tulajdonosi [entitása](../../concepts/entities.md)határozza meg .

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>CutPlaneComponent tulajdonságai

A következő tulajdonságok vannak kitéve a vágott sík komponens:

* **Engedélyezve:** Az alkatrész letiltásával ideiglenesen kikapcsolhatja a vágósíkokat. A letiltott vágású síkok nem merülnek fel renderelési terheléssel, és nem számítanak bele a globális vágási síkkorlátba.

* **Normál:** Megadja, hogy a program melyik irányt (+X,-X,+Y,-Y,+Z,-Z) használja a normál síkként. Ez az irány a tulajdonos entitás tájolásához viszonyítva történik. A tulajdonos entitás áthelyezése és elforgatása a pontos elhelyezéshez.

* **FadeColor** és **FadeLength:**

  Ha a *FadeColor* alfaértéke nem nulla, a kivágott síkhoz közeli képpontok elhalványulnak a FadeColor RGB része felé. Az alfa-csatorna erőssége határozza meg, hogy teljesen elhalványul-e a halványításszíne felé, vagy csak részben. *A FadeLength* határozza meg, hogy ez a fade milyen távolságra kerül sor.

## <a name="next-steps"></a>További lépések

* [Egyoldalas renderelés](single-sided-rendering.md)
* [Térbeli lekérdezések](spatial-queries.md)
