---
title: A HDInsight-tartományhoz csatlakoztatott fürtök Hadoop Oozie-munkafolyamatok
description: Használat Hadoop Oozie a Linux-alapú HDInsight-tartományhoz csatlakoztatott vállalati biztonsági csomag. Megtudhatja, hogyan Oozie munkafolyamatokat, és az Oozie-feladatok elküldéséhez.
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: omidm
ms.openlocfilehash: 928f6adbb348683a110f7da9b20efaae998290ca
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972213"
---
#<a name="run-apache-oozie-in-domain-joined-hdinsight-hadoop-clusters"></a>Az Apache Oozie futtatása a tartományhoz csatlakoztatott HDInsight Hadoop-fürtök
Az Oozie egy rendszer munkafolyamat és összehangoláshoz, amely a Hadoop-feladatokat kezeli. Az Oozie integrálva van a Hadoop-veremmel, és támogatja a következő feladatokat:
- Az Apache MapReduce
- Az Apache Pig
- Az Apache Hive
- Az Apache sqoop használatával

Az Oozie használatával a rendszer, például Java programok vagy héjparancsfájlok ütemezésére adott feladatok ütemezéséhez.

##<a name="prerequisites"></a>Előfeltételek:
- Tartományhoz csatlakoztatott HDInsight Hadoop-fürt. Lásd: [konfigurálása tartományhoz csatlakoztatott HDInsight-fürtök](./apache-domain-joined-configure-using-azure-adds.md)

    > [!NOTE]
    > A részletes utasításokat talál, az Oozie használata a tartományhoz nem csatlakozó fürtök Lásd [Ez](../hdinsight-use-oozie-linux-mac.md)

