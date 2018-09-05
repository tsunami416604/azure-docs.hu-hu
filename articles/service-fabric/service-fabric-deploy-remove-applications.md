---
title: Az Azure Service Fabric-alkalmazás központi telepítésének |} A Microsoft Docs
description: Hogyan telepíthet és távolíthat el alkalmazásokat a Service Fabric PowerShell-lel.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: d38ec87fb634e1809959b85f0382935e8a78bf3b
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697164"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Üzembe helyezése és távolíthat el alkalmazásokat a PowerShell használatával
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric parancssori felület](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient API-k](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Miután egy [alkalmazástípus a csomagolás][10], üzembe helyezését az Azure Service Fabric-fürt használatra kész. Központi telepítés a következő három lépésből áll:

1. Az alkalmazáscsomag feltöltése a lemezképtároló.
2. Regisztrálja az alkalmazás típusát a lemezképet tároló relatív elérési út.
3. Az alkalmazás-példány létrehozása.

Miután az üzembe helyezett alkalmazás már nem szükséges, törölheti az alkalmazáspéldány és az alkalmazás típusát. Teljes mértékben az alkalmazás eltávolítása a fürtből, a következő lépésekből áll:

1. Távolítsa el (vagy törli) a futó alkalmazás példánya.
2. Törölje az alkalmazástípus regisztrációját, ha már nincs szüksége.
3. Távolítsa el az alkalmazáscsomag a képet tárból.

Ha üzembe helyezéséhez és a helyi fejlesztési fürtön futó alkalmazások hibakeresése a Visual Studio használja, az előző lépések egy PowerShell-parancsfájl segítségével automatikusan kezelése történik.  Ez a szkript megtalálható a *parancsfájlok* mappát a projekt. Ez a cikk milyen a szkript módon, hogy ugyanazokat a műveleteket, Visual Studión kívül is elvégezheti a háttérben futó biztosít. 

Alkalmazás üzembe helyezése egy másik módja, külső kiépítése használatával. Az alkalmazáscsomag lehet [, csomagolt `sfpkg` ](service-fabric-package-apps.md#create-an-sfpkg) , és egy külső tároló töltött fel. Ebben az esetben nincs szükség a lemezképtároló való feltöltéshez. Központi telepítés a következő lépéseket kell tennie:

1. Töltse fel a `sfpkg` külső tárolóhoz. A külső tároló minden olyan tároló, amely elérhetővé teszi a REST-http- vagy https-végpont lehet.
2. Regisztrálja az alkalmazás típusát, a külső letöltési URI és az alkalmazás típus adatainak használatával.
2. Az alkalmazás-példány létrehozása.

Távolítsa el az alkalmazáspéldányok karbantartása, és törölje az alkalmazástípus regisztrációját. A csomag nem másolta az image store, mert nincs ideiglenes hely karbantartása. A külső tárolóból kiépítési érhető el a Service Fabric 6.1-es verzió indítása.

>[!NOTE]
> A Visual Studio jelenleg nem támogatja a külső kiépítése.

 
## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz
Ebben a cikkben minden olyan PowerShell-parancsok futtatása, előtt minden esetben indítsa el a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) a Service Fabric-fürthöz való csatlakozáshoz. Szeretne csatlakozni a helyi fejlesztési fürtöt, futtassa a következőt:

```powershell
PS C:\>Connect-ServiceFabricCluster
```

Példák a távoli fürtön vagy az Azure Active Directoryval, X509 védett fürthöz csatlakozik tanúsítványokat, vagy a Windows Active Directory [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Az alkalmazáscsomag feltöltése
Az alkalmazáscsomag feltöltése helyezi belső Service Fabric összetevői által elérhető helyen.
Ha szeretné ellenőrizni az alkalmazáscsomagot helyileg, használja a [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) parancsmagot.

A [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) parancs feltölti az alkalmazáscsomag a fürt lemezképtárolójába.

Tegyük fel, hogy hozhat létre, és nevű alkalmazás becsomagolása *MyApplication* a Visual Studio 2015-ben. Alapértelmezés szerint az alkalmazástípus neve szerepel az ApplicationManifest.xml "MyApplicationType".  Az alkalmazáscsomagot, amely tartalmazza a szükséges alkalmazás jegyzékfájlja, szolgáltatásjegyzékek és kód/config/data csomagok, található *C:\Users\<felhasználónév\>\Documents\Visual Studio 2015\Projects\ MyApplication\MyApplication\pkg\Debug*. 

A következő parancsot az alkalmazás-csomag tartalmát listázza:

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
A kiszolgálóoldali hatása az, hogy való regisztrálásáról és megbízhatatlan regisztrálásához, az alkalmazás típusának gyorsabbak. Feltöltés ideje lassabb lehet jelenleg, különösen akkor, ha adja meg az időt a csomag tömörítése. 

Tömörítendő egy csomagot, használja ugyanazt [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) parancsot. A tömörítés hajtható végre külön feltöltés, a használatával a `SkipCopy` jelző, vagy a feltöltési művelet együtt. Műveletvégzés tömörítés alkalmazása egy tömörített csomagot a rendszer.
Bontsa ki a tömörített csomag, használja ugyanazt [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) parancsot a `UncompressPackage` váltani.

A következő parancsmagot a csomag nélkül másolja, a lemezképtároló tömöríti. A csomag most már tartalmazza a fájlok kibontását a `Code` és `Config` csomagokat. Az alkalmazás- és a szolgáltatásjegyzékek vannak nem zip, mert (ilyen például a csomag megosztási, alkalmazás kapcsolattípus neve és verziója kibontása az egyes ellenőrzések) számos belső művelethez szükség van. A jegyzékek becsomagolás biztosítja, ezek a műveletek nem elég hatékony.

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

A tömörítés nagy alkalmazáscsomagok esetén időt vesz igénybe. A legjobb eredmények érdekében egy gyors SSD meghajtót használja. A tömörítés idők és a tömörített csomag méretét is alapján eltérnek a csomag tartalmát.
Például Íme néhány csomag, amely a kezdeti és a tömörített csomag mérete, a tömörítési idő megjelenítése a tömörítés statisztikája.

|Kezdeti méret (MB)|Fájlok száma|A tömörítés idő|Tömörített méret (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Ha egy csomag tömörített, azt is feltölthetők a egy vagy több Service Fabric-fürtök igény szerint. Az üzembe helyezési mechanizmus ugyanazon a tömörített és tömörítetlen csomagokhoz. Tömörített csomagok ilyen tárolja a fürt lemezképtárolójába. A csomagok vannak tömörítetlen a csomóponton, az alkalmazás futtatása előtt.


A következő példa feltölti a csomag az image store "MyApplicationV1" nevű mappába:

```powershell
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Ha nem adja meg a *- ApplicationPackagePathInImageStore* az image store "Debug" mappájában történő másolása paramétert, az alkalmazáscsomagot.

>[!NOTE]
>**Másolás – ServiceFabricApplicationPackage** automatikusan észleli a megfelelő lemezképet tároló kapcsolati karakterláncot, ha a PowerShell-munkamenetet egy Service Fabric-fürthöz csatlakozik. A Service Fabric verziója régebbi, mint 5.6-os a **- ImageStoreConnectionString** argumentum explicit módon meg kell adni.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>A **Get-ImageStoreConnectionStringFromClusterManifest** parancsmag a Service Fabric SDK PowerShell-modul részét képező kéri le a lemezképet tároló kapcsolati karakterláncot.  Az SDK modul importálásához futtassa:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Lásd: [megismerheti a lemezképet tároló kapcsolati karakterlánc](service-fabric-image-store-connection-string.md) kiegészítő információk a lemezképtároló és lemezképet tárolja a kapcsolati karakterláncot.
>
>
>

A csomag feltöltéséhez szükséges idő több tényezőtől függően eltérő. Ezek a tényezők némelyike a csomag, a csomag mérete és a fájlméret fájlok száma. A hálózat sebességétől, a forrásgép és a Service Fabric-fürt között is hatással van a feltöltési időt. Az alapértelmezett időtúllépési érték [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 30 perc.
Az itt ismertetett tényezőktől függően előfordulhat, hogy rendelkezik az időtúllépési. Ha a csomag másolása hívásában gazdameghajtóhoz, kell megfontolnia a tömörítés idő.



## <a name="register-the-application-package"></a>Az alkalmazáscsomag regisztrálása
Az alkalmazás típusát és verzióját deklarálva az alkalmazásjegyzékben, legyen az alkalmazáscsomag regisztrációja esetén használható. A rendszer beolvassa a csomag az előző lépésben feltöltött, ellenőrzi a csomagot, feldolgozza a csomag tartalmát és a feldolgozott csomag egy belső helyére másolja.  

Futtassa a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) parancsmagot, hogy regisztrálja az alkalmazás típusát, a fürt és a központi telepítés elérhetővé:

### <a name="register-the-application-package-copied-to-image-store"></a>Az alkalmazáscsomag lemezképtárolójába másolja regisztrálása
Ha egy csomag az image store korábban másolta, a register-művelet a következő relatív elérési a lemezképtároló adja meg.

```powershell
PS C:\> Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
Register application type succeeded
```

"MyApplicationV1" az a mappa a lemezképtároló, ahol az alkalmazáscsomag megtalálható a. Az alkalmazástípus neve "MyApplicationType" és "1.0.0-s" (is található az alkalmazásjegyzékben) verziójának most már regisztrálva van a fürtben.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Az alkalmazáscsomag másolja egy külső tároló regisztrálása
Támogatja a csomag letöltésével egy külső áruházból-es verziótól kezdve a Service Fabric 6.1-es verziójának üzembe helyezhető. A letöltési URI elérési útját jelöli a [ `sfpkg` alkalmazáscsomag](service-fabric-package-apps.md#create-an-sfpkg) , ahonnan az alkalmazáscsomag HTTP vagy HTTPS protokoll segítségével lehet letölteni. A csomag már korábban fel kell a külső helyre. Az URI-t engedélyeznie kell a OLVASÁSI hozzáférést, így a Service Fabric letöltheti a fájlt. A `sfpkg` fájl ".sfpkg" kiterjesztéssel kell rendelkeznie. Az üzembe helyezési művelete tartalmaznia kell az alkalmazás típussal kapcsolatos információk, az alkalmazásjegyzékben formában.

```
PS C:\> Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

A [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) parancs hatására az eredményobjektumoknak csak azt követően a rendszer sikeresen regisztrálta az alkalmazáscsomagot. Mennyi ideig regisztrációs vesz igénybe attól függ, méretét és az alkalmazás-csomag tartalmát. Ha szükséges, a **- TimeoutSec** paraméter segítségével adjon meg egy hosszabb időkorlát (az alapértelmezett időtúllépési érték 60 másodperc).

Ha egy nagy méretű alkalmazás csomag, vagy ha időtúllépések tapasztal, használja a **– aszinkron** paraméter. A parancs visszaadja, ha a fürt elfogadja a register-parancsot. A regisztráció művelet továbbra is fennáll, igény szerint.
A [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) parancs megjeleníti az alkalmazástípus-verziók és azok regisztrációs állapotát. Ez a parancs segítségével határozható meg, hogy a regisztráció történik.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>A lemezképtároló alkalmazáscsomag eltávolítása
Ha egy csomagot a rendszer átmásolta a lemezképtároló-, távolítsa el azt az átmeneti mappából után az alkalmazás regisztrálása sikeres volt. Rendszer-erőforrásokat szabadít alkalmazáscsomagok törlése a képet tárból. A fel nem használt alkalmazáscsomagok lemezes tárolást használ fel, és alkalmazásteljesítménnyel kapcsolatos problémák vezet.

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Az alkalmazás létrehozása
Tetszőleges alkalmazástípus-verzió használatával sikeresen regisztrált egy alkalmazás példányosítható a [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) parancsmagot. Minden alkalmazás nevére kell kezdődnie az *"fabric:"* séma és az egyes alkalmazáspéldányokról egyedinek kell lennie. Az alkalmazásjegyzékben, a célalkalmazás típusa definiálva alapértelmezett szolgáltatások is jönnek létre.

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
Több alkalmazáspéldány is létrehozható egy regisztrált alkalmazástípus bármely adott verzióját. Mindegyik alkalmazáspéldány elkülönítve, az a saját munkahelyi directory és a folyamat fut.

Megtekintéséhez, amely nevű alkalmazások és szolgáltatások futnak a fürthöz, futtassa a [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) és [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) parancsmagok:

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

## <a name="remove-an-application"></a>Alkalmazás eltávolítása
Egy alkalmazáspéldány már nincs rá szükség, amikor véglegesen eltávolíthatja név használatával a [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) parancsmagot. [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) automatikusan eltávolítja az összes szolgáltatás, amely az alkalmazás is, véglegesen eltávolítja az összes szolgáltatás állapota tartozik. 

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

## <a name="unregister-an-application-type"></a>Törölje az alkalmazástípus regisztrációját
Ha egy adott verzióját az alkalmazástípus már nincs rá szükség, akkor törölje regisztrációját típus használatával a [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) parancsmagot. Az alkalmazás típusú fájlok eltávolításával a lemezképtároló által felhasznált lemezterület regisztrációjának törlése a fel nem használt alkalmazástípusok kiadások. Alkalmazástípust regisztrációjának törlése nem távolítja el az alkalmazáscsomagot, másolja a lemezkép-store ideiglenes helyre, ha használta a lemezképtároló másolás. Az alkalmazástípus regisztrációját lehet mindaddig, amíg nem találhatók alkalmazások példányosítása szemben, és nem alkalmazás függőben lévő frissítések hivatkozik rá.

Futtatás [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) a fürt jelenleg regisztrált alkalmazás típusának megtekintéséhez:

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Futtatás [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) regisztrációjának törlése egy adott alkalmazás típusa:

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Az ImageStoreConnectionString másolási-ServiceFabricApplicationPackage kéri
A Service Fabric SDK környezet már rendelkezik a megfelelő alapértelmezett beállítása. De ha szükséges, az összes parancsra vonatkozó ImageStoreConnectionString meg kell egyeznie a Service Fabric-fürt által használt érték. Az ImageStoreConnectionString találhatja meg a fürtjegyzék hitelesítő adatokat használja a [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) és a Get-ImageStoreConnectionStringFromClusterManifest parancsok:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

A **Get-ImageStoreConnectionStringFromClusterManifest** parancsmag a Service Fabric SDK PowerShell-modul részét képező kéri le a lemezképet tároló kapcsolati karakterláncot.  Az SDK modul importálásához futtassa:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Az ImageStoreConnectionString megtalálható a fürtjegyzék:

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Lásd: [megismerheti a lemezképet tároló kapcsolati karakterlánc](service-fabric-image-store-connection-string.md) kiegészítő információk a lemezképtároló és lemezképet tárolja a kapcsolati karakterláncot.

### <a name="deploy-large-application-package"></a>Nagy alkalmazáscsomag telepítése
Probléma: [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) túllépi az időkorlátot egy nagy alkalmazáscsomag (GB-os sorrendben).
Próbálja ki:
- Adja meg a próbáljon nagyobb időtúllépést [másolási-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) parancsot, `TimeoutSec` paraméter. Alapértelmezés szerint az időtúllépési érték 30 perc.
- Ellenőrizze a hálózati kapcsolat a forrásgép és a fürt között. Ha a kapcsolat lassú, fontolja meg egy jobb hálózati kapcsolattal rendelkező gép használatával.
Ha az ügyfélszámítógépen, mint a fürt egy másik régióban található, fontolja meg egy ügyfélszámítógépre egy közelebb vagy ugyanabban a régióban a fürttel.
- Ellenőrizze, hogy elérte-e külső szabályozás. Ha például az image store az azure storage használatára van konfigurálva, amikor feltöltése előfordulhat, hogy folyamatban.

Probléma: Feltöltés csomag sikeresen befejeződött, de [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) túllépi az időkorlátot. Próbálja ki:
- [A csomag tömörítése](service-fabric-package-apps.md#compress-a-package) a lemezképtároló másolás előtt.
A tömörítés csökkenti a méretét, és végezze el a fájlok, számát, ami viszont forgalom mennyisége csökkenti, és működik, hogy a Service Fabric. Lehet, hogy a feltöltési művelet lassabb (különösen ha adja meg a tömörítés idő), de gyorsabb regisztrálása és az alkalmazástípus regisztrációját.
- Adja meg a próbáljon nagyobb időtúllépést [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) a `TimeoutSec` paraméter.
- Adja meg `Async` a Váltás [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). A parancs visszaadja, ha a fürt elfogadja a parancsot, és az alkalmazástípus regisztrációja folyamatosan aszinkron módon történik. Ezen okból kifolyólag nem magasabb időkorlát megadásához ebben az esetben nincs szükség. A [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) parancs megjeleníti az összes sikeresen regisztrált alkalmazástípus-verziók és azok regisztrációs állapotát. Ez a parancs segítségével határozható meg, hogy a regisztráció történik.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Sok fájlt az alkalmazáscsomag telepítése
Probléma: [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) túllépi az időkorlátot egy alkalmazáscsomag fájllal (több ezer sorrendben).
Próbálja ki:
- [A csomag tömörítése](service-fabric-package-apps.md#compress-a-package) a lemezképtároló másolás előtt. A tömörítés csökkenti a fájlok száma.
- Adja meg a próbáljon nagyobb időtúllépést [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) a `TimeoutSec` paraméter.
- Adja meg `Async` a Váltás [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). A parancs visszaadja, ha a fürt elfogadja a parancsot, és az alkalmazástípus regisztrációja folyamatosan aszinkron módon történik.
Ezen okból kifolyólag nem magasabb időkorlát megadásához ebben az esetben nincs szükség. A [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) parancs megjeleníti az összes sikeresen regisztrált alkalmazástípus-verziók és azok regisztrációs állapotát. Ez a parancs segítségével határozható meg, hogy a regisztráció történik.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>További lépések
[Alkalmazás becsomagolása](service-fabric-package-apps.md)

[Service Fabric-alkalmazás frissítése](service-fabric-application-upgrade.md)

[A Service Fabric health bemutatása](service-fabric-health-introduction.md)

[Diagnosztika és hibaelhárítás a Service Fabric-szolgáltatás](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[A Service Fabric-alkalmazás minta](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
