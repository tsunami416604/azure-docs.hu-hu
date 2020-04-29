---
title: Fürt átmigrálása újabb verzióra
titleSuffix: Azure HDInsight
description: Útmutató az Azure HDInsight-fürt újabb verzióra való átváltásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: f7198aeff5e9ef6d37e29c2336dc38e4eec0dda1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77023973"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>HDInsight-fürt migrálása egy újabb verzióra

A legújabb HDInsight funkciók kihasználása érdekében javasoljuk, hogy a HDInsight-fürtöket rendszeresen telepítse át a legújabb verzióra. A HDInsight nem támogatja a helyben történő frissítést, ha egy meglévő fürtöt egy újabb verzióra frissítenek. Létre kell hoznia egy új fürtöt a kívánt összetevő-és platform-verzióval, majd át kell telepítenie az alkalmazásokat az új fürt használatára. Az alábbi útmutatást követve áttelepítheti a HDInsight-fürtök verzióját.

> [!NOTE]  
> A HDInsight támogatott verzióival kapcsolatos információkért lásd: a [HDInsight-összetevők verziói](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="migration-tasks"></a>Áttelepítési feladatok

A HDInsight-fürt frissítésének munkafolyamata a következő.
![HDInsight-frissítési munkafolyamat diagramja](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Olvassa el a jelen dokumentum egyes szakaszait, és Ismerje meg, hogy milyen módosítások szükségesek a HDInsight-fürt frissítésekor.
2. Hozzon létre egy fürtöt tesztelési/minőségi megbízhatósági környezetben. A fürtök létrehozásáról további információt a [Linux-alapú HDInsight-fürtök létrehozásának megismerését](hdinsight-hadoop-provision-linux-clusters.md) ismertető témakörben talál.
3. Meglévő feladatok, adatforrások és elsüllyedés másolása az új környezetbe.
4. Az érvényesítési teszt végrehajtásával győződjön meg arról, hogy a feladatok a várt módon működnek az új fürtön.

Miután meggyőződött róla, hogy minden a várt módon működik, ütemezze az áttelepítéshez szükséges állásidőt. A leállás során hajtsa végre a következő műveleteket:

1. A fürt csomópontjain helyileg tárolt átmeneti adattárolás biztonsági mentése. Ha például közvetlenül egy Head csomóponton van tárolva adat.
1. [Törölje a meglévő fürtöt](./hdinsight-delete-cluster.md).
1. Hozzon létre egy fürtöt ugyanabban a VNET-alhálózatban, amely a legújabb (vagy támogatott) HDI-verziót használja ugyanazzal az alapértelmezett adattárral, amelyet az előző fürt használ. Ez lehetővé teszi, hogy az új fürt folytassa a munkát a meglévő termelési adataival.
1. Importálja a biztonsági mentés alatt lévő összes átmeneti adatkészletet.
1. Feladatok indítása/a feldolgozás folytatása az új fürt használatával.

## <a name="workload-specific-guidance"></a>Munkaterhelés-specifikus útmutató

A következő dokumentumok útmutatást nyújtanak bizonyos számítási feladatok áttelepítéséhez:

* [HBase migrálása](./hbase/apache-hbase-migrate-new-version.md)
* [Kafka migrálása](./kafka/migrate-versions.md)
* [Struktúra/interaktív lekérdezés migrálása](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

Az adatbázis biztonsági mentésével és visszaállításával kapcsolatos további információkért lásd: [Az Azure SQL Database helyreállítása automatikus adatbázis-biztonsági mentéssel](../sql-database/sql-database-recovery-using-backups.md).

## <a name="next-steps"></a>További lépések

* [Ismerje meg, hogyan hozhat létre Linux-alapú HDInsight-fürtöket](hdinsight-hadoop-provision-linux-clusters.md)
* [Csatlakozás a HDInsighthoz SSH-val](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Linux-alapú fürt kezelése Apache Ambari használatával](hdinsight-hadoop-manage-ambari.md)
* [HDInsight kibocsátási megjegyzései](./hdinsight-version-release.md)
