---
title: Csomag az Azure Service Fabric-alkalmazás |} A Microsoft Docs
description: Hogyan csomag a Service Fabric-alkalmazás üzembe helyezése egy fürtön előtt.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: b8e66a9d5bba0c48f15b1ccd3f2d47e5405db792
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785598"
---
# <a name="package-an-application"></a>Alkalmazás becsomagolása

Ez a cikk bemutatja, hogyan csomag a Service Fabric-alkalmazás, és lehetővé teszi a telepítésre készen áll.

## <a name="package-layout"></a>Csomag elrendezés

Az alkalmazásjegyzékben, egy vagy több szolgáltatásjegyzékek és egyéb szükséges csomagfájlok kell beállítani egy Service Fabric-fürtön történő üzembe helyezéshez megadott elrendezésben. A példa jegyzékek ebben a cikkben a következő könyvtárstruktúrát szervezett kell:

```
tree /f .\MyApplicationType
```

```Output
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

A médiafájlokat tartalmazó mappák neve megfelelően a **neve** minden megfelelő elem attribútumai. Például, ha a szolgáltatásjegyzék tartalmazott két kód csomagot a nevekkel **MyCodeA** és **MyCodeB**, majd ugyanazzal a névvel két mappát tartalmaz a szükséges bináris fájlokat minden kódcsomaghoz.

## <a name="use-setupentrypoint"></a>SetupEntryPoint használata

A tipikus forgatókönyvek **SetupEntryPoint** amikor egy végrehajtható fájl futtatásához a szolgáltatás indulása előtt szüksége vagy egy emelt szintű jogosultságokkal a művelet végrehajtásához. Példa:

* Beállítását, és a környezeti változók, amelyet a szolgáltatás futtatható fájlja inicializálása. Nem korlátozódik az csak a Service Fabric programozási modell használatával írt végrehajtható. Például npm.exe kell néhány környezeti változók konfigurált node.js-alkalmazás üzembe helyezéséhez.
* Hozzáférés-vezérlés beállítása a biztonsági tanúsítványok telepítésével.

További információ a konfigurálása a **SetupEntryPoint**, lásd: [a házirend-szolgáltatásbeállítás belépési pontjának konfigurálása](service-fabric-application-runas-security.md)

<a id="Package-App"></a>

## <a name="configure"></a>Konfigurálás

### <a name="build-a-package-by-using-visual-studio"></a>Csomag létrehozása a Visual Studio használatával

Ha az alkalmazás létrehozása a Visual Studio 2015 használatával, a csomag paranccsal automatikusan hozzon létre egy csomagot, amely megfelel a fent leírt elrendezést.

Hozzon létre egy csomagot, kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza ki a csomag parancs, ahogy az alábbi:

![A Visual Studio az alkalmazás csomagolás][vs-package-command]

Csomagolási befejeződése után annak helye a csomag a **kimeneti** ablak. A csomagolási lépés üzembe helyezésekor vagy alkalmazás hibakeresése a Visual Studio automatikusan történik.

### <a name="build-a-package-by-command-line"></a>Csomag létrehozása a parancssorból

Programozott módon csomagolnia az alkalmazást a lehetőség arra is `msbuild.exe`. Technikai részletek a Visual Studio fut, így a kimenet a azonos.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>A csomag tesztelése

A csomag struktúra helyileg a PowerShell használatával ellenőrizheti a [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) parancsot.
Ez a parancs ellenőrzi a jegyzékfájl problémák elemzése, és ellenőrizze minden hivatkozás. Ezzel a paranccsal csak az a csomagban lévő fájlok és könyvtárak szerkezeti helyességét ellenőrzi.
A kódon vagy csomag tartalmának ellenőrzése, hogy telepítve-e az összes szükséges fájlok túl bármelyike nem ellenőrzi.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Ez a hiba azt mutatja, hogy a *MySetup.bat* szolgáltatásjegyzékben hivatkozott fájl **SetupEntryPoint** a kódcsomag hiányzik. A hiányzó fájl hozzáadása után az alkalmazás-ellenőrzésre adja át:

```
tree /f .\MyApplicationType
```

```Output
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
```

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
True
```

