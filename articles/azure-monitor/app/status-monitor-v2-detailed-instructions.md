---
title: Az Azure Application Insights Agent részletes útmutatója | Microsoft Docs
description: Részletes utasítások a Application Insights-ügynök első lépéseihez. Webhelyek teljesítményének figyelése a webhely újbóli üzembe helyezése nélkül. Együttműködik a helyszínen, a virtuális gépeken vagy az Azure-on üzemeltetett ASP.NET Web Apps szolgáltatásokkal.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 8a9ae2364b51c0c201f2efffb3a7433594f76c49
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899646"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Application Insights ügynök (korábbi nevén Állapotmonitor v2): részletes utasítások

Ez a cikk a PowerShell-galéria bevezetését és a ApplicationMonitor modul letöltését ismerteti.
Itt láthatók azok a leggyakoribb paraméterek, amelyeket el kell kezdenie.
A manuális letöltési utasításokat is megadta, ha nem rendelkezik internet-hozzáféréssel.

## <a name="get-an-instrumentation-key"></a>Rendszerállapot-kulcs beszerzése

Első lépésként szükség van egy kialakítási kulcsra. További információ: [Application Insights erőforrás létrehozása](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Futtassa a PowerShellt rendszergazdaként egy emelt szintű végrehajtási házirenddel

### <a name="run-as-admin"></a>Futtatás rendszergazdaként

A PowerShellnek rendszergazdai szintű engedélyekkel kell rendelkeznie a számítógép módosításához.
### <a name="execution-policy"></a>Végrehajtási házirend
- Leírás: alapértelmezés szerint a PowerShell-parancsfájlok futtatása le van tiltva. Javasoljuk, hogy csak az aktuális hatókörhöz engedélyezze a RemoteSigned-parancsfájlokat.
- Hivatkozás: [a végrehajtási házirendek és a](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Parancs: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Választható paraméter:
    - `-Force` kérdésre adott válaszban foglalt lépéseket. Megkerüli a megerősítési kérést.

**Példák a hibákra**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>A PowerShell előfeltételei

A PowerShell-példány naplózása a `$PSVersionTable` parancs futtatásával.
Ez a parancs a következő kimenetet hozza létre:


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

Ezeket az utasításokat a Windows 10 rendszert és a fent felsorolt verziókat futtató számítógépen írták és tesztelték.

## <a name="prerequisites-for-powershell-gallery"></a>Az PowerShell-galéria előfeltételei

Ezek a lépések előkészítik a kiszolgálót a PowerShell-galéria-ból származó modulok letöltésére.

> [!NOTE] 
> A PowerShell-galéria Windows 10, Windows Server 2016 és PowerShell 6 rendszeren támogatott.
> További információ a korábbi verziókról: a [PowerShellGet telepítése](https://docs.microsoft.com/powershell/gallery/installing-psget).


1. Futtassa a PowerShellt rendszergazdaként egy emelt szintű végrehajtási házirenddel.
2. Telepítse a NuGet csomag szolgáltatóját.
    - Leírás: szükség van erre a szolgáltatóra, hogy együttműködjön a NuGet-alapú adattárakkal, például a PowerShell-galériaokkal.
    - Hivatkozás: [install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Parancs: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Választható paraméterek:
        - `-Proxy` kérdésre adott válaszban foglalt lépéseket. Megad egy proxykiszolgálót a kérelemhez.
        - `-Force` kérdésre adott válaszban foglalt lépéseket. Megkerüli a megerősítési kérést.
    
    Ezt a kérdést akkor kapja meg, ha a NuGet nincs beállítva:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. PowerShell-galéria konfigurálása megbízható tárházként.
    - Leírás: alapértelmezés szerint a PowerShell-galéria nem megbízható tárház.
    - Hivatkozás: [set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Parancs: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Választható paraméter:
        - `-Proxy` kérdésre adott válaszban foglalt lépéseket. Megad egy proxykiszolgálót a kérelemhez.

    Ezt a kérdést akkor kapja meg, ha PowerShell-galéria nem megbízható:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Ezt a módosítást és az összes PSRepositories naplózását a `Get-PSRepository` parancs futtatásával ellenőrizheti.

4. Telepítse a PowerShellGet legújabb verzióját.
    - Leírás: Ez a modul a más modulok PowerShell-galériaból való beolvasásához használt eszközöket tartalmazza. Az 1.0.0.1-es verzió a Windows 10 és a Windows Server rendszerű. A 1.6.0 vagy újabb verzió szükséges. A telepített verzió meghatározásához futtassa a `Get-Command -Module PowerShellGet` parancsot.
    - Hivatkozás: a [PowerShellGet telepítése](https://docs.microsoft.com/powershell/gallery/installing-psget).
    - Parancs: `Install-Module -Name PowerShellGet`.
    - Választható paraméterek:
        - `-Proxy` kérdésre adott válaszban foglalt lépéseket. Megad egy proxykiszolgálót a kérelemhez.
        - `-Force` kérdésre adott válaszban foglalt lépéseket. Megkerüli a "már telepített" figyelmeztetést, és telepíti a legújabb verziót.

    Ez a hibaüzenet akkor jelenik meg, ha nem a PowerShellGet legújabb verzióját használja:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Indítsa újra a PowerShellt. Az új verzió nem tölthető be az aktuális munkamenetben. Az új PowerShell-munkamenetek betöltik a PowerShellGet legújabb verzióját.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>A modul letöltése és telepítése PowerShell-galéria használatával

Ezek a lépések az az. ApplicationMonitor modult töltik le PowerShell-galériaból.

1. Győződjön meg arról, hogy a PowerShell-galéria összes előfeltétele teljesül.
2. Futtassa a PowerShellt rendszergazdaként egy emelt szintű végrehajtási házirenddel.
3. Telepítse az az. ApplicationMonitor modult.
    - Hivatkozás: [install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Parancs: `Install-Module -Name Az.ApplicationMonitor`.
    - Választható paraméterek:
        - `-Proxy` kérdésre adott válaszban foglalt lépéseket. Megad egy proxykiszolgálót a kérelemhez.
        - `-AllowPrerelease` kérdésre adott válaszban foglalt lépéseket. Az Alpha és a Beta kiadásának telepítését teszi lehetővé.
        - `-AcceptLicense` kérdésre adott válaszban foglalt lépéseket. A "licenc elfogadása" üzenet megkerülése
        - `-Force` kérdésre adott válaszban foglalt lépéseket. Megkerüli a "nem megbízható tárház" figyelmeztetést.

## <a name="download-and-install-the-module-manually-offline-option"></a>Töltse le és telepítse manuálisan a modult (offline beállítás)

Ha bármilyen okból nem tud csatlakozni a PowerShell-modulhoz, akkor manuálisan letöltheti és telepítheti az az. ApplicationMonitor modult.

### <a name="manually-download-the-latest-nupkg-file"></a>A legújabb nupkg-fájl manuális letöltése

1. Nyissa meg a következőt: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Válassza ki a fájl legújabb verzióját az **verziótörténete** táblában.
3. A **telepítési beállítások**alatt válassza a **manuális Letöltés**lehetőséget.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>1\. lehetőség: telepítés PowerShell-modulok könyvtárába
Telepítse a manuálisan letöltött PowerShell-modult egy PowerShell-könyvtárba, hogy a PowerShell-munkamenetek is felderíthetők legyenek.
További információ: PowerShell- [modul telepítése](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Nupkg kibontása zip-fájlként a Expand-Archive (v 1.0.1.0) használatával

- Leírás: a Microsoft. PowerShell. Archive (v 1.0.1.0) alapverziója nem tudja kibontani a nupkg-fájlokat. Nevezze át a fájlt a. zip kiterjesztéssel.
- Hivatkozás: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Parancs

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Nupkg kicsomagolása a Expand-Archive (v 1.1.0.0) használatával

- Leírás: a Expand-Archive aktuális verziójának használatával bontsa ki a nupkg-fájlokat a bővítmény módosítása nélkül.
- Hivatkozás: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) és [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Parancs

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>2\. lehetőség: a nupkg manuális kicsomagolása és importálása
Telepítse a manuálisan letöltött PowerShell-modult egy PowerShell-könyvtárba, hogy a PowerShell-munkamenetek is felderíthetők legyenek.
További információ: PowerShell- [modul telepítése](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).

Ha a modult bármely más könyvtárba telepíti, manuálisan importálja a modult [importálási modul](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)használatával.

> [!IMPORTANT] 
> A DLL-eket relatív elérési utakon keresztül kell telepíteni.
> Tárolja a csomag tartalmát a kívánt futtatókörnyezeti címtárban, és győződjön meg arról, hogy a hozzáférési engedélyek lehetővé teszik az olvasást, de nem írhatnak.

1. Módosítsa a kiterjesztést a ". zip" értékre, és bontsa ki a csomag tartalmát a kívánt telepítési könyvtárba.
2. Keresse meg az az. ApplicationMonitor. psd1 fájl elérési útját.
3. Futtassa a PowerShellt rendszergazdaként egy emelt szintű végrehajtási házirenddel.
4. Töltse be a modult a `Import-Module Az.ApplicationMonitor.psd1` parancs használatával.
    

## <a name="route-traffic-through-a-proxy"></a>Forgalom irányítása proxyn keresztül

Ha a privát intraneten lévő számítógépet figyeli, a HTTP-forgalmat egy proxyn keresztül kell átirányítani.

A (z) PowerShell-galéria a (z) `-Proxy` paramétert támogató PowerShell-parancsok az az. ApplicationMonitor fájlból tölthetők le és telepíthetők.
A telepítési parancsfájlok írásakor tekintse át a fenti utasításokat.

A Application Insights SDK-nak el kell küldenie az alkalmazás telemetria a Microsoftnak. Javasoljuk, hogy konfigurálja az alkalmazás proxybeállításait a web. config fájlban. További információ [: Application INSIGHTS GYIK: proxy átadó](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Monitorozás engedélyezése

A figyelés engedélyezéséhez használja a `Enable-ApplicationInsightsMonitoring` parancsot.

A parancsmag használatának részletes ismertetését az [API-referenciában](status-monitor-v2-api-enable-monitoring.md) tekintheti meg.



## <a name="next-steps"></a>Következő lépések

 A telemetriai adatok megtekintése:

- [Ismerje meg a mérőszámokat](../../azure-monitor/app/metrics-explorer.md) a teljesítmény és a használat figyeléséhez.
- [Események és naplók keresése](../../azure-monitor/app/diagnostic-search.md) a problémák diagnosztizálásához.
- További speciális lekérdezésekhez [használja az elemzést](../../azure-monitor/app/analytics.md) .
- [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).

 További telemetriai funkciók hozzáadása:

- [Létrehozhat webes teszteket](monitor-web-app-availability.md) annak biztosításához, hogy a hely élőben maradjon.
- [Vegyen fel webes ügyfél-telemetria](../../azure-monitor/app/javascript.md) a kivételek megjelenítéséhez a weboldali kódból és a nyomkövetési hívások engedélyezéséhez.
- [Adja hozzá a Application INSIGHTS SDK-t a kódhoz](../../azure-monitor/app/asp-net.md) , hogy nyomkövetési és naplózási hívásokat helyezzen el.

Több Application Insights-ügynökkel:

- Az útmutató segítségével Application Insights-ügynököt lehet [elhárítani](status-monitor-v2-troubleshoot.md) .
