---
title: "A verem Azure Key Vault kezelése a PowerShell használatával |} Microsoft Docs"
description: "A verem Azure Key Vault kezelése a PowerShell használatával"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: e920ee20268f5f43592e5a27fe82dcf27cb85af1
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2017
---
# <a name="manage-key-vault-in-azure-stack-by-using-powershell"></a>A verem Azure Key Vault kezelése a PowerShell használatával

Ez a cikk segítséget nyújt a kezdéshez létrehozásához és kezeléséhez a verem Azure Key Vault PowerShell használatával. A jelen cikkben ismertetett Key Vault PowerShell-parancsmagok érhetők el, az Azure PowerShell SDK részeként. A következő szakaszok ismertetik a PowerShell-parancsmagokat, amelyek ahhoz szükségesek:
   - Hozzon létre egy tárolót. 
   - Tárolásához és titkosítási kulcsokat és titkos kódokat. 
   - Engedélyezi a felhasználók vagy alkalmazások a tárolóban lévő műveleteket hívhatnak meg. 

## <a name="prerequisites"></a>Előfeltételek
* Az ajánlat, amely tartalmazza az Azure Key Vault szolgáltatás elő kell fizetnie.
* [Telepítse a PowerShell Azure verem](azure-stack-powershell-install.md).  
* [Konfigurálja az Azure-verem felhasználói PowerShell környezetet](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>A bérlő előfizetés a Key Vault műveletek engedélyezése

Mielőtt bármely kulcstároló műveleteket adhatnak ki, győződjön meg arról, hogy a bérlő előfizetés engedélyezve van-e a tároló műveletek kell. Győződjön meg arról, hogy engedélyezve van-e a tároló művelet, futtassa a következő parancsot:

```PowerShell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```
**Kimeneti**

Ha az előfizetés engedélyezve van a tárolóban műveletek, az az alábbiakat mutatja be, "RegistrationState" egyenlő "Ajánlott" a kulcstároló minden erőforrástípus esetén.

![Regisztráció állapota](media/azure-stack-kv-manage-powershell/image1.png)

Tároló műveletek nem engedélyezettek, ha hívása az előfizetés regisztrálása a Key Vault szolgáltatás a következő parancsot:

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**Kimeneti**

Ha a regisztráció sikeres, akkor a következő eredményt adott vissza:

![Regisztrálni](media/azure-stack-kv-manage-powershell/image2.png) a kulcstartót parancsokat hívhatnak meg, amikor előfordulhat, hogy hibaüzenet, például "az előfizetés nincs regisztrálva"Microsoft.KeyVault"névtér használatára." Ha hibaüzenetet kap, győződjön meg arról, hogy [engedélyezve van a Key Vault erőforrás-szolgáltató](#enable-your-tenant-subscription-for-vault-operations) korábban szereplő utasítások alapján.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása 

Kulcstároló létrehozása előtt hozzon létre egy erőforráscsoportot, hogy az összes kapcsolódó a key vault erőforrás szerepel egy erőforráscsoportot. Az alábbi parancs segítségével hozzon létre egy új erőforráscsoportot:

```PowerShell
New-AzureRmResourceGroup -Name “VaultRG” -Location local -verbose -Force

```

**Kimeneti**

![Új erőforráscsoport](media/azure-stack-kv-manage-powershell/image3.png)

Most, használja a **New-AzureRMKeyVault** parancs futtatásával hozzon létre egy kulcstartót a korábban létrehozott erőforráscsoportot. Ez a parancs beolvassa három kötelező paraméterrel: erőforráscsoport-név, a kulcstároló neve és a földrajzi helyet. 

A következő paranccsal hozzon létre egy kulcstartót:

```PowerShell
New-AzureRmKeyVault -VaultName “Vault01” -ResourceGroupName “VaultRG” -Location local -verbose
```
**Kimeneti**

![Új kulcstartó](media/azure-stack-kv-manage-powershell/image4.png)

Ez a parancs kimenetét mutatja a létrehozott kulcstartó tulajdonságait. Ha egy alkalmazás hozzáfér ehhez a tárolóhoz, akkor használja a **tároló URI** a kimenetben megjelenő tulajdonság. Például a tároló egységes erőforrás-azonosító (URI) ebben az esetben az "https://vault01.vault.local.azurestack.external". Ezen a kulcstartón REST API-n keresztül kommunikáló alkalmazásokat ezt az URI kell használnia.

Az Active Directory összevonási szolgáltatásokban (AD FS)-alapú telepítések esetén a kulcs létrehozásakor tároló PowerShell használatával, akkor előfordulhat, hogy a figyelmeztetést, amely szerint "a házirend nincs beállítva. Egyetlen felhasználó vagy alkalmazás jogosult hozzáférés használata ehhez a tárolóhoz." A probléma megoldásához állítsa be a tároló hozzáférési házirend használatával a [Set-AzureRmKeyVaultAccessPolicy](azure-stack-kv-manage-powershell.md#authorize-an-application-to-use-a-key-or-secret) parancs:

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value 

#Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation 
```

## <a name="manage-keys-and-secrets"></a>Kulcsok és titkos kulcsok kezelése

Tároló létrehozása után a következő lépésekkel hozhatja létre és kezelheti a kulcsok és titkos belül a tárolóban.

### <a name="create-a-key"></a>Kulcs létrehozása

Használja a **Add-AzureKeyVaultKey** parancs futtatásával hozzon létre vagy importáljon egy szoftveres védelemmel ellátott kulcs kulcstároló. 

```PowerShell
Add-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01” -verbose -Destination Software
```
A **cél** paraméterrel adja meg, hogy a kulcs védett szoftver. Ha a kulcs sikeresen létrejött, a parancs kimenetében a létrehozott kulcs adatait.

**Kimeneti**

![Új kulcs](media/azure-stack-kv-manage-powershell/image5.png)

A létrehozott kulcsot hivatkozhat az URI használatával. Ha hoz létre, vagy egy kulcs, amelynek neve megegyezik egy meglévő kulcs importálása, az eredeti kulcsra frissül az új kulcs megadott értékeket. Az előző verzió verzióspecifikus URI-azonosítója a kulcs használatával végezheti el. Példa: 

* "Https://vault10.vault.local.azurestack.external:443/kulcsok/key01" mindig letöltheti a legfrissebb verziót használja. 
* "Https://vault010.vault.local.azurestack.external:443/kulcsok/key01/d0b36ee2e3d14e9f967b8b6b1d38938a" használja ezt a verziót.

### <a name="get-a-key"></a>A kulcs lekérése

Használja a **Get-AzureKeyVaultKey** parancs futtatásával olvassa el a kulcs és a hozzá tartozó részletek.

```PowerShell
Get-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01”
```

### <a name="create-a-secret"></a>Titkos kulcs létrehozása

Használja a **Set-AzureKeyVaultSecret** parancs használatával hozható létre vagy frissíthető egy tároló titkos kulcs. Titkos kulcs jön létre, ha egy már nem létezik. A titkos kulcsot egy új verziója jön létre, ha már létezik.

```PowerShell
$secretvalue = ConvertTo-SecureString “User@123” -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01” -SecretValue $secretvalue
```

**Kimeneti**

![Titkos kulcs létrehozása](media/azure-stack-kv-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Titkos kulcs beszerzése

Használja a **Get-AzureKeyVaultSecret** parancs futtatásával olvassa el a kulcstároló titkos kulcs. Ez a parancs térhet vissza az összes vagy titkos kulcs verzióról. 

```PowerShell
Get-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01”
```

Miután létrehozta a kulcsok és titkos kulcsok, engedélyezheti a külső alkalmazásokkal is használhatja őket.

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>Az alkalmazások kulcsok vagy titkos kulcsok használata

Használja a **Set-AzureRmKeyVaultAccessPolicy** parancs használatával az alkalmazások, a kulcsok vagy titkos a key vaultban eléréséhez.
A következő példában a tároló neve: *ContosoKeyVault* , és engedélyezni kívánt alkalmazás Ügyfélazonosítója *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*. Engedélyezi az alkalmazásnak, hogy a következő parancsot. Ha szükséges, megadhatja a **PermissionsToKeys** paraméter segítségével állítsa be az engedélyeket a felhasználó, alkalmazás vagy egy biztonsági csoportot.

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Ha szeretné engedélyezni, hogy az alkalmazás megnyithassa a tárolóban, futtassa az alábbi parancsmagot:

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>Következő lépések
* [A Key Vault tárolt jelszóval egy virtuális gép üzembe helyezése](azure-stack-kv-deploy-vm-with-secret.md) 
* [A Key Vault tárolt tanúsítvány egy virtuális gép üzembe helyezése](azure-stack-kv-push-secret-into-vm.md)

