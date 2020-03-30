---
title: Az Azure HDInsight 3.6 Hive-számítási feladatok áttelepítése a HDInsight 4.0-ra
description: Ismerje meg, hogyan telepítheti át az Apache Hive-számítási feladatokat a HDInsight 3.6-on a HDInsight 4.0-s rendszerbe.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: ec96189185a06c1fcbd95eed6216ade47f3089c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214648"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Az Azure HDInsight 3.6 Hive-számítási feladatok áttelepítése a HDInsight 4.0-ra

Ez a dokumentum bemutatja, hogyan telepítheti át az Apache Hive- és LLAP-számítási feladatokat a HDInsight 3.6-on a HDInsight 4.0-ra. A HDInsight 4.0 újabb Hive- és LLAP-funkciókat, például materializált nézeteket és lekérdezési eredmény-gyorsítótárazást biztosít. Amikor áttelepíti a számítási feladatokat a HDInsight 4.0-s verzióra, a Hive 3 számos újabb, a HDInsight 3.6-on nem elérhető szolgáltatását használhatja.

Ez a cikk a következő témákkal foglalkozik:

* Hive-metaadatok áttelepítése a HDInsight 4.0-s rendszerre
* Acid és nem ACID asztalok biztonságos kioldódása
* A Hive biztonsági házirendjeinek megőrzése a HDInsight-verziókban
* Lekérdezés-végrehajtás és hibakeresés a HDInsight 3.6-ról a HDInsight 4.0-ra

A Hive egyik előnye, hogy képes metaadatokat exportálni egy külső adatbázisba (a továbbiakban: A Hive Metastore). A **Hive Metastore** felelős a táblastatisztikák tárolásáért, beleértve a tábla tárolási helyét, az oszlopneveket és a táblaindex adatait. A metatár-adatbázis sémája eltér a Hive-verziók között. A Hive metastore biztonságos frissítésének ajánlott módja egy másolat létrehozása és a másolat frissítése az aktuális éles környezet helyett.

## <a name="copy-metastore"></a>Metastore másolása

A HDInsight 3.6-os és HDInsight 4.0-s hdinsight-sémákat igényelnek, és nem tudnak megosztani egyetlen metatárolót.

### <a name="external-metastore"></a>Külső metastore

Hozzon létre egy új másolatot a külső metastore. Ha külső metatárolót használ, a metatároló ról történő másolásegyik biztonságos és egyszerű módja [az adatbázis](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) visszaállítása más néven az SQL Database-visszaállítási funkcióval.  A külső metatárolók HDInsight-fürthöz való csatolásáról további információt az [Azure HDInsight külső metaadat-tárolóinak](../hdinsight-use-external-metadata-stores.md) használata.

### <a name="internal-metastore"></a>Belső metabolt

Ha a belső metatárolót használja, lekérdezésekkel exportálhatja az objektumdefiníciókat a Hive metatárolóban, és importálhatja őket egy új adatbázisba.

A parancsfájl befejezése után feltételezzük, hogy a régi fürt a továbbiakban nem lesz használva a parancsfájlban említett táblák vagy adatbázisok eléréséhez.

> [!NOTE]
> ACID-táblák esetében a tábla alatti adatok új másolata jön létre.

1. Csatlakozzon a HDInsight-fürthöz [egy Secure Shell (SSH) ügyfél](../hdinsight-hadoop-linux-use-ssh-unix.md)használatával.

1. Csatlakozzon a HiveServer2-hez a [Beeline ügyféllel](../hadoop/apache-hadoop-use-hive-beeline.md) a megnyitott SSH-munkamenetből a következő parancs megadásával:

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; 
    do
        echo "Scanning Database: $d"
        echo "create database if not exists $d; use $d;" >> alltables.hql; 
        for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"`;
        do
            echo "Copying Table: $t"
            ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`;

            echo "$ddl;" >> alltables.hql;
            lowerddl=$(echo $ddl | awk '{print tolower($0)}')
            if [[ $lowerddl == *"'transactional'='true'"* ]]; then
                if [[ $lowerddl == *"partitioned by"* ]]; then
                    # partitioned
                    raw_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "CREATE TABLE .*" | cut -d"(" -f2- | cut -f1 -d")" | sed 's/`//g');
                    ptn_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "PARTITIONED BY .*" | cut -f1 -d")" | cut -d"(" -f2- | sed 's/`//g');
                    final_cols=$(echo "(" $raw_cols "," $ptn_cols ")")

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t $final_cols TBLPROPERTIES ('transactional'='false');";
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    parsed_ptn_cols=$(echo $ptn_cols| sed 's/ [a-z]*,/,/g' | sed '$s/\w*$//g');
                    echo "create table flattened_$t $final_cols;" >> alltables.hql;
                    echo "load data inpath '$dir' into table flattened_$t;" >> alltables.hql;
                    echo "insert into $t partition($parsed_ptn_cols) select * from flattened_$t;" >> alltables.hql;
                    echo "drop table flattened_$t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                else
                    # not partitioned
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t like $t TBLPROPERTIES ('transactional'='false');";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    echo "load data inpath '$dir' into table $t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                fi
            fi
            echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t;" >> alltables.hql;
        done;
    done
    ```

    Ez a parancs **egy alltables.hql**nevű fájlt hoz létre.

1. Lépjen ki az SSH-munkamenetből. Ezután adjon meg egy scp parancsot az **alltables.hql** helyi letöltéséhez.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Töltse fel **az alltables.hql fájlt** az *új* HDInsight-fürtbe.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Ezután az SSH segítségével csatlakozzon az *új* HDInsight-fürthöz. Futtassa a következő kódot az SSH-munkamenetből:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.hql
    ```


