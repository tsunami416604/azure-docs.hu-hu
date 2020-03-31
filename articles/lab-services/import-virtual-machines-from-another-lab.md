---
title: Virtuális gépek importálása egy másik laborból az Azure DevTest Labs-ben
description: Ez a cikk bemutatja, hogyan importálhat virtuális gépeket egy másik laborból az Azure DevTest Labs aktuális laborjába.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 299d5c8758a13edded63b99abb2f12ddf9fa14be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759516"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Virtuális gépek importálása egy másik laborból az Azure DevTest Labs-ben
Ez a cikk arról nyújt tájékoztatást, hogyan importálhat virtuális gépeket egy másik laborból a laborba.

## <a name="scenarios"></a>Forgatókönyvek
Íme néhány forgatókönyv, ahol virtuális gépeket kell importálnia az egyik laborból egy másik laborba:

- A csapat egy tagja a vállalaton belül egy másik csoportba költözik, és a fejlesztői asztalt az új csapat DevTest Labs-éba szeretné vinni.
- A csoport elérte az [előfizetési szintű kvótát,](../azure-resource-manager/management/azure-subscription-service-limits.md) és fel szeretné osztani a csapatokat néhány előfizetésre
- A vállalat az Express Route (vagy más új hálózati topológia) felé halad, és a csapat át akarja helyezni a virtuális gépeket az új infrastruktúra használatához

## <a name="solution-and-constraints"></a>Megoldás és korlátozások
Ez a funkció lehetővé teszi, hogy virtuális gépeket importáljon egy tesztkörnyezetben (forrás) egy másik laborba (cél). A folyamat során megadhatja a célvirtuális gép új nevét. Az importálási folyamat tartalmazza az összes függőséget, például a lemezeket, az ütemezéseket, a hálózati beállításokat és így tovább.

A folyamat némi időt vesz igénybe, és a következő tényezők befolyásolják:

- A forrásgéphez csatlakoztatott lemezek száma/mérete (mivel ez másolási művelet, nem áthelyezési művelet)
- Távolság a céltól (például az USA keleti régiója És Délkelet-Ázsia között).

A folyamat befejezése után a forrás virtuális gép leáll, és az új fut a céllaborban.

Két fő megkötések kell figyelembe venni, amikor tervezi, hogy a virtuális gépek importálása az egyik laborból egy másik laborba:

- Virtuális gép importálása előfizetések és régiók között támogatottak, de az előfizetések kell társítható az azonos Azure Active Directory-bérlő.
- A virtuális gépek nem lehetnek követelhető állapotban a forráslaborban.
- Ön a virtuális gép tulajdonosa a forráslaborban és a labor tulajdonosa a céllaborban.
- Jelenleg ez a funkció csak a Powershell és a REST API-n keresztül támogatott.

## <a name="use-powershell"></a>A PowerShell használata
Az ImportVirtualMachines.ps1 fájl letöltése a [GitHubról](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). A parancsfájl segítségével egyetlen virtuális gépet vagy a forráslaborban lévő összes virtuális gépet importálhat a céllaborba.

### <a name="use-powershell-to-import-a-single-vm"></a>Egyetlen virtuális gép importálása a PowerShell használatával
A powershell-parancsfájl végrehajtásához azonosítani kell a virtuális gép forrásgépét és a céllabort, és szükség esetén új nevet kell megadni a célgéphez:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>A PowerShell használatával importálja az összes virtuális gépet a forráskörnyezetben
Ha a forrás virtuális gép nincs megadva, a parancsfájl automatikusan importálja az összes virtuális gépet a DevTest Labs.  Példa:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Virtuális gép importálása a HTTP REST segítségével
A REST-hívás egyszerű. Elegendő információt ad meg a forrás- és célerőforrások azonosításához. Ne feledje, hogy a művelet a céllabor erőforráson történik.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Szabályzatok beállítása egy tesztkörnyezethez](devtest-lab-get-started-with-lab-policies.md)
- [Gyakori kérdések](devtest-lab-faq.md)
