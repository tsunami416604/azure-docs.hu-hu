---
title: Alkalmazásfrissítések – hibaelhárítás
description: Ez a cikk néhány gyakori problémát ismerteti a Service Fabric-alkalmazások frissítésével és azok megoldásával kapcsolatban.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d462f2c2482e0fbb4d252967754a9675ed362674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377922"
---
# <a name="troubleshoot-application-upgrades"></a>Alkalmazásfrissítések hibaelhárítása

Ez a cikk ismerteti az Azure Service Fabric-alkalmazások frissítésével kapcsolatos gyakori problémákat, és hogyan oldhatja meg őket.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Sikertelen alkalmazásfrissítés – problémamegoldás

Ha egy frissítés sikertelen, a **Get-ServiceFabricApplicationUpgrade** parancs kimenete további információkat tartalmaz a hiba hibakereséséhez.  Az alábbi lista meghatározza a további információk felhasználhatósul történő fel- és felolásmódját:

1. Azonosítsa a hiba típusát.
2. Azonosítsa a hiba okát.
3. Egy vagy több hibás összetevő elkülönítése további vizsgálat céljából.

Ez az információ akkor érhető el, ha a Service Fabric észleli a hibát, függetlenül attól, hogy a **FailureAction** a visszaállítása vagy a frissítés felfüggesztése.

### <a name="identify-the-failure-type"></a>A hibatípus azonosítása

A **Get-ServiceFabricApplicationUpgrade**kimenetében **a FailureTimestampUtc** azonosítja azt az időbélyeget (UTC-ben), amelyen a Service Fabric frissítési hibát észlelt, és **a FailureAction** aktiválódott. **A FailureReason** a hiba három lehetséges magas szintű okának egyikét azonosítja:

1. UpgradeDomainTimeout – Azt jelzi, hogy egy adott frissítési tartomány befejezése túl sokáig tartott, és **a UpgradeDomainTimeout** lejárt.
2. OverallUpgradeTimeout – Azt jelzi, hogy a teljes frissítés túl sokáig tartott, és **upgradeTimeout** lejárt.
3. HealthCheck - Azt jelzi, hogy egy frissítési tartomány frissítése után az alkalmazás a megadott állapotházirendek és **healthCheckRetryTimeout** lejárt állapotának megfelelően nem megfelelően maradt.

Ezek a bejegyzések csak akkor jelennek meg a kimenetben, ha a frissítés sikertelen, és megkezdi a visszaállítást. A hiba típusától függően további információk jelennek meg.

### <a name="investigate-upgrade-timeouts"></a>Frissítési időmegtinák vizsgálata

A frissítési időmegmaradási hibákat leggyakrabban a szolgáltatás rendelkezésre állásával kapcsolatos problémák okozzák. Az ezt a bekezdést követő kimenet jellemző azokra a frissítésekre, ahol a szolgáltatás replikái vagy példányai nem indulnak el az új kódverzióban. Az **UpgradeDomainProgressAtFailure** mező pillanatképet rögzít a sikertelenség időpontjában folyamatban lévő frissítési munkáról.

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

Ebben a példában a frissítés nem sikerült a frissítési tartomány *MYUD1* és két partíció (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* és *4b43f4d8-b26b-424e-9307-7a7a62e79750*) ragadt. A partíciók beragadtak, mert a futásidő nem tudta elhelyezni az elsődleges replikákat (*WaitForPrimaryPlacement*) a *csomópont1* és a *4 csomópont*célcsomópontjára.

A **Get-ServiceFabricNode** parancs segítségével ellenőrizhető, hogy ez a két csomópont *a MYUD1*frissítési tartományban van-e. A *UpgradePhase* a *PostUpgradeSafetyCheck ( PostUpgradeSafetyCheck*) parancsra szól, ami azt jelenti, hogy ezek a biztonsági ellenőrzések a frissítési tartomány összes csomópontjának frissítése után következnek be. Mindezek az információk az alkalmazáskód új verziójával kapcsolatos potenciális problémára utalnak. A leggyakoribb problémák a szolgáltatási hibák a nyílt vagy előléptetés elsődleges kód elérési utak.

A *PreUpgradeSafetyCheck* *upgradephase-e* azt jelenti, hogy problémák merültek fel a frissítési tartomány előkészítésekor a végrehajtás előtt. A leggyakoribb problémák ebben az esetben a szolgáltatás hibák a bezárása vagy lefokozása az elsődleges kód elérési utak.

Az aktuális **UpgradeState** *a RollingBackCompleted*, így az eredeti frissítést egy visszaállítási **failureAction**művelettel kell végrehajtani, amely hiba esetén automatikusan visszaállította a frissítést. Ha az eredeti frissítés manuális **FailureAction**művelettel történt, akkor a frissítés ehelyett felfüggesztett állapotban lenne, hogy engedélyezze az alkalmazás élő hibakeresését.