## <a name="upgrade-metastore"></a>Metastore frissítése

Miután a metatároló **példány befejeződött,** futtasson egy sémafrissítési parancsfájlt a [parancsfájlműveletben](../hdinsight-hadoop-customize-cluster-linux.md) a meglévő HDInsight 3.6-fürtön az új metatároló Hive 3 sémára való frissítéséhez. Ez lehetővé teszi, hogy az adatbázis HDInsight 4.0 metastore-ként legyen csatolva.

Használja az alábbi táblázatban szereplő értékeket. Cserélje `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` le a megfelelő értékeket a **másolt** Hive metastore, szóközökkel elválasztva. Az SQL-kiszolgáló nevének megadásakor ne adja meg a ".database.windows.net" értéket.

|Tulajdonság | Érték |
|---|---|
|Parancsfájl típusa|- Egyéni|
|Név|Hive-frissítés|
|Bash parancsfájl URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Csomóponttípus(ok)|Head|
|Paraméterek|SQLSERVERNAME ADATBÁZISNÉV FELHASZNÁLÓNÉV JELSZÓ|

> [!Warning]  
> A HDInsight 3.6 metaadatsémát HDInsight 4.0-s sémává konvertáló frissítés nem vonható vissza.

A frissítést úgy ellenőrizheti, hogy a következő sql-lekérdezést futtatja az adatbázison:

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Hive-táblák áttelepítése a HDInsight 4.0-s rendszerbe

