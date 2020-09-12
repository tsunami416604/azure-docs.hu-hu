---
title: Jelenet megvilágítása
description: A fényforrás leírása és tulajdonságai
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 2ddce7e2f073914e9393eabf12e8b0f4ef347828
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613644"
---
# <a name="scene-lighting"></a>Jelenet megvilágítása

Alapértelmezés szerint a távolról megjelenített objektumok egy [Sky-fénnyel](sky.md)vannak kivilágítva. A legtöbb alkalmazás esetében ez már elegendő, de további fényforrásokat is hozzáadhat a jelenethez.

> [!IMPORTANT]
> A fényforrások csak a [pbr-anyagokat](pbr-materials.md) érintik. A [színanyagok](color-materials.md) mindig teljesen világos színnel jelennek meg.

> [!NOTE]
> Az árnyékok öntése jelenleg nem támogatott. Az Azure távoli renderelés nagy mennyiségű geometria renderelésére van optimalizálva, ha szükséges, több GPU-t is felhasználva. A Shadow Casting hagyományos megközelítései ilyen helyzetekben nem működnek megfelelően.

## <a name="common-light-component-properties"></a>Gyakori világos összetevő tulajdonságai

Minden világos típus az absztrakt alaposztályból származik `LightComponent` , és megosztja ezeket a tulajdonságokat:

* **Szín:** A fény színe a gamma- [térben](https://en.wikipedia.org/wiki/SRGB). Az Alpha figyelmen kívül lesz hagyva.

* **Intenzitás:** A fény fényessége A pont-és direktszínek esetében az intenzitás azt is meghatározza, hogy a fény milyen messzire ragyog.

## <a name="point-light"></a>Pont fénye

Az Azure távoli renderelés során a `PointLightComponent` nem csak egyetlen pontról, hanem egy kis gömbből vagy egy kis csőből is képes fényt kibocsátani a lágyabb fényforrások szimulálása érdekében.

### <a name="pointlightcomponent-properties"></a>PointLightComponent tulajdonságai

* **RADIUS:** Az alapértelmezett sugár nulla, ebben az esetben a fény pont fényként működik. Ha a sugár nullánál nagyobb, a gömb alakú fényforrásként funkcionál, amely megváltoztatja a visszaverődési csúcsfények megjelenését.

* **Hossz:** Ha mindkettő `Length` és `Radius` nem nulla, a fény cső fényként működik. Ez a neon-csövek szimulálására használható.

* **AttenuationCutoff:** Ha a bal oldali (0, 0) értékre áll, a fény gyengülése csak a sajáttól függ `Intensity` . Megadhat azonban olyan egyéni minimális/maximális távolságot, ameddig a fény intenzitása lineárisan 0-ra van méretezve. Ezzel a funkcióval egy adott fény befolyásának kisebb körét lehet kikényszeríteni.

* **ProjectedCubemap:** Ha érvényes [cubemap](../../concepts/textures.md)van beállítva, a textúrát a rendszer a fény környező geometriáján vetíti. A cubemap színe a fény színével van modulálva.

## <a name="spot-light"></a>Helyszíni fény

A hasonló a következőhöz, `SpotLightComponent` `PointLightComponent` de a fény korlátozza a kúp alakját. A kúp tájolását a *tulajdonosi entitás negatív z-tengelye határozza meg*.

### <a name="spotlightcomponent-properties"></a>SpotLightComponent tulajdonságai

* **RADIUS:** Ugyanaz, mint a esetében `PointLightComponent` .

* **SpotAngleDeg:** Ez az intervallum a kúp belső és külső szögét határozza meg fokban mérve. A belső szögen belül minden a teljes fényerővel világít. A rendszer egy hatókört alkalmaz a Penumbra-effektust generáló külső szög irányába.

* **FalloffExponent:** Meghatározza, hogy a belső és a külső kúp szöge között a fakulási átmenetek milyen élesben legyenek. A magasabb érték élesebb átmenetet eredményez. Az alapértelmezett 1,0 a lineáris átmenetet eredményezi.

* **AttenuationCutoff:** Ugyanaz, mint a esetében `PointLightComponent` .

* **Projected2dTexture:** Ha érvényes [2D-textúrára](../../concepts/textures.md)van beállítva, a rendszer a képet a geometriában ábrázolja, és a fény ragyog. A textúra színe a fény színével van modulálva.

## <a name="directional-light"></a>Irányított fény

A `DirectionalLightComponent` egy olyan fényforrás szimulálása, amely végtelenül távol van. A fény a *tulajdonos entitás negatív z tengelyének*irányába mutat. Az entitás pozícióját a rendszer figyelmen kívül hagyja.

Nincsenek további tulajdonságok.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

A fényforrások jelentős hatással vannak a renderelési teljesítményre. Körültekintően használhatja őket, és csak akkor, ha az alkalmazás igényli. Bármilyen statikus globális megvilágítási feltétel, beleértve a statikus irányú összetevőket is, az [Egyéni égbolt textúrával](sky.md)is megvalósítható, amely nem tartalmaz további renderelési költségeket.

## <a name="api-documentation"></a>API-dokumentáció

* [C# LightComponentBase osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.lightcomponentbase)
* [C# PointLightComponent osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.pointlightcomponent)
* [C# SpotLightComponent osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.spotlightcomponent)
* [C# DirectionalLightComponent osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.directionallightcomponent)
* [C++ LightComponentBase osztály](https://docs.microsoft.com/cpp/api/remote-rendering/lightcomponentbase)
* [C++ PointLightComponent osztály](https://docs.microsoft.com/cpp/api/remote-rendering/pointlightcomponent)
* [C++ SpotLightComponent osztály](https://docs.microsoft.com/cpp/api/remote-rendering/spotlightcomponent)
* [C++ DirectionalLightComponent osztály](https://docs.microsoft.com/cpp/api/remote-rendering/directionallightcomponent)

## <a name="next-steps"></a>Következő lépések

* [Anyagok](../../concepts/materials.md)
* [Ég](sky.md)
