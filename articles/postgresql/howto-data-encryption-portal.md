---
title: Adattitkosítás Azure Database for PostgreSQL önálló kiszolgáló számára a Azure Portal használatával
description: Megtudhatja, hogyan állíthatja be és kezelheti a Azure Database for PostgreSQL önálló kiszolgáló adattitkosítását a Azure Portal használatával.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 6028f5e618b4b480a2259241fc2380f0200cebc6
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898351"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Adattitkosítás Azure Database for PostgreSQL önálló kiszolgáló számára a Azure Portal használatával

Ebből a témakörből megtudhatja, hogyan állíthatja be és kezelheti a Azure Database for PostgreSQL egyetlen kiszolgáló adattitkosítását a Azure Portal használatával.

## <a name="prerequisites-for-azure-cli"></a>Az Azure CLI előfeltételei

* Rendelkeznie kell egy Azure-előfizetéssel, és rendszergazdának kell lennie az előfizetésben.
* A Azure Key Vault-ban hozzon létre egy Key vaultot és egy kulcsot, amelyet az ügyfél által felügyelt kulcshoz szeretne használni.
* A Key vaultnak a következő tulajdonságokkal kell rendelkeznie az ügyfél által felügyelt kulcsként való használathoz:
  * [Helyreállítható törlés](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Védett kiürítés](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* A kulcsnak a következő attribútumokkal kell rendelkeznie, amelyeket ügyfél által felügyelt kulcsként kell használni:
  * Nincs lejárati dátum
  * Nincs letiltva
  * Képes a Get, a wrap Key és a dewrap Key művelet végrehajtására

## <a name="set-the-right-permissions-for-key-operations"></a>A megfelelő engedélyek beállítása a kulcsfontosságú műveletekhez

1. A Key Vault területen válassza a **hozzáférési szabályzatok** > **hozzáférési házirend hozzáadása**lehetőséget.

   ![Képernyőkép a Key Vaultről, hozzáférési házirendekkel, és Kiemelt hozzáférési szabályzat hozzáadása](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Válassza a **kulcs engedélyei**lehetőséget, majd válassza a **beolvasás**, **becsomagolás**, **kicsomagolás**és a **rendszerbiztonsági tag**lehetőséget, amely a PostgreSQL-kiszolgáló neve. Ha a kiszolgáló rendszerbiztonsági tagja nem található a meglévő rendszerbiztonsági tag listájában, regisztrálnia kell. A rendszer arra kéri, hogy regisztrálja a kiszolgálói rendszerbiztonsági tag-t, amikor első alkalommal kísérli meg az adattitkosítás beállítását, és sikertelen lesz.  

   ![Hozzáférési szabályzat – áttekintés](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Kattintson a **Mentés** gombra.

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Adattitkosítás beállítása Azure Database for PostgreSQL önálló kiszolgálóhoz

1. Az ügyfél által felügyelt kulcs beállításához Azure Database for PostgreSQL válassza az **adattitkosítás** lehetőséget.

   ![Képernyőkép a Azure Database for PostgreSQLről, az adattitkosítás kiemelésével](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Kijelölhet egy kulcstartót és egy kulcspárt, vagy megadhatja a kulcs azonosítóját is.

   ![Képernyőkép a Azure Database for PostgreSQLről, az adattitkosítási lehetőségek kiemelésével](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Kattintson a **Mentés** gombra.

4. Annak biztosítása érdekében, hogy az összes fájl (beleértve az ideiglenes fájlokat is) teljes mértékben titkosítva legyen, indítsa újra a kiszolgálót.

## <a name="restore-or-create-a-replica-of-the-server"></a>A kiszolgáló replikájának visszaállítása vagy létrehozása

Miután Azure Database for PostgreSQL egy kiszolgálót a Key Vault tárolt ügyfél felügyelt kulcsával, a kiszolgáló minden újonnan létrehozott példánya is titkosítva lesz. Ezt az új másolatot helyi vagy geo-visszaállítási művelettel vagy replika (helyi/régió) művelettel teheti meg. Tehát titkosított PostgreSQL-kiszolgáló esetén a következő lépésekkel hozhat létre titkosított visszaállított kiszolgálót.

1. A kiszolgálón válassza az **áttekintés** > **visszaállítás**lehetőséget.

   ![Képernyőkép a Azure Database for PostgreSQLről, áttekintés és visszaállítás kiemelve](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Vagy replikálásra alkalmas kiszolgáló esetén a **Beállítások** fejléc alatt válassza a **replikálás**lehetőséget.

   ![Képernyőkép a Azure Database for PostgreSQLról, a replikálás kiemelve](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. A visszaállítási művelet befejezése után a létrehozott új kiszolgáló az elsődleges kiszolgáló kulcsával lesz titkosítva. A kiszolgáló szolgáltatásai és beállításai azonban le vannak tiltva, és a kiszolgáló nem érhető el. Ez megakadályozza az adatkezelést, mert az új kiszolgáló identitása még nem kapott engedélyt a kulcstartó elérésére.

   ![Képernyőkép a Azure Database for PostgreSQLről, a nem elérhető állapot kiemelésével](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. A kiszolgáló elérhetővé tételéhez érvényesítse újra a kulcsot a visszaállított kiszolgálón. Válassza **az adattitkosítás** > a **kulcs újraérvényesítése**lehetőséget.

   > [!NOTE]
   > Az első újraellenőrzési kísérlet sikertelen lesz, mert az új kiszolgáló egyszerű szolgáltatásának hozzáférést kell adni a kulcstartóhoz. Az egyszerű szolgáltatásnév létrehozásához válassza a **kulcs újraérvényesítése**lehetőséget, amely hibaüzenetet jelenít meg, de létrehozza az egyszerű szolgáltatásnevet. Ezt követően tekintse meg a jelen cikk korábbi részében [ismertetett lépéseket](#set-the-right-permissions-for-key-operations) .

   ![Képernyőkép a Azure Database for PostgreSQLról, az újraérvényesítési lépés kiemelve](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   A Key vaultnak hozzáférést kell adnia az új kiszolgálóhoz.

4. Az egyszerű szolgáltatás regisztrálását követően ismét ellenőrizze újra a kulcsot, és a kiszolgáló folytatja a normál működést.

   ![Képernyőkép a Azure Database for PostgreSQLről, amely a visszaállított funkciókat mutatja](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Adattitkosítás engedélyezése Azure Resource Manager sablon használatával

A Azure Portalon kívül a Azure Database for PostgreSQL egyetlen kiszolgálón is engedélyezheti az adattitkosítást az új és a meglévő kiszolgálók Azure Resource Manager sablonjaival.

### <a name="for-a-new-server"></a>Új kiszolgáló esetén

Az egyik előre létrehozott Azure Resource Manager-sablon használatával kiépítheti a kiszolgálót az adattitkosítás engedélyezésével: [példa adattitkosításra](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Ez a Azure Resource Manager sablon létrehoz egy Azure Database for PostgreSQL önálló kiszolgálót, és a **kulcstartót** és a kulcsot adja át paraméterként **az** adattitkosítás engedélyezéséhez a kiszolgálón.

### <a name="for-an-existing-server"></a>Meglévő kiszolgáló esetén
Emellett Azure Resource Manager-sablonokkal is engedélyezheti az adattitkosítást a meglévő Azure Database for PostgreSQL önálló kiszolgálókon.

* Adja át a korábban a tulajdonságok objektum `keyVaultKeyUri` tulajdonsága alatt átmásolt Azure Key Vault kulcs URI-JÁT.

* Használja az *2020-01-01-Preview API-* verziót.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

 További információ az adattitkosításról: [Azure Database for PostgreSQL egykiszolgálós adattitkosítás az ügyfél által felügyelt kulccsal](concepts-data-encryption-postgresql.md).
