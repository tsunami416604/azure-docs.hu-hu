---
title: Azure-identitás ellenőrzése az Azure Stackhez |} A Microsoft Docs
description: Az Azure Stack-készültségi ellenőrző használatával Azure-identitás ellenőrzése.
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
ms.reviewer: unknown
ms.openlocfilehash: 2cfa789b9817562eba5224257a365f8552cdf833
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245940"
---
# <a name="validate-azure-identity"></a>Azure-identitás ellenőrzése 
Az Azure Stack készültségi ellenőrző eszköz (AzsReadinessChecker) használatával ellenőrizze, hogy az Azure Stack használatra készen áll-e az Azure Active Directory (Azure AD). Ellenőrizze az Azure identitáskezelési megoldás, az Azure Stack központi telepítésének megkezdése előtt.  

A készenléti ellenőrző ellenőrzi:
 - Az Azure Active Directory (Azure AD) identitás-szolgáltatóként az Azure Stackhez.
 - Az Azure AD-fiókot, amelyet használni szeretne az Azure Active Directory globális rendszergazdájaként jelentkezhet be. 

Az érvényesítés biztosítja, hogy a környezet készen áll a felhasználók, alkalmazások, csoportok és az Azure Stack szolgáltatásnevek kapcsolatos információk tárolására az Azure AD-ben az Azure Stack.

