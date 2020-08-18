---
title: Mintamodellek
description: A minta modellek forrásait sorolja fel.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 8e5dcb6c9dfa08efc0889fcab779d6cb333d2330
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507535"
---
# <a name="sample-models"></a>Mintamodellek

Ez a cikk az Azure távoli renderelési szolgáltatás teszteléséhez használható mintaadatok egyes erőforrásait sorolja fel.

## <a name="built-in-sample-model"></a>Beépített minta modell

Egy beépített minta modellt biztosítunk, amely mindig betölthető az URL- **Builtin://Engine** használatával

![Mintamodell](./media/sample-model.png "Mintamodell")

Modell statisztikái:

| Name (Név) | Érték |
|-----------|:-----------|
| [Szükséges kiszolgáló mérete](../how-tos/session-rest-api.md#create-a-session) | Standard |
| Háromszögek száma | 18 700 000 |
| Mozgó részek száma | 2073 |
| Anyagok száma | 94 |

## <a name="third-party-data"></a>Harmadik féltől származó adatszolgáltatások

A Khronos csoport a teszteléshez glTF-mintákat tart fenn. Az ARR a glTF formátumot is támogatja szöveg (*. glTF*) és bináris (*. borai*) formában. Javasoljuk, hogy használja a PBR-modelleket a legjobb vizuális eredményekhez:

* [glTF-minták](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>További lépések

* [Gyors útmutató: modell megjelenítése egységgel](../quickstarts/render-model.md)
* [Gyors útmutató: modell átalakítása renderelésre](../quickstarts/convert-model.md)
