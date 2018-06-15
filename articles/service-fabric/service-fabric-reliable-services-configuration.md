---
title: Az Azure megbízható mikroszolgáltatások konfigurálása |} Microsoft Docs
description: További tudnivalók az Azure Service Fabric állapotalapú Reliable Services konfigurálása.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: vturecek
ms.assetid: 9f72373d-31dd-41e3-8504-6e0320a11f0e
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: sumukhs
ms.openlocfilehash: c5aaf9869326f2de86d3bff33f36e8f967f3e6fa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210001"
---
# <a name="configure-stateful-reliable-services"></a>Állapot-nyilvántartó megbízható szolgáltatások konfigurálása
Megbízható szolgáltatások konfigurációs beállításainak két csoportjára van. Egy kiszolgáló a fürt összes megbízható szolgáltatás globális, míg a más set egy adott megbízható szolgáltatással.

## <a name="global-configuration"></a>A globális konfiguráció
A globális megbízható szolgáltatáskonfiguráció a fürtjegyzékben, a fürt KtlLogger szakaszban van megadva. Ez lehetővé teszi, hogy a megosztott napló helyét és méretét, valamint a globális memóriakorlátokat a naplózó által használt konfigurációs. A fürtjegyzékben, amely tárolja a beállításokat és konfigurációkat, amelyek minden csomópont és a fürt szolgáltatások egyetlen XML-fájl. A fájl neve általában ClusterManifest.xml. Láthatja, hogy a fürt jegyzékének a fürthöz, a Get-ServiceFabricClusterManifest powershell-paranccsal.

### <a name="configuration-names"></a>Konfigurációs nevek
| Name (Név) | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |kilobájt |8388608 |Lefoglalni a kernel módban naplózó írási puffer memóriakészletben KB-os minimális száma. A memóriakészletben használható állapotadatokat előtt lemezre írás gyorsítótárazását. |
| WriteBufferMemoryPoolMaximumInKB |kilobájt |Korlátlan |Milyen mértékben növelhető a, amelyhez a naplózó írható memória pufferkészlet maximális mérete. |
| SharedLogId |GUID |"" |Adja meg egy egyedi GUID azonosítója, az alapértelmezett megosztott naplófájl a fürt minden csomópontjára, amelyek nem adnak meg a SharedLogId a szolgáltatáskonfiguráció egyedi az összes megbízható szolgáltatás által használt azonosítására használható. Ha SharedLogId meg van adva, majd SharedLogPath is kötelező. |
| SharedLogPath |Teljes elérési útja |"" |Adja meg a teljes elérési útja, ahol a megosztott naplófájl a fürt minden csomópontjára, amelyek nem adnak meg a SharedLogPath a szolgáltatáskonfiguráció egyedi az összes megbízható szolgáltatások használnak. Azonban ha SharedLogPath meg van adva, majd SharedLogId is kötelező. |
| SharedLogSizeInMB |megabájt |8192 |Azt a statikusan lefoglalni a megosztott napló MB szabad lemezterület. Az érték 2048 vagy nagyobb lehet. |

Azure ARM vagy a helyszíni JSON-sablon, az alábbi példa bemutatja, hogyan módosíthatja a biztonsági másolatot a megbízható gyűjtemények állapotalapú szolgáltatások jön létre a megosztott tranzakciónapló.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>A minta helyi fejlesztői fürt manifest szakasz
Ha szeretné módosítani a helyi fejlesztési környezet, módosítsa a helyi clustermanifest.xml fájlt szeretné.

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
A tranzakciónaplókat tartalmazó globális memóriát lefoglalni érhető el az összes megbízható szolgáltatáshoz a csomópont állapotadatok gyorsítótárazása, mielőtt a megbízható szolgáltatás replika társított a dedikált naplóba való írása nem lapozható kernelmemória rendelkezik. A készlet méretét a WriteBufferMemoryPoolMinimumInKB és WriteBufferMemoryPoolMaximumInKB beállítások vezérlik. WriteBufferMemoryPoolMinimumInKB határozza meg, mind a kezdeti memóriakészlet és a legkisebb mérete, amelyre a memóriakészletben csökkentheti. WriteBufferMemoryPoolMaximumInKB mérete a legmagasabb, amelyhez a memóriakészletben is növekszik. Minden megnyitott megbízható szolgáltatás replika növelheti a memória-készlet mérete legfeljebb WriteBufferMemoryPoolMaximumInKB megállapítása szerint a rendszer összeggel. Ha további igény szerint a memóriakészletében található, mint amennyi rendelkezésre álló memória, memória kérelmek program elhalasztja mindaddig, amíg elérhető memória. Ezért ha memóriakészletben írási puffer túl kicsi az adott konfigurációs majd a teljesítmény romolhat.

