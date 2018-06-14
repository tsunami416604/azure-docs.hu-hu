---
ms.assetid: ''
title: Az Azure Key Vault - CLI helyreállítható törlésre használata
description: CLI kódrészletek soft-törlés eset példái használata
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: alleonar
ms.openlocfilehash: a9b80cae69c4e5852341385b98fcccc86d7959e9
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
ms.locfileid: "27927974"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Key Vault soft-törlés használata parancssori felület

Az Azure Key Vault helyreállítható törlés funkció lehetővé teszi, hogy a törölt tárolók és a tároló objektumok. Pontosabban soft-törlés címeket a következő esetekben:

- Helyreállítható törlésre kerültek a kulcstároló támogatása
- A kulcstároló objektumok; helyreállítható törlésre támogatása a kulcsok, a titkos kulcsok, és tanúsítványokat

## <a name="prerequisites"></a>Előfeltételek

- Lásd az Azure CLI 2.0 – Ha a telepítő nem rendelkezik a környezetnek [kezelése Key Vault CLI 2.0 használatával](key-vault-manage-with-cli2.md).

Parancssori felület Key Vault referenciával információkért lásd: [Azure CLI 2.0 Key Vault hivatkozás](https://docs.microsoft.com/cli/azure/keyvault).

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

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Új kulcstartó

Egy új kulcstartó soft-törlésének engedélyezése a létrehozás időpontjában hozzáadásával történik a soft-Törlés engedélyezése jelzőt, amely a create parancs.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Ellenőrizze a soft-Törlés engedélyezése

Győződjön meg arról, hogy kulcstároló soft-törlés engedélyezve van-e, futtassa a *megjelenítése* parancsot, és keresse meg a "Soft törlése engedélyezve?" attribútum és a beállítása true vagy false.

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Védi a soft-törlés kulcstároló törlése

A parancs kulcstároló törlése (vagy eltávolításához) változatlan marad, de attól függően, hogy engedélyezte a soft-törlés vagy nem módosítja annak viselkedését.

```azurecli
az keyvault delete --name ContosoVault
```

> [!IMPORTANT]
>A kulcstároló, amely nem rendelkezik a soft-törlés engedélyezve van az előző parancs futtatása, ha véglegesen törli a kulcstartót és helyreállítási lehetőségeket nélkül minden tartalmát.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hogyan soft-törlés védje a kulcstárolók

Soft-Törlés engedélyezése:

- Kulcstároló törlése után van távolítva az erőforráscsoportot, és el egy fenntartott névtér, amely csak a hely, ahol létrehozták társított. 
- Az objektumok a törölt kulcsot használó, például a kulcs, a titkos kulcsok és tanúsítványok, nem érhető el, és maradnak, amíg azok tartalmazó kulcstároló a törölt állapotban van. 
- A DNS-nevét kulcstároló törölt állapotban továbbra is fenntartva, ezért nem hozható létre egy új kulcstartó azonos nevű.  

Törölt állapotban kulcstárolót, az Ön előfizetéséhez rendelve az alábbi parancs segítségével megtekintheti:

```azurecli
az keyvault list-deleted
```

A *erőforrás-azonosító* a kimenetben az eredeti erőforrás-azonosító az ehhez a tárolóhoz hivatkozik. Ezen a kulcstartón most már törölt állapotban van, mert nincs erőforrás létezik-e az adott erőforrás-azonosító. A *azonosító* mező helyreállítása, vagy végleges törlése az erőforrás azonosítására használható. A *kiürítése dátum ütemezett* mező azt jelzi, ha a tároló véglegesen törli (törölve), ha a törölt tároló történik semmi. Az alapértelmezett megőrzési időtartamot, kiszámításához használt a *kiürítése dátum ütemezett*, 90 nap.

## <a name="recovering-a-key-vault"></a>Kulcstároló helyreállítása

Kulcstároló helyreállításához, meg kell adnia a kulcstároló neve, erőforráscsoportot és helyet. Megjegyzés: a hely és az erőforráscsoport a törölt kulcstároló szükség van ezekre a kulcstartót a helyreállítási folyamatot.

```azurecli
az keyvault recover --location westus --name ContosoVault
```

Kulcstároló helyreállításakor eredménye egy új erőforrás a key vault eredeti erőforrás-azonosító. Az erőforráscsoport, ha már létező a key vault el lett távolítva, azonos nevű új erőforráscsoport előtt a key vault állíthatók helyre kell létrehozni.

## <a name="key-vault-objects-and-soft-delete"></a>Key Vault objektumok és a soft-törlés

A kulcs, "ContosoFirstKey", a kulcstároló neve "ContosoVault" a soft-törlés engedélyezve van, itt a hogyan akkor törlődik kulcs.

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

A kulcstartót helyreállítható törlésre engedélyezve van, a törölt kulcs azt továbbra is megjelenik, például törlődik, kivéve, ha explicit módon listájához vagy törölt kulcsok beolvasása. A műveleteik zömét a törölt állapotban egy kulcs egy törölt kulcs listázása, helyreállítását vagy végleges törlésére, kivéve sikertelen lesz. 

Például a kulcstároló törlése listában kulcsok kérelmezéséhez a következő paranccsal:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Közbenső műveletfázisa 

Egy kulcsot a kulcstároló törlése a soft-törlés engedélyezve van, az átmenet befejezéséhez néhány másodpercet vehet igénybe. A közbenső műveletfázisa során tűnhet, hogy a kulcs nem az aktív vagy törölt állapotban van. Ez a parancs felsorolja az összes törölt kulcsok "ContosoVault" nevű key vaultban lévő.

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="using-soft-delete-with-key-vault-objects"></a>Kulcstároló objektumok soft-törlés használata

Csak a például a kulcstárolók, a törölt kulcs titkos kulcs vagy tanúsítvány állapotban marad törölt 90 napig kivéve végezze el a helyreállítást, vagy törli azt. 

#### <a name="keys"></a>Kulcsok

A törölt kulcs helyreállítása:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Végleg törölni kívánja a kulcs:

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

>[!NOTE]
>A kulcs törlése véglegesen törli azt, ami azt jelenti, akkor nem fog helyreállítható.

A **helyreállítása** és **kiürítése** műveletek engedélye a saját tartozó a kulcstároló hozzáférési házirendben. Egy felhasználó vagy a szolgáltatás egyszerű tudják futtatni egy **helyreállítása** vagy **kiürítése** művelet a kulcstároló hozzáférési házirendben a megfelelő engedéllyel az adott objektum (kulcsok vagy titkos kulcsok) kell rendelkezniük. Alapértelmezés szerint a **kiürítése** engedély nem kerül a kulcstároló hozzáférési házirend, az "all" helyi használatakor a felhasználó az összes engedélyt. Kifejezetten biztosítania kell **kiürítése** engedéllyel. Például a következő parancsot a biztosít user@contoso.com engedéllyel a kulcsokat számos műveletek végrehajtásához *ContosoVault* beleértve **kiürítése**.

#### <a name="set-a-key-vault-access-policy"></a>A kulcstároló hozzáférési házirend beállítása

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Ha egy meglévő kulcstároló, amely még csak soft-törlés engedélyezve van, nem lehet **helyreállítása** és **kiürítése** engedélyek.

#### <a name="secrets"></a>Titkos kulcsok

Kulcsok, például kulcstároló titkos kulcsainak üzemelteti a saját parancsokkal. A következő, törlése, listázása, helyreállítása és titkos kulcsok kiürítése parancsok vannak.

- SQLPassword nevű titkos kulcs törlése: 
```azurecli
az keyvault secret delete --vault-name ContosoVault -name SQLPassword
```

- Kulcstároló törölt titkos kulcsainak listázása: 
```azurecli
az keyvault secret list-deleted --vault-name ContosoVault
```

- Helyreállítás törölt állapotban titkos kulcs: 
```azurecli
az keyvault secret recover --name SQLPassword --vault-name ContosoVault
```

- Törölt állapotban titkos kulcs törlése: 
```azurecli
az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
```

>[!NOTE]
>Titkos kulcs kiürítése véglegesen törli azt, ami azt jelenti, akkor nem fog helyreállítható.

## <a name="purging-and-key-vaults"></a>Tárolók kiürítési és kulcs

### <a name="key-vault-objects"></a>Kulcstároló-objektumok

A kulcs törlése, titkos kulcs vagy tanúsítvány véglegesen törli azt, ami azt jelenti, akkor nem fog helyreállítható. A kulcstároló, a törölt objektum találhatók azonban változatlan marad, a rendszer a key vaultban lévő összes más objektumra. 

### <a name="key-vaults-as-containers"></a>Kulcs-tárolók tárolóként
Ha a kulcstároló véglegesen törlődnek, és annak teljes tartalmát, beleértve a kulcsokat, a titkos kulcsok és a tanúsítványok, véglegesen törlődik. Kulcstároló törlése, használja a `az keyvault purge` parancsot. A helyen az előfizetés törölt kulcstárolójának a paranccsal is megtalálhatja `az keyvault list-deleted`.

```azurecli
az keyvault purge --location westus --name ContosoVault
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

