---
title: Az Azure Service Fabric Reliable Services konfigurálása |} A Microsoft Docs
description: Ismerje meg az Azure Service Fabric állapotalapú Reliable Services konfigurálása.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: vturecek
ms.assetid: 9f72373d-31dd-41e3-8504-6e0320a11f0e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: sumukhs
ms.openlocfilehash: ef7b012c96522f15bab230475a97681945592d59
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728590"
---
# <a name="configure-stateful-reliable-services"></a>A stateful reliable services konfigurálása
Nincsenek konfigurációs beállításait a reliable services két csoportját. Egy, a fürt összes reliable Services globális, viszont a többi csoport csak egy adott megbízható szolgáltatás.

## <a name="global-configuration"></a>A globális konfiguráció
A globális reliable Services-konfiguráció van megadva a fürtjegyzék a fürt a KtlLogger szakaszában. Lehetővé teszi a megosztott napló helyét és méretét, valamint a globális memóriakorlátokat a naplózó által használt konfigurációját. A fürtjegyzék egy egyetlen XML-fájl, amely tartalmazza a beállítások és konfigurációk, amely a alkalmazni a csomópontok és a szolgáltatások a fürtben. A fájl neve általában ClusterManifest.xml. Láthatja, hogy a fürt a fürt a Get-ServiceFabricClusterManifest powershell-paranccsal jegyzékfájl.

### <a name="configuration-names"></a>Konfigurációs nevek
| Name (Név) | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |kilobájt |8388608 |A naplózó írási memória pufferkészlet rendszermag módban lefoglalni KB minimális száma. Gyorsítótárazás lemezre írás előtt állapotinformációkat a memóriakészletben használható. |
| WriteBufferMemoryPoolMaximumInKB |kilobájt |Korlátlan |Amelyhez a naplózó írási pufferkészletben memória maximális mérete növelhető. |
| SharedLogId |GUID |"" |Adja meg egy egyedi GUID Azonosítót az alapértelmezett megosztott naplófájl a fürt összes csomópontja, amely a szolgáltatás adott konfigurációban a SharedLogId nem adja meg az összes megbízható szolgáltatások által használt azonosítására használható. Ha SharedLogId van megadva, majd SharedLogPath is kötelező. |
| SharedLogPath |Teljes elérési útja |"" |Itt adhatja meg a teljes elérési útja, ahol a megosztott naplófájlban a fürt összes csomópontja, amely a szolgáltatás adott konfigurációban a SharedLogPath nem adja meg az összes megbízható szolgáltatás használja. Azonban ha SharedLogPath van megadva, majd SharedLogId is kötelező. |
| SharedLogSizeInMB |megabájt |8192 |Ennyi statikusan lefoglalni a közös naplók MB szabad lemezterület. Az értéknek kell lennie, 2048 vagy nagyobb. |

Az Azure ARM vagy a helyszíni JSON-sablon az alábbi példa bemutatja, hogyan módosíthatja a megosztott tranzakciós napló, amely minden, az állapotalapú szolgáltatások esetében a reliable collections biztonsági létrejön.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Minta helyi fejlesztői fürtöt manifest szakasz
Ha azt szeretné, módosíthatja ezt a helyi fejlesztési környezetet a helyi clustermanifest.xml fájl szerkesztése szüksége.

```xml
   <Section Name="KtlLogger">
     <Parameter Name="SharedLogSizeInMB" Value="4096"/>
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
   </Section>
```

### <a name="remarks"></a>Megjegyzések
A naplózó rendelkezik egy globális készlet nem lapozható kernel memória érhető el az összes megbízható szolgáltatás a csomópont állapotadatok gyorsítótárazáshoz, előtt a reliable Services replika társított ír a dedikált napló számára lefoglalt memória. A készlet méretét a WriteBufferMemoryPoolMinimumInKB és WriteBufferMemoryPoolMaximumInKB beállításai vezérlik majd. WriteBufferMemoryPoolMinimumInKB megadja a memóriakészlet kezdeti mérete és a legkisebb mérete, amelyhez a memóriakészletben lehet zsugorítani. WriteBufferMemoryPoolMaximumInKB, amelyre a memóriakészletben előfordulhat, hogy növelje a legnagyobb mérete. Minden egyes megnyitott reliable Services-replika növelhető a memóriakészletben mérete legfeljebb WriteBufferMemoryPoolMaximumInKB rendszer meghatározott értékkel. Ha több igény szerint a memóriakészletében nincs elég memória, kérelmek, a memória időszakú késleltetéssel kerül mindaddig, amíg elérhető memória. Ezért ha az írási puffert memóriakészletben túl kicsi, egy adott konfigurációhoz majd a teljesítmény romolhat.

