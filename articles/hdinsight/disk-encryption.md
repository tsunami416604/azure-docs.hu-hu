---
title: Ügyfél által felügyelt kulcslemez-titkosítás az Azure HDInsighthoz
description: Ez a cikk ismerteti, hogyan használhatja a saját titkosítási kulcs az Azure Key Vault az Azure HDInsight-fürtök felügyelt lemezein tárolt adatok titkosításához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 732709dbcb5ebe54025a963379128f1a1e74183e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536301"
---
# <a name="customer-managed-key-disk-encryption"></a>Ügyfél által felügyelt kulcson alapuló lemeztitkosítás

Az Azure HDInsight támogatja az ügyfél által felügyelt kulcstitkosítást a HDInsight fürt virtuális gépeihez csatlakoztatott felügyelt lemezeken és erőforráslemezeken lévő adatokhoz. Ez a funkció lehetővé teszi, hogy az Azure Key Vault használatával kezelje a hdinsight-fürtök inaktív adatait biztonságos titkosítási kulcsokat.

A HDInsight összes felügyelt lemeze az Azure Storage Service Encryption (SSE) szolgáltatással védett. Alapértelmezés szerint a lemezeken lévő adatok microsoftáltal kezelt kulccsal vannak titkosítva. Ha engedélyezi az ügyfél által felügyelt kulcsokat a HDInsight számára, akkor biztosítja a HDInsight titkosítási kulcsait a kulcsok azure Key Vault használatával való használatához és kezeléséhez.

Ez a dokumentum nem foglalkozik az Azure Storage-fiókban tárolt adatokkal. Az Azure Storage titkosításáról az [Azure Storage titkosítása az inaktív adatokért](../storage/common/storage-service-encryption.md)című témakörben talál további információt. A fürtök előfordulhat, hogy egy vagy több csatolt Azure Storage-fiókok, ahol a titkosítási kulcsok is microsoft által kezelt vagy ügyfél által felügyelt, de a titkosítási szolgáltatás eltérő.

## <a name="introduction"></a>Introduction (Bevezetés)

Az ügyfél által felügyelt kulcstitkosítás egy egylépéses folyamat, amelyet a fürt létrehozása során további költségek nélkül kezelnek. Mindössze annyit kell tennie, hogy regisztrálja a HDInsight-ot felügyelt identitásként az Azure Key Vaultban, és adja hozzá a titkosítási kulcsot a fürt létrehozásakor.

A fürt minden csomópontján az erőforráslemez és a felügyelt lemezek egy szimmetrikus adattitkosítási kulccsal (DEK) vannak titkosítva. A DEK a kulcstitkosítási kulcs (KEK) használatával védett a kulcstartóból. A titkosítási és visszafejtési folyamatokat teljes egészében az Azure HDInsight kezeli.

Ha a kulcstartó tűzfala engedélyezve van a kulcstartóban, ahol a lemeztitkosítási kulcs található, a HDInsight regionális erőforrás-szolgáltató IP-címét a régióban, ahol a fürt telepítve lesz hozzá kell adni a key vault tűzfalának konfigurációját. Erre azért van szükség, mert a HDInsight nem megbízható Azure-kulcstartó szolgáltatás.

Az Azure Portalon vagy az Azure CLI-ben biztonságosan forgathatja a kulcsokat a key vaultban. Amikor egy kulcs elfordul, a HDInsight-fürt perceken belül elkezdi használni az új kulcsot. Engedélyezze a [Soft Delete](../key-vault/general/overview-soft-delete.md) kulcsvédelmi funkciókat a zsarolóprogramok forgatókönyvei és a véletlen törlés elleni védelem érdekében. A biztonsági szolgáltatás nélküli kulcstartók nem támogatottak.

