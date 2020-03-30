---
title: Az Apache Oozie munkafolyamatai & Nagyvállalati biztonság – Azure HDInsight
description: Az Azure HDInsight Enterprise Biztonsági csomag használatával biztonságossá teszi az Apache Oozie-munkafolyamatokat. Ismerje meg, hogyan definiálhat oozie-munkafolyamatot, és hogyan küldhet be oozie-feladatot.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seodec18
ms.date: 12/09/2019
ms.openlocfilehash: 9ef54707f7fac3dd1328e29f6d05f62c1dee2561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194903"
---
# <a name="run-apache-oozie-in-hdinsight-hadoop-clusters-with-enterprise-security-package"></a>Az Apache Oozie futtatása a HDInsight Hadoop-fürtökben vállalati biztonsági csomaggal

Az Apache Oozie egy munkafolyamat- és koordinációs rendszer, amely az Apache Hadoop-feladatokat kezeli. Az Oozie integrálva van a Hadoop verembe, és a következő feladatokat támogatja:

- Apache MapReduce
- Apache sertés
- Apache Hive
- Apache Sqoop

Az Oozie használatával a rendszerre jellemző feladatokat, például a Java-programokat vagy a rendszerhéj-parancsfájlokat is ütemezheti.

## <a name="prerequisite"></a>Előfeltétel

Egy Azure HDInsight Hadoop-fürt vállalati biztonsági csomaggal (ESP). Lásd: [HDInsight-fürtök konfigurálása ESP-vel.](./apache-domain-joined-configure-using-azure-adds.md)

> [!NOTE]  
> Az Oozie nem ESP-fürtökön való használatáról az [Apache Oozie-munkafolyamatok használata linuxos Azure HDInsight ban című témakörben talál részletes útmutatást.](../hdinsight-use-oozie-linux-mac.md)

## <a name="connect-to-an-esp-cluster"></a>Csatlakozás ESP-fürthöz

A Biztonságos rendszerhéjról (SSH) további információt a [Csatlakozás hdinsighthoz (Hadoop) SSH használatával című](../hdinsight-hadoop-linux-use-ssh-unix.md)témakörben talál.

1. Csatlakozzon a HDInsight-fürthöz az SSH használatával:

    ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```

1. A sikeres Kerberos-hitelesítés `klist` ellenőrzéséhez használja a parancsot. Ha nem, `kinit` indítsa el a Kerberos-hitelesítést.

1. Jelentkezzen be a HDInsight-átjáróba az Azure Data Lake Storage eléréséhez szükséges OAuth-jogkivonat regisztrálásához:

    ```bash
    curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
    ```

    A **200 OK** állapotválasz-kód a sikeres regisztrációt jelzi. Ellenőrizze a felhasználónevet és a jelszót, ha nem fogadott válasz érkezik, például 401.

## <a name="define-the-workflow"></a>A munkafolyamat meghatározása

Az Oozie munkafolyamat-definíciók apache hadoop folyamatdefiníciós nyelv (hPDL) nyelven íródnak. A hPDL egy XML-folyamatdefiníciós nyelv. A munkafolyamat meghatározásához tegye a következő lépéseket:

1. Tartományi felhasználó munkaterületének beállítása:

   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```

   Cserélje `DomainUser` le a tartomány felhasználónevét.
   Cserélje `DomainUserPath` le a tartományi felhasználó kezdőkönyvtárának elérési útját.
   Cserélje `ClusterVersion` le a fürtadatplatform verziójára.

2. Új fájl létrehozásához és szerkesztéséhez használja a következő utasítást:

   ```bash
   nano workflow.xml
   ```

3. A nanoszerkesztő megnyitása után adja meg a következő XML-t a fájl tartalmaként:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://<active-headnode-name>-<clustername>.<Domain>.com:9083</value>
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

4. Cserélje `clustername` le a fürt nevére.

