---
title: 'A saját kulcs használata: az Apache Kafka az Azure HDInsight (előzetes verzió)'
description: Ez a cikk ismerteti az Azure Key vaultból saját kulcs használata, az Apache Kafka Azure HDInsight platformon tárolt adatok titkosításához.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b5f7c472c8ebd60d8e7f928534834c9672fe3b14
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489019"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight-preview"></a>A saját kulcs használata: az Apache Kafka az Azure HDInsight (előzetes verzió)

Az Azure HDInsight az Apache Kafka Bring Your Own Key (BYOK) támogatását tartalmazza. Ez a funkció lehetővé teszi, hogy Ön a tulajdonosa, és az inaktív adatok titkosításához használt kulcsok kezelése. 

Azok a felügyelt lemezek HDInsight védve vannak az Azure Storage Service Encryption (SSE). Alapértelmezés szerint az adatokat a lemezeken lévő van titkosítva, a Microsoft által kezelt kulcsok használata. Ha engedélyezi a BYOK, meg kell adnia a HDInsight használata és kezelése az Azure Key Vault használatával való titkosítására szolgáló kulcsot. 

A BYOK-titkosítás a további költségek nélkül a fürt létrehozásakor kezelt adatbázisunk. Teendők csak HDInsight regisztrálása az Azure Key Vault egy felügyelt identitás, és adja hozzá a titkosítási kulcsot, a fürt létrehozásakor.

A Kafka-fürt (beleértve a Kafka által kezelt replikák) azokat az üzeneteket az egy szimmetrikus adatok titkosítási kulcsa (Adattitkosítási) vannak titkosítva. Az adattitkosítási kulcsot a kulcs titkosítása kulcscserekulcs (KEK) a key vaultból használatával védett. A titkosítási és visszafejtési folyamatok teljes egészében az Azure HDInsight a kezeli. 

Az Azure Portalon vagy az Azure CLI segítségével biztonságosan elforgatása a kulcsok a key vaultban. Kulcs forgatása, a HDInsight Kafka-fürt elindítja a percen belül az új kulccsal. Zsarolóprogramok elleni forgatókönyvek és véletlen törlés elleni védelem érdekében az "A helyreállítható törlés" Kulcsvédelmi szolgáltatások engedélyezése. A kulcstartók nélkül a védelmi szolgáltatás nem támogatottak.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-byok"></a>A BYOK használatának első lépései
Hozhat létre egy BYOK engedélyezve van a Kafka-fürt, végigvesszük azokat a következő lépéseket:
1. Felügyelt identitások az Azure-erőforrások létrehozása
2. Az Azure Key Vault és kulcsok beállítása
3. HDInsight Kafka-fürt létrehozása a BYOK engedélyezve

## <a name="create-managed-identities-for-azure-resources"></a>Felügyelt identitások az Azure-erőforrások létrehozása

   A hitelesítést a Key Vault, hozzon létre egy felügyelt identitás felhasználó által hozzárendelt a [az Azure portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell-lel](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md), vagy [ Az Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Felügyelt identitások munka az Azure HDInsight további információkért lásd: [által felügyelt identitásokat az Azure HDInsight](../hdinsight-managed-identities.md). Míg az Azure Active Directoryval felügyelt identitások és a kafka BYOK, vállalati biztonsági csomag (ESP) nem követelmény. Győződjön meg arról, a felügyelt identitás erőforrás-azonosító mentése a Key Vault hozzáférési szabályzattal való hozzáadásakor.

   ![Felhasználó által hozzárendelt felügyelt identitás létrehozása az Azure Portalon](./media/apache-kafka-byok/user-managed-identity-portal.png)

## <a name="setup-the-key-vault-and-keys"></a>A Key Vault és a kulcsok beállítása

   HDInsight csak az Azure Key Vault támogatja. Ha rendelkezik saját key vault, a kulcsok importálhatja az Azure Key Vaultban. Ne feledje, hogy a kulcsokat rendelkezniük kell "A helyreállítható törlés". Az "A helyreállítható törlés" funkció érhető el a REST, .NET-en keresztül /C#, PowerShell és az Azure CLI-felületeihez.

   1. Hozzon létre egy új kulcstartót, hajtsa végre a [Azure Key Vault](../../key-vault/key-vault-overview.md) rövid. Meglévő kulcsok importálása kapcsolatos további információkért látogasson el [kapcsolatos kulcsok, titkos kódok és tanúsítványok](../../key-vault/about-keys-secrets-and-certificates.md).

   2. A key vaulttal segítségével "helyreállítható törlés" engedélyezheti a [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) cli-parancsot.
        "" Az azure CLI az keyvault update – neve <Key Vault Name> --engedélyezése helyreállítható törlés
        ```

   3. Create keys

        a. To create a new key, select **Generate/Import** from the **Keys** menu under **Settings**.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png)

        b. Set **Options** to **Generate** and give the key a name.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-a-key.png)

        c. Select the key you created from the list of keys.

        ![Azure Key Vault key list](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

        d. When you use your own key for Kafka cluster encryption, you need to provide the key URI. Copy the **Key identifier** and save it somewhere until you're ready to create your cluster.

        ![Copy key identifier](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
    4. Add managed identity to the key vault access policy.
        a. Create a new Azure Key Vault access policy.

        ![Create new Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy.png)

        b. Under **Select Principal**, choose the user-assigned managed identity you created.

        ![Set Select Principal for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

        c. Set **Key Permissions** to **Get**, **Unwrap Key**, and **Wrap Key**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

        d. Set **Secret Permissions** to **Get**, **Set**, and **Delete**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

        e. Click on **Save** 

        ![Save Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## Create HDInsight cluster

   You're now ready to create a new HDInsight cluster. BYOK can only be applied to new clusters during cluster creation. Encryption can't be removed from BYOK clusters, and BYOK can't be added to existing clusters.

   ![Kafka disk encryption in Azure portal](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   During cluster creation, provide the full key URL, including the key version. For example, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. You also need to assign the managed identity to the cluster and provide the key URI.

## FAQ for BYOK to Apache Kafka

**How does the Kafka cluster access my key vault?**

   Associate a managed identity with the HDInsight Kafka cluster during cluster creation. This managed identity can be created before or during cluster creation. You also need to grant the managed identity access to the key vault where the key is stored.

**Is this feature available for all Kafka clusters on HDInsight?**

   BYOK encryption is only possible for Kafka 1.1 and above clusters.

**Can I have different keys for different topics/partitions?**

   No, all managed disks in the cluster are encrypted by the same key.

**How can I recover the cluster if the keys are deleted?**

   Since only “Soft Delete” enabled keys are supported, if the keys are recovered in the key vault, the cluster should regain access to the keys. To recover an Azure Key Vault key, see [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) or [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Can I have producer/consumer applications working with a BYOK cluster and a non-BYOK cluster simultaneously?**

   Yes. The use of BYOK is transparent to producer/consumer applications. Encryption happens at the OS layer. No changes need to be made to existing producer/consumer Kafka applications.

**Are OS disks/Resource disks also encrypted?**

   No. OS disks and Resource disks are not encrypted.

**If a cluster is scaled up, will the new brokers support BYOK seamlessly?**

   Yes. The cluster needs access to the key in the key vault during scale up. The same key is used to encrypt all managed disks in the cluster.

**Is BYOK available in my location?**

   Kafka BYOK is available in all public clouds.

## Next steps

* For more information about Azure Key Vault, see [What is Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* To get started with Azure Key Vault, see [Getting Started with Azure Key Vault](../../key-vault/key-vault-overview.md).