A SharedLogId és SharedLogPath beállítások mindig használhatók együtt adható meg GUID és a helyét csomópontjaihoz tartozó alapértelmezett megosztott napló a fürtben. Az alapértelmezett megosztott napló, amely nem adja meg a beállításokat a settings.xml az adott szolgáltatás minden reliable Services szolgál. A legjobb teljesítmény érdekében közös naplófájlokat a versengés csökkentése érdekében a megosztott naplófájl kizárólag a használt lemezen kell elhelyezni.

SharedLogSizeInMB megadja az alapértelmezett megosztott naplók az összes csomóponton lefoglalandó területet.  SharedLogId és SharedLogPath nem kell megadni ahhoz, hogy SharedLogSizeInMB adni.

## <a name="service-specific-configuration"></a>Konfigurációs szolgáltatás
A stateful Reliable Services alapértelmezett konfigurációk módosíthatja a konfigurációs csomagot (konfiguráció) vagy a szolgáltatás megvalósítása (kód) használatával.

* **Konfigurációs** -konfigurációt a konfigurációs csomag keresztül történik a Settings.xml a Microsoft Visual Studio csomag gyökerében, az alkalmazás minden egyes szolgáltatás Config mappában létrehozott fájl módosításával.
* **Kód** -konfigurációs kódot keresztül történik egy használatával egy ReliableStateManagerConfiguration objektumot a megfelelő beállítások értéke ReliableStateManager létrehozásával.

Alapértelmezés szerint az Azure Service Fabric-futtatókörnyezet előre definiált nevek a Settings.xml fájlban keres, és felhasználja a konfigurációs értékeket az alapul szolgáló futásidejű összetevők létrehozása során.

> [!NOTE]
> Tegye **nem** a szakaszneveket, a Visual Studio-megoldásban, kivéve, ha azt tervezi, konfigurálja a szolgáltatást kód keresztül létrehozott Settings.xml fájlban az alábbi konfigurációk törlése.
> A konfigurációs csomagot vagy szakasz nevek átnevezése lesz szükség a kód módosítása, a ReliableStateManager konfigurálásakor.
> 
> 

### <a name="replicator-security-configuration"></a>A replikáló biztonsági konfiguráció
A replikáló biztonsági konfigurációk szolgálnak a replikáció során használt kommunikációs csatornát. Ez azt jelenti, hogy szolgáltatás nem lesz a többi összes replikációs forgalmat, amely biztosítja, hogy az adatok magas rendelkezésre állású legyen is biztonságos láthatja. Alapértelmezés szerint egy üres biztonsági konfigurációs szakasz megakadályozza, hogy a replikációs biztonságot.

> [!IMPORTANT]
> A Linux-csomópontokat a tanúsítványok kell PEM-formátumú. További információk megkeresése és tanúsítványok konfigurálása Linux kapcsolatban lásd: [tanúsítványok konfigurálása Linux](./service-fabric-configure-certificates-linux.md). 
> 
> 

### <a name="default-section-name"></a>Alapértelmezett szakasz neve
ReplicatorSecurityConfig

> [!NOTE]
> Ez a szakasz nevének módosításához, bírálja felül a replicatorSecuritySectionName paraméter ReliableStateManagerConfiguration konstruktor ezt a szolgáltatást a ReliableStateManager létrehozásakor.
> 
> 

### <a name="replicator-configuration"></a>Replikációs konfiguráció
A replikáló konfigurációk konfigurálása a replikátor, amely feladata, hogy magas megbízhatóságú az állapotalapú Reliable Services állapot replikálása, és az állapot helyi megőrzése.
Az alapértelmezett konfiguráció a Visual Studio-sablon által létrehozott és elegendőnek kell lennie. Ez a szakasz ismerteti a replikátor finomhangolása elérhető további konfigurációs.

