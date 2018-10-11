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
ms.date: 05/08/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 9c7ac89d1f12e8ec033b201f2c2dd845c11486e2
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077817"
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
 - A használandó AzureEnvironement azonosítása: *AzureCloud*, *AzureGermanCloud*, vagy *AzureChinaCloud*.

## <a name="validate-azure-identity"></a>Azure-identitás ellenőrzése 
1. A számítógépen, amely megfelel az előfeltételeknek nyisson meg egy rendszergazdai PowerShell-parancssort, és futtassa a következő parancsot a AzsReadinessChecker telepítéséhez:  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. A PowerShell parancssorában futtassa a következőt beállítása *$serviceAdminCredential* , a szolgáltatás-rendszergazda az Azure AD-Bérlőben.  Cserélje le *serviceadmin@contoso.onmicrosoft.com* a fiók és a bérlő. 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. A PowerShell használatával futtassa a következő, az Azure AD-ellenőrzés indítása. 
   - Adja meg az értéket, AzureEnvironment *AzureCloud*, *AzureGermanCloud*, vagy *AzureChinaCloud*.  
   - Adja meg az Azure Active Directory bérlői neve helyett *contoso.onmicrosoft.com*. 

   > `Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AADDirectoryTenantName contoso.onmicrosoft.com`
4. Az eszköz futtatása után tekintse át a kimenetet. Az állapot megerősítéséhez **OK** bejelentkezési és a telepítési követelményeknek. Sikeres ellenőrzés a következő képhez hasonlóan jelenik meg: 
 
![Futtatás-ellenőrzés](./media/azure-stack-validate-identity/validation.png)


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
 
![lejárt jelszót](./media/azure-stack-validate-identity/expired-password.png)
**OK** – a fiók nem jelentkezhet be, mert a jelszó vagy lejárt, vagy ideiglenes.     

**Feloldási** – a PowerShellben futtassa a következő, és a jelszó alaphelyzetbe állítása az utasításokat.  
> `Login-AzureRMAccount`

Azt is megteheti, jelentkezzen be https://portal.azure.com , a fiók és a felhasználó kényszeríti a jelszó módosítására.
### <a name="unknown-user-type"></a>Ismeretlen felhasználó típusa 
 
![Ismeretlen felhasználó](./media/azure-stack-validate-identity/unknown-user.png)
**OK** – a fiók nem tud bejelentkezni a megadott Azure Active Directory (AADDirectoryTenantName). Ebben a példában *AzureChinaCloud* a következőként van megadva a *AzureEnvironment*.

**Feloldási** -erősítse meg, hogy a fiók a megadott Azure-környezet esetében érvényes. A PowerShellben futtassa a következő ellenőrizni a fiók érvényességét, egy adott környezetben: Login-AzureRmAccount-EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>Fiók nem áll a rendszergazda 
 
![nem rendszergazda](./media/azure-stack-validate-identity/not-admin.png)

**OK** – Bár a fiók sikeresen jelentkezhetnek be, a fiók nem áll-e az Azure Active Directory (AADDirectoryTenantName) rendszergazdája.  

**Feloldási** -jelentkezzen be https://portal.azure.com fiókként, Ugrás **Azure Active Directory** > **felhasználók** > *válassza ki a felhasználót*  >  **Címtárbeli szerepkör**, és ezután ellenőrizze, hogy a felhasználó egy **globális rendszergazdai**.  Ha a fiók a felhasználó, lépjen a **Azure Active Directory** > **egyéni tartomány** neveket, és győződjön meg arról, hogy a név, megadott *AADDirectoryTenantName* van az elsődleges tartomány nevét, a címtárhoz tartozó megjelölve.  Ebben a példában ez *contoso.onmicrosoft.com*. 

Az Azure Stack kell lennie, hogy a tartomány nevét az elsődleges tartomány nevét.

## <a name="next-steps"></a>További lépések
[Azure-regisztráció ellenőrzése](azure-stack-validate-registration.md)  
[A készültségi jelentés megtekintése](azure-stack-validation-report.md)  
[Általános Azure Stack-integráció szempontok](azure-stack-datacenter-integration.md)  

