---
title: Hozzon létre és kezelhet virtuális gépeket az Azure CLI-vel a DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure DevTest Labs szolgáltatásban hozhat létre és kezelhet virtuális gépeket az Azure CLI 2.0 használatával
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 5e50bc3c6804a6f3d3dafd07b2918605c4cbc6ab
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434679"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Hozzon létre és kezelhet virtuális gépeket a DevTest Labs szolgáltatással az Azure CLI használatával
Ez a rövid útmutató végigvezeti létrehozása, indítása, csatlakozás, frissítése és a fejlesztői gépen, a laborban karbantartása. 

Előkészületek:

* Ha a labor nem lett létrehozva, útmutató [Itt](devtest-lab-create-lab.md).

* [Telepítse a CLI 2.0-s](https://docs.microsoft.com/cli/azure/install-azure-cli). Indításához futtassa az jelentkezzen be a kapcsolat létrehozása az Azure-ral. 

## <a name="create-and-verify-the-virtual-machine"></a>Hozzon létre, és ellenőrizze a virtuális gép 
Hozzon létre egy virtuális gép egy Piactéri rendszerképből ssh hitelesítés.
```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```
> [!NOTE]
> Helyezze a **tesztkörnyezet erőforráscsoport** a--resource-group-paraméter neve.
>

Ha szeretne egy virtuális gép létrehozása egy képletet, használja a--képlet paraméter használatával [az lab vm létrehozása](https://docs.microsoft.com/cli/azure/lab/vm#az-lab-vm-create).


Győződjön meg arról, hogy a virtuális gép érhető el.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Indítsa el, és csatlakozzon a virtuális géphez
Virtuális gép elindítása.
```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```
> [!NOTE]
> Helyezze a **tesztkörnyezet erőforráscsoport** a--resource-group-paraméter neve.
>

Kapcsolódás virtuális Géphez: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) vagy [a távoli asztal](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>A virtuális gép frissítése
Összetevők alkalmazása egy virtuális géphez.
```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

A lab-ben elérhető összetevők listázása.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>Állítsa le és törölje a virtuális gép    
Virtuális gép leállítása.
```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Virtuális gép törlése.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]