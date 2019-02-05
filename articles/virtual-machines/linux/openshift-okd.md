---
title: Az Azure-ban OKD üzembe helyezése |} A Microsoft Docs
description: Helyezze üzembe az Azure-ban OKD.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
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
ms.openlocfilehash: 571190324c5a0844624bd8a838cd103317fb53ca
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55729149"
---
# <a name="deploy-okd-in-azure"></a>Az Azure-ban OKD üzembe helyezése

Kétféle módon telepíthet az Azure-ban (korábbi nevén az OpenShift Origin) OKD egyikét használhatja:

- Manuálisan telepítheti az összes szükséges az Azure infrastruktúra-összetevőket, és kövesse a [OKD dokumentáció](https://docs.okd.io).
- Használhatja egy meglévő [Resource Manager-sablon](https://github.com/Microsoft/openshift-origin) , amely leegyszerűsíti a OKD fürt központi telepítése.

## <a name="deploy-using-the-okd-template"></a>Üzembe helyezés a OKD-sablonnal

Használatával a Resource Manager-sablon üzembe helyezéséhez használhatja egy paraméterfájl adja meg a bemeneti paraméterek. További testreszabását az üzembe helyezés, a GitHub-tárház elágaztatását, és módosítsa a megfelelő elemeket.

Néhány gyakori testreszabási lehetőségek közé tartozik, de nem korlátozódik:

- Megerősített Virtuálisgép-méret (az azuredeploy.json változó)
- Elnevezési konvenciók (az azuredeploy.json változók)
- OpenShift fürt tulajdonságairól, módosítani állomásleíró fájlhoz (deployOpenShift.sh)

A [OKD sablon](https://github.com/Microsoft/openshift-origin) OKD különböző verzióihoz elérhető ágak rendelkezik.  Igényei alapján telepítheti közvetlenül a tárházból vagy elágaztassa a példatárt és egyéni módosításokat üzembe helyezése előtt.

Használja a `appId` értékét a korábban létrehozott szolgáltatásnevet a `aadClientId` paraméter.

Az alábbiakban látható egy példa egy nevű azuredeploy.parameters.json az összes szükséges bemeneti paramétereket tartalmazó fájlt.

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

Cserélje le a paramétereket a jellemző információk.

Különböző kiadások úgy lehet különböző paraméterek ellenőrizze a szükséges paramétereket az ágat használja.

### <a name="deploy-using-azure-cli"></a>Üzembe helyezés az Azure CLI használatával


> [!NOTE] 
> A következő parancs használatához az Azure CLI-vel 2.0.8 vagy újabb. Az a parancssori felület verziójának ellenőrzéséhez az `az --version` parancsot. A parancssori felület verziójának frissítéséhez lásd [Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Az alábbi példa a OKD fürt és az összes kapcsolódó erőforrást helyez üzembe helyezzen egy erőforráscsoportban openshiftrg, nevű myOpenShiftCluster központi telepítés nevét. A sablon közvetlenül a GitHub-adattárat a hivatkozott azuredeploy.parameters.json nevű helyi paraméterfájl használata során.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Az üzembe helyezés legalább 30 percet vesz igénybe fejeződik be, az üzembe helyezett csomópontok száma alapján. Az OpenShift fő megrendelése a terminálon az üzembe helyezést, a DNS-nevét és a OpenShift-konzol URL-címe. Azt is megteheti a kimeneti szakasz az üzembe helyezés az Azure Portalon is megtekintheti.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Ha nem szeretné a parancssorból, Várakozás a telepítés befejeződik, adja hozzá a lefoglalhatnak `--no-wait` , a csoport központi telepítésének a lehetőségek közül. Az üzembe helyezés kimenete az erőforráscsoport az üzembe helyezés szakaszban az Azure Portalról kérhető.

## <a name="connect-to-the-okd-cluster"></a>Csatlakozzon a OKD fürthöz

Az üzembe helyezést követően az OpenShift konzolhoz a böngésző használatával csatlakozik a `OpenShift Console Url`. Lehetőségként SSH a OKD fő. Következő egy példa, amely használja az üzembe helyezés kimenete:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használja a [az csoport törlése](/cli/azure/group) paranccsal eltávolítható az erőforráscsoport, az OpenShift fürt, és az összes kapcsolódó erőforrást, amikor azok már nincs szükség van.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>További lépések

- [Üzembe helyezés utáni feladatok](./openshift-post-deployment.md)
- [OpenShift telepítés hibaelhárítása](./openshift-troubleshooting.md)
- [OKD – első lépések](https://docs.okd.io)
