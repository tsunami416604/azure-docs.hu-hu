---
title: Mintamodellek
description: A mintamodellek forrásainak listája.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 0c28d36c7934fbbac0ddd11562c8dc237b0360ee
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679484"
---
# <a name="sample-models"></a>Mintamodellek

Ez a cikk az Azure remote rendering szolgáltatás teszteléséhez használható mintaadatok egyes erőforrásait sorolja fel.

## <a name="built-in-sample-model"></a>Beépített mintamodell

Beépített mintamodellt biztosítunk, amely mindig betölthető az **URL-builtin://Engine**

![Mintamodell](./media/sample-model.png "Mintamodell")

Modell statisztika:

| Név | Érték |
|-----------|:-----------|
| [Szükséges virtuális gép mérete](../how-tos/session-rest-api.md#create-a-session) | Standard |
| Háromszögek száma | 18 millió |
| Mozgatható alkatrészek száma | 2073 |
| Anyagok száma | 94 |

## <a name="third-party-data"></a>Harmadik fél től származó adatok

A Khronos Csoport egy sor glTF mintamodellt tart fenn tesztelésre. Az ARR támogatja a glTF formátumot szövegben (*.gltf*) és bináris (*.glb*) formában. Javasoljuk, hogy a legjobb vizuális eredmények érdekében használja a PBR modelleket:

* [glTF mintamodellek](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Modell renderelése unityvel](../quickstarts/render-model.md)
* [Rövid útmutató: Modell konvertálása rendereléshez](../quickstarts/convert-model.md)
