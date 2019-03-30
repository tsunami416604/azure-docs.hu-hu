---
title: PowerShell-lel Service Fabric-alkalmazás frissítése |} A Microsoft Docs
description: Ez a cikk végigvezeti a Service Fabric-alkalmazás üzembe helyezése, a kód megváltoztatása és PowerShell-lel frissítés bevezetéséről élménye.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 9bc75748-96b0-49ca-8d8a-41fe08398f25
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 3d4634249b0dc2638373383b7a7cea376b98c65a
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670539"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>A Service Fabric alkalmazás frissítése a PowerShell használatával
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Ajánlott frissítési módszer a leggyakrabban használt és a figyelt működés közbeni frissítés.  Az Azure Service Fabric health házirendjei alapján az alkalmazás frissítés alatt állapotát figyeli. Után (UD) frissítési tartomány frissítve van, Service Fabric kiértékeli az alkalmazás állapotáról, és továbblép a következő frissítési tartomány vagy egészségügyi függően a frissítés meghiúsul.

A figyelt alkalmazás frissítését a felügyelt és natív API-k, PowerShell, Azure CLI-vel, a Java vagy REST használatával hajtható végre. A Visual Studio használatával történő frissítése, lásd: [Visual Studio használatával az alkalmazás frissítéséhez](service-fabric-application-upgrade-tutorial.md).

A Service Fabric figyelt működés közbeni frissítésekkel az alkalmazás-rendszergazda konfigurálhatja a kiértékelés olyan házirendet, amely a Service Fabric segítségével határozza meg, ha az alkalmazás állapota kifogástalan. Emellett a rendszergazda konfigurálhatja a művelet végrehajtását, ha az állapot értékelése nem sikerült (például egy automatikus visszaállítása során.) Ez a szakasz végigvezeti egy figyelt a frissítést, amely a Powershellt használja az SDK-minták egyikét. 

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>1. lépés: Létrehozása és üzembe helyezése a Visual objektumok minta
Hozhat létre, és kattintson a jobb gombbal az alkalmazásprojektre, az alkalmazás közzététele **VisualObjectsApplication,** , és válassza a **közzététel** parancsot.  További információkért lásd: [Service Fabric-alkalmazás frissítési oktatóanyag](service-fabric-application-upgrade-tutorial.md).  Másik megoldásként a PowerShell használatával az alkalmazás üzembe helyezéséhez.

> [!NOTE]
> A Service Fabric-parancsokhoz a PowerShell használni, először birtokában használatával csatlakozni a fürthöz a `Connect-ServiceFabricCluster` parancsmagot. Hasonlóképpen feltételezzük, hogy a fürt már be lett állítva a helyi gépen. Tekintse meg a cikket a [a Service Fabric fejlesztési környezet beállítása](service-fabric-get-started.md).
> 
> 

Után hoz létre a projektet a Visual Studióban, a PowerShell-parancs használható [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) az alkalmazáscsomag átmásolása a ImageStore. Ha szeretné ellenőrizni az alkalmazáscsomagot helyileg, használja a [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) parancsmagot. A következő lépés az, hogy regisztrálnia kell az alkalmazást a Service Fabric futtatókörnyezet történő a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) parancsmagot. A következő lépés az, hogy indítsa el az alkalmazás egy példányát a [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) parancsmagot.  Ezek három lépések a következők használatával csatlakoztatja a **telepítés** menüpont a Visual Studióban.  A telepítés elvégzése után meg kell törölni a képet tárból másolt alkalmazáscsomag annak érdekében, hogy csökkentse a felhasznált erőforrásokért.  Az alkalmazástípus már nem szükséges, ha, akkor ugyanezen okból nem regisztrált kell lennie. Lásd: [PowerShell-lel telepítés és eltávolítás alkalmazások](service-fabric-application-upgrade-tutorial-powershell.md) további információt.

