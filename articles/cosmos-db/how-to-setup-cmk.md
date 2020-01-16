---
title: Ügyfél által felügyelt kulcsok konfigurálása a Azure Cosmos DB-fiókhoz
description: Ismerje meg, hogyan konfigurálhatja az ügyfél által felügyelt kulcsokat a Azure Cosmos DB-fiókjához a Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: c77e89c509f10155ddc27e92f09465959b629f67
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979201"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Ügyfél által felügyelt kulcsok konfigurálása Azure Cosmos-fiókhoz Azure Key Vault

> [!NOTE]
> Jelenleg a funkció használatához hozzáférést kell kérnie. Ehhez vegye fel a kapcsolatot [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com).

Az Azure Cosmos-fiókban tárolt adatai automatikusan és zökkenőmentesen titkosítva vannak. Azure Cosmos DB két lehetőséget kínál az inaktív adatok titkosításához használt kulcsok kezelésére:

- **Szolgáltatás által felügyelt kulcsok** – alapértelmezés szerint a Microsoft kezeli az Azure Cosmos-fiókban tárolt adattitkosításhoz használt kulcsokat.

- **Ügyfél által felügyelt kulcsok (CMK)** – lehetőség van arra is, hogy egy második titkosítási réteget adjon hozzá a saját kulcsaihoz.

Az ügyfél által felügyelt kulcsokat a [Azure Key Vaultban](../key-vault/key-vault-overview.md) kell tárolnia, és meg kell adnia egy kulcsot minden olyan Azure Cosmos-fiókhoz, amely engedélyezve van az ügyfél által felügyelt kulcsokkal. Ezzel a kulccsal lehet titkosítani az adott fiókban tárolt összes adathalmazt.

> [!NOTE]
> Az ügyfél által felügyelt kulcsok jelenleg csak az új Azure Cosmos-fiókok esetében érhetők el, és a fiók létrehozása során kell azokat konfigurálni.

## <a id="register-resource-provider"></a>Az Azure-előfizetéshez tartozó Azure Cosmos DB erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/), nyissa meg az Azure-előfizetését, és válassza az **erőforrás-szolgáltatók** lehetőséget a **Beállítások** lapon:

   !["Erőforrás-szolgáltatók" bejegyzés a bal oldali menüben](./media/how-to-setup-cmk/portal-rp.png)

