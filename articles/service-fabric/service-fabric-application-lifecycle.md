---
title: Alkalmazás életciklusa a Service Fabricben
description: A Service Fabric-alkalmazások fejlesztését, üzembe helyezését, tesztelését, frissítését, karbantartását és eltávolítását ismerteti.
ms.topic: conceptual
ms.date: 1/19/2018
ms.openlocfilehash: beeb1f1512cf94582dd561fa768f2e8e6649d686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378004"
---
# <a name="service-fabric-application-lifecycle"></a>A Service Fabric alkalmazás életciklusa
Más platformokhoz is az Azure Service Fabric-en egy alkalmazás általában a következő fázisokon megy keresztül: tervezés, fejlesztés, tesztelés, üzembe helyezés, frissítés, karbantartás és eltávolítás. A Service Fabric első osztályú támogatást nyújt a felhőalapú alkalmazások teljes alkalmazáséletciklusához, a fejlesztéstől a telepítésen át a napi felügyeletig és a karbantartásig, az esetleges leszerelésig. A szolgáltatásmodell lehetővé teszi, hogy több különböző szerepkörök egymástól függetlenül vegyenek részt az alkalmazás életciklusában. Ez a cikk áttekintést nyújt az API-kat, és hogyan használják a különböző szerepkörök a Service Fabric-alkalmazás életciklusának fázisaiban.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Szolgáltatásimodell-szerepkörök
A szolgáltatásmodell-szerepkörök a következők:

