---
title: Az Azure Állapotfigyelőt v2 részletes utasítások |} A Microsoft Docs
description: Részletes útmutató az Állapotfigyelőt v2 – első lépések. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. ASP.NET-webalkalmazásokat együttműködik a helyileg üzemeltetett, a virtuális gépek vagy az Azure-ban.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: d0960c749d74903acc778c0f21d5c49f380195ae
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734193"
---
# <a name="status-monitor-v2-detailed-instructions"></a>A figyelő v2 állapota: Részletes utasítások

Ez a cikk azt ismerteti, hogyan veheti a PowerShell-galéria és a letöltés a ApplicationMonitor modul.
Ismerteti a leggyakoribb paramétereket kell a kezdéshez.
Emellett olyan manuális utasításokat tartalmaz, abban az esetben, ha nem rendelkezik internet-hozzáféréssel.

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió egy szolgáltatásiszint-megállapodás nélkül biztosított, és ezt nem javasoljuk a termelési számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, és néhány előfordulhat, hogy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="get-an-instrumentation-key"></a>Kialakítási kulcs beszerzése

Első lépésként szüksége van egy kialakítási kulcsot. További információkért lásd: [hozzon létre egy Application Insights-erőforrást](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Futtassa a Powershellt rendszergazdaként egy emelt szintű végrehajtási házirend

**Futtassa rendszergazdaként**

PowerShell módosítja a számítógép rendszergazdai engedélyekre van szüksége.

**Végrehajtási házirend**
- Leírás: Alapértelmezés szerint az PowerShell-parancsfájlok futtatásakor le van tiltva. Javasoljuk, hogy a RemoteSigned parancsfájlok csak az aktuális hatókör.
- Leírások: [Végrehajtási házirendek kapcsolatos](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) és [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- A parancs: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Nem kötelező paraméter:
    - `-Force`. Figyelmen kívül hagyja a megerősítési kérést.

**Példahibák**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>PowerShell előfeltételei

Naplózási PowerShell-példányát futtatja a `$PSVersionTable` parancsot.
Ez a parancs a következő kimenetet eredményez:


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Ezek az utasítások írása és tesztelése a Windows 10-es és a fent felsorolt verziói futtató számítógépen.

## <a name="prerequisites-for-powershell-gallery"></a>PowerShell-galériából előfeltételei

Ezeket a lépéseket a PowerShell-galériából töltse le a modulok-kiszolgáló előkészítésével foglalkozunk.

> [!NOTE] 
> A Windows 10-es, a Windows Server 2016 és a PowerShell 6-os PowerShell-galériából támogatott.
> Korábbi verzióival kapcsolatos további információkért lásd: [PowerShellGet telepítése](https://docs.microsoft.com/powershell/gallery/installing-psget).


1. PowerShell futtatása rendszergazdaként az emelt szintű végrehajtási házirendjével.
2. Telepítse a NuGet csomag.
    - Leírás: Ez a szolgáltató használatával kommunikálhat a NuGet-alapú adattárak, például a PowerShell-galériából van szüksége.
    - Leírások: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - A parancs: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Választható paraméterek:
        - `-Proxy`. Itt adhatja meg a proxykiszolgáló a kérelem.
        - `-Force`. Figyelmen kívül hagyja a megerősítési kérést.
    
    Ez a kérdés fog kapni, ha a NuGet nem jött létre:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Állítsa be a PowerShell-Galériával vagy a megbízható.
    - Leírás: Alapértelmezés szerint a PowerShell-galériából a egy nem megbízható tárház.
    - Leírások: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - A parancs: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Nem kötelező paraméter:
        - `-Proxy`. Itt adhatja meg a proxykiszolgáló a kérelem.

    Ez a kérdés fog kapni, ha a PowerShell-galériában nem megbízható:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Ez a Módosítás megerősítése és a naplózási összes PSRepositories futtatásával a `Get-PSRepository` parancsot.

4. A PowerShellGet legújabb verziójának telepítéséhez.
    - Leírás: Ez a modul tartalmazza a PowerShell-galériából más modulok beszerzéséhez használt eszközök. A Windows 10 és Windows Server verzió 1.0.0.1 letöltésként érhető el. 1.6.0-s verziójának vagy újabb verziója szükséges. Annak megállapításához, hogy mely verziója van telepítve, futtassa a `Get-Command -Module PowerShellGet` parancsot.
    - Leírások: [A PowerShellGet telepítése](https://docs.microsoft.com/powershell/gallery/installing-psget).
    - A parancs: `Install-Module -Name PowerShellGet`.
    - Választható paraméterek:
        - `-Proxy`. Itt adhatja meg a proxykiszolgáló a kérelem.
        - `-Force`. Megkerüli a "telepítve" figyelmeztetést, és telepíti a legújabb verziót.

    Ez a hiba fog kapni, ha a PowerShellGet legújabb verziója nem használ:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Indítsa újra a PowerShell. Az új verziót a jelenlegi munkamenet nem tölthető be. Új PowerShell-munkameneteket fogja tölteni a PowerShellGet legújabb verzióját.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Töltse le és telepítse a modul PowerShell-galéria-n keresztül

Ezeket a lépéseket a Az.ApplicationMonitor modul letölt a PowerShell-galériából.

1. Győződjön meg arról, hogy a PowerShell-galériából minden előfeltétele teljesül.
2. PowerShell futtatása rendszergazdaként az emelt szintű végrehajtási házirendjével.
3. A Az.ApplicationMonitor modul telepítése.
    - Leírások: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - A parancs: `Install-Module -Name Az.ApplicationMonitor`.
    - Választható paraméterek:
        - `-Proxy`. Itt adhatja meg a proxykiszolgáló a kérelem.
        - `-AllowPrerelease`. Lehetővé teszi a telepítést a alfa és béta kiadások.
        - `-AcceptLicense`. Megkerüli a "Licenc elfogadása" használatával
        - `-Force`. Figyelmen kívül hagyja a "Nem megbízható tárház" figyelmeztetés.

## <a name="download-and-install-the-module-manually-offline-option"></a>Töltse le és telepítse a modult manuálisan (kapcsolat beállítása)

Ha bármilyen okból nem tud csatlakozni a PowerShell-modult, manuálisan töltse le és telepítse a Az.ApplicationMonitor modult.

### <a name="manually-download-the-latest-nupkg-file"></a>Manuálisan töltse le a legújabb nupkg

1. Nyissa meg a következőt: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Válassza ki a fájlt a legújabb verzióját a **korábbi verziók** tábla.
3. A **telepítési beállítások**válassza **manuális letöltés**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Option 1: Egy PowerShell-modulok könyvtárba telepítése
Manuálisan letöltött PowerShell-modul telepítéséhez, egy olyan PowerShell-könyvtárba, így felderíthető PowerShell-munkamenetek szerint.
További információkért lásd: [egy PowerShell-modul telepítése](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Kibontás – archívum (v1.0.1.0) használatával csomagolja ki nupkg zip-fájlként

- Leírás: Alapszintű verziójának Microsoft.PowerShell.Archive (v1.0.1.0) nupkg fájlokat nem kicsomagolni. Nevezze át a fájlt .zip kiterjesztéssel.
- Leírások: [Bontsa ki a csomópontot archív](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- A parancs:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Csomagolja ki a nupkg Kibontás – archívum (v1.1.0.0) használatával

- Leírás: A jelenlegi verzióját használja, Expand-archívum nupkg fájlokat kicsomagolni a bővítmény módosítása nélkül.
- Leírások: [Bontsa ki a csomópontot archív](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) és [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- A parancs:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Option 2: Csomagolja ki, és nupkg manuális importálása
Manuálisan letöltött PowerShell-modul telepítéséhez, egy olyan PowerShell-könyvtárba, így felderíthető PowerShell-munkamenetek szerint.
További információkért lásd: [egy PowerShell-modul telepítése](https://docs.mircrosoft.com/powershell/developer/module/installing-a-powershell-module).

Ha telepíti a modul más könyvtárba, manuálisan importálja a modult a [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> DLL-fájl relatív elérési utakat keresztül fogja telepíteni.
> A csomag tartalmának Store az importálni kívánt modul könyvtárban, és győződjön meg arról, hogy hozzáférési engedélyek olvasása, de nem írási engedélyezik-e.

1. Módosítsa a bővítmény ".zip", majd bontsa ki a csomag tartalmát a kívánt telepítési könyvtárába.
2. Keresse meg a Az.ApplicationMonitor.psd1 elérési útja.
3. PowerShell futtatása rendszergazdaként az emelt szintű végrehajtási házirendjével.
4. A modul betöltése a használatával a `Import-Module Az.ApplicationMonitor.psd1` parancsot.
    

## <a name="route-traffic-through-a-proxy"></a>Forgalom irányítása a proxyn keresztül

A saját intraneten figyelése a számítógépen, szüksége lesz egy proxyn keresztül HTTP-forgalom irányítására.

Töltse le és telepítse a Az.ApplicationMonitor a PowerShell-galériából, a PowerShell-parancsok támogatása egy `-Proxy` paraméter.
Tekintse át az előző utasítások, ha a telepítési szkriptek ír.

Az Application Insights SDK-t kell az alkalmazás telemetriai adatokat küldeni a Microsoftnak. Azt javasoljuk, hogy Ön Proxybeállítások konfigurálása az alkalmazáshoz a web.config fájlban. További információkért lásd: [Application Insights – gyakori kérdések: Proxy csatlakoztatott](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Monitorozás engedélyezése

Használja a `Enable-ApplicationInsightsMonitoring` parancs használatával engedélyezze a monitorozást.

Tekintse meg a [API-referencia](status-monitor-v2-api-enable-monitoring.md) Ez a parancsmag részletes leírását.



## <a name="next-steps"></a>További lépések

 A telemetriai adatok megtekintése:

- [Metrikák böngészése](../../azure-monitor/app/metrics-explorer.md) teljesítményének figyelése és használati.
- [Eseményeket és naplókat kereshet](../../azure-monitor/app/diagnostic-search.md) problémák diagnosztizálásához.
- [Elemzések](../../azure-monitor/app/analytics.md) az összetettebb lekérdezésekhez.
- [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).

 További telemetriai funkciók hozzáadása:

- [Létrehozhat webes teszteket](monitor-web-app-availability.md) , hogy a hely elérhető maradjon.
- [Ügyfél-telemetriát adhat hozzá](../../azure-monitor/app/javascript.md) lássa a weblapkód kivételeit és nyomkövetési hívásokat engedélyezése.
- [Az Application Insights SDK hozzáadása a kódhoz](../../azure-monitor/app/asp-net.md) így nyomkövetési és naplóhíváskat szúrhasson.

Hozzon ki még többet Állapotfigyelőt v2:

- Használja az útmutatóban [hibaelhárítása](status-monitor-v2-troubleshoot.md) Állapotfigyelőt v2.
