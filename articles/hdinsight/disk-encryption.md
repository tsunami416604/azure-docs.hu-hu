---
title: Ügyfél által felügyelt kulcs lemezének titkosítása az Azure HDInsight
description: Ez a cikk azt ismerteti, hogyan használható a Azure Key Vault saját titkosítási kulcsa a felügyelt lemezeken tárolt adatok titkosításához az Azure HDInsight-fürtökben.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2019
ms.openlocfilehash: 2c015db828bcbfa8b26f519b3a4707b5ec69b8f3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982497"
---
# <a name="customer-managed-key-disk-encryption"></a>Ügyfél által felügyelt kulcs lemezének titkosítása

Az Azure HDInsight támogatja az ügyfél által felügyelt kulcsokat, más néven Bring Your Own Key (BYOK) titkosítást a felügyelt lemezeken és a HDInsight-fürt virtuális gépekhez csatolt. Ez a funkció lehetővé teszi, hogy a Azure Key Vault segítségével kezelje a HDInsight-fürtökön tárolt adatok védelmét biztosító titkosítási kulcsokat. Előfordulhat, hogy a fürtök egy vagy több mellékelt Azure Storage-fiókkal rendelkeznek, amelyekben a titkosítási kulcsok Microsoft által felügyelt vagy felhasználó által felügyelt, de a titkosítási szolgáltatás eltérő.

Ez a dokumentum nem foglalkozik az Azure Storage-fiókban tárolt adataival. További információ a kiegészítő [csomagról: az Azure Storage titkosítása a nyugalmi állapotban lévő adatokhoz](../storage/common/storage-service-encryption.md).

A HDInsight összes felügyelt lemeze az Azure Storage Service Encryption (SSE) védelemmel van ellátva. Alapértelmezés szerint ezeknek a lemezeknek az adatai a Microsoft által felügyelt kulcsokkal vannak titkosítva. Ha engedélyezi az ügyfél által felügyelt kulcsokat a HDInsight, akkor a HDInsight titkosítási kulcsainak használata és a kulcsok kezelése Azure Key Vault használatával.

Az ügyfél által felügyelt kulcs titkosítása egy egylépéses folyamat, amelyet a fürt létrehozása során a további díjak nélkül kezelnek. Mindössze annyit kell tennie, hogy felügyelt identitásként regisztrálja a HDInsight-t Azure Key Vault és hozzáadja a titkosítási kulcsot a fürt létrehozásakor.

A fürt minden egyes csomópontján található erőforrás-lemez és felügyelt lemez is titkosítva van egy szimmetrikus adattitkosítási kulccsal (ADATTITKOSÍTÁSI kulcsot). A ADATTITKOSÍTÁSI kulcsot a Key encryption Key (KEK) használatával védett a kulcstartóban. A titkosítási és a visszafejtési folyamatokat teljes mértékben az Azure HDInsight kezeli.

A Key vaultban lévő kulcsok biztonságos elforgatásához használhatja a Azure Portal vagy az Azure CLI-t is. Ha egy kulcs forog, a HDInsight-fürt perceken belül megkezdi az új kulcs használatát. Engedélyezze a "Soft Delete" kulcsfontosságú védelmi funkciókat a ransomware-forgatókönyvek és a véletlen törlés elleni védelemhez. A védelmi funkciót nem támogató kulcstartók nem támogatottak.

## <a name="get-started-with-customer-managed-keys"></a>Ismerkedés az ügyfél által felügyelt kulcsokkal

Az ügyfél által felügyelt kulcsokkal rendelkező HDInsight-fürt létrehozásához hajtsa végre a következő lépéseket:

1. Felügyelt identitások létrehozása az Azure-erőforrásokhoz
2. Azure Key Vault és kulcsok beállítása
3. HDInsight-fürt létrehozása az ügyfél által felügyelt kulccsal engedélyezve
4. A titkosítási kulcs elforgatása

## <a name="create-managed-identities-for-azure-resources"></a>Felügyelt identitások létrehozása az Azure-erőforrásokhoz

Ha Key Vault szeretne hitelesíteni, hozzon létre egy felhasználó által hozzárendelt felügyelt identitást a [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), a [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), a [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)vagy az [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)használatával. További információ arról, hogyan működnek a felügyelt identitások az Azure HDInsight-ben: [felügyelt identitások az Azure HDInsight](hdinsight-managed-identities.md). Ügyeljen arra, hogy a felügyelt identitás erőforrás-AZONOSÍTÓját mentse a Key Vault hozzáférési házirendhez való hozzáadásakor.

