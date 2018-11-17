---
title: Az Azure Service Fabric önálló fürt konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az önálló vagy a helyi Azure Service Fabric-fürt.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: dekapur
ms.openlocfilehash: c71473e975333d33406d78130ad28f417b9b967e
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853336"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Egy különálló Windows-fürt konfigurációs beállításai
Ez a cikk ismerteti, amely megadható önálló Azure Service Fabric-fürt konfigurációs beállításainak a *ClusterConfig.json* fájlt. Ez a fájl lesz használatával adhatja meg a fürt csomópontjai, biztonsági konfigurációkat, valamint a hálózati topológia hibatűrési és frissítési tartományokba tekintetében.  Miután módosítása, vagy a konfigurációs beállítások hozzáadása, választhatja [önálló fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md) vagy [önálló fürt konfigurációjának frissítése](service-fabric-cluster-config-upgrade-windows-server.md).

Ha Ön [a különálló Service Fabric-csomag letöltése](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), ClusterConfig.json mintákat is szerepelnek. A mintákat, amelyek a nevük "DevCluster" ugyanarra a gépre, logikai csomópontok használatával minden három csomóponttal rendelkező fürt létrehozása. Ezek a csomópontok ki legalább egy kell megjelölni egy elsődleges csomóponthoz. Az ilyen típusú fürt hasznos fejlesztési-tesztelési környezetre. Nem támogatott éles fürtként. A mintákat, amelyek a nevük "MultiMachine" Súgó, termelési szintű fürtök létrehozását, az egyes csomópontok egy külön számítógépen. Az ilyen fürtök elsődleges csomópontok száma alapján a fürt [megbízhatósági szint](#reliability). 5.7, API-verzió 05 – 2017, a kiadásban eltávolítottuk a megbízhatósági szintű tulajdonság. Ehelyett a kód a legtöbb optimalizált megbízhatósági szint a fürthöz tartozó számítja ki. Ne próbálja meg egy értéket ehhez a tulajdonsághoz 5.7-es verzió frissítésétől kezdve.

* ClusterConfig.Unsecure.DevCluster.json és ClusterConfig.Unsecure.MultiMachine.json bemutatják, hogyan hozhat létre egy nem biztonságos, tesztelési vagy éles fürtöt jelölik.

* ClusterConfig.Windows.DevCluster.json és ClusterConfig.Windows.MultiMachine.json bemutatják, hogyan hozhat létre a védett tesztelési és éles fürtök [Windows biztonsági](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.json és ClusterConfig.X509.MultiMachine.json bemutatják, hogyan hozhat létre a védett tesztelési és éles fürtök [X509 ügyféltanúsítvány-alapú biztonsági](service-fabric-windows-cluster-x509-security.md).

Most hozzunk vizsgálja meg a különböző részeit, egy ClusterConfig.json fájlt.

## <a name="general-cluster-configurations"></a>Általános fürtkonfigurációk
Általános fürtkonfigurációk terjed ki a széles körű fürtre jellemző konfiguráció esetén, az alábbi JSON-kódrészletben látható módon:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

A Service Fabric-fürt bármilyen könnyen felismerhető nevet, ha hozzárendeli a name változó segítségével biztosíthat. A clusterConfigurationVersion a fürt verziószáma. Növelje, minden alkalommal, amikor a Service Fabric-fürt frissítése. Az alapértelmezett értéket hagyja az API-verzió beállítása.

## <a name="nodes-on-the-cluster"></a>A fürtben lévő csomópontok
A csomópontok területen az alábbi kódrészletben látható módon használatával konfigurálhatja a csomópontok a Service Fabric-fürtön:
```json
"nodes": [{
    "nodeName": "vm0",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD0"
}, {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType1",
    "faultDomain": "fd:/dc1/r1",
    "upgradeDomain": "UD1"
}, {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType2",
    "faultDomain": "fd:/dc1/r2",
    "upgradeDomain": "UD2"
}],
```

Service Fabric-fürt legalább három csomóponttal kell tartalmaznia. Ebben a szakaszban további csomópontokat adhat a beállításai alapján. A következő táblázat ismerteti az egyes csomópontok konfigurációs beállítások:

| **Csomópont-konfiguráció** | **Leírás** |
| --- | --- |
| Csomópontnév |Bármilyen könnyen felismerhető nevet adhat a csomópontra. |
| IP-cím |Nyissa meg egy parancsablakot, és írja be az IP-cím, a csomópont található `ipconfig`. Tekintse meg az IPV4-címet, és rendelje hozzá az IP-cím változó. |
| nodetyperef hivatkozással |Minden csomóponton egy másik csomópont típusa is hozzárendelhető. A [csomóponttípusok](#node-types) határozzák meg a következő szakaszban. |
| faultDomain |Tartalék tartományok engedélyezze a fürt-rendszergazdák meghatározhatnak, amelyek egy időben közös fizikai függőségek miatt meghiúsulhat a fizikai csomópontokon. |
| upgradeDomain |Frissítési tartományok nagyjából egy időben, a Service Fabric frissítéskezelésének leállítás csomópontok készleteit ismerteti. Melyik csomópontokon hozzárendelni, amelyhez frissítési tartományok is választható, mert nem korlátozza a biztonsági fizikai követelményeit. |

## <a name="cluster-properties"></a>Fürt tulajdonságai
Tulajdonságok szakaszában található a ClusterConfig.json segítségével konfigurálja a fürt látható módon:

### <a name="reliability"></a>Megbízhatóság
ReliabilityLevel fogalma replikák száma vagy a Service Fabric-rendszerszolgáltatások, amely képes futtatni a fürt elsődleges csomóponton példányait határozza meg. Meghatározza, hogy ezek a szolgáltatások megbízhatóságát, és ezért a fürthöz. Az érték alapján számítja ki a rendszer fürt létrehozása és a frissítés ideje.

### <a name="diagnostics"></a>Diagnosztika
A diagnosticsStore szakaszban konfigurálhatja a paraméterekkel engedélyezheti a diagnosztikai és hibaelhárítási csomópont- vagy fürt esetén, az alábbi kódrészletben látható módon: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

A metaadatok leírását, a fürt diagnosztikai és a telepítés megfelelően állíthatja be. Ezeket a változókat súgó gyűjt az ETW-nyomkövetési naplókat, és összeomlási memóriaképek, valamint a teljesítményszámlálókat. ETW-nyomkövetési naplókat a további információkért lásd: [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) és [ETW-nyomkövetés](https://msdn.microsoft.com/library/ms751538.aspx). Minden napló, beleértve a [összeomlási memóriaképek](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) és [teljesítményszámlálók](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx), a gépén a connectionString mappa lehet irányítani. AzureStorage diagnosztikai tárolására is használhatja. Tekintse meg a következő minta kódrészletre:

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "AzureStorage",
    "IsEncrypted": "false",
    "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
}
```

### <a name="security"></a>Biztonság
A biztonsági szakaszban szükség egy biztonságos önálló Service Fabric-fürtön. Az alábbi kódrészlet egy részét ez a szakasz bemutatja:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

A metaadatok leírását, a biztonságos fürthöz, és a telepítés megfelelően állíthatja be. A ClusterCredentialType és ServerCredentialType meghatározni a biztonsági, amelyek a fürt és a csomópontok valósítanak meg. Beállíthatja azokat termékeken *X509* egy tanúsítványalapú biztonsági vagy *Windows* Azure Active Directory-alapú biztonság. A biztonsági szakasz többi biztonság típusától alapul. Töltse ki a biztonsági szakasz többi módjáról további információkért lásd: [tanúsítványok-alapú biztonság egy önálló fürt](service-fabric-windows-cluster-x509-security.md) vagy [Windows biztonsági önálló fürt](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Csomóponttípusok
A NodeType szakasz ismerteti, amely rendelkezik a fürt csomópontjai típusát. Legalább egy csomópont típusa meg kell adni egy fürthöz, az alábbi kódrészletben látható módon: 

```json
"nodeTypes": [{
    "name": "NodeType0",
    "clientConnectionEndpointPort": "19000",
    "clusterConnectionEndpointPort": "19001",
    "leaseDriverEndpointPort": "19002"
    "serviceConnectionEndpointPort": "19003",
    "httpGatewayEndpointPort": "19080",
    "reverseProxyEndpointPort": "19081",
    "applicationPorts": {
        "startPort": "20575",
        "endPort": "20605"
    },
    "ephemeralPorts": {
        "startPort": "20606",
        "endPort": "20861"
    },
    "isPrimary": true
}]
```

A név az adott csomóponttípus rövid nevét. A csomóponttípusok csomópontot hozhat létre, a rövid nevet a csomóponton, a nodetyperef hivatkozással változó hozzárendelés másként [korábban említett](#nodes-on-the-cluster). Minden egyes csomópont típusa határozza meg a használt kapcsolati végpontok. Kiválaszthatja az ezen kapcsolati végpontok bármilyen portszám mindaddig, amíg azok nem ütköznek a bármely más végpontok a fürtben. A többcsomópontos fürt egy vagy több elsődleges csomóponthoz vannak (azaz isPrimary értékre van állítva *igaz*), attól függően, a [reliabilityLevel](#reliability). Elsődleges és a nonprimary csomóponttípusok kapcsolatos további információkért lásd: [tervezési megfontolások a Service Fabric-fürt kapacitása](service-fabric-cluster-capacity.md) NodeType és reliabilityLevel kapcsolatos információkat. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>A csomóponttípusok konfigurálása használt végpontokat
* clientConnectionEndpointPort csatlakozhat a fürthöz, amikor az ügyfél API-kat használják az ügyfél által használt port. 
* clusterConnectionEndpointPort, a portot, amelyen a csomópontok kommunikálni egymással.
* leaseDriverEndpointPort ismerje meg, ha a csomópont még aktívak a fürt bérleti illesztőprogram által használt port. 
* serviceConnectionEndpointPort az adott csomóponton a Service Fabric ügyfél kommunikálni az alkalmazások és a egy csomóponton telepített szolgáltatások által használt portot.
* httpGatewayEndpointPort csatlakozni a fürthöz a Service Fabric Explorer által használt port.
* az ephemeralPorts bírálja felül a [az operációs rendszer által használt dinamikus portok](https://support.microsoft.com/kb/929851). A Service Fabric egy része ezeket a portokat használja, mint alkalmazásportok, és a fennmaradó érhetők el az operációs rendszer. Emellett vannak leképezve ezt a tartományt a meglévő tartomány szerepel az operációs rendszer, így minden célra használhatja az adott példa JSON-fájljaiban szereplő tartományok. Győződjön meg róla, hogy a kezdő és záró portok közötti különbség legalább 255. Ha ezt a különbséget túl alacsony, mivel ez a tartomány meg van osztva az operációs rendszerrel való ütközések futhatnak. A konfigurált dinamikus porttartomány megtekintéséhez futtassa `netsh int ipv4 show dynamicport tcp`.
* az applicationPorts a Service Fabric-alkalmazások által használt portok. Az alkalmazás porttartományából elég nagynak kell lennie ahhoz, hogy biztosítsák a végpont követelmény az alkalmazások. Ebben a tartományban kell lennie a gépen, ahogyan az a konfiguráció az ephemeralPorts tartomány a dinamikus porttartomány a kizárólagos. A Service Fabric ezeket a portokat használja, amikor új portok használata kötelező, és nyissa meg ezeket a portokat a tűzfalán gondoskodik. 
* reverseProxyEndpointPort egy nem kötelező a fordított proxy végpontot. További információkért lásd: [Service Fabric fordított proxyja](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Naplózási beállítások
A fabricSettings szakaszban beállíthatja a gyökérkönyvtárak a Service Fabric-adatok és a naplókat. Testre szabhatja, hogy ezek a könyvtárak csak a kezdeti fürt létrehozása során. Ez a szakasz az alábbi minta kódrészletet lásd:

```json
"fabricSettings": [{
    "name": "Setup",
    "parameters": [{
        "name": "FabricDataRoot",
        "value": "C:\\ProgramData\\SF"
    }, {
        "name": "FabricLogRoot",
        "value": "C:\\ProgramData\\SF\\Log"
}]
```

Azt javasoljuk, hogy a FabricDataRoot és FabricLogRoot használja egy nem operációsrendszer-meghajtón. Biztosít további megbízhatóság a helyzetek elkerülése, amikor az operációs rendszer nem válaszol. Ha csak az adatok legfelső szintű szabja testre, a napló legfelső szintű kerül az adatok legfelső szintű alatt egy szinttel.

### <a name="stateful-reliable-services-settings"></a>Állapotalapú Reliable Services-beállítások
A KtlLogger szakaszban beállíthatja a Reliable Services globális konfigurációs beállításait. Ezek a beállítások további információkért lásd: [Stateful Reliable Services konfigurálása](service-fabric-reliable-services-configuration.md). Az alábbi példa bemutatja, hogyan módosíthatja a megosztott tranzakciós napló, amely minden, az állapotalapú szolgáltatások esetében a reliable collections biztonsági jön létre:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Bővítményfunkciók
Kiegészítő funkciók konfigurálásához az API-verzió, 2017. 04. vagy újabb, valamint konfigurálni a addonFeatures itt látható módon:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```

### <a name="container-support"></a>Tárolótámogatás
Ahhoz, hogy a tároló támogatja a Windows Server-tárolók és az önálló fürtök Hyper-V-tárolók, a nincs kiegészítő funkciót engedélyezni kell.

## <a name="next-steps"></a>További lépések
Ha már van egy teljes *ClusterConfig.json* az önálló fürt beállítása megfelelően konfigurálta-e a fájl telepítheti a fürtben. Kövesse a [önálló Service Fabric-fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md). 

Ha egy önálló fürtön üzembe helyezett, is [önálló fürt konfigurációjának frissítése](service-fabric-cluster-config-upgrade-windows-server.md). 

Ismerje meg, hogyan [a fürt megjelenítése a Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