### <a name="default-section-name"></a>Alapértelmezett szakasz neve
ReplicatorConfig

> [!NOTE]
> Ez a szakasz nevének módosításához, bírálja felül a replicatorSettingsSectionName paraméter ReliableStateManagerConfiguration konstruktor ezt a szolgáltatást a ReliableStateManager létrehozásakor.
> 
> 

### <a name="configuration-names"></a>Konfigurációs nevek
| Name (Név) | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |másodperc |0.015 |Adott időszakban, amelyhez a replikátor, miután kapott egy művelet, mielőtt elküldené a másodlagos vár az elsődleges biztonsági nyugtázást. Bármely más nyugtázás a intervallumon belül feldolgozott műveletek kell elküldeni, egy választ kapnak. |
| ReplicatorEndpoint |– |Alapértelmezett – kötelező paraméter |IP-cím és az elsődleges és másodlagos replikátor kommunikálni más gyártóitól a replika által használt port megadása A TCP-erőforrás végpontjának a szolgáltatásjegyzékben ez kell hivatkoznia. Tekintse meg [szolgáltatásjegyzéki erőforrások](service-fabric-service-manifest-resources.md) további végpont erőforrások meghatározása szolgáltatásjegyzékben kapcsolatban. |
| MaxPrimaryReplicationQueueSize |Műveletek száma |8192 |Az elsődleges üzenetsor-műveletek maximális száma. Egy műveletet a fel nem szabadul, miután az elsődleges replikátor nyugtázást fogad az összes másodlagos gyártóitól. Ez az érték nagyobb, mint 64 és 2 hatványának kell lennie. |
| MaxSecondaryReplicationQueueSize |Műveletek száma |16384 |A másodlagos üzenetsor-műveletek maximális száma. Egy művelet fel nem szabadul, az állapot megőrzése révén magas rendelkezésre állású elvégzése után. Ez az érték nagyobb, mint 64 és 2 hatványának kell lennie. |
| CheckpointThresholdInMB |MB |50 |Az állapot alkulcsaihoz log-fájl terület mennyiségét. |
| MaxRecordSizeInKB |KB |1024 |Legnagyobb rekord méretet, amely a replikátor írhatnak a naplóban. Ez az érték 4 és 16-nál nagyobb többszörösének kell lennie. |
| MinLogSizeInMB |MB |0 (rendszer határozza meg) |A tranzakciós napló mérete minimális. A napló nem engedélyezni alatt ez a beállítás méretűre csonkolja. 0 azt jelzi, hogy a replikátor határozza meg a minimális napló méretét. Ez az érték növelése növeli a szoftver-és a kapcsolódó naplórekordok csonkolása arányában óta részleges másolatok és a növekményes biztonsági mentések ezt a lehetőséget. |
| TruncationThresholdFactor |Tényező |2 |Azt határozza meg, milyen a napló mérete, akkor aktiválódik, csonkolása. Csonkolási küszöbérték TruncationThresholdFactor megszorozza MinLogSizeInMB határozza meg. TruncationThresholdFactor 1-nél nagyobbnak kell lennie. MinLogSizeInMB * TruncationThresholdFactor MaxStreamSizeInMB kisebbnek kell lennie. |
| ThrottlingThresholdFactor |Tényező |4 |Meghatározza, hogy milyen a naplófájlok méretétől, a replika kezdi szabályozás alatt áll. Sávszélesség-szabályozási küszöbérték (MB) maximális száma határozza meg ((MinLogSizeInMB * ThrottlingThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)). Sávszélesség-szabályozási küszöbérték (MB) csonkolása küszöbérték (MB-ban) nagyobbnak kell lennie. Csonkolási küszöbérték (MB-ban) MaxStreamSizeInMB kisebbnek kell lennie. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Maximális mérete (MB) a megadott biztonságimásolat-naplólánccal rendelkeznek a biztonsági mentési naplók tevődik össze. Egy növekményes biztonsági mentési kérelem sikertelen lesz, ha a növekményes biztonsági mentést hoz létre egy biztonsági másolatának a napló, amely a lehet nagyobb, mint ez a méret kapcsolódó teljes biztonsági mentés óta a halmozott biztonsági mentési naplók okozna. Ezekben az esetekben felhasználó szükséges egy teljes biztonsági mentés. |
| SharedLogId |GUID |"" |Adja meg egy egyedi GUID Azonosítót a megosztott naplófájlban, és a replika használt azonosítására használható. Szolgáltatások általában, ne használja ezt a beállítást. Azonban ha SharedLogId van megadva, majd SharedLogPath is kötelező. |
| SharedLogPath |Teljes elérési útja |"" |Itt adhatja meg a teljes elérési útja, ahol létrejön a megosztott naplófájlt a replika. Szolgáltatások általában, ne használja ezt a beállítást. Azonban ha SharedLogPath van megadva, majd SharedLogId is kötelező. |
| SlowApiMonitoringDuration |másodperc |300 |Megadja a figyelési időközt felügyelt API-hívások. Példa: felhasználó által megadott biztonsági mentési visszahívást függvény. Az intervallum letelte után egy figyelmeztetés állapotjelentés küldi el a Health Manager. |
| LogTruncationIntervalSeconds |másodperc |0 |Konfigurálható időköz, mely log csonkolása megkezdődik a minden egyes replikának. Győződjön meg, hogy a napló is csonkolva van, az időtartam helyett csak napló mérete alapján szolgál. Ez a beállítás is arra kényszeríti a törölt bejegyzések végleges törlésére a megbízható szótárban. Ezért azt segítségével győződjön meg arról, hogy a rendszer a törölt elemek időben üríti ki. |

