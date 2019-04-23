---
title: Az OpenShift Container Platform az Azure-beli üzembe helyezése |} A Microsoft Docs
description: Telepítse az OpenShift Container Platform az Azure-ban.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2019
ms.author: haroldw
ms.openlocfilehash: 39eea84cc9301263381533e03e8f783e0a73ea19
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "59999960"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Az OpenShift Container Platform az Azure-beli üzembe helyezése

Többféle módszer egyikét használhatja az OpenShift Container Platform az Azure-beli üzembe helyezéséhez:

- Manuálisan telepítheti a szükséges Azure-infrastruktúra-összetevőket, és kövesse a [OpenShift Tárolóplatform dokumentáció](https://docs.openshift.com/container-platform).
- Használhatja egy meglévő [Resource Manager-sablon](https://github.com/Microsoft/openshift-container-platform/) , amely leegyszerűsíti az OpenShift Tárolóplatform fürt központi telepítése.
- Egy másik lehetőség a [Azure Marketplace-ajánlat](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Minden beállítás a Red Hat-előfizetésüket szükség. Az üzembe helyezés során a Red Hat Enterprise Linux-példány van regisztrálva a Red Hat-előfizetésüket, a készlet azonosítója, amely tartalmazza a jogosultságokat az OpenShift Tárolóplatform kapcsolódik.
Ellenőrizze, hogy egy érvényes Red Hat előfizetés Manager (RHSM) felhasználónév, jelszó és készlet azonosítója. Használhat egy aktiválási kulcsot, a szervezeti azonosító és a készlet azonosítója. Bejelentkezés a ellenőrizheti ezt az információt https://access.redhat.com.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Üzembe helyezés az OpenShift Container Platform Resource Manager-sablon használatával

### <a name="private-clusters"></a>Privát fürtök

Privát OpenShift-fürtök üzembe helyezése szükség van arra, több mint csak nem kapcsolódik a fő terheléselosztóhoz (webkonzol) vagy a nyilvános IP-cím az infrastruktúra az terheléselosztó (router).  A privát fürt általában használ egy egyéni DNS-kiszolgálót (nem az alapértelmezett Azure DNS-ben), egy egyéni tartománynevet (például contoso.com) és az előre meghatározott virtuális hálózat.  A privát fürtök esetén a virtuális hálózat konfigurálása a megfelelő alhálózatokat és a DNS-kiszolgáló beállításainak előre kell.  Ezután **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference**, és  **existingNodeSubnetReference** a fürt által használható a meglévő alhálózat megadását.

Ha privát fő van kijelölve (**masterClusterType**= a titkos), egy statikus magánhálózati IP-címet meg kell határozni a **masterPrivateClusterIp**.  Az IP-hozzá lesz rendelve a fő terheléselosztó előtérrendszerhez.  A IP-cím a fő alhálózathoz, és nincs használatban a CIDR belül kell lennie.  **masterClusterDnsType** állítsa "egyéni" és a DNS-nevet kell adni a fő **masterClusterDns**.  A DNS-nevet a statikus magánhálózati IP-címet meg kell felelnie, és a fő csomópontok a konzol eléréséhez használható.

Ha privát útválasztó van kijelölve (**routerClusterType**= a titkos), egy statikus magánhálózati IP-címet meg kell határozni a **routerPrivateClusterIp**.  Az IP-előtérrendszerét hozzá lesz rendelve a infra terheléselosztó.  A IP-cím a CIDR belül kell lennie az infrastruktúra alhálózatot, és nincs használatban.  **routingSubDomainType** állítsa "egyéni" és a helyettesítő karaktert tartalmazó DNS-nevét az útválasztási kell adni a **routingSubDomain**.  

Ha privát főkiszolgálókból és a privát útválasztó van kiválasztva, az egyéni tartománynevet is meg kell adni a **tartománynév**

A sikeres telepítést követően a megerősített csomópont egyetlen csomópont egy nyilvános IP-címet is ssh be.  Akkor is, ha a fő csomópontok nyilvános vannak konfigurálva, hogy nem érhetőek el az ssh hozzáférést.

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
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
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
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
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
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
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
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

Cserélje le a paramétereket a jellemző információk.

Előfordulhat, hogy a különböző kiadások eltérő paraméterekkel, ezért ellenőrizze a szükséges paramétereket az ágat használja.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy.Parameters.json file explained

| Tulajdonság | Leírás | Az érvényes beállítások | Alapértelmezett érték |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL-cím-összetevők (json, parancsprogramok stb.) |  |  https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Azure-régió erőforrásokat üzembe kívánja helyezni |  |  |
| `masterVmSize` | A fő virtuális gép méretét. Válasszon olyan az azuredeploy.json fájlban felsorolt engedélyezett Virtuálisgép-méretek |  | Standard_E2s_v3 |
| `infraVmSize` | Méretét az infrastruktúra virtuális Gépet. Válasszon olyan az azuredeploy.json fájlban felsorolt engedélyezett Virtuálisgép-méretek |  | Standard_D4s_v3 |
| `nodeVmSize` | Az alkalmazás csomópont virtuális gép méretét. Válasszon olyan az azuredeploy.json fájlban felsorolt engedélyezett Virtuálisgép-méretek |  | Standard_D4s_v3 |
| `cnsVmSize` | A tároló natív (CNS) tárolócsomópont virtuális gép méretét. Válasszon olyan az azuredeploy.json fájlban felsorolt engedélyezett Virtuálisgép-méretek |  | Standard_E4s_v3 |
| `osImageType` | Az RHEL-lemezkép használata. defaultgallery: Igény szerinti; Marketplace-en: külső kép | defaultgallery <br> piactér | defaultgallery |
| `marketplaceOsImage` | Ha `osImageType` Marketplace-en, akkor adja meg az "publisher", "ajánlat", "sku", "verziójú" a marketplace-ajánlat a megfelelő értékeket. Ez a paraméter egy olyan objektum típus |  |  |
| `storageKind` | Használandó tárolási típus  | managed<br> unmanaged | managed |
| `openshiftClusterPrefix` | A fürt csomópontjaihoz tartozó gazdagépnevek konfigurálásához használt előtag.  1 – 20 karakter |  | sajátfürt |
| `minoVersion` | Az OpenShift Container Platform 3.11 üzembe helyezéséhez alverziója |  | 69 |
| `masterInstanceCount` | A főkiszolgálók csomópontok üzembe helyezéséhez | 1, 3, 5 | 3 |
| `infraInstanceCount` | Hány infra csomópontok üzembe helyezése | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Csomópontok üzembe helyezéséhez | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | CNS csomópontok üzembe helyezéséhez | 3, 4 | 3 |
| `osDiskSize` | A virtuális géphez (GB) operációsrendszer-lemez mérete | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Adatlemez csatlakoztatása a Docker-köteten (GB-ban) csomópontok mérete | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Adatok lemez (GB-ban glusterfs által használható CNS csomópontok csatlakoztatni kívánt mérete | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Az operációs rendszer (VM) bejelentkezési és a kezdeti OpenShift felhasználó rendszergazdai felhasználónév |  | ocpadmin |
| `enableMetrics` | Engedélyezze a mérőszámok. Metrikák szükséges további erőforrások ezért válasszon megfelelő méretű infrastruktúra a virtuális gép számára | true <br> false | false |
| `enableLogging` | Engedélyezze a naplózást. elasticsearch pod van szükség, 8 GB RAM-MAL ezért válasszon megfelelő méretű infrastruktúra a virtuális gép számára | true <br> false | false |
| `enableCNS` | Tároló natív tároló engedélyezése | true <br> false | false |
| `rhsmUsernameOrOrgId` | Red Hat-előfizetés-kezelő felhasználónév vagy a szervezet azonosítója |  |  |
| `rhsmPoolId` | A Red Hat-előfizetés-kezelő készlet azonosítója, amely tartalmazza a számítási csomópontok OpenShift jogosultságok |  |  |
| `rhsmBrokerPoolId` | A Red Hat-előfizetés-kezelő készlet azonosítója, amely az OpenShift jogosultságok főkiszolgálóhoz, és infrastruktúra-csomópontokat tartalmaz. Ha nincs másik címkészlet azonosítókat, adja meg a "rhsmPoolId" ugyanazon készlet azonosítója |  |
| `sshPublicKey` | Az SSH nyilvános kulcs másolása Itt |  |  |
| `keyVaultSubscriptionId` | Az előfizetés-azonosító, az előfizetés, amely tartalmazza a Key Vault |  |  |
| `keyVaultResourceGroup` | Az erőforráscsoport, amely tartalmazza a kulcstároló nevét |  |  |
| `keyVaultName` | A létrehozott Key Vault neve |  |  |
| `enableAzure` | Azure-felhő szolgáltató engedélyezése | true <br> false | true |
| `aadClientId` | Az Azure Active Directory ügyfél-azonosító az egyszerű szolgáltatás Alkalmazásazonosítója néven is ismert |  |  |
| `domainName` | Az egyéni tartománynév használata (ha alkalmazható) neve. Állítsa be a "none", ha nem teljesen privát fürt üzembe helyezése |  | nincs |
| `masterClusterDnsType` | OpenShift webkonzol tartomány típusa. "default" infra fogja használni az eredeti DNS-címke nyilvános IP-cím. "egyéni" lehetővé teszi, hogy a saját nevének meghatározása | alapértelmezett <br> egyéni | alapértelmezett |
| `masterClusterDns` | Az OpenShift webkonzol eléréséhez, ha az "egyéni" a kiválasztott egyéni DNS-név `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Ha a "nipio", állítsa `routingSubDomain` nip.io fogja használni.  Használja a "custom", ha saját tartomány, amelyet az továbbításához használni kívánt | nipio <br> egyéni | nipio |
| `routingSubDomain` | A helyettesítő karakteres útválasztáshoz, ha a kiválasztott "egyéni", a használni kívánt DNS-név `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Válassza ki, hogy egy meglévő virtuális hálózatot, vagy hozzon létre egy új virtuális hálózatot | meglévő <br> új | új |
| `virtualNetworkResourceGroupName` | Az új virtuális hálózatot, ha a kiválasztott "új" az erőforráscsoport nevét `virtualNetworkNewOrExisting` |  | resourceGroup().name |
| `virtualNetworkName` | Az új virtuális hálózatot hozhat létre, ha a "new" a kiválasztott neve `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Az új virtuális hálózatot, cím előtagja |  | 10.0.0.0/14 |
| `masterSubnetName` | A fő alhálózat neve |  | mastersubnet |
| `masterSubnetPrefix` | A fő alhálózat - használt CIDR kell lennie egy részét a címelőtagja |  | 10.1.0.0/16 |
| `infraSubnetName` | Neve az infrastruktúra alhálózat |  | infrasubnet |
| `infraSubnetPrefix` | Használt CIDR az infrastruktúra - alhálózat kell lennie egy részét a címelőtagja |  | 10.2.0.0/16 |
| `nodeSubnetName` | A csomópont-alhálózat neve |  | nodesubnet |
| `nodeSubnetPrefix` | A csomópont alhálózat - használt CIDR kell lennie egy részét a címelőtagja |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Teljes fő csomópontok meglévő alhálózat-hivatkozás. Nincs szükség, ha létrehozása új virtuális hálózat / alhálózat |  |  |
| `existingInfraSubnetReference` | Csomópontok infrastruktúra teljes leírása a meglévő IP-alhálózatot. Nincs szükség, ha létrehozása új virtuális hálózat / alhálózat |  |  |
| `existingCnsSubnetReference` | Teljes CNS csomópontok meglévő alhálózat-hivatkozás. Nincs szükség, ha létrehozása új virtuális hálózat / alhálózat |  |  |
| `existingNodeSubnetReference` | Teljes számítási csomópontok meglévő alhálózat-hivatkozás. Nincs szükség, ha létrehozása új virtuális hálózat / alhálózat |  |  |
| `masterClusterType` | Adja meg, hogy a fürt használja-e a nyilvános vagy privát fő csomóponttal. Ha privát választ, a fő csomópontok nem az interneten egy nyilvános IP-címen keresztül kitéve. Ehelyett a magánhálózati IP-cím megadott fogja használni a `masterPrivateClusterIp` | nyilvános <br> privát | nyilvános |
| `masterPrivateClusterIp` | Ha privát fő csomópont van kiválasztva, majd egy magánhálózati IP-címet kell megadni használatra a belső terheléselosztó fő csomóponttal. A statikus IP-cím a fő alhálózat és még nincs használatban a CIDR-blokk belül kell lennie. Ha nyilvános fő csomópont van kiválasztva, ez az érték nem használható, de továbbra is meg kell adni |  | 10.1.0.200 |
| `routerClusterType` | Adja meg, hogy a fürt használja-e nyilvános vagy privát infra csomópontok. Ha privát választ, az infrastruktúra csomópontok nem érhetők el az interneten egy nyilvános IP-címen keresztül. Ehelyett a magánhálózati IP-cím megadott fogja használni a `routerPrivateClusterIp` | nyilvános <br> privát | nyilvános |
| `routerPrivateClusterIp` | Ha privát infra csomópont ki van jelölve, majd egy magánhálózati IP-címet kell megadni a belső terheléselosztó által infra csomópontok használhatók. A statikus IP-cím a fő alhálózat és még nincs használatban a CIDR-blokk belül kell lennie. Ha nyilvános infra csomópont ki van jelölve, ez az érték nem használható, de továbbra is meg kell adni |  | 10.2.0.200 |
| `routingCertType` | Egyéni tanúsítvány használata az útválasztási tartomány vagy az alapértelmezett önaláírt tanúsítvány – a kapott utasításokat követve **egyéni tanúsítványokat** szakasz | selfsigned <br> egyéni | selfsigned |
| `masterCertType` | Egyéni tanúsítvány használata a fő tartomány vagy az alapértelmezett önaláírt tanúsítvány – a kapott utasításokat követve **egyéni tanúsítványokat** szakasz | selfsigned <br> egyéni | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Üzembe helyezés az Azure CLI használatával

> [!NOTE] 
> A következő parancs használatához az Azure CLI-vel 2.0.8 vagy újabb. Az a parancssori felület verziójának ellenőrzéséhez az `az --version` parancsot. A parancssori felület verziójának frissítéséhez lásd [Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

Az alábbi példa az OpenShift fürt és minden kapcsolódó erőforrás helyez üzembe helyezzen egy erőforráscsoportban openshiftrg, nevű myOpenShiftCluster központi telepítés nevét. A sablon közvetlenül a GitHub-adattárat, és a egy helyi paraméterek azuredeploy.parameters.json fájlhoz nevű fájllal a hivatkozik.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Az üzembe helyezés legalább 60 percig tart, üzembe helyezett csomópontok és a konfigurált beállítások teljes száma alapján. A megerősített DNS teljes tartománynév és az OpenShift-konzol URL-cím nyomtat a terminálon az üzembe helyezést.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Ha nem szeretné a parancssorból, Várakozás a telepítés befejeződik, adja hozzá a lefoglalhatnak `--no-wait` , a csoport központi telepítésének a lehetőségek közül. Az üzembe helyezés kimenete az erőforráscsoport az üzembe helyezés szakaszban az Azure Portalról kérhető.

## <a name="connect-to-the-openshift-cluster"></a>Csatlakozzon az OpenShift-fürthöz

Az üzembe helyezést követően a kimeneti szakaszban az üzemelő példány lekérdezni a kapcsolatot. Csatlakozás a böngészőjében az OpenShift konzol használatával a **OpenShift-konzol URL-címe**. Akkor is SSH, a bástyagazdagép. Következő egy példa, ahol a rendszergazda felhasználóneve clusteradmin, a megerősített nyilvános IP-cím DNS teljes Tartományneve pedig bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

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
