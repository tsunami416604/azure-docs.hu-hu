---
title: Objektumhatárok
description: A térbeli objektumhatárok lekérdezésének módját ismerteti
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681713"
---
# <a name="object-bounds"></a>Objektumhatárok

Az objektumhatárok azt a kötetet jelölik, amelyet az [entitás](entities.md) és gyermekei elfoglalnak. Az Azure távoli renderelés, objektum határok mindig *tengelyhez igazított határolókeret* (AABB) mindig. Az objektumhatárok lehetnek a *helyi vagy* a *világtérben.* Akárhogy is, ezek mindig tengely-igazított, ami azt jelenti, a terjedelem és a hangerő eltérhet a helyi és a világ tér ábrázolása.

## <a name="querying-object-bounds"></a>Objektumhatárok lekérdezése

A [háló](meshes.md) helyi AABB-ja közvetlenül a hálóerőforrásból kérdezhető le. Ezek a határok az entitás átalakításával átalakíthatók egy entitás helyi vagy világterébe.

A teljes objektumhierarchia határait így lehet kiszámítani, de ehhez át kell haladnia a hierarchián, le kell kérdeznie az egyes hálók határait, és manuálisan kell kombinálnia őket. Ez a művelet egyszerre unalmas és nem hatékony.

Egy jobb módja `QueryLocalBoundsAsync` az, hogy hívja, vagy `QueryWorldBoundsAsync` egy entitás. A számítás ezután kikerül a kiszolgálóra, és minimális késleltetéssel adja vissza.

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