A SharedLogId és SharedLogPath-beállítások mindig használhatók együtt adható meg a GUID Azonosítót és az összes csomópont az alapértelmezett megosztott napló helye a fürt. Az alapértelmezett megosztott napló az összes megbízható szolgáltatás, amely nem adja meg a beállításokat az adott szolgáltatás settings.xml szolgál. A legjobb teljesítmény érdekében a megosztott fájlok kizárólag használják a megosztott naplófájl való versengés csökkentheti lemezekre kell elhelyezni.

SharedLogSizeInMB meghatározza a szabad lemezterület az alapértelmezett megosztott napló minden csomóponton készletméret.  SharedLogId és SharedLogPath nem kell megadni ahhoz, hogy SharedLogSizeInMB meghatározását.

## <a name="service-specific-configuration"></a>Adott konfigurációs szolgáltatás
Állapotalapú Reliable Services alapértelmezett konfigurációk módosíthatja, ha a konfigurációs csomagot (konfiguráció) vagy a szolgáltatás megvalósítása (kód) használatával.

* **Config** -konfiguráció a konfigurációs csomag keresztül valósul módosítása a Settings.xml fájl, amelyet a Microsoft Visual Studio csomag legfelső szintű konfigurációs mappában lévő minden egyes szolgáltatás az alkalmazásban.
* **Kód** -konfigurációs kódot keresztül valósul hozzon létre egy ReliableStateManager használatával egy ReliableStateManagerConfiguration objektum a megfelelő beállításokat.

Alapértelmezés szerint az Azure Service Fabric-futtatókörnyezet megkeresi az előre definiált nevek a Settings.xml fájlban, és a konfigurációs értékeket az alapul szolgáló futásidejű összetevők létrehozása során fogyaszt.

> [!NOTE]
> Tegye **nem** a szakaszneveket, a Visual Studio megoldás jön létre, kivéve, ha azt tervezi, hogy konfigurálja a szolgáltatást kód Settings.xml fájlban a következő konfigurációk törlése.
> A konfigurációs csomag szakasz név vagy átnevezése szükséges kódváltoztatást a ReliableStateManager konfigurálásakor.
> 
> 

### <a name="replicator-security-configuration"></a>A replikáló biztonsági konfiguráció
A replikáció során használt kommunikációs csatornát replikátor biztonsági beállításokkal szolgálnak. Ez azt jelenti, hogy szolgáltatások nem fog tudni megtekintéséhez egymás replikációs forgalmat, amely biztosítja, hogy a magas rendelkezésre állási adatok is biztonságos. Alapértelmezés szerint egy üres biztonsági konfigurációs szakasz megakadályozza, hogy a replikációs biztonságot.

### <a name="default-section-name"></a>Alapértelmezett szakasz neve
ReplicatorSecurityConfig

> [!NOTE]
> Ez a szakasz nevének módosításához, bírálja felül a ReliableStateManagerConfiguration konstruktora replicatorSecuritySectionName paraméter, a szolgáltatás ReliableStateManager létrehozásakor.
> 
> 

