---
ms.assetid: ''
title: Az Azure Key Vault - helyreállítható törlés használata a PowerShell-lel
description: Kis példák a helyreállítható törlés funkciójának használata PowerShell-kódrészletek
services: key-vault
author: bryanla
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 08/21/2017
ms.author: bryanla
ms.openlocfilehash: 93105210267ebadf4273db56e2e147b1b34485e3
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298131"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>A Key Vault helyreállítható törlés használata a PowerShell-lel

Az Azure Key Vault helyreállítható törlési funkció lehetővé teszi, hogy a törölt tárolók és a tároló objektumok. Pontosabban a helyreállítható törlés címeket a következő esetekben:

- A key vault helyreállítható törlés támogatása
- A key vault-objektumokon; helyreállítható törlés támogatása a kulcsok, titkos kódok, és tanúsítványok

## <a name="prerequisites"></a>Előfeltételek

- Az Azure PowerShell 4.0.0 vagy újabb – Ha nem rendelkezik már ebben a telepítő, az Azure PowerShell telepítése, és társíthatja azt az Azure-előfizetése, lásd: [telepítése és konfigurálása az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> Elavult verziójának használatát a Key Vault PowerShell-kimeneti formázás a fájlt, amely van **előfordulhat, hogy** kell betölteni a környezet helyett a megfelelő verziót. Azt is várhatóan frissített sémaverzióját tartalmazza a szükséges javítási a kimeneti formázás a PowerShell, és ez a témakör ekkor frissül. Az aktuális Áthidaló megoldásként kell tapasztal a formázási probléma van:
> - A következő lekérdezést, ha nem látja a helyreállítható törlés engedélyezve van a jelen témakörben ismertetett tulajdonság használatát: `$vault = Get-AzureRmKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


Adott refernece információk a Key Vault PowerShell: [Azure Key Vault PowerShell-referencia](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0).

## <a name="required-permissions"></a>Szükséges engedélyek

Key Vault-műveletek külön-külön módon felügyelt keresztül szerepköralapú hozzáférés-vezérlés (RBAC) engedélyekkel:

| Művelet | Leírás | Felhasználói engedélyek |
|:--|:--|:--|
|Lista|Listák törölt kulcstartók.|Microsoft.KeyVault/deletedVaults/read|
|Helyreállítás|Visszaállít egy törölt kulcstartó.|Microsoft.KeyVault/vaults/write|
|Véglegesen töröl|Véglegesen töröl egy törölt kulcstartó és annak teljes tartalmát.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Engedélyek és hozzáférés-vezérlés további információkért lásd: [kulcstartó védelme](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Helyreállítható Törlés engedélyezése

Ahhoz, hogy helyreállíthatja a törölt kulcstartó vagy a key vaultban tárolt objektumok, először engedélyeznie kell, hogy a key vault helyreállítható törlés.

### <a name="existing-key-vault"></a>Meglévő kulcstároló

Egy meglévő kulcstartón ContosoVault nevű, a helyreállítható törlés engedélyezéséhez a következő. 

>[!NOTE]
>Jelenleg kell használnia az Azure Resource Manager-erőforrás adatkezelési közvetlenül írni a *enableSoftDelete* tulajdonság a Key Vault-erőforráshoz.

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzureRmResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Új kulcstartó

Az új key vault helyreállítható Törlés engedélyezése létrehozáskor hozzáadásával történik, a helyreállítható törlés funkciójának engedélyezése jelzőt a create paranccsal.

```powershell
New-AzureRmKeyVault -VaultName "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Ellenőrizze a helyreállítható Törlés engedélyezése

Győződjön meg arról, hogy a key vault helyreállítható törlés engedélyezve van, futtassa a *első* parancsot, és keresse meg a "helyreállítható törlés engedélyezve?" attribútum és a beállítása true vagy FALSE (hamis).

```powershell
Get-AzureRmKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Key vault helyreállítható törlés által védett törlése

A parancs törlése (vagy eltávolítása) key vault változatlan marad, de attól függően, hogy engedélyezte a helyreállítható törlés vagy nem módosítja annak viselkedését.

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoVault'
```

> [!IMPORTANT]
>A key vault helyreállítható Törlés engedélyezése nem rendelkező az előző parancs futtatásakor, azzal végleg törli a kulcstartó és a helyreállítási beállítások nélkül annak tartalmát.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hogyan védje a helyreállítható törlés a a kulcstartók

Helyreállítható törlési engedélyezve:

- Key vault törlésekor az erőforráscsoportból eltávolítása és elhelyezni egy fenntartott névtér, amely csak a hely, ahol létrehozták társított. 
- Egy törölt kulcsban található objektumok tárolót, például a kulcsok, titkos kódok és tanúsítványok, elérhetetlenné válnak, és így marad, amíg azok tartalmazó kulcstartó a törölt állapotban van. 
- A DNS-név kulcstartó törölt állapotban továbbra is fenntartva, ezért nem hozhatók létre egy új kulcstartóba, azonos nevű.  

Előfordulhat, hogy megtekintheti az állapot törölt kulcstartók, az Ön előfizetéséhez rendelve az alábbi paranccsal:

```powershell
PS C:\> Get-AzureRmKeyVault -InRemovedStateVault 

Name           : ContosoVault
Location             : westus
Id                   : /subscriptions/xxx/providers/Microsoft.KeyVault/locations/westus/deletedVaults/ContosoVault
Resource ID          : /subscriptions/xxx/resourceGroups/ContosoVault/providers/Microsoft.KeyVault/vaults/ContosoVault
Deletion Date        : 5/9/2017 12:14:14 AM
Scheduled Purge Date : 8/7/2017 12:14:14 AM
Tags                 :
```

A *erőforrás-azonosító* a kimenetben a tár az eredeti erőforrás-Azonosítójára hivatkozik. A kulcstartó most már törölt állapotban van, mivel nincs erőforrás létezik-e az adott erőforrás-azonosítója. A *azonosító* mező segítségével azonosíthatja az erőforrás helyreállításához, vagy törlése. A *végleges törlés dátuma ütemezett* mező jelzi, ha a tároló véglegesen törölve lesz (törölve) Ha nem tesz a törölt tároló. Az alapértelmezett megőrzési időtartamot, kiszámításához használt a *végleges törlés dátuma ütemezett*, 90 nap.

## <a name="recovering-a-key-vault"></a>Key vault helyreállítása

Key vault szeretné használni, adja meg a kulcstartó nevét, erőforráscsoportot és helyet kell. Megjegyzés: a hely és az erőforráscsoport, a törölt kulcstartó szükség van ezekre a key vault a helyreállítási folyamatot.

```powershell
Undo-AzureRmKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Amikor a key vault helyreállítása után jön egy új erőforrást a key vault eredeti erőforrás-azonosítója. Az erőforráscsoport, ahol a key vault létezett el lett távolítva, ugyanilyen nevű új erőforráscsoportot, amelybe a key vault helyreállítható előtt kell létrehozni.

## <a name="key-vault-objects-and-soft-delete"></a>A Key Vault-objektumok és a helyreállítható törlés

Egy kulcs, a "ContosoFirstKey", a kulcstartó neve "ContosoVault" helyreállítható törlési engedélyezve van, itt a hogyan így törölni ezt a kulcsot.

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

A key vault helyreállítható törlés funkciójának engedélyezve az egy törölt továbbra is megjelenik a kulcs, akkor törlődik, kivéve, ha explicit módon listában vagy törölt kulcsok lekéréséhez. A törölt állapotban kulcs vonatkozó műveletek többségét kivéve egy törölt kulcs listázása, helyreállítását tisztázására vagy végleges törlésére, sikertelen lesz. 

Kérelem törölve lista kulcsok a key vaultban, például használja a következő parancsot:

```powershell
Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Átmeneti állapot 

Ha töröl egy kulcsot a key vault helyreállítható törlési engedélyezve van, igénybe vehet néhány másodpercet, amíg az áttérés befejezéséhez. Ebben az átmeneti állapotban tűnhet, hogy a kulcs nem szerepel az aktív állapot vagy a törölt állapotból. Ez a parancs listázza az összes törölt kulcsok tárol a kulcstárolóban "ContosoVault" nevű.

```powershell
  Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
  Vault Name           : ContosoVault
  Name                 : ContosoFirstKey
  Id                   : https://ContosoVault.vault.azure.net:443/keys/ContosoFirstKey
  Deleted Date         : 2/14/2017 8:20:52 PM
  Scheduled Purge Date : 5/15/2017 8:20:52 PM
  Enabled              : True
  Expires              :
  Not Before           :
  Created              : 2/14/2017 8:16:07 PM
  Updated              : 2/14/2017 8:16:07 PM
  Tags                 :
```

### <a name="using-soft-delete-with-key-vault-objects"></a>Helyreállítható törlés használata a key vault-objektumokon

Hasonlóan a kulcstartók, egy törölt kulcs titkos kulcsot, vagy tanúsítvány állapotban marad törölt legfeljebb 90 napig, kivéve ha a helyreállítást, vagy végleges törlése. 

#### <a name="keys"></a>Kulcsok

Helyreállítani egy törölt kulcs:

```powershell
Undo-AzureKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Véglegesen törli a kulcs:

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

>[!NOTE]
>Kulcs törlése véglegesen törli azt, ami azt jelenti, nem lesz helyreállítható.

A **helyreállítása** és **kiürítése** műveletek rendelkezik saját a kulcstartó hozzáférési házirendben tartozó engedélyeket. Egy felhasználó vagy szolgáltatásnév lehessen végrehajtani egy **helyreállítása** vagy **kiürítése** művelet (kulcs vagy titkos kód) objektum a megfelelő engedéllyel kell rendelkezniük a kulcstartó-hozzáférési házirendben. Alapértelmezés szerint a **kiürítése** engedély nem kerül be a kulcstartó-hozzáférési házirend, ha az "all" helyi minden engedélyt biztosíthat a felhasználók. Kifejezetten biztosítania kell **kiürítése** engedéllyel. Az alábbi parancs például biztosít user@contoso.com több műveletek végrehajtása a kulcsokat az engedély *ContosoVault* többek között **kiürítése**.

#### <a name="set-a-key-vault-access-policy"></a>A kulcstartó-hozzáférési házirend beállítása

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Ha egy meglévő kulcstároló, amely még csak helyreállítható törlés engedélyezve van, előfordulhat, hogy nem rendelkezik **helyreállítása** és **kiürítése** engedélyeket.

#### <a name="secrets"></a>Titkos kulcsok

Például a kulcsok titkos kulcsok a key vault üzemeltetnek a saját parancsokkal. A következő, rendszer törlése, listázása, visszaállítása és titkos kódok törlése parancsokat.

- SQLPassword nevű titkos kulcs törlése: 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -name SQLPassword
```

- Összes törölt key vault titkos kulcsainak listázása: 
```powershell
Get-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState
```

- Helyreállítás törölt állapotban titkos kulcs: 
```powershell
Undo-AzureKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
```

- Törölt állapotban titkos kulcs törlése: 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
```

>[!NOTE]
>Titkos kulcs törlése véglegesen törli azt, ami azt jelenti, nem lesz helyreállítható.

## <a name="purging-and-key-vaults"></a>Tárolók kiürítési és kulcs

### <a name="key-vault-objects"></a>Key vault-objektumokon

Kulcs törlése, titkos kulcs, vagy tanúsítvány véglegesen törölni fogja, ami azt jelenti, nem lesz helyreállítható. A key vaultban, amelyek a törölt objektum azonban érintetlen marad, a key vaultban lévő összes többi objektum lesz. 

### <a name="key-vaults-as-containers"></a>A kulcstartók tárolók
Amikor a key vault törölve van, és annak teljes tartalmát, beleértve a kulcsok, titkos kódok és tanúsítványok, véglegesen törlődnek. Kulcstartó végleges törlése, használja a `Remove-AzureRmKeyVault` parancsot a lehetőséggel `-InRemovedState` , és adja meg a törölt kulcstartó helye a `-Location location` argumentum. A parancs egy törölt tároló helyét annak `Get-AzureRmKeyVault -InRemovedState`.

```powershell
Remove-AzureRmKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

>[!NOTE]
>Kulcstartó végleges törlése véglegesen törli azt, ami azt jelenti, nem lesz helyreállítható.

### <a name="purge-permissions-required"></a>Szükséges engedélyek törlése
- Egy törölt kulcstartó végleges törlése, úgy, hogy a tárolóhoz és annak minden tartalma véglegesen törlődnek, hogy a felhasználónak az RBAC-engedély végrehajtásához egy *Microsoft.KeyVault/locations/deletedVaults/purge/action* műveletet. 
- A törölt kulcs listázásához, a tároló egy felhasználónak kell végrehajtani az RBAC-engedély *Microsoft.KeyVault/deletedVaults/read* engedéllyel. 
- Alapértelmezés szerint csak egy előfizetés-rendszergazda rendelkezik ezekkel az engedélyekkel. 

### <a name="scheduled-purge"></a>Ütemezett végleges törlése

Listázása a törölt kulcstartó-objektumokat jeleníti meg, amikor azok schedled kiürítse a Key Vault. A *végleges törlés dátuma ütemezett* mező jelzi, hogy amikor a key vaulttal objektum véglegesen törölve lesz, ha nem tesz. Alapértelmezés szerint a törölt kulcstartó objektum megőrzési időtartama a 90 nap.

>[!NOTE]
>A tár törölve objektum, által aktivált annak *végleges törlés dátuma ütemezett* mezőben, az véglegesen törlődni fog. Esetén nem állítható helyre.

## <a name="other-resources"></a>Egyéb erőforrások

- A Key Vault helyreállítható törlés funkciójának áttekintéséhez lásd: [Azure Key Vault helyreállítható törlés áttekintése](key-vault-ovw-soft-delete.md).
- Az Azure Key Vault használatának általános áttekintéséért lásd: [első lépései az Azure Key Vault](key-vault-get-started.md).

