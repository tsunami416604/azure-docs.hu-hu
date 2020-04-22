---
title: Hitelesítő adatok kezelése az Azure Automationben
description: Az Azure Automation hitelesítő adatok olyan biztonsági hitelesítő adatokat tartalmaznak, amelyek a runbook vagy a DSC-konfiguráció által elért erőforrások hitelesítésére használhatók. Ez a cikk ismerteti, hogyan hozhat létre hitelesítő adatokat, és használja őket egy runbook vagy DSC konfigurációban.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59e32087d4489cbb155a9cff7d40094c0606c0cf
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732848"
---
# <a name="manage-credentials-in-azure-automation"></a>Hitelesítő adatok kezelése az Azure Automationben

Az Automation hitelesítő adatok olyan objektumot tartalmaznak, amely biztonsági hitelesítő adatokat, például felhasználónevet és jelszót tartalmaz. A Runbookok és a DSC-konfigurációk olyan parancsmagokat használnak, amelyek elfogadják a [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) objektumot a hitelesítéshez. Azt is megteheti, hogy kibontja `PSCredential` az objektum felhasználónevét és jelszavát, hogy bizonyos, hitelesítést igénylő alkalmazásokat vagy szolgáltatást biztosítson. 

> [!NOTE]
> Az Azure Automation biztonságos eszközei közé tartoznak a hitelesítő adatok, tanúsítványok, kapcsolatok és titkosított változók. Ezek az eszközök titkosítva vannak, és az Azure Automation-ben egy egyedi kulcs használatával, amely minden Automation-fiókhoz létrejön. Ez a kulcs a Key Vault ban tárolódik. A biztonságos eszköz tárolása előtt a kulcs betöltődik a Key Vaultból, és ezután az eszköz titkosításához használható.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](../automation-update-azure-modules.md)

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Hitelesítő adatokhoz használt Azure PowerShell Az-parancsmagok

