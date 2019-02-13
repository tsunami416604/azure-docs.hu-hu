---
title: Alkalmazás-életciklus a Service Fabricben |} A Microsoft Docs
description: Ismerteti, fejlesztéséhez, üzembe helyezéséhez, tesztelés, frissítése, karbantartása és eltávolítása a Service Fabric-alkalmazásokat.
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
ms.openlocfilehash: 1e714faa04717ac8e6687db3c074b8a77d649fb2
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217207"
---
# <a name="service-fabric-application-lifecycle"></a>A Service Fabric-alkalmazás-életciklus
Egyéb platformok esetén az Azure Service fabric-alkalmazás általában halad végig a következő fázisok szerint: tervezési, fejlesztési, tesztelési, üzembe helyezés, frissítése, karbantartási és eltávolítását. A Service Fabric első osztályú támogatási szolgáltatásokat biztosít a felhőalapú alkalmazások, a fejlesztéstől az üzembe helyezés, a napi felügyeleti és a karbantartási végleges leszerelésének alkalmazás teljes életciklusa. A modell lehetővé teszi, hogy több különböző szerepkörök egymástól függetlenül részt vesznek az alkalmazásfejlesztési életciklust. Ez a cikk áttekintést az API-k és azok hogyan használhatók a eltérő szerepkörök fázisai a a Service Fabric-alkalmazás életciklusa során.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Modell szerepkörök
A modell szerepkörök a következők:

* **Fejlesztői szolgáltatás**: Moduláris és általános szolgáltatásokat, amelyek újra végezhesse, és az azonos típusú vagy különböző több alkalmazásokban használt alakul ki. A queue szolgáltatás például egy hibajegykezelő alkalmazást (segélyszolgálat) vagy egy e-kereskedelmi alkalmazásban (bevásárlókocsiba) létrehozásához használható.
* **Alkalmazás fejlesztője**: Alkalmazások létrehoz egy bizonyos különleges követelmények vagy forgatókönyvek kielégítéséhez szolgáltatások gyűjteménye integrálásával. Ha például egy elektronikus kereskedelmi webhellyel integrálódhatnak "JSON állapotmentes előtér-szolgáltatás," "Árverés állapotalapú szolgáltatás" és "Várólista állapotalapú szolgáltatás" hozhat létre egy auctioning megoldást.
* **Alkalmazás-rendszergazda**: Az alkalmazás konfigurációja (Ehhez adja meg a konfigurációs paraméterei), deployment (elérhető erőforrásokhoz való hozzárendelés) és szolgáltatás-minőségi döntéseket. Például egy alkalmazás-rendszergazda úgy dönt, hogy az alkalmazás nyelvi kódot Japánban, például japán vagy (az Egyesült Államok angol nyelven). Egy másik üzembe helyezett alkalmazás különböző beállítással is rendelkezhetnek.
* **Operátor**: Üzembe helyez, az alkalmazás konfigurációja alapján alkalmazásokat és az alkalmazás-rendszergazda által meghatározott követelményeknek. Az operátornak például építi ki és helyez üzembe az alkalmazást, és biztosítja, hogy fut-e az Azure-ban. Operátorok alkalmazás állapotának és teljesítményének adatainak figyelése és a fizikai infrastruktúra karbantartása, igény szerint.

## <a name="develop"></a>Fejlesztés
1. A *fejlesztői szolgáltatás* különböző szolgáltatások használatával fejti ki a [Reliable Actors](service-fabric-reliable-actors-introduction.md) vagy [Reliable Services](service-fabric-reliable-services-introduction.md) programozási modellt.
2. A *fejlesztői szolgáltatás* deklaratív ismerteti a fejlett service szolgáltatás jegyzékfájl álló kód, a konfiguráció és az adatok egy vagy több csomagot.
3. Egy *alkalmazásfejlesztő* majd buildek használatával különböző típusú alkalmazáshoz.
4. Egy *alkalmazásfejlesztő* deklaratív írja le az alkalmazásjegyzéket az alkalmazástípus a különböző szolgáltatások szolgáltatásjegyzékek hivatkozó megfelelően felülbírálása, és különböző paraméterezése a különböző szolgáltatások konfigurációja és központi telepítési beállítások.

Lásd: [Ismerkedés a Reliable Actors](service-fabric-reliable-actors-get-started.md) és [Ismerkedés a Reliable Services](service-fabric-reliable-services-quick-start.md) példákat.

