---
title: "Az Azure-szolgáltatások csomag Fabric-alkalmazás |} Microsoft Docs"
description: "Hogyan csomag a Service Fabric-alkalmazás egy fürt üzembe helyezése előtt."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: ryanwi
ms.openlocfilehash: 486a27d7ca576c8fe1552c02eb24ece6b8bb2ba8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="package-an-application"></a>Egy alkalmazás becsomagolása
Ez a cikk ismerteti, hogyan csomag a Service Fabric-alkalmazás, és lehetővé teszi a telepítésre kész.

## <a name="package-layout"></a>Csomag elrendezés
Az alkalmazás jegyzékében, egy vagy több szolgáltatás jegyzékfájlban és egyéb szükséges fájlok egy adott elrendezésben átesett szolgáltatássablonjaikat a Service Fabric-fürt kell beállítani. A példa jegyzékfájlokat ebben a cikkben a következő könyvtárstruktúrát szervezett kell:

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

A elnevezése megfelelően a **neve** minden megfelelő elem attribútumaihoz. Például, ha a szolgáltatás jegyzékben szereplő két kód csomagok nevű **MyCodeA** és **MyCodeB**, majd ugyanazzal a névvel két mappák tartalmazná a szükséges bináris fájlok az egyes kódot.

## <a name="use-setupentrypoint"></a>SetupEntryPoint használata
A jellemző forgatókönyvek **SetupEntryPoint** Ha a szolgáltatás indítása előtt egy végrehajtható fájl futtatásához szükséges vagy egy emelt szintű jogosultságokkal a művelet végrehajtásához. Példa:

* Beállítását, valamint a környezeti változókat, amelyek a szolgáltatás futtatható fájlja kell inicializálása. Nincs korlátozva csak végrehajtható fájlok írása a Service Fabric programozási modell használatával. Például npm.exe kell néhány környezetiblokk-változót, egy node.js-alkalmazás telepítéséhez konfigurált.
* Hozzáférés-vezérlés beállítása biztonsági tanúsítványok telepítésével.

Konfigurálásával kapcsolatos további információ a **SetupEntryPoint**, lásd: [egy szolgáltatás-telepítés belépési pont vonatkozó házirend konfigurálása](service-fabric-application-runas-security.md)

<a id="Package-App"></a>
## <a name="configure"></a>Konfigurálás
### <a name="build-a-package-by-using-visual-studio"></a>Csomag létrehozása a Visual Studio használatával
Ha az alkalmazás létrehozása a Visual Studio 2015-öt használja, a csomag paranccsal automatikusan hozzon létre egy csomagot, amely megfelel a fenti elrendezését.

Csomag létrehozásához kattintson a jobb gombbal a projektre a Solution Explorer, és válassza ki a csomag parancs alább látható módon:

![A Visual Studio alkalmazás csomagolás][vs-package-command]

Csomagolás befejeződése után található a csomag helyét a **kimeneti** ablak. A csomagolási lépés esetén automatikusan telepíteni, vagy a Visual Studióban az alkalmazás hibakeresését.

### <a name="build-a-package-by-command-line"></a>Csomag létrehozása a parancssorból
Akkor is lehet programozott módon be az alkalmazást a csomag `msbuild.exe`. A technikai részletek a Visual Studio futtatja a kimeneti legyen, így.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>A csomag tesztelése
A csomag struktúra, a PowerShell segítségével helyileg segítségével ellenőrizheti a [teszt-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) parancsot.
Ez a parancs problémák elemzése jegyzékfájl keres, és ellenőrizze az összes hivatkozást. A parancs csak ellenőrzi a strukturális helyességét a könyvtárak és fájlok a csomagban.
A kódban, illetve az adatok csomag tartalmának ellenőrzése, hogy jelen-e minden szükséges fájlok túl bármelyikét nem ellenőrzése.

```
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Ez a hiba azt mutatja, hogy a *MySetup.bat* a szolgáltatás jegyzékben hivatkozott fájl **SetupEntryPoint** a kód csomagból hiányzik. A hiányzó fájl hozzáadása után az alkalmazás ellenőrzés továbbítja:

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
```

Ha az alkalmazás [alkalmazás paraméterei](service-fabric-manage-multiple-environment-app-configuration.md) definiált, is adja meg azokat a [teszt-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) megfelelő érvényesítés.

Ha ismeri a fürt, ahol az alkalmazás központi telepítése, adja meg az ajánlott a `ImageStoreConnectionString` paraméter. A csomag ebben az esetben is összevetni az alkalmazás korábbi verziója már fut a fürtön. Például az érvényesítési észleli, hogy egy csomag ugyanazzal a verzióval, de eltérő tartalomra már telepítve lett.  

