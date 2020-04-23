---
title: Alkalmazások életciklusa Service Fabric
description: Ismerteti Service Fabric alkalmazások fejlesztését, üzembe helyezését, tesztelését, frissítését, karbantartását és eltávolítását.
ms.topic: conceptual
ms.date: 1/19/2018
ms.openlocfilehash: beeb1f1512cf94582dd561fa768f2e8e6649d686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378004"
---
# <a name="service-fabric-application-lifecycle"></a>Az alkalmazások életciklusának Service Fabric
Más platformokhoz hasonlóan az Azure Service Fabric-alkalmazások általában a következő fázisokon haladnak át: tervezés, fejlesztés, tesztelés, üzembe helyezés, frissítés, karbantartás és eltávolítás. A Service Fabric első osztályú támogatást biztosít a felhőalapú alkalmazások teljes életciklusához, a fejlesztéstől az üzembe helyezés, a napi felügyelet és a végleges leszerelésig történő karbantartás révén. A szolgáltatási modell lehetővé teszi, hogy a különböző szerepkörök egymástól függetlenül vehessenek részt az alkalmazás életciklusában. Ez a cikk áttekintést nyújt az API-król, valamint arról, hogyan használják a különböző szerepkörök a Service Fabric alkalmazás életciklusának fázisaiban.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Szolgáltatási modell szerepkörei
A szolgáltatási modell szerepkörei a következők:

* **Szolgáltatás fejlesztője**: olyan moduláris és általános szolgáltatásokat fejleszt, amelyek több azonos típusú vagy különböző típusú alkalmazásban is használhatók. Egy üzenetsor-szolgáltatás például egy Ticketing-alkalmazás (helpdesk) vagy egy e-kereskedelmi alkalmazás (bevásárlókosár) létrehozásához használható.
* **Alkalmazás fejlesztője**: az alkalmazásokat a szolgáltatások egy gyűjteményének integrálásával hozza létre bizonyos konkrét követelmények vagy forgatókönyvek kielégítése érdekében. Egy e-kereskedelmi webhely például a "JSON állapot nélküli kezelőfelület-szolgáltatás", "aukciós állapot-nyilvántartó szolgáltatás" és "üzenetsor-állapot-nyilvántartó szolgáltatás" integrálásával hozhat létre aukciós megoldást.
* **Alkalmazás-rendszergazda**: az alkalmazás konfigurációjának (a konfigurációs sablon paramétereinek kitöltése), a központi telepítés (elérhető erőforrások leképezése) és a szolgáltatás minőségének meghozatalát teszi lehetővé. Az alkalmazás rendszergazdája például a nyelv területi beállítását (például az angol nyelvet a japán Egyesült Államok vagy Japán esetében) az alkalmazáshoz. Egy másik telepített alkalmazás különböző beállításokkal rendelkezhet.
* **Operátor**: alkalmazások telepítése az alkalmazás konfigurációja és az alkalmazás rendszergazdája által meghatározott követelmények alapján. Egy operátor például kiépíti és telepíti az alkalmazást, és gondoskodik arról, hogy az az Azure-ban fusson. A kezelők az alkalmazás állapotával és teljesítményével kapcsolatos információkat figyelik, és szükség szerint karbantartják a fizikai infrastruktúrát.

## <a name="develop"></a>Fejlesztés
1. A *szolgáltatás-fejlesztő* a [Reliable Actors](service-fabric-reliable-actors-introduction.md) vagy [Reliable Services](service-fabric-reliable-services-introduction.md) programozási modell használatával különböző típusú szolgáltatásokat fejleszt.
2. A *szolgáltatás-fejlesztő* deklaratívan leírja a szolgáltatás jegyzékfájljának fejlett szolgáltatásait, amelyek egy vagy több kód, konfigurációs és adatcsomagból állnak.
3. Az *alkalmazások fejlesztői* ezután különböző típusú szolgáltatásokkal építenek be alkalmazást.
4. Egy *alkalmazás-fejlesztő* deklaratív módon írja le az alkalmazási jegyzékben szereplő alkalmazás típusát úgy, hogy hivatkozik az összetevő-szolgáltatások szolgáltatási jegyzékfájlokra, és megfelelően felülbírálja és parameterizing a különböző konfigurációs és telepítési beállításokat.

Tekintse meg az első [lépések a Reliable Actors](service-fabric-reliable-actors-get-started.md) és az első [lépések Reliable Services](service-fabric-reliable-services-quick-start.md) példákkal című témakört.

