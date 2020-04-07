---
title: Hierarchikus állapot felülbírálása
description: A hierarchikus állapot felülbírálási összetevőinek fogalmát ismerteti.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: f3be073857cc8583669ab26f306760478479e2ae
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680790"
---
# <a name="hierarchical-state-override"></a>Hierarchikus állapot felülbírálása

Sok esetben dinamikusan meg kell változtatni a [modell](../../concepts/models.md)részeinek megjelenését , például el kell takargatni az algráfokat, vagy át kell állítani az alkatrészeket átlátszó renderelésre. Az egyes érintett részek anyagainak módosítása nem praktikus, mivel az egész jelenetgrafikonon végig kell étkeznie, és minden csomóponton kezelni kell az anyagklónozást és -hozzárendelést.

Ha ezt a használati esetet a `HierarchicalStateOverrideComponent`lehető legkisebb többletterheléssel szeretné elvégezni, használja a . Ez az összetevő hierarchikus állapotfrissítéseket valósít meg a jelenetdiagram tetszőleges ágain. Ez azt jelenti, hogy egy állapot a jelenetdiagram bármely szintjén definiálható, és addig szivárog le a hierarchiában, amíg egy új állapot felül nem bírálja, vagy egy levélobjektumra nem alkalmazza.

Példaként, úgy a modell egy autó, és azt szeretné, hogy kapcsolja be az egész autót, hogy átlátható legyen, kivéve a belső motor része. Ez a használati eset az összetevőnek csak két példányát érinti:

* Az első összetevő a modell gyökércsomópontjához van rendelve, és bekapcsolja az átlátszó renderelést az egész autószámára.
* A második összetevő a motor gyökércsomópontjához van rendelve, és az átlátszó üzemmód kifejezett kikapcsolásával újra felülbírálja az állapotot.

## <a name="features"></a>Szolgáltatások

A felülbírálható állapotok rögzített készletei a következők:

* **Rejtett**: A jelenetdiagramon lévő megfelelő felvételek rejtettek vagy láthatók.
* **Színárnyalat szín:** A renderelt objektum színárnyalattal színezhető az egyes színekkel és színvastagsággal. Az alábbi képen a kerék peremének színárnyalata látható.
  
  ![Színárnyalat](./media/color-tint.png)

* **Átlátszó**: A geometria félig átlátszóvá válik, például egy objektum belső részeinek megjelenítéséhez. Az alábbi képen az egész autó átlátszó üzemmódban jelenik meg, kivéve a piros féknyergét:

  ![Átlátszó](./media/see-through.png)

  > [!IMPORTANT]
  > Az átlátszó hatás csak a *TileBasedComposition* [renderelési mód](../../concepts/rendering-modes.md) használata esetén működik.

* **Kiválasztva**: A geometria [kijelölési körvonallal](outlines.md)jelenik meg.

  ![Kijelölési körvonal](./media/selection-outline.png)

* **DisableCollision**: A geometria mentesül a [térbeli lekérdezések](spatial-queries.md)alól. A **Rejtett** jelző nem kapcsolja ki az ütközéseket, így ez a két jelző gyakran együtt van beállítva.

## <a name="hierarchical-overrides"></a>Hierarchikus felülbírálások

Az `HierarchicalStateOverrideComponent` objektumhierarchia több szintjén is csatolható. Mivel egy entitáson minden típusnak csak egy `HierarchicalStateOverrideComponent` összetevője lehet, mindegyik kezeli a rejtett, átlátszó, kiválasztott, színárnyalat és ütközés állapotát.

Ezért minden állam a következők egyikére állítható:

* `ForceOn`- az állapot engedélyezve van az összes hálóhoz ezen a csomóponton és alatt
* `ForceOff`- az állapot le van tiltva az összes olyan és az alatt lévő vagy az alatt lévő
* `InheritFromParent`- az állapotot nem befolyásolja ez a felülbírálási összetevő

Az állapotokat közvetlenül `SetState` vagy a funkción keresztül módosíthatja:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

### <a name="tint-color"></a>Színárnyalat színe

A színárnyalat színfelülírása kissé különleges, mivel egyszerre van be/ki/öröklődik, és egy színárnyalat-tulajdonság. A színszín alfa-része határozza meg a színezőhatás súlyát: Ha 0,0-ra van állítva, akkor nem látható színszín, és ha 1,0-ra van állítva, az objektum színszínnel jelenik meg. A köztes értékek esetében a végső szín keveredik a színszínnel. A színárnyalat színe képkockánként módosítható a színanimáció elérése érdekében.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Egy példány `HierarchicalStateOverrideComponent` önmagában nem ad sok futásidejű terhelést. Azonban mindig jó gyakorlat, hogy az aktív összetevők száma alacsony. Ha például olyan kijelölési rendszert valósít meg, amely kiemeli a kitárolt objektumot, a csúcsfény eltávolításakor ajánlott törölni az összetevőt. Az alkatrészek semleges funkciókkal való megtartása gyorsan összeadódhat.

Az átlátszó renderelés nagyobb terhelést helyez el a kiszolgáló GPU-in, mint a normál renderelés. Ha a jelenetdiagram nagy része *átlátszóra*vált, és a geometria számos rétege látható, akkor az teljesítményszűk keresztmetszetté válhat. Ugyanez vonatkozik a [kijelöléskörvonallal](../../overview/features/outlines.md#performance)rendelkező objektumokra is.

## <a name="next-steps"></a>További lépések

* [Felvázolja](../../overview/features/outlines.md)
* [Renderelési módok](../../concepts/rendering-modes.md)
* [Térbeli lekérdezések](../../overview/features/spatial-queries.md)
