---
title: "A DevTest Labs szolgáltatásban az Azure parancssori felület a virtuális gépek létrehozására és kezelésére |} Microsoft Docs"
description: "Útmutató: Azure DevTest Labs segítségével az Azure CLI 2.0 virtuális gépek létrehozására és kezelésére"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: v-craic
ms.openlocfilehash: e73ddeba56c779d9fb1be77a50cbae5111de03c4
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Hozzon létre és virtuális gépek kezelése a DevTest Labs szolgáltatásban az Azure parancssori felület használatával
A gyors üzembe helyezési végigvezeti keresztül létrehozása, elindítása, csatlakozás, frissítése és törléséről a fejlesztési számítógépén a tesztkörnyezetben. 

Előzetes teendők

* Ha a labor nem lett létrehozva, található utasításokat [Itt](devtest-lab-create-lab.md).

* [CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli). Indításához futtassa az bejelentkezés az Azure VPN-kapcsolat létrehozásához. 

## <a name="create-and-verify-the-virtual-machine"></a>Hozzon létre, és ellenőrizze a virtuális gép 
Hozzon létre egy virtuális gép egy Piactéri rendszerképből az ssh hitelesítés.
```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```
> [!NOTE]
> Helyezze a **labor tartozó erőforráscsoport** paraméterben--erőforráscsoport név.
>

Ha szeretne létrehozni egy virtuális gép használatával egy képlet, használja a--képlet paraméter [az tesztlabor virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/lab/vm#az_lab_vm_create).


Győződjön meg arról, hogy rendelkezésre áll-e a virtuális Gépet.
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
Indítsa el a virtuális Gépet.
```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```
> [!NOTE]
> Helyezze a **labor tartozó erőforráscsoport** paraméterben--erőforráscsoport név.
>

Csatlakoztassa a virtuális Gépet: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) vagy [távoli asztal](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>A virtuális gép frissítése
A virtuális gépek összetevők vonatkozik.
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

Lista összetevők a tesztkörnyezet érhető el.
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
Állítsa le a virtuális Gépet.
```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

A virtuális gép törlése.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]