|Fürt típusa |OPERÁCIÓS RENDSZER-lemez (felügyelt lemez) |Adatlemez (felügyelt lemez) |Ideiglenes adatlemez (helyi SSD) |
|---|---|---|---|
|Kafka, HBase gyorsított írások|[SSE titkosítás](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|SSE titkosítás + opcionális CMK titkosítás|Opcionális CMK titkosítás|
|Minden más fürt (Spark, Interactive, Hadoop, HBase gyorsított írások nélkül)|SSE titkosítás|N/A|Opcionális CMK titkosítás|

## <a name="get-started-with-customer-managed-keys"></a>Első lépések az ügyfél által kezelt kulcsokkal

Ügyfél által felügyelt kulcsképes HDInsight-fürt létrehozásához az alábbi lépéseken hajtjuk végre:

1. Felügyelt identitások létrehozása az Azure-erőforrásokhoz
1. Azure Key Vault létrehozása
1. Kulcs létrehozása
1. Hozzáférési házirend létrehozása
1. HDInsight-fürt létrehozása ügyféláltal kezelt kulcsgal engedélyezve
1. A titkosítási kulcs elforgatása

## <a name="create-managed-identities-for-azure-resources"></a>Felügyelt identitások létrehozása az Azure-erőforrásokhoz

Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást a Key Vault hitelesítéséhez.

Lásd: [Felhasználó által hozzárendelt felügyelt identitás létrehozása](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) bizonyos lépésekhez. A felügyelt identitások Azure HDInsightban való működéséről további információt az [Azure HDInsight felügyelt identitásai című témakörben talál.](hdinsight-managed-identities.md) Ügyeljen arra, hogy mentse a felügyelt identitás-erőforrás-azonosító, amikor hozzáadja a Key Vault hozzáférési szabályzat.

## <a name="create-azure-key-vault"></a>Azure Key Vault létrehozása

Kulcstartó létrehozása. Az [Azure Key Vault létrehozása](../key-vault/secrets/quick-create-portal.md) című témakörben további lépéseket.

A HDInsight csak az Azure Key Vault ot támogatja. Ha saját kulcstartóval rendelkezik, importálhatja a kulcsokat az Azure Key Vaultba. Ne feledje, hogy a key vault kell **soft delete** engedélyezve kell. A meglévő kulcsok importálásáról a [Kulcsok, titkos kulcsok és tanúsítványok című](../key-vault/about-keys-secrets-and-certificates.md)területen talál további információt.

## <a name="create-key"></a>Kulcs létrehozása

1. Az új kulcstartóból válassza a **Beállítások** > **kulcsok** > **+ Létrehozás/importálás**lehetőséget.

    ![Új kulcs létrehozása az Azure Key Vaultban](./media/disk-encryption/create-new-key.png "Új kulcs létrehozása az Azure Key Vaultban")

1. Adjon meg egy nevet, majd válassza **a Létrehozás gombot.** Az RSA alapértelmezett **kulcstípusának** **karbantartása**.

    ![kulcsnevet hoz létre](./media/disk-encryption/create-key.png "Kulcsnév létrehozása")

1. Amikor visszatér a **Kulcsok** lapra, jelölje ki a létrehozott kulcsot.

    ![kulcstartó kulcslistája](./media/disk-encryption/key-vault-key-list.png)

1. Válassza ki a **kulcsverzió** lap megnyitásához a verziót. Ha a saját kulcs hdinsight fürttitkosítás, meg kell adnia a kulcs URI.When you use your own key for HDInsight cluster encryption, you need to provide the key URI. Másolja a **kulcsazonosítót,** és mentse el valahova, amíg készen nem áll a fürt létrehozására.

    ![kulcsazonosító beírása](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>Hozzáférési házirend létrehozása

1. Az új kulcstartóban keresse meg a **Beállítások** > **elérése házirendjeit** > **+ Hozzáférés-házirend hozzáadása lehetőséget.**

    ![Új Azure Key Vault hozzáférési szabályzat létrehozása](./media/disk-encryption/key-vault-access-policy.png)

1. A **Hozzáférési házirend hozzáadása** lapon adja meg a következő információkat:

    |Tulajdonság |Leírás|
    |---|---|
    |Kulcsengedélyek|Válassza **a Bekés,** **a Kulcs kicsomagolása**és **a Körbefolyatási kulcs lehetőséget.**|
    |Titkos engedélyek|Válassza **a Get**, **Set**és **Delete**lehetőséget.|
    |Egyszerű kijelölés|Válassza ki a korábban létrehozott, felhasználó által hozzárendelt felügyelt identitást.|

    ![Az Azure Key Vault hozzáférési szabályzatának kiválasztása egyszerű kiválasztása](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Válassza a **Hozzáadás** lehetőséget.

1. Kattintson a **Mentés** gombra.

    ![Az Azure Key Vault hozzáférési szabályzatának mentése](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Fürt létrehozása ügyfél által kezelt kulcslemez-titkosítással

Most már készen áll egy új HDInsight-fürt létrehozására. Az ügyfél által felügyelt kulcs csak a fürt létrehozása során alkalmazható az új fürtökre. A titkosítás nem távolítható el az ügyfél által felügyelt kulcsfürtökből, és az ügyfél által felügyelt kulcs nem adható hozzá a meglévő fürtökhöz.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

A fürt létrehozása során adja meg a teljes **kulcsazonosítót**, beleértve a kulcsverziót is. Például: `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. A felügyelt identitást is hozzá kell rendelnie a fürthöz, és meg kell adnia a kulcs URI-ját.

![Új fürt létrehozása](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

A következő példa bemutatja, hogyan azure CLI egy új Apache Spark-fürt létrehozásához lemeztitkosítás engedélyezve van. További információ: [Azure CLI az hdinsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create).

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

### <a name="using-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok használata

A következő példa bemutatja, hogyan azure Resource Manager sablon használatával hozzon létre egy új Apache Spark-fürt lemeztitkosítás engedélyezve van. További információ: [Mik azok az ARM sablonok?](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

Ez a példa a PowerShell használatával hívja meg a sablont.

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

Az erőforrás-kezelési sablon `azuredeploy.json`tartalma, :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

## <a name="rotating-the-encryption-key"></a>A titkosítási kulcs elforgatása

Előfordulhatnak olyan esetek, amikor érdemes lehet módosítani a HDInsight-fürt által a létrehozás után használt titkosítási kulcsokat. Ez könnyen átvihető a portálon. Ehhez a művelethez a fürtnek hozzáféréssel kell rendelkeznie mind az aktuális kulcshoz, mind a tervezett új kulcshoz, ellenkező esetben az elforgatási kulcs művelete sikertelen lesz.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

A kulcs elforgatásához az alap kulcstartó URI-jára van szükség. Miután ezt megtette, lépjen a portál HDInsight-fürttulajdonságai szakaszára, és kattintson a **Kulcs módosítása** elemre a **Lemeztitkosítási kulcs URL-címe**csoportban. Írja be az új kulcs URL-jét, és küldje el forgatni a kulcsot.

![lemeztitkosítási kulcs elforgatása](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

A következő példa bemutatja, hogyan forgatható el egy meglévő HDInsight-fürt lemeztitkosítási kulcsa. További információ: [Azure CLI az hdinsight rotate-disk-encryption-key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Gyakran feltett kérdések az ügyfél által kezelt kulcstitkosítással kapcsolatban

**Hogyan fér hozzá a HDInsight-fürt a key vaultomhoz?**

A HDInsight a HDInsight-fürthöz társítani kívánt felügyelt identitás használatával éri el az Azure Key Vault-példányt. Ez a felügyelt identitás a fürt létrehozása előtt vagy alatt hozható létre. A felügyelt identitás hozzáférést is biztosítania kell a kulcstárolóhoz, ahol a kulcs tárolják.

**Ez a funkció a HDInsight összes fürtje számára elérhető?**

Az ügyfél által felügyelt kulcstitkosítás a Spark 2.1 és 2.2 kivételével minden fürttípushoz elérhető.

**Több kulcsot is használhatok különböző lemezek vagy mappák titkosítására?**

Nem, az összes felügyelt lemezt és erőforráslemezt ugyanaz a kulcs titkosítja.

**Mi történik, ha a fürt elveszíti a hozzáférést a key vault hoz vagy a kulcshoz?**

Ha a fürt elveszíti a hozzáférést a kulcshoz, figyelmeztetések jelennek meg az Apache Ambari portálon. Ebben az állapotban a **kulcs módosítása** művelet sikertelen lesz. A kulcshozzáférés visszaállítása után az Ambari figyelmeztetései megszűnnek, és az olyan műveletek, mint a kulcselforgatás, sikeresen végrehajthatók.

![kulcshozzáférés Ambari riasztás](./media/disk-encryption/ambari-alert.png)

**Hogyan állítható helyre a fürt, ha a kulcsok törlődnek?**

Mivel csak a "Soft Delete" engedélyezett kulcsok támogatottak, ha a kulcsok at a kulcstartóban helyreáll, a fürtnek vissza kell szereznie a kulcsokhoz való hozzáférést. Az Azure Key Vault-kulcs helyreállításáról az Undo-AzKeyVaultKeyRemoval vagy az-keyvault-key-recover [(Visszavonás-AzKeyVaultKeyRemoval) (Visszavonás-AzKeyVaultKeyRemoval vagy az-keyvault-key-recover) (Visszavonás-AzKeyVaultKeyRemoval) (Visszavonás-AzKeyVaultKeyRemoval vagy](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) [az-keyvault-key-recover) (](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover)

**Mely lemeztípusok vannak titkosítva? Az operációs rendszer lemezei/erőforráslemezei is titkosítva vannak?**

Az erőforráslemezek és az adat/felügyelt lemezek titkosítottak. Az operációs rendszer lemezei nincsenek titkosítva.

**Ha egy fürt felskálázott, az új csomópontok zökkenőmentesen támogatják az ügyfélszolgálat által felügyelt kulcsokat?**

Igen. A fürtnek hozzáférésre van szüksége a kulcsa a key vault ban a felskálázás során. Ugyanaz a kulcs a felügyelt lemezek és a fürt erőforráslemezei titkosítására szolgál.

**Elérhetők az ügyfél által felügyelt kulcsok a tartózkodási helyemen?**

A HDInsight ügyféláltal felügyelt kulcsai minden nyilvános felhőben és nemzeti felhőben elérhetők.

## <a name="next-steps"></a>További lépések

* Az Azure Key Vaultról a [Mi az Azure Key Vault](../key-vault/general/overview.md)című témakörben talál további információt.
* [Az Azure HDInsight vállalati biztonságának áttekintése](./domain-joined/hdinsight-security-overview.md).