* **Szolgáltatásfejlesztő**: Moduláris és általános szolgáltatásokat fejleszt, amelyek újrafelhasználhatók és felhasználhatók több, azonos típusú vagy különböző típusú alkalmazásban. Például egy várólista-szolgáltatás használható jegykiállítási alkalmazás (helpdesk) vagy e-kereskedelmi alkalmazás (bevásárlókosár) létrehozásához.
* **Alkalmazásfejlesztő:** Bizonyos speciális követelmények vagy forgatókönyvek teljesítéséhez szolgáltatások gyűjteményének integrálásával hoz létre alkalmazásokat. Például egy e-kereskedelmi webhely integrálhatja a "JSON állapotnélküli front-end szolgáltatás", "Aukciós állapotalapú szolgáltatás" és a "Queue Stateful Service" egy aukciós megoldás létrehozásához.
* **Alkalmazásadminisztrátor**: Döntéseket hoz az alkalmazás konfigurációjával (a konfigurációs sablon paramétereinek kitöltésével), a telepítéssel (a rendelkezésre álló erőforrásokhoz való hozzárendeléssel) és a szolgáltatás minőségével kapcsolatban. Például egy alkalmazás-rendszergazda dönti el az alkalmazás nyelvbeállítását (például az Egyesült Államok nyelvének beállítását(angolul az Egyesült Államokban vagy japánul Japánban). Egy másik telepített alkalmazás különböző beállításokkal rendelkezhet.
* **Operátor**: Az alkalmazásokat az alkalmazás konfigurációja és az alkalmazásrendszergazdááltal megadott követelmények alapján telepíti. Például egy operátor rendelkezéseket, és telepíti az alkalmazást, és biztosítja, hogy fut az Azure-ban. Az operátorok figyelemmel kísérik az alkalmazások állapotát és teljesítményét, és szükség szerint fenntartják a fizikai infrastruktúrát.

## <a name="develop"></a>Fejlesztés
1. A *szolgáltatásfejlesztő* fejleszti a különböző típusú szolgáltatások segítségével a [Reliable Actors](service-fabric-reliable-actors-introduction.md) vagy a Reliable [Services](service-fabric-reliable-services-introduction.md) programozási modell.
2. A *szolgáltatásfejlesztő* deklaratív módon leírja a szolgáltatásjegyzékfájlban kifejlesztett szolgáltatástípusokat, amelyek egy vagy több kódból, konfigurációból és adatcsomagokból állnak.
3. Az *alkalmazásfejlesztő* ezután különböző szolgáltatástípusok használatával hoz létre egy alkalmazást.
4. Az *alkalmazásfejlesztő* deklaratív módon írja le az alkalmazás típusát az alkalmazásjegyzékben az összetevő szolgáltatások szolgáltatásjegyzékeire való hivatkozással, és megfelelően felülbírálva és paraméterezve az összetevő-szolgáltatások különböző konfigurációs és telepítési beállításait.

Példákért [tekintse meg a Megbízható szereplők kelése](service-fabric-reliable-actors-get-started.md) és a Megbízható szolgáltatások [kalkulát.](service-fabric-reliable-services-quick-start.md)

## <a name="deploy"></a>Üzembe helyezés
1. Az *alkalmazás-rendszergazda* az alkalmazás típusát egy adott alkalmazáshoz igazítja, amelyet egy Service Fabric-fürtre kell telepíteni az **ApplicationType** elem megfelelő paramétereinek megadásával az alkalmazásjegyzékben.
2. Az *operátor* a [ **CopyApplicationPackage** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) vagy a [ **Copy-ServiceFabricApplicationPackage** parancsmag](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps)használatával tölti fel az alkalmazáscsomagot a fürtlemezkép-tárolóba. Az alkalmazáscsomag tartalmazza az alkalmazásjegyzéket és a szolgáltatáscsomagok gyűjteményét. A Service Fabric telepíti az alkalmazásokat a lemezképtárban tárolt alkalmazáscsomagból, amely lehet egy Azure blob áruház vagy a Service Fabric rendszerszolgáltatás.
3. Az *operátor* ezután a provision the target cluster in the uploaded application package from the [ **provisionApplicationAsync** method](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), the [ **Register-ServiceFabricApplicationType** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)vagy a Provision an [ **Application** REST operation](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application)használatával.
4. Az alkalmazás kiépítése után az *operátor* elindítja az alkalmazást az *alkalmazás rendszergazdája* által megadott paraméterekkel a [ **CreateApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **New-ServiceFabricApplication** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication)vagy az Alkalmazás REST [ **létrehozása** művelet használatával.](https://docs.microsoft.com/rest/api/servicefabric/create-an-application)
5. Az alkalmazás üzembe helyezése után az *operátor* a [ **CreateServiceAsync** metódust](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), a [ **New-ServiceFabricService** parancsmalettet](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice)vagy a [ **Szolgáltatás** REST létrehozása műveletet](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) használja az alkalmazáshoz elérhető szolgáltatástípusok alapján új szolgáltatáspéldányok létrehozásához.
6. Az alkalmazás most fut a Service Fabric-fürtben.

Példákat az [alkalmazás telepítése.](service-fabric-deploy-remove-applications.md)

## <a name="test"></a>Test
1. A helyi fejlesztési fürtre vagy egy tesztfürtre való üzembe helyezés után a *szolgáltatásfejlesztő* a beépített feladatátvételi tesztforgatókönyvet a [**FailoverTestScenarios**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) és a [**FailoverTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario) osztályok, illetve az [ **Invoke-ServiceFabricFailoverTestScenario** parancsmag](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps)használatával futtatja. A feladatátvételi teszt forgatókönyv egy adott szolgáltatás fontos átmenetek és feladatátvételek, annak biztosítása érdekében, hogy továbbra is elérhető és működik.
2. A *szolgáltatásfejlesztő* ezután futtatja a beépített káosz tesztforgatókönyvet a [**ChaosTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) és a [**ChaosTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario) osztályok vagy az [ **Invoke-ServiceFabricChaosTestScenario** parancsmag](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps)használatával. A káosz teszt forgatókönyv véletlenszerűen indukál több csomópont, kódcsomag és replika hibák a fürtbe.
3. A *szolgáltatásfejlesztő* [teszteli a szolgáltatás-szolgáltatás kommunikáció](service-fabric-testability-scenarios-service-communication.md) tanusításával teszt forgatókönyvek, amelyek az elsődleges replikák a fürt körül.

További információt [a Hibaelemzési szolgáltatás bevezetés című témakörben](service-fabric-testability-overview.md) talál.

## <a name="upgrade"></a>Frissítés
1. A *szolgáltatásfejlesztő* frissíti a példányosított alkalmazás rendszerező szolgáltatásait, és/vagy javítja a hibákat, és a szolgáltatásjegyzék új verzióját biztosítja.
2. Az *alkalmazásfejlesztő* felülbírálja és paraméterezi a konzisztens szolgáltatások konfigurációs és telepítési beállításait, és biztosítja az alkalmazásjegyzék új verzióját. Az alkalmazás fejlesztője ezután beépíti a szolgáltatásjegyzékek új verzióit az alkalmazásba, és az alkalmazástípus új verzióját egy frissített alkalmazáscsomagban biztosítja.
3. Az *alkalmazás-rendszergazda* a megfelelő paraméterek frissítésével beépíti az alkalmazástípus új verzióját a célalkalmazásba.
4. Egy *operátor* a frissített alkalmazáscsomagot a [ **CopyApplicationPackage** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) vagy a [ **Copy-ServiceFabricApplicationPackage** parancsmag](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps)használatával tölti fel a fürtlemeztárba. Az alkalmazáscsomag tartalmazza az alkalmazásjegyzéket és a szolgáltatáscsomagok gyűjteményét.
5. Az *operátor* a [ **provisionApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **Register-ServiceFabricApplicationType** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)vagy az Application [REST-szolgáltatás **létesítése** művelet](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application)teljében határozza meg az alkalmazás új verzióját a célfürtben .
6. A *operátor* a célalkalmazást az új verzióra frissíti a [ **UpgradeApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **Start-ServiceFabricApplicationUpgrade** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade)vagy az Application REST [ **frissítése** művelet használatával.](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application)
7. A *operátor* a [ **GetApplicationUpgradeProgressAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **Get-ServiceFabricApplicationUpgrade** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplicationupgrade)vagy az [ **Alkalmazásfrissítési folyamat folyamatának** letöltője művelettel](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1)ellenőrzi a frissítés előrehaladását.
8. Szükség esetén az *operátor* módosítja és újra alkalmazza az aktuális alkalmazásfrissítés paramétereit az [ **UpdateApplicationUpgradeAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), az [ **Update-ServiceFabricApplicationUpgrade** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade)vagy az [ **Alkalmazásfrissítési** REST frissítése művelet használatával.](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade)
9. Szükség esetén az *operátor* visszaállítja az aktuális alkalmazásfrissítést a [ **RollbackApplicationUpgradeAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **Start-ServiceFabricApplicationRollback** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback)vagy a [ **Visszagörgetési alkalmazásfrissítési** REST művelet használatával.](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade)
10. A Service Fabric frissíti a fürtben futó célalkalmazást anélkül, hogy elveszítené az alkotó szolgáltatások rendelkezésre állását.

Tekintse meg az [alkalmazás frissítési oktatóanyag](service-fabric-application-upgrade-tutorial.md) a példákat.

## <a name="maintain"></a>Karbantartás
1. Az operációs rendszer frissítései és javításai esetén a Service Fabric az Azure-infrastruktúrával csatlakozik, hogy garantálja a fürtben futó összes alkalmazás rendelkezésre állását.
2. A Service Fabric platformra való frissítések és javítások esetén a Service Fabric anélkül frissíti magát, hogy elveszítené a fürtön futó alkalmazások rendelkezésre állását.
3. Az *alkalmazás-rendszergazda* jóváhagyja a csomópontok hozzáadását vagy eltávolítását a fürtből, miután elemezte a korábbi kapacitáskihasználtsági adatokat és a jövőbeli igényeket.
4. Az *operátor* hozzáadja és eltávolítja az *alkalmazás rendszergazdája*által megadott csomópontokat.
5. Amikor új csomópontokat ad hozzá, vagy meglévő csomópontokat távolít a fürtből, a Service Fabric automatikusan betölti a futó alkalmazásokat a fürt összes csomópontján az optimális teljesítmény elérése érdekében.

## <a name="remove"></a>Eltávolítás
1. Az *operátor* törölheti a fürtben futó szolgáltatás egy adott példányát anélkül, hogy a teljes alkalmazást eltávolítaná a [ **DeleteServiceAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), az [ **Remove-ServiceService** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice)vagy a Service REST [ **törlése** művelettel.](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service)  
2. Az *operátorok* törölhetik az alkalmazáspéldányt és annak összes szolgáltatását is a [ **DeleteApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), az [ **Remove-ServiceFabricApplication** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricapplication)vagy az Alkalmazás REST [ **törlése** művelet használatával.](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application)
3. Az alkalmazás és a szolgáltatások leállítása után az *operátor* megszüntetheti az alkalmazástípus kiépítését az [ **UnprovisionApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **Unregister-ServiceFabricApplicationType** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/unregister-servicefabricapplicationtype)vagy az [Alkalmazás REST-művelet **megszüntetése** használatával.](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application) Az alkalmazástípus kibontása nem távolítja el az alkalmazáscsomagot az ImageStore-ból. Az alkalmazáscsomagot manuálisan kell eltávolítania.
4. Egy *operátor* eltávolítja az alkalmazáscsomagot az ImageStore-ból az [ **RemoveApplicationPackage** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) vagy az [ **Remove-ServiceFabricApplicationPackage** parancsmag](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps)használatával.

Példákat az [alkalmazás telepítése.](service-fabric-deploy-remove-applications.md)

## <a name="next-steps"></a>További lépések
A Service Fabric-alkalmazások és -szolgáltatások fejlesztésével, tesztelése és kezelésével kapcsolatos további információkért lásd:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)
* [Alkalmazás frissítése](service-fabric-application-upgrade.md)
* [A tesztelhetőség áttekintése](service-fabric-testability-overview.md)
