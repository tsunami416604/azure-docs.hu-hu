---
title: Diagnosztika az Azure Stackben
description: Azure-készletben diagnosztikai naplófájlok gyűjtéséről
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 06/27/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 50fef25a3b7b71821e64638729eb8d93f65b9e31
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063848"
---
# <a name="azure-stack-diagnostics-tools"></a>Az Azure verem diagnosztikai eszközök

Az Azure verem összetevők együttesen működő és egymáshoz való interakció nagy gyűjteménye. Ezeket az összetevőket a saját egyedi naplófájlokat hoznak létre. Ez tehet diagnosztizálás problémák nehezebb feladat, különösen a több Azure verem összetevőket használják érkező hibák. 

A diagnosztikai eszközök biztosíthatja a napló gyűjtemény módszer használata egyszerű és hatékony. Az alábbi ábrán látható Azure verem munkahelyi hogyan bejelentkezés gyűjtemény eszközök:

![Azure-verem diagnosztikai eszközök](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Nyomkövetési adatgyűjtő
 
A nyomkövetési adatgyűjtő alapértelmezés szerint engedélyezve van, és minden esemény Windows (nyomkövetés) gyűjteni Komponensszolgáltatások Azure verem háttérben fut, folyamatosan. Egy közös helyi megosztás mással egy öt nap korhatár ETW naplók tárolja. Ha eléri ezt a korlátot, a legrégebbi fájlok törlődnek, amikor újakat hoz létre. Az alapértelmezett maximális engedélyezett az egyes fájlok mérete 200 MB. A méret ellenőrzés 2 percenként történik, és ha az aktuális fájl > = 200 MB, azok mentésekor, és egy új fájl jön létre. Az esemény-munkamenet létrehozott fájlok összesített mérete a is van egy 8 GB-os korlátot. 

## <a name="log-collection-tool"></a>Napló gyűjtemény eszköz
 
A PowerShell-parancsmag **Get-AzureStackLog** gyűjteni a összetevőit Azure verem környezetben is használható. Azokat a zip-fájloknak a felhasználó által definiált helyre menti. Ha az Azure-verem technikai támogatást nyújtó csoportnak a naplók megoldhatja a problémát, akkor megkérheti, hogy az eszköz futtatásához.

> [!CAUTION]
> Ezekben a naplófájlokban tartalmazhatnak személyes azonosításra alkalmas adatokat (PII). Ez figyelembe kell venni a könyvelés nyilvánosan minden naplófájl előtt.
 
Az alábbiakban néhány példa napló típust összegyűjtött:
*   **Az Azure verem telepítési naplói**
*   **Windows-Eseménynapló**
*   **Panther naplók**
*   **Fürt naplók**
*   **Diagnosztikai naplók tárolási**
*   **ETW-naplók**

Ezeket a fájlokat vannak, és menti a megosztási nyomkövetési gyűjtő által. A **Get-AzureStackLog** PowerShell-parancsmag felhasználható gyűjtéséhez őket, amikor erre szükség van.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Futtassa a Get-AzureStackLog Azure veremben integrált a rendszerek 
A napló gyűjtemény eszköz futtatásához az integrált rendszeren, akkor hozzáféréssel kell rendelkeznie a Rendszerjogosultságú végpont (EGP). Íme egy példa parancsfájl használata a EGP gyűjtött naplók az integrált rendszeren is futtathatja:

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

- A paraméterek **OutputSharePath** és **OutputShareCredential** használják a naplók feltöltése egy külső megosztott mappába.
- Ahogy az előző példában a **FromDate** és **ToDate** paraméterek segítségével naplógyűjtéshez egy adott időszakra vonatkozóan. Ez származhatnak helyzetekben, például az integrált rendszeren frissítések alkalmazása után a naplók gyűjtésére vonatkozó lesz szüksége.


 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Get-AzureStackLog futtathatnak egy Azure verem Development Kit (ASDK)
1. Jelentkezzen be a **AzureStack\CloudAdmin** a gazdagépen.
2. Nyissa meg rendszergazdaként egy PowerShell-ablakot.
3. Futtassa a **Get-AzureStackLog** PowerShell-parancsmagot.

**Példák:**

  Az összes szerepkör minden naplógyűjtéshez:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs
  ```

  Gyűjteni a virtuális gép és BareMetal szerepkörök:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal
  ```

  Virtuális gép és BareMetal szerepkörök, az a dátum az előző 8 óra naplófájlok szűrést naplóinak gyűjtése:
    
  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  Virtuális gép és BareMetal szerepkörök, az a dátum szerinti 8 óra telt el és 2 óra telt el közötti időszakban naplófájlok szűrést naplóinak gyűjtése:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>ASDK és integrált rendszerek mindkét paraméter szempontjai

- Ha a **FromDate** és **ToDate** paraméter nincs megadva, a naplók alapértelmezés szerint az elmúlt négy óra összegyűjtését.
- Használhatja a **TimeOutInMinutes** paraméter segítségével állítsa be a lekérdezés időkorlátját. Alapértelmezés szerint a 150 (2,5 óra) érték.
- 1805 és újabb verziójában memóriakép fájl naplógyűjtést alapértelmezés szerint le van tiltva. Az engedélyezéséhez használja a **IncludeDumpFile** paraméter váltani. 
- Jelenleg, használhatja a **FilterByRole** paraméter szűrő napló gyűjteményhez a következő szerepkörök:

   |   |   |   |
   | - | - | - |
   | ACS                    | DeploymentMachine                | HÁLÓZATI VEZÉRLŐ ÁLTAL                         |
   | ACSBlob                | DiskRP                           | Network (Hálózat)                    |
   | ACSFabric              | Tartomány                           | NonPrivilegedAppGateway    |
   | ACSFrontEnd            | ECE                              | NRP                        |
   | ACSMetrics             | ExternalDNS                      | OEM                        |
   | ACSMigrationService    | Fabric                           | PXE                        |
   | ACSMonitoringService   | FabricRing                       | SeedRing                   | 
   | ACSSettingsService     | FabricRingServices               | SeedRingServices           |
   | ACSTableMaster         | FRP                              | SLB                        |   
   | ACSTableServer         | Katalógus                          | SlbVips                    |
   | ACSWac                 | Átjáró                          | SQL                        |   
   | ADFS                   | HealthMonitoring                 | SRP                        |
   | ASAppGateway           | HRP                              | Storage                    |   
   | NCAzureBridge          | IBC                              | Tárfiókok            |    
   | AzurePackConnector     | IdentityProvider                 | StorageController          |  
   | AzureStackBitlocker    | IDN                             | Bérlő                     |
   | BareMetal              | InfraServiceController           | TraceCollector             |
   | BRP                    | Infrastruktúra                   | URP                        |
   | CA                     | KeyVaultAdminResourceProvider    | UsageBridge                |
   | Felhő                  | KeyVaultControlPlane             | virtuális gép            |
   | Fürt                | KeyVaultDataPlane                | WAS                        |
   | Compute                | KeyVaultInternalControlPlane     | WASBootstrap               |
   | CPI                    | KeyVaultInternalDataPlane        | WASPUBLIC                  |
   | KSZT                    | KeyVaultNamingService            |                            |
   | DatacenterIntegration  | MonitoringAgent                  |                            |
   |                        |                                  |                            |

### <a name="bkmk_gui"></a>Naplógyűjtéshez egy grafikus felhasználói felület használatával
Ahelyett, hogy a Get-AzureStackLog parancsmag Azure verem naplók beolvasása szükséges paraméterek biztosít, is kihasználhatja a rendelkezésre álló nyílt forráskódú Azure verem eszközök található, a fő Azure verem eszközök GitHub eszközök tárház: http://aka.ms/AzureStackTools.

A **ERCS_AzureStackLogs.ps1** PowerShell-parancsfájl a GitHub-tárházban eszközök tárolja, és rendszeresen frissül. Annak érdekében, hogy az elérhető legújabb verzióra van, le kell töltenie azt közvetlenül a http://aka.ms/ERCS. Indította el egy felügyeleti PowerShell-munkamenetet, a parancsfájl a rendszerjogosultságú végpont csatlakozik, és futtatja a Get-AzureStackLog megadott paraméterekkel. Ha paraméter nélkül van megadva, a parancsfájl alapértelmezés szerint az adatkérés paraméterek grafikus felhasználói felületen keresztül.

További információt a ERCS_AzureStackLogs.ps1 PowerShell-parancsfájlt, figyelheti az [egy rövid videót](https://www.youtube.com/watch?v=Utt7pLsXEBc) vagy a parancsfájl megtekintése [információs fájl](https://github.com/Azure/AzureStack-Tools/blob/master/Support/ERCS_Logs/ReadMe.md) a Azure verem eszközök GitHub-tárházban található. 

### <a name="additional-considerations"></a>Néhány fontos megjegyzés

* A parancs bizonyos idő alapján futtassa mely szerepkör(ök) gyűjti a naplókat. Tényezőkről is naplógyűjtést, és az Azure-verem környezete számait megadott időtartamot.
* A log gyűjtemény fut, ellenőrizze az új mappa létrehozása a **OutputSharePath** a parancsban megadott paraméter.
* Minden szerepkörhöz naplók belül egyedi zip fájlt. A gyűjtött naplók méretétől függően a szerepkör lehet osztani, több zip-fájl a naplók. Egy szerepkör egyetlen mappába a unzipped naplófájlok szeretne használni, ha egy is csomagolja ki egyszerre több (például 7zip) eszközt használja. Válassza ki a szerepkör összes zip fájlt, és válassza ki **kibontása Itt**. Ez unzips, egyetlen egyesített mappa szerepkörre a rendszernapló fájljaiban.
* Fájl neve **Get-AzureStackLog_Output.log** zip naplófájlokat tartalmazó mappa is létrejön. A fájl a parancs kimenete, amely hibaelhárítás során naplógyűjtést esetén használható a naplófájlt. Egyes esetekben a naplófájl tartalmazza `PS>TerminatingError` bejegyzéseket tartalmaz, amelyek biztonságosan figyelmen kívül hagyható, kivéve, ha hiányoznak a várt naplófájlok után jelentkezzen gyűjtemény futtatása.
* Vizsgálja meg a hiba, naplók egynél több összetevő lehet szükség.
    -   Rendszer- és infrastruktúra virtuális gépeinek eseménynaplóiban keresse meg a rendszer gyűjti a *: VirtualMachines* szerepkör.
    -   Rendszer és minden állomás eseménynaplóiban keresse meg a rendszer gyűjti a *BareMetal* szerepkör.
    -   Feladatátvevő fürt és a Hyper-V eseménynaplóit a rendszer gyűjti a *tárolási* szerepkör.
    -   Az ACS-naplókat a rendszer gyűjti a *tárolási* és *ACS* szerepkörök.

> [!NOTE]
> Méret és a korszűrő vannak korlátozva a gyűjtött, mivel a tárhely biztosításához a naplók árasztott nem get lehessen a hatékony működést annak biztosítására, hogy a naplók. Azonban ha a probléma diagnosztizálása néha szüksége naplófájlokat, amelyek már nem létezik a korlátok miatt. Így **erősen ajánlott** , hogy a egy külső tárhelyen (egy Azure storage-fiókot, egy további helyszíni tárolóeszközre stb.) a naplók kiürítési 8 – 12 óránként és láthatóan tartja őket nincs 1-3 hónapig, attól függően, a követelmények. Gondoskodjon arról is, a tárolási hely titkosítva van.

## <a name="next-steps"></a>További lépések
[A Microsoft Azure verem hibaelhárítása](azure-stack-troubleshooting.md)

