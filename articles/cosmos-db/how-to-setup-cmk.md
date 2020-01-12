---
title: Ügyfél által felügyelt kulcsok konfigurálása a Azure Cosmos DB-fiókhoz
description: Ismerje meg, hogyan konfigurálhatja az ügyfél által felügyelt kulcsokat a Azure Cosmos DB-fiókjához
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: thweiss
ms.openlocfilehash: 32266abd5bcf8d7e137095d130ee872cc07edaf0
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904080"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-db-account"></a>Ügyfél által felügyelt kulcsok konfigurálása a Azure Cosmos DB-fiókhoz

> [!NOTE]
> Jelenleg a funkció használatához hozzáférést kell kérnie. Ehhez vegye fel a kapcsolatot [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com).

A Azure Cosmos DB fiókjában tárolt adatai automatikusan és zökkenőmentesen titkosítva vannak. Azure Cosmos DB két lehetőséget kínál az inaktív adatok titkosításához használt kulcsok kezelésére:
- **Szolgáltatás által felügyelt kulcsok**. Alapértelmezés szerint a Microsoft kezeli a Azure Cosmos DB-fiók titkosításához használt kulcsokat.
- **Ügyfél által felügyelt kulcsok (CMK)** . Lehetőség van arra is, hogy egy második titkosítási réteget adjon hozzá a felügyelt kulcsokkal.

Az ügyfél által felügyelt kulcsokat [Azure Key Vault](../key-vault/key-vault-overview.md)kell tárolni. Minden CMK-kompatibilis fiókhoz meg kell adni egy kulcsot, amely a fiókban tárolt összes információ titkosítására szolgál.

## <a name="setup"></a>Telepítés

Az ügyfél által felügyelt kulcsok jelenleg csak az új fiókok számára érhetők el, és a fiók létrehozása során kell beállítani őket.

### <a name="1-make-sure-the-azure-cosmos-db-resource-provider-is-registered-for-your-azure-subscription"></a>1. Győződjön meg arról, hogy a Azure Cosmos DB erőforrás-szolgáltató regisztrálva van az Azure-előfizetéséhez

A Azure Portal nyissa meg az Azure-előfizetését, és válassza az "erőforrás-szolgáltatók" lehetőséget a bal oldali menüben:

!["Erőforrás-szolgáltatók" bejegyzés a bal oldali menüben](./media/how-to-setup-cmk/portal-rp.png)

Keresse meg a "Microsoft. DocumentDB" erőforrás-szolgáltatót.
- Ha az erőforrás-szolgáltató már regisztráltként van megjelölve, semmit nem kell végrehajtania.
- Ha nem, válassza ki, majd kattintson a "regisztráció" gombra:

    ![A Microsoft. DocumentDB erőforrás-szolgáltató regisztrálása](./media/how-to-setup-cmk/portal-rp-register.png)

### <a name="2-configure-your-azure-key-vault-instance"></a>2. az Azure Key Vault-példány konfigurálása

Az ügyfél által felügyelt kulcsok Azure Cosmos DB használatához két tulajdonságot kell beállítani a titkosítási kulcsok tárolásához használni kívánt Azure Key Vault-példányon: **Soft delete** és **not Purge**. Ezek a tulajdonságok alapértelmezés szerint nem engedélyezettek, de a PowerShell vagy az Azure CLI használatával is engedélyezhető.

Ha meg szeretné tudni, hogyan engedélyezheti ezeket a tulajdonságokat egy meglévő Azure Key Vault-példányon, tekintse meg a következő cikkek egyikében, a Soft-delete engedélyezése és a kiürítési védelem engedélyezése című szakaszt:
- [A Soft delete használata a PowerShell-lel](../key-vault/key-vault-soft-delete-powershell.md)
- [A Soft delete használata az Azure CLI-vel](../key-vault/key-vault-soft-delete-cli.md)

### <a name="3-add-an-access-policy-to-your-azure-key-vault-instance"></a>3. hozzáférési szabályzat hozzáadása az Azure Key Vault-példányhoz

A Azure Portal lépjen a titkosítási kulcsok üzemeltetéséhez használni kívánt Azure Key Vault-példányra. Ezután válassza a bal oldali menüben a "hozzáférési szabályzatok" elemet:

!["Hozzáférési szabályzatok" a bal oldali menüben](./media/how-to-setup-cmk/portal-akv-ap.png)

