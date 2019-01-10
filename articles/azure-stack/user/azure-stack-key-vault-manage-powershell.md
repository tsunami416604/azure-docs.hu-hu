---
title: A Key Vault az Azure Stackben kezelése a PowerShell használatával |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a Key Vault az Azure Stack PowerShell-lel
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.openlocfilehash: db68d3ac626d80361e456a251b93d847a73afb8c
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192216"
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>Kezelése a Key Vault az Azure Stack PowerShell-lel

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A Key Vault az Azure Stack PowerShell használatával kezelheti. Ez a cikk ismerteti a Key Vault PowerShell-parancsmagok használatával:

* Kulcstartó létrehozása.
* Store és a titkosítási kulcsokat és titkos kódokat.
* Engedélyezze a felhasználónak vagy alkalmazásnak a tárban lévő műveletek meghívását.

>[!NOTE]
>A Key Vault PowerShell-parancsmagok oldhatja meg ez a cikk az Azure PowerShell SDK vannak megadva.

## <a name="prerequisites"></a>Előfeltételek

* Meg kell előfizetés egy ajánlatra, amely magában foglalja az Azure Key Vault szolgáltatást.
* [Az Azure Stack PowerShell telepítése](azure-stack-powershell-install.md).
* [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>A Key Vault-műveletek a bérlő előfizetés engedélyezése

Key vault kapcsolatos bármilyen művelet elvégzése előtt győződjön meg róla, hogy a bérlő előfizetés engedélyezve van-e a vault-műveletek. Győződjön meg arról, hogy engedélyezve vannak-e a vault-műveletek, futtassa a következő parancsot:

```PowerShell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

**Kimenet**

Ha az előfizetés engedélyezve van a vault-műveletek, a kimenet mutatja **RegistrationState** van **regisztrált** a key vault minden erőforrástípus esetén.

![A Key vault regisztrációs állapot](media/azure-stack-key-vault-manage-powershell/image1.png)

Ha vault-műveletek nem engedélyezettek, futtassa az előfizetés regisztrálása a Key Vault szolgáltatás a következő parancsot:

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**Kimenet**

Ha a regisztráció sikeres, a következő eredményt adja vissza:

![Regisztráljon](media/azure-stack-key-vault-manage-powershell/image2.png)

Amikor a Key Vault-parancsokat indít el, akkor előfordulhat, hogy megjelenik egy hibaüzenet, például "az előfizetés nincs regisztrálva a névtér"Microsoft.KeyVault"." Ha hibaüzenetet kap, ellenőrizze, hogy [engedélyezve van a Key Vault erőforrás-szolgáltató](#enable-your-tenant-subscription-for-vault-operations) , amely a korábban kapott utasításokat követve.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Mielőtt egy kulcstartót hoz létre, hozzon létre egy erőforráscsoportot, hogy az összes olyan erőforrást a key vaulttal kapcsolatos egy erőforráscsoportban létezik. A következő paranccsal hozzon létre egy új erőforráscsoportot:

```PowerShell
New-AzureRmResourceGroup -Name "VaultRG" -Location local -verbose -Force
```

**Kimenet**

![Új erőforráscsoport](media/azure-stack-key-vault-manage-powershell/image3.png)

Mostantól használhatja a **New-AzureRMKeyVault** parancsmaggal hozzon létre egy kulcstartót az erőforráscsoportban, amelyet korábban hozott létre. Ez a parancs beolvassa a három kötelező paraméterrel: erőforráscsoport-nevet, a kulcstároló nevét és a földrajzi hely.

Futtassa a key vault létrehozása a következő parancsot:

```PowerShell
New-AzureRmKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```

**Kimenet**

![Új kulcstartó](media/azure-stack-key-vault-manage-powershell/image4.png)

Ez a parancs kimenetét mutatja az Ön által létrehozott kulcstartó tulajdonságait. Ha egy alkalmazás hozzáfér az ebben a tárban, azt kell használnia a `Vault URI` tulajdonság, amely `https://vault01.vault.local.azurestack.external` ebben a példában.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Az Active Directory összevonási szolgáltatások (AD FS) központi telepítés

Az AD FS telepítése az ezt a figyelmeztetést kaphat: "A hozzáférési szabályzat nincs beállítva. Nincs felhasználó vagy alkalmazás nem hozzáférési engedélyt a tár használatára." A probléma megoldásához állítsa be a tároló hozzáférési házirend használatával a [Set-AzureRmKeyVaultAccessPolicy](azure-stack-key-vault-manage-powershell.md#authorize-an-application-to-use-a-key-or-secret) parancsmagot:

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>Kulcsok és titkos kulcsok kezelése

Miután létrehozott egy kulcstartót, használja a következő lépések hozhat létre, és a kulcsokat és titkos kódokat, a tárolóban.

### <a name="create-a-key"></a>Kulcs létrehozása

Használja a **Add-AzureKeyVaultKey** parancsmag segítségével hozzon létre vagy importáljon egy szoftveresen védett kulcsot a kulcstartóban.

```PowerShell
Add-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01" -verbose -Destination Software
```

A `Destination` paraméter adja meg, hogy a kulcs által védett szoftver használható. Ha a kulcs sikeresen létrejött, a parancs jelenít meg az adatait az újonnan létrehozott kulcsot.

**Kimenet**

![Új kulcs](media/azure-stack-key-vault-manage-powershell/image5.png)

Az újonnan létrehozott kulcsot az URI használatával hivatkozhat. Hozzon létre vagy importálhat egy kulcsot, amelynek neve megegyezik egy meglévő kulcsot, ha az eredeti kulcs frissül az új kulcs megadott értékeket. Elérheti az előző verzió verzióspecifikus URI-ját a kulcs használatával. Példa:

* Használat `https://vault10.vault.local.azurestack.external:443/keys/key01` , mindig letöltheti a legfrissebb verziót.
* Használat `https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a` lekérni ezt a verziót.

### <a name="get-a-key"></a>Kulcs lekérése

Használja a **Get-AzureKeyVaultKey** parancsmag egy kulcsot és az adatok olvasásához.

```PowerShell
Get-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01"
```

### <a name="create-a-secret"></a>Titkos kulcs létrehozása

Használja a **Set-AzureKeyVaultSecret** parancsmag létrehozni vagy frissíteni egy tároló titkos kulcs. Titkos kód jön létre, ha egy nem létezik. A titkos kulcs új verziója jön létre, ha már létezik.

```PowerShell
$secretvalue = ConvertTo-SecureString "User@123" -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01" -SecretValue $secretvalue
```

**Kimenet**

![Titkos kulcs létrehozása](media/azure-stack-key-vault-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Titkos kulcs lekérése

Használja a **Get-AzureKeyVaultSecret** parancsmag használatával, olvassa el a kulcstartóban található titkos kulcs. Ezzel a paranccsal lépjen vissza az összes vagy titkos kulcs bizonyos verziójának.

```PowerShell
Get-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01"
```

Miután létrehozta a kulcsok és titkos kulcsok, engedélyezheti a külső alkalmazások általi használatát.

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>Az alkalmazások számára egy kulcs vagy titkos kód használata

Használja a **Set-AzureRmKeyVaultAccessPolicy** parancsmag segítségével engedélyezze az alkalmazás eléréséhez, kulcsok vagy titkos a key vaultban. A következő példában a tároló nevére van `ContosoKeyVault`, és az alkalmazás számára engedélyezni kívánja az ügyfél-Azonosítót `8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed`. Engedélyezze az alkalmazás, futtassa a következő parancsot. Másik lehetőségként megadhatja a `PermissionsToKeys` paraméter segítségével állítsa be az engedélyeket a felhasználó, alkalmazás vagy egy biztonsági csoportot.

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Ha azt szeretné, hogy a tároló titkos kulcsainak olvasásához az alkalmazás engedélyezésére, futtassa a következő parancsmagot:

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>További lépések

* [Virtuális gép üzembe helyezése a Key Vault-jelszóval](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Virtuális gép üzembe helyezése a Key Vault-tanúsítvánnyal](azure-stack-key-vault-push-secret-into-vm.md)
