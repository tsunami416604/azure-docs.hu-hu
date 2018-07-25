---
title: Újabb verzióra való frissítési HDInsight-fürt – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan frissítse HDInsight-fürt egy újabb verzióra.
services: hdinsight
documentationcenter: ''
author: bhanupr
manager: asadk
editor: bhanupr
ms.assetid: 60eb573c-e639-4815-9fc6-ea8b106d8dbc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: bhanupr
ms.openlocfilehash: 8845a049ebcda59bc0e6fd26618c33f51565e0ca
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225489"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>HDInsight-fürt frissítése újabb verzióra
A legújabb HDInsight-funkciók előnyeit, azt javasoljuk, hogy a HDInsight-fürtök frissíthető-e a legújabb verzióra. Kövesse az alábbi irányelvek frissítése a HDInsight-fürt verziók.

> [!NOTE]
> A HDInsight a támogatott verziókról további információkért lásd: [HDInsight összetevő verziók](hdinsight-component-versioning.md#supported-hdinsight-versions).
>
>

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
* [Csatlakozhat a HDInsight SSH-val](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Linux-alapú fürt kezelése az Ambari](hdinsight-hadoop-manage-ambari.md)

