---
title: "Az Azure Service Fabric-alkalmazás központi telepítése |} Microsoft Docs"
description: "Hogyan telepítheti és távolíthat el alkalmazásokat a Service Fabric PowerShell-lel."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/05/2017
ms.author: ryanwi
ms.openlocfilehash: 6d0f85a839171c43d226741f54e0dc954b85601d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Központi telepítése, és távolítsa el az alkalmazásokat a PowerShell használatával
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> * [FabricClient API-k](service-fabric-deploy-remove-applications-fabricclient.md)
> * [Service Fabric parancssori felület](service-fabric-application-lifecycle-sfctl.md)

<br/>

Egyszer egy [alkalmazástípus csomagolás][10], az Azure Service Fabric-fürt szolgáltatássablonjaikat használatra kész. Központi telepítés a következő három lépést foglal magában:

1. Az image store az alkalmazáscsomag feltöltése
2. Az alkalmazástípus regisztrálása
3. Az alkalmazáspéldány létrehozása

Miután egy alkalmazás lett telepítve, és egy példányát a fürtben fut, törölheti az alkalmazáspéldány és az alkalmazás típusa. Az alkalmazás teljes eltávolítása a fürt a következő lépésekből áll:

1. Távolítsa el (vagy törlése) futó alkalmazáspéldány
2. Az alkalmazástípus regisztrációjának törlése, ha már nincs szüksége
3. Az alkalmazáscsomag eltávolítása a lemezképtárolóból

Ha [központi telepítéséhez és alkalmazások hibakeresése a Visual Studio](service-fabric-publish-app-remote-cluster.md) meg a helyi fejlesztési fürtöt, az előző lépések kezeli automatikusan egy PowerShell-parancsfájl segítségével.  Ez a parancsfájl megtalálható a *parancsfájlok* mappában található a projektet. Ez a cikk nyújt háttér milyen, hogy a parancsfájl módon, hogy a Visual Studio kívül ugyanazokat a műveleteket végezheti el. 
 
## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz
Ebben a cikkben a PowerShell-parancsok futtatása, előtt mindig használatával indítsa el [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) csatlakozni a Service Fabric-fürt. Szeretne csatlakozni a helyi fejlesztési fürtöt, futtassa az alábbi parancsot:

```powershell
PS C:\>Connect-ServiceFabricCluster
```

