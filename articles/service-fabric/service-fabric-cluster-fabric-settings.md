---
title: "Azure Service Fabric-fürt módosítása |} Microsoft Docs"
description: "Ez a cikk ismerteti a hálóbeállításokat és a háló frissítési házirendekben, amelyek testre szabhatja."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: 
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: chackdan
ms.openlocfilehash: c6289df50d17de1ef542abbf617eb1cdb2f32311
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="customize-service-fabric-cluster-settings-and-fabric-upgrade-policy"></a>A Service Fabric-fürt beállításait és a háló házirend testreszabása
Ez a dokumentum azt ismerteti, hogyan szabhatja testre a különböző hálóbeállításokat, és a háló frissítéséhez a Service Fabric-fürt házirendet. Testre szabhatja azokat keresztül a [Azure-portálon](https://portal.azure.com) vagy Azure Resource Manager-sablonnal.

> [!NOTE]
> Nem minden beállítások állnak rendelkezésre a portálon. Amennyiben a lenti beállítás nem érhető el a portálon testreszabása az Azure Resource Manager-sablonnal.
> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Fürt a beállításokat a Resource Manager-sablonok testreszabása
Az alábbi lépések bemutatják, hogyan lehet egy új beállítással *MaxDiskQuotaInMB* számára a *diagnosztika* szakasz.

1. Ugrás a https://resources.azure.com
2. Keresse meg az előfizetéshez kibontásával **előfizetések** -> **erőforráscsoportok** -> **Microsoft.ServiceFabric**  ->   **\<a fürt neve >**
3. A jobb felső sarokban válassza **olvasási/írási.**
4. Válassza ki **szerkesztése** , és frissítse a `fabricSettings` JSON elem és az új elem hozzáadása:

```
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Az alábbiakban olvashat egy listát háló beállítások testre szabható, szakasz szerint vannak rendezve.

### <a name="section-name-diagnostics"></a>Szakasz Name: diagnosztika
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| ConsumerInstances |Karakterlánc | Dinamikus |A DCA fogyasztói példányok listáját. |
| ProducerInstances |Karakterlánc | Dinamikus |A DCA készítő példányok listáját. |
| AppEtwTraceDeletionAgeInDays |Int, alapértelmezett érték 3 | Dinamikus |Nap elteltével nem törölni alkalmazás ETW-nyomkövetési adatokat tartalmazó régi ETL-fájlok száma. |
| AppDiagnosticStoreAccessRequiresImpersonation |Logikai érték, az alapértelmezett érték true | Dinamikus |-E megszemélyesítési, ha az alkalmazás nevében elérése diagnosztikai tárolja. |
| MaxDiskQuotaInMB |Int, alapértelmezett érték 65536 | Dinamikus |Lemezkvóta Windows Fabric MB a rendszernapló fájljaiban. |
| DiskFullSafetySpaceInMB |Int, alapértelmezett érték: 1024 | Dinamikus |Fennmaradó szabad lemezterület (MB) megvédeni a DCA használja. |
| ApplicationLogsFormatVersion |Int, alapértelmezett érték a 0 | Dinamikus |Alkalmazás verziója formátum naplózza. Támogatott értékek: 0 és 1. Az 1-es verzió 0-nál a ETW-esemény rekordból további mezőket tartalmazza. |
| ClusterId |Karakterlánc | Dinamikus |A fürt egyedi azonosítója. Ez jön létre, amikor létrehozza a fürtöt. |
| EnableTelemetry |Logikai érték, az alapértelmezett érték true | Dinamikus |Ez lesz engedélyezheti vagy tilthatja le a telemetriai adatokat. |
| EnableCircularTraceSession |Logikai érték, alapértelmezett értéke "false" | Statikus |A jelző azt jelzi, hogy használják-e körkörös nyomkövetési munkamenet. |

### <a name="section-name-traceetw"></a>Szakasz nevét: Nyomkövetési/Etw
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| Szint |Int, alapértelmezett érték 4 | Dinamikus |Nyomkövetési etw szint is igénybe vehet az érték 1, 2, 3, 4. Támogatott 4 kell őrizniük a nyomkövetési szint |

### <a name="section-name-performancecounterlocalstore"></a>Szakasz Name: PerformanceCounterLocalStore
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| IsEnabled |Logikai érték, az alapértelmezett érték true | Dinamikus |A jelző azt jelzi, hogy engedélyezve van-e a teljesítményszámlálók gyűjteményét a helyi csomóponton. |
| SamplingIntervalInSeconds |Int, alapértelmezett érték 60 | Dinamikus |Mintavételi időköz gyűjtött teljesítményszámlálók. |
| Számlálók |Karakterlánc | Dinamikus |Teljesítményszámlálók gyűjtéséhez vesszővel tagolt listája. |
| MaxCounterBinaryFileSizeInMB |Int, alapértelmezett érték 1 | Dinamikus |Maximális mérete (MB) a teljesítmény számláló bináris fájl. |
| NewCounterBinaryFileCreationIntervalInMinutes |Int, alapértelmezett érték 10 | Dinamikus |Maximális korlát (másodperc) után, amely egy új teljesítmény számláló bináris fájl jön létre. |

### <a name="section-name-setup"></a>Szakasz Name: a telepítő
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| FabricDataRoot |Karakterlánc | Nem engedélyezett |A Service Fabric-adatok gyökérkönyvtár. Alapértelmezett Azure d:\svcfab: |
| Fabriclogroot mappában |Karakterlánc | Nem engedélyezett |Service fabric napló gyökérkönyvtár. Ez azért, ahol kerülnek ú naplók és a nyomkövetési adatokat. |
| ServiceRunAsAccountName |Karakterlánc | Nem engedélyezett |A fabric host szolgáltatás futtatásához használt fiók nevét. |
| SkipFirewallConfiguration |Logikai érték, alapértelmezett értéke "false" | Nem engedélyezett |Itt adhatja meg, ha a tűzfal beállításait meg kell adnia a rendszer, vagy nem. Ez vonatkozik, csak akkor, ha a windows tűzfalat használja. Ha külső gyártótól származó tűzfalak használ, majd nyissa meg a portokat a rendszer és alkalmazások az |
|NodesToBeRemoved|karakterlánc, alapértelmezett érték a ""| Dinamikus |A csomópontokat, amelyeknek konfigurációs frissítés részeként el kell távolítani. (Csak az önálló verziója telepítéseinek)|
|ContainerNetworkSetup|logikai érték, alapértelmezett értéke hamis| Statikus |E tároló hálózat beállítása.|
|ContainerNetworkName|karakterlánc, alapértelmezett értéke L""| Statikus |A hálózatok nevét, a tároló hálózati beállítása során.|

### <a name="section-name-transactionalreplicator"></a>Szakasz Name: TransactionalReplicator
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| MaxCopyQueueSize |Uint, alapértelmezett érték 16384 | Statikus |Ez az a maximális értéke határozza meg a kezdeti replikálási műveletek fenntartó várólista. Vegye figyelembe, hogy 2 szintűnek kell lennie. Ha ez a méret a várólista eléri a futtatás során művelet számára halmozódni fog, az elsődleges és másodlagos gyártóitól között. |
| BatchAcknowledgementInterval | Idő (másodpercben), az alapértelmezett érték 0.015 | Statikus | Adja meg az időtartam másodpercben. Meghatározza, hogy az, hogy mennyi ideig a replikátor megvárja-e olyan műveleteket, mielőtt küld vissza nyugtázást fogadása után. Más műveletek kapott ebben az időszakban a nyugtázás a küldött vissza egy egyetlen -> csökkentése hálózati forgalmat, de lehetséges csökkenteni az átviteli sebessége a replikátor fog rendelkezni. |
| (Maxreplicationmessagesize). |Uint, alapértelmezett érték 52428800 | Statikus | Replikációs műveletek maximális méretét. Az alapértelmezett érték 50MB. |
| ReplicatorAddress |karakterlánc, alapértelmezett értéke "localhost:0" | Statikus | A végpont egy karakterlánc-"IP:Port", amely a Windows Fabric replikátor ahhoz, hogy a küldési és fogadási műveletek más replikák-kapcsolatok létesítéséhez használt formában. |
| InitialPrimaryReplicationQueueSize |Uint, alapértelmezett érték 64 | Statikus |Ez az érték határozza meg a várólista, a replikálási műveletek az elsődleges fenntartó kezdeti méretét. Vegye figyelembe, hogy 2 szintűnek kell lennie.|
| MaxPrimaryReplicationQueueSize |Uint, alapértelmezett érték 8192 | Statikus |Ez az az elsődleges replikációs várólistában lévő létező sikerült műveletek maximális száma. Vegye figyelembe, hogy 2 szintűnek kell lennie. |
| MaxPrimaryReplicationQueueMemorySize |Uint, alapértelmezett érték a 0 | Statikus |Ez az a maximális érték az elsődleges replikációs sor bájtokban. |
| InitialSecondaryReplicationQueueSize |Uint, alapértelmezett érték 64 | Statikus |Ez az érték határozza meg a kezdeti mérete a tart fenn a replikálási műveletek a másodlagos várólista. Vegye figyelembe, hogy 2 szintűnek kell lennie. |
| MaxSecondaryReplicationQueueSize |Uint, alapértelmezett érték 16384 | Statikus |Ez az volt a másodlagos replikációs várólistában lévő létező műveletek maximális száma. Vegye figyelembe, hogy 2 szintűnek kell lennie. |
| MaxSecondaryReplicationQueueMemorySize |Uint, alapértelmezett érték a 0 | Statikus |Ez az a maximális érték a másodlagos replikációs sor bájtokban. |
| SecondaryClearAcknowledgedOperations |Logikai érték, alapértelmezett értéke "false" | Statikus |Logikai érték, amely szabályozza, hogy a műveletek a másodlagos replikátor törlődik, miután az elsődleges (a lemezre kiürített) vonatkozik, hogy. Beállítások a TRUE közben elfogja replikák fel egy feladatátvétel után az új elsődleges további lemezolvasások eredményezhet. |
| MaxMetadataSizeInKB |Int, alapértelmezett érték 4 |Nem engedélyezett|A napló adatfolyam metaadatai maximális méretét. |
| MaxRecordSizeInKB |Uint, alapértelmezett érték: 1024 |Nem engedélyezett| A naplóbejegyzés adatfolyam maximális mérete. |
| CheckpointThresholdInMB |Int, alapértelmezett érték 50 |Statikus|Ellenőrzőpont indul, amikor a napló-használat meghaladja ezt az értéket. |
| MaxAccumulatedBackupLogSizeInMB |Int, alapértelmezett érték a 800 |Statikus|Maximális mérete (MB) a megadott biztonságimásolat-naplólánccal rendelkeznek a biztonsági mentési naplók halmozott. Egy növekményes biztonsági mentési kérelem sikertelen lesz, ha a növekményes biztonsági mentés hoz létre egy biztonsági mentési napló, amelyek a halmozott biztonsági mentési naplók a lehet nagyobb, mint ez a méret kapcsolódó teljes biztonsági mentés óta. Ilyen esetben felhasználói teljes biztonsági mentés végrehajtása szükséges. |
| MaxWriteQueueDepthInKB |Int, alapértelmezett érték a 0 |Nem engedélyezett| Maximális int várólistamélység, az alapvető naplózó kilobájtban megadott is használhat az adott replikához társított napló írása. Ez az érték core naplózó frissítései közben lehet függőben lévő bájtok maximális száma. A core naplózó számítja ki a megfelelő érték 0 vagy a 4 többszöröse lehet. |
| SharedLogId |Karakterlánc |Nem engedélyezett|Megosztott napló azonosítóját. Ez egy GUID azonosítót, és minden megosztott napló egyedinek kell lennie. |
| SharedLogPath |Karakterlánc |Nem engedélyezett|A megosztott naplófájl elérési útja. Ha ez az érték üres az alapértelmezett megosztott napló szolgál. |
| SlowApiMonitoringDuration |Idő (másodpercben), az alapértelmezett érték 300 |Statikus| Adja meg a duration API figyelmeztetés állapotát az esemény előtt.|
| MinLogSizeInMB |Int, alapértelmezett érték a 0 |Statikus|A tranzakciós napló legkisebb méretét. A napló nem engedélyezett alatt ez a beállítás méretűre csonkolja. 0 azt jelenti, hogy a replikátor határozza meg a minimális naplóméret egyéb beállítások szerint. Az érték növelésével növeli a részleges példányszám és a növekményes biztonsági mentés óta veszélyét annak, hogy a megfelelő naplófájlok rekordok csonkolva lesz arányában ezt a lehetőséget. |

### <a name="section-name-fabricclient"></a>Szakasz Name: FabricClient
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| NodeAddresses |karakterlánc, alapértelmezett érték a "" |Statikus|Egy gyűjtemény címek (kapcsolati karakterláncok) való kommunikációhoz használható különböző csomópontokon a a szolgáltatás. Az ügyfél először kapcsolódik, a címek egyikének kiválasztásával véletlenszerűen. Ha egynél több kapcsolati karakterlánc van megadva, és egy kapcsolat egy kommunikációs vagy időtúllépési hiba; miatt sikertelen az ügyfél kapcsolók egymás után a következő cím használatára. Lásd: a elnevezési címét című szakaszban talál információt újrapróbálkozások szemantikáját próbálkozzon újra. |
| ConnectionInitializationTimeout |Idő (másodpercben), az alapértelmezett érték 2 |Dinamikus|Adja meg az időtartam másodpercben. Kapcsolat időkorlátja minden alkalommal ügyfél megkísérli az átjáró csatlakoztatásához. |
| PartitionLocationCacheLimit |Int, alapértelmezett érték 100000 |Statikus|A szolgáltatás feloldásához (Ha nincs korlát, 0 beállítása) gyorsítótárazott partíciók száma. |
| ServiceChangePollInterval |Idő (másodpercben), alapértelmezett érték a 120 |Dinamikus|Adja meg az időtartam másodpercben. A szolgáltatás egymást követő lekérdezések közötti távolság regisztrált szolgáltatás módosítási értesítést visszahívások átjáróján módosításait az ügyféltől. |
| KeepAliveIntervalInSeconds |Int, alapértelmezett érték 20 |Statikus|Az időköz, ahol a FabricClient átviteli életben tartási üzenetek küldése az átjáró. A 0; életben tartási le van tiltva. Pozitív értéknek kell lennie. |
| HealthOperationTimeout |Idő (másodpercben), alapértelmezett érték a 120 |Dinamikus|Adja meg az időtartam másodpercben. A Health Manager küldött jelentést tartalmazó üzenet időkorlátját. |
| HealthReportSendInterval |Idő (másodpercben), az alapértelmezett érték 30 |Dinamikus|Adja meg az időtartam másodpercben. A jelentéskészítési időközét küld halmozott állapotjelentések a Health Manager összetevő. |
| HealthReportRetrySendInterval |Idő (másodpercben), az alapértelmezett érték 30 |Dinamikus|Adja meg az időtartam másodpercben. A jelentéskészítési időközét az összetevő újra elküldi halmozott állapotjelentések száma az állapotkezelő. |
| RetryBackoffInterval |Idő (másodpercben), az alapértelmezett érték 3 |Dinamikus|Adja meg az időtartam másodpercben. A biztonsági ki időköz mielőtt megpróbálná megismételni a műveletet. |
| MaxFileSenderThreads |Uint, alapértelmezett érték 10 |Statikus|Párhuzamos átvitt fájlok maximális száma. |

### <a name="section-name-common"></a>Szakasz Name: közös
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| PerfMonitorInterval |Idő (másodpercben), az alapértelmezett érték 1 |Dinamikus|Adja meg az időtartam másodpercben. Teljesítmény figyelési időköz. Figyelési kikapcsolja 0 vagy negatív értékre. |

### <a name="section-name-healthmanager"></a>Szakasz Name: HealthManager
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |Logikai érték, alapértelmezett értéke "false" |Statikus|Fürt állapotházirend kiértékelése: egy alkalmazás típusa állapotának kiértékelését engedélyezése. |

### <a name="section-name-nodedomainids"></a>Szakasz Name: NodeDomainIds
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| UpgradeDomainId |karakterlánc, alapértelmezett érték a "" |Statikus|A frissítési tartomány egy csomópont tartozik ismerteti. |
| PropertyGroup |NodeFaultDomainIdCollection |Statikus|Ismerteti a tartalék tartományok egy csomópont tartozik. A tartalék tartomány URI, amely a datacenter csomópontjának helyét ismerteti keresztül van definiálva.  Tartalék tartomány URI formátumú vannak fd: / fd/URI elérésiút-szegmens követ.|

### <a name="section-name-nodeproperties"></a>Szakasz Name: NodeProperties
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| PropertyGroup |NodePropertyCollectionMap |Statikus|Csomópont tulajdonságai kulcs-érték pár karakterlánc gyűjteménye. |

### <a name="section-name-nodecapacities"></a>Szakasz Name: NodeCapacities
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| PropertyGroup |NodeCapacityCollectionMap |Statikus|Csomópont-kapacitás különböző metrikáihoz gyűjteménye. |

### <a name="section-name-fabricnode"></a>Szakasz Name: FabricNode
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| StateTraceInterval |Idő (másodpercben), az alapértelmezett érték 300 |Statikus|Adja meg az időtartam másodpercben. A nyomkövetés csomópont állapota minden egyes csomóponton, illetve a hierarchiában felfelé a FM/FMM csomópontjai intervallumát. |
| StartApplicationPortRange |Int, alapértelmezett érték a 0 |Statikus|Kezdő alrendszer üzemeltető által kezelt alkalmazás port. Szükséges, ha EndpointFilteringEnabled üzemeltetési igaz. |
| EndApplicationPortRange |Int, alapértelmezett érték a 0 |Statikus|A záró (nem a két szélsőértéket beleértve) alrendszer üzemeltető által kezelt alkalmazás port. Szükséges, ha EndpointFilteringEnabled üzemeltetési igaz. |
| ClusterX509StoreName |karakterlánc, alapértelmezett érték "A" |Dinamikus|Fürtön belüli kommunikáció biztonságához fürt tanúsítványt tartalmazó X.509 tanúsítvány tároló neve. |
| ClusterX509FindType |karakterlánc, alapértelmezett értéke "FindByThumbprint" |Dinamikus|Azt jelzi, hogyan keresse meg a ClusterX509StoreName támogatott értékek által meghatározott tárolóban fürt tanúsítvány: "FindByThumbprint"; "FindBySubjectName" a "FindBySubjectName"; Ha több egyező; a másikat a legtávolabbi lejárati szolgál. |
| ClusterX509FindValue |karakterlánc, alapértelmezett érték a "" |Dinamikus|Keresési szűrő értéknek fürt tanúsítvány kereséséhez használható. |
| ClusterX509FindValueSecondary |karakterlánc, alapértelmezett érték a "" |Dinamikus|Keresési szűrő értéknek fürt tanúsítvány kereséséhez használható. |
| ServerAuthX509StoreName |karakterlánc, alapértelmezett érték "A" |Dinamikus|X.509 tanúsítvány tároló, amely tartalmazza a kiszolgálói tanúsítvány entrée szolgáltatás neve. |
| ServerAuthX509FindType |karakterlánc, alapértelmezett értéke "FindByThumbprint" |Dinamikus|Azt jelzi, hogyan keresse meg a kiszolgálói tanúsítvány a tárolóban ServerAuthX509StoreName támogatott értéke által meghatározott: FindByThumbprint; FindBySubjectName. |
| ServerAuthX509FindValue |karakterlánc, alapértelmezett érték a "" |Dinamikus|Keresési szűrő értéket, keresse meg a kiszolgálói tanúsítványt. |
| ServerAuthX509FindValueSecondary |karakterlánc, alapértelmezett érték a "" |Dinamikus|Keresési szűrő értéket, keresse meg a kiszolgálói tanúsítványt. |
| ClientAuthX509StoreName |karakterlánc, alapértelmezett érték "A" |Dinamikus|Az alapértelmezett rendszergazdai szerepkörhöz FabricClient tanúsítványt tartalmazó X.509 tanúsítványtároló neve. |
| ClientAuthX509FindType |karakterlánc, alapértelmezett értéke "FindByThumbprint" |Dinamikus|Azt jelzi, hogyan keresse meg a ClientAuthX509StoreName támogatott értéke által meghatározott tárolóban tanúsítvány: FindByThumbprint; FindBySubjectName. |
| ClientAuthX509FindValue |karakterlánc, alapértelmezett érték a "" | Dinamikus|Az alapértelmezett rendszergazdai szerepkörhöz FabricClient tanúsítvány kereséséhez használható keresési szűrőérték. |
| ClientAuthX509FindValueSecondary |karakterlánc, alapértelmezett érték a "" |Dinamikus|Az alapértelmezett rendszergazdai szerepkörhöz FabricClient tanúsítvány kereséséhez használható keresési szűrőérték. |
| UserRoleClientX509StoreName |karakterlánc, alapértelmezett érték "A" |Dinamikus|Az X.509 tanúsítvány tároló, a tanúsítványt az alapértelmezett felhasználói szerepkörhöz FabricClient neve. |
| UserRoleClientX509FindType |karakterlánc, alapértelmezett értéke "FindByThumbprint" |Dinamikus|Azt jelzi, hogyan keresse meg a UserRoleClientX509StoreName támogatott értéke által meghatározott tárolóban tanúsítvány: FindByThumbprint; FindBySubjectName. |
| UserRoleClientX509FindValue |karakterlánc, alapértelmezett érték a "" |Dinamikus|Az alapértelmezett felhasználói szerepkörhöz FabricClient tanúsítvány kereséséhez használható keresési szűrőérték. |
| UserRoleClientX509FindValueSecondary |karakterlánc, alapértelmezett érték a "" |Dinamikus|Az alapértelmezett felhasználói szerepkörhöz FabricClient tanúsítvány kereséséhez használható keresési szűrőérték. |

### <a name="section-name-paas"></a>Szakasz Name: Paas
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| ClusterId |karakterlánc, alapértelmezett érték a "" |Nem engedélyezett|A konfiguráció védelmét háló által használt X509 tanúsítványtárolóból. |

### <a name="section-name-fabrichost"></a>Szakasz név: {a FabricHost
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| StopTimeout |Idő (másodpercben), az alapértelmezett érték 300 |Dinamikus|Adja meg az időtartam másodpercben. Az üzemeltetett szolgáltatás aktiváláshoz; időtúllépés az inaktiválást és frissítését. |
| StartTimeout |Idő (másodpercben), az alapértelmezett érték 300 |Dinamikus|Adja meg az időtartam másodpercben. A fabricactivationmanager indítása túllépi az időkorlátot. |
| ActivationRetryBackoffInterval |Idő (másodpercben), az alapértelmezett érték 5 |Dinamikus|Adja meg az időtartam másodpercben. Visszalépési időköz minden aktiválási hibájánál; minden folyamatos aktiválási hiba esetén a rendszer újra próbálkozik az aktiválás a akár a MaxActivationFailureCount. Az újrapróbálkozási időköz minden próbálkozásra folyamatos aktiválási hiba termék és az aktiválási vissza az indító időköz. |
| ActivationMaxRetryInterval |Idő (másodpercben), az alapértelmezett érték 300 |Dinamikus|Adja meg az időtartam másodpercben. Maximális újrapróbálkozási időköz az aktiváláshoz. Minden folyamatos hiba esetén az újrapróbálkozási időköz számítjuk Min (ActivationMaxRetryInterval; Folyamatos hibaszámláló * ActivationRetryBackoffInterval). |
| ActivationMaxFailureCount |Int, alapértelmezett érték 10 |Dinamikus|Ez az a maximális szám, amelynek a rendszer a sikertelen aktiválást, mielőtt megpróbálja. |
| EnableServiceFabricAutomaticUpdates |Logikai érték, alapértelmezett értéke "false" |Dinamikus|Ez az engedélyezi a háló frissítést a Windows Update segítségével. |
| EnableServiceFabricBaseUpgrade |Logikai érték, alapértelmezett értéke "false" |Dinamikus|Ez az alapszintű frissítési kiszolgáló engedélyezéséhez. |
| EnableRestartManagement |Logikai érték, alapértelmezett értéke "false" |Dinamikus|Ez a kiszolgáló újraindítása engedélyezéséhez. |


### <a name="section-name-failovermanager"></a>Szakasz Name: FailoverManager
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| UserReplicaRestartWaitDuration |Idő (másodpercben), az alapértelmezett érték 60.0 * 30 |Dinamikus|Adja meg az időtartam másodpercben. Ha egy megőrzött replikát leáll; A Windows Fabric megvárja, ennek az időtartamnak a replika elérni vissza új helyettesítő replikák (amelyek igényelnének állapotát másolatát) létrehozása előtt. |
| QuorumLossWaitDuration |Idő (másodpercben), alapértelmezett érték a MaxValue |Dinamikus|Adja meg az időtartam másodpercben. Ez az a maximális időtartam, amelyeknek a kvórum elvesztése állapotban partíció azt engedélyezi. Ha a partíció továbbra is a kvórum elvesztése után az ezen időtartam; a partíció helyre lett állítva a kvórum elvesztése a lefelé replikákat elveszett figyelembe véve. Vegye figyelembe, hogy ez potenciálisan járnak a adatvesztés. |
| UserStandByReplicaKeepDuration |Idő (másodpercben), az alapértelmezett érték 3600.0 * 24 * 7 |Dinamikus|Adja meg az időtartam másodpercben. Ha egy megőrzött replikát térjen vissza az alsó állapota; akkor lehet, hogy már helyett. Ez az időzítő meghatározza, hogy mennyi ideig az FM akkor is megtartja a készenléti replika törlésük előtt. |
| UserMaxStandByReplicaCount |Int, alapértelmezett érték 1 |Dinamikus|Az alapértelmezett maximális található StandBy replikák száma, amelyek a rendszer tartja a felhasználó számára. |
| ExpectedClusterSize|Int, alapértelmezett érték 1|Dinamikus|Ha a fürt először elindul. az FM megvárja, hogy ez sok csomópont helyezi el az egyéb szolgáltatások; megkezdése előtt jelentésének maguk mentése beleértve a rendszer-szolgáltatásokat, például elnevezési.  Az érték növelésével növeli a elindításához; fürt szükséges idő de megakadályozza, hogy az túlterheléséhez a korai csomópontokat, és a további helyezi át, amely szükséges, több csomópontot ismét online elérhető lesz.  Ez az érték általában néhány töredéke a fürtcsomópontok kezdeti méret kell megadni. |
|ClusterPauseThreshold|Int, alapértelmezett érték 1|Dinamikus|Ha ez alatt nyissa meg a rendszer a csomópontok száma érték majd elhelyezési; terheléselosztás; és feladatátvételi le van állítva. |
|TargetReplicaSetSize|Int, alapértelmezett érték 7|Nem engedélyezett|Ez az FM replikáit, megőrzi a Windows Fabric cél száma.  Minél nagyobb karakterkészletszámot FM adatok; nagyobb megbízhatóságot eredményez. az egy kis teljesítmény kompromisszumot. |
|MinReplicaSetSize|Int, alapértelmezett érték 3|Nem engedélyezett|Ez az a FM a replikakészlet minimális méretét.  Ha ez az érték alá süllyed aktív FM replikák száma az FM elvetése lesz a fürthöz, amíg legalább replikák minimális száma helyre lett állítva. |
|ReplicaRestartWaitDuration|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(60.0 * 30)|Nem engedélyezett|Adja meg az időtartam másodpercben. Ez az a FMService a ReplicaRestartWaitDuration |
|StandByReplicaKeepDuration|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(3600.0 * 24 * 7)|Nem engedélyezett|Adja meg az időtartam másodpercben. Ez az a FMService a StandByReplicaKeepDuration |
|PlacementConstraints|karakterlánc, alapértelmezett értéke L""|Nem engedélyezett|A feladatátvételi manager replikáinak bármely placement Constraints korlátozásokat |
|ExpectedNodeFabricUpgradeDuration|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(60.0 * 30)|Dinamikus|Adja meg az időtartam másodpercben. Ez egy csomópont frissítve lesz a várható időtartama a Windows Fabric frissítés során. |
|ExpectedReplicaUpgradeDuration|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(60.0 * 30)|Dinamikus|Adja meg az időtartam másodpercben. Ez az alkalmazásfrissítés során frissítendő csomóponton található összes replika várható időtartamát. |
|ExpectedNodeDeactivationDuration|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(60.0 * 30)|Dinamikus|Adja meg az időtartam másodpercben. Ez az a csomópont inaktiválása a befejezéséhez a várható időtartama. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|logikai érték, alapértelmezett értéke igaz|Dinamikus|Ha beállítása: igaz; a replikakészlet célméretének 1-replikák lesz engedélyezett a frissítés során. |
|ReconfigurationTimeLimit|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(300)|Dinamikus|Adja meg az időtartam másodpercben. A határidő újrakonfigurálás; amely után a figyelmeztetés állapotjelentése indul el |
|BuildReplicaTimeLimit|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(3600)|Dinamikus|Adja meg az időtartam másodpercben. Az időkorlát készítéséhez állapot-nyilvántartó replika; amely után a figyelmeztetés állapotjelentése indul el |
|CreateInstanceTimeLimit|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(300)|Dinamikus|Adja meg az időtartam másodpercben. A határidő; állapotmentes példány létrehozása amely után a figyelmeztetés állapotjelentése indul el |
|PlacementTimeLimit|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(600)|Dinamikus|Adja meg az időtartam másodpercben. A határidő elérésekor cél replikaszám; amely után a figyelmeztetés állapotjelentése indul el |

### <a name="section-name-namingservice"></a>Szakasz Name: NamingService
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Int, alapértelmezett érték 7 |Nem engedélyezett|A replika száma a szolgáltatás tároló minden partíció esetében állítja be. Replikakészletekhez számának növelésével növeli a szintű megbízhatóságot nyújtson az információk a Naming Service tárolóban; a változás, hogy az adatok elvesznek miatt csomóponthibák; csökkentése a Windows Fabric és ennyi idő alatt megnövekedett terhelés költségekkel tart az elnevezési adatok frissítését.|
|MinReplicaSetSize | Int, alapértelmezett érték 3 |Nem engedélyezett| A szolgáltatás replikák írni a frissítés befejezéséhez szükséges minimális száma. Ha a megadottnál kevesebb replikák aktív, a rendszer a megbízhatóság rendszer megtagadja a frissítések a Naming Service áruházra mutató replikák visszaállításáig. Ezt az értéket nem lehet több, mint a TargetReplicaSetSize. |
|ReplicaRestartWaitDuration | Idő (másodpercben), az alapértelmezett érték (60.0 * 30)|Nem engedélyezett| Adja meg az időtartam másodpercben. Ha a szolgáltatás replika leáll; Ez az időzítő elindul.  Amikor lejár a FM megkezdik cserélje le a replikákat, amely nem működnek (hogy még tartja őket elveszett). |
|QuorumLossWaitDuration | Idő (másodpercben), alapértelmezett érték a MaxValue |Nem engedélyezett| Adja meg az időtartam másodpercben. Ha a szolgáltatás lekérdezi a kvórum elvesztése; Ez az időzítő elindul.  Lejáratot követően a FM figyelembe veszi a lefelé replikákat elveszett; és megpróbálja helyreállítani a kvórum. Nem, hogy emiatt előfordulhat adatvesztés. |
|StandByReplicaKeepDuration | Idő (másodpercben), az alapértelmezett érték 3600.0 * 2 |Nem engedélyezett| Adja meg az időtartam másodpercben. Ha egy szolgáltatás replika térjen vissza az alsó állapota; akkor lehet, hogy már helyett.  Ez az időzítő meghatározza, hogy mennyi ideig az FM akkor is megtartja a készenléti replika törlésük előtt. |
|PlacementConstraints | karakterlánc, alapértelmezett érték a "" |Nem engedélyezett| A szolgáltatás elhelyezésének határértékét. |
|ServiceDescriptionCacheLimit | Int, alapértelmezett érték a 0 |Statikus| A gyorsítótárában LRU szolgáltatás leírása, a tároló szolgáltatás (Ha nincs korlát, 0 beállítása) fenntartott bejegyzések maximális száma. |
|RepairInterval | Idő (másodpercben), az alapértelmezett érték 5 |Statikus| Adja meg az időtartam másodpercben. Időköz, amelyben az elnevezési inkonzisztenciát javítsa ki a szolgáltató tulajdonos és a név tulajdonosának indul el. |
|MaxNamingServiceHealthReports | Int, alapértelmezett érték 10 |Dinamikus|Egy időben névhasználati tároló lassú műveletek maximális száma a szolgáltatás jelentések nem kifogástalan. Ha 0; minden lassú műveletek kerülnek. |
| MaxMessageSize |Int, alapértelmezett érték 4\*1024\*1024 |Statikus|Az üzenetek maximális méretét, az ügyfél-kommunikációhoz csomópont elnevezési használatakor. Szolgáltatásmegtagadási támadás enyhítése; alapértelmezett érték: 4MB. |
| MaxFileOperationTimeout |Idő (másodpercben), az alapértelmezett érték 30 |Dinamikus|Adja meg az időtartam másodpercben. A maximális időkorlátot fájl tárolási szolgáltatási művelet engedélyezett. Kérések megadó nagyobb időtúllépést a program elutasítja. |
| Konfigurált MaxOperationTimeout |Idő (másodpercben), az alapértelmezett érték 600 |Dinamikus|Adja meg az időtartam másodpercben. Az Ügyfélműveletek engedélyezett maximális időkorlátot. Kérések megadó nagyobb időtúllépést a program elutasítja. |
| MaxClientConnections |Int, alapértelmezett érték 1000 |Dinamikus|A maximálisan megengedett számú ügyfél kapcsolódik-átjárón. |
| ServiceNotificationTimeout |Idő (másodpercben), az alapértelmezett érték 30 |Dinamikus|Adja meg az időtartam másodpercben. Az időtúllépés használható, ha az ügyfél szolgáltatás értesítések kézbesítéséhez. |
| MaxOutstandingNotificationsPerClient |Int, alapértelmezett érték 1000 |Dinamikus|Az átjáró kényszerített lezár egy függőben lévő értesítések előtt egy ügyfél-regisztrációk maximális száma. |
| MaxIndexedEmptyPartitions |Int, alapértelmezett érték 1000 |Dinamikus|A maximális számát, amely a marad üres partíciók indexelt értesítési gyorsítótárba újracsatlakozására ügyfelek szinkronizálása. Ez a szám fent üres partíciókat növekvő keresési verzió sorrendben az indexből törlődni fog. Újracsatlakozás ügyfelek továbbra is szinkronizálja és kihagyott üres partíció frissítését; de a szinkronizálási protokoll drágább válik. |
| GatewayServiceDescriptionCacheLimit |Int, alapértelmezett érték a 0 |Statikus|Az elnevezési átjáróra (Ha nincs korlát, 0 beállítása) LRU szolgáltatás leírása gyorsítótár karbantartása bejegyzések maximális száma. |
| PartitionCount |Int, alapértelmezett érték 3 |Nem engedélyezett|A szolgáltatás a partíciók száma létre kell tárolni. Mindegyik partíció tulajdonában van egy partíciókulcsot, amely megfelel a indexe; Igen partíciókulcsok [0; PartitionCount) létezik. Növelje meg a szolgáltatás partíciók növeli a skála, amely a szolgáltatás hajthat végre mivel csökkenti a átlagos bármilyen biztonsági a replika által tárolt adatok megadása; a nagyobb erőforrás-felhasználás költségekkel (óta PartitionCount * ReplicaSetSize szolgáltatás replikák fenn kell tartani).|

### <a name="section-name-runas"></a>Szakasz Name: RunAs
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| Ezért |karakterlánc, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a Futtatás mint fiók nevét. Ez csak a "DomainUser" vagy "ManagedServiceAccount" fiókot a szükséges típus. Érvényes értékek: "tartomány\felhasználónév" vagy "user@domain". |
|RunAsAccountType|karakterlánc, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a futtató fiók típusa. Ez szükséges minden RunAs szakasz érvényes értékek: "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|Futtatási_jelszó|karakterlánc, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a RunAs fiók jelszavát. Ez csak a "DomainUser" fióktípus szükséges. |

### <a name="section-name-runasfabric"></a>Szakasz Name: RunAs_Fabric
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| Ezért |karakterlánc, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a Futtatás mint fiók nevét. Ez csak a "DomainUser" vagy "ManagedServiceAccount" fiókot a szükséges típus. Érvényes értékek: "tartomány\felhasználónév" vagy "user@domain". |
|RunAsAccountType|karakterlánc, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a futtató fiók típusa. Ez szükséges minden RunAs szakasz érvényes értékek: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|Futtatási_jelszó|karakterlánc, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a RunAs fiók jelszavát. Ez csak a "DomainUser" fióktípus szükséges. |

### <a name="section-name-runashttpgateway"></a>Szakasz Name: RunAs_HttpGateway
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| Ezért |karakterlánc, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a Futtatás mint fiók nevét. Ez csak a "DomainUser" vagy "ManagedServiceAccount" fiókot a szükséges típus. Érvényes értékek: "tartomány\felhasználónév" vagy "user@domain". |
|RunAsAccountType|karakterlánc, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a futtató fiók típusa. Ez szükséges minden RunAs szakasz érvényes értékek: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|Futtatási_jelszó|karakterlánc, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a RunAs fiók jelszavát. Ez csak a "DomainUser" fióktípus szükséges. |

### <a name="section-name-runasdca"></a>Szakasz Name: RunAs_DCA
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| Ezért |karakterlánc, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a Futtatás mint fiók nevét. Ez csak a "DomainUser" vagy "ManagedServiceAccount" fiókot a szükséges típus. Érvényes értékek: "tartomány\felhasználónév" vagy "user@domain". |
|RunAsAccountType|karakterlánc, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a futtató fiók típusa. Ez szükséges minden RunAs szakasz érvényes értékek: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|Futtatási_jelszó|karakterlánc, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a RunAs fiók jelszavát. Ez csak a "DomainUser" fióktípus szükséges. |

### <a name="section-name-httpgateway"></a>Szakasz Name: HttpGateway
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
|IsEnabled|Logikai érték, alapértelmezett értéke "false" |Statikus| Engedélyezi vagy letiltja a HttpGateway. HttpGateway alapértelmezés szerint le van tiltva. |
|ActiveListeners |Uint, alapértelmezett érték 50 |Statikus| Olvasási és küldje el a http-kiszolgáló várólista száma. Ez meghatározza, hogy tudja teljesíteni a HttpGateway egyidejű kérelmek száma. |
|MaxEntityBodySize |Uint, alapértelmezett érték 4194304 |Dinamikus|A maximális méretének, amelyek a HTTP-kérelem lehet számítani biztosít. Alapértelmezett érték: 4MB. Httpgateway meghiúsul kérelmet, ha a törzs mérete rendelkezik > ezt az értéket. Minimális olvasási adatrészlet mérete 4096 bájt. Ezért azt kell > = 4096. |
|HttpGatewayHealthReportSendInterval |Idő (másodpercben), az alapértelmezett érték 30 |Statikus|Adja meg az időtartam másodpercben. Az időköz, amelyen a Http-átjáró küld halmozott állapotának az állapotkezelő jelentéseket. |

### <a name="section-name-ktllogger"></a>Szakasz Name: KtlLogger
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, alapértelmezett érték 1 |Dinamikus|Az jelzőt, amely azt jelzi, ha a memória beállításait konfigurálni kell, hogy dinamikusan és automatikusan. Ha a nulla, majd a memória-konfigurációs beállítások közvetlenül használatosak, és ne változtassa meg a rendszer feltételek alapján. Ha egy ezután a memóriabeállításokat beállítása automatikusan megtörténik, és módosíthatja a rendszer feltételek alapján. |
|WriteBufferMemoryPoolMinimumInKB |Int, alapértelmezett érték 8388608 |Dinamikus|Kezdetben lefoglalni az írási puffer memóriakészletben KB száma. Az alábbi SharedLogSizeInMB kell lenniük 0 nincs alapértelmezett korlát jelzéséhez használja. |
|WriteBufferMemoryPoolMaximumInKB | Int, alapértelmezett érték a 0 |Dinamikus|Engedélyezi az írási puffer memóriakészletben nő, akár KB száma. 0 ezzel jelezheti nincs korlát. |
|MaximumDestagingWriteOutstandingInKB | Int, alapértelmezett érték a 0 |Dinamikus|Ahhoz, hogy a dedikált napló előre megosztott napló engedélyezéséhez KB száma. 0 ezzel jelezheti nincs korlát.
|SharedLogPath |karakterlánc, alapértelmezett érték a "" |Statikus|Helyezhető el a megosztott naplózási tároló helye elérési útját és nevét. Használjon "" az alapértelmezett elérési út a fabric adatgyökerében használatával. |
|SharedLogId |karakterlánc, alapértelmezett érték a "" |Statikus|Megosztott naplózási tároló egyedi GUID azonosítója. Használjon "" Ha a fabric adatgyökerében alapértelmezett elérési úttal. |
|SharedLogSizeInMB |Int, alapértelmezett érték 8192 |Statikus|A megosztott napló tárolóban lefoglalni MB száma. |

### <a name="section-name-applicationgatewayhttp"></a>Szakasz nevét: Alapú/Http
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
|IsEnabled |Logikai érték, alapértelmezett értéke "false" |Statikus| Engedélyezi vagy letiltja a HttpApplicationGateway. HttpApplicationGateway alapértelmezés szerint le van tiltva, és ebben a konfigurációban kell állítani az engedélyezéshez. |
|NumberOfParallelOperations | Uint, alapértelmezett érték 5000 |Statikus|Olvasási és küldje el a http-kiszolgáló várólista száma. Ez meghatározza, hogy tudja teljesíteni a HttpGateway egyidejű kérelmek száma. |
|DefaultHttpRequestTimeout |Idő másodpercben. alapértelmezett érték a 120 |Dinamikus|Adja meg az időtartam másodpercben.  Az alapértelmezett kérelem időtúllépése ad az a http-alkalmazás átjáró feldolgozott http-kérelmekre. |
|ResolveServiceBackoffInterval |Idő (másodpercben), az alapértelmezett érték 5 |Dinamikus|Adja meg az időtartam másodpercben.  Ad vissza az indító alapértelmezésben egy sikertelen újrapróbálkozás előtt oldja meg a szolgáltatási művelet. |
|BodyChunkSize |Uint, alapértelmezett érték 16384 |Dinamikus| A szervezet olvasásához használt bájt adatrészlet méretének ad. |
|GatewayAuthCredentialType |karakterlánc, alapértelmezett értéke "None" |Statikus| Meghatározza, hogy milyen típusú hitelesítő adatokat használni a http app átjáró végpont az érvényes értékek: "Nincs / X 509. |
|GatewayX509CertificateStoreName |karakterlánc, alapértelmezett érték "A" |Dinamikus| X.509 tanúsítvány tároló http app átjáró tanúsítványt tartalmazó neve. |
|GatewayX509CertificateFindType |karakterlánc, alapértelmezett értéke "FindByThumbprint" |Dinamikus| Azt jelzi, hogyan keresse meg a GatewayX509CertificateStoreName támogatott értéke által meghatározott tárolóban tanúsítvány: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | karakterlánc, alapértelmezett érték a "" |Dinamikus| A http-alkalmazás átjáró tanúsítvány kereséséhez használható keresési szűrőérték. Ezt a tanúsítványt a https-végponton van konfigurálva, és azonosító adatainak ellenőrzéséhez a az alkalmazás által a szolgáltatás szükség esetén is használható. Első; findvalue – keresése Ha nem létezik; FindValueSecondary keresése. |
|GatewayX509CertificateFindValueSecondary | karakterlánc, alapértelmezett érték a "" |Dinamikus|A http-alkalmazás átjáró tanúsítvány kereséséhez használható keresési szűrőérték. Ezt a tanúsítványt a https-végponton van konfigurálva, és azonosító adatainak ellenőrzéséhez a az alkalmazás által a szolgáltatás szükség esetén is használható. Első; findvalue – keresése Ha nem létezik; FindValueSecondary keresése.|
|HttpRequestConnectTimeout|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(5)|Dinamikus|Adja meg az időtartam másodpercben.  A connect timeout ad a http-alkalmazás átjáróról küldött http-kérelmekre.  |
|RemoveServiceResponseHeaders|karakterlánc, alapértelmezett értéke L "Date; Kiszolgáló"|Statikus|Pontosvessző / vesszővel tagolt listája, amely eltávolítja a szolgáltatás válasza; válaszfejlécek való továbbítása előtt az ügyfél. Ha ez be üres karakterláncra; a szolgáltatás által visszaadott összes fejléc átadni-van. Egytényezős ne írja felül a dátum és a kiszolgáló |
|ApplicationCertificateValidationPolicy|karakterlánc, alapértelmezett értéke L "None"|Statikus| ApplicationCertificateValidationPolicy: Nincs: érvényesíteni a kiszolgálói tanúsítvány; a kérelem sikertelen. ServiceCertificateThumbprints: Tekintse meg a config ServiceCertificateThumbprints a fordított proxy bíznak távoli tanúsítványok ujjlenyomatai vesszővel tagolt listája. ServiceCommonNameAndIssuer: Tekintse meg a a távoli Tanúsítványos a fordított proxy bíznak a tulajdonos neve és a kiállító ujjlenyomata ServiceCommonNameAndIssuer konfigurációját. |
|ServiceCertificateThumbprints|karakterlánc, alapértelmezett értéke L""|Dinamikus| |
|CrlCheckingFlag|uint, alapértelmezett értéke 0x40000000 |Dinamikus| Alkalmazás/kiszolgáló tanúsítványlánc érvényesítése; jelzők pl. CRL-ellenőrzés 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY beállítás 0-ra letiltja a CRL ellenőrzése teljes listáját támogatott értékek a CertGetCertificateChain dwFlags által dokumentált: http://msdn.microsoft.com/library/windows/desktop/aa376078 (v=VS.85).aspx megfelelő  |
|IgnoreCrlOfflineError|logikai érték, alapértelmezett értéke igaz|Dinamikus|E tanúsítvány-visszavonási listát offline hiba alkalmazás/kiszolgáló tanúsítványellenőrzés figyelmen kívül hagyása. |
|SecureOnlyMode|logikai érték, alapértelmezett értéke hamis|Dinamikus| SecureOnlyMode: true: fordított Proxy szolgáltatásokról, amelyek biztonságos végpontok közzététele csak továbbítja. hamis: fordított Proxy továbbíthatja a végpontok biztonságos vagy nem biztonságos kérelmeket.  |
|ForwardClientCertificate|logikai érték, alapértelmezett értéke hamis|Dinamikus| |

### <a name="section-name-applicationgatewayhttpservicecommonnameandissuer"></a>Szakasz nevét: Alapú/Http/ServiceCommonNameAndIssuer
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, alapértelmezés szerint nincs|Dinamikus|  |

### <a name="section-name-management"></a>Szakasz Name: kezelése
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| Előtaggal |SecureString |Statikus|A legfelső szintű Lemezképtárolóba kapcsolati karakterláncot. |
| ImageStoreMinimumTransferBPS | Int, alapértelmezett érték: 1024 |Dinamikus|A minimális átviteli sebesség a fürt és a Lemezképtárolóba között. Ez az érték határozza meg az időtúllépés, a külső Lemezképtárolóba elérésekor szolgál. Módosítsa ezt az értéket csak akkor, ha a fürt és a Lemezképtárolóba közötti késés magas, hogy a külső Lemezképtárolóba töltheti le a fürt további időt. |
|AzureStorageMaxWorkerThreads | Int, alapértelmezett érték 25 |Dinamikus|A párhuzamos munkaszálak maximális száma. |
|AzureStorageMaxConnections | Int, alapértelmezett érték 5000 |Dinamikus|Az azure storage létesített egyidejű kapcsolatok maximális száma. |
|AzureStorageOperationTimeout | Idő (másodpercben), az alapértelmezett érték 6000 |Dinamikus|Adja meg az időtartam másodpercben. Időtúllépés xstore művelet elvégzéséhez. |
|ImageCachingEnabled | Logikai érték, az alapértelmezett érték true |Statikus|Ez a konfiguráció lehetővé teszi, hogy, hogy engedélyezi vagy letiltja a gyorsítótárazást. |
|DisableChecksumValidation | Logikai érték, alapértelmezett értéke "false" |Statikus| Ez a konfiguráció lehetővé teszi engedélyezése vagy letiltása az ellenőrzőösszeg-érvényesítés alkalmazás kiépítése során. |
|DisableServerSideCopy | Logikai érték, alapértelmezett értéke "false" |Statikus|Ez a konfiguráció lehetővé teszi, vagy letilthatja a kiszolgálóoldali másolása a Lemezképtárolóba az alkalmazáscsomag alkalmazás kiépítése során. |

### <a name="section-name-healthmanagerclusterhealthpolicy"></a>Szakasz nevét: HealthManager/ClusterHealthPolicy
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| A ConsiderWarningAsError |Logikai érték, alapértelmezett értéke "false" |Statikus|Fürt állapotházirend kiértékelése: figyelmeztetések hibák tekintendők. |
| MaxPercentUnhealthyNodes | Int, alapértelmezett érték a 0 |Statikus|Állapotházirend értékelési fürt: a fürt lesz kifogástalan, megengedett maximális százalékát nem kifogástalan csomópontokat. |
| MaxPercentUnhealthyApplications | Int, alapértelmezett érték a 0 |Statikus|Állapotházirend értékelési fürt: a nem megfelelő alkalmazások maximális százalékát engedélyezett lesz kifogástalan, a fürt. |

### <a name="section-name-healthmanagerclusterupgradehealthpolicy"></a>Szakasz nevét: HealthManager/ClusterUpgradeHealthPolicy
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
|A MaxPercentDeltaUnhealthyNodes|Int, alapértelmezett érték 10|Statikus|A fürt frissítési állapotházirend értékelése: különbözeti nem kifogástalan csomópontokat maximális százalékát engedélyezett lesz kifogástalan, a fürt |
|Maxpercentupgradedomaindeltaunhealthynodes paraméterek hiányzó értékei|Int, alapértelmezett érték 15|Statikus|A fürt frissítési állapotházirend értékelése: a frissítési tartományok sérült csomópontok különbözeti maximális százalék engedélyezett lesz kifogástalan, a fürt |

### <a name="section-name-faultanalysisservice"></a>Szakasz Name: FaultAnalysisService
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Int, alapértelmezett érték a 0 |Nem engedélyezett|NOT_PLATFORM_UNIX_START FaultAnalysisService a TargetReplicaSetSize. |
| MinReplicaSetSize |Int, alapértelmezett érték a 0 |Nem engedélyezett|A MinReplicaSetSize FaultAnalysisService számára. |
| ReplicaRestartWaitDuration |Idő (másodpercben), az alapértelmezett érték 60 perc|Statikus|Adja meg az időtartam másodpercben. A FaultAnalysisService ReplicaRestartWaitDuration. |
| QuorumLossWaitDuration | Idő (másodpercben), alapértelmezett érték a MaxValue |Statikus|Adja meg az időtartam másodpercben. A FaultAnalysisService QuorumLossWaitDuration. |
| StandByReplicaKeepDuration| Idő (másodpercben), az alapértelmezett érték (60*24*7) perc |Statikus|Adja meg az időtartam másodpercben. A FaultAnalysisService StandByReplicaKeepDuration. |
| PlacementConstraints | karakterlánc, alapértelmezett érték a ""|Statikus| A FaultAnalysisService PlacementConstraints. |
| StoredActionCleanupIntervalInSeconds | Int, alapértelmezett érték 3600 |Statikus|Ez azért, hogy milyen gyakran törlődnek az áruházból.  Csak azokat a műveleteket, terminál állapotban; és az elvégzett legalább ezelőtt CompletedActionKeepDurationInSeconds lesz eltávolítva. |
| CompletedActionKeepDurationInSeconds | Int, alapértelmezett érték 604800 |Statikus| Ez körülbelül az mennyi ideig tartsa egy terminál állapotban lévő műveletek.  Ez is attól függ, StoredActionCleanupIntervalInSeconds; mivel a karbantartási feladat csak adott időközönként történik. 604800 érték 7 nap. |
| StoredChaosEventCleanupIntervalInSeconds | Int, alapértelmezett érték 3600 |Statikus|Ez az a tároló karbantartása; naplózni gyakoriságát. Ha az események száma legfeljebb 30000; a karbantartás fogja indítsa. |
|DataLossCheckWaitDurationInSeconds|Int, alapértelmezett érték 25|Statikus|A teljes időmennyiséget; másodpercben; hogy a rendszer megvárja adatvesztés megtörténjen-e.  Ha a StartPartitionDataLossAsync() api hívása belső használatos. |
|DataLossCheckPollIntervalInSeconds|int, alapértelmezett érték 5|Statikus|Ez az az idő a rendszer hajt végre, miközben az adatvesztés fordulhat elő, az ellenőrzések között.  Az adatok elvesztését szám ellenőrzött / belső iterációs hányszor DataLossCheckWaitDurationInSeconds ez van. |
|ReplicaDropWaitDurationInSeconds|int, alapértelmezett értéke 600|Statikus|Ezzel a paraméterrel az adatvesztés api metódus meghívásakor.  Meghatározza, mennyi ideig várjon a rendszer replika eltávolítása után szakadjon beolvasása rajta belső meghívni a replika. |

### <a name="section-name-filestoreservice"></a>Szakasz Name: FileStoreService
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| NamingOperationTimeout |Idő (másodpercben), az alapértelmezett érték 60 |Dinamikus|Adja meg az időtartam másodpercben. Elnevezési művelet végrehajtása időtúllépés. |
| QueryOperationTimeout | Idő (másodpercben), az alapértelmezett érték 60 |Dinamikus|Adja meg az időtartam másodpercben. A lekérdezési művelet végrehajtása időtúllépés. |
| MaxCopyOperationThreads | Uint, alapértelmezett érték a 0 |Dinamikus| A maximális számú párhuzamos fájl, amely másodlagos elsődleges másolhatja. "0" == magok száma. |
| MaxFileOperationThreads | Uint, alapértelmezett érték 100 |Statikus| Az elsődleges FileOperations (Copy/Move) végrehajtásához engedélyezett párhuzamos szálak maximális száma. "0" == magok száma. |
| MaxStoreOperations | Uint, alapértelmezett érték 4096 |Statikus|A maximális engedélyezett elsődleges párhuzamos tároló tranzakciós műveletek száma. "0" == magok száma. |
| MaxRequestProcessingThreads | Uint, alapértelmezett érték 200 |Statikus|A párhuzamos az elsődleges kérelmek feldolgozásához használható szálak maximális száma. "0" == magok száma. |
| MaxSecondaryFileCopyFailureThreshold | Uint, alapértelmezett érték 25|Dinamikus|Mielőtt a másodlagos fájl másolása újbóli próbálkozások maximális számának. |
| AnonymousAccessEnabled | Logikai érték, az alapértelmezett érték true |Statikus|A FileStoreService megosztások névtelen hozzáférés engedélyezése vagy letiltása. |
| PrimaryAccountType | karakterlánc, alapértelmezett érték a "" |Statikus|Az elsődleges osztja meg a rendszerbiztonsági tag való hozzáférés-vezérlési lista a FileStoreService AccountType. |
| PrimaryAccountUserName | karakterlánc, alapértelmezett érték a "" |Statikus|Az elsődleges fiók felhasználóneve, az egyszerű a hozzáférés-vezérlési lista a FileStoreService megosztja. |
| PrimaryAccountUserPassword | SecureString, az alapértelmezett érték üres |Statikus|Az egyszerű a hozzáférés-vezérlési lista elsődleges fiók jelszavát a FileStoreService megosztja. |
| PrimaryAccountNTLMPasswordSecret | SecureString, az alapértelmezett érték üres |Statikus| A jelszó titkos adatot, amely annak használja ugyanazt a jelszót jönnek létre, az NTLM-hitelesítés használata esetén. |
| PrimaryAccountNTLMX509StoreLocation | karakterlánc, alapértelmezett értéke "LocalMachine"|Statikus| A tárolási helye a X509 az NTLM-hitelesítés használata esetén a PrimaryAccountNTLMPasswordSecret a HMAC létrehozásához használt tanúsítvány. |
| PrimaryAccountNTLMX509StoreName | karakterlánc, alapértelmezett értéke "MY"|Statikus| A tároló neve a X509 NTLM-hitelesítés használata esetén a PrimaryAccountNTLMPasswordSecret a HMAC létrehozásához használt tanúsítvány. |
| PrimaryAccountNTLMX509Thumbprint | karakterlánc, alapértelmezett érték a ""|Statikus|A X509 ujjlenyomatát az NTLM-hitelesítés használata esetén a PrimaryAccountNTLMPasswordSecret a HMAC létrehozásához használt tanúsítvány. |
| SecondaryAccountType | karakterlánc, alapértelmezett érték a ""|Statikus| A másodlagos osztja meg a rendszerbiztonsági tag való hozzáférés-vezérlési lista a FileStoreService AccountType. |
| SecondaryAccountUserName | karakterlánc, alapértelmezett érték a ""| Statikus|A másodlagos fiók felhasználóneve, az egyszerű a hozzáférés-vezérlési lista a FileStoreService megosztja. |
| SecondaryAccountUserPassword | SecureString, az alapértelmezett érték üres |Statikus|A másodlagos fiók jelszavát a rendszerbiztonsági tag való hozzáférés-vezérlési lista a FileStoreService megosztja.  |
| SecondaryAccountNTLMPasswordSecret | SecureString, az alapértelmezett érték üres |Statikus| A jelszó titkos adatot, amely annak használja ugyanazt a jelszót jönnek létre, az NTLM-hitelesítés használata esetén. |
| SecondaryAccountNTLMX509StoreLocation | karakterlánc, alapértelmezett értéke "LocalMachine" |Statikus|A tárolási helye a X509 az NTLM-hitelesítés használata esetén a SecondaryAccountNTLMPasswordSecret a HMAC létrehozásához használt tanúsítvány. |
| SecondaryAccountNTLMX509StoreName | karakterlánc, alapértelmezett értéke "MY" |Statikus|A tároló neve a X509 NTLM-hitelesítés használata esetén a SecondaryAccountNTLMPasswordSecret a HMAC létrehozásához használt tanúsítvány. |
| SecondaryAccountNTLMX509Thumbprint | karakterlánc, alapértelmezett érték a ""| Statikus|A X509 ujjlenyomatát az NTLM-hitelesítés használata esetén a SecondaryAccountNTLMPasswordSecret a HMAC létrehozásához használt tanúsítvány. |
|CommonNameNtlmPasswordSecret|SecureString, alapértelmezett érték Common::SecureString(L"")| Statikus|A jelszó titkos adatot, amely annak használja ugyanazt a jelszót jönnek létre, ha NTLM-hitelesítéssel |
|CommonName1Ntlmx509StoreLocation|karakterlánc, alapértelmezett értéke L "LocalMachine"|Statikus|A tárolási helye a X509 az NTLM-hitelesítés használata esetén a CommonName1NtlmPasswordSecret a HMAC létrehozásához használt tanúsítvány |
|CommonName1Ntlmx509StoreName|karakterlánc, alapértelmezett értéke L "MY"| Statikus|A tároló neve a X509 NTLM-hitelesítés használata esetén a CommonName1NtlmPasswordSecret a HMAC létrehozásához használt tanúsítvány |
|CommonName1Ntlmx509CommonName|karakterlánc, alapértelmezett értéke L""|Statikus| A X509 köznapi neve az NTLM-hitelesítés használata esetén a CommonName1NtlmPasswordSecret a HMAC létrehozásához használt tanúsítvány |
|CommonName2Ntlmx509StoreLocation|karakterlánc, alapértelmezett értéke L "LocalMachine"| Statikus|A tárolási helye a X509 az NTLM-hitelesítés használata esetén a CommonName2NtlmPasswordSecret a HMAC létrehozásához használt tanúsítvány |
|CommonName2Ntlmx509StoreName|karakterlánc, alapértelmezett értéke L "MY"|Statikus| A tároló neve a X509 NTLM-hitelesítés használata esetén a CommonName2NtlmPasswordSecret a HMAC létrehozásához használt tanúsítvány |
|CommonName2Ntlmx509CommonName|karakterlánc, alapértelmezett értéke L""|Statikus|A X509 köznapi neve az NTLM-hitelesítés használata esetén a CommonName2NtlmPasswordSecret a HMAC létrehozásához használt tanúsítvány |

### <a name="section-name-imagestoreservice"></a>Szakasz Name: ImageStoreService
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| Engedélyezve |Logikai érték, alapértelmezett értéke "false" |Statikus|A ImageStoreService Enabled jelzőt. Alapértelmezett: hamis |
| TargetReplicaSetSize | Int, alapértelmezett érték 7 |Nem engedélyezett|A ImageStoreService TargetReplicaSetSize. |
| MinReplicaSetSize | Int, alapértelmezett érték 3 |Nem engedélyezett|A MinReplicaSetSize ImageStoreService számára. |
| ReplicaRestartWaitDuration | Idő (másodpercben), az alapértelmezett érték 60.0 * 30 |Statikus|Adja meg az időtartam másodpercben. A ImageStoreService ReplicaRestartWaitDuration. |
| QuorumLossWaitDuration | Idő (másodpercben), alapértelmezett érték a MaxValue |Statikus| Adja meg az időtartam másodpercben. A ImageStoreService QuorumLossWaitDuration. |
| StandByReplicaKeepDuration | Idő (másodpercben), az alapértelmezett érték 3600.0 * 2 |Statikus| Adja meg az időtartam másodpercben. A ImageStoreService StandByReplicaKeepDuration. |
| PlacementConstraints | karakterlánc, alapértelmezett érték a "" |Statikus| A ImageStoreService PlacementConstraints. |

### <a name="section-name-imagestoreclient"></a>Szakasz Name: ImageStoreClient
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| ClientUploadTimeout |Idő (másodpercben), az alapértelmezett érték 1800 |Dinamikus|Adja meg az időtartam másodpercben. Kép Store szolgáltatás legfelső szintű feltöltés kérelmek időtúllépési értéknek. |
| ClientCopyTimeout | Idő (másodpercben), az alapértelmezett érték 1800 |Dinamikus| Adja meg az időtartam másodpercben. A legfelső szintű kérelmet kép Store szolgáltatás időtúllépési értéknek. |
|ClientDownloadTimeout | Idő (másodpercben), az alapértelmezett érték 1800 |Dinamikus| Adja meg az időtartam másodpercben. Kép Store szolgáltatás legfelső szintű letöltési kérelmek időtúllépési értéknek. |
|ClientListTimeout | Idő (másodpercben), az alapértelmezett érték 600 |Dinamikus|Adja meg az időtartam másodpercben. Kép Store szolgáltatás legfelső szintű lista kérelmek időtúllépési értéknek. |
|ClientDefaultTimeout | Idő (másodpercben), alapértelmezett értéke 180 |Dinamikus| Adja meg az időtartam másodpercben. Minden nem feltöltés/nem letöltési kérelmek időtúllépési értékét (pl. létezik; törlése) lemezkép tárolási szolgáltatásba. |

### <a name="section-name-tokenvalidationservice"></a>Szakasz Name: TokenValidationService
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| Szolgáltatók |karakterlánc, alapértelmezett értéke "DSTS" |Statikus|Vesszővel elválasztott listáját jogkivonat érvényesítésére szolgáltatók engedélyezése (érvényes szolgáltatók a következők: DSTS; AAD-BEN). Jelenleg csak egyetlen szolgáltató bármikor engedélyezhető. |

### <a name="section-name-upgradeorchestrationservice"></a>Szakasz Name: UpgradeOrchestrationService
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Int, alapértelmezett érték a 0 |Nem engedélyezett|A UpgradeOrchestrationService TargetReplicaSetSize. |
| MinReplicaSetSize |Int, alapértelmezett érték a 0 |Nem engedélyezett|A MinReplicaSetSize UpgradeOrchestrationService számára.
| ReplicaRestartWaitDuration | Idő (másodpercben), az alapértelmezett érték 60 perc|Statikus| Adja meg az időtartam másodpercben. A UpgradeOrchestrationService ReplicaRestartWaitDuration. |
| QuorumLossWaitDuration | Idő (másodpercben), alapértelmezett érték a MaxValue |Statikus| Adja meg az időtartam másodpercben. A UpgradeOrchestrationService QuorumLossWaitDuration. |
| StandByReplicaKeepDuration | Idő (másodpercben), az alapértelmezett érték 60*24*7 perc |Statikus| Adja meg az időtartam másodpercben. A UpgradeOrchestrationService StandByReplicaKeepDuration. |
| PlacementConstraints | karakterlánc, alapértelmezett érték a "" |Statikus| A UpgradeOrchestrationService PlacementConstraints. |
| AutoupgradeEnabled | Logikai érték, az alapértelmezett érték true |Statikus| Automatikus lekérdezési és frissítési művelet a cél-állapot fájl alapján. |
| UpgradeApprovalRequired | Logikai érték, alapértelmezett értéke "false" | Statikus|Kód frissítés folytatása előtt rendszergazdai jóváhagyás megkövetelése, hogy a beállítás. |

### <a name="section-name-upgradeservice"></a>Szakasz Name: UpgradeService
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| PlacementConstraints |karakterlánc, alapértelmezett érték a "" |Nem engedélyezett|A frissítési szolgáltatás PlacementConstraints. |
| TargetReplicaSetSize | Int, alapértelmezett érték 3 |Nem engedélyezett| A UpgradeService TargetReplicaSetSize. |
| MinReplicaSetSize | Int, alapértelmezett érték a 2. régiója |Nem engedélyezett| A MinReplicaSetSize UpgradeService számára. |
| CoordinatorType | karakterlánc, alapértelmezett értéke "WUTest"|Nem engedélyezett|A UpgradeService CoordinatorType. |
| BaseUrl | karakterlánc, alapértelmezett érték a "" |Statikus|A UpgradeService BaseUrl. |
| ClusterId | karakterlánc, alapértelmezett érték a "" |Statikus|A UpgradeService ClusterId. |
| X509StoreName | karakterlánc, alapértelmezett érték "A"|Dinamikus|A UpgradeService X509StoreName. |
| X509StoreLocation | karakterlánc, alapértelmezett érték a "" |Dinamikus| A UpgradeService X509StoreLocation. |
| X509FindType | karakterlánc, alapértelmezett érték a ""|Dinamikus| A UpgradeService X509FindType. |
| X509FindValue | karakterlánc, alapértelmezett érték a "" |Dinamikus| A UpgradeService X509FindValue. |
| X509SecondaryFindValue | karakterlánc, alapértelmezett érték a "" |Dinamikus| A UpgradeService X509SecondaryFindValue. |
| OnlyBaseUpgrade | Logikai érték, alapértelmezett értéke "false" |Dinamikus|A UpgradeService OnlyBaseUpgrade. |
| TestCabFolder | karakterlánc, alapértelmezett érték a "" |Statikus| A UpgradeService TestCabFolder. |

### <a name="section-name-security"></a>Szakasz Name: biztonsági
| **A paraméter** | **Megengedett értékek** |**Frissítési házirend**| **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
|ClusterCredentialType|karakterlánc, alapértelmezett értéke L "None"|Nem engedélyezett|Azt jelzi, hogy a fürt védelmére vonatkoznak használandó biztonsági hitelesítő adatok típusát. Érvényes értékek: "Nincs/X509/Windows" |
|ServerAuthCredentialType|karakterlánc, alapértelmezett értéke L "None"|Statikus|Ahhoz, hogy a FabricClient és a fürt közötti kommunikáció védelméhez használandó biztonsági hitelesítő adatok típusát jelöli. Érvényes értékek: "Nincs/X509/Windows" |
|ClientRoleEnabled|logikai érték, alapértelmezett értéke hamis|Statikus|Azt jelzi, hogy ügyfél szerepkör engedélyezve van-e; Ha értéke igaz; ügyfelek rendelt szerepkörök letöltésük alapján. V2; engedélyezés, ez azt jelenti, hogy nem AdminClientCommonNames/AdminClientIdentities az ügyfél csak hajtható végre a csak olvasható műveletekhez. |
|ClusterCertThumbprints|karakterlánc, alapértelmezett értéke L""|Dinamikus|A fürt; csatlakoztathatják tanúsítványok ujjlenyomatai egy vesszővel tagolt listája. |
|ServerCertThumbprints|karakterlánc, alapértelmezett értéke L""|Dinamikus|Ügyfelek; felvegye a fürt által használt kiszolgálói tanúsítványok ujjlenyomatai az ügyfelek használják a fürt hitelesítéséhez. Egy vesszővel elválasztott listája legyen. |
|ClientCertThumbprints|karakterlánc, alapértelmezett értéke L""|Dinamikus|A fürt; felvegye ügyfelek által használt tanúsítványok ujjlenyomatai fürt használ, ez engedélyezi a bejövő kapcsolatot. Egy vesszővel elválasztott listája legyen. |
|AdminClientCertThumbprints|karakterlánc, alapértelmezett értéke L""|Dinamikus|A rendszergazda szerepkör ügyfelek által használt tanúsítványok ujjlenyomatai. Egy vesszővel elválasztott listája legyen. |
|CrlCheckingFlag|uint, alapértelmezett értéke 0x40000000|Dinamikus|Alapértelmezett tanúsítvány lánc érvényesítési jelző; felülbírálható összetevő-specifikus jelző; például a összevonási/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ CSAK a 0-ra a beállítás letiltja a teljes CRL-ellenőrzés a támogatott értékeinek listáján a CertGetCertificateChain dwFlags által dokumentált: http://msdn.microsoft.com/library/windows/desktop/aa376078 (v=VS.85).aspx megfelelő |
|IgnoreCrlOfflineError|logikai érték, alapértelmezett értéke hamis|Dinamikus|E figyelmen kívül hagyja CRL offline hiba, ha a kiszolgálóoldali ellenőrzi a bejövő ügyféltanúsítványok |
|IgnoreSvrCrlOfflineError|logikai érték, alapértelmezett értéke igaz|Dinamikus|E figyelmen kívül hagyja CRL offline hiba, ha az ügyféloldali ellenőrzi a bejövő kiszolgálótanúsítványok; alapértelmezett érték igaz. Támadások ellen, és a visszavont tanúsítványok szükséges veszélyeztetése DNS; nehezebb, mint a visszavont ügyféltanúsítványok. |
|CrlDisablePeriod|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromMinutes(15)|Dinamikus|Adja meg az időtartam másodpercben. Mennyi ideig CRL-ellenőrzés le van tiltva a megadott tanúsítvány után észlelt offline hiba; Ha a tanúsítvány-visszavonási listát offline hiba figyelmen kívül hagyható. |
|CrlOfflineHealthReportTtl|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromMinutes(1440)|Dinamikus|Adja meg az időtartam másodpercben. |
|CertificateHealthReportingInterval|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(3600 * 8)|Statikus|Adja meg az időtartam másodpercben. Adja meg a tanúsítvány állapotának jelentési; időközt alapértelmezett 8 óra; 0 a beállítás letiltja a tanúsítvány állapotának reporting |
|CertificateExpirySafetyMargin|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromMinutes(43200)|Statikus|Adja meg az időtartam másodpercben. Tanúsítvány lejáratának; ráhagyást tanúsítvány állapotfigyelő jelentés figyelmeztetési állapotra vált a OK szorosabb, mint a lejárati esetén. Alapértelmezett érték 30 nap. |
|ClientClaimAuthEnabled|logikai érték, alapértelmezett értéke hamis|Statikus|Azt jelzi, ha az igénylés-alapú hitelesítés engedélyezve van-e az ügyfeleken; ClientRoleEnabled beállítás true implicit módon állítja be. |
|ClientClaims|karakterlánc, alapértelmezett értéke L""|Dinamikus|Az összes lehetséges jogcím várt ügyfelekről az átjáró csatlakozik. Egy "Vagy" lista: ClaimsEntry || ClaimsEntry || ClaimsEntry... minden ClaimsEntry egy-egy "AND" lista: ClaimType = ClaimValue & & ClaimType = ClaimValue & & ClaimType = ClaimValue... |
|AdminClientClaims|karakterlánc, alapértelmezett értéke L""|Dinamikus|Az összes lehetséges jogcím felügyeleti ügyfelek; vár a felhasználónévként ClientClaims; Ez a lista belső lekérdezi a hozzáadandó ClientClaims; így nincs szükség ClientClaims bejegyzések is hozzáadhat. |
|ClusterSpn|karakterlánc, alapértelmezett értéke L""|Nem engedélyezett|Egyszerű szolgáltatásnév a fürt; Ha a háló egyetlen tartományi felhasználóként (gmsa-k/tartományi felhasználói fiók) futtatja. Az egyszerű szolgáltatásnév bérleti figyelők és a figyelők fabric.exe: összevonási figyelői; belső replikációs figyelői; futásidejű szolgáltatáshoz figyelőt és elnevezési átjáró figyelő. Ez kell üresen kell hagyni amikor háló fut számítógépfiókok; Ebben az esetben az ügyféloldali csatlakozás számítási figyelőjének SPN figyelő átviteli címről. |
|ClusterIdentities|karakterlánc, alapértelmezett értéke L""|Dinamikus|Windows identitások fürtcsomópontok; az fürt tagsági engedélyezési műveletekben használatos. Egy vesszővel elválasztott listában; minden egyes bejegyzés nem egy tartományfiók-neve vagy a csoport neve |
|ClientIdentities|karakterlánc, alapértelmezett értéke L""|Dinamikus|Windows-identitást a FabricClient; elnevezési átjáró használja ahhoz, hogy engedélyezze a bejövő kapcsolatot. Egy vesszővel elválasztott listában; mindegyik bejegyzés egy tartományi fiók neve vagy a csoport nevét. Kényelmi célokat szolgál; a fabric.exe futtatásához használt fióknak automatikusan engedélyezett; ezért a rendszer csoport ServiceFabricAllowedUsers és ServiceFabricAdministrators. |
|AdminClientIdentities|karakterlánc, alapértelmezett értéke L""|Dinamikus|Windows identitások háló ügyfele rendszergazdai szerepkörrel; használt rendszerjogosultságú háló műveletek engedélyezéséhez. Egy vesszővel elválasztott listában; mindegyik bejegyzés egy tartományi fiók neve vagy a csoport nevét. Kényelmi célokat szolgál; a fabric.exe futtató fiók automatikusan rendszergazdai szerepkörrel; Ezért van csoport ServiceFabricAdministrators. |
|AADTenantId|karakterlánc, alapértelmezett értéke L""|Statikus|Bérlő azonosítója (GUID) |
|AADClusterApplication|karakterlánc, alapértelmezett értéke L""|Statikus|Webes API-alkalmazás nevét vagy a fürt jelölő azonosító |
|AADClientApplication|karakterlánc, alapértelmezett értéke L""|Statikus|Natív ügyfél neve vagy a háló ügyfelek jelölő azonosító |
|X509Folder|karakterlánc, alapértelmezett /var/lib/waagent:|Statikus|Mappa ahol X509 tanúsítványok és titkos kulcsok találhatók |
|FabricHostSpn| karakterlánc, alapértelmezett értéke L"" |Statikus| {A FabricHost; egyszerű szolgáltatásnév Ha a háló (gmsa-k/tartományi felhasználói fiók) egyetlen tartományi felhasználóként fut, és a Hálóbeli fut, a számítógépfiókot. A Hálóbeli; az egyszerű Szolgáltatásnevet az IPC-figyelő amely alapértelmezés szerint kell üresen kell hagyni óta Hálóbeli gép fiók alatt fut |
|DisableFirewallRuleForPublicProfile| logikai érték, alapértelmezett értéke igaz | Statikus|Azt jelzi, ha a tűzfalszabály engedélyeznie kell a nyilvános profil |
|DisableFirewallRuleForPrivateProfile| logikai érték, alapértelmezett értéke igaz |Statikus| Azt jelzi, ha az tűzfalszabály nem engedélyezhető a magánhálózati profil | 
|DisableFirewallRuleForDomainProfile| logikai érték, alapértelmezett értéke igaz |Statikus| Azt jelzi, ha az tűzfalszabály nem engedélyezhető a tartományi profilhoz |
|SettingsX509StoreName| karakterlánc, alapértelmezett értéke L "MY"| Dinamikus|A konfiguráció védelmét háló által használt X509 tanúsítványtárolóból |

### <a name="section-name-securityadminclientx509names"></a>Szakasz nevét: Biztonsági/AdminClientX509Names
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, alapértelmezés szerint nincs|Dinamikus| |

### <a name="section-name-securityclientx509names"></a>Szakasz nevét: Biztonsági/ClientX509Names
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
PropertyGroup|X509NameMap, alapértelmezés szerint nincs|Dinamikus| |

### <a name="section-name-securityclusterx509names"></a>Szakasz nevét: Biztonsági/ClusterX509Names
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
PropertyGroup|X509NameMap, alapértelmezés szerint nincs|Dinamikus| |

### <a name="section-name-securityserverx509names"></a>Szakasz nevét: Biztonsági/ServerX509Names
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
PropertyGroup|X509NameMap, alapértelmezés szerint nincs|Dinamikus| |

### <a name="section-name-securityclientaccess"></a>Szakasz nevét: Biztonsági/ClientAccess
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| CreateName |karakterlánc, alapértelmezett az "Admin" |Dinamikus|Biztonsági beállítások a elnevezési URI létrehozásához. |
| DeleteName |karakterlánc, alapértelmezett az "Admin" |Dinamikus|Biztonsági beállítások elnevezési URI törlésre. |
| PropertyWriteBatch |karakterlánc, alapértelmezett az "Admin" |Dinamikus|Biztonsági beállításokkal elnevezési tulajdonság írási műveleteket. |
| CreateService |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások a szolgáltatások létrehozásához. |
| CreateServiceFromTemplate |karakterlánc, alapértelmezett az "Admin" |Dinamikus|Szolgáltatás létrehozása sablonból biztonsági beállításainak konfigurálása. |
| Updateservice függvényhez |karakterlánc, alapértelmezett az "Admin" |Dinamikus|Biztonsági beállítások a szolgáltatásfrissítéseket. |
| DeleteService  |karakterlánc, alapértelmezett az "Admin" |Dinamikus|Biztonsági beállítások a szolgáltatás törlésre. |
| ProvisionApplicationType |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások alkalmazás típusa üzembe helyezéséhez. |
| CreateApplication |karakterlánc, alapértelmezett az "Admin" | Dinamikus|Alkalmazás létrehozása biztonsági beállításainak konfigurálása. |
| Deleteapplication függvényhez |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások az alkalmazás törléséhez. |
| UpgradeApplication |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások elindításáról és alkalmazásfrissítések megszakítása. |
| RollbackApplicationUpgrade |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Alkalmazásfrissítések visszaállítása biztonsági konfigurációját. |
| UnprovisionApplicationType |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Alkalmazás típus leépítése biztonsági konfigurációját. |
| MoveNextUpgradeDomain |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások az alkalmazásfrissítések explicit frissítési tartománnyal folytatása. |
| ReportUpgradeHealth |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Alkalmazásfrissítések az aktuális frissítési állapotát újrakezdése biztonsági konfigurációját. |
| ReportHealth |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállításainak konfigurálása állapotát. |
| ProvisionFabric |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások MSI-fájl és/vagy a fürt jegyzékének történő üzembe helyezéséhez. |
| UpgradeFabric |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások fürt frissítések elindító. |
| RollbackFabricUpgrade |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások a fürt frissítési visszaállítása. |
| UnprovisionFabric |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások a MSI-fájl és/vagy a fürt jegyzékének kiépítés megszüntetésére. |
| MoveNextFabricUpgradeDomain |karakterlánc, alapértelmezett az "Admin" |Dinamikus| A fürt frissítési Folytatás egy külön frissítési tartomány a biztonsági beállítások. |
| ReportFabricUpgradeHealth |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások a fürt frissítések az aktuális frissítési folyamat folytatása. |
| StartInfrastructureTask |karakterlánc, alapértelmezett az "Admin" | Dinamikus|Biztonsági beállítások az infrastruktúra-feladatok elindítása. |
| FinishInfrastructureTask |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások a befejezési infrastruktúra feladatokhoz. |
| ActivateNode |karakterlánc, alapértelmezett az "Admin" |Dinamikus| A csomópont aktiválási biztonsági beállításainak konfigurálása. |
| DeactivateNode |karakterlánc, alapértelmezett az "Admin" |Dinamikus| A csomópont inaktiválása biztonsági konfigurációját. |
| DeactivateNodesBatch |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Több csomópont inaktiválása biztonsági konfigurációját. |
| RemoveNodeDeactivations |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Több csomóponton visszaállítási inaktiválási biztonsági beállításainak konfigurálása. |
| GetNodeDeactivationStatus |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások az inaktiválást állapotának ellenőrzése. |
| NodeStateRemoved |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállításainak konfigurálása csomópont állapota eltávolítva. |
| RecoverPartition |karakterlánc, alapértelmezett az "Admin" | Dinamikus|Biztonsági beállítások partíció helyreállításához. |
| RecoverPartitions |karakterlánc, alapértelmezett az "Admin" | Dinamikus|Biztonsági beállítások partíciók helyreállításához. |
| RecoverServicePartitions |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások szolgáltatáspartíciók helyreállításához. |
| RecoverSystemPartitions |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások rendszer szolgáltatáspartíciók helyreállításához. |
| ReportFault |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállításainak konfigurálása hiba. |
| InvokeInfrastructureCommand |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások az infrastruktúra feladat parancsok. |
| FileContent |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások lemezkép ügyfél fájlátvitel (külső fürthöz) tárolja. |
| FileDownload |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások a lemezképet tároló ügyfél fájl letöltése kezdeményezés (külső fürthöz). |
| InternalList |karakterlánc, alapértelmezett az "Admin" | Dinamikus|Biztonsági beállítások lemezkép ügyfél fájl list művelet (belső) tárolja. |
| Törlés |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállításokkal lemezkép tárolásához ügyfél törlési művelet. |
| Feltöltés |karakterlánc, alapértelmezett az "Admin" | Dinamikus|Biztonsági beállítások lemezkép tárolásához ügyfél feltöltési művelete. |
| GetStagingLocation |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások lemezkép átmeneti helyre lekérés ügyfél tárolja. |
| GetStoreLocation |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások lemezkép ügyfél tároló helye lekérése tárolja. |
| NodeControl |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások indítása; leállítása; és a csomópontok újraindítását. |
| CodePackageControl |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások kód csomagok újraindításához. |
| UnreliableTransportControl |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Nem megbízható átviteli hozzáadása és eltávolítása a viselkedés a. |
| MoveReplicaControl |karakterlánc, alapértelmezett az "Admin" | Dinamikus|Helyezze át a replikát. |
| PredeployPackageToNode |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Telepítés előtti api. |
| StartPartitionDataLoss |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Partíciók adatvesztés kapott. |
| StartPartitionQuorumLoss |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Partíciók a kvórum elvesztése kapott. |
| StartPartitionRestart |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Egyidejűleg újraindítja vagy egésze a replikák partíció. |
| CancelTestCommand |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Egy adott TestCommand - megszakítja a felhőszolgáltató közötti átviteléhez esetén. |
| StartChaos |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Chaos - elindul, ha nincs elindítva. |
| StopChaos |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Leállítja Chaos - elindítása. |
| StartNodeTransition |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások csomópont átmenet indításához. |
| StartClusterConfigurationUpgrade |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Kapott StartClusterConfigurationUpgrade partícióra. |
| GetUpgradesPendingApproval |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Kapott GetUpgradesPendingApproval partícióra. |
| StartApprovedUpgrades |karakterlánc, alapértelmezett az "Admin" |Dinamikus| Kapott StartApprovedUpgrades partícióra. |
| Ping |karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" |Dinamikus| Ügyfél ping-üzenetek biztonsági beállításainak konfigurálása. |
| Lekérdezés |karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" |Dinamikus| Biztonsági beállítások a lekérdezések. |
| NameExists |karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" | Dinamikus|A biztonsági konfiguráció elnevezési URI meglétét ellenőrzi. |
| EnumerateSubnames |karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" |Dinamikus| Biztonsági beállítások elnevezési URI számbavétel. |
| EnumerateProperties |karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" | Dinamikus|Biztonsági beállítások elnevezési tulajdonság enumerálása. |
| PropertyReadBatch |karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" |Dinamikus| Biztonsági beállítások elnevezési tulajdonság olvasási műveleteket. |
| GetServiceDescription |karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" |Dinamikus| Biztonsági beállítások szolgáltatás hosszú-lekérdezési értesítéseket és a szolgáltatások ismertetése olvasásakor. |
| ResolveService |karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" |Dinamikus| Biztonsági beállítások a megfelelő szolgáltatásra feloldásához. |
| ResolveNameOwner |karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" | Dinamikus|Biztonsági beállítások kapcsolatos elnevezési URI tulajdonosa. |
| ResolvePartition |karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" | Dinamikus|Biztonsági beállítások rendszerszolgáltatások megoldása. |
| ServiceNotifications |karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" |Dinamikus| Biztonsági beállítások eseményalapú szolgáltatási értesítésekhez. |
| PrefixResolveService |karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" |Dinamikus| Biztonsági beállítások a megfelelő szolgáltatásra előtag feloldásához. |
| GetUpgradeStatus |karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" |Dinamikus| Biztonsági beállítások a lekérdezés alkalmazás frissítési állapot. |
| GetFabricUpgradeStatus |karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" |Dinamikus| Biztonsági beállítások a lekérdezés fürt frissítési állapot. |
| InvokeInfrastructureQuery |karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" | Dinamikus|Biztonsági beállítások infrastruktúrakezelési feladatok lekérdezése. |
| Lista |karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" | Dinamikus|Biztonsági beállítások lemezkép ügyfél fájl list művelet tárolja. |
| Resetpartitionload függvényhez |karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" |Dinamikus| Egy failoverUnit alaphelyzetbe állítása betöltésének biztonsági beállításainak konfigurálása. |
| ToggleVerboseServicePlacementHealthReporting | karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" |Dinamikus| Részletes ServicePlacement HealthReporting való átváltással biztonsági beállításainak konfigurálása. |
| GetPartitionDataLossProgress | karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" | Dinamikus|Lekéri a folyamatban lévő adatok elvesztését invoke api-hívások. |
| GetPartitionQuorumLossProgress | karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" |Dinamikus| Lekéri a kvórum elvesztése invoke api-hívások folyamatban van. |
| GetPartitionRestartProgress | karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" |Dinamikus| Lekéri a folyamatban van egy újraindítás partíció api-hívások. |
| GetChaosReport | karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" |Dinamikus| Lekéri egy adott időtartományban Chaos állapotát. |
| GetNodeTransitionProgress | karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" |Dinamikus| Folyamatban van a csomópont átmenet parancs helyezésének biztonsági konfigurációját. |
| GetClusterConfigurationUpgradeStatus | karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" |Dinamikus| Kapott GetClusterConfigurationUpgradeStatus partícióra. |
| GetClusterConfiguration | karakterlánc, alapértelmezett értéke "Admin\|\|Felhasználó" | Dinamikus|Kapott GetClusterConfiguration partícióra. |
|CreateComposeDeployment|karakterlánc, alapértelmezett értéke L "Rendszergazda"| Dinamikus|Létrehoz egy új központi telepítési compose fájlok szerint |
|DeleteComposeDeployment|karakterlánc, alapértelmezett értéke L "Rendszergazda"| Dinamikus|Az új üzemelő példány törlése |
|UpgradeComposeDeployment|karakterlánc, alapértelmezett értéke L "Rendszergazda"| Dinamikus|Az új üzemelő példány frissítése |
|ResolveSystemService|karakterlánc, alapértelmezett L "Admin\:|\|Felhasználó"|Dinamikus| Biztonsági beállítások rendszerszolgáltatások megoldása |
|GetUpgradeOrchestrationServiceState|karakterlánc, alapértelmezett értéke L "Rendszergazda"| Dinamikus|GetUpgradeOrchestrationServiceState kapott partíciók |
|SetUpgradeOrchestrationServiceState|karakterlánc, alapértelmezett értéke L "Rendszergazda"| Dinamikus|SetUpgradeOrchestrationServiceState kapott partíciók |

### <a name="section-name-reconfigurationagent"></a>Szakasz Name: ReconfigurationAgent
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| ApplicationUpgradeMaxReplicaCloseDuration | Idő (másodpercben), alapértelmezett érték a 900 |Dinamikus|Adja meg az időtartam másodpercben. Az időtartam, amelynek a rendszer vár, mielőtt leállítja az üzemeltetett szolgáltatások azzal replikák rendelkező mappában zárja be az alkalmazás frissítése során.|
| ServiceApiHealthDuration | Idő (másodpercben), alapértelmezett értéke 30 perc |Dinamikus| Adja meg az időtartam másodpercben. ServiceApiHealthDuration határozza meg, hogy mennyi ideig várunk egy service API futhat, mielőtt azt jelentse be a nem megfelelő. |
| ServiceReconfigurationApiHealthDuration | Idő (másodpercben), az alapértelmezett érték 30 |Dinamikus| Adja meg az időtartam másodpercben. ServiceReconfigurationApiHealthDuration határozza meg, mennyi ideig várunk egy service API futhat, mielőtt azt a jelentést nem kifogástalan. Ez az API-hívásokban elérhetőségére vonatkozik.|
| PeriodicApiSlowTraceInterval | Idő (másodpercben), az alapértelmezett érték 5 perc |Dinamikus| Adja meg az időtartam másodpercben. PeriodicApiSlowTraceInterval az időköz, amelyben lassú API-hívásokat fog szerepelnie az API-figyelő által határozza meg. |
| NodeDeactivationMaxReplicaCloseDuration | Idő (másodpercben), alapértelmezett érték a 900 |Dinamikus|Adja meg az időtartam másodpercben. Az időtartam, amelynek a rendszer vár, mielőtt leállítja az üzemeltetett szolgáltatások azzal replikák rendelkező mappában zárja be a csomópont inaktiválása során. |
| FabricUpgradeMaxReplicaCloseDuration | Idő (másodpercben), alapértelmezett érték a 900 |Dinamikus| Adja meg az időtartam másodpercben. Az időtartam, amelynek a rendszer vár, mielőtt leállítja az üzemeltetett szolgáltatások azzal replikák rendelkező mappában zárja be a fabric frissítés során. |
|GracefulReplicaShutdownMaxDuration|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus|Adja meg az időtartam másodpercben. Zárja be az az időtartam, amelynek a rendszer vár, mielőtt leállítja az üzemeltetett szolgáltatások azzal replikák rendelkező mappában. Ha ez az érték 0, replikák nem utasítani fogja bezárásához.|
|ReplicaChangeRoleFailureRestartThreshold|Int, alapértelmezett érték 10|Dinamikus| Egész szám. Adja meg az API-hibák száma elteltével automatikusan-megoldás művelet (replika újraindítás) alkalmazandó elsődleges előléptetés során. |
|ReplicaChangeRoleFailureWarningReportThreshold|int, alapértelmezett érték 2147483647|Dinamikus| Egész szám. Adja meg az API-hibák száma elteltével állapotjelentése figyelmeztetés generál elsődleges előléptetés során.|

### <a name="section-name-placementandloadbalancing"></a>Szakasz Name: PlacementAndLoadBalancing
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| TraceCRMReasons |Logikai érték, az alapértelmezett érték true |Dinamikus|Megadja, hogy nyomon követésére, a működési események csatorna típusú áthelyezések kiadott CRM okát. |
| ValidatePlacementConstraint | Logikai érték, az alapértelmezett érték true |Dinamikus| Itt adhatja meg, függetlenül attól, a szolgáltatás PlacementConstraint kifejezést van hitelesítve, amikor frissül egy szolgáltatás ServiceDescription leírásban. |
| PlacementConstraintValidationCacheSize | Int, az alapértelmezett beállítás 10000 |Dinamikus| A tábla gyors érvényesítésének és elhelyezési korlátozás kifejezések gyorsítótár mérete korlátozza. |
|VerboseHealthReportLimit | Int, alapértelmezett érték 20 | Dinamikus|Meghatározza a szám, ahányszor egy replika rendelkezik olyan helyezett ugorhat, mielőtt egy állapotfigyelési figyelmeztetése azt jelentette, (Ha részletes állapotfigyelő reporting engedélyezve van). |
|ConstraintViolationHealthReportLimit | Int, alapértelmezett érték 50 |Dinamikus| A szám, ahányszor korlátozás megsértése replika kifejezetten tartósan megoldatlan diagnosztika végzik, és a rendszerállapot-jelentéseket kibocsátott előtt határozza meg. |
|DetailedConstraintViolationHealthReportLimit | Int, alapértelmezett érték 200 |Dinamikus| A szám, ahányszor korlátozás megsértése replika kifejezetten tartósan megoldatlan előtt diagnosztika végzik, és a jelentések kibocsátott állapotának részletes meghatározása. |
|DetailedVerboseHealthReportLimit | Int, alapértelmezett érték 200 | Dinamikus|A szám, ahányszor egy helyezett replika kifejezetten tartósan helyezett előtt részletes állapotjelentések kibocsátott határozza meg. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, alapértelmezett érték 20 | Dinamikus|Meghatározza, hogy hány egymást követő alkalommal előtt végzik a diagnosztika és állapotfigyelő figyelmeztetések kibocsátott eldobott áthelyezések ResourceBalancer által kiállított. Negatív: Nincs figyelmeztetések kibocsátott azzal a feltétellel. |
|DetailedNodeListLimit | Int, alapértelmezett érték 15 |Dinamikus| Meghatározza a korlátozást a előtt a helyezett replika jelentések csonkolása csomópontok számát. |
|DetailedPartitionListLimit | Int, alapértelmezett érték 15 |Dinamikus| A diagnosztika csonkolása előtt felvenni megkötés diagnosztikai bejegyzésenként partíciók száma határozza meg. |
|DetailedDiagnosticsInfoListLimit | Int, alapértelmezett érték 15 |Dinamikus| Határozza meg a számú diagnosztikai bejegyzést (részletes adatokkal) / korlátozást a diagnosztika a csonkolási előtt.|
|PLBRefreshGap | Idő (másodpercben), az alapértelmezett érték 1 |Dinamikus| Adja meg az időtartam másodpercben. Határozza meg a minimális, hogy mennyi ideig kell telnie PLB újra frissíti az állapotot. |
|MinPlacementInterval | Idő (másodpercben), az alapértelmezett érték 1 |Dinamikus| Adja meg az időtartam másodpercben. Határozza meg a minimális, hogy mennyi ideig két egymást követő elhelyezési kerekítés előtt meg kell felelnie. |
|MinConstraintCheckInterval | Idő (másodpercben), az alapértelmezett érték 1 |Dinamikus| Adja meg az időtartam másodpercben. Határozza meg a minimális, hogy mennyi ideig kell telnie két egymást követő megkötés kerekítés ellenőrzése. |
|MinLoadBalancingInterval | Idő (másodpercben), az alapértelmezett érték 5 |Dinamikus| Adja meg az időtartam másodpercben. Határozza meg a minimális, hogy mennyi ideig két egymást követő terheléselosztási kerekítés előtt meg kell felelnie. |
|BalancingDelayAfterNodeDown | Idő (másodpercben), alapértelmezett érték a 120 |Dinamikus|Adja meg az időtartam másodpercben. Nem indulnak el tevékenységek terheléselosztás ez idő alatt a csomópont esemény után. |
|BalancingDelayAfterNewNode | Idő (másodpercben), alapértelmezett érték a 120 |Dinamikus|Adja meg az időtartam másodpercben. Nem indulnak el tevékenységeket végző új csomópont hozzáadása után ez idő alatt. |
|ConstraintFixPartialDelayAfterNodeDown | Idő (másodpercben), alapértelmezett érték a 120 |Dinamikus| Adja meg az időtartam másodpercben. Hajtsa végre a nem Fix FaultDomain és UpgradeDomain korlátozások megsértésének ez idő alatt a csomópont esemény után. |
|ConstraintFixPartialDelayAfterNewNode | Idő (másodpercben), alapértelmezett érték a 120 |Dinamikus| Adja meg az időtartam másodpercben. DDo FaultDomain nem javítsa ki és UpgradeDomain korlátozások megsértésének új csomópont hozzáadása után ez idő alatt. |
|GlobalMovementThrottleThreshold | Uint, alapértelmezett érték 1000 |Dinamikus| Az elmúlt időszakban GlobalMovementThrottleCountingInterval jelölik a terheléselosztás fázisban engedélyezett áthelyezések maximális száma. |
|GlobalMovementThrottleThresholdForPlacement | Uint, alapértelmezett érték a 0 |Dinamikus| Maximális száma az elmúlt időszakban GlobalMovementThrottleCountingInterval.0 által jelzett elhelyezési fázisban engedélyezett áthelyezések korlátozás nélküli állapotot jelzi.|
|GlobalMovementThrottleThresholdForBalancing | Uint, alapértelmezett érték a 0 | Dinamikus|Az elmúlt időszakban GlobalMovementThrottleCountingInterval által jelzett fázisban terheléselosztás engedélyezett áthelyezések maximális száma. 0 a korlátozás nélküli állapotot jelzi. |
|GlobalMovementThrottleCountingInterval | Idő (másodpercben), az alapértelmezett érték 600 |Statikus| Adja meg az időtartam másodpercben. Azt jelzi, amelyek esetében szeretne nyomon követése (GlobalMovementThrottleThreshold párhuzamosan használva) tartomány replika áthelyezések száma az elmúlt időköz hossza. 0 értékre állítható figyelmen kívül hagyja a globális sávszélesség-szabályozás regisztrálását. |
|MovementPerPartitionThrottleThreshold | Uint, alapértelmezett érték 50 |Dinamikus| Nincs terheléselosztás kapcsolatos adatátviteli partíció fog létrejönni, ha a kapcsolódó mozgásának replikákra vonatkozó adott partíció terheléselosztási száma elérte vagy meghaladta MovementPerFailoverUnitThrottleThreshold az elmúlt időszakban által jelzett MovementPerPartitionThrottleCountingInterval. |
|MovementPerPartitionThrottleCountingInterval | Idő (másodpercben), az alapértelmezett érték 600 |Statikus| Adja meg az időtartam másodpercben. Az elmúlt időköz, amelynek replika áthelyezések minden partíció (MovementPerPartitionThrottleThreshold párhuzamosan használva) nyomon követésére hosszát jelzik. |
|PlacementSearchTimeout | Idő (másodpercben), az alapértelmezett érték 0,5 |Dinamikus| Adja meg az időtartam másodpercben. Amikor az szolgáltatások; Keresse meg a hosszú, legfeljebb eredményt visszatérése előtt. |
|UseMoveCostReports | Logikai érték, alapértelmezett értéke "false" | Dinamikus|Arra utasítja a LB figyelmen kívül hagyja a költség elem pontozó függvény; jobb kiegyensúlyozott elhelyezésre helyezi át a létrejövő potenciálisan nagy száma. |
|PreventTransientOvercommit | Logikai érték, alapértelmezett értéke "false" | Dinamikus|Meghatározza, hogy PLB azonnal számolja szabadul fel által kezdeményezett a kurzor által erőforrásokra vonatkozóan. Alapértelmezett; PLB kezdeményezhetnek kilép, és ugyanazon a csomóponton átmeneti hozhat létre, amely az áthelyezés teszi. A paraméter TRUE értékre megakadályozza, hogy ezek különböző a overcommits és igény szerinti töredezettségmentesítés (más néven placementWithMove) le kell tiltani. |
|InBuildThrottlingEnabled | Logikai érték, alapértelmezett értéke "false" |Dinamikus| Határozza meg, hogy engedélyezve legyen-e az a beépített szabályozás. |
|InBuildThrottlingAssociatedMetric | karakterlánc, alapértelmezett érték a "" |Statikus| A szabályozás társított metrika neve. |
|InBuildThrottlingGlobalMaxValue | Int, alapértelmezett érték a 0 |Dinamikus|Beépített replikák globálisan engedélyezett maximális száma. |
|SwapPrimaryThrottlingEnabled | Logikai érték, alapértelmezett értéke "false"|Dinamikus| Határozza meg, hogy engedélyezve van-e a lapozófájl-kapacitás-elsődleges-szabályozás. |
|SwapPrimaryThrottlingAssociatedMetric | karakterlánc, alapértelmezett érték a ""|Statikus| A szabályozás társított metrika neve. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, alapértelmezett érték a 0 |Dinamikus| Lapozófájl-kapacitás elsődleges replika globálisan engedélyezett maximális száma. |
|PlacementConstraintPriority | Int, alapértelmezett érték a 0 | Dinamikus|Meghatározza, hogy a prioritású virtuális gép elhelyezési korlátozás: 0: rögzített; 1: enyhe; negatív: figyelmen kívül hagyja. |
|PreferredLocationConstraintPriority | Int, alapértelmezett érték a 2. régiója| Dinamikus|Meghatározza, hogy az előnyben részesített földrajzi megszorítás prioritásának: 0: rögzített; 1: enyhe; 2: optimalizálási; negatív: figyelmen kívül hagyása |
|CapacityConstraintPriority | Int, alapértelmezett érték a 0 | Dinamikus|A kapacitás megkötés prioritásának: 0: rögzített; 1: enyhe; negatív: figyelmen kívül hagyja. |
|AffinityConstraintPriority | Int, alapértelmezett érték a 0 | Dinamikus|Meghatározza, hogy a kapcsolat megkötés prioritását: 0: rögzített; 1: enyhe; negatív: figyelmen kívül hagyja. |
|FaultDomainConstraintPriority | Int, alapértelmezett érték a 0 |Dinamikus| Meghatározza, hogy a tartalék tartomány korlátozás prioritás: 0: rögzített; 1: enyhe; negatív: figyelmen kívül hagyja. |
|UpgradeDomainConstraintPriority | Int, alapértelmezett érték 1| Dinamikus|Meghatározza, hogy a frissítési tartomány korlátozás prioritás: 0: rögzített; 1: enyhe; negatív: figyelmen kívül hagyja. |
|ScaleoutCountConstraintPriority | Int, alapértelmezett érték a 0 |Dinamikus| Határozza meg, a prioritás scaleout száma megkötés: 0: rögzített; 1: enyhe; negatív: figyelmen kívül hagyja. |
|ApplicationCapacityConstraintPriority | Int, alapértelmezett érték a 0 | Dinamikus|A kapacitás megkötés prioritásának: 0: rögzített; 1: enyhe; negatív: figyelmen kívül hagyja. |
|MoveParentToFixAffinityViolation | Logikai érték, alapértelmezett értéke "false" |Dinamikus| Beállítást, amely meghatározza, hogy áthelyezhető-e a szülő replikák affinitás megkötések javításához.|
|MoveExistingReplicaForPlacement | Logikai érték, az alapértelmezett érték true |Dinamikus|Beállítás, amely meghatározza, hogy ha meglévő replika áthelyezése az Elhelyezés során. |
|UseSeparateSecondaryLoad | Logikai érték, az alapértelmezett érték true | Dinamikus|Beállítás, amely meghatározza, hogy ha eltérő másodlagos terhelési használja. |
|PlaceChildWithoutParent | Logikai érték, az alapértelmezett érték true | Dinamikus|Beállítás, amely meghatározza, hogy az alárendelt szolgáltatás replika helyezhető be nincs szülő replika esetén. |
|PartiallyPlaceServices | Logikai érték, az alapértelmezett érték true |Dinamikus| Meghatározza, hogy ha a fürt összes szolgáltatás replika "mindent vagy semmit" kerülnek-e a megadott korlátozott megfelelő csomópontok számukra.|
|InterruptBalancingForAllFailoverUnitUpdates | Logikai érték, alapértelmezett értéke "false" | Dinamikus|Azt határozza meg, ha feladatátvételi egységek frissítés bármely típusú megszakítási gyors vagy lassú terheléselosztás futtatásához. A megadott, "false" terheléselosztás futtatása megszakad, ha FailoverUnit: van létrehozni vagy törölni; hiányoznak a replikák; módosította elsődleges replika helyére vagy replikák száma megváltozott. Terheléselosztás futtatása nem szakad más esetben – ha FailoverUnit: extra replikák; bármely replika jelző; megváltozott csak a partíció-es vagy bármely egyéb megváltozott. |
|GlobalMovementThrottleThresholdPercentage|dupla alapértelmezett érték a 0|Dinamikus|Terheléselosztás és elhelyezési fázisban történik (a fürt replikák száma százalékában kifejezve) az elmúlt időszakban GlobalMovementThrottleCountingInterval által jelzett engedélyezett teljes áthelyezések maximális száma. 0 a korlátozás nélküli állapotot jelzi. Ha ez és GlobalMovementThrottleThreshold megadott; majd korlátozóbb korlát szolgál.|
|GlobalMovementThrottleThresholdPercentageForBalancing|dupla alapértelmezett érték a 0|Dinamikus|Típusú áthelyezések terheléselosztás fázis (PLB replikák száma százalékában kifejezve) az elmúlt időszakban GlobalMovementThrottleCountingInterval által jelzett engedélyezett maximális száma. 0 a korlátozás nélküli állapotot jelzi. Ha ez és GlobalMovementThrottleThresholdForBalancing megadott; majd korlátozóbb korlát szolgál.|
|AutoDetectAvailableResources|logikai érték, alapértelmezett értéke igaz|Statikus|Ez a konfiguráció akkor indul el, az automatikus észlelés rendelkezésre álló erőforrások a csomópont (Processzor és memória) Ha a konfiguráció beállítása true – a rendszer olvassa el a valódi kapacitások és javítsa ki azokat, ha felhasználói rossz csomópont-kapacitás megadva, illetve nem meghatározott őket, ha ez a konfiguráció beállítása false - fedi le  nyomon követni egy figyelmeztetés, hogy a felhasználó által megadott rossz csomópont kapacitása; de azt újraindítása nem szünteti meg. ami azt jelenti, hogy a felhasználó rendelkezik a megadott kapacitások szeretné > mint valóban van a csomópont, vagy ha a telepítést nem definiált; azt fogja feltételezni, hogy korlátlan kapacitás |

### <a name="section-name-hosting"></a>Szakasz Name: üzemeltetéséhez
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| ServiceTypeRegistrationTimeout |Idő (másodpercben), az alapértelmezett érték 300 |Dinamikus|A ServiceType lehet regisztrálni a háló megengedett maximális időtartamot |
| ServiceTypeDisableFailureThreshold |Egész szám, alapértelmezett értéke 1 |Dinamikus|Ez az a küszöbérték a hibaszámláló, amely után FailoverManager (FM) értesítést kap a szolgáltatás típusa ezen a csomóponton letiltása, majd próbálja meg egy másik csomópont elhelyezésre. |
| ActivationRetryBackoffInterval |Idő (másodpercben), az alapértelmezett érték 5 |Dinamikus|Visszalépési időköz minden aktiválási hiba esetén; Minden folyamatos aktiválási hiba esetén a rendszer újrapróbálkozik a MaxActivationFailureCount legfeljebb az az aktiválás. Az újrapróbálkozási időköz minden próbálkozásra folyamatos aktiválási hiba termék és az aktiválási vissza az indító időköz. |
| ActivationMaxRetryInterval |Idő (másodpercben), az alapértelmezett érték 300 |Dinamikus|Minden folyamatos aktiválási hiba esetén a rendszer újrapróbálkozik az aktiválás a ActivationMaxFailureCount legfeljebb. ActivationMaxRetryInterval Megadja azt a várakozási időközt újrapróbálkozás előtti, minden aktiválási hiba után |
| ActivationMaxFailureCount |Egész szám, alapértelmezett érték 10 |Dinamikus|Ennyiszer rendszer újrapróbálkozások sikertelenek, mielőtt aktiválás |
|ActivationTimeout| TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(180)|Dinamikus| Adja meg az időtartam másodpercben. Alkalmazás-aktiválásra, időtúllépés az inaktiválást és frissítését. |
|ApplicationHostCloseTimeout| TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus| Adja meg az időtartam másodpercben. Ha a saját háló kilépési észlel aktivált folyamata; FabricRuntime bezárja az összes, a felhasználói gazdagépfolyamathoz (alkalmazásgazdáról) a replikán. Ez az a bezárási művelet időtúllépés. |
|ApplicationUpgradeTimeout| TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(360)|Dinamikus| Adja meg az időtartam másodpercben. Az alkalmazásfrissítés időtúllépés. Ha az időtúllépési érték kisebb, mint a "ActivationTimeout" telepítőn sikertelen lesz. |
|CreateFabricRuntimeTimeout|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus| Adja meg az időtartam másodpercben. Az időtúllépés értéke a szinkronizálási szolgáltatás FabricCreateRuntime hívása |
|DeploymentMaxFailureCount|Int, alapértelmezett érték 20| Dinamikus|Alkalmazás központi telepítésének megpróbálja újból végrehajtani az DeploymentMaxFailureCount időpontokat, hogy a csomópont-alkalmazás központi telepítésének végrehajtása előtt.| 
|DeploymentMaxRetryInterval| TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(3600)|Dinamikus| Adja meg az időtartam másodpercben. Maximális újrapróbálkozási időközt a központi telepítéshez. Minden folyamatos hiba esetén az újrapróbálkozási időköz számítjuk Min (DeploymentMaxRetryInterval; Folyamatos hibaszámláló * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(10)|Dinamikus|Adja meg az időtartam másodpercben. Vissza az indító időközét a központi telepítési problémáiról. Minden folyamatos üzembe helyezés hiba esetén a rendszer megpróbálja a MaxDeploymentFailureCount legfeljebb tartozó központi telepítést. Az újrapróbálkozási időköz a termék a folyamatos üzembe helyezés hiba és a központi telepítés visszalépési időköz. |
|EnableActivateNoWindow| logikai érték, alapértelmezett értéke hamis|Dinamikus| Az aktivált folyamat a háttérben, a konzol nélküli jön létre. |
|EnableProcessDebugging|logikai érték, alapértelmezett értéke hamis|Dinamikus| Lehetővé teszi, hogy a hibakereső alkalmazásgazdája indítása |
|EndpointProviderEnabled| logikai érték, alapértelmezett értéke hamis|Statikus| Lehetővé teszi, hogy a háló végpont eszközeiket. A kötelező FabricNode a kezdő és záró alkalmazás porttartomány. |
|FabricContainerAppsEnabled| logikai érték, alapértelmezett értéke hamis|Statikus| |
|FirewallPolicyEnabled|logikai érték, alapértelmezett értéke hamis|Statikus| Lehetővé teszi, hogy tűzfalportok végpont erőforrások ServiceManifest megadott explicit portok megnyitása |
|GetCodePackageActivationContextTimeout|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus|Adja meg az időtartam másodpercben. Az időtúllépési értékének CodePackageActivationContext hívást. Ez a tulajdonság nem vonatkozik az alkalmi szolgáltatásokban. |
|IPProviderEnabled|logikai érték, alapértelmezett értéke hamis|Statikus|Lehetővé teszi az IP-címek kezelését. |
|NTLMAuthenticationEnabled|logikai érték, alapértelmezett értéke hamis|Statikus| Lehetővé teszi az NTLM használatával kód csomagokat fut más felhasználókkal, hogy a folyamatok közötti gépek biztonságosan kommunikálhassanak. |
|NTLMAuthenticationPasswordSecret|SecureString, alapértelmezett érték Common::SecureString(L"")|Statikus|Egy titkosított létrehozni az NTLM-felhasználó jelszót használt. Ha NTLMAuthenticationEnabled igaz beállítandó rendelkezik. A telepítő hitelesíteni. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromMinutes(3)|Dinamikus|Adja meg az időtartam másodpercben. FileStoreService NTLM-konfigurációhoz használandó új tanúsítványok keres a lépésközt, mely üzemeltetési környezet által megadott beállításokat. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromMinutes(4)|Dinamikus| Adja meg az időtartam másodpercben. A tanúsítvány köznapi nevek használatával NTLM-felhasználók konfigurálásával időkorlátját. Az NTLM felhasználók FileStoreService megosztások van szükség. |
|RegisterCodePackageHostTimeout|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus| Adja meg az időtartam másodpercben. Az időtúllépési értékének FabricRegisterCodePackageHost szinkronizálási hívása. Ez a tulajdonság csak multi kód csomag alkalmazásgazdája esetén például FWP vonatkozik |
|RequestTimeout|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(30)|Dinamikus| Adja meg az időtartam másodpercben. A felhasználó alkalmazást és a háló folyamat gyári regisztrációs; például különféle üzemeltetési kapcsolódó műveletek közötti kommunikáció időtúllépés jelképez futásidejű regisztrációja. |
|RunAsPolicyEnabled| logikai érték, alapértelmezett értéke hamis|Statikus| Lehetővé teszi, hogy csak a felhasználó helyi felhasználóként fut a kód csomagok melyik háló folyamat fut. Ahhoz, hogy ez a házirend Fabric rendszert vagy SeAssignPrimaryTokenPrivilege rendelkező felhasználóként kell futnia. |
|ServiceFactoryRegistrationTimeout| TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus|Adja meg az időtartam másodpercben. Az időtúllépés értéke a szinkronizálási szolgáltatás regisztrálása (Stateless/Stateful) ServiceFactory hívás |
|ServiceTypeDisableGraceInterval|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(30)|Dinamikus|Adja meg az időtartam másodpercben. Időtartam, amely után a szolgáltatás típusának le kell tiltani. |

### <a name="section-name-federation"></a>Szakasz Name: összevonási
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| LeaseDuration |Idő (másodpercben), az alapértelmezett érték 30 |Dinamikus|A címbérlet tart egy csomópont és a szomszédjaihoz csatlakoztatni közötti időtartamot. |
| LeaseDurationAcrossFaultDomain |Idő (másodpercben), az alapértelmezett érték 30 |Dinamikus|Egy csomópont és a szomszédjaihoz csatlakoztatni között tartalék tartományokban tovább tartó a címbérlet időtartama. |

### <a name="section-name-clustermanager"></a>Szakasz Name: ClusterManager
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend** | **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
| UpgradeStatusPollInterval |Idő (másodpercben), az alapértelmezett érték 60 |Dinamikus|Az alkalmazás frissítési állapot lekérdezési gyakorisága. Ez az érték határozza meg a frissítés, bármely GetApplicationUpgradeProgress hívás aránya |
| UpgradeHealthCheckInterval |Idő (másodpercben), az alapértelmezett érték 60 |Dinamikus|Állapot gyakoriságát ellenőrzi a figyelt alkalmazás frissítéskor |
| FabricUpgradeStatusPollInterval |Idő (másodpercben), az alapértelmezett érték 60 |Dinamikus|A háló lekérdezési gyakorisága frissítési állapot. Ez az érték határozza meg a frissítés, bármely GetFabricUpgradeProgress hívás aránya |
| FabricUpgradeHealthCheckInterval |Idő (másodpercben), az alapértelmezett érték 60 |Dinamikus|Állapot gyakoriságát ellenőrzése közben a figyelt Fabric-frissítés. |
|InfrastructureTaskProcessingInterval | Idő (másodpercben), az alapértelmezett érték 10 |Dinamikus|Adja meg az időtartam másodpercben. A feldolgozási időköze az állapotjelző gép feldolgozási infrastruktúra feladat által használt. |
|TargetReplicaSetSize |Int, alapértelmezett érték 7 |Nem engedélyezett|A ClusterManager TargetReplicaSetSize. |
|MinReplicaSetSize |Int, alapértelmezett érték 3 |Nem engedélyezett|A MinReplicaSetSize ClusterManager számára. |
|ReplicaRestartWaitDuration |Idő (másodpercben), az alapértelmezett érték (60.0 * 30)|Nem engedélyezett|Adja meg az időtartam másodpercben. A ClusterManager ReplicaRestartWaitDuration. |
|QuorumLossWaitDuration |Idő (másodpercben), alapértelmezett érték a MaxValue |Nem engedélyezett| Adja meg az időtartam másodpercben. A ClusterManager QuorumLossWaitDuration. |
|StandByReplicaKeepDuration | Idő (másodpercben), az alapértelmezett érték (3600.0 * 2)|Nem engedélyezett|Adja meg az időtartam másodpercben. A ClusterManager StandByReplicaKeepDuration. |
|PlacementConstraints | karakterlánc, alapértelmezett érték a "" |Nem engedélyezett|A ClusterManager PlacementConstraints. |
|SkipRollbackUpdateDefaultService | Logikai érték, alapértelmezett értéke "false" |Dinamikus|A Tanúsítványkezelő alkalmazás frissítési visszaállítás során kihagyja visszaállítási frissített alapértelmezett szolgáltatások. |
|EnableDefaultServicesUpgrade | Logikai érték, alapértelmezett értéke "false" |Dinamikus|Frissíti az alapértelmezett szolgáltatások engedélyezése az alkalmazásfrissítés során. Alapértelmezett szolgáltatások leírásai volna felülírja a frissítés után. |
|InfrastructureTaskHealthCheckWaitDuration |Idő (másodpercben), az alapértelmezett érték 0|Dinamikus| Adja meg az időtartam másodpercben. Az állapot-ellenőrzési eredményeire után utófeldolgozási egy infrastruktúra-feladat indítása előtt időtartam. |
|InfrastructureTaskHealthCheckStableDuration | Idő (másodpercben), az alapértelmezett érték 0|Dinamikus| Adja meg az időtartam másodpercben. Ennyi idő alatt, és figyelje meg az egymást követő átadott állapotellenőrzést infrastruktúra feladatról utáni feldolgozás előtt futtatása sikeresen befejeződött. Megfigyelő állapotellenőrzése nem sikerült Ez az időzítő alaphelyzetbe állnak. |
|InfrastructureTaskHealthCheckRetryTimeout | Idő (másodpercben), az alapértelmezett érték 60 |Dinamikus|Adja meg az időtartam másodpercben. A szükséges időt töltenek az újrapróbálkozás állapotellenőrzést infrastruktúra feladatról utáni feldolgozásakor nem sikerült. Átadott állapotellenőrzése betartásával Ez az időzítő alaphelyzetbe állnak. |
|ImageBuilderTimeoutBuffer |Idő (másodpercben), az alapértelmezett érték 3 |Dinamikus|Adja meg az időtartam másodpercben. Ennyi idő alatt, amelyek engedélyezik az Image Builder adott időtúllépést vissza az ügyfélnek. Ha a puffer túl kicsi. Ezután az ügyfél időtúllépése előtt a kiszolgáló, és lekérdezi egy általános időtúllépési hiba. |
|MinOperationTimeout | Idő (másodpercben), az alapértelmezett érték 60 |Dinamikus|Adja meg az időtartam másodpercben. A minimális globális időkorlátjának belső feldolgozási ClusterManager műveleteket. |
|Konfigurált MaxOperationTimeout |Idő (másodpercben), alapértelmezett érték a MaxValue |Dinamikus| Adja meg az időtartam másodpercben. A maximális globális időkorlátjának belső feldolgozási ClusterManager műveleteket. |
|MaxTimeoutRetryBuffer | Idő (másodpercben), az alapértelmezett érték 600 |Dinamikus|Adja meg az időtartam másodpercben. A maximális művelet időkorlát megkísérlésekor belső időtúllépések miatt <Original Time out>  +  <MaxTimeoutRetryBuffer>. További időtúllépés MinOperationTimeout lépésekben kerül. |
|MaxCommunicationTimeout |Idő (másodpercben), az alapértelmezett érték 600 |Dinamikus|Adja meg az időtartam másodpercben. Belső kommunikációs ClusterManager és egyéb rendszer között a maximális időkorlátot (azaz; szolgáltatások A Naming Service; Feladatátvevőfürt-kezelő és a stb.). Ez az időkorlát kisebb, mint a globális konfigurált MaxOperationTimeout (mivel előfordulhat, hogy minden ügyfél művelethez rendszerösszetevők közötti több kommunikáció) kell lennie. |
|MaxDataMigrationTimeout |Idő (másodpercben), az alapértelmezett érték 600 |Dinamikus|Adja meg az időtartam másodpercben. A maximális időkorlátot áttelepítési helyreállítási műveletek után a Fabric frissítése megtörtént. |
|MaxOperationRetryDelay |Idő (másodpercben), az alapértelmezett érték 5|Dinamikus| Adja meg az időtartam másodpercben. A hibák hibát amikor belső újrapróbálkozások maximális késleltetés. |
|ReplicaSetCheckTimeoutRollbackOverride |Idő (másodpercben), az alapértelmezett érték 1200 |Dinamikus| Adja meg az időtartam másodpercben. Ha ReplicaSetCheckTimeout értéke a megengedett legnagyobb érték duplaszó; Ezután azt felülbírálja a config értékét visszaállítási alkalmazásában. Összegző-továbbításra használt érték nem soha nem bírálják felül. |
|ImageBuilderJobQueueThrottle |Int, alapértelmezett érték 10 |Dinamikus|Az Image Builder proxy feladat-várólistán alkalmazás kérelmek száma késleltetési szál. |
|MaxExponentialOperationRetryDelay|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(30)|Dinamikus|Adja meg az időtartam másodpercben. A hibák többször is fellépő belső újrapróbálkozások maximális exponenciális késleltetése |

### <a name="section-name-defragmentationemptynodedistributionpolicy"></a>Szakasz Name: DefragmentationEmptyNodeDistributionPolicy
| **A paraméter** | **Megengedett értékek** |**Frissítési házirend**| **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, alapértelmezés szerint nincs|Dinamikus|Meghatározza, hogy a házirend töredezettségmentesítés követ, amikor a csomópontok kiürítése. Egy metrika a 0 azt jelenti, hogy ú próbálkozzon az csomópontok egyenletesen töredezettségmentesítése UDs és FDs; 1 csak azt jelzi, hogy a csomópontok kell töredezettségmentesíteni |

### <a name="section-name-defragmentationmetrics"></a>Szakasz Name: DefragmentationMetrics
| **A paraméter** | **Megengedett értékek** |**Frissítési házirend**| **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, alapértelmezés szerint nincs|Dinamikus|Meghatározza, hogy töredezettségmentesítés és nem a terheléselosztáshoz használt mérőszámok készletét. |

### <a name="section-name-defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>Szakasz Name: DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **A paraméter** | **Megengedett értékek** |**Frissítési házirend**| **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, alapértelmezés szerint nincs|Dinamikus|Határozza meg, amelyek szükségesek ahhoz, hogy fontolja meg a tartomány vagy százalékban megadva töredezettségmentesíteni fürt szabad csomópontok száma [0.0-1.0) vagy számot üres csomópontok száma > = 1.0 |

### <a name="section-name-dnsservice"></a>Szakasz Name: DnsService
| **A paraméter** | **Megengedett értékek** |**Frissítési házirend**| **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
|IsEnabled|logikai érték, alapértelmezett értéke hamis|Statikus| |
|InstanceCount|int, alapértelmezett értéke -1|Statikus|  |

### <a name="section-name-metricactivitythresholds"></a>Szakasz Name: MetricActivityThresholds
| **A paraméter** | **Megengedett értékek** |**Frissítési házirend**| **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, alapértelmezés szerint nincs|Dinamikus|Meghatározza, hogy a fürt metrikáihoz MetricActivityThresholds készletét. Terheléselosztás működnek, ha maxNodeLoad MetricActivityThresholds nagyobb. A metrikák töredezettségmentesítési vagy alá, amely a Service Fabric úgy tekinti, hogy a csomópont üres meghatározza a terhelés egyenlő mennyisége |

### <a name="section-name-metricbalancingthresholds"></a>Szakasz Name: MetricBalancingThresholds
| **A paraméter** | **Megengedett értékek** |**Frissítési házirend**| **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, alapértelmezés szerint nincs|Dinamikus|Meghatározza, hogy a fürt metrikáihoz MetricBalancingThresholds készletét. Terheléselosztás működnek, ha maxNodeLoad/minNodeLoad MetricBalancingThresholds nagyobb. Lemeztöredezettség-mentesítés fog működni, ha legalább egy FD vagy UD maxNodeLoad/minNodeLoad MetricBalancingThresholds kisebb. |

### <a name="section-name-nodebufferpercentage"></a>Szakasz Name: NodeBufferPercentage
| **A paraméter** | **Megengedett értékek** |**Frissítési házirend**| **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, alapértelmezés szerint nincs|Dinamikus|Csomópont kapacitás százalékos arányát / metrika neve; használt néhány szabad hely a feladatátvételi eset csomóponton megőrzése érdekében. |

### <a name="section-name-replication"></a>Szakasz Name: replikáció
| **A paraméter** | **Megengedett értékek** | **Frissítési házirend**| **Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
|MaxCopyQueueSize|Uint, alapértelmezett érték: 1024|Statikus|Ez az a maximális értéke határozza meg a kezdeti replikálási műveletek fenntartó várólista.  Vegye figyelembe, hogy 2 szintűnek kell lennie.  Ha ez a méret a várólista eléri a futtatás során művelet számára halmozódni fog, az elsődleges és másodlagos gyártóitól között.|
|BatchAcknowledgementInterval|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromMilliseconds(15)|Statikus|Adja meg az időtartam másodpercben. Meghatározza, hogy az, hogy mennyi ideig a replikátor megvárja-e olyan műveleteket, mielőtt küld vissza nyugtázást fogadása után. Más műveletek kapott ebben az időszakban a nyugtázás a küldött vissza egy egyetlen -> csökkentése hálózati forgalmat, de lehetséges csökkenteni az átviteli sebessége a replikátor fog rendelkezni.|
|(Maxreplicationmessagesize).|Uint, alapértelmezett érték 52428800|Statikus|Replikációs műveletek maximális méretét. Az alapértelmezett érték 50MB.|
|ReplicatorAddress|karakterlánc, alapértelmezett értéke L "localhost:0"|Statikus|A végpont egy karakterlánc-"IP:Port", amely a Windows Fabric replikátor ahhoz, hogy a küldési és fogadási műveletek más replikák-kapcsolatok létesítéséhez használt formában.|
|ReplicatorListenAddress|karakterlánc, alapértelmezett értéke L "localhost:0"|Statikus|A végpont egy karakterlánc-"IP:Port" műveletek fogadása más replikák a Windows Fabric replikátor által használt formában.|
|ReplicatorPublishAddress|karakterlánc, alapértelmezett értéke L "localhost:0"|Statikus|A végpont egy karakterlánc-"IP:Port" műveletek küldendő más replikák a Windows Fabric replikátor által használt formában.|
|MaxPrimaryReplicationQueueSize|Uint, alapértelmezett érték: 1024|Statikus|Ez az az elsődleges replikációs várólistában lévő létező sikerült műveletek maximális száma. Vegye figyelembe, hogy 2 szintűnek kell lennie.|
|MaxPrimaryReplicationQueueMemorySize|Uint, alapértelmezett érték a 0|Statikus|Ez az a maximális érték az elsődleges replikációs sor bájtokban.|
|MaxSecondaryReplicationQueueSize|uint, alapértelmezett értéke 2048|Statikus|Ez az volt a másodlagos replikációs várólistában lévő létező műveletek maximális száma. Vegye figyelembe, hogy 2 szintűnek kell lennie.|
|MaxSecondaryReplicationQueueMemorySize|Uint, alapértelmezett érték a 0|Statikus|Ez az a maximális érték a másodlagos replikációs sor bájtokban.|
|QueueHealthMonitoringInterval|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(30)|Statikus|Adja meg az időtartam másodpercben. Ez az érték határozza meg az adott időszakban a replikátor által a replikációs művelet várólistára figyelmeztetés/hiba állapotfigyelő eseményeket figyelésére használható. "0" érték letiltja az állapotfigyelés |
|QueueHealthWarningAtUsagePercent|uint, alapértelmezett értéke 80|Statikus|Ez az érték határozza meg a replikációs várólista használata (százalék) elteltével azt jelentést készít a figyelmeztetés magas várólista-használat. A Microsoft ehhez a QueueHealthMonitoringInterval türelmi időszak után. Ha a várólista használata a türelmi időszak ezen százalékos arány alá esik.|
|RetryInterval|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(5)|Statikus|Adja meg az időtartam másodpercben. Ha egy művelet elvész, vagy nem utasítható el ez az időzítő határozza meg, milyen gyakran próbálkozik újra a replikátor küldése a műveletet.|

### <a name="section-name-transport"></a>Szakasz Name: átviteli
| **A paraméter** | **Megengedett értékek** |**Frissítési házirend** |**Útmutatás vagy rövid leírása** |
| --- | --- | --- | --- |
|ResolveOption|karakterlánc, alapértelmezett érték a "nincs megadva" L|Statikus|Meghatározza, hogy FQDN feloldása.  Érvényes értékek: "nincs megadva vagy ipv4-/ ipv6". |
|ConnectionOpenTimeout|TimeSpan érték, alapértelmezett érték Common::TimeSpan::FromSeconds(60)|Statikus|Adja meg az időtartam másodpercben. A csatlakozási telepítés (beleértve a biztonsági egyeztetés biztonságos módban), mind a bejövő, mind az átvevő oldalon túllépi az időkorlátot |

## <a name="next-steps"></a>Következő lépések
Ezek a cikkek további információt a kiszolgálófürt-felügyelet olvasható:

[Adja hozzá, a át, a tanúsítványok eltávolítása az Azure-fürttel](service-fabric-cluster-security-update-certs-azure.md) 

