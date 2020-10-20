---
title: Hálók
description: A rácsvonalak meghatározása az Azure távoli renderelés hatókörében
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 628331677a84f6cf1dd8fa1df1e7f6c1a2fa09e1
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202803"
---
# <a name="meshes"></a>Hálók

## <a name="mesh-resource"></a>Háló erőforrás

A rácsvonalak egy nem módosítható [megosztott erőforrás](../concepts/lifetime.md), amely csak [modell-átalakítással](../how-tos/conversion/model-conversion.md)hozható létre. A rácsvonalak egy vagy több *alhálót* tartalmaznak a [raycast-lekérdezések](../overview/features/spatial-queries.md)fizikai ábrázolásával együtt. Minden alháló egy olyan [anyagra](materials.md) hivatkozik, amelyben alapértelmezés szerint meg kell jeleníteni. Ha a hálót 3D területre kívánja elhelyezni, adjon hozzá egy [MeshComponent](#meshcomponent) egy [entitáshoz](entities.md).

### <a name="mesh-resource-properties"></a>Rácsvonal erőforrásának tulajdonságai

Az `Mesh` osztály tulajdonságai a következők:

* **Anyagok:** Anyagok tömbje. Minden anyagot egy másik alháló használ. A tömbben több bejegyzés is hivatkozhat ugyanarra az [anyagra](materials.md). Ezek az adatkészletek nem módosíthatók futásidőben.

* **Korlátok:** A rácsvonalak csúcspontjának helyi tengelyre igazított (AABB) határoló mezője.

## <a name="meshcomponent"></a>MeshComponent

Az `MeshComponent` osztály egy rácsvonal-erőforrás példányának elhelyezésére szolgál. Minden MeshComponent egyetlen rácsvonalra hivatkozik. Az egyes alhálók megjelenítéséhez használt anyagokat felül lehet bírálni.

### <a name="meshcomponent-properties"></a>MeshComponent tulajdonságai

* **Háló:** Az összetevő által használt rácsvonal-erőforrás.

* **Anyagok:** A háló összetevőn meghatározott anyagok tömbje. A tömb mindig ugyanolyan hosszúságú lesz, mint a Mesh erőforrásban található *anyagok* tömbje. Azok az anyagok, amelyek nem lesznek felülbírálva a háló alapértelmezésével, *Null* értékre vannak állítva ebben a tömbben.

* **UsedMaterials:** A ténylegesen felhasznált anyagok tömbje az egyes alhálók esetében. Azonos lesz az *anyagok* tömbben lévő adatokkal, nem null érték esetén. Ellenkező esetben a Mesh-példányban található *anyagok* tömb értékét tartalmazza.

### <a name="sharing-of-meshes"></a>Rácsvonalak megosztása

Egy `Mesh` erőforrás több háló-összetevő példánya között is megosztható. Emellett a `Mesh` háló összetevőhöz rendelt erőforrás bármikor programozott módon módosítható. Az alábbi kód a rácsvonalak klónozását mutatja be:

```cs
Entity CloneEntityWithModel(RemoteManager manager, Entity sourceEntity)
{
    MeshComponent meshComp = sourceEntity.FindComponentOfType<MeshComponent>();
    if (meshComp != null)
    {
        Entity newEntity = manager.CreateEntity();
        MeshComponent newMeshComp = manager.CreateComponent(ObjectType.MeshComponent, newEntity) as MeshComponent;
        newMeshComp.Mesh = meshComp.Mesh; // share the mesh
        return newEntity;
    }
    return null;
}
```

```cpp
ApiHandle<Entity> CloneEntityWithModel(ApiHandle<RemoteManager> manager, ApiHandle<Entity> sourceEntity)
{
    if (ApiHandle<MeshComponent> meshComp = sourceEntity->FindComponentOfType<MeshComponent>())
    {
        ApiHandle<Entity> newEntity = *manager->CreateEntity();
        ApiHandle<MeshComponent> newMeshComp = manager->CreateComponent(ObjectType::MeshComponent, newEntity)->as<RemoteRendering::MeshComponent>();
        newMeshComp->SetMesh(meshComp->GetMesh()); // share the mesh
        return newEntity;
    }
    return nullptr;
}
```

## <a name="api-documentation"></a>API-dokumentáció

* [C# háló osztály](/dotnet/api/microsoft.azure.remoterendering.mesh)
* [C# MeshComponent osztály](/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [C++ háló osztály](/cpp/api/remote-rendering/mesh)
* [C++ MeshComponent osztály](/cpp/api/remote-rendering/meshcomponent)


## <a name="next-steps"></a>Következő lépések

* [Anyagok](materials.md)