## <a name="get-the-readiness-checker-tool"></a>A készenléti-ellenőrző eszköz beolvasása
Az Azure Stack készültségi ellenőrző eszköz (AzsReadinessChecker) legújabb verziójának letöltése a [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Előfeltételek
A következő előfeltételek vonatkoznak a helyen kell lennie.

**A számítógép, amelyen az eszköz fut:**
 - Windows 10-es vagy Windows Server 2016, az internetkapcsolattal rendelkező.
 - A PowerShell 5.1-es vagy újabb. A verzió ellenőrzéséhez futtassa az alábbi PowerShell-parancsot, és tekintse át a *fő* verzió és *kisebb* verziók:  

   > `$PSVersionTable.PSVersion`
 - Konfigurálása [PowerShell az Azure Stack-](azure-stack-powershell-install.md). 
 - A legújabb [a Microsoft Azure Stack készültségi ellenőrző](https://aka.ms/AzsReadinessChecker) eszközt.

**Az Azure Active Directory-környezetet:**
 - Azonosítsa az Azure AD-fiókot fogja használni az Azure Stack és, hogy az Azure Active Directory globális rendszergazdája.
 - Azonosítsa az Azure AD-bérlő nevét. A bérlő nevének kell lennie a *elsődleges* tartománynév az Azure Active Directoryban. Ha például *contoso.onmicrosoft.com*. 
 - Azonosítsa a AzureEnvironement fogja használni. A környezet neve paraméter támogatott értékei a következők: AzureCloud, AzureChinaCloud vagy AzureUSGovernment attól függően, hogy mely Azure-előfizetést használ.

## <a name="validate-azure-identity"></a>Azure-identitás ellenőrzése 
1. A számítógépen, amely megfelel az előfeltételeknek nyisson meg egy rendszergazdai PowerShell-parancssort, és futtassa a következő parancsot a AzsReadinessChecker telepítéséhez:  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. A PowerShell parancssorában futtassa a következőt beállítása *$serviceAdminCredential* , a szolgáltatás-rendszergazda az Azure AD-Bérlőben.  Cserélje le *serviceadmin@contoso.onmicrosoft.com* a fiók és a bérlő. 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. A PowerShell használatával futtassa a következő, az Azure AD-ellenőrzés indítása. 
   - Adja meg a környezet név-érték AzureEnvironment. A környezet neve paraméter támogatott értékei a következők: AzureCloud, AzureChinaCloud vagy AzureUSGovernment attól függően, hogy mely Azure-előfizetést használ.  
   - Adja meg az Azure Active Directory bérlői neve helyett *contoso.onmicrosoft.com*. 

   > `Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com`
4. Az eszköz futtatása után tekintse át a kimenetet. Az állapot megerősítéséhez **OK** a telepítési követelményeknek. Sikeres ellenőrzés a következő képhez hasonlóan jelenik meg: 
 
````PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: OK

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
````


## <a name="report-and-log-file"></a>A jelentés és-naplófájl
Minden egyes alkalommal érvényesítési fut, az eredmények naplózza **AzsReadinessChecker.log** és **AzsReadinessCheckerReport.json**. Ezek a fájlok helyét jeleníti meg az ellenőrzés eredményét a PowerShellben.

Ezek a fájlok segítségével megoszthatja érvényesítési állapot érvényesítési problémák vizsgálatához vagy az Azure Stack üzembe helyezése előtt.  Mindkét fájlt megmarad, egyes további ellenőrzés eredményeit. A jelentés tartalmazza a telepítési csapat megerősítés az identitás-konfiguráció. A naplófájl segíthet a telepítés vagy a támogatási csapat érvényesítési problémák kivizsgálásában. 

Alapértelmezés szerint mindkét fájlt írt *C:\Users\<username > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Használja a **- OutputPath** ***&lt;elérési&gt;*** végén található egy másik jelentés helyét adja meg a futtatási parancssori paraméter.   
 - Használja a **- CleanReport** paraméter adatainak törlése a futtatási parancs végén *AzsReadinessCheckerReport.json*.  az eszköz előző futtatások. 

További információ [Azure Stack érvényesítési jelentés](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Érvényesítési hibák
Ha egy ellenőrzés nem sikerül, a hibával kapcsolatos részleteket a PowerShell-ablakban jeleníti meg. Az eszköz is a AzsReadinessChecker.log információt naplóz.

Az alábbi példák gyakori ellenőrzési hibákat ad útmutatást.

### <a name="expired-or-temporary-password"></a>Lejárt vagy ideiglenes jelszó 
 
````PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The password for account  has expired or is a temporary password that needs to be reset before continuing. Run Login-AzureRMAccount, login with  credentials and follow the prompts to reset.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
````
**OK** – a fiók nem jelentkezhet be, mert a jelszó vagy lejárt, vagy ideiglenes.     

**Feloldási** – a PowerShellben futtassa a következő, és a jelszó alaphelyzetbe állítása az utasításokat.  
> `Login-AzureRMAccount`

Azt is megteheti, jelentkezzen be https://portal.azure.com , a fiók és a felhasználó kényszeríti a jelszó módosítására.
### <a name="unknown-user-type"></a>Ismeretlen felhasználó típusa 
 
````PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
Unknown user type detected. Check the account  is valid for AzureChinaCloud
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
````
**OK** – a fiók nem tud bejelentkezni a megadott Azure Active Directory (AADDirectoryTenantName). Ebben a példában *AzureChinaCloud* a következőként van megadva a *AzureEnvironment*.

**Feloldási** -erősítse meg, hogy a fiók a megadott Azure-környezet esetében érvényes. A PowerShellben futtassa a következő ellenőrizni a fiók érvényességét, egy adott környezetben:   Login-AzureRmAccount-EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>Fiók nem áll a rendszergazda 
 
````PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The Service Admin account you entered 'admin@contoso.onmicrosoft.com' is not an administrator of the Azure Active Directory tenant 'contoso.onmicrosoft.com'.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
````

**OK** – Bár a fiók sikeresen jelentkezhetnek be, a fiók nem áll-e az Azure Active Directory (AADDirectoryTenantName) rendszergazdája.  

**Feloldási** -jelentkezzen be https://portal.azure.com fiókként, Ugrás **Azure Active Directory** > **felhasználók** > *válassza ki a felhasználót*  >  **Címtárbeli szerepkör**, és ezután ellenőrizze, hogy a felhasználó egy **globális rendszergazdai**.  Ha a fiók a felhasználó, lépjen a **Azure Active Directory** > **egyéni tartomány** neveket, és győződjön meg arról, hogy a név, megadott *AADDirectoryTenantName* van az elsődleges tartomány nevét, a címtárhoz tartozó megjelölve.  Ebben a példában ez *contoso.onmicrosoft.com*. 

Az Azure Stack kell lennie, hogy a tartomány nevét az elsődleges tartomány nevét.

## <a name="next-steps"></a>További lépések
[Azure-regisztráció ellenőrzése](azure-stack-validate-registration.md)  
[A készültségi jelentés megtekintése](azure-stack-validation-report.md)  
[Általános Azure Stack-integráció szempontok](azure-stack-datacenter-integration.md)  

