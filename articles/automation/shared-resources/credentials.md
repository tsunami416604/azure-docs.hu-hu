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
ms.openlocfilehash: 680e68d17637d71c1a1e5e8cfa539ee90028ac4e
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478761"
---
# <a name="credential-assets-in-azure-automation"></a>Az Azure Automation hitelesítési adategységei

Az Automation hitelesítő adatok olyan objektumot tartalmaznak, amely biztonsági hitelesítő adatokat, például felhasználónevet és jelszót tartalmaz. A Runbookok és a DSC-konfigurációk olyan parancsmagokat használnak, amelyek elfogadják a [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) objektumot a hitelesítéshez. Azt is megteheti, hogy kibontja `PSCredential` az objektum felhasználónevét és jelszavát, hogy bizonyos, hitelesítést igénylő alkalmazásokat vagy szolgáltatást biztosítson. Az Azure Automation biztonságosan tárolja a hitelesítő adatok tulajdonságait és a tulajdonságokhoz való hozzáférést egy runbook vagy DSC-konfigurációban a [Get-AutomationPSCredential](#activities-used-to-access-credentials) tevékenységgel.

> [!NOTE]
> Az Azure Automation biztonságos eszközei közé tartoznak a hitelesítő adatok, tanúsítványok, kapcsolatok és titkosított változók. Ezek az eszközök titkosítva vannak, és az Azure Automation-ben egy egyedi kulcs használatával, amely minden Automation-fiókhoz létrejön. Ez a kulcs a Key Vault ban tárolódik. A biztonságos eszköz tárolása előtt a kulcs betöltődik a Key Vaultból, és ezután az eszköz titkosításához használható.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Hitelesítő adatokhoz használt Azure PowerShell Az-parancsmagok

Az Azure PowerShell Az modul esetében az alábbi táblázatban található parancsmagok az Automation hitelesítő adatok létrehozásához és kezeléséhez szolgálnak a Windows PowerShell használatával. Az [Az.Automation modul](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)részeként szállítanak, amely az Automation runbookok és a DSC-konfigurációk ban használható. Lásd: [Az modul támogatás az Azure Automationben.](https://docs.microsoft.com/azure/automation/az-modules)

| Parancsmag | Leírás |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Hitelesítő adatok lekérése. Ez a parancsmag nem ad vissza PSCredential objektumot.  |
| [Új-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Új automatizálási hitelesítő adatok létrehozása. |
| [Eltávolítás-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Eltávolítja az Automatizáláshitelesítő adatokat. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Beállítja egy meglévő Automation-hitelesítő adatok tulajdonságait. |

## <a name="activities-used-to-access-credentials"></a>A hitelesítő adatok eléréséhez használt tevékenységek

Az alábbi táblázatban szereplő tevékenységek runbookok és DSC-konfigurációk hitelesítő adatainak elérésére szolgálnak.

| Tevékenység | Leírás |
|:--- |:--- |
| `Get-AutomationPSCredential` |Beszerzi a runbook vagy dsc konfigurációban használandó hitelesítő adatokat. A hitelesítő adat `PSCredential` objektum formájában van. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Hitelesítő adatokat kér a felhasználónévhez és a jelszóhoz. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Létrehoz egy hitelesítő egységet. |

Az Azure Automation authoring toolkit használatával `Get-AutomationPSCredential` történő helyi fejlesztéshez a parancsmag az [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)szerelvény része. Az Automation környezettel dolgozó Azure számára a `Orchestrator.AssetManagement.Cmdlets`parancsmag a. Lásd: [Modulok kezelése az Azure Automationben.](modules.md)

A kódban `PSCredential` lévő objektumok lekéréséhez telepítheti a [Microsoft Azure Automation ISE bővítményt a PowerShell ISE-hez.](https://github.com/azureautomation/azure-automation-ise-addon)

```azurepowershell
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser -Force
```

A parancsfájl szükség esetén importálhatja a szükséges modult is, ahogy az a következő példában is látható: 

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Ne használjon változókat `Name` a `Get-AutomationPSCredential`paraméterben. Használatuk megnehezítheti a runbookok vagy a DSC-konfigurációk és a hitelesítő adatok közötti függőségek felderítését a tervezés időpontjában.

## <a name="python2-functions-that-access-credentials"></a>Python2-függvények, amelyek hozzáférnek a hitelesítő adatokhoz

Az alábbi táblázatban található függvény a Python2 runbook hitelesítő adatainak elérésére szolgál.

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
5. Ha a többtényezős hitelesítés jelölőnégyzet be van jelölve, törölje a jelet a jelölőnégyzetből. 
6. Az új hitelesítő adatok mentéséhez kattintson a **Létrehozás** gombra.

> [!NOTE]
> Az Azure Automation nem támogatja a többtényezős hitelesítést használó felhasználói fiókokat.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Új hitelesítő egység létrehozása a Windows PowerShell használatával

A következő példa bemutatja, hogyan hozhat létre egy új Automation hitelesítő adatokat. Az `PSCredential` objektum először a névvel és a jelszóval jön létre, majd a hitelesítő adatok létrehozásához. Másik lehetőségként a `Get-Credential` parancsmag segítségével kérheti a felhasználót, hogy írja be a nevet és a jelszót.


```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>PowerShell-hitelesítő adatok használata

A runbook vagy a DSC-konfiguráció `Get-AutomationPSCredential` lekéri a hitelesítő adatokat a tevékenységgel. Ez a tevékenység `PSCredential` lekéri egy objektumot, amely használható egy tevékenység vagy parancsmag, amely hitelesítő adatokat igényel. A hitelesítő adatok objektumának tulajdonságait is lekérheti, hogy külön-külön használhassa. Az objektum tulajdonságai a felhasználónévhez és a biztonságos jelszóhoz. Másik lehetőségként a [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) metódussal bekeresheti a jelszó nem biztonságos verzióját képviselő [NetworkCredential](/dotnet/api/system.net.networkcredential) objektumot.

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

Az alábbi képen egy példa egy hitelesítő adatok használata egy grafikus runbook. Ebben az esetben a hitelesítő adat az Azure-erőforrások runbookjának hitelesítésére szolgál, az [Azure Automation Azure-beli AD használata az Azure Automationben az Azure Automationben történő hitelesítéshez.](../automation-use-azure-ad.md) Az első tevékenység lekéri a hitelesítő adatokat, amely hozzáfér az Azure-előfizetéshez. A fiókcsatlakozási tevékenység ezután ezt a hitelesítő adatot használja az azt követő tevékenységek hitelesítésének biztosításához. Itt [egy csővezeték-kapcsolat](../automation-graphical-authoring-intro.md#links-and-workflow) használatos, mivel `Get-AutomationPSCredential` egyetlen objektumot vár.  

![Hitelesítő adatok hozzáadása a vászonhoz](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Hitelesítő adatok használata DSC-konfigurációban

Míg az Azure Automation DSC-konfigurációi `Get-AutomationPSCredential`a hitelesítő adatok használatával is működhetnek, paramétereken keresztül is továbbíthatnak hitelesítő adatokat. További információ: [Konfigurációk összeállítása az Azure Automation DSC-ben című témakörben.](../automation-dsc-compile.md#credential-assets)

## <a name="using-credentials-in-python2"></a>Hitelesítő adatok használata a Python2-ben

A következő példa egy példa a python2 runbookok hitelesítő adatainak elérésére.


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
* A Python2-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első Python2-forgatókönyvem](../automation-first-runbook-textual-python2.md). 
