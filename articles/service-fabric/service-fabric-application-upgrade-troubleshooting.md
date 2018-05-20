---
title: Alkalmazásfrissítések hibaelhárítása |} Microsoft Docs
description: Ez a cikk a Service Fabric-alkalmazás és azok megoldását frissítése körül jelentkező gyakori problémákat tárgyalja.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 19ad152e-ec50-4327-9f19-065c875c003c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: c6ba61354bf7466819e34a0d619a5a1820dd7b90
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="troubleshoot-application-upgrades"></a>Alkalmazásfrissítések hibaelhárítása
Ez a cikk ismerteti az Azure Service Fabric-alkalmazás és azok megoldását frissítése körül gyakori problémák némelyikéről.

## <a name="troubleshoot-a-failed-application-upgrade"></a>A hibás alkalmazást-frissítés hibaelhárítása
Ha egy frissítés sikertelen, a kimenetét a **Get-ServiceFabricApplicationUpgrade** parancs tartalmaz további információt a hiba a hibakereséshez.  Az alábbi listában határozza meg, hogy miként legyen használható a további információt:

1. Azonosítsa a hiba típusa.
2. A hiba okának azonosításához.
3. Különítse el a további vizsgálatok esetén egy vagy több hibás összetevőt.

Ez az információ esetén érhető el a Service Fabric észleli a hibát, függetlenül attól, hogy a **FailureAction** visszaállítása vagy a frissítés felfüggesztése.

### <a name="identify-the-failure-type"></a>Azonosítsa a hiba típusa
A kimenet **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** azonosítja az időbélyegzőnek (UTC), amelyen a Service Fabric frissítési hibát észlelt, és  **FailureAction** lett elindítva. **Hibaoka** azonosítja a hiba három lehetséges magas szintű okok egyike:

1. UpgradeDomainTimeout - azt jelzi, hogy egy adott frissítési tartomány túl sokáig tartott, és **UpgradeDomainTimeout** lejárt.
2. OverallUpgradeTimeout - azt jelzi, hogy a teljes frissítés túl sokáig tartott, és **UpgradeTimeout** lejárt.
3. HealthCheck - azt jelzi, hogy egy frissítési tartományt a frissítés után az alkalmazás maradt a meghatározott házirendek szerint sérült és **HealthCheckRetryTimeout** lejárt.

Ezek a bejegyzések csak jelenik meg a kimeneti a frissítés sikertelen lesz, és elindítja a visszaállítása. További információk a hiba típusától függően.

### <a name="investigate-upgrade-timeouts"></a>Vizsgálja meg a frissítési időtúllépések
Frissítse az időtúllépési hibákat leggyakrabban okozzák a szolgáltatás elérhetőségével kapcsolatos problémákat. Az alábbi eredménye a tipikus, a frissítési ahol szolgáltatás replikák és a példány nem indulnak el az új kódot verzióban. A **UpgradeDomainProgressAtFailure** mező rögzíti a hibát időpontjában függőben lévő frissítési munka pillanatképet.

```
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

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

Ebben a példában a frissítése nem sikerült a frissítési tartomány *MYUD1* és két partíció (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* és *4b43f4d8-b26b-424e-9307-7a7a62e79750*) volt akadt-e. A partíciók akadt volt, mert a futtatókörnyezet nem helyezhető el az elsődleges replikára változott (*WaitForPrimaryPlacement*) a célcsomópontokat *csomópont1* és *csomópont4*.

A **Get-ServiceFabricNode** parancs segítségével ellenőrizze, hogy a két csomópont frissítési tartomány *MYUD1*. A *UpgradePhase* szerint *PostUpgradeSafetyCheck*, ami azt jelenti, hogy a biztonsági ellenőrzést követően a frissítési tartomány minden csomópontja rendelkezik is megjelenhetnek. Ezt az információt a problémát az új verzió az alkalmazás kódjának mutat. A leggyakoribb problémák a Megnyitás vagy elsődleges kódelérési használatával való előléptetést hibákat.

Egy *UpgradePhase* a *PreUpgradeSafetyCheck* azt jelenti, hogy a frissítési tartomány előkészítése előtt történt meg az problémák merültek. A leggyakoribb problémák ebben az esetben a Bezárás gombra vagy a lefokozás elsődleges kód elérési utakról a hibákat.

Az aktuális **UpgradeState** van *RollingBackCompleted*, így az eredeti frissítésének kell elvégezte a visszaállítás **FailureAction**, mely automatikusan összesített biztonsági hiba esetén a frissítés. Ha az eredeti frissítésének elvégezték a kézi **FailureAction**, majd a frissítés kellene inkább kell felfüggesztett állapotban, hogy az élő Hibakeresés az alkalmazás.

Ritka esetekben a **UpgradeDomainProgressAtFailure** mező lehet üres, ha a teljes frissítés időkorlátja lejár, ugyanúgy, mint a rendszer az aktuális frissítési tartomány összes munkaelem befejeződött. Ha ez történik, próbálja növelni a **UpgradeTimeout** és **UpgradeDomainTimeout** paraméterértékek frissítése, és próbálkozzon újra a frissítéssel.

### <a name="investigate-health-check-failures"></a>Vizsgálja meg a állapotának ellenőrzése sikertelen
Különböző problémákat, ami bekövetkezhet a frissítési tartományok összes csomópontjának frissítése, és átadja az biztonsági ellenőrzés befejezése után forrása a állapotának ellenőrzése sikertelen. Az alábbi eredménye egy frissítési hiba miatt sikertelen állapotellenőrzést jellemző. A **UnhealthyEvaluations** mező rögzíti, amely szerint a megadott frissítés alkalmával sikertelen állapotellenőrzést pillanatképe [állapotházirend](service-fabric-health-introduction.md).

```
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

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

