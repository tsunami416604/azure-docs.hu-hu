---
title: Alkalmazásfrissítések hibaelhárítása |} A Microsoft Docs
description: Ez a cikk néhány olyan gyakori problémát körül frissítése a Service Fabric-alkalmazások és azok megoldását ismerteti.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 19ad152e-ec50-4327-9f19-065c875c003c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: e393eb92e11dc8dc296f1dc5f1c0036566c285c5
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58792450"
---
# <a name="troubleshoot-application-upgrades"></a>Alkalmazásfrissítések hibaelhárítása

Ez a cikk ismerteti a gyakori problémák körül frissítése az Azure Service Fabric-alkalmazás és azok megoldását.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Egy sikertelen alkalmazásfrissítés hibaelhárítása

Ha egy frissítés sikertelen, a kimenetét a **Get-ServiceFabricApplicationUpgrade** parancsot a hibakereséshez a hiba további információkat tartalmaz.  Az alábbi listában határozza meg, hogyan használható a további információk:

1. Azonosítsa a hiba típusát.
2. A hiba okának azonosításához.
3. Különítse el a további vizsgálat egy vagy több hibás összetevőt.

Ez az információ érhető el, amikor a Service Fabric észleli a hibát, függetlenül attól, hogy a **FailureAction** állítja vissza, vagy a frissítés felfüggesztése.

### <a name="identify-the-failure-type"></a>Azonosítsa a hiba típusa

A kimenetben az **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** időbélyegzőjének (UTC), amelyen egy frissítési hiba a Service Fabric észlelt azonosítja és  **FailureAction** lett elindítva. **FailureReason** azonosítja a hiba három lehetséges magas szintű okok egyike:

1. UpgradeDomainTimeout - azt jelzi, hogy az adott frissítési tartományban túl sokáig tartott, és **UpgradeDomainTimeout** lejárt.
2. OverallUpgradeTimeout - azt jelzi, hogy a teljes frissítés túl sokáig tartott, és **UpgradeTimeout** lejárt.
3. HealthCheck - azt jelzi, hogy egy frissítési tartományt a frissítés után az alkalmazás maradt a meghatározott házirendek szerint megfelelő és **HealthCheckRetryTimeout** lejárt.

Ezek a bejegyzések csak jelenik meg a kimenetet a frissítés sikertelen lesz, és elindítja a visszaállítása. További információk jelennek meg a hiba típusától függően.

### <a name="investigate-upgrade-timeouts"></a>Vizsgálja meg a frissítés időtúllépése

