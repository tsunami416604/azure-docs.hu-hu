---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 074d28f8144245247944f9c3409507d331c81166
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758630"
---
|Név |Leírás |Házirendek |Verzió |
|---|---|---|---|
|[Az Azure-figyelő engedélyezése virtuálisgép-méretezési készletekhez](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Engedélyezze az Azure Monitort a virtuálisgép-méretezési készletekhez a megadott hatókörben (felügyeleti csoport, előfizetés vagy erőforráscsoport). A Log Analytics munkaterületet paraméterként veszi fel. Megjegyzés: ha a méretezési készlet upgradePolicy beállítása Kézi, a bővítményt kell alkalmazni a bővítményt a készlet összes virtuális gépére frissítés hívásával rájuk. A CLI ez lenne az vmss update-példányok. |6 |1.0.1 |
|[Az Azure-figyelő engedélyezése virtuális gépekhez](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Engedélyezze az Azure Monitort a virtuális gépek (VM-ek) számára a megadott hatókörben (felügyeleti csoport, előfizetés vagy erőforráscsoport). A Log Analytics munkaterületet paraméterként veszi fel. |6 |1.0.1 |