##<a name="connecting-to-domain-joined-cluster"></a>Tartományhoz csatlakozás egy tartományhoz csatlakozó fürthöz
További információk az SSH: [hitelesítés: tartományhoz csatlakoztatott HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
- Csatlakozzon SSH-val a HDInsight-fürthöz:
     ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```
Győződjön meg arról, hogy a Kerberos-hitelesítés sikeres volt-e, használja a `klist` parancsot. Ha nem, használja a `kinit` kezdeményezése a Kerberos-hitelesítést.

- Jelentkezzen be az oAuth-jogkivonatot az Azure Data Lake Store (ADLS) eléréséhez szükséges regisztrálni HDInsight-átjárón
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    Válasz állapotkódot _200 OK_ sikeres regisztrációját jelzi. Ellenőrizze a felhasználónevet és jelszót, ha jogosulatlan választ kapott (401-es).

## <a name="define-the-workflow"></a>Határozhat meg munkafolyamatot
Oozie munkafolyamat-meghatározások a Hadoop folyamat adatdefiníciós nyelv (hPDL), amely folyamat XML-definíció nyelven íródtak. A következő lépések használatával határozhat meg munkafolyamatot:

-   Tartományi felhasználó munkaterület beállításával:
 ```bash
hdfs dfs -mkdir /user/<DomainUser>
cd /home/<DomainUserPath>
cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
tar -xvf oozie-examples.tar.gz
hdfs dfs -put examples /user/<DomainUser>/
 ```
Cserélje le `DomainUser` tartományi felhasználónévvel. Cserélje le `DomainUserPath` a kezdőkönyvtár elérési útvonal a tartományi felhasználók számára. Cserélje le `ClusterVersion` a fürt HDP verziójával.

-   A következő utasítás használatával hozhat létre és módosíthat egy új fájlt:
 ```bash
nano workflow.xml
 ```

- Miután megnyílik a nano szerkesztő, adja meg a következő XML formátumú, a fájl tartalmát:
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
Cserélje le `clustername` a fürt nevére. 

Mentse a fájlt, jelölje ki a Ctrl + X, adja meg `Y`, majd válassza ki **Enter**.

A munkafolyamat két részre oszlik:
*   Hitelesítő adatok szakaszban: A hitelesítő adatok szakaszban fogadja az oozie-művelet hitelesítéséhez használt hitelesítő adatokkal:

    Ebben a példában a Hive-műveletek hitelesítést használja. További tudnivalókért lásd: [ez]( https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

    A hitelesítőadat-szolgáltatás engedélyezi a felhasználó megszemélyesítése Hadoop-szolgáltatásokhoz való hozzáférésre vonatkozó oozie műveletek.

*   Művelet szakasz: Három műveletet itt-mapreduce, a hive server 2 művelet és a egy hive-kiszolgáló 1 műveleti rendelkezünk:

    A példában az oozie-csomagból map-reduce futtat-mapreduce amely kiírja a word összesített száma.

    A hive server 2 és a hive server 1 műveletek egy egyszerű lekérdezést végrehajtja a HDInsight megadott hivesample táblán.

    A hive-műveletek kulcsszó használatával hitelesítést a hitelesítő adatok szakaszban megadott hitelesítő adatok használata `cred` művelet eleme

- A következő parancs használatával másolja a `workflow.xml` fájlt `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`:
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

    Cserélje le `domainuser` a felhasználónevet használva a tartományhoz.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Az Oozie-feladat tulajdonságai-fájljának definiálása

1.  A következő utasítás használatával hozhat létre és módosíthat egy új fájlt a feladat tulajdonságai:
     ```bash
    nano job.properties
     ```

2.   Miután megnyílik a nano szerkesztő, használja a következő XML-kódot a fájl tartalmát:

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
    

   * Cserélje le `domainuser` a felhasználónevet használva a tartományhoz.
   * Cserélje le `ClusterShortName` a gazdagépnév, a fürt számára. Ha a fürtnév https://sechadoopcontoso.azurehdisnight.net, a `clustershortname` van a fürt első hat betűje: sechad
   * Cserélje le `jdbcurlvalue` JDBC URL-címmel, a hive-konfiguráció. Ha például jdbc:hive2: / / headnodehost:10001 /; átviteli = http.
    
   * Mentse a fájlt, jelölje ki a Ctrl + X, adja meg `Y`, majd válassza ki **Enter**.

   * Ez a fájl tulajdonságok megtalálhatónak kell lennie helyi oozie feladatok futtatásakor

## <a name="creating-custom-hive-scripts-for-the-oozie-job"></a>Egyéni hive parancsfájlok, az Oozie-feladat létrehozása
A hive server 1 és a hive server 2 2 hive-parancsprogramok következőképpen hozható létre:
-   Hive Server 1 fájl:
1.  A következő utasítás használatával hozhat létre és módosíthat egy fájlt a hive server 1 művelet:
    ```bash
    nano countrowshive1.hql
    ```

2.  A parancsfájl létrehozása
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  Mentse a fájlt a Hdfs-be
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

-   Hive Server 2 fájl:
1.  A következő utasítás használatával hozhat létre és módosíthat egy mező hive server 2 művelet:
    ```bash
    nano countrowshive2.hql
    ```

2.  A parancsfájl létrehozása
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  Mentse a fájlt a Hdfs-be
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submission-of-oozie-jobs"></a>Az Oozie-feladatok beküldése
A tartományhoz csatlakoztatott fürtök oozie-feladatok elküldése a nem tartományhoz csatlakoztatott fürtök oozie-feladatok elküldése hasonló.

További információért tekintse meg [nyújt, és kezelheti a feladat](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-oozie-job-submission"></a>Az Oozie feladatküldéshez eredményei
Oozie-feladat futtatása be – a felhasználó nevében, mivel a Yarn és a Ranger audit naplók megjelenítése a feladatok végrehajtása a megszemélyesített felhasználó. Az oozie-feladat a parancssori felület kimeneti alább látható:


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

*    A Ranger naplók hive Server 2 műveleteket mutat be oozie, a felhasználó nevében a művelet végrehajtása. A ranger és Yarn nézet csak akkor látható, a fürt rendszergazdának.

## <a name="configuration-of-user-authorization-in-oozie"></a>Az Oozie felhasználói hitelesítés konfigurálása
Oozie önmagában egy felhasználói hitelesítés konfigurációt, amely segítségével megakadályozhatja a felhasználók leáll, a más felhasználói feladatok leállítása rendelkezik. Ez akkor engedélyezett, a `oozie.service.AuthorizationService.security.enabled` való `true`. 

Ezzel kapcsolatban további részletek találhatók Oozie dokumentáció szakasz - [felhasználói hitelesítési konfiguráció]( https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html):

Összetevők, mint például a hive-kiszolgáló, Ranger beépülő modul nem elérhető és támogatott 1 csak coarse-grained hdfs engedélyezési lehetőség. Finom részletes engedélyezési csak ranger beépülő modulok keresztül érhető el.

## <a name="oozie-web-ui"></a>Az Oozie webes felhasználói felületen
Az Oozie webes felület lehetővé teszi a webes az Oozie-feladat állapotát a fürtön. A tartományhoz csatlakoztatott fürtök kell:

1. Adjon hozzá egy [élcsomópont](../hdinsight-apps-use-edge-node.md) , és engedélyezze [SSH Kerberos-hitelesítés](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. Kövesse a [Oozie webes felhasználói felületen](../hdinsight-use-oozie-linux-mac.md) az élcsomóponthoz SSH-Alagútkezelés engedélyezéséhez és a webes felhasználói felületen eléréséhez.

## <a name="next-steps"></a>További lépések
* [Az Oozie használata a Hadooppal és a Linux-alapú Azure HDInsight munkafolyamat futtatása](../hdinsight-use-oozie-linux-mac.md)
* [Időalapú Oozie-koordinátor használata](../hdinsight-use-oozie-coordinator-time.md)
* [Az SSH használata a tartományhoz csatlakoztatott HDInsight-fürtök Hive-lekérdezések futtatása](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
