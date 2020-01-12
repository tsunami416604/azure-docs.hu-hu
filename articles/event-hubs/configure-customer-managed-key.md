---
title: Saját kulcs konfigurálása az Azure-Event Hubs inaktív adatok titkosításához
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja saját kulcsát az Azure Event Hubs-adatok titkosításához.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: 50d12a0aba9018b1ecb30c018249e8f94ebe6d95
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903291"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Ügyfél által felügyelt kulcsok konfigurálása az Azure Event Hubs-adatok inaktív titkosításához a Azure Portal használatával
Az Azure Event Hubs az Azure Storage Service Encryption (Azure SSE) segítségével titkosítja az inaktív adatok titkosítását. Event Hubs az Azure Storage-ra támaszkodik az adattárolásra, és alapértelmezés szerint az Azure Storage-ban tárolt összes adattal titkosították a Microsoft által felügyelt kulcsokkal. 

## <a name="overview"></a>Áttekintés
Az Azure Event Hubs mostantól támogatja az inaktív adatok titkosítását a Microsoft által felügyelt kulcsokkal vagy az ügyfél által felügyelt kulcsok használatával (Bring Your Own Key – BYOK). Ez a funkció lehetővé teszi az Azure Event Hubs-adatok inaktív állapotban való titkosításához használt ügyfél által felügyelt kulcsok elérésének létrehozását, elforgatását, letiltását és visszavonását.

A BYOK funkció engedélyezése egy egyszeri telepítési folyamat a névtérben.

> [!NOTE]
> A BYOK képességet [Event Hubs dedikált egybérlős](event-hubs-dedicated-overview.md) fürtök támogatják. Nem engedélyezhető a standard Event Hubs névterekhez.

A Azure Key Vault segítségével kezelheti a kulcsokat, és naplózhatja a kulcshasználat. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../key-vault/key-vault-overview.md)

