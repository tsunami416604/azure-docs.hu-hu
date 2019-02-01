---
title: Hitelesítő eszközök az Azure Automationben
description: Hitelesítő eszközök az Azure Automationben a runbook vagy DSC-konfiguráció számára elérhető erőforrásokhoz történő hitelesítéshez használható biztonsági hitelesítő adatokat tartalmaz. Ez a cikk bemutatja, hogyan hitelesítő eszközök létrehozása és használata őket a runbookból vagy DSC-konfigurációból.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0454bc211d2ae8497babc808f9794fae4d22c47e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55498165"
---
# <a name="credential-assets-in-azure-automation"></a>Hitelesítő eszközök az Azure Automationben

Egy Automation-hitelesítőeszközt tárolja a biztonsági hitelesítő adatok, például a felhasználónevet és jelszót tartalmazó objektumot. A Runbookok és a DSC-konfigurációk előfordulhat, hogy parancsmagokat használják, amelyek fogadja el a hitelesítést egy PSCredential objektumot, vagy, előfordulhat, hogy bontsa ki a felhasználónevet és jelszót adjon meg néhány alkalmazáshoz vagy a hitelesítést igénylő szolgáltatáshoz PSCredential objektum. A tulajdonságok a hitelesítő adatait biztonságos tárolása az Azure Automationben, és a runbook vagy DSC-konfiguráció érhetők el a [Get-AutomationPSCredential](https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) tevékenység.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Az Azure Automationben biztonságos eszközök tartalmazzák, hitelesítő adatok, tanúsítványok, kapcsolatok és a titkosított változókat. Ezek az eszközök titkosítottak és a létrehozott egyedi kulcs segítségével minden automation-fiókhoz tartozó Azure Automation tárolja. Ezt a kulcsot a Key Vaultban tárolt. A kulcs tárolása egy biztonságos objektumot, előtt betöltése a Key Vaultból és majd az eszköz titkosításához használt.

## <a name="azure-classic-powershell-cmdlets"></a>Az Azure klasszikus PowerShell-parancsmagok

A következő táblázatban található parancsmagokkal létrehozása és kezelése automation hitelesítő eszközök a Windows PowerShell segítségével.  Részét képezi a [Azure PowerShell-modul](/powershell/azure/overview) elérhető a Automation-runbookok és a DSC-konfigurációkat használhatnak.

| Parancsmagok | Leírás |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/servicemanagement/azure/get-azureautomationcredential?view=azuresmps-3.7.0) |Hitelesítőadat-eszköz adatait kérdezi le. Csak kérheti le a hitelesítő adatokat, magát a **Get-AutomationPSCredential** tevékenység. |
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Létrehoz egy új Automation-hitelesítő adat. |
| [Remove- AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Eltávolítja az automatizálási hitelesítő adatok. |
| [Set- AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Egy meglévő Automation hitelesítő adat tulajdonságainak beállítása. |

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-parancsmagok

Az alábbi táblázatban a parancsmagok AzureRM, létrehozása és kezelése automation hitelesítő eszközök a Windows PowerShell használatával történik.  Részét képezi a [AzureRM.Automation modul](/powershell/azure/overview) elérhető a Automation-runbookok és a DSC-konfigurációkat használhatnak.

| Parancsmagok | Leírás |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential?view=azurermps-4.4.0) |Hitelesítőadat-eszköz adatait kérdezi le.  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential?view=azurermps-4.4.0) |Létrehoz egy új Automation-hitelesítő adat. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential?view=azurermps-4.4.0) |Eltávolítja az automatizálási hitelesítő adatok. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential?view=azurermps-4.4.0) |Egy meglévő Automation hitelesítő adat tulajdonságainak beállítása. |

## <a name="activities"></a>Tevékenységek

Az alábbi táblázatban felsorolt tevékenységek a runbookok és a DSC-konfigurációk hitelesítő adatok elérésére használhatók.

