---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: 71bbcdc48357ba929db34a471f3ae6f37270b166
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80623999"
---
|Név |Leírás |Házirendek |Verzió |
|---|---|---|---|
|[Az Azure-figyelő engedélyezése virtuálisgép-méretezési készletekhez](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Engedélyezze az Azure Monitort a virtuálisgép-méretezési készletekhez a megadott hatókörben (felügyeleti csoport, előfizetés vagy erőforráscsoport). A Log Analytics munkaterületet paraméterként veszi fel. Megjegyzés: ha a méretezési készlet upgradePolicy beállítása Kézi, a bővítményt kell alkalmazni a bővítményt a készlet összes virtuális gépére frissítés hívásával rájuk. A CLI ez lenne az vmss update-példányok. |6 |1.0.1 |
|[Az Azure-figyelő engedélyezése virtuális gépekhez](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Engedélyezze az Azure Monitort a virtuális gépek (VM-ek) számára a megadott hatókörben (felügyeleti csoport, előfizetés vagy erőforráscsoport). A Log Analytics munkaterületet paraméterként veszi fel. |6 |1.0.1 |
