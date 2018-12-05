---
title: Azure-regisztráció ellenőrzése az Azure Stackhez |} A Microsoft Docs
description: Az Azure Stack-készültségi ellenőrző használatával Azure-regisztráció ellenőrzése.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/04/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 209793545078a47f91e8fe34c3f85ffb671a2bdb
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888171"
---
# <a name="validate-azure-registration"></a>Azure-regisztráció ellenőrzése 
Az Azure Stack készültségi ellenőrző eszköz (AzsReadinessChecker) használatával ellenőrizze, hogy az Azure Stack használatra készen áll-e az Azure-előfizetésében. Az Azure Stack központi telepítésének megkezdése előtt a regisztráció érvényesítése. A készenléti ellenőrző ellenőrzi:
- Az Azure-előfizetést használ olyan támogatott. Előfizetések egy Felhőszolgáltató (CSP) vagy a nagyvállalati szerződés (EA) kell lennie. 
- Regisztrálja az előfizetését az Azure-ral használt fióknak bejelentkezhet az Azure-ba, és a egy előfizetés tulajdonosa. 

Azure Stack-regisztráció kapcsolatos további információkért lásd: [regisztrálása az Azure Stack az Azure-ral](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>A készenléti-ellenőrző eszköz beolvasása
Az Azure Stack készültségi ellenőrző eszköz (AzsReadinessChecker) legújabb verziója érhető el a letöltési [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Előfeltételek
A következő előfeltételek vonatkoznak a helyen kell lennie.

**A számítógép, amelyen az eszköz fut:**
 - Windows 10-es vagy Windows Server 2016, az internetkapcsolattal rendelkező.
 - A PowerShell 5.1-es vagy újabb. A verzió ellenőrzéséhez futtassa az alábbi PowerShell-parancsot, és tekintse át a *fő* verzió és *kisebb* verziók:  

    >`$PSVersionTable.PSVersion` 
 - Konfigurálása [PowerShell az Azure Stack-](azure-stack-powershell-install.md). 
 - Töltse le a legújabb verzióját [a Microsoft Azure Stack készültségi ellenőrző](https://aka.ms/AzsReadinessChecker) eszközt.  

**Az Azure Active Directory-környezetet:**
 - Azonosítsa a felhasználónevet és jelszót, amely használhatja az Azure Stack Azure-előfizetés tulajdonosa.  
 - Azonosítsa az előfizetés-azonosító az Azure-előfizetés fogja használni. 
 - Azonosítsa az AzureEnvironment fogja használni. A környezet neve paraméter támogatott értékei a következők: AzureCloud, AzureChinaCloud vagy AzureUSGovernment attól függően, hogy mely Azure-előfizetést használ.

## <a name="validate-azure-registration"></a>Azure-regisztráció ellenőrzése
1. A számítógépen, amely megfelel az előfeltételeknek nyisson meg egy rendszergazdai PowerShell-parancssort, és futtassa a következő parancsot a AzsReadinessChecker telepítéséhez.
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. A PowerShell parancssorában futtassa a következőt beállítása *$registrationCredential* a fiókot, amely az előfizetés tulajdonosa.   Cserélje le *subscriptionowner@contoso.onmicrosoft.com* a fiók és a bérlő. 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. A PowerShell parancssorában futtassa a következőt beállítása *$subscriptionID* mint az Azure-előfizetés fogja használni. Cserélje le *: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* a saját előfizetés-azonosítójára.  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. A PowerShell parancssorában futtassa a következőt indítsa el az előfizetés érvényesítése 
   - Adja meg az értéket AzureEnvironment. A környezet neve paraméter támogatott értékei a következők: AzureCloud, AzureChinaCloud vagy AzureUSGovernment attól függően, hogy mely Azure-előfizetést használ.  
   - Adja meg az Azure Active Directory-rendszergazda és az Azure Active Directory-bérlő nevét. 

   > `Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment <environment name> -RegistrationSubscriptionID $subscriptionID`

5. Az eszköz futtatása után tekintse át a kimenetet. Győződjön meg arról, a állapota OK: a bejelentkezési és a regisztrációs követelményeket is. Sikeres ellenőrzés jelenik meg, a következőhöz hasonló:  

    ```PowerShell
    Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
    Checking Registration Requirements: OK
    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsRegistrationValidation Completed
    ```

## <a name="report-and-log-file"></a>A jelentés és-naplófájl
Minden egyes alkalommal érvényesítési fut, az eredmények naplózza **AzsReadinessChecker.log** és **AzsReadinessCheckerReport.json**. Ezek a fájlok helyét jeleníti meg az ellenőrzés eredményét a PowerShellben. 

Ezek a fájlok segítségével megoszthatja érvényesítési állapot érvényesítési problémák vizsgálatához vagy az Azure Stack üzembe helyezése előtt. Mindkét fájlt megmarad, egyes további ellenőrzés eredményeit. A jelentés tartalmazza a telepítési csapat megerősítés az identitás-konfiguráció. A naplófájl segíthet a telepítés vagy a támogatási csapat érvényesítési problémák kivizsgálásában. 

Alapértelmezés szerint mindkét fájlt írt *C:\Users\<username > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Használja a **- OutputPath** ***&lt;elérési&gt;*** végén található egy másik jelentés helyét adja meg a futtatási parancssori paraméter.   
 - Használja a **- CleanReport** paraméter adatainak törlése a futtatási parancs végén *AzsReadinessCheckerReport.json*.  az eszköz előző futtatások. További információ [Azure Stack érvényesítési jelentés](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Érvényesítési hibák
Ha egy ellenőrzés nem sikerül, a hibával kapcsolatos részleteket a PowerShell-ablakban jeleníti meg. Az eszköz is a AzsReadinessChecker.log információt naplóz.

Az alábbi példák gyakori ellenőrzési hibákat ad útmutatást.

### <a name="user-must-be-an-owner-of-the-subscription"></a>Felhasználói előfizetés tulajdonosának kell lennie.   
```PowerShell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```
**OK** – a fiók nem áll az Azure-előfizetés rendszergazdája.   

**Feloldási** -fiókkal, amely rendszergazda az Azure-előfizetés, amely az Azure Stack üzemelő példányához. a használat után kell fizetnie.


### <a name="expired-or-temporary-password"></a>Lejárt vagy ideiglenes jelszó 
```PowerShell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change P
assword.
Trace ID: 48fe06f5-a5b4-4961-ad45-a86964689900
Correlation ID: 3dd1c9b2-72fb-46a0-819d-058f7562cb1f
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```
**OK** – a fiók nem jelentkezhet be, mert a jelszó vagy lejárt, vagy ideiglenes.     

**Feloldási** –, a PowerShellben futtassa és kövesse az utasításokat a jelszó alaphelyzetbe állítása. 
  > `Login-AzureRMAccount` 

Azt is megteheti, jelentkezzen be https://portal.azure.com , a fiók és a felhasználó kényszeríti a jelszó módosítására.


### <a name="unknown-user-type"></a>Ismeretlen felhasználó típusa  
```PowerShell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown Us
er Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```
**OK** – a fiók nem tud bejelentkezni a megadott Azure Active Directory-környezetet. Ebben a példában *AzureChinaCloud* a következőként van megadva a *AzureEnvironment*.  

**Feloldási** -erősítse meg, hogy a fiók a megadott Azure-környezet esetében érvényes. A PowerShellben futtassa a következő ellenőrizni a fiók érvényességét, egy adott környezetben.     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>További lépések
[Azure-identitás ellenőrzése](azure-stack-validate-identity.md)
[tekintse meg a készültségi jelentést](azure-stack-validation-report.md)
[általános Azure Stack-integráció szempontok](azure-stack-datacenter-integration.md)

