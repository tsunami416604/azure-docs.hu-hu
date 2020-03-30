---
title: A Service Fabric alkalmazásfrissítése a PowerShell használatával
description: Ez a cikk bemutatja a Service Fabric-alkalmazás üzembe helyezésének, a kód módosításának és a PowerShell használatával történő frissítés bevezetésének élményét.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: b113b5a1042518e3b0d86e53796c5fe49afed418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426787"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>A Service Fabric alkalmazásfrissítése a PowerShell használatával
> [!div class="op_single_selector"]
> * [Powershell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Vizuális stúdió](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

A leggyakrabban használt és ajánlott frissítési módszer a figyelt működés közbeni frissítés.  Az Azure Service Fabric a frissítve lévő alkalmazás állapotát figyeli az állapotházirendek alapján. Egy frissítési tartomány (UD) frissítése után a Service Fabric kiértékeli az alkalmazás állapotát, és vagy folytatja a következő frissítési tartományba, vagy az állapotházirendektől függően nem sikerül a frissítés.

A figyelt alkalmazásfrissítés a felügyelt vagy natív API-k, a PowerShell, az Azure CLI, a Java vagy a REST használatával hajtható végre. A Visual Studio használatával történő frissítéssel kapcsolatos [tudnivalókról az Alkalmazás frissítése a Visual Studio használatával](service-fabric-application-upgrade-tutorial.md)című témakörben talál.

A Service Fabric figyelt működés közbeni frissítések, az alkalmazás rendszergazdája konfigurálhatja az állapotértékelési szabályzatot, amely service fabric segítségével határozza meg, ha az alkalmazás kifogástalan állapotú. Ezenkívül a rendszergazda konfigurálhatja az állapotkiértékelés sikertelensége esetén végrehajtandó műveletet (például automatikus visszaállítást.) Ez a szakasz bemutatja a PowerShellt használó SDK-minták egyikének figyelt frissítését. 

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>1. lépés: A Vizuális objektumok minta összeállítása és üzembe helyezése
Az alkalmazás felépítéséhez és közzétételéhez kattintson a jobb gombbal az alkalmazásprojektre, a **VisualObjectsApplication alkalmazásra,** és válassza a **Közzététel** parancsot.  További információ: [Service Fabric alkalmazásfrissítési oktatóanyag.](service-fabric-application-upgrade-tutorial.md)  Másik lehetőségként a PowerShell segítségével telepítheti az alkalmazást.

> [!NOTE]
> Mielőtt a Service Fabric-parancsok bármelyike használható a PowerShellben, először `Connect-ServiceFabricCluster` csatlakoznia kell a fürthöz a parancsmag használatával. Hasonlóképpen feltételezhető, hogy a fürt már be van állítva a helyi számítógépen. Tekintse meg a [Service Fabric-fejlesztői környezet beállításáról](service-fabric-get-started.md)szóló cikket.
> 
> 

Miután a Visual Studióban felépítette a projektet, a PowerShell [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) paranccsal másolhatja az alkalmazáscsomagot az ImageStore-ba. Ha helyileg szeretné ellenőrizni az alkalmazáscsomagot, használja a [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) parancsmagját. A következő lépés az, hogy regisztrálja az alkalmazást a Service Fabric futásidejű a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) parancsmag használatával. A következő lépés az alkalmazás egy példányának indítása a [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) parancsmag használatával.  Ez a három lépés hasonló a Visual Studio **Központi telepítés** menüelemének használatához.  Miután a kiépítés befejeződött, meg kell tisztítania a másolt alkalmazáscsomagot a lemezkép-tárolóból a felhasznált erőforrások csökkentése érdekében.  Ha egy alkalmazástípusra már nincs szükség, ugyanezen okból nem regisztrálható. További [információ: Alkalmazások telepítése és eltávolítása a PowerShell használatával](service-fabric-application-upgrade-tutorial-powershell.md) című témakörben található.

Most már [használhatja service fabric intézővel a fürt és az alkalmazás megtekintéséhez.](service-fabric-visualizing-your-cluster.md) Az alkalmazás rendelkezik egy webszolgáltatással, amely az [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) Internet Explorer programban a címsorba való beírással navigálható.  Látnia kell néhány lebegő vizuális objektumot a képernyőn.  Emellett a [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) segítségével ellenőrizheti az alkalmazás állapotát.

