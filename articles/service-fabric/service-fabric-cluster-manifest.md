---
title: Az Azure Service Fabric önálló fürtjének konfigurálása
description: Ismerje meg, hogyan konfigurálhatja az önálló vagy helyszíni Azure Service Fabric-fürt.
author: dkkapur
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: dekapur
ms.openlocfilehash: 0f9b625dfbe9c39bea7771dcc5fd58805ce19811
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458373"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Önálló Windows-fürt konfigurációs beállításai
Ez a cikk egy önálló Azure Service Fabric-fürt konfigurációs beállításait ismerteti, amely a *ClusterConfig.json* fájlban állítható be. Ezzel a fájllal adhat meg információkat a fürt csomópontjairól, biztonsági konfigurációiról, valamint a hálózati topológiáról a hiba- és frissítési tartományok tekintetében.  A konfigurációs beállítások módosítása vagy hozzáadása után [létrehozhat egy önálló fürtöt,](service-fabric-cluster-creation-for-windows-server.md) vagy [frissítheti egy önálló fürt konfigurációját.](service-fabric-cluster-config-upgrade-windows-server.md)

Az [önálló Service Fabric-csomag letöltésekor a](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)ClusterConfig.json minták is szerepelnek. A minták, amelyek "DevCluster" a nevükben hozzon létre egy fürtöt mindhárom csomópont ugyanazon a gépen, logikai csomópontok használatával. Ezek közül a csomópontok közül legalább egyet elsődleges csomópontként kell megjelölni. Ez a fürttípus fejlesztési vagy tesztelési környezetekben hasznos. Éles fürtként nem támogatott. A minták, amelyek "MultiMachine" a nevükben segít létrehozni éles minőségű fürtök, minden csomópont egy külön gépen. A fürtök elsődleges csomópontjainak száma a fürt [megbízhatósági szintjétől](#reliability)függ. Az 5.7-es kiadás05-2017-es verziójában eltávolítottuk a megbízhatósági szintű tulajdonságot. Ehelyett kódunk kiszámítja a fürtlegoptimalizált megbízhatósági szintjét. Ne próbálja meg beállítani a tulajdonság értékét az 5.7-es verziótól kezdve.

* ClusterConfig.Unsecure.DevCluster.json és ClusterConfig.Unsecure.MultiMachine.json megmutatja, hogyan hozhat létre egy nem biztonságos teszt vagy éles fürt, illetve.

* A ClusterConfig.Windows.DevCluster.json és a ClusterConfig.Windows.MultiMachine.json bemutatja, hogyan hozhat létre a [Windows biztonságával](service-fabric-windows-cluster-windows-security.md)védett teszt- vagy éles fürtöket.

* A ClusterConfig.X509.DevCluster.json és a ClusterConfig.X509.MultiMachine.json bemutatja, hogyan hozhat létre [x509 tanúsítványalapú biztonsági](service-fabric-windows-cluster-x509-security.md)rendszerrel védett teszt- vagy élesfürtöket.

Most vizsgáljuk meg a ClusterConfig.json fájl különböző szakaszait.

## <a name="general-cluster-configurations"></a>Általános fürtkonfigurációk
Az általános fürtkonfigurációk a széles fürtspecifikus konfigurációkra vonatkoznak, amint azt a következő JSON-kódrészlet mutatja:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Bármilyen rövid nevet adhat a Service Fabric-fürtnek, ha hozzárendeli a névváltozóhoz. A clusterConfigurationVersion a fürt verziószáma. Növelje a Service Fabric-fürt minden frissítésekor. Hagyja az apiVersion értéket az alapértelmezett értékre állítva.

## <a name="nodes-on-the-cluster"></a>Csomópontok a fürtön
A Service Fabric-fürt csomópontjait a csomópontok szakasz használatával konfigurálhatja, ahogy azt a következő kódrészlet mutatja:
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

A Service Fabric-fürtnek legalább három csomópontot kell tartalmaznia. A beállításnak megfelelően további csomópontokat adhat hozzá ehhez a szakaszhoz. Az alábbi táblázat az egyes csomópontok konfigurációs beállításait ismerteti:

| **Csomópont-konfiguráció** | **Leírás** |
| --- | --- |
| csomópontneve |Bármilyen rövid nevet megadhat a csomópontnak. |
| iPAddress cím |A csomópont IP-címét a parancsablak megnyitásával és `ipconfig`a beírással tudja meg. Jegyezze fel az IPV4-címet, és rendelje hozzá az iPAddress változóhoz. |
| nodeTypeRef |Minden csomóponthoz különböző csomóponttípus rendelhető. A [csomóponttípusoka](#node-types) a következő szakaszban van definiálva. |
| faultDomain |A tartalék tartományok lehetővé teszik a fürtrendszergazdák számára, hogy meghatározzák azokat a fizikai csomópontokat, amelyek a megosztott fizikai függőségek miatt egyidejűleg sikertelenek lehetnek. |
| upgradeDomain |A frissítési tartományok a Service Fabric-frissítések hez körülbelül ugyanabban az időben leállított csomópontok készleteit ismertetik. Kiválaszthatja, hogy mely csomópontokhoz mely frissítési tartományokhoz rendeljen, mivel azokat nem korlátozzák semmilyen fizikai követelmény. |

## <a name="cluster-properties"></a>Fürt tulajdonságai
A ClusterConfig.json tulajdonságszakasza a fürt konfigurálására szolgál az alábbiak szerint:

### <a name="reliability"></a>Megbízhatóság
A megbízhatósági szint fogalma határozza meg a fürt elsődleges csomópontjain futtatható Service Fabric rendszerszolgáltatások replikáinak vagy példányainak számát. Ez határozza meg ezeknek a szolgáltatásoknak a megbízhatóságát, és így a fürtöt. Az értéket a rendszer a fürt létrehozási és frissítési idejével számítja ki.

### <a name="diagnostics"></a>Diagnosztika
A DiagnosticsStore szakaszban konfigurálhatja a paramétereket a diagnosztikai és hibaelhárítási csomópontok vagy fürthibák engedélyezéséhez, ahogy az a következő kódrészletben látható: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

A metaadatok a fürt diagnosztikájának leírása, és a beállításoknak megfelelően állíthatók be. Ezek a változók segítenek az ETW nyomkövetési naplók és összeomlási memóriaképek, valamint a teljesítményszámlálók összegyűjtésében. Az ETW nyomkövetési naplóiról további információt a [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) és [az ETW nyomkövetési témakörben talál.](https://msdn.microsoft.com/library/ms751538.aspx) Minden napló, beleértve [az összeomlási memóriaképeket](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) és [a teljesítményszámlálókat](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx)is, a számítógépen lévő connectionString mappába irányítható. Az AzureStorage-t is használhatja a diagnosztika tárolására. Lásd a következő mintarészletet:

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
A biztonsági szakasz egy biztonságos önálló Service Fabric-fürthöz szükséges. A következő kódrészlet a szakasz egy részét mutatja be:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

A metaadatok a biztonságos fürt leírása, és a beállításoknak megfelelően állíthatók be. A ClusterCredentialType és a ServerCredentialType határozza meg a fürt és a csomópontok által megvalósított biztonság típusát. *Ezek x509-re* állíthatók a tanúsítványalapú biztonság, illetve *a Windows* az Active Directory alapú biztonság érdekében. A biztonsági szakasz többi része a biztonság típusától függ. A biztonsági szakasz többi részének kitöltéséről a [Tanúsítványok alapú biztonság önálló fürtben](service-fabric-windows-cluster-x509-security.md) vagy a Windows [biztonsága önálló fürtben című témakörben](service-fabric-windows-cluster-windows-security.md)talál további információt.

### <a name="node-types"></a>Csomóponttípusok
A nodeTypes szakasz a fürt által birtokolt csomópontok típusát ismerteti. A fürthöz legalább egy csomóponttípust meg kell adni, ahogy az a következő kódrészletben látható: 

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

A név ennek a csomóponttípusnak a rövid neve. Ennek a csomóponttípusnak a csomóponttípuslétrehozásához rendelje hozzá rövid nevét az adott csomópont nodeTypeRef változójához, ahogy [azt korábban említettük.](#nodes-on-the-cluster) Minden csomóponttípushoz adja meg a használt kapcsolatvégpontokat. Ezekhez a csatlakozási végpontokhoz bármilyen portszámot választhat, feltéve, hogy azok nem ütköznek a fürt többi végpontjával. Egy többnokfürtben egy vagy több elsődleges csomópont van (azaz az isPrimary értéke *igaz),* a [megbízhatóságszinttől](#reliability)függően. Az elsődleges és nem elsődleges csomóponttípusokról a [Service Fabric-fürt kapacitástervezési szempontjaia](service-fabric-cluster-capacity.md) a nodeTypes és a reliabilityLevel témakörben olvashat. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>A csomóponttípusok konfigurálásához használt végpontok
* Az ügyfélconnectionendpointport az a port, amelyet az ügyfél a fürthöz való csatlakozáshoz használ, ha ügyfél API-kat használ. 
* A clusterConnectionPointPort az a port, ahol a csomópontok kommunikálnak egymással.
* a leaseDriverEndpointPort a fürtbérlő által használt port, amely ből kiderül, hogy a csomópontok még aktívak-e. 
* serviceConnectionEndpointPort az a port, amelyet a csomóponton üzembe helyezett alkalmazások és szolgáltatások használnak az adott csomópont Service Fabric-ügyféllel való kommunikációhoz.
* A httpGatewayEndpointPort a Service Fabric Explorer által a fürthöz való csatlakozáshoz használt port.
* az ideiglenes portok felülbírálják [az operációs rendszer által használt dinamikus portokat.](https://support.microsoft.com/kb/929851) A Service Fabric ezeknek a portoknak egy részét használja alkalmazásportként, a fennmaradó pedig elérhető az operációs rendszer számára. Azt is leképezi ezt a tartományt a meglévő tartományban jelen van az operációs rendszer, így minden célra, használhatja a megadott tartományok a minta JSON fájlokat. Győződjön meg arról, hogy a kezdő és a záró port közötti különbség legalább 255. Ütközések léphetnek be, ha ez a különbség túl alacsony, mert ez a tartomány meg van osztva az operációs rendszerrel. A beállított dinamikus porttartomány `netsh int ipv4 show dynamicport tcp`megtekintéséhez futtassa a futtassa a futtassa a futtassa a futtassa a futtassa a fut
* applicationPorts a portok által használt Service Fabric-alkalmazások. Az alkalmazásport tartományának elég nagynak kell lennie ahhoz, hogy fedezze az alkalmazások végpontkövetelményét. Ennek a tartománynak kizárólagosnak kell lennie a készülék dinamikus porttartományából, azaz a konfigurációban beállított efemerportok tartományból. A Service Fabric ezeket a portokat használja, amikor új portokra van szükség, és gondoskodik a tűzfal megnyitásáról ezekhez a portokhoz. 
* reverseProxyEndpointPort egy választható fordított proxy végpont. További információ: [Service Fabric reverse proxy](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Naplózási beállítások
A fabricSettings szakaszban beállíthatja a root könyvtárakat a Service Fabric-adatok és naplók. Ezeket a könyvtárakat csak a kezdeti fürt létrehozása során szabhatja testre. Lásd a szakasz következő mintarészletét:

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

Azt javasoljuk, hogy egy nem operációs rendszermeghajtót használjon FabricDataRoot és FabricLogRoot néven. Nagyobb megbízhatóságot biztosít az olyan helyzetek elkerülése érdekében, amikor az operációs rendszer nem válaszol. Ha csak az adatgyökeret szabja testre, a naplógyökér egy szinttel az adatgyökér alá kerül.

### <a name="stateful-reliable-services-settings"></a>Állapotalapú megbízható szolgáltatások beállításai
A KtlLogger szakaszban beállíthatja a megbízható szolgáltatások globális konfigurációs beállításait. Ezekről a beállításokról további információt az [Állapotalapú megbízható szolgáltatások konfigurálása című témakörben talál.](service-fabric-reliable-services-configuration.md) A következő példa bemutatja, hogyan módosíthatja a megosztott tranzakciónaplót, amely az állapotalapú szolgáltatások megbízható gyűjteményeinek biztonsági másolataként jön létre:

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
A bővítményfunkciók konfigurálásához konfigurálja az apiVersion-t 04-2017 vagy újabb verzióra, és konfigurálja az addonFeatures-t az itt látható módon:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
Az összes elérhető bővítményfunkció látható a [Service Fabric REST API-referencia területen.](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-addonfeatures)

### <a name="container-support"></a>Tárolótámogatás
Ahhoz, hogy a Tárolók támogatása a Windows Server-tárolók és a Hyper-V tárolók önálló fürtök, a DnsService bővítmény szolgáltatás engedélyezni kell.

## <a name="next-steps"></a>További lépések
Miután a fürt *Config.json* fájlja az önálló fürt beállításának megfelelően konfigurálva van, telepítheti a fürtöt. Kövesse az [Önálló service Fabric-fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)című részben leírt lépéseket. 

Ha önálló fürtvan telepítve, [frissítheti egy önálló fürt konfigurációját](service-fabric-cluster-config-upgrade-windows-server.md)is. 

Ismerje meg, hogyan [jelenítheti meg a fürtöt a Service Fabric Intézővel.](service-fabric-visualizing-your-cluster.md)

