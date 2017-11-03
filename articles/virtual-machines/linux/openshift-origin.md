---
title: "Központi telepítése az Azure-ban OpenShift származási |} Microsoft Docs"
description: "Központi telepítése az Azure-ban OpenShift forrása."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 1a40c4cc064b32aced7e976f40f6ed6a57e62204
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-openshift-origin-in-azure"></a>Az Azure-ban OpenShift származási telepítése

Több módon is telepítheti az Azure-ban OpenShift forrás. Manuálisan telepíti a szükséges Azure infrastruktúra-összetevőihez, és hajtsa végre a OpenShift forrás [dokumentáció](https://docs.openshift.org/3.6/welcome/index.html).
Egy meglévő Resource Manager-sablon, amely leegyszerűsíti a OpenShift forrás fürt központi telepítése is használható. Amikor ilyen sablon átkerül [Itt](https://github.com/Microsoft/openshift-origin).

## <a name="deploy-using-the-openshift-origin-template"></a>A OpenShift eredeti sablonnal telepítése

Használja a `appId` értékét a korábban létrehozott egyszerű szolgáltatás a `aadClientId` paraméter.

Az alábbi példa létrehoz egy nevű paraméterek fájlt **azuredeploy.parameters.json** az összes szükséges bemeneti adatok.

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

### <a name="deploy-using-azure-cli"></a>Telepítheti az Azure parancssori felület használatával


> [!NOTE] 
> A következő parancshoz szükséges Azure CLI 2.0.8 vagy újabb. Ellenőrizheti a CLI az verziójával a `az --version` parancsot. Frissítés a parancssori felület, lásd: [Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

A következő példa telepíti a OpenShift és minden kapcsolódó erőforrás egy contoso.com nevű myOpenShiftCluster telepítési nevű erőforrás csoporthoz. A sablon a github-tárház közvetlenül a hivatkozik, és egy helyi paraméterfájl nevű **azuredeploy.parameters.json** fájllal.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A központi telepítés legalább 25 percet is igénybe vehet attól függően, hogy a telepített csomópontok teljes száma. A OpenShift konzol és a DNS-név OpenShift főkiszolgálójának URL-CÍMÉT a Terminálszolgáltatások van nyomtatva, a telepítés befejezéséről.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-openshift-cluster"></a>Csatlakozzon a OpenShift fürthöz

A telepítés befejezését követően a OpenShift konzol használatával, a böngésző használatával kapcsolódni a `OpenShift Console Uri`. Másik lehetőségként is elérheti a OpenShift master, a következő parancsot:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége, használhatja a [az csoport törlése](/cli/azure/group#delete) távolítsa el az erőforráscsoportot, OpenShift fürt parancsot, és az összes kapcsolódó erőforrások.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

- [Utáni telepítési feladatok](./openshift-post-deployment.md)
- [OpenShift üzembe helyezés hibaelhárítása](./openshift-troubleshooting.md)
- [Ismerkedés a OpenShift forrása](https://docs.openshift.org/latest/getting_started/index.html)