## <a name="set-up-the-key-vault-and-keys"></a>A Key Vault és a kulcsok beállítása

A HDInsight csak a Azure Key Vaultt támogatja. Ha rendelkezik saját kulcstartóval, a kulcsokat a Azure Key Vaultba importálhatja. Ne feledje, hogy a kulcsoknak "Soft Delete" értékkel kell rendelkezniük. A "Soft Delete" funkció a REST, a .NET/C#, a PowerShell és az Azure CLI felületén keresztül érhető el.

1. Új kulcstartó létrehozásához kövesse az [Azure Key Vault](../key-vault/key-vault-overview.md) rövid útmutatót. A meglévő kulcsok importálásával kapcsolatos további információkért tekintse meg [a kulcsok, titkok és tanúsítványok](../key-vault/about-keys-secrets-and-certificates.md)című témakört.

1. Engedélyezze a "Soft-Delete" parancsot a Key-vaulton az az kulcstartó [Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) CLI parancs használatával.

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. Kulcsok létrehozása.

    a. Új kulcs létrehozásához válassza a **Létrehozás/importálás** elemet a **kulcsok** menüből a **Beállítások**területen.

    ![Új kulcs létrehozása a Azure Key Vaultban](./media/disk-encryption/create-new-key.png "Új kulcs létrehozása a Azure Key Vaultban")

    b. Adja **meg a** kívánt nevet a kulcs **létrehozásához** és megadásához.

    ![kulcs nevének generálása](./media/disk-encryption/create-key.png "Kulcs nevének előállítása")

    c. Válassza ki a kulcsok listájából létrehozott kulcsot.

    ![Key Vault-kulcsok listája](./media/disk-encryption/key-vault-key-list.png)

    d. Ha saját kulcsot használ a HDInsight-fürt titkosításához, meg kell adnia a kulcs URI-JÁT. Másolja a **kulcs azonosítóját** , és mentse valahova, amíg készen nem áll a fürt létrehozására.

    ![kulcs azonosítójának beolvasása](./media/disk-encryption/get-key-identifier.png)

