---
title: "Linux-alapú Service Fabric-fürt létrehozása az Azure-ban | Microsoft Docs"
description: "Megismerheti, hogyan helyezhet üzembe egy Linux-alapú Service Fabric-fürtöt egy meglévő Azure-beli virtuális hálózatban az Azure CLI használatával."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/22/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 3b09e676a26336d1ef1e744f9e45066c4815fe21
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>Linux-alapú Service Fabric-fürt üzembe helyezése egy Azure-beli virtuális hálózatban
Ez az oktatóanyag egy sorozat első része. Megismerheti, hogyan helyezhet üzembe egy Linux rendszert futtató Service Fabric-fürtöt egy [Azure-beli virtuális hálózaton (VNET)](../virtual-network/virtual-networks-overview.md) és [hálózati biztonsági csoportban (NSG)](../virtual-network/virtual-networks-nsg.md) az Azure CLI és egy sablon használatával. Amikor végzett, a felhőben futó fürttel fog rendelkezni, amelyre alkalmazásokat telepíthet. Ha a PowerShell használatával szeretne Windows-fürtöt létrehozni, lásd: [Biztonságos Windows-fürt létrehozása az Azure-ban](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

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
> * [Az API Management üzembe helyezése a Service Fabrickel](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdése előtt:
- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Telepítse a [Service Fabric parancssori felületet](service-fabric-cli.md)
- Telepítse az [Azure CLI 2.0-t](/cli/azure/install-azure-cli)

Az alábbi eljárások egy ötcsomópontos Service Fabric-fürtöt hoznak létre. A Service Fabric-fürtök Azure-ban történő futtatásával járó költségek kiszámításához használja az [Azure-díjkalkulátort](https://azure.microsoft.com/pricing/calculator/).

## <a name="key-concepts"></a>Fő fogalmak
A [Service Fabric-fürt](service-fabric-deploy-anywhere.md) virtuális és fizikai gépek hálózaton keresztül csatlakozó készlete, amelyen mikroszolgáltatásokat helyezhet üzembe és felügyelhet. A fürtök több ezer gépre skálázhatók. A fürtök részét képező gépeket vagy virtuális gépeket csomópontoknak nevezzük. Minden csomóponthoz hozzá van rendelve egy csomópontnév (egy karakterlánc). A csomópontok jellemzőkkel, például elhelyezési tulajdonságokkal rendelkeznek.

A csomópont típusa meghatározza a fürt egyes virtuálisgép-készleteinek méretét, számát és tulajdonságait. Minden megadott csomóponttípus [virtuálisgép-méretezési csoportként](/azure/virtual-machine-scale-sets/) lesz beállítva, amely egy, a virtuális gépek gyűjteményének csoportként való üzembe helyezésére és felügyeletére használható Azure számítási erőforrás. Ezután mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy fel, eltérő nyitott portokkal rendelkezhet, és eltérő kapacitásmetrikái lehetnek. A csomóponttípusok a fürtcsomópontcsoportok szerepkörének (például „előtér” vagy „háttér”) meghatározására szolgálnak.  A fürt több csomóponttípussal is rendelkezhet, de éles fürtök esetében az elsődleges csomóponttípusnak legalább öt (vagy tesztfürtök esetében legalább három) virtuális géppel kell rendelkeznie.  [A Service Fabric-rendszerszolgáltatások](service-fabric-technical-overview.md#system-services) elhelyezése az elsődleges csomóponttípusra történik.

A fürtöt egy fürttanúsítvány védi. A fürttanúsítvány egy X.509-tanúsítvány, amely a csomópontok közötti kommunikáció biztonságossá tételére, valamint a fürtkezelési végpontoknak egy kezelési ügyfél felé történő hitelesítésére szolgál.  A fürttanúsítvány egy SSL-tanúsítványt is biztosít a HTTPS-felügyeleti API, valamint a HTTPS protokollt használó Service Fabric Explorer számára. Az önaláírt tanúsítványok a tesztfürtök esetén hasznosak.  Éles fürtök esetén hitelesítésszolgáltatótól (CA) származó tanúsítványt használjon fürttanúsítványként.

A fürttanúsítványnak a következő feltételeknek kell megfelelnie:

- Tartalmaznia kell egy titkos kulcsot.
- A létrehozásának kulcscserét kell szolgálnia, amely exportálható egy személyes információcsere (.pfx) fájlba.
- Rendelkeznie kell tulajdonosnévvel, amely megegyezik a Service Fabric-fürt eléréséhez használt tartománnyal. Erre a megfeleltetésre azért van szükség, hogy a rendszer biztosíthassa az SSL-kapcsolatot a fürt HTTPS-felügyeleti végpontjai és a Service Fabric Explorer számára. Nem szerezhető be SSL-tanúsítvány hitelesítésszolgáltatótól (CA) a .cloudapp.azure.com tartomány számára. Egyéni tartománynevet kell beszereznie a fürt számára. Amikor tanúsítványt igényel egy hitelesítésszolgáltatótól, a tanúsítvány tulajdonosnevének meg kell felelnie a fürthöz használt egyéni tartománynévnek.

Az Azure Key Vault használatával kezelheti a Service Fabric-fürtök tanúsítványait az Azure-ban.  Amikor egy fürtöt üzembe helyez az Azure-ban, Azure Service Fabric-fürtök létrehozásáért felelős erőforrás-szolgáltatója lekéri a tanúsítványokat a Key Vaultból, és telepíti őket a fürt virtuális gépein.

A jelen oktatóanyag olyan fürtöt helyez üzembe, amelyben öt csomópont van egyetlen csomóponttípusban. A [kapacitástervezés](service-fabric-cluster-capacity.md) azonban az éles fürttelepítések lényeges lépése. Az alábbiakban néhány dolog láthat, amelyet érdemes a folyamat részeként figyelembe vennie.

- A fürt számára szükséges csomópontok és csomóponttípusok száma. 
- Az egyes csomóponttípusok tulajdonságai (például a virtuális gépek mérete, elsődlegessége, internetkapcsolata és száma).
- A fürt megbízhatóságra és tartósságra vonatkozó jellemzői.

## <a name="download-and-explore-the-template"></a>A sablon letöltése és megismerése
Töltse az alábbi Resource Manager-sablonfájlokat:
- [vnet-linuxcluster.json][template]
- [vnet-linuxcluster.parameters.json][parameters]

A [vnet-linuxcluster.json][template] több erőforrást is üzembe helyez, többek között az alábbiakat.

### <a name="service-fabric-cluster"></a>Service Fabric-fürt
A rendszer üzembe helyez egy Linux-fürtöt, amely a következő jellemzőkkel rendelkezik:
- egyetlen csomóponttípus; 
- öt csomópont az elsődleges csomóponttípusban (a sablon paramétereiben konfigurálható);
- Ubuntu 16.04 LTS operációs rendszer (a sablon paramétereiben konfigurálható);
- tanúsítványon alapuló védelem (a sablon paramétereiben konfigurálható);
- engedélyezve van a [DNS szolgáltatás](service-fabric-dnsservice.md);
- bronz szintű [tartóssági szint](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) (a sablon paramétereiben konfigurálható);
- ezüst szintű [megbízhatósági szint](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) (a sablon paramétereiben konfigurálható);
- ügyfélkapcsolati végpont: 19000 (a sablon paramétereiben konfigurálható);
- HTTP-átjáró végpontja: 19080 (a sablon paramétereiben konfigurálható).

### <a name="azure-load-balancer"></a>Azure Load Balancer
A rendszer egy terheléselosztót helyez üzembe, a mintavételeket és szabályokat pedig az alábbi portokra állítja be:
- ügyfélkapcsolati végpont: 19000;
- HTTP-átjáró végpontja: 19080; 
- alkalmazásport: 80;
- alkalmazásport: 443;

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtuális hálózat, alhálózat és hálózati biztonsági csoport
A virtuális hálózat, az alhálózat és a hálózati biztonsági csoport neve a sablon paramétereiben van meghatározva.  A virtuális hálózat és az alhálózat címtere szintén a sablon paramétereiben határozható meg:
- virtuális hálózat címtere: 10.0.0.0/16
- Service Fabric-alhálózat címtere: 10.0.2.0/24

Az alábbi bejövő forgalmi szabályok vannak engedélyezve a hálózati biztonsági csoportban. A portok értékét a sablon változóinak módosításával módosíthatja.
- ClientConnectionEndpoint (TCP): 19000
- HttpGatewayEndpoint (HTTP/TCP): 19080
- SMB : 445
- Csomópontok közötti kommunikáció – 1025, 1026, 1027,
- rövid élettartamú porttartomány – 49152–65534 (legalább 256 port szükséges),
- alkalmazások által használható portok: 80 és 443,
- alkalmazásportok tartománya – 49152–65534 (a szolgáltatások közötti kommunikációra szolgál, és nincs megnyitva a terheléselosztóban),
- összes többi port letiltása.

Ha további alkalmazásportokra van szükség, akkor módosítania kell a Microsoft.Network/loadBalancers és a Microsoft.Network/networkSecurityGroups erőforrást a forgalom beengedésére.

## <a name="set-template-parameters"></a>Sablon paramétereinek megadása
A [vnet-cluster.parameters.json][parameters] paraméterfájl számos, a fürt és a társított erőforrások üzembe helyezéséhez használt értéket meghatároz. Néhány paraméter, amelyeket lehet, hogy módosítani kell az üzembe helyezéshez:

|Paraméter|Példaérték|Megjegyzések|
|---|---||
|adminUserName|vmadmin| Rendszergazdai felhasználónév a fürt virtuális gépeihez. |
|adminPassword|Password#1234| Rendszergazdai jelszó a fürt virtuális gépeihez.|
|clusterName|mysfcluster123| A fürt neve. |
|location|southcentralus| A fürt helye. |
|certificateThumbprint|| <p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a tanúsítvány ujjlenyomatának értékét. Például: „6190390162C988701DB5676EB81083EA608DCCF3” </p>| 
|certificateUrlValue|| <p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a tanúsítvány URL-címét. For example, "https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a forrástároló értékét. For example, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|


<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>A virtuális hálózat és a fürt üzembe helyezése
Ezután állítsa be a hálózati topológiát, és helyezze üzembe a Service Fabric-fürtöt. A [vnet-linuxcluster.json][template] Resource Manager-sablon egy virtuális hálózatot (VNET-et), továbbá egy alhálózatot és egy hálózati biztonsági csoportot (NSG-t) hoz létre a Service Fabric számára. A sablon emellett egy fürtöt is üzembe helyez engedélyezett tanúsítványalapú biztonsággal.  Éles fürtök esetén hitelesítésszolgáltatótól (CA) származó tanúsítványt használjon fürttanúsítványként. A tesztfürtök számára önaláírt tanúsítvánnyal is biztosítható védelem.

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
   --template-file vnet-linuxcluster.json --parameter-file vnet-linuxcluster.parameters.json
```

## <a name="connect-to-the-secure-cluster"></a>Csatlakozás a biztonságos fürthöz
Csatlakozzon a fürthöz a Service Fabric parancssori felület `sfctl cluster select` parancsával a kulccsal.  A **--no-verify** paramétert csak önaláírt tanúsítvány esetén használja.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Az `sfctl cluster health` parancs futtatásával ellenőrizze, hogy csatlakozik-e, és hogy a fürt állapota kifogástalan-e.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A jelen oktatóanyag-sorozatban található további cikkek a létrehozott fürtöt fogják felhasználni. Ha nem azonnal tér rá a következő cikkre, érdemes törölnie a fürtöt a felmerülő költségek elkerülése érdekében. A fürt és az összes általa használt erőforrás törlésének legegyszerűbb módja az erőforráscsoport törlése.

Jelentkezzen be az Azure-ba, és válassza ki azon előfizetés azonosítóját, amelyikkel el szeretné távolítani a fürtöt.  Az [Azure Portalra](http://portal.azure.com) bejelentkezve keresheti meg az előfizetés azonosítóját. A fürt és az összes általa használt erőforrás az [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete) paranccsal törölhető.

```azurecli
az group delete --name $ResourceGroupName
```

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


[template]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.json
[parameters]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.parameters.json
