---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: 753a3d16792e0dbaffe318cd188ea3f0d40bd5cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191038"
---
Adjon hozzá olyan `Push-OutputBinding` kódot, amely a parancsmag `msg` segítségével szöveget ír a várólistába a kimeneti kötés használatával. Adja hozzá ezt a kódot, `if` mielőtt beállítja az OK állapotot a kimutatásban.

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

Ezen a ponton a funkciónak a következőképpen kell kinéznie:

:::code language="powershell" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::
