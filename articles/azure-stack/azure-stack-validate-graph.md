---
title: Az Azure Graph-integráció az Azure Stack ellenőrzése
description: Az Azure Stack-készültségi ellenőrző segítségével ellenőrizze a graph-integráció az Azure Stackhez.
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
ms.openlocfilehash: e1c1ba0a065a20874bf51d7464cbcfdfa13a571d
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947261"
---
# <a name="validate-graph-integration-for-azure-stack"></a>Ellenőrizze a graph-integráció az Azure Stackhez

Az Azure Stack készültségi ellenőrző eszköz (AzsReadinessChecker) használatával, hogy az készen áll az Azure Stack graph-integráció. Data center integrálása megkezdése előtt, illetve az Azure Stack üzembe helyezés előtt ellenőriznie kell a graph-integráció.

A készenléti ellenőrző ellenőrzi:

* A hitelesítő adatokat a szolgáltatás fiók létrehozása a graph-integráció az Active Directory lekérdezés megfelelő jogosultságokkal rendelkezik.
* A *globális katalógus* feloldható, és felerősítéséhez.
* A KDC feloldható és felerősítéséhez.
* Szükséges hálózati kapcsolat van.

Azure Stack data center integrációval kapcsolatos további információkért lásd: [Azure Stack adatközpont integrációja - identitás](azure-stack-integrate-identity.md)

## <a name="get-the-readiness-checker-tool"></a>A készenléti-ellenőrző eszköz beolvasása

Az Azure Stack készültségi ellenőrző eszköz (AzsReadinessChecker) legújabb verziójának letöltése a [PSGallery](https://aka.ms/AzsReadinessChecker).

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek vonatkoznak a helyen kell lennie.

**A számítógép, amelyen az eszköz fut:**

* Windows 10-es vagy Windows Server 2016, a tartományi kapcsolat.
* A PowerShell 5.1-es vagy újabb. A verzió ellenőrzéséhez futtassa az alábbi PowerShell-parancsot, és tekintse át a *fő* verzió és *kisebb* verziók:  
   > `$PSVersionTable.PSVersion`
* Az Active Directory PowerShell-modul
* A legújabb verzióját a [a Microsoft Azure Stack készültségi ellenőrző](https://aka.ms/AzsReadinessChecker) eszköz

**Az Active Directory-környezetet:**

* A felhasználónevet és jelszót a meglévő Active Directory graph szolgáltatása esetében egy olyan fiók azonosítása
* Az Active Directory-erdő gyökerének teljes tartománynév azonosítása

## <a name="validate-graph"></a>Graph ellenőrzése

1. A számítógépen, amely megfelel az előfeltételeknek nyisson meg egy rendszergazdai PowerShell-parancssort, és futtassa a következő parancsot a AzsReadinessChecker telepítéséhez.

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. A PowerShell parancssorában futtassa a következőt beállítása *$graphCredential* változót a gráfadatbázis-fiók. Cserélje le `contoso\graphservice` a fiók használatakor a `domain\username` formátumban.

    `$graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"`

1. A PowerShell használatával futtassa a következő érvényesítési gráf elindításához. Adja meg az értékét **- ForestFQDN** az erdő legfelső szintű FQDN-ként:

     `Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential`

1. Az eszköz futtatása után tekintse át a kimenetet. Győződjön meg róla, az állapot OK: a graph-integrációs követelményeinek. Sikeres ellenőrzés az alábbihoz hasonlóan jelenik meg:

    ```
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

Éles környezetben operátorok munkaállomásról a hálózati kapcsolat tesztelése nem tekinthető teljes mértékben az Azure Stack számára elérhető kapcsolatok jelezhetnek. Az Azure Stack-blokk nyilvános VIP-hálózat lesz szüksége a kapcsolatot az LDAP-forgalom identitásintegráció végrehajtásához.

## <a name="report-and-log-file"></a>A jelentés és-naplófájl

Minden egyes alkalommal érvényesítési fut, az eredmények naplózza **AzsReadinessChecker.log** és **AzsReadinessCheckerReport.json**. Ezek a fájlok helyét jeleníti meg az ellenőrzés eredményét a PowerShellben.

Az érvényesítés fájlok segítségével megoszthatja állapot érvényesítési problémák vizsgálatához vagy az Azure Stack üzembe helyezése előtt. Mindkét fájlt megmarad, egyes további ellenőrzés eredményeit. A jelentés tartalmazza a telepítési csapat megerősítés az identitás-konfiguráció. A naplófájl segíthet a telepítés vagy a támogatási csapat érvényesítési problémák kivizsgálásában.

Alapértelmezés szerint mindkét fájlt írt `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`

Használat:

* **-OutputPath** *elérési út* végén található egy másik jelentés helyét adja meg a futtatási parancssori paraméter.
* **-CleanReport** törölje a futtatási parancs végén paraméter *AzsReadinessCheckerReport.json* előző jelentés információk. További információkért lásd: [Azure Stack érvényesítési jelentés](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Érvényesítési hibák

Ha egy ellenőrzés nem sikerül, a hibával kapcsolatos részleteket a PowerShell-ablakban jeleníti meg. Az eszköz is naplózza az adatokat a *AzsGraphIntegration.log*.

## <a name="next-steps"></a>További lépések

[A készültségi jelentés megtekintése](azure-stack-validation-report.md)  
[Általános Azure Stack-integráció szempontok](azure-stack-datacenter-integration.md)  
