---
title: AD FS-integráció az Azure Stack ellenőrzése
description: Az Azure Stack-készültségi ellenőrző segítségével ellenőrizze az ADFS-integráció az Azure Stackhez.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: patricka
ms.reviewer: jerskine
ms.openlocfilehash: 0aa13f2fced9122163d5278b5bb50cc1e11a0379
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947268"
---
# <a name="validate-adfs-integration-for-azure-stack"></a>Ellenőrizze az ADFS-integráció az Azure Stackhez

Az Azure Stack készültségi ellenőrző eszköz (AzsReadinessChecker) használatával, hogy az készen áll az AD FS-integráció az Azure Stack használatával. Data center integrálása megkezdése előtt, illetve az Azure Stack üzembe helyezés előtt ellenőrizni kell az AD FS-integráció.

A készenléti ellenőrző ellenőrzi:

* A *összevonási metaadatok* összevonáshoz érvényes XML-elemeket tartalmazza.
* A *AD FS SSL-tanúsítvány* lekérhető és megbízhatósági lánc építhetők fel. A stamp ADFS meg kell bíznia az SSL-tanúsítványok lánc. A tanúsítvánnyal kell aláírni azonos *hitelesítésszolgáltató* az Azure Stack-központitelepítési tanúsítványok vagy egy megbízható legfelső szintű szolgáltató partner által. A megbízható legfelső szintű szolgáltató partnereink teljes listájáért lásd: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca
* *ADFS-aláíró tanúsítvány* megbízható és nem hamarosan lejárati van.

Azure Stack data center integrációval kapcsolatos további információkért lásd: [Azure Stack adatközpont integrációja - identitás](azure-stack-integrate-identity.md)

## <a name="get-the-readiness-checker-tool"></a>A készenléti-ellenőrző eszköz beolvasása

Az Azure Stack készültségi ellenőrző eszköz (AzsReadinessChecker) legújabb verziójának letöltése a [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek vonatkoznak a helyen kell lennie.

**A számítógép, amelyen az eszköz fut:**

* Windows 10-es vagy Windows Server 2016, a tartományi kapcsolat.
* A PowerShell 5.1-es vagy újabb. A verzió ellenőrzéséhez futtassa az alábbi PowerShell-parancsot, és tekintse át a *fő* verzió és *kisebb* verziók:  
   > `$PSVersionTable.PSVersion`
* A legújabb verzióját a [a Microsoft Azure Stack készültségi ellenőrző](https://aka.ms/AzsReadinessChecker) eszközt.

**Az Active Directory összevonási szolgáltatások környezetben:**

Legalább egy, a következő formátumokban metaadatok lesz szüksége:

* Az AD FS összevonási metaadatainak URL-címe. Például: `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`
* Az összevonási metaadatainak XML-fájl. Ha például federationmetadata.XML mintát követi

## <a name="validate-adfs-integration"></a>Ellenőrizze az ADFS-integráció

1. A számítógépen, amely megfelel az előfeltételeknek nyisson meg egy rendszergazdai PowerShell-parancssort, és futtassa a következő parancsot a AzsReadinessChecker telepítéséhez.

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. A PowerShell használatával futtassa a következő érvényesítési elindításához. Adja meg az értékét **- CustomADFSFederationMetadataEndpointUri** , az összevonási metaadatok URI Azonosítóját:

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. Az eszköz futtatása után tekintse át a kimenetet. Győződjön meg arról, a állapota OK: az AD FS integrációs követelményeinek. Sikeres ellenőrzés az alábbihoz hasonlóan jelenik meg.

    ```
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

Éles környezetben tanúsítványláncok egy operátorok munkaállomásról bizalmi kapcsolat tesztelése nem tekinthető a nyilvános kulcsokra épülő infrastruktúra megbízhatósági állapotáról, az Azure Stack-infrastruktúra teljes jelezhetnek. Az Azure Stack-blokk nyilvános VIP-hálózat a kapcsolatot kell létesítenie a visszavont tanúsítványok Listáját a PKI-infrastruktúra.

## <a name="report-and-log-file"></a>A jelentés és-naplófájl

Minden egyes alkalommal érvényesítési fut, az eredmények naplózza **AzsReadinessChecker.log** és **AzsReadinessCheckerReport.json**. Ezek a fájlok helyét jeleníti meg az ellenőrzés eredményét a PowerShellben.

Az érvényesítés fájlok segítségével megoszthatja állapot érvényesítési problémák vizsgálatához vagy az Azure Stack üzembe helyezése előtt. Mindkét fájlt megmarad, egyes további ellenőrzés eredményeit. A jelentés tartalmazza a telepítési csapat megerősítés az identitás-konfiguráció. A naplófájl segíthet a telepítés vagy a támogatási csapat érvényesítési problémák kivizsgálásában.

Alapértelmezés szerint mindkét fájlt írt `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Használat:

* **-OutputPath** *elérési út* végén található egy másik jelentés helyét adja meg a futtatási parancssori paraméter.
* **-CleanReport** törölje a futtatási parancs végén paraméter *AzsReadinessCheckerReport.json* előző jelentés információk. További információkért lásd: [Azure Stack érvényesítési jelentés](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Érvényesítési hibák

Ha egy ellenőrzés nem sikerül, a hibával kapcsolatos részleteket a PowerShell-ablakban jeleníti meg. Az eszköz is naplózza az adatokat a *AzsReadinessChecker.log*.

Az alábbi példák gyakori ellenőrzési hibákat ad útmutatást.

### <a name="command-not-found"></a>A parancs nem található

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**OK** -PowerShell Autoload nem sikerült megfelelően betölteni készenlét ellenőrző modul.

**Feloldási** – importálás készültségi ellenőrző modul explicit módon. Másolja és illessze be az alábbi kódot a Powershellt, és a frissítés a \<verzió\> a jelenleg telepített verzióban verziószámmal.

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>További lépések

[A készültségi jelentés megtekintése](azure-stack-validation-report.md)  
[Általános Azure Stack-integráció szempontok](azure-stack-datacenter-integration.md)  