Először vizsgálja állapotának ellenőrzése sikertelen kell a Service Fabric állapotmodell érteni. Ilyen alapos megértése, nélkül is láthatja, hogy a két szolgáltatás sérült állapotban, de: *fabric: / DemoApp/Svc3* és *fabric: / DemoApp/Svc2*, együtt a hiba állapotjelentések ("InjectedFault" Ebben az esetben). Ebben a példában két négy szolgáltatások sérült állapotban, amelyek nem éri el az alapértelmezett cél sérült 0 % (*MaxPercentUnhealthyServices*).

A frissítés fel lett függesztve, hibás megadásával egy **FailureAction** manuális, ha a frissítés megkezdése. Ez a mód lehetővé teszi vizsgálja meg az élő rendszer a hibás állapotban lévő minden további megtétele előtt.

### <a name="recover-from-a-suspended-upgrade"></a>A felfüggesztett frissítés helyreállítása
A visszaállítás **FailureAction**, az egyetlen helyreállítási szükséges, mert a frissítés automatikusan visszaállítja a meghibásodása esetén. A kézi **FailureAction**, több helyreállítási lehetőség:

1.  a visszaállítás eseményindító
2. Végezze el a frissítési fennmaradó manuális
3. A figyelt frissítésének folytatása

A **Start-ServiceFabricApplicationRollback** parancs használható bármikor visszaállítása az alkalmazás indításához. A parancs sikeresen adja vissza, ha a visszaállítási kérelem regisztrálva van a rendszerben, és ezt követően hamarosan elindul.

A **Resume-ServiceFabricApplicationUpgrade** parancs segítségével végezze el a frissítési fennmaradó manuálisan, egyszerre több frissítési tartományt. Ebben az üzemmódban csak biztonsági ellenőrzéseket hajtja végre a rendszer. Nincs több állapotellenőrzést végez. Ez a parancs csak lehet használni a *UpgradeState* látható *RollingForwardPending*, ami azt jelenti, hogy az aktuális frissítési tartomány frissítése befejeződött, de a következő nem indult el (függőben).

A **frissítés-ServiceFabricApplicationUpgrade** végrehajtás alatt álló ellenőrzi és folytatni a figyelt frissítés mindkét biztonsági parancs használható.

```
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

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

PS D:\temp>
```

A frissítés továbbra is fennáll, a frissítési tartomány, ahol utolsó felfüggesztették és azonos frissítse a paraméterek és az állapotházirendeket, mielőtt használja. Ha szükséges, a frissítési paraméterek és a fenti kimenetben megjelenő állapotházirendeket módosítható ugyanazzal a paranccsal, ha a frissítés folytatása. Ebben a példában a frissítés a figyelt módban, a paraméterek és az állapotházirendeket változatlan folytatódik.

## <a name="further-troubleshooting"></a>További hibaelhárításhoz
### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>A Service Fabric nem követi a megadott házirendek
Lehetséges ok: 1:

A Service Fabric összes százalékos fordítja le az entitások (például a replikákat, partíciók és szolgáltatások) állapotának kiértékelését tényleges számok, és mindig teljes entitások kerekít. Például ha a maximálisan engedélyezett *MaxPercentUnhealthyReplicasPerPartition* 21 %, és öt replikákat, akkor a Service Fabric lehetővé teszi, hogy legfeljebb két nem megfelelő állapotú replika (Ez azt jelenti, hogy`Math.Ceiling (5*0.21)`). Ebből kifolyólag állapotházirendeket ennek megfelelően kell állítani.

