---
title: Azure Application Insights-ügynök – első lépések | Microsoft dokumentumok
description: Rövid útmutató az Application Insights-ügynökhöz. A webhely teljesítményének figyelése a webhely újratelepítése nélkül. Együttműködik ASP.NET helyszíni, virtuális gépeken vagy az Azure-ban üzemeltetett webalkalmazásokkal.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 7819de1f3dfab7f934421de86c0481d2e063f7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671188"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Első lépések az Azure Monitor Application Insights Agent helyszíni kiszolgálókhoz

Ez a cikk a legtöbb környezetben várhatóan működni fog.
Az utasítások a PowerShell-galériától függenek a frissítések terjesztéséhez.
Ezek a parancsok `-Proxy` támogatják a PowerShell-paramétert.

Ezeknek a parancsoknak, testreszabási utasításoknak és hibaelhárítási információknak a magyarázatát a [részletes utasításokban](status-monitor-v2-detailed-instructions.md)találja.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="download-and-install-via-powershell-gallery"></a>Letöltés és telepítés a PowerShell-galérián keresztül

### <a name="install-prerequisites"></a>Az előfeltételek telepítése
Futtassa a PowerShellt rendszergazdaként.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Zárja be a PowerShellt.

### <a name="install-application-insights-agent"></a>Az Application Insights-ügynök telepítése
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
    
        
## <a name="download-and-install-manually-offline-option"></a>Manuális letöltés és telepítés (kapcsolat nélküli beállítás)
### <a name="download-the-module"></a>A modul letöltése
Töltse le manuálisan a modul legújabb verzióját a [PowerShell-galériából.](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)

### <a name="unzip-and-install-application-insights-agent"></a>Az Application Insights-ügynök kicipzárazása és telepítése
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



## <a name="next-steps"></a>További lépések

 A telemetriai adatok megtekintése:

- [Fedezze fel a teljesítmény](../../azure-monitor/app/metrics-explorer.md) és a használat figyeléséhez.
- [Keresés események és naplók](../../azure-monitor/app/diagnostic-search.md) diagnosztizálására problémákat.
- [Az Analytics használata](../../azure-monitor/app/analytics.md) fejlettebb lekérdezésekhez.
- [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).

 További telemetriai funkciók hozzáadása:

- [Létrehozhat webes teszteket](monitor-web-app-availability.md) annak biztosításához, hogy a hely elérhető maradjon.
- [Webes ügyfél telemetriai adatok](../../azure-monitor/app/javascript.md) hozzáadásával megtekintheti a weblapkódból származó kivételeket, és engedélyezheti a nyomkövetési hívásokat.
- [Adja hozzá az Application Insights SDK-t a kódhoz,](../../azure-monitor/app/asp-net.md) így nyomkövetési és naplóhívásokat szúrhat be.

További információk az Application Insights-ügynökkel:

- Tekintse át a [részletes utasításokat](status-monitor-v2-detailed-instructions.md) az itt található parancsok magyarázatához.
- Útmutatónk segítségével [elháríthatja](status-monitor-v2-troubleshoot.md) az Application Insights-ügynök hibáit.
