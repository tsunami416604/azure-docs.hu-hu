---
title: Azure Key Vault – a Soft delete használata a CLI használatával
description: Példa a CLI-kódrészletek használatával történő törlésre példákkal
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 7288e5d8c01122bea7650274cdaf358c7fc24cd0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197317"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>A Key Vault Soft-delete használata a parancssori felülettel

Azure Key Vault Soft delete funkciója lehetővé teszi a törölt tárolók és tároló objektumok helyreállítását. A Soft-Delete a következő forgatókönyvekkel foglalkozik:

- Key Vault helyreállítható törlésének támogatása
- A Key Vault-objektumok helyreállítható törlésének támogatása; kulcsok, titkos kódok és tanúsítványok

## <a name="prerequisites"></a>Előfeltételek

- Azure CLI – ha nem rendelkezik ezzel a beállítással a környezetéhez, tekintse meg az [Key Vault kezelése az Azure CLI használatával](key-vault-manage-with-cli2.md)című témakört.

A CLI-vel kapcsolatos Key Vault-specifikus információk: [Azure cli Key Vault-referenciák](https://docs.microsoft.com/cli/azure/keyvault).

## <a name="required-permissions"></a>Szükséges engedélyek

A Key Vault műveleteket a szerepköralapú hozzáférés-vezérlési (RBAC) engedélyekkel külön kezelik a következők szerint:

| Művelet | Leírás | Felhasználói engedély |
|:--|:--|:--|
|Lista|Felsorolja a törölt kulcstartókat.|Microsoft.KeyVault/deletedVaults/read|
|Helyreállítás|Visszaállítja a törölt kulcstartót.|Microsoft.KeyVault/vaults/write|
|Véglegesen töröl|Véglegesen eltávolítja a törölt kulcstartót és annak teljes tartalmát.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Az engedélyekkel és a hozzáférés-vezérléssel kapcsolatos további információkért lásd a [kulcstartó biztonságossá](key-vault-secure-your-key-vault.md)tételét ismertető témakört.

## <a name="enabling-soft-delete"></a>Törlés engedélyezése

A "Soft-Delete" lehetővé teszi a törölt kulcstartó vagy a kulcstartóban tárolt objektumok helyreállítását.

> [!IMPORTANT]
> A "Soft Delete" engedélyezése a Key vaulton visszafordíthatatlan művelet. Ha a Soft-delete tulajdonság értéke "true" (igaz), akkor nem módosítható és nem távolítható el.  

### <a name="existing-key-vault"></a>Meglévő kulcstartó

Egy ContosoVault nevű meglévő kulcstartó esetén a következő módon engedélyezze a Soft delete törlését. 

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Új kulcstartó

Az új kulcstartó törlésének engedélyezése a létrehozáskor történik, ha hozzáadja a Soft-delete Enable jelzőt a Create parancshoz.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Helyreállítható törlési engedélyezés ellenőrzése

Annak ellenőrzéséhez, hogy a kulcstárolóban engedélyezve van-e a helyreállítható törlés, futtassa a *show* parancsot, és keresse meg a "Soft delete enabled?" lehetőséget. attribútum

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>A védett kulcstartó törlésének törlése

Az a parancs, amellyel törölheti a kulcstartót, attól függően, hogy engedélyezve van-e a helyreállítható törlés.

> [!IMPORTANT]
>Ha olyan kulcstartóhoz futtatja a következő parancsot, amely nem rendelkezik a helyreállítható törléssel, véglegesen törli ezt a kulcstartót és annak összes tartalmát a helyreállítás lehetőség nélkül.

```azurecli
az keyvault delete --name ContosoVault
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>A Soft-delete védi a kulcstartókat

A helyreállítható törlés engedélyezve:

- A rendszer eltávolítja a Deleted Key vaultot az erőforráscsoporthoz, és egy fenntartott névtérbe helyezi, amely ahhoz a helyhez van társítva, ahol létrehozták. 
- A törölt objektumok (például kulcsok, titkos kódok és tanúsítványok) nem érhetők el, amíg a kulcstárolót tartalmazó kulcstartó törölve állapotban van. 
- A törölt kulcstartó DNS-neve fenntartva, megakadályozva, hogy egy új kulcstároló ugyanazzal a névvel legyen létrehozva.  

Az előfizetéshez társított törölt állapotú kulcstartók a következő paranccsal tekinthetők meg:

```azurecli
az keyvault list-deleted
```
- Az *azonosító* használatával lehet azonosítani az erőforrást a helyreállítás vagy a kiürítés során. 
- Az *erőforrás-azonosító* a tár eredeti erőforrás-azonosítója. Mivel ez a kulcstartó már törölt állapotban van, nem létezik erőforrás az erőforrás-AZONOSÍTÓval. 
- Az *ütemezett kiürítési dátum* az, amikor a tároló véglegesen törölve lesz, ha nincs művelet. Az *ütemezett kiürítési dátum*kiszámításához használt alapértelmezett megőrzési időtartam 90 nap.

## <a name="recovering-a-key-vault"></a>Kulcstartó helyreállítása

Kulcstartó helyreállításához meg kell adnia a kulcstároló nevét, az erőforráscsoportot és a helyet. Jegyezze fel a törölt kulcstartó helyét és erőforrás-csoportját, ahogy a helyreállítási folyamathoz szükség van rájuk.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

Kulcstartó helyreállításakor a Key Vault eredeti erőforrás-azonosítójával létrejön egy új erőforrás. Ha az eredeti erőforráscsoport el lett távolítva, a helyreállítás megkísérlése előtt egyet kell létrehoznia ugyanazzal a névvel.

## <a name="deleting-and-purging-key-vault-objects"></a>Key Vault-objektumok törlése és törlése

A következő parancs törli a "ContosoFirstKey" kulcsot a (z) "ContosoVault" nevű kulcstartóban, amelynél engedélyezve van a helyreállítható törlés:

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Ha a kulcstartó engedélyezve van a helyreállítható törléshez, a törölt kulcsok továbbra is törlődnek, kivéve, ha explicit módon listáz vagy lekéri a törölt kulcsokat. A törölt állapotban lévő egyik kulcson végrehajtott műveletek többsége sikertelen lesz, kivéve a törölt kulcsok listázását, helyreállítását vagy törlését. 

Ha például egy kulcstartóban szeretné megkeresni a törölt kulcsok listáját, használja a következő parancsot:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Átmeneti állapot 

Ha töröl egy kulcsot egy kulcstartóban, és engedélyezve van a helyreállítható törlés, eltarthat néhány másodpercig, amíg az áttérés be nem fejeződik. A váltás során előfordulhat, hogy a kulcs nem aktív állapotban van, vagy a törölt állapotban van. 

### <a name="using-soft-delete-with-key-vault-objects"></a>A Soft delete használata a Key Vault-objektumokkal

Csakúgy, mint a Key vaultok, a törölt kulcsok, titkos kódok vagy tanúsítványok, akár 90 napig törölve maradnak, hacsak nem állítja helyre vagy nem törli azt.

#### <a name="keys"></a>Kulcsok

Helyreállítható kulcs helyreállítása:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Ha véglegesen törölni kívánja (más néven öblítést), a rendszer egy helyreállított kulcsot is töröl:

> [!IMPORTANT]
> A kulcsok végleges törlése véglegesen törli azt, és nem állítható vissza. 

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

A **helyreállítás** és **Törlés** műveletekhez saját engedélyek tartoznak a Key Vault hozzáférési házirendjében. Ahhoz, hogy egy felhasználó vagy egy egyszerű szolgáltatásnév végrehajtson egy **helyreállítási** vagy kitakarítási műveletet, rendelkeznie kell a megfelelő engedélyekkel az adott kulcshoz **vagy** titokhoz. Alapértelmezés szerint a **kiürítés** nincs hozzáadva a kulcstartó hozzáférési házirendjéhez, ha az összes engedély megadására az "all" (összes) parancsikont használja. Kifejezetten meg kell adnia a **kiürítési** engedélyt. 

#### <a name="set-a-key-vault-access-policy"></a>Key Vault hozzáférési szabályzatának beállítása

Az alábbi parancs user@contoso.com engedélyt biztosít a kulcsok *ContosoVault* való használatának több műveletéhez, beleértve a **kiürítést**is:

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Ha már van olyan kulcstartója, amelynél engedélyezve van a **helyreállított** törlés, akkor előfordulhat, hogy nem rendelkezik helyreállítási és **törlési** engedélyekkel.

#### <a name="secrets"></a>Titkos kulcsok

A kulcsokhoz hasonlóan a titkos kódok is a saját parancsaikkal kezelhetők:

- SQLPassword nevű titkos kód törlése: 
  ```azurecli
  az keyvault secret delete --vault-name ContosoVault -name SQLPassword
  ```

- A Key Vault összes törölt titkának listázása: 
  ```azurecli
  az keyvault secret list-deleted --vault-name ContosoVault
  ```

- Titkos kód helyreállítása törölt állapotban: 
  ```azurecli
  az keyvault secret recover --name SQLPassword --vault-name ContosoVault
  ```

- Titkos kód törlése törölt állapotban: 

  > [!IMPORTANT]
  > A titkos kód végleges törlésével véglegesen törlődik, és nem lesz helyreállítható. 

  ```azurecli
  az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
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

Ha töröl egy kulcstartót, a teljes tartalma véglegesen törlődik, beleértve a kulcsokat, a titkokat és a tanúsítványokat is. A helyreállítható kulcstartó törléséhez használja a `az keyvault purge` parancsot. Az előfizetése által törölt kulcstartók helye a `az keyvault list-deleted`parancs használatával található.

```azurecli
az keyvault purge --location westus --name ContosoVault
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

Ha a védelem kitisztítása be van kapcsolva, a tároló vagy a törölt állapotú objektum nem törölhető, amíg a 90 napos megőrzési idő el nem telt. Ilyen tár vagy objektum továbbra is helyreállítható. Ez a funkció biztosítja, hogy egy tár vagy egy objektum soha nem törölhető véglegesen, amíg a megőrzési időszak el nem telik.

Csak akkor engedélyezheti a kiürítést, ha a Soft-delete is engedélyezve van. 

Ha be szeretné kapcsolni a törlés és a kiürítés védelmét a tár létrehozásakor, használja az az kulcstartó [create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) parancsot:

```
az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
```

Ha a kiürítési védelmet egy meglévő tárolóhoz kívánja hozzáadni (amely már rendelkezik a helyreállított törlés engedélyezésével), használja az az kulcstartó [Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) parancsot:

```
az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
```

## <a name="other-resources"></a>Egyéb erőforrások

- A Key Vault-törlési funkció áttekintését lásd: Azure Key Vault- [Törlés – áttekintés](key-vault-ovw-soft-delete.md).
- A Azure Key Vault használatának általános áttekintését a [Mi az Azure Key Vault?](key-vault-overview.md)című témakörben tekintheti meg.

