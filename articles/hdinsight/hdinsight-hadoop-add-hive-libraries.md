---
title: A HDInsight-fürt létrehozása – Azure során adja hozzá a Hive-könyvtárakhoz |} Microsoft Docs
description: Megtudhatja, hogyan Hive-könyvtárakhoz (jar fájlok) hozzáadása egy HDInsight-fürtre a fürt létrehozása során.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 2fd74b8d-c006-45c6-a9e2-72ff5d2d978a
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 71e2859085dc4a9f4fa327d88faff4fecf5108ef
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400656"
---
# <a name="add-custom-hive-libraries-when-creating-your-hdinsight-cluster"></a>Adja hozzá az egyedi Hive-könyvtárakhoz, ha a HDInsight-fürt létrehozása

Útmutató a HDInsight Hive-könyvtárakhoz előzetes betöltése. Ez a dokumentum ismerteti a parancsfájl művelet segítségével előre fürt létrehozása során a függvénykönyvtárak betöltésére. A jelen dokumentum lépéseit követve hozzáadott szalagtárak világszerte elérhető a Hive - használatához nincs szükség [hozzáadása JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) betölteni őket.

## <a name="how-it-works"></a>Működés

A fürt létrehozásakor a parancsfájlművelet segítségével módosíthatja a fürtcsomópontokat, azok létrehozásakor. Ebben a dokumentumban a parancsfájl egyetlen paramétert, amely a szalagtárak helye fogad el. Egy Azure Storage-fiók ezen a helyen kell lennie, és a szalagtárak jar-fájlok formájában kell tárolni.

Fürt létrehozása során a parancsfájl a fájlok enumerálása, másolja őket a `/usr/lib/customhivelibs/` directory head és a munkavégző csomópont, majd hozzáadja őket a `hive.aux.jars.path` tulajdonságot a `core-site.xml` fájlt. Linux-alapú fürtökön is frissíti a `hive-env.sh` fájlt a fájlok helyét.

> [!NOTE]
> Ebben a cikkben a Parancsfájlműveletek segítségével elérhetővé teszi a könyvtárak a következő esetekben:
>
> * **Linux-alapú HDInsight** – Ha a Hive-ügyfél használatával **WebHCat**, és **hiveserver2-n**.
> * **Windows-alapú HDInsight** – Ha a Hive-ügyfélprogrammal és **WebHCat**.

## <a name="the-script"></a>A parancsfájl

**Parancsfájl helyét**

A **Linux-alapú fürtökön**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

A **Windows-alapú fürtök**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

**Követelmények**

* A parancsfájlok egyaránt telepíteni kell a **Átjárócsomópontokat** és **munkavégző csomópontokhoz**.

* A telepíteni kívánt JAR-fájlok kivételével az Azure Blob Storage tárolóban kell tárolni egy **egyetlen tároló**.

* A tárfiók tartalmazó jar-fájlok a könyvtárban **kell** is csatolva van a HDInsight-fürt létrehozása során. Azt az alapértelmezett tárfiókot kell lennie, vagy a fiók hozzáadva __opcionális konfigurációs__.

* A tároló WASB elérési útja meg kell adni a Script Action paramétereként. Például, ha a JAR-fájlok kivételével nevű tárolóban vannak tárolva **függvénytárak** egy tárfiókon nevű **mystorage**, a paraméter lenne **wasb://libs@mystorage.blob.core.windows.net/**.

  > [!NOTE]
  > Jelen dokumentum céljából feltételezzük, hogy már létrehozott egy tárfiókot, a blob tároló, és a fájlok fel.
  >
  > Ha nem hozott létre egy tárfiókot, ezért a végezhető el a [Azure-portálon](https://portal.azure.com). Ezután egy segédprogramot használhatja például a [Azure Tártallózó](http://storageexplorer.com/) tárolókat hozhat létre, a fiók és a fájlok feltöltése.

## <a name="create-a-cluster-using-the-script"></a>Hozzon létre egy fürtöt, a parancsfájl használatával

> [!NOTE]
> Az alábbi lépéseket egy Linux-alapú HDInsight-fürtök létrehozása. A Windows-alapú fürtök létrehozásához válasszon **Windows** a fürt létrehozása a fürt és a Windows (PowerShell) a bash parancsfájlok helyett parancsfájl használata esetén operációs rendszer.
>
> Azure PowerShell vagy a HDInsight .NET SDK használatával is hozzon létre egy fürtöt, a parancsfájl. A következő módszerekkel további információkért lásd: [testreszabása HDInsight-fürtök parancsfájlműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

1. Indítsa el a fürt kiépítése a lépések segítségével [Provision HDInsight-fürtök Linux rendszeren](hdinsight-hadoop-provision-linux-clusters.md), de kiépítése nem fejeződött be.

2. Az a **opcionális konfigurációs** szakaszban jelölje be **Parancsfájlműveletek**, és adja meg a következő információkat:

   * **NÉV**: Adja meg a parancsfájlművelet rövid nevét.

   * **PARANCSFÁJL URI AZONOSÍTÓJA**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh

   * **HEAD**: ezt a beállítást.

   * **MUNKAVÉGZŐ**: ezt a beállítást.

   * **ZOOKEEPER**: hagyja üresen a mezőt.

   * **PARAMÉTEREK**: a WASB címet adjon meg a tároló és a storage-fiókhoz, amely tartalmazza a JAR-fájlok kivételével. Például **wasb://libs@mystorage.blob.core.windows.net/**.

3. Alján a **Parancsfájlműveletek**, használja a **válasszon** gombra kattintva mentse a konfigurációt.

4. Az a **opcionális konfigurációs** szakaszban jelölje be **a társított Tárfiókokban** válassza ki a **tárolási kulcs hozzáadása** hivatkozásra. Válassza ki a tárfiók, amely tartalmazza a JAR-fájlok kivételével. Ezután a **válasszon** gombok beállítások mentéséhez, és térjen vissza a **opcionális konfigurációs**.

5. A választható konfiguráció mentéséhez, használja a **kiválasztása** gomb alján a **opcionális konfigurációs** szakasz.

6. Továbbra is a fürt kiépítése a [Provision HDInsight-fürtök Linux rendszeren](hdinsight-hadoop-provision-linux-clusters.md).

Miután befejeződött a fürt létrehozása, le is tudja használni a JAR-fájlok kivételével ez a parancsfájl hozzáadva a Hive használata nélkül a `ADD JAR` utasítást.

## <a name="next-steps"></a>További lépések

Hive munkavégzés további információkért lásd: [hdinsight Hive használata](hadoop/hdinsight-use-hive.md)
