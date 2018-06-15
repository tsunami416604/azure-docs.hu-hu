---
title: Azure-identitás ellenőrzése az Azure verem |} Microsoft Docs
description: Az Azure verem készültségi ellenőrző segítségével ellenőrizheti az Azure-identitás.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: fe5e7281cbe01ad11f667729df344f91ef1327d2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937828"
---
# <a name="validate-azure-identity"></a>Azure-identitás ellenőrzése 
Az Azure verem készültségi-ellenőrző eszközzel (AzsReadinessChecker) ellenőrizze, hogy az Azure Active Directory (Azure AD), az Azure veremnek megfelelő használatra kész. Ellenőrizze az Azure-identitás megoldás az Azure-verem központi telepítésének megkezdése előtt.  

A készenléti-ellenőrző ellenőrzi:
 - Azure Active Directory (Azure AD) Azure verem identitás-szolgáltatóként.
 - Az Azure AD-fiókot használni kívánt Azure Active Directory globális rendszergazdaként jelentkezhetnek be. 

Az érvényesítés biztosítja, hogy a környezet készen áll a Azure verem, az alkalmazások, csoportok, és az Azure-veremből szolgáltatásnevekről felhasználók tárolására az Azure AD-ben.

## <a name="get-the-readiness-checker-tool"></a>A készenléti-ellenőrző eszköz beolvasása
A Azure verem készültségi-ellenőrző eszközt (AzsReadinessChecker) legfrissebb verziójának letöltését a [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Előfeltételek
A következő előfeltételeknek kell teljesülniük.

**A számítógép, amelyen az eszköz fut:**
 - Windows 10 vagy Windows Server 2016, az internetkapcsolat.
 - PowerShell 5.1-es vagy újabb. A verzió ellenőrzéséhez futtassa a következő PowerShell-parancs, és tekintse át a *fő* verziója és *kisebb* verziók:  

   > `$PSVersionTable.PSVersion`
 - Konfigurálása [PowerShell Azure verem](azure-stack-powershell-install.md). 
 - A legújabb [Microsoft Azure verem készültségi ellenőrző](https://aka.ms/AzsReadinessChecker) eszköz.

**Az Azure Active Directory-környezetben:**
 - Azonosítsa a rendszer Azure verem használható, és győződjön meg arról az Azure Active Directory globális rendszergazda Azure AD-fiókot.
 - Határozza meg az Azure AD-bérlő nevét. A bérlő nevének kell lennie a *elsődleges* az Azure Active Directory tartomány nevét. Például *contoso.onmicrosoft.com*. 
 - Azonosítsa a AzureEnvironement fogja használni: *AzureCloud*, *AzureGermanCloud*, vagy *AzureChinaCloud*.

## <a name="validate-azure-identity"></a>Azure-identitás ellenőrzése 
1. A számítógépen, amely megfelel az előfeltételeknek nyisson meg egy rendszergazda PowerShell-parancssort, és futtassa a következő parancsot a AzsReadinessChecker telepítéséhez:  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. A PowerShell-parancssorból, futtassa az alábbi *$serviceAdminCredential* , a szolgáltatás-rendszergazda az Azure AD-bérlő.  Cserélje le *serviceadmin@contoso.onmicrosoft.com* a fiók és a bérlői. 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. A PowerShell-parancssorból futtassa a következő ellenőrzés az Azure ad. 
   - Adjon meg a AzureEnvironment, *AzureCloud*, *AzureGermanCloud*, vagy *AzureChinaCloud*.  
   - Adja meg az Azure Active Directory bérlő neve lecseréli *contoso.onmicrosoft.com*. 

   > `Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AADDirectoryTenantName contoso.onmicrosoft.com`
4. Az eszköz futtatása után tekintse át a kimenetet. Győződjön meg arról az állapot **OK** bejelentkezési és a telepítési követelményeknek. Ellenőrzés sikerességét az alábbi kép jelenik meg: 
 
![Futtatás-érvényesítése](./media/azure-stack-validate-identity/validation.png)


## <a name="report-and-log-file"></a>A jelentés-és naplófájlokat
Egyes időben történő érvényesítés futtat, akkor naplózza az eredményeket **AzsReadinessChecker.log** és **AzsReadinessCheckerReport.json**. Az ellenőrzés eredményét a PowerShellben mutatja be a fájlok helyét.

Ezek a fájlok segítségével érvényesítési állapota megosztani, mielőtt Azure verem telepítése, illetve a érvényesítési kapcsolatos problémák kivizsgálásához.  A fájlok továbbra is fennáll, minden ezt követő eredetiség ellenőrzésének eredményét. A jelentés tartalmazza a telepítési csapat megerősítés az identitás-konfiguráció. A naplófájl segít a központi telepítés vagy a támogatási csapata vizsgálja ki az érvényesítési problémák. 

Alapértelmezés szerint mindkettőt írt *C:\Users\<felhasználónév > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Használja a **- OutputPath** ***&lt;elérési&gt;*** paraméter parancssor futtatása a jelentés másik hely megadásához végén.   
 - Használja a **- CleanReport** parancs futtatásával törölje a következőről végén paraméter *AzsReadinessCheckerReport.json*.  tudnivalók az eszköz korábbi futtatásakor. 

További információ [Azure verem ellenőrzési jelentés](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Az érvényesítési hibák
Ha érvényességének ellenőrzése sikertelen, a hiba részleteit jeleníti meg, a PowerShell-ablakban. Az eszköz is naplózza az adatokat a AzsReadinessChecker.log.

Az alábbi példák közös érvényesítési hibák ad útmutatást.

### <a name="expired-or-temporary-password"></a>A lejárt vagy ideiglenes jelszó 
 
![lejárt jelszó](./media/azure-stack-validate-identity/expired-password.png)
**OK** -a fiók nem jelentkezhet be, mert a jelszó vagy lejárt, vagy ideiglenes.     

**Megoldási** – a PowerShellben futtassa a következő parancsot, és kövesse az utasításokat a jelszó alaphelyzetbe állítása.  
> `Login-AzureRMAccount`

Másik lehetőségként a bejelentkezés a https://portal.azure.com , a fiók és a felhasználó jelszavának módosításához lenne kényszerítve.
### <a name="unknown-user-type"></a>Ismeretlen felhasználó típusa 
 
![Ismeretlen felhasználó](./media/azure-stack-validate-identity/unknown-user.png)
**OK** -fiók nem tud bejelentkezni a megadott Azure Active Directory (AADDirectoryTenantName). Ebben a példában *AzureChinaCloud* van megadva a *AzureEnvironment*.

**Megoldási** -erősítse meg, hogy a fiók érvényes a megadott Azure-környezethez. A PowerShellben futtassa a következő ellenőrizze a fiók nem érvényes az adott környezethez: Login-AzureRmAccount – EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>A fiók nincs a rendszergazda 
 
![nem rendszergazda](./media/azure-stack-validate-identity/not-admin.png)

**OK** – Bár a fiók sikeresen jelentkezhetnek be, a fiók nincs az Azure Active Directoryban (AADDirectoryTenantName) rendszergazdája.  

**Megoldási** -bejelentkezés a https://portal.azure.com a fiókkal, Ugrás **Azure Active Directory** > **felhasználók** > *válassza ki a felhasználó*  >  **Directory szerepkör**, majd győződjön meg róla, a felhasználó egy **globális rendszergazda**.  Ha a fiók a felhasználó, Ugrás **Azure Active Directory** > **egyéni tartomány** nevét, és győződjön meg arról, hogy a név megadott a *AADDirectoryTenantName* van az elsődleges tartomány nevét ennél a címtárnál jelölésű.  Ebben a példában ez *contoso.onmicrosoft.com*. 

Az Azure verem kell lennie, a tartomány nevét az elsődleges tartomány nevét.

## <a name="next-steps"></a>További lépések
[Az Azure regisztráció érvényesítése](azure-stack-validate-registration.md)  
[A készültségi jelentés megtekintése](azure-stack-validation-report.md)  
[Általános Azure verem integrációs kapcsolatos szempontok](azure-stack-datacenter-integration.md)  

