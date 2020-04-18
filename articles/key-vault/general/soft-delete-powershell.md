---
title: Azure Key Vault - A soft-delete használata a PowerShell használatával
description: Példaként idét a PowerShell-kódmetszetekkel történő helyreállítható törlésre
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 79d8cb4b09ef547bf1c0b01f48872ddcb4f964ee
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616533"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>A Key Vault helyreállítható törlés funkciójának használata PowerShell-lel

Az Azure Key Vault helyreállítható törlési funkciója lehetővé teszi a törölt tárolók és a tárolóobjektumok helyreállítását. Pontosabban, a soft-delete címek a következő forgatókönyvek:

- A kulcstartó helyreállítható törlésének támogatása
- A kulcstartó-objektumok helyreállítható törlésének támogatása; kulcsok, titkos kulcsok és tanúsítványok

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 vagy újabb verzió – Ha még nem rendelkezik ezzel a beállítással, telepítse az Azure PowerShellt, és társítsa azure-előfizetéséhez, olvassa el [az Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/overview)című témakört. 

>[!NOTE]
> Van egy elavult verziója a Key Vault PowerShell kimeneti formázási fájl, amely **lehet** betölteni a környezetbe, hanem a megfelelő verziót. A PowerShell frissített verzióját várjuk, hogy tartalmazza a kimeneti formázáshoz szükséges korrekciót, és akkor frissíti ezt a témakört. Az aktuális megoldás, ha a formázási probléma merül fel, a következő:
> - Használja a következő lekérdezést, ha azt veszi észre, hogy nem `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete`látja a jelen témakörben ismertetett, helyreállítható törlésre engedélyezett tulajdonságot: .


A Key Vault-specifikus referenciainformációk a PowerShell, az [Azure Key Vault PowerShell referencia.](/powershell/module/az.keyvault)

## <a name="required-permissions"></a>Szükséges engedélyek

A Key Vault-műveleteket külön kezeli a szerepköralapú hozzáférés-vezérlési (RBAC) engedélyek az alábbiak szerint:

| Művelet | Leírás | Felhasználói engedély |
|:--|:--|:--|
|Lista|A törölt kulcstartók listája.|Microsoft.KeyVault/deletedVaults/read|
|Helyreállítás|Visszaállít egy törölt kulcstartót.|Microsoft.KeyVault/vaults/write|
|Véglegesen töröl|Véglegesen eltávolítja a törölt kulcstartót és annak teljes tartalmát.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Az engedélyekről és a hozzáférés-vezérlésről a [Kulcstartó védelme](secure-your-key-vault.md)című témakörben talál további információt.

## <a name="enabling-soft-delete"></a>Helyreállítható törlés engedélyezése

A "helyreállítva törlést" engedélyezi a törölt kulcstartó vagy a key vaultban tárolt objektumok helyreállításához.

> [!IMPORTANT]
> A "helyreállítható törlés" engedélyezése a key vaultban visszafordíthatatlan művelet. Ha a helyreállítható törlés tulajdonság értéke "true", nem módosítható vagy távolítható el.  

### <a name="existing-key-vault"></a>Meglévő kulcstartó

Egy ContosoVault nevű meglévő kulcstartó esetén engedélyezze a helyreállítható törlést az alábbiak szerint. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Új kulcstartó

Az új kulcstartó helyreállítható törlésének engedélyezése a létrehozás időpontjában történik, ha a létrehozási parancshoz hozzáadja a helyreállítható törlésengedélyező jelzőt.

