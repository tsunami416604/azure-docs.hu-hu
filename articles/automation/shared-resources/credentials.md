---
title: Az Azure Automation hitelesítési adategységei
description: Az Azure Automation hitelesítő adatok olyan biztonsági hitelesítő adatokat tartalmaznak, amelyek a runbook vagy a DSC-konfiguráció által elért erőforrások hitelesítésére használhatók. Ez a cikk ismerteti, hogyan hozhat létre hitelesítő adatokat, és használja őket egy runbook vagy DSC konfigurációban.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 767c1fddbc3d1f46d4341a70c990c2b57ad40e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252700"
---
# <a name="credential-assets-in-azure-automation"></a>Az Azure Automation hitelesítési adategységei

Az Automation hitelesítő adatok egy objektumot tartalmaznak, amely biztonsági hitelesítő adatokat, például felhasználónevet és jelszót tartalmaz. A Runbookok és a DSC-konfigurációk olyan parancsmagokat használhatnak, amelyek elfogadják a PSCredential objektumot a hitelesítéshez, vagy kinyerhetik a PSCredential objektum felhasználónevét és jelszavát, hogy bizonyos, hitelesítést igénylő alkalmazásoknak vagy szolgáltatásoknak biztosítsanak. A hitelesítő adatok tulajdonságait biztonságosan tárolja az Azure Automation, és a [Get-AutomationPSCredential](#activities) tevékenységgel a runbook vagy a DSC-konfigurációban érhető el.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Az Azure Automation biztonságos eszközei közé tartoznak a hitelesítő adatok, tanúsítványok, kapcsolatok és titkosított változók. Ezek az eszközök titkosítva vannak, és az Azure Automationben egy egyedi kulcs használatával kerülnek tárolásra, amely minden automatizálási fiókhoz létrejön. Ez a kulcs a Key Vault ban tárolódik. A biztonságos eszköz tárolása előtt a kulcs betöltődik a Key Vaultból, és ezután az eszköz titkosításához használható.

## <a name="azure-powershell-az-cmdlets"></a>Azure PowerShell Az-parancsmagok

Az Azure PowerShell Az modul esetében az alábbi táblázatban található parancsmagok a Windows PowerShell használatával az automatizálási hitelesítő adatok létrehozásához és kezeléséhez használatosak. Az [AzureAz.Automation modul](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)részeként szállítanak, amely az Automation runbookok és a DSC-konfigurációk ban használható.

| Parancsmagok | Leírás |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Hitelesítő adatok lekérése. Ez nem ad vissza PSCredential objektumot.  |
| [Új-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Új automatizálási hitelesítő adatok létrehozása. |
| [Eltávolítás-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Eltávolítja az Automatizáláshitelesítő adatokat. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Beállítja egy meglévő Automation-hitelesítő adatok tulajdonságait. |

## <a name="activities"></a>Tevékenységek

Az alábbi táblázatban szereplő tevékenységek a runbook- és DSC-konfigurációk hitelesítő adatainak elérésére szolgálnak.

| Tevékenységek | Leírás |
|:--- |:--- |
| Get-AutomationPSCredential |Beszerzi a runbook vagy dsc konfigurációban használandó hitelesítő adatokat. [System.Management.Automation.PSCredential](/dotnet/api/system.management.automation.pscredential) objektumot ad vissza. |

> [!NOTE]
> Ne használjon változókat a Get-AutomationPSCredential –Name paraméterében, mivel ez megnehezítheti a runbookok vagy a DSC-konfigurációk és a hitelesítő adatok közötti függőségek felderítését a tervezés időpontjában.

## <a name="python2-functions"></a>Python2 függvények

Az alábbi táblázatban található függvény a Python2 runbook hitelesítő adatainak elérésére szolgál.

| Függvény | Leírás |
|:---|:---|
| automationassets.get_automation_credential | Hitelesítő adatok lekérése. |

> [!NOTE]
> Az eszközfüggvények eléréséhez importálnia kell a "automationassets" modult a Python runbook tetején.

## <a name="creating-a-new-credential-asset"></a>Új hitelesítő egység létrehozása

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Új hitelesítő adatok létrehozása az Azure Portalon

1. Az automatizálási fiókban válassza a **Hitelesítő adatok** lehetőséget a **Megosztott erőforrások csoportban.**
1. Válassza **a Hitelesítő adatok hozzáadása**lehetőséget.
1. Töltse ki az űrlapot, és válassza a **Létrehozás** gombot az új hitelesítő adatok mentéséhez.

> [!NOTE]
> A többtényezős hitelesítést használó felhasználói fiókok nem támogatottak az Azure Automationben.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Új hitelesítő adatok létrehozása a Windows PowerShell használatával

A következő mintaparancsok bemutatják, hogyan hozhat létre új automatizálási hitelesítő adatokat. A PSCredential objektum először a névvel és a jelszóval jön létre, majd a hitelesítő adatok létrehozásához használja. Másik lehetőségként használhatja a **Get-Credential** parancsmag, hogy kérje meg, hogy írja be a nevet és a jelszót.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>PowerShell-hitelesítő adatok használata

A **Get-AutomationPSCredential** tevékenységgel lekérheti a hitelesítő adatokat egy runbook- vagy DSC-konfigurációban. Ez egy [PSCredential objektumot](/dotnet/api/system.management.automation.pscredential) ad vissza, amelyet pscredential paramétert igénylő tevékenységgel vagy parancsmaggal használhat. A hitelesítő adatok objektumának tulajdonságait is lekérheti, hogy külön-külön használhassa. Az objektum rendelkezik a felhasználónév és a biztonságos jelszó tulajdonságával, vagy a **GetNetworkCredential** metódussegítségével visszaadhat egy [NetworkCredential](/dotnet/api/system.net.networkcredential) objektumot, amely a jelszó nem biztonságos verzióját adja meg.

> [!NOTE]
> **A Get-AzAutomationCredential** nem ad vissza hitelesítésre használható **PSCredential-t.** Csak a hitelesítő adatokkal kapcsolatos információkat tartalmaz. Ha egy runbookban hitelesítő adatokat kell használnia, a **Get-AutomationPSCredential segítségével** kell beolvasnia a **PSCredential** objektumot.

### <a name="textual-runbook-sample"></a>Szöveges runbook minta

Az alábbi mintaparancsok bemutatják, miként használhatja a PowerShell-hitelesítő adatokat a runbookokban. Ebben a példában a rendszer lekéri a hitelesítő adatokat, és hozzárendeli a felhasználónevét és jelszavát a változókhoz.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Hitelesítő adatokkal is hitelesítheti magát az [Azure-ban a Connect-AzAccount segítségével.](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0) A legtöbb esetben a [Futtatás másként fiókot](../manage-runas-account.md) kell használnia, és le kell kérnie a [Get-AzAutomationConnection használatával.](../automation-connections.md)

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Grafikus runbook minta

**Get-AutomationPSCredential** tevékenységet adhat hozzá egy grafikus runbookhoz úgy, hogy a jobb gombbal a grafikus szerkesztő Könyvtár ablaktáblájában a hitelesítő adatokra kattint, és a **Hozzáadás a vászonhoz parancsot**választja.

![Hitelesítő adatok hozzáadása a vászonhoz](../media/credentials/credential-add-canvas.png)

Az alábbi képen egy példa egy hitelesítő adatok használata egy grafikus runbook. Ebben az esetben a runbook azure-erőforrásokhoz való hitelesítésére szolgál, [arunbookok hitelesítése az Azure AD-felhasználói fiókkal című könyv hitelesítése szerint.](../automation-create-aduser-account.md) Az első tevékenység lekéri a hitelesítő adatokat, amely hozzáfér az Azure-előfizetéshez. A **Connect-AzureRmAccount** tevékenység ezután ezt a hitelesítő adatot használja a hitelesítés biztosításához minden olyan tevékenységhez, amely utána jön. A [folyamatkapcsolat](../automation-graphical-authoring-intro.md#links-and-workflow) itt van, mivel a **Get-AutomationPSCredential** egyetlen objektumot vár.  

![Hitelesítő adatok hozzáadása a vászonhoz](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>PowerShell-hitelesítő adatok használata a DSC-ben

Míg az Azure Automation DSC-konfigurációi hivatkozhatnak a **Get-AutomationPSCredential**használatával hitelesítő adatokra, a hitelesítő adatok is továbbíthatók paramétereken keresztül, ha szükséges. További információ: [Konfigurációk összeállítása az Azure Automation DSC-ben című témakörben.](../automation-dsc-compile.md#credential-assets)

## <a name="using-credentials-in-python2"></a>Hitelesítő adatok használata a Python2-ben

Az alábbi minta egy példa a Python2 runbookok hitelesítő adatainak elérésére.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a grafikus szerzői hivatkozásokról, olvassa el [a Hivatkozások a grafikus szerzői szövegben című témakört.](../automation-graphical-authoring-intro.md#links-and-workflow)
* Az Automation különböző hitelesítési módszereinek megismeréséhez olvassa el az [Azure Automation Security című témakört.](../automation-security-overview.md)
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](../automation-first-runbook-graphical.md).
* A PowerShell-munkafolyamat-runbookok első lépései: [Az első PowerShell-munkafolyamat-runbook](../automation-first-runbook-textual.md)
* A Python2 runbookok első lépései: [Az első Python2 runbook](../automation-first-runbook-textual-python2.md) 