- Kattintson a "+ hozzáférési szabályzat hozzáadása" elemre.
- A "kulcs engedélyei" legördülő menüben válassza a "Get", "kicsomagolási kulcs" és "wrap Key" lehetőséget:

    ![A megfelelő engedélyek kiválasztása](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

- A "tag kiválasztása" területen kattintson a "nincs kijelölt" elemre, keresse meg és válassza ki a "Azure Cosmos DB" rendszerbiztonsági tag elemet, majd kattintson alul a "kiválasztás" gombra (ha a "Azure Cosmos DB" rendszerbiztonsági tag nem található, akkor előfordulhat, hogy újra regisztrálnia kell a "Microsoft. DocumentDB" erőforrást. szolgáltató a 2. lépésben):

    ![A Azure Cosmos DB rendszerbiztonsági tag kiválasztása](./media/how-to-setup-cmk/portal-akv-add-ap.png)

- Az új hozzáférési szabályzat hozzáadásához kattintson a "Hozzáadás" gombra.

### <a name="4-generate-a-key-in-azure-key-vault"></a>4. kulcs létrehozása a Azure Key Vaultban

A Azure Portal lépjen a titkosítási kulcsok üzemeltetéséhez használni kívánt Azure Key Vault-példányra. Ezután válassza a "kulcsok" lehetőséget a bal oldali menüben:

!["Keys" bejegyzés a bal oldali menüben](./media/how-to-setup-cmk/portal-akv-keys.png)

- Kattintson a "létrehozó/Importálás" elemre.
- Adja meg az új kulcs nevét, válasszon ki egy RSA-kulcs méretét (legalább 3072 ajánlott a legjobb biztonság érdekében), majd kattintson a "létrehozás" gombra:

    ![Új kulcs létrehozása](./media/how-to-setup-cmk/portal-akv-gen.png)

- A kulcs létrehozása után kattintson az újonnan létrehozott kulcsra, majd a jelenlegi verziójára
- Másolja a kulcs azonosítóját a legutolsó perjel utáni rész kivételével:

    ![A kulcs kulcs-azonosítójának másolása](./media/how-to-setup-cmk/portal-akv-keyid.png)

### <a name="5-create-a-new-azure-cosmos-db-account"></a>5. új Azure Cosmos DB fiók létrehozása

#### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Amikor új Azure Cosmos DB fiókot hoz létre a Azure Portal, a "titkosítás" lépésnél válassza az "ügyfél által felügyelt kulcs" lehetőséget. A "kulcs URI-ja" mezőben adja meg a 4. lépésből másolt Azure Key Vault kulcs URI-JÁT:

![CMK paraméterek beállítása a Azure Portalban](./media/how-to-setup-cmk/portal-cosmos-enc.png)

#### <a name="using-powershell"></a>A PowerShell használata

Új Azure Cosmos DB-fiók létrehozásakor a PowerShell-lel
- a "PropertyObject" "keyVaultKeyUri" tulajdonságában a 4. lépésben átmásolt Azure Key Vault kulcs URI-JÁT adja át.
- Győződjön meg arról, hogy az "2019-12-12" API-verzióként használja.

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

#### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok használata

Új Azure Cosmos DB fiók létrehozásakor Azure Resource Manager sablonon keresztül:
- adja át a "tulajdonságok" objektum "keyVaultKeyUri" tulajdonságában a 4. lépésben átmásolt Azure Key Vault kulcs URI-JÁT.
- Ügyeljen arra, hogy az "2019-12-12" API-verzióként használja

```
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

Igen. Az ügyfél által felügyelt kulcsokkal való adattitkosítás és-visszafejtés kezeléséhez szükséges további számítási terhelések kiszámításához az Azure Cosmos DB-fiókkal végrehajtott összes művelet 25%-kal növekszik a [kérések felhasznált egységében](./request-units.md) .

### <a name="what-data-gets-encrypted-with-the-cmk"></a>Milyen adattitkosítást kap a CMK?

A rendszer a Azure Cosmos DB fiókjában tárolt összes adatfájlt titkosítja a CMK, a következő meta-adatértékek kivételével:
- a Azure Cosmos DB [-fiókok,-adatbázisok és-tárolók](./account-overview.md#elements-in-an-azure-cosmos-account)neve,
- a [tárolt eljárások](./stored-procedures-triggers-udfs.md)neve,
- az [indexelési szabályzatokban](./index-policy.md)deklarált tulajdonságok elérési útjai
- a tárolók [partíciós kulcsának](./partitioning-overview.md)értékei.

### <a name="will-customer-managed-keys-be-supported-for-existing-accounts"></a>Támogatni fogja az ügyfél által felügyelt kulcsokat a meglévő fiókok esetében?

Ez a funkció jelenleg csak az új fiókok esetében érhető el.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Van olyan terv, amely támogatja a legfinomabb részletességet, mint a fiók szintű kulcsok?

Jelenleg nem, de a tároló szintű kulcsok is megtekinthetők.

### <a name="how-does-customer-managed-keys-affect-backups"></a>Hogyan befolyásolja az ügyfél által felügyelt kulcsok a biztonsági mentéseket?

A Azure Cosmos DB [rendszeres és automatikus biztonsági mentést](./online-backup-and-restore.md) készít a fiókjában tárolt adatairól. Ezzel a művelettel biztonsági másolatot készít a titkosított adatforgalomról. Ahhoz, hogy egy visszaállított biztonsági mentés felhasználható legyen, a biztonsági mentés idején használt titkosítási kulcsnak továbbra is elérhetőnek kell lennie. Ez azt jelenti, hogy nem történt visszavonás, és a biztonsági mentés idején használt kulcs verziószáma továbbra is engedélyezve van.

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