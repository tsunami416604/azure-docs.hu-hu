---
title: Magas rendelkezésre állású összetevők az Azure HDInsightban
description: A HDInsight-fürtök által használt különböző magas rendelkezésre állású összetevők áttekintése.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 38fb45fd339b5e2c7cab6f66a1ed6c0df73fb29e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74069624"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Az Azure HDInsight által támogatott magas rendelkezésre állási szolgáltatások

 Annak érdekében, hogy az elemzési összetevők számára optimális rendelkezésre állást biztosítson, a HDInsight egyedi architektúrával lett kifejlesztve a kritikus szolgáltatások magas rendelkezésre állásának (HA) biztosítására. Az architektúra egyes összetevőit a Microsoft fejlesztette ki az automatikus feladatátvétel biztosítása érdekében. Az egyéb összetevők olyan szabványos Apache-összetevők, amelyek meghatározott szolgáltatások támogatására vannak telepítve. Ez a cikk ismerteti a HDInsight HA szolgáltatásmodelljének architektúráját, azt, hogy a HDInsight hogyan támogatja a HA-szolgáltatások feladatátvételét, és ajánlott eljárásokat a szolgáltatás más megszakításai ból való helyreállításhoz.

## <a name="high-availability-infrastructure"></a>Magas rendelkezésre állású infrastruktúra

A HDInsight testre szabott infrastruktúrát biztosít annak biztosítására, hogy négy elsődleges szolgáltatás magas rendelkezésre állású legyen automatikus feladatátvételi képességekkel:

- Apache Ambari szerver
- Alkalmazás idővonal-kiszolgáló apache YARN-hoz
- A Hadoop MapReduce feladatelőzmények-kiszolgálója
- Apache Livy

Ez az infrastruktúra számos szolgáltatásból és szoftverösszetevőből áll, amelyek közül néhányat a Microsoft tervezett. A következő összetevők a HDInsight platformon egyediek:

- Másodlagos feladatátvételi vezérlő
- Fő feladatátvevő vezérlő
- Slave magas rendelkezésre állású szolgáltatás
- Fő magas rendelkezésre állású szolgáltatás

![magas rendelkezésre állású infrastruktúra](./media/hdinsight-high-availability-components/high-availability-architecture.png)

Vannak más magas rendelkezésre állási szolgáltatások is, amelyeket a nyílt forráskódú Apache megbízhatósági összetevők támogatnak. Ezek az összetevők a HDInsight-fürtökön is jelen vannak:

- Hadoop fájlrendszer (HDFS) NameNode
- YARN Erőforráskezelő
- HBase főkiszolgáló

A következő szakaszok részletesebben ismertetik, hogyan működnek együtt ezek a szolgáltatások.

## <a name="hdinsight-high-availability-services"></a>HDInsight magas rendelkezésre állási szolgáltatások

A Microsoft támogatja a hdinsight-fürtök alábbi táblázatában található négy Apache-szolgáltatást. Ha meg szeretné különböztetni őket az összetevők által támogatott magas rendelkezésre állású szolgáltatásoktól az Apache-tól, *HDInsight HA-szolgáltatásoknak*nevezzük őket.

| Szolgáltatás | Fürtcsomópontok | Fürttípusok | Cél |
|---|---|---|---|
| Apache Ambari szerver| Aktív fejnóm | Összes | Figyeli és kezeli a fürtöt.|
| Alkalmazás idővonal-kiszolgáló apache YARN-hoz | Aktív fejnóm | Minden, kivéve Kafka | A fürtön futó YARN-feladatok hibakeresési adatait tartja karban.|
| A Hadoop MapReduce feladatelőzmények-kiszolgálója | Aktív fejnóm | Minden, kivéve Kafka | A MapReduce feladatok hibakeresési adatait tartja karban.|
| Apache Livy | Aktív fejnóm | Spark | Lehetővé teszi a Spark-fürttel való egyszerű interakciót REST-felületen keresztül |

>[!Note]
> A HDInsight Enterprise Security Package (ESP) fürtök jelenleg csak az Ambari kiszolgáló magas rendelkezésre állását biztosítják.

### <a name="architecture"></a>Architektúra

Minden HDInsight-fürt két headnodeaktív és készenléti módban rendelkezik. A HDInsight HA-szolgáltatások csak headnodes-on futnak. Ezeknek a szolgáltatásoknak mindig az aktív headnode-on kell futniuk, és le kell állítaniuk és karbantartási módban kell helyezniük a készenléti fejnómon.