## <a name="deploy"></a>Üzembe helyezés
1. Az alkalmazás- *rendszergazda* leképezi az alkalmazás típusát egy adott alkalmazáshoz Service Fabric-fürtre történő központi telepítéshez az **alkalmazásban (** elem megfelelő paramétereinek megadásával az alkalmazás jegyzékfájljában.
2. Az *operátor* a [ **CopyApplicationPackage** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) vagy a [ **copy-ServiceFabricApplicationPackage** parancsmag](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps)használatával tölti fel az alkalmazáscsomag a fürt rendszerkép-tárolójába. Az alkalmazáscsomag tartalmazza az alkalmazás jegyzékfájlját és a szolgáltatási csomagok gyűjteményét. Service Fabric telepíti az alkalmazásokat a rendszerkép-tárolóban tárolt alkalmazáscsomag alapján, amely lehet egy Azure Blob-tároló vagy a Service Fabric rendszerszolgáltatás.
3. Az *operátor* ezután a [ **ProvisionApplicationAsync** metódussal](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **Register-ServiceFabricApplicationType** parancsmaggal](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)vagy az alkalmazás-Rest művelet kiosztásával [ **kiépíti** ](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application)az alkalmazás típusát a cél fürtben a feltöltött alkalmazáscsomag alapján.
4. Az alkalmazás kiépítés után az *operátor* az alkalmazás *rendszergazdája* által a [ **CreateApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **New-ServiceFabricApplication** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication)vagy az [ **alkalmazás létrehozása** Rest művelettel](https://docs.microsoft.com/rest/api/servicefabric/create-an-application)megadott paraméterekkel elindítja az alkalmazást.
5. Az alkalmazás üzembe helyezése után az *operátor* a [ **CreateServiceAsync** metódust](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), a [ **New-ServiceFabricService** parancsmagot](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice)vagy a [ **szolgáltatás létrehozása** Rest műveletet](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) használja új szolgáltatási példányok létrehozására az alkalmazás számára az elérhető szolgáltatások típusai alapján.
6. Az alkalmazás mostantól a Service Fabric-fürtön fut.

Lásd: [alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md) példákhoz.

## <a name="test"></a>Tesztelés
1. A helyi fejlesztési fürtre vagy egy tesztelési fürtre történő telepítés után a *szolgáltatás-fejlesztő* a beépített feladatátvételi teszt forgatókönyvet futtatja a [**FailoverTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) és a [**FailoverTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario) osztályok, vagy a [ **meghívó-ServiceFabricFailoverTestScenario** parancsmag](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps)használatával. A feladatátvételi teszt forgatókönyv a megadott szolgáltatást a fontos átmeneteken és feladatátvételeken keresztül futtatja, így biztosítva, hogy továbbra is elérhető és működőképes legyen.
2. A *szolgáltatás fejlesztője* ezután futtatja a beépített Chaos-teszt forgatókönyvet a [**ChaosTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) és a [**ChaosTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario) osztályok, vagy a [ **meghívó-ServiceFabricChaosTestScenario** parancsmag](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps)használatával. A káosz tesztelési forgatókönyve véletlenszerűen több csomópont-, kód-és replika-hibát idéz elő a fürtben.
3. A *szolgáltatás-fejlesztő* a szolgáltatás [és a szolgáltatás közötti kommunikációt](service-fabric-testability-scenarios-service-communication.md) az elsődleges replikákat a fürt körül áthelyező tesztelési forgatókönyvek létrehozásával teszteli.

További információ: [Bevezetés a fault Analysis Service-be](service-fabric-testability-overview.md) .

## <a name="upgrade"></a>Frissítés
1. A *szolgáltatás-fejlesztő* frissíti a létrehozott alkalmazás összetevő-szolgáltatásait és/vagy javítja a hibákat, és a szolgáltatás jegyzékfájljának új verzióját biztosítja.
2. Az *alkalmazások fejlesztői* felülbírálják és felparaméterezi a konzisztens szolgáltatások konfigurációs és telepítési beállításait, és az alkalmazás jegyzékfájljának új verzióját biztosítják. Az alkalmazás fejlesztője ezután magában foglalja a szolgáltatás-jegyzékfájlok új verzióit az alkalmazásban, és egy frissített alkalmazáscsomag új verzióját nyújtja az alkalmazás típusának.
3. Az *alkalmazás rendszergazdája* a megfelelő paraméterek frissítésével magában foglalja az alkalmazás típusának új verzióját a célalkalmazás alkalmazásban.
4. A *kezelő* a [ **CopyApplicationPackage** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) vagy a [ **copy-ServiceFabricApplicationPackage** parancsmag](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps)használatával tölti fel a frissített alkalmazáscsomag a fürt rendszerkép-tárolójába. Az alkalmazáscsomag tartalmazza az alkalmazás jegyzékfájlját és a szolgáltatási csomagok gyűjteményét.
5. Az *operátor* az alkalmazás új verzióját a [ **ProvisionApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **Register-ServiceFabricApplicationType** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)vagy [egy alkalmazás Rest-műveletének használatával **kiépíti** ](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application)a célként megadott fürtben.
6. Az *operátor* a [ **UpgradeApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **Start-ServiceFabricApplicationUpgrade** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade)vagy az [ **alkalmazás** Rest-műveletének frissítése](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application)segítségével frissíti a célalkalmazás-alkalmazást az új verzióra.
7. Az *operátor* ellenőrzi a frissítés előrehaladását a [ **GetApplicationUpgradeProgressAsync** metódussal](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **Get-ServiceFabricApplicationUpgrade** parancsmaggal](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplicationupgrade)vagy az [ **alkalmazás frissítésének beolvasása folyamat** Rest művelettel](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. Ha szükséges, a *kezelő* módosítja és újraalkalmazza az aktuális alkalmazás frissítésének paramétereit az [ **UpdateApplicationUpgradeAsync** metódussal](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), az [ **Update-ServiceFabricApplicationUpgrade** parancsmaggal](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade)vagy az [ **alkalmazás** frissítése Rest művelettel](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. Ha szükséges, az *operátor* visszaállítja az aktuális alkalmazás frissítését a [ **RollbackApplicationUpgradeAsync** metódussal](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **Start-ServiceFabricApplicationRollback** parancsmaggal](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback)vagy a [ **visszaállítási alkalmazás frissítési** Rest műveletével](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. Service Fabric frissíti a fürtön futó célalkalmazás használatát anélkül, hogy elveszítené a hozzá tartozó összetevők rendelkezésre állását.

Példákért tekintse meg az [alkalmazás verziófrissítését ismertető oktatóanyagot](service-fabric-application-upgrade-tutorial.md) .

## <a name="maintain"></a>Karbantartás
1. Az operációs rendszer frissítései és javításai esetében Service Fabric az Azure-infrastruktúrával rendelkező adaptereket a fürtben futó összes alkalmazás rendelkezésre állásának biztosításához.
2. A Service Fabric platform frissítéseihez és javításához Service Fabric a fürtön futó alkalmazások rendelkezésre állásának elvesztése nélkül frissíti magát.
3. Az *alkalmazás rendszergazdája* jóváhagyja a csomópontok hozzáadását vagy eltávolítását a fürtből a korábbi kapacitáskihasználási adatok elemzése és a várható jövőbeli igény alapján.
4. Az *operátor* hozzáadja és eltávolítja az *alkalmazás rendszergazdája*által megadott csomópontokat.
5. Ha új csomópontokat ad hozzá a szolgáltatáshoz, vagy a meglévő csomópontokat eltávolítja a fürtből, Service Fabric automatikusan terheléselosztást végez a futó alkalmazások között a fürt összes csomópontján az optimális teljesítmény érdekében.

## <a name="remove"></a>Eltávolítás
1. Egy *operátor* törölheti a futó szolgáltatás adott példányát a fürtben anélkül, hogy a teljes alkalmazást el kellene távolítani a [ **DeleteServiceAsync** metódussal](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), a [ **Remove-ServiceFabricService** parancsmaggal](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice)vagy a [ **szolgáltatás törlése** Rest művelettel](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. Az *operátor* a [ **DeleteApplicationAsync** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **Remove-ServiceFabricApplication** parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricapplication)vagy az [ **alkalmazás törlése** Rest művelet](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application)használatával is törölhet egy alkalmazás-példányt és annak összes szolgáltatását.
3. Az alkalmazás és a szolgáltatások leállítása után az *operátor* kiépítheti az alkalmazás típusát a [ **UnprovisionApplicationAsync** metódussal](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), a [ **Regisztráció törlése-ServiceFabricApplicationType** parancsmaggal](https://docs.microsoft.com/powershell/module/servicefabric/unregister-servicefabricapplicationtype), vagy az [ **alkalmazás** ](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application)kiépítésének megszüntetése művelettel. Az alkalmazás típusának kiépítése nem távolítja el az alkalmazáscsomagt a Lemezképtárolóba. Manuálisan el kell távolítania az alkalmazáscsomag-csomagot.
4. Az *operátor* eltávolítja az alkalmazáscsomag a Lemezképtárolóba a [ **RemoveApplicationPackage** metódus](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) vagy a [ **Remove-ServiceFabricApplicationPackage** parancsmag](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps)használatával.

Lásd: [alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md) példákhoz.

## <a name="next-steps"></a>További lépések
A Service Fabric-alkalmazások és-szolgáltatások fejlesztésével, tesztelésével és kezelésével kapcsolatos további információkért lásd:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)
* [Alkalmazás frissítése](service-fabric-application-upgrade.md)
* [Tesztelési áttekintés](service-fabric-testability-overview.md)
