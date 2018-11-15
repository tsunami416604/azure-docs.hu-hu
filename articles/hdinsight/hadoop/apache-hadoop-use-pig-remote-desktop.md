---
title: Az Apache Pig használata a távoli asztalon keresztül a HDInsight – Azure
description: Ismerje meg, a Pig parancs használata a távoli asztali kapcsolatáról Apache Pig Latin utasításokkal futtatnia a HDInsight egy Windows-alapú Apache Hadoop-fürtöt.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 9ab3c5399263e301760b7822488fe00145149d2a
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633342"
---
# <a name="run-apache-pig-jobs-from-a-remote-desktop-connection"></a>A távoli asztali kapcsolat Apache Pig-feladatok futtatása
[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Ez a dokumentum egy forgatókönyv biztosít az Apache Pig paranccsal futtassa a Pig Latin utasításokkal egy távoli asztali kapcsolaton keresztül egy Windows-alapú HDInsight-fürthöz. A Pig Latin adatátalakítások, azok MapReduce-alkalmazások létrehozása helyett leképezéséhez, és csökkentheti a functions teszi lehetővé.

> [!IMPORTANT]
> A távoli asztal csak akkor használható a Windows operációs rendszert használó HDInsight-fürtökön. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> A HDInsight 3.4 vagy újabb, lásd: [a Pig használata a HDInsight és az SSH](apache-hadoop-use-pig-ssh.md) információk interaktív módon Pig-feladatok közvetlenül a fürtön futó a parancssorból.

## <a id="prereq"></a>Előfeltételek
A jelen cikkben ismertetett lépések elvégzéséhez a következőkre lesz szüksége.

* A Windows-alapú HDInsight (Hadoop on HDInsight)-fürt
* A Windows 10, Windows 8 vagy Windows 7 rendszert futtató ügyfélszámítógép

## <a id="connect"></a>Csatlakozzon a távoli asztallal
A távoli asztal engedélyezése a HDInsight-fürt, majd csatlakozhat az utasítások szerint [csatlakozhat a HDInsight-fürtök RDP-vel](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="pig"></a>A Pig-parancs használata
1. Miután egy távoli asztali kapcsolat, indítsa el a **Hadoop parancssor** az ikonra az asztalon használatával.
2. A következő segítségével indítsa el a Pig-parancsot:

        %pig_home%\bin\pig

    Megjelenik egy `grunt>` parancssort.
3. Adja meg a következő utasítást:

        LOGS = LOAD 'wasb:///example/data/sample.log';

    Ez a parancs a sample.log fájlt tartalmát betölti a naplók fájlt. A fájl tartalmát a következő paranccsal tekintheti meg:

        DUMP LOGS;
4. Az adatok átalakítása alkalmazása csak a naplózási szint kinyerésére minden rekord egy reguláris kifejezést:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Használhat **DUMP** az átalakítás után az adatok megtekintéséhez. Ebben az esetben `DUMP LEVELS;`.
5. Továbbra is az átalakítások alkalmazása a következő utasítások használatával. Használat `DUMP` az átalakítás minden lépése után az eredmény megtekintéséhez.

    <table>
    <tr>
    <th>Utasítás</th><th>Művelet</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = szűrő szintek LOGLEVEL szerint nem null;</td><td>A naplózási szint megadásához null értéket tartalmazó sorok eltávolítja, és tárolja az eredményeket FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS által LOGLEVEL; csoport FILTEREDLEVELS =</td><td>A sorok naplózási szint szerint csoportosítja, és tárolja az eredményeket GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>GYAKORISÁGOK = foreach GROUPEDLEVELS csoport létrehozásához, LOGLEVEL, COUNT (FILTEREDLEVELS. LOGLEVEL), COUNT;</td><td>Létrehoz egy új csoportot, amely tartalmazza az egyes egyedi naplózási adatok szintű hatékonyságot, és hány alkalommal történik. A tárolás GYAKORISÁGOT be</td>
    </tr>
    <tr>
    <td>EREDMÉNY sorrend szerint száma desc; GYAKORISÁGOK =</td><td>A naplózási szintek orders (csökkenő) száma szerint, és tárolja az eredmény</td>
    </tr>
</table>

6. Az eredményeket az átalakítás használatával is mentheti a `STORE` utasítást. Például a következő parancsot a menti a `RESULT` , a **/example/data/pigout** könyvtárat a az alapértelmezett tárolókat a fürt számára:

        STORE RESULT into 'wasb:///example/data/pigout'

   > [!NOTE]
   > Az adatok fájlban található a megadott könyvtárban tárolt **. rész – nnnnn**. Ha a könyvtár már létezik, egy hibaüzenetet fog kapni.
   >
   >
   
7. Lépjen ki a grunt a rendszer kéri, adja meg a következő utasítást.

        QUIT;

### <a name="pig-latin-batch-files"></a>A Pig Latin kötegfájlok
A Pig-parancs segítségével is futtathat, amelyek egy fájl tartalmazza a Pig Latin.

1. Után kilép a grunt parancssorban, nyisson meg **Jegyzettömb** , és hozzon létre egy új fájlt **pigbatch.pig** a a **PIG_HOME %** könyvtár.
2. Írja be vagy illessze be a következő sorokat a **pigbatch.pig** fájlt, és mentse azt:

        LOGS = LOAD 'wasb:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;
3. Használja az alábbi parancsot futtassa a **pigbatch.pig** fájlt, a pig-parancs segítségével.

        pig %PIG_HOME%\pigbatch.pig

    A batch-feladat befejezését követően megjelenik az alábbi kimenet, amely lehet ugyanaz, mint amikor használt `DUMP RESULT;` az előző lépésekben:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="summary"></a>Summary (Összefoglalás)
Amint láthatja, a Pig parancs lehetővé teszi interaktív módon MapReduce műveletek futtatásához, vagy egy batch-fájlban tárolt, a Pig Latin-feladatok futtatása.

## <a id="nextsteps"></a>Következő lépések
A HDInsight Pig általános tájékoztatást:

* [A Pig használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-pig.md)

Egyéb módjaival kapcsolatos további információk a HDInsight Hadoop-keretrendszerrel használhatja:

* [A Hive használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight Hadoop](hdinsight-use-mapreduce.md)
