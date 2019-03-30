---
title: Linux-alapú Service Fabric-fürt létrehozása az Azure-ban | Microsoft Docs
description: Megismerheti, hogyan helyezhet üzembe egy Linux-alapú Service Fabric-fürtöt egy meglévő Azure-beli virtuális hálózatban az Azure CLI használatával.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 00d7e510fa43865f1427092f2f20b9847f1afa9b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661122"
---
# <a name="deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Azure-beli virtuális hálózathoz Linux Service Fabric-fürt üzembe helyezése

Ez a cikk ismerteti a Linux Service Fabric-fürt üzembe helyezése egy [az Azure virtual network (VNET)](../virtual-network/virtual-networks-overview.md) Azure CLI-vel és a egy sablon használatával. Amikor végzett, a felhőben futó fürttel fog rendelkezni, amelyre alkalmazásokat telepíthet. Ha a PowerShell használatával szeretne Windows-fürtöt létrehozni, lásd: [Biztonságos Windows-fürt létrehozása az Azure-ban](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Telepítse a [Service Fabric parancssori felületet](service-fabric-cli.md)
* Telepítse az [Azure CLI-t](/cli/azure/install-azure-cli)
* További információt a fürtök kapcsolatos főbb fogalmakat, [áttekintése az Azure-fürtök](service-fabric-azure-clusters-overview.md)
* [Megtervezheti és előkészítheti a](service-fabric-cluster-azure-deployment-preparation.md) éles fürt üzembe helyezéséhez.

Az alábbi eljárással létrehozunk egy hét csomópontos Service Fabric-fürtön. A Service Fabric-fürtök Azure-ban történő futtatásával járó költségek kiszámításához használja az [Azure-díjkalkulátort](https://azure.microsoft.com/pricing/calculator/).

## <a name="download-and-explore-the-template"></a>A sablon letöltése és megismerése

Töltse az alábbi Resource Manager-sablonfájlokat:

* [AzureDeploy.json][template]
* [AzureDeploy.Parameters.json][parameters]

Ez a sablon üzembe helyez egy virtuális hálózatban egy biztonságos fürt hét virtuális gépek és a három csomópont típusa.  További mintasablonokat a [GitHubon](https://github.com/Azure-Samples/service-fabric-cluster-templates) talál. Az [AzureDeploy.json][template] több erőforrást is üzembe helyez, többek között az alábbiakat.

### <a name="service-fabric-cluster"></a>Service Fabric-fürt

A **Microsoft.ServiceFabric/clusters** erőforrásban a rendszer üzembe helyez egy Linux-fürtöt, amelyet a következők jellemeznek:

* három csomópont típusa
* öt csomópont van az elsődleges csomóponttípusban (a sablon paramétereiben konfigurálható), minden más csomóponttípus egy csomópont
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
|certificateUrlValue|| <p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a tanúsítvány URL-címét. Például "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a forrástároló értékét. For example, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>A virtuális hálózat és a fürt üzembe helyezése

Ezután állítsa be a hálózati topológiát, és helyezze üzembe a Service Fabric-fürtöt. Az [AzureDeploy.json][template] Resource Manager-sablon egy virtuális hálózatot (VNET-et) hoz létre a Service Fabric számára. A sablon emellett egy fürtöt is üzembe helyez engedélyezett tanúsítványalapú biztonsággal.  Éles fürtök esetén hitelesítésszolgáltatótól (CA) származó tanúsítványt használjon fürttanúsítványként. A tesztfürtök számára önaláírt tanúsítvánnyal is biztosítható védelem.

Ez a cikk a sablon üzembe egy fürtöt, amely a tanúsítvány ujjlenyomatát a fürttanúsítvány azonosítására használ.  Nincs két tanúsítványt ugyanazzal az ujjlenyomattal, ami megnehezíti a tanúsítványkezelés is rendelkezhet. Egy tanúsítvány-ujjlenyomatok a tanúsítvány köznapi nevek használatával üzembe helyezett fürt közötti váltás révén jóval egyszerűbb tanúsítványkezelés.  A tanúsítvány köznapi nevek a tanúsítványok kezeléséhez használja a fürt frissítése, olvassa el [fürt módosítása a tanúsítvány köznapi neve felügyeleti](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-using-an-existing-certificate"></a>Fürt létrehozása meglévő tanúsítvány használatával

Az alábbi szkript az [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) parancs és egy sablon használatával helyez üzembe egy meglévő tanúsítvánnyal védett új fürtöt. A parancs emellett egy új kulcstárolót is létrehoz az Azure-ban, és feltölti a tanúsítványt.

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

Az alábbi szkript az [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) parancs és egy sablon használatával helyez üzembe egy új fürtöt az Azure-ban. A parancs is létrehoz egy új kulcstárolót az Azure-ban, hozzáad egy új önaláírt tanúsítványt a key vaultban, és letölti helyben a tanúsítványfájlt.

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

Ha nem azonnal tér rá a következő cikkre, érdemes [törölnie a fürtöt](service-fabric-cluster-delete.md) a felmerülő költségek elkerülése érdekében.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [fürt horizontális fel-](service-fabric-tutorial-scale-cluster.md).

Ez a cikk a sablon üzembe egy fürtöt, amely a tanúsítvány ujjlenyomatát a fürttanúsítvány azonosítására használ.  Nincs két tanúsítványt ugyanazzal az ujjlenyomattal, ami megnehezíti a tanúsítványkezelés is rendelkezhet. Egy tanúsítvány-ujjlenyomatok a tanúsítvány köznapi nevek használatával üzembe helyezett fürt közötti váltás révén jóval egyszerűbb tanúsítványkezelés.  A tanúsítvány köznapi nevek a tanúsítványok kezeléséhez használja a fürt frissítése, olvassa el [fürt módosítása a tanúsítvány köznapi neve felügyeleti](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.Parameters.json
