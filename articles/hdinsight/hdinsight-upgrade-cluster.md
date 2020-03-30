---
title: Fürt áttelepítése újabb verzióra
titleSuffix: Azure HDInsight
description: Ismerje meg az Azure HDInsight-fürt újabb verzióra való áttelepítésének irányelveit.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: f7198aeff5e9ef6d37e29c2336dc38e4eec0dda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023973"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>A HDInsight-fürt áttelepítése újabb verzióra

A legújabb HDInsight-szolgáltatások előnyeinek kihasználása érdekében azt javasoljuk, hogy a HDInsight-fürtöket rendszeresen telepítse át a legújabb verzióra. A HDInsight nem támogatja a helybeni frissítéseket, ha egy meglévő fürt újabb összetevőverzióra frissül. Létre kell hoznia egy új fürtöt a kívánt összetevővel és platformverzióval, majd át kell telepítenie az alkalmazásokat az új fürt használatához. A HDInsight-fürtverzióinak áttelepítéséhez kövesse az alábbi irányelveket.

> [!NOTE]  
> A HDInsight támogatott verzióiról a [HDInsight összetevő-verziói](hdinsight-component-versioning.md#supported-hdinsight-versions)című témakörben talál további információt.

## <a name="migration-tasks"></a>Áttelepítési feladatok

A HDInsight-fürt frissítéséhez szükséges munkafolyamat a következő.
![HDInsight-frissítési munkafolyamat-diagram](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Olvassa el a dokumentum egyes szakaszait, és ismerje meg a HDInsight-fürt frissítésekor esetleg szükséges módosításokat.
2. Hozzon létre egy fürtöt teszt-/minőségbiztosítási környezetként. A fürt létrehozásáról további [információ: Információ a Linux-alapú HDInsight-fürtök létrehozásáról](hdinsight-hadoop-provision-linux-clusters.md)
3. Másolja a meglévő feladatokat, adatforrásokat és fogadókat az új környezetbe.
4. Ellenőrzési tesztelés végrehajtásával győződjön meg arról, hogy a feladatok a várt módon működnek az új fürtön.

Miután meggyőződött arról, hogy minden a várt módon működik, ütemezze az áttelepítés állásidejét. Az állásidő alatt tegye a következő műveleteket:

1. A fürtcsomópontokon helyileg tárolt átmeneti adatok biztonsági és biztonsági másolatot. Ha például közvetlenül a fő csomóponton tárolt adatokat.
1. [A meglévő fürt törlése](./hdinsight-delete-cluster.md).
1. Hozzon létre egy fürtöt ugyanabban a virtuális hálózat alhálózatában a legújabb (vagy támogatott) HDI-verzióval, ugyanazt az alapértelmezett adattort használva, amelyet az előző fürt használt. Ez lehetővé teszi, hogy az új fürt továbbra is a meglévő termelési adatokkal való munkát folytassa.
1. Importálja a biztonsági másolatot.
1. Indítsa el a feladatokat/folytassa a feldolgozást az új fürt használatával.

## <a name="workload-specific-guidance"></a>Munkaterhelés-specifikus útmutatás

Az alábbi dokumentumok útmutatást nyújtanak az egyes munkaterhelések áttelepítéséhez:

* [HBase áttelepítése](./hbase/apache-hbase-migrate-new-version.md)
* [Kafka áttelepítése](./kafka/migrate-versions.md)
* [Hive/interaktív lekérdezés áttelepítése](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

Az adatbázisok biztonsági mentéséről és visszaállításáról az [Azure SQL-adatbázis helyreállítása automatikus adatbázis-biztonsági mentések használatával című témakörben](../sql-database/sql-database-recovery-using-backups.md)talál további információt.

## <a name="next-steps"></a>További lépések

* [Ismerje meg, hogyan hozhat létre Linux-alapú HDInsight-fürtöket](hdinsight-hadoop-provision-linux-clusters.md)
* [Csatlakozás a HDInsighthoz SSH-val](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Linux alapú fürt kezelése apache ambari használatával](hdinsight-hadoop-manage-ambari.md)
* [HDInsight kiadási megjegyzések](./hdinsight-version-release.md)