1. Adja hozzá a felügyelt identitást a Key Vault hozzáférési házirendjéhez.

    a. Hozzon létre egy új Azure Key Vault hozzáférési szabályzatot.

    ![Új Azure Key Vault hozzáférési szabályzat létrehozása](./media/disk-encryption/add-key-vault-access-policy.png)

    b. A **rendszerbiztonsági tag kiválasztása**területen válassza ki a létrehozott, felhasználó által hozzárendelt felügyelt identitást.

    ![A rendszerbiztonsági tag kiválasztása Azure Key Vault hozzáférési házirendhez](./media/disk-encryption/add-key-vault-access-policy-select-principal.png)

    c. Kulcs **engedélyeinek** beállítása a **beolvasáshoz**, a **kicsomagoláshoz**és a **becsomagolási kulcshoz**.

    ![Kulcs engedélyeinek beállítása Azure Key Vault hozzáférési Házirend1](./media/disk-encryption/add-key-vault-access-policy-keys.png "Kulcs engedélyeinek beállítása Azure Key Vault hozzáférési Házirend1")

    d. **Titkos engedélyek** beállítása a **beolvasáshoz**, a **beállításhoz**és a **törléshez**.

    ![Kulcs engedélyeinek beállítása Azure Key Vault hozzáférési policy2](./media/disk-encryption/add-key-vault-access-policy-secrets.png "Kulcs engedélyeinek beállítása Azure Key Vault hozzáférési policy2")

    e. Kattintson a **Mentés** gombra.

    ![Azure Key Vault hozzáférési szabályzat mentése](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Fürt létrehozása az ügyfél által felügyelt kulcs lemezének titkosításával

Most már készen áll egy új HDInsight-fürt létrehozására. Az ügyfél által felügyelt kulcs csak az új fürtökön alkalmazható a fürt létrehozása során. Nem lehet eltávolítani a titkosítást az ügyfél által felügyelt kulcstartóból, és az ügyfél által felügyelt kulcs nem adható hozzá a meglévő fürtökhöz.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

A fürt létrehozása során adja meg a teljes kulcs URL-címét, beleértve a kulcs verziószámát is. Például: `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Emellett a felügyelt identitást is hozzá kell rendelnie a fürthöz, és meg kell adnia a kulcs URI-JÁT.

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Az alábbi példa bemutatja, hogyan használható az Azure CLI egy új Apache Spark-fürt létrehozásához, amelyen engedélyezve van a lemezes titkosítás. További információkért tekintse meg az [Azure CLI az hdinsight Create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) dokumentációját.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

## <a name="rotating-the-encryption-key"></a>A titkosítási kulcs elforgatása

Előfordulhat, hogy előfordulhat, hogy a HDInsight-fürt által létrehozott titkosítási kulcsokat módosítani szeretné a létrehozása után. Ez könnyen elvégezhető a portálon keresztül. Ehhez a művelethez a fürtnek hozzá kell férnie az aktuális kulcshoz és a kívánt új kulcshoz, ellenkező esetben az elforgatási kulcs művelete sikertelen lesz.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

A kulcs elforgatásához az új kulcs teljes URL-címével kell rendelkeznie (lásd [a Key Vault és a kulcsok beállításának](#set-up-the-key-vault-and-keys)3. lépését). Ha ezt megtette, nyissa meg a HDInsight-fürt tulajdonságai szakaszt a portálon, majd kattintson a **kulcs módosítása** elemre a **lemez titkosítási kulcsának URL-címe**alatt. Adja meg az új kulcs URL-címét, és küldje el a kulcs elforgatásához.

![lemez titkosítási kulcsának elforgatása](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Az alábbi példa azt szemlélteti, hogyan lehet elforgatni a lemez titkosítási kulcsát egy meglévő HDInsight-fürthöz. További részletekért tekintse meg az [Azure CLI az hdinsight forgatni-Disk-Encryption-Key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key) című témakört.

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Az ügyfél által felügyelt kulcs titkosításával kapcsolatos gyakori kérdések

**Hogyan fér hozzá a HDInsight-fürt a Key vaulthoz?**

A HDInsight a HDInsight-fürthöz társított felügyelt identitás használatával fér hozzá a Azure Key Vault-példányhoz. Ezt a felügyelt identitást a fürt létrehozása előtt vagy közben lehet létrehozni. Emellett a felügyelt identitás elérését is meg kell adnia ahhoz a kulcstartóhoz, ahol a kulcsot tárolják.

**Elérhető ez a funkció a HDInsight összes fürtjén?**

Az ügyfél által felügyelt kulcs titkosítása minden fürt esetében elérhető a Spark 2,1 és a 2,2 kivételével.

**Használhatok több kulcsot a különböző lemezek vagy mappák titkosítására?**

Nem, az összes felügyelt lemezt és erőforrás-lemezt ugyanazzal a kulccsal titkosítja a rendszer.

**Mi történik, ha a fürt elveszti a Key Vault vagy a kulcs elérését?**

Ha a fürt elveszti a kulcs elérését, a figyelmeztetések az Apache Ambari portálon jelennek meg. Ebben az állapotban a **kulcs módosítása** művelet sikertelen lesz. A kulcs-hozzáférés visszaállítása után a Ambari figyelmeztetései elindulnak, és a műveletek, például a kulcsok elforgatása sikeresen elvégezhető.

![kulcs-hozzáférés Ambari riasztása](./media/disk-encryption/ambari-alert.png)

**Hogyan állíthatom helyre a fürtöt a kulcsok törlésekor?**

Mivel a Key vaultban csak a "Soft Delete" engedélyezett kulcsok támogatottak, a fürtnek ismét hozzá kell férnie a kulcsokhoz. Azure Key Vault kulcs helyreállításához tekintse meg a következőt: [Visszavonás-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) vagy [az-kulcstartó-Key-Recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Milyen típusú lemezek vannak titkosítva? Titkosítva vannak-e az operációsrendszer-lemezek és az erőforrások lemezei is?**

Az erőforrás-lemezek és az adat/felügyelt lemezek titkosítva vannak. Az operációsrendszer-lemezek nincsenek titkosítva.

**Ha a fürt felskálázásra kerül, az új csomópontok zökkenőmentesen támogatják az ügyfél által felügyelt kulcsokat?**

Igen. A felskálázás során a fürtnek hozzá kell férnie a Key vaultban lévő kulcshoz. Ugyanez a kulcs használható a fürtben található felügyelt lemezek és erőforrás-lemezek titkosítására is.

**Elérhetők-e az ügyfél által felügyelt kulcsok a saját helyen?**

A HDInsight ügyfél által felügyelt kulcsok az összes nyilvános felhőkben és az országos felhőkben is elérhetők.

## <a name="next-steps"></a>Következő lépések

* [A vállalati biztonság áttekintése az Azure HDInsight](./domain-joined/hdinsight-security-overview.md)
