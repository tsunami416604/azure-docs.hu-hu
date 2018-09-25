---
title: Az OpenShift Container Platform az Azure-beli üzembe helyezése |} A Microsoft Docs
description: Telepítse az OpenShift Container Platform az Azure-ban.
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
ms.openlocfilehash: 48b6287fef673c5f335531b6f230993969fc9e1c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996332"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Az OpenShift Container Platform az Azure-beli üzembe helyezése

Többféle módszer egyikét használhatja az OpenShift Container Platform az Azure-beli üzembe helyezéséhez:

- Manuálisan telepítheti a szükséges Azure-infrastruktúra-összetevőket, és hajtsa végre az OpenShift Tárolóplatform [dokumentáció](https://docs.openshift.com/container-platform/3.10/welcome/index.html).
- Használhatja egy meglévő [Resource Manager-sablon](https://github.com/Microsoft/openshift-container-platform/) , amely leegyszerűsíti az OpenShift Tárolóplatform fürt központi telepítése.
- Egy másik lehetőség a [Azure Marketplace-ajánlat](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Minden beállítás a Red Hat-előfizetésüket szükség. Az üzembe helyezés során a Red Hat Enterprise Linux-példány van regisztrálva a Red Hat-előfizetésüket, a készlet azonosítója, amely tartalmazza a jogosultságokat az OpenShift Tárolóplatform kapcsolódik.
Gondoskodjon arról, hogy egy Red Hat előfizetés Manager (RHSM) érvényes felhasználónév, jelszó és a készlet azonosítóját. Bejelentkezés a ellenőrizheti ezt az információt https://access.redhat.com.

## <a name="deploy-by-using-the-openshift-container-platform-resource-manager-template"></a>Az OpenShift Container Platform Resource Manager-sablon segítségével üzembe helyezése

Használatával a Resource Manager-sablon üzembe helyezéséhez használhatja egy paraméterfájl adja meg a bemeneti paraméterek. Testre szabhatja az üzembe helyezés elemek, amelyek nem tartoznak a bemeneti paraméterek, a GitHub-tárház elágaztatását, és módosítsa a megfelelő elemeket.

Néhány gyakori testreszabási lehetőségek közé tartozik, de nem korlátozódik:

- Virtuális hálózat CIDR (az azuredeploy.json változó)
- Megerősített Virtuálisgép-méret (az azuredeploy.json változó)
- Elnevezési konvenciók (az azuredeploy.json változók)
- OpenShift fürt tulajdonságairól, módosítani állomásleíró fájlhoz (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>A paraméterfájl konfigurálása

Használja a `appId` értékét a korábban létrehozott egyszerű szolgáltatás a `aadClientId` paraméter. 

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
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "true"
        },
        "enableCockpit": {
            "value": "false"
        },
        "rhsmUsernamePasswordOrActivationKey": {
            "value": "usernamepassword"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
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

Cserélje le a szögletes zárójelben információkat az elemeket.

### <a name="deploy-by-using-azure-cli"></a>Azure CLI-vel üzembe helyezése

> [!NOTE] 
> A következő parancs használatához Azure CLI.8 vagy újabb. Az a parancssori felület verziójának ellenőrzéséhez az `az --version` parancsot. A parancssori felület verziójának frissítéséhez lásd [Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

Az alábbi példa egy myResourceGroup nevű myOpenShiftCluster telepítési nevű erőforrás-csoportba helyez üzembe az OpenShift fürt és minden kapcsolódó erőforrás. A sablon közvetlenül a GitHub-adattárat, és a egy helyi paraméterek azuredeploy.parameters.json fájlhoz nevű fájllal a hivatkozik.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Az üzembe helyezés befejeződik az üzembe helyezett csomópontok száma legalább 30 percet vesz igénybe. Az OpenShift fő megrendelése a terminálon az üzembe helyezést, a DNS-nevét és a OpenShift-konzol URL-címe.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-by-using-the-openshift-container-platform-azure-marketplace-offer"></a>Az OpenShift Container Platform Azure Marketplace-ajánlat használatával üzembe helyezése

Üzembe helyezésének OpenShift Container Platform az Azure-bA a legegyszerűbb módja az, hogy használja a [Azure Marketplace-ajánlat](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Ez a legegyszerűbb lehetőség, de azt is korlátozott testreszabási lehetőségeket. Az ajánlat három konfigurációs beállításokat tartalmazza:

- **Kis**: egy nem magas rendelkezésre ÁLLÁS fürtöt egy fő csomóponttal, egyetlen infrastruktúra csomópont, két alkalmazás csomópont és a egy megerősített csomópont helyez üzembe. Minden csomópont olyan szabványos DS2v2 Virtuálisgép-méretek. Ez a fürt 10 összes mag szükséges, és ideális megoldás kis-teszteléséhez.
- **Közepes**: három fő csomóponttal, a két infrastruktúra csomópont, a alkalmazás négy csomópont és a egy megerősített csomópont egy magas rendelkezésre ÁLLÁSÚ fürtöt helyez üzembe. A megerősített csomópont kívül minden csomópont olyan szabványos DS3v2 Virtuálisgép-méretek. A megerősített csomópontnak számít egy standard DS2v2. Ez a fürt 38 magra van szükség.
- **Nagy**: egy három fő csomóponttal, a két infrastruktúra csomópont, a hat alkalmazás csomópontok és a egy megerősített csomópont a magas rendelkezésre ÁLLÁSÚ fürtöt helyez üzembe. A fő- és infrastruktúra-csomópontok használata standard DS3v2 Virtuálisgép-méretek. Az alkalmazás csomópontok standard DS4v2 Virtuálisgép-méretek, és a megerősített csomópont egy standard DS2v2. Ez a fürt 70 mag szükséges.

Az Azure Cloud Solution Provider konfigurálása nem kötelező, a közepes és nagy méretű fürt méretét. A kis méretű fürt mérete nem biztosít az Azure Cloud Solution Provider konfigurálásához.

## <a name="connect-to-the-openshift-cluster"></a>Csatlakozzon az OpenShift-fürthöz

Az üzembe helyezést követően az OpenShift konzolhoz csatlakozik a böngésző használatával a `OpenShift Console Uri`. Azt is megteheti csatlakoztathatja az OpenShift fő a következő paranccsal:

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használja a [az csoport törlése](/cli/azure/group#az_group_delete) paranccsal eltávolítható az erőforráscsoport, az OpenShift fürt, és az összes kapcsolódó erőforrást, amikor azok már nincs szükség van.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

- [Üzembe helyezés utáni feladatok](./openshift-post-deployment.md)
- [Az Azure-ban az OpenShift telepítés hibaelhárítása](./openshift-troubleshooting.md)
- [Ismerkedés az OpenShift Tárolóplatform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
