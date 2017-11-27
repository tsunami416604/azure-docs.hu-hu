---
title: Diagnosztika az Azure Stackben
description: "Azure-készletben diagnosztikai naplófájlok gyűjtéséről"
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 11/22/2017
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 8afde912ca48297ae60eb7d05aa624a1d72c1637
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
---
# <a name="azure-stack-diagnostics-tools"></a>Az Azure verem diagnosztikai eszközök

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*
 
Az Azure verem összetevők együttesen működő és egymáshoz való interakció nagy gyűjteménye. Ezeket az összetevőket a saját egyedi naplófájlokat hoznak létre. Ez tehet diagnosztizálás problémák nehezebb feladat, különösen a több Azure verem összetevőket használják érkező hibák. 

A diagnosztikai eszközök biztosíthatja a napló gyűjtemény módszer használata egyszerű és hatékony. Az alábbi ábrán látható Azure verem munkahelyi hogyan bejelentkezés gyűjtemény eszközök:

![Azure-verem diagnosztikai eszközök](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Nyomkövetési adatgyűjtő
 
A nyomkövetési adatgyűjtő alapértelmezés szerint engedélyezve van. Ez folyamatosan a háttérben fut, és gyűjti össze az összes esemény Windows (nyomkövetés) napló Komponensszolgáltatások Azure veremben és egy közös helyi megosztáson tárolja azokat. 

Fontos tudnivaló a nyomkövetési adatgyűjtő kapcsolatban a következők:
 
* A nyomkövetési adatgyűjtő folyamatosan fut, alapértelmezett mérete korlátokat. Alapértelmezés szerint minden fájl (200 MB) megengedett maximális méret **nem** maximális méretét. A mérete ellenőrzés akkor fordul elő, rendszeres időközönként (jelenleg 2 percenként), és ha az aktuális fájl > = 200 MB, azok mentésekor, és egy új fájl jön létre. Az esemény-munkamenet létrehozott fájlok összesített mérete a is van egy 8 GB (konfigurálható) korlátot. Ha eléri ezt a korlátot, a legrégebbi fájlok törlődnek, amikor újakat hoz létre.
* A naplók egy 5 napos korhatár nincs. Ez a korlátozás akkor is konfigurálható. 
* Minden egyes összetevő nyomkövetési konfiguráció tulajdonságainak meghatározása a JSON-fájl használatával. A JSON-fájlok találhatók **C:\TraceCollector\Configuration**. Ha szükséges, ezeket a fájlokat a gyűjtött naplók korával és mérete határain módosítása szerkeszthető. Ezek a fájlok módosításait kell indítani a *Microsoft Azure verem nyomkövetési adatgyűjtő* szolgáltatás, a módosítások életbe léptetéséhez.

A következő példa egy nyomkövetési konfigurációs JSON-fájlt a XRP virtuális FabricRingServices műveletekhez: 

```json
{
    "LogFile": 
    {
        "SessionName": "FabricRingServicesOperationsLogSession",
        "FileName": "\\\\SU1FileServer\\SU1_ManagementLibrary_1\\Diagnostics\\FabricRingServices\\Operations\\AzureStack.Common.Infrastructure.Operations.etl",
        "RollTimeStamp": "00:00:00",
        "MaxDaysOfFiles": "5",
        "MaxSizeInMB": "200",
        "TotalSizeInMB": "5120"
    },
    "EventSources":
    [
        {"Name": "Microsoft-AzureStack-Common-Infrastructure-ResourceManager" },
        {"Name": "Microsoft-OperationManager-EventSource" },
        {"Name": "Microsoft-Operation-EventSource" }
    ]
}
```

* **MaxDaysOfFiles**. Ezzel a paraméterrel állítható fájlokat, és tarthatja korát. Törlődnek a régi naplófájlokat.
* **Maxsizeinmb értéknél**. Ezzel a paraméterrel állítható a adatbázisméret küszöbértéke egy fájl. Ha a méretet, a rendszer egy új .etl fájl jön létre.
* **TotalSizeInMB**. Ezzel a paraméterrel állítható egy esemény-munkamenet az .etl fájlok teljes mérete. Ha a fájlok összesített mérete nagyobb, mint a paraméter értéke, a rendszer törli régebbi fájlokat.
  
## <a name="log-collection-tool"></a>Napló gyűjtemény eszköz
 
A PowerShell-paranccsal **Get-AzureStackLog** gyűjteni a összetevőit Azure verem környezetben is használható. Azokat a zip-fájloknak a felhasználó által definiált helyre menti. Ha a technikai támogatási csapat a naplók megoldhatja a problémát, akkor megkérheti, hogy az eszköz futtatásához.

> [!CAUTION]
> Ezekben a naplófájlokban tartalmazhatnak személyes azonosításra alkalmas adatokat (PII). Ez figyelembe kell venni a könyvelés nyilvánosan minden naplófájl előtt.
 
Az alábbiakban néhány példa napló típust összegyűjtött:
*   **Az Azure verem telepítési naplói**
*   **Windows-Eseménynapló**
*   **Panther naplók**
*   **Fürt naplók**
*   **Diagnosztikai naplók tárolási**
*   **ETW-naplók**

Ezeket a fájlokat a nyomkövetés-gyűjtő által gyűjtött, és honnan megosztáson található **Get-AzureStackLog** olvassa be ezeket.
 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Get-AzureStackLog futtathatnak egy Azure verem Development Kit (ASDK)
1. Jelentkezzen be **AzureStack\CloudAdmin** a gazdagépen.
2. Nyissa meg rendszergazdaként egy PowerShell-ablakot.
3. Futtassa a **Get-AzureStackLog** PowerShell-parancsmagot.

   **Példák**

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

### <a name="to-run-get-azurestacklog-on-an-azure-stack-integrated-system"></a>Futtassa a Get-AzureStackLog egy Azure veremben integrált a rendszer

A napló gyűjtemény eszköz futtatásához az integrált rendszeren, akkor hozzáféréssel kell rendelkeznie a Rendszerjogosultságú végpont (EGP). Íme egy példa parancsfájl használata a EGP gyűjtött naplók az integrált rendszeren is futtathatja:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputPath "\\<HLH MACHINE ADDRESS>\c$\logs" -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- Amikor naplóinak gyűjtése az EGP, adja meg a **OutputPath** paraméternek kell lennie egy helyet, a hardver életciklus állomás (HLH) számítógépen. Bizonyosodjon meg arról, hogy a hely titkosítva van.
- A paraméterek **OutputSharePath** és **OutputShareCredential** megadása nem kötelező, és akkor használatosak, ha a naplók feltöltése egy külső megosztott mappába. Ezekkel a paraméterekkel *továbbá* való **OutputPath**. Ha **OutputPath** nincs megadva, a napló gyűjtemény eszköz a rendszermeghajtó EGP VM használja a tároláshoz. Ez a parancsfájl sikertelen lesz, mivel a lemezterület korlátozott okozhat.
- Ahogy az előző példában a **FromDate** és **ToDate** paraméterek segítségével naplógyűjtéshez egy adott időszakra vonatkozóan. Ez származhatnak helyzetekben, például az integrált rendszeren frissítések alkalmazása után a naplók gyűjtésére vonatkozó lesz szüksége.

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>ASDK és integrált rendszerek mindkét paraméter szempontjai

- Ha a **FromDate** és **ToDate** paraméter nincs megadva, a naplók alapértelmezés szerint az elmúlt négy óra összegyűjtését.
- Használhatja a **TimeOutInMinutes** paraméter segítségével állítsa be a lekérdezés időkorlátját. Alapértelmezés szerint a 150 (2,5 óra) érték.

- Jelenleg, használhatja a **FilterByRole** paraméter szűrő napló gyűjteményhez a következő szerepkörök:

   |   |   |   |
   | - | - | - |
   | ACSMigrationService     | ACSMonitoringService   | ACSSettingsService |
   | ACS                     | ACSFabric              | ACSFrontEnd        |
   | ACSTableMaster          | ACSTableServer         | ACSWac             |
   | ADFS                    | ASAppGateway           | BareMetal          |
   | BRP                     | HITELESÍTÉSSZOLGÁLTATÓ                     | CPI                |
   | KSZT                     | DeploymentMachine      | DHCP               |
   | Tartomány                  | DOKUMENTUMOKAT                    | ECESeedRing        | 
   | FabricRing              | FabricRingServices     | FRP                |
   | Átjáró                 | HealthMonitoring       | HRP                |   
   | IBC                     | InfraServiceController |KeyVaultAdminResourceProvider|
   | KeyVaultControlPlane    | KeyVaultDataPlane      | HÁLÓZATI VEZÉRLŐ ÁLTAL                 |   
   | NonPrivilegedAppGateway | NRP-BEN                    | SeedRing           |
   | SeedRingServices        | SLB                    | SQL                |   
   | AZ SRP                     | Storage                | StorageController  |
   | URP                     | UsageBridge            | virtuális gép    |  
   | VOLT                     | WASPUBLIC              | A WDS                |


### <a name="additional-considerations"></a>Néhány fontos megjegyzés

* A parancs bizonyos idő alapján futtassa mely szerepkör(ök) gyűjti a naplókat. Tényezőkről is naplógyűjtést, és az Azure-verem környezete számait megadott időtartamot.
* Naplógyűjtést befejezése után ellenőrizze az új mappa létrehozása a **OutputPath** a parancsban megadott paraméter.
* Minden szerepkörhöz naplók belül egyedi zip fájlt. A gyűjtött naplók méretétől függően a szerepkör lehet osztani, több zip-fájl a naplók. Egy szerepkör egyetlen mappába a unzipped naplófájlok szeretne használni, ha egy is csomagolja ki egyszerre több (például 7zip) eszközt használja. Válassza ki a szerepkör összes zip fájlt, és válassza ki **kibontása Itt**. Ez unzips, egyetlen egyesített mappa szerepkörre a rendszernapló fájljaiban.
* Fájl neve **Get-AzureStackLog_Output.log** zip naplófájlokat tartalmazó mappa is létrejön. A fájl a parancs kimenete, amely hibaelhárítás során naplógyűjtést esetén használható a naplófájlt.
* Vizsgálja meg a hiba, naplók egynél több összetevő lehet szükség.
    -   Rendszer- és infrastruktúra virtuális gépeinek eseménynaplóiban keresse meg a rendszer gyűjti a *: VirtualMachines* szerepkör.
    -   Rendszer és minden állomás eseménynaplóiban keresse meg a rendszer gyűjti a *BareMetal* szerepkör.
    -   Feladatátvevő fürt és a Hyper-V eseménynaplóit a rendszer gyűjti a *tárolási* szerepkör.
    -   Az ACS-naplókat a rendszer gyűjti a *tárolási* és *ACS* szerepkörök.

> [!NOTE]
> Méret és a korszűrő vannak korlátozva a gyűjtött, mivel a tárhely biztosításához a naplók árasztott nem get lehessen a hatékony működést annak biztosítására, hogy a naplók. Azonban ha a probléma diagnosztizálása néha szüksége naplófájlokat, amelyek már nem létezik a korlátok miatt. Így **erősen ajánlott** , hogy a egy külső tárhelyen (egy Azure storage-fiókot, egy további helyszíni tárolóeszközre stb.) a naplók kiürítési 8 – 12 óránként és láthatóan tartja őket nincs 1-3 hónapig, attól függően, a követelmények. Gondoskodjon arról is, a tárolási hely titkosítva van.

## <a name="next-steps"></a>Következő lépések
[A Microsoft Azure verem hibaelhárítása](azure-stack-troubleshooting.md)
