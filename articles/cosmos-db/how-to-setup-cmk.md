---
title: Ügyfél által felügyelt kulcsok konfigurálása az Azure Cosmos DB-fiókjához
description: Megtudhatja, hogyan konfigurálhatja az Azure Cosmos DB-fiókjához az ügyfél által felügyelt kulcsokat az Azure Key Vault segítségével
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 8f58887a056c8ca0cd175a44127556562338de38
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450032"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Ügyfél által felügyelt kulcsok konfigurálása az Azure Cosmos-fiókhoz az Azure Key Vault segítségével

> [!NOTE]
> Ebben az időben hozzáférést kell kérnie a funkció használatához. Ehhez vegye fel [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com)a kapcsolatot.

Az Azure Cosmos-fiókban tárolt adatok automatikusan és zökkenőmentesen titkosítva vannak a Microsoft által kezelt kulcsokkal (**szolgáltatás által kezelt kulcsok).** Opcionálisan hozzáadhat egy második titkosítási réteget a kezelt kulcsokkal (**ügyfél által kezelt kulcsok**).

![Az ügyféladatok körüli titkosítás rétegei](./media/how-to-setup-cmk/cmk-intro.png)

Az ügyfelek által felügyelt kulcsokat az [Azure Key Vaultban](../key-vault/general/overview.md) kell tárolnia, és minden olyan Azure Cosmos-fiókhoz kulcsot biztosítania kell, amely az ügyfél által felügyelt kulcsokkal van engedélyezve. Ez a kulcs az adott fiókban tárolt összes adat titkosítására szolgál.

> [!NOTE]
> Jelenleg az ügyfél által felügyelt kulcsok csak az új Azure Cosmos-fiókok hoz érhetők el. A fiók létrehozása során konfigurálnia kell őket.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a>Regisztrálja az Azure Cosmos DB erőforrás-szolgáltatóját az Azure-előfizetéshez

