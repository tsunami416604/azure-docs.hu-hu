---
title: Azure Key Vault – a Soft-delete használata a PowerShell használatával
description: Megtudhatja, hogyan használhatja a Azure PowerShell a Azure Key Vault Soft-delete funkciójának használatára, amely lehetővé teszi a kulcstartók és a Key Vault-objektumok helyreállítását.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/11/2020
ms.author: sudbalas
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 251159c4fcf27ceb4d8b0dddeecbe501c7346e9e
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073233"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>A Key Vault helyreállítható törlés funkciójának használata PowerShell-lel

Azure Key Vault-törlési funkciója lehetővé teszi a törölt tárolók és tároló objektumok helyreállítását. A Soft-Delete a következő forgatókönyvekkel foglalkozik:

- Key Vault helyreállítható törlésének támogatása
- A Key Vault-objektumok helyreállítható törlésének támogatása; kulcsok, titkos kódok és tanúsítványok

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 vagy újabb verzió – ha nem rendelkezik ezzel a beállítással, telepítse Azure PowerShell és társítsa az Azure-előfizetéséhez, lásd: [a Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/). 

>[!NOTE]
> A Key Vault PowerShell kimeneti formázó fájl elavult változata, amely a megfelelő verzió helyett a környezetbe **tölthető be.** A PowerShell frissített verziója arra készül, hogy tartalmazza a kimeneti formázáshoz szükséges korrekciót, és hogy a témakört a későbbiekben frissíti. A jelenlegi megkerülő megoldás, ha ezt a formázási problémát tapasztalja, a következő:
> - Ha azt észleli, hogy a következő lekérdezés nem jelenik meg a jelen témakörben ismertetett, nem kötelező törlés engedélyezve tulajdonság: `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete` .


A PowerShell-hez Key Vault konkrét referenciáért lásd: [Azure Key Vault PowerShell-útmutató](/powershell/module/az.keyvault).

## <a name="required-permissions"></a>Szükséges engedélyek

A Key Vault műveleteket a szerepköralapú hozzáférés-vezérlési (RBAC) engedélyekkel külön kezelik a következők szerint:

| Művelet | Leírás | Felhasználói engedély |
|:--|:--|:--|
|Lista|Felsorolja a törölt kulcstartókat.|Microsoft. kulcstartó/deletedVaults/olvasás|
|Helyreállítás|Visszaállítja a törölt kulcstartót.|Microsoft. kulcstartó/tárolók/írás|
|Véglegesen töröl|Véglegesen eltávolítja a törölt kulcstartót és annak teljes tartalmát.|Microsoft. kulcstartó/Locations/deletedVaults/Purge/művelet|

