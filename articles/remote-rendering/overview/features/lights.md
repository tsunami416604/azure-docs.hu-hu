---
title: Lámpa
description: Fényforrás leírása és tulajdonságai
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 0a4a226af1347b5302b0c3964889fc072f89e7f8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680946"
---
# <a name="lights"></a>Lámpa

Alapértelmezés szerint a távolról renderelt objektumok [égi fénnyel](sky.md)világítanak. A legtöbb alkalmazás esetében ez már elegendő, de további fényforrásokat adhat a jelenethez.

> [!IMPORTANT]
> A fényforrások csak [a PBR-anyagokat](pbr-materials.md) érintik. [A színes anyagok](color-materials.md) mindig teljesen világosnak tűnnek.

> [!NOTE]
> Az árnyékok öntése jelenleg nem támogatott. Az Azure remote rendering úgy van optimalizálva, hogy hatalmas mennyiségű geometriát jelenítsen meg, szükség esetén több GPU-t használva. Az árnyéköntés hagyományos megközelítései nem működnek jól az ilyen helyzetekben.

## <a name="common-light-component-properties"></a>Gyakori fénykomponens-tulajdonságok

Minden fénytípus az absztrakt `LightComponent` alaposztályból származik, és megosztja ezeket a tulajdonságokat:

* **Szín:** A fény színe a [Gamma térben.](https://en.wikipedia.org/wiki/SRGB) Az Alfát figyelmen kívül hagyjuk.

* **Intenzitás:** A fény fényereje. A pont- és spotlámpák esetében az intenzitás azt is meghatározza, hogy a fény milyen messzire ragyog.

## <a name="point-light"></a>Pontfény

Az Azure Remote `PointLightComponent` Renderelés a nem csak fényt bocsát ki egyetlen pontból, hanem egy kis gömb vagy egy kis cső, lágyabb fényforrások szimulálására.

### <a name="pointlightcomponent-properties"></a>PointLightComponent tulajdonságai

* **Sugár:** Az alapértelmezett sugár nulla, ebben az esetben a fény pontfényként működik. Ha a sugár nagyobb, mint nulla, akkor gömb alakú fényforrásként működik, ami megváltoztatja a fényvisszaverő di.

* **Hossza:** Ha `Length` mindkettő, és `Radius` nem nulla, a fény működik, mint egy cső fény. Ezt fel lehet használni, hogy szimulálja neon csövek.

* **Csillapításlevágás:** Ha hagyjuk , hogy (0,0) a csillapítás `Intensity`a fény csak attól függ, hogy . Azonban egyéni min/max távolságokat is megadhat, amelyek felett a fény intenzitása lineárisan 0-ra csökken. Ez a funkció egy adott fény kisebb hatástartományának kikényszerítésére használható.

* **TervezettKockatérkép:** Ha érvényes [kockatérképre](../../concepts/textures.md)van állítva, a textúra a fény környező geometriájára lesz vetítve. A kockatérkép színe a fény színével van modulálva.

## <a name="spot-light"></a>Spot fény

A `SpotLightComponent` hasonló a, `PointLightComponent` de a fény van korlátozva, hogy az alakja egy kúp. A kúp tájolását a *tulajdonos entitás negatív z tengelye*határozza meg .

### <a name="spotlightcomponent-properties"></a>SpotLightComponent tulajdonságai

* **Sugár:** Ugyanaz, mint `PointLightComponent`a .

* **SpotAngleDeg:** Ez az intervallum határozza meg a kúp belső és külső szögét, fokban mérve. A belső szögben minden teljes fényerővel világít. A falloff felé alkalmazzák a külső szög, amely létrehoz egy penumbra-szerű hatást.

* **FalloffExponent:** Azt határozza meg, hogy a belső és a külső kúpszög között milyen élesen alakuljon át a lesibe. A magasabb érték élesebb átmenetet eredményez. Az 1.0 alapértelmezett beállítása lineáris átmenetet eredményez.

* **Csillapításlevágás:** Ugyanaz, mint `PointLightComponent`a .

* **Tervezett2dTextúra:** Ha érvényes [2D textúrára](../../concepts/textures.md)van állítva, a kép olyan geometriára vetítődik, amelyen a fény ragyog. A textúra színe modulált a fény színe.

## <a name="directional-light"></a>Irányított fény

Egy `DirectionalLightComponent` végtelenül távoli fényforrást szimulál. A fény a *tulajdonos entitás negatív z tengelyének*irányába világít. Az entitás pozícióját a rendszer figyelmen kívül hagyja.

Nincsenek további tulajdonságok.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

A fényforrások jelentős hatással vannak a renderelési teljesítményre. Használja őket óvatosan, és csak akkor, ha az alkalmazás megköveteli. Bármilyen statikus globális megvilágítási feltétel, beleértve a statikus irányított komponenst is, [egyéni égbolt-textúrával](sky.md)érhető el, további renderelési költség nélkül.

## <a name="next-steps"></a>További lépések

* [Nyersanyagok](../../concepts/materials.md)
* [Ég](sky.md)
