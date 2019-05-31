---
title: Endpoint protection megoldások felderítés és az egészségügyi értékelése az Azure Security Centerben |} A Microsoft Docs
description: A végpontvédelmi megoldások hogyan felderítésének és kifogástalan állapotúként azonosított.
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
ms.date: 05/23/2019
ms.author: v-mohabe
ms.openlocfilehash: b17e5f16b988bfa562b00bc6f5b9dfd34be4ca43
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247964"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Endpoint protection-értékelés és javaslatok az Azure Security Centerben

Endpoint protection-értékelés és javaslatok az Azure Security Center észleli, és az egészségügyi értékelési keretrendszert biztosít [támogatott](https://docs.microsoft.com/azure/security-center/security-center-os-coverage#supported-platforms-for-windows-computers-and-vms) Végpontvédelmi megoldások verzióit. Ez a témakör ismerteti, hogy a Végpontvédelmi megoldások az Azure Security Center a következő két javaslatok létrehozása.

* **A virtuális gépekre telepített végpontvédelmi megoldások**
* **Oldja meg az endpoint protection állapotbeli problémák a gépeken**

## <a name="windows-defender"></a>Windows Defender

* A **"A végpontvédelmi megoldások telepítése a virtuális gép"** javaslat jön létre, amikor [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) futtatások és az eredmény **AMServiceEnabled: FALSE (hamis)**

* A **"Az endpoint protection állapotbeli problémák a gépeken megoldása"** javaslat jön létre, amikor [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) futtatások és valamelyike vagy mindegyike a következő történik:

  * A következő tulajdonságok közül legalább a False (hamis):

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Ha egyik vagy mindkét a következő tulajdonságok nagyobb vagy egyenlő a 7.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>A Microsoft System Center endpoint protection

* A **"A végpontvédelmi megoldások telepítése a virtuális gép"** javaslat jön létre importálásakor **SCEPMpModule ("$env: ProgramFiles\Microsoft biztonsági Client\MpProvider\MpProvider.psd1")** és futó **Get-MProtComputerStatus** az eredmények **AMServiceEnabled = false**

* A **"Az endpoint protection állapotbeli problémák a gépeken megoldása"** javaslat jön létre, amikor **Get-MprotComputerStatus** futtatások és valamelyike vagy mindegyike a következő történik:

    * A következő tulajdonságok közül legalább a False (hamis):

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * Egyikét vagy mindkettőt a következő aláírást frissítések-e nagyobb vagy egyenlő a 7. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* A **"A végpontvédelmi megoldások telepítése a virtuális gép"** javaslat generál, ha egy vagy több, a következő ellenőrzések nem teljesülnek:
    * **HKLM:\SOFTWARE\TrendMicro\Deep biztonsági ügynök** létezik
    * **HKLM:\SOFTWARE\TrendMicro\Deep biztonsági Agent\InstallationFolder** létezik
    * A **dsq_query.cmd** fájl megtalálható a telepítési mappa
    * Futó **dsa_query.cmd** az eredmények **Component.AM.mode: a - Trend Micro Deep Security Agent észlelt**

## <a name="symantec-endpoint-protection"></a>A Symantec endpoint protection
A **"A végpontvédelmi megoldások telepítése a virtuális gép"** javaslat akkor jön létre, ha nem teljesülnek az alábbi ellenőrzéseket bármelyikét:

* **HKLM:\Software\Symantec\Symantec végpont Protection\CurrentVersion\PRODUCTNAME = "A Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Vagy

* **HKLM:\Software\Wow6432Node\Symantec\Symantec végpont Protection\CurrentVersion\PRODUCTNAME = "A Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

A **"Az endpoint protection állapotbeli problémák a gépeken megoldása"** javaslat akkor jön létre, ha nem teljesülnek az alábbi ellenőrzéseket bármelyikét:  

* Ellenőrizze a Symantec verzió > = 12:  Beállításjegyzék helye: **HKLM:\Software\Symantec\Symantec végpont Protection\CurrentVersion"-"PRODUCTVERSION"érték**

* Ellenőrizze a valós idejű védelem állapota: **HKLM:\Software\Wow6432Node\Symantec\Symantec végpont Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Ellenőrizze az adatbázis-frissítés állapotát: **HKLM\Software\Symantec\Symantec végpont Protection\CurrentVersion\public-opstate\LatestVirusDefsDate < = 7 nap**

* Ellenőrizze a teljes vizsgálat állapota: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 days**

* Adatbázis verziója aláírás verziószám elérési út keresése Symantec 12: **Beállításjegyzékbeli elérési út + "CurrentVersion\SharedDefs"-"SRTSP" érték** 

* A Symantec 14 aláírás-verzió elérési útja: **Beállításjegyzékbeli elérési út + "CurrentVersion\SharedDefs\SDSDefs"-"SRTSP" érték**

Beállításjegyzékbeli elérési út:

**"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path; ** 
 **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee endpoint protection Windows

A **"A végpontvédelmi megoldások telepítése a virtuális gép"** javaslat akkor jön létre, ha az alábbi ellenőrzéseket nem teljesülnek:

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** létezik

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

A **"Az endpoint protection állapotbeli problémák a gépeken megoldása"** javaslat akkor jön létre, ha az alábbi ellenőrzéseket nem teljesülnek:

* McAfee verziója: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* Keresse meg az adatbázis verziója: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* Keresse meg az aláírás dátuma: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 days**

* Keresse meg a vizsgálati dátuma: **HKLM:\Software\McAfee\Endpoint\AV\ODS-érték "LastFullScanOdsRunTime" > = 7 nap**

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A Microsoft Antimalware bővítmény naplók webhelyen érhetők el:  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Vagy egy Azure-támogatási esemény fájl is. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
