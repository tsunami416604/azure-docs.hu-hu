---
title: Virtuális gépek létrehozása és kezelése a DevTest Labs ben az Azure CLI-vel
description: Ismerje meg, hogyan hozhat létre és kezelhet virtuális gépeket az Azure CLI segítségével az Azure DevTest Labs segítségével
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: d3cd104e36cb407e9b1b833335869cac2c69d0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167050"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Virtuális gépek létrehozása és kezelése a DevTest Labs segítségével az Azure CLI használatával
Ez a rövid útmutató végigvezeti a fejlesztői gép létrehozásán, elindításán, csatlakoztatásán, frissítésén és tisztításán a laborban. 

Előkészületek:

* Ha nem hozott létre labort, az [utasítások itt](devtest-lab-create-lab.md)találhatók.

* [Telepítse az Azure CLI](/cli/azure/install-azure-cli). A kezdéshez futtassa az az bejelentkezési kapcsolatot az Azure-ral. 

## <a name="create-and-verify-the-virtual-machine"></a>A virtuális gép létrehozása és ellenőrzése 
A DevTest Labs alkalmazással kapcsolatos parancsok végrehajtása előtt `az account set` állítsa be a megfelelő Azure-környezetet a következő paranccsal:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

A virtuális gép létrehozásának `az lab vm create`parancsa: . A labor, a labor neve és a virtuális gép neve erőforráscsoport köteles. A többi argumentum a virtuális gép típusától függően változik.

A következő parancs létrehoz egy Windows-alapú lemezképet az Azure Market Place-ből. A rendszerkép neve megegyezik az Azure Portalon keresztül egy virtuális gép létrehozásakor. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

A következő parancs létrehoz egy virtuális gépet a laborban elérhető egyéni lemezkép alapján:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

A **képtípusú** argumentum **gyűjteményről** **egyénire**változott. A rendszerkép neve megegyezik azzal, amit lát, ha az Azure Portalon hozza létre a virtuális gépet.

A következő parancs létrehoz egy virtuális gép egy piactéri lemezkép ssh hitelesítéssel:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

A képletek alapján is létrehozhat virtuális gépeket, ha a **képtípus paramétert** **képletre**állítja. Ha egy adott virtuális hálózatot kell választania a virtuális géphez, használja a **virtuális hálózat nevét** és az **alhálózati** paramétereket. További információ: [az lab vm create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Ellenőrizze, hogy a virtuális gép elérhető-e.
A `az lab vm show` paranccsal ellenőrizheti, hogy a virtuális gép elérhető-e, mielőtt elkezdené, és csatlakozna hozzá. 

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

## <a name="start-and-connect-to-the-virtual-machine"></a>A virtuális gép indítása és csatlakoztatása
A következő példaparancs elindítja a virtuális gép:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Csatlakozás virtuális géphez: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) vagy [Távoli asztal.](../virtual-machines/windows/connect-logon.md)
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>A virtuális gép frissítése
A következő mintaparancs összetevőket alkalmaz egy virtuális gépre:

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

### <a name="list-artifacts-available-in-the-lab"></a>A laborban elérhető összetevők listázása

A tesztkörnyezetben a virtuális gépeken elérhető összetevők listázásához futtassa a következő parancsokat.

**Cloud Shell - PowerShell**: figyelje\`meg a backtick ( ) használatát a $ $expand előtt (azaz "$expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell - Bash**: figyelje\\meg a perjel ( ) karakter használatát a $ előtt a parancsban. 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

Példa a kimenetre: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>A virtuális gép leállítása és törlése    
A következő mintaparancs leállít egy virtuális gép.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Virtuális gép törlése.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>További lépések
Tekintse meg a következő tartalmat: [Azure CLI dokumentáció az Azure DevTest Labs.](/cli/azure/lab?view=azure-cli-latest) 