Ha az alkalmazás [alkalmazásparamétereket](service-fabric-manage-multiple-environment-app-configuration.md) definiált, továbbíthatja őket [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) megfelelő érvényesítés.

Ha tudja, hogy a fürt, ahol az alkalmazás központi telepítése, akkor ajánlott adja át a `ImageStoreConnectionString` paraméter. A csomag ebben az esetben is ellenőrzi az alkalmazás korábbi verziói már fut a fürtben. Például az érvényesítés képes észlelni, hogy egy csomag ugyanazzal a verzióval, de különböző tartalom már telepítve lett.  

Az alkalmazás megfelelően van csomagolva, és a átmennek az ellenőrzésen, fontolja meg a tömörítés gyorsabb üzembe helyezési műveletek esetében a csomagot.

## <a name="compress-a-package"></a>A csomag tömörítése

Ha a csomag mérete nagy, vagy sok fájl van, gyorsabb üzembe helyezéshez képes tömöríteni. A tömörítés csökkenti a fájlok számát és a csomag mérete.
Egy tömörített alkalmazáscsomag [az alkalmazáscsomag feltöltése](service-fabric-deploy-remove-applications.md#upload-the-application-package) hosszabb időt vehet igénybe, a tömörítetlen csomag feltöltése képest, különösen akkor, ha a tömörítés másolása részeként történik. A tömörítés [regisztrálása](service-fabric-deploy-remove-applications.md#register-the-application-package) és [nem regisztrálása az alkalmazástípus](service-fabric-deploy-remove-applications.md#unregister-an-application-type) gyorsabb.

Az üzembe helyezési mechanizmus ugyanazon a tömörített és tömörítetlen csomagokhoz. Ha a csomag tömörített, a fürt lemezképtárolójába ilyen van tárolva, és azt van tömörítetlen a csomóponton, az alkalmazás futtatása előtt.
A tömörítés lecseréli a tömörített verzió érvényes a Service Fabric-csomag. A mappa lehetővé kell tennie, hogy rendelkezik írási engedéllyel. A tömörítés futtat egy már tömörített csomagot poskytne módosítása nélkül.

Csomag tömörítheti a Powershell-parancs futtatásával [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) az `CompressPackage` váltani. A csomag azonos kibonthatja parancsot `UncompressPackage` váltani.

A következő parancsot a csomag nélkül másolja, a lemezképtároló tömöríti. Másolhat egy tömörített csomag egy vagy több Service Fabric-fürtök használatával szükség szerint [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) nélkül a `SkipCopy` jelzőt.
A csomag most már tartalmazza a fájlok kibontását a `code`, `config`, és `data` csomagokat. Az alkalmazásjegyzék- és a szolgáltatásjegyzékek vannak nem zip, mivel számos belső művelethez szükség van. Ha például a csomag megosztása után a alkalmazás kapcsolattípus neve és verziója kibontása a jegyzékek eléréséhez szükséges minden egyes ellenőrzések. A jegyzékek becsomagolás biztosítja, ezek a műveletek nem elég hatékony.

```
tree /f .\MyApplicationType
```

```Output
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
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

Másik lehetőségként tömörítése, és másolja a csomagot a [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) egy lépésben.
Ha a csomag nagyméretű, adja meg, hogy időt a csomag tömörítése és a feltöltés, a fürthöz is elég nagy időtúllépés.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

A Service Fabric belsőleg, kiszámítja az ellenőrzőösszegek az alkalmazáscsomagok ellenőrzés céljából. Tömörítés használata esetén az ellenőrzőösszegek minden csomag tömörített verzióiban vannak számított. Különböző ellenőrzőösszeg egy új zip generálása az ugyanazon alkalmazás csomagból hoz létre. Érvényesítési hibák megelőzése érdekében használjon [diff kiépítés](service-fabric-application-upgrade-advanced.md). Ezzel a beállítással nem tartalmazzák a változatlan csomagokat az új verzióban. Ehelyett hivatkozzon őket közvetlenül a az új adatszolgáltatási jegyzékfájl.

Ha diff kiépítése nem lehetőség, a csomagok meg kell adni, hozzon létre az új verziókat a `code`, `config`, és `data` csomagok nem egyeznek az ellenőrzőösszegek elkerülése érdekében. Változatlan csomagok új verzióit generálása szükség, ha egy tömörített csomagot használ, függetlenül a korábbi verziót használ-e a tömörítés vagy sem.

A csomag most már megfelelően csomagolva, érvényesítve, és tömörített (ha szükséges), hogy az készen álljon [üzembe helyezési](service-fabric-deploy-remove-applications.md) legalább egy Service Fabric-fürtök.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Compress csomagok üzembe helyezésekor a Visual Studio használatával

Visual Studio csomagokat a központi telepítést a tömörítendő hozzáadásával utasíthatja a `CopyPackageParameters` elem a közzétételi profilt, és állítsa be a `CompressPackage` attribútumot `true`.

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Hozzon létre egy sfpkg

6.1-es verzióval kezdődően a Service Fabric lehetővé teszi a külső adattárba kiépítése.
Ezzel a beállítással az alkalmazáscsomag nem kell átmásolni a lemezképtároló. Ehelyett létrehozhat egy `sfpkg` , és töltse fel az egy külső tároló, majd adja meg a letöltési URI-t a Service Fabric üzembe helyezésekor. Több fürt ugyanaz a csomag építhető ki. Másolja a csomagot minden egyes fürt szükséges idő a külső tárolóból kiépítés menti.

A `sfpkg` fájlt a zip, a kezdeti alkalmazáscsomag tartalmazza, és a bővítmény ".sfpkg".
A zip-fájl belül az alkalmazáscsomag tömörített vagy tömörítetlen. A tömörítés a zip-fájl belül alkalmazáscsomag code, config és csomag adatszintek történik [a korábban említett](service-fabric-package-apps.md#compress-a-package).

Hozhat létre egy `sfpkg`, indítsa el az eredeti alkalmazáscsomag, tömörített vagy nem tartalmazó mappát. Ezután használja minden olyan segédprogram a zip, a ".sfpkg" kiterjesztésű mappát. Például [ZipFile.CreateFromDirectory](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

A `sfpkg` kell feltölteni a Service Fabric-en kívül a sávon kívüli külső tárolójában. A külső tároló minden olyan tároló, amely elérhetővé teszi a REST-http- vagy https-végpont lehet. Üzembe helyezésekor, a Service Fabric letölteni egy művelet végrehajtása a `sfpkg` alkalmazáscsomagot, így a tároló engedélyeznie kell a csomag számára OLVASÁSI hozzáférést.

Hozza létre a csomagot, használja a külső kiépítését, amelyhez szükség van a letöltési URI és az alkalmazás típussal kapcsolatos információk.

>[!NOTE]
> Kiépítés a lemezképet tároló relatív elérési út alapján jelenleg nem támogatja a `sfpkg` fájlokat. Ezért a `sfpkg` nem kell átmásolni a lemezképtároló.

## <a name="next-steps"></a>További lépések

[Üzembe helyezése és távolíthat el alkalmazásokat] [ 10] ismerteti, hogyan lehet az alkalmazáspéldányok kezelése PowerShell használatával

[Kezelése több környezethez alkalmazásparamétereket] [ 11] paramétereket és a különböző alkalmazáspéldányok a környezeti változók konfigurálását ismerteti.

[Az alkalmazás biztonsági szabályzatainak konfigurálásához] [ 12] ismerteti, hogyan lehet futtatni a szolgáltatásokhoz való hozzáférés korlátozása a biztonsági szabályzatok alapján.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md