---
title: Az OKD üzembe helyezése az Azure-ban
description: Telepítse az OKD-t az Azure-ban.
author: haroldwongms
manager: joraio
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: d7d251370aefdfadc0b77a67f6dad1be2dcb9e9a
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759443"
---
# <a name="deploy-okd-in-azure"></a>Az OKD üzembe helyezése az Azure-ban

Az OKD (korábban OpenShift Origin) azure-beli üzembe helyezésének két módja van:

- Manuálisan telepítheti az összes szükséges Azure-infrastruktúra-összetevőt, majd követheti az [OKD dokumentációját.](https://docs.okd.io)
- Egy meglévő [Erőforrás-kezelő sablont](https://github.com/Microsoft/openshift-origin) is használhat, amely leegyszerűsíti az OKD-fürt telepítését.

## <a name="deploy-using-the-okd-template"></a>Telepítés az OKD-sablon használatával

Az Erőforrás-kezelő sablonnal történő telepítéshez egy paraméterfájlt kell használnia a bemeneti paraméterek biztosításához. A központi telepítés további testreszabásához elágazás a GitHub-tármű, és módosítsa a megfelelő elemeket.

Néhány gyakori testreszabási lehetőség, de nem korlátozódik a következőkre:

- Bástya virtuális gép mérete (változó azuredeploy.json)
- Elnevezési konvenciók (változók az azuredeploy.jsonban)
- OpenShift fürtspecifikus, gazdagépfájlon keresztül módosítva (deployOpenShift.sh)

Az [OKD-sablon](https://github.com/Microsoft/openshift-origin) több ágat is elrendelhet az OKD különböző verzióihoz.  Az igényeinek megfelelően közvetlenül a tárcsóból telepítheti, vagy elágazhatja a tárta, és egyéni módosításokat hajthat végre a telepítés előtt.

Használja `appId` a `aadClientId` paraméterhez korábban létrehozott egyszerű szolgáltatás értékét.

Az alábbiakban egy azuredeploy.parameters.json nevű paraméterfájl látható az összes szükséges bemenettel.

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

A különböző kiadások különböző paraméterekkel rendelkezhetnek, ezért ellenőrizze a használt ág hoz szükséges paramétereket.

### <a name="deploy-using-azure-cli"></a>Üzembe helyezés az Azure CLI-vel


> [!NOTE] 
> A következő parancs hoz azure CLI 2.0.8 vagy újabb. A CLI-verziót a `az --version` paranccsal ellenőrizheti. A CLI-verzió frissítéséhez olvassa [el az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)című témakört.

A következő példa az OKD-fürtöt és az összes kapcsolódó erőforrást egy openshiftrg nevű erőforráscsoportba helyezi, a myOpenShiftCluster központi telepítési nevével. A sablon közvetlenül a GitHub-tárlaton keresztül hivatkozik, miközben egy azuredeploy.parameters.json nevű helyi paraméterfájlt használ.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A központi telepítés legalább 30 percet vesz igénybe, az üzembe helyezett csomópontok teljes száma alapján. Az OpenShift konzol URL-címe és az OpenShift főkiszolgáló DNS-neve a központi telepítés befejezésekor a terminálra nyomtatódik. Másik lehetőségként megtekintheti a központi telepítés kimeneti szakaszát az Azure Portalon.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Ha nem szeretné lekötni a parancssort, amíg a `--no-wait` központi telepítés befejeződik, adja hozzá a csoport központi telepítésének egyik beállítását. A központi telepítés kimenete lehívható az Azure Portalon az erőforráscsoport üzembe helyezési szakaszában.

## <a name="connect-to-the-okd-cluster"></a>Csatlakozás az OKD-fürthöz

Amikor a telepítés befejeződik, csatlakozzon az OpenShift `OpenShift Console Url`konzolhoz a böngészőjével a segítségével. Másik lehetőségként az SSH-t az OKD-főkiszolgálóhoz is használhatja. A következő példa a központi telepítés kimenetét használja:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az [az csoport törlése](/cli/azure/group) paranccsal távolítsa el az erőforráscsoportot, az OpenShift-fürtöt és az összes kapcsolódó erőforrást, ha már nincs rájuk szükség.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>További lépések

- [Telepítés utáni feladatok](./openshift-container-platform-3x-post-deployment.md)
- [OpenShift központi telepítésének – problémamegoldás](./openshift-container-platform-3x-troubleshooting.md)
- [Az OKD – első lépések](https://docs.okd.io)
