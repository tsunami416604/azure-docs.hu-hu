---
title: "Az Azure-ban OpenShift tároló Platform telepítése |} Microsoft Docs"
description: "Az Azure-ban OpenShift tároló Platform telepítése."
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
ms.openlocfilehash: c91b7232b2f87e0b4b5e659126b96a6ef8b4202c
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Az Azure-ban OpenShift tároló Platform telepítése

Több módon is OpenShift tároló Platform az Azure-ban telepítheti. A szükséges Azure infrastruktúra-összetevőihez manuálisan telepítheti, és hajtsa végre a OpenShift tároló Platform [dokumentáció](https://docs.openshift.com/container-platform/3.6/welcome/index.html).
Egy meglévő Resource Manager-sablon, amely leegyszerűsíti a OpenShift tároló Platform fürt központi telepítése is használható. Amikor ilyen sablon átkerül [Itt](https://github.com/Microsoft/openshift-container-platform/).

Egy másik lehetőség az [Azure piactér ajánlat](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Mindkét lehetőség Red Hat előfizetés megadása kötelező. A telepítés során az RHEL példány a Red Hat előfizetés regisztrálva, a készlet Azonosítóját, amely tartalmazza a jogosultságok OpenShift tároló platform csatolva.
Ellenőrizze, hogy a egy érvényes Red Hat előfizetés Manager felhasználónév, jelszó és alkalmazáskészlet-azonosító (RHSM felhasználónév, jelszó RHSM és alkalmazáskészlet-azonosító). Jelentkezzen be https://access.redhat.com ellenőrizheti az adatokat.

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>A OpenShift tároló Platform Resource Manager sablonnal telepítése

A Resource Manager sablonnal telepítéséhez paraméterfájl segítségével adja meg a bemeneti paramétereket. Ha szeretné testre szabhatja a központi telepítési elemek, amelyek nem tartoznak bemeneti paraméterek, a github-tárház elágazás használatával, és módosítsa a megfelelő elemeket.

Néhány gyakori testreszabási lehetőségek (de nem kizárólagosan):

- Virtuális hálózat CIDR [azuredeploy.json változót]
- Megerősített Virtuálisgép-méretet [azuredeploy.json változót]
- Elnevezési konvenciók [azuredeploy.json változók]
- OpenShift fürt rögzítésen - módosító keresztül hosts fájl [deployOpenShift.sh]

### <a name="configure-parameters-file"></a>Paraméterek fájljának konfigurálása

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

Az elemek zárt megkeresése a lényeges adatot cseréje.

### <a name="deploy-using-azure-cli"></a>Telepítheti az Azure parancssori felület használatával

> [!NOTE] 
> A következő parancshoz szükséges Azure CLI 2.0.8 vagy újabb. Ellenőrizheti a CLI az verziójával a `az --version` parancsot. Frissítés a parancssori felület, lásd: [Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

A következő példa telepíti a OpenShift és minden kapcsolódó erőforrás egy contoso.com nevű myOpenShiftCluster telepítési nevű erőforrás csoporthoz. A sablon a github-tárház közvetlenül a hivatkozik, és egy helyi paraméterfájl nevű **azuredeploy.parameters.json** fájllal.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A központi telepítés legalább 30 percet is igénybe vehet attól függően, hogy a telepített csomópontok teljes száma. A OpenShift konzol és a DNS-név OpenShift főkiszolgálójának URL-CÍMÉT a Terminálszolgáltatások van nyomtatva, a telepítés befejezéséről.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-using-openshift-container-platform-marketplace-offer"></a>Szabályzatsablonokat OpenShift tároló Platform Piactéri ajánlat

A legegyszerűbben úgy OpenShift tároló Platform telepítése az Azure-hoz használni a [Azure piactér ajánlat](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Ezt a beállítást, a legegyszerűbb egy, de is korlátozott testreszabási képességeket. Az ajánlat három konfigurációs beállítások is:

- Kis: Egy nem telepít-magas rendelkezésre ÁLLÁSÚ fürt egyik fő csomóponton, egy infrastruktúra-csomópont, két alkalmazás csomópontot és több megerősített csomópont. Minden csomópont szabványos DS2v2 Virtuálisgép-méretek. A fürt 10 teljes mag szükséges, és kis léptékű tesztelési ideális.
- Közepes: Egy magas rendelkezésre ÁLLÁSÚ fürt három fő csomópontok, két infrastruktúra-kiszolgálókon, négy alkalmazás csomópont és egy megerősített csomópont telepíti. A megerősített kívül minden csomópont szabványos DS3v2 Virtuálisgép-méretek. A megerősített csomópont egy szabványos DS2v2. A fürt 38 magok igényel.
- Nagy: Telepít egy magas rendelkezésre ÁLLÁSÚ fürt három fő csomópontok, két infrastruktúra-kiszolgálókon, hat alkalmazás csomópontok és egy megerősített csomópont. A fő- és az infrastruktúra-kiszolgálók szabványos DS3v2 Virtuálisgép-méretek, az alkalmazás csomópontja a normál DS4v2 Virtuálisgép-méretek és a megerősített csomópont egy szabványos DS2v2. A fürt 70 magok igényel.

Azure Cloud szolgáltató konfigurációjában nem kötelező megadni a közepes és nagy fürt méretét. A kis foglalásiegység-méret nem nyújtja a beállítás a Azure Cloud szolgáltató konfigurálása.

## <a name="connect-to-the-openshift-cluster"></a>Csatlakozzon a OpenShift fürthöz

A telepítés befejezését követően a OpenShift konzol használatával, a böngésző használatával kapcsolódni a `OpenShift Console Uri`. Másik lehetőségként is elérheti a OpenShift master, a következő parancsot:

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége, használhatja a [az csoport törlése](/cli/azure/group#delete) távolítsa el az erőforráscsoportot, OpenShift fürt parancsot, és az összes kapcsolódó erőforrások.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

- [Utáni telepítési feladatok](./openshift-post-deployment.md)
- [OpenShift üzembe helyezés hibaelhárítása](./openshift-troubleshooting.md)
- [Ismerkedés a OpenShift tároló Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
