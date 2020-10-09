---
title: Mintamodellek
description: A minta modellek forrásait sorolja fel.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 8e5dcb6c9dfa08efc0889fcab779d6cb333d2330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88507535"
---
# <a name="sample-models"></a>Mintamodellek

Ez a cikk az Azure távoli renderelési szolgáltatás teszteléséhez használható mintaadatok egyes erőforrásait sorolja fel.

## <a name="built-in-sample-model"></a>Beépített minta modell

Egy beépített minta modellt biztosítunk, amely mindig betölthető az URL- **Builtin://Engine** használatával

![Mintamodell](./media/sample-model.png "Mintamodell")

Modell statisztikái:

| Név | Érték |
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
