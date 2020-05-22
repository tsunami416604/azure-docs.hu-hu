---
title: Tanúsítványok kezelése a Azure Automationban
description: Ez a cikk azt ismerteti, hogyan használhatók a tanúsítványok a runbookok és a DSC-konfigurációkhoz való hozzáféréshez.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ebd4bf0ff91342229edb4295489f0cbd0970699a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745028"
---
# <a name="manage-certificates-in-azure-automation"></a>Tanúsítványok kezelése a Azure Automationban

Azure Automation a tanúsítványokat biztonságos módon tárolja a runbookok és a DSC-konfigurációk számára, a [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) parancsmag használatával Azure Resource Manager erőforrásokhoz. A biztonságos tanúsítványtároló lehetővé teszi, hogy olyan runbookok és DSC-konfigurációkat hozzon létre, amelyek tanúsítványokat használnak a hitelesítéshez, illetve hozzáadhatják őket az Azure-hoz vagy harmadik féltől származó erőforrásokhoz.

>[!NOTE]
>Azure Automation a biztonságos adategységek közé tartoznak a hitelesítő adatok, a tanúsítványok, a kapcsolatok és a titkosított változók. Az eszközök titkosítása és automatizálása az egyes Automation-fiókokhoz generált egyedi kulcs használatával történik. Az Automation tárolja a kulcsot a rendszer által felügyelt Key Vault szolgáltatásban. A biztonságos eszköz tárolása előtt az Automation betölti a kulcsot a Key Vault, majd a használatával titkosítja az eszközt. 

## <a name="powershell-cmdlets-to-access-certificates"></a>A tanúsítványok eléréséhez szükséges PowerShell-parancsmagok

