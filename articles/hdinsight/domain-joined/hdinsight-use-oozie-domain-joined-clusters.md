---
title: Biztonságos, vállalati biztonsági csomag – Azure HDInsight az Apache Oozie-munkafolyamatok
description: Biztonságos Apache Oozie munkafolyamatokat az Azure HDInsight vállalati biztonsági csomag használatával. Megtudhatja, hogyan Oozie munkafolyamatokat, és az Oozie-feladatok elküldéséhez.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 15cdc78559a8f299e2bf0f357bbb7c0664881712
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58116894"
---
# <a name="run-apache-oozie-in-hdinsight-hadoop-clusters-with-enterprise-security-package"></a>Futtathat Apache Oozie a HDInsight Hadoop-fürtöket a vállalati biztonsági csomaggal

Az Apache Oozie egy munkafolyamat és koordinációs rendszer, amely az Apache Hadoop-feladatokat kezeli. Az Oozie integrálva van a Hadoop-veremmel, és támogatja a következő feladatokat:
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Az Oozie használatával a rendszer, például Java programok vagy héjparancsfájlok ütemezésére adott feladatok ütemezéséhez.

## <a name="prerequisite"></a>Előfeltétel

- Az Azure HDInsight Hadoop-fürtön vállalati biztonsági csomag (ESP). Lásd: [konfigurálása HDInsight-fürtök ESP](./apache-domain-joined-configure-using-azure-adds.md).

    > [!NOTE]  
    > Az Oozie használata az ESP fürtökön részletes utasításokért lásd: [Linux-alapú Azure HDInsight az Apache Oozie használata a munkafolyamatok](../hdinsight-use-oozie-linux-mac.md).

## <a name="connect-to-an-esp-cluster"></a>ESP-fürthöz kapcsolódás

