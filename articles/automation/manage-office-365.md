---
title: Az Office 365 szolgáltatásainak kezelése az Azure Automation használatával
description: Bemutatja, hogyan kezelheti az Azure Automation t az Office 365 előfizetéses szolgáltatásainak kezelésére.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550421"
---
# <a name="manage-office-365-services-using-azure-automation"></a>Az Office 365 szolgáltatásainak kezelése az Azure Automation használatával

Az Azure Automation segítségével az Office 365-előfizetéses szolgáltatások felügyeletére, például a Microsoft Word és a Microsoft Outlook termékekre is használható. Az Office 365-tel való interakciókat az [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)engedélyezi. Az Azure Automation ben az Azure Automation használata az [Azure Automationben.](automation-use-azure-ad.md)

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Előfeltételek

Az Office 365 előfizetéses szolgáltatásainak kezeléséhez az Alábbiakra van szüksége az Azure Automationben.

* Azure-előfizetés. Lásd: [Előfizetési döntési útmutató](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Automation-objektum az Azure-ban a felhasználói fiók hitelesítő adatainak és runbookjainak tárolására. Lásd: [Bevezetés az Azure Automationbe.](https://docs.microsoft.com/azure/automation/automation-intro)
* Azure AD. Az Azure Automation ben az Azure Automation használata az [Azure Automationben.](automation-use-azure-ad.md)
* Office 365-ös bérlő fiókkal. Lásd: [Az Office 365-ös bérlő beállítása.](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant)

## <a name="installing-the-msonline-and-msonlineext-modules"></a>Az MSOnline és AZ MSOnlineExt modulok telepítése

Az Office 365 azure Automationben való használatához a`MSOnline` Microsoft Azure Active Directory for Windows PowerShell ( modul) szükséges. Szüksége lesz a modulra [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35)is, amely leegyszerűsíti az Azure AD-felügyeletet egy- és több-bérlős környezetekben. Telepítse a modulokat az Azure Automation Azure AD-használata című témakörben leírtak [szerint az Azure-hitelesítéshez.](automation-use-azure-ad.md)

>[!NOTE]
>Az MSOnline PowerShell használatához az Azure AD tagjának kell lennie. A vendégfelhasználók nem használhatják a modult.

## <a name="creating-an-azure-automation-account"></a>Azure Automation-fiók létrehozása

A cikkben leírt lépések végrehajtásához szüksége van egy fiókra az Azure Automationben. Lásd: [Azure Automation-fiók létrehozása.](automation-quickstart-create-account.md)
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>AZ MSOnline és az MSOnlineExt hozzáadása eszközként

Most adja hozzá a telepített MSOnline és MSOnlineExt modulokat az Office 365 funkcióinak engedélyezéséhez. Tekintse meg [a modulok kezelése az Azure Automationben.](shared-resources/modules.md)

1. Az Azure Portalon válassza az **Automation-fiókok lehetőséget.**
2. Válassza ki az Automation-fiókot.
3. Válassza a **Modulok gyűjteményének** lehetőséget a **Megosztott erőforrások csoportban.**
4. Keresse meg az MSOnline-t.
5. Válassza `MSOnline` ki a PowerShell modult, és kattintson az **Importálás** gombra a modul eszközként történő importálásához.
6. Ismételje meg a `MSOnlineExt` 4. 

## <a name="creating-a-credential-asset-optional"></a>Hitelesítő eszköz létrehozása (nem kötelező)

Nem kötelező hitelesítő adatokat létrehozni az Office 365 felügyeleti felhasználója számára, aki rendelkezik a parancsfájl futtatásához szükséges engedélyekkel. Segíthet azonban megakadályozni a felhasználónevek és jelszavak kiteszik a PowerShell-parancsfájlok on belül. További információt a [Hitelesítő adatok létrehozása .](automation-use-azure-ad.md#creating-a-credential-asset)

## <a name="creating-an-office-365-service-account"></a>Office 365 szolgáltatásfiók létrehozása

Az Office 365-előfizetéses szolgáltatások futtatásához engedéllyel rendelkező Office 365-szolgáltatási fiókra van szüksége ahhoz, hogy azt teegyék, amit akarsz. Használhatja egy globális rendszergazdai fiók, szolgáltatásonként egy fiók, vagy egy függvény vagy parancsfájl végrehajtásához. Mindenesetre a szolgáltatásfiók összetett és biztonságos jelszót igényel. Az [Office 365 vállalati verzió beállítása](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connecting-to-the-azure-ad-online-service"></a>Csatlakozás az Azure AD online szolgáltatáshoz

>[!NOTE]
>Az MSOnline modul parancsmagjainak használatához futtatnia kell őket a Windows PowerShellből. A PowerShell Core nem támogatja ezeket a parancsmagokat.

Az MSOnline modul segítségével csatlakozhat az Azure AD-hez az Office 365-előfizetésből. A kapcsolat Office 365-ös felhasználónevet és jelszót használ, vagy többtényezős hitelesítést (MFA) használ. Az Azure Portalon vagy a Windows PowerShell parancssorában csatlakozhat (nem kell emelt szintűnek lennie).

A PowerShell-példa alább látható. A [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) parancsmag kéri a hitelesítő adatokat, és tárolja őket a `Msolcred` változóban. Ezután a [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) parancsmag a hitelesítő adatokat használja az Azure-címtár online szolgáltatáshoz való csatlakozáshoz. Ha egy adott Azure-környezethez szeretne `AzureEnvironment` csatlakozni, használja a paramétert.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Ha nem kap hibaüzenetet, sikeresen csatlakozott. Egy gyors teszt például egy Office 365-parancsmag futtatására `Get-MsolUser`és az eredmények megtekintésére. Ha hibaüzenetet kap, vegye figyelembe, hogy a gyakori probléma a helytelen jelszó.

>[!NOTE]
>Az AzureRM-modul vagy az Az modul segítségével is csatlakozhat az Azure AD-hez az Office 365-előfizetésből. A fő csatlakozási parancsmag a [Connect-AzureAD.](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0) Ez a parancsmag `AzureEnvironmentName` támogatja az adott Office 365-környezetek paraméterét.

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>PowerShell-runbook létrehozása meglévő parancsfájlból

Az Office 365 funkcióit PowerShell-parancsfájlból érheti el. Íme egy példa egy parancsfájlra a `Office-Credentials` felhasználónévvel `admin@TenantOne.com`elnevezett hitelesítő adatokhoz. Az `Get-AutomationPSCredential` Office 365 hitelesítő adatainak importálására használja.

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

## <a name="running-the-script-in-a-runbook"></a>A parancsfájl futtatása runbookban

Használhatja a parancsfájlt egy Azure Automation-runbookban. Például a PowerShell runbook típusát használjuk.

1. Hozzon létre egy új PowerShell-runbookot. Az [Azure Automation-runbook létrehozása](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook)című dokumentumban tájékot...
2. Az Automation-fiókban válassza a **Runbookok** lehetőséget a **Folyamatautomatizálás**csoportban.
3. Jelölje ki az új runbookot, és kattintson a **Szerkesztés gombra.**
4. Másolja a parancsfájlt, és illessze be a runbook szöveges szerkesztőjébe.
5. Válassza **az ASSETS**lehetőséget, majd bontsa ki a Hitelesítő adatok **csomópontot,** és ellenőrizze, hogy az Office 365-hitelesítő adatok megvannak-e.
6. Kattintson a **Mentés** gombra.
7. Válassza **a Teszt ablaktábla**lehetőséget, majd a Runbook tesztelésének megkezdéséhez kattintson a **Start** gombra. Lásd: [Runbookok kezelése az Azure Automationben.](https://docs.microsoft.com/azure/automation/manage-runbooks)
8. Ha a tesztelés befejeződött, lépjen ki a Teszt ablaktáblából.

## <a name="publishing-and-scheduling-the-runbook"></a>A runbook közzététele és ütemezése

A runbook közzétételéhez és ütemezéséhez olvassa [el a Runbookok kezelése az Azure Automationben című témakört.](https://docs.microsoft.com/azure/automation/manage-runbooks)

## <a name="next-steps"></a>További lépések

* Az Automation hitelesítő adatokkal kapcsolatos eszközökkel kapcsolatos információkat az [Azure Automation hitelesítő adatokban](shared-resources/credentials.md)találja.
* Az [Azure Automation moduljainak kezelése az Automation-modulok](shared-resources/modules.md) kezeléséről.
* A runbook-kezelés áttekintését az [Azure Automation runbookok kezelése című témakörben](https://docs.microsoft.com/azure/automation/manage-runbooks)találja.
* Ha többet szeretne megtudni a runbook okádékának az Azure Automationben történő indításához használható módszerekről, [olvassa el a Runbook indítása az Azure Automationben című témakört.](automation-starting-a-runbook.md)
* A PowerShellről, beleértve a nyelvi referencia- és tanulási modulokat, a [PowerShell-dokumentumokban](https://docs.microsoft.com/powershell/scripting/overview)talál további információt.