### <a name="replicator-configuration"></a>Replikációs konfiguráció
Replikációs beállítások konfigurálása a replikátor, amely feladata, hogy az állapot-nyilvántartó megbízható szolgáltatás állapotának nagymértékben megbízható replikálódik, és az állapot helyi megőrzése.
Az alapértelmezett konfiguráció a Visual Studio-sablon által generált és elegendőnek kell lennie. Ez a szakasz beszél finomhangolhatják a replikátor rendelkezésre álló további beállításokat.

### <a name="default-section-name"></a>Alapértelmezett szakasz neve
ReplicatorConfig

> [!NOTE]
> Ez a szakasz nevének módosításához, bírálja felül a ReliableStateManagerConfiguration konstruktora replicatorSettingsSectionName paraméter, a szolgáltatás ReliableStateManager létrehozásakor.
> 
> 

### <a name="configuration-names"></a>Konfigurációs nevek
| Name (Név) | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |másodperc |0.015 |Az időszak, amely a következő művelet elküldése előtt fogadása után a másodlagos vár replikátor biztonsági nyugtázást az elsődleges. Ezt az időközt végrehajtva műveletek küldésének bármely más nyugták küldése a egy választ. |
| ReplicatorEndpoint |– |Nincs alapértelmezett érték – a kötelező paraméter: |IP-cím és az elsődleges és másodlagos replikátor kommunikálni más gyártóitól a replika által használt port beállítása. Ez hivatkoznia kell a TCP-erőforrás végpont a szolgáltatás jegyzékben. Tekintse meg [Service manifest erőforrások](service-fabric-service-manifest-resources.md) további végpont erőforrások definiálása a szolgáltatás jegyzékben. |
| MaxPrimaryReplicationQueueSize |Műveletek száma |8192 |Az elsődleges várólistában lévő műveletek maximális száma. Egy műveletet a fel nem szabadul, miután az elsődleges replikációs nyugtázást fogad az összes másodlagos gyártóitól. Ez az érték 64 és 2 valamelyik hatványa nagyobbnak kell lennie. |
| MaxSecondaryReplicationQueueSize |Műveletek száma |16384 |A másodlagos várólista műveletek maximális száma. Egy művelet fel nem szabadul állapotában adatmegőrzési keresztül magas rendelkezésre állású elvégzése után. Ez az érték 64 és 2 valamelyik hatványa nagyobbnak kell lennie. |
| CheckpointThresholdInMB |MB |50 |Az állapot alkulcsaihoz fájl naplóterület mennyisége. |
| MaxRecordSizeInKB |KB |1024 |A replikátor írhat a naplóban szereplő legnagyobb rekordméretet. Ez az érték nagyobb, mint 16 és 4 többszörösének kell lennie. |
| MinLogSizeInMB |MB |0 (megállapítása szerint a rendszer) |A tranzakciós napló legkisebb méretét. A napló nem engedélyezett alatt ez a beállítás méretűre csonkolja. 0 azt jelenti, hogy a replikátor határozza meg a minimális naplóméret. Az érték növelésével növeli a részleges példányszám és a növekményes biztonsági mentés óta veszélyét annak, hogy a megfelelő naplófájlok rekordok csonkolva lesz arányában ezt a lehetőséget. |
| TruncationThresholdFactor |Tényező |2 |Meghatározza, hogy milyen a napló mérete, a csonkolási indul. Csonkolási küszöbérték TruncationThresholdFactor megszorozza MinLogSizeInMB határozza meg. TruncationThresholdFactor 1-nél nagyobbnak kell lennie. MinLogSizeInMB * TruncationThresholdFactor MaxStreamSizeInMB kisebbnek kell lennie. |
| ThrottlingThresholdFactor |Tényező |4 |Meghatározza, hogy milyen a napló mérete, a replika kezdi szabályozva. Sávszélesség-szabályozási küszöbértéke (MB) határozza meg a maximális ((MinLogSizeInMB * ThrottlingThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)). Sávszélesség-szabályozási küszöbértéke (MB) csonkolása küszöbértéket (megabájtban) nagyobbnak kell lennie. Csonkolási küszöbérték (MB) a MaxStreamSizeInMB kisebbnek kell lennie. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Maximális mérete (MB) a megadott biztonságimásolat-naplólánccal rendelkeznek a biztonsági mentési naplók halmozott. Egy növekményes biztonsági mentési kérelem sikertelen lesz, ha a növekményes biztonsági mentés hoz létre egy biztonsági mentési napló, amelyek a halmozott biztonsági mentési naplók a lehet nagyobb, mint ez a méret kapcsolódó teljes biztonsági mentés óta. Ilyen esetben felhasználói teljes biztonsági mentés végrehajtása szükséges. |
| SharedLogId |GUID |"" |Adja meg egy egyedi GUID azonosítója, a megosztott naplófájlt a replika használt azonosítására használható. Szolgáltatások általában, ne használja ezt a beállítást. Azonban ha SharedLogId meg van adva, majd SharedLogPath is kötelező. |
| SharedLogPath |Teljes elérési útja |"" |Adja meg a teljes elérési útja, ahol létrejön-e a megosztott naplófájlban a replikára vonatkozóan. Szolgáltatások általában, ne használja ezt a beállítást. Azonban ha SharedLogPath meg van adva, majd SharedLogId is kötelező. |
| SlowApiMonitoringDuration |másodperc |300 |Megadja a felügyelt API-hívások figyelési időközt. Példa: felhasználó által megadott biztonsági mentési visszahívási függvény. A lejárta után egy figyelmeztetés állapotjelentése küldi az állapotkezelő. |

