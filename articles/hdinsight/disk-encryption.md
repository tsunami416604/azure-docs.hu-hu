---
title: Ügyfél által felügyelt kulcs lemezének titkosítása az Azure HDInsight
description: Ez a cikk azt ismerteti, hogyan használható a Azure Key Vault saját titkosítási kulcsa a felügyelt lemezeken tárolt adatok titkosításához az Azure HDInsight-fürtökben.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c22ee0ef0393c0dae64674d18bae5a2e92969b4c
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206058"
---
# <a name="customer-managed-key-disk-encryption"></a>Ügyfél által felügyelt kulcson alapuló lemeztitkosítás

Az Azure HDInsight támogatja az ügyfél által felügyelt kulcs titkosítását a felügyelt lemezeken és a HDInsight-fürt virtuális gépei számára csatolt erőforrás-lemezeken. Ez a funkció lehetővé teszi, hogy a Azure Key Vault segítségével kezelje a HDInsight-fürtökön tárolt adatok védelmét biztosító titkosítási kulcsokat. 

A HDInsight összes felügyelt lemeze az Azure Storage Service Encryption (SSE) védelemmel van ellátva. Alapértelmezés szerint ezeknek a lemezeknek az adatai a Microsoft által felügyelt kulcsokkal vannak titkosítva. Ha engedélyezi az ügyfél által felügyelt kulcsokat a HDInsight, akkor a HDInsight titkosítási kulcsainak használata és a kulcsok kezelése Azure Key Vault használatával.

Ez a dokumentum nem foglalkozik az Azure Storage-fiókban tárolt adataival. Az Azure Storage encryption szolgáltatással kapcsolatos további információkért lásd: [Az Azure Storage titkosítása inaktív adatokhoz](../storage/common/storage-service-encryption.md). Előfordulhat, hogy a fürtök egy vagy több mellékelt Azure Storage-fiókkal rendelkeznek, amelyekben a titkosítási kulcsok Microsoft által felügyelt vagy felhasználó által felügyelt, de a titkosítási szolgáltatás eltérő.

## <a name="introduction"></a>Introduction (Bevezetés)

Az ügyfél által felügyelt kulcs titkosítása egy egylépéses folyamat, amelyet a fürt létrehozása során a további díjak nélkül kezelnek. Mindössze annyit kell tennie, hogy felügyelt identitásként regisztrálja a HDInsight-t Azure Key Vault és hozzáadja a titkosítási kulcsot a fürt létrehozásakor.

A fürt minden egyes csomópontján található erőforrás-lemez és felügyelt lemez is titkosítva van egy szimmetrikus adattitkosítási kulccsal (ADATTITKOSÍTÁSI kulcsot). A ADATTITKOSÍTÁSI kulcsot a Key encryption Key (KEK) használatával védett a kulcstartóban. A titkosítási és a visszafejtési folyamatokat teljes mértékben az Azure HDInsight kezeli.

Ha a Key Vault-tűzfal engedélyezve van azon a kulcstartón, amelyen a lemez titkosítási kulcsa tárolva van, a HDInsight regionális erőforrás-szolgáltatói IP-címeit hozzá kell adni ahhoz a régióhoz, ahol a fürtöt telepíteni kívánja. Erre azért van szükség, mert a HDInsight nem megbízható Azure Key Vault-szolgáltatás.

A Key vaultban lévő kulcsok biztonságos elforgatásához használhatja a Azure Portal vagy az Azure CLI-t is. Ha egy kulcs forog, a HDInsight-fürt perceken belül megkezdi az új kulcs használatát. A ransomware-forgatókönyvek és a véletlen törlés elleni védelem érdekében engedélyezze a kulcs nélküli [törlési](../key-vault/key-vault-ovw-soft-delete.md) funkciók védelmét. A védelmi funkciót nem támogató kulcstartók nem támogatottak.

|Fürt típusa |OPERÁCIÓSRENDSZER-lemez (felügyelt lemez) |Adatlemez (felügyelt lemez) |Ideiglenes adatlemez (helyi SSD) |
|---|---|---|---|
|Kafka, HBase gyorsított írásokkal|SSE titkosítás|SSE titkosítás + opcionális CMK-titkosítás|Nem kötelező CMK-titkosítás|
|Minden más fürt (Spark, Interactive, Hadoop, gyorsított írások nélkül HBase)|SSE titkosítás|N/A|Nem kötelező CMK-titkosítás|

## <a name="get-started-with-customer-managed-keys"></a>Ismerkedés az ügyfél által felügyelt kulcsokkal

Az ügyfél által felügyelt kulcsokkal rendelkező HDInsight-fürt létrehozásához hajtsa végre a következő lépéseket:

1. Felügyelt identitások létrehozása az Azure-erőforrásokhoz
1. Azure Key Vault létrehozása
1. Kulcs létrehozása
1. Hozzáférési szabályzat létrehozása
1. HDInsight-fürt létrehozása az ügyfél által felügyelt kulccsal engedélyezve
1. A titkosítási kulcs elforgatása

## <a name="create-managed-identities-for-azure-resources"></a>Felügyelt identitások létrehozása az Azure-erőforrásokhoz

Felhasználó által hozzárendelt felügyelt identitás létrehozása a Key Vault való hitelesítéshez.