Az engedélyekkel és a hozzáférés-vezérléssel kapcsolatos további információkért lásd: [a Key Vault biztonságossá tétele](secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Törlés engedélyezése

A "Soft-Delete" lehetővé teszi a törölt kulcstartó vagy a kulcstartóban tárolt objektumok helyreállítását.

> [!IMPORTANT]
> A Key Vault "Soft-Delete" engedélyezése visszafordíthatatlan művelet. Ha a Soft-delete tulajdonság értéke "true" (igaz), akkor nem módosítható és nem távolítható el.  

### <a name="existing-key-vault"></a>Meglévő kulcstartó

Egy ContosoVault nevű meglévő kulcstartó esetén a következő módon engedélyezze a Soft delete törlését. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Új kulcstartó

Alapértelmezés szerint az összes új kulcstartó esetében a Soft delete automatikusan bekerül. December 31-ig a 2020 többé nem lesz lehetséges a Soft delete letiltása bármely kulcstartón. 

### <a name="verify-soft-delete-enablement"></a>Helyreállítható törlési engedélyezés ellenőrzése

Annak ellenőrzéséhez, hogy a kulcstárolóban engedélyezve van-e a helyreállítható törlés, futtassa a *show* parancsot, és keresse meg a "Soft delete enabled?" lehetőséget. attribútum

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>A védett kulcstartó törlésének törlése

Az a parancs, amellyel törölheti a kulcstartót, attól függően, hogy engedélyezve van-e a helyreállítható törlés.

> [!IMPORTANT]
>Ha olyan kulcstartóhoz futtatja a következő parancsot, amely nem rendelkezik a helyreállítható törléssel, véglegesen törli ezt a kulcstartót és annak összes tartalmát a helyreállítás lehetőség nélkül.

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>A Soft-delete védi a kulcstartókat

A helyreállítható törlés engedélyezve:

- A rendszer eltávolítja a Deleted Key vaultot az erőforráscsoporthoz, és egy fenntartott névtérbe helyezi, amely ahhoz a helyhez van társítva, ahol létrehozták. 
- A törölt objektumok (például kulcsok, titkos kódok és tanúsítványok) nem érhetők el, amíg a kulcstárolót tartalmazó kulcstartó törölve állapotban van. 
- A törölt kulcstartó DNS-neve fenntartva, megakadályozva, hogy egy új kulcstároló ugyanazzal a névvel legyen létrehozva.  

Az előfizetéshez társított törölt állapotú kulcstartók a következő paranccsal tekinthetők meg:

```powershell
Get-AzKeyVault -InRemovedState 
```

- Az *azonosító* használatával lehet azonosítani az erőforrást a helyreállítás vagy a kiürítés során. 
- Az *erőforrás-azonosító* a tár eredeti erőforrás-azonosítója. Mivel ez a kulcstartó már törölt állapotban van, nem létezik erőforrás az erőforrás-AZONOSÍTÓval. 
- Az *ütemezett kiürítési dátum* az, amikor a tároló véglegesen törölve lesz, ha nincs művelet. Az *ütemezett kiürítési dátum*kiszámításához használt alapértelmezett megőrzési időtartam 90 nap.

## <a name="recovering-a-key-vault"></a>Kulcstartó helyreállítása

Kulcstartó helyreállításához meg kell adnia a kulcstároló nevét, az erőforráscsoportot és a helyet. Jegyezze fel a törölt kulcstartó helyét és erőforrás-csoportját, ahogy a helyreállítási folyamathoz szükség van rájuk.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Kulcstartó helyreállításakor a Key Vault eredeti erőforrás-azonosítójával létrejön egy új erőforrás. Ha az eredeti erőforráscsoport el lett távolítva, a helyreállítás megkísérlése előtt egyet kell létrehoznia ugyanazzal a névvel.

## <a name="deleting-and-purging-key-vault-objects"></a>Key Vault-objektumok törlése és törlése

A következő parancs törli a "ContosoFirstKey" kulcsot a (z) "ContosoVault" nevű kulcstartóban, amelynél engedélyezve van a helyreállítható törlés:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Ha a kulcstartó engedélyezve van a helyreállítható törléshez, a törölt kulcs továbbra is törölve lesz, kivéve, ha kifejezetten a törölt kulcsokat listázza. A törölt állapotban lévő egyik kulcson végrehajtott műveletek többsége sikertelen lesz, kivéve a listát, a helyreállítást, a törölt kulcs törlését. 

Például a következő parancs felsorolja a törölt kulcsokat a "ContosoVault" kulcstartóban:

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Átmeneti állapot 

Ha töröl egy kulcsot egy kulcstartóban, és engedélyezve van a helyreállítható törlés, eltarthat néhány másodpercig, amíg az áttérés be nem fejeződik. A váltás során előfordulhat, hogy a kulcs nem aktív állapotban van, vagy a törölt állapotban van. 

### <a name="using-soft-delete-with-key-vault-objects"></a>A Soft delete használata a Key Vault-objektumokkal

Csakúgy, mint a Key vaultok, a törölt kulcsok, titkos kódok vagy tanúsítványok, akár 90 napig törölve maradnak, hacsak nem állítja helyre vagy nem törli azt. 

#### <a name="keys"></a>Kulcsok

Helyreállítható kulcs helyreállítása:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Ha véglegesen törölni kívánja (más néven öblítést), a rendszer egy helyreállított kulcsot is töröl:

> [!IMPORTANT]
> A kulcsok végleges törlése véglegesen törli azt, és nem állítható vissza. 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

A **helyreállítás** és **Törlés** műveletekhez saját engedélyek tartoznak a Key Vault hozzáférési házirendjében. Ahhoz, hogy egy felhasználó vagy egy egyszerű szolgáltatásnév végrehajtson egy **helyreállítási** vagy kitakarítási műveletet, rendelkeznie kell a megfelelő engedélyekkel az adott kulcshoz **vagy** titokhoz. Alapértelmezés szerint a **kiürítés** nincs hozzáadva a kulcstartó hozzáférési házirendjéhez, ha az összes engedély megadására az "all" (összes) parancsikont használja. Kifejezetten meg kell adnia a **kiürítési** engedélyt. 

#### <a name="set-a-key-vault-access-policy"></a>Key Vault hozzáférési szabályzatának beállítása

A következő parancs user@contoso.com engedélyt ad arra, hogy több műveletet használjon a kulcsokon a *ContosoVault* , beleértve a **kiürítést**:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Ha már van olyan kulcstartója, amelynél engedélyezve van a **helyreállított** törlés, akkor előfordulhat, hogy nem rendelkezik helyreállítási és **törlési** engedélyekkel.

#### <a name="secrets"></a>Titkos kulcsok

A kulcsokhoz hasonlóan a titkos kódok is a saját parancsaikkal kezelhetők:

- SQLPassword nevű titkos kód törlése: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- A Key Vault összes törölt titkának listázása: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Titkos kód helyreállítása törölt állapotban: 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Titkos kód törlése törölt állapotban: 

  > [!IMPORTANT]
  > A titkos kód végleges törlésével véglegesen törlődik, és nem lesz helyreállítható.

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

#### <a name="certificates"></a>Tanúsítványok

A tanúsítványokat az alábbi parancsokkal kezelheti:

- Tanúsítvány törlése: 
  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

- A Key Vault összes törölt tanúsítványának listázása: 
  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

- Tanúsítvány helyreállítása törölt állapotban: 
  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

- Tanúsítvány kiürítése törölt állapotban: 

  > [!IMPORTANT]
  > A tanúsítvány végleges törlésével véglegesen törlődik, és nem lesz helyreállítható.

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState 
  ```
  
## <a name="purging-a-soft-delete-protected-key-vault"></a>Védett kulcstartó törlésének végleges törlése

> [!IMPORTANT]
> Egy kulcstartó vagy egy benne lévő objektum kiürítése véglegesen törölve lesz, ami azt jelenti, hogy nem lesz helyreállítható.

A kiürítési függvény használatával véglegesen törölhető egy kulcstároló-objektum vagy egy teljes kulcstartó, amelyet korábban már töröltek. Ahogy az előző szakaszban is látható, a Key vaultban tárolt objektumok, amelyeken engedélyezve van a Soft delete funkció, több állapotba is léphetnek:
- **Aktív**: törlés előtt.
- **Soft-Deleted**: törlés után a rendszer képes a listára, és helyreállítani az aktív állapotot.
- **Véglegesen törölve**: törlés után nem lehet helyreállítani.


Ugyanez érvényes a Key Vault esetében is. Ha véglegesen törölni szeretné a helyreállított kulcstartót és annak tartalmát, akkor magát a kulcstartót kell kiürítenie.

### <a name="purging-a-key-vault"></a>Kulcstartó törlése

Ha töröl egy kulcstartót, a teljes tartalma véglegesen törlődik, beleértve a kulcsokat, a titkokat és a tanúsítványokat is. A helyreállítható kulcstartó törléséhez használja a `Remove-AzKeyVault` parancsot a kapcsolóval, és a `-InRemovedState` törölt kulcstartó helyét megadhatja az `-Location location` argumentummal. A törölt tároló helyét a parancs használatával találja meg `Get-AzKeyVault -InRemovedState` .

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Törlési engedélyek szükségesek
- A törölt kulcstartó kiürítéséhez a felhasználónak RBAC engedéllyel kell rendelkeznie a *Microsoft. kulcstartó/Locations/deletedVaults/Purge/Action* művelethez. 
- A törölt kulcstartók listázásához a felhasználónak RBAC engedéllyel kell rendelkeznie a *Microsoft. kulcstartó/deletedVaults/Read* művelethez. 
- Alapértelmezés szerint csak az előfizetések rendszergazdái rendelkeznek ezekkel az engedélyekkel. 

### <a name="scheduled-purge"></a>Ütemezett kiürítés

A törölt Key Vault-objektumok listázása azt is jelzi, hogy mikor Key Vault a rendszer az ütemezett törlést ütemezi. Az *ütemezett kiürítési dátum* azt jelzi, hogy a Key Vault-objektumok véglegesen törlődnek-e, ha nem végeznek műveletet. Alapértelmezés szerint a törölt Key Vault-objektum megőrzési időtartama 90 nap.

>[!IMPORTANT]
>A rendszer véglegesen törli az *ütemezett kiürítési dátum* mező által aktivált, kiürített tár objektumot. Nem helyreállítható!

## <a name="enabling-purge-protection"></a>A kiürítési védelem engedélyezése

Ha a kiürítési védelem be van kapcsolva, a tároló vagy a törölt állapotban lévő objektum nem törölhető, amíg meg nem adta a megőrzési időszakot. Ilyen tár vagy objektum továbbra is helyreállítható. Ez a funkció biztosítja, hogy egy tár vagy egy objektum soha nem törölhető véglegesen, amíg a megőrzési időszak el nem telik. Az alapértelmezett megőrzési időtartam 90 nap, de a Key Vault létrehozása során lehetséges, hogy a megőrzési házirend intervallumát 7 – 90 nap értékre állítja be. A kiürítési védelem adatmegőrzési szabályzata ugyanazt az intervallumot használja. A beállítás után az adatmegőrzési házirend intervalluma nem módosítható.

Csak akkor engedélyezheti a kiürítést, ha a Soft-delete is engedélyezve van. A kiürítési védelem letiltása nem támogatott. 

Ha be szeretné kapcsolni a törlést és a kiürítést a tár létrehozásakor, használja a [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0) parancsmagot:

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Ha egy meglévő tárolóhoz kívánja hozzáadni a kiürítési védelmet (amely már rendelkezik az engedélyezett törlési lehetőséggel), használja a [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0), a [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0)és a [set-AzResource](/powershell/module/az.resources/set-azresource?view=azps-1.5.0) parancsmagot:

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Egyéb erőforrások

- A Key Vault-törlési funkció áttekintését lásd: Azure Key Vault- [Törlés – áttekintés](soft-delete-overview.md)).
- A Azure Key Vault használatának általános áttekintését lásd: [Mi az a Azure Key Vault?](overview.md)).
