---
title: Adattitkosítás - Azure CLI - Azure Database for PostgreSQL - Single server
description: Ismerje meg, hogyan állíthatja be és kezelheti az Azure Database for PostgreSQL Single server adattitkosítását az Azure CLI használatával.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: c778130a95be1aa0afe81241130b7daf165360c4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522133"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-cli"></a>Az Azure Database for PostgreSQL Single server adattitkosítása az Azure CLI használatával

Ismerje meg, hogyan használhatja az Azure CLI-t az Azure Database for PostgreSQL Single server adattitkosításának beállításához és kezeléséhez.

## <a name="prerequisites-for-azure-cli"></a>Az Azure CLI előfeltételei

* Rendelkeznie kell egy Azure-előfizetéssel, és az adott előfizetés rendszergazdájának kell lennie.
* Hozzon létre egy kulcstartót és egy kulcsot, amelyet egy ügyfél által felügyelt kulcshoz használhat. Is lehetővé teszi a kiürítési védelem és a kulcstartóban a törlés.

    ```azurecli-interactive
    az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
    ```

* A létrehozott Azure Key Vault, hozza létre a kulcsot, amely az Azure Database for PostgreSQL Single server adattitkosításához lesz használva.

    ```azurecli-interactive
    az keyvault key create --name <key_name> -p software --vault-name <vault_name>
    ```

* Egy meglévő kulcstartó használatához a következő tulajdonságokkal kell rendelkeznie az ügyfél által felügyelt kulcsként való használathoz:
  * [Helyreállítható törlés](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Tisztítás védett](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* A kulcsnak a következő attribútumokkal kell rendelkeznie az ügyfél által felügyelt kulcsként való használathoz:
  * Nincs lejárati dátum
  * Nincs letiltva
  * **Be-** és **kicsomagolási** műveletek végrehajtása **wrap**

## <a name="set-the-right-permissions-for-key-operations"></a>A kulcsműveletekhez szükséges engedélyek beállítása

1. Kétféleképpen lehet bekérni az Azure Database for PostgreSQL Single server felügyelt identitását.

    ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>Hozzon létre egy új Azure-adatbázist a MySQL-kiszolgálóhoz felügyelt identitással.

    ```azurecli-interactive
    az postgres server create --name -g <resource_group> --location <locations> --storage-size <size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled>  --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>Frissítsen egy meglévő Azure Database for MySQL-kiszolgálót felügyelt identitás lekérni.

    ```azurecli-interactive
    az postgres server update –name <server name>  -g <resoure_group> --assign-identity
    ```

2. Állítsa be a Kulcs **engedélyeket** **(Bekés,** **Befolyás**, **Kicsomagolás**) a **Felhasználó**hoz , amely a PostgreSQL Single kiszolgáló kiszolgáló neve.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Adattitkosítás beállítása az Azure Database for PostgreSQL Single server számára

1. Engedélyezze az adatok titkosítását az Azure Database for PostgreSQL single server az Azure Key Vaultban létrehozott kulcs használatával.

    ```azurecli-interactive
    az postgres server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    Kulcs url:https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Adattitkosítás használata visszaállítási vagy replikakiszolgálókhoz

Miután az Azure Database for PostgreSQL Single server titkosítva van az ügyfél által a Key Vaultban tárolt kezelt kulccsal, a kiszolgáló újonnan létrehozott példányai is titkosítva lesznek. Ezt az új másolatot helyi vagy geo-visszaállítási művelettel, vagy egy replika (helyi/régióközi) művelettel is elkészítheti. Tehát egy titkosított PostgreSQL Single szerver szerver, akkor a következő lépéseket, hogy hozzon létre egy titkosított visszaállított szerver.

### <a name="creating-a-restoredreplica-server"></a>Visszaállított/replikakiszolgáló létrehozása

  *  [Visszaállítási kiszolgáló létrehozása](howto-restore-server-cli.md) 
  *  [Olvasott replikakiszolgáló létrehozása](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>A kiszolgáló visszaállítása után a visszaállított kiszolgáló újraérvényesíti az adattitkosítást

    ```azurecli-interactive
    az postgres server key create –name  <server name> -g <resource_group> --kid <key url>
    ```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-postgresql-single-server"></a>További képesség a PostgreSQL single server Azure Database for PostgreSQL-adatbázishoz használt kulcshoz

### <a name="get-the-key-used"></a>A kulcs használatba hozása

    ```azurecli-interactive
    az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
    ```

    Key url:  https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

### <a name="list-the-key-used"></a>A használt kulcs felsorolása

    ```azurecli-interactive
    az postgres server key list --name  <server name>  -g <resource_group>
    ```

### <a name="drop-the-key-being-used"></a>A használt kulcs eldobása

    ```azurecli-interactive
    az postgres server key delete -g <resource_group> --kid <key url> 
    ```
## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Az Adatok titkosításának engedélyezéséhez Azure Resource Manager-sablon használata

Az Azure Portalon kívül engedélyezheti az adatok titkosítását az Azure Database for PostgreSQL egyetlen kiszolgálón az Azure Resource Manager-sablonok használatával az új és a meglévő kiszolgálóhoz.

### <a name="for-a-new-server"></a>Új kiszolgáló esetén

Használja az előre létrehozott Azure Resource Manager-sablonok egyikét a kiszolgáló kiépítéséhez, amelyen engedélyezve van az adattitkosítás: [Példa adattitkosítással](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Ez az Azure Resource Manager-sablon létrehoz egy Azure Database for PostgreSQL Single server és használja a **KeyVault** és a **keypassed** paraméterekként átadott adatok titkosítását a kiszolgálón.

### <a name="for-an-existing-server"></a>Meglévő kiszolgáló esetén
Emellett az Azure Resource Manager-sablonok használatával engedélyezheti az adatok titkosítását a meglévő Azure-adatbázison a PostgreSQL Single kiszolgálókhoz.

* Adja át az Azure Key Vault-kulcs erőforrás-azonosítóját, `Uri` amelyet korábban másolt a tulajdonság alatt a tulajdonság objektumban.

* Használja *a 2020-01-01-preview API-verzió.*

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

 Az adattitkosításról az [Azure Database for PostgreSQL Single server adattitkosítás ügyféláltal kezelt kulccsal című témakörben](concepts-data-encryption-postgresql.md)olvashat bővebben.
