---
title: Az Azure HDInsight használatával történő HBase hibáinak megoldása
description: Választ kaphat a HBase és az Azure HDInsight való használattal kapcsolatos gyakori kérdésekre.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 13a4831d946eb7e25e586cafae4cae51b49fd8a7
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780761"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Az Apache HBase hibáinak megoldása az Azure HDInsight használatával

Ismerje meg a leggyakoribb problémákat és azok megoldásait, amikor Apache HBase-adattartalommal dolgozik az Apache Ambari-ben.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Több hozzá nem rendelt régióval Hogyan futtatni a hbck-parancsok jelentéseit?

A `hbase hbck` parancs futtatásakor a következő általános hibaüzenet jelenik meg: "több régió sincs kiosztva vagy lyukak a régiók láncában."

A HBase Master felhasználói felületén megtekintheti az összes régió-kiszolgáló közötti kiegyensúlyozatlan régiók számát. Ezután a parancs futtatásával `hbase hbck` megtekintheti a lyukakat a régió láncában.

Előfordulhat, hogy a lyukakat az offline régiók okozzák, ezért először javítsa ki a hozzárendeléseket. 

A nem hozzárendelt régiók normál állapotba való visszaállításához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a HDInsight HBase-fürtbe SSH használatával.
2. A Apache ZooKeeper rendszerhéjhoz való kapcsolódáshoz futtassa a `hbase zkcli` parancsot.
3. Futtassa a `rmr /hbase/regions-in-transition` parancsot vagy a `rmr /hbase-unsecure/regions-in-transition` parancsot.
4. A `hbase zkcli` rendszerhéjból való kilépéshez használja `exit` az parancsot.
5. Nyissa meg az Apache Ambari felhasználói felületét, majd indítsa újra az aktív HBase Master szolgáltatást.
6. Futtassa újra `hbase hbck` a parancsot (beállítások nélkül). Ellenőrizze a parancs kimenetét, és győződjön meg arról, hogy az összes régió hozzá van rendelve.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Hogyan kijavítani az időtúllépési problémákat, amikor hbck-parancsokat használ a régió-hozzárendelésekhez?

### <a name="issue"></a>Probléma

Ha a `hbck` parancs használatakor időtúllépési problémák merülhetnek fel, lehetséges, hogy több régió "átmenetes" állapotban van hosszú ideig. Ezeket a régiókat kapcsolat nélküli üzemmódban láthatja a HBase Master felhasználói felületen. Mivel a nagy számú régió átalakulóban van, HBase Master lehet, hogy időtúllépés történt, és a régiókat nem lehet ismét online állapotba hozni.

### <a name="resolution-steps"></a>A megoldás lépései

1. Jelentkezzen be a HDInsight HBase-fürtbe SSH használatával.
2. A Apache ZooKeeper rendszerhéjhoz való kapcsolódáshoz futtassa a `hbase zkcli` parancsot.
3. Futtassa a `rmr /hbase/regions-in-transition` vagy a `rmr /hbase-unsecure/regions-in-transition` parancsot.
4. A `hbase zkcli` rendszerhéjból való kilépéshez `exit` használja az parancsot.
5. A Ambari felhasználói felületén indítsa újra az aktív HBase Master szolgáltatást.
6. Futtassa újra `hbase hbck -fixAssignments` a parancsot.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Hogyan kényszeríti a HDFS csökkentett üzemmódjának letiltását a fürtben?

### <a name="issue"></a>Probléma

A helyi Apache Hadoop elosztott fájlrendszer (HDFS) biztonságos módban ragadt a HDInsight-fürtön.

### <a name="detailed-description"></a>Részletes leírás

Ezt a hibát a következő HDFS-parancs futtatásakor fellépő hiba okozhatta:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

A parancs futtatásakor a következő hibaüzenet jelenik meg:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
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

A HDInsight-fürt egy nagyon kevés csomópontra lett méretezve. A csomópontok száma nem éri el a HDFS replikálási tényezőt, vagy azokhoz közeledik.

### <a name="resolution-steps"></a>A megoldás lépései 

