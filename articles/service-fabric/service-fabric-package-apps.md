---
title: Azure Service Fabric-alkalmazás csomagolása
description: Ismerje meg az Azure Service Fabric-alkalmazások csomagolását, és hogyan készülhet fel a fürtbe való üzembe helyezésre.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 7c99eec28ac06ecf666d6dda1015f889841a5dbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258342"
---
# <a name="package-an-application"></a>Alkalmazás becsomagolása

Ez a cikk ismerteti, hogyan csomag egy Service Fabric-alkalmazást, és készen áll a központi telepítésre.

## <a name="package-layout"></a>Csomag elrendezése

Az alkalmazásjegyzéket, egy vagy több szolgáltatásjegyzéket és más szükséges csomagfájlokat egy adott elrendezésben kell rendszerezni a Service Fabric-fürtbe való telepítéshez. A jelen cikkben szereplő példa jegyzékfájljait a következő könyvtárstruktúrába kell rendszerezni:

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

A mappák elnevezése megegyezik az egyes megfelelő elemek **Name** attribútumainak. Ha például a szolgáltatásjegyzék két **Kódkód** és **MyCodeB**nevű kódcsomagot tartalmaz, akkor két azonos nevű mappa tartalmazza az egyes kódcsomagokhoz szükséges bináris fájlokat.

## <a name="use-setupentrypoint"></a>A SetupEntryPoint használata

A **SetupEntryPoint** használatának tipikus forgatókönyvei azok, amikor a szolgáltatás indítása előtt futtatnia kell egy végrehajtható fájlt, vagy emelt szintű jogosultságokkal rendelkező műveletet kell végrehajtania. Példa:

* A szolgáltatás végrehajtható rendszerének igényeinek megfelelő környezeti változók beállítása és inicializálása. Nem korlátozódik csak a Service Fabric programozási modelleken keresztül írt végrehajtható fájlokra. Az npm.exe például szüksége van néhány olyan környezeti változóra, amely et konfigurált egy node.js alkalmazás telepítéséhez.
* Hozzáférés-vezérlés beállítása biztonsági tanúsítványok telepítésével.

A **SetupEntryPoint**konfigurálásáról további információt a [Szolgáltatás beállítási belépési pontjához szükséges házirend konfigurálása című](service-fabric-application-runas-security.md) témakörben talál.

<a id="Package-App"></a>

## <a name="configure"></a>Konfigurálás

### <a name="build-a-package-by-using-visual-studio"></a>Csomag készítése a Visual Studio használatával

Ha a Visual Studio segítségével hozhatja létre az alkalmazást, a *Csomag paranccsal* automatikusan létrehozhat egy, a fent leírt elrendezésnek megfelelő csomagot.

Csomag létrehozásához kattintson a jobb gombbal az alkalmazásprojektre a *Megoldáskezelőben,* és válassza a **Csomag parancsot:**

![Alkalmazás csomagolása a Visual Studio alkalmazással][vs-package-command]

Ha a csomagolás befejeződött, a csomag helyét a **Kimenet** ablakban találja. A csomagolási lépés automatikusan megtörténik, amikor telepíti vagy hibakeresés a Visual Studio.

### <a name="build-a-package-by-command-line"></a>Csomag létrehozása parancssorból

Az is lehetséges, hogy programozott csomagolás `msbuild.exe`az alkalmazás segítségével. A motorháztető alatt a Visual Studio futtatja, így a kimenet ugyanaz.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>A csomag tesztelése

A [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) paranccsal ellenőrizheti a csomagszerkezetet helyileg a PowerShellen keresztül.
Ez a parancs ellenőrzi a jegyzékfájl elemzési problémáit, és ellenőrzi az összes hivatkozást. Ez a parancs csak a csomagban lévő könyvtárak és fájlok szerkezeti helyességét ellenőrzi.
Nem ellenőrzi a kód vagy az adatcsomag tartalmát azon túl, hogy ellenőrzi, hogy minden szükséges fájl jelen van-e.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Ez a hiba azt mutatja, hogy a service **manifest-ben** hivatkozott *MySetup.bat* fájl hiányzik a kódcsomagból. A hiányzó fájl hozzáadása után az alkalmazás ellenőrzése megfelel:

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

