---
title: Az IBM Db2 HADR beállítása az Azure virtuális gépeken ( VM-ek) | Microsoft dokumentumok
description: Az IBM Db2 LUW magas rendelkezésre állását azure-beli virtuális gépeken (VM-eken) hozza létre.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2020
ms.author: juergent
ms.openlocfilehash: 614ac8b651224a3b6ec605a6bffd520449a1d793
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926749"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Az IBM Db2 LUW magas rendelkezésre állása az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server en pacemakerrel

Az IBM Db2 linuxos, UNIX- és Windowsrendszerhez (LUW) [magas rendelkezésre állású és vész-helyreállítási (HADR) konfigurációban](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) lévő egy csomópontból áll, amely elsődleges adatbázispéldányt futtat, és legalább egy csomópontot, amely másodlagos adatbázispéldányt futtat. Az elsődleges adatbázispéldány módosításai a konfigurációtól függően szinkron vagy aszinkron módon replikálódnak egy másodlagos adatbázispéldányra. 

Ez a cikk bemutatja, hogyan telepítheti és konfigurálhatja az Azure virtuális gépeket (VM-ek), telepítheti a fürtkeretrendszert, és hogyan telepítheti az IBM Db2 LUW-t HADR-konfigurációval. 

A cikk nem foglalkozik az IBM Db2 LUW HADR vagy SAP szoftvertelepítéssel történő telepítésével és konfigurálásával. E feladatok elvégzéséhez hivatkozásokat biztosítunk az SAP és az IBM telepítési kézikönyveire. Ez a cikk az Azure-környezetre jellemző alkatrészekre összpontosít. 

A támogatott IBM Db2 verziók 10.5-ös és újabb verziók, az SAP [1928533]megjegyzésében dokumentáltmódon.

A telepítés megkezdése előtt tekintse meg a következő SAP-megjegyzéseket és dokumentációt:

| SAP-jegyzet | Leírás |
| --- | --- |
| [1928533] | SAP-alkalmazások az Azure-ban: Támogatott termékek és Azure virtuálisgép-típusok |
| [2015553] | SAP az Azure-ban: Támogatási előfeltételek |
| [2178632] | Az SAP legfontosabb figyelési metrikái az Azure-ban |
| [2191498] | SAP Linuxon az Azure-ral: Továbbfejlesztett figyelés |
| [2243692] | Linux az Azure-on (IaaS) virtuális gép: SAP licencproblémák |
| [1984787] | SUSE LINUX Enterprise Server 12: Telepítési megjegyzések |
| [1999351] | Továbbfejlesztett Azure-figyelés az SAP-hoz – hibaelhárítás |
| [2233094] | DB6: SAP-alkalmazások az Azure-ban, amelyek AZ IBM Db2 Linux, UNIX és Windows - további információk |
| [1612105] | DB6: Gyakran feltett kérdések a Db2-ről hadr-rel |


