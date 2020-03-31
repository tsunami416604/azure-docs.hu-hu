---
title: Az Azure Service Fabric üzembe helyezése a PowerShell használatával
description: Ismerje meg az alkalmazások eltávolítását és üzembe helyezését az Azure Service Fabricben, és hogyan hajthatja végre ezeket a műveleteket a Powershellben.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: e3fdd194f2949f1246e991968e02b3278f33f7db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282509"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Alkalmazások telepítése és eltávolítása a PowerShell használatával

> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [Powershell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric parancssori felület](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient API-k](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Egy [alkalmazástípus csomagolása][10]után készen áll az Azure Service Fabric-fürtbe való üzembe helyezésre. A telepítés a következő három lépést foglalja magában:

1. Töltse fel az alkalmazáscsomagot a képtárba.
2. Regisztrálja az alkalmazástípust a képtár relatív elérési útjával.
3. Hozza létre az alkalmazáspéldányt.

Ha a telepített alkalmazás már nincs szükség, törölheti az alkalmazáspéldányt és annak alkalmazástípusát. Ahhoz, hogy teljesen távolítsa el az alkalmazást a fürtből a következő lépésekkel:

1. Távolítsa el (vagy törölje) a futó alkalmazáspéldányt.
2. Törölje az alkalmazástípus regisztrációját, ha már nincs rá szüksége.
3. Távolítsa el az alkalmazáscsomagot a lemezképtárolóból.

Ha a Visual Studio segítségével telepíti és hibakeresés alkalmazások a helyi fejlesztési fürtön, az összes előző lépés automatikusan kezeli egy PowerShell-parancsfájlon keresztül.  Ez a parancsfájl az alkalmazásprojekt *Parancsfájlok* mappájában található. Ez a cikk háttérképet nyújt arról, hogy mit csinál a parancsfájl, így ugyanazokat a műveleteket a Visual Studio-n kívül is elvégezheti. 

Egy alkalmazás központi telepítésének másik módja a külső szolgáltatás használata. Az alkalmazáscsomag [ `sfpkg` becsomagolható,](service-fabric-package-apps.md#create-an-sfpkg) és feltölthető egy külső tárolóba. Ebben az esetben a képtárba való feltöltés nem szükséges. A központi telepítésnek a következő lépésekre van szüksége:

1. Töltse `sfpkg` fel egy külső áruházba. A külső tároló bármely olyan tároló lehet, amely egy REST http vagy https végpontot tesz elérhetővé.
2. Regisztrálja az alkalmazástípust a külső letöltési URI és az alkalmazástípus adatai alapján.
2. Hozza létre az alkalmazáspéldányt.

A karbantartáshoz távolítsa el az alkalmazáspéldányokat, és törölje az alkalmazástípus regisztrációját. Mivel a csomag nem lett átmásolva a lemezképtárolóba, nincs ideiglenes törlésre alkalmas hely. A külső tárolóból történő kiépítés a Service Fabric 6.1-es verziójától kezdve érhető el.

>[!NOTE]
> A Visual Studio jelenleg nem támogatja a külső ellátást.

 

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Mielőtt futtatja a powershell-parancsokebben a cikkben, mindig indítsa el a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) a Service Fabric fürthöz való csatlakozáshoz. A helyi fejlesztési fürthöz való csatlakozáshoz futtassa az alábbiakat:

```powershell
Connect-ServiceFabricCluster
```

Példák az Azure Active Directory, x509-tanúsítványok vagy Windows Active Directory használatával védett távoli fürthöz vagy fürthöz való csatlakozásra: [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md)című témakörben.

## <a name="upload-the-application-package"></a>Az alkalmazáscsomag feltöltése

Az alkalmazáscsomag feltöltése olyan helyre helyezi, amely a Service Fabric belső összetevői által elérhető.
Ha helyileg szeretné ellenőrizni az alkalmazáscsomagot, használja a [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) parancsmagját.

A [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) parancs feltölti az alkalmazáscsomagot a fürtlemezkép-tárolóba.

Tegyük fel, hogy létrehoz és csomagol egy *MyApplication* nevű alkalmazást a Visual Studio 2015-ben. Alapértelmezés szerint az ApplicationManifest.xml fájlban felsorolt alkalmazástípus neve "MyApplicationType".  A szükséges alkalmazásjegyzéket, szolgáltatásjegyzékeket és kód/konfigurációs/adatcsomagokat tartalmazó alkalmazáscsomag a *C:\Users\<\>felhasználóneve \Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*. 

A következő parancs az alkalmazáscsomag tartalmát sorolja fel:

```powershell
$path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
tree /f $path
```

```Output
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

Ha az alkalmazáscsomag nagy és/vagy sok fájlt tartalmaz, [tömörítheti azt.](service-fabric-package-apps.md#compress-a-package) A tömörítés csökkenti a fájlok méretét és számát.
A mellékhatása az, hogy az alkalmazástípus regisztrálása és regisztrációjának megszüntetése gyorsabb. A feltöltési idő jelenleg lassabb lehet, különösen akkor, ha a csomag tömörítéséhez időt is beleszámítjuk. 

Csomag tömörítéséhez használja ugyanazt a [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) parancsot. A tömörítés a feltöltéstől, `SkipCopy` a zászló használatától vagy a feltöltési művelettől elkülönítve végezhető el. A tömörítés alkalmazása tömörített csomagra nem művelet.
A tömörített csomagok kibontásához használja ugyanazt a [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) parancsot a `UncompressPackage` kapcsolóval.

A következő parancsmag tömöríti a csomagot anélkül, hogy átmásolna a lemezképtárolóba. A csomag most már tartalmaz `Code` `Config` tömörített fájlokat a és csomagok. Az alkalmazás és a szolgáltatásjegyzékek nincsenek tömörítve, mert számos belső művelethez szükségesek (például csomagmegosztáshoz, alkalmazástípus nevéhez és verziókinyeréshez bizonyos ellenőrzésekhez). A jegyzékek felcipzárazása nem lenne hatékony.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
tree /f $path
```

```Output
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

Nagy alkalmazáscsomagok esetén a tömörítés időt vesz igénybe. A legjobb eredmény érdekében használjon gyors SSD-meghajtót. A tömörítési idő és a tömörített csomag mérete is eltér a csomag tartalmától függően.
Itt például egyes csomagok tömörítési statisztikái, amelyek a kezdeti és a tömörített csomagméretet mutatják a tömörítési idővel.

|Kezdeti méret (MB)|Fájlok száma|Tömörítési idő|Tömörített csomagmérete (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

A csomag tömörítése után szükség szerint feltölthető egy vagy több Service Fabric-fürtre. A telepítési mechanizmus megegyezik a tömörített és tömörítetlen csomagok. A tömörített csomagok a fürtképtárban is tárolódnak. A csomagok tömörítetlen ülnek a csomóponton az alkalmazás futtatása előtt.


A következő példa feltölti a csomagot a lemezképtárolóba egy "MyApplicationV1" nevű mappába:

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Ha nem adja meg a *-ApplicationPackagePathInImageStore* paramétert, az alkalmazáscsomag a lemezképtároló "Debug" mappájába kerül.

>[!NOTE]
>**Copy-ServiceFabricApplicationPackage** automatikusan észleli a megfelelő lemezképtároló kapcsolati karakterláncot, ha a PowerShell-munkamenet egy Service Fabric-fürthöz csatlakozik. Az 5.6-nál régebbi Service Fabric-verziók esetén a **-ImageStoreConnectionString** argumentumot explicit módon kell megadni.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>A **Get-ImageStoreConnectionStringFromClusterManifest** parancsmag, amely a Service Fabric SDK PowerShell modul része, a rendszer a lemezképtároló kapcsolati karakterláncának leválasztására szolgál.  Az SDK-modul importálásához futtassa a következőt:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>[A lemezkép-tároló kapcsolati karakterláncának ismertetése](service-fabric-image-store-connection-string.md) című témakörben talál további információt a lemezkép-tároló és a lemezképtároló kapcsolati karakterláncáról.
>
>
>

A csomag feltöltéséhez szükséges idő több tényezőtől is függ. Ezen tényezők közül néhány a csomagban lévő fájlok száma, a csomag mérete és a fájlméretek. A forrásgép és a Service Fabric-fürt közötti hálózati sebesség is hatással van a feltöltési időre. A [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) alapértelmezett időmegfizetése 30 perc.
A leírt tényezőktől függően előfordulhat, hogy növelnie kell az időbeli elévülést. Ha a csomagot a másolási hívásban tömöríti, figyelembe kell vennie a tömörítési időt is.



## <a name="register-the-application-package"></a>A kérelemcsomag regisztrálása

Az alkalmazásjegyzékben deklarált alkalmazástípus és verzió elérhetővé válik az alkalmazáscsomag regisztrálásakor. A rendszer beolvassa az előző lépésben feltöltött csomagot, ellenőrzi a csomagot, feldolgozza a csomag tartalmát, és átmásolja a feldolgozott csomagot egy belső rendszerhelyre.  

Futtassa a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) parancsmast az alkalmazástípus fürtben történő regisztrálásához és központi telepítéshez való elérhetővé téve:

### <a name="register-the-application-package-copied-to-image-store"></a>A képtárolóba másolt alkalmazáscsomag regisztrálása

Amikor egy csomagot korábban másolt a lemezképtárolóba, a regiszterművelet megadja a lemezképtároló relatív elérési útját.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
```

```Output
Register application type succeeded
```

A "MyApplicationV1" az alkalmazástárolóban található mappa, ahol az alkalmazáscsomag található. A "MyApplicationType" és az "1.0.0" verziójú alkalmazástípus (mindkettő megtalálható az alkalmazásjegyzékben) már regisztrálva van a fürtben.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Külső tárolóba másolt alkalmazáscsomag regisztrálása

A Service Fabric 6.1-es verziójával kezdve a kiépítés támogatja a csomag külső áruházból való letöltését. A letöltési URI az [ `sfpkg` alkalmazáscsomag](service-fabric-package-apps.md#create-an-sfpkg) elérési útját jelöli, ahonnan az alkalmazáscsomag HTTP- vagy HTTPS protokollok használatával letölthető. A csomagot korábban már fel kellett tölteni erre a külső helyre. Az URI-nak lehetővé kell tennie az OLVASÁSi hozzáférést, hogy a Service Fabric letölthesse a fájlt. A `sfpkg` fájlnak a ".sfpkg" kiterjesztéssel kell rendelkeznie. A létesítési műveletnek tartalmaznia kell az alkalmazástípusadatait, ahogy az alkalmazásjegyzékben található.

```powershell
Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

A [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) parancs csak akkor ad vissza, ha a rendszer sikeresen regisztrálta az alkalmazáscsomagot. A regisztráció ideje az alkalmazáscsomag méretétől és tartalmától függ. Szükség esetén a **-TimeoutSec** paraméter hosszabb időtúltöltés (az alapértelmezett időtúlérték 60 másodperc) adható meg.

Ha nagy alkalmazáscsomaggal rendelkezik, vagy időkihatolását tapasztalja, használja az **-Async paramétert.** A parancs akkor ad vissza, amikor a fürt elfogadja a regiszter parancsot. A regiszterművelet szükség szerint folytatódik.
A [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) parancs felsorolja az alkalmazástípus-verziókat és azok regisztrációs állapotát. Ezzel a paranccsal meghatározhatja, hogy mikor történik a regisztráció.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Alkalmazáscsomag eltávolítása a lemezképtárolóból

Ha egy csomagot a lemezképtárolóba másoltak, az alkalmazás sikeres regisztrálása után el kell távolítania az ideiglenes helyről. Az alkalmazáscsomagok lemezképtárolóból történő törlése rendszererőforrásokat szabadít fel. A nem használt alkalmazáscsomagok megtartása lemezes tárolást eredményez, és az alkalmazás teljesítményével kapcsolatos problémákhoz vezet.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Az alkalmazás létrehozása

A [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) parancsmag használatával bármely alkalmazástípus-verzióból létrehozhat egy alkalmazást, amely sikeresen regisztrálva van. Az egyes alkalmazások nevének a *"fabric:"* sémával kell kezdődnie, és minden egyes alkalmazáspéldányhoz egyedinek kell lennie. Az alkalmazásjegyzékben a célalkalmazás típusának definiált alapértelmezett szolgáltatások is létrejönnek.

```powershell
New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```

A regisztrált alkalmazástípus bármely verziójához több alkalmazáspéldány is létrehozható. Minden alkalmazáspéldány elszigetelten fut, saját munkakönyvtárral és folyamattal.

A Get-ServiceFabricApplication és a [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) parancsmagjainak megtekintéséhez futtassa a [get-servicefabricapplication](/powershell/module/servicefabric/get-servicefabricapplication) és a Get-ServiceService parancsmagjait:

```powershell
Get-ServiceFabricApplication  
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}
```

```powershell
Get-ServiceFabricApplication | Get-ServiceFabricService
```

```Output
ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Alkalmazás eltávolítása

Ha egy alkalmazáspéldányra már nincs szükség, véglegesen eltávolíthatja név szerint az [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) parancsmag használatával. [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) automatikusan eltávolítja az összes szolgáltatást, amely az alkalmazáshoz tartozik is, véglegesen eltávolítja az összes szolgáltatás állapotát. 

> [!WARNING]
> Ez a művelet nem vonható vissza, és az alkalmazás állapota nem állítható helyre.

```powershell
Remove-ServiceFabricApplication fabric:/MyApp
```

```Output
Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded
```

```powershell
Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Alkalmazástípus regisztrációjának megszüntetése

Ha egy alkalmazástípus egy adott verziójára már nincs szükség, törölje az alkalmazástípus regisztrációját az [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) parancsmag használatával. A nem használt alkalmazástípusok regisztrációjának megszüntetése az alkalmazástípus-fájlok eltávolításával szabadít fel a lemezképtároló által használt tárhelyet. Az alkalmazástípus regisztrációjának megszüntetése nem távolítja el a lemezképtároló ideiglenes helyére másolt alkalmazáscsomagot, ha a lemezképtárolóba másolást használt. Az alkalmazástípus nem regisztrálható mindaddig, amíg egyetlen alkalmazás sincs hozzárendelve, és nincs függőben lévő alkalmazásfrissítés, amely hivatkozna rá.

Futtassa a [Get-ServiceFabricApplicationType alkalmazást](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) a fürtben jelenleg regisztrált alkalmazástípusok megtekintéséhez:

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Adott alkalmazástípus regisztrációjának megszüntetése érdekében futtassa [a Unregister-ServiceFabricApplicationType programot:](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps)

```powershell
Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage kér egy ImageStoreConnectionString

A Service Fabric SDK-környezetben már be kell állítani a megfelelő alapértelmezett beállításokat. De ha szükséges, az ImageStoreConnectionString az összes parancs meg kell egyeznie az értéket, amely a Service Fabric-fürt használ. Az ImageStoreConnectionString a fürtjegyzékben található, a [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) és a Get-ImageStoreConnectionStringFromClusterManifest parancsokkal beolvasva:

```powershell
Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

A **Get-ImageStoreConnectionStringFromClusterManifest** parancsmag, amely a Service Fabric SDK PowerShell modul része, a rendszer a lemezképtároló kapcsolati karakterláncának leválasztására szolgál.  Az SDK-modul importálásához futtassa a következőt:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Az ImageStoreConnectionString a fürtjegyzékben található:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

[A lemezkép-tároló kapcsolati karakterláncának ismertetése](service-fabric-image-store-connection-string.md) című témakörben talál további információt a lemezkép-tároló és a lemezképtároló kapcsolati karakterláncáról.

### <a name="deploy-large-application-package"></a>Nagy alkalmazáscsomag telepítése

Probléma: [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) idővel ki egy nagy alkalmazáscsomag (gb sorrendben).
Próbálja:
- Adjon meg egy nagyobb időmeghosszabbítást a `TimeoutSec` [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) parancshoz paraméterrel. Alapértelmezés szerint az időhosszabbítás 30 perc.
- Ellenőrizze a hálózati kapcsolatot a forrásgép és a fürt között. Ha a kapcsolat lassú, fontolja meg egy jobb hálózati kapcsolattal rendelkező gép használatát.
Ha az ügyfélgép a fürttől eltérő régióban található, fontolja meg egy ügyfélgép használatát a fürttel egy közelebbi vagy azonos régióban.
- Ellenőrizze, hogy külső fojtás. Ha például a lemezképtároló azure storage használatára van konfigurálva, a feltöltés szabályozható.

Probléma: A csomag feltöltése sikeresen befejeződött, de [a Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) idővel kijelentkezik. Próbálja:
- [A csomag tömörítése](service-fabric-package-apps.md#compress-a-package) a képtárolóba való másolás előtt.
A tömörítés csökkenti a fájlok méretét és számát, ami viszont csökkenti a szolgáltatásháló által végrehajtandó forgalom és munka mennyiségét. A feltöltési művelet lehet lassabb (különösen, ha tartalmazza a tömörítési idő), de regisztrálja, és un-regisztrálja az alkalmazás típusát gyorsabb.
- Adjon meg egy nagyobb időoutot a `TimeoutSec` [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) paraméterrel.
- Adja `Async` meg a Kapcsolót a [Register-ServiceFabricApplicationType alkalmazáshoz.](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) A parancs akkor ad vissza, ha a fürt elfogadja a parancsot, és az alkalmazástípus regisztrációja aszinkron módon folytatódik. Emiatt ebben az esetben nincs szükség nagyobb időtúltöltés megadására. A [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) parancs felsorolja az összes sikeresen regisztrált alkalmazástípus-verziót és azok regisztrációs állapotát. Ezzel a paranccsal meghatározhatja, hogy mikor történik a regisztráció.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Alkalmazáscsomag telepítése sok fájllal

Probléma: [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) idővel ki egy alkalmazáscsomag sok fájlt (több ezer sorrendben).
Próbálja:
- [A csomag tömörítése](service-fabric-package-apps.md#compress-a-package) a képtárolóba való másolás előtt. A tömörítés csökkenti a fájlok számát.
- Adjon meg egy nagyobb időoutot a `TimeoutSec` [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) paraméterrel.
- Adja `Async` meg a Kapcsolót a [Register-ServiceFabricApplicationType alkalmazáshoz.](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) A parancs akkor ad vissza, ha a fürt elfogadja a parancsot, és az alkalmazástípus regisztrációja aszinkron módon folytatódik.
Emiatt ebben az esetben nincs szükség nagyobb időtúltöltés megadására. A [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) parancs felsorolja az összes sikeresen regisztrált alkalmazástípus-verziót és azok regisztrációs állapotát. Ezzel a paranccsal meghatározhatja, hogy mikor történik a regisztráció.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>További lépések

[Alkalmazás becsomagolása](service-fabric-package-apps.md)

[A Service Fabric alkalmazásfrissítése](service-fabric-application-upgrade.md)

[A Service Fabric egészségügyi bevezetése](service-fabric-health-introduction.md)

[Service Fabric-szolgáltatás diagnosztizálása és hibaelhárítása](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Alkalmazás modellezése a Service Fabricben](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md