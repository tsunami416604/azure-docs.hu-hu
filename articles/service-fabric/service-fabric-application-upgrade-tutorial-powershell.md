---
title: "Service Fabric-alkalmazás frissítés PowerShell használatával |} Microsoft Docs"
description: "Ez a cikk végigvezeti a Service Fabric-alkalmazás telepítése, kódjának a módosítása és PowerShell használatával frissítés terítésével élménye."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 9bc75748-96b0-49ca-8d8a-41fe08398f25
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 0306a219112a14121fd881a7cc52d58597a073a2
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Az alkalmazásfrissítés Service Fabric PowerShell-lel
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

A leggyakrabban használt és ajánlott frissítési módszer a figyelt működés közbeni frissítés.  Az Azure Service Fabric állapotfigyelő házirendjei alapján az alkalmazás frissítés alatt állapotát figyeli. Miután egy frissítési tartományt (UD) frissítve van, a Service Fabric kiértékeli az alkalmazás állapotának, és folytatódik a következő frissítési tartományra vagy attól függően, hogy a házirendek frissítése sikertelen.

A figyelt alkalmazáshoz frissítés használatával végezheti el a natív vagy felügyelt API-k, PowerShell vagy REST. Visual Studio használatával történő frissítése, lásd: [Visual Studio segítségével az alkalmazás frissítését](service-fabric-application-upgrade-tutorial.md).

Az alkalmazás-rendszergazda figyeli a Service Fabric működés közbeni frissítések segítségével beállíthatja a kiértékelési házirendet, amely a Service Fabric használ annak megállapításához, hogy az alkalmazás kifogástalan. Emellett a rendszergazda úgy is konfigurálhatja a a művelet végrehajtását, ha az állapot kiértékelésekor sikertelen (például egy automatikus visszaállítási történt.) Ez a szakasz végigvezeti az SDK-minták PowerShell használó egyik egy figyelt frissítésével. A következő Microsoft Virtual Academy videó is bemutatja, hogyan alkalmazás frissítése: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=OrHJH66yC_6406218965">
<img src="./media/service-fabric-application-upgrade-tutorial-powershell/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>1. lépés: Létrehozása és központi telepítése a Visual objektumok minta
Hozza létre, és tegye közzé az alkalmazást, kattintson a jobb gombbal a projektet, a **VisualObjectsApplication,** , majd válassza a **közzététel** parancsot.  További információkért lásd: [Service Fabric-alkalmazás frissítési oktatóanyag](service-fabric-application-upgrade-tutorial.md).  Azt is megteheti a PowerShell használatával telepítheti az alkalmazást.

> [!NOTE]
> A Service Fabric parancsok PowerShell is használható, mielőtt először használatával csatlakozzon a fürthöz a `Connect-ServiceFabricCluster` parancsmag. Hasonlóképpen feltételezzük, hogy a fürt már be van állítva a helyi számítógépen. Olvassa el a [a Service Fabric fejlesztési környezet létrehozása](service-fabric-get-started.md).
> 
> 

A Visual Studio projekt létrehozása, után a PowerShell-parancsot használhatja [másolási-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) alkalmazás csomag másolása a lemezképtárolóba. Ha szeretné ellenőrizni a csomag helyi, használja a [teszt-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) parancsmag. A következő lépés az, hogy az alkalmazás a Service Fabric futásidejű történő regisztrálása a [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) parancsmag. A következő lépés az, hogy az alkalmazás példányának használatával indítsa el a [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) parancsmag.  Fenti három lépést is megfelel a használatával a **telepítés** menüpont a Visual Studióban.  A telepítés elvégzése után kell távolítja el a lemezképtárolóból másolt alkalmazáscsomag az erőforrások használatának csökkentése érdekében.  Ha egy alkalmazás típus már nem szükséges, kell regisztrációját ugyanebből az okból. Lásd: [központi telepítése és törlése alkalmazás PowerShell-lel](service-fabric-application-upgrade-tutorial-powershell.md) további információt.

