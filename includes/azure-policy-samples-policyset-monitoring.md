---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: f06a78039217d7bc6b03c9196ac6824477ea977e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79381714"
---
|Név |Leírás |Házirendek |Verzió |
|---|---|---|---|
|[Az Azure-figyelő engedélyezése virtuálisgép-méretezési készletekhez](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Engedélyezze az Azure Monitort a virtuálisgép-méretezési készletekhez a megadott hatókörben (felügyeleti csoport, előfizetés vagy erőforráscsoport). A Log Analytics munkaterületet paraméterként veszi fel. Megjegyzés: ha a méretezési készlet upgradePolicy beállítása Kézi, a bővítményt kell alkalmazni a bővítményt a készlet összes virtuális gépére frissítés hívásával rájuk. A CLI ez lenne az vmss update-példányok. |6 |1.0.1 |
|[Az Azure-figyelő engedélyezése virtuális gépekhez](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Engedélyezze az Azure Monitort a virtuális gépek (VM-ek) számára a megadott hatókörben (felügyeleti csoport, előfizetés vagy erőforráscsoport). A Log Analytics munkaterületet paraméterként veszi fel. |6 |1.0.1 |
