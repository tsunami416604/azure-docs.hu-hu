---
title: Office 365-szolgáltatások kezelése az Azure Automation használatával
description: Azt mutatja be, hogyan használható a Azure Automation az Office 365 előfizetési szolgáltatások kezeléséhez.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550421"
---
# <a name="manage-office-365-services-using-azure-automation"></a>Office 365-szolgáltatások kezelése az Azure Automation használatával

Az Office 365 előfizetési szolgáltatások felügyeletéhez Azure Automation használhatja a Microsoft Word és a Microsoft Outlook termékekhez. Az Office 365-mel folytatott interakciókat [Azure Active Directory (Azure ad)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)engedélyezték. Lásd: az Azure [ad használata Azure Automation az Azure-ban való hitelesítéshez](automation-use-azure-ad.md).

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Előfeltételek

Az Office 365 előfizetési szolgáltatásainak Azure Automation-ben való kezeléséhez a következőkre lesz szüksége.

* Azure-előfizetés. Lásd: [előfizetés-döntési útmutató](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Egy Automation-objektum az Azure-ban a felhasználói fiók hitelesítő adatainak és runbookok tárolásához. Tekintse [meg a Azure Automation bevezetését](https://docs.microsoft.com/azure/automation/automation-intro).
* Azure AD. Lásd: az Azure [ad használata Azure Automation az Azure-ban való hitelesítéshez](automation-use-azure-ad.md).
* Office 365-bérlő, fiókkal. Lásd: [Office 365-bérlő beállítása](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="installing-the-msonline-and-msonlineext-modules"></a>A MSOnline és a MSOnlineExt modulok telepítése

A Azure Automationon belüli Office 365 használatához Microsoft Azure Active Directory szükséges a Windows PowerShellhez (`MSOnline` modul). Emellett a modulra [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35)is szüksége lesz, amely leegyszerűsíti az Azure ad-felügyeletet az egy-és több-bérlős környezetekben. Telepítse a modulokat az Azure [ad-ban Azure Automation az Azure-ban való hitelesítéshez](automation-use-azure-ad.md)című témakörben leírtak szerint.

>[!NOTE]
>A MSOnline PowerShell használatához az Azure AD-tagnak kell lennie. A vendég felhasználók nem használhatják a modult.

## <a name="creating-an-azure-automation-account"></a>Azure Automation fiók létrehozása

A cikkben szereplő lépések végrehajtásához egy Azure Automation fiókra van szükség. Lásd: [Azure Automation fiók létrehozása](automation-quickstart-create-account.md).
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>MSOnline és MSOnlineExt hozzáadása eszközként

Most adja hozzá a telepített MSOnline-és MSOnlineExt-modulokat az Office 365 funkcióinak engedélyezéséhez. Tekintse át a [Azure Automation moduljainak kezelése](shared-resources/modules.md)című témakört.

1. A Azure Portal válassza az **Automation-fiókok**elemet.
2. Válassza ki az Automation-fiókját.
3. Válassza a **modulok tárat** a **megosztott erőforrások**területen.
4. MSOnline keresése.
5. Válassza ki `MSOnline` a PowerShell-modult, és kattintson az **Importálás** gombra a modul eszközként való importálásához.
6. A modul megkereséséhez és importálásához ismételje `MSOnlineExt` meg a 4. és az 5. lépést. 

## <a name="creating-a-credential-asset-optional"></a>Hitelesítőadat-eszköz létrehozása (nem kötelező)

Nem kötelező létrehozni a hitelesítő adatokat az Office 365 rendszergazda felhasználó számára, aki rendelkezik a parancsfájl futtatásához szükséges engedélyekkel. Ez segíthet azonban abban, hogy a felhasználói neveket és jelszavakat a PowerShell-szkripteken belül ne tegye közzé. Útmutatásért lásd: [hitelesítőadat-eszköz létrehozása](automation-use-azure-ad.md#creating-a-credential-asset).

## <a name="creating-an-office-365-service-account"></a>Office 365-szolgáltatásfiók létrehozása

Az Office 365 előfizetési szolgáltatásainak futtatásához szüksége van egy Office 365-szolgáltatásfiók, amely a kívánt műveletekre vonatkozó engedélyekkel rendelkezik. Használhat egy globális rendszergazdai fiókot, egy-egy fiókot, vagy egy függvényt vagy szkriptet a végrehajtáshoz. A szolgáltatási fióknak minden esetben összetett és biztonságos jelszót kell megadnia. Lásd: [az Office 365 for Business beállítása](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connecting-to-the-azure-ad-online-service"></a>Csatlakozás az Azure AD online szolgáltatáshoz

>[!NOTE]
>A MSOnline modul-parancsmagok használatához futtatnia kell őket a Windows PowerShellből. A PowerShell Core nem támogatja ezeket a parancsmagokat.

A MSOnline modul használatával kapcsolódhat az Azure AD-hez az Office 365-előfizetésből. A kapcsolatok egy Office 365-felhasználónevet és-jelszót használnak, vagy többtényezős hitelesítést (MFA) használnak. Az Azure Portal vagy egy Windows PowerShell-parancssor használatával is csatlakozhat (nem kell megemelni).

Alább látható egy PowerShell-példa. A [Get-hitelesítőadat](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) parancsmag megkéri a hitelesítő adatokat, és tárolja azokat `Msolcred` a változóban. Ezután a [MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) parancsmag a hitelesítő adatok használatával csatlakozik az Azure Directory online szolgáltatáshoz. Ha egy adott Azure-környezethez szeretne csatlakozni, használja a `AzureEnvironment` (z) paramétert.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Ha nem kap hibát, sikeresen kapcsolódott. A gyors tesztelés egy Office 365-parancsmag futtatása, például: `Get-MsolUser`, és az eredmények megtekintése. Ha hibaüzenetet kap, vegye figyelembe, hogy egy gyakori probléma helytelen jelszó.

>[!NOTE]
>A AzureRM modul vagy az az modul használatával is csatlakozhat az Azure AD-hez az Office 365-előfizetésből. A fő csatlakozási parancsmag a kapcsolat [– AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). Ez a parancsmag a `AzureEnvironmentName` megadott Office 365-környezetek paraméterét támogatja.

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>PowerShell-runbook létrehozása meglévő parancsfájlból

Az Office 365 funkcióit egy PowerShell-szkriptből érheti el. Az alábbi példa egy, a felhasználónévvel ellátott `Office-Credentials` hitelesítő adathoz tartozó `admin@TenantOne.com`parancsfájlt mutat be. Az Office `Get-AutomationPSCredential` 365 hitelesítő adatának importálására használja.

```powershell
$emailFromAddress = "admin@TenantOne.com" 
$emailToAddress = "servicedesk@TenantOne.com" 
$emailSMTPServer = "outlook.office365.com" 
$emailSubject = "Office 365 License Report" 

$credObject = Get-AutomationPSCredential -Name "Office-Credentials" 
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String 
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body 

$O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="running-the-script-in-a-runbook"></a>A szkript futtatása runbook

A parancsfájlt Azure Automation runbook használhatja. Például a PowerShell runbook típusát fogjuk használni.

1. Hozzon létre egy új PowerShell-runbook. Tekintse át [Azure Automation Runbook létrehozását](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook)ismertető témakört.
2. Az Automation-fiókjából válassza a **runbookok** lehetőséget a **folyamat automatizálása**alatt.
3. Válassza ki az új runbook, és kattintson a **Szerkesztés**gombra.
4. Másolja a szkriptet, és illessze be a runbook szöveges szerkesztőjébe.
5. Válassza az **eszközök**lehetőséget, majd bontsa ki a **hitelesítő adatokat** , és ellenőrizze, hogy az Office 365 hitelesítő adatai vannak-e.
6. Kattintson a **Save** (Mentés) gombra.
7. Válassza a **teszt panel**lehetőséget, majd kattintson az **Indítás** gombra a runbook tesztelésének megkezdéséhez. Lásd: [Runbookok kezelése Azure Automationban](https://docs.microsoft.com/azure/automation/manage-runbooks).
8. A tesztelés befejezésekor lépjen ki a teszt panelről.

## <a name="publishing-and-scheduling-the-runbook"></a>A runbook közzététele és ütemezése

A runbook közzétételéhez és a runbookok való bevezetéséhez tekintse meg a következő témakört: [a Azure Automation kezelése](https://docs.microsoft.com/azure/automation/manage-runbooks).

## <a name="next-steps"></a>További lépések

* Az Automation hitelesítőadat-eszközeivel kapcsolatban a [Azure Automationban](shared-resources/credentials.md)talál információt.
* Az Automation-modulok használatáról további információt a [Azure Automation moduljainak kezelése](shared-resources/modules.md) című témakörben talál.
* A runbook-kezelés áttekintését lásd: [Runbookok kezelése a Azure Automationban](https://docs.microsoft.com/azure/automation/manage-runbooks).
* Ha többet szeretne megtudni a Azure Automation runbook indításához használható módszerekről, tekintse meg a [runbook Azure Automation-ben](automation-starting-a-runbook.md)való elindításával foglalkozó témakört.
* A PowerShell-lel kapcsolatos további információkért, beleértve a nyelvi referenciákat és a tanulási modulokat is, tekintse meg a [PowerShell-dokumentumokat](https://docs.microsoft.com/powershell/scripting/overview).