| Tevékenységek | Leírás |
|:--- |:--- |
| Get-AutomationPSCredential |Lekérdezi egy runbookból vagy DSC-konfigurációból a használni kívánt hitelesítő adatokat. Értéket ad vissza egy [System.Management.Automation.PSCredential](https://msdn.microsoft.com/library/system.management.automation.pscredential) objektum. |

> [!NOTE]
> Kerülendő a változók használata a Get-AutomationPSCredential, mivel ez nehezíti a runbookok és a DSC-konfigurációk közti függőségek, és a tervezés során hitelesítőeszközei – Name paraméterében.

## <a name="python2-functions"></a>Python2-funkciók

A függvény a következő táblázatban található a Python2-forgatókönyvem hitelesítő adatok elérésére szolgál.

| Függvény | Leírás |
|:---|:---|
| automationassets.get_automation_credential | Hitelesítőadat-eszköz adatait kérdezi le. |

> [!NOTE]
> Importálnia kell a "automationassets" modul a Python-alapú runbook tetején az eszközintelligencia-funkciók eléréséhez.

## <a name="creating-a-new-credential-asset"></a>Új hitelesítő objektum létrehozása

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Új hitelesítő objektum létrehozása az Azure portal használatával

1. Válassza ki az automation-fiók **hitelesítő adatok** alatt **megosztott erőforrások**.
1. Kattintson a **+ hitelesítő adatainak hozzáadása**.
1. Töltse ki az űrlapot, és kattintson a **létrehozás** új hitelesítő adatok mentéséhez.

> [!NOTE]
> Használható az Azure Automationben a multi-factor Authentication felhasználói fiókok nem támogatottak.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Új hitelesítő objektum létrehozása a Windows PowerShell-lel

A következő mintaparancsok bemutatják, hogyan hozhat létre egy új automation-hitelesítő adat. Egy PSCredential objektumot a névvel és jelszóval először létrehozott és a hitelesítőadat-eszköz létrehozásához. Másik lehetőségként használhatja a **Get-Credential** parancsmag felszólítja, hogy adjon meg egy nevet és jelszót.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>PowerShell-hitelesítő adat használata

Egy runbook vagy DSC-konfiguráció hitelesítőadat-eszköz kérheti a **Get-AutomationPSCredential** tevékenység. Ez visszaad egy [PSCredential objektum](https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) , hogy egy tevékenységet, vagy PSCredential paramétert igénylő parancsmagot használhatja. A külön-külön használandó hitelesítő objektum tulajdonságait is lekérhet. Az objektum szerepel egy olyan tulajdonság, a felhasználónév és a biztonságos jelszót, vagy használhatja a **GetNetworkCredential** metódussal adja vissza egy [NetworkCredential](https://msdn.microsoft.com/library/system.net.networkcredential.aspx) objektum, amely titkosítatlan verziója biztosít a a jelszó.

### <a name="textual-runbook-sample"></a>Minta szöveges forgatókönyv

A következő mintaparancsok bemutatják a PowerShell-hitelesítő adat használata runbookban. Ebben a példában a rendszer lekéri a hitelesítő adatokat, és a felhasználónév és jelszó rendelt változók.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

### <a name="graphical-runbook-sample"></a>Grafikus forgatókönyv minta

Hozzáadhat egy **Get-AutomationPSCredential** tevékenység, amely a hitelesítő adatokat, a grafikus szerkesztő a könyvtár ablaktáblán a jobb gombbal, majd válassza a grafikus runbookok **adja hozzá a vászonhoz**.

![Adja hozzá a vászonhoz hitelesítő adatok](media/automation-credentials/credential-add-canvas.png)

Az alábbi képen egy példa a grafikus runbookokban hitelesítő adatok használatával.  Ebben az esetben használatos a hitelesítés az Azure-erőforrások egy runbook leírtak szerint [Runbookok hitelesítése Azure AD felhasználói fiókkal](automation-create-aduser-account.md).  Az első tevékenység, amely hozzáfér az Azure-előfizetés hitelesítő adatait kérdezi le.  A **Add-AzureAccount** tevékenység majd használja a hitelesítő adatok olyan tevékenységet, amely azt későbbinek hitelesítésének biztosításához.  A [folyamatkapcsolódása](automation-graphical-authoring-intro.md#links-and-workflow) óta már itt **Get-AutomationPSCredential** egyetlen objektumot vár.  

![Adja hozzá a vászonhoz hitelesítő adatok](media/automation-credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>A DSC használata a PowerShell-hitelesítő adat

Bár az Azure Automation DSC-konfigurációk használatával hitelesítő eszközök hivatkozhat **Get-AutomationPSCredential**, hitelesítő eszközök is átadható paraméterekkel, ha szükséges. További információkért lásd: [összeállítása az Azure Automation DSC-konfigurációja](automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>A Python2-hitelesítőadatokkal

Az alábbi minta egy hitelesítő adatai a Python2-forgatókönyvekkel elérése példát mutat be.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>További lépések

* A grafikus létrehozásról hivatkozások kapcsolatos további információkért lásd: [hivatkozások a grafikus létrehozásról](automation-graphical-authoring-intro.md#links-and-workflow)
* A különböző hitelesítési módszerek az Automation ismertetése: [Azure Automation biztonsága](automation-security-overview.md)
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md) 
* A Python2-forgatókönyvekkel való ismerkedéshez, lásd: [az első Python2-forgatókönyvem](automation-first-runbook-textual-python2.md) 