| Dokumentáció | 
| --- |
| [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Rendelkezik az összes szükséges SAP-megjegyzésszel Linuxhoz |
| [Az Azure virtuális gépek tervezése és megvalósítása az SAP Linux-alapú útmutatóhoz][planning-guide] |
| [Az Azure virtuális gépek üzembe helyezése az SAP Linux on Linux (ez][deployment-guide] a cikk) |
| [Az Azure Virtual Machines adatbázis-kezelő rendszer(DBMS) üzembe helyezése az SAP Linuxon][dbms-guide] útmutató |
| [SAP-munkaterhelés az Azure tervezési és telepítési ellenőrzőlistájában][azr-sap-plancheck] |
| [A SUSE Linux Enterprise Server for SAP Applications 12 SP4 gyakorlati tanácsok útmutatói][sles-for-sap-bp] |
| [SUSE Linux Enterprise magas rendelkezésre állású bővítmény 12 SP4][sles-ha-guide] |
| [IBM Db2 Azure Virtual Machines DBMS üzembe helyezése az SAP-munkaterheléshez][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Áttekintés
A magas rendelkezésre állás elérése érdekében az IBM Db2 LUW HADR-rel legalább két Azure virtuális gépre van telepítve, amelyek egy [Azure rendelkezésre állási készletben](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) vagy az [Azure rendelkezésre állási zónáiban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)vannak telepítve. 

A következő grafikák két adatbázis-kiszolgáló Azure-virtuális gép beállítását jelenítik meg. Mindkét adatbázis-kiszolgáló Azure-beli virtuális gépek saját tárolóval rendelkeznek, és már futnak. A HADR-ben az egyik Azure-beli virtuális gép egyik adatbázis-példányának az elsődleges példány szerepköre. Minden ügyfél kapcsolódik ehhez az elsődleges példányhoz. Az adatbázis-tranzakciók minden változása helyileg megmarad a Db2 tranzakciós naplóban. Mivel a tranzakciónapló-rekordok helyi legmegőrzöttek, a rekordok TCP/IP protokollon keresztül kerülnek átvitelre a második adatbázis-kiszolgáló, a készenléti kiszolgáló vagy a készenléti példány adatbázispéldányába. A készenléti példány frissíti a helyi adatbázist az átvitt tranzakciónapló-rekordok továbbításával. Ily módon a készenléti kiszolgáló szinkronban marad az elsődleges kiszolgálóval.

A HADR csak replikációs funkció. Nem rendelkezik hibaészleléssel, és nincs automatikus átvételi vagy feladatátvételi lehetőség. A készenléti kiszolgálóra történő átvételt vagy átvitelt manuálisan kell kezdeményeznie az adatbázis rendszergazdájának. Az automatikus átvétel és a hibaészlelés eléréséhez használhatja a Linux Pacemaker fürtözési funkciót. A pacemaker figyeli a két adatbázis-kiszolgálópéldányt. Amikor az elsődleges adatbázis-kiszolgálópéldány összeomlik, a Pacemaker *automatikus* HADR-átvételt kezdeményez a készenléti kiszolgálón. A pacemaker azt is biztosítja, hogy a virtuális IP-cím hozzá legyen rendelve az új elsődleges kiszolgálóhoz.

![IBM Db2 magas rendelkezésre állás – áttekintés](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Ahhoz, hogy az SAP-alkalmazáskiszolgálók csatlakozzanak az elsődleges adatbázishoz, virtuális állomásnévre és virtuális IP-címre van szükség. Feladatátvétel esetén az SAP-alkalmazáskiszolgálók új elsődleges adatbázis-példányhoz fognak csatlakozni. Azure-környezetben egy [Azure-terheléselosztó](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) szükséges a virtuális IP-cím használatához az IBM Db2 HADR-hez szükséges módon. 

Annak érdekében, hogy teljes mértékben megérthesd, hogy az IBM DB2 LUW HADR-rel és Pacemakerrel hogyan illeszkedik egy magas rendelkezésre állású SAP rendszer beállításába, az alábbi kép áttekintést nyújt az IBM Db2 adatbázison alapuló SAP rendszer magas rendelkezésre állású beállításáról. Ez a cikk csak az IBM Db2-t ismerteti, de az SAP-rendszer más összetevőinek beállításáról szóló egyéb cikkekre mutató hivatkozásokat tartalmaz.

![IBM DB2 magas rendelkezésre állás teljes környezet áttekintése](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>A szükséges lépések magas szintű áttekintése
Az IBM Db2 konfiguráció telepítéséhez az alábbi lépéseket kell végrehajtania:

  + Tervezze meg környezetét.
  + Telepítse a virtuális gépeket.
  + Frissítse a SUSE Linuxot és konfigurálja a fájlrendszereket.
  + Pacemaker telepítése és konfigurálása.
  + Telepítse [a magas rendelkezésre állású NFS-t.][nfs-ha]
  + Telepítse [az ASCS/ERS-t egy külön fürtre.][ascs-ha]
  + Telepítse az IBM Db2 adatbázist az Elosztott/Magas rendelkezésre állás beállítással (SWPM).
  + Telepítsen és hozzon létre egy másodlagos adatbáziscsomópontot és -példányt, és konfigurálja a HADR-t.
  + Ellenőrizze, hogy a HADR működik-e.
  + Az IBM Db2 vezérléséhez alkalmazza a Pacemaker konfigurációt.
  + Konfigurálja az Azure Load Balancer.
  + Telepítse az elsődleges és a párbeszédalkalmazás-kiszolgálókat.
  + Ellenőrizze és igazítsa az SAP alkalmazáskiszolgálók konfigurációját.
  + Feladatátvételi és átvételi tesztek végrehajtása.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Tervezze meg az Azure-infrastruktúrát az IBM Db2 LUW HADR-rel való üzemeltetéséhez

A telepítés végrehajtása előtt fejezze be a tervezési folyamatot. A tervezés a Db2 és a HADR azure-beli konfigurációjának üzembe helyezésének alapját építi fel. Az IMB Db2 LUW (az SAP-környezet adatbázisrésze) tervezésének részét igénylő legfontosabb elemeket az alábbi táblázat sorolja fel:

| Témakör | Rövid leírás |
| --- | --- |
| Azure-erőforráscsoportok definiálása | Erőforráscsoportok, ahol üzembe helyezheti a virtuális gép, virtuális hálózat, az Azure Load Balancer és egyéb erőforrások. Lehet létező vagy új. |
| Virtuális hálózat / Alhálózati definíció | Ahol az IBM Db2 és az Azure Load Balancer virtuális gépei vannak telepítve. Lehet létező vagy újonnan létrehozott. |
| IBM Db2 LUW-t tároló virtuális gépek | Virtuális gép mérete, tárolás, hálózatkezelés, IP-cím. |
| Virtuális állomásnév és virtuális IP az IBM Db2 adatbázishoz| Az SAP-alkalmazáskiszolgálók csatlakoztatására használt virtuális IP- vagy állomásnév. **db-virt-hostname**, **db-virt-ip**. |
| Azure kerítés | Azure kerítés vagy SBD kerítés (erősen ajánlott). Módszer, hogy elkerüljék osztott agyi helyzetekben. |
| SBD virtuális gép | SBD virtuális gép mérete, tárolás, hálózat. |
| Azure Load Balancer | Alapszintű vagy standard (ajánlott), a Db2 adatbázis (a mi javaslatunk: 62500) szonda-port mintavételi **portjának**használata. |
| Névfeloldás| Hogyan működik a névfeloldás a környezetben? A DNS-szolgáltatás használata erősen ajánlott. A helyi hosts fájl használható. |
    
Az Azure-beli Linux-pacemakerről a [Szívritmus-szabályozó beállítása az Azure-ban SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)alkalmazásban című témakörben talál további információt.

## <a name="deployment-on-suse-linux"></a>Telepítés SUSE Linux-on

Az IBM Db2 LUW erőforrás-ügynöke a SUSE Linux Enterprise Server for SAP Applications része. A jelen dokumentumban ismertetett telepítéshez a SUSE Linux Server for SAP Applications alkalmazást kell használnia. Az Azure Marketplace tartalmaz egy lemezképet a SUSE Enterprise Server for SAP Applications 12 számára, amely új Azure virtuális gépek üzembe helyezéséhez használható. Vegye figyelembe a SUSE által az Azure Marketplace-en keresztül kínált különböző támogatási vagy szolgáltatási modelleket, amikor kiválaszt egy virtuálisgép-lemezképet az Azure VM Piactéren. 

### <a name="hosts-dns-updates"></a>Gazdagépek: DNS-frissítések
Készítsen listát az összes állomásnévről, beleértve a virtuális állomásneveket is, és frissítse a DNS-kiszolgálókat, hogy a megfelelő IP-cím állomásnév-feloldáshoz legyen. Ha egy DNS-kiszolgáló nem létezik, vagy nem tudja frissíteni és létrehozni a DNS-bejegyzéseket, akkor az ebben a forgatókönyvben részt vevő egyes virtuális gépek helyi állomásfájljait kell használnia. Ha gazdafájlok bejegyzéseit használja, győződjön meg arról, hogy a bejegyzések az SAP-rendszerkörnyezetben lévő összes virtuális gépre vonatkoznak. Azt javasoljuk azonban, hogy használja a DNS-t, amely ideális esetben kiterjed az Azure-ra


### <a name="manual-deployment"></a>Manuális üzembe helyezés

Győződjön meg arról, hogy a kiválasztott operációs rendszert támogatja az IBM/SAP az IBM Db2 LUW számára. Az Azure virtuális gépek és db2-kiadások támogatott operációs rendszerverzióinak listája az SAP [1928533]megjegyzésében érhető el. Az operációs rendszer egyes Db2-kiadások által kiadott verziók listája az SAP-termék rendelkezésre állási mátrixában érhető el. Javasoljuk, hogy legalább SLES 12 SP4 az Azure-ral kapcsolatos teljesítménybeli fejlesztések miatt ebben vagy később SUSE Linux-verziók.

1. Erőforráscsoport létrehozása vagy kijelölése.
1. Virtuális hálózat és alhálózat létrehozása vagy kijelölése.
1. Hozzon létre egy Azure rendelkezésre állási készletet, vagy telepítsen egy rendelkezésre állási zónát.
    + A rendelkezésre állási csoport esetében állítsa a maximális frissítési tartományokat 2-re.
1. Virtuális gép létrehozása 1.
    + SLES az SAP-lemezkép az Azure Marketplace-en.
    + Válassza ki a 3.
1.  Virtuális gép létrehozása 2.
    + SLES az SAP-lemezkép az Azure Marketplace-en.
    + Válassza ki az Azure rendelkezésre állási készlet létrehozott 3.
1. Adja hozzá az adatlemezeket a virtuális gépekhez, majd ellenőrizze a fájlrendszer beállításának ajánlását az [IBM Db2 Azure Virtual Machines DBMS üzembe helyezésének az SAP-munkaterheléshez][dbms-db2]című cikkben.

## <a name="create-the-pacemaker-cluster"></a>A pacemaker-fürt létrehozása
    
Az IBM Db2 kiszolgálóhoz alapvető Pacemaker-fürt létrehozásáról a [Szívritmus-szabályozó beállítása az Azure-beli SUSE Linux Enterprise Server szolgáltatásban][sles-pacemaker]című témakörben található. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Telepítse az IBM Db2 LUW és SAP környezetet

Az IBM Db2 LUW alapú SAP-környezet telepítésének megkezdése előtt tekintse át a következő dokumentációt:

+ Azure dokumentáció
+ SAP dokumentáció
+ IBM dokumentáció

A dokumentációra mutató hivatkozásokat a cikk bevezető részében talál.

Ellenőrizze az SAP telepítési kézikönyveit a NetWeaver-alapú alkalmazások IBM Db2 LUW-ra történő telepítéséről.

Az SAP súgóportálján található útmutatók az [SAP telepítési útmutató keresőjével][sap-instfind]találhatók.

A portálon megjelenő segédvonalak számát a következő szűrők beállításával csökkentheti:

- Azt akarom, hogy: "Telepítsen egy új rendszert"
- Saját adatbázis: "IBM Db2 Linuxra, Unixra és Windowsra"
- További szűrők az SAP NetWeaver verziókhoz, veremkonfigurációhoz vagy operációs rendszerhez

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Telepítési tanácsok az IBM Db2 LUW HADR-rel történő beállításához

Az elsődleges IBM Db2 LUW adatbázispéldány beállítása:

- Használja a magas rendelkezésre állásvagy elosztott lehetőséget.
- Telepítse az SAP ASCS/ERS és az adatbázis-példány.
- Készítsen biztonsági másolatot az újonnan telepített adatbázisról.


> [!IMPORTANT] 
> Írja le a telepítés során beállított "Adatbázis-kommunikációs portot". Mindkét adatbázispéldányhoz ugyanaz a portszámnak kell lennie.

Ha a készenléti adatbázis-kiszolgálót az SAP homogén rendszermásolási eljárással szeretné beállítani, hajtsa végre az alábbi lépéseket:

1. Jelölje be a **Rendszermásolás** beállítást > **Célrendszerek** > **elosztott** > **adatbázispéldányát.**
1. Másolási módszerként válassza a **Homogén rendszer** lehetőséget, hogy a biztonsági mentés segítségével visszaállíthassa a biztonsági másolatot a készenléti kiszolgálópéldányon.
1. Amikor eléri a kilépési lépést, hogy visszaállítsa az adatbázishomogén rendszer másolatát, lépjen ki a telepítőből. Az adatbázis visszaállítása az elsődleges állomás biztonsági másolatából. Az összes további telepítési fázis már végrehajtásra került az elsődleges adatbázis-kiszolgálón.
1. Hadr beállítása AZ IBM Db2-hez.

   > [!NOTE]
   > Az Azure-ra és a Pacemakerre jellemző telepítéshez és konfigurációhoz: Az SAP Szoftverkiépítési kezelőn keresztüli telepítési eljárás során egyértelmű kérdés merül fel az IBM Db2 LUW magas rendelkezésre állásával kapcsolatban:
   >+ Ne válassza **az IBM Db2 pureScale lehetőséget**.
   >+ Ne válassza **az IBM Tivoli System Automation telepítése többplatformos platformokra**lehetőséget.
   >+ Ne válassza **a Fürtkonfigurációs fájlok létrehozása lehetőséget.**

   Ha SBD-eszközt használ Linux pacemakerhez, állítsa be a következő Db2 HADR paramétereket:
   + HADR társablak időtartama (másodperc) (HADR_PEER_WINDOW) = 300  
   + HADR időtúlérték (HADR_TIMEOUT) = 60

   Ha egy Azure Pacemaker vívó ügynök, állítsa be a következő paramétereket:
   + HADR társablak időtartama (másodperc) (HADR_PEER_WINDOW) = 900  
   + HADR időtúlérték (HADR_TIMEOUT) = 60

Javasoljuk, hogy az előző paraméterek alapján a kezdeti feladatátvételi/átvételi tesztelés. Ezekkel a paraméterbeállításokkal kötelező a feladatátvétel és a átvétel megfelelő működését tesztelni. Mivel az egyes konfigurációk eltérőek lehetnek, a paraméterek módosítására szorulhatnak. 

> [!IMPORTANT]
> Az IBM Db2-re jellemző, normál indítással rendelkező HADR-konfigurációval: A másodlagos vagy készenléti adatbázispéldánynak az elsődleges adatbázispéldány elindításához fel kell állnia és futnia kell.

Szemléltetési célokra és az ebben a cikkben ismertetett eljárásokra az adatbázis SID azonosítója **PTR**.

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR ellenőrzés
Miután konfigurálta a HADR-t, és az állapot PEER és CONNECTED az elsődleges és készenléti csomópontokon, hajtsa végre a következő ellenőrzést:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2 pacemaker konfiguráció

Ha csomóponthiba esetén automatikus feladatátvételre használ pacemakert, ennek megfelelően kell konfigurálnia a Db2-példányokat és a Pacemakert. Ez a szakasz az ilyen típusú konfigurációt ismerteti.

A következő elemek előtaggal vannak ellátva:

- **[A]**: Minden csomópontra alkalmazható
- **[1]**: Csak az 1. 
- **[2]**: Csak a 2- es csomópontra alkalmazható

**[A]** A pacemaker konfigurációjának előfeltételei:
1. Állítsa le mindkét adatbázis-kiszolgálót a db2\<user sid> a db2stop segítségével.
1. Módosítsa a db2\<sid> felhasználó shell környezetét */bin/ksh-ra.* Javasoljuk, hogy használja a Yast eszközt. 


### <a name="pacemaker-configuration"></a>Szívritmus-szabályozó konfigurációja

> [!IMPORTANT]
> A legutóbbi tesztelés olyan helyzeteket tárt fel, amikor a netcat nem válaszol a kérelmekre a hátralék miatt, és csak egy kapcsolat kezelésének korlátozása miatt. A netcat erőforrás leállítja az Azure Load balancer kérelmek meghallgatását, és a lebegő IP elérhetetlenné válik.  
> Meglévő Pacemaker klaszterek esetében korábban azt javasoljuk, hogy a netcat-et socat-ra cserélje. Jelenleg azt javasoljuk, hogy az azure-lb erőforrás-ügynök, amely része a csomag erőforrás-ügynökök, a következő csomag verziókövetelmények:
> - Az SLES 12 SP4/SP5 esetében a verziónak legalább erőforrás-ügynököknek kell lennie-4.3.018.a7fb5035-3.30.1.  
> - Az SLES 15/15 SP1 esetében a verziónak legalább erőforrás-ügynököknek kell lennie-4.3.0184.6ee15eb2-4.13.1.  
>
> Vegye figyelembe, hogy a módosítás rövid állásidőt igényel.  
> Meglévő pacemaker-fürtök esetén, ha a konfiguráció már megváltozott socat használatára az [Azure Load-Balancer Detection Hardening című](https://www.suse.com/support/kb/doc/?id=7024128)dokumentumban leírtak szerint, nincs szükség arra, hogy azonnal váltson az azure-lb erőforrás-ügynökre.

**[1]** IBM Db2 HADR-specifikus pacemaker konfiguráció:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** IBM Db2 források létrehozása:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** Indítsa el az IBM Db2 erőforrásait:
* A Pacemaker tegyék ki a karbantartási módból.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** Ellenőrizze, hogy a fürt állapota rendben van-e, és hogy az összes erőforrás elindult.[1] Győződjön meg arról, hogy a fürt állapota rendben van, és hogy az összes erőforrás elindult. Nem fontos, hogy melyik csomóponton futnak az erőforrások.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 konfigurált csomópont
# <a name="5-resources-configured"></a>5 konfigurált erőforrás

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [ azibmdb01 azibmdb02 ]

# <a name="full-list-of-resources"></a>Az erőforrások teljes listája:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd (stonith:external/sbd): Started azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Erőforráscsoport: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (ocf::szívverés:IPaddr2): Started azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::szívverés:azure-lb): Started azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Mester/szolga készlet: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Mesterek: [ azibmdb02 ]
#      <a name="slaves--azibmdb01-"></a>Rabszolgák: [ azibmdb01 ]
</pre>

> [!IMPORTANT]
> A Pacemaker fürtözött Db2 példányát a Pacemaker eszközök használatával kell kezelnie. Ha db2 parancsokat használ, például db2stop, pacemaker észleli a műveletet, mint az erőforrás meghibásodása. Ha karbantartást végez, a csomópontokat vagy erőforrásokat karbantartási módba helyezheti. Pacemaker felfüggeszti a figyelési erőforrásokat, és ezután használhatja a normál db2 felügyeleti parancsokat.


### <a name="configure-azure-load-balancer"></a>Az Azure Load Balancer konfigurálása
Az Azure Load Balancer konfigurálásához azt javasoljuk, hogy használja az [Azure Standard Load Balancer Termékváltozat,](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) majd tegye a következőket;

> [!NOTE]
> A standard terheléselosztó termékváltozat korlátozza a nyilvános IP-címek elérését a terheléselosztó alatti csomópontokról. A cikk [nyilvános végpont-kapcsolat az Azure Standard Load Balancer használatával az Azure Standard Load Balancer sap magas rendelkezésre állású forgatókönyvek](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) ismerteti, hogyan lehet engedélyezni ezeket a csomópontokat a nyilvános IP-címek eléréséhez

1. Előtér-IP-készlet létrehozása:

   a. Az Azure Portalon nyissa meg az Azure Load Balancer-t, válassza **az előtétes IP-készletet,** majd válassza **a Hozzáadás**lehetőséget.

   b. Adja meg az új előtér-IP-készlet nevét (például **Db2-kapcsolat**).

   c. Állítsa a **hozzárendelést** **Statikusra,** és írja be az elején definiált **Virtuális IP-címet.**

   d. Válassza **az OK gombot.**

   e. Az új előtér-IP-készlet létrehozása után vegye figyelembe a készlet IP-címét.

1. Háttérkészlet létrehozása:

   a. Az Azure Portalon nyissa meg az Azure Load Balancer-t, válassza a **háttérkészleteket,** és válassza **a Hozzáadás**lehetőséget.

   b. Adja meg az új háttérkészlet nevét (például **Db2-háttér).**

   c. Válassza **a Virtuális gép hozzáadása**lehetőséget.

   d. Válassza ki a rendelkezésre állási készlet vagy az IBM Db2 adatbázist üzemeltető virtuális gépeket, amelyeket az előző lépésben hoztak létre.

   e. Válassza ki az IBM Db2 fürt virtuális gépeit.

   f. Válassza **az OK gombot.**

1. Hozzon létre egy állapotminta:

   a. Az Azure Portalon nyissa meg az Azure Load Balancer,válassza **ki az állapotminta,** és válassza **a Hozzáadás**lehetőséget.

   b. Adja meg az új állapotminta nevét (például **Db2-hp**).

   c. Válassza a **TCP protokollt** és a **62500-as portot.** Tartsa az **Intervallum** értéket **5-re,** és tartsa a Nem megfelelő **küszöbértéket** **2-re**.

   d. Válassza **az OK gombot.**

1. Hozza létre a terheléselosztási szabályokat:

   a. Az Azure Portalon nyissa meg az Azure Load Balancer-t, válassza a **Terheléselosztási szabályok**lehetőséget, majd válassza **a Hozzáadás**lehetőséget.

   b. Adja meg az új terheléselosztó szabály nevét (például **Db2-SID).**

   c. Válassza ki az előtér-IP-címet, a háttérkészletet és a korábban létrehozott állapotminta (például **Db2-előtér).**

   d. Tartsa a **Protokollt** **TCP-re**állítva, és adja meg *a portadatbázis kommunikációs portját*.

   e. Növelje az **tétlen időout30** percre.

   f. Győződjön meg arról, hogy **engedélyezi a lebegő IP protokollt.**

   g. Válassza **az OK gombot.**


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Az SAP-profilok módosítása a virtuális IP-kapcsolat használatához
A HADR-konfiguráció elsődleges példányához való csatlakozáshoz az SAP alkalmazásrétegnek az Azure Load Balancer hez definiált és konfigurált virtuális IP-címet kell használnia. A következő módosításokra van szükség:

/sapmnt/\<SID>/profile/DEFAULT. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Elsődleges és párbeszédalkalmazás-kiszolgálók telepítése

Ha az elsődleges és a párbeszédalkalmazás-kiszolgálókat Db2 HADR konfigurációval telepíti, használja a konfigurációhoz kiválasztott virtuális állomásnevet. 

Ha a telepítést a Db2 HADR konfiguráció létrehozása előtt hajtotta végre, hajtsa végre a módosításokat az előző szakaszban leírtak szerint, és az SAP Java-veremek esetében az alábbiak szerint.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP+Java vagy Java veremrendszerek JDBC URL-ellenőrzése

A J2EE Config eszközzel ellenőrizheti vagy frissítheti a JDBC URL-címét. Mivel a J2EE Config eszköz egy grafikus eszköz, meg kell, hogy X szerver telepítve:
 
1. Jelentkezzen be a J2EE-példány elsődleges alkalmazáskiszolgálójára, és hajtsa végre:`sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. A bal oldali keretben válassza a **Biztonsági tároló lehetőséget.**
1. A megfelelő keretben válassza ki a kulcsot\<jdbc/pool/ SAPSID>/url.
1. Módosítsa a JDBC URL-címében lévő állomásnevet a virtuális állomás névre.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Válassza a **Hozzáadás** lehetőséget.
1. A módosítások mentéséhez válassza a lemez ikonját a bal felső sarokban.
1. Zárja be a konfigurációs eszközt.
1. Indítsa újra a Java-példányt.

## <a name="configure-log-archiving-for-hadr-setup"></a>Naplóarchiválás konfigurálása hadr beállításhoz
A DB2-napló archiválásának hadr-telepítéshez történő konfigurálásához azt javasoljuk, hogy mind az elsődleges, mind a készenléti adatbázist úgy állítsa be, hogy az automatikus napló-lekérési képesség legyen az összes naplóarchívum-helyről. Mind az elsődleges, mind a készenléti adatbázisnak képesnek kell lennie a napló-archívum fájljainak beolvasására az összes olyan naplóarchívum-helyről, ahamelybe az adatbázis-példányok bármelyike archiválhatja a naplófájlokat. 

A naplóarchiválást csak az elsődleges adatbázis végzi. Ha módosítja az adatbázis-kiszolgálók HADR szerepköreit, vagy ha hiba történik, az új elsődleges adatbázis felelős a naplóarchiválásért. Ha több naplóarchívum-helyet állított be, előfordulhat, hogy a naplók at kétszer archiválja a rendszer. Helyi vagy távoli felzárkózás esetén előfordulhat, hogy manuálisan is át kell másolnia az archivált naplókat a régi elsődleges kiszolgálóról az új elsődleges kiszolgáló aktív naplóhelyére.

Azt javasoljuk, hogy konfigurálja a közös NFS-megosztás, ahol naplók írása mindkét csomópontról. Az NFS-megosztásnak magas rendelkezésre állásúnak kell lennie. 

A meglévő, magas rendelkezésre állású NFS-megosztásokat átvitelhez vagy profilkönyvtárhoz használhatja. További információkért lásd:

- [Magas rendelkezésre állás az NFS számára az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server rendszeren][nfs-ha] 
- [Az SAP NetWeaver magas rendelkezésre állása az Azure virtuális gépeken a SUSE Linux Enterprise Server en az SAP-alkalmazásokhoz készült Azure NetApp-fájlokkal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Azure NetApp-fájlok](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (NFS-megosztások létrehozása)


## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

Ez a szakasz azt ismerteti, hogyan tesztelheti a Db2 HADR beállítását. *Minden teszt feltételezi, hogy felhasználói gyökérként van bejelentkezve,* és az IBM Db2 elsődleges fut az *azibmdb01* virtuális gépen.

Az összes vizsgálati eset kezdeti állapotát itt ismertetik: (crm_mon -r vagy crm állapot)

- **crm állapot** a Pacemaker állapotának pillanatképe a végrehajtás időpontjában 
- **crm_mon -r** a pacemaker állapotának folyamatos kimenete

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

Az SAP-rendszer eredeti állapotát a Transaction DBACOCKPIT > Konfiguráció > áttekintése című témakör ben dokumentálja, ahogy az az alábbi képen látható:

![DBACockpit – áttelepítés előtti](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Az IBM Db2 tesztátvétele


> [!IMPORTANT] 
> A teszt megkezdése előtt győződjön meg arról, hogy:
> * A pacemaker nem rendelkezik sikertelen műveletekkel (crm állapot).
> * Nincsenek helykorlátozások (az áttelepítési teszt maradékai)
> * Az IBM Db2 HADR szinkronizálás működik. Ellenőrizze a felhasználó\<db2 sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Telepítse át az elsődleges Db2 adatbázist futtató csomópontot a következő parancs végrehajtásával:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Az áttelepítés után a crm állapot kimenete így néz ki:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

Az SAP-rendszer eredeti állapotát a Transaction DBACOCKPIT > Konfiguráció > áttekintése című témakör ben dokumentálja, ahogy az az alábbi képen látható:

![DBACockpit – Áttelepítés után](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

A "crm erőforrás-áttelepítéssel" rendelkező erőforrás-áttelepítés helykényszereket hoz létre. A helymegkötéseket törölni kell. Ha a helymegkötések nem törlődnek, az erőforrás nem tud visszaadható, vagy nem kívánt átvételeket tapasztalhat. 

Az erőforrás áttelepítése *azibmdb01-be,* és törölje a helymegkötéseket
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **a \<crm-erőforrás \<res_name> gazdagép> áttelepítése:** Helymegkötéseket hoz létre, és problémákat okozhat az átvételsel
- **crm erőforrás \<törlése res_name>**: Törli a helymegkötéseket
- **crm erőforrás \<karbantartása res_name>**: Törli az erőforrás összes hibás

### <a name="test-the-fencing-agent"></a>Tesztelje a vívó ügynök

Ebben az esetben teszteljük az SBD kerítést, amelyet a SUSE Linux használata esetén ajánlunk.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

A *fürtcsomópont azibmdb01-et* újra kell indítani. Az IBM Db2 elsődleges HADR szerepkör átkerül *azibmdb02 -be.* Ha az *azibmdb01* újra online állapotba kerül, a Db2 példány egy másodlagos adatbázispéldány szerepkörében fog mozogni. 

Ha a Pacemaker szolgáltatás nem indul el automatikusan az újraindított korábbi elsődleges, győződjön meg róla, hogy indítsa el manuálisan:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Kézi átvétel tesztelése

A manuális átvételt az *azibmdb01* csomópont pacemaker szolgáltatásának leállításával tesztelheti:
<pre><code>service pacemaker stop</code></pre>

állapot *azibmdb02-n*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

A feladatátvétel után újra indíthatja a szolgáltatást *azibmdb01-en.*
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>A Db2 folyamat megölése a HADR elsődleges adatbázist futtató csomóponton

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

A Db2 példány sikertelen lesz, és a Pacemaker a következő állapotot fogja jelenteni:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

A pacemaker újraindítja a Db2 elsődleges adatbázispéldányt ugyanazon a csomóponton, vagy átadja a feladatát a másodlagos adatbázis-példányt futtató csomópontnak, és hibát jelent.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>A Db2 folyamat leírása a másodlagos adatbázispéldányt futtató csomóponton

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

A csomópont sikertelen állapotba kerül, és a jelentett hiba
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

A Db2-példány újraindul a korábban hozzárendelt másodlagos szerepkörben.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>A DB leállítása a DB2stop force-on keresztül a HADR elsődleges adatbázispéldányt futtató csomóponton

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Mivel a\<felhasználó db2 sid> végre parancs db2stop erő:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Hiba észlelve
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

A Db2 HADR másodlagos adatbázispéldány lett előléptetve az elsődleges szerepkörbe
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Virtuális gép összeomlása újraindítással a HADR elsődleges adatbázis-példányt futtató csomóponton

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

A pacemaker elősegíti a másodlagos példányt az elsődleges példány szerepkörben. A régi elsődleges példány a virtuális gép után a másodlagos szerepkörbe lép, és a virtuális gép újraindítása után az összes szolgáltatás teljesen helyreáll:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>A HADR elsődleges adatbázis-példányát futtató virtuális gép összeomlása "stop"

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Ebben az esetben a Pacemaker észleli, hogy az elsődleges adatbázis-példányt futtató csomópont nem válaszol.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

A következő lépés az, hogy ellenőrizze a *Split agyi* helyzet. Miután a túlélő csomópont megállapította, hogy az elsődleges adatbázis-példányt utoljára futtató csomópont nem, az erőforrások feladatátvétele végrehajtásra kerül.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


Abban az esetben, ha a csomópont "leáll", a meghibásodott csomópontot újra kell indítani az Azure Management-eszközökön keresztül (az Azure Portalon, a PowerShellben vagy az Azure CLI-ben). Miután a sikertelen csomópont újra online állapotba került, elindítja a Db2 példányt a másodlagos szerepkörbe.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>További lépések
- [Magas rendelkezésre állású architektúra és forgatókönyvek az SAP NetWeaver számára](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Pacemaker beállítása az Azure-beli SUSE Linux Enterprise Server szolgáltatásban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

