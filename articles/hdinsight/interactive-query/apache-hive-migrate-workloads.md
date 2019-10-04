---
title: Az Azure HDInsight 3,6 kaptár számítási feladatait áttelepítheti HDInsight 4,0
description: Megtudhatja, hogyan telepítheti át Apache Hive számítási feladatait a 3,6-es HDInsight a HDInsight 4,0-ra.
ms.service: hdinsight
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 0363f2d8da1ca1371fd55107c6487c3d96f6d00e
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091456"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Az Azure HDInsight 3,6 kaptár számítási feladatait áttelepítheti HDInsight 4,0

Ebből a dokumentumból megtudhatja, hogyan telepítheti át Apache Hive és LLAP számítási feladatait a HDInsight 3,6 HDInsight 4,0-es verzióra. A HDInsight 4,0 újabb kaptár-és LLAP funkciókat biztosít, mint például a jelentős nézetek és a lekérdezési eredmények gyorsítótárazása. Ha a számítási feladatokat a HDInsight 4,0-re telepíti át, a 3. struktúra számos újabb funkcióját használhatja, amelyek nem érhetők el a HDInsight 3,6-on.

Ez a cikk a következő témákat tárgyalja:

* A kaptár metaadatainak áttelepítése a HDInsight 4,0-re
* SAVAS és nem savas táblák biztonságos áttelepítése
* A struktúra biztonsági házirendjének megőrzése HDInsight-verziókon keresztül
* Lekérdezés végrehajtása és hibakeresése a HDInsight 3,6 és a HDInsight 4,0 között

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Apache Hive metaadatok migrálása a HDInsight 4,0-re

A kaptár egyik előnye, hogy lehetővé teszi a metaadatok exportálását egy külső adatbázisba (ez a struktúra Metaadattár néven ismert). A **Metaadattár struktúra** feladata a táblák statisztikáinak tárolása, beleértve a tábla tárolási helyét, az oszlopnevek és a tábla indexét. A metaadattár-adatbázis sémája eltér a kaptár-verzióktól. Az alábbi lépések végrehajtásával frissítheti a HDInsight 3,6 kaptár Metaadattár, hogy az kompatibilis legyen a HDInsight 4,0-mel.

1. Hozzon létre egy új másolatot a külső metaadattár. A HDInsight 3,6 és a HDInsight 4,0 különböző metaadattár sémákat igényel, és nem oszthat meg egyetlen metaadattár. További információ a külső metaadattár HDInsight-fürthöz való csatolásáról: [külső metaadat-tárolók használata az Azure HDInsight](../hdinsight-use-external-metadata-stores.md) . 
2. Indítson el egy parancsfájl-műveletet a HDI 3,6-fürtön a "fő csomópontok" csomóponttal a végrehajtáshoz. Illessze be a következő URI-t a "bash script URI" jelölésű szövegmezőbe: https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh. Az "argumentumok" jelölésű szövegmezőbe írja be a másolt Hive-metaadattár servername, adatbázis, Felhasználónév és jelszó mezőt, szóközzel elválasztva. A servername megadásakor ne tartalmazza a ". database.windows.net" értéket.

> [!Warning]
> A HDInsight 3,6 metaadat-sémát a HDInsight 4,0 sémába konvertáló frissítés nem vonható vissza.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Struktúra-táblák migrálása HDInsight 4,0-re

