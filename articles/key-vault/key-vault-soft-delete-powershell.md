---
ms.assetid: ''
title: Az Azure Key Vault - soft-törlés használata a PowerShell használatával
description: PowerShell kódrészletek soft-törlés eset példái használata
services: key-vault
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/21/2017
ms.author: alleonar
ms.openlocfilehash: 48569e31e6400e3ec8958e0bceda1fd3b72207ea
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
ms.locfileid: "27927991"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Key Vault soft-törlés használata a PowerShell használatával

Az Azure Key Vault helyreállítható törlés funkció lehetővé teszi, hogy a törölt tárolók és a tároló objektumok. Pontosabban soft-törlés címeket a következő esetekben:

- Helyreállítható törlésre kerültek a kulcstároló támogatása
- A kulcstároló objektumok; helyreállítható törlésre támogatása a kulcsok, a titkos kulcsok, és tanúsítványokat

## <a name="prerequisites"></a>Előfeltételek

- Az Azure PowerShell 4.0.0 vagy újabb – Ha nem rendelkezik ilyen már telepítő, Azure PowerShell telepítése, és társítsa azt az Azure-előfizetése, lásd: [telepítése és konfigurálása az Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> A Key Vault PowerShell kimeneti formázás elavult verziójára fájlba van **előfordulhat, hogy** kell betölteni a környezet helyett a helyes verzióra. Azt vannak előrejelző magában foglalja a kimeneti formázásához szükséges javítása PowerShell frissített verziója, és ekkor frissíti az ebben a témakörben. A jelenlegi megoldást kell tapasztal formázási probléma van:
> - A következő lekérdezés nem is lát a soft-törlés észlel a jelen témakörben ismertetett tulajdonság engedélyezve: `$vault = Get-AzureRmKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


PowerShell Key Vault adott refernece információkért lásd: [Azure Key Vault PowerShell-referencia](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0).

## <a name="required-permissions"></a>Szükséges engedélyek

Key Vault műveleteket külön-külön az alábbiak szerint felügyelt keresztül szerepköralapú hozzáférés-vezérlést (RBAC) engedélyekkel:

| Művelet | Leírás | Felhasználói engedélyt |
|:--|:--|:--|
|Lista|Listák kulcstároló törlése.|Microsoft.KeyVault/deletedVaults/read|
|Helyreállítás|Visszaállítja a törölt kulcstároló.|Microsoft.KeyVault/vaults/write|
|Véglegesen töröl|Végleg törli a törölt kulcstároló és annak teljes tartalmát.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Hozzáférés-vezérlés az engedélyekkel kapcsolatos további információkért lásd: [a kulcstartót biztonságos](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Soft-Törlés engedélyezése

Helyre szeretné állítani a törölt kulcstároló vagy a key vaultban tárolt objektumok, először engedélyeznie kell, hogy kulcstároló soft-törlésének.

### <a name="existing-key-vault"></a>Meglévő kulcstároló

Egy meglévő kulcstároló nevű ContosoVault engedélyezze a soft-Törlés az alábbiak szerint. 

>[!NOTE]
>Jelenleg kell használnia az Azure Resource Manager erőforrás-kezelést közvetlenül írni a *enableSoftDelete* a Key Vault erőforrás-tulajdonság.

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzureRmResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Új kulcstartó

Egy új kulcstartó soft-törlésének engedélyezése a létrehozás időpontjában hozzáadásával történik a soft-Törlés engedélyezése jelzőt, amely a create parancs.

```powershell
New-AzureRmKeyVault -VaultName "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Ellenőrizze a soft-Törlés engedélyezése

Győződjön meg arról, hogy kulcstároló soft-törlés engedélyezve van-e, futtassa a *beolvasása* parancsot, és keresse meg a "Soft törlése engedélyezve?" attribútum és a beállítása true vagy false.

```powershell
Get-AzureRmKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Védi a soft-törlés kulcstároló törlése

A parancs kulcstároló törlése (vagy eltávolításához) változatlan marad, de attól függően, hogy engedélyezte a soft-törlés vagy nem módosítja annak viselkedését.

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoVault'
```

> [!IMPORTANT]
>A kulcstároló, amely nem rendelkezik a soft-törlés engedélyezve van az előző parancs futtatása, ha véglegesen törli a kulcstartót és helyreállítási lehetőségeket nélkül minden tartalmát.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hogyan soft-törlés védje a kulcstárolók

Soft-Törlés engedélyezése:

- Kulcstároló törlése után van távolítva az erőforráscsoportot, és el egy fenntartott névtér, amely csak a hely, ahol létrehozták társított. 
- Az objektumok a törölt kulcsot használó, például a kulcs, a titkos kulcsok és tanúsítványok, nem érhető el, és maradnak, amíg azok tartalmazó kulcstároló a törölt állapotban van. 
- A DNS-nevét kulcstároló törölt állapotban továbbra is fenntartva, ezért nem hozható létre egy új kulcstartó azonos nevű.  

Törölt állapotban kulcstárolót, az Ön előfizetéséhez rendelve az alábbi parancs segítségével megtekintheti:

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

A *erőforrás-azonosító* a kimenetben az eredeti erőforrás-azonosító az ehhez a tárolóhoz hivatkozik. Ezen a kulcstartón most már törölt állapotban van, mert nincs erőforrás létezik-e az adott erőforrás-azonosító. A *azonosító* mező helyreállítása, vagy végleges törlése az erőforrás azonosítására használható. A *kiürítése dátum ütemezett* mező azt jelzi, ha a tároló véglegesen törli (törölve), ha a törölt tároló történik semmi. Az alapértelmezett megőrzési időtartamot, kiszámításához használt a *kiürítése dátum ütemezett*, 90 nap.

## <a name="recovering-a-key-vault"></a>Kulcstároló helyreállítása

Kulcstároló helyreállításához, meg kell adnia a kulcstároló neve, erőforráscsoportot és helyet. Megjegyzés: a hely és az erőforráscsoport a törölt kulcstároló szükség van ezekre a kulcstartót a helyreállítási folyamatot.

```powershell
Undo-AzureRmKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Kulcstároló helyreállításakor eredménye egy új erőforrás a key vault eredeti erőforrás-azonosító. Az erőforráscsoport, ha már létező a key vault el lett távolítva, azonos nevű új erőforráscsoport előtt a key vault állíthatók helyre kell létrehozni.

## <a name="key-vault-objects-and-soft-delete"></a>Key Vault objektumok és a soft-törlés

A kulcs, "ContosoFirstKey", a kulcstároló neve "ContosoVault" a soft-törlés engedélyezve van, itt a hogyan akkor törlődik kulcs.

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

A kulcstartót helyreállítható törlésre engedélyezve van, a törölt kulcs azt továbbra is megjelenik, például törlődik, kivéve, ha explicit módon listájához vagy törölt kulcsok beolvasása. A műveleteik zömét a törölt állapotban egy kulcs egy törölt kulcs listázása, helyreállítását vagy végleges törlésére, kivéve sikertelen lesz. 

Például a kulcstároló törlése listában kulcsok kérelmezéséhez a következő paranccsal:

```powershell
Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Közbenső műveletfázisa 

Egy kulcsot a kulcstároló törlése a soft-törlés engedélyezve van, az átmenet befejezéséhez néhány másodpercet vehet igénybe. A közbenső műveletfázisa során tűnhet, hogy a kulcs nem az aktív vagy törölt állapotban van. Ez a parancs felsorolja az összes törölt kulcsok "ContosoVault" nevű key vaultban lévő.

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

### <a name="using-soft-delete-with-key-vault-objects"></a>Kulcstároló objektumok soft-törlés használata

Csak a például a kulcstárolók, a törölt kulcs titkos kulcs vagy tanúsítvány állapotban marad törölt 90 napig kivéve végezze el a helyreállítást, vagy törli azt. 

#### <a name="keys"></a>Kulcsok

A törölt kulcs helyreállítása:

```powershell
Undo-AzureKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Végleg törölni kívánja a kulcs:

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

>[!NOTE]
>A kulcs törlése véglegesen törli azt, ami azt jelenti, akkor nem fog helyreállítható.

A **helyreállítása** és **kiürítése** műveletek engedélye a saját tartozó a kulcstároló hozzáférési házirendben. Egy felhasználó vagy a szolgáltatás egyszerű tudják futtatni egy **helyreállítása** vagy **kiürítése** művelet a kulcstároló hozzáférési házirendben a megfelelő engedéllyel az adott objektum (kulcsok vagy titkos kulcsok) kell rendelkezniük. Alapértelmezés szerint a **kiürítése** engedély nem kerül a kulcstároló hozzáférési házirend, az "all" helyi használatakor a felhasználó az összes engedélyt. Kifejezetten biztosítania kell **kiürítése** engedéllyel. Például a következő parancsot a biztosít user@contoso.com engedéllyel a kulcsokat számos műveletek végrehajtásához *ContosoVault* beleértve **kiürítése**.

#### <a name="set-a-key-vault-access-policy"></a>A kulcstároló hozzáférési házirend beállítása

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Ha egy meglévő kulcstároló, amely még csak soft-törlés engedélyezve van, nem lehet **helyreállítása** és **kiürítése** engedélyek.

#### <a name="secrets"></a>Titkos kulcsok

Kulcsok, például kulcstároló titkos kulcsainak üzemelteti a saját parancsokkal. A következő, törlése, listázása, helyreállítása és titkos kulcsok kiürítése parancsok vannak.

- SQLPassword nevű titkos kulcs törlése: 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -name SQLPassword
```

- Kulcstároló törölt titkos kulcsainak listázása: 
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
>Titkos kulcs kiürítése véglegesen törli azt, ami azt jelenti, akkor nem fog helyreállítható.

## <a name="purging-and-key-vaults"></a>Tárolók kiürítési és kulcs

### <a name="key-vault-objects"></a>Kulcstároló-objektumok

A kulcs törlése, titkos kulcs vagy tanúsítvány véglegesen törli azt, ami azt jelenti, akkor nem fog helyreállítható. A kulcstároló, a törölt objektum találhatók azonban változatlan marad, a rendszer a key vaultban lévő összes más objektumra. 

### <a name="key-vaults-as-containers"></a>Kulcs-tárolók tárolóként
Ha a kulcstároló véglegesen törlődnek, és annak teljes tartalmát, beleértve a kulcsokat, a titkos kulcsok és a tanúsítványok, véglegesen törlődik. Kulcstároló törlése, használja a `Remove-AzureRmKeyVault` beállítás parancsot `-InRemovedState` és a törölt kulcstároló helye megadásával a `-Location location` argumentum. A paranccsal egy törölt tároló helyét található `Get-AzureRmKeyVault -InRemovedState`.

```powershell
Remove-AzureRmKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

>[!NOTE]
>Kulcstároló törlése véglegesen törli azt, ami azt jelenti, akkor nem fog helyreállítható.

### <a name="purge-permissions-required"></a>Törli a szükséges engedélyek
- Törli a törölt kulcstároló, úgy, hogy véglegesen törli a a tárolóhoz és annak teljes tartalmát, a felhasználónak a végrehajtásához az RBAC-engedély van szüksége egy *Microsoft.KeyVault/locations/deletedVaults/purge/action* műveletet. 
- A törölt kulcs listázásához, a tárolót a felhasználó számára szükséges végrehajtásához az RBAC-engedély *Microsoft.KeyVault/deletedVaults/read* engedéllyel. 
- Alapértelmezés szerint csak egy előfizetés rendszergazdája rendelkezik ezekkel a jogosultságokkal. 

### <a name="scheduled-purge"></a>Ütemezett kiürítése

A törölt kulcstároló objektumok listázása látható, ha azok schedled Key Vault kell kiüríti. A *kiürítése dátum ütemezett* mező jelzi az amikor egy kulcstartót objektum véglegesen törölve lesz, ha nem tesz. Alapértelmezés szerint a törölt kulcstároló objektum megőrzési időtartama 90 nap.

>[!NOTE]
>Egy tároló kiürítve objektum által indított annak *kiürítése dátum ütemezett* mezőben, az véglegesen törlődni fog. Nincs helyreállítható.

## <a name="other-resources"></a>Egyéb erőforrások

- Key Vault soft-törlés szolgáltatás áttekintését lásd: [Azure Key Vault soft-törlés – áttekintés](key-vault-ovw-soft-delete.md).
- Az Azure Key Vault használatának általános áttekintést, lásd: [Ismerkedés az Azure Key Vault](key-vault-get-started.md).

