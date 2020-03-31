---
title: Végpontvédelmi javaslatok az Azure biztonsági központokban
description: Hogyan fedeznek fel és azonosítják a végpontvédelmi megoldásokat kifogástalanállapotúként.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: dcf7df501665ea3885d00b9f7668a95cbbf02428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208542"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Végpontvédelem felmérése és javaslatok az Azure Security Centerben

Az Azure Security Center az Endpoint-védelmi megoldások [támogatott](security-center-services.md#endpoint-supported) verzióinak állapotfelmérését tartalmazza. Ez a cikk azokat az eseteket ismerteti, amelyek alapján a Security Center a következő két javaslatot hozza létre:

* **Végpontvédelmi megoldások telepítése a virtuális gépre**
* **Végpontvédelmi egészségügyi problémák megoldása a gépeken**

## <a name="windows-defender"></a>Windows Defender

* A Security Center azt javasolja, hogy a [Get-MpComputerStatus futtatásakor](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) **"Végpontvédelmi megoldások telepítése a virtuális számítógépen"** értéket javasol, és az eredmény **AMServiceEnabled: False**

* A Security Center azt javasolja, hogy a [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) **futtatásakor "A végpontok védelmével kapcsolatos egészségügyi problémák megoldása"** a Leget használva, és az alábbi műveletek bármelyike következzen be:

  * Az alábbi tulajdonságok bármelyike hamis:

    **Amserviceenabled**

    **Kémprogram-elhárító**

    **RealTimeProtectionEnabled**

    **BehaviorMonitorEnabled**

    **IoavProtectionEnabled**

    **OnAccessProtectionEnabled**

  * Ha az alábbi tulajdonságok egyike vagy mindkettő 7 vagy több.

    **Kémprogram-elhárítóSignatureAge**

    **VíruskeresőSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center végpontvédelem

* A Security Center az **SCEPMpModule ("$env:ProgramFiles\Microsoft Security Client\MpProvider\MpProvider.psd1")** importálásakor és az **AMServiceEnabled = false** parancãing futtatásakor a **Get-MProtComputerStatus** **"Végpontvédelmi megoldások telepítése a virtuális számítógépen"** parancsot javasolja.

* A Security Center azt javasolja, hogy a **Get-MprotComputerStatus** **futtatásakor "A végpontok védelmével kapcsolatos egészségügyi problémák megoldása"** a Get-MprotComputerStatus futtatásakor, és az alábbi műveletek bármelyike következik be:

    * Az alábbi tulajdonságok közül legalább egy hamis:

            **AMServiceEnabled**

            **AntispywareEnabled**
    
            **RealTimeProtectionEnabled**
    
            **BehaviorMonitorEnabled**
    
            **IoavProtectionEnabled**
    
            **OnAccessProtectionEnabled**
          
    * Ha az alábbi aláírásfrissítések egyike vagy mindkettő 7-es vagy annálnagyobb. 

            **AntispywareSignatureAge**
    
            **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Mikro

* A Security Center azt javasolja, hogy **"Végpontvédelmi megoldások telepítése a virtuális gépen"** azt javasolja, hogy ha az alábbi ellenőrzések valamelyike nem teljesül:
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent** létezik
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** létezik
    * A **dsa_query.cmd** fájl a telepítési mappában található
    * **dsa_query.cmd-eredmények** futtatása **a Component.AM.mode függem le: be- Trend Micro Deep Security Agent észlelve**

## <a name="symantec-endpoint-protection"></a>Symantec végpontvédelem
A Security Center azt javasolja, hogy **"Végpontvédelmi megoldások telepítése a virtuális gépen"** azt javasolja, hogy ha az alábbi ellenőrzések valamelyike nem teljesül:

* **HKLM:\Software\Symantec\Symantec végpontvédelem\CurrentVersion\PRODUCTNAME = "Symantec végpontvédelem"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Vagy

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Végpontvédelem\CurrentVersion\PRODUCTNAME = "Symantec végpontvédelem"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

A Security Center azt javasolja, hogy **"A végpontok védelmével kapcsolatos egészségügyi problémák megoldása a gépeken"** azt javasolja, hogy ha az alábbi ellenőrzések valamelyike nem teljesül:

* Ellenőrizze a Symantec >= 12: Rendszerleíró adatbázis helye: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* Valós idejű védelem állapotának ellenőrzése: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Aláírásfrissítés állapotának ellenőrzése: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 nap**

* Teljes vizsgálat állapotának ellenőrzése: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 nap**

* Aláírásverziószám keresése A Symantec 12 aláírási verziójának elérési útja: **Rendszerleíró útvonalak+ "CurrentVersion\SharedDefs" -Érték "SRTSP"** 

* A Symantec 14 aláírási verziójának elérési útja: **Rendszerleíró útvonalak+ "CurrentVersion\SharedDefs\SDSDefs" -Érték "SRTSP"**

Rendszerleíró adatbázis elérési útjai:

* **"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee végpontvédelem Windows rendszeren

A Security Center azt javasolja, hogy **"Végpontvédelmi megoldások telepítése a virtuális gépen"** azt javasolja, hogy ha az alábbi ellenőrzések valamelyike nem teljesül:

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** létezik

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

A Security Center azt javasolja, hogy **"A végpontok védelmével kapcsolatos egészségügyi problémák megoldása a gépeken"** azt javasolja, hogy ha az alábbi ellenőrzések valamelyike nem teljesül:

* McAfee verzió: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* Aláírási verzió keresése: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* Aláírás dátumának keresése: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 nap**

* A vizsgálat dátumának keresése: **HKLM:\Software\McAfee\Endpoint\AV\ODS -Value "LastFullScanOdsRunTime" >= 7 nap**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>McAfee Végpont biztonság a Linux fenyegetésmegelőzés 

A Security Center azt javasolja, hogy **"Végpontvédelmi megoldások telepítése a virtuális gépen"** azt javasolja, hogy ha az alábbi ellenőrzések valamelyike nem teljesül:

- Fájl **/opt/isec/ens/threatprevention/bin/isecav** kilép 

- **"/opt/isec/ens/threatprevention/bin/isecav --version"** kimenet: **McAfee name = McAfee Endpoint Security for Linux Threat Prevention és McAfee version >= 10**

A Security Center azt javasolja, hogy **"A végpontok védelmével kapcsolatos egészségügyi problémák megoldása a gépeken"** azt javasolja, hogy ha az alábbi ellenőrzések valamelyike nem teljesül:

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** visszaadja **a gyors vizsgálatot,** a teljes vizsgálatot és mindkét vizsgálatot <= 7 nap

- **A "/opt/isec/ens/threatprevention/bin/isecav --listtask"** visszaadja a **DAT és a motor frissítési idejét,** és mindkettő <= 7 nap

- **"/opt/isec/ens/threatprevention/bin/isecav --getoasconfig --summary" returns On Access Scan status "/opt/isec/ens/threatprevention/bin/isecav --getoasconfig --summary"** returns **On Access Scan** status

## <a name="sophos-antivirus-for-linux"></a>Sophos Víruskereső Linux 

A Security Center azt javasolja, hogy **"Végpontvédelmi megoldások telepítése a virtuális gépen"** azt javasolja, hogy ha az alábbi ellenőrzések valamelyike nem teljesül:

- Fájl **/opt/sophos-av/bin/savdstatus** kilép, vagy keressen személyre szabott helyet **"readlink $(amely savscan)"**

- **A "/opt/sophos-av/bin/savdstatus --version"** visszaadja a Sophos-nevet = **Sophos Anti-Virus és Sophos verzió >= 9**

A Security Center azt javasolja, hogy **"A végpontok védelmével kapcsolatos egészségügyi problémák megoldása a gépeken"** azt javasolja, hogy ha az alábbi ellenőrzések valamelyike nem teljesül:

- **"/opt/sophos-av/bin/savlog --maxage=7 | grep -i "Ütemezett vizsgálat . \* befejezett" | -1" farok**, értéket ad eredményül

- **"/opt/sophos-av/bin/savlog --maxage=7 | grep "scan kész"** | -1", értéket ad eredményül

- **"/opt/sophos-av/bin/savdstatus --lastupdate"** visszaadja a lastUpdate-et, amelynek <= 7 napnak kell lennie 

- **A "/opt/sophos-av/bin/savdstatus -v"** egyenlő a **"On-access scanning is running"** 

- **"/opt/sophos-av/bin/savconfig get LiveProtection"** returns enabled

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A Microsoft Kártevőirtó bővítménynaplói a következő helyen érhetők el: **%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Vagy PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>Támogatás

További segítségért forduljon az Azure szakértőihez az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Vagy fájl egy Azure-támogatási incidens. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza a Támogatás beszerezni lehetőséget. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.