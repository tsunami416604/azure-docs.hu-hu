---
ms.assetid: ''
title: Az Azure Key Vault - helyreállítható törlés használata a parancssori felület
description: Kis példák a helyreállítható törlés használata a parancssori felület a használatukat bemutató kódrészletet
author: bryanla
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 10/15/2018
ms.author: bryanla
ms.openlocfilehash: af2d480e84ca69c0ecd795e38371375e6a71542b
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363639"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Key Vault helyreállítható törlés funkciójának használata parancssori felülettel

Az Azure Key Vault helyreállítható törlési funkció lehetővé teszi, hogy a törölt tárolók és a tároló objektumok. Pontosabban a helyreállítható törlés címeket a következő esetekben:

- A key vault helyreállítható törlés támogatása
- A key vault-objektumokon; helyreállítható törlés támogatása a kulcsok, titkos kódok, és tanúsítványok

## <a name="prerequisites"></a>Előfeltételek

- Az Azure CLI - Ha ez a környezet beállítása, lásd: nincs [kezelése a Key Vault Azure parancssori felületével](key-vault-manage-with-cli2.md).

A Key Vault műveletekre vonatkozó specifikus információkat CLI, lásd: [Azure parancssori felület a Key Vault referencia](https://docs.microsoft.com/cli/azure/keyvault).

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

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Új kulcstartó

Az új key vault helyreállítható Törlés engedélyezése létrehozáskor hozzáadásával történik, a helyreállítható törlés funkciójának engedélyezése jelzőt a create paranccsal.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Ellenőrizze a helyreállítható Törlés engedélyezése

Győződjön meg arról, hogy a key vault helyreállítható törlés engedélyezve van, futtassa a *megjelenítése* parancsot, és keresse meg a "helyreállítható törlés engedélyezve?" attribútum:

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Key vault helyreállítható törlés által védett törlése

A parancs törli a viselkedése attól függően, hogy a helyreállítható törlés funkciójának engedélyezve van-e a key vault változásai.

> [!IMPORTANT]
>Ha futtatja a következő parancsot a key vault helyreállítható Törlés engedélyezése nem rendelkező, azzal végleg törli ezt a kulcstartót és a tartalmára nincs helyreállítási lehetőségeket!

```azurecli
az keyvault delete --name ContosoVault
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hogyan védje a helyreállítható törlés a a kulcstartók

Helyreállítható törlési engedélyezve:

- Egy törölt kulcstartó távolítva az erőforráscsoport és a helyet, ahol létrehozták társított fenntartott névtérben helyezi. 
- Törölt objektumok, például a kulcsok, titkos kódok és tanúsítványok, elérhetetlenné válnak, amennyiben azok tartalmazó kulcstartó a törölt állapotban van. 
- A DNS-név, a törölt kulcstartó van fenntartva, meggátolja, hogy egy azonos nevű új kulcstartó létrehozása folyamatban.  

Előfordulhat, hogy megtekintheti az állapot törölt kulcstartók, az Ön előfizetéséhez rendelve az alábbi paranccsal:

```azurecli
az keyvault list-deleted
```
- *ID* helyreállításához, vagy végleges törlése az erőforrás azonosítására használható. 
- *Erőforrás-azonosító* ebben a tárban annak az eredeti erőforrás azonosítója. A kulcstartó most már törölt állapotban van, mivel nincs erőforrás létezik-e az adott erőforrás-azonosítója. 
- *Ütemezett dátum kiürítése* akkor, ha a tároló véglegesen törölve lesz, ha nem tesz. Az alapértelmezett megőrzési időtartamot, kiszámításához használt a *végleges törlés dátuma ütemezett*, 90 nap.

## <a name="recovering-a-key-vault"></a>Key vault helyreállítása

Key vault szeretné használni, adja meg a kulcstartó nevét, erőforráscsoportot és helyet. Vegye figyelembe a hely és az erőforráscsoport, a törölt key vault, a helyreállítási folyamat és igény szerint.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

Amikor a key vault helyreállítása után egy új erőforrás jön létre a key vault az eredeti erőforrás azonosítóval. Ha eltávolít az eredeti erőforráscsoportot, egy kell létrehozni ugyanazzal a névvel rendelkező helyreállítási megkísérlése előtt.

## <a name="key-vault-objects-and-soft-delete"></a>A Key Vault-objektumok és a helyreállítható törlés

Egy kulcs, a "ContosoFirstKey", a kulcstartó neve "ContosoVault" helyreállítható törlési engedélyezve van, itt a hogyan így törölni ezt a kulcsot.

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

A key vault helyreállítható törlés funkciójának engedélyezve az egy törölt továbbra is megjelenik a kulcs, akkor törlődik, kivéve, ha explicit módon listában vagy törölt kulcsok lekéréséhez. A törölt állapotban kulcs vonatkozó műveletek többségét kivéve egy törölt kulcs listázása, helyreállítását tisztázására vagy végleges törlésére, sikertelen lesz. 

Kérelem törölve lista kulcsok a key vaultban, például használja a következő parancsot:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Átmeneti állapot 

Ha töröl egy kulcsot a key vault helyreállítható törlési engedélyezve van, igénybe vehet néhány másodpercet, amíg az áttérés befejezéséhez. Ez a váltás során jelenhet meg, hogy a kulcs nem aktív állapota vagy a törölt állapotban. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Helyreállítható törlés használata a key vault-objektumokon

Kulcstartók, mint egy törölt kulcs, a titkos kulcsot vagy a tanúsítvány, állapotban marad a törölt legfeljebb 90 napig, kivéve, ha a helyreállítást, vagy végleges törlése.

#### <a name="keys"></a>Kulcsok

Helyreállíthatóan törölt kulcs helyreállítása:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Végleg törölni kívánja (más néven végleges törlése) a helyreállíthatóan törölt kulcs:

> [!IMPORTANT]
> Kulcs törlése véglegesen törli azt, és nem lesz helyreállítható. 

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

A **helyreállítása** és **kiürítése** műveletek rendelkezik saját a kulcstartó hozzáférési házirendben tartozó engedélyeket. Egy felhasználó vagy szolgáltatásnév lehessen végrehajtani egy **helyreállítása** vagy **kiürítése** műveletet, a kulcs vagy titkos kód a megfelelő engedéllyel kell rendelkezniük. Alapértelmezés szerint **kiürítése** nem a kulcstartó-hozzáférési házirend, amikor megjelenik az "all" helyi összes engedélyek megadására szolgál. Kifejezetten biztosítania kell **kiürítése** engedéllyel. 

#### <a name="set-a-key-vault-access-policy"></a>A kulcstartó-hozzáférési házirend beállítása

A következő parancsot a biztosít user@contoso.com használja a kulcsok több műveletre engedélyt *ContosoVault* beleértve **kiürítése**:

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Ha egy meglévő kulcstároló, amely még csak helyreállítható törlés engedélyezve van, előfordulhat, hogy nem rendelkezik **helyreállítása** és **kiürítése** engedélyeket.

#### <a name="secrets"></a>Titkos kulcsok

Például a kulcsok titkos kulcsok kezelt saját parancsokkal:

- SQLPassword nevű titkos kulcs törlése: 
```azurecli
az keyvault secret delete --vault-name ContosoVault -name SQLPassword
```

- Összes törölt key vault titkos kulcsainak listázása: 
```azurecli
az keyvault secret list-deleted --vault-name ContosoVault
```

- Helyreállítás törölt állapotban titkos kulcs: 
```azurecli
az keyvault secret recover --name SQLPassword --vault-name ContosoVault
```

- Törölt állapotban titkos kulcs törlése: 

  > [!IMPORTANT]
  > Titkos kulcs törlése véglegesen törli azt, és nem lesz helyreállítható. 

  ```azurecli
  az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
  ```

## <a name="purging-and-key-vaults"></a>Tárolók kiürítési és kulcs

### <a name="key-vault-objects"></a>Key vault-objektumokon

Végleges törlés kiürítése egy kulcsot, a titkos kulcsot vagy a tanúsítvány, okoz, és nem lesz helyreállítható. A key vaultban, amelyek a törölt objektum azonban érintetlen marad, a key vaultban lévő összes többi objektum lesz. 

### <a name="key-vaults-as-containers"></a>A kulcstartók tárolók
Amikor a key vault törölve van, a teljes tartalmát véglegesen törlődnek, beleértve a kulcsok, titkos kódok és tanúsítványok. Kulcstartó végleges törlése, használja a `az keyvault purge` parancsot. Annak a helyen az előfizetés törölt kulcstartók a paranccsal `az keyvault list-deleted`.

>[!IMPORTANT]
>Kulcstartó végleges törlése véglegesen törli azt, ami azt jelenti, nem lesz helyreállítható!

```azurecli
az keyvault purge --location westus --name ContosoVault
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

