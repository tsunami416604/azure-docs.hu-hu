---
title: A hbase hbck inkonzisztenciákat ad vissza az Azure HDInsightban
description: A hbase hbck inkonzisztenciákat ad vissza az Azure HDInsightban
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: fa02ac0dfe229f3e82d1c1c62d83ca06a81efca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887325"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Forgatókönyv: `hbase hbck` A parancs inkonzisztenciákat ad vissza az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue-region-is-not-in-hbasemeta"></a>Probléma: A régió nincs`hbase:meta`

Régió xxx a HDFS-en, `hbase:meta` de nem szerepel, vagy nem telepített bármely régió kiszolgálón.

### <a name="cause"></a>Ok

Változik.

### <a name="resolution"></a>Megoldás:

1. Javítsa ki a metatáblát a következő futtatásával:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Régiók hozzárendelése a RegionServers kiszolgálókhoz a következő futtatásával:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Probléma: A régió offline állapotban van

Az xxx régió nincs telepítve egyetlen RegionServer kiszolgálón sem. Ez azt jelenti, `hbase:meta`hogy a régió a , de offline.

### <a name="cause"></a>Ok

Változik.

### <a name="resolution"></a>Megoldás:

A régiók online állapotba hozása a következő futtatásával:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Probléma: A régiók azonos kezdő/záró billentyűvel rendelkeznek

### <a name="cause"></a>Ok

Változik.

### <a name="resolution"></a>Megoldás:

Az átfedő területek manuális egyesítése. Nyissa meg a HBase HMaster Web UI tábla szakaszt, és jelölje ki a table linket, amely a problémát. A táblához tartozó minden régió kezdőkulcsa/zárókulcsa megjelenik. Ezután egyesítse ezeket az átfedő területeket. A HBase shell, nem `merge_region 'xxxxxxxx','yyyyyyy', true`. Példa:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

Ebben a forgatókönyvben egyesítenie kell a RegionA és a RegionC régiót, és a RegionD-t ugyanazzal a kulcstartománysal kell beszereznie, mint a B régiót, majd egyesítenie kell a B régiót és a RegionD-t. Xxxxxxx és yyyyyy a hash string végén minden régió nevét. Legyen óvatos, hogy ne egyesítsen két szakaszos régiót. Minden egyes egyesítés után, például az A és a C egyesítése után a HBase tömörítést indít a RegionD-n. Várja meg, amíg befejeződik a tömörítés, mielőtt újabb egyesítést végezne a RegionD szolgáltatással. A tömörítési állapot az adott régiókiszolgáló lapján a HBase HMaster felhasználói felületén található.

---

## <a name="issue-cant-load-regioninfo"></a>Probléma: Nem lehet betölteni`.regioninfo`

A régiónem `.regioninfo` `/hbase/data/default/tablex/regiony`tölthető be.

### <a name="cause"></a>Ok

Ez valószínűleg a régió részleges törlésének köszönhető, amikor a RegionServer összeomlik vagy a virtuális gép újraindul. Jelenleg az Azure Storage egy sima blob fájlrendszer, és néhány fájlműveletek nem atomi.

### <a name="resolution"></a>Megoldás:

A fennmaradó fájlok és mappák manuális karbantartása:

1. Hajtsa végre, `hdfs dfs -ls /hbase/data/default/tablex/regiony` hogy ellenőrizze, milyen mappák / fájlok még alatta.

1. Végrehajtás `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` az összes gyermekfájl/mappa törléséhez

1. Végrehajtás `hdfs dfs -rmr /hbase/data/default/tablex/regiony` a régiómappa törléséhez.

---

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