Ez a cikk bemutatja, hogyan konfigurálhat egy Key vaultot az ügyfél által felügyelt kulcsokkal a Azure Portal használatával. Ha meg szeretné tudni, hogyan hozhat létre kulcstartót a Azure Portal használatával, tekintse meg a következőt [: gyors üzembe helyezés és a titkos kód beolvasása Azure Key Vault a Azure Portal használatával](../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok Azure-Event Hubs való használata megköveteli, hogy a kulcstartó két szükséges tulajdonsággal rendelkezzen. Ezek a következők: **Soft delete** és **not Purge**. Ezek a tulajdonságok alapértelmezés szerint engedélyezve vannak, amikor új kulcstartót hoz létre a Azure Portal. Ha azonban egy meglévő kulcstartón kell engedélyeznie ezeket a tulajdonságokat, akkor a PowerShellt vagy az Azure CLI-t kell használnia.

## <a name="enable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok engedélyezése
Az ügyfél által felügyelt kulcsok Azure Portal való engedélyezéséhez kövesse az alábbi lépéseket:

1. Navigáljon a dedikált Event Hubs-fürthöz.
1. Válassza ki azt a névteret, amelyen engedélyezni szeretné a BYOK.
1. A Event Hubs névtér **Beállítások** lapján válassza a **titkosítás**lehetőséget. 
1. Válassza ki az **ügyfél által felügyelt kulcs titkosítását a nyugalmi** állapotban, ahogy az az alábbi képen is látható. 

    ![Ügyfél által felügyelt kulcs engedélyezése](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Key Vault beállítása kulcsokkal
Az ügyfél által felügyelt kulcsok engedélyezése után hozzá kell rendelnie az ügyfél által felügyelt kulcsot az Azure Event Hubs-névtérhez. A Event Hubs csak Azure Key Vault használatát támogatja. Ha az előző szakaszban az **ügyfél által felügyelt kulcs** beállítással engedélyezi a titkosítást, a kulcsot Azure Key Vaultba kell importálnia. Emellett a kulcsoknak is rendelkeznie kell a **Soft delete** szolgáltatással, és **nem szabad kiüríteni** a kulcsot. Ezeket a beállításokat a [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) vagy a [parancssori](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)felület használatával lehet konfigurálni.

1. Új kulcstartó létrehozásához kövesse [az Azure Key Vault rövid](../key-vault/key-vault-overview.md)útmutatót. A meglévő kulcsok importálásával kapcsolatos további információkért lásd: [a kulcsok, a titkok és a tanúsítványok ismertetése](../key-vault/about-keys-secrets-and-certificates.md).
1. Ha be szeretné kapcsolni a törlés és a kiürítés védelmét a tároló létrehozásakor, használja az az kulcstartó [create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) parancsot.

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Ha a kiürítési védelmet egy meglévő tárolóba kívánja hozzáadni (amely már rendelkezik a helyreállított törlés engedélyezésével), használja az az kulcstartó [Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) parancsot.

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. A kulcsok létrehozásához kövesse az alábbi lépéseket:
    1. Új kulcs létrehozásához válassza a **Létrehozás/importálás** elemet a **kulcsok** menüből a **Beállítások**területen.
        
        ![A létrehozó/Importálás gomb kiválasztása](./media/configure-customer-managed-key/select-generate-import.png)
    1. Adja **meg a** kívánt nevet a kulcs **létrehozásához** és megadásához.

        ![Kulcs létrehozása](./media/configure-customer-managed-key/create-key.png) 
    1. Most kiválaszthatja ezt a kulcsot, hogy társítsa a Event Hubs névteret a titkosításhoz a legördülő listából. 

        ![Kulcs kiválasztása a Key vaultból](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Adja meg a kulcs részleteit, és kattintson a **kiválasztás**elemre. Ez lehetővé teszi a névtérben tárolt adatok titkosítását az ügyfél által felügyelt kulccsal. 


## <a name="rotate-your-encryption-keys"></a>A titkosítási kulcsok elforgatása
A Key vaultban az Azure Key Vaults rotációs mechanizmus használatával forgathatja el a kulcsot. További információkért lásd: a [kulcs rotációjának és naplózásának beállítása](../key-vault/key-vault-key-rotation-log-monitoring.md). Az aktiválási és a lejárati dátumok is megadhatók a kulcs elforgatásának automatizálására. A Event Hubs szolgáltatás felderíti az új kulcs-verziókat, és automatikusan elkezdi használni őket.

## <a name="revoke-access-to-keys"></a>Kulcsok elérésének visszavonása
A titkosítási kulcsokhoz való hozzáférés visszavonása nem törli az Event Hubsból származó adatok törlését. Azonban az adatok nem érhetők el a Event Hubs névtérből. A titkosítási kulcsot a hozzáférési házirendben vagy a kulcs törlésével vonhatja vissza. További információ a hozzáférési házirendekről és a Key Vault biztonságossá [tételéről a kulcstartó biztonságos eléréséről](../key-vault/key-vault-secure-your-key-vault.md).

A titkosítási kulcs visszavonása után a titkosított névtér Event Hubs szolgáltatása inműködőképes lesz. Ha a kulcshoz való hozzáférés engedélyezve van, vagy a törlési kulcs vissza van állítva, Event Hubs szolgáltatás a kulcsot fogja kiválasztani, hogy hozzáférhessen az adatokhoz a titkosított Event Hubs névtérből.

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása 
A diagnosztikai naplók beállítása a BYOK-kompatibilis névterekhez megadja a szükséges információkat a műveletekről, amikor egy névtér az ügyfél által felügyelt kulcsokkal van titkosítva. Ezeket a naplókat engedélyezheti és később továbbíthatja az Event hub számára, vagy elemezheti a log Analytics szolgáltatásban, vagy továbbíthatja a tárolóba a testreszabott elemzések elvégzéséhez. További információ a diagnosztikai naplókról: [Az Azure diagnosztikai naplók áttekintése](../azure-monitor/platform/platform-logs-overview.md).

## <a name="enable-user-logs"></a>Felhasználói naplók engedélyezése
Az alábbi lépéseket követve engedélyezheti a naplók számára az ügyfél által felügyelt kulcsokat.

1. A Azure Portal Navigáljon arra a névtérre, amelyen engedélyezve van a BYOK.
1. A **figyelés**területen válassza a **diagnosztikai beállítások** lehetőséget.

    ![Diagnosztikai beállítások kiválasztása](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Válassza a **+ diagnosztikai beállítások hozzáadása**elemet. 

    ![Válassza a diagnosztikai beállítás hozzáadása elemet.](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Adjon meg egy **nevet** , és válassza ki, hová szeretné továbbítani a naplókat.
1. Válassza a **CustomerManagedKeyUserLogs** és a **Mentés**lehetőséget. Ez a művelet engedélyezi a BYOK lévő naplókat a névtérben.

    ![Válassza az ügyfél által felügyelt kulcs felhasználói naplók lehetőséget](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Napló sémája 
Az összes napló JavaScript Object Notation (JSON) formátumban van tárolva. Minden bejegyzés tartalmaz egy karakterlánc-mezőt, amely az alábbi táblázatban ismertetett formátumot használja. 

| Név | Leírás |
| ---- | ----------- | 
| Feladatnév | A sikertelen feladat leírása. |
| Tevékenységazonosító | A nyomon követéshez használt belső azonosító. |
| category | Meghatározza a feladat besorolását. Ha például a kulcstartó kulcsa le van tiltva, akkor az egy információs kategória lenne, vagy ha egy kulcs nem csomagolható ki, a hiba a következő lehet:. |
| resourceId | Erőforrás-azonosító Azure Resource Manager |
| keyVault | A Key Vault teljes neve. |
| kulcs | Az Event Hubs névtér titkosításához használt kulcsnév. |
| version | A használt kulcs verziószáma. |
| művelet | A Key vaultban a kulcsban végrehajtott művelet. Például letilthatja/engedélyezheti a kulcsot, becsomagolhatja vagy kicsomagolhatja |
| kód | A művelethez társított kód. Példa: hibakód, 404 azt jelenti, hogy a kulcs nem található. |
| message | A művelethez társított hibaüzenetek |

Íme egy példa egy ügyfél által felügyelt kulcs naplójára:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="troubleshoot"></a>Hibaelhárítás
Ajánlott eljárásként mindig engedélyezze a naplókat, például az előző szakaszban láthatókat. Segít a tevékenységek nyomon követésében, ha a BYOK titkosítás engedélyezve van. Emellett segít a problémák megoldásában.

A következő gyakori hibakódokat kell megkeresnie, amikor a BYOK-titkosítás engedélyezve van.

| Műveletek | Hibakód | Eredményül kapott állapot |
| ------ | ---------- | ----------------------- | 
| Kicsomagolási/kicsomagolási engedély eltávolítása a kulcstartóból | 403 |    Nem érhető el |
| HRE-szerepkör tagságának eltávolítása egy olyan HRE, amely a wrap/unwrap engedélyt adta | 403 |  Nem érhető el |
| Titkosítási kulcs törlése a Key vaultból | 404 | Nem érhető el |
| A Key Vault törlése | 404 | Nem érhető el (feltételezi, hogy a helyreállítható törlés engedélyezve van, ami kötelező beállítás.) |
| A lejárati idő módosítása a titkosítási kulcson úgy, hogy az már lejárt | 403 |   Nem érhető el  |
| A NBF módosítása (nem korábban), így a kulcs titkosítási kulcsa nem aktív | 403 | Nem érhető el  |
| A Key Vault-tűzfalhoz tartozó **MSFT-szolgáltatások engedélyezése** lehetőség kiválasztásával vagy egyéb módon blokkolja a titkosítási kulccsal rendelkező kulcstartó hálózati hozzáférését. | 403 | Nem érhető el |
| A Key Vault áthelyezése másik bérlőre | 404 | Nem érhető el |  
| Átmeneti hálózati hiba vagy DNS-/HRE/MSI-leállás |  | Elérhető a gyorsítótárazott adattitkosítási kulcs használatával |

> [!IMPORTANT]
> Ha engedélyezni szeretné, hogy a Geo-DR olyan névtérben legyen, amely a BYOK-titkosítást használja, a párosítás másodlagos névterének dedikált fürtben kell lennie, és rendelkeznie kell egy hozzá tartozó, rendszerhez rendelt felügyelt identitással. További információ: [felügyelt identitások az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:
- [Event Hubs – áttekintés](event-hubs-about.md)
- [Key Vault áttekintése](../key-vault/key-vault-overview.md)