Frissítse az időtúllépési hibákat leggyakrabban szolgáltatás rendelkezésre állási problémák okozzák. A bekezdés kimenete a frissítési tipikus ahol szolgáltatás replikák és példányok nem indulnak el az új verzióban kódot. A **UpgradeDomainProgressAtFailure** mező rögzíti a hiba idején függőben lévő frissítési munka pillanatképét.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
```

Ebben a példában a frissítés nem sikerült a frissítési tartomány *MYUD1* és a két partíció (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* és *4b43f4d8-b26b-424e-9307-7a7a62e79750*) is elakadt. A partíciók is elakadt, mert a modul nem tudott helyezze el az elsődleges replika (*WaitForPrimaryPlacement*) a célcsomópontokat *csomópont1* és *csomópont4*.

A **Get-ServiceFabricNode** parancs is használható, és ellenőrizze, hogy a két csomópont a frissítési tartomány *MYUD1*. A *UpgradePhase* szerint *PostUpgradeSafetyCheck*, ami azt jelenti, hogy a biztonsági ellenőrzés után a frissítési tartomány összes csomópontjának frissítése is megjelenhetnek. Ez az információ az új verzióval, az alkalmazás kódja egy lehetséges hibát mutat. A leggyakoribb problémák a nyílt vagy promóciós kód elsődleges elérési utakhoz hibákat.

Egy *UpgradePhase* , *PreUpgradeSafetyCheck* azt jelenti, hogy a problémák fel a frissítési tartomány előkészítése, mielőtt hajtotta végre. A leggyakoribb problémák ebben az esetben a zárja be vagy a lefokozási elsődleges kódhoz tartozó elérési út a hibákat.

Az aktuális **UpgradeState** van *RollingBackCompleted*, ezért az eredeti frissítés kell hajtottak végre a visszaállítás **FailureAction**, amely automatikusan összesített biztonsági hiba esetén a frissítés. Ha az eredeti frissítés hajtottak végre a kézi **FailureAction**, majd a frissítés szeretné inkább kell felfüggesztett állapotban, hogy az élő Hibakeresés az alkalmazás.

Ritka esetekben a **UpgradeDomainProgressAtFailure** mező üres, ha a teljes frissítés időkorlátja lejár, ugyanúgy, mint a rendszer kitölti a jelenlegi frissítési tartománya az összes munkahelyi is lehet. Ha ez történik, próbálja meg növelni a **UpgradeTimeout** és **UpgradeDomainTimeout** frissítse a paraméter értékét, és próbálkozzon újra a frissítést.

### <a name="investigate-health-check-failures"></a>Állapot-ellenőrzés hibák vizsgálata

Állapot ellenőrzése hibákat is elindítható a különböző problémákat, a frissítési tartomány összes csomópontjának frissítése és sikeres összes biztonsági ellenőrzés befejezése után folytatható. A bekezdés kimenete egy frissítési hiba miatt sikertelen állapot-ellenőrzések jellemző. A **UnhealthyEvaluations** mező rögzíti, amelyek nem sikerült a frissítés szerint a megadott időpontban állapot-ellenőrzések pillanatképét [állapotházirend](service-fabric-health-introduction.md).

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

Először állapotának ellenőrzése hibák kivizsgálása a Service Fabric állapotmodell ismeretét igényli. Azonban az ilyen közelebbről is megismerheti, nélkül is láthatjuk, hogy sérült állapotban-e két szolgáltatás: *fabric: / DemoApp/Svc3* és *fabric: / DemoApp/Svc2*, a hiba rendszerállapot-jelentések ("InjectedFault" együtt Ebben az esetben). Ebben a példában két kívüli négy szolgáltatások sérült állapotban, amely az alapértelmezett nem megfelelő állapotú 0 %-os célérték alatt van (*MaxPercentUnhealthyServices*).

A frissítés fel lett függesztve meghibásodása esetén adjon meg egy **FailureAction** manuális a frissítés indításakor. Ez az üzemmód lehetővé teszi az élő rendszer, a sikertelen állapotú további műveletek végrehajtása előtti.

### <a name="recover-from-a-suspended-upgrade"></a>A felfüggesztett frissítés helyreállítása

A visszaállítás **FailureAction**, nem szükséges, mivel a frissítés automatikusan visszaállítja után sikertelen helyreállítási van. A kézi **FailureAction**, több helyreállítási lehetőség:

1.  visszaállítás az eseményindító
2. Folytassa a fennmaradó részében a frissítés manuálisan
3. A figyelt frissítés folytatása

A **Start-ServiceFabricApplicationRollback** parancs használható bármikor visszaállítása az alkalmazás elindításához. A parancs sikeresen adja vissza, miután a visszaállítási kérés regisztrálva van a rendszerben, és ezt követően kevéssel elindul.

A **Resume-ServiceFabricApplicationUpgrade** parancs segítségével végezze el a frissítés hátralévő manuálisan, egyszerre több frissítési tartományt. Ebben a módban a rendszer egyetlen biztonsági ellenőrzéseket hajtja végre. Nincs több állapotellenőrzést végez. Ez a parancs csak lehet használni a *UpgradeState* látható *RollingForwardPending*, ami azt jelenti, hogy az aktuális frissítési tartomány frissítése befejeződött, de a következő nem indult el (folyamatban).

A **Update-ServiceFabricApplicationUpgrade** parancs használható mindkét biztonsági a figyelt frissítés folytatásához és állapot-ellenőrzések folyamatban van.

```powershell
Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored
```

```Output
UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

A frissítés továbbra is fennáll, a frissítési tartomány, ahol utolsó felfüggesztés és azonos frissítési paraméterek és az állapotházirendeket, mielőtt használja. Szükség esetén a frissítési paraméterek és a fenti kimenetben állapotházirendeket módosíthatja ugyanazt a parancsot a Ha folytatja a frissítést. Ebben a példában a frissítés folytatódik figyelt módban, a paraméterek és az állapotházirendeket változatlan marad.

## <a name="further-troubleshooting"></a>További hibaelhárítás

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>A Service Fabric nem követik a megadott állapotházirendeket

Lehetséges okok: 1:

A Service Fabric összes százalékos fordítja le a tényleges szám az entitások (például a replikákat, partíciók és szolgáltatások) állapotának kiértékelését, és mindig felfelé kerekít egész entitásokat. Például ha a maximális *MaxPercentUnhealthyReplicasPerPartition* 21 %, és öt replikákat, akkor a Service Fabric lehetővé teszi, hogy a két sérült replikák (azaz`Math.Ceiling (5*0.21)`). Állapotházirendeket, ennek megfelelően kell állítani.

Lehetséges ok: 2:

