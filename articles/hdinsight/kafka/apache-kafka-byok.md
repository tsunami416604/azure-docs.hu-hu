---
title: Saját kulcs használata az Azure HDInsight Apache Kafka
description: Ez a cikk azt ismerteti, hogyan használhatja a saját kulcsát a Azure Key Vaultból az Azure HDInsight Apache Kafka tárolt adatok titkosításához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 72fd23e4283925b91d749fef0afac4e87e93405c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841656"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Saját kulcs használata az Azure HDInsight Apache Kafka

Az Azure HDInsight Bring Your Own Key (BYOK) támogatást biztosít Apache Kafka számára. Ez a funkció lehetővé teszi az inaktív adatok titkosításához használt kulcsok megépítését és kezelését.

A HDInsight összes felügyelt lemeze az Azure Storage Service Encryption (SSE) védelemmel van ellátva. Alapértelmezés szerint ezeknek a lemezeknek az adatai a Microsoft által felügyelt kulcsokkal vannak titkosítva. Ha engedélyezi a BYOK-t, a HDInsight titkosítási kulcsát kell megadnia, és azt a Azure Key Vault használatával kell felügyelni.

A BYOK-titkosítás egy egylépéses folyamat, amely a fürt létrehozása során külön díj nélkül kezelhető. Mindössze annyit kell tennie, hogy felügyelt identitásként regisztrálja a HDInsight-t Azure Key Vault és hozzáadja a titkosítási kulcsot a fürt létrehozásakor.

A Kafka-fürtre (beleértve a Kafka által karbantartott replikákat is) származó összes üzenetet szimmetrikus adattitkosítási kulccsal (ADATTITKOSÍTÁSI kulcsot) titkosítja a rendszer. A ADATTITKOSÍTÁSI kulcsot a Key encryption Key (KEK) használatával védett a kulcstartóban. A titkosítási és a visszafejtési folyamatokat teljes mértékben az Azure HDInsight kezeli.

A Key vaultban lévő kulcsok biztonságos elforgatásához használhatja a Azure Portal vagy az Azure CLI-t is. Ha egy kulcs forog, a HDInsight Kafka-fürt perceken belül megkezdi az új kulcs használatát. Engedélyezze a "Soft Delete" kulcsfontosságú védelmi funkciókat a ransomware-forgatókönyvek és a véletlen törlés elleni védelemhez. A védelmi funkciót nem támogató kulcstartók nem támogatottak.

## <a name="get-started-with-byok"></a>Ismerkedés a BYOK

A BYOK-t támogató Kafka-fürt létrehozásához hajtsa végre a következő lépéseket:

1. Felügyelt identitások létrehozása az Azure-erőforrásokhoz
2. Azure Key Vault és kulcsok beállítása
3. HDInsight Kafka-fürt létrehozása a BYOK engedélyezve
4. A titkosítási kulcs elforgatása

## <a name="create-managed-identities-for-azure-resources"></a>Felügyelt identitások létrehozása az Azure-erőforrásokhoz

Ha Key Vault szeretne hitelesíteni, hozzon létre egy felhasználó által hozzárendelt felügyelt identitást a [Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), a [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), a [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)vagy az [Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)használatával. További információ arról, hogyan működnek a felügyelt identitások az Azure HDInsight-ben: [felügyelt identitások az Azure HDInsight](../hdinsight-managed-identities.md). Noha az Azure Active Directory szükséges a felügyelt identitásokhoz és a BYOK a Kafka-hez, Enterprise Security Package (ESP) nem követelmény. Ügyeljen arra, hogy a felügyelt identitás erőforrás-AZONOSÍTÓját mentse a Key Vault hozzáférési házirendhez való hozzáadásakor.

![Felhasználó által hozzárendelt felügyelt identitás létrehozása Azure Portalban](./media/apache-kafka-byok/azure-portal-create-managed-identity.png)

## <a name="set-up-the-key-vault-and-keys"></a>A Key Vault és a kulcsok beállítása

A HDInsight csak a Azure Key Vaultt támogatja. Ha rendelkezik saját kulcstartóval, a kulcsokat a Azure Key Vaultba importálhatja. Ne feledje, hogy a kulcsoknak "Soft Delete" értékkel kell rendelkezniük. A "Soft Delete" funkció a REST, a .NET/C#, a PowerShell és az Azure CLI felületén keresztül érhető el.

