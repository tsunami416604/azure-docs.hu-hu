---
title: Az Azure Key Vault - helyreállítható törlés használata a PowerShell-lel
description: Kis példák a helyreállítható törlés funkciójának használata PowerShell-kódrészletek
author: bryanla
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: bryanla
ms.openlocfilehash: 48c471e17fb28843bf61f1591faafc119eb8dec8
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56002331"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>A Key Vault helyreállítható törlés használata a PowerShell-lel

Az Azure Key Vault helyreállítható törlési funkció lehetővé teszi, hogy a törölt tárolók és a tároló objektumok. Pontosabban a helyreállítható törlés címeket a következő esetekben:

- A key vault helyreállítható törlés támogatása
- A key vault-objektumokon; helyreállítható törlés támogatása a kulcsok, titkos kódok, és tanúsítványok

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- Az Azure PowerShell 1.0.0 vagy újabb – Ha nem rendelkezik már ebben a telepítő, az Azure PowerShell telepítése, és társíthatja azt az Azure-előfizetése, lásd: [telepítése és konfigurálása az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> Elavult verziójának használatát a Key Vault PowerShell-kimeneti formázás a fájlt, amely van **előfordulhat, hogy** kell betölteni a környezet helyett a megfelelő verziót. Azt is várhatóan frissített sémaverzióját tartalmazza a szükséges javítási a kimeneti formázás a PowerShell, és ez a témakör ekkor frissül. Az aktuális Áthidaló megoldásként kell tapasztal a formázási probléma van:
> - A következő lekérdezést, ha nem látja a helyreállítható törlés engedélyezve van a jelen témakörben ismertetett tulajdonság használatát: `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


A Key Vault műveletekre vonatkozó specifikus információkat PowerShell, lásd: [Azure Key Vault PowerShell-referencia](/powershell/module/az.keyvault).

## <a name="required-permissions"></a>Szükséges engedélyek

Key Vault-műveletek külön-külön módon felügyelt keresztül szerepköralapú hozzáférés-vezérlés (RBAC) engedélyekkel:

| Művelet | Leírás | Felhasználói engedélyek |
|:--|:--|:--|
|Lista|Listák törölt kulcstartók.|Microsoft.KeyVault/deletedVaults/read|
|Helyreállítás|Visszaállít egy törölt kulcstartó.|Microsoft.KeyVault/vaults/write|
|Véglegesen töröl|Véglegesen töröl egy törölt kulcstartó és annak teljes tartalmát.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Engedélyek és hozzáférés-vezérlés további információkért lásd: [kulcstartó védelme](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Helyreállítható Törlés engedélyezése

Engedélyezi a "helyreállítható törlés" törölt kulcstartó, vagy a key vaultban tárolt objektumok megbízotti.

> [!IMPORTANT]
> A key vault engedélyezése a "helyreállítható törlés" nem vonható vissza. A helyreállítható törlés tulajdonság van beállítva Ha "true", akkor nem lehet módosítani vagy eltávolítani.  

### <a name="existing-key-vault"></a>Meglévő kulcstároló

Egy meglévő kulcstartón ContosoVault nevű, a helyreállítható törlés engedélyezéséhez a következő. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Új kulcstartó

Az új key vault helyreállítható Törlés engedélyezése létrehozáskor hozzáadásával történik, a helyreállítható törlés funkciójának engedélyezése jelzőt a create paranccsal.

```powershell
New-AzKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Ellenőrizze a helyreállítható Törlés engedélyezése

Győződjön meg arról, hogy a key vault helyreállítható törlés engedélyezve van, futtassa a *megjelenítése* parancsot, és keresse meg a "helyreállítható törlés engedélyezve?" attribútum:

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>A key vault helyreállítható törlés törlése védett.

A parancs törli a viselkedése attól függően, hogy a helyreállítható törlés funkciójának engedélyezve van-e a key vault változásai.

> [!IMPORTANT]
>Ha futtatja a következő parancsot a key vault helyreállítható Törlés engedélyezése nem rendelkező, azzal végleg törli ezt a kulcstartót és a tartalmára nincs helyreállítási lehetőségeket!

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hogyan védje a helyreállítható törlés a a kulcstartók

Helyreállítható törlési engedélyezve:

- Egy törölt kulcstartó távolítva az erőforráscsoport és a helyet, ahol létrehozták társított fenntartott névtérben helyezi. 
- Törölt objektumok, például a kulcsok, titkos kódok és tanúsítványok, elérhetetlenné válnak, amennyiben azok tartalmazó kulcstartó a törölt állapotban van. 
- A DNS-név, a törölt kulcstartó van fenntartva, meggátolja, hogy egy azonos nevű új kulcstartó létrehozása folyamatban.  

Előfordulhat, hogy megtekintheti az állapot törölt kulcstartók, az Ön előfizetéséhez rendelve az alábbi paranccsal:

```powershell
PS C:\> Get-AzKeyVault -InRemovedState 
```

- *ID* helyreállításához, vagy végleges törlése az erőforrás azonosítására használható. 
- *Erőforrás-azonosító* ebben a tárban annak az eredeti erőforrás azonosítója. A kulcstartó most már törölt állapotban van, mivel nincs erőforrás létezik-e az adott erőforrás-azonosítója. 
- *Ütemezett dátum kiürítése* akkor, ha a tároló véglegesen törölve lesz, ha nem tesz. Az alapértelmezett megőrzési időtartamot, kiszámításához használt a *végleges törlés dátuma ütemezett*, 90 nap.

## <a name="recovering-a-key-vault"></a>Key vault helyreállítása

Key vault szeretné használni, adja meg a kulcstartó nevét, erőforráscsoportot és helyet. Vegye figyelembe a hely és az erőforráscsoport, a törölt key vault, a helyreállítási folyamat és igény szerint.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Amikor a key vault helyreállítása után egy új erőforrás jön létre a key vault az eredeti erőforrás azonosítóval. Ha eltávolít az eredeti erőforráscsoportot, egy kell létrehozni ugyanazzal a névvel rendelkező helyreállítási megkísérlése előtt.

## <a name="deleting-and-purging-key-vault-objects"></a>Key vault-objektumokon törléshez és törlése

A következő parancs törli a "ContosoFirstKey" kulcsot, a key vault neve "ContosoVault", ami helyreállítható törlés engedélyezve van:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

A key vault helyreállítható törlés funkciójának engedélyezve a törölt kulcsot továbbra is megjelenik törölni kell, kivéve, ha explicit módon listában törölt kulcsok. Egy kulcsot a törölt állapotban a legtöbb művelet sikertelen lesz, kivéve listázása, visszaállítása, egy törölt kulcs törlése. 

Ha például a következő parancs felsorolja a "ContosoVault" key vaultban lévő kulcsok törlése:

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Átmeneti állapot 

Ha töröl egy kulcsot a key vault helyreállítható törlési engedélyezve van, igénybe vehet néhány másodpercet, amíg az áttérés befejezéséhez. Ez a váltás során, előfordulhat, hogy jelennek meg, hogy a kulcs nem szerepel az aktív állapot vagy a törölt állapotból. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Helyreállítható törlés használata a key vault-objektumokon

Kulcstartók, mint egy törölt kulcs, a titkos kulcsot vagy a tanúsítvány, állapotban marad a törölt legfeljebb 90 napig, kivéve, ha a helyreállítást, vagy végleges törlése. 

#### <a name="keys"></a>Kulcsok

Helyreállíthatóan törölt kulcs helyreállítása:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Végleg törölni kívánja (más néven végleges törlése) a helyreállíthatóan törölt kulcs:

> [!IMPORTANT]
> Kulcs törlése véglegesen törli azt, és nem lesz helyreállítható. 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

A **helyreállítása** és **kiürítése** műveletek rendelkezik saját a kulcstartó hozzáférési házirendben tartozó engedélyeket. Egy felhasználó vagy szolgáltatásnév lehessen végrehajtani egy **helyreállítása** vagy **kiürítése** műveletet, a kulcs vagy titkos kód a megfelelő engedéllyel kell rendelkezniük. Alapértelmezés szerint **kiürítése** nem a kulcstartó-hozzáférési házirend, amikor megjelenik az "all" helyi összes engedélyek megadására szolgál. Kifejezetten biztosítania kell **kiürítése** engedéllyel. 

#### <a name="set-a-key-vault-access-policy"></a>A kulcstartó-hozzáférési házirend beállítása

A következő parancsot a biztosít user@contoso.com használja a kulcsok több műveletre engedélyt *ContosoVault* beleértve **kiürítése**:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Ha egy meglévő kulcstároló, amely még csak helyreállítható törlés engedélyezve van, előfordulhat, hogy nem rendelkezik **helyreállítása** és **kiürítése** engedélyeket.

#### <a name="secrets"></a>Titkos kulcsok

Például a kulcsok titkos kulcsok kezelt saját parancsokkal:

- SQLPassword nevű titkos kulcs törlése: 
```powershell
Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
```

- Összes törölt key vault titkos kulcsainak listázása: 
```powershell
Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
```

- Helyreállítás törölt állapotban titkos kulcs: 
```powershell
Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
```

- Törölt állapotban titkos kulcs törlése: 

  > [!IMPORTANT]
  > Titkos kulcs törlése véglegesen törli azt, és nem lesz helyreállítható.

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>A key vault helyreállítható törlés kiürítése védett

> [!IMPORTANT]
> Key vault vagy a benne tárolt objektum-ürítést, véglegesen törli, ami azt jelenti, nem lesz helyreállítható!

A végleges törlés amelyeket át véglegesen törli a kulcstartó-objektum vagy egy teljes key vault, amely lett korábban helyreállíthatóan törölt szolgál. Ahogyan az az előző szakaszban, a helyreállítható törlési funkció engedélyezve van, az a key vaultban tárolt objektumok végigveheti több állapota:
- **Aktív**: törlés előtt.
- **Helyreállíthatóan törölt**: törlés, a felsorolt és aktív állapotba helyreállítása után.
- **Véglegesen törli**: után végleges törlése nem sikerült helyreállítani.


Ugyanez igaz a kulcstartó. Annak érdekében, hogy véglegesen törli a helyreállíthatóan törölt kulcstartó és annak tartalmát, véglegesen törli magát a kulcstartót.

### <a name="purging-a-key-vault"></a>Kulcstartó végleges törlése

Amikor a key vault törölve van, a teljes tartalmát véglegesen törlődnek, beleértve a kulcsok, titkos kódok és tanúsítványok. Helyreállíthatóan törölt kulcstartó végleges törlése, használja a `Remove-AzKeyVault` parancsot a lehetőséggel `-InRemovedState` , és adja meg a törölt kulcstartó helye a `-Location location` argumentum. A parancs egy törölt tároló helyét annak `Get-AzKeyVault -InRemovedState`.

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Szükséges engedélyek törlése
- A törölt kulcstartó végleges törlése, a felhasználónak az RBAC-engedély van szüksége a *Microsoft.KeyVault/locations/deletedVaults/purge/action* műveletet. 
- Egy törölt kulcstárolót listázásához, a felhasználónak az RBAC-engedély van szüksége a *Microsoft.KeyVault/deletedVaults/read* műveletet. 
- Alapértelmezés szerint csak egy előfizetés-rendszergazda rendelkezik ezekkel az engedélyekkel. 

### <a name="scheduled-purge"></a>Ütemezett végleges törlése

Törölt key vault-objektumokon ajánlati is bemutatja, amikor még a Key Vault kiürítse ütemezett. *Ütemezett dátum kiürítése* azt jelzi, hogy amikor a key vaulttal objektum véglegesen törölve lesz, ha nem tesz. Alapértelmezés szerint a törölt kulcstartó objektum megőrzési időtartama a 90 nap.

>[!IMPORTANT]
>A tár törölve objektum, által aktivált annak *végleges törlés dátuma ütemezett* mezőben, az véglegesen törlődni fog. Már nem helyreállítható!

## <a name="other-resources"></a>Egyéb erőforrások

- A Key Vault helyreállítható törlés funkciójának áttekintéséhez lásd: [Azure Key Vault helyreállítható törlés áttekintése](key-vault-ovw-soft-delete.md).
- Az Azure Key Vault használatának általános áttekintéséért lásd: [első lépései az Azure Key Vault](key-vault-get-started.md).

