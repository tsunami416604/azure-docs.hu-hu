---
title: Az Azure Application Insights Ügynök részletes utasításai | Microsoft dokumentumok
description: Részletes utasítások az Application Insights-ügynök első lépésekhez. A webhely teljesítményének figyelése a webhely újratelepítése nélkül. Együttműködik ASP.NET helyszíni, virtuális gépeken vagy az Azure-ban üzemeltetett webalkalmazásokkal.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 8f6134e8f8fdb9af3f578afaf0670c32a3896e01
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766866"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Application Insights-ügynök (korábbi nevén Állapotfigyelő v2): Részletes utasítások

Ez a cikk ismerteti, hogyan a powershell-galériába való alaplapra, és töltse le az ApplicationMonitor modult.
Ide tartoznak a leggyakoribb paraméterek, amelyeket az első lépésekhez kell beszereznie.
Manuális letöltési utasításokat is mellékeltünk arra az esetre, ha nem rendelkezik internet-hozzáféréssel.

## <a name="get-an-instrumentation-key"></a>Műszerkulcs beszerezni

A kezdéshez szüksége van egy műszerkulcsra. További információt az [Application Insights-erőforrás létrehozása című témakörben talál.](create-new-resource.md#copy-the-instrumentation-key)

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>PowerShell futtatása rendszergazdaként emelt szintű végrehajtási szabályzattal

### <a name="run-as-admin"></a>Futtatás rendszergazdaként

A PowerShellnek rendszergazdai szintű engedélyekre van szüksége a számítógép módosításához.
### <a name="execution-policy"></a>Végrehajtási házirend
- Leírás: Alapértelmezés szerint a PowerShell-parancsfájlok futtatása le van tiltva. Azt javasoljuk, hogy csak az aktuális hatókörhöz engedélyezze a RemoteSigned parancsfájlokat.
- Hivatkozás: [A végrehajtási házirendekről](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) és [a Set-ExecutionPolicy-ről.](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)
- Parancs: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Választható paraméter:
    - `-Force`. Megkerüli a megerősítő kérdést.

**Példa hibákra**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>A PowerShell előfeltételei

A powershell-példány naplózása `$PSVersionTable` a parancs futtatásával.
Ez a parancs a következő kimenetet adja elő:


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

Ezeket az utasításokat Windows 10 rendszert futtató számítógépen és a fent felsorolt verziókon írták és tesztelték.

## <a name="prerequisites-for-powershell-gallery"></a>A PowerShell-gyűjtemény előfeltételei

Ezek a lépések előkészítik a kiszolgálót a modulok PowerShell-galériából való letöltésére.

> [!NOTE] 
> A PowerShell-galériát támogatja a Windows 10, a Windows Server 2016 és a PowerShell 6.
> A korábbi verziókról a [PowerShellGet telepítése című](/powershell/scripting/gallery/installing-psget)témakörben talál további információt.


1. Futtassa a PowerShellt rendszergazdaként emelt szintű végrehajtási szabályzattal.
2. Telepítse a NuGet csomagszolgáltatót.
    - Leírás: A NuGet-alapú tárházak, például a PowerShell-tár kezeléséhez szükség van erre a szolgáltatóra.
    - Hivatkozás: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Parancs: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Választható paraméterek:
        - `-Proxy`. A kérelem proxykiszolgálóját adja meg.
        - `-Force`. Megkerüli a megerősítő kérdést.
    
    Ez a kérdés akkor jelenik meg, ha a NuGet nincs beállítva:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Konfigurálja a PowerShell-galériát megbízható tárházként.
    - Leírás: Alapértelmezés szerint a PowerShell-tár egy nem megbízható tárház.
    - Hivatkozás: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Parancs: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Választható paraméter:
        - `-Proxy`. A kérelem proxykiszolgálóját adja meg.

    Ez a kérdés akkor jelenik meg, ha a PowerShell-galéria nem megbízható:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Ezt a módosítást megerősítheti, és a `Get-PSRepository` parancs futtatásával naplózhatja az összes PSRepositores-t.

4. Telepítse a PowerShellGet legújabb verzióját.
    - Leírás: Ez a modul tartalmazza a PowerShell-galériából más modulok lefelvételéhez használt eszközt. Az 1.0.0.1-es verzió windows 10-es és Windows Server rendszerrel van elszállítva. Az 1.6.0-s vagy újabb verzió szükséges. A telepített verzió meghatározásához `Get-Command -Module PowerShellGet` futtassa a parancsot.
    - Hivatkozás: [A PowerShellGet telepítése](/powershell/scripting/gallery/installing-psget).
    - Parancs: `Install-Module -Name PowerShellGet`.
    - Választható paraméterek:
        - `-Proxy`. A kérelem proxykiszolgálóját adja meg.
        - `-Force`. Megkerüli a "már telepítve" figyelmeztetést, és telepíti a legújabb verziót.

    Ez a hibaüzenet akkor jelenik meg, ha nem a PowerShellGet legújabb verzióját használja:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Indítsa újra a PowerShellt. Az új verzió nem tölthető be az aktuális munkamenetbe. Az új PowerShell-munkamenetek betöltik a PowerShellGet legújabb verzióját.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>A modul letöltése és telepítése a PowerShell-galérián keresztül

Ezek a lépések letöltik az Az.ApplicationMonitor modult a PowerShell-galériából.

1. Győződjön meg arról, hogy a PowerShell-katalógus minden előfeltétele teljesül.
2. Futtassa a PowerShellt rendszergazdaként emelt szintű végrehajtási szabályzattal.
3. Telepítse az Az.ApplicationMonitor modult.
    - Hivatkozás: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Parancs: `Install-Module -Name Az.ApplicationMonitor`.
    - Választható paraméterek:
        - `-Proxy`. A kérelem proxykiszolgálóját adja meg.
        - `-AllowPrerelease`. Lehetővé teszi az alfa- és bétakiadások telepítését.
        - `-AcceptLicense`. Megkerüli a "Licenc elfogadása" kérdést
        - `-Force`. Megkerüli a "Nem megbízható tárház" figyelmeztetést.

## <a name="download-and-install-the-module-manually-offline-option"></a>Töltse le és telepítse a modult manuálisan (offline opció)

Ha bármilyen okból nem tud csatlakozni a PowerShell modulhoz, manuálisan letöltheti és telepítheti az Az.ApplicationMonitor modult.

### <a name="manually-download-the-latest-nupkg-file"></a>A legújabb nupkg fájl manuális letöltése

1. Nyissa meg a következőt: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Válassza ki a fájl legújabb verzióját a **Verzióelőzmények** táblában.
3. A **Telepítési beállítások csoportban**válassza a **Kézi letöltés**lehetőséget.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>1. lehetőség: Telepítés PowerShell-modulok könyvtárába
Telepítse a manuálisan letöltött PowerShell-modult egy PowerShell-címtárba, hogy a PowerShell-munkamenetek felderíthetők legyenek.
További információt a [PowerShell-modul telepítése című témakörben talál.](/powershell/scripting/developer/module/installing-a-powershell-module)


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>A nupkg kicipzárazása zip fájlként az Expand-Archive (v1.0.1.0) használatával

- Leírás: A Microsoft.PowerShell.Archive (1.0.1.0-s verzió) alapverziója nem tudja kicsomagolni a nupkg fájlokat. Nevezze át a fájlt .zip kiterjesztéssel.
- Hivatkozás: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Parancs:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Nupkg kicipzárazása az Expand-Archive használatával (1.1.0.0-s érték)

- Leírás: Az Expand-Archive jelenlegi verziójával csomagolja ki a nupkg fájlokat a kiterjesztés módosítása nélkül.
- Hivatkozás: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) és [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Parancs:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>2. lehetőség: Csomagolja ki és importálja a nupkg-ot manuálisan
Telepítse a manuálisan letöltött PowerShell-modult egy PowerShell-címtárba, hogy a PowerShell-munkamenetek felderíthetők legyenek.
További információt a [PowerShell-modul telepítése című témakörben talál.](/powershell/scripting/developer/module/installing-a-powershell-module)

Ha a modult más könyvtárba telepíti, manuálisan importálja a modult az [Importálásmodul](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)segítségével.

> [!IMPORTANT] 
> A DL-ek relatív elérési utakon keresztül települnek.
> Tárolja a csomag tartalmát a tervezett futásidejű könyvtárban, és ellenőrizze, hogy a hozzáférési engedélyek engedélyezik-e az olvasást, de az írást nem.

1. Módosítsa a bővítményt ".zip" névre, és bontsa ki a csomag tartalmát a kívánt telepítési könyvtárba.
2. Keresse meg az Az.ApplicationMonitor.psd1 fájlelérési útját.
3. Futtassa a PowerShellt rendszergazdaként emelt szintű végrehajtási szabályzattal.
4. Töltse be a `Import-Module Az.ApplicationMonitor.psd1` modult a paranccsal.
    

## <a name="route-traffic-through-a-proxy"></a>Forgalom átirányítása proxyn keresztül

Amikor a privát intraneten figyeli a számítógépet, a HTTP-forgalmat proxyn keresztül kell irányítania.

A PowerShell-parancsok az Az.ApplicationMonitor letöltéséhez és `-Proxy` telepítéséhez a PowerShell-galériából támogatnak egy paramétert.
A telepítési parancsfájlok írásakor tekintse át az előző utasításokat.

Az Application Insights SDK-nak el kell küldenie az alkalmazás telemetriáját a Microsoftnak. Javasoljuk, hogy a web.config fájlban adja meg az alkalmazás proxybeállításait. További információ: [Application Insights GYQ: Proxy passthrough](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Monitorozás engedélyezése

A `Enable-ApplicationInsightsMonitoring` figyelés engedélyezéséhez használja a parancsot.

Tekintse meg az [API-referencia](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-applicationinsightsmonitoring) részletes leírását, hogyan kell használni ezt a parancsmamot.



## <a name="next-steps"></a>További lépések

 A telemetriai adatok megtekintése:

- [Fedezze fel a teljesítmény](../../azure-monitor/platform/metrics-charts.md) és a használat figyeléséhez.
- [Keresés események és naplók](../../azure-monitor/app/diagnostic-search.md) diagnosztizálására problémákat.
- [Az Analytics használata](../../azure-monitor/app/analytics.md) fejlettebb lekérdezésekhez.
- [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).

 További telemetriai funkciók hozzáadása:

- [Létrehozhat webes teszteket](monitor-web-app-availability.md) annak biztosításához, hogy a hely elérhető maradjon.
- [Webes ügyfél telemetriai adatok](../../azure-monitor/app/javascript.md) hozzáadásával megtekintheti a weblapkódból származó kivételeket, és engedélyezheti a nyomkövetési hívásokat.
- [Adja hozzá az Application Insights SDK-t a kódhoz,](../../azure-monitor/app/asp-net.md) így nyomkövetési és naplóhívásokat szúrhat be.

További információk az Application Insights-ügynökkel:

- Útmutatónk segítségével [elháríthatja](status-monitor-v2-troubleshoot.md) az Application Insights-ügynök hibáit.
