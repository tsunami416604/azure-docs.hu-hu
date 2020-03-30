---
title: Biztonságos hozzáférés a Key Vaulthoz a Batch segítségével – Azure Batch
description: Ismerje meg, hogyan érheti el a hitelesítő adatokat a Key Vaultból az Azure Batch használatával.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.topic: article
ms.date: 02/13/2020
ms.author: lahugh
ms.openlocfilehash: 0134e7d92ddca9bd3b45abaf642f33de9d209b33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192302"
---
# <a name="securely-access-key-vault-with-batch"></a>A Key Vault biztonságos elérése a Batch használatával

Ebben a cikkben megtudhatja, hogyan állíthatja be a Batch-csomópontokat az Azure Key Vaultban tárolt hitelesítő adatok biztonságos eléréséhez. Nincs értelme a rendszergazdai hitelesítő adatok at Key Vault, majd a hitelesítő adatok kódolása a Key Vault egy parancsfájlból való eléréséhez. A megoldás egy tanúsítvány használata, amely hozzáférést biztosít a Batch-csomópontok a Key Vaulthoz. Néhány lépéssel biztonságos kulcstárolást valósíthatunk meg a Batch számára.

Az Azure Key Vault batch-csomópontról történő hitelesítéséhez a következőkre van szükség:

- Az Azure Active Directory (Azure AD) hitelesítő adatai
- Tanúsítvány
- Kötegszámla
- Batch készlet legalább egy csomón

## <a name="obtain-a-certificate"></a>Tanúsítvány beszerzése

Ha még nem rendelkezik tanúsítvánnyal, a legegyszerűbb módja annak, hogy önaláírt `makecert` tanúsítványt hozzon létre a parancssori eszközzel.

Az elérési út `makecert` általában megtalálható: `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`. Nyisson meg egy parancssort `makecert` rendszergazdaként, és keresse meg a következő példát.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Ezután az `makecert` eszközzel hozzon létre `batchcertificate.cer` önaláírt tanúsítványfájlokat nevű és `batchcertificate.pvk`. A használt közös név (CN) nem fontos az alkalmazás, de hasznos, hogy ez valami, ami megmondja, hogy mi a tanúsítvány használt.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

A batch `.pfx` fájlra van szüksége. Használja a [pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) eszközt, `.pvk` hogy `makecert` konvertálja `.pfx` a és a `.cer` létrehozott fájlokat egyetlen fájlba.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

A Key Vaulthoz való hozzáférést egy **felhasználó** vagy egy **egyszerű szolgáltatás**kapja. A Key Vault programozott eléréséhez használjon egyszerű szolgáltatást az előző lépésben létrehozott tanúsítvánnyal.

Az Azure egyszerű szolgáltatásainak használatával kapcsolatos további információkért [lásd: Alkalmazás- és egyszerű szolgáltatásobjektumok az Azure Active Directoryban.](../active-directory/develop/app-objects-and-service-principals.md)

> [!NOTE]
> A szolgáltatásnévnek ugyanabban az Azure AD-bérlőben kell lennie, mint a Key Vault.

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

Az alkalmazás URL-címei nem fontosak, mivel csak key vault-hozzáféréshez használjuk őket.

## <a name="grant-rights-to-key-vault"></a>Jogok megadása a Key Vault számára

Az előző lépésben létrehozott egyszerű szolgáltatásnak engedélyt kell kérnie a titkos kulcsok lekéréséhez a Key Vaultból. Az engedély az Azure Portalon keresztül vagy az alábbi PowerShell-paranccsal adható meg.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Tanúsítvány hozzárendelése Batch-fiókhoz

Hozzon létre egy kötegkészletet, majd lépjen a készlet tanúsítványlapjára, és rendelje hozzá a létrehozott tanúsítványt. A tanúsítvány most már az összes Batch csomóponton található.

Ezután hozzá kell rendelnünk a tanúsítványt a Batch-fiókhoz. A tanúsítvány hozzárendelése a fiókhoz lehetővé teszi számunkra, hogy hozzárendeljük a készletekhez, majd a csomópontokhoz. Ennek legegyszerűbb módja, ha megnyitja a Batch-fiókját a portálon, megnyithatja a **Tanúsítványok**lapot, és a Hozzáadás lehetőséget **választja.** Töltse `.pfx` fel a fájlt generált a [tanúsítvány beszerzése](#obtain-a-certificate) és adja meg a jelszót. A teljes befejezés után a tanúsítvány hozzáadódik a listához, és ellenőrizheti az ujjlenyomatot.

Most, amikor létrehoz egy batch készletet, megnyithatja a **készleten** belüli tanúsítványokat, és hozzárendelheti a létrehozott tanúsítványt a készlethez. Ha így tesz, győződjön meg róla, hogy válassza ki a **LocalMachine** az üzlet helyét. A tanúsítvány a készlet összes Batch-csomópontjára be van töltve.

## <a name="install-azure-powershell"></a>Az Azure PowerShell telepítése

Ha azt tervezi, hogy a PowerShell-parancsfájlok használatával a Key Vault elérése, majd telepítenie kell az Azure PowerShell-függvénytár telepítve van. Ennek több módja is van, ha a csomópontokon telepítve van a Windows Management Framework (WMF) 5, akkor a telepítés-modul paranccsal letöltheti azt. Ha wmf 5-tel nem rendelkező csomópontokat használ, a legegyszerűbb en telepítse, ha összecsomagolja az Azure PowerShell-fájlt `.msi` a Batch-fájlokkal, majd hívja meg a telepítőt a Batch indítási parancsfájl első részeként. Lásd ezt a példát a részletekért:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>A Key Vault elérése

Most már minden beállítás eléréséhez Key Vault parancsfájlok futó Batch csomópontokon. A Key Vault elérése egy parancsfájlból, mindössze annyit kell a parancsfájl hitelesítéséhez az Azure AD a tanúsítvány használatával. Ehhez a PowerShellben használja a következő példaparancsokat. Adja meg a megfelelő GUID **ujjlenyomat,** **App ID** (az azonosító a szolgáltatásnév) és **a bérlői azonosító** (a bérlő, ahol a szolgáltatásnév létezik).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

A hitelesítés után a szokásos módon érheti el a KeyVault-ot.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Ezek azok a hitelesítő adatok, amelyeket a parancsfájlban használni kell.
