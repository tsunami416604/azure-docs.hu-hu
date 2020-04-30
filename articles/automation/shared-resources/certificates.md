---
title: Tanúsítvány kezelése Azure Automation
description: A tanúsítványokat a rendszer biztonságosan tárolja Azure Automationban, hogy a runbookok-vagy DSC-konfigurációk hozzáférhessenek az Azure-ban és a harmadik féltől származó erőforrásokhoz való hitelesítéshez. Ez a cikk ismerteti a tanúsítványok részleteit, valamint azt, hogy miként dolgozhat velük a szöveges és a grafikus létrehozásban.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732827"
---
# <a name="manage-certificates-in-azure-automation"></a>Tanúsítványok kezelése a Azure Automationban

A tanúsítványokat a rendszer biztonságosan tárolja Azure Automation, hogy a runbookok vagy DSC-konfigurációk hozzáférhessenek a [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) tevékenységgel Azure Resource Manager erőforrásaihoz. A biztonságos tanúsítványtároló lehetővé teszi, hogy olyan runbookok-és DSC-konfigurációkat hozzon létre, amelyek tanúsítványokat használnak a hitelesítéshez, vagy hozzáadhatják őket az Azure-hoz vagy külső erőforrásokhoz

Azure Automation a biztonságos adategységek közé tartoznak a hitelesítő adatok, a tanúsítványok, a kapcsolatok és a titkosított változók. Ezeket az eszközöket a rendszer titkosítja és tárolja Azure Automation az egyes Automation-fiókokhoz generált egyedi kulcs használatával. Ezt a kulcsot egy rendszer által felügyelt Key Vault tárolja. A biztonságos eszköz tárolása előtt a kulcs betöltődik Key Vault, majd az eszköz titkosítására szolgál. Ezt a folyamatot a Azure Automation felügyeli.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](../automation-update-azure-modules.md).

## <a name="az-powershell-cmdlets"></a>Az PowerShell-parancsmagok

Az az esetében az alábbi táblázatban található parancsmagok használhatók az Automation hitelesítőadat-eszközök létrehozásához és kezeléséhez a Windows PowerShell használatával. Az az [. Automation modul](/powershell/azure/overview)részét képezik, amely az Automation runbookok és a DSC-konfigurációk esetében használható.

|Parancsmag |Leírás|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Feltölt egy szolgáltatási tanúsítványt a megadott felhőalapú szolgáltatáshoz.|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Egy runbook vagy DSC-konfigurációban használandó tanúsítvány információinak beolvasása. A tanúsítványt csak maga kérheti le a `Get-AutomationCertificate` tevékenység használatával.|
|[Új – AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Új tanúsítvány létrehozása Azure Automationban.|
|[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Eltávolít egy tanúsítványt a Azure Automationból.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Beállítja egy meglévő tanúsítvány tulajdonságait, beleértve a tanúsítványfájl feltöltését és a **. pfx** fájl jelszavának beállítását.|

## <a name="activities"></a>Tevékenységek

Az alábbi táblázatban szereplő tevékenységek a tanúsítványok runbook és DSC-konfigurációkhoz való elérésére szolgálnak.

| Tevékenységek | Leírás |
|:---|:---|
|`Get-AutomationCertificate`|Lekéri egy runbook vagy DSC-konfigurációban használandó tanúsítványt. Egy [System. Security. kriptográfiai. X509Certificates. x509certificate2)](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) objektumot ad vissza.|

> [!NOTE] 
> Ne használjon változókat a `Name` RUNBOOK vagy DSC- `Get-AutomationCertificate` konfigurációban található paraméterben. Az ebben a paraméterben szereplő változók használata megnehezíti a runbookok vagy DSC-konfigurációk és az Automation-változók közötti függőségek felderítését a tervezési időszakban.

## <a name="python-2-functions"></a>Python 2 függvények

A következő táblázatban szereplő függvény a tanúsítványoknak a Python 2 runbook való elérésére szolgál.

| Függvény | Leírás |
|:---|:---|
| `automationassets.get_automation_certificate` | Adatokat kér le egy tanúsítvány tárgyáról. |

> [!NOTE]
> Az Asset functions `automationassets` eléréséhez importálnia kell a modult a Python-runbook elején.

## <a name="creating-a-new-certificate"></a>Új tanúsítvány létrehozása

Új tanúsítvány létrehozásakor fel kell töltenie egy. cer vagy. pfx fájlt Azure Automationba. Ha a tanúsítványt exportálható jelöli meg, akkor a Azure Automation tanúsítványtárolóból is átviheti. Ha nem exportálható, akkor csak a runbook vagy DSC-konfiguráción belüli aláíráshoz használható. Azure Automation megköveteli, hogy a tanúsítvány rendelkezzen a szolgáltató **Microsoft Enhanced RSA és AES titkosítási szolgáltatóval**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Új tanúsítvány létrehozása a Azure Portal

1. Az Automation-fiókban kattintson az **eszközök** elemre az eszközök lap megnyitásához.
2. Válassza a **tanúsítványok** lehetőséget a tanúsítványok lap megnyitásához.
3. Kattintson a **tanúsítvány hozzáadása** lehetőségre az oldal tetején.
4. Adja meg a tanúsítvány nevét a **név** mezőben.
5. Egy **. cer** -vagy **. pfx** -fájl tallózásához kattintson **a fájl kiválasztása** elemre a **tanúsítványfájl feltöltése**területen. Ha **. pfx**-fájlt választ, adjon meg egy jelszót, és jelezze, hogy exportálható-e.
6. Kattintson a **Létrehozás** gombra az új tanúsítvány-eszköz mentéséhez.

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
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Tanúsítvány használata

Ha tanúsítványt szeretne használni, használja a `Get-AutomationCertificate` tevékenységet. Nem használhatja a [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) parancsmagot, mivel a a tanúsítvány objektumával kapcsolatos információkat ad vissza, nem maga a tanúsítvány.

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

Adjon hozzá `Get-AutomationCertificate` egy tevékenységet egy grafikus runbook, ehhez kattintson a jobb gombbal a tanúsítványra a könyvtár ablaktáblán, és válassza a **Hozzáadás a vászonhoz**lehetőséget.

![Tanúsítvány hozzáadása a vászonhoz](../media/certificates/automation-certificate-add-to-canvas.png)

Az alábbi képen egy példa látható a tanúsítvány grafikus runbook való használatára. Ez megegyezik az előző példával, amely bemutatja, hogyan adhat hozzá egy tanúsítványt egy felhőalapú szolgáltatáshoz egy szöveges runbook.

![Példa grafikus készítésre](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>2. Python-példa

Az alábbi példa bemutatja, hogyan érheti el a tanúsítványokat a Python2 runbookok.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni a runbook által végzett tevékenységek logikai áramlását vezérlő hivatkozások kezelésével kapcsolatban, tekintse meg a [hivatkozások grafikus szerzői műveletekből](../automation-graphical-authoring-intro.md#links-and-workflow)című témakört. 