Teljes szolgáltatások és a nem meghatározott szolgáltatáspéldányok százalékos állapotházirendeket vannak megadva. Például, ha egy alkalmazásnak négy frissítés előtt szolgáltatás példányai A, B, C és D, ahol D szolgáltatás állapota nem megfelelő, de csekély hatással van az alkalmazáshoz. Szeretnénk figyelmen kívül hagyja az ismert nem megfelelő állapotú szolgáltatás D, a frissítés során, és állítsa be a paraméter *MaxPercentUnhealthyServices* 25 %-kal, feltéve, hogy csak A, B és C kell állapota megfelelő lesz.

Azonban a frissítés során D előfordulhat, hogy állapotúak lesznek, amíg C akkor kerül sérült. A frissítés továbbra is járnak, mivel csak 25 %-a szolgáltatások nem megfelelő állapotú. Azonban ez azt eredményezheti, hogy a nem várt hibák miatt folyamatban váratlanul nem megfelelő állapotú D. helyett C Ebben a helyzetben D kell modellezni egy másik szolgáltatás típusaként a, B és c jelöli. Mivel állapotházirendeket szolgáltatás típus szerint meg van adva, különböző nem megfelelő állapotú százalékos küszöbérték különböző szolgáltatások alkalmazhatók. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>E nem adta meg az alkalmazás frissítését állapotházirend, de bizonyos időtúllépési soha nem megadott továbbra is sikertelen, a frissítés

Amikor állapotházirendeket nem biztosított a frissítési kérést, akkor megnyílik a a *ApplicationManifest.xml* , a jelenlegi verziója. Például ha az 1.0-s verzió alkalmazás állapotházirendeket, 2.0-s verziójában az 1.0-s verziója a megadott X alkalmazás frissít szolgálnak. Ha a frissítés egy másik állapotházirend kell használni, majd a házirendet kell adni az alkalmazás frissítési API-hívás részeként. Az API-hívás részeként megadott szabályzatok csak a frissítés során érvényesek. Ha a frissítés befejeződött, a házirendek megadott a *ApplicationManifest.xml* szolgálnak.

### <a name="incorrect-time-outs-are-specified"></a>Helytelen gondok vannak megadva.

Akkor lehet, hogy rendelkezik testreszabásakor nem tudta arról, hogy mi történik, ha gondok vannak beállítva működése. Például előfordulhat, hogy rendelkezik egy *UpgradeTimeout* hogy kisebb, mint a *UpgradeDomainTimeout*. A válasz az, hogy hibát ad vissza. Ha a hibát a *UpgradeDomainTimeout* kisebb, mint az összegét *HealthCheckWaitDuration* és *HealthCheckRetryTimeout*, vagy ha  *UpgradeDomainTimeout* kisebb, mint az összegét *HealthCheckWaitDuration* és *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>A verziófrissítések túl sokáig tart

Való frissítés befejezéséhez szükséges idő attól függ, az állapot-ellenőrzések és a megadott időtúllépést. Mennyi ideig tart másolja, üzembe helyezését és az alkalmazás stabilizálódhatnak mobilhálózat állapot-ellenőrzések és időtúllépéseket. Túl agresszív a várakozási idő alatt előfordulhat, hogy jelenti azt, hogy több sikertelen frissítését, ezért javasoljuk, hogy hosszabb gondok konzervatív módon vegyen figyelembe kezdve.

Íme egy gyors frissítő hogyan kommunikáljanak az a várakozási idő a frissítési idő a:

Frissíti egy frissítési tartományt nem tudja végrehajtani a gyorsabb, mint a *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Frissítési hiba esetén nem hajtható végre gyorsabban *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

A frissítési idő a frissítési tartomány korlátozza *UpgradeDomainTimeout*.  Ha *HealthCheckRetryTimeout* és *HealthCheckStableDuration* is nullától eltérő, és az alkalmazás állapotának tartja oda-vissza vált, majd a frissítés végül túllépi az időkorlátot a *UpgradeDomainTimeout*. *UpgradeDomainTimeout* elindítja a számlálási leskálázás egyszer a frissítés számára az aktuális frissítési tartomány kezdete.

## <a name="next-steps"></a>További lépések

[Az alkalmazás használatával a Visual Studio frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti egy alkalmazás frissítése a Visual Studio használatával.

[Az alkalmazás használatával Powershell frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti egy alkalmazás frissítése a PowerShell használatával.

Vezérelheti, hogyan az alkalmazásfrissítések használatával [frissítési paraméterek](service-fabric-application-upgrade-parameters.md).

Hogyan használja az alkalmazásfrissítések kompatibilissé [adatok szerializálása](service-fabric-application-upgrade-data-serialization.md).

Speciális funkciók használata közben lépésként tekintse át az alkalmazás frissítéséhez [haladó témakörök](service-fabric-application-upgrade-advanced.md).