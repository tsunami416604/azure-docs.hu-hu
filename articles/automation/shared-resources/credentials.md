---
title: Hitelesítőadat-eszközök a Azure Automation
description: A Azure Automation hitelesítő adatai olyan biztonsági hitelesítő adatokat tartalmaznak, amelyek a runbook vagy DSC-konfiguráció által elért erőforrásokhoz való hitelesítéshez használhatók. Ez a cikk bemutatja, hogyan hozhat létre hitelesítő eszközöket, és hogyan használhatja őket runbook vagy DSC-konfigurációban.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 582645919825c308fce4fe3211fa601955aaf37d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850176"
---
# <a name="credential-assets-in-azure-automation"></a>Hitelesítőadat-eszközök a Azure Automation

Az Automation hitelesítőadat-objektum olyan objektumot tartalmaz, amely biztonsági hitelesítő adatokat, például felhasználónevet és jelszót tartalmaz. A runbookok és a DSC-konfigurációk olyan parancsmagokat használhatnak, amelyek elfogadnak egy PSCredential objektumot a hitelesítéshez, vagy kinyerik a PSCredential-objektum felhasználónevét és jelszavát, hogy a hitelesítést igénylő alkalmazások vagy szolgáltatások számára is elérhetővé válnak. A hitelesítő adatok tulajdonságai biztonságosan tárolódnak Azure Automationban, és a runbook vagy DSC-konfigurációban a [Get-AutomationPSCredential](#activities) tevékenységgel érhetők el.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Azure Automation a biztonságos adategységek közé tartoznak a hitelesítő adatok, a tanúsítványok, a kapcsolatok és a titkosított változók. Ezeket az eszközöket a rendszer titkosítja és tárolja Azure Automation az egyes Automation-fiókokhoz generált egyedi kulcs használatával. Ezt a kulcsot Key Vault tárolja. A biztonságos eszköz tárolása előtt a kulcs betöltődik Key Vault, majd az eszköz titkosítására szolgál.

## <a name="azure-classic-powershell-cmdlets"></a>Klasszikus Azure PowerShell-parancsmagok

Az alábbi táblázatban található parancsmagokkal automatizálható a hitelesítő adatok eszköze a Windows PowerShell használatával.  A [Azure PowerShell modul](/powershell/azure/overview)részeként szállítják, amely az Automation runbookok és a DSC-konfigurációkhoz is használható.

| A  parancsmagjai | Leírás |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/servicemanagement/azure/get-azureautomationcredential) |A hitelesítő adatokkal kapcsolatos adatok beolvasása. A hitelesítő adatokat csak a **Get-AutomationPSCredential** tevékenységből kérheti le. |
| [Új – AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Létrehoz egy új Automation-hitelesítő adatot. |
| [Remove-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Eltávolít egy Automation-hitelesítő adatot. |
| [Set-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Egy meglévő Automation-hitelesítő adat tulajdonságainak beállítása. |

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-parancsmagok

A AzureRM az alábbi táblázatban található parancsmagokkal automatizálható a hitelesítő adatok eszközei a Windows PowerShell használatával.  A [AzureRM. Automation modul](/powershell/azure/overview)részét képezik, amely az Automation runbookok és a DSC-konfigurációkhoz is használható.

| A  parancsmagjai | Leírás |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential) |A hitelesítő adatokkal kapcsolatos adatok beolvasása. Ez nem ad vissza PSCredential objektumot.  |
| [Új – AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential) |Létrehoz egy új Automation-hitelesítő adatot. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential) |Eltávolít egy Automation-hitelesítő adatot. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential) |Egy meglévő Automation-hitelesítő adat tulajdonságainak beállítása. |

## <a name="activities"></a>Activities (Tevékenységek)

Az alábbi táblázatban szereplő tevékenységek a hitelesítő adatok runbook és DSC-konfigurációkhoz való elérésére szolgálnak.

| Activities (Tevékenységek) | Leírás |
|:--- |:--- |
| Get-AutomationPSCredential |A runbook vagy DSC-konfigurációban használandó hitelesítő adatok beolvasása. Egy [System. Management. Automation. PSCredential](/dotnet/api/system.management.automation.pscredential) objektumot ad vissza. |

> [!NOTE]
> Kerülje a Get-AutomationPSCredential-Name paraméterben szereplő változók használatát, mivel ez megnehezítheti a runbookok-vagy DSC-konfigurációk közötti függőségek észlelését, valamint a hitelesítő adatokat a tervezési időben.

## <a name="python2-functions"></a>Python2 függvények

A következő táblázatban szereplő függvény a hitelesítő adatok Python2-runbook való elérésére szolgál.

| Függvény | Leírás |
|:---|:---|
| automationassets. get_automation_credential | A hitelesítő adatokkal kapcsolatos adatok beolvasása. |

> [!NOTE]
> Az Asset functions eléréséhez importálnia kell a "automationassets" modult a Python-runbook tetején.

## <a name="creating-a-new-credential-asset"></a>Új hitelesítőadat-eszköz létrehozása

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Új hitelesítőadat-eszköz létrehozása a Azure Portal

