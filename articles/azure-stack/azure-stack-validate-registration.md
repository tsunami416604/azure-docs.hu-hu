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
ms.openlocfilehash: 9d84d8b40d0d3ebc58b86dbdc95ec737f13324af
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966408"
---
# <a name="validate-azure-registration"></a>Azure-regisztráció ellenőrzése
 
Az Azure Stack készültségi ellenőrző eszköz (AzsReadinessChecker) használatával ellenőrizze, hogy az Azure Stack használatra készen áll-e az Azure-előfizetésében. Az Azure Stack központi telepítésének megkezdése előtt a regisztráció érvényesítése. A készenléti-ellenőrző a következőket ellenőrzi:

- Az Azure-előfizetést használ olyan támogatott. Előfizetések egy Felhőszolgáltató (CSP) vagy a nagyvállalati szerződés (EA) kell lennie. 
- Regisztrálja az előfizetését az Azure-ral használt fióknak bejelentkezhet az Azure-ba, és a egy előfizetés tulajdonosa. 

Azure Stack-regisztráció kapcsolatos további információkért lásd: [regisztrálása az Azure Stack az Azure-ral](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>A készenléti-ellenőrző eszköz beolvasása

Az Azure Stack készültségi ellenőrző eszköz (AzsReadinessChecker) legújabb verziójának letöltése a [PowerShell-galériából](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételeknek kell teljesülnie:

**A számítógép, amelyen az eszköz fut:**
 - Windows 10-es vagy Windows Server 2016, az internetkapcsolattal rendelkező.
 - A PowerShell 5.1-es vagy újabb. A verzió ellenőrzéséhez futtassa a következő PowerShell-parancsmagot, és tekintse át a *fő* és *kisebb* verziók:  

    ```powershell
    $PSVersionTable.PSVersion
    ``` 
 - Konfigurálása [PowerShell az Azure Stack-](azure-stack-powershell-install.md). 
 - Töltse le a legújabb verzióját a [a Microsoft Azure Stack készültségi ellenőrző](https://aka.ms/AzsReadinessChecker) eszközt.  

**Az Azure Active Directory-környezetet:**
 - Azonosítsa a felhasználónevet és jelszót, amely használhatja az Azure Stack Azure-előfizetés tulajdonosa.  
 - Azonosítsa az előfizetés-azonosító az Azure-előfizetés fogja használni. 
 - Azonosítsa a **AzureEnvironment** fogja használni. A környezet neve paraméter támogatott értékei a következők **AzureCloud**, **AzureChinaCloud** vagy **AzureUSGovernment**, attól függően, hogy mely Azure-előfizetést használ.

## <a name="validate-azure-registration"></a>Azure-regisztráció ellenőrzése

1. Egy számítógépen, amely megfelel az előfeltételeknek, nyisson meg egy felügyeleti PowerShell-parancssort, és futtassa a következő paranccsal telepíthető a **AzsReadinessChecker**.

    ```powershell
    Install-Module Microsoft.AzureStack.ReadinessChecker -Force
    ```

2. A PowerShell parancssorában futtassa a következőt beállítása `$registrationCredential` a fiókot, amely az előfizetés tulajdonosa. Cserélje le `subscriptionowner@contoso.onmicrosoft.com` a fiók és a bérlő: 
   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

3. A PowerShell parancssorában futtassa a következőt beállítása `$subscriptionID` mint az Azure-előfizetés fogja használni. Cserélje le `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` a saját előfizetés-Azonosítójára:
   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ``` 

4. A PowerShell használatával indítsa el az előfizetéséhez tartozó érvényesítési a következő parancs futtatásával: 
   - Adja meg az értéket, AzureEnvironment **AzureCloud**, **AzureGermanCloud**, vagy **AzureChinaCloud**.  
   - Adja meg az Azure Active Directory-rendszergazda és az Azure Active Directory-bérlő neve. 

   ```powershell
   Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
   ```

5. Az eszköz futtatása után tekintse át a kimenetet. Győződjön meg arról, a állapota OK: a bejelentkezési és a regisztrációs követelményeket is. Sikeres ellenőrzés a következő példához hasonlóan jelenik meg:
  
   ```shell
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
Ha egy ellenőrzés nem sikerül, a hibával kapcsolatos részleteket a PowerShell-ablakban jeleníti meg. Az eszköz is naplózza az információkat a AzsReadinessChecker.log fájl.

Az alábbi példák útmutatást nyújtanak a közös ellenőrzési hibák:

### <a name="user-must-be-an-owner-of-the-subscription"></a>Felhasználói előfizetés tulajdonosának kell lennie.   

```shell
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
 
```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription [subscription ID] using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change P
assword.
Trace ID: [Trace ID]
Correlation ID: [Correlation ID]
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**OK** – a fiók nem jelentkezhet be, mert a jelszó vagy lejárt, vagy ideiglenes.     

**Feloldási** –, a PowerShellben futtassa és kövesse az utasításokat a jelszó alaphelyzetbe állítása. 

```powershell
Login-AzureRMAccount
``` 

Azt is megteheti, jelentkezzen be a https://portal.azure.com , a fiók és a felhasználó kényszeríti a jelszó módosítására.

### <a name="unknown-user-type"></a>Ismeretlen felhasználó típusa  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**OK** – a fiók nem tud bejelentkezni a megadott Azure Active Directory-környezetet. Ebben a példában *AzureChinaCloud* a következőként van megadva a *AzureEnvironment*.  

**Feloldási** -erősítse meg, hogy a fiók a megadott Azure-környezet esetében érvényes. A PowerShellben futtassa a következő ellenőrizni a fiók érvényességét, egy adott környezetben:
     
```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>További lépések

- [Azure-identitás ellenőrzése](azure-stack-validate-identity.md)
- [A készültségi jelentés megtekintése](azure-stack-validation-report.md)
- [Általános Azure Stack-integráció szempontok](azure-stack-datacenter-integration.md)

