---
title: Adattitkosítás - Azure CLI - Azure Database for MySQL
description: Ismerje meg, hogyan állíthatja be és kezelheti az azure-i MySQL-adatbázis adattitkosítását az Azure CLI használatával.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 37f6f0dc9c1221207273110252bff445d2e1245b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459100"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>Az Azure Database for MySQL adattitkosítása az Azure CLI használatával

Ismerje meg, hogyan használhatja az Azure CLI-t az Azure Database for MySQL adattitkosításának beállításához és kezeléséhez.

## <a name="prerequisites-for-azure-cli"></a>Az Azure CLI előfeltételei

* Rendelkeznie kell egy Azure-előfizetéssel, és az adott előfizetés rendszergazdájának kell lennie.
* Hozzon létre egy kulcstartót és egy kulcsot, amelyet egy ügyfél által felügyelt kulcshoz használhat. Is lehetővé teszi a kiürítési védelem és a kulcstartóban a törlés.

    ```azurecli-interactive
    az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
    ```

* A létrehozott Azure Key Vault, hozza létre a kulcsot, amely az Azure Database for MySQL adattitkosításához lesz használva.

    ```azurecli-interactive
    az keyvault key create --name <key_name> -p software --vault-name <vault_name>
    ```

* Egy meglévő kulcstartó használatához a következő tulajdonságokkal kell rendelkeznie az ügyfél által felügyelt kulcsként való használathoz:
  * [Helyreállítható törlés](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Tisztítás védett](../key-vault/general/overview-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* A kulcsnak a következő attribútumokkal kell rendelkeznie az ügyfél által felügyelt kulcsként való használathoz:
  * Nincs lejárati dátum
  * Nincs letiltva
  * Be- és **törlesztési** **műveletek** végrehajtása **get**

## <a name="set-the-right-permissions-for-key-operations"></a>A kulcsműveletekhez szükséges engedélyek beállítása

1. Kétféleképpen lehet bekérni a felügyelt identitás az Azure Database for MySQL.

    ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>Hozzon létre egy új Azure-adatbázist a MySQL-kiszolgálóhoz felügyelt identitással.

    ```azurecli-interactive
    az mysql server create --name -g <resource_group> --location <locations> --storage-size <size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled>  --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>Frissítsen egy meglévő Azure Database for MySQL-kiszolgálót felügyelt identitás lekérni.

    ```azurecli-interactive
    az mysql server update --name  <server name>  -g <resource_group> --assign-identity
    ```

2. Állítsa be a Kulcs **engedélyeket** **(Be:** **Befolyás,** **Befolyás, Kicsomagolás**) a **Fő**, amely a MySQL-kiszolgáló neve.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Adattitkosítás beállítása a MySQL-hez készült Azure Database számára

1. Engedélyezze az adatok titkosítását az Azure Database for MySQL az Azure Key Vaultban létrehozott kulcs használatával.

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    Kulcs url:https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Adattitkosítás használata visszaállítási vagy replikakiszolgálókhoz

Miután az Azure Database for MySQL titkosítva van az ügyfél által a Key Vaultban tárolt kezelt kulccsal, a kiszolgáló újonnan létrehozott példányais titkosítva lesz. Ezt az új másolatot helyi vagy geo-visszaállítási művelettel, vagy egy replika (helyi/régióközi) művelettel is elkészítheti. Így egy titkosított MySQL szerver, akkor a következő lépéseket, hogy hozzon létre egy titkosított visszaállított szerver.

### <a name="creating-a-restoredreplica-server"></a>Visszaállított/replikakiszolgáló létrehozása

  *  [Visszaállítási kiszolgáló létrehozása](howto-restore-server-cli.md) 
  *  [Olvasott replikakiszolgáló létrehozása](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>A kiszolgáló visszaállítása után a visszaállított kiszolgáló újraérvényesíti az adattitkosítást

    ```azurecli-interactive
    az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
    ```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>További képesség a MySQL Azure-adatbázisához használt kulcshoz

### <a name="get-the-key-used"></a>A kulcs használatba hozása

    ```azurecli-interactive
    az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
    ```

    Key url:  https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

### <a name="list-the-key-used"></a>A használt kulcs felsorolása

    ```azurecli-interactive
    az mysql server key list --name  <server name>  -g <resource_group>
    ```

### <a name="drop-the-key-being-used"></a>A használt kulcs eldobása

    ```azurecli-interactive
    az mysql server key delete -g <resource_group> --kid <key url> 
    ```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Az Adatok titkosításának engedélyezéséhez Azure Resource Manager-sablon használata

Az Azure Portalon kívül engedélyezheti az adatok titkosítását az Azure Database for MySQL-kiszolgálón az Azure Resource Manager-sablonok használatával az új és a meglévő kiszolgálókhoz.

### <a name="for-a-new-server"></a>Új kiszolgáló esetén

Használja az előre létrehozott Azure Resource Manager-sablonok egyikét a kiszolgáló kiépítéséhez, amelyen engedélyezve van az adattitkosítás: [Példa adattitkosítással](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

Ez az Azure Resource Manager-sablon létrehoz egy Azure-adatbázist a MySQL-kiszolgálóhoz, és a **keyvault-ot** és a paraméterként átadott **kulcsot** használja az adattitkosítás engedélyezéséhez a kiszolgálón.

### <a name="for-an-existing-server"></a>Meglévő kiszolgáló esetén
Emellett az Azure Resource Manager-sablonok használatával engedélyezheti az adattitkosítást a meglévő Azure-adatbázisban a MySQL-kiszolgálók számára.

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
      "type": "Microsoft.DBforMySQL/servers",
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
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
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
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
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
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
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

 Az adattitkosításról az [Azure Database for MySQL adattitkosítás ügyféláltal kezelt kulccsal című](concepts-data-encryption-mysql.md)témakörben olvashat bővebben.