Most már használhatja [a fürt és az alkalmazás megtekintése a Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Az alkalmazás rendelkezik egy webszolgáltatás, amelyet is azután nyit meg, az Internet Explorerben írja be [ http://localhost:8081/visualobjects ](http://localhost:8081/visualobjects) címet a címsorba.  Megtekintheti az egyes lebegőpontos visual objektumok Navigálás a képernyő.  Emellett használhatja [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) alkalmazás állapotának ellenőrzéséhez.

## <a name="step-2-update-the-visual-objects-sample"></a>2. lépés: Frissítés a Visual objektumok minta
Észreveheti, hogy az 1. lépésben telepített verziójával, a vizuális objektumok nem elforgatása. Most frissítse az alkalmazás egy, a vizuális objektumokat is elforgatása.

Válassza ki a VisualObjects megoldáson belül a VisualObjects.ActorService projektet, és nyissa meg a StatefulVisualObjectActor.cs fájlt. Ugrás a fájlt, a metódus `MoveObject`, tegye megjegyzésbe `this.State.Move()`, és vonja vissza `this.State.Move(true)`. Ez a változás elforgatása az objektumok, a szolgáltatás frissítése után.

Frissíteni kell a *ServiceManifest.xml* (alatt PackageRoot) fájlt a projekt **VisualObjects.ActorService**. Frissítés a *CodePackage* és a 2.0-s és a megfelelő sorokat verzióját a *ServiceManifest.xml* fájlt.
Használhatja a Visual Studio *Manifest fájlok szerkesztése* után a jobb gombbal a megoldás a jegyzékfájl módosíthatja a beállítást.

Után a módosításokat, a jegyzékfájlt a következőhöz hasonlóan kell kinéznie (a kiemelt részeket a változások megjelenítéséhez):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Most már a *ApplicationManifest.xml* fájlt (alatt található a **VisualObjects** projektre a **VisualObjects** megoldás) frissül, és a 2.0-sverziójához**VisualObjects.ActorService** projekt. Emellett a alkalmazás verzióra frissült 2.0.0.0-s a 1.0.0.0. A *ApplicationManifest.xml* az alábbi kódrészlethez hasonlóan kell kinéznie:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Most hozza létre a projektet kiválasztásával csak a **ActorService** projektet, és majd kattintson a jobb gombbal, majd válassza a **hozhat létre** lehetőség a Visual Studióban. Ha **újraépíti az összes**, frissítenie kell az összes projekt-verziók a kódot kellene rendelkeznie módosítása óta. Ezután nézzük csomag a frissített alkalmazás kattintson a jobb gombbal a ***VisualObjectsApplication***, a Service Fabric menü kiválasztásával és a választás **csomag**. Ez a művelet létrehoz egy alkalmazáscsomagot, amely telepíthető.  A frissített alkalmazás a telepítésre készen áll.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>3. lépés:  Döntse el, a házirendek és a frissítési paraméterek
Ismerje meg az a [alkalmazásfrissítési paraméterek](service-fabric-application-upgrade-parameters.md) és a [frissítési folyamat](service-fabric-application-upgrade.md) lekérni a különböző frissítési paraméterek, időtúllépéseket és egészségügyi feltétel alkalmazása beható ismerete. Ebben a bemutatóban a service health értékelési feltétel az alapértelmezett értékre (és javasolt) értéket, ami azt jelenti, hogy az összes szolgáltatás és -példány legyen *kifogástalan* a frissítés után.  

Most azonban növeli a *HealthCheckStableDuration* 180 másodperc (úgy, hogy a szolgáltatások kifogástalan állapotú a frissítés előrehalad az a következő frissítési tartománnyal, mielőtt legalább 120 másodperc).  Nézzük is beállíthat a *UpgradeDomainTimeout* kell 1200-as másodperc és a *UpgradeTimeout* 3000 másodperc kell.

Végezetül hozzunk is beállíthat a *UpgradeFailureAction* visszaállítására. Ez utóbbi lehetőség megköveteli a Service Fabric szeretné visszaállítani az alkalmazás a korábbi verzióra, ha problémákat tapasztal a frissítés során. Így (a 4. lépését) a frissítés indításakor a következő paraméterek vannak megadva:

FailureAction visszaállítási =

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>4. lépés: Frissítés az alkalmazás előkészítése
Az alkalmazás mostantól beépített és készen áll a frissítendő áll. Ha, nyissa meg egy PowerShell-ablakot rendszergazdaként, és írja be [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps), hagyja meg tudom, hogy az alkalmazás típusát 1.0.0.0 **VisualObjects** van telepítve.  

A következő relatív elérési úton, ahol a Service Fabric SDK - tömörítés nélkül tárolja az alkalmazáscsomag *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Keresse meg a "Csomag" mappát ebben a könyvtárban, az alkalmazáscsomag tárolására. Ellenőrizze a időbélyegei annak érdekében, hogy-e a legújabb buildre (Előfordulhat, hogy módosítania az elérési utak megfelelően is).

Most tekintsük másolja a frissített alkalmazást csomagot a Service Fabric ImageStore (az alkalmazáscsomagok tárolására a Service Fabric által). A paraméter *ApplicationPackagePathInImageStore* tájékoztatja a Service Fabric, ahol megtalálhatja az alkalmazáscsomagot. A frissített alkalmazás rendelkezik tárgyaljuk, "VisualObjects\_V2" az alábbi parancsot (Előfordulhat, hogy módosítania elérési utak újra megfelelően).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

A következő lépés az, hogy az alkalmazás regisztrálása a Service Fabric, amelyek használatával végezheti el a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) parancsot:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Ha az előző parancs nem jár sikerrel, valószínű, hogy kell-e az összes szolgáltatást újjáépítést. Ahogy már említettük, a 2. lépésben, akkor előfordulhat, hogy, valamint a webszolgáltatás-verziójának frissítése.

Ajánlott az alkalmazáscsomag törlése után az alkalmazás regisztrálása sikeres volt.  Rendszer-erőforrásokat szabadít alkalmazáscsomagok törlése a képet tárból.  A fel nem használt alkalmazáscsomagok lemezes tárolást használ fel, és alkalmazásteljesítménnyel kapcsolatos problémák vezet.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>5. lépés: Indítsa el az alkalmazás frissítése
Most, hogy minden megvan, indítása az alkalmazás frissítése a [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) parancsot:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Az alkalmazás neve megegyezik a azt ennek a *ApplicationManifest.xml* fájlt. A Service Fabric azonosításához, amelyek az alkalmazás első frissítés ezt a nevet használja. Ha beállította az időtúllépéseket túl rövid, egy hibaüzenet arról, hogy a probléma felmerülhet. Tekintse meg a hibaelhárítási szakaszt, vagy növelje a várakozási idő.

Most, mint az alkalmazás frissítési telepítése folytatódik, nyomon követheti a Service Fabric Explorer használatával vagy a [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) PowerShell-parancsot: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Néhány perc alatt, az állapot, a PowerShell-paranccsal az előző, tartalmaznia kell, hogy az összes frissítési tartományok frissített (Befejezve). És látnia kell, hogy a vizuális objektumokat a böngészőablak megkezdte az elforgatás!

3. verzió 2-es vagy 2-es verzióra gyakorlatként 1 frissítése próbálhatja ki. 1. verziójának 2. verzió áthelyezését, a frissítés is számít. Időtúllépések és, hogy saját maga őket ismeri házirendek kísérletezhet. Egy Azure-fürtön való telepítéséhez, a paramétereket kell megfelelően be kell állítani. A jó konzervatív módon vegyen figyelembe állítsa be a várakozási idő.

## <a name="next-steps"></a>További lépések
[A Visual Studio használata az alkalmazás frissítéséhez](service-fabric-application-upgrade-tutorial.md) végigvezeti egy alkalmazás frissítése a Visual Studio használatával.

Vezérelheti, hogyan az alkalmazásfrissítések használatával [frissítési paraméterek](service-fabric-application-upgrade-parameters.md).

Hogyan használja az alkalmazásfrissítések kompatibilissé [adatok szerializálása](service-fabric-application-upgrade-data-serialization.md).

Speciális funkciók használata közben lépésként tekintse át az alkalmazás frissítéséhez [Speciális témakörök](service-fabric-application-upgrade-advanced.md).

Az alkalmazásfrissítések gyakori problémák megoldása szerint hajtsa végre a hivatkozó [alkalmazásfrissítések hibaelhárítása](service-fabric-application-upgrade-troubleshooting.md).