### <a name="sample-configuration-via-code"></a>Minta konfigurációs kódot keresztül
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>Minta konfigurációs fájl
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


### <a name="remarks"></a>Megjegyzések
BatchAcknowledgementInterval replikációs késésétől szabályozza. "0" értéket eredményez a lehető legkisebb késleltetést, átviteli sebesség cserébe (a nyugtázó további üzenetek küldhetők és feldolgozott kevesebb nyugtázás tartalmazó kell).
BatchAcknowledgementInterval minél nagyobb az értéke, annál az általános replikációs átviteli sebességet, cserébe magasabb. műveleti késés. Ez közvetlenül a rendszer lefordítja arra a tranzakció-véglegesítések késését.

CheckpointThresholdInMB értéke szabályozza a lemezterület-állapot adatainak tárolására a replika dedikált naplófájlban a replikátor használhatja. Növelésére, mint az alapértelmezett értéke azt eredményezheti, hogy új replikát hozzáadásakor a készlethez, gyorsabb újrakonfigurálása. A részleges állapotátvitel, amely miatt a műveletek a naplóban szereplő további előzmények rendelkezésre állásának okozza. Ez potenciálisan növelheti a replika helyreállítási idő rendszerösszeomlás után.

A MaxRecordSizeInKB beállítás határozza meg, hogy egy rekord, amely a replikátor által a naplófájlba írható maximális méretét. A legtöbb esetben az alapértelmezett rekord 1024 KB-os mérete optimális. Előfordulhat azonban, ha a szolgáltatás részeként az állapotinformációkat nagyobb adatelemek okoz, majd ezt az értéket kell kell emelni. Nincs kis azért előnyös MaxRecordSizeInKB kisebb, mint 1024, így kisebb rekordok csak a szükséges a kisebb rekord hely használja. Terveink szerint már, hogy ez az érték csak ritka esetekben módosítani kellene.

A SharedLogId és SharedLogPath beállításokat mindig együtt használja, hogy a csomópont egy külön megosztott naplóban az alapértelmezett megosztott naplóból egy szolgáltatást. A hatékonyság növelése érdekében a lehető legtöbb szolgáltatások meg kell adnia az ugyanabba a megosztott naplófájlba. Megosztott naplófájlok kizárólag használják a megosztott naplófájl fő áthelyezését a versengés csökkentése érdekében lemezen kell elhelyezni. Terveink szerint már, hogy ez az érték csak ritka esetekben módosítani kellene.

## <a name="next-steps"></a>További lépések
* [A Visual Studióban a Service Fabric-alkalmazás hibakeresése](service-fabric-debugging-your-application.md)
* [Reliable Services – fejlesztői referencia](https://msdn.microsoft.com/library/azure/dn706529.aspx)

