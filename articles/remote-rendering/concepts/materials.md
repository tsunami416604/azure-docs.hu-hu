---
title: Anyagok
description: Renderelési anyagok leírása és az anyagok tulajdonságai
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 8551e17ddd71e76aca0c85b9768f564ae0e5f049
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681843"
---
# <a name="materials"></a>Anyagok

Az anyagok olyan [megosztott erőforrások](../concepts/lifetime.md) , amelyek meghatározzák a [Rácsvonalak](meshes.md) megjelenítésének módját. Az anyagok segítségével megadhatja, hogy mely [textúrákat](textures.md) kell alkalmazni, hogy az objektumok transzparensek legyenek, és a rendszer hogyan számítsa ki a világítást.

Az anyagok automatikusan létrejönnek a [modell átalakítása](../how-tos/conversion/model-conversion.md) során, és elérhetők futásidőben. Létrehozhat egyéni anyagokat is a kódban, és lecserélheti a meglévőket. Ez a forgatókönyv különösen akkor hasznos, ha ugyanazt az anyagot több rácsvonalon szeretné megosztani. Mivel az anyagok módosítása minden olyan rácsvonalon látható, amely hivatkozik rá, ezzel a módszerrel egyszerűen alkalmazhatja a módosításokat.

> [!NOTE]
> Egyes használati esetek, például a kiválasztott objektumok kiemelése az anyagok módosításával végezhető el, de sokkal könnyebben megvalósítható a [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md).

## <a name="material-types"></a>Anyagok típusai

Az Azure távoli renderelés két különböző típusú anyagot tartalmaz:

* A [pbr-anyagokat](../overview/features/pbr-materials.md) olyan felületek használják, amelyeket a lehető legpontosabban kell megjeleníteni. A reális megvilágítás kiszámítása ezen anyagok esetében *fizikai alapú renderelés* (pbr) használatával történik. Ahhoz, hogy a lehető legtöbbet hozza ki az anyag típusától, fontos, hogy magas színvonalú bemeneti adatokat adjon meg, például a durva és a normál térképeket.

* A [színanyagokat](../overview/features/color-materials.md) olyan esetekre használja a rendszer, amikor nincs szükség további megvilágítás megvilágítására. Ezek az anyagok mindig teljes mértékben világosak, és egyszerűbben állíthatók be. A színanyagokat olyan adatforrások használják, amelyeknek nincs megvilágítás egyáltalán, vagy már tartalmaz statikus megvilágítás, például a [photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry)keresztül beszerzett modellek.

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Háló és MeshComponent anyagok hozzárendelése

A [Rácsvonalak](meshes.md) egy vagy több alhálóval rendelkeznek. Mindegyik alháló egy anyagra hivatkozik. Az anyagot közvetlenül a rácsvonalon is módosíthatja, vagy felülbírálhatja, hogy mely anyagokat kell használni egy [MeshComponent](meshes.md#meshcomponent)alhálóhoz.

Ha közvetlenül a Mesh erőforráson módosít egy anyagot, ez a változás a rácsvonal összes példányát érinti. A MeshComponent módosítása azonban csak az egyik rácsvonal-példányt érinti. A legmegfelelőbb módszer a kívánt viselkedéstől függ, de a MeshComponent módosítása a gyakoribb megközelítés.

## <a name="material-classes"></a>Anyagelszámolású osztályok

Az API által biztosított összes anyag az alaposztályból `Material`származik. A típusuk közvetlenül a következő módon `Material.MaterialSubType` kérdezhető le:

``` cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if( pbrMat!= null )
    {
        PbrMaterial pbrMaterial = material.PbrMaterial.Value;
        pbrMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

## <a name="next-steps"></a>További lépések

* [PBR-anyagok](../overview/features/pbr-materials.md)
* [Színes anyagok](../overview/features/color-materials.md)
