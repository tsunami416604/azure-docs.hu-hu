---
title: Hibaelhárítás rendszerállapot-jelentések segítségével
description: Az Azure Service Fabric-összetevők által küldött állapotjelentések, valamint a fürt- vagy alkalmazásproblémák elhárítására szolgáló használatuk
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: a76ae803b1283ce50d2f4e259943ce5ffcf0274c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282015"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Rendszerállapot-jelentések használata a hibaelhárítás során
Az Azure Service Fabric-összetevők azonnal rendszerállapot-jelentéseket biztosítanak a fürt összes entitásáról. Az [állapottároló](service-fabric-health-introduction.md#health-store) a rendszerjelentések alapján hoz létre és töröl entitásokat. Az entitásokkal folytatott interakciókat rögzítő hierarchiába is rendszerezi őket.

> [!NOTE]
> Az egészséggel kapcsolatos fogalmak megértéséhez olvassa el a [Service Fabric állapotmodelljének](service-fabric-health-introduction.md)további tudnivalóit.
> 
> 

A rendszerállapot-jelentések betekintést nyújtanak a fürt- és alkalmazásfunkciókba, valamint megjelölik a problémákat. Alkalmazások és szolgáltatások esetén a rendszerállapot-jelentések ellenőrzik, hogy az entitások vannak-e megvalósítva, és megfelelően viselkednek-e a Service Fabric szempontból. A jelentések nem biztosítanak állapotfigyelést a szolgáltatás üzleti logikájának vagy a nem válaszoló folyamatok észlelésének. A felhasználói szolgáltatások az állapotadatokat a logikájukra jellemző információkkal gazdagíthatják.

> [!NOTE]
> A felhasználói figyelők által küldött állapotjelentések csak akkor láthatók, *ha* a rendszerösszetevők létrehoznak egy entitást. Egy entitás törlésekor az állapottároló automatikusan törli a hozzá társított összes állapotjelentést. Ugyanez igaz az entitás új példányának létrehozásakor is. Egy példa, ha egy új állapotalapú, megőrzött szolgáltatás replikapéldány jön létre. A régi példányhoz társított összes jelentés törlődik, és törlődik a tárolóból.
> 
> 

A rendszerösszetevő-jelentéseket a forrás azonosítja, amely a "**rendszerrel**kezdődik." előtagot. A figyelők nem használhatják ugyanazt az előtagot a forrásaikhoz, mivel az érvénytelen paraméterekkel rendelkező jelentéseket a rendszer elutasítja.

Nézzünk meg néhány rendszerjelentést, hogy megértsük, mi váltja ki őket, és hogyan lehet kijavítani az általuk képviselt potenciális problémákat.

> [!NOTE]
> A Service Fabric továbbra is jelentéseket ad hozzá az érdeklődési feltételekről, amelyek javítják a fürtben és az alkalmazásokban zajló események láthatóságát. A meglévő jelentések további részletekkel javíthatók a probléma gyorsabb elhárításához.
> 
> 

## <a name="cluster-system-health-reports"></a>Fürtrendszer állapotjelentései
A fürt állapotentitása automatikusan létrejön az állapottárolóban. Ha minden megfelelően működik, nincs rendszerjelentése.

### <a name="neighborhood-loss"></a>A környék elvesztése
**System.Federation** hibát jelez, ha a környék elvesztését észleli. A jelentés különálló csomópontokból származik, és a csomópontazonosító szerepel a tulajdonság nevében. Ha egy szomszédság elvész a teljes Service Fabric-gyűrű, általában számíthat két esemény, amely a gap jelentés mindkét oldalát képviseli. Ha több környék is elvész, több esemény is van.

A jelentés a globális lízing időtúltöltését az élő idő (TTL) ként határozza meg. A jelentés a TTL időtartamának minden felében resent, amíg a feltétel aktív marad. Az esemény automatikusan törlődik, amikor lejár. Eltávolítás-mikor-lejárt viselkedés biztosítja, hogy a jelentés megfelelően törlődik a health Store, akkor is, ha a jelentési csomópont nem érhető el.

* **SourceId**: System.Federation
* **Tulajdonság**: **A Szomszédsággal kezdődik,** és tartalmazza a csomópontadatait.
* **Következő lépések**: Vizsgálja meg, miért veszett el a környék. Ellenőrizze például a fürtcsomópontok közötti kommunikációt.

### <a name="rebuild"></a>Újraépítés

A Feladatátvevő-kezelő (FM) szolgáltatás kezeli a fürtcsomópontok adatait. Amikor az FM elveszíti az adatait, és adatvesztésbe kerül, nem tudja garantálni, hogy a fürtcsomópontoklegfrissebb információival rendelkezik. Ebben az esetben a rendszer egy újraépítésen megy keresztül, és System.FM adatokat gyűjt a fürt összes csomópontjáról az állapot újraépítése érdekében. Néha hálózati vagy csomóponti problémák miatt az újraépítés elakadhat vagy elakadhat. Ugyanez történhet a Feladatátvevő kezelő főkiszolgáló (FMM) szolgáltatással is. Az FMM egy állapotnélküli rendszerszolgáltatás, amely nyomon követi, hogy az összes fm-ek hol vannak a fürtben. Az FMM elsődleges mindig a 0-hoz legközelebb eső azonosítóval rendelkező csomópont. Ha a csomópont megszakad, az újraépítés aktiválódik.
Ha az előző feltételek valamelyike megtörténik, **System.FM** vagy **system.FMM** megjelöli azt egy hibajelentésen keresztül. Az újraépítés a következő két fázis egyikében ragadhat meg:

* **Várakozás az adásra**: Fm/FMM várja a többi csomópont tól érkező adásüzenet-választ.

  * **Következő lépések**: Vizsgálja meg, hogy van-e hálózati kapcsolati probléma a csomópontok között.
* **Várakozás a csomópontokra**: Az FM/FMM már kapott egy közvetítési választ a többi csomóponttól, és bizonyos csomópontok válaszára vár. Az állapotjelentés felsorolja azokat a csomópontokat, amelyekre az FM/FMM választ vár.
   * **Következő lépések:** Vizsgálja meg az FM/FMM és a felsorolt csomópontok közötti hálózati kapcsolatot. Vizsgálja meg az egyes felsorolt csomópontokat más lehetséges problémák ra.

* **SourceID**: System.FM vagy System.FMM
* **Tulajdonság**: Újraépítés.
* **Következő lépések:** Vizsgálja meg a csomópontok közötti hálózati kapcsolatot, valamint az állapotjelentés leírásában felsorolt csomópontok állapotát.

### <a name="seed-node-status"></a>Magotcsomópont állapota
**System.FM** fürtszintű figyelmeztetést jelent, ha egyes magcsomópontok nem kifogástalanállapotúak. A kezdő csomópontok azok a csomópontok, amelyek fenntartják az alapul szolgáló fürt rendelkezésre állását. Ezek a csomópontok segítenek annak biztosításában, hogy a fürt továbbra is fel, mert más csomópontokkal bérleteket hoz létre, és bizonyos típusú hálózati hibák esetén tiebreakerként szolgál. Ha a magcsomópontok többsége nem található meg a fürtben, és nem hozzák vissza őket, a fürt automatikusan leáll. 

A magozott csomópont nem kifogástalan, ha a csomópont állapota Le, Eltávolítva vagy Ismeretlen.
A figyelmeztető jelentés a mag csomópont állapota felsorolja az összes nem megfelelő állapotú mag csomópontok részletes információkat.

* **Forrásazonosító**: System.FM
* **Ingatlan**: SeedNodeStatus
* **Következő lépések:** Ha ez a figyelmeztetés a fürtben jelenik meg, kövesse az alábbi utasításokat a javításhoz: A Service Fabric 6.5-ös vagy újabb verzióját futtató fürt: Az Azure-beli Service Fabric-fürt, miután a magcsomópont leáll, a Service Fabric automatikusan megpróbálja módosítani azt egy nem magos csomópontra. Ehhez győződjön meg arról, hogy az elsődleges csomóponttípus nem magozott csomópontjainak száma nagyobb vagy egyenlő a Le magcsomópontok számával. Ha szükséges, adjon hozzá további csomópontokat az elsődleges csomóponttípushoz ennek eléréséhez.
A fürt állapotától függően eltarthat egy ideig a probléma megoldása. Ha ez megtörtént, a figyelmeztető jelentés automatikusan törlődik.

A Service Fabric önálló fürt, a figyelmeztetési jelentés törléséhez az összes magcsomópontok kell kifogástalan állapotúvá válnia kell. Attól függően, hogy miért nem kifogástalan a magcsomópontok, különböző műveleteket kell végezni: ha a magcsomópont le, a felhasználóknak fel kell hozniuk a magcsomópontot; ha a magcsomópont Eltávolítva vagy Ismeretlen, ezt a magcsomópontot [el kell távolítani a fürtből](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes).
A figyelmeztetési jelentés automatikusan törlődik, ha az összes magcsomópont kifogástalanállapotba kerül.

A Service Fabric 6.5-nél régebbi verzióját futtató fürt esetén: Ebben az esetben a figyelmeztetési jelentést manuálisan kell törölni. **A felhasználóknak meg kell győződniük arról, hogy az összes magcsomópont kifogástalan állapotba kerül a jelentés törlése előtt:** ha a magcsomópont le, a felhasználóknak fel kell hozniuk a magcsomópontot;ha a magcsomópont eltávolítva vagy ismeretlen, akkor a magcsomópontot el kell távolítani a fürtből.
Miután az összes magcsomópontok kifogástalanná válnak, használja a következő parancsot a Powershell [törölje a figyelmeztető jelentést:](https://docs.microsoft.com/powershell/module/servicefabric/send-servicefabricclusterhealthreport)

```powershell
PS C:\> Send-ServiceFabricClusterHealthReport -SourceId "System.FM" -HealthProperty "SeedNodeStatus" -HealthState OK

## Node system health reports
System.FM, which represents the Failover Manager service, is the authority that manages information about cluster nodes. Each node should have one report from System.FM showing its state. The node entities are removed when the node state is removed. For more information, see [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### Node up/down
System.FM reports as OK when the node joins the ring (it's up and running). It reports an error when the node departs the ring (it's down, either for upgrading or simply because it has failed). The health hierarchy built by the health store acts on deployed entities in correlation with System.FM node reports. It considers the node a virtual parent of all deployed entities. The deployed entities on that node are exposed through queries if the node is reported as up by System.FM, with the same instance as the instance associated with the entities. When System.FM reports that the node is down or restarted, as a new instance, the health store automatically cleans up the deployed entities that can exist only on the down node or on the previous instance of the node.

* **SourceId**: System.FM
* **Property**: State.
* **Next steps**: If the node is down for an upgrade, it should come back up after it's been upgraded. In this case, the health state should switch back to OK. If the node doesn't come back or it fails, the problem needs more investigation.

The following example shows the System.FM event with a health state of OK for node up:

```powershell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Tanúsítvány lejárata
**System.FabricNode** figyelmeztetést jelent, ha a csomópont által használt tanúsítványok lejárati idő közelében vannak. Csomópontonként három tanúsítvány létezik: **Certificate_cluster**, **Certificate_server**és **Certificate_default_client**. Ha a lejárati legalább két hét múlva, a jelentés állapota rendben van. Ha a lejárat két héten belül, a jelentés típusa egy figyelmeztetés. Ezeknek az eseményeknek a TTL-je végtelen, és eltávolításra kerülnek, amikor egy csomópont elhagyja a fürtöt.

* **SourceId**: System.FabricNode
* **Tulajdonság**: **A tanúsítványlal kezdődik,** és további információkat tartalmaz a tanúsítvány típusáról.
* **Következő lépések**: Frissítse a tanúsítványokat, ha azok a lejárat közelében vannak.

### <a name="load-capacity-violation"></a>Kapacitás terhelési megsértése
A Service Fabric terheléselosztó figyelmeztetést jelent, ha egy csomópont kapacitásának megsértését észleli.

* **SourceId**: System.PLB
* **Tulajdonság**: **Kapacitással**kezdődik.
* **Következő lépések:** Ellenőrizze a megadott metrikákat, és tekintse meg az aktuális kapacitást a csomóponton.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Csomópontkapacitás-eltérés az erőforrás-irányítási mérőszámokhoz
A System.Hosting figyelmeztetést jelent, ha a fürtjegyzékben meghatározott csomópontkapacitások nagyobbak, mint az erőforrás-irányítási metrikák (memória- és cpumagok) valós csomópontkapacitásai. Egy állapotjelentés jelenik meg, amikor az első szolgáltatáscsomag, amely [erőforrás-szabályozás](service-fabric-resource-governance.md) regisztrál egy adott csomóponton.

* **SourceId**: System.Hosting
* **Ingatlan**: **ResourceGovernance**.
* **Következő lépések:** Ez a probléma problémát okozhat, mert a szolgáltatáscsomagok szabályozása nem a várt módon történik, és [az erőforrás-szabályozás](service-fabric-resource-governance.md) nem működik megfelelően. Frissítse a fürtjegyzéket a metrikák megfelelő csomópontkapacitásával, vagy ne adja meg őket, és hagyja, hogy a Service Fabric automatikusan észlelje a rendelkezésre álló erőforrásokat.

## <a name="application-system-health-reports"></a>Az alkalmazásrendszer állapotjelentései
System.CM, amely a Fürtkezelő szolgáltatást jelöli, az alkalmazással kapcsolatos információkat kezelő hatóság.

### <a name="state"></a>Állapot
System.CM az alkalmazás létrehozásakor vagy frissítésekor rendben lévő jelentéseket. Tájékoztatja a health Store-t, amikor az alkalmazást törlik, így eltávolítható az üzletből.

* **SourceId**: System.CM
* **Tulajdonság**: állam.
* **Következő lépések:** Ha az alkalmazást létrehozták vagy frissítették, tartalmaznia kell a Fürtkezelő állapotjelentését. Ellenkező esetben ellenőrizze az alkalmazás állapotát lekérdezés kibocsátásával. Használja például a **Get-ServiceFabricApplication -ApplicationName** *alkalmazásnév*PowerShell-parancsmalapot.

A következő példa a **fabric:/WordCount** alkalmazás állapoteseményét mutatja be:

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Szolgáltatási rendszer állapotjelentései
System.FM, amely a Feladatátvevő-kezelő szolgáltatást képviseli, az a hatóság, amely a szolgáltatásokkal kapcsolatos információkat kezeli.

### <a name="state"></a>Állapot
System.FM a szolgáltatás létrehozásakor rendben lévő ként jelent. Törli az entitást az állapottárolóból, amikor a szolgáltatás törlődik.

* **SourceId**: System.FM
* **Tulajdonság**: állam.

A következő példa a **szolgáltatásháló állapoteseményét mutatja be:/WordCount/WordCountWebService:**

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Szolgáltatás korrelációs hibája
**A System.PLB** hibát jelez, ha azt észleli, hogy egy szolgáltatás frissítése korrelál egy másik szolgáltatással, amely affinitási láncot hoz létre. A jelentés törlődik, ha sikeres frissítés történik.

* **SourceId**: System.PLB
* **Tulajdonság**: **ServiceDescription**.
* **Következő lépések**: Ellenőrizze a kapcsolódó szolgáltatásleírásokat.

## <a name="partition-system-health-reports"></a>A partirendszer állapotjelentései
System.FM, amely a Feladatátvevő-kezelő szolgáltatást képviseli, az a hatóság, amely a szolgáltatáspartíciókkal kapcsolatos információkat kezeli.

### <a name="state"></a>Állapot
System.FM jelentéseket rendben, ha a partíció létrejött, és kifogástalan állapotú. Törli az entitást a jogalanyból a partíció törlésekor.

Ha a partíció a minimális replikaszám alatt van, hibát jelez. Ha a partíció nem éri el a minimális replikaszáma, de ez nem éri el a cél replika száma, figyelmeztetést jelent. Ha a partíció kvórumveszteségben van, System.FM hibát jelez.

További említésre méltó események közé tartozik egy figyelmeztetés, ha az újrakonfigurálás a vártnál tovább tart, és ha a build a vártnál tovább tart. A létrehozás és az újrakonfigurálás várható időpontjai a szolgáltatási forgatókönyvek alapján konfigurálhatók. Ha például egy szolgáltatás egy terabájtállapottal rendelkezik, például az Azure SQL Database, a build hosszabb időt vesz igénybe, mint egy kis mennyiségű állapotú szolgáltatás.

* **SourceId**: System.FM
* **Tulajdonság**: állam.
* **Következő lépések:** Ha az állapot nem rendben van, lehetséges, hogy egyes replikák nem hoztak létre, nem nyitottak meg, vagy előléptetett elsődleges vagy másodlagos helyesen. 

Ha a leírás ismerteti a kvórum elvesztése, majd megvizsgálja a részletes állapotjelentés replikák, amelyek nem, és hozza vissza őket segít, hogy a partíció újra online állapotba.

Ha a leírás az [újrakonfigurálásban](service-fabric-concepts-reconfiguration.md)ragadt partíciót írja le, akkor az elsődleges replika állapotjelentése további információkat tartalmaz.

Más System.FM állapotjelentések esetén a replikákról, a partícióról vagy a szolgáltatásról más rendszerösszetevőkről származó jelentések lesznek. 

Az alábbi példák néhány ilyen jelentést mutatnak be. 

A következő példa egy kifogástalan állapotú partíciót mutat be:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

A következő példa egy olyan partíció állapotát mutatja be, amely a célreplikaszáma alatt van. A következő lépés a partíció leírása, amely megmutatja, hogyan van konfigurálva: **MinReplicaSetSize** három és **TargetReplicaSetSize** hét. Ezután kapja meg a csomópontok számát a fürtben, amely ebben az esetben öt. Ebben az esetben két replikát nem lehet elhelyezni, mert a replikák célszáma magasabb, mint a rendelkezésre álló csomópontok száma.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

A következő példa egy olyan partíció állapotát mutatja be, amely az újrakonfigurálásban ragadt, mivel a felhasználó nem tartja tiszteletben a törlési jogkivonatot a **RunAsync** metódusban. Az elsődlegesként (P) megjelölt replika állapotjelentésének vizsgálata segíthet a probléma további részletezésében.

```powershell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
Ez az állapotjelentés az újrakonfigurálás alatt álló partíció replikáinak állapotát mutatja: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Az állapotjelentés minden egyes kópiához a következőket tartalmazza:
- Előző konfigurációs szerepkör
- Jelenlegi konfigurációs szerepkör
- [Replika állapota](service-fabric-concepts-replica-lifecycle.md)
- Az a csomópont, amelyen a replika fut
- Replika azonosítója

Egy példához hasonló esetben további vizsgálatra van szükség. Vizsgálja meg az egyes replika állapotát, `Primary` `Secondary` kezdve a replikák jelölt és (131482789658160654 és 131482789688598467) az előző példában.

### <a name="replica-constraint-violation"></a>Replikamegkötés megsértése
**A System.PLB** figyelmeztetést jelent, ha replikamegkötés-megsértést észlel, és nem tudja elhelyezni az összes partícióreplikát. A jelentés részletei azt mutatják, hogy mely korlátozások és tulajdonságok akadályozzák meg a replika elhelyezését.

* **SourceId**: System.PLB
* **Tulajdonság**: A **ReplicaConstraintViolation**kezdetével kezdődik.

## <a name="replica-system-health-reports"></a>Replikarendszer állapotjelentései
**System.RA**, amely az újrakonfigurációs ügynök összetevő, a hatóság a replika állapota.

### <a name="state"></a>Állapot
A System.RA a kópia létrehozásakor rendben van.

* **SourceId**: System.RA
* **Tulajdonság**: állam.

A következő példa egy kifogástalan állapotú replikát mutat be:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
Ez a tulajdonság figyelmeztetéseket vagy hibákat jelez, amikor megpróbál megnyitni egy replikát, vagy átvált egy kópiát egyik szerepkörből a másikba. További információ: [Replika életciklusa](service-fabric-concepts-replica-lifecycle.md). A hibák lehetnek kivételek az API-hívások vagy összeomlik a szolgáltatás gazdagép folyamat ez idő alatt. A C# kódból érkező API-hívások miatti hibák esetén a Service Fabric hozzáadja a kivételt és a veremkövetési nyomkövetést az állapotjelentéshez.

Ezek az állapotfigyelmeztetések a művelet helyi újbóli megkísérlése után jelennek meg néhány alkalommal (a házirendtől függően). A Service Fabric újrapróbálkozik a művelettel egy maximális küszöbértékig. A maximális küszöbérték elérése után megpróbálhat cselekedni a helyzet javítása érdekében. Ez a kísérlet a figyelmeztetések törlődéséhez vezethet, mivel feladja a csomóponton végzett műveletet. Ha például egy replika nem nyitja meg a csomóponton, a Service Fabric állapotfigyelmeztetést ad ki. Ha a replika továbbra sem nyílik meg, a Service Fabric önjavítási jár el. Ez a művelet magában foglalhatja ugyanazt a műveletet egy másik csomóponton. Ez a kísérlet okozza a replika számára előadott figyelmeztetés törlését. 

* **SourceId**: System.RA
* **Tulajdonság**: **ReplicaOpenStatus**, **ReplicaCloseStatus**és **ReplicaChangeRoleStatus**.
* **Következő lépések:** Vizsgálja meg a szolgáltatáskódot vagy az összeomlási memóriaképeket, hogy megállapítsa, miért a művelet sikertelen.

A következő példa egy olyan replika `TargetInvocationException` állapotát mutatja be, amely a nyílt módszerből jelenik meg. A leírás tartalmazza a hibapontot, **az IStatefulServiceReplica.Open**, a **TargetInvocationException**kivételtípust és a veremnyomkövetést.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

A következő példa egy olyan replikát mutat be, amely a zárás során folyamatosan összeomlik:

```powershell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Reconfiguration
Ez a tulajdonság jelzi, ha egy [újrakonfigurálást](service-fabric-concepts-reconfiguration.md) végző replika észleli, hogy az újrakonfigurálás leállt vagy beragadt. Ez az állapotjelentés lehet a replika, amelynek jelenlegi szerepkörelsődleges, kivéve a swap elsődleges újrakonfigurálása esetén, ahol lehet, hogy a replika, amely lefokozták az elsődleges aktív másodlagos.

Az újrakonfigurálás a következő okok valamelyike miatt ragadhat meg:

- Nem hajt végre egy művelet a helyi kópián, ugyanaz a kópia, mint az újrakonfigurálást végző kópia. Ebben az esetben a más összetevők, a System.RAP vagy a System.RE kópiájának állapotjelentései további információkat nyújthatnak.

- A művelet nem történik meg egy távoli kópián. Azok a replikák, amelyekhez műveletek vannak függőben, az állapotjelentésben vannak felsorolva. További vizsgálatot kell végezni az egészségügyi jelentések e távoli replikák. Kommunikációs problémák is lehetnek a csomópont és a távoli csomópont között.

Ritka esetekben az újrakonfigurálás a csomópont és a feladatátvételi kezelő szolgáltatás közötti kommunikáció vagy egyéb problémák miatt beragadhat.

* **SourceId**: System.RA
* **Tulajdonság**: Újrakonfigurálás.
* **Következő lépések:** Vizsgálja meg a helyi vagy távoli replikák leírásától függően az állapotjelentés.

A következő példa egy állapotjelentést mutat be, amelyben az újrakonfigurálás beragadt a helyi replika. Ebben a példában ez annak köszönhető, hogy a szolgáltatás nem tartja tiszteletben a lemondási jogkivonatot.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

A következő példa egy állapotjelentést mutat be, amelyben az újrakonfigurálás két távoli kópia válaszára vár. Ebben a példában három replikák a partíción, beleértve az aktuális elsődleges. 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

Ez az állapotjelentés azt mutatja, hogy az újrakonfigurálás két kópia válaszára vár: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Minden egyes kópiára a következő információk állnak rendelkezésre:
- Előző konfigurációs szerepkör
- Jelenlegi konfigurációs szerepkör
- [Replika állapota](service-fabric-concepts-replica-lifecycle.md)
- Csomópont azonosítója
- Replika azonosítója

Az újrakonfigurálás feloldásához:
- A **le** replikákat fel kell hozni. 
- Az **inbuild** replikák befejezéséhez kell fejeznie a build és az átmenet kész.

### <a name="slow-service-api-call"></a>Lassú szolgáltatás API-hívás
**A System.RAP** és a **System.Replicator** figyelmeztetést jelent, ha a felhasználói szolgáltatás kódjának hívása a beállított időnél tovább tart. A hívás befejezésekor a rendszer törli a figyelmeztetést.

* **SourceId**: System.RAP vagy System.Replicator
* **Tulajdonság**: A lassú API neve. A leírás további részleteket tartalmaz az API függőben lévő időpontjáról.
* **Következő lépések:** Vizsgálja meg, hogy a hívás miért tart tovább a vártnál.

A következő példa a System.RAP állapoteseményét mutatja be egy olyan megbízható szolgáltatás esetén, amely nem tartja tiszteletben a **RunAsync**lemondási jogkivonatát:

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

A tulajdonság és a szöveg jelzi, hogy melyik API ragadt meg. A következő lépések, hogy a különböző beragadt API-k eltérőek. Az *IStatefulServiceReplica* vagy az *IStatelessServiceInstance* bármely API-ja általában hiba a szolgáltatáskódban. A következő szakasz bemutatja, hogyan fordítanak ezek a [Megbízható szolgáltatások modellre:](service-fabric-reliable-services-lifecycle.md)

- **IStatefulServiceReplica.Open**: Ez a figyelmeztetés `CreateServiceInstanceListeners`azt `ICommunicationListener.OpenAsync`jelzi, hogy a `OnOpenAsync` , vagy ha felülbírált hívás a beragadt.

- **IStatefulServiceReplica.Close** és **IStatefulServiceReplica.Abort**: A leggyakoribb eset egy olyan szolgáltatás, `RunAsync`amely nem tartja tiszteletben a . Az is lehet, hogy `ICommunicationListener.CloseAsync`, vagy `OnCloseAsync` ha felülírják, beragadt.

- **IStatefulServiceReplica.ChangeRole(S)** és **IStatefulServiceReplica.ChangeRole(N)**: A leggyakoribb eset egy olyan szolgáltatás, `RunAsync`amely nem tartja tiszteletben a . Ebben a forgatókönyvben a legjobb megoldás a replika újraindítása.

- **IStatefulServiceReplica.ChangeRole(P)**: A leggyakoribb eset az, hogy a `RunAsync`szolgáltatás nem adott vissza feladatot.

Más API-hívások, amelyek elakadnak, az **IReplicator** felületen találhatók. Példa:

- **IReplicator.CatchupReplicaSet**: Ez a figyelmeztetés két dolgot jelez. Nincs elegendő replikák. Ha meg szeretné tudni, hogy ez a helyzet, tekintse meg a replika állapotát a replikák a partíción, vagy a System.FM állapotjelentés egy beragadt újrakonfigurálás. Vagy a replikák nem ismerik el a műveleteket. A PowerShell-parancsmag `Get-ServiceFabricDeployedReplicaDetail` az összes kópia állapotának meghatározására használható. A probléma a replikák, amelyek `LastAppliedReplicationSequenceNumber` értéke `CommittedSequenceNumber` az elsődleges értéke mögött van.

- **IReplicator.BuildReplica(\<Távoli replikálid>) ) :** Ez a figyelmeztetés a létrehozási folyamat problémájára utal. További információ: [Replika életciklusa](service-fabric-concepts-replica-lifecycle.md). Ennek oka lehet a replikátor cím helytelen konfigurálása. További információ: [Állapotalapú megbízható szolgáltatások konfigurálása](service-fabric-reliable-services-configuration.md) és [erőforrások megadása a szolgáltatásjegyzékben.](service-fabric-service-manifest-resources.md) Ez a távoli csomóponton is problémát jelenthet.

### <a name="replicator-system-health-reports"></a>Replikátor rendszer állapotjelentései
**A replikációs várólista megtelt:**
**A System.Replicator** figyelmeztetést jelent, ha a replikációs várólista megtelt. Az elsődleges, a replikációs várólista általában megtelik, mert egy vagy több másodlagos replikák lassan nyugtázza a műveleteket. A másodlagos, ez általában akkor történik, ha a szolgáltatás lassan alkalmazza a műveleteket. A figyelmeztetés törlődik, ha a várólista már nem teljes.

* **SourceId**: System.Replicator
* **Tulajdonság**: **PrimaryReplicationQueueStatus** vagy **SecondaryReplicationQueueStatus**, a replikaszerepkörtől függően.
* **Következő lépések**: Ha a jelentés az elsődleges, ellenőrizze a kapcsolatot a csomópont a fürtben. Ha az összes kapcsolat kifogástalan, előfordulhat, hogy legalább egy lassú másodlagos magas lemezkésés a műveletek alkalmazása. Ha a jelentés a másodlagos, ellenőrizze a lemezhasználat és a teljesítmény a csomóponton először. Ezután ellenőrizze a kimenő kapcsolatot a lassú csomóponttól az elsődlegesig.

**RemoteReplicatorConnectionStatus:**
**System.Replicator** az elsődleges replika jelenti a figyelmeztetést, ha a kapcsolat egy másodlagos (távoli) replikátor nem kifogástalan. A távoli replikátor címe megjelenik a jelentés üzenetében, így kényelmesebb felismerni, ha a rossz konfiguráció tért át, vagy ha hálózati problémák vannak a replikátorok között.

* **SourceId**: System.Replicator
* **Tulajdonság**: **RemoteReplicatorConnectionStatus**.
* **Következő lépések:** Ellenőrizze a hibaüzenetet, és győződjön meg arról, hogy a távoli replikátor cím megfelelően van konfigurálva. Ha például a távoli replikátor a "localhost" figyelési címmel nyílik meg, kívülről nem érhető el. Ha a cím helyesnek tűnik, ellenőrizze az elsődleges csomópont és a távoli cím közötti kapcsolatot, hogy megtalálja az esetleges hálózati problémákat.

### <a name="replication-queue-full"></a>A replikációs várólista megtelt
**A System.Replicator** figyelmeztetést jelent, ha a replikációs várólista megtelt. Az elsődleges, a replikációs várólista általában megtelik, mert egy vagy több másodlagos replikák lassan nyugtázza a műveleteket. A másodlagos, ez általában akkor történik, ha a szolgáltatás lassan alkalmazza a műveleteket. A figyelmeztetés törlődik, ha a várólista már nem teljes.

* **SourceId**: System.Replicator
* **Tulajdonság**: **PrimaryReplicationQueueStatus** vagy **SecondaryReplicationQueueStatus**, a replikaszerepkörtől függően.

### <a name="slow-naming-operations"></a>Lassú elnevezési műveletek
**A System.NamingService** az elsődleges replikán jelenti az állapotot, ha egy elnevezési művelet az elfogadhatónál hosszabb időt vesz igénybe. Elnevezési műveletek például [a CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) vagy [a DeleteServiceAsync.](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) További módszerek találhatók a FabricClient alatt. Például a [szolgáltatáskezelési módszerek](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) vagy [a vagyonkezelési módszerek](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient)alatt találhatók.

> [!NOTE]
> Az elnevezési szolgáltatás feloldja a szolgáltatásneveket a fürt egy helyére. A felhasználók a szolgáltatásnevek és -tulajdonságok kezelésére használhatják. Ez egy Service Fabric particionált-tartósan szolgáltatás. Az egyik partíció a *Hatóság tulajdonosa,* amely metaadatokat tartalmaz az összes Service Fabric nevek és szolgáltatások. A Service Fabric-nevek vannak leképezve *Name Owner* a különböző partíciók, az úgynevezett névtulajdonos-partíciók, így a szolgáltatás bővíthető. További információ az [elnevezési szolgáltatásról.](service-fabric-architecture.md)
> 
> 

Ha egy elnevezési művelet a vártnál tovább tart, a művelet a műveletet kiszolgáló elnevezési szolgáltatáspartíció elsődleges replikájára vonatkozó figyelmeztető jelentéssel van megjelölve. Ha a művelet sikeresen befejeződik, a figyelmeztetés törlődik. Ha a művelet egy hibával fejeződik be, az állapotjelentés a hiba részleteit tartalmazza.

* **SourceId**: System.NamingService
* **Tulajdonság**: A " Duration_ " előtaggal**kezdődik,** és azonosítja a lassú működést és a Service Fabric nevét, amelyre a műveletet alkalmazza. Ha például a **fabric:/MyApp/MyService** névvel létrehozott szolgáltatás túl sokáig tart, a tulajdonság **Duration_AOCreateService.fabric:/MyApp/MyService .** Az "AO" a név és a művelet elnevezési partíciójának szerepkörére utal.
* **Következő lépések**: Ellenőrizze, hogy miért nem sikerül az elnevezési művelet. Minden műveletnek különböző kiváltó okai lehetnek. Előfordulhat például, hogy a törlési szolgáltatás beragadt. Előfordulhat, hogy a szolgáltatás elakad, mert az alkalmazásgazda folyamatosan összeomlik egy csomóponton a szolgáltatáskódban lévő felhasználói hiba miatt.

A következő példa egy létrehozási szolgáltatás műveletet mutat be. A művelet hosszabb időt vett igénybe, mint a beállított időtartam. Az "AO" újrapróbálkozik, és a munkát "NEM" számra küldi. A "NO" az utolsó műveletet a TIMEOUT-mal végezte. Ebben az esetben ugyanaz a replika elsődleges az "AO" és a "NO" szerepkörökhöz.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>Telepített Alkalmazás rendszerállapot-jelentései
**System.Hosting** a hatóság telepített entitások.

### <a name="activation"></a>Aktiválás
A System.Hosting rendben van, ha egy alkalmazás sikeresen aktiválva van a csomóponton. Ellenkező esetben hibát jelez.

* **SourceId**: System.Hosting
* **Tulajdonság**: **Aktiválás**, beleértve a bevezetési verziót is.
* **Következő lépések**: Ha az alkalmazás nem kifogástalan, vizsgálja meg, hogy miért nem sikerült az aktiválás.

A következő példa sikeres aktiválást mutat be:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Letöltés
A System.Hosting hibát jelez, ha az alkalmazáscsomag letöltése sikertelen.

* **SourceId**: System.Hosting
* **Tulajdonság**: **Letöltés**, beleértve a bevezetési verziót is.
* **Következő lépések:** Vizsgálja meg, hogy miért nem sikerült a letöltés a csomóponton.

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage rendszer állapotjelentései
**System.Hosting** a hatóság telepített entitások.

### <a name="service-package-activation"></a>Szolgáltatási csomag aktiválása
A System.Hosting rendben van, ha a szolgáltatáscsomag aktiválása sikeres. Ellenkező esetben hibát jelez.

* **SourceId**: System.Hosting
* **Tulajdonság**: Aktiválás.
* **Következő lépések**: Vizsgálja meg, hogy miért nem sikerült az aktiválás.

### <a name="code-package-activation"></a>Kódcsomag aktiválása
A System.Hosting minden kódcsomag esetében rendben van, ha az aktiválás sikeres. Ha az aktiválás sikertelen, a beállított módon figyelmeztetést jelent. Ha **a CodePackage** nem tudja aktiválni vagy leállítani a beállított **CodePackageHealthErrorThreshold-nál**nagyobb hibával, a rendszer hibát jelez. Ha egy szolgáltatáscsomag több kódcsomagot tartalmaz, mindegyikhez aktiválási jelentés jön létre.

* **SourceId**: System.Hosting
* **Tulajdonság**: A **CodePackageActivation** előtagot használja, és tartalmazza a kódcsomag nevét és a belépési pontot *CodePackageActivation:CodePackageName:SetupEntryPoint/EntryPoint néven.* Például **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Szolgáltatástípus regisztrálása
A System.Hosting rendben van, ha a szolgáltatástípus regisztrálva van. Hibát jelez, ha a regisztráció nem történt meg időben, **aserviceTypeRegistrationTimeout**használatával konfigurálva. Ha a futásidő le van zárva, a szolgáltatás típusa nem regisztrált a csomópontról, és az üzemeltetési jelentések egy figyelmeztetést.

* **SourceId**: System.Hosting
* **Tulajdonság**: A **ServiceTypeRegistration** előtagot használja, és tartalmazza a szolgáltatástípus nevét. Például **ServiceTypeRegistration:FileStoreServiceType**.

A következő példa egy kifogástalan állapotú üzembe helyezett szolgáltatáscsomagot mutat be:

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Letöltés
A System.Hosting hibát jelez, ha a szolgáltatáscsomag letöltése sikertelen.

* **SourceId**: System.Hosting
* **Tulajdonság**: **Letöltés**, beleértve a bevezetési verziót is.
* **Következő lépések:** Vizsgálja meg, hogy miért nem sikerült a letöltés a csomóponton.

### <a name="upgrade-validation"></a>Frissítés érvényesítése
A System.Hosting hibát jelez, ha a frissítés során az érvényesítés sikertelen, vagy ha a frissítés sikertelen lesz a csomóponton.

* **SourceId**: System.Hosting
* **Tulajdonság**: A **FabricUpgradeValidation** előtagot használja, és tartalmazza a frissítési verziót.
* **Leírás**: A felmerült hibára mutat.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Nem definiált csomópontkapacitás az erőforrás-irányítási mérőszámokhoz
A System.Hosting figyelmeztetést jelent, ha a csomópont kapacitása nincs definiálva a fürtjegyzékben, és az automatikus észlelési konfiguráció ki van kapcsolva. A Service Fabric állapotjelzőt ad ki, ha a szolgáltatáscsomag, amely [erőforrás-cégirányítási](service-fabric-resource-governance.md) regiszterek egy adott csomóponton.

* **SourceId**: System.Hosting
* **Ingatlan**: **ResourceGovernance**.
* **Következő lépések:** A probléma megoldásának előnyben részesített módja a fürtjegyzék módosítása a rendelkezésre álló erőforrások automatikus észlelésének lehetővé tétele érdekében. Egy másik módja a fürtjegyzék frissítése helyesen megadott csomópont kapacitásokkal ezekhez a metrikákhoz.

## <a name="next-steps"></a>További lépések
* [A Service Fabric állapotjelentésének megtekintése](service-fabric-view-entities-aggregated-health.md)

* [A szolgáltatás állapotának jelentése és ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [A szolgáltatások helyi figyelése és diagnosztikája](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [A Service Fabric alkalmazásfrissítése](service-fabric-application-upgrade.md)

