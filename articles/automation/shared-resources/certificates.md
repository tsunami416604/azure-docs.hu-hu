---
title: Az Azure Automation tanúsítvány adategységei
description: A tanúsítványok biztonságosan vannak az Azure Automationben, így runbookok vagy DSC-konfigurációk segítségével is elérhetők az Azure és a külső erőforrások hitelesítéséhez.  Ez a cikk ismerteti a tanúsítványok részleteit, valamint azt, hogy hogyan dolgozhat velük szöveges és grafikus nyelven.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a66f73e028594cf90f1fa1765910a3df3adbad1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849479"
---
# <a name="certificate-assets-in-azure-automation"></a>Az Azure Automation tanúsítvány adategységei

A tanúsítványok biztonságosan tárolódnak az Azure Automationben, így runbookok vagy DSC-konfigurációk érhetik el őket az Azure Resource Manager-erőforrások **Get-AzureRmAutomationCertificate** tevékenységével. Ez a funkció lehetővé teszi, hogy runbookok és DSC-konfigurációk, amelyek tanúsítványokat használnak a hitelesítéshez, vagy hozzáadja őket az Azure vagy a külső erőforrásokhoz.

>[!NOTE]
>Az Azure Automation biztonságos eszközei közé tartoznak a hitelesítő adatok, tanúsítványok, kapcsolatok és titkosított változók. Ezek az eszközök titkosítva vannak, és az Azure Automationben egy egyedi kulcs használatával kerülnek tárolásra, amely minden automatizálási fiókhoz létrejön. Ez a kulcs egy rendszer által felügyelt Key Vault tárolja. A biztonságos eszköz tárolása előtt a kulcs betöltődik a Key Vaultból, és ezután az eszköz titkosításához használható. Ezt a folyamatot az Azure Automation kezeli.

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-parancsmagok

Az AzureRM esetében az alábbi táblázatban található parancsmagok a Windows PowerShell automation hitelesítő adatok eszközeinek létrehozásához és kezeléséhez használatosak. Az [AzureRM.Automation modul](/powershell/azure/overview)részeként szállítanak, amely az Automation runbookok és a DSC-konfigurációk ban használható.

|Parancsmagok|Leírás|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Lekéri a runbook- vagy DSC-konfigurációban használandó tanúsítvány adatait. Magát a tanúsítványt csak a Get-AutomationCertificate tevékenységből tudja beolvasni.|
|[Új-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Új tanúsítványt hoz létre az Azure Automationben.|
[Eltávolítás-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Eltávolítja a tanúsítványt az Azure Automationből.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Beállítja egy meglévő tanúsítvány tulajdonságait, beleértve a tanúsítványfájl feltöltését és a .pfx jelszavának beállítását.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Feltölti a szolgáltatástanúsítványt a megadott felhőszolgáltatáshoz.|

## <a name="activities"></a>Tevékenységek

Az alábbi táblázatban szereplő tevékenységek a runbook- és DSC-konfigurációk tanúsítványainak elérésére szolgálnak.

| Tevékenységek | Leírás |
|:---|:---|
|Get-AutomationCertificate|Beszerzi a runbook vagy dsc konfigurációban használandó tanúsítványt. [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) objektumot ad vissza.|

> [!NOTE] 
> Ne használjon változókat a **Get-AutomationCertificate** –Name paraméterében egy runbook- vagy DSC-konfigurációban, mivel megnehezíti a runbookok vagy a DSC-konfiguráció kontrói és az Automation-változók közötti függőségek felderítését a tervezés időpontjában.

## <a name="python2-functions"></a>Python2 függvények

Az alábbi táblázatban található függvény a Python2 runbookok tanúsítványainak elérésére szolgál.

| Függvény | Leírás |
|:---|:---|
| automationassets.get_automation_certificate | Egy tanúsítványeszköz adatait olvassa be. |

> [!NOTE]
> Az eszközfüggvények eléréséhez importálnia kell az **automationassets** modult a Python runbook elején.

## <a name="creating-a-new-certificate"></a>Új tanúsítvány létrehozása

Amikor új tanúsítványt hoz létre, egy .cer vagy .pfx fájlt tölt fel az Azure Automationbe. Ha a tanúsítványt exportálhatóként jelöli meg, akkor átviheti az Azure Automation tanúsítványtárolóból. Ha nem exportálható, akkor csak a runbook vagy a DSC konfiguráción belüli aláíráshoz használható. Az Azure Automation megköveteli, hogy a tanúsítvány szolgáltatóval rendelkezik: **Microsoft Enhanced RSA és AES kriptográfiai szolgáltató.**

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Új tanúsítvány létrehozása az Azure Portalon

1. Az Automation-fiókban kattintson az **Eszközök** csempére az **Eszközök** lap megnyitásához.
2. Kattintson a **Tanúsítványok** csempére a **Tanúsítványok** lap megnyitásához.
3. Kattintson a lap tetején a **Tanúsítvány hozzáadása** elemre.
4. Írja be a tanúsítvány nevét a **Név** mezőbe.
5. .cer vagy .pfx fájl tallózásához kattintson **a Fájl kijelölése** a **Tanúsítványfájl feltöltése**csoportban. Ha .pfx fájlt választ, adja meg a jelszót, és hogy exportálható-e.
6. Az új tanúsítványeszköz mentéséhez kattintson a **Létrehozás** gombra.

### <a name="to-create-a-new-certificate-with-powershell"></a>Új tanúsítvány létrehozása a PowerShell használatával

A következő példa bemutatja, hogyan hozhat létre egy új Automation-tanúsítványt, és jelölje meg exportálható. Ez egy meglévő .pfx fájlt importál.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Új tanúsítvány létrehozása az Erőforrás-kezelő sablonnal

A következő példa bemutatja, hogyan helyezhet üzembe egy tanúsítványt az Automation-fiókba egy Erőforrás-kezelő sablon használatával a PowerShellen keresztül:

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

Tanúsítvány használatához használja a **Get-AutomationCertificate** tevékenységet. A [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) parancsmag nem használható, mivel a tanúsítványeszközre vonatkozó információkat ad vissza, de magát a tanúsítványt nem.

### <a name="textual-runbook-sample"></a>Szöveges runbook minta

A következő mintakód bemutatja, hogyan adhat hozzá egy tanúsítványt egy runbook felhőszolgáltatásához. Ebben a példában a jelszó egy titkosított automatizálási változóból származik.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Grafikus runbook minta

**Get-AutomationCertificate-et** ad hozzá egy grafikus runbookhoz úgy, hogy a jobb gombbal a könyvtár ablaktáblában a tanúsítványra kattint, és a **Hozzáadás a vászonhoz parancsot**választja.

![Tanúsítvány hozzáadása a vászonhoz](../media/certificates/automation-certificate-add-to-canvas.png)

Az alábbi képen egy példa egy tanúsítvány grafikus runbook. Ez megegyezik az előző példával, amely bemutatja, hogyan adhat hozzá egy tanúsítványt egy felhőalapú szolgáltatáshoz egy szöveges runbookból.

![Példa grafikus szerzői](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2-minta

Az alábbi minta bemutatja, hogyan érheti el a tanúsítványokat a Python2 runbookok.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>További lépések

- Ha többet szeretne tudni arról, hogy miként végezheti a runbook által végrehajtandó tevékenységek logikai folyamatát szabályozó hivatkozásokat, olvassa el [a Hivatkozások grafikus szerzői című témakört.](../automation-graphical-authoring-intro.md#links-and-workflow) 
