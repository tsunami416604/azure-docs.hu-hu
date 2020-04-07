---
title: Nyersanyagok
description: Renderelés imátanyag-leírás és anyagtulajdonságok
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 8551e17ddd71e76aca0c85b9768f564ae0e5f049
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681843"
---
# <a name="materials"></a>Nyersanyagok

Az anyagok [megosztott erőforrások,](../concepts/lifetime.md) amelyek meghatározzák [a közvetlen műveletek](meshes.md) megjelenítésének módját. Az anyagok segítségével határozható meg, hogy mely [textúrákat](textures.md) kell alkalmazni, hogy az objektumok átlátszóak legyenek-e, és hogyan számítják ki a világítást.

Az anyagok automatikusan létrejönnek a [modellátalakítás](../how-tos/conversion/model-conversion.md) során, és futásidőben érhetők el. Egyéni anyagokat is létrehozhat a kódból, és lecserélheti a meglévőket. Ez a forgatókönyv különösen akkor van értelme, ha ugyanazt az anyagot szeretné megosztani sok szem között. Mivel egy anyag módosításai minden arra hivatkozó hálón láthatók, ez a módszer a módosítások egyszerű alkalmazására használható.

> [!NOTE]
> Egyes használati esetek, például a kitárolt objektum kiemelése, az anyagok módosításával elvégezhetők, de sokkal könnyebben megvalósíthatók a [HierarchicalStateOverrideComponent segítségével.](../overview/features/override-hierarchical-state.md)

## <a name="material-types"></a>Anyagtípusok

Az Azure távoli renderelésének két különböző anyagtípusa van:

* [A PBR-anyagokat](../overview/features/pbr-materials.md) olyan felületekhez használják, amelyeket a lehető legfizikailag megfelelővé kell tenni. A valósághű megvilágítást fizikai *alapú renderelés* (PBR) segítségével számítják ki ezekhez az anyagokhoz. Ahhoz, hogy a legtöbbet hozhassa ki ebből az anyagtípusból, fontos, hogy kiváló minőségű bemeneti adatokat, például érdességet és normál térképeket biztosítson.

* [Színes anyagokat](../overview/features/color-materials.md) használnak azokban az esetekben, ahol nincs szükség további világítás. Ezek az anyagok mindig teljes fényes és könnyebb beállítani. A színes anyagokolyan adatokhoz használatosak, amelyeknek egyáltalán nem kell világítással rendelkezniük, vagy már statikus megvilágítást is tartalmaznak, például [a fotogrammetria](https://en.wikipedia.org/wiki/Photogrammetry)i modelljével.

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Háló és MeshComponent anyaghozzárendelés

[A menek](meshes.md) egy vagy több submeshes. Minden részháló egy anyagra hivatkozik. Módosíthatja azt az anyagot, amelyet közvetlenül a hálón szeretne használni, vagy felülbírálhatja, hogy melyik anyagot használja a [MeshComponent alhálójának.](meshes.md#meshcomponent)

Ha egy anyagot közvetlenül a hálóerőforráson módosít, ez a módosítás a háló összes példányára hatással van. A MeshComponent összetevőn való módosítása azonban csak egy hálópéldányra van hatással. Az, hogy melyik módszer megfelelőbb, a kívánt viselkedéstől függ, de a MeshComponent módosítása a leggyakoribb megközelítés.

## <a name="material-classes"></a>Anyagosztályok

Az API által biztosított összes anyag `Material`az alaposztályból származik. Típusuk lehet kérdezni keresztül, `Material.MaterialSubType` vagy öntés őket közvetlenül:

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

* [PBR anyagok](../overview/features/pbr-materials.md)
* [Színes anyagok](../overview/features/color-materials.md)
