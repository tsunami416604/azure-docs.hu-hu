---
title: Hadoop a Pig használata a HDInsight - Azure távoli asztal |} Microsoft Docs
description: Ismerje meg, a Pig parancs használata a távoli asztali kapcsolat egy Windows-alapú Hadoop-fürt hdinsightban történő Pig Latin utasítás futtatását.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e034a286-de0f-465f-8bf1-3d085ca6abed
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: f293aab67fb5e952dc32e20a6370d45cded682b5
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "35761997"
---
# <a name="run-pig-jobs-from-a-remote-desktop-connection"></a>A távoli asztali kapcsolat-ről futtatva a Pig-feladatokhoz
[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Ez a dokumentum egy forgatókönyv biztosít a Pig paranccsal futtassa a Pig Latin utasításokat egy távoli asztali kapcsolaton keresztül egy Windows-alapú HDInsight-fürthöz. A Pig Latin adatátalakítást, azok MapReduce-alkalmazások létrehozása helyett hozzárendelését és funkciók teszi lehetővé.

> [!IMPORTANT]
> A távoli asztal a Windows operációs rendszert használó HDInsight-fürtök csak érhető el. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> HDInsight 3.4-es vagy nagyobb, lásd: [HDInsight és az SSH használata a Pig](apache-hadoop-use-pig-ssh.md) információk interaktív Pig-feladatokhoz közvetlenül a fürtön futó parancsot a parancssorból.

## <a id="prereq"></a>Előfeltételek
Ebben a cikkben szereplő lépések elvégzéséhez a következőkre lesz szüksége.

* (A HDInsight Hadoop) a Windows-alapú HDInsight-fürt
* Windows 10, Windows 8 vagy Windows 7 rendszert futtató ügyfélszámítógép

## <a id="connect"></a>Csatlakozzon a távoli asztal
A távoli asztal engedélyezése a HDInsight-fürthöz, majd csatlakozni a következő utasításokat követve [csatlakozás RDP Funkciót használnak a HDInsight-fürtök](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="pig"></a>A Pig paranccsal
1. A távoli asztali kapcsolattal rendelkezik, után indítsa el a **Hadoop parancssori** ikonnal az asztalon.
2. A Pig parancs indítását. használja a következőket:

        %pig_home%\bin\pig

    Meg fog jelenni a `grunt>` kérdés.
3. Adja meg a következő utasítást:

        LOGS = LOAD 'wasb:///example/data/sample.log';

    Ez a parancs a sample.log fájl tartalmát betölti a naplók fájlba. A fájl tartalmát a következő parancs használatával tekintheti meg:

        DUMP LOGS;
4. Az adatok átalakítása úgy, hogy csak a naplózási szint kibontani rekordokban reguláris kifejezést alkalmazza:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Használhat **DUMP** az átalakítás után az adatok megtekintéséhez. Ebben az esetben `DUMP LEVELS;`.
5. További, átalakítások alkalmazása a következő utasítások használatával. Használjon `DUMP` az átalakítás lépések után az eredmények megjelenítéséhez.

    <table>
    <tr>
    <th>Utasítás</th><th>Művelet</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = által LOGLEVEL SZINTJEINEK értéke nem null;</td><td>A naplózási szint megadásához null értéket tartalmazó sorok eltávolítása és az eredményt FILTEREDLEVELS be.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = csoport FILTEREDLEVELS által LOGLEVEL;</td><td>A sorok naplózási szint szerint csoportosítja, és tárolja az eredményeket a GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>GYAKORISÁGOT = foreach GROUPEDLEVELS csoport létrehozás módja LOGLEVEL, COUNT (FILTEREDLEVELS. LOGLEVEL), COUNT;</td><td>Egy új készletet hoz létre, amely tartalmazza az egyes egyedi napló adatok értéke szinten, és hány alkalommal következik be. A GYAKORISÁGOT történő tárolás</td>
    </tr>
    <tr>
    <td>EREDMÉNY = rendelés GYAKORISÁGOT által száma desc;</td><td>A naplózási szintek rendelések (csökkenő) száma szerint, és tárolja az eredmény</td>
    </tr>
</table>

6. Az átalakítás eredménye használatával is mentheti a `STORE` utasítást. Például a következő parancsot a menti a `RESULT` számára a **/example/data/pigout** könyvtárban lévő az alapértelmezett tároló, a fürt:

        STORE RESULT into 'wasb:///example/data/pigout'

   > [!NOTE]
   > Az adatok tárolási fájlban található a megadott könyvtár **rész-nnnnn**. Ha a könyvtár már létezik, kapni fog egy hibaüzenetet.
   >
   >
   
7. Lépjen ki a grunt parancssorába, írja be a következő utasítást.

        QUIT;

### <a name="pig-latin-batch-files"></a>A Pig Latin parancsfájlokat
A Pig-parancs segítségével futtassa a Pig Latin, amely egy fájlban található.

1. Nyissa meg a grunt parancssorába kilépéskor **Jegyzettömb** , és hozzon létre egy új fájlt **pigbatch.pig** a a **PIG_HOME %** könyvtár.
2. Írja vagy illessze be a következő sorokat a **pigbatch.pig** fájlt, és mentse azt:

        LOGS = LOAD 'wasb:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;
3. Használja a következő futtatásához a **pigbatch.pig** fájl, a pig-paranccsal.

        pig %PIG_HOME%\pigbatch.pig

    A kötegelt befejezését követően megjelenik a következő kimenetet, és legyen ugyanaz, mint amikor használt `DUMP RESULT;` az előző lépésekben:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="summary"></a>Summary (Összefoglalás)
Ahogy látja, a Pig-parancs segítségével interaktív módon futtassa MapReduce műveleteket, vagy egy kötegfájlt tárolt Pig Latin feladatok futtatása.

## <a id="nextsteps"></a>Következő lépések
Általános információk a hdinsight Pig:

* [A Pig használata a HDInsight Hadoop](hdinsight-use-pig.md)

Más módszerekkel kapcsolatos információk a HDInsight Hadoop dolgozhat:

* [A Hive használata a hdinsight Hadoop](hdinsight-use-hive.md)
* [A HDInsight Hadoop MapReduce használata](hdinsight-use-mapreduce.md)
