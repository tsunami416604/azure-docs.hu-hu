---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 8fe3dca69974f1df09ffb9ca4e1ece5a614f61f5
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172967"
---
|Name (Név) |Leírás |Szabályzatok |Verzió |
|---|---|---|---|
|[VM Scale Sets (VMSS) Azure Monitor engedélyezése](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |A megadott hatókörben lévő VM Scale Sets Azure Monitor engedélyezése (felügyeleti csoport, előfizetés vagy erőforráscsoport). Log Analytics munkaterületet paraméterként veszi fel. Megjegyzés: Ha a méretezési csoport upgradePolicy manuális értékre van állítva, a bővítményt a készletben lévő összes virtuális gépre alkalmaznia kell a frissítés meghívásával. A CLI-ben ez az az vmss Update-instances lenne. |6 |1.0.0 – előzetes verzió |
|[Azure Monitor for VMs engedélyezése](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Azure Monitor engedélyezése a Virtual Machines (VM) számára a megadott hatókörben (felügyeleti csoport, előfizetés vagy erőforráscsoport). Log Analytics munkaterületet paraméterként veszi fel. |6 |1.0.0 – előzetes verzió |
