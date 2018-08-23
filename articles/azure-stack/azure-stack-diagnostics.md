---
title: Diagnosztika az Azure Stackben
description: Hogyan gyűjtheti az Azure stack diagnosztikai naplófájlok
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 08/22/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: a36609ae63351070bb28469d9ccf1f3deb7bc6ff
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42616949"
---
# <a name="azure-stack-diagnostics-tools"></a>Azure Stack-diagnosztikai eszközök

Az Azure Stack együttműködése meg és kezelhetők az egyes összetevők nagy gyűjteménye. Ezeket az összetevőket a saját egyedi naplófájlokat hoznak létre. Ez megnehezítheti diagnosztizálás problémák egy feladat, különösen a több, az Azure Stack-összetevők használatához érkező hibákat. 

A diagnosztikai eszközök segítségével, győződjön meg, hogy a napló gyűjtése mechanizmus egyszerű és hatékony. Az alábbi ábrán látható gyűjtemény eszközök hogyan jelentkezzen be az Azure Stack során:

![Az Azure Stack-diagnosztikai eszközök](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Nyomkövetési gyűjtő
 
A nyomkövetési gyűjtő alapértelmezés szerint engedélyezve van, és folyamatosan fut a háttérben, az összes esemény-nyomkövetése Windows (ETW) gyűjteni az Azure Stack Komponensszolgáltatások. ETW-naplók és a egy öt nap kora korlátja egy közös helyi megosztás vannak tárolva. Ha eléri ezt a korlátot, a legrégebbi fájlok törlődnek, amikor újakat hoz létre. Alapértelmezett maximális engedélyezett az egyes fájlok mérete 200 MB. Egy mérete ellenőrzés 2 percenként történik, és ha az aktuális fájl > 200 MB = a rendszer menti és a egy új fájl jön létre. Esemény-munkamenet által létrehozott fájlok összesített mérete is van egy 8 GB-os korlátot. 

## <a name="log-collection-tool"></a>Napló gyűjtése eszköz
 
A PowerShell-parancsmag **Get-AzureStackLog** használható gyűjteni az Azure Stack-környezet lévő valamennyi összetevőnél. Ez menti őket a zip-fájlokat egy felhasználó által megadott helyen. Az Azure Stack technikai támogatási csapatával a probléma elhárításához naplók van szüksége, ha azok megkérheti, hogy az eszköz futtatásához.

> [!CAUTION]
> Ezek a naplófájlok személyes azonosításra alkalmas adatokat (PII) tartalmazhat. Vegye figyelembe ennek előtt nyilvánosan közzé minden naplófájl.
 
Az alábbiakban néhány példa log típusok gyűjtött:
*   **Az Azure Stack-telepítési naplók**
*   **Windows-eseménynaplók**
*   **Panther naplók**
*   **Fürt naplóit**
*   **Diagnosztikai naplók tárolása**
*   **ETW-naplók**

Ezek a fájlok vannak, és nyomkövetési gyűjtő által olyan megosztáson található, menti. A **Get-AzureStackLog** PowerShell-parancsmag felhasználható gyűjtéséhez őket, amikor erre szükség van.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Get-AzureStackLog futtathatók az Azure Stack integrált rendszerek 
A napló gyűjtése eszköz futtatásához egy integrált rendszer, hozzáféréssel kell rendelkeznie az emelt szintű végpontját (EGP). Íme egy példa parancsfájl segítségével futtathat az EGP naplók összegyűjtése az integrált rendszereken:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- A paraméterek **OutputSharePath** és **OutputShareCredential** segítségével naplók feltöltése egy külső megosztott mappához.
- Az előző példában látható módon a **FromDate** és **ToDate** paraméterek használhatók naplók összegyűjtése egy adott időszakban. Ez többféle kivitelűek lehetnek például a naplók gyűjtésére az integrált rendszereken frissítések alkalmazása után is praktikus.


 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Get-AzureStackLog futtatásához az Azure Stack Development Kit (ASDK) rendszeren
1. Jelentkezzen be, **AzureStack\CloudAdmin** a gazdagépen.
2. Nyisson meg egy PowerShell-ablakot rendszergazdaként.
3. Futtassa a **Get-AzureStackLog** PowerShell-parancsmagot.

**Példák:**

  Gyűjtse össze az összes napló összes szerepköre:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs
  ```

  Naplók gyűjtése a virtuális gép és BareMetal szerepkörök:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal
  ```

  Gyűjtsön naplókat azokról a virtuális gép és BareMetal szerepkörök, a dátum szerinti szűrést a naplófájlok az elmúlt 8 óra:
    
  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  Gyűjtsön naplókat azokról a virtuális gép és BareMetal szerepkörök, a dátum szerinti szűrést a naplófájlokon 8 órával ezelőtt és 2 órával ezelőtt között az adott időszakban:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>A paraméter szempontok ASDK és integrált rendszereket

- Ha a **FromDate** és **ToDate** paraméter nincs megadva, a naplók alapértelmezés szerint az elmúlt 4 órán keresztül gyűjtött.
- Használja a **FilterByNode** paraméter naplók szűrése a számítógép neve szerint. Például:```Get-AzureStackLog -OutputPath <path> -FilterByNode azs-xrp01```
- Használja a **FilterByLogType** paraméter típusa szerint naplók szűrése. Ha szeretné, fájl, fájlmegosztás vagy WindowsEvent alapján. Például:```Get-AzureStackLog -OutputPath <path> -FilterByLogType File```
- Használhatja a **TimeOutInMinutes** paraméter segítségével állítsa be a naplógyűjtés időkorlátja. Időintervallumként 150 (2,5 óra) alapértelmezés szerint.
- Az 1805 és újabb memóriakép-fájl naplógyűjtés alapértelmezés szerint le van tiltva. Annak engedélyezéséhez használja a **IncludeDumpFile** paraméter váltani. 
- Jelenleg is használhatja a **FilterByRole** szűrőt szeretne gyűjteni a következő szerepkörök paramétert:

 |   |   |   |    |
 | - | - | - | -  |   
 |ACS|Compute|InfraServiceController|QueryServiceCoordinator|
 |ACSBlob|CPI|Infrastruktúra|QueryServiceWorker|
 |ACSDownloadService|CRP|KeyVaultAdminResourceProvider|SeedRing|
 |ACSFabric|DatacenterIntegration|KeyVaultControlPlane|SeedRingServices|
 |ACSFrontEnd|DeploymentMachine|KeyVaultDataPlane|SLB|
 |ACSMetrics|DiskRP|KeyVaultInternalControlPlane|SlbVips|
 |ACSMigrationService|Tartomány|KeyVaultInternalDataPlane|SQL|
 |ACSMonitoringService|ECE|KeyVaultNamingService|SRP|
 |ACSSettingsService|EventAdminRP|MDM|Storage|
 |ACSTableMaster|EventRP|MetricsAdminRP|Tárfiókok|
 |ACSTableServer|ExternalDNS|MetricsRP|StorageController|
 |ACSWac|Fabric|MetricsServer|Bérlő|
 |ADFS|FabricRing|MetricsStoreService|TraceCollector|
 |ApplicationController|FabricRingServices|MonAdminRP|URP|
 |ASAppGateway|FirstTierAggregationService|MonitoringAgent|Használat|
 |AzureBridge|FRP|MonRP|UsageBridge|
 |AzureMonitor|Katalógus|HÁLÓZATI VEZÉRLŐ|virtuális gép|
 |AzureStackBitlocker|Átjáró|Network (Hálózat)|WAS|
 |BareMetal|HealthMonitoring|NonPrivilegedAppGateway|WASBootstrap|
 |BRP|HintingServiceV2|NRP|WASPUBLIC|
 |CA|HRP|OboService|WindowsDefender|
 |CacheService|IBC|OEM|     |
 |Felhő|Identitásszolgáltató|OnboardRP|     |   
 |Fürt|IDN formátumú tartománynevek|A PXE|     |
 |   |   |   |    |


### <a name="bkmk_gui"></a>Naplók gyűjtése az grafikus felhasználói felület használatával
Ahelyett, hogy megadta a szükséges paraméterek a Get-AzureStackLog parancsmaggal kérheti le a naplókat az Azure Stack, hogy kihasználhatja az elérhető, nyílt forráskódú Azure Stack eszközök a fő Azure Stack eszközök GitHub eszközök tárházban címen található http://aka.ms/AzureStackTools.

A **ERCS_AzureStackLogs.ps1** PowerShell-szkript az eszközök GitHub-adattár tárolja, és rendszeresen frissül. Győződjön meg arról, hogy az elérhető legújabb verzióval rendelkezik, le kell töltenie közvetlenül a http://aka.ms/ERCS. Lépések egy felügyeleti PowerShell-munkamenetből, a parancsfájl a kiemelt végponthoz csatlakozik, és futtatja a Get-AzureStackLog megadott paraméterekkel. Ha nincsenek paraméterek vannak megadva, a parancsfájl alapértelmezés szerint a paraméterek a grafikus felhasználói felületen kéri.

További információk a ERCS_AzureStackLogs.ps1 PowerShell-parancsfájl megtekintése [rövid videó](https://www.youtube.com/watch?v=Utt7pLsXEBc) vagy a parancsfájl megtekintése [információs fájl](https://github.com/Azure/AzureStack-Tools/blob/master/Support/ERCS_Logs/ReadMe.md) az Azure Stack eszközök GitHub-adattárában található. 

### <a name="additional-considerations"></a>Néhány fontos megjegyzés

* A parancsnak némi időre melyik szerepkör(ök) gyűjti a naplók alapján futtathatók. Hozzájáruló tényezők is a naplógyűjtés és az Azure Stack-környezet a csomópontok számát a megadott időtartam.
* Gyűjtemény futtatások naplózásához ellenőrizze a létrehozott új mappa a **OutputSharePath** a parancsban megadott paraméter.
* Minden egyes szerepkörhöz naplók belül az egyes zip-fájlokat. Az összegyűjtött naplók méretétől függően egy szerepkör előfordulhat, hogy a naplók a zip-fájlok több bontva. Egy szerepkör egyetlen mappába a kicsomagolt naplófájlok szeretne használni, ha egy eszköz, amely képes csomagolja ki, egyszerre több (például 7zip) használja. Válassza ki a tömörített fájlokat a szerepkörhöz, és válassza ki **itt kinyerése**. Ez unzips a szerepkör egyetlen egyesített mappában található naplófájlokat.
* Fájl neve **Get-AzureStackLog_Output.log** szintén létrejön a ZIP naplófájlokat tartalmazó mappát. Ez a fájl a parancs kimenete, amely során az Erőforrásnapló-gyűjtés problémák elhárításához használható naplóját. Egyes esetekben a naplófájl tartalmazza `PS>TerminatingError` bejegyzéseket, amelyek biztonságosan figyelmen kívül hagyható, kivéve, ha a várt naplófájlok hiányoznak a gyűjtemény futtatások naplózása.
* Egy adott hibák vizsgálatához több összetevőtől naplók lehet szükség.
    -   Rendszer- és az összes infrastruktúra-beli virtuális gépek eseménynaplóinak az gyűjtött a *virtuális gép* szerepkör.
    -   Rendszer- és minden gazdagép eseménynaplóit a gyűjtött a *BareMetal* szerepkör.
    -   A gyűjtött eseménynaplók feladatátvevő fürt és a Hyper-V a *tárolási* szerepkör.
    -   A gyűjtött naplók az ACS a *tárolási* és *ACS* szerepköröket.

> [!NOTE]
> Méret és a korszűrő vannak korlátozva a naplókban gyűjtött, fontos, hogy annak érdekében, nem áramlanak a naplókkal a tárterület hatékony felhasználása érdekében. Azonban a probléma diagnosztizálásakor néha szüksége naplóival, amelyeket már nem létezik ezek a korlátok miatt. Így **erősen ajánlott** , hogy a egy külső tárhelyen (az Azure storage-fiók, egy további helyszíni eszköz stb.) a naplók kiürítési 8 – 12 óránként és láthatóan tartja őket van 1 – 3 hónapos, attól függően, a követelmények. Gondoskodjon arról is, a tárolási hely titkosítása.

## <a name="next-steps"></a>További lépések
[A Microsoft Azure Stack hibaelhárítása](azure-stack-troubleshooting.md)

