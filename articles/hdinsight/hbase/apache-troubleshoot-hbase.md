---
title: A HBase hibaelhárítása az Azure HDInsight használatával
description: Válaszok a HBase és az Azure HDInsight használata kapcsolatos gyakori kérdésekre.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 37a8882653ffede121d2e2cd3f3357741d8d641a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336424"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Az Azure HDInsight az Apache HBase hibaelhárítása

A leggyakoribb problémák és azok megoldásait ismerje meg az Apache Ambari az Apache HBase is észleltünk adattartalmakat használatakor.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Hogyan futtathatok hbck parancs jelentések ki nem osztott több régióval?

Egy általános hibaüzenetet, láthatja, ha futtatja a `hbase hbck` parancs a "több alatt hozzá nem rendelt régióban vagy régiók láncában lyuk."

A HBase-Mesterfelület láthatja, hogy az összes régióbeli kiszolgálók között vannak kiegyensúlyozatlan régiók száma. Ezt követően futtathatja `hbase hbck` parancsot lyuk a régió lánc megtekintéséhez.

Lyuk lehet, hogy a kapcsolat nélküli régiókban okozta, így javítsa ki a hozzárendelések először. 

Ahhoz, hogy a ki nem osztott régiók vissza a normál állapotban, végezze el az alábbi lépéseket:

1. Jelentkezzen be az SSH segítségével a HDInsight HBase-fürtnek.
2. Az Apache ZooKeeper shell kapcsolódni, futtassa a `hbase zkcli` parancsot.
3. Futtassa a `rmr /hbase/regions-in-transition` parancsot vagy a `rmr /hbase-unsecure/regions-in-transition` parancsot.
4. Kilépés a a `hbase zkcli` rendszerhéj, használja a `exit` parancsot.
5. Nyissa meg az Apache Ambari felhasználói felületén, és indítsa újra az aktív HBase főkiszolgáló.
6. Futtassa a `hbase hbck` parancsot újra (kapcsolók) nélkül. Ellenőrizze, győződjön meg arról, hogy minden régióban hozzárendeli a következő parancs kimenetét.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Hogyan lehet kijavítani a hibát időtúllépési problémák régió hozzárendelések hbck parancsok használatakor?

### <a name="issue"></a>Probléma

Időtúllépési problémák használata esetén egy lehetséges oka a `hbck` parancs lehet, hogy számos régióban állapotban van a "az átmeneti" hosszú ideje. Ezekben a régiókban, a HBase-Mesterfelület az offline megjelenik. Régiók nagy számú átmeneti próbál, mert a HBase főkiszolgáló előfordulhat, hogy időtúllépés, és nem lehet azokban a régiókban ismét online állapotba.

### <a name="resolution-steps"></a>A megoldás lépései

1. Jelentkezzen be az SSH segítségével a HDInsight HBase-fürtnek.
2. Az Apache ZooKeeper shell kapcsolódni, futtassa a `hbase zkcli` parancsot.
3. Futtassa a `rmr /hbase/regions-in-transition` vagy a `rmr /hbase-unsecure/regions-in-transition` parancsot.
4. Kilép a `hbase zkcli` rendszerhéj, használja a `exit` parancsot.
5. Az Ambari felhasználói felületén indítsa újra az aktív HBase főkiszolgáló szolgáltatást.
6. Futtassa a `hbase hbck -fixAssignments` újra a parancsot.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Hogyan do I force-letiltása HDFS csökkentett módban egy fürtben?

### <a name="issue"></a>Probléma

A helyi Apache Hadoop elosztott fájlrendszer (HDFS) elakadt csökkentett módban, a HDInsight-fürtön.

### <a name="detailed-description"></a>Részletes leírás

Ez a hiba oka lehet egy hiba a következő HDFS parancs futtatásakor:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

A hiba, láthatja, ha a parancs futtatásakor a következőhöz hasonló:

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

A HDInsight-fürt mérete le egy nagyon kevés csomópontot. A csomópontok számát alább, vagy a HDFS replikációs tényező közelében van.

### <a name="resolution-steps"></a>A megoldás lépései 

1. A HDFS állapotának lekérése a HDInsight-fürtön a következő parancsok futtatásával:

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
2. Akkor is is sértetlenségének ellenőrzéséhez a HDFS a HDInsight-fürtön a következő parancsokat:

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

3. Ha azt állapítja meg, amelyek nincsenek nem hiányzik, sérült, vagy under-replikált blokkolja, vagy hogy blokkokat figyelmen kívül hagyható, futtassa a következő parancsot a név csomópont biztonságos módból érvénybe:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Hogyan lehet kijavítani a hibát hoz a JDBC vagy az SQLLine kapcsolódási problémái az Apache Phoenix?

### <a name="resolution-steps"></a>A megoldás lépései

Csatlakozás az Apache Phoenix, az aktív Apache ZooKeeper-csomópont IP-címét kell megadnia. Győződjön meg arról, hogy a ZooKeeper-szolgáltatást, hogy melyik sqlline.py csatlakozni próbál működik-e.
1. Jelentkezzen be a HDInsight-fürthöz SSH használatával.
2. Írja be a következő parancsot:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > Az Ambari felhasználói felületén is kap az aktív ZooKeeper-csomópont IP-címét. Lépjen a **HBase** > **Gyorshivatkozások** > **ZK\* (aktív)** > **Zookeeper Info**. 

