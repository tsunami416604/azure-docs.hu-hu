---
title: OpenShift Container Platform 3.11 üzembe helyezése az Azure-ban
description: OpenShift Container Platform 3.11 üzembe helyezése az Azure-ban.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 615d9a3c5c359174ef15028e82044a85da0dd733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561286"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>OpenShift Container Platform 3.11 üzembe helyezése az Azure-ban

Az OpenShift Container Platform 3.11 azure-beli üzembe helyezéséhez számos módszer közül választhat:

- Manuálisan telepítheti a szükséges Azure-infrastruktúra-összetevőket, majd követheti az [OpenShift Container Platform dokumentációját.](https://docs.openshift.com/container-platform)
- Egy meglévő [Erőforrás-kezelő sablont](https://github.com/Microsoft/openshift-container-platform/) is használhat, amely leegyszerűsíti az OpenShift container platformfürt telepítését.
- Egy másik lehetőség az [Azure Marketplace-ajánlat](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)használata.

Minden lehetőséghez Red Hat előfizetés szükséges. A központi telepítés során a Red Hat Enterprise Linux-példány regisztrálva van a Red Hat-előfizetéshez, és az OpenShift container platform jogosultságait tartalmazó készletazonosítóhoz van csatolva.
Győződjön meg arról, hogy rendelkezik érvényes Red Hat Subscription Manager (RHSM) felhasználónévvel, jelszóval és készletazonosítóval. Használhat aktiválókulcsot, orgia-azonosítót és készletazonosítót. Ezeket az adatokat a https://access.redhat.comrendszerbe bejelentkezve ellenőrizheti.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Telepítés az OpenShift container platform erőforrás-kezelő 3.11-es sablonjával

### <a name="private-clusters"></a>Privát fürtök

A privát OpenShift-fürtök üzembe helyezése többre van szükség, mint hogy a fő terheléselosztóhoz (webkonzolhoz) vagy az infravörös terheléselosztóhoz (útválasztóhoz) nem tartozik nyilvános IP-cím.  A privát fürt általában egyéni DNS-kiszolgálót (nem az alapértelmezett Azure DNS-t), egyéni tartománynevet (például contoso.com) és előre definiált virtuális hálózatot használ.  A magánfürtök esetében előre konfigurálnia kell a virtuális hálózatot az összes megfelelő alhálózattal és DNS-kiszolgáló-beállítással.  Ezután a **meglévőMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference**és **existingNodeSubnetReference használatával** adja meg a meglévő alhálózatot a fürt számára.

Ha a saját főkiszolgáló van kiválasztva **(masterClusterType**=private), statikus privát IP-címet kell megadni a **masterPrivateClusterIp**számára.  Ez az IP lesz rendelve a fő terheléselosztó előtétjéhez.  Az IP-címnek a fő alhálózat CIDR-én belül kell lennie, és nem használatban kell lennie.  **a masterClusterDnsType típust** "egyéni"-re kell állítani, és meg kell adni a **masterClusterDns fő**DNS-nevét.  A DNS-névnek le kell képeznie a statikus privát IP-címet, és a fő csomópontokon lévő konzol eléréséhez kell használni.

Ha a privát útválasztó van kiválasztva **(routerClusterType**=private), statikus privát IP-címet kell megadni a **routerPrivateClusterIp**számára.  Ez az IP lesz rendelve az infra terheléselosztó elülső végén.  Az IP-címnek az infravörös alhálózat CIDR-én belül kell lennie, és nem használhatónak kell lennie.  **az routingSubDomainType** típust "custom" -re kell állítani, és az útválasztáshoz helyettesítő DNS-nevet kell megadni **az routingSubDomain**művelethez.  

Ha a privát mesterek és a privát útválasztó van kiválasztva, az egyéni tartománynevet is meg kell adni a **domainName**

A sikeres üzembe helyezés után a bástyacsomópont az egyetlen olyan csomópont, amelynek nyilvános IP-címe van, amelybe ssh-t használhat.  Még akkor is, ha a fő csomópontok nyilvános hozzáférésre vannak konfigurálva, nem érhetők el az ssh hozzáféréshez.

Az Erőforrás-kezelő sablonnal történő telepítéshez egy paraméterfájlt kell használnia a bemeneti paraméterek biztosításához. A központi telepítés további testreszabásához elágazás a GitHub-tármű, és módosítsa a megfelelő elemeket.

Néhány gyakori testreszabási lehetőség, de nem korlátozódik a következőkre:

- Bástya virtuális gép mérete (változó azuredeploy.json)
- Elnevezési konvenciók (változók az azuredeploy.jsonban)
- OpenShift fürtspecifikus, gazdagépfájlon keresztül módosítva (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>A paraméterfájl konfigurálása

Az [OpenShift Container Platform sablon](https://github.com/Microsoft/openshift-container-platform) több ágat is elérhető az OpenShift container platform különböző verzióihoz.  Az igényeinek megfelelően közvetlenül a tárcsóból telepítheti, vagy elágazhatja a tárta, és egyéni módosításokat hajthat végre a sablonokon vagy parancsfájlokon a telepítés előtt.

Használja `appId` a `aadClientId` paraméterhez korábban létrehozott egyszerű szolgáltatás értékét.

A következő példa az azuredeploy.parameters.json nevű paramétereket mutatja be az összes szükséges bemenettel.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

Cserélje le a paramétereket a megadott adatokra.

A különböző kiadások különböző paraméterekkel rendelkezhetnek, ezért ellenőrizze a használt ághoz szükséges paramétereket.

### <a name="azuredeployparametersjson-file-explained"></a>Azuredeploy. Parameters.json fájl magyarázata

| Tulajdonság | Leírás | Érvényes beállítások | Alapértelmezett érték |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | Az összetevők URL-címe (json, parancsfájlok stb.) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Az Azure-régió az erőforrások üzembe helyezéséhez |  |  |
| `masterVmSize` | A fő virtuális gép mérete. Válasszon az azuredeploy.json fájlban felsorolt engedélyezett virtuálisgép-méretek közül |  | Standard_E2s_v3 |
| `infraVmSize` | Az Infra VM mérete. Válasszon az azuredeploy.json fájlban felsorolt engedélyezett virtuálisgép-méretek közül |  | Standard_D4s_v3 |
| `nodeVmSize` | Az alkalmazáscsomópont virtuális gépének mérete. Válasszon az azuredeploy.json fájlban felsorolt engedélyezett virtuálisgép-méretek közül |  | Standard_D4s_v3 |
| `cnsVmSize` | A tároló natív tároló (CNS) csomópont virtuális gép mérete. Válasszon az azuredeploy.json fájlban felsorolt engedélyezett virtuálisgép-méretek közül |  | Standard_E4s_v3 |
| `osImageType` | A használandó RHEL-kép. defaultgallery: On-Demand; piactér: harmadik fél től származó kép | alapértelmezett galéria <br> piactér | alapértelmezett galéria |
| `marketplaceOsImage` | Ha `osImageType` piactér, akkor adja meg a megfelelő értékeket a "kiadó", "ajánlat", "sku", "verzió" a piactéri ajánlat. Ez a paraméter egy objektumtípus |  |  |
| `storageKind` | A használandó tároló típusa  | Kezelt<br> Menedzselt | Kezelt |
| `openshiftClusterPrefix` | Az állomásnevek konfigurálásához használt fürtelőtag az összes csomóponthoz.  1 és 20 karakter között |  | énfürt |
| `minoVersion` | Az OpenShift Container Platform 3.11 telepítésének alverziója |  | 69 |
| `masterInstanceCount` | Telepítandó mestercsomópontok száma | 1, 3, 5 | 3 |
| `infraInstanceCount` | Telepítandó infravörös csomópontok száma | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Telepítandó csomópontok száma | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Az üzembe helyezandó központi létszámcsomópontok száma | 3, 4 | 3 |
| `osDiskSize` | A virtuális gép operációsrendszer-lemezének mérete (GB-ban) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | A Docker-kötet csomópontjaihoz csatolandó adatlemez mérete (GB-ban) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | A cns-csomópontokhoz csatolandó adatlemez mérete a gluszterfek számára (GB-ban | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Rendszergazdai felhasználónév mind az operációs rendszer (VM) bejelentkezéséhez, mind a kezdeti OpenShift-felhasználóhoz |  | ocpadmin |
| `enableMetrics` | Metrikák engedélyezése. A metrikák több erőforrást igényelnek, ezért válassza ki a megfelelő méretet az Infra VM-hez | igaz <br> hamis | hamis |
| `enableLogging` | Naplózás engedélyezése. elasticsearch pod igényel 8 GB RAM így válassza ki a megfelelő méretű Infra VM | igaz <br> hamis | hamis |
| `enableCNS` | Tároló natív tárolójának engedélyezése | igaz <br> hamis | hamis |
| `rhsmUsernameOrOrgId` | Red Hat Előfizetés-kezelő felhasználóneve vagy szervezeti azonosítója |  |  |
| `rhsmPoolId` | A Red Hat Előfizetéskezelő készletazonosítója, amely a számítási csomópontok OpenShift-jogosultságait tartalmazza |  |  |
| `rhsmBrokerPoolId` | A Red Hat Előfizetéskezelő készletazonosítója, amely a mesterekre és az infracsomópontokra vonatkozó OpenShift-jogosultságokat tartalmazza. Ha nem rendelkezik különböző készletazonosítókkal, adja meg ugyanazt a készletazonosítót, mint az "rhsmPoolId" |  |
| `sshPublicKey` | Másolja az SSH nyilvános kulcsot ide |  |  |
| `keyVaultSubscriptionId` | A Key Vaultot tartalmazó előfizetés előfizetés-azonosítója |  |  |
| `keyVaultResourceGroup` | A Key Vaultot tartalmazó erőforráscsoport neve |  |  |
| `keyVaultName` | A létrehozott Key Vault neve |  |  |
| `enableAzure` | Az Azure Cloud-szolgáltató engedélyezése | igaz <br> hamis | igaz |
| `aadClientId` | Az Azure Active Directory ügyfélazonosítója , más néven egyszerű szolgáltatás alkalmazásazonosítója |  |  |
| `domainName` | A használandó egyéni tartománynév neve (ha van ilyen). "Nincs" beállítás, ha nem telepíti a teljesen privát fürtöt |  | Nincs |
| `masterClusterDnsType` | Az OpenShift webkonzol tartománytípusa. "default" fogja használni dns címke fő infra nyilvános IP. "egyéni" lehetővé teszi, hogy meghatározza a saját nevét | alapértelmezett <br> Egyéni | alapértelmezett |
| `masterClusterDns` | Az OpenShift webkonzol eléréséhez használt egyéni DNS-név, ha az "egyéni" lehetőséget választotta a`masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Ha a beállítás "nipio", `routingSubDomain` fogja használni nip.io.  Használja az "egyéni" lehetőséget, ha saját tartományt szeretne használni az útválasztáshoz. | nipio között <br> Egyéni | nipio között |
| `routingSubDomain` | Az útválasztáshoz használni kívánt helyettesítő karakter DNS-neve, ha az "egyéni" lehetőséget választotta a`routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Válassza ki, hogy meglévő virtuális hálózatot szeretne-e használni, vagy új virtuális hálózatot szeretne létrehozni | Meglévő <br> Új | Új |
| `virtualNetworkResourceGroupName` | Az új virtuális hálózat erőforráscsoportjának neve, ha az "új" lehetőséget választotta a`virtualNetworkNewOrExisting` |  | resourceGroup(.name |
| `virtualNetworkName` | A létrehozandó új virtuális hálózat neve, ha az "új" lehetőséget választotta a`virtualNetworkNewOrExisting` |  | nyíltshiftvnet |
| `addressPrefixes` | Az új virtuális hálózat címelőtagja |  | 10.0.0.0/14 |
| `masterSubnetName` | A főalhálózat neve |  | főalalhálózat |
| `masterSubnetPrefix` | A fő alhálózathoz használt CIDR -, a címelőfix egy részhalmazának kell lennie |  | 10.1.0.0/16 |
| `infraSubnetName` | Az infravörös alhálózat neve |  | infraaljtahálózat |
| `infraSubnetPrefix` | AZ infraalhálózathoz használt CIDR -, a címelőfix egy részhalmazának kell lennie |  | 10.2.0.0/16 |
| `nodeSubnetName` | A csomópont alhálózatának neve |  | nodesubnet |
| `nodeSubnetPrefix` | A csomópont alhálózatához használt CIDR -, a címelőfix egy részhalmazának kell lennie |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Teljes hivatkozás a főcsomópontok meglévő alhálózatára. Nem szükséges, ha új virtuális hálózat / alhálózat létrehozása |  |  |
| `existingInfraSubnetReference` | Teljes hivatkozás az infracsomópontok meglévő alhálózatára. Nem szükséges, ha új virtuális hálózat / alhálózat létrehozása |  |  |
| `existingCnsSubnetReference` | Teljes hivatkozás a cns-csomópontok meglévő alhálózatára. Nem szükséges, ha új virtuális hálózat / alhálózat létrehozása |  |  |
| `existingNodeSubnetReference` | Teljes hivatkozás a számítási csomópontok meglévő alhálózatára. Nem szükséges, ha új virtuális hálózat / alhálózat létrehozása |  |  |
| `masterClusterType` | Adja meg, hogy a fürt magán- vagy nyilvános főcsomópontokat használ-e. Ha a magánterület van kiválasztva, a fő csomópontok nem lesznek kitéve az internetnek nyilvános IP-cím révén. Ehelyett a`masterPrivateClusterIp` | Nyilvános <br> Privát | Nyilvános |
| `masterPrivateClusterIp` | Ha a saját főcsomópontok vannak kiválasztva, akkor meg kell adni egy privát IP-címet a fő csomópontok belső terheléselosztójának használatához. Ennek a statikus IP-címnek a fő alhálózat CIDR-blokkján belül kell lennie, és még nem használatban kell lennie. Ha nyilvános főcsomópontok vannak kiválasztva, akkor ez az érték nem lesz használva, de meg kell adni. |  | 10.1.0.200 |
| `routerClusterType` | Adja meg, hogy a fürt magán- vagy nyilvános infracsomópontokat használ-e. Ha privát ot választ, az infracsomópontok nem lesznek kitéve az internetnek nyilvános IP-n keresztül. Ehelyett a`routerPrivateClusterIp` | Nyilvános <br> Privát | Nyilvános |
| `routerPrivateClusterIp` | Ha a privát infracsomópontok vannak kiválasztva, akkor meg kell adni egy privát IP-címet az infracsomópontok belső terheléselosztójának használatához. Ennek a statikus IP-címnek a fő alhálózat CIDR-blokkján belül kell lennie, és még nem használatban kell lennie. Ha nyilvános infracsomópontok vannak kiválasztva, akkor ez az érték nem lesz használva, de meg kell adni. |  | 10.2.0.200 |
| `routingCertType` | Egyéni tanúsítvány használata a tartomány útválasztásához vagy az alapértelmezett önaláírt tanúsítványhoz – kövesse az Egyéni tanúsítványok szakasz **utasításait** | saját maga által aláírt <br> Egyéni | saját maga által aláírt |
| `masterCertType` | Egyéni tanúsítvány használata a főtartományhoz vagy az alapértelmezett önaláírt tanúsítványhoz – kövesse az Egyéni tanúsítványok szakasz **utasításait** | saját maga által aláírt <br> Egyéni | saját maga által aláírt |

<br>

### <a name="deploy-using-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

> [!NOTE] 
> A következő parancs hoz azure CLI 2.0.8 vagy újabb. A CLI-verziót a `az --version` paranccsal ellenőrizheti. A CLI-verzió frissítéséhez olvassa [el az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti)című témakört.

A következő példa az OpenShift-fürtöt és az összes kapcsolódó erőforrást egy openshiftrg nevű erőforráscsoportba helyezi, a myOpenShiftCluster központi telepítési nevével. A sablon közvetlenül a GitHub-tárból hivatkozik, és egy azuredeploy.parameters.json nevű helyi paraméterfájl használatos.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A központi telepítés legalább 60 percet vesz igénybe, a telepített csomópontok és a konfigurált lehetőségek teljes száma alapján. A Bastion DNS Teljes tartományontartományés AZ OpenShift konzol URL-címe a központi telepítés befejezésekor a terminálra nyomtatódik.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Ha nem szeretné lekötni a parancssort, amíg a `--no-wait` központi telepítés befejeződik, adja hozzá a csoport központi telepítésének egyik beállítását. A központi telepítés kimenete lehívható az Azure Portalon az erőforráscsoport üzembe helyezési szakaszában.

## <a name="connect-to-the-openshift-cluster"></a>Csatlakozás az OpenShift fürthöz

Amikor a központi telepítés befejeződik, olvassa be a kapcsolatot a központi telepítés kimeneti szakaszából. Csatlakozzon az OpenShift konzolhoz a böngészővel az **OpenShift konzol URL-címének**használatával. Azt is SSH a Bástya állomás. Az alábbiakban egy példa látható, amikor a rendszergazdai felhasználónév clusteradmin, és a megerősített nyilvános IP DNS-fqdn bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az [az csoport törlése](/cli/azure/group) paranccsal távolítsa el az erőforráscsoportot, az OpenShift-fürtöt és az összes kapcsolódó erőforrást, ha már nincs rájuk szükség.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>További lépések

- [Telepítés utáni feladatok](./openshift-container-platform-3x-post-deployment.md)
- [Az OpenShift azure-beli telepítésének hibái](./openshift-container-platform-3x-troubleshooting.md)
- [Az OpenShift container platform első lépései](https://docs.openshift.com)