1. A HDFS állapotának lekérése a HDInsight-fürtön a következő parancsok futtatásával:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
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
2. A HDFS integritását a HDInsight-fürtön is megtekintheti az alábbi parancsokkal:

   ```apache
   hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
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

3. Ha azt állapítja meg, hogy nincs hiányzó, sérült vagy nem replikált blokk, vagy ha ezek a blokkok figyelmen kívül hagyhatók, futtassa a következő parancsot, hogy a név csomóponton kívülre kerüljön a biztonságos módból:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Hogyan kijavítani a JDBC vagy a az sqlline használata kapcsolódási problémáit Apache Phoenix?

### <a name="resolution-steps"></a>A megoldás lépései

Apache Phoenixhoz való kapcsolódáshoz meg kell adnia egy aktív Apache ZooKeeper csomópont IP-címét. Győződjön meg arról, hogy a ZooKeeper szolgáltatás, amelyhez a sqlline.py csatlakozni próbál, fut.
1. Jelentkezzen be a HDInsight-fürtbe SSH használatával.
2. Írja be a következő parancsot:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > Az aktív ZooKeeper csomópont IP-címét a Ambari felhasználói felületéről kérheti le. Ugrás a **HBase** > **Quick Links** > **ZK\* (aktív)**  > **Zookeeper adataira**. 

3. Ha a sqlline.py a Phoenixhez csatlakozik, és nem időtúllépést okoz, futtassa a következő parancsot a Phoenix rendelkezésre állásának és állapotának ellenőrzéséhez:

   ```apache
           !tables
           !quit
   ```      
4. Ha ez a parancs működik, nincs probléma. Lehet, hogy a felhasználó által megadott IP-cím helytelen. Ha azonban a parancs hosszabb ideig szünetel, majd a következő hibaüzenetet jeleníti meg, folytassa az 5. lépéssel.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Futtassa a következő parancsokat a fő csomópontról (hn0) a Phoenix rendszer feltételének diagnosztizálásához. Katalógus táblázata:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   A parancsnak az alábbihoz hasonló hibát kell visszaadnia: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. Az Apache Ambari felhasználói felületén hajtsa végre a következő lépéseket a HMaster szolgáltatás újraindításához az összes ZooKeeper-csomóponton:

    1. A HBase **Összefoglalás** szakaszában válassza a **HBase** > **aktív HBase Master**lehetőséget. 
    2. Az **összetevők** szakaszban indítsa újra a HBase Master szolgáltatást.
    3. Ismételje meg ezeket a lépéseket az összes fennmaradó **készenléti HBase Master** szolgáltatás esetében. 

Akár öt percet is igénybe vehet, amíg a HBase Master szolgáltatás stabilizálni és befejezni a helyreállítási folyamatot. Néhány perc elteltével ismételje meg a sqlline.py-parancsokat annak megerősítéséhez, hogy a rendszer. A katalógus tábla fel van készülve, és le is kérdezhető. 

A rendszeren. A katalógus tábla visszatér a normális kerékvágásba, a Phoenixhez való kapcsolódási problémát automatikusan fel kell oldani.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Mi okozza, hogy a főkiszolgáló nem indul el?

### <a name="error"></a>Hiba 

Atomi átnevezési hiba történik.

### <a name="detailed-description"></a>Részletes leírás

Az indítási folyamat során a HMaster számos inicializálási lépést hajt végre. Ezek közé tartozik az adatok áthelyezése a Scratch (. tmp) mappából az adatmappába. A HMaster a Write-Ahead logs (WALs) mappában is megtekintheti, hogy vannak-e nem válaszoló régió-kiszolgálók, és így tovább. 

Az indítás során a HMaster alapszintű `list` parancsot végez ezeken a mappákon. Ha bármikor, a HMaster egy nem várt fájlt lát ezen mappák valamelyikében, kivételt jelez, és nem indul el.  

### <a name="probable-cause"></a>Lehetséges ok

A régió-kiszolgáló naplóiban próbálja meg azonosítani a fájl létrehozásának idővonalát, majd ellenőrizze, hogy a fájl létrehozásakor összeomlott-e a folyamat. (Forduljon a HBase támogatási szolgálatához, hogy segítsen ennek megvalósításában.) Ez segít a robusztus mechanizmusok biztosításában, így elkerülhető a hiba elhagyása, és gondoskodhat a zökkenőmentes folyamatok leállításáról.

### <a name="resolution-steps"></a>A megoldás lépései

Ellenőrizze a hívási veremet, és próbálja meg megállapítani, hogy melyik mappa okozza a problémát (például lehet, hogy a WALs mappa vagy a. tmp mappa). Ezután a Cloud Explorerben vagy a HDFS parancsok használatával próbálja megkeresni a problémát tartalmazó fájlt. Ez általában egy \*-renamePending. JSON fájl. (A \*-renamePending. JSON fájl egy olyan naplófájl, amely az atomi átnevezési művelet megvalósítására szolgál a WASB-illesztőprogramban. Az ebben a megvalósításban található hibák miatt ezek a fájlok a folyamat összeomlása után is megmaradhatnak, és így tovább.) Kényszerítse a fájl törlését a Cloud Explorerben vagy a HDFS parancsok használatával. 

Időnként előfordulhat, hogy az adott helyen a *$ $ $. $ $* $ nevű ideiglenes fájl is szerepel. A fájl megjelenítéséhez a `ls` HDFS parancsot kell használnia; a fájl nem látható a Cloud Explorerben. A fájl törléséhez használja a HDFS parancsot `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.  

A parancsok futtatása után a HMaster azonnal el kell indulnia. 

### <a name="error"></a>Hiba

Nem szerepel kiszolgálócím a *hbase: meta* for régió XXX-ben.

### <a name="detailed-description"></a>Részletes leírás

Előfordulhat, hogy a Linux-fürtön megjelenik egy üzenet, amely azt jelzi, hogy a *hbase: meta* tábla nem online állapotú. A `hbck` Futtatás jelenthet jelentést arról, hogy a "hbase: meta Table replicaId 0 nem található bármely régióban." A probléma oka az lehet, hogy a HMaster nem tudott inicializálni a HBase újraindítása után. A HMaster-naplókban a következő üzenet jelenhet meg: "Nem szerepel a hbase: meta a régió hbase: a biztonsági mentési \<régió neve\>" nevű kiszolgálócím.  