Az alábbi táblázatban található parancsmagok automatizálási tanúsítványokat hoznak létre és kezelhetnek a PowerShell-lel. Az az [modulok](modules.md#az-modules)részét képezik.

|Parancsmag |Leírás|
| --- | ---|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Egy runbook vagy DSC-konfigurációban használandó tanúsítvány információinak beolvasása. A tanúsítványt csak a belső parancsmag használatával kérheti le `Get-AutomationCertificate` .|
|[Új – AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Új tanúsítvány létrehozása az Automationben.|
|[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Eltávolít egy tanúsítványt az Automationből.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Beállítja egy meglévő tanúsítvány tulajdonságait, beleértve a tanúsítványfájl feltöltését és a **. pfx** fájl jelszavának beállítását.|

Az [Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate) parancsmag használható a megadott felhőalapú szolgáltatáshoz tartozó szolgáltatási tanúsítvány feltöltésére is.

## <a name="internal-cmdlets-to-access-certificates"></a>A tanúsítványok elérésére szolgáló belső parancsmagok

A következő táblázatban található belső parancsmag a runbookok lévő tanúsítványok elérésére szolgál. Ez a parancsmag a globális modulhoz tartozik `Orchestrator.AssetManagement.Cmdlets` . További információ: [belső parancsmagok](modules.md#internal-cmdlets).

| Belső parancsmag | Leírás |
|:---|:---|
|`Get-AutomationCertificate`|Lekéri egy runbook vagy DSC-konfigurációban használandó tanúsítványt. Egy [System. Security. kriptográfiai. X509Certificates. x509certificate2)](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) objektumot ad vissza.|

> [!NOTE] 
> Ne használjon változókat a `Name` `Get-AutomationCertificate` RUNBOOK vagy DSC-konfigurációban található paraméterben. Ezek a változók megnehezítik a runbookok-vagy DSC-konfigurációk és az Automation-változók közötti függőségek felderítését a tervezési időszakban.

## <a name="python-2-functions-to-access-certificates"></a>A Python 2 függvények a tanúsítványok eléréséhez

A következő táblázatban található függvény használatával férhet hozzá a Python 2 runbook lévő tanúsítványokhoz.

| Függvény | Leírás |
|:---|:---|
| `automationassets.get_automation_certificate` | Adatokat kér le egy tanúsítvány tárgyáról. |

> [!NOTE]
> Az `automationassets` Asset functions eléréséhez importálnia kell a modult a Python-runbook elején.

## <a name="create-a-new-certificate"></a>Új tanúsítvány létrehozása

Új tanúsítvány létrehozásakor fel kell töltenie egy. cer vagy. pfx fájlt az Automation szolgáltatásba. Ha a tanúsítványt exportálható jelöli meg, akkor átviheti azt az Automation-tanúsítványtárolóból. Ha nem exportálható, akkor csak a runbook vagy DSC-konfiguráción belüli aláíráshoz használható. Az Automation megköveteli, hogy a tanúsítvány rendelkezzen a szolgáltató **Microsoft Enhanced RSA és AES titkosítási szolgáltatóval**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Új tanúsítvány létrehozása a Azure Portal

1. Az Automation-fiókban válassza az **eszközök**  >  **tanúsítványok**tanúsítvány  >  **hozzáadása**elemet.
1. A **név** mezőbe írja be a tanúsítvány nevét.
1. Egy **. cer** vagy **. pfx** fájl tallózásához a **tanúsítványfájl feltöltése**területen válassza **a fájl kiválasztása**lehetőséget. Ha **. pfx** -fájlt választ, adjon meg egy jelszót, és jelezze, hogy exportálható-e.
1. Válassza a **Létrehozás** lehetőséget az új tanúsítvány-eszköz mentéséhez.

### <a name="create-a-new-certificate-with-powershell"></a>Új tanúsítvány létrehozása a PowerShell-lel

Az alábbi példa bemutatja, hogyan hozhat létre egy új Automation-tanúsítványt, és hogyan jelölheti meg exportálható. Ez a példa egy meglévő **. pfx** fájlt importál.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Új tanúsítvány létrehozása Resource Manager-sablonnal

Az alábbi példa bemutatja, hogyan helyezhet üzembe egy tanúsítványt az Automation-fiókjában egy Resource Manager-sablonnal a PowerShell használatával:

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
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>Tanúsítvány beszerzése

Tanúsítvány lekéréséhez használja a belső `Get-AutomationCertificate` parancsmagot. Nem használhatja a [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) parancsmagot, mert információt ad vissza a tanúsítvány adategységéről, de nem maga a tanúsítvány.

### <a name="textual-runbook-example"></a>Szöveges runbook példa

Az alábbi példa bemutatja, hogyan adhat hozzá egy tanúsítványt egy felhőalapú szolgáltatáshoz egy runbook. Ebben a példában a jelszót egy titkosított Automation-változóból kéri le a rendszer.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Példa grafikus runbook

Adjon hozzá egy tevékenységet a belső `Get-AutomationCertificate` parancsmaghoz egy grafikus runbook. ehhez kattintson a jobb gombbal a tanúsítványra a könyvtár ablaktáblán, és válassza a **Hozzáadás a vászonhoz**lehetőséget.

![Képernyőfelvétel a tanúsítvány hozzáadásáról a vászonhoz](../media/certificates/automation-certificate-add-to-canvas.png)

Az alábbi képen egy példa látható a tanúsítvány grafikus runbook való használatára. 

![Képernyőkép egy grafikus szerzői műveletről](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>2. Python-példa

Az alábbi példa bemutatja, hogyan érheti el a tanúsítványokat a Python 2 runbookok.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a tanúsítványok eléréséhez használt parancsmagokról, tekintse meg a [modulok kezelése a Azure Automationban](modules.md)című témakört.
* A runbookok kapcsolatos általános információkért lásd: [a Runbook végrehajtása Azure Automation](../automation-runbook-execution.md).
* A DSC-konfigurációk részletes ismertetését lásd: [Azure Automation állapot konfigurációjának áttekintése](../automation-dsc-overview.md). 

