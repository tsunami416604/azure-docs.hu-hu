---
title: Tanúsítvány kezelése az Azure Automationben
description: A tanúsítványok biztonságosan tárolódnak az Azure Automationben, így a runbookok vagy a DSC-konfigurációk hozzáférhetnek az Azure és a külső erőforrások hitelesítéséhez. Ez a cikk ismerteti a tanúsítványok részleteit, valamint azt, hogy hogyan dolgozhat velük szöveges és grafikus nyelven.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732827"
---
# <a name="manage-certificates-in-azure-automation"></a>Tanúsítványok kezelése az Azure Automationben

A tanúsítványok biztonságosan tárolódnak az Azure Automationben, így runbookok vagy DSC-konfigurációk érhetik el őket az Azure Resource Manager-erőforrások [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) tevékenységével. A biztonságos tanúsítványtárolás lehetővé teszi olyan runbookok és DSC-konfigurációk létrehozását, amelyek tanúsítványokat használnak a hitelesítéshez, vagy hozzáadhatják őket az Azure-hoz vagy a külső erőforrásokhoz.

Az Azure Automation biztonságos eszközei közé tartoznak a hitelesítő adatok, tanúsítványok, kapcsolatok és titkosított változók. Ezek az eszközök titkosítva vannak, és az Azure Automationben egy egyedi kulcs használatával kerülnek tárolásra, amely minden automatizálási fiókhoz létrejön. Ez a kulcs egy rendszer által felügyelt key vault tárolja. A biztonságos eszköz tárolása előtt a kulcs betöltődik a Key Vaultból, és ezután az eszköz titkosításához használható. Ezt a folyamatot az Azure Automation kezeli.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](../automation-update-azure-modules.md)

## <a name="az-powershell-cmdlets"></a>Az PowerShell-parancsmagok

Az Az esetében az alábbi táblázatban található parancsmagok a Windows PowerShell automation credential-eszközeinek létrehozásához és kezeléséhez használatosak. Az [Az.Automation modul](/powershell/azure/overview)részeként szállítanak, amely az Automation runbookok és a DSC-konfigurációk ban használható.

|Parancsmag |Leírás|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Feltölti a szolgáltatástanúsítványt a megadott felhőszolgáltatáshoz.|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Lekéri a runbook- vagy DSC-konfigurációban használandó tanúsítvány adatait. A `Get-AutomationCertificate` tevékenység használatával csak magát a tanúsítványt lehet beolvasni.|
|[Új-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Új tanúsítványt hoz létre az Azure Automationben.|
|[Eltávolítás-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Eltávolítja a tanúsítványt az Azure Automationből.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Beállítja egy meglévő tanúsítvány tulajdonságait, beleértve a tanúsítványfájl feltöltését és a **.pfx** fájl jelszavának beállítását.|

## <a name="activities"></a>Tevékenységek

Az alábbi táblázatban szereplő tevékenységek a runbook- és DSC-konfigurációk tanúsítványainak elérésére szolgálnak.

| Tevékenységek | Leírás |
|:---|:---|
|`Get-AutomationCertificate`|Beszerzi a runbook vagy dsc konfigurációban használandó tanúsítványt. [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) objektumot ad vissza.|

> [!NOTE] 
> Ne használjon változókat `Name` a `Get-AutomationCertificate` runbook vagy dsc konfigurációban lévő paraméterben. A változók ebben a paraméterben való használata megnehezíti a runbookok vagy dsc-konfigurációk és az automation-változók közötti függőségek felderítését a tervezés időpontjában.

## <a name="python-2-functions"></a>Python 2 függvények

Az alábbi táblázatban található függvény a Python 2 runbookok tanúsítványainak elérésére szolgál.

| Függvény | Leírás |
|:---|:---|
| `automationassets.get_automation_certificate` | Egy tanúsítványeszköz adatait olvassa be. |

> [!NOTE]
> Az eszközfüggvények eléréséhez importálnia kell a `automationassets` modult a Python runbook elején.

## <a name="creating-a-new-certificate"></a>Új tanúsítvány létrehozása

Amikor új tanúsítványt hoz létre, egy .cer vagy .pfx fájlt tölt fel az Azure Automationbe. Ha a tanúsítványt exportálhatóként jelöli meg, akkor átviheti az Azure Automation tanúsítványtárolóból. Ha nem exportálható, akkor csak a runbook vagy a DSC konfiguráción belüli aláíráshoz használható. Az Azure Automation megköveteli, hogy a tanúsítvány a **szolgáltató Microsoft Enhanced RSA és AES kriptográfiai szolgáltató.**

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Új tanúsítvány létrehozása az Azure Portalon

1. Az Automation-fiókban kattintson az **Eszközök** elemre az Eszközök lap megnyitásához.
2. A Tanúsítványok lap megnyitásához válassza a **Tanúsítványok** lehetőséget.
3. Kattintson a lap tetején a **Tanúsítvány hozzáadása** elemre.
4. Írja be a tanúsítvány nevét a **Név** mezőbe.
5. .cer vagy **.pfx** fájl tallózásához kattintson **a Fájl kijelölése** a **Tanúsítványfájl feltöltése**csoportban. **.cer** Ha **.pfx**fájlt választ, adjon meg egy jelszót, és jelezze, hogy exportálható-e.
6. Az új tanúsítványeszköz mentéséhez kattintson a **Létrehozás** gombra.

### <a name="create-a-new-certificate-with-powershell"></a>Új tanúsítvány létrehozása a PowerShell használatával

A következő példa bemutatja, hogyan hozhat létre egy új Automation-tanúsítványt, és jelölje meg exportálható. Ez a példa egy meglévő **.pfx** fájlt importál.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Új tanúsítvány létrehozása Erőforrás-kezelő sablonnal

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
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Tanúsítvány használata

Tanúsítvány használatához használja a `Get-AutomationCertificate` tevékenységet. A [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) parancsmag nem használható, mivel a tanúsítványeszközre vonatkozó információkat ad vissza, de magát a tanúsítványt nem.

### <a name="textual-runbook-example"></a>Példa szöveges runbookra

A következő példa bemutatja, hogyan adhat hozzá egy tanúsítványt egy felhőszolgáltatáshoz egy runbookban. Ebben a példában a jelszó egy titkosított automatizálási változóból származik.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Példa grafikus runbookra

`Get-AutomationCertificate` Tevékenység hozzáadása grafikus runbookhoz úgy, hogy a jobb gombbal a könyvtár ablaktáblájában a tanúsítványra kattint, és a **Hozzáadás a vászonhoz parancsot**választja.

![Tanúsítvány hozzáadása a vászonhoz](../media/certificates/automation-certificate-add-to-canvas.png)

Az alábbi képen egy példa egy tanúsítvány grafikus runbook. Ez megegyezik az előző példával, amely bemutatja, hogyan adhat hozzá egy tanúsítványt egy felhőalapú szolgáltatáshoz egy szöveges runbookból.

![Példa grafikus szerzői](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Python 2 példa

A következő példa bemutatja, hogyan érheti el a tanúsítványokat a Python2 runbookok.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>További lépések

- Ha többet szeretne tudni a runbook által végzett tevékenységek logikai folyamatának szabályozására mutató hivatkozásokról, olvassa el [a Hivatkozások grafikus szerzői című témakört.](../automation-graphical-authoring-intro.md#links-and-workflow) 