1. Keresse meg a **Microsoft. DocumentDB** erőforrás-szolgáltatót. Ellenőrizze, hogy az erőforrás-szolgáltató már regisztráltként van-e megjelölve. Ha nem, válassza ki az erőforrás-szolgáltatót, és válassza a **regisztráció**lehetőséget:

   ![A Microsoft. DocumentDB erőforrás-szolgáltató regisztrálása](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Az Azure Key Vault-példány konfigurálása

Az ügyfél által felügyelt kulcsok Azure Cosmos DB alkalmazásával a titkosítási kulcsok üzemeltetéséhez használni kívánt Azure Key Vault-példányon két tulajdonságot kell beállítania. Ezek a tulajdonságok közé tartozik a Soft DELETE, és **nem** **törölhető** . Ezek a tulajdonságok alapértelmezés szerint nem engedélyezettek, a PowerShell vagy az Azure CLI használatával engedélyezheti azokat.

Ha meg szeretné tudni, hogyan engedélyezheti ezeket a tulajdonságokat egy meglévő Azure Key Vault-példányon, tekintse meg a következő cikkek egyikében a "Soft-delete engedélyezése" és a "kiürítés engedélyezése" szakaszt:

- [A Soft delete használata a PowerShell-lel](../key-vault/key-vault-soft-delete-powershell.md)
- [A Soft delete használata az Azure CLI-vel](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Hozzáférési szabályzat hozzáadása az Azure Key Vault-példányhoz

1. A Azure Portal lépjen a titkosítási kulcsok üzemeltetéséhez használni kívánt Azure Key Vault-példányra. A bal oldali menüben válassza a **hozzáférési szabályzatok** lehetőséget:

   !["Hozzáférési szabályzatok" a bal oldali menüben](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Válassza a **+ hozzáférési házirend hozzáadása** lehetőséget

1. A **kulcs engedélyei** legördülő menüben kattintson a **beolvasás**, a **kulcs kicsomagolása** és a **kicsomagolási kulcs** engedélyei lehetőségre:

   ![A megfelelő engedélyek kiválasztása](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. A **rendszerbiztonsági tag kiválasztása**területen válassza a **nincs kiválasztva**lehetőséget. Ezután keresse meg **Azure Cosmos db** rendszerbiztonsági tag elemet, majd válassza ki. Végül kattintson az alul található **kijelölés** elemre (ha a **Azure Cosmos db** rendszerbiztonsági tag nem szerepel a listában, akkor előfordulhat, hogy újra regisztrálnia kell a **Microsoft. DocumentDB** erőforrás-szolgáltatót a jelen cikk [erőforrás-szolgáltató regisztrálása](#register-resource-provider) című szakaszában leírtak szerint):

   ![Azure Cosmos DB rendszerbiztonsági tag kiválasztása](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Válassza a **Hozzáadás** lehetőséget az új hozzáférési szabályzat hozzáadásához

## <a name="generate-a-key-in-azure-key-vault"></a>Kulcs létrehozása Azure Key Vault

1. A Azure Portal lépjen a titkosítási kulcsok üzemeltetéséhez használni kívánt Azure Key Vault-példányra. Ezután válassza a bal oldali menü **kulcsok** elemét:

   !["Keys" bejegyzés a bal oldali menüben](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Válassza a **Létrehozás/importálás**lehetőséget, adja meg az új kulcs nevét, válasszon ki egy RSA-kulcs méretét (legalább 3072 ajánlott a legjobb biztonság érdekében), majd válassza a **Létrehozás**lehetőséget:

   ![Új kulcs létrehozása](./media/how-to-setup-cmk/portal-akv-gen.png)

1. A kulcs létrehozása után válassza ki az újonnan létrehozott kulcsot, majd a jelenlegi verzióját.

1. Másolja a kulcs **azonosítóját** a legutolsó perjel utáni rész kivételével:

   ![A kulcs kulcs-azonosítójának másolása](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Új Azure Cosmos-fiók létrehozása

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Amikor új Azure Cosmos DB fiókot hoz létre a Azure Portal, válassza az **ügyfél által felügyelt kulcs** elemet a **titkosítási** lépésben. Illessze be a **kulcs URI** mezőjébe az előző lépésből másolt Azure Key Vault-kulcs URI-azonosítóját:

![CMK paraméterek beállítása a Azure Portalban](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

Új Azure Cosmos DB-fiók létrehozásakor a PowerShell-lel

- Adja át a **PropertyObject** **keyVaultKeyUri** tulajdonságának előző részében átmásolt Azure Key Vault kulcs URI-ját

- Használja az **2019-12-12** -as API-verziót.

> [!IMPORTANT]
> Az `Location` paramétert explicit módon kell beállítani ahhoz, hogy a fiók sikeresen létre legyen hozva az ügyfél által felügyelt kulcsokkal.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a name="using-azure-resource-manager-template"></a>Az Azure Resource Manager-sablonok használata

Új Azure Cosmos-fiók létrehozásakor Azure Resource Manager sablonon keresztül:

- Adja meg a korábban a **Tulajdonságok** objektum **keyVaultKeyUri** tulajdonsága alatt másolt Azure Key Vault-kulcs URI-ját.

- Használja az **2019-12-12** -as API-verziót.

> [!IMPORTANT]
> Az `Location` paramétert explicit módon kell beállítani ahhoz, hogy a fiók sikeresen létre legyen hozva az ügyfél által felügyelt kulcsokkal.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}

```

Telepítse a sablont a következő PowerShell-parancsfájl használatával:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="is-there-any-additional-charge-when-using-customer-managed-keys"></a>Díjköteles az ügyfél által felügyelt kulcsok használata?

Igen. Az ügyfél által felügyelt kulcsokkal való adattitkosítás és-visszafejtés kezeléséhez szükséges további számítási terhelések kiszámításához az Azure Cosmos-fiókon végrehajtott összes művelet 25%-os növekedést igényel a [kérelmek egységében](./request-units.md).

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Milyen adattitkosítást kap az ügyfél által felügyelt kulcsokkal?

Az Azure Cosmos-fiókban tárolt összes adatfájl titkosítása az ügyfél által felügyelt kulcsokkal történik, a következő meta-adatértékek kivételével:

- A Azure Cosmos DB [-fiókok,-adatbázisok és-tárolók](./account-overview.md#elements-in-an-azure-cosmos-account) neve

- A [tárolt eljárások](./stored-procedures-triggers-udfs.md) nevei

- Az [indexelési szabályzatokban](./index-policy.md) deklarált tulajdonságok elérési útjai

- A tárolók [partíciós kulcsának](./partitioning-overview.md) értékei

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Támogatottak-e az ügyfél által felügyelt kulcsok a meglévő Azure Cosmos-fiókok esetében?

Ez a funkció jelenleg csak az új fiókok esetében érhető el.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Van olyan terv, amely támogatja a legfinomabb részletességet, mint a fiók szintű kulcsok?

Jelenleg nem, de a tároló szintű kulcsok is megtekinthetők.

### <a name="how-does-customer-managed-keys-affect-a-backup"></a>Hogyan befolyásolja az ügyfél által felügyelt kulcsok a biztonsági mentést?

A Azure Cosmos DB [rendszeres és automatikus biztonsági mentést](./online-backup-and-restore.md) készít a fiókjában tárolt adatairól. Ezzel a művelettel biztonsági másolatot készít a titkosított adatforgalomról. A visszaállított biztonsági mentés használatához a biztonsági mentés időpontjában használt titkosítási kulcsot kötelező megadni. Ez azt jelenti, hogy nem történt visszavonás, és a biztonsági mentés idején használt kulcs verziószáma továbbra is engedélyezve van.

### <a name="how-do-i-revoke-an-encryption-key"></a>Hogyan visszavon egy titkosítási kulcsot?

A kulcs visszavonása a kulcs legújabb verziójának letiltásával végezhető el:

![Kulcs verziójának letiltása](./media/how-to-setup-cmk/portal-akv-rev2.png)

Másik lehetőségként a Azure Key Vault-példány összes kulcsának visszavonásához törölheti a Azure Cosmos DB rendszerbiztonsági tag számára biztosított hozzáférési szabályzatot:

![A Azure Cosmos DB rendszerbiztonsági tag hozzáférési szabályzatának törlése](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Milyen műveletek érhetők el az ügyfél által felügyelt kulcs visszavonása után?

Az egyetlen művelet lehetséges, ha a titkosítási kulcs visszavonva van, a fiók törlése.

## <a name="next-steps"></a>Következő lépések

- További információ az [adattitkosításról Azure Cosmos db](./database-encryption-at-rest.md)
- Áttekintés a [Cosmos db lévő adathozzáférések biztonságos eléréséről](secure-access-to-data.md)