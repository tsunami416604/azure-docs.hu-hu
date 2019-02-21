---
title: Windows-alapú Service Fabric-fürt létrehozása az Azure-ban | Microsoft Docs
description: Ebből az oktatóanyagból megismerheti, hogyan helyezhet üzembe Windows-alapú Service Fabric-fürtöt egy Azure virtuális hálózatban és hálózati biztonsági csoportban a PowerShell használatával.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/19/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 590e1e5853ccf4a525477f194c78f1fd8ce679ed
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453069"
---
# <a name="tutorial-deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Oktatóanyag: Windows-alapú Service Fabric-fürt üzembe helyezése Azure virtuális hálózatban

Ez az oktatóanyag egy sorozat első része. Megismerheti, hogyan helyezhet üzembe Windows rendszert futtató Service Fabric-fürtöt [Azure virtuális hálózatban (VNET)](../virtual-network/virtual-networks-overview.md) és [hálózati biztonsági csoportban](../virtual-network/virtual-networks-nsg.md) a PowerShell és egy sablon használatával. Amikor végzett, a felhőben futó fürttel fog rendelkezni, amelyre alkalmazásokat telepíthet.  Linux-alapú fürt létrehozása Azure CLI használatával: [Biztonságos Linux-fürt létrehozása az Azure-ban](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Ez az oktatóanyag egy éles forgatókönyvet ismertet.  Ha azt szeretné, hozzon létre egy kisebb fürtöt tesztelési célokra, lásd: [egy tesztfürt létrehozása](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * VNET létrehozása a PowerShell használatával
> * Kulcstartó létrehozása és tanúsítvány feltöltése
> * Az Azure Active Directory-hitelesítés beállítása
> * Biztonságos Service Fabric-fürt létrehozása az Azure PowerShellben
> * A fürt védelme X.509-tanúsítvánnyal
> * Csatlakozás a fürthöz PowerShell használatával
> * Fürt eltávolítása

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Biztonságos fürt létrehozása az Azure-ban
> * [Fürt horizontális fel- és leskálázása](service-fabric-tutorial-scale-cluster.md)
> * [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)
> * [Fürt törlése](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Telepítse a [Service Fabric SDK-t és PowerShell-modult](service-fabric-get-started.md).
* Telepítse az [Azure PowerShell-modul 4.1-es vagy újabb verzióját](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
* Tekintse át a kapcsolatos főbb fogalmakat [Azure fürtök](service-fabric-azure-clusters-overview.md)

Az alábbi eljárással létrehozunk egy hét csomópontos Service Fabric-fürtön. A Service Fabric-fürtök Azure-ban történő futtatásával járó költségek kiszámításához használja az [Azure-díjkalkulátort](https://azure.microsoft.com/pricing/calculator/).

## <a name="download-and-explore-the-template"></a>A sablon letöltése és megismerése

Töltse az alábbi Resource Manager-sablonfájlokat:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Ez a sablon egy virtuális hálózatot és a egy hálózati biztonsági csoport hét virtuális gépek és a három csomóponttípusok biztonságos fürt üzembe helyezése.  További mintasablonokat a [GitHubon](https://github.com/Azure-Samples/service-fabric-cluster-templates) talál.  Az [azuredeploy.json][template] több erőforrást is üzembe helyez, többek között az alábbiakat.

### <a name="service-fabric-cluster"></a>Service Fabric-fürt

A **Microsoft.ServiceFabric/clusters** erőforrásban egy Windows-fürt az alábbi jellemzőkkel lesz konfigurálva:

* három csomópont típusa
* öt csomópont van az elsődleges csomóponttípusban (a sablon paramétereiben konfigurálható), az egyes csomópontok a két csomópont típusok
* Operációs rendszer: A Windows Server 2016 Datacenter tárolókkal (a sablon paramétereiben konfigurálható)
* tanúsítványon alapuló védelem (a sablon paramétereiben konfigurálható);
* engedélyezve van a [fordított proxy](service-fabric-reverseproxy.md);
* engedélyezve van a [DNS szolgáltatás](service-fabric-dnsservice.md);
* bronz szintű [tartóssági szint](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) (a sablon paramétereiben konfigurálható);
* ezüst szintű [megbízhatósági szint](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) (a sablon paramétereiben konfigurálható);
* ügyfélkapcsolati végpont: 19000 (a sablon paramétereiben konfigurálható)
* HTTP-átjáró végpontja: 19080-as (a sablon paramétereiben konfigurálható)

### <a name="azure-load-balancer"></a>Azure Load Balancer

A **Microsoft.Network/loadBalancers** erőforrásban a rendszer egy terheléselosztót konfigurál, a mintavételeket és szabályokat pedig az alábbi portokra állítja be:

* ügyfélkapcsolati végpont: 19000
* HTTP-átjáró végpontja: 19080
* alkalmazásport: 80
* alkalmazásport: 443
* A Service Fabric fordított proxy: 19081

Ha további alkalmazásportokra van szükség, akkor módosítania kell a **Microsoft.Network/loadBalancers** és a **Microsoft.Network/networkSecurityGroups** erőforrást a forgalom beengedésére.

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtuális hálózat, alhálózat és hálózati biztonsági csoport

A virtuális hálózat, az alhálózat és a hálózati biztonsági csoport neve a sablon paramétereiben van meghatározva.  A virtuális hálózat és az alhálózat címtere szintén a sablon paramétereiben határozható meg és a **Microsoft.Network/virtualNetworks** erőforrásban van konfigurálva:

* virtuális hálózat címtere: 172.16.0.0/20
* A Service Fabric-alhálózat címtere: 172.16.2.0/23

Az alábbi bejövő forgalmi szabályok vannak engedélyezve a **Microsoft.Network/networkSecurityGroups** erőforrásban. A portok értékét a sablon változóinak módosításával módosíthatja.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Csomópontok közötti kommunikáció – 1025, 1026, 1027,
* rövid élettartamú porttartomány – 49152–65534 (legalább 256 port szükséges),
* Alkalmazások által használható portok: 80 és 443
* alkalmazásportok tartománya – 49152–65534 (a szolgáltatások közötti kommunikációra szolgál, és nincs megnyitva a terheléselosztóban),
* összes többi port letiltása.

Ha további alkalmazásportokra van szükség, akkor módosítania kell a **Microsoft.Network/loadBalancers** és a **Microsoft.Network/networkSecurityGroups** erőforrást a forgalom beengedésére.

### <a name="windows-defender"></a>Windows Defender
Alapértelmezés szerint [Windows Defender víruskereső](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) telepítve és működőképes a Windows Server 2016-on. A felhasználói felület néhány termékváltozat alapértelmezés szerint telepítve van, de nem kötelező.  Minden egyes csomópont típusa vagy Virtuálisgép-méretezési deklarált a sablonban a [Azure virtuális gép az Antimaleware bővítmény](/azure/virtual-machines/extensions/iaas-antimalware-windows) szolgál a Service Fabric-könyvtárak és folyamatok kizárása:

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>Sablon paramétereinek megadása

Az [azuredeploy.parameters.json][parameters] paraméterfájl számos, a fürt és a társított erőforrások üzembe helyezéséhez használt értéket meghatároz. Néhány paraméter, amelyeket lehet, hogy módosítani kell az üzembe helyezéshez:

|Paraméter|Példaérték|Megjegyzések|
|---|---||
|adminUserName|vmadmin| Rendszergazdai felhasználónév a fürt virtuális gépeihez.[Virtuális gép felhasználónévre vonatkozó követelményei](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm) |
|adminPassword|Password#1234| Rendszergazdai jelszó a fürt virtuális gépeihez. [Virtuális gép jelszóra vonatkozó követelményei](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm)|
|clusterName|mysfcluster123| A fürt neve. Csak betűket és számokat tartalmazhat. 3–23 karakter hosszú lehet.|
|location|southcentralus| A fürt helye. |
|certificateThumbprint|| <p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a tanúsítvány SHA1 ujjlenyomatának értékét. Például: „6190390162C988701DB5676EB81083EA608DCCF3”</p>. |
|certificateUrlValue|| <p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie. </p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a tanúsítvány URL-címét. Például: „https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346”.</p>|
|sourceVaultValue||<p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a forrástároló értékét. For example, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Azure Active Directory-ügyfél-hitelesítés beállítása
Service Fabric-fürtök az Azure-ban üzemeltetett nyilvános hálózaton üzembe helyezett az ügyfél és a csomópont közötti kölcsönös hitelesítés ajánlás van:
* Az ügyfelek identitását az Azure Active Directory használata
* Egy tanúsítványt a kiszolgáló identitását és a http-kommunikációhoz SSL-titkosítás

A Service Fabric-fürt előtt kell elvégezni az ügyfelek hitelesítéséhez az Azure AD beállításához [a fürt létrehozása](#createvaultandcert).  Azure ad-ben alkalmazásokhoz való felhasználói hozzáférés kezelése lehetővé teszi a szervezetek (más néven bérlők). 

Service Fabric-fürt kínál a különböző belépési pontok annak felügyeleti funkciójához, beleértve a webalapú [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) és [Visual Studio](service-fabric-manage-application-in-visual-studio.md). Ennek eredményeképpen hoz létre, ki férhet hozzá a fürt két Azure AD-alkalmazások: egy webalkalmazás és a egy natív alkalmazást.  Az alkalmazások létrehozása után felhasználók hozzárendelése csak olvasható, és rendszergazdai szerepkörök.

> [!NOTE]
> A következő lépéseket kell elvégeznie, a fürt létrehozása előtt. A parancsfájlok várhatóan a fürt nevét és a végpontok, mert az értékeket meg kell tervezni, és nem az, hogy már létrehozott értékeket.

Ez a cikk feltételezzük, hogy már létrehozott egy bérlőt. Ha nem rendelkezik, először olvassa el [Azure Active Directory-bérlő beszerzése](../active-directory/develop/quickstart-create-new-tenant.md).

Egyes lépéseit az Azure AD konfigurálása a Service Fabric-fürt leegyszerűsítése hoztunk létre egy Windows PowerShell-parancsprogramok halmaza. [Töltse le a parancsfájlok](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) a számítógépre.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Az Azure AD-alkalmazások létrehozása és a felhasználók szerepkörökhöz rendelése
Hozzon létre két Azure AD-alkalmazást a fürthöz való hozzáférés szabályozásához: egy webalkalmazás és a egy natív alkalmazást. Az alkalmazások, amelyek a fürt létrehozását követően rendelje hozzá a felhasználókat, hogy a [szerepkörök a Service Fabric által támogatott](service-fabric-cluster-security-roles.md): csak olvasható és a rendszergazdával.

Futtatás `SetupApplications.ps1`, és meg paraméterekként a bérlő azonosítója, a fürt neve és a webes alkalmazás válasz URL-cím.  Felhasználónevek és jelszavak, a felhasználók számára is megadhatja.  Példa:

```PowerShell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Az országos felhők (például az Azure Government, Azure China esetén az Azure Germany), meg kell adnia a `-Location` paraméter.

Annak a *TenantId*, vagy a címtár-azonosító, az a [az Azure portal](https://portal.azure.com). Válassza ki **Azure Active Directory -> Tulajdonságok** , és másolja a a **címtár-azonosító** értéket.

*ClusterName* az Azure AD-alkalmazások, a parancsfájl által létrehozott előtagot használja. Ez nem pontosan egyeznie kell a tényleges fürt neve. Célja, hogy csak az, hogy egyszerűbb legyen az Azure AD-összetevők leképezése, amelyet éppen használ a Service Fabric-fürthöz.

*WebApplicationReplyUrl* van az alapértelmezett végpont, amely az Azure AD a felhasználóknak ad vissza, miután a bejelentkezés befejezéséhez. Állítsa be ezt a végpontot a Service Fabric Explorert a fürtben, amely alapértelmezés szerint az átemelt:

https://&lt;cluster_domain&gt;:19080/Explorer

Jelentkezzen be az Azure AD-bérlői rendszergazdai jogosultságokkal rendelkező fiók kéri. Miután bejelentkezett, a parancsfájl a webes és natív alkalmazások, amelyek a Service Fabric-fürtöt hoz létre. Ha megtekinti a bérlő alkalmazások a [az Azure portal](https://portal.azure.com), két új bejegyzést kell megjelennie:

   * *ClusterName*\_fürt
   * *ClusterName*\_ügyfél

A parancsfájl jelenít meg, hogy legyen célszerű hagyja megnyitva a PowerShell-ablakot, hogy a fürt létrehozásakor az Azure Resource Manager-sablon által szükséges JSON-fájl.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Azure AD használata az ügyfél hozzáférésének az Azure AD-konfiguráció hozzáadása
Az a [azuredeploy.json][template], konfigurálja az Azure AD-t a **Microsoft.ServiceFabric/clusters** szakaszban.  Paraméterek hozzáadása a bérlő azonosítója, fürt Alkalmazásazonosító és ügyfél-alkalmazás azonosítója.  

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

Adja hozzá a paraméter értékét a [azuredeploy.parameters.json] [ parameters] paramétereket tartalmazó fájlt.  Példa:

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>A virtuális hálózat és a fürt üzembe helyezése

Ezután állítsa be a hálózati topológiát, és helyezze üzembe a Service Fabric-fürtöt. Az [azuredeploy.json][template] Resource Manager-sablon egy virtuális hálózatot (VNET-et), továbbá egy alhálózatot és egy hálózati biztonsági csoportot (NSG-t) hoz létre a Service Fabric számára. A sablon emellett egy fürtöt is üzembe helyez engedélyezett tanúsítványalapú biztonsággal.  Éles fürtök esetén hitelesítésszolgáltatótól (CA) származó tanúsítványt használjon fürttanúsítványként. A tesztfürtök számára önaláírt tanúsítvánnyal is biztosítható védelem.

Ez a cikk a sablon üzembe helyez egy fürtöt, amely a tanúsítvány ujjlenyomatát a fürttanúsítvány azonosítására használ.  Nincs két tanúsítványt ugyanazzal az ujjlenyomattal, ami megnehezíti a tanúsítványkezelés is rendelkezhet. Egy tanúsítvány-ujjlenyomatok a tanúsítvány köznapi nevek használatával üzembe helyezett fürt közötti váltás révén jóval egyszerűbb tanúsítványkezelés.  A tanúsítvány köznapi nevek a tanúsítványok kezeléséhez használja a fürt frissítése, olvassa el [fürt módosítása a tanúsítvány köznapi neve felügyeleti](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-using-an-existing-certificate"></a>Fürt létrehozása meglévő tanúsítvány használatával

Az alábbi szkript a [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) parancsmag és egy sablon használatával helyez üzembe egy új fürtöt az Azure-ban. A parancsmag emellett egy új kulcstárolót is létrehoz az Azure-ban, és feltölti a tanúsítványt.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # must match the clusterName parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Fürt létrehozása új, önaláírt tanúsítvány használatával

Az alábbi szkript a [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) parancsmag és egy sablon használatával helyez üzembe egy új fürtöt az Azure-ban. A parancsmag emellett egy új kulcstárolót is létrehoz az Azure-ban, hozzáad egy új önaláírt tanúsítványt a kulcstárolóhoz, és letölti helyben a tanúsítványfájlt.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Csatlakozás a biztonságos fürthöz

A fürthöz a Service Fabric SDK-val együtt telepített Service Fabric PowerShell-modullal csatlakozhat.  Először telepítse a tanúsítványt a számítógépen az aktuális felhasználó Személyes (Saját) tárolójába.  Futtassa az alábbi PowerShell-parancsot:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Most már készen áll a biztonságos fürthöz való csatlakozásra.

A **Service Fabric** PowerShell-modul számos parancsmagot biztosít a Service Fabric-fürtök, -alkalmazások és -szolgáltatások kezelésére.  A biztonságos fürthöz való kapcsolódáshoz használja a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) parancsmagot. A tanúsítvány SHA1 ujjlenyomatával és kapcsolati végpontjával kapcsolatos részletek az előző lépés kimenetében találhatók.

Ha korábban már beállított AAD ügyfél-hitelesítés, futtassa a következőt: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Ha nem állított be AAD ügyfél-hitelesítés, futtassa a következő parancsot:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

A [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) parancsmag futtatásával ellenőrizze, hogy csatlakozik-e, és hogy a fürt állapota kifogástalan-e.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A jelen oktatóanyag-sorozatban található további cikkek a létrehozott fürtöt fogják felhasználni. Ha nem azonnal tér rá a következő cikkre, érdemes [törölnie a fürtöt](service-fabric-cluster-delete.md) a felmerülő költségek elkerülése érdekében.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * VNET létrehozása a PowerShell használatával
> * Kulcstartó létrehozása és tanúsítvány feltöltése
> * Az Azure Active Directory-hitelesítés beállítása
> * Biztonságos Service Fabric-fürt létrehozása az Azure-ban PowerShell használatával
> * A fürt védelme X.509-tanúsítvánnyal
> * Csatlakozás a fürthöz PowerShell használatával
> * Fürt eltávolítása

Folytassa a következő oktatóanyaggal, amelyben megismerheti a fürtök skálázásának módját.
> [!div class="nextstepaction"]
> [Fürt skálázása](service-fabric-tutorial-scale-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
