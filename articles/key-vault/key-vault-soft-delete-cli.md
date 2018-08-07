---
ms.assetid: ''
title: Az Azure Key Vault - helyreállítható törlés használata a parancssori felület
description: Kis példák a helyreállítható törlés használata a parancssori felület a használatukat bemutató kódrészletet
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: alleonar
ms.openlocfilehash: b25d3d7bd5348d4e4ae5dc33362a9d0a2504236e
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578602"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Key Vault helyreállítható törlés funkciójának használata parancssori felülettel

Az Azure Key Vault helyreállítható törlési funkció lehetővé teszi, hogy a törölt tárolók és a tároló objektumok. Pontosabban a helyreállítható törlés címeket a következő esetekben:

- A key vault helyreállítható törlés támogatása
- A key vault-objektumokon; helyreállítható törlés támogatása a kulcsok, titkos kódok, és tanúsítványok

## <a name="prerequisites"></a>Előfeltételek

- Az Azure CLI 2.0 – Ha ezt a beállítást nincs a környezetben, lásd: [kezelése a Key Vault parancssori felület 2.0-val](key-vault-manage-with-cli2.md).

A Key Vault műveletekre vonatkozó specifikus információkat CLI, lásd: [Azure CLI 2.0-s Key Vault referencia](https://docs.microsoft.com/cli/azure/keyvault).

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

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Új kulcstartó

Az új key vault helyreállítható Törlés engedélyezése létrehozáskor hozzáadásával történik, a helyreállítható törlés funkciójának engedélyezése jelzőt a create paranccsal.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Ellenőrizze a helyreállítható Törlés engedélyezése

Győződjön meg arról, hogy a key vault helyreállítható törlés engedélyezve van, futtassa a *megjelenítése* parancsot, és keresse meg a "helyreállítható törlés engedélyezve?" attribútum és a beállítása true vagy FALSE (hamis).

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Key vault helyreállítható törlés által védett törlése

A parancs törlése (vagy eltávolítása) key vault változatlan marad, de attól függően, hogy engedélyezte a helyreállítható törlés vagy nem módosítja annak viselkedését.

```azurecli
az keyvault delete --name ContosoVault
```

> [!IMPORTANT]
>A key vault helyreállítható Törlés engedélyezése nem rendelkező az előző parancs futtatásakor, azzal végleg törli a kulcstartó és a helyreállítási beállítások nélkül annak tartalmát.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hogyan védje a helyreállítható törlés a a kulcstartók

Helyreállítható törlési engedélyezve:

- Key vault törlésekor az erőforráscsoportból eltávolítása és elhelyezni egy fenntartott névtér, amely csak a hely, ahol létrehozták társított. 
- Egy törölt kulcsban található objektumok tárolót, például a kulcsok, titkos kódok és tanúsítványok, elérhetetlenné válnak, és így marad, amíg azok tartalmazó kulcstartó a törölt állapotban van. 
- A DNS-név kulcstartó törölt állapotban továbbra is fenntartva, ezért nem hozhatók létre egy új kulcstartóba, azonos nevű.  

Előfordulhat, hogy megtekintheti az állapot törölt kulcstartók, az Ön előfizetéséhez rendelve az alábbi paranccsal:

```azurecli
az keyvault list-deleted
```

A *erőforrás-azonosító* a kimenetben a tár az eredeti erőforrás-Azonosítójára hivatkozik. A kulcstartó most már törölt állapotban van, mivel nincs erőforrás létezik-e az adott erőforrás-azonosítója. A *azonosító* mező segítségével azonosíthatja az erőforrás helyreállításához, vagy törlése. A *végleges törlés dátuma ütemezett* mező jelzi, ha a tároló véglegesen törölve lesz (törölve) Ha nem tesz a törölt tároló. Az alapértelmezett megőrzési időtartamot, kiszámításához használt a *végleges törlés dátuma ütemezett*, 90 nap.

## <a name="recovering-a-key-vault"></a>Key vault helyreállítása

Key vault szeretné használni, adja meg a kulcstartó nevét, erőforráscsoportot és helyet kell. Megjegyzés: a hely és az erőforráscsoport, a törölt kulcstartó szükség van ezekre a key vault a helyreállítási folyamatot.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

Amikor a key vault helyreállítása után jön egy új erőforrást a key vault eredeti erőforrás-azonosítója. Az erőforráscsoport, ahol a key vault létezett el lett távolítva, ugyanilyen nevű új erőforráscsoportot, amelybe a key vault helyreállítható előtt kell létrehozni.

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

Ha töröl egy kulcsot a key vault helyreállítható törlési engedélyezve van, igénybe vehet néhány másodpercet, amíg az áttérés befejezéséhez. Ebben az átmeneti állapotban tűnhet, hogy a kulcs nem szerepel az aktív állapot vagy a törölt állapotból. Ez a parancs listázza az összes törölt kulcsok tárol a kulcstárolóban "ContosoVault" nevű.

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="using-soft-delete-with-key-vault-objects"></a>Helyreállítható törlés használata a key vault-objektumokon

Hasonlóan a kulcstartók, egy törölt kulcs titkos kulcsot, vagy tanúsítvány állapotban marad törölt legfeljebb 90 napig, kivéve ha a helyreállítást, vagy végleges törlése. 

#### <a name="keys"></a>Kulcsok

Helyreállítani egy törölt kulcs:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Véglegesen törli a kulcs:

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

>[!NOTE]
>Kulcs törlése véglegesen törli azt, ami azt jelenti, nem lesz helyreállítható.

A **helyreállítása** és **kiürítése** műveletek rendelkezik saját a kulcstartó hozzáférési házirendben tartozó engedélyeket. Egy felhasználó vagy szolgáltatásnév lehessen végrehajtani egy **helyreállítása** vagy **kiürítése** művelet (kulcs vagy titkos kód) objektum a megfelelő engedéllyel kell rendelkezniük a kulcstartó-hozzáférési házirendben. Alapértelmezés szerint a **kiürítése** engedély nem kerül be a kulcstartó-hozzáférési házirend, ha az "all" helyi minden engedélyt biztosíthat a felhasználók. Kifejezetten biztosítania kell **kiürítése** engedéllyel. Az alábbi parancs például biztosít user@contoso.com több műveletek végrehajtása a kulcsokat az engedély *ContosoVault* többek között **kiürítése**.

#### <a name="set-a-key-vault-access-policy"></a>A kulcstartó-hozzáférési házirend beállítása

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Ha egy meglévő kulcstároló, amely még csak helyreállítható törlés engedélyezve van, előfordulhat, hogy nem rendelkezik **helyreállítása** és **kiürítése** engedélyeket.

#### <a name="secrets"></a>Titkos kulcsok

Például a kulcsok titkos kulcsok a key vault üzemeltetnek a saját parancsokkal. A következő, rendszer törlése, listázása, visszaállítása és titkos kódok törlése parancsokat.

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
```azurecli
az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
```

>[!NOTE]
>Titkos kulcs törlése véglegesen törli azt, ami azt jelenti, nem lesz helyreállítható.

## <a name="purging-and-key-vaults"></a>Tárolók kiürítési és kulcs

### <a name="key-vault-objects"></a>Key vault-objektumokon

Kulcs törlése, titkos kulcs, vagy tanúsítvány véglegesen törölni fogja, ami azt jelenti, nem lesz helyreállítható. A key vaultban, amelyek a törölt objektum azonban érintetlen marad, a key vaultban lévő összes többi objektum lesz. 

### <a name="key-vaults-as-containers"></a>A kulcstartók tárolók
Amikor a key vault törölve van, és annak teljes tartalmát, beleértve a kulcsok, titkos kódok és tanúsítványok, véglegesen törlődnek. Kulcstartó végleges törlése, használja a `az keyvault purge` parancsot. Annak a helyen az előfizetés törölt kulcstartók a paranccsal `az keyvault list-deleted`.

```azurecli
az keyvault purge --location westus --name ContosoVault
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

