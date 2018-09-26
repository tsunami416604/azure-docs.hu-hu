---
title: Virtuális gépek importálhat egy másik tesztlabor az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg, hogyan importálja a virtuális gépeket egy másik labor a jelenlegi labor létrehozása az Azure DevTest Labs szolgáltatásban
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 4585d151e286917c67586a02539a10ade32bdd4c
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094529"
---
# <a name="import-vms-from-another-lab-in-azure-devtest-labs"></a>Virtuális gépek importálhat egy másik tesztlabor az Azure DevTest Labs szolgáltatásban
Az Azure DevTest Labs szolgáltatás jelentős mértékben javítja a virtuális gépek (VM) a fejlesztési és tesztelési tevékenységek felügyeletét. Lehetővé teszi, hogy a virtuális gép áthelyezése egy tesztkörnyezet között, a csapat vagy infrastruktúra-követelmények változnak. Az alábbiakban néhány gyakori forgatókönyvet, ahol előfordulhat, hogy ehhez szükség: 

- A csapat egy személy a vállalaton belül egy másik csoportba helyezi, és a fejlesztési virtuális gépeket az új csoport laborhoz szeretné.
- A csoport elérte az előfizetés-szintű kvótát, és szeretné felosztani a csoportok több előfizetést is be.
- A vállalat-csomagok áthelyezése az Express Route (vagy valamilyen egyéb új hálózati topológia), és a csapat azt akarja áthelyezi a virtuális gépeket az új infrastruktúra használatára.

## <a name="solution-and-constraints"></a>Megoldás és megkötések
Az Azure DevTest Labs lehetővé teszi, hogy a virtuális gépek cél labor forrás tesztkörnyezetben importálni egy tesztlabor tulajdonosa. A lab tulajdonosa a cél virtuális gép a folyamat új nevet is megadhat. Az importálási folyamat magában foglalja az összes függőségét, például a lemezek, a ütemezéseket, a hálózati beállítások, stb. A folyamat hosszabb időt is igénybe, és hatással van a forrásgép és a cél (például USA keleti régiójában, Délkelet-ázsiai régió) távolság csatlakoztatva a lemezek számát és méretét. Az importálási folyamat befejeződése után a a forrásoldali virtuális gép leállítása és az új egyik fut-e a cél a lab-ben marad.

Nincsenek két kulcsra vonatkozó megkötések, figyelembe importálása a virtuális gépeket egy másik labor tervezése során:

- Virtuális gépek importálását, előfizetések között, és keresztben régiókban érhető el, de az előfizetések ugyanahhoz az Azure Active Directory-bérlőhöz társított kell lennie.
- Virtuális gépek nem igényelhető a forrás tesztkörnyezetben állapotban kell lennie.

Ezenkívül, hogy a virtuális gép importálása egy tesztkörnyezet egy másikba, meg kell lennie a forrás labor és a cél-tesztkörnyezetben a labor tulajdonosa a virtuális gép tulajdonosa.

## <a name="steps-to-import-a-vm-from-another-lab"></a>Virtuális gép importálhat lab egy másik lépései
Jelenleg importálhatja egy virtuális Gépet egy tesztkörnyezet egy másik kizárólag az Azure PowerShell és REST API használatával.

### <a name="use-powershell"></a>A PowerShell használata
A PowerShell-parancsfájlt a ImportVirtualMachines.ps1 fájl letöltése [Azure DevTest Lab Git-tárház](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImportVirtualMachines) helyi meghajtójára. 

#### <a name="import-a-single-vm"></a>Egyetlen virtuális gép importálása
Futtassa a ImportVirtualMachines.ps1 cél labor importálja a forrás labor egyetlen virtuális Gépet. Megadhatja, hogy egy új nevet a virtuális gép, amely használatával a DestinationVirtualMachineName paramer másolásakor. 

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -SourceVirtualMachineName "<Name of the machine. Optional. If not specified, all VMs are copied>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VM is copied>" `
                            -DestinationVirtualMachineName "<New name for the VM. Optional>"
```


#### <a name="importing-all-vms"></a>Minden virtuális gép importálása
A ImportVirtualMachines.ps1 parancsfájl futtatásakor, ha nem adja meg a virtuális gép a forrás tesztkörnyezetben, a parancsfájl a forrás tesztlabor virtuális gépeinek importálja a cél tesztkörnyezetben. 

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VMs are copied>"
```

### <a name="use-rest-api"></a>A REST API használata
Indítja el a REST API-t a cél és a cél labor szemben, és adja át a forrás labor, az előfizetés és a virtuális gép adatai meg paraméterként, az alábbi példában látható módon: 

```json
POST https://management.azure.com/subscriptions/<ID of the target/destination subscription>/resourceGroups/<Name of the resource group that contains the destination lab>/providers/Microsoft.DevTestLab/labs/<Name of the lab to which the VMs are copied>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<ID of the subscription that contains the source VM>/resourcegroups/<Name of the resource group that contains the source lab>/providers/microsoft.devtestlab/labs/<Name of the lab that contains the source VM>/virtualmachines/MyVm",
   destinationVirtualMachineName: "MyVmNew"
}
```

## <a name="next-steps"></a>További lépések

- További információ a virtuális gép átméretezése: [virtuális gép átméretezése](devtest-lab-resize-vm.md).
- További információ a virtuális gép újbóli: [egy virtuális gép ismételt üzembe](devtest-lab-redeploy-vm.md).


