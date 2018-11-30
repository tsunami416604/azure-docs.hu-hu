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
ms.date: 09/27/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: a720bb906192731b8b636939e22b13a8e52bbe76
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632891"
---
# <a name="tutorial-deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Oktatóanyag: Windows-alapú Service Fabric-fürt üzembe helyezése Azure virtuális hálózatban

Ez az oktatóanyag egy sorozat első része. Megismerheti, hogyan helyezhet üzembe Windows rendszert futtató Service Fabric-fürtöt [Azure virtuális hálózatban (VNET)](../virtual-network/virtual-networks-overview.md) és [hálózati biztonsági csoportban](../virtual-network/virtual-networks-nsg.md) a PowerShell és egy sablon használatával. Amikor végzett, a felhőben futó fürttel fog rendelkezni, amelyre alkalmazásokat telepíthet.  Linux-alapú fürt létrehozása Azure CLI használatával: [Biztonságos Linux-fürt létrehozása az Azure-ban](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Ez az oktatóanyag egy éles forgatókönyvet ismertet.  Ha azt szeretné, hozzon létre egy kisebb fürtöt tesztelési célokra, lásd: [egy tesztfürt létrehozása](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * VNET létrehozása a PowerShell használatával
> * Kulcstartó létrehozása és tanúsítvány feltöltése
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
* Telepítse az [Azure PowerShell-modul 4.1-es vagy újabb verzióját](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Az alábbi eljárások egy ötcsomópontos Service Fabric-fürtöt hoznak létre. A Service Fabric-fürtök Azure-ban történő futtatásával járó költségek kiszámításához használja az [Azure-díjkalkulátort](https://azure.microsoft.com/pricing/calculator/).

## <a name="key-concepts"></a>Fő fogalmak

A [Service Fabric-fürt](service-fabric-deploy-anywhere.md) virtuális és fizikai gépek hálózaton keresztül csatlakozó készlete, amelyen mikroszolgáltatásokat helyezhet üzembe és felügyelhet. A fürtök több ezer gépre skálázhatók. A fürtök részét képező gépeket vagy virtuális gépeket csomópontoknak nevezzük. Minden csomóponthoz hozzá van rendelve egy csomópontnév (egy sztring). A csomópontok jellemzőkkel, például elhelyezési tulajdonságokkal rendelkeznek.

A csomópont típusa meghatározza a fürt egyes virtuálisgép-készleteinek méretét, számát és tulajdonságait. Minden megadott csomóponttípus [virtuálisgép-méretezési csoportként](/azure/virtual-machine-scale-sets/) lesz beállítva, amely egy, a virtuális gépek gyűjteményének csoportként való üzembe helyezésére és felügyeletére használható Azure számítási erőforrás. Ezután mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy fel, eltérő nyitott portokkal rendelkezhet, és eltérő kapacitásmetrikái lehetnek. A csomóponttípusok a fürtcsomópontcsoportok szerepkörének (például „előtér” vagy „háttér”) meghatározására szolgálnak.  A fürt több csomóponttípussal is rendelkezhet, de éles fürtök esetében az elsődleges csomóponttípusnak legalább öt (vagy tesztfürtök esetében legalább három) virtuális géppel kell rendelkeznie.  [A Service Fabric-rendszerszolgáltatások](service-fabric-technical-overview.md#system-services) elhelyezése az elsődleges csomóponttípusra történik.

A fürtöt egy fürttanúsítvány védi. A fürttanúsítvány egy X.509-tanúsítvány, amely a csomópontok közötti kommunikáció biztonságossá tételére, valamint a fürtkezelési végpontoknak egy kezelési ügyfél felé történő hitelesítésére szolgál.  A fürttanúsítvány egy SSL-tanúsítványt is biztosít a HTTPS-felügyeleti API, valamint a HTTPS protokollt használó Service Fabric Explorer számára. Az önaláírt tanúsítványok a tesztfürtök esetén hasznosak.  Éles fürtök esetén hitelesítésszolgáltatótól (CA) származó tanúsítványt használjon fürttanúsítványként.

A fürttanúsítványnak a következő feltételeknek kell megfelelnie:

* Tartalmaznia kell egy titkos kulcsot.
* A létrehozásának kulcscserét kell szolgálnia, amely exportálható egy személyes információcsere (.pfx) fájlba.
* Rendelkeznie kell tulajdonosnévvel, amely megegyezik a Service Fabric-fürt eléréséhez használt tartománnyal. Erre a megfeleltetésre azért van szükség, hogy a rendszer biztosíthassa az SSL-kapcsolatot a fürt HTTPS-felügyeleti végpontjai és a Service Fabric Explorer számára. Nem szerezhető be SSL-tanúsítvány hitelesítésszolgáltatótól (CA) a .cloudapp.azure.com tartomány számára. Egyéni tartománynevet kell beszereznie a fürt számára. Amikor tanúsítványt igényel egy hitelesítésszolgáltatótól, a tanúsítvány tulajdonosnevének meg kell felelnie a fürthöz használt egyéni tartománynévnek.

Az Azure Key Vault használatával kezelheti a Service Fabric-fürtök tanúsítványait az Azure-ban.  Amikor egy fürtöt üzembe helyez az Azure-ban, Azure Service Fabric-fürtök létrehozásáért felelős erőforrás-szolgáltatója lekéri a tanúsítványokat a Key Vaultból, és telepíti őket a fürt virtuális gépein.

A jelen oktatóanyag egy olyan fürtöt mutat be, amelyben öt csomópont van egyetlen csomóponttípusban. A [kapacitástervezés](service-fabric-cluster-capacity.md) azonban az éles fürttelepítések lényeges lépése. Az alábbiakban néhány dolog láthat, amelyet érdemes a folyamat részeként figyelembe vennie.

* A fürt számára szükséges csomópontok és csomóponttípusok száma.
* Az egyes csomóponttípusok tulajdonságai (például a virtuális gépek mérete, elsődlegessége, internetkapcsolata és száma).
* A fürt megbízhatóságra és tartósságra vonatkozó jellemzői.

## <a name="download-and-explore-the-template"></a>A sablon letöltése és megismerése

Töltse az alábbi Resource Manager-sablonfájlokat:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Ez a sablon egy öt virtuális gépből álló biztonságos fürtöt és egyetlen csomóponttípust helyez üzembe egy virtuális hálózaton és egy hálózati biztonsági csoportban.  További mintasablonokat a [GitHubon](https://github.com/Azure-Samples/service-fabric-cluster-templates) talál.  Az [azuredeploy.json][template] több erőforrást is üzembe helyez, többek között az alábbiakat.

### <a name="service-fabric-cluster"></a>Service Fabric-fürt

A **Microsoft.ServiceFabric/clusters** erőforrásban egy Windows-fürt az alábbi jellemzőkkel lesz konfigurálva:

* egyetlen csomóponttípus;
* öt csomópont az elsődleges csomóponttípusban (a sablon paramétereiben konfigurálható);
* operációs rendszer: Windows Server 2016 Datacenter tárolókkal (a sablon paramétereiben konfigurálható);
* tanúsítványon alapuló védelem (a sablon paramétereiben konfigurálható);
* engedélyezve van a [fordított proxy](service-fabric-reverseproxy.md);
* engedélyezve van a [DNS szolgáltatás](service-fabric-dnsservice.md);
* bronz szintű [tartóssági szint](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) (a sablon paramétereiben konfigurálható);
* ezüst szintű [megbízhatósági szint](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) (a sablon paramétereiben konfigurálható);
* ügyfélkapcsolati végpont: 19000 (a sablon paramétereiben konfigurálható);
* HTTP-átjáró végpontja: 19080 (a sablon paramétereiben konfigurálható).

### <a name="azure-load-balancer"></a>Azure Load Balancer

A **Microsoft.Network/loadBalancers** erőforrásban a rendszer egy terheléselosztót konfigurál, a mintavételeket és szabályokat pedig az alábbi portokra állítja be:

* ügyfélkapcsolati végpont: 19000;
* HTTP-átjáró végpontja: 19080;
* alkalmazásport: 80;
* alkalmazásport: 443;
* Service Fabric fordított proxyja: 19081.

Ha további alkalmazásportokra van szükség, akkor módosítania kell a **Microsoft.Network/loadBalancers** és a **Microsoft.Network/networkSecurityGroups** erőforrást a forgalom beengedésére.

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtuális hálózat, alhálózat és hálózati biztonsági csoport

A virtuális hálózat, az alhálózat és a hálózati biztonsági csoport neve a sablon paramétereiben van meghatározva.  A virtuális hálózat és az alhálózat címtere szintén a sablon paramétereiben határozható meg és a **Microsoft.Network/virtualNetworks** erőforrásban van konfigurálva:

* virtuális hálózat címtere: 172.16.0.0/20,
* Service Fabric-alhálózat címtere: 172.16.2.0/23.

Az alábbi bejövő forgalmi szabályok vannak engedélyezve a **Microsoft.Network/networkSecurityGroups** erőforrásban. A portok értékét a sablon változóinak módosításával módosíthatja.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB : 445
* Csomópontok közötti kommunikáció – 1025, 1026, 1027,
* rövid élettartamú porttartomány – 49152–65534 (legalább 256 port szükséges),
* alkalmazások által használható portok: 80 és 443,
* alkalmazásportok tartománya – 49152–65534 (a szolgáltatások közötti kommunikációra szolgál, és nincs megnyitva a terheléselosztóban),
* összes többi port letiltása.

Ha további alkalmazásportokra van szükség, akkor módosítania kell a **Microsoft.Network/loadBalancers** és a **Microsoft.Network/networkSecurityGroups** erőforrást a forgalom beengedésére.

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

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>A virtuális hálózat és a fürt üzembe helyezése

Ezután állítsa be a hálózati topológiát, és helyezze üzembe a Service Fabric-fürtöt. Az [azuredeploy.json][template] Resource Manager-sablon egy virtuális hálózatot (VNET-et), továbbá egy alhálózatot és egy hálózati biztonsági csoportot (NSG-t) hoz létre a Service Fabric számára. A sablon emellett egy fürtöt is üzembe helyez engedélyezett tanúsítványalapú biztonsággal.  Éles fürtök esetén hitelesítésszolgáltatótól (CA) származó tanúsítványt használjon fürttanúsítványként. A tesztfürtök számára önaláírt tanúsítvánnyal is biztosítható védelem.

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
> * Biztonságos Service Fabric-fürt létrehozása az Azure-ban PowerShell használatával
> * A fürt védelme X.509-tanúsítvánnyal
> * Csatlakozás a fürthöz PowerShell használatával
> * Fürt eltávolítása

Folytassa a következő oktatóanyaggal, amelyben megismerheti a fürtök skálázásának módját.
> [!div class="nextstepaction"]
> [Fürt skálázása](service-fabric-tutorial-scale-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Windows-1-NodeTypes-Secure-NSG/azuredeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Windows-1-NodeTypes-Secure-NSG/azuredeploy.parameters.json
