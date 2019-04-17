---
title: Azure HDInsight 3.6-os Hive számítási feladatok migrálása HDInsight 4.0-s
description: Ismerje meg, hogyan telepítheti át számítási feladatokat az Apache Hive a HDInsight 3.6-os HDInsight 4.0-s.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 04/15/2019
ms.openlocfilehash: 708df64802ace17fa77b4e0a695c9f1c3bd18a77
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610225"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Azure HDInsight 3.6-os Hive számítási feladatok migrálása HDInsight 4.0-s

Ez a dokumentum bemutatja, hogyan telepítheti át a HDInsight 3.6 Apache Hive és az LLAP munkaterhelések HDInsight 4.0-s. HDInsight 4.0 materializált nézetek és a lekérdezési eredmények gyorsítótárazása újabb Hive és az LLAP funkciókat biztosít. A számítási feladatok HDInsight 4.0-s áttelepítésekor számos újabb a Hive-3-ból elérhető funkciók igénylésével a HDInsight 3.6-ot is használhatja.

Ez a cikk ismerteti az alábbiakat:

* 4.0-s HDInsight Hive-metaadatok migrálása
* Nem ACID-táblák és-biztonságos áttelepítése
* HDInsight-verziók között a Hive-biztonsági házirendek megőrzése
* A lekérdezés végrehajtása és a hibakeresés, a HDInsight 3.6-os HDInsight 4.0-s

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Az Apache Hive-metaadatok migrálása HDInsight 4.0-s

Hive egyik előnye a külső adatbázishoz (néven a Hive-Metaadattár) metaadatainak exportálásához lehetővé teszi. A **Hive-Metaadattár** felelős tábla statisztikai adatait, beleértve a tábla tárolási helye, oszlopnevek és tábla index adatainak tárolására. A metaadattár adatbázisséma eltér a Hive-verziók között. Tegye a következőket, a HDInsight 3.6-os Hive-Metaadattár frissítése, hogy a HDInsight 4.0 rendszerrel kompatibilis legyen.

1. Hozzon létre egy új példányt a külső metaadattár. HDInsight 3.6-os és a HDInsight 4.0 különböző metaadattár sémákra van szükség, és nem oszthat meg egyetlen metaadattár.
1. A metaadattár új másolatának csatolása a) egy meglévő HDInsight 4.0-fürthöz, vagy (b) egy fürtöt, amely az első alkalommal hoz létre. Lásd: [külső metaadat-tárolók az Azure HDInsight használata](../hdinsight-use-external-metadata-stores.md) tudhat meg többet a külső metaadattár csatolása egy HDInsight-fürtön. A Metaadattár csatolást, automatikusan alakítja át a 4.0-kompatibilis metaadattár.

> [!Warning]
> A frissítés, amely a HDInsight 3.6-os metaadatok séma alakítja át a HDInsight 4.0 séma nem fordítható vissza.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Hive-táblákat át HDInsight 4.0

