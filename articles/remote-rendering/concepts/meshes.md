---
title: Rácsvonalak
description: A menek meghatározása az Azure távoli renderelés hatókörében
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681765"
---
# <a name="meshes"></a>Rácsvonalak

## <a name="mesh-resource"></a>Háló erőforrás

A szemegységek egy megváltoztathatatlan [megosztott erőforrás](../concepts/lifetime.md), amely csak [modellátalakítással](../how-tos/conversion/model-conversion.md)hozható létre . A minden rendben van egy vagy több *alkonysal.* Minden részháló olyan [anyagra](materials.md) hivatkozik, amellyel alapértelmezés szerint meg kell jelennie. Ha hálót szeretne elhelyezni a 3D térben, vegyen fel egy [MeshComponent összetevőt](#meshcomponent) [egy entitásba](entities.md).

### <a name="mesh-resource-properties"></a>Háló erőforrás tulajdonságai

Az `Mesh` osztály tulajdonságai a következők:

* **Anyagok:** Egy sor anyag. Minden anyagot egy másik alháló használ. A tömb több bejegyzése is hivatkozhat ugyanarra az [anyagra](materials.md). Ezek az adatok nem módosíthatók futásidőben.

* **Határok:** A hálócsúcsok helyi tértengelyhez igazított határolókerete (AABB).

## <a name="meshcomponent"></a>Hálós összetevő

Az `MeshComponent` osztály egy hálóerőforrás példányának elhelyezésére szolgál. Minden MeshComponent egyetlen hálóra hivatkozik. Felülbírálhatja, hogy mely anyagokat használja az egyes részhálók megjelenítéséhez.

### <a name="meshcomponent-properties"></a>MeshComponent tulajdonságai

* **Háló:** Az összetevő által használt hálóerőforrás.

* **Anyagok:** A háló komponensen megadott anyagok tömbje. A tömb hossza mindig megegyezik a hálóerőforrás *Anyag* tömbjével. Azok az anyagok, amelyeket nem szabad felülírni a háló alapértelmezett értékéből, *null* értékűek ebben a tömbben.

* **Használtanyagok:** Az egyes alhálók ténylegesen felhasznált anyagainak tömbje. Az *Anyagok* tömbben lévő adatokkal azonos lesz, nem null értékek esetén. Ellenkező esetben a hálópéldány *Anyagtömbjének* értékét tartalmazza.

## <a name="next-steps"></a>További lépések

* [Nyersanyagok](materials.md)
