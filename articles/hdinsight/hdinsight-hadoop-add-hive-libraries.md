---
title: Hive-kódtárak hozzáadása során a HDInsight-fürt létrehozása – Azure
description: Ismerje meg, hogyan adja hozzá a Hive-kódtárak (jar fájlok) HDInsight-fürthöz a fürt létrehozásakor.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: jasonh
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: fe7cb2c3990a5aa161665519490ede90be04e1ee
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594607"
---
# <a name="add-custom-hive-libraries-when-creating-your-hdinsight-cluster"></a>A HDInsight-fürt létrehozásakor egyéni Hive-kódtárak hozzáadása

Útmutató a HDInsight Hive-kódtárak előzetes betöltése. Ez a dokumentum információkat tartalmaz az szkriptműveletekkel előre a fürt létrehozásakor a függvénykönyvtárak betöltésére. Kódtárakkal kiegészítve, ez a dokumentum lépéseivel globálisan elérhető a Hive - használatához nem kell [hozzáadása JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) betölteni azokat.

## <a name="how-it-works"></a>Működés

A fürt létrehozásakor Szkriptműveletet segítségével módosíthatja a fürtcsomópontok azok létrehozásakor. Ebben a dokumentumban a szkript egyetlen paramétert, és a szalagtárak helye fogad el. Ezen a helyen kell lennie az Azure Storage-fiókban, és a szalagtárak jar-fájlként kell tárolni.

Fürt létrehozása során, a szkriptet a fájlok enumerálása, másolja őket a `/usr/lib/customhivelibs/` könyvtár a fő- és munkavégző csomóponton, majd hozzáadja őket a `hive.aux.jars.path` tulajdonságot a `core-site.xml` fájlt. Linux-alapú fürtökön is frissíti a `hive-env.sh` fájlt a fájlok helyét.

> [!NOTE]
> Ebben a cikkben a Parancsfájlműveletek segítségével elérhetővé teszi a könyvtárak a következő esetekben:
>
> * **Linux-alapú HDInsight** – Ha egy Hive-ügyfél használatával **WebHCat**, és **hiveserver2-n keresztül**.
> * **Windows-alapú HDInsight** – Ha a Hive-ügyfél használatával és **WebHCat**.

## <a name="the-script"></a>A parancsfájl

**A parancsprogram helye**

A **Linux-alapú fürtök**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

A **Windows-alapú fürtök**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

**Követelmények**

* A parancsfájlokat egyszerre kell alkalmazni a **Átjárócsomópontokhoz** és **munkavégző csomópontok**.

* Az Azure Blob Storage-ban kell tárolni a JAR-fájlok kivételével szeretne telepíteni egy **egyetlen tároló**.

* A storage-fiókot tartalmazó jar-fájlok a könyvtárban **kell** csatolható a HDInsight-fürt létrehozása során. Azt kell az alapértelmezett tárfiókot, vagy egy fiók során hozzáadott __opcionális konfigurációs__.

* A WASB-elérési út a tárolóhoz, a Script Action paraméterként kell megadni. Például, ha a JAR-fájlok kivételével nevű tárolóban tárolt **libs** a storage-fiók nevű **mystorage**, a paraméter lehet **wasb://libs@mystorage.blob.core.windows.net/**.

  > [!NOTE]
  > Jelen dokumentum céljából feltételezzük, hogy már létrehozott egy tárfiókot, a blob-tárolóba, és feltölti a fájlokat.
  >
  > Ha nem létrehozott egy tárfiókot, megteheti úgy keresztül a [az Azure portal](https://portal.azure.com). Ezután egy segédprogramot használhatja például [Azure Storage Explorer](http://storageexplorer.com/) , hozzon létre egy tárolót a fiókban, és fájlokat tölthet fel azt.

## <a name="create-a-cluster-using-the-script"></a>Fürt létrehozása a parancsfájl használatával

> [!NOTE]
> Az alábbi lépéseket egy Linux-alapú HDInsight-fürt létrehozásához. Válassza ki a Windows-alapú fürt létrehozása **Windows** a fürt operációs rendszer, amikor létrehozza a fürtöt, és használja a Windows (PowerShell) helyett a bash-szkript futtatása.
>
> Azure PowerShell vagy a HDInsight .NET SDK használatával is hozzon létre egy fürtöt, ez a szkript használatával. Ezek a módszerek használatáról további információkért lásd: [testreszabása HDInsight fürtök parancsfájlműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

1. Indítsa el a fürt kiépítése a lépéseket követve [Provision HDInsight-fürtök a linuxon futó](hdinsight-hadoop-provision-linux-clusters.md), azonban nem fejeződnek be kiépítése.

2. Az a **opcionális konfigurációs** szakaszban jelölje be **Parancsfájlműveletek**, és adja meg a következő információkat:

   * **NÉV**: Adjon egy rövid nevet a parancsprogram-művelet.

   * **SZKRIPT URI**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh

   * **A fő**: ezt a beállítást választva.

   * **FELDOLGOZÓ**: ezt a beállítást választva.

   * **ZOOKEEPER**: hagyja üresen a mezőt.

   * **PARAMÉTEREK**: Adja meg a WASB-címet a tároló és a storage-fiókhoz, amely tartalmazza a JAR-fájlok kivételével. Például: **wasb://libs@mystorage.blob.core.windows.net/**.

3. Alsó részén a **Parancsfájlműveletek**, használja a **kiválasztása** gombra a konfiguráció mentéséhez.

4. Az a **opcionális konfigurációs** szakaszban jelölje be **a társított Tárfiókokban** , és válassza ki a **tárkulcs hozzáadása** hivatkozásra. Válassza ki a tárfiókot, amely tartalmazza a JAR-fájlok kivételével. Ezután a **válassza** gombok beállítások mentéséhez és a **opcionális konfigurációs**.

5. A választható konfiguráció mentéséhez, használja a **kiválasztása** gomb alsó részén a **opcionális konfigurációs** szakaszban.

6. A fürt kiépítése a leírtak szerint folytassa [Provision HDInsight-fürtök a linuxon futó](hdinsight-hadoop-provision-linux-clusters.md).

Ha befejezte a fürt létrehozása, Ön a JAR-fájlok kivételével ez a szkript keresztül hozzáadva a Hive használata nélkül használhatják az `ADD JAR` utasítást.

## <a name="next-steps"></a>További lépések

Hive való használatáról további információkért lásd: [Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
