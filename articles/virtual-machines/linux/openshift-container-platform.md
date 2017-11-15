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
ms.openlocfilehash: 159f30fc59a050b9a4ff983e8ac84e424104b484
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Az Azure-ban OpenShift tároló Platform telepítése

Az Azure-ban OpenShift tároló Platform telepítése többféle módszer használható:

- Manuálisan telepítheti a szükséges Azure infrastruktúra-összetevőihez, és hajtsa végre a OpenShift tároló Platform [dokumentáció](https://docs.openshift.com/container-platform/3.6/welcome/index.html).
- Használhat egy meglévő [Resource Manager-sablon](https://github.com/Microsoft/openshift-container-platform/) , amely egyszerűbbé teszi a OpenShift tároló Platform fürt központi telepítése.
- Egy másik lehetőség az [Azure piactér ajánlat](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Red Hat előfizetés összes beállítás megadása kötelező. A telepítés során a Red Hat Enterprise Linux-példány a Red Hat előfizetés regisztrálva, a készlet Azonosítóját, amely tartalmazza a jogosultságok OpenShift tároló platform csatolva.
Győződjön meg arról, hogy rendelkezik-e egy érvényes Red Hat előfizetés Manager (RHSM) felhasználónév, jelszó és a készlet azonosítóját. Ez az információ https://access.redhat.com bejelentkezés ellenőrizheti.

## <a name="deploy-by-using-the-openshift-container-platform-resource-manager-template"></a>Központi telepítése a OpenShift tároló Platform Resource Manager-sablon használatával

A Resource Manager-sablon használatával telepítéséhez segítségével egy paraméterfájl adja meg a bemeneti paramétereket. A bemeneti paraméterek használatával nem ismertetett telepítési elemek testreszabásához oszthatja ketté a GitHub-tárház, és módosítsa a megfelelő elemeket.

Néhány gyakori testreszabási lehetőségek közé tartozik, de nincsenek korlátozva:

- Virtuális hálózat CIDR (azuredeploy.json változó)
- Megerősített Virtuálisgép-méretet (azuredeploy.json változó)
- Elnevezési konvenciók (azuredeploy.json változók)
- Gazdafájl (deployOpenShift.sh) keresztül módosított OpenShift fürt rögzítésen,

### <a name="configure-the-parameters-file"></a>A paraméterfájl konfigurálása

Használja a `appId` értékét a korábban létrehozott egyszerű szolgáltatás a `aadClientId` paraméter. 

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

Cserélje le a megadott adatokkal zárójelek elemeket.

### <a name="deploy-by-using-azure-cli"></a>Központi telepítése az Azure parancssori felület használatával

> [!NOTE] 
> A következő parancshoz szükséges Azure CLI 2.0.8 vagy újabb. A parancssori felület verziójával ellenőrizheti a `az --version` parancsot. Frissítés a parancssori felület, lásd: [Azure CLI 2.0 telepítése](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latesti).

A következő példa telepíti a OpenShift és minden kapcsolódó erőforrás a contoso.com, nevű myOpenShiftCluster telepítési nevű erőforráscsoport. A sablon-ről a GitHub-tárház, és egy helyi paraméterek azuredeploy.parameters.json fájl nevű fájllal hivatkozik.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A központi telepítés legalább 30 percet is igénybe vehet, attól függően, hogy a telepített csomópontok teljes száma. Az URL-CÍMÉT a OpenShift konzol és a OpenShift fő megrendelése a terminál, a telepítés befejezése után a DNS-nevét.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-by-using-the-openshift-container-platform-azure-marketplace-offer"></a>Telepítését a OpenShift tároló Platform Azure piactér vonatkozó ajánlatot

A legegyszerűbben úgy OpenShift tároló Platform telepítése az Azure-hoz használni a [Azure piactér ajánlat](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Ez a legegyszerűbb lehetőség, de azt is korlátozott testreszabási képességeket. Az ajánlat három konfigurációs beállítások is:

- **Kis**: egy nem magas rendelkezésre ÁLLÁS fürt egyik fő csomóponton, egy infrastruktúra-csomópont, két alkalmazás csomópontot és egy megerősített csomópont telepíti. Minden csomópont szabványos DS2v2 Virtuálisgép-méretek. A fürt 10 teljes mag szükséges, és csak kevés számítógépet érintő tesztelési ideális.
- **Közepes**: egy magas rendelkezésre ÁLLÁSÚ fürt három fő csomópontok, két infrastruktúra csomópont, négy alkalmazás csomópontok és egy megerősített csomópont telepíti. A megerősített csomópont kívül minden csomópont szabványos DS3v2 Virtuálisgép-méretek. A megerősített csomópont egy szabványos DS2v2. A fürt 38 magok igényel.
- **Nagy**: egy magas rendelkezésre ÁLLÁSÚ fürt három fő csomópontok, két infrastruktúra-kiszolgálókon, hat alkalmazás csomópontok és egy megerősített csomópont telepíti. A fő- és infrastruktúra csomópontja szabványos DS3v2 Virtuálisgép-méretek. Az alkalmazás csomópontok szabványos DS4v2 Virtuálisgép-méretek, és a megerősített csomópont egy szabványos DS2v2. A fürt 70 magok igényel.

Azure Cloud Solution Provider konfigurálása nem kötelező megadni a fürt közepes és nagy méretű. A kis foglalásiegység-méret nem nyújtja a beállítás a Azure Cloud Solution Provider konfigurálása.

## <a name="connect-to-the-openshift-cluster"></a>Csatlakozzon a OpenShift fürthöz

A telepítés befejezése után a böngésző a OpenShift konzol használatával kapcsolódik a `OpenShift Console Uri`. Azt is megteheti csatlakozhat a OpenShift fő szerint a következő parancsot:

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használja a [az csoport törlése](/cli/azure/group#delete) távolítsa el az erőforráscsoportot, OpenShift fürt parancsot, és minden kapcsolódó erőforrásokat, ha azok már nincs szükség.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

- [Telepítés utáni feladatok](./openshift-post-deployment.md)
- [Az Azure-ban OpenShift telepítési hibáinak elhárítása](./openshift-troubleshooting.md)
- [Ismerkedés a OpenShift tároló Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
