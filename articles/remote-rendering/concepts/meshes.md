---
title: Hálók
description: A rácsvonalak meghatározása az Azure távoli renderelés hatókörében
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681765"
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

## <a name="next-steps"></a>További lépések

* [Anyagok](materials.md)