1. Új kulcstartó létrehozásához kövesse az [Azure Key Vault](../../key-vault/quick-create-cli.md) rövid útmutatót. A meglévő kulcsok importálásával kapcsolatos további információkért tekintse meg [a kulcsok, titkok és tanúsítványok](../../key-vault/about-keys-secrets-and-certificates.md)című témakört.

1. Engedélyezze a "Soft-Delete" parancsot a Key-vaulton az az kulcstartó [Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) CLI parancs használatával.

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. Kulcsok létrehozása.

    a. Új kulcs létrehozásához válassza a **Létrehozás/importálás** elemet a **kulcsok** menüből a **Beállítások**területen.

    ![Új kulcs létrehozása a Azure Key Vaultban](./media/apache-kafka-byok/kafka-create-new-key.png "Új kulcs létrehozása a Azure Key Vaultban")

    b. Adja **meg a** kívánt nevet a kulcs **létrehozásához** és megadásához.

    ![Az Apache Kafka generálja a kulcs nevét](./media/apache-kafka-byok/apache-kafka-create-key.png "Kulcs nevének előállítása")

    c. Válassza ki a kulcsok listájából létrehozott kulcsot.

    ![Apache Kafka Key Vault-kulcsok listája](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

    d. Ha a Kafka-fürt titkosításához saját kulcsot használ, meg kell adnia a kulcs URI-JÁT. Másolja a **kulcs azonosítóját** , és mentse valahova, amíg készen nem áll a fürt létrehozására.

    ![Apache Kafka – kulcs azonosítójának beolvasása](./media/apache-kafka-byok/kafka-get-key-identifier.png)

1. Adja hozzá a felügyelt identitást a Key Vault hozzáférési házirendjéhez.

    a. Hozzon létre egy új Azure Key Vault hozzáférési szabályzatot.

    ![Új Azure Key Vault hozzáférési szabályzat létrehozása](./media/apache-kafka-byok/add-key-vault-access-policy.png)

    b. A **rendszerbiztonsági tag kiválasztása**területen válassza ki a létrehozott, felhasználó által hozzárendelt felügyelt identitást.

    ![A rendszerbiztonsági tag kiválasztása Azure Key Vault hozzáférési házirendhez](./media/apache-kafka-byok/azure-portal-add-access-policy.png)

    c. Kulcs **engedélyeinek** beállítása a **beolvasáshoz**, a **kicsomagoláshoz**és a **becsomagolási kulcshoz**.

    ![Kulcs engedélyeinek beállítása Azure Key Vault hozzáférési Házirend1](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png "Kulcs engedélyeinek beállítása Azure Key Vault hozzáférési Házirend1")

    d. **Titkos engedélyek** beállítása a **beolvasáshoz**, a **beállításhoz**és a **törléshez**.

    ![Kulcs engedélyeinek beállítása Azure Key Vault hozzáférési policy2](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png "Kulcs engedélyeinek beállítása Azure Key Vault hozzáférési policy2")

    e. Kattintson a **Mentés** gombra.

    ![Azure Key Vault hozzáférési szabályzat mentése](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## <a name="create-hdinsight-cluster"></a>HDInsight-fürt létrehozása

Most már készen áll egy új HDInsight-fürt létrehozására. A BYOK csak a fürt létrehozása során alkalmazhatók az új fürtökre. A titkosítás nem távolítható el a BYOK-fürtökből, és a BYOK nem adható hozzá a meglévő fürtökhöz.

![Kafka-lemez titkosítása Azure Portal](./media/apache-kafka-byok/azure-portal-cluster-security-networking-kafka.png)

A fürt létrehozása során adja meg a teljes kulcs URL-címét, beleértve a kulcs verziószámát is. Például: `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Emellett a felügyelt identitást is hozzá kell rendelnie a fürthöz, és meg kell adnia a kulcs URI-JÁT. A fürt teljes létrehozásával kapcsolatos részletekért lásd: [Apache Hadoop-fürtök létrehozása a Azure Portal használatával](./apache-kafka-get-started.md)

## <a name="rotating-the-encryption-key"></a>A titkosítási kulcs elforgatása

Előfordulhat, hogy előfordulhat, hogy módosítani szeretné a Kafka-fürt által a létrehozás után használt titkosítási kulcsokat. Ez könnyen elvégezhető a portálon keresztül. Ehhez a művelethez a fürtnek hozzá kell férnie az aktuális kulcshoz és a kívánt új kulcshoz, ellenkező esetben az elforgatási kulcs művelete sikertelen lesz.

A kulcs elforgatásához az új kulcs teljes URL-címével kell rendelkeznie (lásd [a Key Vault és a kulcsok beállításának](#set-up-the-key-vault-and-keys)3. lépését). Miután ezt megtette, nyissa meg a Kafka-fürt tulajdonságai szakaszt a portálon, és kattintson a **kulcs módosítása** elemre a **lemez titkosítási kulcsának URL-címe**alatt. Adja meg az új kulcs URL-címét, és küldje el a kulcs elforgatásához.

![Kafka elforgatása lemez titkosítási kulcsa](./media/apache-kafka-byok/apache-kafka-change-key.png)

## <a name="faq-for-byok-to-apache-kafka"></a>BYOK – gyakori kérdések Apache Kafka

**Hogyan fér hozzá a Kafka-fürt a Key vaulthoz?**

Felügyelt identitást társítson a HDInsight Kafka-fürthöz a fürt létrehozása során. Ezt a felügyelt identitást a fürt létrehozása előtt vagy közben lehet létrehozni. Emellett a felügyelt identitás elérését is meg kell adnia ahhoz a kulcstartóhoz, ahol a kulcsot tárolják.

**Elérhető ez a funkció a HDInsight összes Kafka-fürtjén?**

A BYOK-titkosítás csak a Kafka 1,1-es és újabb fürtöknél lehetséges.

**Használhatok különböző kulcsokat a különböző témakörökhöz/partíciókhoz?**

Nem, a fürtben lévő összes felügyelt lemez ugyanazzal a kulccsal van titkosítva.

**Mi történik, ha a fürt elveszti a Key Vault vagy a kulcs elérését?**

Ha a fürt elveszti a kulcs elérését, a figyelmeztetések az Apache Ambari portálon jelennek meg. Ebben az állapotban a **kulcs módosítása** művelet sikertelen lesz. A kulcs-hozzáférés visszaállítása után a Ambari figyelmeztetései elindulnak, és a műveletek, például a kulcsok elforgatása sikeresen elvégezhető.

![Apache Kafka Key Access Ambari riasztása](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**Hogyan állíthatom helyre a fürtöt a kulcsok törlésekor?**

Mivel a Key vaultban csak a "Soft Delete" engedélyezett kulcsok támogatottak, a fürtnek ismét hozzá kell férnie a kulcsokhoz. Azure Key Vault kulcs helyreállításához tekintse meg a következőt: [Visszavonás-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) vagy [az-kulcstartó-Key-Recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**A BYOK-fürtökkel és a nem BYOK-fürtökkel párhuzamosan használhatók-e a gyártói/fogyasztói alkalmazások?**

Igen. A BYOK használata átlátható a termelő/fogyasztói alkalmazások számára. A titkosítás az operációsrendszer-rétegen történik. A meglévő gyártói/fogyasztói Kafka-alkalmazásokban nem szükséges módosítani a módosításokat.

**Titkosítva vannak-e az operációsrendszer-lemezek és az erőforrások lemezei is?**

Nem. Az operációsrendszer-lemezek és az erőforrás-lemezek nem titkosítottak.

**Ha a fürt felskálázásra kerül, az új közvetítők zökkenőmentesen támogatják a BYOK?**

Igen. A felskálázás során a fürtnek hozzá kell férnie a Key vaultban lévő kulcshoz. Ugyanazt a kulcsot használja a rendszer a fürt összes felügyelt lemezének titkosítására.

**A BYOK elérhető a saját helyen?**

A Kafka BYOK minden nyilvános felhőben elérhető.

## <a name="next-steps"></a>Következő lépések

* További információ a Azure Key Vaultről: [Mi az Azure Key Vault](../../key-vault/key-vault-overview.md)?
* A Azure Key Vault megkezdéséhez lásd: [első lépések a Azure Key Vault](../../key-vault/key-vault-overview.md).
