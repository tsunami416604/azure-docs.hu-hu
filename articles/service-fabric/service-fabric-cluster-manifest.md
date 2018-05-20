---
title: Az Azure Service Fabric önálló fürt konfigurálása |} Microsoft Docs
description: Megtudhatja, hogyan konfigurálja az önálló vagy a helyszíni Azure Service Fabric-fürt.
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
ms.date: 12/06/2017
ms.author: dekapur
ms.openlocfilehash: e0fed608ac9dd02a6fe5563eefc30edb63d224b1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>A különálló Windows-fürt konfigurációs beállítások
Ez a cikk ismerteti, hogyan önálló Azure Service Fabric-fürt konfigurálása a művelet fájl használatával. Ez a fájl használandó adjon meg információt a fürtcsomópontokon, biztonsági beállításokkal, valamint a hálózati topológia hiba és a frissítési tartományok tekintetében.

Ha Ön [a különálló Service Fabric-csomag](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), művelet minták is szerepelnek. A minták, a név "DevCluster" rendelkező összes három csomópontja ugyanazon a számítógépen, logikai csomópontokból hozzon létre egy fürtöt. Ezek a csomópontok kívül legalább egy jelölésűnek kell lennie egy elsődleges csomóponton. Ez a fürt típus fejlesztési vagy tesztelési környezetben ajánlott. Nem támogatott a termelési fürtbe. A mintákat, amelyek "MultiMachine" a név segítségével létre üzemi osztályú fürtöket, minden egyes csomópont egy külön számítógépen. Az elsődleges csomópont ezeken a fürtökön a határozzák meg a fürt [megbízhatósági szint](#reliability). Kiadás 5.7, API-verzió 05-2017, a megbízhatóság szintű tulajdonság eltávolítása. Ehelyett a kód a legtöbb optimalizált megbízhatósági szint a fürt számítja ki. Ne próbáljon 5.7 verzióiban és újabb verziók esetében ez a tulajdonság értékének beállítása.


* ClusterConfig.Unsecure.DevCluster.json és ClusterConfig.Unsecure.MultiMachine.json létrehozását mutatják be egy nem biztonságos teszt- vagy éles fürt, illetve.

* ClusterConfig.Windows.DevCluster.json és ClusterConfig.Windows.MultiMachine.json létrehozását mutatják be biztonságáról teszt- vagy éles fürtöket [Windows biztonsági](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.json és ClusterConfig.X509.MultiMachine.json létrehozását mutatják be biztonságáról teszt- vagy éles fürtöket [X509 biztonsági](service-fabric-windows-cluster-x509-security.md).

Most vizsgáljuk meg egy művelet fájl különböző részeit.

## <a name="general-cluster-configurations"></a>Általános fürtkonfigurációk
Általános fürtkonfigurációk terjed ki a széles körű fürt-specifikus konfigurációk esetén, ahogy az az alábbi JSON kódrészletet:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

A rövid nevet a name változó hozzárendelésével adhat a Service Fabric-fürt. A clusterConfigurationVersion a fürt verziószáma. Azt minden alkalommal, amikor a Service Fabric-fürt frissítése növeléséhez. ApiVersion set hagyja az alapértelmezett értékre.

## <a name="nodes-on-the-cluster"></a>A fürt csomópontjai

    <a id="clusternodes"></a>

A Service Fabric-fürt a csomópontok területen az alábbi kódrészletben látható módon használatával konfigurálhatja a csomópontok:

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

A Service Fabric-fürt tartalmaznia kell legalább három csomópontot. Ez a szakasz a több csomópont is hozzáadható a beállításai alapján. Az alábbi táblázat ismerteti az egyes csomópontok konfigurációs beállítások:

| **A csomópont-konfiguráció** | **Leírás** |
| --- | --- |
| Csomópontnév |Bármilyen rövid nevet adhat a csomópontra. |
| IP-cím |Nyisson meg egy parancsablakot, és írja be az IP-cím, a csomópont található `ipconfig`. Tekintse meg az IPV4-címet, és rendelje hozzá az IP-cím változó. |
| nodeTypeRef |Minden csomópont rendelhetők hozzá másik csomóponttípus. A [csomóponttípusok](#node-types) a következő szakaszban definiálhatók. |
| faultDomain |Tartalék tartományok lehetővé teszik a rendszergazdák fürtön határozza meg a fizikai csomópontok, amelyek egyszerre megosztott fizikai függőségek miatt meghiúsulhat. |
| upgradeDomain |Frissítési tartományok jellemezhető a csomópontokra, amelyeket nagyjából egy időben, a Service Fabric-frissítések állnak le. Melyik frissítési tartományok hozzárendelése csomópontok is választható, mert nem korlátozza a fizikai követelmények. |

## <a name="cluster-properties"></a>Fürt tulajdonságai
A művelet a Tulajdonságok szakaszának segítségével konfigurálja a fürt látható módon:

### <a name="reliability"></a>Megbízhatóság
ReliabilityLevel fogalma a replikák száma vagy szolgáltatáspéldánynak a Service Fabric rendszer futtatható a fürt elsődleges csomópontjait határoz meg. Meghatározza, hogy ezek a szolgáltatások megbízhatóságát, így a fürt. Az érték kiszámítása fürt létrehozása és frissítése során a rendszer.

    <a id="reliability"></a>

### <a name="diagnostics"></a>Diagnosztika
A diagnosticsStore területen paraméterekkel engedélyezheti a diagnosztikai és a hibaelhárítási csomópont vagy a fürt hibák, ahogy az az alábbi kódrészletben is konfigurálhatja: 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

A metaadatok a fürt diagnosztika leírása, és a beállításai alapján állítható be. Ezek a változók megkönnyíti a ETW-nyomkövetési naplók gyűjtésére és összeomlási memóriaképek, valamint a teljesítményszámlálók. Az ETW-nyomkövetési naplók további információkért lásd: [a követési naplóban](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) és [ETW-nyomkövetés](https://msdn.microsoft.com/library/ms751538.aspx). Összes naplófájlt, beleértve a [összeomlási memóriaképek](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) és [teljesítményszámlálók](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx), irányítható a connectionString mappába a számítógépen. AzureStorage diagnosztika tárolására is használható. Tekintse meg az alábbi minta kódrészletet:

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Biztonság
A biztonsági szakaszban szükség a biztonságos különálló Service Fabric-fürt. Az alábbi kódrészletben láthatja az ebben a szakaszban egy része:

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

A metaadatok a biztonságos fürt leírása, és a beállításai alapján állítható be. A ClusterCredentialType és ServerCredentialType határozza meg, milyen típusú biztonsági, amely a fürt és a csomópontok valósítja meg. Akkor lehet megadni *X509* egy tanúsítványalapú biztonsági vagy *Windows* az Azure Active Directory-alapú biztonsági. A biztonsági szakasz többi biztonsági típusú alapul. Adja meg a biztonsági szakasz többi kapcsolatos információkért lásd: [tanúsítványok-alapú biztonsági önálló fürtben](service-fabric-windows-cluster-x509-security.md) vagy [Windows biztonsági önálló fürtben](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Csomóponttípusok

    <a id="nodetypes"></a>

A NodeType tulajdonságok értéke szakasz ismerteti, amely a fürt rendelkezik csomópontok típusú. Fürt esetén kell adni legalább egy csomópont típusát, ahogy az a következő kódrészletet: 

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

A név az adott csomóponttípus rövid nevét. A csomópont típusú csomópont létrehozásához a rövid nevet a nodeTypeRef változóhoz a csomóponton, rendeljen [azt már korábban említettük](#nodes-on-the-cluster). Az egyes csomópont adja meg a kapcsolati végpontok használt. Minden kapcsolat végpontokkal portszáma dönthet úgy, mindaddig, amíg azok nem ütköznek a fürt bármely más végpontja. Egy többcsomópontos fürt egy vagy több elsődleges csomópontok nincsenek (Ez azt jelenti, hogy isPrimary értéke *igaz*) attól függően, hogy a [reliabilityLevel](#reliability). Az elsődleges és a nonprimary csomóponttípusok kapcsolatos további információkért lásd: [Service Fabric fürt kapacitástervezésének szempontjai](service-fabric-cluster-capacity.md) NodeType tulajdonságok értéke és reliabilityLevel információt. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>A csomóponttípusok konfigurálásához használt végpontok
* clientConnectionEndpointPort csatlakozni a fürthöz, ha az ügyfél API-k használják az ügyfél által használt port. 
* clusterConnectionEndpointPort a port, amelyen a csomópontok kommunikálnak egymással.
* leaseDriverEndpointPort a csomópont még aktívak megállapítása a fürt bérleti illesztőprogram által használt port. 
* serviceConnectionEndpointPort a Service Fabric-ügyfél, hogy adott csomóponton kommunikálni az alkalmazások és szolgáltatások egy csomópont telepítése által használt port.
* httpGatewayEndpointPort a portot használják a Service Fabric Explorer kapcsolódik a fürthöz.
* ephemeralPorts bírálja felül a [az operációs rendszer által használt dinamikus portok](https://support.microsoft.com/kb/929851). A Service Fabric egy részét ezeket a portokat használja, mint a alkalmazás portok, és a fennmaradó érhetők el az operációs rendszer. Azt is rendeli ezt a tartományt a meglévő tartomány szerepel az operációs rendszer, így minden célra használhatja a minta JSON-fájlokat a megadott tartományokon. Győződjön meg arról, hogy az a kezdő és záró portokat közötti különbség legalább 255. Előfordulhat, hogy futtatja az ütközések, ha ezt a különbséget túl alacsony, mert ebben a tartományban van osztva az operációs rendszer. A beállított dinamikus porttartományt megtekintéséhez futtassa `netsh int ipv4 show dynamicport tcp`.
* applicationPorts a Service Fabric-alkalmazások által használt portokat. Az alkalmazás porttartományát elég nagynak kell lennie, amelyek a végpont követelmény az alkalmazások. Ezt a tartományt a dinamikus port tartományból a gépen, ez azt jelenti, hogy a konfiguráció szerint a ephemeralPorts tartomány kizárólagos kell lennie. A Service Fabric ezeket a portokat használja, amikor új portok használata kötelező, és gondoskodik megnyitni ezeket a portokat a tűzfalon. 
* reverseProxyEndpointPort egy nem kötelező fordított proxy végpontot. További információkért lásd: [Service Fabric fordított proxy](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Naplózási beállítások
A fabricSettings területen állíthatja be a gyökérkönyvtárak a Service Fabric-adatokat és a naplókat. Testre szabhatja, hogy ezeket a könyvtárakat csak a kezdeti fürt létrehozása során. Ez a szakasz a következő minta szövegrészletet lásd:

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

Azt javasoljuk, hogy egy-az operációs rendszer meghajtót használja-e a FabricDataRoot és a fabriclogroot mappában. Biztosít további megbízhatóság a helyzetek elkerülése, amikor az operációs rendszer nem válaszol. Ha csak a adatgyökerében szabja testre, a napló legfelső szintű kerül adatok gyökere alatt egy szinttel.

### <a name="stateful-reliable-services-settings"></a>Állapotalapú Reliable Services beállításai
KtlLogger területen állítsa be a Reliable Services globális konfigurációs beállításait. A beállításokkal további információkért lásd: [állapotalapú Reliable Services konfigurálása](service-fabric-reliable-services-configuration.md). A következő példa bemutatja, hogyan módosíthatja a megosztott tranzakciónapló biztonsági a megbízható gyűjtemények állapotalapú szolgáltatások jön létre:

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="add-on-features"></a>Bővítmény szolgáltatások
Bővítmény funkciók konfigurálására, a apiVersion 04-2017 vagy újabb, valamint konfigurálni a addonFeatures itt látható módon:

    "apiVersion": "04-2017",
    "properties": {
      "addOnFeatures": [
          "DnsService",
          "RepairManager"
      ]
    }

### <a name="container-support"></a>Tároló-támogatás
Ahhoz, hogy a Windows Server-tárolók és a Hyper-V fürtök különálló tárolói tároló támogatása, a DnsService bővítmény szolgáltatást engedélyezni kell.


## <a name="next-steps"></a>További lépések
Miután az önálló fürttelepítés megfelelően konfigurálta teljes művelet fájlt, a fürtök telepítése. Kövesse a [hozzon létre egy különálló Service Fabric-fürt](service-fabric-cluster-creation-for-windows-server.md). Majd folytassa a [fürt megjelenítése a Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) , és kövesse a lépéseket.

