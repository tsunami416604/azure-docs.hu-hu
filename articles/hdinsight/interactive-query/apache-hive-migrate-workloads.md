---
title: Az Azure HDInsight 3,6 kaptár számítási feladatait áttelepítheti HDInsight 4,0
description: Megtudhatja, hogyan telepítheti át Apache Hive számítási feladatait a 3,6-es HDInsight a HDInsight 4,0-ra.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: eceb4b312476d701ec8ce4eb0ce4886621824b3a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841591"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Az Azure HDInsight 3,6 kaptár számítási feladatait áttelepítheti HDInsight 4,0

Ebből a dokumentumból megtudhatja, hogyan telepítheti át Apache Hive és LLAP számítási feladatait a HDInsight 3,6 HDInsight 4,0-es verzióra. A HDInsight 4,0 újabb kaptár-és LLAP funkciókat biztosít, mint például a jelentős nézetek és a lekérdezési eredmények gyorsítótárazása. Ha a számítási feladatokat a HDInsight 4,0-re telepíti át, a 3. struktúra számos újabb funkcióját használhatja, amelyek nem érhetők el a HDInsight 3,6-on.

Ez a cikk a következő témákat tárgyalja:

* A kaptár metaadatainak áttelepítése a HDInsight 4,0-re
* SAVAS és nem savas táblák biztonságos áttelepítése
* A struktúra biztonsági házirendjének megőrzése HDInsight-verziókon keresztül
* Lekérdezés végrehajtása és hibakeresése a HDInsight 3,6 és a HDInsight 4,0 között

A kaptár egyik előnye, hogy lehetővé teszi a metaadatok exportálását egy külső adatbázisba (ez a struktúra Metaadattár néven ismert). A **Metaadattár struktúra** feladata a táblák statisztikáinak tárolása, beleértve a tábla tárolási helyét, az oszlopnevek és a tábla indexét. A metaadattár-adatbázis sémája eltér a kaptár-verzióktól. A Hive-metaadattár biztonságos frissítésének ajánlott módja a másolat létrehozása, és a másolás frissítése az aktuális éles környezet helyett.

## <a name="copy-metastore"></a>Metaadattár másolása

A HDInsight 3,6 és a HDInsight 4,0 különböző metaadattár sémákat igényel, és nem oszthat meg egyetlen metaadattár.

### <a name="external-metastore"></a>Külső metaadattár

Hozzon létre egy új másolatot a külső metaadattár. Ha külső metaadattár használ, az egyik biztonságos és egyszerű módszer a metaadattár másolására, ha az adatbázist másik néven szeretné [visszaállítani](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) a SQL Database Restore függvény használatával.  További információ a külső metaadattár HDInsight-fürthöz való csatolásáról: [külső metaadat-tárolók használata az Azure HDInsight](../hdinsight-use-external-metadata-stores.md) .

### <a name="internal-metastore"></a>Belső metaadattár

Ha a belső metaadattár használja, a lekérdezésekkel exportálhatja az objektum-definíciókat a Hive-metaadattárban, és importálhatja őket egy új adatbázisba.

1. Kapcsolódjon a HDInsight-fürthöz egy [Secure Shell-(SSH-) ügyfél](../hdinsight-hadoop-linux-use-ssh-unix.md)használatával.

1. A következő parancs beírásával csatlakozhat a HiveServer2-hez a [Beeline-ügyféllel](../hadoop/apache-hadoop-use-hive-beeline.md) az Open SSH-munkamenetből:

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; do echo "create database $d; use $d;" >> alltables.sql; for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"` ; do ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

    Ez a parancs létrehoz egy **alltables. SQL**nevű fájlt. Mivel az alapértelmezett adatbázist nem lehet törölni/újból létrehozni, távolítsa el `create database default;` utasítást a **alltables. SQL**-ben.