3. Ha az sqlline.py Phoenix csatlakozik, és nem időkorlátja nem, futtassa a következő parancsot a rendelkezésre állás és a Phoenix állapotának ellenőrzése:

   ```apache
           !tables
           !quit
   ```      
4. Ez a parancs működik, ha nincs probléma. Lehet, hogy a felhasználó által megadott IP-cím helytelen. Ha a parancs hosszabb ideig szünetelteti, és ezután a következő hibaüzenetet jeleníti meg, azonban továbbra is az 5. lépés.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Futtassa az alábbi parancsokat a fő csomópont (hn0), a feltétel a Phoenix rendszer diagnosztizálása érdekében. KATALÓGUS tábla:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   A parancs a hiba az alábbihoz hasonlóan kell visszaadnia: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. Az Apache Ambari felhasználói felületén hajtsa végre az alábbi lépéseket a ZooKeeper-csomópontok a HMaster szolgáltatás újraindításához:

    1. Az a **összefoglalás** szakaszában a HBase, lépjen a **HBase** > **aktív HBase főkiszolgáló**. 
    2. Az a **összetevők** szakaszban, a HBase főkiszolgáló szolgáltatás újraindításához.
    3. Ismételje meg ezeket a lépéseket minden fennmaradó **készenléti HBase főkiszolgáló** szolgáltatások. 

A HBase főkiszolgáló szolgáltatás stabilizálódhatnak, majd fejezze be a helyreállítási folyamat akár öt percet is igénybe vehet. Néhány perc múlva ismételje meg az sqlline.py parancsokkal ellenőrizheti, hogy a rendszer. KATALÓGUS tábla mentése, és hogy lekérdezhetők legyenek. 

Amikor a rendszer. KATALÓGUS tábla vissza a normál értékre, a kapcsolódási probléma a Phoenix fel kell automatikusan oldani.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>A főkiszolgáló nem indul el, mi okozza?

### <a name="error"></a>Hiba 

Egy atomi átnevezési hiba akkor fordul elő.

### <a name="detailed-description"></a>Részletes leírás

A rendszerindítási folyamat során a HMaster számos inicializálási lépések befejeződik. Ezek közé tartozik, amely adatokat helyez át a teljesen új (.tmp) mappát a data mappához. HMaster is megvizsgálja a írási előre naplók (WALs) mappában vannak-e bármilyen nem válaszoló régióbeli kiszolgálók, és így tovább. 

Rendszerindítás során HMaster does alapszintű `list` parancs ezeket a mappákat. Ha bármikor HMaster kap a fájl váratlanul, ezek a mappák egyikében, kivételt jelez, és nem indul el.  

### <a name="probable-cause"></a>Lehetséges ok

A régió naplófájljai próbálja meg azonosíthatja a támadás idővonalának, a fájl létrehozása, és nézzük meg, ha a fájl létrehozásának időpontja környékén rögzített folyamat összeomlás történt. (A HBase az ügyfélszolgálattól segítséget nyújt a ennek.) Ez segít, hogy elkerülje a hibát, és ellenőrizze, sikeres-e folyamat leállások robusztusabb mechanizmusok velünk a kapcsolatot biztosít.

### <a name="resolution-steps"></a>A megoldás lépései

Ellenőrizze a hívási vermet, és állapítható meg, előfordulhat, hogy melyik mappába okozza a problémát (például lehet a WALs vagy a .tmp mappában). A Cloud Explorerben vagy a HDFS parancs használatával, majd próbálja meg keresse meg a probléma fájlt. Általában ez van egy \*-renamePending.json fájlt. (A \*-renamePending.json fájl egy journal-fájl, amellyel az atomi átnevezési műveletet megvalósítása a WASB-illesztőprogram. Ez a megvalósítás a hibák, mert ezeket a fájlokat is maradhat után a folyamat, és így tovább.) Törlés kényszerített ezt a fájlt a Cloud Explorerben vagy a HDFS-parancsok használatával. 

