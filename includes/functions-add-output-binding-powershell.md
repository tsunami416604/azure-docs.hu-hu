---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: 753a3d16792e0dbaffe318cd188ea3f0d40bd5cb
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191038"
---
Vegyen fel olyan kódot, amely a `Push-OutputBinding` parancsmagot használja, hogy szöveget írjon a várólistához a `msg` kimeneti kötés használatával. Adja hozzá ezt a kódot, mielőtt beállítja az OK állapotot a `if` utasításban.

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

Ezen a ponton a függvénynek a következőképpen kell kinéznie:

:::code language="powershell" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::