1. Lépjen ki az SSH-munkamenetből. Ezután adjon meg egy scp-parancsot a **alltables. SQL** helyi letöltéséhez.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.sql c:/hdi
    ```

1. Töltse fel a **alltables. SQL** -t az *új* HDInsight-fürtre.

    ```bash
    scp c:/hdi/alltables.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Ezután használja az SSH-t az *új* HDInsight-fürthöz való kapcsolódáshoz. Futtassa az alábbi kódot az SSH-munkamenetből:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.sql
    ```

## <a name="upgrade-metastore"></a>Metaadattár frissítése

A metaadattár **másolásának** befejeződése után futtasson egy [séma-frissítési parancsfájlt a meglévő](../hdinsight-hadoop-customize-cluster-linux.md) HDInsight 3,6-fürtön az új metaadattár a kaptár 3 sémára való frissítéséhez. Ez lehetővé teszi, hogy az adatbázis HDInsight 4,0 metaadattár legyen csatolva.

Használja az alábbi táblázatban szereplő értékeket. Cserélje le a `SQLSERVERNAME DATABASENAME USERNAME PASSWORD`t a **másolt** Hive-metaadattár megfelelő értékeire, szóközzel elválasztva. Ne adja meg a ". database.windows.net" kifejezést az SQL Server nevének megadásakor.

|Tulajdonság | Value (Díj) |
|---|---|
|Parancsfájl típusa|– Egyéni|
|Név|Struktúra frissítése|
|Bash-parancsfájl URI-ja|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Csomópont típusa (i)|Fej|
|Paraméterek|SQLSERVERNAME DATABASENAME FELHASZNÁLÓNÉV JELSZAVA|

> [!Warning]  
> A HDInsight 3,6 metaadat-sémát a HDInsight 4,0 sémába konvertáló frissítés nem vonható vissza.

A frissítés ellenőrzéséhez futtassa a következő SQL-lekérdezést az adatbázison:

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Struktúra-táblák migrálása HDInsight 4,0-re

Miután befejezte az előző lépések végrehajtását a kaptár Metaadattár HDInsight 4,0-re való átadásához, a metaadattár rögzített táblák és adatbázisok a HDInsight 4,0-fürtön lesznek láthatók a fürtön belüli `show tables` vagy `show databases` végrehajtásával. A HDInsight 4,0-fürtök lekérdezés-végrehajtásával kapcsolatos információkért lásd: [HDInsight-verziók lekérdezési végrehajtása](#query-execution-across-hdinsight-versions) .

A táblákból származó tényleges adatok azonban nem érhetők el, amíg a fürt nem fér hozzá a szükséges Storage-fiókokhoz. A következő lépések végrehajtásával gondoskodhat arról, hogy a HDInsight 4,0-fürtje hozzáférhessen a régi HDInsight 3,6-fürthöz tartozó adatbázisokhoz:

1. Határozza meg a tábla vagy az adatbázis Azure Storage-fiókját.

1. Ha a HDInsight 4,0-fürt már fut, csatolja az Azure Storage-fiókot a fürthöz a Ambari-n keresztül. Ha még nem hozta létre a HDInsight 4,0-fürtöt, győződjön meg arról, hogy az Azure Storage-fiók az elsődleges vagy másodlagos fürt Storage-fiókként van megadva. További információ a Storage-fiókok HDInsight-fürtökhöz való hozzáadásáról: [további Storage-fiókok hozzáadása a HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>Az új HDInsight 4,0 üzembe helyezése és az új metaadattár való kapcsolódás

A séma verziófrissítésének befejezése után helyezzen üzembe egy új HDInsight 4,0-fürtöt, és kapcsolódjon a frissített metaadattár. Ha már telepítette a 4,0-es rendszert, állítsa be úgy, hogy a Ambari-ből csatlakozhasson a metaadattár.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>Séma-áttelepítési parancsfájl futtatása a HDInsight 4,0-ből

A táblákat a HDInsight 3,6 és a HDInsight 4,0 eltérő módon kezelik. Ezért nem oszthat meg ugyanazokat a táblákat különböző verziójú fürtökhöz. Ha a HDInsight 3,6-et a HDInsight 4,0-as verziójával megegyező időpontban szeretné használni, az egyes verziókhoz külön másolatokkal kell rendelkeznie.

A struktúra számítási feladatához a savas és a nem savas táblák kombinációja is tartozhat. A HDInsight 3,6 (kaptár 2) és a kaptár on HDInsight 4,0 (kaptár 3) közötti egyik fő különbség a táblákhoz tartozó sav-megfelelés. A HDInsight 3,6-ben a kaptárak megfelelőségének engedélyezése további konfigurációt igényel, de a HDInsight 4,0-táblákban alapértelmezés szerint sav-kompatibilis. A Migrálás előtt csak az 3,6-es fürtön található sav tábla egyik jelentős tömörítését kell végrehajtani. Futtassa a következő lekérdezést a kaptár vagy a Beeline nézetből:

```sql
alter table myacidtable compact 'major';
```

Erre a tömörítésre azért van szükség, mert a HDInsight 3,6 és a HDInsight 4,0 sav táblázata eltérően értelmezi a savas különbözeteket. A tömörítés olyan tiszta Palat kényszerít, amely garantálja a konzisztenciát. A [kaptár-áttelepítési dokumentáció](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) 4. szakasza útmutatást tartalmaz a HDINSIGHT 3,6 savas táblák tömeges tömörítéséhez.

A metaadattár áttelepítési és tömörítési lépéseinek elvégzése után áttelepítheti a tényleges raktárat. A kaptár-tárház áttelepítésének befejezése után a HDInsight 4,0 Warehouse a következő tulajdonságokkal fog rendelkezni:

|3,6 |4,0 |
|---|---|
|Külső táblák|Külső táblák|
|Nem tranzakciós felügyelt táblák|Külső táblák|
|Tranzakciós felügyelt táblák|Felügyelt táblák|

Előfordulhat, hogy a Migrálás végrehajtása előtt módosítania kell a raktár tulajdonságait. Ha például azt szeretné, hogy egy harmadik fél (például egy HDInsight 3,6-fürt) egy táblát fog elérni, akkor az áttelepítés befejeződése után a táblának kívül kell lennie. A HDInsight 4,0-ben minden felügyelt tábla tranzakciós. Ezért a HDInsight 4,0 felügyelt tábláihoz csak a HDInsight 4,0-fürtök férhetnek hozzá.

Miután helyesen beállította a táblázat tulajdonságait, hajtsa végre a kaptár Warehouse áttelepítési eszközt az egyik fürt átjárócsomópontokkal az SSH-rendszerhéj használatával:

1. Csatlakozzon a fürt átjárócsomóponthoz SSH használatával. Útmutatásért lásd: [Kapcsolódás a HDInsight az SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. `sudo su - hive` futtatásával nyisson meg egy bejelentkezési rendszerhéjat a kaptár felhasználóként
1. Határozza meg az adatplatform-verem verzióját a `ls /usr/hdp`végrehajtásával. Ekkor megjelenik a következő parancsban használni kívánt Version sztring.
1. Futtassa a következő parancsot a rendszerhéjból. Cserélje le a `STACK_VERSION`t az előző lépésben szereplő Version sztringre:

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
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

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Ellenőrizze a kompatibilitást, és szükség szerint módosítsa a kódokat az alkalmazás teszteléséhez.

A munkaterhelések, például a meglévő programok és lekérdezések áttelepítésekor ellenőrizze a kibocsátási megjegyzéseket és a dokumentációt, és szükség szerint alkalmazza a módosításokat. Ha a HDInsight 3,6-fürt megosztott Sparkot és Hive-metaadattár használ, akkor a [kaptár-tárház összekötőjét használó további konfigurációra](./apache-hive-warehouse-connector.md) van szükség.

## <a name="deploy-new-app-for-production"></a>Új alkalmazás üzembe helyezése éles környezetben

Az új fürtre való váltáshoz például telepíthet egy új ügyfélalkalmazás alkalmazást, és használhatja azt új éles környezetként, vagy frissítheti meglévő ügyfélalkalmazásját, és átválthat a HDInsight 4,0-re.

## <a name="switch-hdinsight-40-to-the-production"></a>HDInsight 4,0 váltása az éles környezetbe

Ha a tesztelés során eltérések jöttek létre a metaadattár, előbb frissítenie kell a változtatásokat a váltás előtt. Ebben az esetben exportálhatja & importálhatja a metaadattár, majd újra elvégezheti a frissítést.

## <a name="remove-the-old-production"></a>A régi éles környezet eltávolítása

Miután meggyőződött arról, hogy a kiadás elkészült és teljes mértékben működőképes, eltávolíthatja a 3,6-es és a korábbi metaadattár-verziót. A környezet törlése előtt győződjön meg arról, hogy minden migrálva van.

## <a name="query-execution-across-hdinsight-versions"></a>Lekérdezés végrehajtása HDInsight-verziókon keresztül

A HDInsight 3,6-fürtön belül két módon hajtható végre a kaptár/LLAP lekérdezések. A HiveCLI parancssori felületet biztosít, a TEZ nézet/struktúra nézet pedig grafikus felhasználói felületen alapuló munkafolyamatot biztosít.

A HDInsight 4,0-ben a HiveCLI lecserélte a Beeline elemre. A HiveCLI egy takarékossági ügyfél az 1. Hiveserver, a Beeline pedig egy JDBC-ügyfél, amely hozzáférést biztosít a 2. Hiveserver. A Beeline bármely más JDBC-kompatibilis adatbázis-végponthoz való kapcsolódáshoz is használható. A Beeline a 4,0-es HDInsight-on keresztül érhető el anélkül, hogy telepítésre lenne szükség.

A HDInsight 3,6-ben a kaptár-kiszolgálóval való interakcióra szolgáló grafikus felhasználói felület a Ambari struktúra nézet. A HDInsight 4,0 nem Ambari nézettel rendelkezik. Lehetőséget biztosítunk ügyfeleinknek az adatelemzési Studio (DAS) használatára, amely nem alapvető HDInsight szolgáltatás. A DAS nem támogatja a HDInsight-fürtöket, és nem hivatalosan támogatott csomag. A DAS azonban a következő módon telepíthető a fürtre a [parancsfájl](../hdinsight-hadoop-customize-cluster-linux.md) használatával:

|Tulajdonság | Value (Díj) |
|---|---|
|Parancsfájl típusa|– Egyéni|
|Név|DAS|
|Bash-parancsfájl URI-ja|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Csomópont típusa (i)|Fej|

Várjon 10 – 15 percet, majd indítsa el az adatelemzési stúdiót a következő URL-cím használatával: `https://CLUSTERNAME.azurehdinsight.net/das/`.

A DAS-hoz való hozzáférés előtt a Ambari felhasználói felületének frissítése és/vagy az összes Ambari-összetevő újraindítása szükséges lehet.

Ha a DAS telepítve van, ha nem látja a futtatott lekérdezéseket a lekérdezések megjelenítőben, hajtsa végre a következő lépéseket:

1. Állítsa be a kaptár, a TEZ és a DAS konfigurációit az útmutatóban ismertetett módon a [Das telepítésének hibaelhárítása](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)című részben leírtak szerint.
2. Győződjön meg arról, hogy a következő Azure Storage-címtár-konfiguráció a lapok blobja, és hogy azok szerepelnek a `fs.azure.page.blob.dirs`alatt:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Indítsa újra a HDFS, a kaptárt, a TEZ és a DAS-t mindkét átjárócsomópontokkal.

## <a name="next-steps"></a>Következő lépések

* [HDInsight 4,0 közlemény](../hdinsight-version-release.md)
* [HDInsight 4,0 Deep Dive](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [3. struktúra – savas táblák](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
