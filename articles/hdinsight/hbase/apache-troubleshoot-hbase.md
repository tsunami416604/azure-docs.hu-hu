---
title: Azure HDInsight HBase hibaelhárításáról |} Microsoft Docs
description: A HBase és az Azure HDInsight kapcsolatos gyakori kérdésekre adott válaszok.
services: hdinsight
documentationcenter: ''
author: nitinver
manager: ashitg
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: nitinver
ms.openlocfilehash: d5d50121cd0375af1b57baadeb40efb237aaea11
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="troubleshoot-hbase-by-using-azure-hdinsight"></a>A HBase hibaelhárítása az Azure HDInsight használatával

A legfőbb problémákat és azok megoldásait ismerje meg az Apache Ambari az Apache HBase hasznos adat található használatakor.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Hogyan futtathatok hbck parancs jelentéseket a ki nem osztott több régióba?

Egy általános hibaüzenetet, láthatja, ha futtatja a `hbase hbck` parancs a "több régióba alatt hozzá nem rendelt vagy régiókban láncában lyuk."

A HBase fő felhasználói felületén láthatja, hogy minden régióban kiszolgálóján is tette régiók számát. Ezt követően futtathatja `hbase hbck` parancsot a régió lánc lyuk megtekintéséhez.

Lyuk előfordulhat, hogy a kapcsolat nélküli régiók által okozott, ezért javítsa ki a hozzárendelések először. 

Ahhoz, hogy a ki nem osztott régiók normál állapotra, kövesse az alábbi lépéseket:

1. Jelentkezzen be a HDInsight HBase-fürtöt SSH használatával.
2. A ZooKeeper rendszerhéj kapcsolódni, futtassa a `hbase zkcli` parancsot.
3. Futtassa a `rmr /hbase/regions-in-transition` parancs vagy a `rmr /hbase-unsecure/regions-in-transition` parancsot.
4. Kilépés a a `hbase zkcli` rendszerhéj, használja a `exit` parancsot.
5. Az Apache Ambari felhasználói felületének megnyitásához, és indítsa újra az aktív HBase fő szolgáltatás.
6. Futtassa a `hbase hbck` parancsot ismét (kapcsolók) nélkül. Ellenőrizze, győződjön meg arról, hogy minden egyes hozzárendelve a parancs kimenetét.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Hogyan oldja meg időtúllépés problémák, régió hozzárendelések hbck parancsok használata esetén?

### <a name="issue"></a>Probléma

Az időtúllépés problémák használatakor egyik lehetséges oka a `hbck` parancs lehet, hogy több régiók állapotban van a "az átmeneti" hosszú ideig. Azokban a régiókban, kapcsolat nélküli a HBase fő felhasználói felületén tekintheti meg. Régiók nagy mennyiségű átmenet megpróbálja, mert a HBase fő időtúllépés lehet, és nem lehet azokban a régiókban újra online állapotba.

### <a name="resolution-steps"></a>A megoldás lépései

1. Jelentkezzen be a HDInsight HBase-fürtöt SSH használatával.
2. A ZooKeeper rendszerhéj kapcsolódni, futtassa a `hbase zkcli` parancsot.
3. Futtassa a `rmr /hbase/regions-in-transition` vagy a `rmr /hbase-unsecure/regions-in-transition` parancsot.
4. A kilépéshez a `hbase zkcli` rendszerhéj, használja a `exit` parancsot.
5. Az Ambari felhasználói felületén indítsa újra az aktív HBase fő szolgáltatást.
6. Futtassa a `hbase hbck -fixAssignments` újra a parancsot.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Hogyan tegye I kényszerített – letiltása HDFS csökkentett módban egy fürtben?

### <a name="issue"></a>Probléma

A helyi Hadoop elosztott fájlrendszerrel (HDFS) Beragadt a HDInsight-fürt csökkentett módban.

### <a name="detailed-description"></a>Részletes leírás

Ez a hiba oka lehet a hiba a következő HDFS parancs futtatásakor:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

A hiba, láthatja, ha a parancs futtatásakor így néz ki:

