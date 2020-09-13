---
title: Objektumhatárok
description: Ismerteti, hogyan lehet lekérdezni a térbeli objektumok határait
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: bd2b1026f69b5ec5814124a92d6a78d7f0225143
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613818"
---
# <a name="object-bounds"></a>Objektumhatárok

Az objektum-korlátok az [entitások](entities.md) és a gyermekeik által elfoglalt köteteket jelölik. Az Azure távoli renderelésben a rendszer mindig a *tengelyhez igazított határolókeret* (AABB) jelölésű objektum-határokat adja meg. Az objektumok határai a *helyi térben* vagy a *globális térben*is lehetnek. Mindkét esetben a tengely igazítva van, ami azt jelenti, hogy a mértékek és a kötetek eltérhetnek a helyi és a globális terület ábrázolástól.

## <a name="querying-object-bounds"></a>Objektum-korlátok lekérdezése

A [háló](meshes.md) helyi AABB közvetlenül a Mesh erőforrásból kérdezhető le. Ezek a korlátok az entitás átalakításával alakíthatók át egy entitás helyi területére vagy területére.

Ily módon lehetséges a teljes objektum-hierarchia határainak kiszámítása, de ez a hierarchia bejárásához, az egyes rácsvonalak határainak lekérdezéséhez, valamint a manuális összekapcsolásához szükséges. Ez a művelet unalmas és nem hatékony.

A jobb módszer az `QueryLocalBoundsAsync` entitások hívása vagy használata `QueryWorldBoundsAsync` . Ekkor a rendszer kiszervezi a számítást a kiszolgálóra, és minimális késleltetéssel visszaadja.

```cs
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

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    ApiHandle<BoundsQueryAsync> boundsQuery = *entity->QueryWorldBoundsAsync();
    boundsQuery->Completed([](ApiHandle<BoundsQueryAsync> bounds)
    {
        if (bounds->GetIsRanToCompletion())
        {
            Double3 aabbMin = bounds->GetResult().min;
            Double3 aabbMax = bounds->GetResult().max;
            // ...
        }
    });
}
```

## <a name="api-documentation"></a>API-dokumentáció

* [C# entitás. QueryLocalBoundsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [C++ entitás:: QueryLocalBoundsAsync](https://docs.microsoft.com/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>Következő lépések

* [Térbeli lekérdezések](../overview/features/spatial-queries.md)