Ha az alkalmazás [alkalmazásparaméterek et](service-fabric-manage-multiple-environment-app-configuration.md) definiált, átadhatja azokat a [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) megfelelő érvényesítéshez.

Ha ismeri azt a fürtöt, amelyben az alkalmazás telepítve `ImageStoreConnectionString` lesz, ajánlott átadni a paramétert. Ebben az esetben a csomag is érvényesítve van az alkalmazás korábbi verzióival, amelyek már futnak a fürtben. Az ellenőrzés például észleli, hogy egy azonos verziójú, de más tartalommal rendelkező csomag már telepítve van-e.  

Miután az alkalmazás megfelelően van csomagolva, és átmegy az ellenőrzésen, fontolja meg a csomag tömörítését a gyorsabb telepítési műveletek érdekében.

## <a name="compress-a-package"></a>Csomag tömörítése

Ha egy csomag nagy méretű vagy sok fájlt tartalmaz, tömörítheti a gyorsabb telepítés érdekében. A tömörítés csökkenti a fájlok számát és a csomag méretét.
Tömörített alkalmazáscsomag esetén [az alkalmazáscsomag feltöltése](service-fabric-deploy-remove-applications.md#upload-the-application-package) hosszabb időt vehet igénybe a tömörítetlen csomag feltöltéséhez képest, különösen akkor, ha a tömörítés a másolat részeként történik. A [tömörítés, regisztráció](service-fabric-deploy-remove-applications.md#register-the-application-package) és [un-regisztráció az alkalmazás típusát](service-fabric-deploy-remove-applications.md#unregister-an-application-type) gyorsabb.

A telepítési mechanizmus megegyezik a tömörített és tömörítetlen csomagok. Ha a csomag tömörített, a fürtlemezkép-tárolóban is tárolódik, és az alkalmazás futtatása előtt tömörítetlen lesz a csomóponton.
A tömörítés lecseréli az érvényes Service Fabric csomagot a tömörített verzióra. A mappának engedélyeznie kell az írási engedélyeket. Ha egy már tömörített csomagon futtatja a tömörítést, az nem eredményez változásokat.

A csomagok tömörítéséhez futathat a Powershell parancs `CompressPackage` [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) kapcsolóval. A csomag kibontása ugyanazzal a `UncompressPackage` paranccsal, switch használatával.

A következő parancs tömöríti a csomagot anélkül, hogy a lemezképtárolóba másolna. A tömörített csomagot szükség szerint egy vagy több Service Fabric-fürtre másolhatja `SkipCopy` a [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) használatával a jelző nélkül.
A csomag most már tartalmazza `code` `config`a `data` , és csomagok tömörített fájljait. Az alkalmazásjegyzék és a szolgáltatásjegyzékek nincsenek tömörítve, mert sok belső művelethez szükségesek. Például a csomagmegosztás, az alkalmazás típusneve és a verziókinyerés bizonyos ellenőrzések hez mind szükség van a jegyzékek eléréséhez. A jegyzékek felcipzárazása nem lenne hatékony.

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

Azt is megteheti, tömöríti és másolja a csomagot [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) egy lépésben.
Ha a csomag nagy, adjon meg elég nagy időoutot ahhoz, hogy időt biztosítson mind a csomagtömörítés, mind a fürtbe való feltöltés számára.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Belsőleg Service Fabric számítási ellenőrzőösszegeket az alkalmazáscsomagok érvényesítésre. Tömörítés esetén az ellenőrzőösszegeket az egyes csomagok tömörített verziói alapján számítja ki a függvény. Új zip létrehozása ugyanabból az alkalmazáscsomagból különböző ellenőrzőösszegeket hoz létre. Az érvényesítési hibák elkerülése érdekében használja a [diff provisioning (diff provisioning) használatát.](service-fabric-application-upgrade-advanced.md) Ezzel a beállítással ne adja meg a változatlan csomagokat az új verzióban. Ehelyett hivatkozzon rájuk közvetlenül az új szolgáltatásjegyzékből.

Ha a diff-kiépítés nem választható, és meg kell adnia `config`a `data` csomagokat, hozzon létre új verziókat a `code`számára, és a csomagokat, hogy elkerülje az ellenőrzőösszeg eltérését. A változatlan csomagokhoz új verziók létrehozására van szükség, ha tömörített csomagot használ, függetlenül attól, hogy az előző verzió tömörítést használ-e vagy sem.

A csomag most már megfelelően van csomagolva, érvényesítve és tömörítve (ha szükséges), így készen áll egy vagy több Service Fabric-fürtre [való telepítésre.](service-fabric-deploy-remove-applications.md)

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Csomagok tömörítése a Visual Studio használatával történő telepítés során

Utasíthatja a Visual Studio-t, hogy a `CopyPackageParameters` telepítéskor tömörítse a `CompressPackage` csomagokat, ha hozzáadja az elemet a közzétételi profilhoz, és állítsa az attribútumot a beállításra. `true`

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Hozzon létre egy sfpkg

A 6.1-es verziótól kezdve a Service Fabric lehetővé teszi a külső áruházból történő kiépítést.
Ezzel a beállítással az alkalmazáscsomagot nem kell másolni a lemezképtárolóba. Ehelyett létrehozhat egy `sfpkg` és feltöltheti egy külső tárolóba, majd a letöltési URI-t a Service Fabric-nek a kiépítéskor. Ugyanaz a csomag több fürthöz is kiépíthető. A külső tárolóból történő kiépítés időt takarít meg a csomag egyes fürtökbe történő másolásához.

A `sfpkg` fájl egy zip, amely tartalmazza a kezdeti alkalmazás csomagot, és a kiterjesztés ".sfpkg".
A zip belsejében az alkalmazáscsomag tömöríthető vagy tömörítetlen. A tömörítés az alkalmazás csomag belsejében zip történik kód, konfigurációs, és az adatcsomag szintje, mint [korábban említettük](service-fabric-package-apps.md#compress-a-package).

A `sfpkg`létrehozásához kezdje az eredeti alkalmazáscsomagot tartalmazó, tömörített vagy nem tömörített mappával. Ezután használjon olyan segédprogramot, amely a ".sfpkg kiterjesztéssel" lévő mappát zip-eli. Használja például a [ZipFile.CreateFromDirectory fájlt.](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx)

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

A `sfpkg` kell feltölteni a külső áruház sávon kívül, a Service Fabric kívül. A külső tároló bármely olyan tároló lehet, amely egy REST http vagy https végpontot tesz elérhetővé. A kiépítés során a Service Fabric egy `sfpkg` GET-műveletet hajt végre az alkalmazáscsomag letöltéséhez, így az üzletnek lehetővé kell tennie a READ-hozzáférést a csomagszámára.

A csomag kiépítéséhez használjon külső kiépítést, amelyhez a letöltési URI és az alkalmazástípus adatai szükségesek.

>[!NOTE]
> A lemezképtár relatív elérési útja alapján `sfpkg` történő kiépítés jelenleg nem támogatja a fájlokat. Ezért a `sfpkg` nem másolható a képtárba.

## <a name="next-steps"></a>További lépések

[Alkalmazások telepítése és eltávolítása][10] azt ismerteti, hogy a PowerShell hogyan kezelheti az alkalmazáspéldányokat

[Az alkalmazásparaméterek kezelése több környezetben][11] azt ismerteti, hogyan konfigurálható paraméterek és környezeti változók a különböző alkalmazáspéldányokhoz.

[Az alkalmazás biztonsági házirendjeinek konfigurálása][12] azt ismerteti, hogy miként futtathatók a szolgáltatások a biztonsági házirendek alatt a hozzáférés korlátozása érdekében.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md