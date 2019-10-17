---
title: OKD üzembe helyezése az Azure-ban | Microsoft Docs
description: OKD üzembe helyezése az Azure-ban.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: 66d17fb2a96bbb5b1dcb51151242f014b4116b86
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390530"
---
# <a name="deploy-okd-in-azure"></a>OKD üzembe helyezése az Azure-ban

Az Azure-ban kétféleképpen helyezhet üzembe OKD (korábban OpenShift Origin):

- Manuálisan telepítheti az összes szükséges Azure-infrastruktúra-összetevőt, majd követheti a [OKD dokumentációját](https://docs.okd.io).
- Használhat egy meglévő [Resource Manager-sablont](https://github.com/Microsoft/openshift-origin) is, amely leegyszerűsíti a OKD-fürt üzembe helyezését.

## <a name="deploy-using-the-okd-template"></a>Üzembe helyezés a OKD sablon használatával

A Resource Manager-sablonnal történő üzembe helyezéshez egy Parameters-fájl segítségével adja meg a bemeneti paramétereket. Az üzembe helyezés további testreszabásához a GitHub-tárházat kell megváltoztatnia, és módosítani a megfelelő elemeket.

Néhány gyakori testreszabási lehetőség, de nem korlátozódik a következőkre:

- Megerősített VM-méret (változó a azuredeploy. JSON fájlban)
- Elnevezési konvenciók (változók a azuredeploy. JSON fájlban)
- OpenShift-fürtök, a Hosts fájlon keresztül módosítva (deployOpenShift.sh)

A [OKD sablonban](https://github.com/Microsoft/openshift-origin) több ág is elérhető a OKD különböző verzióihoz.  Igény szerint közvetlenül a tárházból is üzembe helyezhető, vagy a tárházat leválaszthatja, és egyéni módosításokat hajthat végre az üzembe helyezés előtt.

Használja a `appId` értéket az `aadClientId` paraméterhez korábban létrehozott egyszerű szolgáltatásnév alapján.

A következő példa egy azuredeploy. Parameters. JSON nevű paramétereket tartalmazó fájlt mutat be az összes szükséges bemenettel.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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
        "storageKind": {
            "value": "managed"
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
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
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
        "enableAzure": {
            "value": "true"
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

Cserélje le a paramétereket a megadott adatokra.

A különböző kiadások különböző paraméterekkel rendelkezhetnek, ezért kérjük, ellenőrizze a használt ág szükséges paramétereit.

### <a name="deploy-using-azure-cli"></a>Üzembe helyezés az Azure CLI-vel


> [!NOTE] 
> A következő parancshoz Azure CLI-2.0.8 vagy újabb verzió szükséges. A CLI-verziót a `az --version` paranccsal ellenőrizheti. A CLI verziójának frissítéséhez lásd: az [Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

A következő példa a OKD-fürtöt és az összes kapcsolódó erőforrást egy openshiftrg nevű erőforráscsoporthoz helyezi üzembe a myOpenShiftCluster központi telepítési nevével. A sablon közvetlenül a GitHub-tárházból van hivatkozva a azuredeploy. Parameters. JSON nevű helyi paraméterek fájljának használatakor.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Az üzembe helyezés legalább 30 percet vesz igénybe, a telepített csomópontok teljes száma alapján. A OpenShift-konzol URL-címe és a OpenShift-főkiszolgáló DNS-neve a terminálra nyomtatódik a telepítés befejeződése után. Másik lehetőségként megtekintheti a központi telepítés kimenetek szakaszát is a Azure Portal.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Ha nem szeretné összekapcsolni a parancssort, amíg a telepítés befejeződik, adja hozzá a `--no-wait` értéket a csoport központi telepítésének egyik beállításaként. A központi telepítés kimenete az erőforráscsoport telepítési szakaszában található Azure Portalból kérhető le.

## <a name="connect-to-the-okd-cluster"></a>Kapcsolódás a OKD-fürthöz

Az üzembe helyezés befejezésekor a `OpenShift Console Url` használatával kapcsolódjon a OpenShift-konzolhoz a böngészőben. Azt is megteheti, hogy SSH-t használ a OKD-főkiszolgálóval. Az alábbi példa az üzemelő példány kimenetét használja:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az az [Group delete](/cli/azure/group) paranccsal távolítsa el az erőforráscsoportot, a OpenShift-fürtöt és az összes kapcsolódó erőforrást, ha már nincs rájuk szükség.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Következő lépések

- [Üzembe helyezés utáni feladatok](./openshift-container-platform-3x-post-deployment.md)
- [OpenShift-telepítés hibáinak megoldása](./openshift-container-platform-3x-troubleshooting.md)
- [A OKD első lépései](https://docs.okd.io)