5. A fájl mentéséhez válassza a **Ctrl+X gombot.** Írja be **az Y**értéket. Ezután válassza **az Enter**lehetőséget.

    A munkafolyamat két részből áll:

   - **Megbízólevél.** Ez a szakasz az Oozie-műveletek hitelesítéséhez használt hitelesítő adatokat veszi figyelembe:

     Ez a példa a Hive-műveletek hitelesítését használja. További információ: [Action Authentication](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

     A hitelesítő adatok szolgáltatás lehetővé teszi, hogy az Oozie-műveletek megszemélyesítsék a felhasználót a Hadoop-szolgáltatások eléréséhez.

   - **Akció.** Ebben a szakaszban három művelet található: térképcsökkentés, Hive-kiszolgáló 2 és Hive-kiszolgáló 1:

     - A map-reduce művelet egy példa egy Oozie csomag map-reduce, amely az összesített szavak számát adja ki.

     - A Hive-kiszolgáló 2 és a Hive-kiszolgáló 1 műveletek lekérdezést futtatnak a HDInsight-hoz mellékelt minta Hive-táblán.

     A Hive-műveletek a hitelesítő adatok szakaszban megadott hitelesítő adatokat használják a hitelesítéshez a műveletelemben lévő kulcsszó `cred` használatával.

6. A `workflow.xml` fájl másolásához használja `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`a következő parancsot:

    ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
    ```

7. Cserélje `domainuser` le a tartomány felhasználónevére.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Az Oozie-feladat tulajdonságfájljának meghatározása

1. A következő utasítással új fájlt hozhat létre és szerkesztthet a feladattulajdonságokhoz:

    ```bash
    nano job.properties
    ```

2. A nanoszerkesztő megnyitása után használja a következő XML-t a fájl tartalmaként:

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
   jdbcPrincipal=hive/<active-headnode-name>.<Domain>.com@<Domain>.COM
   jdbcURL=[jdbcurlvalue]
   hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
   hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   - Használja `adl://home` az URI-t a `nameNode` tulajdonsághoz, ha az Azure Data Lake Storage Gen1 az elsődleges fürttároló. Ha az Azure Blob Storage-t használja, módosítsa ezt a . `wasb://home` Ha az Azure Data Lake Storage Gen2-t `abfs://home`használja, módosítsa ezt a .
   - Cserélje `domainuser` le a tartomány felhasználónevére.  
   - Cserélje `ClusterShortName` le a fürt rövid nevére. Ha például a fürtnév https:// *[példa hivatkozás]* sechadoopcontoso.azurehdisnight.net, akkor a `clustershortname` fürt első hat karaktere: **sechad**.  
   - Cserélje `jdbcurlvalue` le a Hive-konfiguráció JDBC URL-címét. Példa erre a jdbc:hive2://headnodehost:10001/;transportMode=http.
   - A fájl mentéséhez válassza a `Y`Ctrl+X billentyűkombinációt, írja be a billentyűt, majd válassza az **Enter parancsot.**

   Ennek a tulajdonságfájlnak helyileg kell jelen lennie az Oozie-feladatok futtatásakor.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Egyéni Hive-parancsfájlok létrehozása oozie-feladatokhoz

Létrehozhatja a hive-kiszolgáló 1 és hive kiszolgáló 2 két Hive-parancsfájlt a következő szakaszokban látható módon.

### <a name="hive-server-1-file"></a>Hive-kiszolgáló 1 fájlja

1. Fájl létrehozása és szerkesztése a Hive-kiszolgáló 1 műveletekhez:

    ```bash
    nano countrowshive1.hql
    ```

2. Hozza létre a parancsfájlt:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    select devicemake from hivesampletable limit 2;
    ```

3. Mentse a fájlt az Apache Hadoop Distributed File System (HDFS) rendszerbe:

    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Hive-kiszolgáló 2 fájlja

1. A Hive-kiszolgáló 2 műveletek mezőjének létrehozása és szerkesztése:

    ```bash
    nano countrowshive2.hql
    ```

2. Hozza létre a parancsfájlt:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3. Mentse a fájlt hdfs fájlba:

    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Oozie-feladatok beküldése

Oozie-feladatok küldése esp-fürtökhöz olyan, mint oozie-feladatok küldése nem ESP-fürtökben.

További információ: [Az Apache Oozie használata az Apache Hadoop segítségével definiálhatja és futtathatja a munkafolyamatot linuxos Azure HDInsight on.](../hdinsight-use-oozie-linux-mac.md)

## <a name="results-from-an-oozie-job-submission"></a>Oozie-állásbeküldések eredményei

Oozie feladatok futnak a felhasználó számára. Így mind az Apache Hadoop YARN, mind az Apache Ranger naplózási naplói azt mutatják, hogy a feladatok megszemélyesített felhasználóként futnak. Az Oozie-feladat parancssori felületkimenete a következő kódnak tűnik:

```output
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

A Hive-kiszolgáló 2 műveletének Ranger naplózási naplói azt mutatják, hogy az Oozie futtatja a műveletet a felhasználó számára. A Ranger és yarn nézetek csak a fürt rendszergazdája számára láthatók.

## <a name="configure-user-authorization-in-oozie"></a>Felhasználói engedélyezés konfigurálása az Oozie-ban

Az Oozie önmagában olyan felhasználói engedélyezési konfigurációval rendelkezik, amely letilthatja a felhasználókat más felhasználók feladatai leállításában vagy törlésében. A konfiguráció engedélyezéséhez `oozie.service.AuthorizationService.security.enabled` állítsa `true`a beállítását. 

További információ: [Apache Oozie Installation and Configuration](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

Olyan összetevők esetében, mint a Hive server 1, ahol a Ranger beépülő modul nem érhető el vagy nem támogatott, csak durva szemcsés HDFS-engedélyezés lehetséges. A részletes engedély csak ranger beépülő modulokon keresztül érhető el.

## <a name="get-the-oozie-web-ui"></a>Az Oozie webes felhasználói felületének beszereznie

Az Oozie webes felhasználói felület webalapú nézetet biztosít a fürt oozie-feladatainak állapotáról. A webes felhasználói felület levételéhez tegye a következő lépéseket az ESP-fürtökben:

1. Adjon hozzá egy [peremhálózati csomópontot,](../hdinsight-apps-use-edge-node.md) és engedélyezze [az SSH Kerberos-hitelesítést.](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. Kövesse az [Oozie webes felhasználói felületlépéseit](../hdinsight-use-oozie-linux-mac.md) az SSH-bújtatás engedélyezéséhez a peremhálózati csomóponthoz, és férjen hozzá a webes felhasználói felülethez.

## <a name="next-steps"></a>További lépések

- [Az Apache Oozie és az Apache Hadoop segítségével definiálhatja és futtathatja a munkafolyamatot linuxos Azure HDInsight on.](../hdinsight-use-oozie-linux-mac.md)
- [Csatlakozzon a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)
