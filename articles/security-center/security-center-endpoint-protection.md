---
title: Endpoint Protection-megoldások felderítése és állapotának felmérése Azure Security Centerban | Microsoft Docs
description: Az Endpoint Protection-megoldások felderítése és azonosítása kifogástalanként.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2019
ms.author: v-mohabe
ms.openlocfilehash: 4d3fc90a722b9f4043e891a14b542e6b90c94c55
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881034"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Endpoint Protection-Értékelés és javaslatok a Azure Security Center

Az Endpoint Protection felmérése és ajánlásai a Azure Security Center észlelik és biztosítják az Endpoint Protection-megoldások [támogatott](https://docs.microsoft.com/azure/security-center/security-center-os-coverage#supported-platforms-for-windows-computers-and-vms) verzióinak egészségügyi értékelését. Ez a témakör azokat a forgatókönyveket ismerteti, amelyek az Endpoint Protection-megoldásokra Azure Security Center alapján létrehozzák az alábbi két javaslatot.

* **Endpoint Protection-megoldások telepítése a virtuális gépre**
* **Az Endpoint Protection állapotával kapcsolatos problémák megoldása a gépeken**

## <a name="windows-defender"></a>Windows Defender

* Az **"Endpoint Protection-megoldások telepítése virtuális gépen"** javaslat a [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) futtatásakor jön létre, és **az eredmény AMServiceEnabled: Hamis**

* A **"végpontok elleni védelem állapotával kapcsolatos problémák megoldása a gépeken"** javaslat a [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) futtatásakor jön létre, és a következők valamelyike történik:

  * A következő tulajdonságok közül legalább az egyik hamis:

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Ha a következő tulajdonságok egyike vagy mindkettő nagyobb vagy egyenlő, mint 7.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center Endpoint Protection

* Az **"Endpoint Protection-megoldások telepítése a virtuális gépen"** javaslat a SCEPMpModule importálásakor **("$env:P rogramfiles\microsoft biztonsági Client\MpProvider\MpProvider.psd1")** és a futtatáskor **jön létre. Get-MProtComputerStatus** eredmények a **AMServiceEnabled = false értékkel**

* A **"végpontok elleni védelem állapotával kapcsolatos problémák megoldása a gépeken"** javaslat a **Get-MprotComputerStatus** futtatásakor jön létre, és a következők valamelyike történik:

    * A következő tulajdonságok közül legalább az egyik hamis:

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * Ha a következő aláírások egyike vagy mindkét frissítése nagyobb vagy egyenlő, mint 7. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Ha a következő ellenőrzések közül egy vagy több nem teljesül, az **"Endpoint Protection-megoldások telepítése virtuális gépen"** javaslat jön létre:
    * **HKLM: \ SOFTWARE\TrendMicro\Deep biztonsági ügynök** létezik
    * **HKLM: \ SOFTWARE\TrendMicro\Deep biztonsági Agent\InstallationFolder** létezik
    * A **dsq_query. cmd** fájl a telepítési mappában található.
    * A **dsa_query. cmd** eredményének futtatása az **Component. am. Mode: on-Trend Micro Deep Security Agent észlelve**

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
Az **"Endpoint Protection-megoldások telepítése a virtuális gépen"** javaslat akkor jön létre, ha a következő ellenőrzések bármelyike nem teljesül:

* **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Vagy

* **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

**"Az Endpoint Protection állapotával kapcsolatos problémák megoldása a gépeken"** javaslat akkor jön létre, ha a következő ellenőrzések bármelyike nem teljesül:  

* A Symantec > = 12 verziójának megkeresése:  Beállításjegyzék helye: **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion "-value" PRODUCTVERSION "**

* Valós idejű védelem állapotának keresése: **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff = = 1**

* Aláírás frissítési állapotának ellenőrzését: **HKLM\Software\Symantec\Symantec-végpont Protection\CurrentVersion\public-opstate\LatestVirusDefsDate < = 7 nap**

* Teljes vizsgálat állapotának ellenőrzése: **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime < = 7 nap**

* Az aláírás verziószámának elérési útja a Symantec 12 aláírási verziójának megkereséséhez: **Beállításjegyzékbeli elérési utak + "CurrentVersion\SharedDefs" – érték "SRTSP"** 

* A Symantec 14 aláírási verziójának elérési útja: **Beállításjegyzékbeli elérési utak + "CurrentVersion\SharedDefs\SDSDefs" – érték "SRTSP"**

Beállításjegyzékbeli elérési utak:

* **"HKLM: \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>A Windows rendszerhez készült McAfee Endpoint Protection

Ha a következő ellenőrzések nem teljesülnek, az **"Endpoint Protection-megoldások telepítése virtuális gépen"** javaslat jön létre:

* **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion** létezik

* **HKLM: \ SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

**"Az Endpoint Protection állapotával kapcsolatos problémák megoldása a gépeken"** javaslat akkor jön létre, ha a következő ellenőrzések nem teljesülnek:

* McAfee verziója: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* Aláírás verziójának keresése: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* Aláírási dátum keresése: **HKLM: \ Software\McAfee\AVSolution\DS\DS-Value "szContentCreationDate" > = 7 nap**

* Vizsgálat dátumának keresése: **HKLM: \ Software\McAfee\Endpoint\AV\ODS-Value "LastFullScanOdsRunTime" > = 7 nap**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>A McAfee-végpont biztonsága a Linux-veszélyforrások megelőzésére 

Ha a következő ellenőrzések közül egyet vagy mindkettőt nem teljesíti, a rendszer létrehozza az **Endpoint Protection-megoldások telepítését a virtuális gépen** :  

- A fájl **/opt/ISEC/ENS/threatprevention/bin/isecav** bezárása 

- a **"/opt/ISEC/ENS/threatprevention/bin/isecav--version"** kimenet a következő: **McAfee name = a McAfee Endpoint Security for Linux Threat Prevention és a McAfee verziója > = 10**

Ha a következő ellenőrzések közül egy vagy több nem teljesült, akkor az **Endpoint Protection állapotával kapcsolatos problémák megoldása a gépekre vonatkozó** javaslat.

- **"/opt/ISEC/ENS/threatprevention/bin/isecav--listtask"** – a **gyors vizsgálat, a teljes ellenőrzés** és mindkét vizsgálat eredménye < = 7 nap

- a **"/opt/ISEC/ENS/threatprevention/bin/isecav--listtask"** függvény a **dat és a motor frissítési idejét** , valamint mindkettőt adja vissza < = 7 nap

- **"/opt/ISEC/ENS/threatprevention/bin/isecav--getoasconfig--Summary"** **a hozzáférési vizsgálat** állapotának visszaadása

## <a name="sophos-antivirus-for-linux"></a>A Linux rendszerhez készült Sophos Antivirus 

Ha a következő ellenőrzések közül egyet vagy mindkettőt nem teljesíti, a rendszer létrehozza az **Endpoint Protection-megoldások telepítését a virtuális gépen** :

- A fájl **/opt/Sophos-AV/bin/savdstatus** kilép, vagy megkeresi a **"readlink $ (amely savscan)"** testreszabott helyet

- a **"/opt/Sophos-AV/bin/savdstatus--version"** a Sophos name = **Sophos Anti-Virus és a Sophos > = 9 verzióját** adja vissza.

Ha a következő ellenőrzések közül egy vagy több nem teljesült, akkor az **Endpoint Protection állapotával kapcsolatos problémák megoldása a gépekre vonatkozó** javaslat.

- **"/opt/Sophos-AV/bin/savlog--MaxAge = 7 | GREP-i "ütemezett vizsgálat. a\* (z) "| tail-1" befejeződött**, egy értéket ad vissza.   

- **"/opt/Sophos-AV/bin/savlog--MaxAge = 7 | a GREP "vizsgálat elkészült"** | farok-1 ", egy értéket ad vissza.   

- a **"/opt/Sophos-AV/bin/savdstatus--LastUpdate"** függvény a LastUpdate értéket adja vissza, amelynek < = 7 nap 

- a **"/opt/Sophos-AV/bin/savdstatus-v"** egyenlő **"a hozzáférés-ellenőrzés fut"** értékkel. 

- a **"/opt/Sophos-AV/bin/savconfig Get LiveProtection"** érték engedélyezve  

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A Microsoft antimalware-bővítmény naplói a következő címen érhetők el:  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Vagy egy Azure-támogatási incidenst is betölthet. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
