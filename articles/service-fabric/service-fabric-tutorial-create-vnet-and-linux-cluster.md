---
title: Linux-alapú Service Fabric-fürt létrehozása az Azure-ban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy Linux-alapú Service Fabric-fürtöt egy meglévő Azure-beli virtuális hálózatban az Azure CLI használatával.
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
ms.openlocfilehash: b494da1c87feafd1b9db8485d16a9dcf5b999e3d
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55101805"
---
# <a name="tutorial-deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Oktatóanyag: Azure-beli virtuális hálózathoz Linux Service Fabric-fürt üzembe helyezése

Ez az oktatóanyag egy sorozat első része. Megismerheti, hogyan helyezhet üzembe egy Linux rendszert futtató Service Fabric-fürtöt egy [Azure-beli virtuális hálózaton (VNET)](../virtual-network/virtual-networks-overview.md) az Azure CLI és egy sablon használatával. Amikor végzett, a felhőben futó fürttel fog rendelkezni, amelyre alkalmazásokat telepíthet. Ha a PowerShell használatával szeretne Windows-fürtöt létrehozni, lásd: [Biztonságos Windows-fürt létrehozása az Azure-ban](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása az Azure CLI használatával
> * Biztonságos Service Fabric-fürt létrehozása az Azure-ban az Azure CLI használatával
> * A fürt védelme X.509-tanúsítvánnyal
> * Csatlakozás a fürthöz Service Fabric parancssori felületével
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
* Telepítse a [Service Fabric parancssori felületet](service-fabric-cli.md)
* Telepítse az [Azure CLI-t](/cli/azure/install-azure-cli)

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

Ez az oktatóanyag egy olyan fürtöt helyez üzembe, amelyben öt csomópont van egyetlen csomóponttípusban. A [kapacitástervezés](service-fabric-cluster-capacity.md) azonban az éles fürttelepítések lényeges lépése. Az alábbiakban néhány dolog láthat, amelyet érdemes a folyamat részeként figyelembe vennie.

* A fürt számára szükséges csomópontok és csomóponttípusok száma.
* Az egyes csomóponttípusok tulajdonságai (például a virtuális gépek mérete, elsődlegessége, internetkapcsolata és száma).
* A fürt megbízhatóságra és tartósságra vonatkozó jellemzői.

## <a name="download-and-explore-the-template"></a>A sablon letöltése és megismerése

Töltse az alábbi Resource Manager-sablonfájlokat:

* [AzureDeploy.json][template]
* [AzureDeploy.Parameters.json][parameters]

Ez a sablon egy öt virtuális gépből álló biztonságos fürtöt és egyetlen csomóponttípust helyez üzembe egy virtuális hálózaton.  További mintasablonokat a [GitHubon](https://github.com/Azure-Samples/service-fabric-cluster-templates) talál. Az [AzureDeploy.json][template] több erőforrást is üzembe helyez, többek között az alábbiakat.

### <a name="service-fabric-cluster"></a>Service Fabric-fürt

A **Microsoft.ServiceFabric/clusters** erőforrásban a rendszer üzembe helyez egy Linux-fürtöt, amelyet a következők jellemeznek:

* egyetlen csomóponttípus;
* öt csomópont az elsődleges csomóponttípusban (a sablon paramétereiben konfigurálható);
* Operációs rendszer: Ubuntu 16.04 LTS (a sablon paramétereiben konfigurálható)
* tanúsítványon alapuló védelem (a sablon paramétereiben konfigurálható);
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

### <a name="virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat

A virtuális hálózat és az alhálózat neve a sablon paramétereiben határozható meg.  A virtuális hálózat és az alhálózat címtere szintén a sablon paramétereiben határozható meg és a **Microsoft.Network/virtualNetworks** erőforrásban van konfigurálva:

* virtuális hálózat címtere: 10.0.0.0/16
* A Service Fabric-alhálózat címtere: 10.0.2.0/24

Ha további alkalmazásportokra van szükség, akkor módosítania kell a Microsoft.Network/loadBalancers erőforrást a forgalom beengedésére.

## <a name="set-template-parameters"></a>Sablon paramétereinek megadása

Az [AzureDeploy.Parameters][parameters] paraméterfájl számos, a fürt és a társított erőforrások üzembe helyezéséhez használt értéket meghatároz. Néhány paraméter, amelyeket lehet, hogy módosítani kell az üzembe helyezéshez:

|Paraméter|Példaérték|Megjegyzések|
|---|---||
|adminUserName|vmadmin| Rendszergazdai felhasználónév a fürt virtuális gépeihez. |
|adminPassword|Password#1234| Rendszergazdai jelszó a fürt virtuális gépeihez.|
|clusterName|mysfcluster123| A fürt neve. |
|location|southcentralus| A fürt helye. |
|certificateThumbprint|| <p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a tanúsítvány SHA1 ujjlenyomatának értékét. Például: „6190390162C988701DB5676EB81083EA608DCCF3” </p>|
|certificateUrlValue|| <p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a tanúsítvány URL-címét. Például: „https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346”.</p>|
|sourceVaultValue||<p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a forrástároló értékét. For example, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>A virtuális hálózat és a fürt üzembe helyezése

Ezután állítsa be a hálózati topológiát, és helyezze üzembe a Service Fabric-fürtöt. Az [AzureDeploy.json][template] Resource Manager-sablon egy virtuális hálózatot (VNET-et) hoz létre a Service Fabric számára. A sablon emellett egy fürtöt is üzembe helyez engedélyezett tanúsítványalapú biztonsággal.  Éles fürtök esetén hitelesítésszolgáltatótól (CA) származó tanúsítványt használjon fürttanúsítványként. A tesztfürtök számára önaláírt tanúsítvánnyal is biztosítható védelem.

### <a name="create-a-cluster-using-an-existing-certificate"></a>Fürt létrehozása meglévő tanúsítvány használatával

Az alábbi szkript az [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) parancs és egy sablon használatával helyez üzembe egy meglévő tanúsítvánnyal védett új fürtöt. A parancs emellett egy új kulcstárolót is létrehoz az Azure-ban, és feltölti a tanúsítványt.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Fürt létrehozása új, önaláírt tanúsítvány használatával

Az alábbi szkript az [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) parancs és egy sablon használatával helyez üzembe egy új fürtöt az Azure-ban. A parancs is létrehoz egy új kulcstárolót az Azure-ban, hozzáad egy új önaláírt tanúsítványt a key vaultban, és letölti helyben a tanúsítványfájlt.

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>Csatlakozás a biztonságos fürthöz

Csatlakozzon a fürthöz a Service Fabric parancssori felület `sfctl cluster select` parancsát használva a kulcsával.  A **--no-verify** paramétert csak önaláírt tanúsítvány esetén használja.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Az `sfctl cluster health` parancs futtatásával ellenőrizze, hogy csatlakozik-e, és hogy a fürt állapota kifogástalan-e.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A jelen oktatóanyag-sorozatban található további cikkek a létrehozott fürtöt fogják felhasználni. Ha nem azonnal tér rá a következő cikkre, érdemes [törölnie a fürtöt](service-fabric-cluster-delete.md) a felmerülő költségek elkerülése érdekében.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása az Azure CLI használatával
> * Biztonságos Service Fabric-fürt létrehozása az Azure-ban az Azure CLI használatával
> * A fürt védelme X.509-tanúsítvánnyal
> * Csatlakozás a fürthöz Service Fabric parancssori felületével
> * Fürt eltávolítása

Folytassa a következő oktatóanyaggal, amelyben megismerheti a fürtök skálázásának módját.
> [!div class="nextstepaction"]
> [Fürt skálázása](service-fabric-tutorial-scale-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Ubuntu-1-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Ubuntu-1-NodeTypes-Secure/AzureDeploy.Parameters.json