```apache
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>Lehetséges ok

A HDInsight-fürt csökkentették le egy nagyon kevés csomópontot. A csomópontok száma alatt, vagy a HDFS replikációs tényező közel van.

### <a name="resolution-steps"></a>A megoldás lépései 

1. A HDInsight-fürt a HDFS állapotának lekérése a következő parancsok futtatásával:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdiuser@hn0-spark2:~$ hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   Safe mode is ON
   Configured Capacity: 3372381241344 (3.07 TB)
   Present Capacity: 3138625077248 (2.85 TB)
   DFS Remaining: 3102710317056 (2.82 TB)
   DFS Used: 35914760192 (33.45 GB)
   DFS Used%: 1.14%
   Under replicated blocks: 0
   Blocks with corrupt replicas: 0
   Missing blocks: 0
   Missing blocks (with replication factor 1): 0

   -------------------------------------------------
   Live datanodes (8):

   Name: 10.0.0.17:30010 (10.0.0.17)
   Hostname: 10.0.0.17
   Decommission Status : Normal
   Configured Capacity: 421547655168 (392.60 GB)
   DFS Used: 5288128512 (4.92 GB)
   Non DFS Used: 29087272960 (27.09 GB)
   DFS Remaining: 387172253696 (360.58 GB)
   DFS Used%: 1.25%
   DFS Remaining%: 91.85%
   Configured Cache Capacity: 0 (0 B)
   Cache Used: 0 (0 B)
   Cache Remaining: 0 (0 B)
   Cache Used%: 100.00%
   Cache Remaining%: 0.00%
   Xceivers: 2
   Last contact: Wed Apr 05 16:22:00 UTC 2017
   ...

   ```
2. Is ellenőrizheti a HDFS a HDInsight-fürt integritását a következő parancsokkal:

   ```apache
   hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
   ```

   ```apache
   Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
   FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
   ....................................................................................................

   ....................................................................................................
   ..................Status: HEALTHY
   Total size:    9330539472 B
   Total dirs:    37
   Total files:   2618
   Total symlinks:                0 (Files currently being written: 2)
   Total blocks (validated):      2535 (avg. block size 3680686 B)
   Minimally replicated blocks:   2535 (100.0 %)
   Over-replicated blocks:        0 (0.0 %)
   Under-replicated blocks:       0 (0.0 %)
   Mis-replicated blocks:         0 (0.0 %)
   Default replication factor:    3
   Average block replication:     3.0
   Corrupt blocks:                0
   Missing replicas:              0 (0.0 %)
   Number of data-nodes:          8
   Number of racks:               1
   FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds

   The filesystem under path '/' is HEALTHY
   ```

3. Ha azt állapítja meg, amelyek nincsenek nincs hiányzik, sérült, vagy under-replikált blokkolja, vagy az, hogy ezek a blokkok figyelmen kívül hagyható, a következő parancsot a név csomópont biztonságos módból érvénybe:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Hogyan állítsa helyre JDBC vagy SQLLine elérhetőségét Apache Phoenix problémái?

### <a name="resolution-steps"></a>A megoldás lépései

Phoenix kapcsolódni, az aktív ZooKeeper csomópont IP-címét kell megadnia. Győződjön meg arról, hogy a ZooKeeper szolgáltatást, hogy mely sqlline.py csatlakozni próbál működik és elérhető.
1. Jelentkezzen be a HDInsight-fürthöz SSH használatával.
2. Írja be a következő parancsot:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > Az IP-cím, az aktív ZooKeeper csomópontjának lekérheti az Ambari felhasználói felületén. Ugrás a **HBase** > **Gyorshivatkozások** > **ZK\* (aktív)** > **Zookeeper információ**. 

3. Ha a sqlline.py Phoenix csatlakozik, és amelyen nincs időtúllépés, futtassa a következő parancsot a rendelkezésre állás és a Phoenix állapotának ellenőrzése:

   ```apache
           !tables
           !quit
   ```      
4. Ez a parancs működik, ha nincs probléma. Lehet, hogy a felhasználó által megadott IP-cím helytelen. Ha a parancs hosszabb ideig szünetelteti, és ezután a következő hibaüzenetet jeleníti meg, azonban továbbra is az 5.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. A következő parancsokat a feltétel a Phoenix rendszer diagnosztizálása érdekében a központi csomópontból (hn0). KATALÓGUS tábla:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   A parancs visszaadja-e hiba a következőhöz hasonló: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. Az Ambari felhasználói felületén hajtsa végre az alábbi lépéseket az összes ZooKeeper csomópontok HMaster szolgáltatás újraindításához:

    1. Az a **összegzés** HBase, szakasza lépjen **HBase** > **aktív HBase fő**. 
    2. Az a **összetevők** szakaszban, indítsa újra a HBase fő szolgáltatást.
    3. Ismételje meg ezeket a lépéseket minden fennmaradó **készenléti HBase fő** szolgáltatások. 

