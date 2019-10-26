---
title: Azure Application Insights-ügynök – első lépések | Microsoft Docs
description: Útmutató Application Insights-ügynökhöz. Webhelyek teljesítményének figyelése a webhely újbóli üzembe helyezése nélkül. Együttműködik a helyszínen, a virtuális gépeken vagy az Azure-on üzemeltetett ASP.NET Web Apps szolgáltatásokkal.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 1ef354bc61d849a4d536bd26355ef21b0f1c0035
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899613"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Ismerkedés a Azure Monitor Application Insights-ügynökkel a helyszíni kiszolgálók esetében

Ez a cikk a legtöbb környezetben várhatóan használható gyors üzembe helyezési parancsokat tartalmazza.
Az utasítások a frissítések terjesztésének PowerShell-galéria függenek.
Ezek a parancsok támogatják a PowerShell `-Proxy` paramétert.

A parancsok magyarázatát, a testreszabási utasításokat és a hibaelhárítással kapcsolatos információkat a [részletes utasításokban](status-monitor-v2-detailed-instructions.md)találja.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="download-and-install-via-powershell-gallery"></a>Letöltés és telepítés PowerShell-galéria használatával

### <a name="install-prerequisites"></a>Az előfeltételek telepítése
Futtassa a PowerShellt rendszergazdaként.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
A PowerShell bezárásához.

### <a name="install-application-insights-agent"></a>Application Insights ügynök telepítése
Futtassa a PowerShellt rendszergazdaként.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Monitorozás engedélyezése
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Manuális letöltés és telepítés (offline beállítás)
### <a name="download-the-module"></a>A modul letöltése
A modul legújabb verziójának manuális letöltése [PowerShell-galériaról](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-application-insights-agent"></a>Application Insights-ügynök kicsomagolása és telepítése
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Monitorozás engedélyezése
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



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

- Tekintse át az itt található parancsok magyarázatának [részletes utasításait](status-monitor-v2-detailed-instructions.md) .
- Az útmutató segítségével Application Insights-ügynököt lehet [elhárítani](status-monitor-v2-troubleshoot.md) .
