---
title: Hierarchikus állapot felülbírálása
description: A hierarchikus állapot felülbírálási összetevőinek fogalmát ismerteti.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 5ef5af77831c01ae484398c1f2d8905e5e2bc11e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84021330"
---
# <a name="hierarchical-state-override"></a>Hierarchikus állapot felülbírálása

Sok esetben szükség van a [modell](../../concepts/models.md)részeinek megjelenésének dinamikus módosítására, például az algráfok elrejtésére vagy a részek átváltására transzparens megjelenítésre. Az egyes részekben található anyagok módosítása nem praktikus, mivel a teljes jelenet gráfon való iterációra, valamint az egyes csomópontokon az anyagok klónozásának és hozzárendelésének kezelésére van szükség.

Ha a lehető legkevesebb terheléssel szeretné elérni a használati esetet, használja a következőt: `HierarchicalStateOverrideComponent` . Ez az összetevő hierarchikus állapotú frissítéseket valósít meg a jelenet gráf tetszőleges ágain. Ez azt jelenti, hogy az állapot bármilyen szinten meghatározható a Scene Graphban, és a hierarchiában leszivárog, egészen addig, amíg egy új állapot felülbírálja, vagy egy levél objektumra van alkalmazva.

Tegyük fel például, hogy egy autó modelljét szeretné átadni, és a belső motor részének kivételével át kívánja váltani az egész autót. Ez a használati eset az összetevő két példányát foglalja magában:

* Az első összetevő a modell legfelső szintű csomópontjára van rendelve, és a teljes autó transzparens megjelenítését kapcsolja be.
* A második összetevő a motor gyökérszintű csomópontjára van hozzárendelve, és ismét felülbírálja az állapotot, ha explicit módon kikapcsolja a megtekintési módot.

## <a name="features"></a>Szolgáltatások

A felülbírálható állapotok rögzített halmaza:

* **`Hidden`**: A jelenet gráfban található megfelelő rácsvonalak rejtettek vagy láthatók.
* **`Tint color`**: Egy megjelenített objektum színezhető színezhető az egyedi színárnyalat és a színárnyalat súlyozásával. Az alábbi képen egy kerék peremének színárnyalata látható.
  
  ![Színárnyalat](./media/color-tint.png)

* **`See-through`**: A geometria félig transzparens módon jelenik meg, például egy objektum belső részeinek megjelenítéséhez. Az alábbi képen látható, hogy a teljes autó a látható módon jelenik meg, kivéve a piros fék vastagságát:

  ![Áttekintő](./media/see-through.png)

  > [!IMPORTANT]
  > A megtekintési effektus csak akkor működik, ha a *TileBasedComposition* [renderelési módot](../../concepts/rendering-modes.md) használja.

* **`Selected`**: A geometria egy [kijelölési körvonalsal](outlines.md)jelenik meg.

  ![Kijelölési körvonal](./media/selection-outline.png)

* **`DisableCollision`**: A geometria mentesül a [térbeli lekérdezések](spatial-queries.md)alól. A **`Hidden`** jelző nem befolyásolja az ütközési állapot jelzőjét, így a két jelzőt gyakran együtt kell beállítani.

## <a name="hierarchical-overrides"></a>Hierarchikus felülbírálások

A `HierarchicalStateOverrideComponent` csatolható egy objektum-hierarchia több szintjén is. Mivel az entitásokban csak egyetlen összetevő lehet, a rendszer a `HierarchicalStateOverrideComponent` rejtett, a megjelenő, a kijelölt, a színárnyalatok és az ütközések állapotait kezeli.

Ezért az egyes állapotok az alábbiak egyikére állíthatók be:

* `ForceOn`– az állapot engedélyezve van az összes rácsvonal számára ezen a csomóponton.
* `ForceOff`– az állapot le van tiltva az összes rácsvonalon ezen a csomóponton.
* `InheritFromParent`– Ez a felülbírálási összetevő nem érinti az állapotot

Az állapotokat közvetlenül vagy a függvény használatával módosíthatja `SetState` :

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->HiddenState(HierarchicalEnableState::ForceOn);

// set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>Színárnyalat színe

A `tint color` felülbírálás némileg különleges abban az állapotban, hogy a be-és kikapcsolás/öröklési állapot és a színárnyalat-szín tulajdonság is szerepel. Az árnyalat színének alfa része határozza meg az árnyalatos hatás súlyozását: Ha a 0,0 értékre van állítva, a színárnyalat nem látható, és ha a értéke 1,0, az objektum tiszta tónusú színnel jelenik meg. Az értékek között a végső szín az árnyalat színével lesz keverve. A színárnyalatok színének megváltoztatásához a színárnyalat a képkockák alapján módosítható.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Maga egy példánya `HierarchicalStateOverrideComponent` nem ad hozzá sok futásidejű terhelést. Azonban mindig érdemes megtartani az aktív összetevők számát. Ha például olyan kiválasztási rendszer megvalósítását tervezi, amely kiemeli a kiválasztott objektumot, akkor azt javasoljuk, hogy törölje az összetevőt a kiemelés eltávolításakor. Az összetevők és a semleges funkciók megtartása gyorsan felvehető.

Az átlátszó renderelés több számítási feladatot tesz lehetővé a kiszolgáló GPU-nál a normál megjelenítésnél. Ha a Scene gráf nagy része átváltott, és a geometria számos rétege *látható, akkor*a teljesítmény szűk keresztmetszetet eredményezhet. Ugyanez érvényes a [kijelölési körvonalakkal](../../overview/features/outlines.md#performance)rendelkező objektumok esetében is.

## <a name="next-steps"></a>További lépések

* [Ismerteti](../../overview/features/outlines.md)
* [Renderelési módok](../../concepts/rendering-modes.md)
* [Térbeli lekérdezések](../../overview/features/spatial-queries.md)
