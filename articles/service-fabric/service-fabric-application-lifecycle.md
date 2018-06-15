---
title: Alkalmazás életciklusa a Service Fabric |} Microsoft Docs
description: Ismerteti a fejlesztés, telepítése, tesztelési, frissítése, karbantartása és a Service Fabric-alkalmazások eltávolítása.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 08837cca-5aa7-40da-b087-2b657224a097
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/19/2018
ms.author: ryanwi
ms.openlocfilehash: e7dddfca3640615cb851fb6dce9eaa80260ccbf6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212561"
---
# <a name="service-fabric-application-lifecycle"></a>A Service Fabric-alkalmazás életciklusa
És egyéb platformok, az alkalmazás Azure Service Fabric általában végig kell vinnie a következő fázisok: tervezési, fejlesztési, tesztelési, telepítési, frissítésére, karbantartási és eltávolítása. A Service Fabric első osztályú támogatást nyújt a teljes alkalmazás életciklusa a felhőalapú alkalmazások, a fejlesztését a telepítés, a napi felügyeleti és a karbantartás végleges leszerelése. A modell lehetővé teszi, hogy az alkalmazás-életciklus egymástól függetlenül részt számos különböző szerepkörrel. Ez a cikk áttekintést nyújt az API-k és azok használata során a Service Fabric-alkalmazás életciklusa fázisai a különböző szerepkörökhöz.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

A következő Microsoft Virtual Academy videó bemutatja, hogyan kezelheti az alkalmazás-életciklus: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-application-lifecycle/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="service-model-roles"></a>Szolgáltatás modell szerepkörök
A service model szerepkörök a következők:

* **Fejlesztői szolgáltatás**: házon belül fejlesztett alkalmazásokra moduláris és általános szolgáltatások rendeltetéssel és több azonos típusú vagy különböző használja. Például a queue szolgáltatás használható a jegykiadási alkalmazás (segélyszolgálat) vagy e-kereskedelmi alkalmazás (kosár) létrehozásához.
* **Alkalmazásfejlesztő**: bizonyos különleges követelményeket vagy forgatókönyvek kielégítéséhez szolgáltatások gyűjteménye integrálásával alkalmazásokat hoz létre. Például az elektronikus kereskedelmi webhely előfordulhat, hogy integrálni a "JSON állapotmentes előtér-szolgáltatás," "árverés állapotalapú alkalmazások és szolgáltatások" és "Várólista állapotalapú alkalmazások és szolgáltatások szolgáltatás" egy auctioning megoldás kiépítését.
* **Alkalmazás-rendszergazda**: lehetővé teszi a döntést arról, hogy az alkalmazás konfigurációja (a konfigurációs Sablonparaméterek kitöltése), a telepítési (a leképezés csak akkor elérhető erőforrások) és a szolgáltatásminőség. Például egy alkalmazás-rendszergazda úgy dönt, az alkalmazás területi Japánban, például japán vagy (az Egyesült Államok angol nyelvű). Egy másik üzembe helyezett alkalmazás különböző beállítással is rendelkezhetnek.
* **Operátor**: telepíti a az alkalmazás konfigurációját alapuló alkalmazások és az alkalmazás-rendszergazda által meghatározott követelményeknek. Operátor például kiépítését és központilag telepíti az alkalmazást, és biztosítja, hogy fut-e az Azure-ban. Operátorok alkalmazás állapotának és teljesítményének információk figyelését, és a fizikai infrastruktúra karbantartása, igény szerint.

## <a name="develop"></a>Fejlesztés
1. A *fejlesztői szolgáltatás* házon belül fejlesztett alkalmazásokra különböző szolgáltatások segítségével a [Reliable Actors](service-fabric-reliable-actors-introduction.md) vagy [Reliable Services](service-fabric-reliable-services-introduction.md) programozási modellt.
2. A *fejlesztői szolgáltatás* deklarációval ismerteti fejlett szolgáltatás a szolgáltatás egy vagy több kódot, a konfiguráció és az adatok csomagból álló jegyzékfájl.
3. Egy *alkalmazásfejlesztő* majd összeállít típusú különböző szolgáltatásokat használó alkalmazások.
4. Egy *alkalmazásfejlesztő* deklaratív módon írja le az alkalmazásjegyzéket az alkalmazástípus hivatkozik a szolgáltatás jegyzékfájlokat alkotó szolgáltatások megfelelően felülbírálása, és a bennük foglalt szolgáltatások különböző konfigurációs és központi telepítési beállítások paraméterezése által.

Lásd: [Ismerkedés a Reliable Actors](service-fabric-reliable-actors-get-started.md) és [Reliable Services használatába](service-fabric-reliable-services-quick-start.md) példákat.

