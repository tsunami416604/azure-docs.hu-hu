---
title: Újabb verzióra való frissítési HDInsight-fürt – Azure
description: Ismerje meg, hogyan frissítse HDInsight-fürt egy újabb verzióra.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 266de596633201b5590a6433d078cc05f2a267b4
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994521"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>HDInsight-fürt frissítése újabb verzióra
A legújabb HDInsight-funkciók előnyeit, azt javasoljuk, hogy a HDInsight-fürtök frissíthető-e a legújabb verzióra. Kövesse az alábbi irányelvek frissítése a HDInsight-fürt verziók.

> [!NOTE]  
> A HDInsight a támogatott verziókról további információkért lásd: [HDInsight összetevő verziók](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="upgrade-tasks"></a>Frissítési feladatok
A munkafolyamatot, hogy a HDInsight-fürt frissítése a következőképpen történik.

![Frissítési munkafolyamat diagramja](./media/hdinsight-upgrade-cluster/upgrade-workflow.png)

1. Olvassa el ezt a dokumentumot, megismerheti a módosításokat, amelyek szükségesek lehetnek a HDInsight-fürt frissítésekor minden szakasza.
2. Hozzon létre egy fürtöt egy tesztelési és minőség frissítési garanciát biztosító környezetben. Fürt létrehozásával kapcsolatos további információkért lásd: [megtudhatja, hogyan hozhat létre Linux-alapú HDInsight-fürtök](hdinsight-hadoop-provision-linux-clusters.md)
3. Másolja a meglévő feladatokat, adatok, forrásként és fogadóként az új környezetre. Lásd: [másolási adatokat a tesztelési környezetben](hdinsight-migrate-from-windows-to-linux.md#copy-data-to-the-test-environment) további részletekért.
4. Hajtsa végre az ellenőrzési tesztelés célja annak győződjön meg arról, hogy a feladatok az új fürtön a várt módon működik-e.


Miután ellenőrizte, hogy minden a várt módon működik, az áttelepítés tervezze. A leállás ideje alatt a következő műveleteket hajthatja végre:

1.  Készítsen biztonsági másolatot a fürtcsomópontokon helyileg tárolt átmeneti adatok. Például, ha közvetlenül a központi csomóponton tárolt adatokat.
2.  Törölje a meglévő fürtből.
3.  Hozzon létre egy fürt virtuális hálózat ugyanabban az alhálózatban található legújabb (vagy támogatott) segítségével az ugyanazon alapértelmezett adattár, amely az előző fürtben használt HDI verziójával. Ez lehetővé teszi az új fürt a meglévő éles adataival végzett munka folytatásához.
4.  Biztonsági másolatot készített az átmeneti adatok importálása.
5.  Indítási feladatok/folytatni a használatával az új fürtön.

## <a name="next-steps"></a>További lépések
* [Ismerje meg, hogyan hozhat létre Linux-alapú HDInsight-fürtök](hdinsight-hadoop-provision-linux-clusters.md)
* [Csatlakozás a HDInsighthoz SSH-val](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Az Apache Ambari Linux-alapú fürt kezelése](hdinsight-hadoop-manage-ambari.md)