Most már használhatja [a fürt és az alkalmazás megtekintése a Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Az alkalmazás rendelkezik egy webszolgáltatás, amelyet is nyílik meg, az Internet Explorerben írja be a [http://localhost: 8081/visualobjects](http://localhost:8081/visualobjects) a böngésző címsorába.  Navigálás a képernyő néhány lebegőpontos vizuális objektum kell megjelennie.  Emellett használhatja [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) az alkalmazás állapotának ellenőrzéséhez.

## <a name="step-2-update-the-visual-objects-sample"></a>2. lépés: A Visual objektumok minta frissítése
Bizonyára észrevette, hogy, hogy az 1. lépésben telepített verziója, a visual objektumok nem elforgatása. Most frissítse az alkalmazás olyanra, ahol a vizuális objektumok is elforgatása.

Válassza ki a VisualObjects.ActorService projektet a VisualObjects megoldáson, és nyissa meg a StatefulVisualObjectActor.cs fájlt. Ugrás a fájlt, a módszer `MoveObject`, megjegyzéssé `this.State.Move()`, és állítsa vissza `this.State.Move(true)`. Ez a változás az objektumok elforgatja, a szolgáltatás frissítése után.

Is frissíteni kell a *ServiceManifest.xml* fájlt (PackageRoot) a projekt **VisualObjects.ActorService**. Frissítés a *CodePackage* és a 2.0-s és a megfelelő sorok az verziója a *ServiceManifest.xml* fájlt.
Használhatja a Visual Studio *Manifest fájlok szerkesztése* lehetőséget, kattintson a jobb gombbal a megoldásban a jegyzékfájl módosítások után.

A módosításokat, miután a jegyzék a következőket kell hasonlítania (kiemelt bizonyos részei a módosításoknak):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Most a *ApplicationManifest.xml* fájl (alatt található a **VisualObjects** projekt alatt a **VisualObjects** megoldás) 2.0-s verzióját a frissül**VisualObjects.ActorService** projekt. Emellett a alkalmazás verzióra frissül 2.0.0.0-s 1.0.0.0 a. A *ApplicationManifest.xml* az alábbi kódrészletben hasonlóan kell kinéznie:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Most, a projekt buildjének elkészítéséhez kiválasztásával csak a **ActorService** projektet, és majd kattintson a jobb gombbal, majd válassza a **Build** lehetőséget a Visual Studio. Ha **építse újra az összes**, mivel a kód volna frissítenie kell az összes projektet, a verziók. Ezután tegyük a frissített alkalmazás becsomagolása kattintson a jobb gombbal a ***VisualObjectsApplication***, a Service Fabric menü kiválasztásával, és válassza **csomag**. Ez a művelet létrehoz egy alkalmazáscsomagot, amely telepíthető.  A frissített alkalmazás készen áll a telepíthető.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>3. lépés: Házirendek mellett dönt, és frissítse a paraméterek
Ismerje meg, a [alkalmazás frissítési paraméterei](service-fabric-application-upgrade-parameters.md) és a [frissítési folyamat](service-fabric-application-upgrade.md) lekérni a különböző frissítési paraméterek időtúllépéseket és állapotfigyelő feltétel alkalmazása beható ismerete. Jelen kalauz használatához a szolgáltatás állapotának kiértékelési feltétel az alapértelmezett értékre van beállítva (és a javasolt) értéket, ami azt jelenti, hogy az összes szolgáltatás és példányok legyen *kifogástalan* a frissítés után.  

Azonban nézzük növeli a *HealthCheckStableDuration* 60 másodperc (így a szolgáltatások csak megfelelő előtt a frissítés előrehalad az a következő frissítési tartományra legalább 20 másodperc).  Most is beállíthat a *UpgradeDomainTimeout* az 1200-as másodperc és a *UpgradeTimeout* kell 3000 másodperc.

Végezetül is állítsa a *UpgradeFailureAction* visszaállítani. Ez a beállítás megköveteli a Service Fabric szeretné visszaállítani az előző verzió az alkalmazás Ha probléma merül fel a frissítés során találkozik. Így a frissítés (4. lépés) indításakor a következő paraméterek vannak megadva:

FailureAction visszaállítási =

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>4. lépés: A frissítésre alkalmazás előkészítése
Az alkalmazás most már áll, beépített és készen áll a frissíthető. Ha megnyit egy PowerShell-ablak rendszergazda, írja be [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps), akkor hagyja, hogy azt jelzi, hogy a rendszer alkalmazás típusú 1.0.0.0 **VisualObjects** telepített van.  

Az alkalmazás csomag megtalálható a következő relatív elérési úton, ahol a Service Fabric SDK - tömörítés nélkül *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Ebben a könyvtárban, az alkalmazáscsomag tárolására látnia kell egy "Csomagja" mappát. Ellenőrizze a helyi időre való győződjön meg arról, hogy-e a legújabb buildjével (esetleg módon módosítsa az is).

Most tegyük másolja a frissített alkalmazáscsomag a Service Fabric lemezképtárolóba (az alkalmazáscsomagok tárolására Service Fabric által). A paraméter *ApplicationPackagePathInImageStore* arról tájékoztatja a Service Fabric hol találnak a alkalmazáscsomagot. Azt a frissített alkalmazás rendelkezik be "VisualObjects\_V2" (esetleg újra annak megfelelően módosítsa a elérési utak) a következő paranccsal.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

A következő lépés az, hogy az alkalmazás regisztrálása a Service Fabric, amelyek használatával végezheti el a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) parancs:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Ha az előző parancs nem jár sikerrel, valószínű, hogy kell-e egy újjáépítését az összes szolgáltatás. 2. lépésben említett, előfordulhat, hogy, valamint a WebService verzióra frissíteni.

Ajánlott az alkalmazáscsomag eltávolítása után az alkalmazás regisztrálása sikeres volt.  Rendszererőforrások szabaddá alkalmazáscsomagok az image store való törlésekor.  Nem használt alkalmazáscsomagok használ fel a lemezes tárolás és alkalmazást teljesítményproblémák vezet.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>5. lépés: Indítsa el az alkalmazás frissítését
Most, még az alkalmazásfrissítés használatával indítsa el az összes beállítása a [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) parancs:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Az alkalmazás neve nem azonos a leírtaknak azt a *ApplicationManifest.xml* fájlt. A Service Fabric azonosítására, mely az alkalmazás első frissítés használja ezt a nevet. Ha az időtúllépéseket túl rövid, egy hibaüzenet arról, hogy a probléma merülhetnek fel. Tekintse át a hibaelhárítási részt, vagy növelje az időtúllépéseket.

Most, mint az alkalmazás frissítési telepítése folytatódik, figyelheti használatával a Service Fabric Explorer vagy a [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) PowerShell-parancsot: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Néhány perc múlva, az állapotot, az előző PowerShell-parancs használatával portáltól kell nyújtaniuk, hogy minden frissítési tartományok frissített (elvégezve). És látnia kell, hogy a böngészőablakban visual objektumok elindította elforgatása!

Próbálja meg frissíteni a 3-as verziójú 2-es verzióját, vagy a 2-es verzióra, egy gyakorlatot 1. A 2-es áthelyezése az 1-es frissítés is minősül. Lejátszás a időtúllépéseket és az állapotházirendeket el saját magának ismeri a őket. Egy Azure fürthöz való telepítéséhez, a paraméterek kell megfelelően be kell állítani. Az időtúllépéseket konzervatív módon beállítása helyes legyen.

## <a name="next-steps"></a>További lépések
[Visual Studio segítségével az alkalmazás frissítését](service-fabric-application-upgrade-tutorial.md) végigvezeti Önt az alkalmazásfrissítés Visual Studio használatával.

Szabályozhatja, hogy az alkalmazás használatával frissíti [frissítési paraméterek](service-fabric-application-upgrade-parameters.md).

Az alkalmazásfrissítéseket által használatának megtanulása kompatibilissé [adatszerializálás](service-fabric-application-upgrade-data-serialization.md).

Összetettebb funkciók használata az alkalmazás frissítésekor szakaszra [témakörök speciális](service-fabric-application-upgrade-advanced.md).

Alkalmazásfrissítések gyakori problémáinak megoldásához hajtsa végre a hivatkozással [alkalmazásfrissítések hibaelhárítási](service-fabric-application-upgrade-troubleshooting.md).

