---
title: Virtuális gépek importálhat egy másik tesztlabor az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Importálja a virtuális gépek egy másik tesztlabor az aktuális tesztkörnyezeti útmutató.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: 9cd2e5e211fcda7c59469d3b09e9c9e5bdefdbd6
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546585"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Virtuális gépek importálhat egy másik tesztlabor az Azure DevTest Labs szolgáltatásban
Ez a cikk ismerteti, importálja a virtuális gépeket egy másik labor a labor. 

## <a name="scenarios"></a>Forgatókönyvek
Az alábbiakban néhány esetben, amikor importálja a virtuális gépek egy tesztkörnyezet egy másik labor kell: 

- A csapat egyéni kerül át a vállalaton belül egy másik csoportot, és szeretné a fejlesztői asztali eltarthat, hogy az új csoport DevTest Labs szolgáltatásban.
- A csoport eléri egy [előfizetés-szintű kvóta](../azure-subscription-service-limits.md) , és szeretné felosztani a máshogy néhány-előfizetéssel, a csapatok
- A vállalat az Express Route (vagy valamilyen egyéb új hálózati topológia) kerül át, és a csapat szeretne a virtuális gépeket az új infrastruktúra használata

## <a name="solution-and-constraints"></a>Megoldás és megkötések
Ez a funkció lehetővé teszi, hogy a virtuális gépek egy tesztkörnyezetben (forrás) importálása másik lab (cél). Igény szerint adhat új nevet a cél virtuális gép a folyamat során. Az importálási folyamat magában foglalja az összes függőségét, például a lemezek, ütemezéseket, hálózati beállításokat és így tovább.

A folyamat hosszabb időt is igénybe, és hatással van a következő tényezőket:

- (Mivel ez a másolási műveletek és a egy move művelet nem) a forrásgép csatolt a lemezek számát és méretét 
- Távolság a célhelyre (például USA keleti régiójában, Délkelet-Ázsia).  

A folyamat befejeződése után a a forrás virtuális gép leállítása és az új egyik fut-e a cél a lab-ben marad.

Nincsenek tisztában lenni egy tesztlabor a virtuális gépek importálhatja egy másik labor tervezése során két kulcsokra vonatkozó korlátozások:

- Virtuális gép import előfizetések és -régiók között támogatottak, de az előfizetések ugyanahhoz az Azure Active Directory-bérlőhöz társított kell lennie.
- Virtuális gépek nem igényelhető a forrás tesztkörnyezetben állapotban kell lennie.
- Ön a tulajdonosa, a virtuális gép a forrás labor és a cél-tesztkörnyezetben a labor tulajdonosa.
- Ez a funkció jelenleg csak a Powershell és a REST API használatával támogatott.

## <a name="use-powershell"></a>A PowerShell használata
Töltse le a ImportVirtualMachines.ps1 fájlt a [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). A parancsfájl segítségével egyetlen virtuális gép vagy a forrás laborkörnyezetben található összes virtuális gép importálása a cél laborkörnyezetbe. 

### <a name="use-powershell-to-import-a-single-vm"></a>Egyetlen virtuális gép importálása a PowerShell használatával
A powershell-parancsprogram futtatása a forrásoldali virtuális gép és a cél labor azonosító, és opcionálisan biztosítja egy új nevet a cél gép igényel:

```powershell 
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Importálja az összes virtuális gép a forrás lab-ben a PowerShell használatával
Ha a forrás virtuális gép nincs megadva, a parancsfájl automatikusan importálja a DevTest Labs szolgáltatásban létrehozott összes virtuális gépet.  Példa:
 
```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Virtuális gép importálása HTTP REST használatával
A REST-hívást használata egyszerű. Engedélyezi a elegendő információt a forrás- és erőforrások azonosítására. Ne feledje, hogy a művelet a rendeltetési labor erőforráson történik.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket: 

- [Tesztkörnyezet szabályzatainak beállítása](devtest-lab-get-started-with-lab-policies.md)
- [Gyakori kérdések](devtest-lab-faq.md)