Miután befejezte az előző lépések végrehajtásával telepítse át a Hive-Metaadattár HDInsight 4.0-s készletét, a táblákat és rögzítve a metaadattár adatbázisok láthatók lesznek a 4.0-s HDInsight-fürtön belül végrehajtásával `show tables` vagy `show databases` származó a fürtön belül . Lásd: [HDInsight-verziók között a lekérdezés végrehajtása](#query-execution-across-hdinsight-versions) lekérdezés végrehajtása a HDInsight 4.0 fürtök tájékoztatást.

A tényleges adatok a táblákból, azonban nem érhető el addig, amíg a fürt rendelkezik a szükséges tárfiókok a hozzáférést. Ahhoz, hogy a HDInsight 4.0 rendszerű fürt hozzáférhet a régi HDInsight 3.6-fürt azonos adatokat, kövesse az alábbi lépéseket:

1. Határozza meg a táblázat az Azure storage-fiókot, vagy adatbázis használatával ismertesse formázva.
2. A HDInsight 4.0 rendszerű fürt már fut, ha az Azure storage-fiók csatolása a fürt Ambari-n keresztül. Ha Ön még nem hozta létre a 4.0-s HDInsight-fürt, ellenőrizze, hogy az Azure storage-fiók az elsődleges vagy másodlagos tárfiók van megadva. HDInsight-fürtök tárfiókok hozzáadásával kapcsolatos további információkért lásd: [további tárfiókok hozzáadása a HDInsight](../hdinsight-hadoop-add-storage.md).

> [!Note]
> Táblák a HDInsight 3.6-os és a HDInsight 4.0 eltérően kell kezelni. Ezen okból kifolyólag nem oszthat meg a különböző verziójú fürtök két tábla. Ha szeretné használni a HDInsight 3.6-os HDInsight 4.0 egyszerre, hogy az adatok minden verzió esetében külön példányát kell rendelkeznie.

A Hive számítási feladatokhoz nem ACID-táblák és-vegyesen tartalmazhatnak. Hive a HDInsight 3.6-os (Hive-2) és a Hive a HDInsight 4.0 (Hive-3) között egy fő különbséggel ACID-megfelelőségi táblák. A HDInsight 3.6 Hive ACID-megfelelőség engedélyezése további konfigurálást igényel, de a HDInsight 4.0-s táblák ACID-kompatibilis alapértelmezés szerint. Az egyetlen művelet előtt áttelepítést az ACID-tábla fő tömörítési a 3.6-os fürtön futtatásához szükséges. A Hive-nézet vagy a Beeline futtassa a következő lekérdezést:

```bash
alter table myacidtable compact 'major';
```

Ez a tömörítés szükség, mert a HDInsight 3.6-os és a HDInsight 4.0 ACID tábla különböző ACID eltérések ismertetése. Tömörítési kényszerít egy tiszta lappal, amelyek a tábla konzisztenciát garantál. A tömörítés után az előző lépéseket a metaadattár és tábla áttelepítési elegendő bármely HDInsight 3.6-os ACID-táblák használata a HDInsight 4.0-s lesz.

## <a name="secure-hive-across-hdinsight-versions"></a>Biztonságos Hive a HDInsight-verziók között

HDInsight 3.6-os verziója óta HDInsight integrálható az Azure Active Directory használata a HDInsight vállalati biztonsági csomag (ESP). ESP Kerberos- és Apache Ranger segítségével kezelheti az engedélyeket az adott erőforrások a fürtön belül. Ranger-házirendek üzembe helyezett elleni Hive a HDInsight 3.6 HDInsight 4.0-s verzióját az alábbi lépéseket követve telepíthető át:

1. Keresse meg a Ranger-Service Manager panelen, a HDInsight 3.6-fürt.
2. Keresse meg a házirend nevű **HIVE** és a egy json-fájlt a házirend exportálása.
3. Győződjön meg arról, hogy az exportált házirend json említett összes felhasználó szerepel-e az új fürtön. Ha egy felhasználó hivatkozik, amely a szabályzat json-fájlban, de az új fürt nem létezik, vagy adja hozzá a felhasználót az új fürthöz, vagy távolítsa el a hivatkozást a szabályzat alól.
4. Keresse meg a **Ranger a Service Manager** panel a 4.0-s HDInsight-fürtben.
5. Keresse meg a házirend nevű **HIVE** és importálnia kell a ranger-házirendet json 2. lépés.

## <a name="query-execution-across-hdinsight-versions"></a>A lekérdezés végrehajtása HDInsight-verziók között

Két módon hajtható végre, és a hibakereséshez Hive/LLAP lekérdezéseket a HDInsight 3.6-os-fürtön belül. HiveCLI parancssori felületet biztosít, és a Tez megtekintése/Hive-nézet biztosítja a GUI-alapú munkafolyamat.

A HDInsight 4.0-s HiveCLI Beeline lett cserélve. HiveCLI Hiveserver 1 egy thrift-ügyfél, a Beeline pedig a JDBC-ügyfél, amely hozzáférést biztosít a Hiveserver-2-re. A beeline is használható bármely más JDBC-kompatibilis adatbázist végponthoz csatlakozik. A beeline elérhető – beépített HDInsight 4.0 szükséges telepítéseken nélkül.

A HDInsight 3.6, a grafikus felhasználói Felülettel-ügyfél használata a Hive-kiszolgáló az Ambari Hive-nézet. HDInsight 4.0 váltja fel a Hive-nézet a Hortonworks Data Analytics Studio (DAS). DAS nem szerepel a HDInsight fürtök out-of-box, és nem a hivatalosan támogatott csomagot. Azonban DAS is telepíthetők a fürtön a következő:

1. Töltse le a [DAS-csomag telepítési parancsfájl](https://hdiconfigactions.blob.core.windows.net/dasinstaller/install-das-mpack.sh) futtató mindkét fürt átjárócsomópontjaihoz. Ez a szkript egy parancsprogram-művelet nem hajtható végre.
2. Töltse le a [DAS szolgáltatás telepítési parancsfájl](https://hdiconfigactions.blob.core.windows.net/dasinstaller/install-das-component.sh) és futtassa azt egy parancsfájl műveletként. Válassza ki **Átjárócsomópontokra** egy parancsfájl művelet felületen tetszőleges csomópont típusaként.
3. A parancsprogram-művelet befejeződése után nyissa meg az Ambari, és válassza **Data Analytics Studio** a szolgáltatások listájából. Az összes DAS-szolgáltatás leáll. A jobb felső sarokban válassza **műveletek** és **Start**. Most már hajtsa végre, és hibakeresése a DAS-lekérdezéseket.

Miután telepítette DAS, ha nem látja a lekérdezések futtatása után a lekérdezések megjelenítőben, tegye a következőket:

1. Állítsa be a Hive Tez és DAS konfigurációi leírtak szerint [DAS telepítésének hibaelhárítása a jelen útmutató](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Győződjön meg arról, hogy a következő az Azure storage directory configs lapblobok, és, hogy a felsorolás `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Indítsa újra a HDFS, Hive, Tez és DAS a mindkét átjárócsomópontjaihoz.

## <a name="next-steps"></a>További lépések

* [HDInsight 4.0 bejelentés](../hdinsight-version-release.md)
* [HDInsight 4.0 részletes bemutatása](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive-3 ACID táblákban](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)