## <a name="step-2-update-the-visual-objects-sample"></a>2. lépés: A vizuális objektumok mintája frissítése
Előfordulhat, hogy az 1. Frissítsük ezt az alkalmazást egy olyanra, ahol a vizuális objektumok is forognak.

Jelölje ki a VisualObjects.ActorService projektet a VisualObjects megoldáson belül, és nyissa meg a StatefulVisualObjectActor.cs fájlt. A fájlon belül keresse `MoveObject`meg `this.State.Move()`a metódust, megjegyzést fűzzön hozzá és megjegyzést fért el. `this.State.Move(true)` Ez a módosítás elforgatja az objektumokat a szolgáltatás frissítése után.

Frissítenünk kell a **VisualObjects.ActorService**projekt *ServiceManifest.xml* fájlját is (a PackageRoot csoportban). Frissítse a *CodePackage* és a szolgáltatás verzió2.0-s verzióját, valamint a *ServiceManifest.xml* fájl megfelelő sorait.
A Visual Studio *Jegyzékfájl szerkesztése* beállítást akkor használhatja, ha a jobb gombbal a megoldásra kattintasz a jegyzékfájl módosításához.

A módosítások végrehajtása után a jegyzékfájlnak a következőkre kell hasonlítania (a kiemelt részek a változásokat mutatják):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Most az *ApplicationManifest.xml* fájl (amely a **VisualObjects** megoldás alatt található **VisualObjects** projekt alatt található) a **VisualObjects.ActorService** projekt 2.0-s verziójára frissül. Ezenkívül az Alkalmazás verziója az 1.0.0.0-ról 2.0.0.0-ra frissül. Az *ApplicationManifest.xml* fájlnak a következő kódrészlethez hasonlóan kell kinéznie:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Most hozza létre a projektet csak az **ActorService** projekt kiválasztásával, majd a jobb gombbal kattintva, és válassza a **Build** opciót a Visual Studióban. Ha az **Összes újraépítése**lehetőséget választja, frissítenie kell az összes projekt verzióit, mivel a kód megváltozott volna. Ezután csomagoljuk be a frissített alkalmazást úgy, hogy a jobb gombbal a ***VisualObjectsApplication***elemre kattintanak, kiválasztják a Service Fabric menüt, és a **Csomag parancsot**választják. Ez a művelet egy telepíthető alkalmazáscsomagot hoz létre.  A frissített alkalmazás készen áll a telepítésre.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>3. lépés: Döntsön az állapotházirendekről és a frissítési paraméterekről
Ismerkedjen meg az [alkalmazásfrissítési paraméterekkel](service-fabric-application-upgrade-parameters.md) és a [frissítési folyamattal,](service-fabric-application-upgrade.md) hogy jól megismertesse a különböző frissítési paramétereket, időtúlelőnyöket és az alkalmazott állapotfeltételt. Ebben a forgatókönyvben a szolgáltatás állapotkiértékelési feltétel van beállítva az alapértelmezett (és ajánlott) értékek, ami azt jelenti, hogy minden szolgáltatás és példány *kifogástalan állapotúnak* kell lennie a frissítés után.  

Azonban növeljük a *HealthCheckStableDuration* 180 másodpercre (úgy, hogy a szolgáltatások kifogástalan legalább 120 másodpercig, mielőtt a frissítés folytatódik a következő frissítési tartományba).  Állítsuk be azt is, hogy a *UpgradeDomainTimeout* 1200 másodperc legyen, a *UpgradeTimeout* pedig 3000 másodperc legyen.

Végül állítsuk be a *UpgradeFailureAction-t* is visszaállításra. Ez a beállítás megköveteli, hogy a Service Fabric visszaállítsa az alkalmazást az előző verzióra, ha a frissítés során bármilyen problémát tapasztal. Így a frissítés indításakor (a 4. lépésben) a következő paraméterek vannak megadva:

FailureAction = Visszaállítás

ÁllapotellenőrzésStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>4. lépés: Alkalmazás előkészítése a frissítésre
Most az alkalmazás készen áll a frissítésre. Ha rendszergazdaként nyit meg egy PowerShell-ablakot, és beírja a [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps)parancsot, annak tudatja, hogy az alkalmazás típusa a **VisualObjects** 1.0.0.0 alkalmazástípusa, amely et telepítették.  

Az alkalmazáscsomag a következő relatív elérési út alatt tárolódik, ahol a Service Fabric SDK - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*kibontja a tömörítést. Meg kell találnia egy "Csomag" mappát abban a könyvtárban, ahol az alkalmazáscsomag található. Ellenőrizze az időbélyegeket, hogy megbizonyosodjon arról, hogy ez a legújabb build (előfordulhat, hogy módosítania kell az elérési utak megfelelően is).

Most másolja a frissített alkalmazáscsomagot a Service Fabric ImageStore (ahol az alkalmazáscsomagok által tárolt Service Fabric). Az *ApplicationPackagePathInImageStore* paraméter tájékoztatja a Service Fabric-et arról, hogy hol található az alkalmazáscsomag. A frissített alkalmazást a "VisualObjects\_V2" alkalmazásba helyeztük a következő paranccsal (előfordulhat, hogy újra megfelelően módosítania kell az elérési utakat).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

A következő lépés az alkalmazás regisztrálása a Service Fabric szolgáltatással, amely a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) paranccsal hajtható végre:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Ha az előző parancs nem sikerül, valószínű, hogy az összes szolgáltatás újraépítésére van szükség. A2.

Javasoljuk, hogy távolítsa el az alkalmazáscsomagot, miután az alkalmazás sikeresen regisztrált.  Az alkalmazáscsomagok lemezképtárolóból történő törlése rendszererőforrásokat szabadít fel.  A nem használt alkalmazáscsomagok megtartása lemezes tárolást eredményez, és az alkalmazás teljesítményével kapcsolatos problémákhoz vezet.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>5. lépés: Az alkalmazásfrissítés indítása
Most már készen állunk az alkalmazásfrissítés elindítására a [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) paranccsal:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Az alkalmazás neve megegyezik az *ApplicationManifest.xml* fájlban leírt névvel. A Service Fabric ezt a nevet használja annak azonosítására, hogy melyik alkalmazás frissítése történik. Ha az időtúljárdák túl rövidre vannak állítva, hibaüzenet jelenhet meg, amely a problémát jelenti. Olvassa el a hibaelhárítási szakaszt, vagy növelje az időkiidőket.

Most, az alkalmazás frissítésének előrehaladtával, figyelheti a Service Fabric Explorer használatával, vagy a [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) PowerShell paranccsal: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Néhány percen belül az előző PowerShell-parancs használatával kapott állapotnak meg kell adnia, hogy az összes frissítési tartomány frissítve lett (befejeződött). És meg kell találnia, hogy a vizuális tárgyak a böngésző ablakban kezdtek forog!

Megpróbálhatja a frissítést a 2-es verzióról a 3-as verzióra, vagy a 2-es verzióról az 1-es verzióra edzésként. A 2-es verzióról az 1-es verzióra való áttérés szintén frissítésnek minősül. Játssz on time-outs és az egészségügyi politikák, hogy magát megismerni őket. Ha egy Azure-fürtre telepíti, a paramétereket megfelelően kell beállítani. Jó, hogy állítsa be a time-out konzervatív.

## <a name="next-steps"></a>További lépések
[Az alkalmazás Visual Studio használatával történő frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti a Visual Studio használatával történő alkalmazásfrissítésen.

Az alkalmazás frissítési paraméterek keltörténő frissítésének [szabályozása.](service-fabric-application-upgrade-parameters.md)

Tegye kompatibilissé az alkalmazásfrissítéseket az [adatok szerializálásának](service-fabric-application-upgrade-data-serialization.md)használatával.

A speciális funkciók használata az alkalmazás frissítése során a [Speciális témakörökre](service-fabric-application-upgrade-advanced.md)hivatkozva.

Az alkalmazásfrissítések gyakori problémáit az [alkalmazásfrissítések hibaelhárítása](service-fabric-application-upgrade-troubleshooting.md)című témakör lépéseire hivatkozva oldhatja meg.