Lásd: [felhasználó által hozzárendelt felügyelt identitás létrehozása](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) adott lépésekhez. További információ arról, hogyan működnek a felügyelt identitások az Azure HDInsight-ben: [felügyelt identitások az Azure HDInsight](hdinsight-managed-identities.md). Ügyeljen arra, hogy a felügyelt identitás erőforrás-AZONOSÍTÓját mentse a Key Vault hozzáférési házirendhez való hozzáadásakor.

## <a name="create-azure-key-vault"></a>Azure Key Vault létrehozása

Kulcstartó létrehozása. Lásd: [Azure Key Vault létrehozása](../key-vault/quick-create-portal.md) adott lépésekhez.

A HDInsight csak a Azure Key Vaultt támogatja. Ha rendelkezik saját kulcstartóval, a kulcsokat a Azure Key Vaultba importálhatja. Ne feledje, hogy a kulcstartónak engedélyezve kell lennie az **ideiglenes törlésnek** . A meglévő kulcsok importálásával kapcsolatos további információkért tekintse meg [a kulcsok, titkok és tanúsítványok](../key-vault/about-keys-secrets-and-certificates.md)című témakört.

## <a name="create-key"></a>Kulcs létrehozása

1. Az új kulcstartóban navigáljon a **beállítások** > **kulcsok** >  **+ Létrehozás/importálás**elemre.

    ![Új kulcs létrehozása a Azure Key Vaultban](./media/disk-encryption/create-new-key.png "Új kulcs létrehozása a Azure Key Vaultban")

1. Adjon meg egy nevet, majd válassza a **Létrehozás**lehetőséget. Őrizze meg az **RSA**alapértelmezett **kulcs típusát** .

    ![kulcs nevének generálása](./media/disk-encryption/create-key.png "Kulcs nevének előállítása")

1. Amikor visszatér a **kulcsok** lapra, válassza ki a létrehozott kulcsot.

    ![Key Vault-kulcsok listája](./media/disk-encryption/key-vault-key-list.png)

1. Válassza ki a verziót, és nyissa meg a **kulcs verziója** lapot. Ha saját kulcsot használ a HDInsight-fürt titkosításához, meg kell adnia a kulcs URI-JÁT. Másolja a **kulcs azonosítóját** , és mentse valahova, amíg készen nem áll a fürt létrehozására.

    ![kulcs azonosítójának beolvasása](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>Hozzáférési szabályzat létrehozása

1. Az új kulcstartóban navigáljon a **beállítások** > **hozzáférési házirendek** >  **+ hozzáférési házirend hozzáadása**lehetőségre.

    ![Új Azure Key Vault hozzáférési szabályzat létrehozása](./media/disk-encryption/key-vault-access-policy.png)

1. A **hozzáférési szabályzat hozzáadása** lapon adja meg a következő információkat:

    |Tulajdonság |Leírás|
    |---|---|
    |Kulcs engedélyei|Válassza a **lekérés**, a **kicsomagolási kulcs**és a **becsomagolási kulcs**lehetőséget.|
    |Titkos engedélyek|Válassza a **beolvasás**, **beállítás**és **Törlés**lehetőséget.|
    |Rendszerbiztonsági tag kiválasztása|Válassza ki a korábban létrehozott felhasználó által hozzárendelt felügyelt identitást.|

    ![A rendszerbiztonsági tag kiválasztása Azure Key Vault hozzáférési házirendhez](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Válassza a **Hozzáadás** lehetőséget.

1. Kattintson a **Mentés** gombra.

    ![Azure Key Vault hozzáférési szabályzat mentése](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Fürt létrehozása az ügyfél által felügyelt kulcs lemezének titkosításával

Most már készen áll egy új HDInsight-fürt létrehozására. Az ügyfél által felügyelt kulcs csak az új fürtökön alkalmazható a fürt létrehozása során. Nem lehet eltávolítani a titkosítást az ügyfél által felügyelt kulcstartóból, és az ügyfél által felügyelt kulcs nem adható hozzá a meglévő fürtökhöz.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

A fürt létrehozása során adja meg a teljes **kulcs azonosítóját**, beleértve a kulcs verziószámát is. Például: `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Emellett a felügyelt identitást is hozzá kell rendelnie a fürthöz, és meg kell adnia a kulcs URI-JÁT.

![Új fürt létrehozása](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Az alábbi példa bemutatja, hogyan használható az Azure CLI egy új Apache Spark-fürt létrehozásához, amelyen engedélyezve van a lemezes titkosítás. További információ: [Azure CLI az hdinsight Create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create).

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

A kulcs elforgatásához szüksége lesz az alapkulcs tárolójának URI-ra. Ha ezt megtette, nyissa meg a HDInsight-fürt tulajdonságai szakaszt a portálon, majd kattintson a **kulcs módosítása** elemre a **lemez titkosítási kulcsának URL-címe**alatt. Adja meg az új kulcs URL-címét, és küldje el a kulcs elforgatásához.

![lemez titkosítási kulcsának elforgatása](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Az alábbi példa azt szemlélteti, hogyan lehet elforgatni a lemez titkosítási kulcsát egy meglévő HDInsight-fürthöz. További információ: [Azure CLI az hdinsight forgatás-Disk-Encryption-Key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key).

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

* További információ a Azure Key Vaultről: [Mi az Azure Key Vault](../key-vault/key-vault-overview.md).
* [A vállalati biztonság áttekintése az Azure HDInsight-ben](./domain-joined/hdinsight-security-overview.md).