Az Azure PowerShell Az modul részeként az alábbi táblázatban található parancsmagok az Automation hitelesítő adatok létrehozásához és kezeléséhez szolgálnak a Windows PowerShell használatával. Az [Az.Automation modulban](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)szállítanak, amely az Automation runbookok és a DSC-konfigurációk ban használható. Lásd: [Az modul támogatás az Azure Automationben.](https://docs.microsoft.com/azure/automation/az-modules)

| Parancsmag | Leírás |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Hitelesítő adatok lekérése. Ez a parancsmag nem `PSCredential` ad vissza objektumot.  |
| [Új-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Új automatizálási hitelesítő adatok létrehozása. |
| [Eltávolítás-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Eltávolítja az Automatizáláshitelesítő adatokat. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Beállítja egy meglévő Automation-hitelesítő adatok tulajdonságait. |

## <a name="activities-used-to-access-credentials"></a>A hitelesítő adatok eléréséhez használt tevékenységek

Az alábbi táblázatban szereplő tevékenységek runbookok és DSC-konfigurációk hitelesítő adatainak elérésére szolgálnak.

| Tevékenység | Leírás |
|:--- |:--- |
| `Get-AutomationPSCredential` |Beszerzi a runbook vagy dsc konfigurációban használandó hitelesítő adatokat. A hitelesítő adat `PSCredential` objektum formájában van. A tevékenységnek megfelelő parancsmagról az [Azure Automation moduleszközei című témakörben](modules.md)található. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Hitelesítő adatokat kér a felhasználónévről és a jelszóról. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Létrehoz egy hitelesítő egységet. |

A `PSCredential` kódban lévő objektumok lekéréséhez telepítheti a Microsoft Azure Automation ISE bővítményt a PowerShell ISE-hez. További információ: [Module assets in Azure Automation.](modules.md)

```azurepowershell
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser -Force
```

A parancsfájl szükség esetén importálhatja a szükséges modult is, ahogy az a következő példában is látható: 

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Ne használjon változókat `Name` a `Get-AutomationPSCredential`paraméterben. Használatuk megnehezítheti a runbookok vagy a DSC-konfigurációk és a hitelesítő adatok közötti függőségek felderítését a tervezés időpontjában.

## <a name="python-2-functions-that-access-credentials"></a>Python 2-függvények, amelyek hozzáférnek a hitelesítő adatokhoz

Az alábbi táblázatban található függvény a Python 2 runbook hitelesítő adatainak elérésére szolgál.

| Függvény | Leírás |
|:---|:---|
| `automationassets.get_automation_credential` | Hitelesítő adatok lekérése. |

> [!NOTE]
> Importálja `automationassets` a modult a Python runbook tetején az eszközfüggvények eléréséhez.

## <a name="creating-a-new-credential-asset"></a>Új hitelesítő egység létrehozása

Új hitelesítő adatokat hozhat létre az Azure Portalon vagy a Windows PowerShell használatával.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Új hitelesítő adatok létrehozása az Azure Portalon

1. Az Automation-fiókban válassza a **Hitelesítő adatok** lehetőséget a Megosztott **erőforrások csoportban.**
1. Válassza **a Hitelesítő adatok hozzáadása**lehetőséget.
2. Az Új hitelesítő adatok ablaktáblán adja meg a megfelelő hitelesítő adatok nevét az elnevezési szabványoknak megfelelően. 
3. Írja be a hozzáférési azonosítóját a **Felhasználónév** mezőbe. 
4. Mindkét jelszómezőbe írja be a titkos hozzáférési kulcsot.

    ![Új hitelesítő adatok létrehozása](../media/credentials/credential-create.png)

5. Ha a többtényezős hitelesítés jelölőnégyzet be van jelölve, törölje a jelet a jelölőnégyzetből. 
6. Az új hitelesítő adatok mentéséhez kattintson a **Létrehozás** gombra.

> [!NOTE]
> Az Azure Automation nem támogatja a többtényezős hitelesítést használó felhasználói fiókokat.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Új hitelesítő egység létrehozása a Windows PowerShell használatával

A következő példa bemutatja, hogyan hozhat létre egy új Automation hitelesítő adatokat. Az `PSCredential` objektum először a névvel és a jelszóval jön létre, majd a hitelesítő adatok létrehozásához. Ehelyett a `Get-Credential` parancsmag segítségével kérheti a felhasználót, hogy írja be a nevet és a jelszót.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>PowerShell-hitelesítő adatok használata

A runbook vagy a DSC-konfiguráció `Get-AutomationPSCredential` lekéri a hitelesítő adatokat a tevékenységgel. Ez a tevékenység `PSCredential` lekéri egy objektumot, amely használható egy tevékenység vagy parancsmag, amely hitelesítő adatokat igényel. A hitelesítő adatok objektumának tulajdonságait is lekérheti, hogy külön-külön használhassa. Az objektum a felhasználónév és a biztonságos jelszó tulajdonságaival rendelkezik. Másik lehetőségként a [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) metódussal bekeresheti a jelszó nem biztonságos verzióját képviselő [NetworkCredential](/dotnet/api/system.net.networkcredential) objektumot.

> [!NOTE]
> `Get-AzAutomationCredential`nem olvassa `PSCredential` be a hitelesítéshez használható objektumot. Csak a hitelesítő adatokkal kapcsolatos információkat tartalmaz. Ha egy runbookban hitelesítő adatokat kell használnia, `PSCredential` a `Get-AutomationPSCredential`segítségével objektumként kell beolvasnia.

### <a name="textual-runbook-example"></a>Példa szöveges runbookra

A következő példa bemutatja, hogyan használhatja a PowerShell-hitelesítő adatokat egy runbookban. Lekéri a hitelesítő adatokat, és hozzárendeli a felhasználónevét és jelszavát a változókhoz.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Hitelesítő adatokkal is hitelesítheti magát az [Azure-ban a Connect-AzAccount segítségével.](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0) A legtöbb esetben a [Futtatás másként fiókot](../manage-runas-account.md) kell használnia, és le kell kérnie a kapcsolatot a [Get-AzAutomationConnection](../automation-connections.md)kapcsolattal.


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Példa grafikus runbookra

Tevékenységet hozzáadhat `Get-AutomationPSCredential` egy grafikus runbookhoz úgy, hogy a jobb gombbal a grafikus szerkesztő Könyvtár ablaktáblájában a hitelesítő adatokra kattint, és a **Hozzáadás a vászonhoz parancsot**választja.

![Hitelesítő adatok hozzáadása a vászonhoz](../media/credentials/credential-add-canvas.png)

Az alábbi képen egy példa egy hitelesítő adatok használata egy grafikus runbook. Ebben az esetben a hitelesítő adat hitelesítést biztosít az Azure-erőforrások runbookjának hitelesítéséhez, az [Azure Automation Azure-beli AD-használata az Azure Automationben az Azure-hitelesítéshez](../automation-use-azure-ad.md)című témakörben leírtak szerint. Az első tevékenység lekéri a hitelesítő adatokat, amely hozzáfér az Azure-előfizetéshez. A fiókcsatlakozási tevékenység ezután ezt a hitelesítő adatot használja az azt követő tevékenységek hitelesítésének biztosításához. Itt [egy csővezeték-kapcsolat](../automation-graphical-authoring-intro.md#links-and-workflow) használatos, mivel `Get-AutomationPSCredential` egyetlen objektumot vár.  

![Hitelesítő adatok hozzáadása a vászonhoz](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Hitelesítő adatok használata DSC-konfigurációban

Míg az Azure Automation DSC-konfigurációi `Get-AutomationPSCredential`a hitelesítő adatok használatával is működhetnek, paramétereken keresztül is továbbíthatnak hitelesítő adatokat. További információ: [Konfigurációk összeállítása az Azure Automation DSC-ben című témakörben.](../automation-dsc-compile.md#credential-assets)

## <a name="using-credentials-in-python-2"></a>Hitelesítő adatok használata a Python 2-ben

A következő példa egy példa a python 2 runbookok hitelesítő adatainak elérésére.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a grafikus szerzői hivatkozásokról, olvassa el [a Hivatkozások a grafikus szerzői alkalmazásban című témakört.](../automation-graphical-authoring-intro.md#links-and-workflow)
* Az Automatizálás különböző hitelesítési módszereinek megismeréséhez olvassa el az [Azure Automation Security](../automation-security-overview.md)című témakört.
* A grafikus runbookok első lépései [Az Első grafikus runbook című témakörben](../automation-first-runbook-graphical.md)látható.
* A PowerShell-alapú munkafolyamat-runbookok első lépéseit [Az első PowerShell-alapú munkafolyamat-runbookom](../automation-first-runbook-textual.md) című témakör ismerteti.
* A Python 2 runbookokkal való ismerkedésről az [Első Python 2 runbook](../automation-first-runbook-textual-python2.md)című témakörben van. 