Ritka esetekben az **UpgradeDomainProgressAtFailure** mező üres lehet, ha a teljes frissítés idáig elévül, ahogy a rendszer befejezi az aktuális frissítési tartomány összes munkáját. Ebben az esetben próbálja meg növelni az **UpgradeTimeout** és a **UpgradeDomainTimeout** frissítési paraméter értékeit, majd próbálkozzon újra a frissítéssel.

### <a name="investigate-health-check-failures"></a>Állapot-ellenőrzési hibák vizsgálata

Állapot-ellenőrzési hibák at válthat ki a különböző problémák, amelyek akkor fordulhat elő, miután az összes csomópont egy frissítési tartományban befejezni a frissítést, és az összes biztonsági ellenőrzés átadva. Az ezt a bekezdést követő kimenet a sikertelen állapotellenőrzések miatti frissítési hibára jellemző. Az **UnhealthyEvaluations** mező pillanatképet rögzít az állapotellenőrzésekről, amelyek a frissítés időpontjában a megadott [állapotházirendnek](service-fabric-health-introduction.md)megfelelően sikertelenek.

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

Állapot-ellenőrzési hibák vizsgálata először a Service Fabric állapotmodell megértéséhez szükséges. De még egy ilyen alapos megértés nélkül is láthatjuk, hogy két szolgáltatás nem kifogástalan: *fabric:/DemoApp/Svc3* és *fabric:/DemoApp/Svc2*, valamint a hibaállapot-jelentések ("InjectedFault" ebben az esetben). Ebben a példában négyből két szolgáltatás nem kifogástalan, ami nem éri el a 0%-os nem megfelelő *(MaxPercentUnhealthyServices)* alapértelmezett célértéket.

A frissítés a frissítés indításakor a manuális **hibaművelet** megadásával lett felfüggesztve. Ez a mód lehetővé teszi számunkra, hogy vizsgálja meg az élő rendszer a hibás állapotban, mielőtt bármilyen további lépéseket.

### <a name="recover-from-a-suspended-upgrade"></a>Helyreállítás felfüggesztett frissítésből

A **visszaállítási FailureAction**, nincs szükség helyreállításra, mivel a frissítés automatikusan visszaáll a sikertelen. A manuális **FailureAction**segítségével számos helyreállítási lehetőség létezik:

1.  visszaállítás aktiválása
2. A frissítés hátralévő részének manuális végigfolytatása
3. A figyelt frissítés folytatása

A **Start-ServiceFabricApplicationRollback** parancs bármikor használható az alkalmazás visszaállításának elindításához. Miután a parancs sikeresen visszatér, a visszaállítási kérelem regisztrálva lett a rendszerben, és röviddel ezután kezdődik.

A **Resume-ServiceFabricApplicationUpgrade** parancs segítségével manuálisan, egyszerre egy frissítési tartományban haladhat végig a frissítés hátralévő részében. Ebben az üzemmódban a rendszer csak biztonsági ellenőrzéseket végez. A rendszer nem végez több állapotellenőrzést. Ez a parancs csak akkor használható, ha az *UpgradeState* a *RollingForwardPending*programot jeleníti meg, ami azt jelenti, hogy az aktuális frissítési tartomány frissítése befejeződött, de a következő nem indult el (függőben).

Az **Update-ServiceFabricApplicationUpgrade** parancs segítségével folytathatja a figyelt frissítést a biztonsági és állapoti ellenőrzés ekkel.

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

A frissítés a frissítési tartományból folytatódik, ahol utoljára felfüggesztették, és ugyanazokat a frissítési paramétereket és állapotházirendeket használja, mint korábban. Szükség esetén az előző kimenetben látható frissítési paraméterek és állapotházirendek bármelyike ugyanebben a parancsban módosítható, amikor a frissítés folytatódik. Ebben a példában a frissítés figyelt módban folytatódott, a paraméterek és az állapotházirendek változatlanok.

## <a name="further-troubleshooting"></a>További hibaelhárítás

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>A Service Fabric nem követi a megadott állapotházirendeket

Lehetséges ok 1:

A Service Fabric az összes százalékot az entitások (például replikák, partíciók és szolgáltatások) tényleges számaként fordítja le az állapotkiértékeléshez, és mindig egész entitásokra kerekít. Ha például a *maximális MaxPercentUnhealthyReplicasPerPartition* érték 21% és öt replikák, majd a Service Fabric`Math.Ceiling (5*0.21)`lehetővé teszi, hogy legfeljebb két nem megfelelő állapotú replikák (azaz, ). Ezért az egészségügyi politikákat ennek megfelelően kell meghatározni.

Lehetséges ok 2:

