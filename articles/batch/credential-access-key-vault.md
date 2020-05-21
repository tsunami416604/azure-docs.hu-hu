---
title: A Key Vault biztonságos elérése a Batch használatával
description: Megtudhatja, hogyan férhet hozzá programozott módon a hitelesítő adataihoz Key Vault a Azure Batch használatával.
ms.topic: how-to
ms.date: 02/13/2020
ms.openlocfilehash: 3d0b2128bef1434f073700eb83e5935d74d8bb7a
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725720"
---
# <a name="securely-access-key-vault-with-batch"></a>A Key Vault biztonságos elérése a Batch használatával

Ebből a cikkből megtudhatja, hogyan állíthatja be a Batch-csomópontokat a Azure Key Vault tárolt hitelesítő adatok biztonságos eléréséhez. Nincs értelme a rendszergazdai hitelesítő adatoknak a Key Vault-ben való elhelyezésében, majd a Key Vault parancsfájlokból való eléréséhez szükséges merevlemez-kódolási hitelesítő adatokkal. A megoldás egy olyan tanúsítvány használata, amely engedélyezi a Batch-csomópontok számára a Key Vault elérését. Néhány lépéssel a Batch biztonságos kulcstárolóját is megvalósíthatja.

A Batch-csomópontok Azure Key Vault való hitelesítéséhez a következők szükségesek:

- Egy Azure Active Directory (Azure AD) hitelesítő adat
- Egy tanúsítvány
- Batch-fiók
- Legalább egy csomóponttal rendelkező batch-készlet

## <a name="obtain-a-certificate"></a>Tanúsítvány beszerzése

Ha még nem rendelkezik tanúsítvánnyal, az egyik legegyszerűbb módja, ha létrehoz egy önaláírt tanúsítványt a `makecert` parancssori eszköz használatával.

Ebben `makecert` az elérési úton általában a következőt találja: `C:\Program Files (x86)\Windows Kits\10\bin\<arch>` . Nyisson meg egy parancssort rendszergazdaként, és navigáljon `makecert` a következő példa használatára.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Ezután az eszköz használatával `makecert` hozzon létre önaláírt tanúsítványfájl nevű `batchcertificate.cer` és `batchcertificate.pvk` . A használt köznapi név (CN) nem fontos ehhez az alkalmazáshoz, de hasznos lehet, ha azt szeretné, hogy a tanúsítvány mire szolgál.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

A Batch `.pfx` fájlra van szükség. A [pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) eszközzel konvertálhatja a `.cer` és a `.pvk` által létrehozott fájlokat `makecert` egyetlen `.pfx` fájlba.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

A Key Vaulthoz való hozzáférést egy **felhasználó** vagy egy **egyszerű szolgáltatásnév**kapja meg. Ha programozott módon szeretné elérni Key Vault, használjon egy egyszerű szolgáltatást az előző lépésben létrehozott tanúsítvánnyal.

Az Azure-szolgáltatásokkal kapcsolatos további információkért lásd: [alkalmazás-és szolgáltatásnév-objektumok Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md).

> [!NOTE]
> Az egyszerű szolgáltatásnak ugyanabban az Azure AD-bérlőben kell lennie, mint a Key Vault.

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

Az alkalmazás URL-címei nem fontosak, mivel csak Key Vault-hozzáféréshez használjuk őket.

## <a name="grant-rights-to-key-vault"></a>Jogosultságok megadása Key Vault

Az előző lépésben létrehozott egyszerű szolgáltatásnév jogosult a titkok beolvasására Key Vaultból. Az engedélyek a Azure Portalon vagy az alábbi PowerShell-parancson keresztül adhatók meg.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Tanúsítvány kiosztása batch-fiókhoz

Hozzon létre egy batch-készletet, majd nyissa meg a készlet tanúsítvány lapját, és rendelje hozzá a létrehozott tanúsítványt. A tanúsítvány most már az összes batch-csomóponton található.

Ezután a tanúsítványt a Batch-fiókhoz kell rendelni. A tanúsítványnak a fiókhoz való hozzárendelésével lehetővé válik, hogy hozzárendelje a készletekhez, majd a csomópontokhoz. Ennek a legegyszerűbb módja, ha a Batch-fiókját megnyitja a portálon, navigáljon a **tanúsítványokhoz**, és válassza a **Hozzáadás**lehetőséget. Töltse fel a `.pfx` [Tanúsítvány beszerzése](#obtain-a-certificate) és a jelszó megadásával létrehozott fájlt. Ha elkészült, a rendszer hozzáadja a tanúsítványt a listához, és ellenőrizheti az ujjlenyomatot.

Most, amikor létrehoz egy batch-készletet, megteheti a készletben lévő **tanúsítványokat** , és hozzárendelheti a készlethez létrehozott tanúsítványt. Ha így tesz, győződjön meg róla, hogy az áruház helyének **LocalMachine** választja. A tanúsítvány a készlet összes köteg csomópontjára betöltődik.

## <a name="install-azure-powershell"></a>Az Azure PowerShell telepítése

Ha a csomópontokon PowerShell-parancsfájlok használatával tervezi a Key Vault elérését, akkor telepítenie kell a Azure PowerShell könyvtárat. Ezt többféleképpen is megteheti, ha a csomópontjain telepítve van a Windows Management Framework (WMF) 5, az install-Module paranccsal pedig letöltheti azt. Ha olyan csomópontokat használ, amelyek nem rendelkeznek a WMF 5-öt, akkor a telepítés legegyszerűbb módja, ha a Azure PowerShell `.msi` fájlt a Batch-fájlokkal együtt szeretné felépíteni, majd hívja meg a telepítőt a Batch indítási parancsfájl első részeként. A részletekért tekintse meg a következő példát:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>A Key Vault elérése

Most már minden beállítással elérheti Key Vault a Batch-csomópontokon futó parancsfájlokban. Ha egy parancsfájlból szeretne Key Vault hozzáférni, mindössze annyit kell tennie, hogy a parancsfájlt a tanúsítvány használatával hitelesítse az Azure AD-ben. Ehhez a PowerShellben a következő példában szereplő parancsokat használhatja. Adja meg az **ujjlenyomathoz**tartozó megfelelő GUID azonosítót, az **alkalmazás azonosítóját** (az egyszerű szolgáltatásnév azonosítóját) és a **bérlő azonosítóját** (az a bérlő, ahol a szolgáltatás létezik).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

A hitelesítés után a kulcstartót a megszokott módon érheti el.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Ezek a parancsfájlban használandó hitelesítő adatok.