A HA-szolgáltatások megfelelő állapotának fenntartása és a gyors feladatátvétel biztosítása érdekében a HDInsight az Apache ZooKeeper-t használja, amely az elosztott alkalmazások koordinációs szolgáltatása, aktív headnode-választás lebonyolításához. A HDInsight néhány háttér Java-folyamatot is tartalmaz, amelyek koordinálják a HDInsight HA-szolgáltatások feladatátvételi eljárását. Ezek a szolgáltatások a következők: a fő feladatátvevő vezérlő, a szolga feladatátvételi vezérlő, a *master-ha-service*és a *slave-ha-service*.

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Az Apache ZooKeeper egy nagy teljesítményű koordinációs szolgáltatás az elosztott alkalmazások hoz. Az éles környezetben a ZooKeeper általában replikált módban fut, ahol a ZooKeeper kiszolgálók replikált csoportja kvórumot alkot. Minden HDInsight-fürt három ZooKeeper-csomópontpal rendelkezik, amelyek lehetővé teszik, hogy három ZooKeeper-kiszolgáló kvórumot hozzon létre. A HDInsight két ZooKeeper kvórummal rendelkezik, amelyek párhuzamosan futnak egymással. Az egyik kvórum dönti el az aktív headnode egy fürtben, amelyen hdinsight HA-szolgáltatások futtatásához. Egy másik kvórum az Apache által nyújtott HA-szolgáltatások koordinálására szolgál, a későbbi szakaszokban részletezett módon.

### <a name="slave-failover-controller"></a>Másodlagos feladatátvételi vezérlő

A másodlagos feladatátvételi vezérlő egy HDInsight-fürt minden csomópontján fut. Ez a vezérlő felelős az Ambari ügynök és a *slave-ha-szolgáltatás* elindításáért minden csomóponton. Rendszeresen lekérdezi az első ZooKeeper kvórumot az aktív csomópontról. Amikor az aktív és a készenléti fejcsomópontok megváltoznak, a másodlagos feladatátvevő vezérlő a következőket hajtja végre:

1. Frissíti a gazdagép konfigurációs fájlját.
1. Újraindítja az Ambari ügynököt.

A *slave-ha-service* felelős a HDInsight HA szolgáltatások leállításáért (az Ambari kiszolgáló kivételével) a készenléti csomóponton.

### <a name="master-failover-controller"></a>Fő feladatátvevő vezérlő

A fő feladatátvételi vezérlő mindkét headnodes fut. Mindkét fő feladatátvételi vezérlők kommunikálni az első ZooKeeper kvórum, hogy jelölje ki a csomópont, hogy ők futnak, mint az aktív csomópont.

Ha például a 0-s fő feladatátvételi vezérlő nyeri meg a választást, a következő módosítások történnek:

1. A 0-s fejnode aktívvá válik.
1. A fő feladatátvevő vezérlő elindítja az Ambari-kiszolgálót és a *fő-ha-szolgáltatást* a 0 csomóponton.
1. A másik fő feladatátvevő vezérlő leállítja az Ambari-kiszolgálót és a *fő-ha-szolgáltatást* az 1-es csomóponton.

A master-ha-service csak az aktív headnode-on fut, leállítja a HDInsight HA-szolgáltatásokat (kivéve az Ambari kiszolgálót) készenléti csomóponton, és elindítja őket az aktív headnode-on.

### <a name="the-failover-process"></a>A feladatátvételi folyamat

![feladatátvételi folyamat](./media/hdinsight-high-availability-components/failover-steps.png)

Az állapotfigyelő minden csomóponton fut a fő feladatátvételi vezérlővel együtt, hogy hearbeat értesítéseket küldjön a Zookeeper kvórumának. A headnode ebben a forgatókönyvben ha szolgáltatásnak minősül. Az állapotfigyelő ellenőrzi, hogy minden magas rendelkezésre állású szolgáltatás kifogástalan állapotú-e, és készen áll-e a vezető választáshoz való csatlakozásra. Ha igen, ez a headnode fog versenyezni a választáson. Ha nem, akkor kilép a választás, amíg nem lesz kész újra.

Ha a készenléti headnode valaha is eléri a vezetést, és aktívvá válik (például az előző aktív csomópont meghibásodása esetén), a fő feladatátvevő vezérlő elindítja az összes HDInsight HA-szolgáltatást rajta. A fő feladatátvételi vezérlő is leállítja ezeket a szolgáltatásokat a másik headnode.The master feladatátvételi vezérlő is leállítja ezeket a szolgáltatásokat a másik headnode.

A HDInsight HA szolgáltatás hibák, például egy szolgáltatás nem megfelelő vagy nem megfelelő állapotú, a fő feladatátvevő vezérlő automatikusan újra kell indítania, vagy állítsa le a szolgáltatásokat a headnode állapota szerint. A felhasználók nem indíthatják el manuálisan a HDInsight HA-szolgáltatásokat mindkét főcsomóponton. Ehelyett engedélyezze az automatikus vagy manuális feladatátvételt a szolgáltatás helyreállításához.

### <a name="inadvertent-manual-intervention"></a>Véletlen kézi beavatkozás

A HDInsight HA-szolgáltatások csak az aktív headnode-on fussanak, és szükség esetén automatikusan újraindulnak. Mivel az egyes HA-szolgáltatások nem rendelkeznek saját állapotfigyelővel, a feladatátvétel nem indítható el az egyes szolgáltatások szintjén. Feladatátvétel biztosított a csomópont szintjén, és nem a szolgáltatás szintjén.