A HBase fő szolgáltatás stabilizálását, és fejezze be a helyreállítási folyamat akár öt percet is igénybe vehet. Néhány perc elteltével ismételje meg a sqlline.py parancsokat annak ellenőrzésére, hogy a rendszer. KATALÓGUS tábla működik-e, és, hogy az informatikai kérdezhetők le. 

Amikor a rendszer. KATALÓGUS tábla vissza a normál, a kapcsolódási problémát a Phoenix automatikusan feloldja kell lennie.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Mi okozhatja, hogy elindítsa főkiszolgálóvá?

### <a name="error"></a>Hiba 

Egy atomi átnevezési hiba történik.

### <a name="detailed-description"></a>Részletes leírás

A rendszerindítási folyamat során HMaster sok inicializálási lépések befejeződik. Ezek közé tartozik a teljesen új (.tmp) mappából adatok áthelyezése a data mappán. HMaster is ellenőrzi, hogy az a írási előre naplók (WALs) mappát, ha van-e a nem válaszoló régió kiszolgálók, és így tovább. 

Rendszerindítás közben HMaster does alapvető `list` parancs ezeket a mappákat. Ha bármikor HMaster meglátja ezt egy váratlan fájlt ezeket a mappákat, kivételt jelez, és nem indul el.  

### <a name="probable-cause"></a>Lehetséges ok

A régió kiszolgálónaplókban próbálja ki a fájl létrehozásának ütemterv azonosíthatja, és ha hiba történt a fájl létrehozásának környékén folyamat crash megjelenik. (Ügyfélszolgálatához HBase segít ennek során.) Ez segítséget nyújt nekünk nyújtanak robusztusabb mechanizmusok, így elkerülheti, hogy elérte-e ezt a hibát, és szabályos folyamat leállítások biztosítása.

### <a name="resolution-steps"></a>A megoldás lépései

Ellenőrizze a hívási verem és határozza meg, előfordulhat, hogy melyik mappához okozza a problémát (például lehet a WALs vagy a .tmp mappában). A Cloud Explorer vagy a HDFS parancs használatával ezután próbálja meg a probléma fájl megkereséséhez. Általában ez jelenti a \*-renamePending.json fájlt. (A \*-renamePending.json fájl egy atomi átnevezése a WASB illesztő végrehajtásához használt napló fájlt. Ebben a megvalósításban hibák miatt ezeket a fájlokat is hagyható után folyamat leállásából eredő hiba, és így tovább.) Ez a fájl, a Cloud Explorer vagy a HDFS-parancsok segítségével kényszerített-törlés. 