A Hive Metastore HDInsight 4.0-ra való áttelepítéséhez szükséges előző lépések befejezése után a metatárolóban rögzített táblák és adatbázisok `show tables` a `show databases` HDInsight 4.0-s fürtön belül ről láthatók lesznek a fürt végrehajtásával vagy a fürtön belül. A HDInsight 4.0-fürtök lekérdezés-végrehajtásáról a HDInsight 4.0-s fürtjein a lekérdezések végrehajtásáról című [témakörben](#query-execution-across-hdinsight-versions) talál.

A táblákból származó tényleges adatok azonban nem érhetők el, amíg a fürt nem rendelkezik hozzáféréssel a szükséges tárfiókokhoz. Ha biztosítani szeretné, hogy a HDInsight 4.0-fürt ugyanazokat az adatokat tudja elérni, mint a régi HDInsight 3.6-os fürt, hajtsa végre az alábbi lépéseket:

1. Határozza meg a tábla vagy az adatbázis Azure tárfiókját.

1. Ha a HDInsight 4.0-s fürtmár fut, csatolja az Azure storage-fiókot a fürthöz az Ambari-n keresztül. Ha még nem hozta létre a HDInsight 4.0-fürt, győződjön meg arról, hogy az Azure storage-fiók elsődleges vagy másodlagos fürttárfiókként van megadva. A tárfiókok HDInsight-fürtökhöz való hozzáadásáról további információt a [További tárfiókok hozzáadása a HDInsighthoz című témakörben talál.](../hdinsight-hadoop-add-storage.md)

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>Az új HDInsight 4.0 üzembe helyezése és csatlakozás az új metatárolóhoz

A séma frissítés befejezése után telepítsen egy új HDInsight 4.0-s fürtöt, és csatlakoztassa a frissített metatárolót. Ha már telepítette a 4.0-s t, állítsa be úgy, hogy az Ambari-ból kapcsolódjon a metatárhoz.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>Sémaáttelepítési parancsfájl futtatása a HDInsight 4.0-s részéről

A HDInsight 3.6 és a HDInsight 4.0 eltérően kezeli a táblázatokat. Emiatt nem oszthatja meg ugyanazokat a táblákat a különböző verziók fürtjeihez. Ha a HDInsight 3.6-ot a HDInsight 4.0-s verziójával egy időben szeretné használni, minden verzióhoz külön másolattal kell rendelkeznie az adatokról.

A Hive munkaterhelés tartalmazhat sav és nem ACID táblák keveréke. Az egyik legfontosabb különbség a HIVE hdinsight 3.6 (Hive 2) és a Hive hdinsight 4.0 (Hive 3) az ACID-megfelelőség a táblák. A HDInsight 3.6-ban a Hive ACID-megfelelőség engedélyezése további konfigurációt igényel, de a HDInsight 4.0-táblákban alapértelmezés szerint ACID-kompatibilisek. Az áttelepítés előtt csak akkor szükséges, ha a 3.6-os fürt ACID-táblája ellen jelentős tömörítést kell futtatni. A Hive nézetben vagy a Beeline nézetben futtassa a következő lekérdezést:

```sql
alter table myacidtable compact 'major';
```

Ez a tömörítés azért szükséges, mert a HDInsight 3.6 és a HDInsight 4.0 ACID-táblák eltérően értelmezik az ACID-különbözeteket. A tömörítés tiszta pala, amely garantálja a konzisztenciát. A [Hive áttelepítési dokumentáció](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) 4 szakasza útmutatást tartalmaz a HDInsight 3.6 ACID-táblák tömeges tömörítéséhez.

Miután elvégezte a metatároló áttelepítési és tömörítési lépéseit, áttelepítheti a tényleges raktárat. A Hive-raktár áttelepítésének befejezése után a HDInsight 4.0-s raktár a következő tulajdonságokkal rendelkezik:

|3.6 |4.0 |
|---|---|
|Külső táblák|Külső táblák|
|Nem tranzakciós kezelt táblák|Külső táblák|
|Tranzakciós kezelt táblák|Felügyelt táblák|

Előfordulhat, hogy az áttelepítés végrehajtása előtt módosítania kell a raktár tulajdonságait. Ha például azt várja, hogy egy harmadik fél (például egy HDInsight 3.6-fürt) egy táblához hozzáfér, a táblának az áttelepítés befejezése után külsőnek kell lennie. A HDInsight 4.0-s felbontásban minden felügyelt tábla tranzakciós. Ezért a HDInsight 4.0 felügyelt tábláit csak a HDInsight 4.0-fürtök érhetik el.

A tábla tulajdonságainak megfelelő beállítása után hajtsa végre a Hive-raktár áttelepítési eszközét az SSH-rendszerhéj használatával a fürt egyik headnode-jából:

1. Csatlakozzon a fürt fejnod ssh használatával. További információt a [Csatlakozás a HDInsighthoz SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Nyisson meg egy bejelentkezési rendszerhéjat Hive-felhasználóként a`sudo su - hive`
1. Határozza meg az adatplatform `ls /usr/hdp`verem verzióját a végrehajtásával. Ez egy verziókarakterláncot jelenít meg, amelyet a következő parancsban kell használnia.
1. Hajtsa végre a következő parancsot a rendszerhéjból. Cserélje `STACK_VERSION` le az előző lépés verziókarakterláncára:

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

Az áttelepítési eszköz befejezése után a Hive-raktár készen áll a HDInsight 4.0-s készletére.

> [!Important]  
> A HDInsight 4.0 felügyelt tábláit (beleértve a 3.6-ból áttelepített táblákat is) más szolgáltatások vagy alkalmazások, köztük a HDInsight 3.6-os fürtök nem érhetik el.

## <a name="secure-hive-across-hdinsight-versions"></a>Biztonságos hive a HDInsight-verziókban

A HDInsight 3.6 óta a HDInsight a HDInsight Enterprise Security Package (ESP) használatával integrálható az Azure Active Directoryval. Az ESP a Kerberos és az Apache Ranger segítségével kezeli a fürtön belüli erőforrások engedélyeit. A HIVe-n a HDInsight 3.6-ban telepített Ranger-házirendek a következő lépésekkel telepíthetők át a HDInsight 4.0-s rendszerre:

1. Nyissa meg a Ranger Service Manager panelt a HDInsight 3.6-os fürtben.
2. Keresse meg a **HIVE** nevű házirendet, és exportálja a házirendet egy jsonfájlba.
3. Győződjön meg arról, hogy az exportált házirendjében hivatkozott összes felhasználó létezik az új fürtben. Ha egy felhasználó ra hivatkozik a házirend json, de nem létezik az új fürtben, vagy adja hozzá a felhasználót az új fürthöz, vagy távolítsa el a hivatkozást a házirendből.
4. Nyissa meg a **Ranger Service Manager** panelt a HDInsight 4.0-fürtben.
5. Keresse meg a **HIVE** nevű házirendet, és importálja a ranger házirend jsonját a 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Kompatibilitás ellenőrzése és szükség szerint a kódok módosítása a tesztalkalmazásban

A számítási feladatok, például a meglévő programok és lekérdezések áttelepítésekor ellenőrizze a kibocsátási megjegyzéseket és a dokumentációt a módosításokról, és szükség szerint alkalmazza a módosításokat. Ha a HDInsight 3.6-fürt megosztott Spark- és Hive metatárolót használ, további konfigurációra van szükség a [Hive Warehouse Connector használatával.](./apache-hive-warehouse-connector.md)

## <a name="deploy-new-app-for-production"></a>Új alkalmazás üzembe helyezése éles környezetben

Az új fürtre való váltáshoz például telepíthet egy új ügyfélalkalmazást, és használhatja azt új éles környezetként, vagy frissítheti a meglévő ügyfélalkalmazást, és átválthat a HDInsight 4.0-ra.

## <a name="switch-hdinsight-40-to-the-production"></a>Váltás a HDInsight 4.0-s sorozatra

Ha a tesztelés során különbségek et hoztak létre a metatárolóban, a váltás előtt frissítenie kell a módosításokat. Ebben az esetben exportálhatja & importálhatja a metatárolót, majd újra frissíthet.

## <a name="remove-the-old-production"></a>A régi termelés eltávolítása

Miután megerősítette, hogy a kiadás teljes és teljes mértékben működőképes, eltávolíthatja a 3.6-os verziót és az előző metakészletet. A környezet törlése előtt győződjön meg arról, hogy minden áttelepítésre kerül.

## <a name="query-execution-across-hdinsight-versions"></a>Lekérdezés-végrehajtás a HDInsight-verziókban

A HIVe/LLAP-lekérdezések hdinsight 3.6-os fürtön belüli végrehajtásának és hibakeresésének két módja van. A HiveCLI parancssori élményt nyújt, a Tez nézet/Hive nézet pedig GUI-alapú munkafolyamatot.

A HDInsight 4.0-s felbontásban a HiveCLI-t a Beeline váltotta fel. A HiveCLI a Hiveserver 1 takarékossági ügyfele, a Beeline pedig egy JDBC-ügyfél, amely hozzáférést biztosít a Hiveserver 2-höz. A Beeline bármely más JDBC-kompatibilis adatbázis-végponthoz is használható. A Beeline azonnal elérhető a HDInsight 4.0-n, mindenféle telepítés nélkül.

A HDInsight 3.6-ban a Hive-kiszolgálóval való interakcióra szolgáló grafikus felhasználói felület-ügyfél az Ambari Hive nézet. A HDInsight 4.0 nem szállít ambari nézetben. Biztosítottuk ügyfeleink számára a Data Analytics Studio (DAS) használatát, amely nem egy alapvető HDInsight-szolgáltatás. A DAS nem szállít hdinsight-fürtökkel, és nem hivatalosan támogatott csomag. A DAS azonban [parancsfájlművelettel](../hdinsight-hadoop-customize-cluster-linux.md) telepíthető a fürtre az alábbiak szerint:

|Tulajdonság | Érték |
|---|---|
|Parancsfájl típusa|- Egyéni|
|Név|Das|
|Bash parancsfájl URI|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Csomóponttípus(ok)|Head|

Várjon 10-15 percet, majd indítsa el `https://CLUSTERNAME.azurehdinsight.net/das/`a Data Analytics Studio-t a következő URL-cím használatával: .

A DAS elérése előtt szükség lehet az Ambari felhasználói felület frissítésére és/vagy az összes Ambari összetevő újraindítására.

A DAS telepítése után, ha nem látja a lekérdezések megjelenítőben futtatott lekérdezéseket, tegye a következő lépéseket:

1. Állítsa be a Hive, A Tez és a DAS konfigurációit az ebben az útmutatóban leírtak [szerint a DAS telepítésének hibaelhárításához.](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)
2. Győződjön meg arról, hogy az alábbi Azure-tárolókönyvtár-konfigurációk lapblobok, és a következők: `fs.azure.page.blob.dirs`
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Indítsa újra a HDFS,Hive, Tez és DAS mindkét headnodes.

## <a name="next-steps"></a>További lépések

* [A HDInsight 4.0 közleménye](../hdinsight-version-release.md)
* [HDInsight 4.0 mély merülés](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID táblázatok](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
