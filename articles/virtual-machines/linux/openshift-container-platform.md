---
title: Az OpenShift Container Platform az Azure-beli üzembe helyezése |} A Microsoft Docs
description: Telepítse az OpenShift Container Platform az Azure-ban.
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
ms.date: 02/04/2018
ms.author: haroldw
ms.openlocfilehash: 1d869d822cdeb0051836a5fc5f01eb69c523f9e3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995550"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Az OpenShift Container Platform az Azure-beli üzembe helyezése

Többféle módszer egyikét használhatja az OpenShift Container Platform az Azure-beli üzembe helyezéséhez:

- Manuálisan telepítheti a szükséges Azure-infrastruktúra-összetevőket, és kövesse a [OpenShift Tárolóplatform dokumentáció](https://docs.openshift.com/container-platform).
- Használhatja egy meglévő [Resource Manager-sablon](https://github.com/Microsoft/openshift-container-platform/) , amely leegyszerűsíti az OpenShift Tárolóplatform fürt központi telepítése.
- Egy másik lehetőség a [Azure Marketplace-ajánlat](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Minden beállítás a Red Hat-előfizetésüket szükség. Az üzembe helyezés során a Red Hat Enterprise Linux-példány van regisztrálva a Red Hat-előfizetésüket, a készlet azonosítója, amely tartalmazza a jogosultságokat az OpenShift Tárolóplatform kapcsolódik.
Ellenőrizze, hogy egy érvényes Red Hat előfizetés Manager (RHSM) felhasználónév, jelszó és készlet azonosítója. Használhat egy aktiválási kulcsot, a szervezeti azonosító és a készlet azonosítója. Bejelentkezés a ellenőrizheti ezt az információt https://access.redhat.com.

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Üzembe helyezés az OpenShift Container Platform Resource Manager-sablon használatával

Használatával a Resource Manager-sablon üzembe helyezéséhez használhatja egy paraméterfájl adja meg a bemeneti paraméterek. További testreszabását az üzembe helyezés, a GitHub-tárház elágaztatását, és módosítsa a megfelelő elemeket.

Néhány gyakori testreszabási lehetőségek közé tartozik, de nem korlátozódik:

- Megerősített Virtuálisgép-méret (az azuredeploy.json változó)
- Elnevezési konvenciók (az azuredeploy.json változók)
- OpenShift fürt tulajdonságairól, módosítani állomásleíró fájlhoz (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>A paraméterfájl konfigurálása

A [OpenShift Tárolóplatform sablon](https://github.com/Microsoft/openshift-container-platform) az OpenShift Tárolóplatform különböző verzióihoz elérhető ágak rendelkezik.  Igényei alapján telepítheti közvetlenül a tárházból vagy elágaztassa a példatárt és módosításokat egyéni parancsfájlok vagy sablonok üzembe helyezése előtt.

Használja a `appId` értékét a korábban létrehozott egyszerű szolgáltatás a `aadClientId` paraméter.

Az alábbi példa bemutatja egy nevű azuredeploy.parameters.json az összes szükséges bemeneti paramétereket tartalmazó fájlt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
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
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
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
        "rhsmBrokerPoolId": {
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
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "routing.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "openshiftvnet"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "mastersubnet"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "infrasubnet"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "nodesubnet"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/masterinfrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.201"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        },
        "proxySettings": {
            "value": "none"
        },
        "httpProxyEntry": {
            "value": "none"
        },
        "httpsProxyEntry": {
            "value": "none"
        },
        "noProxyEntry": {
            "value": "none"
        }
    }
}
```

Cserélje le a paramétereket a jellemző információk.

Előfordulhat, hogy a különböző kiadások eltérő paraméterekkel, ezért ellenőrizze a szükséges paramétereket az ágat használja.

### <a name="deploy-using-azure-cli"></a>Üzembe helyezés az Azure CLI használatával

> [!NOTE] 
> A következő parancs használatához az Azure CLI-vel 2.0.8 vagy újabb. Az a parancssori felület verziójának ellenőrzéséhez az `az --version` parancsot. A parancssori felület verziójának frissítéséhez lásd [Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

Az alábbi példa az OpenShift fürt és minden kapcsolódó erőforrás helyez üzembe helyezzen egy erőforráscsoportban openshiftrg, nevű myOpenShiftCluster központi telepítés nevét. A sablon közvetlenül a GitHub-adattárat, és a egy helyi paraméterek azuredeploy.parameters.json fájlhoz nevű fájllal a hivatkozik.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Az üzembe helyezés legalább 30 percig tart, üzembe helyezett csomópontok és a konfigurált beállítások teljes száma alapján. A megerősített DNS teljes tartománynév és az OpenShift-konzol URL-cím nyomtat a terminálon az üzembe helyezést.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Ha nem szeretné a parancssorból, Várakozás a telepítés befejeződik, adja hozzá a lefoglalhatnak `--no-wait` , a csoport központi telepítésének a lehetőségek közül. Az üzembe helyezés kimenete az erőforráscsoport az üzembe helyezés szakaszban az Azure Portalról kérhető.
 
## <a name="deploy-using-the-openshift-container-platform-azure-marketplace-offer"></a>Üzembe helyezés az OpenShift Container Platform Azure Marketplace-ajánlat

Üzembe helyezésének OpenShift Container Platform az Azure-bA a legegyszerűbb módja az, hogy használja a [Azure Marketplace-ajánlat](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Ez a legegyszerűbb lehetőség, de azt is korlátozott testreszabási lehetőségeket. A Piactéri ajánlat az alábbi konfigurációs lehetőségeket tartalmazza:

- **Fő csomópontok**: Írja be a három (3) fő csomópontok konfigurálható példánnyal.
- **Infrastruktúra csomópontok**: Írja be a három (3) infrastruktúra csomópontok konfigurálható példánnyal.
- **Csomópontok**: Csomópontok száma nem konfigurálható (között a 2. és 9) és a példánytípust.
- **Lemez típusa**: Felügyelt lemezeket használnak.
- **Hálózatkezelés**: Új vagy meglévő hálózati, valamint az egyéni CIDR-tartomány támogatása.
- **CNS**: CNS engedélyezhető.
- **Metrikák**: Metrikák engedélyezhető.
- **Naplózás**: Naplózás is engedélyezhető.
- **Az Azure Felhőszolgáltató**: Engedélyezhető.

## <a name="connect-to-the-openshift-cluster"></a>Csatlakozzon az OpenShift-fürthöz

Az üzembe helyezést követően a kimeneti szakaszban az üzemelő példány lekérdezni a kapcsolatot. Csatlakozás a böngészőjében az OpenShift konzol használatával a `OpenShift Console URL`. Azt is megteheti akkor az SSH, a bástyagazdagép. Következő egy példa, ahol a rendszergazda felhasználóneve clusteradmin, a megerősített nyilvános IP-cím DNS teljes Tartományneve pedig bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használja a [az csoport törlése](/cli/azure/group) paranccsal eltávolítható az erőforráscsoport, az OpenShift fürt, és az összes kapcsolódó erőforrást, amikor azok már nincs szükség van.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>További lépések

- [Üzembe helyezés utáni feladatok](./openshift-post-deployment.md)
- [Az Azure-ban az OpenShift telepítés hibaelhárítása](./openshift-troubleshooting.md)
- [Ismerkedés az OpenShift Tárolóplatform](https://docs.openshift.com/container-platform)

### <a name="documentation-contributors"></a>Dokumentáció a közreműködők

Köszönjük, hogy Vincent Power (vincepower) és Alfred Sin (asinn826) gondoskodik a naprakész ebben a dokumentációban való hozzájárulásaikat!
