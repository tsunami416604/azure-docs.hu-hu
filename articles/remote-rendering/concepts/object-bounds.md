---
title: Objektumhatárok
description: Ismerteti, hogyan lehet lekérdezni a térbeli objektumok határait
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681713"
---
# <a name="object-bounds"></a>Objektumhatárok

Az objektum-korlátok az [entitások](entities.md) és a gyermekeik által elfoglalt köteteket jelölik. Az Azure távoli renderelésben a rendszer mindig a *tengelyhez igazított határolókeret* (AABB) jelölésű objektum-határokat adja meg. Az objektumok határai a *helyi térben* vagy a *globális térben*is lehetnek. Mindkét esetben a tengely igazítva van, ami azt jelenti, hogy a mértékek és a kötetek eltérhetnek a helyi és a globális terület ábrázolástól.

## <a name="querying-object-bounds"></a>Objektum-korlátok lekérdezése

A [háló](meshes.md) helyi AABB közvetlenül a Mesh erőforrásból kérdezhető le. Ezek a korlátok az entitás átalakításával alakíthatók át egy entitás helyi területére vagy területére.

Ily módon lehetséges a teljes objektum-hierarchia határainak kiszámítása, de ez a hierarchia bejárásához, az egyes rácsvonalak határainak lekérdezéséhez, valamint a manuális összekapcsolásához szükséges. Ez a művelet unalmas és nem hatékony.

A jobb módszer az entitások `QueryLocalBoundsAsync` hívása vagy `QueryWorldBoundsAsync` használata. Ekkor a rendszer kiszervezi a számítást a kiszolgálóra, és minimális késleltetéssel visszaadja.

``` cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

## <a name="next-steps"></a>További lépések

* [Térbeli lekérdezések](../overview/features/spatial-queries.md)
