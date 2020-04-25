---
title: Hitelesítő adatok kezelése Azure Automationban
description: A Azure Automation hitelesítő adatai olyan biztonsági hitelesítő adatokat tartalmaznak, amelyek a runbook vagy DSC-konfiguráció által elért erőforrásokhoz való hitelesítéshez használhatók. Ez a cikk bemutatja, hogyan hozhat létre hitelesítő eszközöket, és hogyan használhatja őket runbook vagy DSC-konfigurációban.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4226a625918be378b14e14c55fe4dd4ca5c398d5
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82136685"
---
# <a name="manage-credentials-in-azure-automation"></a>Hitelesítő adatok kezelése Azure Automationban

Az Automation hitelesítőadat-adategység olyan objektumot tartalmaz, amely biztonsági hitelesítő adatokat tartalmaz, például egy felhasználónevet és egy jelszót. A runbookok és a DSC-konfigurációk olyan parancsmagokat használnak, amelyek elfogadnak egy [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) objektumot a hitelesítéshez. Azt is megteheti, hogy kinyeri az `PSCredential` objektum felhasználónevét és jelszavát, hogy a hitelesítést igénylő alkalmazások vagy szolgáltatások számára biztosítson. 

> [!NOTE]
> Azure Automation a biztonságos adategységek közé tartoznak a hitelesítő adatok, a tanúsítványok, a kapcsolatok és a titkosított változók. Ezeket az eszközöket a rendszer titkosítja és tárolja Azure Automation az egyes Automation-fiókokhoz generált egyedi kulcs használatával. Ezt a kulcsot Key Vault tárolja. A biztonságos eszköz tárolása előtt a kulcs betöltődik Key Vault, majd az eszköz titkosítására szolgál.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](../automation-update-azure-modules.md).

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Azure PowerShell az parancsmagok a hitelesítőadat-eszközökhöz