### <a name="sample-configuration-via-code"></a>Mintakonfiguráció kód
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


### <a name="sample-configuration-file"></a>Minta konfigurációs fájlt
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
BatchAcknowledgementInterval replikációs késésétől szabályozza. "0" értéket eredményez a lehető legkisebb késleltetést, ugyan átviteli sebesség (a további fel nyugtázási üzeneteket kell küldött és feldolgozásra, kevesebb nyugták tartalmazó).
Minél nagyobb a BatchAcknowledgementInterval értéke, annál magasabb a teljes replikációs teljesítményt, magasabb művelet késés használ. Ez közvetlenül a Tiltás késése a tranzakciók véglegesítése több.

CheckpointThresholdInMB értéke határozza meg, amellyel a replikátor tárolni állapotadatait a replika dedikált naplófájl lemezterületet. Növekvő ennek alapértelmezett értéke azt eredményezheti, hogy ha egy új replika hozzáadódik a gyorsabb újrakonfigurálása. Ez az az oka, amelyek miatt a naplóban szereplő műveletek további előzmények rendelkezésre állását akkor történik részleges állapot átvitelét. A potenciálisan növelheti a helyreállítási idő a replikák rendszerösszeomlás után.

A MaxRecordSizeInKB beállítás határozza meg, hogy egy rekordot, amely a replikátor által a naplófájl írható maximális méretét. A legtöbb esetben az alapértelmezett 1024 KB-os rekord mérete optimális. Előfordulhat azonban, ha a szolgáltatás okozza az állapotinformációkat részeként nagyobb adatelemek, majd ezt az értéket kell növelni. Nincs a MaxRecordSizeInKB kisebb, mint 1024, így kisebb rekordok használja csak a kisebb bejegyzés szükséges hely sok előnye. Elvárjuk, hogy ez az érték csak ritka esetekben módosítani kellene.

A SharedLogId és SharedLogPath beállítások az alapértelmezett megosztott naplóból egy különálló megosztott naplót használja a csomópont szolgáltatás mindig használhatók együtt. A hatékonyság a lehető legtöbb szolgáltatások megosztott napló kell meghatároznia. Megosztott naplófájlok központi adatátviteli versengés csökkentése érdekében a megosztott naplófájl kizárólag a használt lemezen kell elhelyezni. Elvárjuk, hogy ez az érték csak ritka esetekben módosítani kellene.

## <a name="next-steps"></a>További lépések
* [A Visual Studio a Service Fabric-alkalmazás hibakeresése](service-fabric-debugging-your-application.md)
* [Fejlesztői útmutató a Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