2 lehetséges ok:

Házirendek teljes szolgáltatások és szolgáltatáspéldány nem adott százalékos értékben vannak megadva. Például a frissítés, ha egy alkalmazás négy előtt szolgáltatás példányok A, B, C és D, ahol D szolgáltatás állapota nem megfelelő, de minimális hatással van az alkalmazáshoz. Azt szeretnénk, figyelmen kívül hagyja az ismert sérült szolgáltatás D frissítés során, és a paraméter *MaxPercentUnhealthyServices* 25 %, feltéve, hogy csak A, B és C kell lennie kifogástalan.

Azonban a frissítés során D válhat kifogástalan közben C akkor kerül sérült állapotba. A frissítés továbbra is szeretné sikertelen, mert a szolgáltatások csak 25 %-át sérült állapotban. Azonban váratlan hibák miatt váratlanul sérült helyett D. alatt C eredményezi Ebben a helyzetben D kell modellezni egy másik szolgáltatás típusa a, B és c kiszolgálóra. Állapotházirendeket szolgáltatás meg van adva, mert különböző szolgáltatásokhoz különböző sérült százalékos küszöbérték alkalmazhatja. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Az alkalmazásfrissítés állapotházirend nem szeretnék adta meg, de néhány soha nem megadott időtúllépési továbbra is sikertelen, a frissítés
Ha állapotházirendeket nem biztosított a frissítési kérelmet, hogy azok kell venni a *ApplicationManifest.xml* aktuális alkalmazás verziója. Például ha az 1.0-s verziója a 2.0-s verziójában, alkalmazás állapotházirendeket 1.0-s verziója a megadott alkalmazás X frissít szolgálnak. Ha a frissítés egy másik állapotházirend kell használni, majd a házirend kell adni a frissítési API-hívás alkalmazás részeként. A frissítés során csak alkalmazni a házirendeket, az API-hívás megadott. Ha a frissítés befejeződött, a házirendek szerepel a *ApplicationManifest.xml* szolgálnak.

### <a name="incorrect-time-outs-are-specified"></a>Helytelen időtúllépéseket vannak megadva.
Akkor lehet, hogy rendelkezik már azon, hogy következményeiről időtúllépéseket érvénytelenként van beállítva. Például előfordulhat, hogy egy *UpgradeTimeout* meg kisebb, mint a *UpgradeDomainTimeout*. A válasz hibát ad vissza. Hibák vannak adott vissza, ha a *UpgradeDomainTimeout* kisebb, mint a *HealthCheckWaitDuration* és *HealthCheckRetryTimeout*, vagy ha  *UpgradeDomainTimeout* kisebb, mint a összege *HealthCheckWaitDuration* és *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Túl sokáig tart a frissítések
A frissítés befejezéséhez ideje az állapot-ellenőrzési eredményeire és a megadott időtúllépéseket függ. Mennyi ideig tart, telepítése, és az alkalmazás stabilizálását függő állapot-ellenőrzési eredményeire és időtúllépéseket. Túl agresszív a várakozási idő alatt több sikertelen frissítések jelentheti, ezért azt javasoljuk, hosszabb időtúllépéseket konzervatív módon kezdve.

Itt gyors frissítő megtalálható a várakozási idő és a frissítési alkalommal együttműködését:

Frissíti a frissítési tartományok nem tudja elvégezni gyorsabb, mint a *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Frissítés nem sikerülne addig nem kerülhet sor gyorsabb, mint a *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

A frissítési idő egy frissítési tartomány korlátozza *UpgradeDomainTimeout*.  Ha *HealthCheckRetryTimeout* és *HealthCheckStableDuration* mindkettő nem nulla, és az alkalmazás állapotának tartja oda-vissza vált, akkor a frissítés végül időtúllépése a *UpgradeDomainTimeout*. *UpgradeDomainTimeout* számbavételi le egyszer a frissítés az aktuális frissítési tartomány kezdete a kezdődik.

## <a name="next-steps"></a>További lépések
[Az alkalmazás használata a Visual Studio frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti Önt az alkalmazásfrissítés Visual Studio használatával.

[Az alkalmazás használatával Powershell frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti Önt az alkalmazásfrissítés PowerShell használatával.

Szabályozhatja, hogy az alkalmazás használatával frissíti [frissítése paraméterek](service-fabric-application-upgrade-parameters.md).

Az alkalmazásfrissítéseket által használatának megtanulása kompatibilissé [Adatszerializálás](service-fabric-application-upgrade-data-serialization.md).

Összetettebb funkciók használata az alkalmazás frissítésekor szakaszra [Speciális témakörök](service-fabric-application-upgrade-advanced.md).
