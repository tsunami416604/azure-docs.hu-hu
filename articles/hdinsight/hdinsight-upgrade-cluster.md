---
title: Egy újabb verzióra frissítés HDInsight-fürt-Azure |} Microsoft Docs
description: Megtudhatja, hogyan kell frissíteni a HDInsight-fürt egy újabb verzióra.
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
ms.openlocfilehash: d6cd0dd8b326f5340e5014bba4babc6dd945744d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>HDInsight-fürt frissítése újabb verzióra
A legújabb HDInsight-funkciók előnyeit, azt javasoljuk, hogy a HDInsight-fürtök legújabb verzióra frissíteni. Kövesse az útmutatást frissítése a HDInsight alatt fürt verziók.

> [!NOTE]
> HDInsight-fürt 3.2-es és 3.3-as verziója hamarosan kivezetési dátum. Információk a HDInsight támogatott verzióján: [HDInsight összetevő verziók](hdinsight-component-versioning.md#supported-hdinsight-versions).
>
>

## <a name="upgrade-tasks"></a>Frissítési feladatok
A munkafolyamat HDInsight-fürt frissítése a következőképpen történik.

![Frissítési munkafolyamat diagramja](./media/hdinsight-upgrade-cluster/upgrade-workflow.png)

1. Olvassa el a módosításokat, amelyek szükségesek lehetnek a HDInsight-fürt frissítésekor megérteni a dokumentum minden szakaszát.
2. Hozzon létre egy fürtöt, a teszt/minőségi megbízhatósági környezetekben. A fürtök létrehozásáról további információk: [útmutató Linux-alapú HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md)
3. Másolja a meglévő feladatokat, az adatforrások és mosdók az új környezetbe. Lásd: [másolási adatok a tesztkörnyezet](hdinsight-migrate-from-windows-to-linux.md#copy-data-to-the-test-environment) további részleteket.
4. Hajtsa végre az ellenőrzés alá vonni, győződjön meg arról, hogy a feladatok az új fürt a várt módon működik-e.


Miután ellenőrizte, hogy minden megfelelően működik-e, az áttelepítés tervezze. Üzemszünet, során a következő műveleteket hajthatja végre:

1.  Készítsen biztonsági másolatot a fürtcsomópontokon helyileg tárolt átmeneti adatok. Ha például közvetlenül egy átjárócsomóponttal tárolt adatokat.
2.  Törölje a meglévő fürtből.
3.  Hozzon létre egy fürtöt ugyanazon virtuális hálózat alhálózatában legújabb (vagy nem támogatott) HDI-verziója alapértelmezett ugyanazon adattár, amelyeket az előző használt használatával. Ez lehetővé teszi, hogy az új fürtön való további munkához a meglévő éles adatok alapján.
4.  Biztonsági másolatot készített az átmeneti adatok importálása.
5.  Kezdő feladatok/folytatni az új fürt segítségével.

## <a name="next-steps"></a>További lépések
* [Ismerje meg a Linux-alapú HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight SSH használatával kapcsolódni](hdinsight-hadoop-linux-use-ssh-unix.md)
* [A Linux-alapú fürt Ambari kezelése](hdinsight-hadoop-manage-ambari.md)