Miután az alkalmazás megfelelően van csomagolva, és ellenőrzése sikeres, értékelje ki van szüksége a tömörítési méretét és a fájlok száma alapján.

## <a name="compress-a-package"></a>A csomag tömörítése
Ha nagy vagy sok fájl van egy csomagot, akkor gyorsabb telepítése tömöríthetők. Tömörítés csökkenti a fájlok számát és a csomag mérete.
A tömörített alkalmazáscsomag [alkalmazáscsomag feltöltése](service-fabric-deploy-remove-applications.md#upload-the-application-package) hosszabb időt vehet igénybe képest feltöltése a kibontott csomagot (ha kifejezetten tömörítési idő Beleszámítja a), de [regisztrálása](service-fabric-deploy-remove-applications.md#register-the-application-package) és [típustár regisztrációjának megszüntetése az alkalmazástípus](service-fabric-deploy-remove-applications.md#unregister-an-application-type) gyorsabb tömörített alkalmazás csomag.

A központi telepítési módszer használata ugyanabban a tömörített és tömörítetlen csomagokat. Ha a csomag tömörített, a fürt lemezképtárolóhoz ilyen van tárolva, és azt van tömörítetlen a csomóponton, az alkalmazás futtatása előtt.
A tömörítés a érvényes a Service Fabric-csomag tömörített verziójával váltja fel. A mappa engedélyeznie kell írási engedéllyel. Nincs változás tömörítési futó egy már tömörített csomagot adja eredményül.

A csomag tömörítheti a Powershell-parancs futtatásával [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) a `CompressPackage` váltani. A csomag azonos kibonthatja parancsot `UncompressPackage` váltani.

A következő parancs a csomag nélkül másolja az image store tömöríti. Másolhatja a tömörített csomag egy vagy több Service Fabric-fürtök használatával szükség szerint [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) nélkül a `SkipCopy` jelzőt.
A csomag most már tartalmazza a fájlok kibontását a `code`, `config`, és `data` csomagok. Az alkalmazás jegyzékében és a szolgáltatás jegyzékfájlokat vannak nem zip, mert szükség van a sok belső műveletekhez (például a csomag megosztási, alkalmazás neve és verziója kapcsolattípus kibontása az egyes ellenőrzések).
A jegyzékfájlokban tömörítés teszi ezeket a műveleteket nem hatékony.

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy

PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

Másik lehetőségként tömöríti, és másolja a csomagot a [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) egy lépésben.
Ha a csomag túl nagy, adja meg az időt a csomag tömörítés és a fürt való feltöltés elég magas időtúllépés.
```
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Belső a Service Fabric kiszámítja az alkalmazáscsomagok érvényesítéshez ellenőrzőösszegeket. Tömörítés használata esetén a ellenőrzőösszegeket minden csomag tömörített verziói vannak számítja.
Ha a másolt egy alkalmazáscsomag tömörítetlen verzióját, és az ugyanazon csomag tömörítési használni kívánt, módosítania kell a verziók a `code`, `config`, és `data` csomagok nem egyeznek az ellenőrzőösszegek elkerülése érdekében. Ha a csomagok nem változnak, a verzió módosítása helyett, akkor használhatja [különbözeti kiépítés](service-fabric-application-upgrade-advanced.md). Ezzel a beállítással nem tartalmazzák a változatlan csomag ehelyett hivatkozzon a szolgáltatás-jegyzékfájlból.

Hasonlóképpen ha a csomag tömörített változatának feltöltött, és egy tömörített csomagot használni kívánt, frissítenie kell a elkerülése érdekében a nem egyeznek az ellenőrzőösszegek verzióit is.

A csomag most már megfelelően csomagolt, érvényesítve, és tömörített (ha szükséges), hogy készen álljanak [telepítési](service-fabric-deploy-remove-applications.md) egy vagy több Service Fabric-fürtök.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Tömöríti a csomagok központi telepítésekor a Visual Studio használatával
Visual Studio-csomagokat a központi telepítés, tömörítésére hozzáadásával utasíthatja a `CopyPackageParameters` elemben, amely a közzétételi profilt, majd állítsa be a `CompressPackage` attribútumot `true`.

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="next-steps"></a>Következő lépések
[Központi telepítése és távolíthat el alkalmazásokat] [ 10] ismerteti, hogyan lehet alkalmazáspéldányok kezelése a PowerShell használatával

[Alkalmazás paramétereinek több környezet kezelése] [ 11] paramétereket és változókat környezet különböző alkalmazáspéldányok beállításának módját ismerteti.

[Az alkalmazás biztonsági szabályzatainak konfigurálásához] [ 12] ismerteti, hogyan korlátozhatja a hozzáférést a biztonsági házirendek szolgáltatások futtatásához.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