A Secure Shell (SSH) további információért lásd: [HDInsight (Hadoop) SSH-val csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Csatlakozás a HDInsight-fürthöz SSH használatával:  
   ```bash
   ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
   ```

2. A sikeres Kerberos-hitelesítés ellenőrzéséhez használja a `klist` parancsot. Ha nem, használja a `kinit` elindításához a Kerberos-hitelesítést.

3. A HDInsight-átjárón való regisztráláshoz be kell jelentkeznie az OAuth-jogkivonatot az Azure Data Lake Storage eléréséhez szükséges:   
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    Válasz állapotkódot **200 OK** sikeres regisztrációját jelzi. Ha egy jogosulatlan érkezik válasz, mint a 401-es, ellenőrizze a felhasználónevet és jelszót.

## <a name="define-the-workflow"></a>Határozhat meg munkafolyamatot
Az Oozie munkafolyamat-definíciókhoz az Apache Hadoop folyamat adatdefiníciós nyelv (hPDL) nyelven íródtak. hPDL folyamat XML-definíció nyelven. A következő lépésekkel határozhat meg munkafolyamatot:

1. Állítsa be a munkaterület egy tartományi felhasználó:
   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```
   Cserélje le `DomainUser` tartományi felhasználónévvel. 
   Cserélje le `DomainUserPath` a kezdőkönyvtár elérési útvonal a tartományi felhasználók számára. 
   Cserélje le `ClusterVersion` a fürt Hortonworks Data Platform (HDP) verziójával.

2. A következő utasítás használatával hozhat létre és módosíthat egy új fájlt:
   ```bash
   nano workflow.xml
   ```

3. Miután megnyílik a nano szerkesztő, adja meg a következő XML formátumú, a fájl tartalmát:
   ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://hn0-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
   ```
4. Cserélje le `clustername` a fürt nevére. 

5. Mentse a fájlt, jelölje be a Ctrl + X. Írja be a `Y` (igen) kifejezést. Válassza ki **Enter**.

    A munkafolyamat két részre oszlik:
   * **Hitelesítő adatok szakaszban.** Ebben a szakaszban a hitelesítő adatait, az Oozie-művelet hitelesítéséhez használt fogadja:

     Ez a példa a Hive-műveletek hitelesítést használ. További tudnivalókért lásd: [művelet hitelesítési](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

     A hitelesítőadat-szolgáltatás engedélyezi a felhasználó megszemélyesítése Hadoop-szolgáltatásokhoz való hozzáférésre vonatkozó Oozie műveletek.

   * **A művelet szakasz.** Ebben a szakaszban három műveletet tartalmaz: mapreduce, Hive server 2 és a Hive server 1:

     - A művelet végrehajtása egy példa az Oozie csomagból mapreduce, amely az összesített word-count térkép csökkentheti.

     - A Hive server 2 és a Hive server 1 műveletek-lekérdezést futtathat egy minta megadott HDInsight Hive-táblába.

     A Hive-műveletek használata a hitelesítő adatokat határozza meg a hitelesítő adatok szakaszban hitelesítéshez kulcsszó használatával `cred` művelet elemében.

6. A következő parancs használatával másolja a `workflow.xml` fájlt `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`:
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

7. Cserélje le `domainuser` a felhasználónevet használva a tartományhoz.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Az Oozie-feladat tulajdonságai-fájljának definiálása

1. A következő utasítás használatával hozhat létre és módosíthat egy új fájlt a feladat tulajdonságai:

   ```bash
   nano job.properties
   ```

2. Miután megnyílik a nano szerkesztő, használja a következő XML-kódot a fájl tartalmát:

   ```bash
       nameNode=adl://home
       jobTracker=headnodehost:8050
       queueName=default
       examplesRoot=examples
       oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
       hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
       hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
       oozie.use.system.libpath=true
       user.name=[domainuser]
       jdbcPrincipal=hive/hn0-<ClusterShortName>.<Domain>.com@<Domain>.COM
       jdbcURL=[jdbcurlvalue]
       hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
       hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   * Használja a `adl://home` URI-JÁT a `nameNode` tulajdonságot, ha az Azure Data Lake Storage Gen1 rendelkezik az elsődleges fürt tárolóként. Ha az Azure Blob Storage használ, majd módosítsa ezt a `wasb://home`. Ha az Azure Data Lake Storage Gen2 használ, majd módosítsa ezt a `abfs://home`.
   * Cserélje le `domainuser` a felhasználónevet használva a tartományhoz.  
   * Cserélje le `ClusterShortName` a fürt rövid nevét. Például, ha a fürt neve https:// *[példa hivatkozás]* sechadoopcontoso.azurehdisnight.net, a `clustershortname` az első hat karakter hosszú lehet a fürt: **sechad**.  
   * Cserélje le `jdbcurlvalue` a JDBC URL-címet a Hive-konfigurációból. Ilyen például, jdbc:hive2: / / headnodehost:10001 /; átviteli = http.      
   * Mentse a fájlt, jelölje ki a Ctrl + X, adja meg `Y`, majd válassza ki **Enter**.

   Ez a fájl tulajdonságok megtalálhatónak kell lennie helyi Oozie feladatok futtatásakor.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Egyéni Hive-parancsprogramok, az Oozie-feladatok létrehozása

Hive server 1 és a Hive server 2, ahogyan az alábbi szakaszok a két Hive-parancsprogramok is létrehozhat.

### <a name="hive-server-1-file"></a>Hive server 1-fájl

1.  Hozzon létre, és Hive server 1 műveletek egy fájl szerkesztése:
    ```bash
    nano countrowshive1.hql
    ```

2.  Hozza létre a parancsfájlt:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  Mentse a fájlt az Apache Hadoop elosztott fájlrendszer (HDFS):
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Hive server 2-fájl

1.  Hozzon létre, és a Hive server 2 műveletek mező szerkesztése:
    ```bash
    nano countrowshive2.hql
    ```

2.  Hozza létre a parancsfájlt:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  Mentse a fájlt a HDFS-be:
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Az Oozie-feladatok elküldése

ESP fürtök Oozie-feladatok elküldése olyan, mint az ESP fürtök Oozie-feladatok elküldése.

További információkért lásd: [használata Apache Oozie megadásához és a munkafolyamat futtatása a Linux-alapú Azure HDInsight az Apache Hadoop-](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Az Oozie-feladat küldése eredményei
Az Oozie-feladatok futnak, a felhasználó számára. Ezért az Apache Hadoop YARN és az Apache Ranger audit naplók megjelenítése a megszemélyesített felhasználóként futtatott feladatok. A parancssori felület kimeneti Oozie-feladat a következő kódhoz hasonlóan néz ki:



```bash
    Job ID : 0000015-180626011240801-oozie-oozi-W
    ------------------------------------------------------------------------------------------------
    Workflow Name : map-reduce-wf
    App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
    Status        : SUCCEEDED
    Run           : 0
    User          : alicetest
    Group         : -
    Created       : 2018-06-26 19:25 GMT
    Started       : 2018-06-26 19:25 GMT
    Last Modified : 2018-06-26 19:30 GMT
    Ended         : 2018-06-26 19:30 GMT
    CoordAction ID: -
    
    Actions
    ------------------------------------------------------------------------------------------------
    ID                      Status  Ext ID          ExtStatus   ErrCode
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@:start:    OK  -           OK      -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@mr-test    OK  job_1529975666160_0051  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive2    OK  job_1529975666160_0053  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive OK  job_1529975666160_0055  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@end    OK  -           OK      -
    -----------------------------------------------------------------------------------------------
```

A Ranger Hive server 2 műveletek vizsgálati naplók megjelenítése a felhasználó művelet futtatása Oozie. A Ranger és a YARN nézetek csak a fürt rendszergazda láthatók

## <a name="configure-user-authorization-in-oozie"></a>Az Oozie felhasználói hitelesítés konfigurálása

Oozie önmagában, amely segítségével megakadályozhatja a felhasználók leállítása vagy törlése a többi felhasználó feladatok felhasználói engedélyezési konfigurációval rendelkezik. Ez a konfiguráció engedélyezéséhez állítsa be a `oozie.service.AuthorizationService.security.enabled` való `true`. 

További információkért lásd: [Apache Oozie-telepítés és konfigurálás](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

Az összetevők, például a Hive server 1, ahol a Ranger beépülő modul nem érhető el vagy nem támogatott csak a HDFS coarse-grained engedélyezési lehetőség. Minden részletre kiterjedő engedélyezési csak a Ranger modulok keresztül érhető el.

## <a name="get-the-oozie-web-ui"></a>Az Oozie webes felület beolvasása

Az Oozie webes felület lehetővé teszi a webes az Oozie-feladat állapotát a fürtön. A webes felhasználói felületen lekéréséhez tegye a következőket ESP fürtök:

1. Adjon hozzá egy [élcsomópont](../hdinsight-apps-use-edge-node.md) , és engedélyezze [SSH Kerberos-hitelesítés](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Kövesse a [Oozie webes felhasználói felületen](../hdinsight-use-oozie-linux-mac.md) az élcsomóponthoz SSH-Alagútkezelés engedélyezéséhez és a webes felhasználói felületen eléréséhez.

## <a name="next-steps"></a>További lépések
* [Az Apache Hadoop Apache Oozie használatával határozza meg, és a munkafolyamat futtatása a Linux-alapú Azure HDInsight](../hdinsight-use-oozie-linux-mac.md).
* [Időalapú Apache Oozie-koordinátor használata](../hdinsight-use-oozie-coordinator-time.md).
* [Csatlakozhat a HDInsight (az Apache Hadoop) SSH-val](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