A Azure PowerShell az modul részeként az alábbi táblázatban található parancsmagokkal automatizálható a hitelesítő adatok eszközeinek létrehozása és kezelése a Windows PowerShell használatával. Az az [. Automation modulba](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)kerülnek, amely az Automation runbookok és a DSC-konfigurációk esetében használható. Lásd: [az modul support in Azure Automation](https://docs.microsoft.com/azure/automation/az-modules).

| Parancsmag | Leírás |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |A hitelesítő adatokkal kapcsolatos metaadatokat tartalmazó [CredentialInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps) objektum beolvasása. A parancsmag nem kérdezi `PSCredential` le magát az objektumot.  |
| [Új – AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Létrehoz egy új Automation-hitelesítő adatot. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Eltávolít egy Automation-hitelesítő adatot. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Egy meglévő Automation-hitelesítő adat tulajdonságainak beállítása. |

## <a name="activities-used-to-access-credentials"></a>A hitelesítő adatok eléréséhez használt tevékenységek

A következő táblázatban szereplő tevékenységek a hitelesítő adatoknak a grafikus runbookok és a DSC-konfigurációkhoz való elérésére szolgálnak. Példák a tevékenységek használatára: [grafikus létrehozás Azure Automationban](../automation-graphical-authoring-intro.md#activities).

| Tevékenység | Leírás |
|:--- |:--- |
| `Get-AutomationPSCredential` |Lekéri `PSCredential` egy RUNBOOK vagy DSC-konfigurációban használandó objektumot. Leggyakrabban a `Get-AzAutomationCredential` parancsmag helyett ezt a tevékenységet kell használnia, mivel az utóbbi csak a hitelesítő adatokat kéri le. Ezek az információk általában nem hasznosak egy másik parancsmagnak való továbbításra. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Hitelesítő adat beolvasása a Felhasználónév és a jelszó megadásával. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Egy hitelesítőadat-eszköz létrehozása. |

Ha objektumokat `PSCredential` szeretne beolvasni a kódban, importálnia kell `Orchestrator.AssetManagement.Cmdlets` a modult. További információkért lásd: [modulok kezelése Azure Automationban](modules.md).

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Kerülje a változók használatát a `Name` paraméterében. `Get-AutomationPSCredential` A használatuk megnehezítheti a runbookok vagy DSC-konfigurációk és a hitelesítő adatok közötti függőségek felderítését a tervezési időszakban.

## <a name="python-2-functions-that-access-credentials"></a>A hitelesítő adatokhoz hozzáférő Python 2 függvények

A következő táblázatban szereplő függvény a hitelesítő adatoknak a Python 2 runbook való elérésére szolgál.

| Függvény | Leírás |
|:---|:---|
| `automationassets.get_automation_credential` | A hitelesítő adatokkal kapcsolatos adatok beolvasása. |

> [!NOTE]
> Importálja `automationassets` a modult a Python-runbook tetején az eszköz funkcióinak eléréséhez.

## <a name="creating-a-new-credential-asset"></a>Új hitelesítőadat-eszköz létrehozása

Létrehozhat egy új hitelesítőadat-eszközt a Azure Portal vagy a Windows PowerShell használatával.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Új hitelesítőadat-eszköz létrehozása a Azure Portal

1. Az Automation-fiókban válassza a **hitelesítő adatok** lehetőséget a **megosztott erőforrások**területen.
1. Válassza **a hitelesítő adat hozzáadása**elemet.
2. Az új hitelesítő adatok ablaktáblán adjon meg egy megfelelő hitelesítő nevet az elnevezési szabványoknak megfelelően. 
3. Írja be a hozzáférési azonosítót a **Felhasználónév** mezőbe. 
4. A jelszó mezőben adja meg a titkos kulcs elérési kulcsát.

    ![Új hitelesítő adat létrehozása](../media/credentials/credential-create.png)

5. Ha a multi-Factor Authentication jelölőnégyzet be van jelölve, törölje azt. 
6. Kattintson a **Létrehozás** gombra az új hitelesítőadat-eszköz mentéséhez.

> [!NOTE]
> A Azure Automation nem támogatja a többtényezős hitelesítést használó felhasználói fiókokat.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Új hitelesítőadat-eszköz létrehozása a Windows PowerShell használatával

Az alábbi példa bemutatja, hogyan hozhat létre új Automation hitelesítőadat-eszközt. A `PSCredential` rendszer először a névvel és jelszóval hozza létre az objektumot, majd a hitelesítő adatok létrehozásához használja. Ehelyett a `Get-Credential` parancsmaggal kérheti a felhasználótól, hogy adjon meg egy nevet és egy jelszót.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>PowerShell-hitelesítő adat használata

A runbook vagy DSC-konfiguráció egy hitelesítőadat-eszközt kér le `Get-AutomationPSCredential` a tevékenységgel. Ez a tevékenység egy olyan `PSCredential` objektumot kérdez le, amelyet olyan tevékenységgel vagy parancsmaggal lehet használni, amely hitelesítő adatokat igényel. A hitelesítőadat-objektum tulajdonságait egyenként is lekérheti. Az objektum a Felhasználónév és a biztonságos jelszó tulajdonságaival rendelkezik. Azt is megteheti, hogy a [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) metódus használatával lekéri a jelszó nem védett verzióját képviselő [NetworkCredential](/dotnet/api/system.net.networkcredential) objektumot.

> [!NOTE]
> `Get-AzAutomationCredential`a nem kér le `PSCredential` olyan objektumot, amely hitelesítésre használható. Csak a hitelesítő adatokkal kapcsolatos információkat tartalmazza. Ha hitelesítő adatokat kell használnia egy runbook, azt a `PSCredential` használatával `Get-AutomationPSCredential`kell lekérnie objektumként.

### <a name="textual-runbook-example"></a>Szöveges runbook példa

Az alábbi példa bemutatja, hogyan használhatók PowerShell-hitelesítő adatok egy runbook. Lekéri a hitelesítő adatokat, és hozzárendeli a felhasználónevet és a jelszót a változókhoz.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Az Azure-ban való hitelesítéshez hitelesítő adatokat is használhat a [AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0)használatával. A legtöbb esetben a [futtató fiókot](../manage-runas-account.md) kell használnia, és a [Get-AzAutomationConnection](../automation-connections.md)használatával kell lekérnie a kapcsolatokat.


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Példa grafikus runbook

Hozzáadhat egy `Get-AutomationPSCredential` tevékenységet egy grafikus runbook úgy, hogy a jobb gombbal rákattint a hitelesítő adatokra a grafikus szerkesztő könyvtár paneljén, majd a **Hozzáadás a vászonhoz**lehetőségre kattint.

![Hitelesítő adat hozzáadása a vászonhoz](../media/credentials/credential-add-canvas.png)

Az alábbi képen egy példa látható a hitelesítő adatok grafikus runbook való használatára. Ebben az esetben a hitelesítő adatok hitelesítési adatokat biztosítanak az Azure-erőforrások runbook az Azure [ad-ben való hitelesítéshez Azure Automationban](../automation-use-azure-ad.md). Az első tevékenység lekéri az Azure-előfizetéshez hozzáférő hitelesítő adatokat. A fiók kapcsolódási tevékenysége ezután ezt a hitelesítő adatot használja az azt követő tevékenységek hitelesítésének biztosításához. Itt egy [folyamatra mutató hivatkozást](../automation-graphical-authoring-intro.md#links-and-workflow) kell `Get-AutomationPSCredential` használni, mivel egyetlen objektumot vár.  

![Hitelesítő adat hozzáadása a vászonhoz](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Hitelesítő adatok használata DSC-konfigurációban

Habár a Azure Automation DSC-konfigurációja a hitelesítő adatokkal `Get-AutomationPSCredential`is működhet a használatával, a hitelesítő adatokat a paraméterek segítségével is átadhatják. További információ: [konfigurációk fordítása Azure Automation DSC-ben](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python-2"></a>Hitelesítő adatok használata a Python 2-ben

Az alábbi példa egy példát mutat be a hitelesítő adatok elérésére a Python 2 runbookok.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a grafikus létrehozással kapcsolatos hivatkozásokról, tekintse meg a [hivatkozásokat a grafikus szerzői](../automation-graphical-authoring-intro.md#links-and-workflow)műveletekben.
* Az automatizálás különböző hitelesítési módszereinek megismeréséhez lásd: [Azure Automation biztonság](../automation-security-overview.md).
* A grafikus runbookok megkezdéséhez tekintse meg [az első grafikus runbook](../automation-first-runbook-graphical.md).
* A PowerShell-alapú munkafolyamat-runbookok első lépéseit [Az első PowerShell-alapú munkafolyamat-runbookom](../automation-first-runbook-textual.md) című témakör ismerteti.
* A Python 2 runbookok megkezdéséhez tekintse meg [az első Python 2 runbook](../automation-first-runbook-textual-python2.md). 