Miután elvégezte az előző lépések végrehajtását a kaptár metaadattár HDInsight 4,0-re való átadásához, a metaadattár rögzített táblák és adatbázisok a HDInsight 4,0-fürtön `show tables` lesznek láthatók a fürtön vagy `show databases` a-n belül. . A HDInsight 4,0-fürtök lekérdezés-végrehajtásával kapcsolatos információkért lásd: [HDInsight-verziók lekérdezési végrehajtása](#query-execution-across-hdinsight-versions) .

A táblákból származó tényleges adatok azonban nem érhetők el, amíg a fürt nem fér hozzá a szükséges Storage-fiókokhoz. A következő lépések végrehajtásával gondoskodhat arról, hogy a HDInsight 4,0-fürtje hozzáférhessen a régi HDInsight 3,6-fürthöz tartozó adatbázisokhoz:

1. Határozza meg a táblázat vagy adatbázis Azure Storage-fiókját a formázott leírások használatával.
2. Ha a HDInsight 4,0-fürt már fut, csatolja az Azure Storage-fiókot a fürthöz a Ambari-n keresztül. Ha még nem hozta létre a HDInsight 4,0-fürtöt, győződjön meg arról, hogy az Azure Storage-fiók az elsődleges vagy másodlagos fürt Storage-fiókként van megadva. További információ a Storage-fiókok HDInsight-fürtökhöz való hozzáadásáról: [további Storage-fiókok hozzáadása a HDInsight](../hdinsight-hadoop-add-storage.md).

> [!Note]
> A táblákat a HDInsight 3,6 és a HDInsight 4,0 eltérő módon kezelik. Ezért nem oszthat meg ugyanazokat a táblákat különböző verziójú fürtökhöz. Ha a HDInsight 3,6-et a HDInsight 4,0-as verziójával megegyező időpontban szeretné használni, az egyes verziókhoz külön másolatokkal kell rendelkeznie.

A struktúra számítási feladatához a savas és a nem savas táblák kombinációja is tartozhat. A HDInsight 3,6 (kaptár 2) és a kaptár on HDInsight 4,0 (kaptár 3) közötti egyik fő különbség a táblákhoz tartozó sav-megfelelés. A HDInsight 3,6-ben a kaptárak megfelelőségének engedélyezése további konfigurációt igényel, de a HDInsight 4,0-táblákban alapértelmezés szerint sav-kompatibilis. A Migrálás előtt csak az 3,6-es fürtön található sav tábla egyik jelentős tömörítését kell végrehajtani. Futtassa a következő lekérdezést a kaptár vagy a Beeline nézetből:

```bash
alter table myacidtable compact 'major';
```

Erre a tömörítésre azért van szükség, mert a HDInsight 3,6 és a HDInsight 4,0 sav táblázata eltérően értelmezi a savas különbözeteket. A tömörítés olyan tiszta Palat kényszerít, amely garantálja a konzisztenciát. A kaptár-áttelepítési [dokumentáció](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) 4. szakasza útmutatást tartalmaz a HDINSIGHT 3,6 savas táblák tömeges tömörítéséhez.

A metaadattár áttelepítési és tömörítési lépéseinek elvégzése után áttelepítheti a tényleges raktárat. A kaptár-tárház áttelepítésének befejezése után a HDInsight 4,0 Warehouse a következő tulajdonságokkal fog rendelkezni:

* A HDInsight 3,6 külső táblái a HDInsight 4,0 külső táblái lesznek
* A nem tranzakciós felügyelt táblák a HDInsight 3,6-ben külső táblák lesznek a HDInsight 4,0
* A HDInsight 3,6 tranzakciós felügyelt táblái a HDInsight 4,0-ben felügyelt táblák lesznek.

Előfordulhat, hogy a Migrálás végrehajtása előtt módosítania kell a raktár tulajdonságait. Ha például azt szeretné, hogy egy harmadik fél (például egy HDInsight 3,6-fürt) egy táblát fog elérni, akkor az áttelepítés befejeződése után a táblának kívül kell lennie. A HDInsight 4,0-ben minden felügyelt tábla tranzakciós. Ezért a HDInsight 4,0 felügyelt tábláihoz csak a HDInsight 4,0-fürtök férhetnek hozzá.

Miután helyesen beállította a táblázat tulajdonságait, hajtsa végre a kaptár Warehouse áttelepítési eszközt az egyik fürt átjárócsomópontokkal az SSH-rendszerhéj használatával:

1. Csatlakozzon a fürt átjárócsomóponthoz SSH használatával. Útmutatásért lásd: [Kapcsolódás a HDInsight az SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Bejelentkezési rendszerhéj megnyitása kaptár-felhasználóként a futtatásával`sudo su - hive`
1. Határozza meg a Hortonworks adatplatform-verem verzióját a `ls /usr/hdp`végrehajtásával. Ekkor megjelenik a következő parancsban használni kívánt Version sztring.
1. Futtassa a következő parancsot a rendszerhéjból. Cserélje `${{STACK_VERSION}}` le az elemet az előző lépésben szereplő Version sztringre:

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

Az áttelepítési eszköz befejeződése után a kaptár-tárház készen áll a HDInsight 4,0-re. 

> [!Important]
> A HDInsight 4,0 felügyelt táblái (beleértve a 3,6-ból áttelepített táblákat is) más szolgáltatásokkal vagy alkalmazásokkal nem érhetők el, beleértve a HDInsight 3,6-fürtöket.

## <a name="secure-hive-across-hdinsight-versions"></a>Biztonsági struktúra HDInsight-verziókon keresztül

A HDInsight 3,6 óta a HDInsight a HDInsight Enterprise Security Package (ESP) használatával integrálódik a Azure Active Directoryba. Az ESP a Kerberos és az Apache Ranger használatával kezeli a fürtön belüli adott erőforrások engedélyeit. A HDInsight 3,6-es struktúrában üzembe helyezett Ranger-házirendek a következő lépésekkel telepíthetők át a HDInsight 4,0-re:

1. Navigáljon a Ranger Service Manager panelre a HDInsight 3,6-fürtben.
2. Navigáljon a **struktúra** nevű szabályzathoz, és exportálja a szabályzatot egy JSON-fájlba.
3. Győződjön meg arról, hogy az exportált házirend JSON-ban hivatkozott összes felhasználó létezik az új fürtben. Ha a felhasználó a szabályzat JSON-ban szerepel, de nem létezik az új fürtben, adja hozzá a felhasználót az új fürthöz, vagy távolítsa el a hivatkozást a szabályzatból.
4. Navigáljon a **Ranger Service Manager** panelre a HDInsight 4,0-fürtben.
5. Navigáljon a **kaptár** nevű szabályzathoz, és importálja a Ranger Policy JSON-t a 2. lépésből.

## <a name="query-execution-across-hdinsight-versions"></a>Lekérdezés végrehajtása HDInsight-verziókon keresztül

A HDInsight 3,6-fürtön belül két módon hajtható végre a kaptár/LLAP lekérdezések. A HiveCLI parancssori felületet biztosít, a TEZ nézet/struktúra nézet pedig grafikus felhasználói felületen alapuló munkafolyamatot biztosít.

A HDInsight 4,0-ben a HiveCLI lecserélte a Beeline elemre. A HiveCLI egy takarékossági ügyfél az 1. Hiveserver, a Beeline pedig egy JDBC-ügyfél, amely hozzáférést biztosít a 2. Hiveserver. A Beeline bármely más JDBC-kompatibilis adatbázis-végponthoz való kapcsolódáshoz is használható. A Beeline a 4,0-es HDInsight-on keresztül érhető el anélkül, hogy telepítésre lenne szükség.

A HDInsight 3,6-ben a kaptár-kiszolgálóval való interakcióra szolgáló grafikus felhasználói felület a Ambari struktúra nézet. A HDInsight 4,0 a kaptár nézetet a Hortonworks adatelemzési Studióval (DAS) helyettesíti. A DAS nem a HDInsight-fürtökön található, és nem hivatalosan támogatott csomag. A DAS azonban a következő módon telepíthető a fürtre:

Indítson el egy parancsfájl-műveletet a fürtön a "fő csomópontok" csomóponttal a végrehajtáshoz. Illessze be a következő URI-t a "bash script URI" jelölésű szövegmezőbe: https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh

Várjon 5 – 10 percet, majd indítsa el az adatelemzési studiót a következő\<URL-cím használatával: https://clustername >. azurehdinsight. net/Das/

Ha a DAS telepítve van, ha nem látja a futtatott lekérdezéseket a lekérdezések megjelenítőben, hajtsa végre a következő lépéseket:

1. Állítsa be a kaptár, a TEZ és a DAS konfigurációit az útmutatóban ismertetett módon a [Das telepítésének hibaelhárítása](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)című részben leírtak szerint.
2. Győződjön meg arról, hogy a következő Azure Storage-címtár-konfiguráció a lapok blobja, és hogy `fs.azure.page.blob.dirs`azok szerepelnek a területen:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Indítsa újra a HDFS, a kaptárt, a TEZ és a DAS-t mindkét átjárócsomópontokkal.

## <a name="next-steps"></a>További lépések

* [HDInsight 4,0 közlemény](../hdinsight-version-release.md)
* [HDInsight 4,0 Deep Dive](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [3. struktúra – savas táblák](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