1. Jelentkezzen be az [Azure Portalra,](https://portal.azure.com/)nyissa meg az Azure-előfizetését, és válassza **az Erőforrás-szolgáltatók** lehetőséget a **Beállítások** lapon:

   !["Erőforrás-szolgáltatók" bejegyzés a bal oldali menüből](./media/how-to-setup-cmk/portal-rp.png)

1. Keresse meg a **Microsoft.DocumentDB** erőforrás-szolgáltatót. Ellenőrizze, hogy az erőforrás-szolgáltató már regisztráltként van-e megjelölve. Ha nem, válassza ki az erőforrás-szolgáltatót, és válassza a **Regisztráció**lehetőséget:

   ![A Microsoft.DocumentDB erőforrás-szolgáltató regisztrálása](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Az Azure Key Vault-példány konfigurálása

Az ügyfél által felügyelt kulcsok használata az Azure Cosmos DB használatához két tulajdonságot kell beállítania az Azure Key Vault-példányon, amelyet a titkosítási kulcsok üzemeltetéséhez kíván használni. Ezek a tulajdonságok közé tartozik **a Soft Delete** és a Ne **ürítés**. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve. Engedélyezheti őket a PowerShell vagy az Azure CLI használatával.

Ha meg szeretné tudni, hogyan engedélyezheti ezeket a tulajdonságokat egy meglévő Azure Key Vault-példányon, tekintse meg a "Helyreállítható törlés engedélyezése" és a "Kiürítési védelem engedélyezése" című szakaszokat az alábbi cikkek egyikében:

- [A soft-delete használata a PowerShell használatával](../key-vault/general/soft-delete-powershell.md)
- [A soft-delete használata az Azure CLI-vel](../key-vault/general/soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Hozzáférési szabályzat hozzáadása az Azure Key Vault-példányhoz

1. Az Azure Portalon nyissa meg az Azure Key Vault-példányt, amely a titkosítási kulcsok üzemeltetéséhez használni kívánja. Válassza a bal oldali menü **Hozzáférési házirendek parancsát:**

   !["Hozzáférési házirendek" a bal oldali menüből](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Válassza **a + Hozzáférési házirend hozzáadása lehetőséget**.

1. A **Kulcsengedélyek** legördülő menüben válassza a **Get**, Unwrap Key és Wrap Key (Kulcs **kibontása)** és **a Kulcs tördelése** parancsát:

   ![A megfelelő engedélyek kiválasztása](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. A **Tag kijelölése**csoportban válassza **a Nincs lehetőséget.** Ezután keresse meg az **Azure Cosmos DB** fő, és válassza ki (hogy könnyebb `a232010e-820c-4083-83bb-3ace5fc29d0b` megtalálni, akkor is kereshet a fő `57506a73-e302-42a9-b869-6f12d9ec29e9`azonosító: bármely Azure-régióban, kivéve az Azure Government régiók, ahol a fő azonosító). Végül válassza a **Kijelölés** lehetőséget az alján. Ha az **Azure Cosmos DB** főnév nem szerepel a listában, előfordulhat, hogy újra regisztrálnia kell a **Microsoft.DocumentDB** erőforrás-szolgáltatót a cikk [erőforrás-szolgáltató regisztrálása](#register-resource-provider) című részében leírtak szerint.

   ![Válassza ki az Azure Cosmos DB egyszerű](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Az új hozzáférési szabályzat hozzáadásához válassza a **Hozzáadás** lehetőséget.

## <a name="generate-a-key-in-azure-key-vault"></a>Kulcs létrehozása az Azure Key Vaultban

1. Az Azure Portalon nyissa meg az Azure Key Vault-példányt, amelyet a titkosítási kulcsok üzemeltetéséhez kíván használni. Ezután válassza a bal oldali menü **Billentyűparancsait:**

   !["Billentyűk" bejegyzés a bal oldali menüből](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Válassza **a Létrehozás/importálás**lehetőséget, adja meg az új kulcs nevét, és válasszon egy RSA-kulcsméretet. A legjobb biztonság érdekében legalább 3072 ajánlott. Ezután válassza **a Létrehozás lehetőséget:**

   ![Új kulcs létrehozása](./media/how-to-setup-cmk/portal-akv-gen.png)

1. A kulcs létrehozása után jelölje ki az újonnan létrehozott kulcsot, majd annak aktuális verzióját.

1. Másolja a kulcs **kulcsazonosítóját,** kivéve az utolsó perjel utáni részt:

   ![A kulcs kulcsazonosítójának másolása](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Új Azure Cosmos-fiók létrehozása

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Amikor új Azure Cosmos DB-fiókot hoz létre az Azure Portalon, válassza az **Ügyfél által felügyelt kulcsot** a **titkosítási** lépésben. A **Key URI** mezőbe illessze be az előző lépésből másolt Azure Key Vault-kulcs URI-/kulcsazonosítóját:

![CMK-paraméterek beállítása az Azure Portalon](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

Amikor új Azure Cosmos DB-fiókot hoz létre a PowerShell használatával:

- Adja át az Azure Key Vault kulcskulcs URI-ját, amelyet korábban másolt a **PropertyObject** **keyVaultKeyUri** tulajdonsága alatt.

- Használja **a 2019-12-12** API-verziót.

> [!IMPORTANT]
> Explicit módon `Location` kell beállítania a paramétert ahhoz, hogy a fiók sikeresen létrelegyen ügyfél által kezelt kulcsokkal.

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

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager-sablon használata

Amikor új Azure Cosmos-fiókot hoz létre egy Azure Resource Manager-sablonon keresztül:

- Adja át az Azure Key Vault-kulcs URI-ját, amelyet korábban másolt a **tulajdonságobjektum** **keyVaultKeyUri** tulajdonsága alatt.

- Használja **a 2019-12-12** API-verziót.

> [!IMPORTANT]
> Explicit módon `Location` kell beállítania a paramétert ahhoz, hogy a fiók sikeresen létrelegyen ügyfél által kezelt kulcsokkal.

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

Telepítse a sablont a következő PowerShell-parancsfájllal:

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

### <a name="using-the-azure-cli"></a>Az Azure parancssori felületének használata

Amikor új Azure Cosmos-fiókot hoz létre az Azure CLI-n keresztül, adja át az Azure Key Vault-kulcs URI-ját, amelyet korábban másolt a **--key-uri** paraméter alatt.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>Van-e további díj az ügyfél által felügyelt kulcsok használatáért?

Igen. Az adatok titkosításának és visszafejtésének ügyfél által felügyelt kulcsokkal történő kezeléséhez szükséges további számítási terhelés figyelembevétele érdekében az Azure Cosmos-fiókkal végrehajtott összes művelet 25 százalékos növekedést fogyaszt a [kérelemegységekben.](./request-units.md)

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Milyen adatok lesznek titkosítva az ügyfél által felügyelt kulcsokkal?

Az Azure Cosmos-fiókban tárolt összes adat titkosítva van az ügyfél által felügyelt kulcsokkal, kivéve a következő metaadatokat:

- Az Azure Cosmos [DB-fiókok, adatbázisok és tárolók nevei](./account-overview.md#elements-in-an-azure-cosmos-account)

- A tárolt [eljárások nevei](./stored-procedures-triggers-udfs.md)

- Az [indexelési szabályzatokban](./index-policy.md) deklarált tulajdonságelérési utak

- A tárolók [partíciókulcsainak](./partitioning-overview.md) értékei

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Az ügyfél által felügyelt kulcsok támogatottak a meglévő Azure Cosmos-fiókokhoz?

Ez a funkció jelenleg csak új fiókok esetén érhető el.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Van-e terv a finomabb részletesség támogatására, mint a fiókszintű kulcsok?

Jelenleg nem, de a tárolószintű kulcsok at is figyelembe veszik.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Hogyan befolyásolják az ügyfél által felügyelt kulcsok a biztonsági mentést?

Az Azure Cosmos DB [rendszeres és automatikus biztonsági mentést](./online-backup-and-restore.md) készít a fiókjában tárolt adatokról. Ez a művelet biztonsági másolatot ad a titkosított adatokról. A visszaállított biztonsági másolat használatához szükség van a biztonsági mentés idején használt titkosítási kulcsra. Ez azt jelenti, hogy nem történt visszavonás, és a biztonsági mentés időpontjában használt kulcs verziója továbbra is engedélyezve lesz.

### <a name="how-do-i-revoke-an-encryption-key"></a>Hogyan vonhatok vissza egy titkosítási kulcsot?

A kulcs visszavonása a kulcs legújabb verziójának letiltásával történik:

![Kulcs verziójának letiltása](./media/how-to-setup-cmk/portal-akv-rev2.png)

Másik lehetőségként az Azure Key Vault-példány összes kulcsának visszavonásához törölheti az Azure Cosmos DB-alapszámára biztosított hozzáférési szabályzatot:

![Az Azure Cosmos DB főnév hozzáférési szabályzatának törlése](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Milyen műveletek érhetők el az ügyfél által felügyelt kulcs visszavonása után?

A titkosítási kulcs visszavonása esetén csak a fiók törlése lehetséges.

## <a name="next-steps"></a>További lépések

- További információ [az Azure Cosmos DB adattitkosításáról.](./database-encryption-at-rest.md)
- Az adatokhoz való biztonságos hozzáférés áttekintése [a Cosmos DB-ben.](secure-access-to-data.md)
