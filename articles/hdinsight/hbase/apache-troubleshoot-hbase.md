---
title: Az Azure HDInsight használatával történő HBase hibáinak megoldása
description: Választ kaphat a HBase és az Azure HDInsight való használattal kapcsolatos gyakori kérdésekre.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: cf44c27f51bf6312ef546b424646833f69ba0283
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69638418"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Az Apache HBase hibáinak megoldása az Azure HDInsight használatával

Ismerje meg a leggyakoribb problémákat és azok megoldásait, amikor Apache HBase-adattartalommal dolgozik az Apache Ambari-ben.

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

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