```powershell
New-AzKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>A helyreállítható törlés engedélyezése

Annak ellenőrzéséhez, hogy a kulcstartóban engedélyezve van-e a hibamentes törlés, futtassa a *show* parancsot, és keresse meg a "Helyreállítható törlés engedélyezve?" parancsot. Attribútum:

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Védett kulcstartó törlése

A key vault törlésére vonatkozó parancs viselkedése megváltozik attól függően, hogy engedélyezve van-e a helyreállítható törlés.

> [!IMPORTANT]
>Ha a következő parancsot futtatja egy olyan key vaulthoz, amelyen nincs engedélyezve a helyreállítható törlés, véglegesen törli ezt a kulcstartót és annak minden tartalmát, és nincs lehetőség a helyreállításra!

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hogyan védi a helyreállítható törlés a kulcstartókat?

A törlés helyreállíthatóan engedélyezve:

- A törölt kulcstartó törlődik az erőforráscsoportból, és egy fenntartott névtérbe kerül, amely ahhoz a helyhez van társítva, ahol létrehozták. 
- A törölt objektumok, például a kulcsok, a titkos kulcsok és a tanúsítványok mindaddig elérhetetlenek, amíg azok tartalmazó kulcstartójuk törölt állapotban van. 
- A törölt kulcstartó DNS-neve le van foglalva, megakadályozva az azonos nevű új kulcstartó létrehozását.  

Az előfizetéshez társított törölt állapotkulcs-tárolókat a következő paranccsal tekintheti meg:

```powershell
Get-AzKeyVault -InRemovedState 
```

- *Az azonosító* segítségével azonosíthatja az erőforrást a helyreállítás vagy a tisztítás során. 
- *Az erőforrás-azonosító* a tároló eredeti erőforrásazonosítója. Mivel ez a key vault most törölt állapotban van, nincs erőforrás az adott erőforrás-azonosítóval. 
- *Ütemezett kiürítési dátum,* amikor a tároló véglegesen törlődik, ha nem történik művelet. Az *ütemezett kiürítési dátum*kiszámításához használt alapértelmezett megőrzési időszak 90 nap.

## <a name="recovering-a-key-vault"></a>Kulcstartó helyreállítása

Kulcstartó helyreállításához adja meg a key vault nevét, erőforráscsoport és helyét. Vegye figyelembe a helyet és az erőforráscsoportot a törölt kulcstartó, a helyreállítási folyamat hoz szükség van rájuk.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

A key vault helyreáll, egy új erőforrás jön létre a key vault eredeti erőforrás-azonosítójával. Ha az eredeti erőforráscsoportot eltávolítja, a helyreállítás megkísérlése előtt ugyanazzal a névvel kell létrehozni egyet.

## <a name="deleting-and-purging-key-vault-objects"></a>Kulcstartó-objektumok törlése és törlése

A következő parancs törli a "ContosoFirstKey" kulcsot a "ContosoVault" nevű key vaultban, amelynek helyreállítható törlése engedélyezve van:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Ha a kulcstartó engedélyezve van a helyreállítható törléshez, a törölt kulcs továbbra is törlésre kerül, kivéve, ha kifejezetten listázni szeretné a törölt kulcsokat. A törölt állapotban lévő kulcson végzett legtöbb művelet sikertelen lesz, kivéve a listalistázást, a helyreállítást, a törölt kulcs törlését. 

A következő parancs például a "ContosoVault" kulcstartóban lévő törölt kulcsokat sorolja fel:

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Átmeneti állapot 

Ha töröl egy kulcsot egy kulcstartóban, ha a hibakezelés engedélyezve van, az átmenet néhány másodpercig is eltarthat. Az átmenet során úgy tűnhet, hogy a kulcs nem aktív vagy törölt állapotban van. 

### <a name="using-soft-delete-with-key-vault-objects"></a>A törlés helyreállítható használata kulcstartó-objektumokkal

Csakúgy, mint a kulcstartók, a törölt kulcs, titkos kulcs vagy tanúsítvány, továbbra is törölt állapotban legfeljebb 90 napig, kivéve, ha helyreállítja, vagy kiüríti azt. 

#### <a name="keys"></a>Kulcsok

Helyreállítható kulcs helyreállítása:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

A helyreállítható kulcs végleges törlése (más néven tisztítás):

> [!IMPORTANT]
> A kulcs törlése véglegesen törli azt, és nem lesz visszanyerhető! 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

A **helyreállítási** és **kiürítési** műveletek saját engedélyekkel rendelkeznek egy key vault hozzáférési szabályzatban társítva. Ahhoz, hogy egy felhasználó vagy egyszerű szolgáltatás képes legyen végrehajtani a **helyreállítási** vagy **kiürítési** műveletet, rendelkeznie kell a megfelelő engedéllyel a kulcshoz vagy titkos kulcshoz. Alapértelmezés szerint **a kiürítés** nem kerül hozzáadásra a key vault hozzáférési szabályzatához, ha az "all" parancsikon az összes engedély megadására szolgál. Kifejezetten meg kell adnia a **kiürítési** engedélyt. 

#### <a name="set-a-key-vault-access-policy"></a>Kulcstartó-hozzáférési házirend beállítása

A következő user@contoso.com parancs több művelet használatát engedélyezi a *ContosoVault* kulcsain, beleértve a **kiürítést**is:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Ha rendelkezik egy meglévő key vault, amely nemrég volt a helyreállítható törlés engedélyezve van, előfordulhat, hogy nem rendelkezik **helyreállítási** és **kiürítési** engedélyekkel.

#### <a name="secrets"></a>Titkos kulcsok

A kulcsokhoz hasonlóan a titkos kulcsokat is saját parancsokkal kezelik:

- SQLPassword nevű titkos fájl törlése: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- A kulcstartóban lévő összes törölt titok listázása: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Titkos titok helyreállítása törölt állapotban: 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Titkos titok törlése törölt állapotban: 

  > [!IMPORTANT]
  > Egy titok törlése véglegesen törli, és nem lesz visszanyerhető!

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

#### <a name="certificates"></a>Tanúsítványok

A tanúsítványokat az alábbi parancsokkal kezelheti:

- SQLPassword nevű tanúsítvány törlése: 
  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

- A kulcstartóban lévő összes törölt tanúsítvány listázása: 
  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

- Tanúsítvány helyreállítása törölt állapotban: 
  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

- Tanúsítvány törlése törölt állapotban: 

  > [!IMPORTANT]
  > A tanúsítvány törlése véglegesen törli azt, és nem lesz helyreállítható!

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState 
  ```
  
