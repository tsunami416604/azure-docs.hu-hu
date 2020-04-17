---
title: A lemeztitkosítással rendelkező Azure HDInsight-fürtök elveszítik a Key Vault-hozzáférést
description: Az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák lépéseinek és lehetséges megoldásai elhárítása.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: b1d941fbf86d453a56a5157ed988a32173c614fc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461531"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>Eset: A lemeztitkosítással rendelkező Azure HDInsight-fürtök elveszítik a Key Vault-hozzáférést

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Erőforrás-állapotfigyelő központ (RHC) riasztása `The HDInsight cluster is unable to access the key for BYOK encryption at rest`, a Saját kulcs (BYOK) fürtök hozása, ahol a fürtcsomópontok elvesztették a hozzáférést az ügyfelek Key Vault (KV) számára. Hasonló riasztások is láthatók az Apache Ambari felhasználói felületén.

## <a name="cause"></a>Ok

A riasztás biztosítja, hogy a KV elérhető a fürtcsomópontok, ezáltal biztosítva a hálózati kapcsolat, KV állapota és hozzáférési szabályzat a felhasználó által hozzárendelt felügyelt identitás. Ez a riasztás csak egy figyelmeztetés a közelgő közvetítő leállítása a következő csomópont újraindítása, a fürt továbbra is működik, amíg csomópontok újraindítása.

Az Apache Ambari felhasználói felületére talál további információt a **lemeztitkosítási kulcstároló állapota**riasztásról. Ez a riasztás az ellenőrzési hiba okának részleteit tartalmazza.

## <a name="resolution"></a>Megoldás:

### <a name="kvaad-outage"></a>KV/AAD kimaradás

További részletekért tekintse meg az [Azure Key Vault elérhetőségét és redundanciát,](../../key-vault/general/disaster-recovery-guidance.md) valamint az Azure-állapotlapothttps://status.azure.com/

### <a name="kv-accidental-deletion"></a>KV véletlen törlés

* Állítsa vissza a törölt kulcsot a KV-n az automatikus helyreállításhoz. További információ: [Recover Deleted Key](https://docs.microsoft.com/rest/api/keyvault/recoverdeletedkey).
* Érje el a KV csapat, hogy felépüljön a véletlen törlések.

### <a name="kv-access-policy-changed"></a>KV hozzáférési házirend megváltozott

Állítsa vissza a hdi-fürthöz rendelt felügyelt identitáshoz rendelt felhasználó hozzáférési szabályzatait a KV eléréséhez.

### <a name="key-permitted-operations"></a>Kulcsfontosságú engedélyezett műveletek

A KV-ben minden egyes kulcshoz kiválaszthatja az engedélyezett műveletek készletét. Győződjön meg arról, hogy a BYOK kulcshoz engedélyezve van a wrap és a kicsomagolásművelet

### <a name="expired-key"></a>Lejárt kulcs

Ha a lejárati idő lejárt, és a kulcs nem forgatni, visszaállítja a kulcsot a biztonsági mentés HSM vagy lépjen kapcsolatba a KV csapat törölje a lejárati dátumot.

### <a name="kv-firewall-blocking-access"></a>KV tűzfal blokkolja a hozzáférést

Javítsa ki a KV tűzfal beállításait, hogy a BYOK fürtcsomópontok hozzáférhessenek a KV-hoz.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>A virtuális hálózathoz való hozzáférés blokkolására vonatkozó NSG-szabályok

Ellenőrizze a fürthöz csatlakoztatott virtuális hálózathoz társított NSG-szabályokat.

## <a name="mitigation-and-prevention-steps"></a>Kockázatcsökkentési és megelőzési lépések

### <a name="kv-accidental-deletion"></a>KV véletlen törlés

* Konfigurálja a Key Vaultot [erőforrászárolási készlettel.](../../azure-resource-manager/management/lock-resources.md)
* Biztonsági másolatot a hardverbiztonsági modul kulcsairól.

### <a name="key-deletion"></a>Kulcs törlése

A fürtöt a kulcs törlése előtt törölni kell.

### <a name="kv-access-policy-changed"></a>KV hozzáférési házirend megváltozott

Rendszeresen naplózza és tesztelje a hozzáférési házirendeket.

### <a name="expired-key"></a>Lejárt kulcs

* Biztonsági másolatot készíteni a HSM kulcsairól.
* Használjon kulcsot lejárati készlet nélkül.
* Ha be kell állítani a lejárati időt, forgassa el a kulcsokat a lejárati dátum előtt.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