### <a name="resolution-steps"></a>A megoldás lépései

1. A HBase-rendszerhéjban írja be a következő parancsokat (módosítsa a tényleges értékeket, ha vannak ilyenek):  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Törölje a *hbase: Namespace* bejegyzést. Lehet, hogy ez a bejegyzés ugyanaz a hiba, amelyet a rendszer a *hbase: Namespace* tábla vizsgálata során jelentett.

3. Ha a HBase egy futó állapotban szeretné felvenni, akkor a Ambari felhasználói felületén indítsa újra az aktív HMaster szolgáltatást.  

4. Az összes offline tábla létrehozásához a HBase-rendszerhéjban futtassa a következő parancsot:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>További olvasnivaló

[A HBase tábla nem dolgozható fel](https://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Hiba

A HMaster időtúllépést okoz a "Java. IO. IOException hasonló végzetes kivétellel: Időtúllépés-300000ms várakozás a névtér-tábla hozzárendelésére. "

### <a name="detailed-description"></a>Részletes leírás

Ez a probléma akkor fordulhat elő, ha sok olyan táblája és régiója van, amely nem lett kiürítve a HMaster-szolgáltatások újraindításakor. Az újraindítás sikertelen lehet, és az előző hibaüzenet jelenik meg.  

### <a name="probable-cause"></a>Lehetséges ok

Ez a HMaster szolgáltatás ismert problémája. Az általános fürt indítási feladatai hosszú időt vehetnek igénybe. A HMaster leállt, mert a névtér-tábla még nincs hozzárendelve. Ez csak olyan forgatókönyvekben fordul elő, amelyekben nagy mennyiségű kiürítetlen adatok létezik, és egy 5 perces időkorlát nem elegendő.
  
### <a name="resolution-steps"></a>A megoldás lépései

1. Az Apache Ambari felhasználói felületén nyissa meg a **HBase** > **konfigurációit**. Az egyéni hbase-site. xml fájlban adja hozzá a következő beállítást: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Indítsa újra a szükséges szolgáltatásokat (HMaster és esetleg más HBase-szolgáltatásokat).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Mi okozza az újraindítási hibát a régió-kiszolgálókon?

### <a name="issue"></a>Probléma

Az ajánlott eljárások követésével megakadályozható, hogy a régió-kiszolgálón újraindítási hiba történjen. Javasoljuk, hogy a HBase-régió kiszolgálóinak újraindításakor ne szüneteltesse a nagy számítási feladatok tevékenységeit. Ha egy alkalmazás továbbra is csatlakozik a régió-kiszolgálókkal, ha a Leállítás folyamatban van, a régió-kiszolgáló újraindítási művelete több percet is igénybe vehet. Emellett érdemes kiüríteni az összes táblát. A táblák kiürítésével kapcsolatban lásd [: HDInsight HBase: Az Apache HBase-fürt újraindítási idejének javítása táblák](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)kiürítésével.

Ha az Apache Ambari felhasználói felületéről kezdeményezi az újraindítási műveletet a HBase-régió kiszolgálóin, azonnal láthatja, hogy a régió-kiszolgálók leálltak, de nem azonnal indulnak el. 

A következő lépések történnek a jelenetek mögött: 

1. A Ambari ügynök leállítási kérelmet küld a régió-kiszolgálónak.
2. A Ambari-ügynök 30 másodpercig várakozik, hogy a régió kiszolgálója szabályosan leálljon. 
3. Ha az alkalmazás továbbra is kapcsolódik a régió-kiszolgálóhoz, a kiszolgáló nem fog azonnal leállni. A 30 másodperces időtúllépés lejár a leállítás előtt. 
4. 30 másodperc elteltével a Ambari-ügynök kényszerített kill (`kill -9`) parancsot küld a régió-kiszolgálónak. Ezt a ambari-ügynök naplójában tekintheti meg (a megfelelő munkavégző csomópont/var/log/könyvtárában):

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
   A hirtelen leállítás miatt előfordulhat, hogy a folyamathoz társított port nem jelenik meg, noha a régió-kiszolgáló folyamat leáll. Ez a helyzet egy AddressBindException vezethet a régió kiszolgálójának indításakor, ahogy az a következő naplókon is látható. Ezt a Region-Server. log naplófájlban tekintheti meg azon munkavégző csomópontok/var/log/hbase könyvtárában, amelyeken a régió-kiszolgáló nem indul el. 

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

1. Az újraindítás elindítása előtt próbálja csökkenteni a HBase-régió kiszolgálóinak terhelését. 
2. Másik lehetőségként (ha az 1. lépés nem segít), próbálja meg manuálisan újraindítani a régió-kiszolgálókat a munkavégző csomópontokon a következő parancsok használatával:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>Lásd még:
[Hibaelhárítás az Azure HDInsight segítségével](../../hdinsight/hdinsight-troubleshoot-guide.md)
