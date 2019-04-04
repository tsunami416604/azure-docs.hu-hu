---
title: Hozzon létre és kezelhet virtuális gépeket az Azure CLI-vel a DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure DevTest Labs szolgáltatásban hozhat létre és kezelhet virtuális gépeket az Azure CLI-vel
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
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 48a30ef86cdb10b540ffe1231294542ccff87255
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895630"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Hozzon létre és kezelhet virtuális gépeket a DevTest Labs szolgáltatással az Azure CLI használatával
Ez a rövid útmutató végigvezeti létrehozása, elindítása, csatlakozás, frissítése és karbantartása a fejlesztői gépen, a lab-ben. 

Előkészületek:

* Ha a labor nem lett létrehozva, útmutató [Itt](devtest-lab-create-lab.md).

* [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Indításához futtassa az jelentkezzen be a kapcsolat létrehozása az Azure-ral. 

## <a name="create-and-verify-the-virtual-machine"></a>Hozzon létre, és ellenőrizze a virtuális gép 
DevTest Labs kapcsolódó parancsokat, végrehajtása előtt állítsa be a megfelelő Azure-környezet használatával a `az account set` parancsot:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

A parancs egy virtuális gép létrehozása a következő: `az lab vm create`. A labor, labor nevét és virtuálisgép-nevet az erőforráscsoport összes szükségesek. A többi argumentummal módosítsa a virtuális gép típusától függően.

A következő parancs létrehoz egy Windows-alapú lemezkép az Azure Marketplace-beli. A lemezkép neve megegyezik, jelennének meg az Azure portal használatával a virtuális gép létrehozásakor. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3’ --admin-username 'AdminUser' --admin-password 'Password1!'
```

A következő parancs létrehoz egy rendelkezésre álló a laborban egyéni lemezképen alapuló virtuális gépet:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

A **-képtípust** argumentum megváltozott **katalógus** való **egyéni**. A lemezkép neve megegyezik, amit lát van, ha a virtuális gép létrehozása az Azure Portalon.

A következő parancs létrehoz egy virtuális gép egy Piactéri rendszerképből ssh hitelesítés:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Virtuális gépek beállításával képletek alapján is létrehozhat a **-képtípust** paramétert **képlet**. Ha a virtuális géphez megadott virtuális hálózat választása van szüksége, használja a **vnet-name** és **alhálózati** paramétereket. További információkért lásd: [az lab vm létrehozása](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Győződjön meg arról, hogy a virtuális gép érhető el.
Használja a `az lab vm show` paranccsal ellenőrizheti, hogy a virtuális gép elérhető legyen-e előtt indítsa el, és csatlakozhat hozzá. 

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
A következő példaparancs egy virtuális gép elindul:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Kapcsolódás virtuális Géphez: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) vagy [a távoli asztal](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>A virtuális gép frissítése
A következő mintaparancs összetevők virtuális Gépre vonatkozik:

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
A következő mintaparancs leállítja a virtuális gép.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Virtuális gép törlése.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>További lépések
Tekintse meg az alábbi tartalommal: [Azure DevTest Labs szolgáltatásban az Azure CLI dokumentációját](/cli/azure/lab?view=azure-cli-latest). 