Csatlakozás távoli fürt vagy az Azure Active Directoryval, X509 védett fürt példákat tanúsítványokat, vagy a Windows Active Directory [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Az alkalmazáscsomag feltöltése
Az alkalmazáscsomag feltöltése helyezi belső Service Fabric összetevői által elérhető helyen.
Ha szeretné ellenőrizni a alkalmazáscsomagot helyileg, használja a [teszt-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) parancsmag.

A [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) parancs feltölti az alkalmazáscsomagot a fürt lemezképtárolóhoz.

Tegyük fel, hogy most felépíti és nevű csomag *MyApplication* a Visual Studio 2015-öt. Alapértelmezés szerint az alkalmazás szerepel a ApplicationManifest.xml típusneve "MyApplicationType".  Az alkalmazáscsomag, amely tartalmazza a szükséges alkalmazás jegyzékfájlja, a szolgáltatás jegyzékfájlban és a kód/config/adatok csomagok, található *C:\Users\<felhasználónév\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*. 

A következő parancs megjeleníti az alkalmazáscsomag tartalmának:

```powershell
PS C:\> $path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

Ha az alkalmazáscsomag nagy és/vagy sok fájl van, akkor [a tömörítés](service-fabric-package-apps.md#compress-a-package). A tömörítés csökkenti a méretét és a fájlok száma.
A mellékhatása, amely regisztrálásakor és típustár regisztrációjának megszüntetése az alkalmazástípus gyorsabbak. Ideje csökkenhet jelenleg, különösen akkor, ha adja meg az időt a csomag tömörítése. 

Csomag tömörítése, használja a azonos [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) parancsot. Tömörítés hajtható végre külön feltöltés, a használatával a `SkipCopy` jelzőt, vagy a feltöltési művelet együtt. A tömörített csomag tömörítés alkalmazása műveletvégzés.
Bontsa ki a tömörített csomag, használja a azonos [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) parancsot a `UncompressPackage` váltani.

A következő parancsmagot a csomag nélkül másolja az image store tömöríti. A csomag most már tartalmazza a fájlok kibontását a `Code` és `Config` csomagok. Az alkalmazás és a szolgáltatás jegyzékfájlokat vannak nem zip, mert szükség van a sok belső műveletekhez (például a csomag megosztási, alkalmazás neve és verziója kapcsolattípus kibontása az egyes ellenőrzések). A jegyzékfájlokban tömörítés teszi ezeket a műveleteket nem hatékony.

```
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

A tömörítés nagy alkalmazáscsomagok esetén időt vesz igénybe. A legjobb eredmények elérése érdekében a gyors SSD meghajtó használatát. A tömörítési ideje és a tömörített csomag is attól függően változnak, a csomag tartalmát.
Például ez tömörítési statisztika egyes csomagok, amely a kezdeti és a tömörített csomag mérete, a tömörítési időponttal megjelenítése.

|Kezdeti méret (MB)|Fájlok száma|Tömörítés idő|Tömörített mérete (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Ha egy csomag tömörített, akkor is feltölthetők a egy vagy több Service Fabric-fürtök igény szerint. A központi telepítési módszer használata ugyanabban a tömörített és tömörítetlen csomagokat. Ha a csomag tömörített, a fürt lemezképtárolóhoz ilyen van tárolva, és azt van tömörítetlen a csomóponton, az alkalmazás futtatása előtt.


Az alábbi példa feltölti a csomag az image store "MyApplicationV1" nevű mappába:

```powershell
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Ha nem adja meg a *- ApplicationPackagePathInImageStore* paraméter, a alkalmazáscsomag másolódik a az image store "Debug" mappájába.

>[!NOTE]
>**Másolás-ServiceFabricApplicationPackage** automatikusan észleli a megfelelő lemezképet tároló kapcsolati karakterláncot, ha a PowerShell-munkamenetet a Service Fabric-fürt csatlakozik. A Service Fabric-verziók 5.6 a **- előtaggal** argumentum explicit módon meg kell adni.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>A **Get-ImageStoreConnectionStringFromClusterManifest** parancsmagot, amely a Service Fabric SDK PowerShell modulja részét képezi, a lemezkép tárolási kapcsolati karakterlánc beolvasása szolgál.  Az SDK modul importálásához futtassa:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Lásd: [megérteni a lemezkép tárolási kapcsolati karakterlánc](service-fabric-image-store-connection-string.md) az image store és a lemezkép kiegészítő információt tárolja a kapcsolati karakterlánc.
>
>
>

A csomag feltöltéséhez szükséges eltér attól függően, hogy több tényezővel. Ezek a tényezők a csomag, a csomag mérete, és a fájl a fájlok száma. A hálózat sebességétől, a forráskiszolgáló és a Service Fabric-fürt között is hatással van a ideje. Az alapértelmezett időkorlátjának [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 30 perc.
Az ismertetett tényezőktől függően előfordulhat, hogy az időtúllépési érték növeléséhez. Ha a csomag másolása hívásában gazdameghajtóhoz, fontolja meg a tömörítési idő is szeretné.



## <a name="register-the-application-package"></a>Az alkalmazáscsomag regisztrálása
Az alkalmazástípus és -verzió az alkalmazás jegyzékében használható válnak, amikor regisztrál az alkalmazáscsomag deklarálva. A rendszer beolvassa az előző lépésben feltöltött csomag, ellenőrzi a csomag, feldolgozza a csomag tartalmát, és a feldolgozott csomag belső rendszer helyre másolja.  

Futtassa a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) parancsmag az alkalmazástípus regisztrálása a fürtben, és lehetővé teszi az üzembe:

```powershell
PS C:\> Register-ServiceFabricApplicationType MyApplicationV1
Register application type succeeded
```

"MyApplicationV1" az image store, ahol az alkalmazáscsomag-e a mappát. Az alkalmazástípus neve "MyApplicationType" és "1.0.0" (egyaránt megtalálható az alkalmazás jegyzékében) verziója most már regisztrálva van a fürtben.

A [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) parancs csak azt követően a rendszer sikeresen regisztrálta az alkalmazáscsomag adja vissza. Mennyi ideig regisztrációs vesz méretét és az alkalmazáscsomag tartalmának függ. Ha szükséges, a **- TimeoutSec** paraméter segítségével adjon meg egy hosszabb időtúllépési érték (az alapértelmezett időtúllépési érték 60 másodperc).

Ha egy nagy alkalmazás csomagot, vagy ha időtúllépéseket tapasztalnak, használja a **- aszinkron** paraméter. A parancs ad vissza, ha a fürt elfogadja a register parancsot, és a feldolgozási továbbra is fennáll, igény szerint.
A [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) parancs megjeleníti az összes sikeresen regisztrált alkalmazástípus-verziók és a regisztrációs állapotot. Ez a parancs segítségével határozható meg, hogy a regisztrációs történik.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Az alkalmazáscsomag eltávolítása a lemezképtárolóból
Ajánlott az alkalmazáscsomag eltávolítása után az alkalmazás regisztrálása sikeres volt.  Rendszererőforrások szabaddá alkalmazáscsomagok az image store való törlésekor.  Nem használt alkalmazáscsomagok használ fel a lemezes tárolás és alkalmazást teljesítményproblémák vezet.

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Az alkalmazás létrehozása
Az alkalmazás minden alkalmazástípus verziója segítségével sikeresen regisztrált a példányosítható a [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) parancsmag. Minden alkalmazás nevére kell kezdődnie az *"fabric:"* sémáját, és minden alkalmazáspéldányhoz egyedinek kell lennie. A célalkalmazás típusa alkalmazás jegyzékében definiált alapértelmezett szolgáltatások is jönnek létre.

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
Több alkalmazáspéldányt is létrehozható egy regisztrált alkalmazástípus bármely adott verzióját. Minden egyes alkalmazáspéldány feladata elkülönítve, a saját munkahelyi directory és a folyamat.

Meg amely az alkalmazások és szolgáltatások futnak a fürthöz, futtassa a [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) és [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) parancsmagokat:

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Alkalmazások eltávolítása
Az alkalmazáspéldány nincs szükség, ha véglegesen eltávolíthatja azt név használatával a [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) parancsmag. [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) automatikusan eltávolítja az is, végleges eltávolítása az összes szolgáltatás állapota az alkalmazáshoz tartozó összes szolgáltatás. 

> [!WARNING]
> Ez a művelet nem vonható vissza, és az alkalmazás állapota nem állítható helyre.

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Az alkalmazástípus regisztrációjának törlése
Amikor egy alkalmazás típus adott verziójának már nem szükséges, meg kell regisztrációját használt alkalmazások típusa a [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) parancsmag. Regisztrációjának megszüntetése nem használt alkalmazás típusok kiadásokban tárolóhely bináris alkalmazásfájlokat eltávolításával az image store használják. Az alkalmazástípus regisztrációjának törlése nem távolítja el az alkalmazáscsomagot. Az alkalmazástípus regisztrációjának törlése lehet, mindaddig, amíg nincs alkalmazások létrehozásának rajta, és nem alkalmazás függőben lévő frissítések hivatkozik rá.

Futtatás [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) a fürt jelenleg regisztrált alkalmazás típusának:

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Futtatás [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) egy adott alkalmazás típusa beállításjegyzékből való törlése:

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Másolás-ServiceFabricApplicationPackage kér egy előtaggal
A Service Fabric SDK környezet már rendelkezik a megfelelő alapértelmezett beállítása. De ha szükséges, az összes parancs előtaggal meg kell felelnie a Service Fabric fürt által használt érték. A előtaggal megtalálható a fürtjegyzékben le kérni a [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) és Get-ImageStoreConnectionStringFromClusterManifest parancsokat:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

A **Get-ImageStoreConnectionStringFromClusterManifest** parancsmagot, amely a Service Fabric SDK PowerShell modulja részét képezi, a lemezkép tárolási kapcsolati karakterlánc beolvasása szolgál.  Az SDK modul importálásához futtassa:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

A előtaggal megtalálható a fürtjegyzékben:

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Lásd: [megérteni a lemezkép tárolási kapcsolati karakterlánc](service-fabric-image-store-connection-string.md) az image store és a lemezkép kiegészítő információt tárolja a kapcsolati karakterlánc.

### <a name="deploy-large-application-package"></a>Nagy alkalmazáscsomag telepítése
Probléma: [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) túllépi az időkorlátot a nagy alkalmazáscsomagok (sorrendet, GB).
Próbálja meg:
- Adjon meg egy nagyobb időkorlátjának [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) parancsot, `TimeoutSec` paraméter. Alapértelmezés szerint az időtúllépés értéke 30 perc.
- Ellenőrizze a hálózati kapcsolat a forrásgép és fürt között. Ha a kapcsolat lassú, érdemes lehet a gépek nagyobb hálózati kapcsolattal rendelkező.
Ha az ügyfélszámítógép mint a fürt egy másik régióban van, érdemes lehet egy ügyfélszámítógépre egy szorosabb vagy ugyanabban a régióban a fürttel.
- Ellenőrizze, hogy ha hogy elérte-e külső szabályozás. Például ha az image store az azure storage használatára van konfigurálva, feltöltés előfordulhat, hogy szabályozva.

Probléma: Feltöltés csomag sikeresen befejeződött, de [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) túllépi az időkorlátot. Próbálja meg:
- [A csomag tömörítése](service-fabric-package-apps.md#compress-a-package) az image store másolás előtt.
A tömörítés csökkenti a, és a fájlok, számát, amely pedig csökkenti a forgalom mennyiségét, és működnek, hogy a Service Fabric kell elvégezni. Lehet, hogy a feltöltési művelet lassabb (különösen ha a tömörítés idő), de a regisztrálása és az alkalmazástípus regisztrációjának gyorsabb.
- Adjon meg egy nagyobb időkorlátjának [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) a `TimeoutSec` paraméter.
- Adja meg `Async` átkapcsolni a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). A parancs ad vissza, ha a fürt elfogadja a parancsot, és az alkalmazástípus regisztrációjának aszinkron módon folytatódik. Emiatt nincs szükség ebben az esetben a magasabb időkorlát megadásához. A [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) parancs megjeleníti az összes sikeresen regisztrált alkalmazástípus-verziók és a regisztrációs állapotot. Ez a parancs segítségével határozható meg, hogy a regisztrációs történik.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Sok fájlokkal alkalmazáscsomag telepítése
Probléma: [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) sok fájlt (akár több ezer sorrendben) az alkalmazás csomagot időtúllépése.
Próbálja meg:
- [A csomag tömörítése](service-fabric-package-apps.md#compress-a-package) az image store másolás előtt. A tömörítés csökkenti a fájlok száma.
- Adjon meg egy nagyobb időkorlátjának [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) a `TimeoutSec` paraméter.
- Adja meg `Async` átkapcsolni a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). A parancs ad vissza, ha a fürt elfogadja a parancsot, és az alkalmazástípus regisztrációjának aszinkron módon folytatódik.
Emiatt nincs szükség ebben az esetben a magasabb időkorlát megadásához. A [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) parancs megjeleníti az összes sikeresen regisztrált alkalmazástípus-verziók és a regisztrációs állapotot. Ez a parancs segítségével határozható meg, hogy a regisztrációs történik.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Következő lépések
[A Service Fabric-alkalmazás frissítése](service-fabric-application-upgrade.md)

[A Service Fabric állapotának bemutatása](service-fabric-health-introduction.md)

[Diagnosztizálása és megoldása a Service Fabric-szolgáltatás](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[A Service Fabric alkalmazás minta](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
