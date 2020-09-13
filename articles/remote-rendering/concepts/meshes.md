---
title: Hálók
description: A rácsvonalak meghatározása az Azure távoli renderelés hatókörében
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: eea43f48abef5e2b258251d46eca1061a2263519
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613831"
---
# <a name="meshes"></a>Hálók

## <a name="mesh-resource"></a>Háló erőforrás

A rácsvonalak egy nem módosítható [megosztott erőforrás](../concepts/lifetime.md), amely csak [modell-átalakítással](../how-tos/conversion/model-conversion.md)hozható létre. A rácsvonalak egy vagy több *alhálót*tartalmaznak. Minden alháló egy olyan [anyagra](materials.md) hivatkozik, amelyben alapértelmezés szerint meg kell jeleníteni. Ha a hálót 3D területre kívánja elhelyezni, adjon hozzá egy [MeshComponent](#meshcomponent) egy [entitáshoz](entities.md).

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

## <a name="api-documentation"></a>API-dokumentáció

* [C# háló osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.mesh)
* [C# MeshComponent osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [C++ háló osztály](https://docs.microsoft.com/cpp/api/remote-rendering/mesh)
* [C++ MeshComponent osztály](https://docs.microsoft.com/cpp/api/remote-rendering/meshcomponent)

## <a name="next-steps"></a>Következő lépések

* [Anyagok](materials.md)