### <a name="some-known-issues"></a>Néhány ismert probléma

- Ha manuálisan indít el egy HA szolgáltatást a készenléti csomóponton, nem áll le, amíg a következő feladatátvétel meg nem történik. Ha a HA-szolgáltatások mindkét headnodes- on futnak, néhány lehetséges probléma a következő: Az Ambari felhasználói felülete nem érhető el, az Ambari hibákat dob, yarn, Spark és Oozie feladatok elakadhatnak.

- Ha egy HA-szolgáltatás az aktív headnode leáll, nem indul újra, amíg a következő feladatátvétel történik, vagy a fő feladatátvevő vezérlő/master-ha-service újraindul. Ha egy vagy több HA-szolgáltatás leáll az aktív csomóponton, különösen akkor, ha az Ambari-kiszolgáló leáll, az Ambari felhasználói felület nem érhető el, más lehetséges problémák közé tartozik a YARN, a Spark és az Oozie-feladatok hibái.

## <a name="apache-high-availability-services"></a>Apache magas rendelkezésre állású szolgáltatások

Az Apache magas rendelkezésre állást biztosít a HDFS NameNode, a YARN ResourceManager és a HBase Master számára, amelyek a HDInsight-fürtökben is elérhetők. A HDInsight HA-szolgáltatásokkal ellentétben az ESP-fürtök támogatják őket. Az Apache HA-szolgáltatások kommunikálnak a második ZooKeeper kvórummal (a fenti szakaszban leírtak szerint) aktív/készenléti állapotok megválasztásához és automatikus feladatátvétel hez. A következő szakaszok ismertetik, hogyan működnek ezek a szolgáltatások.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Hadoop elosztott fájlrendszer (HDFS) NameNode

Az Apache Hadoop 2.0-s vagy újabb verzión alapuló HDInsight-fürtök magas rendelkezésre állást biztosítanak a NameNode számára. Két NameNodes fut a headnodes, amelyek automatikus feladatátvételre vannak konfigurálva. A NameNodes a *ZKFailoverController* segítségével kommunikál a Zookeeper-rel az aktív/készenléti állapot megválasztásához. A *ZKFailoverController* mindkét headnodes fut, és ugyanúgy működik, mint a fenti fő feladatátvételi vezérlő.

A második Zookeeper kvórum független az első kvórumtól, így előfordulhat, hogy az aktív NameNode nem fut az aktív csomóponton. Ha az aktív NameNode halott vagy nem megfelelő állapotú, a készenléti NameNode megnyeri a választást, és aktívvá válik.

### <a name="yarn-resourcemanager"></a>YARN Erőforráskezelő

Az Apache Hadoop 2.4-es vagy újabb verzióialapján működő HDInsight-fürtök támogatják a YARN ResourceManager magas rendelkezésre állását. Két ResourceManagers, rm1 és rm2, futó headnode 0 és headnode 1, illetve. A NameNode-hoz hasonlóan a YARN ResourceManager is automatikus feladatátvételre van konfigurálva. Egy másik ResourceManager automatikusan aktívnak lesz beválasztva, amikor az aktuális aktív ResourceManager leáll vagy nem válaszol.

A YARN ResourceManager a beágyazott *ActiveStandbyElector-t* hibaérzékelőként és vezető választóként használja. A HDFS NameNode-tal ellentétben a YARN ResourceManager-nek nincs szüksége külön ZKFC démonra. Az aktív ResourceManager írja az állapotait az Apache Zookeeper.The active ResourceManager writes its states into Apache Zookeeper.

A YARN ResourceManager magas rendelkezésre állása független a NameNode és más HDInsight HA-szolgáltatásoktól. Előfordulhat, hogy az aktív ResourceManager nem fut az aktív headnode-on vagy azon a headnode-on, ahol az aktív NameNode fut. A YARN ResourceManager magas rendelkezésre állásáról a [ResourceManager magas rendelkezésre állása című](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html)témakörben talál további információt.

### <a name="hbase-master"></a>HBase főkiszolgáló

A HDInsight HBase-fürtök támogatják a HBase Master magas rendelkezésre állását. Eltérően más HA szolgáltatások, amelyek futnak headnodes, HBase Masters fut a három Zookeeper csomópontok, ahol egyikük az aktív mester és a másik kettő készenléti. A NameNode-hoz hasonlóan a HBase Master is koordinátái az Apache Zookeeper-rel a vezető választásához, és automatikus feladatátvételt végeznek, ha az aktuális aktív főkiszolgáló problémákkal küzd. Egyszerre csak egy aktív HBase Master van.

## <a name="next-steps"></a>További lépések

- [Az Apache Hadoop-fürtök elérhetősége és megbízhatósága a HDInsightban](hdinsight-high-availability-linux.md)
- [Azure HDInsight virtuális hálózati architektúra](hdinsight-virtual-network-architecture.md)
