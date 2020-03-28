---
title: Azure Key Vault - A lágy törlés használata a CLI-vel
description: Példaként használhat a cli-kódmetszetekkel történő ideiglenes törlésre
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 940de5e100da934e0bc4efdfc6686f8040e10954
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79457321"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>A Key Vault helyreállítható törlés funkciójának használata parancssori felülettel

Az Azure Key Vault helyreállítható törlési funkciója lehetővé teszi a törölt tárolók és a tárolóobjektumok helyreállítását. Pontosabban, a soft-delete címek a következő forgatókönyvek:

- A kulcstartó helyreállítható törlésének támogatása
- A kulcstartó-objektumok helyreállítható törlésének támogatása; kulcsok, titkos kulcsok és tanúsítványok

## <a name="prerequisites"></a>Előfeltételek

- Azure CLI – Ha nem rendelkezik ezzel a beállítással a környezetéhez, olvassa el a Key Vault kezelése az Azure CLI használatával című [témakört.](key-vault-manage-with-cli2.md)

A Key Vault-specifikus referenciainformációk a CLI,Azure [CLI Key Vault referencia.](https://docs.microsoft.com/cli/azure/keyvault)

## <a name="required-permissions"></a>Szükséges engedélyek

A Key Vault-műveleteket külön kezeli a szerepköralapú hozzáférés-vezérlési (RBAC) engedélyek az alábbiak szerint:

| Művelet | Leírás | Felhasználói engedély |
|:--|:--|:--|
|Lista|A törölt kulcstartók listája.|Microsoft.KeyVault/deletedVaults/read|
|Helyreállítás|Visszaállít egy törölt kulcstartót.|Microsoft.KeyVault/vaults/write|
|Véglegesen töröl|Véglegesen eltávolítja a törölt kulcstartót és annak teljes tartalmát.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Az engedélyekről és a hozzáférés-vezérlésről a [Kulcstartó védelme](key-vault-secure-your-key-vault.md)című témakörben talál további információt.

## <a name="enabling-soft-delete"></a>Helyreállítható törlés engedélyezése

A "helyreállítva törlést" engedélyezi a törölt kulcstartó vagy a key vaultban tárolt objektumok helyreállításához.

> [!IMPORTANT]
> A "helyreállítható törlés" engedélyezése a key vaultban visszafordíthatatlan művelet. Ha a helyreállítható törlés tulajdonság értéke "true", nem módosítható vagy távolítható el.  

### <a name="existing-key-vault"></a>Meglévő kulcstartó

Egy ContosoVault nevű meglévő kulcstartó esetén engedélyezze a helyreállítható törlést az alábbiak szerint. 

```azurecli
az keyvault update -n ContosoVault --enable-soft-delete true
```

### <a name="new-key-vault"></a>Új kulcstartó

Az új kulcstartó helyreállítható törlésének engedélyezése a létrehozás időpontjában történik, ha a létrehozási parancshoz hozzáadja a helyreállítható törlésengedélyező jelzőt.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>A helyreállítható törlés engedélyezése

Annak ellenőrzéséhez, hogy a kulcstartóban engedélyezve van-e a hibamentes törlés, futtassa a *show* parancsot, és keresse meg a "Helyreállítható törlés engedélyezve?" parancsot. Attribútum:

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Védett kulcstartó törlése

A key vault törlésére vonatkozó parancs viselkedése megváltozik attól függően, hogy engedélyezve van-e a helyreállítható törlés.

> [!IMPORTANT]
>Ha a következő parancsot futtatja egy olyan key vaulthoz, amelyen nincs engedélyezve a helyreállítható törlés, véglegesen törli ezt a kulcstartót és annak minden tartalmát, és nincs lehetőség a helyreállításra!

```azurecli
az keyvault delete --name ContosoVault
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hogyan védi a helyreállítható törlés a kulcstartókat?

A törlés helyreállíthatóan engedélyezve:

- A törölt kulcstartó törlődik az erőforráscsoportból, és egy fenntartott névtérbe kerül, amely ahhoz a helyhez van társítva, ahol létrehozták. 
- A törölt objektumok, például a kulcsok, a titkos kulcsok és a tanúsítványok mindaddig elérhetetlenek, amíg azok tartalmazó kulcstartójuk törölt állapotban van. 
- A törölt kulcstartó DNS-neve le van foglalva, megakadályozva az azonos nevű új kulcstartó létrehozását.  

Az előfizetéshez társított törölt állapotkulcs-tárolókat a következő paranccsal tekintheti meg:

```azurecli
az keyvault list-deleted
```
- *Az azonosító* segítségével azonosíthatja az erőforrást a helyreállítás vagy a tisztítás során. 
- *Az erőforrás-azonosító* a tároló eredeti erőforrásazonosítója. Mivel ez a key vault most törölt állapotban van, nincs erőforrás az adott erőforrás-azonosítóval. 
- *Ütemezett kiürítési dátum,* amikor a tároló véglegesen törlődik, ha nem történik művelet. Az *ütemezett kiürítési dátum*kiszámításához használt alapértelmezett megőrzési időszak 90 nap.

## <a name="recovering-a-key-vault"></a>Kulcstartó helyreállítása

Kulcstartó helyreállításához adja meg a key vault nevét, erőforráscsoport és helyét. Vegye figyelembe a helyet és az erőforráscsoportot a törölt kulcstartó, a helyreállítási folyamat hoz szükség van rájuk.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

A key vault helyreáll, egy új erőforrás jön létre a key vault eredeti erőforrás-azonosítójával. Ha az eredeti erőforráscsoportot eltávolítja, a helyreállítás megkísérlése előtt ugyanazzal a névvel kell létrehozni egyet.

## <a name="deleting-and-purging-key-vault-objects"></a>Kulcstartó-objektumok törlése és törlése

A következő parancs törli a "ContosoFirstKey" kulcsot a "ContosoVault" nevű key vaultban, amelynek helyreállítható törlése engedélyezve van:

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Ha a kulcstartó engedélyezve van a helyreállítható törléshez, a törölt kulcs továbbra is úgy jelenik meg, mintha törölve lenne, kivéve, ha explicit módon listázvagy lekéri a törölt kulcsokat. A törölt állapotban lévő kulcson végzett legtöbb művelet sikertelen lesz, kivéve a törölt kulcs listázását, helyreállítását vagy tisztítását. 

Ha például a törölt kulcsok listázását szeretné kérni egy key vaultban, használja a következő parancsot:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Átmeneti állapot 

Ha töröl egy kulcsot egy kulcstartóban, ha a hibakezelés engedélyezve van, az átmenet néhány másodpercig is eltarthat. Az átmenet során előfordulhat, hogy a kulcs nem aktív vagy törölt állapotban van. 

### <a name="using-soft-delete-with-key-vault-objects"></a>A törlés helyreállítható használata kulcstartó-objektumokkal

Csakúgy, mint a kulcstartók, a törölt kulcs, titkos kulcs vagy tanúsítvány, továbbra is törölt állapotban legfeljebb 90 napig, kivéve, ha helyreállítja, vagy kiüríti azt.

#### <a name="keys"></a>Kulcsok

Helyreállítható kulcs helyreállítása:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

A helyreállítható kulcs végleges törlése (más néven tisztítás):

> [!IMPORTANT]
> A kulcs törlése véglegesen törli azt, és nem lesz visszanyerhető! 

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

A **helyreállítási** és **kiürítési** műveletek saját engedélyekkel rendelkeznek egy key vault hozzáférési szabályzatban társítva. Ahhoz, hogy egy felhasználó vagy egyszerű szolgáltatás képes legyen végrehajtani a **helyreállítási** vagy **kiürítési** műveletet, rendelkeznie kell a megfelelő engedéllyel a kulcshoz vagy titkos kulcshoz. Alapértelmezés szerint **a kiürítés** nem kerül hozzáadásra a key vault hozzáférési szabályzatához, ha az "all" parancsikon az összes engedély megadására szolgál. Kifejezetten meg kell adnia a **kiürítési** engedélyt. 

#### <a name="set-a-key-vault-access-policy"></a>Kulcstartó-hozzáférési házirend beállítása

A következő user@contoso.com parancs több művelet használatát engedélyezi a *ContosoVault* kulcsain, beleértve a **kiürítést**is:

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Ha rendelkezik egy meglévő key vault, amely nemrég volt a helyreállítható törlés engedélyezve van, előfordulhat, hogy nem rendelkezik **helyreállítási** és **kiürítési** engedélyekkel.

#### <a name="secrets"></a>Titkos kulcsok

A kulcsokhoz hasonlóan a titkos kulcsokat is saját parancsokkal kezelik:

- SQLPassword nevű titkos fájl törlése: 
  ```azurecli
  az keyvault secret delete --vault-name ContosoVault -name SQLPassword
  ```

- A kulcstartóban lévő összes törölt titok listázása: 
  ```azurecli
  az keyvault secret list-deleted --vault-name ContosoVault
  ```

- Titkos titok helyreállítása törölt állapotban: 
  ```azurecli
  az keyvault secret recover --name SQLPassword --vault-name ContosoVault
  ```

- Titkos titok törlése törölt állapotban: 

  > [!IMPORTANT]
  > Egy titok törlése véglegesen törli, és nem lesz visszanyerhető! 

  ```azurecli
  az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
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

A kulcstartó törlésekor a teljes tartalom véglegesen törlődik, beleértve a kulcsokat, titkos kulcsokat és tanúsítványokat. A helyreállítható kulcstartó kiürítéséhez `az keyvault purge` használja a parancsot. A parancs `az keyvault list-deleted`segítségével megtalálhatja az előfizetés törölt kulcstartóinak helyét.

```azurecli
az keyvault purge --location westus --name ContosoVault
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

A tároló létrehozásakor a helyreállítható törlést és a védelem kiürítését is be kell kapcsolnia, használja az [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) parancsot:

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
```

Ha kiürítési védelmet szeretne hozzáadni egy meglévő tárolóhoz (amelyen már engedélyezve van a törlés funkciója), használja az [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) parancsot:

```azurecli
az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
```

## <a name="other-resources"></a>Egyéb erőforrások

- A Key Vault helyreállítható törlési funkciójának áttekintését az [Azure Key Vault helyreállítható törlés – áttekintés című témakörben találja.](key-vault-ovw-soft-delete.md)
- Az Azure Key Vault használatának általános áttekintését a [Mi az Azure Key Vault?](key-vault-overview.md)

