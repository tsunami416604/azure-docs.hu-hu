---
title: Az Azure Állapotfigyelőt v2 részletes utasítások |} A Microsoft Docs
description: Részletes útmutató az Állapotfigyelőt v2 – első lépések. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. A helyszíni, valamint a virtuális gépeken, illetve az Azure-ban üzemeltetett ASP.NET-webappokhoz is használható.
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
ms.openlocfilehash: 3aca64c7b0f1ad04967782cb3349da302db557a0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145088"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Állapot figyelő v2 részletes utasítások

A részletek hogyan dokumentálja a PowerShell-galériából és letöltése a ApplicationMonitor modulban előkészítéséhez. Azt már dokumentált a leggyakrabban használt paraméterek, a kezdéshez szükséges.
Emellett bővítettük manuális utasításokat, abban az esetben, ha az internet-hozzáférés nem érhető el.

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="instrumentation-key"></a>Rendszerállapotkulcs

Első lépésként egy kialakítási kulcsot kell rendelkeznie. További információkért olvassa el [hozzon létre egy Application Insights-erőforrást](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-administrator-with-an-elevated-execution-policy"></a>PowerShell futtatása rendszergazdaként az emelt szintű futtatás szabályzatával

**Futtatás rendszergazdaként**: 
- Leírás: PowerShell rendszergazdai szintű engedélyekkel kell módosításokat a számítógépre.

**A végrehajtási házirend**:
- Leírás: Alapértelmezés szerint PowerShell-parancsfájlok futtatásakor letiltásra kerül. Azt javasoljuk, hogy lehetővé teszi a RemoteSigned parancsfájlok csak az aktuális hatókör.
- Leírások: [Végrehajtási házirendek kapcsolatos](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) és [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)
- Cmd: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`
- Választható paraméterek:
    - `-Force` Kihagyja ezt a megerősítési kérést.

**Példa hibák:**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>PowerShell előfeltételei

A jelenlegi verzió PowerShell naplózása a parancs futtatásával: `$PSVersionTable`.
A parancs a következő kimenetet eredményez:


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

Ezek az utasítások írása és tesztelése a Windows 10-es gépen a fent felsorolt verziói.

## <a name="prerequisites-for-powershell-gallery"></a>PowerShell-galériából előfeltételei

Ezeket a lépéseket a modulok letöltése a PowerShell-galériából-kiszolgáló előkészítésével foglalkozunk.

> [!NOTE] 
> A Windows 10-es, a Windows Server 2016 és a PowerShell 6-os PowerShell-galériából támogatását tartalmazza. A korábbi verziók esetében tekintse át az ebben a dokumentumban: [A PowerShellGet telepítése](https://docs.microsoft.com/powershell/gallery/installing-psget)


1. PowerShell futtatása rendszergazdaként az emelt szintű végrehajtási házirendjével.
2. NuGet-csomag szolgáltató 
    - Leírás: Ez a szolgáltató tárházak NuGet-alapú, például a PowerShell-Galériabeli interakcióba van szükség
    - Leírások: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)
    - Cmd: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`
    - Választható paraméterek:
        - `-Proxy` Itt adhatja meg a proxykiszolgáló a kérelem.
        - `-Force` Kihagyja ezt a megerősítési kérést. 
    
    Ez a kérdés fog kapni, ha a NuGet nem jött létre:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Megbízható adattárak
    - Leírás: Alapértelmezés szerint a PowerShell-Galériabeli egy nem megbízható tárház.
    - Leírások: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)
    - Cmd: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`
    - Választható paraméterek:
        - `-Proxy` Itt adhatja meg a proxykiszolgáló a kérelem.

    Ez a kérdés fog kapni, ha a PowerShell-galériában nem megbízható:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    - Erősítse meg ezt a módosítást, és minden PSRepositories naplózása a következő parancs futtatásával: `Get-PSRepository`

4. A PowerShellGet-verzió 
    - Leírás: Ez a modul tartalmazza a PowerShell-galériából más modulok beszerzéséhez használt eszközök. a Windows 10 és Windows Server V1.0.0.1 letöltésként érhető el. Szükséges minimális verziója v1.6.0. Naplózási melyik verziója van telepítve, futtassa a parancsot: `Get-Command -Module PowerShellGet`
    - Leírások: [A PowerShellGet telepítése](https://docs.microsoft.com/powershell/gallery/installing-psget)
    - Cmd: `Install-Module -Name PowerShellGet`
    - Választható paraméterek:
        - `-Proxy` Itt adhatja meg a proxykiszolgáló a kérelem.
        - `-Force` Ez a "telepítve" figyelmeztetést figyelmen kívül, és telepítse a legújabb verziót.

    Ez a hiba fog kapni, ha a PowerShellGet legújabb verziója nem használ:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Indítsa újra a PowerShell. Nem lehet betölteni az új verziót a jelenlegi munkamenet. Minden olyan új Powershell-munkamenetekben lesz a legújabb PowerShellGet betöltve.

## <a name="download--install-via-powershell-gallery"></a>Letöltése és telepítése a PowerShell-galéria-n keresztül

Ezeket a lépéseket a Az.ApplicationMonitor modul letölt a PowerShell-galériából.

1. A PowerShell-galériából Előfeltételek teljesülésére szükség.
2. PowerShell futtatása rendszergazdaként az emelt szintű végrehajtási házirendjével.
3. A Az.ApplicationMonitor modul telepítése
    - Leírások: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)
    - Cmd: `Install-Module -Name Az.ApplicationMonitor`
    - Választható paraméterek:
        - `-Proxy` Itt adhatja meg a proxykiszolgáló a kérelem.
        - `-AllowPrerelease` Ez lehetővé teszi alfa és béta kiadások telepítésével.
        - `-AcceptLicense` Ez kihagyja a "Licenc elfogadása" használatával
        - `-Force` Ezt figyelmen kívül hagyja a "Nem megbízható tárház" figyelmeztetés

## <a name="download--install-manually-offline-option"></a>Töltse le és telepítse manuálisan (kapcsolat beállítása)

Ha bármilyen okból nem tud csatlakozni a PowerShell-modult, akkor előfordulhat, hogy manuálisan letöltése és telepítése a Az.ApplicationMonitor modul.

### <a name="manually-download-the-latest-nupkg"></a>Manuálisan töltse le a legújabb nupkg

1. Keresse meg: https://www.powershellgallery.com/packages/Az.ApplicationMonitor
2. Jelölje ki a korábbi verziók a legújabb verziót.
3. Keresse meg a "Telepítéstípus", és válassza a "Manuális letöltés".

### <a name="option-1-install-into-powershell-modules-directory"></a>1. lehetőség: telepítse a PowerShell-modulok címtárba
PowerShell-címtárhoz való manuálisan letöltött PowerShell-modul telepítéséhez, így lehet felderíthetők a PowerShell-munkameneteket.
További információkért lásd: [Egy PowerShell-modul telepítése](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)


#### <a name="unzip-nupkg-as-zip-using-expand-archive-v1010"></a>Csomagolja ki a nupkg jako zip kibontása – archívum (v1.0.1.0) használatával

- Leírás: Alapszintű verziójának Microsoft.PowerShell.Archive (v1.0.1.0) nupkg fájlokat nem kicsomagolni. Nevezze át a ".zip" kiterjesztésű fájlt.
- Leírások: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)
- Cmd: 

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-using-expand-archive-v1100"></a>Csomagolja ki nupkg Kibontás – archívum (v1.1.0.0) használatával.

- Leírás: Kibontás archiválása aktuális verziója használatával csomagolja ki a nupkgs átnevezése a bővítmény nélkül. 
- Leírások: [Bontsa ki a csomópontot archív](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) és [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)
- Cmd:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-manually"></a>2. lehetőség: csomagolja ki, és manuális importálása
PowerShell-címtárhoz való manuálisan letöltött PowerShell-modul telepítéséhez, így lehet felderíthetők a PowerShell-munkameneteket.
További információkért lásd: [Egy PowerShell-modul telepítése](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)

Telepítés más könyvtárba, ha manuálisan kell importálnia a modul használatával [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)

> [!IMPORTANT] 
> Telepíti a dll-fájl relatív elérési utakat keresztül. Store a csomag tartalmát a kívánt modul könyvtárba, és győződjön meg arról, hogy hozzáférési engedélyek olvasása, de nem írási engedélyezik-e.

1. Módosítsa a bővítmény ".zip", és csomagolja ki a csomag tartalmát a kívánt telepítési könyvtárba.
2. Keresse meg a "Az.ApplicationMonitor.psd1" fájl elérési útját.
3. PowerShell futtatása rendszergazdaként az emelt szintű végrehajtási házirendjével. 
4. A parancs segítségével a modul betöltése: `Import-Module Az.ApplicationMonitor.psd1`.
    

## <a name="proxy"></a>Proxy

A saját intranetes egy gépen figyelésekor proxyn keresztül a http-forgalom irányítására szükség lesz.

Töltse le és telepítse a Az.ApplicationMonitor a PowerShell-galériából, a PowerShell-parancsokat támogatják a `-Proxy` paraméter.
Amikor tekintse át a fenti utasítások a telepítési szkriptek.

Az Application Insights SDK-t kell az alkalmazás telemetriai adatokat küldeni a Microsoftnak. Azt javasoljuk, hogy az alkalmazás Proxybeállítások konfigurálása a web.config fájlban. Lásd: [az Application Insights – gyakori kérdések: Proxy csatlakoztatott](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough) további információt.


## <a name="enable-monitoring"></a>Figyelés engedélyezése 

Cmd: `Enable-ApplicationInsightsMonitoring`

Tekintse át a [API-referencia](status-monitor-v2-api-enable-monitoring.md) Ez a parancsmag részletes leírását. 



## <a name="next-steps"></a>További lépések

 A telemetriai adatok megtekintése:

- [A metrikák áttekintése](../../azure-monitor/app/metrics-explorer.md) a teljesítmény és a használat figyeléséhez
- [Eseményeket és naplókat kereshet](../../azure-monitor/app/diagnostic-search.md) problémák diagnosztizálásához
- [Elemzések](../../azure-monitor/app/analytics.md) az összetettebb lekérdezésekhez
- [Irányítópultok létrehozása](../../azure-monitor/app/app-insights-dashboards.md)

 További telemetriai funkciók hozzáadása:

- [Létrehozhat webes teszteket](monitor-web-app-availability.md) , hogy a hely elérhető maradjon.
- [Ügyfél-telemetriát adhat hozzá](../../azure-monitor/app/javascript.md) lássa a weblapkód kivételeit és nyomkövetési hívásokat szúrhasson be.
- [Application Insights SDK hozzáadása a kódhoz](../../azure-monitor/app/asp-net.md) , hogy Ön nyomkövetési és naplóhíváskat szúrhasson

Hozzon ki még többet Állapotfigyelőt v2:

- Használja az útmutatóban [hibaelhárítás](status-monitor-v2-troubleshoot.md) Állapotfigyelőt v2.
