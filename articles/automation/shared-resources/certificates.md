---
title: A tanúsítvány eszközei a Azure Automationban
description: A tanúsítványok biztonságosan Azure Automation, így a runbookok vagy DSC-konfigurációk is hozzáférhetnek az Azure-beli és a külső gyártótól származó erőforrások hitelesítéséhez.  Ez a cikk ismerteti a tanúsítványok részleteit, valamint azt, hogy miként dolgozhat velük a szöveges és a grafikus létrehozásban.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a66f73e028594cf90f1fa1765910a3df3adbad1a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849479"
---
# <a name="certificate-assets-in-azure-automation"></a>A tanúsítvány eszközei a Azure Automationban

A tanúsítványokat a rendszer biztonságosan tárolja Azure Automation, hogy a runbookok vagy DSC-konfigurációk hozzáférhessenek a **Get-AzureRmAutomationCertificate** tevékenységgel Azure Resource Manager erőforrásaihoz. Ez a funkció lehetővé teszi, hogy olyan runbookok-és DSC-konfigurációkat hozzon létre, amelyek tanúsítványokat használnak a hitelesítéshez, vagy hozzáadja őket az Azure-hoz vagy külső erőforrásokhoz

>[!NOTE]
>Azure Automation a biztonságos adategységek közé tartoznak a hitelesítő adatok, a tanúsítványok, a kapcsolatok és a titkosított változók. Ezeket az eszközöket a rendszer titkosítja és tárolja Azure Automation az egyes Automation-fiókokhoz generált egyedi kulcs használatával. Ezt a kulcsot a rendszer felügyelt Key Vault tárolja. A biztonságos eszköz tárolása előtt a kulcs betöltődik Key Vault, majd az eszköz titkosítására szolgál. Ezt a folyamatot a Azure Automation felügyeli.

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-parancsmagok

A AzureRM az alábbi táblázatban található parancsmagokkal automatizálható a hitelesítő adatok eszközei a Windows PowerShell használatával. A [AzureRM. Automation modul](/powershell/azure/overview)részét képezik, amely az Automation runbookok és a DSC-konfigurációkhoz is használható.

|A  parancsmagjai|Leírás|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Egy runbook vagy DSC-konfigurációban használandó tanúsítvány információinak beolvasása. A tanúsítvány csak a Get-AutomationCertificate tevékenységből kérhető le.|
|[Új – AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Új tanúsítvány létrehozása Azure Automationba.|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Eltávolít egy tanúsítványt a Azure Automationból.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Beállítja egy meglévő tanúsítvány tulajdonságait, beleértve a tanúsítványfájl feltöltését és a. pfx jelszavának beállítását.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Feltölt egy szolgáltatási tanúsítványt a megadott felhőalapú szolgáltatáshoz.|

## <a name="activities"></a>Activities (Tevékenységek)

Az alábbi táblázatban szereplő tevékenységek a tanúsítványok runbook és DSC-konfigurációkhoz való elérésére szolgálnak.

| Activities (Tevékenységek) | Leírás |
|:---|:---|
|Get-AutomationCertificate|Lekéri egy runbook vagy DSC-konfigurációban használandó tanúsítványt. Egy [System. Security. kriptográfiai. X509Certificates. x509certificate2)](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) objektumot ad vissza.|

> [!NOTE] 
> Ne használjon változókat a **Get-AutomationCertificate** – RUNBOOK vagy DSC konfiguráció – Name paraméterében, mivel az megnehezíti a runbookok-vagy DSC-konfiguráció és az Automation-változók közötti függőségek felfedését a tervezési időszakban.

## <a name="python2-functions"></a>Python2 függvények

A következő táblázatban szereplő függvény a Python2-runbook lévő tanúsítványok elérésére szolgál.

| Függvény | Leírás |
|:---|:---|
| automationassets. get_automation_certificate | Adatokat kér le egy tanúsítvány tárgyáról. |

> [!NOTE]
> Az Asset functions eléréséhez importálnia kell a **automationassets** modult a Python-runbook elején.

## <a name="creating-a-new-certificate"></a>Új tanúsítvány létrehozása

Új tanúsítvány létrehozásakor fel kell töltenie egy. cer vagy. pfx fájlt Azure Automationba. Ha a tanúsítványt exportálható jelöli meg, akkor a Azure Automation tanúsítványtárolóból is átviheti. Ha nem exportálható, akkor csak a runbook vagy DSC-konfiguráción belüli aláíráshoz használható. Azure Automation megköveteli, hogy a tanúsítvány rendelkezzen a szolgáltatóval: **Microsoft Enhanced RSA és AES kriptográfiai szolgáltató**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Új tanúsítvány létrehozása a Azure Portal

1. Az Automation-fiókban kattintson az **eszközök** csempére az **eszközök** lap megnyitásához.
2. Kattintson a **tanúsítványok** csempére a **tanúsítványok** lap megnyitásához.
3. Kattintson a **tanúsítvány hozzáadása** lehetőségre az oldal tetején.
4. A **név** mezőben adja meg a tanúsítvány nevét.
5. Egy. cer-vagy. pfx-fájl tallózásához kattintson **a fájl kiválasztása** elemre a **tanúsítványfájl feltöltése**területen. Ha. pfx-fájlt választ, adjon meg egy jelszót, és hogy exportálható-e.
6. Kattintson a **Létrehozás** gombra az új tanúsítvány-eszköz mentéséhez.

### <a name="to-create-a-new-certificate-with-powershell"></a>Új tanúsítvány létrehozása a PowerShell-lel

Az alábbi példa bemutatja, hogyan hozhat létre egy új Automation-tanúsítványt, és hogyan jelölheti meg exportálható. Ezzel importál egy meglévő. pfx-fájlt.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Új tanúsítvány létrehozása Resource Manager-sablonnal

Az alábbi példa bemutatja, hogyan helyezhet üzembe egy tanúsítványt az Automation-fiókban egy Resource Manager-sablonnal a PowerShell használatával:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzureRmResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Tanúsítvány használata

Ha tanúsítványt szeretne használni, használja a **Get-AutomationCertificate** tevékenységet. Nem használhatja a [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) parancsmagot, mivel az adatokat ad vissza a tanúsítvány objektumáról, a tanúsítványról azonban nem.

### <a name="textual-runbook-sample"></a>Szöveges runbook minta

Az alábbi mintakód bemutatja, hogyan adhat hozzá egy tanúsítványt egy felhőalapú szolgáltatáshoz egy runbook. Ebben a példában a jelszót egy titkosított Automation-változóból kéri le a rendszer.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Grafikus runbook minta

A **Get-AutomationCertificate** grafikus runbook való hozzáadásához kattintson a jobb gombbal a tanúsítványra a könyvtár ablaktáblán, és válassza a **Hozzáadás a vászonhoz**lehetőséget.

![Tanúsítvány hozzáadása a vászonhoz](../media/certificates/automation-certificate-add-to-canvas.png)

Az alábbi képen egy példa látható a tanúsítvány grafikus runbook való használatára. Ez megegyezik az előző példával, amely bemutatja, hogyan adhat hozzá egy tanúsítványt egy felhőalapú szolgáltatáshoz egy szöveges runbook.

![Példa grafikus készítésre](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2 minta

Az alábbi példa bemutatja, hogyan érheti el a tanúsítványokat a Python2 runbookok.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni arról, hogyan használhatók a runbook által elvégezhető tevékenységek logikai áramlását vezérlő hivatkozások, tekintse meg a [hivatkozások grafikus szerzői műveletekből](../automation-graphical-authoring-intro.md#links-and-workflow)című témakört. 
