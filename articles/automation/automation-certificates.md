---
title: Azure Automation szolgáltatásbeli tanúsítvány eszközök
description: Tanúsítványok tárolhatja biztonságosan Azure Automation, azok a runbookok vagy DSC-konfigurációk hitelesítése az Azure és a külső erőforrások elérhetők.  Ez a cikk ismerteti a tanúsítványok és a szöveges és a grafikus szerzői őket munkavégzés részleteit.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 70d5c777f98a1e09125c7ef42ba597009bde084b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="certificate-assets-in-azure-automation"></a>Azure Automation szolgáltatásbeli tanúsítvány eszközök

Tanúsítványok tárolhatja biztonságosan Azure Automation, azok a runbookok vagy a DSC-konfigurációk használatával elérhetők a **Get-AzureRmAutomationCertificate** Azure Resource Manager erőforrások tevékenység. Ez a funkció lehetővé teszi a runbookok és a hitelesítési tanúsítványokat használó DSC-konfigurációk létrehozásához, vagy hozzáadja őket az Azure és a külső erőforrásokhoz.

>[!NOTE]
>Az Azure Automationben biztonságos eszközök közé tartozik a hitelesítő adatokat, a tanúsítványokat, a kapcsolatok és a titkosított változók. Ezek az eszközök titkosítva, és tárolja az Azure Automationben létrehozott egyedi kulcs segítségével minden egyes automation-fiókhoz. Ezt a kulcsot a Key Vault van tárolva. A kulcs tárolása biztonságos eszköz, mielőtt Key Vault betöltődnek és majd az eszköz titkosításához használt.

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-parancsmagok
A következő táblázatban található parancsmagokkal AzureRM, létrehozását és kezelését az automatizálási hitelesítő eszközök a Windows PowerShell használatával történik. Részét képezi a [AzureRM.Automation modul](/powershell/azure/overview) elérhető Automation-forgatókönyveket és a DSC-konfigurációk.

|Parancsmagok|Leírás|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Lekéri az információkat a runbookot vagy a DSC-konfiguráció használandó tanúsítványt. Maga a tanúsítvány csak Get-AutomationCertificate tevékenység kérhetnek le.|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Létrehoz egy új tanúsítványt az Azure Automation.|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Azure Automation szolgáltatásbeli tanúsítvány eltávolítása.|Létrehoz egy új tanúsítványt az Azure Automation.
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Beleértve a tanúsítványfájl feltöltését és a jelszót a .pfx fájlhoz beállítás meglévő tanúsítvány tulajdonságainak beállítása.|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|A megadott felhőszolgáltatás egy szolgáltatástanúsítványt feltöltését.|

## <a name="activities"></a>Tevékenységek
Az alábbi táblázatban a tevékenységek a runbookok és a DSC-konfigurációk tanúsítványok eléréséhez használt.

| Tevékenységek | Leírás |
|:---|:---|
|Get-AutomationCertificate|Lekérdezi a runbookot vagy a DSC-konfiguráció használandó tanúsítványt. Értéket ad vissza egy [System.Security.Cryptography.X509Certificates.X509Certificate2](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate2.aspx) objektum.|

> [!NOTE] 
> Kerülendő a változók használata a – Name paraméterében **Get-AutomationCertificate** a runbookot vagy a DSC-konfiguráció, mert növeli a runbookok vagy a DSC-konfiguráció és az Automation-változók közti függőségek tervezési időben.

## <a name="python2-functions"></a>Python2 funkciók

A függvény a következő táblázat a tanúsítványokat egy Python2 runbook eléréséhez használt.

| Függvény | Leírás |
|:---|:---|
| automationassets.get_automation_certificate | A tanúsítvány eszköz adatainak kéri le. |

> [!NOTE]
> Importálnia kell a **automationassets** modulban, az eszköz funkciók eléréséhez a Python runbook elejére.

## <a name="creating-a-new-certificate"></a>Egy új tanúsítvány létrehozása

Amikor létrehoz egy új tanúsítványt, .cer vagy .pfx-fájlt töltsön fel az Azure Automation. Ha a tanúsítvány exportálhatóként, majd azt át is az Azure Automation tanúsítványtároló kívül. Ha nem exportálható, majd csak használat belül a runbookot vagy a DSC-konfiguráció az aláíráshoz. Azure Automation szolgáltatásbeli megköveteli a tanúsítványt szeretné, hogy a szolgáltató: **Microsoft Enhanced RSA és az AES kriptográfiai szolgáltató**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Új tanúsítvány létrehozása az Azure portállal

1. Az Automation-fiók, kattintson a **eszközök** csempére kattintva nyissa meg a **eszközök** panelen.
1. Kattintson a **tanúsítványok** csempére kattintva nyissa meg a **tanúsítványok** panelen.
1. Kattintson a **tanúsítvány hozzáadása** a panel tetején.
1. Adjon meg egy nevet a tanúsítványt a **neve** mezőbe.
1. Keresse meg az egy .cer vagy .pfx fájlt, kattintson a **válasszon ki egy fájlt** alatt **tanúsítvány-fájl feltöltése**. Ha kiválaszt egy .pfx fájlt, adja meg a jelszót, és hogy engedélyezett-e exportálva.
1. Kattintson a **létrehozása** menti az új tanúsítvány adategység.

### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Új tanúsítvány létrehozása a Windows PowerShell használatával

A következő példa bemutatja, hogyan hozzon létre egy új Automation-tanúsítvány, és jelölje be a exportálható. Ez importálja a meglévő .pfx fájlt.

```powershell-interactive
$certName = 'MyCertificate'
$certPath = '.\MyCert.pfx'
$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup
```

## <a name="using-a-certificate"></a>Olyan tanúsítványt használ

A tanúsítvány használatához a **Get-AutomationCertificate** tevékenység. Nem használhatja a [Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx) parancsmag, mivel a tanúsítvány eszköz, de nem maga a tanúsítvány kapcsolatos információkat ad vissza.

### <a name="textual-runbook-sample"></a>Szöveges forgatókönyvként minta

Az alábbi mintakód bemutatja, hogyan tanúsítvány hozzáadása a runbookban egy felhőalapú szolgáltatás. Ez a példa a jelszó lekért egy titkosított automation változó.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Grafikus forgatókönyv minta

Hozzáadhat egy **Get-AutomationCertificate** egy grafikus forgatókönyvnek a tanúsítványt a könyvtár ablaktáblán grafikus szerkesztő csomagot jobb gombbal, majd válassza a **vászonra Hozzáadás**.

![Tanúsítvány felvétele a vászonra](media/automation-certificates/automation-certificate-add-to-canvas.png)

Az alábbi ábrán például egy grafikus runbookban levő olyan tanúsítványt használ. Ez megegyezik az előző példában a tanúsítvány hozzáadásához egy felhőszolgáltatáshoz szöveges runbookból.

![Példa grafikus készítése ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2 minta
A következő példa bemutatja, hogyan Python2 runbookok tanúsítványok eléréséhez.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert 
```

## <a name="next-steps"></a>További lépések

- A tevékenységek a runbookban az célja, hogy végre logikai üzenetáramlásának szabályozására hivatkozások használata kapcsolatos további információkért lásd: [hivatkozások grafikus szerzői](automation-graphical-authoring-intro.md#links-and-workflow). 