Az állapotházirendek az összes szolgáltatás százalékos arányában vannak megadva, nem pedig adott szolgáltatáspéldányokban. Például egy frissítés előtt, ha egy alkalmazás négy a, B, C és D szolgáltatáspéldányt, ahol a D szolgáltatás nem kifogástalan, de kevés hatással van az alkalmazásra. Azt szeretnénk, hogy figyelmen kívül hagyja az ismert nem megfelelő állapotú D szolgáltatás frissítés során, és állítsa be a paraméter *MaxPercentUnhealthyServices* 25%, feltételezve, hogy csak A, B és C kell kifogástalan.

Azonban a frissítés során, D válhat kifogástalan, míg a C lesz egészségtelen. A frissítés továbbra is sikeres, mert a szolgáltatásoknak csak 25%-a nem kifogástalan. Azonban előfordulhat, hogy nem várt hibák miatt C váratlanul nem kifogástalan helyett D. Ebben az esetben a D-t az A, B és C szolgáltatástípustól eltérő szolgáltatástípusként kell modellezni. Mivel az állapotházirendek szolgáltatástípusonként vannak megadva, különböző nem megfelelő állapotú százalékos küszöbértékek alkalmazhatók a különböző szolgáltatásokra. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Nem adtam meg állapotházirendet az alkalmazásfrissítéshez, de a frissítés még mindig sikertelen bizonyos időtúlképek esetén, amelyeket soha nem adtam meg

Ha az állapotházirendek nem kapnak a frissítési kérelemhez, azok az aktuális alkalmazásverzió *ApplicationManifest.xml* verziójából származnak. Ha például az X alkalmazást az 1.0-s verzióról a 2.0-s verzióra frissíti, a rendszer az 1.0-s verzióban megadott alkalmazásállapot-házirendeket használja. Ha egy másik állapotházirendet kell használni a frissítéshez, majd a szabályzatot meg kell adni az alkalmazásfrissítési API-hívás részeként. Az API-hívás részeként megadott házirendek csak a frissítés során érvényesek. A frissítés befejezése után az *ApplicationManifest.xml* fájlban megadott házirendek lesznek használatban.

### <a name="incorrect-time-outs-are-specified"></a>Helytelen időkiidők vannak megadva

Lehet, hogy vajon mi történik, ha időtúl-out van beállítva következetlenül. Előfordulhat például, hogy egy *UpgradeTimeout* kevesebb, mint a *UpgradeDomainTimeout*. A válasz az, hogy egy hiba ad vissza. Hibákat ad vissza, ha a *UpgradeDomainTimeout* kisebb, mint a *HealthCheckWaitDuration* és a *HealthCheckRetryTimeout*összege, vagy ha az *UpgradeDomainTimeout* kisebb, mint a *HealthCheckWaitDuration* és a *HealthCheckStableDuration*összege.

### <a name="my-upgrades-are-taking-too-long"></a>A frissítések túl sokáig tartanak

A frissítés befejezéséhez szükséges idő a megadott állapotellenőrzésektől és időtúl-megtatással függ. Az állapotellenőrzések és az időtúlellenőrzések attól függnek, hogy mennyi ideig tart az alkalmazás másolása, üzembe helyezése és stabilizálása. Ha túl agresszív az időtúllépések több sikertelen frissítéseket, ezért azt javasoljuk, hogy a hosszabb időtúllépések óvatosan.

Íme egy gyors frissítő arról, hogy az időtúlidőszakok hogyan hatnak a frissítési időkre:

A frissítési tartomány frissítései nem fejeződhetnek be gyorsabban, mint *a HealthCheckWaitDuration* + *HealthCheckStableDuration*.

A frissítési hiba nem fordulhat elő gyorsabban, mint *a HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

A frissítési tartomány frissítési idejét az *UpgradeDomainTimeout*korlátozza.  Ha *a HealthCheckRetryTimeout* és a *HealthCheckStableDuration* egyaránt nem nulla, és az alkalmazás állapota folyamatosan vált oda-vissza, akkor a frissítés végül időzést a *UpgradeDomainTimeout*. *Az UpgradeDomainTimeout* az aktuális frissítési tartomány frissítésének megkezdéseután elkezdi a visszaszámlálást.

## <a name="next-steps"></a>További lépések

[Az alkalmazás Visual Studio használatával történő frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti az alkalmazás frissítésén a Visual Studió használatával.

[Az alkalmazás PowerShell használatával történő frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti a PowerShell használatával történő alkalmazásfrissítésen.

A frissítési paraméterek használatával szabályozhatja, hogy az alkalmazás hogyan [frissítsen.](service-fabric-application-upgrade-parameters.md)

Tegye kompatibilissé az alkalmazásfrissítéseket az [adatszerializálás](service-fabric-application-upgrade-data-serialization.md)használatának elsajátításával.

A speciális funkciók használata az alkalmazás frissítése során a [Speciális témakörökre](service-fabric-application-upgrade-advanced.md)hivatkozva.