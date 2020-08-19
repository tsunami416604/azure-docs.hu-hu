---
title: Tanúsítvány exportálása Azure Key Vault
description: Tanúsítvány exportálása Azure Key Vault
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: afab65b22d9487f30da458346bf143a557bec0d8
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588892"
---
# <a name="export-certificate-from-azure-key-vault"></a>Tanúsítvány exportálása Azure Key Vault

A Azure Key Vault lehetővé teszi a hálózat digitális tanúsítványainak üzembe helyezését, kezelését és telepítését, valamint az alkalmazások biztonságos kommunikációjának engedélyezését. A tanúsítványokkal kapcsolatos további általános információkért lásd: [Azure Key Vault tanúsítványok](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)

## <a name="about-azure-key-vault-certificate"></a>Tudnivalók az Azure Key Vault-tanúsítványról

### <a name="composition-of-certificate"></a>A tanúsítvány összetétele
Key Vault-tanúsítvány létrehozásakor a rendszer megcímezhető kulcsot és titkos kulcsot is létrehoz ugyanazzal a névvel. A Key Vault kulcs lehetővé teszi a kulcsfontosságú műveleteket, és a Key Vault titkos kulcs lehetővé teszi a tanúsítvány értékének beolvasását titkos fájlként. A Key Vault tanúsítvány A nyilvános x509 tanúsítvány-metaadatokat is tartalmaz. [További információ](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate)

### <a name="exportable-or-non-exportable-keys"></a>Exportálható vagy nem exportálható kulcsok
Key Vault-tanúsítvány létrehozásakor a megcímezhető titkos kulcsból a PFX vagy PEM formátumban kérhető le. A tanúsítvány létrehozásához használt házirendnek jeleznie kell, hogy a kulcs exportálható. Ha a házirend nem exportálható állapotot jelez, akkor a titkos kulcs nem része az értéknek, amikor a rendszer titokként kéri le.

Két típusú kulcs támogatott: RSA vagy RSA HSM tanúsítványokkal. Az exportálhatóként csak az RSA-mel engedélyezett, az RSA HSM nem támogatja. [További információ](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#exportable-or-non-exportable-key)

A Azure Key Vault tárolt tanúsítványa az Azure CLI, a PowerShell vagy a Portal használatával exportálható.

> [!NOTE]
> Fontos megjegyezni, hogy a Key vaultba való importáláskor csak a tanúsítvány jelszava szükséges. Key Vault nem fogja menteni a társított jelszót, ezért ha exportálni szeretné a tanúsítványt, a jelszó üres lenne.

## <a name="exporting-certificate-using-cli"></a>Tanúsítvány exportálása a parancssori felület használatával
A következő parancs lehetővé teszi egy Key Vault-tanúsítvány **nyilvános részének** letöltését.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```
Példák és paraméterek meghatározása esetén [itt tekintheti meg](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-download) a következőt:



Ha le szeretné tölteni a teljes tanúsítványt, azaz az **összeállítás nyilvános és magánjellegű részét**is, akkor a tanúsítvány titkos fájlként való letöltésével végezhető el.

```azurecli
az keyvault secret download –file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```
A paraméterek meghatározásait [itt tekintheti meg](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-download)


## <a name="exporting-certificate-using-powershell"></a>Tanúsítvány exportálása a PowerShell használatával

Ez a parancs lekéri a TestCert01 nevű tanúsítványt a ContosoKV01 nevű kulcstartóból. A tanúsítvány PFX-fájlként való letöltéséhez futtassa a következő parancsot. Ezek a parancsok hozzáférnek a SecretId, majd a tartalmat pfx-fájlként mentik.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$kvSecret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $Cert.Name
$kvSecretBytes = [System.Convert]::FromBase64String($kvSecret.SecretValueText)
$certCollection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection
$certCollection.Import($kvSecretBytes,$null,[System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable)
$password = '******'
$protectedCertificateBytes = $certCollection.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $password)
$pfxPath = [Environment]::GetFolderPath("Desktop") + "\MyCert.pfx"
[System.IO.File]::WriteAllBytes($pfxPath, $protectedCertificateBytes)
```
Ezzel exportálja a tanúsítványok teljes láncát a titkos kulccsal, és ez a tanúsítvány jelszóval védett lesz.
A ```Get-AzKeyVaultCertificate``` parancsokkal és paraméterekkel kapcsolatos további információkért tekintse meg a [2. példát](https://docs.microsoft.com/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0) .

## <a name="exporting-certificate-using-portal"></a>Tanúsítvány exportálása a portál használatával

A portálon, amikor tanúsítványt hoz létre/importál a tanúsítvány paneljén, megkapja az értesítést, hogy a tanúsítvány sikeresen létrejött. A tanúsítvány kiválasztásakor a jelenlegi verzióra kattinthat, és a letöltés lehetőséget fogja látni.


A "Letöltés CER formátumban" vagy a "Letöltés PFX/PEM formátumban" gombra kattintva letöltheti a tanúsítványt.


![Tanúsítvány letöltése](../media/certificates/quick-create-portal/current-version-shown.png)


## <a name="exporting-app-service-certificate-from-key-vault"></a>App Service-tanúsítvány exportálása a Key vaultból

Azure App Service-tanúsítványok lehetővé teszik az SSL-tanúsítványok megvásárlását és az Azure-alkalmazásokhoz való hozzárendelését közvetlenül a portálon belül. Ezeket a tanúsítványokat a portálról is exportálhatja, mint a máshová felhasználható PFX-fájlokat.
Importálás után az App Service-tanúsítványok a **titkok területen találhatók**.

Az App Service-tanúsítványok exportálásának lépései [itt olvashatók](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx) .

## <a name="read-more"></a>További információk
* [Különböző tanúsítványfájl-típusok és-definíciók](https://docs.microsoft.com/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)