Egyes esetekben előfordulhat, hogy is van egy ideiglenes fájlt hasonlót *$$$. $$$* ezen a helyen. Akkor alkalmazza a HDFS `ls` nem jelennek meg a fájlt a Cloud Explorer; Ez a fájl megtekintéséhez utasításhoz. Ez a fájl törléséhez használja a HDFS parancs `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.  

Ezek a parancsok futtatása után HMaster azonnal elindul. 

### <a name="error"></a>Hiba

Nincs kiszolgálói cím szerepel *hbase: meta* a régió xxx.

### <a name="detailed-description"></a>Részletes leírás

Előfordulhat, hogy megjelenik egy üzenet, amely azt jelzi, hogy a Linux-fürt a *hbase: meta* tábla nem érhető el. Futó `hbck` előfordulhat, hogy jelenti, hogy "hbase: meta tábla Replikaazonosítójú 0 nem található a bármely régióban." A probléma lehet, hogy HMaster nem tudta inicializálni a HBase újraindítása után. A HMaster naplófájlokban, előfordulhat, hogy üzenetet látja: "nem kiszolgálói címet, a hbase szerepel: meta a terület hbase: biztonsági mentés \<terület neve\>".  

### <a name="resolution-steps"></a>A megoldás lépései

1. A HBase rendszerhéjban adja meg a következő parancsokat (tényleges értékek módosítása megfelelő):  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Törölje a *hbase: névtér* bejegyzés. Ez a bejegyzés lehet ugyanaz a hiba, amely alatt jelentett, ha a *hbase: névtér* tábla üzemeltet.

3. HBase elindítani fut, az Ambari felhasználói felületén, indítsa újra az aktív HMaster szolgáltatást.  

4. A HBase rendszerhéj ahhoz, hogy az összes kapcsolat nélküli tábláit, futtassa a következő parancsot:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>További olvasnivaló

[Nem sikerült feldolgozni a HBase táblában](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Hiba

Hasonló végzetes kivétel miatt időtúllépése HMaster "java.io.IOException: időtúllépésbe került 300000ms névtér tábla hozzárendelendő vár."

### <a name="detailed-description"></a>Részletes leírás

A probléma tapasztalhat, ha sok táblák és régiók, amely nem ki lettek ürítve a HMaster szolgáltatás újraindításakor. Újraindítás sikertelen lehet, és látni fogja az előző hibaüzenet.  

### <a name="probable-cause"></a>Lehetséges ok

Ez az egy ismert probléma az HMaster szolgáltatással. Általános fürt indítási feladatok hosszú időt vehet igénybe. HMaster leáll, mert a névtér tábla még nincs hozzárendelve. Ez akkor fordul elő, csak a forgatókönyvek, ahol nagy adatmennyiség unflushed létezik, és egy öt perces időkorlát túl kicsi.
  
### <a name="resolution-steps"></a>A megoldás lépései

1. Az Ambari felhasználói felületén, nyissa meg a **HBase** > **Configs**. Az egyéni hbase-site.xml fájlban adja hozzá a következő beállítást: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Indítsa újra a szükséges szolgáltatásokat (HMaster, és valószínűleg más HBase szolgáltatások).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Mi újraindítás hibát okoz a régió kiszolgálón?

### <a name="issue"></a>Probléma

Előfordulhat, hogy régió kiszolgáló újraindítása meghibásodása megakadályozása következő bevált gyakorlatát. Azt javasoljuk, hogy nagy munkaterhelést tevékenység felfüggesztése, ha azt tervezi, hogy a HBase régió kiszolgálók újraindítása. Ha egy alkalmazás tovább régió kiszolgálókkal való csatlakozás, ha leállítása folyamatban van, a régiót server Újraindítási művelet lassabb lesz által néhány percig. Azt is érdemes először ürítse ki az összes tábla. Hogyan táblák ürítése referenciáért lásd: [HDInsight HBase: a HBase fürt újraindítási idejét javítására által táblák kiürítési](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Ha kezdeményezni az újraindítást, a HBase régió kiszolgálókon a az Ambari felhasználói Felületéről, azonnal láthatja, hogy a régió kiszolgálók csökkent, de azok azonnal ne indítsa újra. 

Mi történik a háttérben itt található: 

1. Az Ambari ügynök leállítási kérelmet küld a régió kiszolgáló.
2. Az Ambari ügynök megvárja, amíg a régió kiszolgáló leállítása 30 másodpercig. 
3. Ha az alkalmazás továbbra is fennáll, a régiót kiszolgálóval kapcsolódni, a kiszolgáló nem áll le azonnal. A 30 másodperces időtúllépési előtt leállítás lejár. 
4. 30 másodperc az Ambari ügynök küld egy kényszerített kill (`kill -9`) parancsot a terület-kiszolgálóhoz. Az ambari-ügynököt tartalmaz (a címtárban /var/napló/a megfelelő munkavégző csomópont) látható:

   ```apache
           2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
           est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
           f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
           -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
           2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
           /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
           PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
           nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
           2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
           2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
   ```
A lezárás miatt a folyamathoz társított port előfordulhat, hogy nem oldhatók fel, annak ellenére, hogy a régió server-folyamat leáll. Ez a helyzet vezethet egy AddressBindException a régió kiszolgáló indításakor, ahogy az a következő naplók kapcsolódnak. Ez a terület-Server.log elérési úton található a munkavégző csomópontokról, ahol a régió kiszolgáló indítása sikertelen /var/log/hbase könyvtárában található ellenőrizheti. 

   ```apache

   2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
   java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
   at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
   at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
        
   Caused by: java.lang.reflect.InvocationTargetException
   at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
   at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
   at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
   at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
   ... 5 more
        
   Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
   at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
   at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
   at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
   ... 10 more
        
   Caused by: java.net.BindException: Address already in use
   at sun.nio.ch.Net.bind0(Native Method)
   at sun.nio.ch.Net.bind(Net.java:463)
   at sun.nio.ch.Net.bind(Net.java:455)
   at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
   at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
   ... 15 more
   ```

### <a name="resolution-steps"></a>A megoldás lépései

1. Próbálja meg újraindítás elindítása előtt, ami csökkenti a HBase régió kiszolgálók terhelését. 
2. Másik lehetőségként (ha 1. lépésben nem oldották), manuálisan indítsa újra a munkavégző csomóponton régió kiszolgálók a következő parancsokkal:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>Lásd még:
[Hibaelhárítás az Azure HDInsight segítségével](../../hdinsight/hdinsight-troubleshoot-guide.md)
