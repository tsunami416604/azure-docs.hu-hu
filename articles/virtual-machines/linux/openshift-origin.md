---
title: Központi telepítése az Azure-ban OpenShift származási |} Microsoft Docs
description: Központi telepítése az Azure-ban OpenShift forrása.
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
ms.openlocfilehash: f7a668f30d7acb1ea14fe9fd8921066d40a6669b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
ms.locfileid: "29123119"
---
# <a name="deploy-openshift-origin-in-azure"></a>Az Azure-ban OpenShift származási telepítése

Kétféle módon telepíthet az Azure-ban OpenShift származási egyikét használhatja:

- A szükséges Azure infrastruktúra-összetevőihez manuális telepítése, és kövesse a OpenShift eredeti [dokumentáció](https://docs.openshift.org/3.6/welcome/index.html).
- Használhat egy meglévő [Resource Manager-sablon](https://github.com/Microsoft/openshift-origin) , amely egyszerűbbé teszi a OpenShift forrás fürt központi telepítése.

## <a name="deploy-by-using-the-openshift-origin-template"></a>Központi telepítése a OpenShift eredeti sablon használatával

Használja a `appId` a szolgáltatás egyszerű, a korábban létrehozott értéket a `aadClientId` paraméter.

Az alábbi példa létrehoz egy, a szükséges bemeneti adatok azuredeploy.parameters.json nevű paraméterek fájlt.

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

### <a name="deploy-by-using-azure-cli"></a>Központi telepítése az Azure parancssori felület használatával


> [!NOTE] 
> A következő parancshoz szükséges Azure CLI 2.0.8 vagy újabb. A parancssori felület verziójával ellenőrizheti a `az --version` parancsot. Frissítés a parancssori felület, lásd: [Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

A következő példa telepíti a OpenShift és minden kapcsolódó erőforrás a contoso.com, nevű myOpenShiftCluster telepítési nevű erőforráscsoport. A sablon a GitHub-tárház közvetlenül a hivatkozott azuredeploy.parameters.json nevű helyi paraméterek fájl használatával.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A telepítés befejezéséhez, attól függően, hogy a telepített csomópontok száma legalább 25 percet vesz igénybe. Az URL-CÍMÉT a OpenShift konzol és a OpenShift fő megrendelése a terminál, a telepítés befejezése után a DNS-nevét.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-openshift-cluster"></a>Csatlakozzon a OpenShift fürthöz

A telepítés befejezése után a böngésző a OpenShift konzol használatával kapcsolódik a `OpenShift Console Uri`. Azt is megteheti csatlakozhat a OpenShift fő szerint a következő parancsot:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használja a [az csoport törlése](/cli/azure/group#az_group_delete) távolítsa el az erőforráscsoportot, OpenShift fürt parancsot, és minden kapcsolódó erőforrásokat, ha azok már nincs szükség.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

- [Telepítés utáni feladatok](./openshift-post-deployment.md)
- [OpenShift telepítési hibáinak elhárítása](./openshift-troubleshooting.md)
- [Bevezetés az OpenShift Origin használatába](https://docs.openshift.org/latest/getting_started/index.html)
