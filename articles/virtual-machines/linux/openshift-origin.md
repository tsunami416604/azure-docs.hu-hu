---
title: Az Azure-ban OKD üzembe helyezése |} A Microsoft Docs
description: Helyezze üzembe az Azure-ban OKD.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 0d3a9f05802bef7d6dfc99fcfae6668044f214c8
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190304"
---
# <a name="deploy-okd-in-azure"></a>Az Azure-ban OKD üzembe helyezése

Kétféle módon telepíthet az Azure-ban (korábbi nevén az OpenShift Origin) OKD egyikét használhatja:

- Manuálisan telepítheti az összes szükséges az Azure infrastruktúra-összetevőket, és hajtsa végre a OKD [dokumentáció](https://docs.okd.io/3.10/welcome/index.html).
- Használhatja egy meglévő [Resource Manager-sablon](https://github.com/Microsoft/openshift-origin) , amely leegyszerűsíti a OKD fürt központi telepítése.

## <a name="deploy-by-using-the-okd-template"></a>A OKD sablonnal üzembe helyezése

Használja a `appId` értékét a korábban létrehozott szolgáltatásnevet a `aadClientId` paraméter.

A következő példában létrehozunk egy nevű azuredeploy.parameters.json az összes szükséges bemeneti paramétereket tartalmazó fájlt.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

### <a name="deploy-by-using-azure-cli"></a>Azure CLI-vel üzembe helyezése


> [!NOTE] 
> A következő parancs használatához az Azure CLI-vel 2.0.8 vagy újabb. Az a parancssori felület verziójának ellenőrzéséhez az `az --version` parancsot. A parancssori felület verziójának frissítéséhez lásd [Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Az alábbi példa egy myResourceGroup nevű myOpenShiftCluster telepítési nevű erőforrás-csoportba helyez üzembe a OKD fürt és minden kapcsolódó erőforrás. A sablon azuredeploy.parameters.json nevű helyi paraméterek fájl használatával közvetlenül a GitHub-adattárat a hivatkozik.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Az üzembe helyezés befejezése után a teljes üzembe helyezett csomópontok számától függően legalább 25 percet vesz igénybe. Az OpenShift fő megrendelése a terminálon az üzembe helyezést, a DNS-nevét és a OKD konzol URL-címe.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-okd-cluster"></a>Csatlakozzon a OKD fürthöz

Az üzembe helyezést követően kapcsolódni a OKD konzolhoz a böngésző használatával a `OpenShift Console Uri`. Azt is megteheti csatlakoztathatja a OKD fő a következő paranccsal:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használja a [az csoport törlése](/cli/azure/group#az_group_delete) paranccsal eltávolítható az erőforráscsoport, az OpenShift fürt, és az összes kapcsolódó erőforrást, amikor azok már nincs szükség van.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

- [Üzembe helyezés utáni feladatok](./openshift-post-deployment.md)
- [OpenShift telepítés hibaelhárítása](./openshift-troubleshooting.md)
- [OKD – első lépések](https://docs.okd.io/latest/getting_started/index.html)