## <a name="deploy"></a>Üzembe helyezés
1. Egy *alkalmazás-rendszergazda* szokásait a Service Fabric-fürt üzembe helyezni a megfelelő paramétereket adjon meg egy adott alkalmazásba való az alkalmazás típusát a **ApplicationType** elem az alkalmazásjegyzékben.
2. Egy *operátor* feltölti az alkalmazáscsomag a fürt lemezképtárolójába használatával a [ **CopyApplicationPackage** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) vagy a [  **Másolás – ServiceFabricApplicationPackage** parancsmag](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Az alkalmazáscsomag az alkalmazásjegyzékben és a szolgáltatáscsomagok gyűjteményét tartalmazza. Service Fabric üzembe helyezi a lemezképet tárolja, amely lehet egy Azure blob-tároló vagy a Service Fabric rendszerszolgáltatás alkalmazáscsomagot az alkalmazások.
3. A *operátor* majd látja el az alkalmazás típusát, a cél fürtben, a feltöltött alkalmazás használatával az [ **ProvisionApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a  [ **Register-ServiceFabricApplicationType** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype), vagy a [ **alkalmazások kiépítése** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. Az alkalmazás üzembe helyezés után egy *operátor* elindítja az alkalmazást a kapcsolatszolgáltató által biztosított paraméterekkel a *alkalmazás-rendszergazda* használatával a [  **CreateApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **New-ServiceFabricApplication** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication), vagy a [ **alkalmazás létrehozása**  REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. Az alkalmazás üzembe helyezését követően egy *operátor* használja a [ **CreateServiceAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), a [ **New-ServiceFabricService**  parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice), vagy a [ **szolgáltatás-létrehozás** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) elérhető szolgáltatás típusa alapján az alkalmazás új szolgáltatáspéldány létrehozásához.
6. Az alkalmazás most már fut a Service Fabric-fürtben.

Lásd: [alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md) példákat.

## <a name="test"></a>Tesztelés
1. A helyi fejlesztési fürt vagy egy test-fürtöt, üzembe helyezése után egy *fejlesztői szolgáltatás* a beépített feladatátvételi tesztkörnyezet használatával futtatja a [ **FailoverTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) és [ **FailoverTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario) osztályok, vagy a [ **Invoke-ServiceFabricFailoverTestScenario** parancsmag](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). A feladatátvételi teszt forgatókönyv fontos átmenetek és feladatátvételi teszteket, hogy, hogy továbbra is elérhető, és üzemel az adott szolgáltatást futtatja.
2. A *fejlesztői szolgáltatás* majd futtatja a beépített káosz teszt forgatókönyv használata a [ **ChaosTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) és [  **ChaosTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario) osztályok, vagy a [ **Invoke-ServiceFabricChaosTestScenario** parancsmag](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). A chaos tesztkörnyezet kapott több csomópont, a kódcsomag és a replika hibák véletlenszerűen a fürtbe.
3. A *fejlesztői szolgáltatás* [teszteli a szolgáltatások közötti kommunikációs](service-fabric-testability-scenarios-service-communication.md) tesztelési helyzetek, amely körül a fürt elsődleges replikára áthelyezése készítésével.

Lásd: [Fault Analysis Service bemutatása](service-fabric-testability-overview.md) további információt.

## <a name="upgrade"></a>Frissítés
1. A *fejlesztői szolgáltatás* frissíti a példányosított alkalmazás megvalósítani a bennük foglalt szolgáltatásait és/vagy kijavítja a hibákat és a egy új verziója a szolgáltatásjegyzék biztosít.
2. Egy *alkalmazásfejlesztő* felülbírálások és felparaméterezi a konzisztens szolgáltatások konfigurációja és központi telepítési beállítások és az alkalmazásjegyzék új verzióját. Az alkalmazás fejlesztőjének magában foglalja a szolgáltatásjegyzékek új verziói az alkalmazásba, és az alkalmazás típusát, a frissített alkalmazáscsomag új verzióját.
3. Egy *alkalmazás-rendszergazda* magában foglalja az alkalmazástípus az új verzió a cél-alkalmazásba a megfelelő paramétereket frissítésével.
4. Egy *operátor* tölt fel a frissített alkalmazáscsomag a fürt lemezképet tároló használatával a [ **CopyApplicationPackage** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) vagy a [ **Másolási-ServiceFabricApplicationPackage** parancsmag](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Az alkalmazáscsomag az alkalmazásjegyzékben és a szolgáltatáscsomagok gyűjteményét tartalmazza.
5. Egy *operátor* építi ki a cél-fürtben az alkalmazás új verziójának használatával a [ **ProvisionApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [  **Register-ServiceFabricApplicationType** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype), vagy a [ **alkalmazások kiépítése** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
6. Egy *operátor* frissíti a cél alkalmazás új verzióját használja a [ **UpgradeApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [  **Start-ServiceFabricApplicationUpgrade** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade), vagy a [ **egy alkalmazás frissítése** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).
7. Egy *operátor* frissítési használatával állapotát ellenőrzi a [ **GetApplicationUpgradeProgressAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [  **Get-ServiceFabricApplicationUpgrade** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplicationupgrade), vagy a [ **alkalmazás frissítés állapota első** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. Ha szükséges, a *operátor* módosítja, és újra alkalmazza a jelenlegi alkalmazás a paraméterek használatával frissítse a [ **UpdateApplicationUpgradeAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **Update-ServiceFabricApplicationUpgrade** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade), vagy a [ **frissítés alkalmazás frissítése** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. Ha szükséges, a *operátor* visszaállítja az aktuális alkalmazás használatával frissítse a [ **RollbackApplicationUpgradeAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **Start-ServiceFabricApplicationRollback** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback), vagy a [ **visszaállítási alkalmazás frissítése** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. A Service Fabric frissíti a célalkalmazás a fürtön futó a különböző szolgáltatások rendelkezésre állásának elvesztése nélkül.

Tekintse meg a [frissítési oktatóanyag](service-fabric-application-upgrade-tutorial.md) példákat.

## <a name="maintain"></a>Karbantartása
1. Operációs rendszer frissítések és javítások Service Fabric a fürtben futó összes alkalmazás rendelkezésre állásának garantálása érdekében az Azure infrastruktúra-felületeihez.
2. Frissítések és javítások a Service Fabric-platform a Service Fabric frissíti magát az a fürtön futó alkalmazások rendelkezésre állásának elvesztése nélkül.
3. Egy *alkalmazás-rendszergazda* hozzáadását és eltávolítását csomópontokat a fürthöz korábbi kapacitás kihasználtsági adatok és az előre jelzett keresletet elemzése után jóváhagyja.
4. Egy *operátor* ad hozzá és távolít el csomópontokat által meghatározott a *alkalmazás-rendszergazda*.
5. Amikor új csomópontokat ad hozzá, vagy a meglévő csomópontok el lesznek távolítva a fürthöz, Service Fabric automatikusan elosztja a futó alkalmazások optimális teljesítményt kihozni a fürt összes csomópontja között.

## <a name="remove"></a>Eltávolítás
1. Egy *operátor* anélkül, hogy eltávolítaná a teljes alkalmazás használatával egy adott példányán a fürtben futó szolgáltatás, törölheti a [ **DeleteServiceAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), a [ **Remove-ServiceFabricService** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice), vagy a [ **Szolgáltatástörlés** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. Egy *operátor* alkalmazáspéldány és az összes hozzá tartozó szolgáltatások használatával is törölheti a [ **DeleteApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [  **Remove-ServiceFabricApplication** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricapplication), vagy a [ **alkalmazás törlése** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. Miután az alkalmazás és szolgáltatás leállt, a *operátor* is leépíteni a következőt: az alkalmazás típusát használja a [ **UnprovisionApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **Unregister-ServiceFabricApplicationType** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/unregister-servicefabricapplicationtype), vagy a [ **leépíteni a következőt: egy alkalmazás** REST-művelet](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). Az alkalmazástípus telepítésének visszavonását távolítsa el az alkalmazáscsomag a ImageStore. Az alkalmazáscsomag manuálisan el kell távolítania.
4. Egy *operátor* eltávolítja az alkalmazáscsomagot a ImageStore használatával a [ **RemoveApplicationPackage** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) vagy a [  **Remove-ServiceFabricApplicationPackage** parancsmag](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

Lásd: [alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md) példákat.

## <a name="next-steps"></a>További lépések
További információt a fejlesztés tesztelés és a Service Fabric-alkalmazások és szolgáltatások kezelése lásd:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)
* [Alkalmazásfrissítés](service-fabric-application-upgrade.md)
* [Testability – áttekintés](service-fabric-testability-overview.md)
