---
title: Tanúsítványeszközök az Azure Automationben
description: Tanúsítványok tárolhatók biztonságosan az Azure Automationben, azok a runbookok és a DSC-konfigurációk hitelesítése az Azure és a külső erőforrások elérhetők.  Ez a cikk ismerteti a tanúsítványok és hogyan dolgozhatnak velük a szöveges és a grafikus szerzői műveletek részleteit.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d91a7ba8d2c05e2d24738764438ce91325743699
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330857"
---
# <a name="certificate-assets-in-azure-automation"></a>Tanúsítványeszközök az Azure Automationben

Tanúsítványok tárolhatók biztonságosan az Azure Automation, a runbook vagy DSC-konfigurációk használatával elérhető lesz a **Get-AzureRmAutomationCertificate** tevékenység az Azure Resource Manager-erőforrásokat. Ez a funkció lehetővé teszi, hogy hozzon létre a runbookokat és a DSC-konfigurációk, amelyek a tanúsítványokat használnak a hitelesítéshez, vagy hozzáadja őket az Azure-ban vagy külső erőforrások.

>[!NOTE]
>Az Azure Automationben biztonságos eszközök tartalmazzák, hitelesítő adatok, tanúsítványok, kapcsolatok és a titkosított változókat. Ezek az eszközök titkosítottak és a létrehozott egyedi kulcs segítségével minden automation-fiókhoz tartozó Azure Automation tárolja. Ezt a kulcsot tárolja a rendszer által felügyelt Key Vault. A kulcs tárolása egy biztonságos objektumot, előtt betöltése a Key Vaultból és majd az eszköz titkosításához használt. Ez a folyamat az Azure Automation felügyeli.

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-parancsmagok
Az alábbi táblázatban a parancsmagok AzureRM, létrehozása és kezelése automation hitelesítő eszközök a Windows PowerShell használatával történik. Részét képezi a [AzureRM.Automation modul](/powershell/azure/overview) elérhető a Automation-runbookok és a DSC-konfigurációkat használhatnak.

|Parancsmagok|Leírás|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate)|A tanúsítványt használja, a runbookból vagy DSC-konfigurációból adatait kérdezi le. Get-AutomationCertificate tevékenység csak maga a tanúsítvány lehet lekérdezni.|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Létrehoz egy új tanúsítványt az Azure Automationbe.|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Azure Automation tanúsítvány eltávolítása.|Létrehoz egy új tanúsítványt az Azure Automationbe.
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Egy meglévő, beleértve a tanúsítványfájl feltöltését és a jelszó beállítását a .pfx tanúsítvány tulajdonságainak beállítása.|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|Feltölt egy szolgáltatási tanúsítványt a megadott felhőszolgáltatás számára.|

## <a name="activities"></a>Tevékenységek
Az alábbi táblázatban felsorolt tevékenységek a runbookok és a DSC-konfigurációk tanúsítványok elérésére használhatók.

| Tevékenységek | Leírás |
|:---|:---|
|Get-AutomationCertificate|Lekérdezi a runbookból vagy DSC-konfigurációból használandó tanúsítványt. Értéket ad vissza egy [System.Security.Cryptography.X509Certificates.X509Certificate2](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate2.aspx) objektum.|

> [!NOTE] 
> Kerülendő a változók használata a – Name paraméterében **Get-AutomationCertificate** runbookból vagy DSC-konfigurációból, mert a bonyolultabbá teszi a runbookok vagy DSC-konfiguráció, és az Automation-változók közti függőségek a tervezés során.

## <a name="python2-functions"></a>Python2-funkciók

A függvény a következő táblázat a tanúsítványokat a Python2-forgatókönyvem elérésére szolgál.

| Függvény | Leírás |
|:---|:---|
| automationassets.get_automation_certificate | Egy tanúsítványeszköz adatait kérdezi le. |

> [!NOTE]
> Importálnia kell a **automationassets** modulban, a Python-alapú runbook ahhoz, hogy hozzáférhessen az eszközintelligencia függvények elejére.

## <a name="creating-a-new-certificate"></a>Egy új tanúsítvány létrehozása

Amikor létrehoz egy új tanúsítványt, az Azure Automationhöz feltölt egy .cer vagy .pfx fájlt. Ha a tanúsítvány megjelölése exportálhatóként, ezután már másolhatja azt ki az Azure Automation tanúsítványtárolójába. Ha nem exportálható, majd azt csak használható a runbook vagy DSC-konfiguráció az aláíráshoz. Az Azure Automation a tanúsítványt, hogy a szolgáltató van szükség: **A Microsoft Enhanced RSA és az AES kriptográfiai szolgáltató**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Új tanúsítvány létrehozása az Azure portal használatával

1. Az Automation-fiókjából, kattintson a **eszközök** csempére kattintva nyissa meg a **eszközök** panelen.
1. Kattintson a **tanúsítványok** csempére kattintva nyissa meg a **tanúsítványok** panelen.
1. Kattintson a **tanúsítvány hozzáadása** a panel tetején.
1. Adja meg a tanúsítvány nevét a **neve** mezőbe.
1. Keresse meg az egy .cer vagy .pfx fájlt, kattintson a **válasszon ki egy fájlt** alatt **töltsön fel egy tanúsítványfájlt**. Ha kiválaszt egy .pfx-fájlt, adja meg a jelszót és exportálható engedélyezett.
1. Kattintson a **létrehozás** új tanúsítványobjektum mentéséhez.

### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Új tanúsítvány létrehozása a Windows PowerShell-lel

Az alábbi példa bemutatja, hogyan hozhat létre egy új Automation-tanúsítvány, és megjelölése exportálhatóként. Ezzel importálja a meglévő .pfx fájlt.

```powershell-interactive
$certName = 'MyCertificate'
$certPath = '.\MyCert.pfx'
$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup
```

## <a name="using-a-certificate"></a>Egy tanúsítvány használatával

A tanúsítvány használatához használja a **Get-AutomationCertificate** tevékenység. Nem használhatja a [Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate?view=azurermps-6.6.0) parancsmag, mivel ez a tanúsítványobjektum, de nem maga a tanúsítvány kapcsolatos információkat ad vissza.

### <a name="textual-runbook-sample"></a>Minta szöveges forgatókönyv

Az alábbi mintakód bemutatja, hogyan adhat tanúsítványt a runbook egy felhőalapú szolgáltatás. Ebben a példában a jelszó lekért egy titkosított automation változó.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Grafikus forgatókönyv minta

Hozzáadhat egy **Get-AutomationCertificate** , a tanúsítványon a grafikus szerkesztő a könyvtár ablaktáblán kattintson a jobb gombbal, majd válassza a grafikus runbookok **adja hozzá a vászonhoz**.

![Tanúsítvány felvétele a vászonra](media/automation-certificates/automation-certificate-add-to-canvas.png)

Az alábbi képen egy példa olyan tanúsítványt használ a grafikus runbookokban. Ez megegyezik az előző példában, hogyan adhat hozzá egy tanúsítványt egy felhőalapú szolgáltatás egy szöveges runbookból.

![A példában a grafikus létrehozásról ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2-minta
A következő minta bemutatja, hogyan érhető el a Python2-forgatókönyvekkel tanúsítványokat.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert 
```

## <a name="next-steps"></a>További lépések

- A tevékenységek a runbookban úgy tervezték, hogy hajtsa végre a logikai átvitelvezérlés mutató hivatkozások használata kapcsolatos további információkért lásd: [hivatkozások a grafikus létrehozásról](automation-graphical-authoring-intro.md#links-and-workflow). 