## <a name="purging-a-soft-delete-protected-key-vault"></a>A védett kulcstartó helyreállító törlése

> [!IMPORTANT]
> A kulcstartó vagy annak egyik objektumának törlése véglegesen törli azt, ami azt jelenti, hogy nem lesz helyreállítható!

A kiürítési funkció egy olyan key vault-objektum vagy egy teljes kulcstartó végleges törlésére szolgál, amelyet korábban helyreállíthatóan töröltek. Az előző szakaszban bemutatottak szerint a kulcstartóban tárolt, a helyreállítható törlés funkcióval rendelkező objektumok több állapoton is átmehetnek:
- **Aktív**: törlés előtt.
- **Helyreállíthatóan törölt:** törlés után, amely felsorolható, és visszaállítható aktív állapotba.
- **Véglegesen törölt:** a kiürítés után nem állítható helyre.


Ugyanez igaz a kulcstartóra is. A helyreállítható kulcstartó és annak tartalmának végleges törléséhez meg kell tisztítania magát a key vaultot.

### <a name="purging-a-key-vault"></a>Kulcstartó kiürítése

A kulcstartó törlésekor a teljes tartalom véglegesen törlődik, beleértve a kulcsokat, titkos kulcsokat és tanúsítványokat. A helyreállíthatóan törölt kulcstartó `Remove-AzKeyVault` törléséhez használja `-InRemovedState` a parancsot a beállítással, és `-Location location` adja meg a törölt kulcstartó helyét az argumentummal. A törölt trezor helyét a `Get-AzKeyVault -InRemovedState`parancs segítségével találhatja meg.

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Szükséges engedélytisztítás
- Törölt kulcstartó törléséhez a felhasználónak RBAC-engedélyre van szüksége a *Microsoft.KeyVault/locations/deletedVaults/purge/action* művelethez. 
- A törölt kulcstartó listázásához a felhasználónak RBAC-engedélyre van szüksége a *Microsoft.KeyVault/deletedVaults/read* művelethez. 
- Alapértelmezés szerint csak az előfizetés rendszergazdája rendelkezik ezekkel az engedélyekkel. 

### <a name="scheduled-purge"></a>Ütemezett kiürítés

A törölt kulcstartó-objektumok listázása azt is megmutatja, hogy mikor rakják le őket a Key Vault. *Ütemezett kiürítési dátum* azt jelzi, hogy egy key vault-objektum véglegesen törlődik, ha nem történik művelet. Alapértelmezés szerint a törölt kulcstartó objektum megőrzési ideje 90 nap.

>[!IMPORTANT]
>A kiürítési tároló objektum, amelyet az *ütemezett kiürítési dátum* mező aktivál, véglegesen törlődik. Ez nem hasznosítható!

## <a name="enabling-purge-protection"></a>Kiürítési védelem engedélyezése

Ha a kiürítési védelem be van kapcsolva, a tároló vagy a törölt állapotban lévő objektum nem törölhető, amíg a 90 napos megőrzési idő le nem telt. Az ilyen trezor vagy objektum továbbra is vissza. Ez a funkció további biztosítékot nyújt arra, hogy egy tároló vagy egy objektum soha nem törölhető véglegesen, amíg a megőrzési időszak le nem telt.

Csak akkor engedélyezheti a törlési védelmet, ha a törlés is engedélyezve van. 

A dexs a dexs in trezor létrehozásakor a helyreállítható és a kiürítési védelem bekapcsolásához használja a [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0) parancsmac:

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Ha kiürítési védelmet szeretne hozzáadni egy meglévő tárolóhoz (amelyen már engedélyezve van a törlés), használja a [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0), [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0)és [Set-AzResource](/powershell/module/az.resources/set-azresource?view=azps-1.5.0) parancsmagokat:

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Egyéb erőforrások

- A Key Vault helyreállítható törlési funkciójának áttekintését az [Azure Key Vault helyreállítható törlés – áttekintéscímű témakörben találja.](overview-soft-delete.md)
- Az Azure Key Vault használatának általános áttekintését a Mi az Azure Key Vault? című [témakörben találja.](overview.md)
