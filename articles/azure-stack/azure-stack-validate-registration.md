---
title: Az Azure regisztráció érvényesítése Azure verem |} Microsoft Docs
description: Az Azure verem készültségi ellenőrző segítségével Azure regisztráció érvényesítése.
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
ms.openlocfilehash: 84eb1c08cc3f9ef104e2eb0b96ed397315c3f374
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="validate-azure-registration"></a>Az Azure regisztráció érvényesítése 
Az Azure verem készültségi-ellenőrző eszközzel (AzsReadinessChecker) ellenőrizheti, hogy az Azure-előfizetéshez az Azure veremnek megfelelő használatra kész. Egy Azure verem központi telepítésének megkezdése előtt a regisztráció érvényesítése. A készenléti-ellenőrző ellenőrzi:
- Az Azure-előfizetés használata típus támogatott. Előfizetések egy Felhőszolgáltató (CSP) vagy a nagyvállalati szerződés (EA) kell lennie. 
- Az előfizetés regisztrálása az Azure-ral használt fiók bejelentkezhet az Azure-ba, és egy előfizetés tulajdonosa. 

Azure verem regisztrációs kapcsolatos további információkért lásd: [Azure verem regisztrálni Azure-ral](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>A készenléti-ellenőrző eszköz beolvasása
A Azure verem készültségi-ellenőrző eszközt (AzsReadinessChecker) legújabb verziója érhető el a letöltési [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Előfeltételek
A következő előfeltételeknek kell teljesülniük.

**A számítógép, amelyen az eszköz fut:**
 - Windows 10 vagy Windows Server 2016, az internetkapcsolat.
 - PowerShell 5.1-es vagy újabb. A verzió ellenőrzéséhez futtassa a következő PowerShell-parancs, és tekintse át a *fő* verziója és *kisebb* verziók:  

    >`$PSVersionTable.PSVersion` 
 - Konfigurálása [PowerShell Azure verem](azure-stack-powershell-install.md). 
 - Töltse le a legújabb verzióját [Microsoft Azure verem készültségi ellenőrző](https://aka.ms/AzsReadinessChecker) eszköz.  

**Az Azure Active Directory-környezetben:**
 - A felhasználónév és jelszó egy olyan fiók, amely az Azure-előfizetéshez az Azure-verem használja, akkor a tulajdonos azonosítása.  
 - Azonosítsa az előfizetés-azonosító az Azure-előfizetés fogja használni. 
 - Azonosítsa a AzureEnvironment fogja használni: *AzureCloud*, *AzureGermanCloud*, vagy *AzureChinaCloud*.

## <a name="validate-azure-registration"></a>Az Azure regisztráció érvényesítése
1. A számítógépen, amely megfelel az előfeltételeknek nyisson meg egy rendszergazda PowerShell-parancssort, és futtassa a következő parancsot a AzsReadinessChecker telepítéséhez.
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. A PowerShell-parancssorból, futtassa az alábbi *$registrationCredential* a fiókot, amely az előfizetés tulajdonosa.   Cserélje le *subscriptionowner@contoso.onmicrosoft.com* a fiók és a bérlői. 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. A PowerShell-parancssorból, futtassa az alábbi *$subscriptionID* , az Azure-előfizetés fogja használni. Cserélje le *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* saját előfizetés-azonosítóval.  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. A PowerShell-parancssorból futtassa a következő ellenőrzés az előfizetéséhez 
   - Adjon meg a AzureEnvironment, *AzureCloud*, *AzureGermanCloud*, vagy *AzureChinaCloud*.  
   - Adja meg az Azure Active Directory-rendszergazda és az Azure Active Directory-bérlő nevét. 

   > `Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. Az eszköz futtatása után tekintse át a kimenetet. Győződjön meg arról, a állapot OK: a bejelentkezési és regisztrációs követelményeinek. Ellenőrzés sikerességét az alábbi kép jelenik meg:  
![Futtatás-érvényesítése](./media/azure-stack-validate-registration/registration-validation.png)


## <a name="report-and-log-file"></a>A jelentés-és naplófájlokat
Egyes időben történő érvényesítés futtat, akkor naplózza az eredményeket **AzsReadinessChecker.log** és **AzsReadinessCheckerReport.json**. Az ellenőrzés eredményét a PowerShellben mutatja be a fájlok helyét. 

Ezek a fájlok segítségével érvényesítési állapota megosztani, mielőtt Azure verem telepítése, illetve a érvényesítési kapcsolatos problémák kivizsgálásához. A fájlok továbbra is fennáll, minden ezt követő eredetiség ellenőrzésének eredményét. A jelentés tartalmazza a telepítési csapat megerősítés az identitás-konfiguráció. A naplófájl segít a központi telepítés vagy a támogatási csapata vizsgálja ki az érvényesítési problémák. 

Alapértelmezés szerint mindkettőt írt *C:\Users\<felhasználónév > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Használja a **- OutputPath** ***&lt;elérési&gt;*** paraméter parancssor futtatása a jelentés másik hely megadásához végén.   
 - Használja a **- CleanReport** parancs futtatásával törölje a következőről végén paraméter *AzsReadinessCheckerReport.json*.  tudnivalók az eszköz korábbi futtatásakor. További információ [Azure verem ellenőrzési jelentés](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Az érvényesítési hibák
Ha érvényességének ellenőrzése sikertelen, a hiba részleteit jeleníti meg, a PowerShell-ablakban. Az eszköz is naplózza az adatokat a AzsReadinessChecker.log.

Az alábbi példák közös érvényesítési hibák ad útmutatást.

### <a name="user-must-be-an-owner-of-the-subscription"></a>A felhasználónak kell lennie az előfizetés tulajdonosa   
![előfizetés-tulajdonosa](./media/azure-stack-validate-registration/subscription-owner.png)
**OK** -a fiók nincs az Azure-előfizetés rendszergazdája.   

**Megoldási** -olyan fiókot használjon, amely rendszergazdai jogosultságokkal rendelkező Azure-előfizetést, amely az Azure-verem telepítéséből használati alapján számlázzuk.


### <a name="expired-or-temporary-password"></a>A lejárt vagy ideiglenes jelszó 
![lejárt jelszó](./media/azure-stack-validate-registration/expired-password.png)
**OK** -a fiók nem jelentkezhet be, mert a jelszó vagy lejárt, vagy ideiglenes.     

**Megoldási** – a PowerShell futtatásához és az utasításokat követve a jelszó alaphelyzetbe állításához. 
  > `Login-AzureRMAccount` 

Másik lehetőségként a bejelentkezés a https://portal.azure.com , a fiók és a felhasználó jelszavának módosításához lenne kényszerítve.


### <a name="microsoft-accounts-are-not-supported-for-registration"></a>Microsoft-fiókok nem támogatottak a regisztrációhoz  
![nem támogatott fiók](./media/azure-stack-validate-registration/unsupported-account.png)
**OK** -A Microsoft-fiókkal (például Outlook.com-os vagy Hotmail.com) lett megadva.  Ezek a fiókok nem támogatottak.

**Megoldási** -fiókja és -előfizetést egy Felhőszolgáltató (CSP) vagy a nagyvállalati szerződés (EA) használatához. 


### <a name="unknown-user-type"></a>Ismeretlen felhasználó típusa  
![Ismeretlen felhasználói](./media/azure-stack-validate-registration/unknown-user.png)
**OK** -fiók nem tud bejelentkezni a megadott Azure Active Directory-környezetbe. Ebben a példában *AzureChinaCloud* van megadva a *AzureEnvironment*.  

**Megoldási** -erősítse meg, hogy a fiók érvényes a megadott Azure-környezethez. A PowerShellben futtassa a következő ellenőrizze a fiók nem érvényes az adott környezetben.     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>További lépések
[Azure-identitás ellenőrzése](azure-stack-validate-identity.md)
[a készültségi jelentés megtekintéséhez](azure-stack-validation-report.md)
[általános Azure verem integrációs kapcsolatos szempontok](azure-stack-datacenter-integration.md)