Egyes esetekben előfordulhat, hogy is van egy hasonló nevű ideiglenes fájl *$$$. $$$* ezen a helyen. Akkor alkalmazza a HDFS `ls` ezt a fájlt parancsot; a fájlt a Cloud Explorer nem fogja látni. Ez a fájl törléséhez használja a HDFS parancs `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.  

Ezek a parancsok futtatása után HMaster azonnal el kell. 

### <a name="error"></a>Hiba

Nincs kiszolgálói cím szerepel *hbase: meta* a régióban: xxx.

### <a name="detailed-description"></a>Részletes leírás

Előfordulhat, hogy megjelenik egy üzenet, amely azt jelzi, hogy a Linux-fürtön a *hbase: meta* tábla nem érhető el. Futó `hbck` előfordulhat, hogy jelenti, hogy "a hbase: meta tábla replicaId 0 nem található a bármely régióban." A probléma az lehet, hogy HMaster nem tudta inicializálni a HBase újraindítása után. A HMaster-naplók az üzenetet láthatja: "Nincs kiszolgáló-cím szerepel a hbase: régió hbase meta: biztonsági mentési \<régió neve\>".  

### <a name="resolution-steps"></a>A megoldás lépései

1. A HBase rendszerhéj adja meg a következő parancsokat (tényleges értékek módosítása, amennyiben alkalmazhatók):  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Törölje a *hbase: névtér* bejegyzés. Lehet, hogy ez a bejegyzés alatt álló ugyanaz a hiba jelentve, ha a *hbase: névtér* tábla beolvasása.

3. Csatlakozva a HBase futó állapotban, az Ambari felhasználói felületén, indítsa újra az aktív HMaster szolgáltatást.  

4. A HBase rendszerhéj használata offline tábláit, futtassa a következő parancsot:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>További olvasnivaló

[Nem sikerült feldolgozni a HBase-tábla](https://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Hiba

Hasonló végzetes kivétel miatt időtúllépés HMaster "java.io.IOException: Időtúllépés miatt megszakadt 300000ms névtér tábla Várakozás hozzá kell rendelni."

### <a name="detailed-description"></a>Részletes leírás

A probléma tapasztalhat, ha sok tábla és régiók, amelyek rendelkeznek nem kiürítése megtörtént a HMaster szolgáltatás újraindításakor. Újraindítás sikertelen lehet, és az előző hibaüzenet jelenik meg.  

### <a name="probable-cause"></a>Lehetséges ok

Ez az egy ismert probléma az HMaster szolgáltatással. Általános fürt indítási feladatok hosszú időt vehet igénybe. HMaster leáll, mert a névtér tábla még nincs hozzárendelve. Ez akkor fordul elő, csak a forgatókönyvek, ahol nagy unflushed adatmennyiség létezik, és a egy öt perces időkorlát nem elegendő.
  
### <a name="resolution-steps"></a>A megoldás lépései

1. Az Apache Ambari felhasználói felületén, nyissa meg **HBase** > **Configs**. Az egyéni hbase-site.xml fájlban adja hozzá a következő beállítást: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Indítsa újra a szükséges szolgáltatások (HMaster, és valószínűleg más HBase szolgáltatások).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Egy régióban kiszolgálón újraindítás hiba mi okozza?

### <a name="issue"></a>Probléma

Egy régiókiszolgálón újraindítást meghiúsul a következő bevált gyakorlatát előfordulhat, hogy nem történik meg. Azt javasoljuk, hogy nagy munkaterhelést tevékenység felfüggesztése, ha azt tervezi, indítsa újra a HBase-régióbeli kiszolgálók. Ha egy alkalmazás továbbra is régióbeli kiszolgálók összekapcsolása, amikor a Shutdown utasítás folyamatban van, az régió server Újraindítási művelet lassabb lesz szerint néhány percig. Azt is célszerű először kiüríti az összes tábla. Hogyan táblák kiüríteni referenciáért lásd: [HDInsight HBase: Hogyan növelheti az Apache HBase fürt újraindítás időpontja a táblák kiürítette](https://web.archive.org/web/20190112153155/ https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Ha kezdeményez az újraindítási műveletet, a HBase-régióbeli kiszolgálók az Apache Ambari felhasználói felületről, azonnal láthatja, hogy a régióbeli kiszolgálók csökkent, de nem azonnal újraindítás. 

Íme, mi történik a háttérben: 

1. Az Ambari ügynök leállítási kérést küld a régiókiszolgálón.
2. Az Ambari ügynök megvárja, amíg a régió kiszolgáló leállítása 30 másodperc. 
3. Ha az alkalmazás továbbra is a régióbeli kiszolgálók összekapcsolása, a kiszolgáló nem azonnal leállítja. A 30 másodperces időkorlát lejár, mielőtt a leállás akkor fordul elő. 
4. 30 másodperc elteltével az Ambari ügynök küld egy kényszerített kill (`kill -9`) parancsot a terület-kiszolgálóhoz. Ez az ambari-ügynök napló (a címtárban /var/log/a megfelelő munkavégző csomópont) tekintheti meg:

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
   A hirtelen leállás miatt a port, a folyamathoz társított előfordulhat, hogy nem kell szabadítani, annak ellenére, hogy a régió kiszolgáló folyamata leállt. Ez a helyzet vezethet egy AddressBindException a régió kiszolgáló indításakor, ahogyan az a következő naplók kapcsolódnak. Ez a terület-Server.log elérési úton található a feldolgozó csomópontok, ahol a régiókiszolgálón nem indul el /var/log/hbase könyvtárában található ellenőrizheti. 

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

1. Próbálja meg a HBase-régióbeli kiszolgálók terhelésének csökkentése előtt újraindítást kezdeményez. 
2. Másik lehetőségként (ha 1. lépés nem segít), manuálisan indítsa újra a feldolgozó csomópontokon futó régióbeli kiszolgálók a következő parancsokat:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>Lásd még:
[Hibaelhárítás az Azure HDInsight segítségével](../../hdinsight/hdinsight-troubleshoot-guide.md)