## <a name="deploy"></a>Üzembe helyezés
1. Egy *alkalmazás-rendszergazda* megfigyelt az alkalmazás típusának megfelelő paramétereinek megadásával a Service Fabric-fürt központilag telepíthető egy adott alkalmazásra a **ApplicationType** elem található az alkalmazás jegyzékében.
2. Egy *operátor* feltölti az alkalmazáscsomagot a fürt lemezképtárolóhoz használatával a [ **CopyApplicationPackage** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_) vagy a [ **másolási-ServiceFabricApplicationPackage** parancsmag](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Az alkalmazáscsomag az alkalmazás jegyzékében és szervizcsomagok gyűjteményét tartalmazza. A Service Fabric az alkalmazáscsomag, a lemezkép tárolja, amely egy Azure blob-tároló vagy a Service Fabric rendszer szolgáltatás alkalmazások telepíti.
3. A *operátor* majd látja el a csomagot a feltöltött alkalmazás a célként megadott fürtben az alkalmazástípus a [ **ProvisionApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_), a [ **Register-ServiceFabricApplicationType** parancsmag](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype), vagy a [ **alkalmazás kiépítése** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. Az alkalmazás kiépítése után egy *operátor* elindul az alkalmazás által szolgáltatott paraméterekkel a *alkalmazás-rendszergazda* használatával a [ **CreateApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CreateApplicationAsync_System_Fabric_Description_ApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), a [ **New-ServiceFabricApplication** parancsmag](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricapplication), vagy a [ **alkalmazás létrehozása** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. Az alkalmazás telepítése után egy *operátor* használja a [ **CreateServiceAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_CreateServiceAsync_System_Fabric_Description_ServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), a [ **New-ServiceFabricService** parancsmag](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice), vagy a [ **szolgáltatás létrehozása** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) az alkalmazáshoz rendelkezésre álló szolgáltatástípusok alapuló új szolgáltatáspéldány létrehozása.
6. Az alkalmazás most már fut a Service Fabric-fürt.

Lásd: [alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md) példákat.

## <a name="test"></a>Tesztelés
1. A helyi fejlesztési fürtök vagy egy teszt fürt telepítése után a *fejlesztői szolgáltatás* a beépített feladatátvételi tesztkörnyezet használatával lefut a [ **FailoverTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters#System_Fabric_Testability_Scenario_FailoverTestScenarioParameters) és [ **FailoverTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario#System_Fabric_Testability_Scenario_FailoverTestScenario) osztályok, vagy a [ **Invoke-ServiceFabricFailoverTestScenario** parancsmag](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). A feladatátvételi teszt forgatókönyv egy megadott szolgáltatás fut a fontos átmenetek és a feladatátvételi tesztek győződjön meg arról, hogy az továbbra is elérhető, és üzemel.
2. A *fejlesztői szolgáltatás* majd futtatja a beépített chaos teszt példa használata a [ **ChaosTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters#System_Fabric_Testability_Scenario_ChaosTestScenarioParameters) és [ **ChaosTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario#System_Fabric_Testability_Scenario_ChaosTestScenario) osztályok, vagy a [ **Invoke-ServiceFabricChaosTestScenario** parancsmag](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). A chaos tesztkörnyezet kapott több csomópont, a kódcsomag és a replika hibák véletlenszerűen a fürthöz.
3. A *fejlesztői szolgáltatás* [teszteli a szolgáltatások közötti kommunikációs](service-fabric-testability-scenarios-service-communication.md) lépés Tesztelési forgatókönyvek, amely körül a fürt elsődleges replikára változott.

Lásd: [bemutatása a tartalék Analysis Service](service-fabric-testability-overview.md) további információt.

## <a name="upgrade"></a>Frissítés
1. A *fejlesztői szolgáltatás* frissíti a példányként létrehozott alkalmazás alkotó szolgáltatások és/vagy javítja a hibákat, és a szolgáltatás jegyzékfájl új verzióját.
2. Egy *alkalmazásfejlesztő* felülbírálások és parameterizes konzisztens szolgáltatások konfigurációja és központi telepítési beállítások és az alkalmazás jegyzékében új verzióját. Az alkalmazás fejlesztőjének magában foglalja a szolgáltatás jegyzékfájlokat új verzióit az alkalmazásba, és az alkalmazás típusa, a frissített alkalmazáscsomag új verzióját.
3. Egy *alkalmazás-rendszergazda* magában foglalja az alkalmazástípus új verziójának azokat a célalkalmazás azáltal, hogy frissíti a megfelelő paramétereket.
4. Egy *operátor* feltölti a frissített alkalmazáscsomagot a fürt lemezképet tároló történő a [ **CopyApplicationPackage** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_) vagy a [ **másolási-ServiceFabricApplicationPackage** parancsmag](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Az alkalmazáscsomag az alkalmazás jegyzékében és szervizcsomagok gyűjteményét tartalmazza.
5. Egy *operátor* látja el a célként megadott fürt alkalmazás új verziójának használatával az [ **ProvisionApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_), a [ **Register-ServiceFabricApplicationType** parancsmag](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype), vagy a [ **alkalmazás kiépítése** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
6. Egy *operátor* frissíti a cél alkalmazás új verzióját használja a [ **UpgradeApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpgradeApplicationAsync_System_Fabric_Description_ApplicationUpgradeDescription_System_TimeSpan_System_Threading_CancellationToken_), a [ **Start-ServiceFabricApplicationUpgrade** parancsmag](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationupgrade), vagy a [ **alkalmazás frissítése** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).
7. Egy *operátor* frissítési használatával állapotát ellenőrzi a [ **GetApplicationUpgradeProgressAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_GetApplicationUpgradeProgressAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), a [ **Get-ServiceFabricApplicationUpgrade** parancsmag](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade), vagy a [ **alkalmazás frissítése folyamatban beolvasása** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. Ha szükséges, a *operátor* módosítja, és újra alkalmazza a jelenlegi alkalmazás paraméterek használatával frissítse a [ **UpdateApplicationUpgradeAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpdateApplicationUpgradeAsync_System_Fabric_Description_ApplicationUpgradeUpdateDescription_System_TimeSpan_System_Threading_CancellationToken_), a [ **frissítés-ServiceFabricApplicationUpgrade** parancsmag](https://docs.microsoft.com/powershell/servicefabric/vlatest/update-servicefabricapplicationupgrade), vagy a [ **frissítés alkalmazás frissítése** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. Ha szükséges, a *operátor* visszaállítja az aktuális alkalmazás használatával frissítse a [ **RollbackApplicationUpgradeAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RollbackApplicationUpgradeAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), a [ **Start-ServiceFabricApplicationRollback** parancsmag](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationrollback), vagy a [ **visszaállítási alkalmazás frissítése** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. A Service Fabric frissíti a bennük foglalt szolgáltatások rendelkezésre állásának elvesztése a fürtben futó célalkalmazás válaszából.

Tekintse meg a [frissítési oktatóanyag](service-fabric-application-upgrade-tutorial.md) példákat.

## <a name="maintain"></a>Karbantartása
1. Operációs rendszer frissítések és javítások a Service Fabric kapcsolódási pontok a fürtben futó összes alkalmazás rendelkezésre állásának biztosításához az Azure-infrastruktúra.
2. Frissítések és javítások a Service Fabric platform a Service Fabric frissíti magát a fürtben futó alkalmazások rendelkezésre állásának elvesztése nélkül.
3. Egy *alkalmazás-rendszergazda* hozzáadását és eltávolítását csomópontot a fürtről korábbi kapacitás kihasználtsági adatok és a tervezett jövőbeli igény szerinti elemzése után jóvá.
4. Egy *operátor* ad hozzá, és eltávolítja a megadott csomópont a *alkalmazás-rendszergazda*.
5. Amikor új csomópontokat ad hozzá, vagy meglévő csomópontok el lesznek távolítva a fürt, a Service Fabric automatikusan kiegyenlíti a futó alkalmazások közötti optimális teljesítményének elérése érdekében a fürt összes csomópontján.

## <a name="remove"></a>Eltávolítás
1. Egy *operátor* anélkül, hogy eltávolítaná a teljes alkalmazás használata a fürt egy futó szolgáltatással adott példányának törlése az [ **DeleteServiceAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_DeleteServiceAsync_System_Fabric_Description_DeleteServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), a [ **Remove-ServiceFabricService** parancsmag](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricservice), vagy a [ **Szolgáltatástörlés** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. Egy *operátor* egy alkalmazáspéldány és szolgáltatásai segítségével is törölhetők a [ **DeleteApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_DeleteApplicationAsync_System_Fabric_Description_DeleteApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), a [ **Remove-ServiceFabricApplication** parancsmag](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricapplication), vagy a [ **alkalmazás törlése** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. Miután az alkalmazás és szolgáltatás leállt, a *operátor* is leépíteni a következőt: az alkalmazás típust használja a [ **UnprovisionApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UnprovisionApplicationAsync_System_String_System_String_System_TimeSpan_System_Threading_CancellationToken_), a [ **Unregister-ServiceFabricApplicationType** parancsmag](https://docs.microsoft.com/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype), vagy a [ **leépíteni a következőt: egy alkalmazás** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). Az alkalmazástípus leépítése távolítsa el az alkalmazáscsomag az ImageStore. Az alkalmazáscsomag manuálisan el kell távolítania.
4. Egy *operátor* eltávolítja az alkalmazáscsomagot a Lemezképtárolóba a a [ **RemoveApplicationPackage** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RemoveApplicationPackage_System_String_System_String_) vagy a [ **Remove-ServiceFabricApplicationPackage** parancsmag](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

Lásd: [alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md) példákat.

## <a name="next-steps"></a>További lépések
További információk fejlesztésével tesztelése és a Service Fabric-alkalmazások és szolgáltatások kezelése::

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)
* [Alkalmazásfrissítés](service-fabric-application-upgrade.md)
* [Tesztelhetőségi áttekintése](service-fabric-testability-overview.md)