1. Az Automation-fiókban válassza a **hitelesítő adatok** lehetőséget a **megosztott erőforrások**területen.
1. Kattintson **a + hitelesítő adat hozzáadása**lehetőségre.
1. Töltse ki az űrlapot, és kattintson a **Létrehozás** gombra az új hitelesítő adat mentéséhez.

> [!NOTE]
> A többtényezős hitelesítést használó felhasználói fiókok használata nem támogatott Azure Automationban.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Új hitelesítőadat-eszköz létrehozása a Windows PowerShell használatával

Az alábbi példák bemutatják, hogyan hozhat létre új Automation-hitelesítő adatokat. A rendszer először létrehoz egy PSCredential objektumot a névvel és a jelszóval, majd a hitelesítő adatok létrehozásához használja. Azt is megteheti, hogy a **Get-hitelesítőadat** parancsmag használatával kéri a név és a jelszó megadását.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>PowerShell-hitelesítő adat használata

A hitelesítő adatokat egy runbook vagy DSC-konfigurációban kell lekérnie a **Get-AutomationPSCredential** tevékenységgel. Ez egy [PSCredential objektumot](/dotnet/api/system.management.automation.pscredential) ad vissza, amelyet egy PSCredential paramétert igénylő tevékenységgel vagy parancsmaggal is használhat. A hitelesítőadat-objektum tulajdonságait egyenként is lekérheti. Az objektum rendelkezik egy tulajdonsággal a felhasználónévhez és a biztonságos jelszóhoz, vagy használhatja a **GetNetworkCredential** metódust egy olyan [NetworkCredential](/dotnet/api/system.net.networkcredential) -objektum visszaadásához, amely a jelszó nem védett verzióját fogja biztosítani.

> [!NOTE]
> A **Get-AzureRmAutomationCredential** nem ad vissza olyan **PSCredential** , amely hitelesítésre használható. Csak a hitelesítő adatokkal kapcsolatos információkat tartalmazza. Ha egy runbook hitelesítő adatot kell használnia, a **Get-AutomationPSCredential** használatával kell lekérnie a **PSCredential** objektumot.

### <a name="textual-runbook-sample"></a>Szöveges runbook minta

Az alábbi mintaparancsok bemutatják, miként használhatja a PowerShell-hitelesítő adatokat a runbookokban. Ebben a példában a rendszer beolvassa a hitelesítő adatokat, valamint a hozzá tartozó felhasználónevet és jelszót.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Az Azure-ban való hitelesítéshez hitelesítő adatokat is használhat a [AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount)használatával. A legtöbb esetben használjon [futtató fiókot](../manage-runas-account.md) , és kérje le a [Get-AutomationConnection](../automation-connections.md).

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Grafikus runbook minta

A **Get-AutomationPSCredential** tevékenység grafikus runbook való hozzáadásához kattintson a jobb gombbal a hitelesítő adatokra a grafikus szerkesztő könyvtár paneljén, és válassza a **Hozzáadás a vászonhoz**lehetőséget.

![Hitelesítő adat hozzáadása a vászonhoz](../media/credentials/credential-add-canvas.png)

Az alábbi képen egy példa látható a hitelesítő adatok grafikus runbook való használatára.  Ebben az esetben az Azure-erőforrások hitelesítésének megadására használják a runbook az Azure [ad felhasználói fiókkal](../automation-create-aduser-account.md)való hitelesítés runbookok.  Az első tevékenység lekéri az Azure-előfizetéshez hozzáférő hitelesítő adatokat.  Az **Add-AzureAccount** tevékenység ezt a hitelesítő adatot használja az azt követő tevékenységek hitelesítésének biztosítására.  Itt van egy [folyamat hivatkozása](../automation-graphical-authoring-intro.md#links-and-workflow) , mert a **Get-AutomationPSCredential** egyetlen objektumot vár.  

![Hitelesítő adat hozzáadása a vászonhoz](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>PowerShell-hitelesítő adatok használata a DSC-ben

Habár a Azure Automation DSC-konfigurációk a **Get-AutomationPSCredential**használatával hivatkozhatnak a hitelesítő adatokra, a hitelesítő adatok a paramétereken keresztül is átadhatók, ha szeretnék. További információ: [konfigurációk fordítása Azure Automation DSC-ben](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Hitelesítő adatok használata a Python2-ben

Az alábbi példa egy példát mutat be a hitelesítő adatok elérésére a Python2-runbookok.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni a grafikus létrehozással kapcsolatos hivatkozásokról, tekintse meg a [hivatkozásokat a grafikus szerzői](../automation-graphical-authoring-intro.md#links-and-workflow) műveletekben
* Az Automation különböző hitelesítési módszereinek megismeréséhez lásd: [Azure Automation biztonság](../automation-security-overview.md)
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](../automation-first-runbook-graphical.md).
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](../automation-first-runbook-textual.md)
* A Python2 runbookok megkezdéséhez tekintse meg [az első Python2 runbook](../automation-first-runbook-textual-python2.md) 
