---
title: Az Azure Állapotfigyelőt v2 – első lépések |} A Microsoft Docs
description: Rövid útmutató Állapotfigyelőt v2-ben. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. ASP.NET-webalkalmazásokat együttműködik a helyileg üzemeltetett, a virtuális gépek vagy az Azure-ban.
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
ms.openlocfilehash: 4da9d8e8efd5f70718f18b2e8e35ea6b5adf6757
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734971"
---
# <a name="get-started-with-status-monitor-v2"></a>Az állapotfigyelő v2 – első lépések

Ez a cikk a várható, hogy a legtöbb környezetben működni a rövid útmutató parancsokat tartalmazza.
Az utasítások a PowerShell-galériából, a frissítéseket függenek.
Ezeket a parancsokat a PowerShell támogatja `-Proxy` paraméter.

Ezen parancsok, testreszabási utasításokat és hibaelhárítási információk ismertetése, tekintse meg a [részletes utasítások](status-monitor-v2-detailed-instructions.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió egy szolgáltatásiszint-megállapodás nélkül biztosított, és ezt nem javasoljuk a termelési számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, és néhány előfordulhat, hogy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="download-and-install-via-powershell-gallery"></a>Töltse le és telepítse a PowerShell-galéria-n keresztül

### <a name="install-prerequisites"></a>Az előfeltételek telepítése
PowerShell futtatása rendszergazdaként
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Zárja be a Powershellt.

### <a name="install-status-monitor-v2"></a>Telepítési állapot figyelése v2
PowerShell futtatása rendszergazdaként
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Monitorozás engedélyezése
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Manuális letöltés és telepítés (offline kapcsoló)
### <a name="download-the-module"></a>A modul letöltése
A modul legújabb verziójának manuális letöltéséhez [PowerShell-galériából](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-status-monitor-v2"></a>Csomagolja ki, és telepítse az Állapotfigyelőt v2
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

- [Metrikák böngészése](../../azure-monitor/app/metrics-explorer.md) teljesítményének figyelése és használati.
- [Eseményeket és naplókat kereshet](../../azure-monitor/app/diagnostic-search.md) problémák diagnosztizálásához.
- [Elemzések](../../azure-monitor/app/analytics.md) az összetettebb lekérdezésekhez.
- [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).

 További telemetriai funkciók hozzáadása:

- [Létrehozhat webes teszteket](monitor-web-app-availability.md) , hogy a hely elérhető maradjon.
- [Ügyfél-telemetriát adhat hozzá](../../azure-monitor/app/javascript.md) lássa a weblapkód kivételeit és nyomkövetési hívásokat engedélyezése.
- [Az Application Insights SDK hozzáadása a kódhoz](../../azure-monitor/app/asp-net.md) így nyomkövetési és naplóhíváskat szúrhasson.

Hozzon ki még többet Állapotfigyelőt v2:

- Tekintse át a [részletes utasítások](status-monitor-v2-detailed-instructions.md) az itt található parancsok részéből.
- Használja az útmutatóban [hibaelhárítása](status-monitor-v2-troubleshoot.md) Állapotfigyelőt v2.
