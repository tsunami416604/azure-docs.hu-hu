---
title: IBM Db2 HADR beállítása az Azure-beli virtuális gépek (VM) |} A Microsoft Docs
description: Magas rendelkezésre állású, IBM Db2 LUW Azure virtuális gépeken (VM) hoz létre.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/10/2019
ms.author: juergent
ms.openlocfilehash: a74dd1a932cac41081786f76938a5b35de62d878
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689716"
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
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Magas rendelkezésre állásának IBM Db2 LUW SUSE Linux Enterprise Server támasztja a az Azure virtuális gépeken

IBM Db2, Linux, UNIX és a Windows (LUW) a [magas rendelkezésre állás és vészhelyreállítás helyreállítási (HADR) konfigurációs](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) egy csomópontot egy elsődleges adatbázis-példányt futtató és a egy másodlagos adatbázis-példányt futtató legalább egy csomópont áll. Az elsődleges adatbázis-példány módosítása replikált egy másodlagos adatbázis-példányt szinkron vagy aszinkron módon, a konfigurációtól függően. 

Ez a cikk bemutatja, hogyan üzembe helyezése és konfigurálása az Azure-beli virtuális gépek (VM), a fürt keretrendszer telepítése és telepítése az IBM Db2 LUW HADR konfigurációval. 

A cikkben nem foglalkozunk a telepítése és konfigurálása az IBM Db2 LUW HADR vagy az SAP-szoftver telepítése. Segítséget a fenti feladatok elvégzéséhez, SAP- és IBM telepítési kézikönyvek mutató hivatkozások biztosítunk. Ez a cikk az Azure-környezethez adott részeket összpontosít. 

IBM DB2-höz verziók a következők 10,5 és újabb verziók, SAP-jegyzetnek leírt [1928533].

A telepítés megkezdése előtt az alábbi SAP-megjegyzések és dokumentációjában talál:

| SAP-jegyzetnek | Leírás |
| --- | --- |
| [1928533] | SAP-alkalmazások az Azure-ban: Támogatott termékek és Azure-beli Virtuálisgép-típusok |
| [2015553] | Azure-beli SAP: Támogatás előfeltételei |
| [2178632] | Figyelési metrikák az Azure-beli SAP kulcs |
| [2191498] | SAP használata Linux az Azure-ral: Speciális figyelés |
| [2243692] | Linuxon futó Azure (IaaS) virtuális Gépet: Az SAP-licenccel kapcsolatos problémák |
| [1984787] | SUSE LINUX Enterprise Server 12: Telepítési jegyzetek |
| [1999351] | Továbbfejlesztett Azure monitoring for SAP hibaelhárítási |
| [2233094] | DB6: SAP-alkalmazások az Azure-ban használt IBM DB2-höz Linux, UNIX és a Windows - további információ |
| [1612105] | DB6: Db2-HADR kapcsolatos gyakori kérdések |


| Dokumentáció | 
| --- |
| [Az SAP közösségi Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Rendelkezik a szükséges SAP-megjegyzések mindegyikét linuxhoz |
| [Az Azure virtuális gépek tervezése és megvalósítása a linuxon futó SAP] [ planning-guide] útmutató |
| [Az Azure virtuális gépek üzembe helyezése Linuxon futó SAP-] [ deployment-guide] (Ez a cikk) |
| [Az Azure Virtual Machines adatbázis felügyeleti system(DBMS) központi telepítése a linuxon futó SAP-] [ dbms-guide] útmutató |
| [Az SAP számítási feladatok az Azure tervezési és telepítési ellenőrzőlista][azr-sap-plancheck] |
| [SUSE Linux Enterprise Server SAP alkalmazások 12 SP3 ajánlott eljárások][sles-for-sap-bp] |
| [SUSE Linux Enterprise magas rendelkezésre állású bővítmény 12 SP3][sles-ha-guide] |
| [IBM Db2 Azure virtuális gépek DBMS üzembe helyezési SAP számítási feladatokhoz][dbms-db2] |
| [IBM DB2-höz HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10,5][db2-hadr-10.5] |

## <a name="overview"></a>Áttekintés
Magas rendelkezésre állás, a HADR az IBM Db2 LUW telepítve van, legalább két Azure virtuális gépeken, amelyek vannak üzembe helyezve egy [Azure rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) vagy keresztben [Azure-beli rendelkezésre állási zónák](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

Az alábbi ábrák a telepítés pedig két adatbázis-kiszolgáló Azure-beli virtuális gépek jelennek meg. Mindkét adatbázis-kiszolgáló Azure-beli virtuális gépek saját csatlakoztatott tárhellyel rendelkező és futnak. Egy adatbázis-példány egy az Azure virtuális gépek HADR, a szerepkört az elsődleges példány van. Összes ügyfél csatlakozik az elsődleges példány. Adatbázis-tranzakciók az összes módosítást a Db2-tranzakciónapló helyileg tárolja. A tranzakciós napló rögzíti a helyi megmaradnak, mivel a rekordok átkerülnek TCP/IP-n keresztül az adatbázispéldányt a második kiszolgáló, a készenléti kiszolgálón vagy a készenléti példány. A készenléti példány működés közbeni előre az átvitt tranzakció rekordok naplózása a helyi adatbázis frissíti. Ezzel a módszerrel a készenléti kiszolgáló marad az elsődleges kiszolgáló szinkronban.

HADR csak egy replikációs funkciót. Nincs hiba észlelése és nincs automatikus kiemelt felvásárlási vagy feladatátvételi létesítményekben rendelkezik. A kiemelt felvásárlási vagy az adatok átvitele a készenléti kiszolgáló kell kezdeményeznie manuálisan egy adatbázis-rendszergazda. Az automatikus átvételét és a hibaészlelés érhető el, használhatja a Linux támasztja fürtszolgáltatást. Támasztja a két adatbázis kiszolgálópéldányok figyeli. Ha az elsődleges adatbázis-kiszolgálópéldányra összeomlik, támasztja kezdeményezi egy *automatikus* HADR átvétele a készenléti kiszolgáló. Támasztja emellett biztosítja, hogy a virtuális IP-cím van hozzárendelve az új elsődleges kiszolgáló.

![IBM DB2-höz magas rendelkezésre állás – Áttekintés](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Szeretné, hogy az SAP alkalmazáskiszolgálók elsődleges adatbázishoz csatlakozni, szüksége van egy virtuális állomás neve és a virtuális IP-címet. Feladatátvétel esetén az SAP-alkalmazáskiszolgálók új elsődleges adatbázis-példányt fog csatlakozni. Azure-környezet egy [az Azure load balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) szükség egy virtuális IP-cím használata az HADR IBM Db2 által megkövetelt módon. 

Teljes mértékben megismerheti, hogyan illeszkedik a IBM Db2 LUW HADR és támasztja egy magas rendelkezésre állású SAP system telepítése érdekében az alábbi ábra mutatja be egy magas rendelkezésre állású telepítés az SAP-rendszer IBM Db2-adatbázis alapján. Ez a cikk ismerteti, csak az IBM DB2-höz, de más az SAP-rendszer más összetevőitől beállításával kapcsolatos cikkek mutató hivatkozásokat biztosít.

![IBM DB2-höz magas rendelkezésre állás teljes környezet – áttekintés](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>A szükséges lépések magas szintű áttekintése
Az IBM Db2-konfigurációjának üzembe helyezéséhez kövesse az alábbi lépéseket kell:

  + A környezet megtervezése.
  + A virtuális gépek üzembe helyezése.
  + SUSE Linux frissítése, és konfigurálja a fájlrendszerek.
  + Telepítse és konfigurálja a támasztja.
  + Telepítés [magas rendelkezésre állású NFS][nfs-ha].
  + Telepítés [ASCS/SSZON külön fürtben lévő][ascs-ha].
  + IBM Db2-adatbázis telepítése elosztott és nagy rendelkezésre állási lehetőségek (SWPM).
  + Telepítse, és hozzon létre egy másodlagos adatbázis-csomópont és a példány, és a HADR konfigurálja.
  + Győződjön meg arról, hogy a HADR működik-e.
  + A támasztja konfigurációs IBM DB2-höz vezérlőelemre vonatkoznak.
  + Az Azure Load Balancer konfigurálása.
  + Elsődleges telepítése és alkalmazáskiszolgálók párbeszédpanel.
  + Ellenőrizze, és alkalmazkodik az SAP-alkalmazáskiszolgálókhoz konfigurációját.
  + Hajtsa végre a feladatátvételt és felvásárlási tesztek.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Az IBM Db2 LUW HADR az üzemeltető Azure-infrastruktúra megtervezése

A tervezési folyamat befejezéséhez, a központi telepítés végrehajtása előtt. A foundation üzembe helyezéséhez az Azure-ban HADR Db2-konfiguráció tervezése épít fel. Fő elemei, amelyek Testülettel Db2 LUW (SAP-környezet adatbázis része) tervezésének részeként kell az alábbi táblázatban láthatók:

| Témakör | Rövid leírás |
| --- | --- |
| Azure-erőforrások csoportjait határozzák meg | Az erőforráscsoportok, amelyen központi telepítését virtuális gép, virtuális hálózatok közötti, Azure Load Balancer és egyéb erőforrásokat. Meglévő vagy új is lehet. |
| Virtuális hálózat / alhálózat definíciója | IBM DB2-höz és az Azure Load Balancer virtuális gépek telepítve vannak folyamatban. Meglévő és újonnan létrehozott is lehetnek. |
| IBM Db2 LUW üzemeltető virtuális gépek | Virtuális gép méretét, tárolási, hálózati, IP-címet. |
| Virtuális állomás nevét és a virtuális IP-cím, az IBM Db2-adatbázis| A virtuális IP-cím vagy a gazdagép neve kapcsolat az SAP-alkalmazáskiszolgálókhoz használt. **DB-virt-állomásnév**, **db-virt-ip**. |
| Az Azure szintaxiskiemeléshez | Az Azure az elkerítés vagy SBD szintaxiskiemeléshez (ajánlott). Split agy helyzetek, amikor metódust letiltja. |
| SBD VM | Virtuálisgép-méret SBD, tárolási, hálózati. |
| Azure Load Balancer | Az alap vagy standard szintű (ajánlott), mintavételi port Db2-adatbázis (Javaslataink 62500) **mintavételi port**. |
| Névfeloldás| Névfeloldásának működésével a környezetben. Erősen ajánlott DNS-szolgáltatás. Helyi gazdafájljában is használható. |
    
Az Azure-ban Linux támasztja kapcsolatos további információkért lásd: [támasztja a SUSE Linux Enterprise Server az Azure-beli beállítása](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker).

## <a name="deployment-on-suse-linux"></a>Üzembe helyezés SUSE Linux rendszeren

Az IBM Db2 LUW erőforrás-ügynök része a SUSE Linux Enterprise Server SAP-alkalmazások számára. Az ebben a dokumentumban leírt beállítása az SAP-alkalmazások SUSE Linux-kiszolgálón kell használnia. Az Azure Marketplace-en kép SUSE Enterprise Server számára, hogy az új Azure-beli virtuális gépek telepítéséhez használhatja az SAP alkalmazások 12 tartalmazza. Vegye figyelembe a különböző támogatási vagy szolgáltatás modellek közben egy Virtuálisgép-lemezkép az Azure Virtuálisgép-Piacteréről által SUSE, az Azure piactéren keresztül elérhetővé. 

### <a name="hosts-dns-updates"></a>Hosts: DNS-frissítések
Győződjön meg arról, az összes gazdagép nevek, például virtuális állomásnevek listáját, és frissítse a DNS-kiszolgálók megfelelő IP-cím állomásnév-feloldás engedélyezéséhez. Ha egy DNS-kiszolgáló nem létezik vagy nem lehet frissíteni, és DNS-bejegyzéseket létrehozni, a helyi gazdagép fájlok az egyes virtuális gépek részt ebben a forgatókönyvben használni szeretne. Ha állomásbejegyzéssel fájlokat használ, ügyeljen arra, hogy a kiegyenlítése a rendszer az SAP-környezet minden virtuális gépre. Azonban azt javasoljuk, hogy használja-e a DNS-kiszolgálót, amely, ideális esetben kiterjeszti az Azure-bA


### <a name="manual-deployment"></a>Manuális telepítés

Győződjön meg arról, hogy a kiválasztott operációs rendszer IBM Db2 LUW az IBM és az SAP által támogatott. Az Azure virtuális gépek és Db2-verziókban támogatott operációsrendszer-verziók listája érhető el az SAP-jegyzetnek [1928533]. Az operációs rendszer kiadásai által az egyes Db2-kiadás listája az SAP-termék rendelkezésre állási mátrix érhető el. Azure-hoz kapcsolódó teljesítménnyel kapcsolatos fejlesztések a jelen vagy újabb miatt erősen ajánlott a SLES 12 SP3 legalább SUSE Linux-verziók.

1. Hozzon létre vagy válasszon ki egy erőforráscsoportot.
1. Létrehozhat vagy kiválaszthat egy virtuális hálózatot és alhálózatot.
1. Hozzon létre egy Azure rendelkezésre állási csoportot, vagy egy rendelkezésre állási zónában üzembe helyezése.
    + A rendelkezésre állási csoport állítsa be a maximális frissítési tartományok 2-re.
1. 1 virtuális gép létrehozása.
    + SAP-lemezképet az Azure piactéren elérhető SLES használja.
    + Válassza ki a 3. lépésben létrehozott Azure rendelkezésre állási csoportot, vagy rendelkezésre állási zónát válasszon ki.
1.  2 virtuális gép létrehozása.
    + SAP-lemezképet az Azure piactéren elérhető SLES használja.
    + Válassza ki az Azure rendelkezésre állási csoport, a 3. lépésében létrehozott, vagy válassza ki a rendelkezésre állási zónában (nem ugyanabban a zónában mint 3. lépés).
1. Hozzáadhat adatlemezeket a virtuális gépeket, és ellenőrizze, a cikkben egy fájl rendszerbeállítás ajánlása [IBM Db2 Azure virtuális gépek DBMS üzembe helyezési SAP számítási][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>A támasztja fürt létrehozása
    
Az IBM Db2-kiszolgáló alapszintű támasztja fürtöt létrehozni, tekintse meg [támasztja a SUSE Linux Enterprise Server az Azure-beli beállítása][sles-pacemaker]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Az IBM Db2 LUW és az SAP-környezet telepítése

Az IBM Db2 LUW alapján az SAP-környezet telepítésének megkezdése előtt tekintse át az alábbi dokumentációt:

+ Azure-dokumentáció
+ Az SAP-dokumentáció
+ IBM-dokumentáció

Ez a dokumentáció mutató hivatkozások szerepelnek a bevezető szakasz ebben a cikkben.

Ellenőrizze az SAP telepítési kézikönyvek NetWeaver-alapú alkalmazások IBM Db2 LUW való telepítésével.

Annak a útmutatókat, az SAP-súgó portál használatával a [SAP telepítési útmutató Finder][sap-instfind].

Megjelenik a portálon a következő szűrők megadásával útmutatók számát csökkentheti:

- akarok: "Az új rendszer telepítése"
- Adatbázis: "A Windows, Linux és UNIX rendszerű az IBM DB2-höz"
- SAP NetWeaver verziók, veremkonfigurációját vagy operációs rendszer további szűrők

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>IBM Db2 LUW HADR való beállításával kapcsolatos telepítési javaslatok

Az IBM Db2 LUW elsődleges adatbázis-példány beállítása:

- A magas rendelkezésre állású vagy elosztott beállítást használja.
- Telepítse az SAP ASCS/SSZON és adatbázis-példányt.
- Az újonnan telepített adatbázis biztonsági másolatának igénybe vehet.


> [!IMPORTANT] 
> Jegyezze fel az "adatbázis kommunikációs port", amely a telepítés során. Az adatbázis-példány is ugyanazt a portszámot kell lennie

A készenléti kiszolgáló beállítása az SAP homogén rendszer másolási eljárás használatával, hajtsa végre ezeket a lépéseket:

1. Válassza ki a **rendszer másolási** lehetőséget > **igénylő rendszerek** > **elosztott** > **adatbázispéldány**.
1. Egy másolási módszerként válassza **homogén rendszer** , hogy a biztonsági mentés használatával biztonsági másolatának visszaállítása egy a készenléti server-példányon.
1. Ha egyenlege eléri a homogén rendszer példányához, az adatbázis visszaállításához a kilépési lépés, a telepítőből való kilépéshez. Elsődleges állomás egy biztonsági másolatból állítsa vissza az adatbázist. Összes ezt követő telepítéséhez fázisban már végre lett hajtva az elsődleges kiszolgálón.
1. IBM DB2-höz HADR beállítását.

   > [!NOTE]
   > A telepítés és konfiguráció, amely kifejezetten az Azure-ban és támasztja: A telepítési folyamatot a SAP Software kiépítés Manager során egy magas rendelkezésre állás az IBM Db2 LUW explicit kérdése van:
   >+ Ne válassza **IBM DB2-höz pureScale**.
   >+ Ne válassza **telepítése IBM Tivoli rendszer automatizálási Multiplatforms**.
   >+ Ne válassza **fürt konfigurációs fájljainak létrehozása**.

   Amikor Linux támasztja egy SBD eszközt használ, állítsa be a következő Db2 HADR paraméterek:
   + HADR társ időszak hossza (mp) (HADR_PEER_WINDOW) = 300  
   + HADR időtúllépési értéket (HADR_TIMEOUT) = 60

   Az Azure támasztja szintaxiskiemeléshez ügynök használata esetén állítsa be a következő paraméterekkel:
   + HADR társ időszak hossza (mp) (HADR_PEER_WINDOW) = 900  
   + HADR időtúllépési értéket (HADR_TIMEOUT) = 60

Azt javasoljuk, hogy a fenti paraméterek alapján a kezdeti tesztelés feladatátvétel/átvételt. Kötelező tesztelheti a feladatátvételi és átvétel megfelelő működésének ezekkel a beállításokkal paraméter. Egyéni konfigurációk eltérőek lehetnek, mert a paraméterek korrekciós lehet szükség. 

> [!IMPORTANT]
> IBM DB2-höz, a rendszerindítási konfiguráció HADR konkrét: Ahhoz, hogy az elsődleges adatbázis-példány üzembe helyezéséig kell lennie a másodlagos vagy készenléti adatbázispéldány.

Bemutató célokra és ebben a cikkben leírt eljárások, az az adatbázis biztonsági AZONOSÍTÓRA van **PTR**.

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR ellenőrzése
Miután konfigurálta a HADR és állapota és a csatlakoztatva az elsődleges és készenléti csomópontokon, hajtsa végre a következő ellenőrzés:

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



## <a name="db2-pacemaker-configuration"></a>Db2-támasztja konfiguráció

Használatakor támasztja automatikus feladatátvételhez egy csomópont meghibásodik, a Db2-példányok és támasztja ennek megfelelően konfigurálni kell. Ez a szakasz azt ismerteti, hogy ezt a konfigurációtípust.

A következő elemek van fűzve a következők egyikével:

- **[A]**: Az összes csomóponthoz alkalmazható
- **[1]**: Alkalmazható, csak az 1. csomópont 
- **[2]**: Csak csomópont 2 használható

**[A]**  Előfeltételeket támasztja a konfigurációhoz:
1. Állítsa le mindkét felhasználói db2-adatbázis-kiszolgálók\<sid > a db2stop.
1. Módosítsa a rendszerhéj-környezetet, a DB2 rendszerhez\<sid > felhasználó */bin/ksh*. Azt javasoljuk, hogy a Yast eszköz használható. 


### <a name="pacemaker-configuration"></a>Támasztja konfiguráció

**[1]**  IBM Db2-specifikus HADR támasztja konfiguráció:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]**  Létrehozása IBM Db2-erőforrások:
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
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> anything \
        params binfile="/usr/bin/nc" cmdline_options="-l -k <b>62500</b>" \
        op monitor timeout="20s" interval="10" depth="0"

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]**  Start IBM Db2-erőforrások:
* Helyezze a támasztja ki karbantartási módból.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]**  Győződjön meg arról, hogy a fürt állapota OK, és az erőforrások használatába. Nem fontos abban, hogy melyik csomópont az erőforrások futnak.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 csomópont konfigurálva
# <a name="5-resources-configured"></a>5 erőforrások konfigurálva

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>Erőforrások teljes listája:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd (stonith:external / sbd): Started azibmdb02
#  <a name="resource-group-gipdb2ptrptr"></a>Erőforráscsoport: g_ip_db2ptr_PTR
#      <a name="rscipdb2ptrptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
#      <a name="rscncdb2ptrptr--ocfheartbeatanything------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat: semmi):      Started azibmdb02
#  <a name="masterslave-set-msldb2db2ptrptr-rscdb2db2ptrptr"></a>Elsődleges és tartalék kiszolgálók közötti beállítása: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Főkiszolgálók: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>Buildelés alárendelt: [azibmdb01]
</pre>

> [!IMPORTANT]
> Kell kezelni a támasztja fürtözött Db2-példány támasztja eszközök használatával. Például db2stop db2-parancsok használatakor támasztja észleli erőforrás a művelet sikertelen. Karbantartási hajt végre, ha a csomópontok vagy az erőforrások is nyilvántartani karbantartási módban. Támasztja felfüggeszti a monitorozási erőforrásokra, és ezután használhatja a szokásos db2 felügyeleti parancsok.


### <a name="configure-azure-load-balancer"></a>Az Azure Load Balancer konfigurálása
Az Azure Load Balancer konfigurálása, azt javasoljuk, hogy használja a [Azure Standard Load Balancer Termékváltozat](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) majd tegye a következőt:

1. Hozzon létre egy előtérbeli IP-címkészlet:

   a. Válassza ki az Azure Portalon nyissa meg az Azure Load Balancer **előtérbeli IP-címkészlet**, majd válassza ki **Hozzáadás**.

   b. Adja meg az új előtérbeli IP-címkészlet nevét (például **Db2-kapcsolat**).

   c. Állítsa be a **hozzárendelés** a **statikus**, és adja meg az IP-cím **virtuális IP-** elején meghatározott.

   d. Kattintson az **OK** gombra.

   e. Az új előtérbeli IP-címkészlet létrehozása után jegyezze fel a készlet IP-cím.

1. Hozzon létre egy háttércímkészletet:

   a. Válassza ki az Azure Portalon nyissa meg az Azure Load Balancer **háttérkészletek**, majd válassza ki **Hozzáadás**.

   b. Adja meg az új háttérkészlet nevét (például **Db2-háttérrendszer**).

   c. Válassza ki **adjon hozzá egy virtuális gépet**.

   d. Válassza ki a rendelkezésre állási csoport vagy a virtuális gépek, az előző lépésben létrehozott IBM Db2-adatbázis üzemeltetéséhez.

   e. Válassza ki a virtuális gépek, az IBM Db2-fürt.

   f. Kattintson az **OK** gombra.

1. Hozzon létre egy állapotmintát:

   a. Válassza ki az Azure Portalon nyissa meg az Azure Load Balancer **állapotadat-mintavételek**, és válassza ki **Hozzáadás**.

   b. Adja meg az új állapotadat-mintavétel nevét (például **Db2-hp**).

   c. Válassza ki **TCP** protokoll és port **62500**. Tartsa a **időköz** értékre állítva **5**, és tartsa a **nem kifogástalan állapot küszöbértéke** értékre állítva **2**.

   d. Kattintson az **OK** gombra.

1. A terheléselosztási szabályok létrehozásához:

   a. Válassza ki az Azure Portalon nyissa meg az Azure Load Balancer **terheléselosztási szabályok**, majd válassza ki **Hozzáadás**.

   b. Adja meg az új terheléselosztó-szabály nevét (például **Db2-SID**).

   c. Válassza ki az előtérbeli IP-címet, a háttérkészlet és az állapotmintákat, amelyek a korábban létrehozott (például **Db2-frontend**).

   d. Tartsa a **protokoll** beállítása **TCP**, és adja meg a port *adatbázis kommunikációjához használt portok*.

   e. Növelje a **üresjárati időkorlát** akár 30 percig.

   f. Ügyeljen arra, hogy **Floating IP engedélyezése**.

   g. Kattintson az **OK** gombra.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>SAP-profilok használatához a virtuális IP-kapcsolat módosítása
Szeretne csatlakozni az elsődleges példány a HADR konfiguráció, a SAP alkalmazásréteg kell használnia a virtuális IP-cím, amely meghatározott, és az Azure Load Balancer konfigurálva. A következő módosítások szükségesek:

/sapmnt/\<SID >/profil/alapértelmezett. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Elsődleges telepítése és alkalmazáskiszolgálók párbeszédpanel

Amikor telepíti az elsődleges és a párbeszédpanelen az alkalmazáskiszolgálókat egy Db2-HADR konfigurációs ellen, használja a virtuális gazdagép neve, amely a konfigurációt a kiválasztott. 

Ha a telepítés előtt a Db2 HADR konfigurációs létrehozott hajtotta végre, végezze el a módosításokat az előző szakaszban, és a következő SAP Java-implementációk leírtak szerint.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + Java vagy a Java stack rendszerek JDBC URL-cím ellenőrzése

A j2ee-alapú konfigurációs eszköz használatával ellenőrizze, vagy frissítse a JDBC URL-CÍMÉT. Mivel a j2ee-alapú konfigurációs eszközt egy grafikus eszközt, szüksége lesz az X kiszolgálón telepítve:
 
1. Jelentkezzen be az elsődleges application server, a J2EE-példány, és hajtsa végre:
     <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
1. Válassza a bal oldali keret **biztonsági tár**.
1. Válassza a megfelelő keretet, a fő jdbc/készlet / \ <SAPSID> /URL.
1. Módosítsa a JDBC URL-címben az állomásnév a virtuális gazdagép nevét.
     <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
1. Válassza ki **hozzáadása**.
1. A módosítások mentéséhez válassza a lemez ikonra a bal felső sarokban.
1. Zárja be a konfigurációs eszközt.
1. A Java-példány újraindítása.

## <a name="configure-log-archiving-for-hadr-setup"></a>Napló archiválás HADR telepítés konfigurálása
Adja meg a Db2-napló HADR telepítő archiválása, azt javasoljuk, hogy konfigurálja az elsődleges, mind a napló automatikus lekérésének funkció az összes napló archív helyekről-készenléti adatbázist. Az elsődleges és készenléti adatbázist archív naplófájlok lekérése az összes napló archív hely, mely vagy az egyik az adatbázis példányok előfordulhat, hogy archiválja a naplófájlokat képesnek kell lennie. 

A napló az archiválás csak az elsődleges adatbázis szerint történik. Ha módosítja a HADR szerepköröket, az adatbázis-kiszolgálók, vagy ha hiba történik, az új elsődleges adatbázis felelős log archiváláshoz. Ha beállította a napló-archívum több helyen, a naplók kétszer előfordulhat, hogy archiválja. Egy helyi vagy távoli sugárzott esetén előfordulhat, hogy is manuálisan kell átmásolnia az archivált naplók a régi elsődleges kiszolgálóról az új elsődleges kiszolgáló aktív napló helyét.

Azt javasoljuk, hogy egy közös NFS-megosztás, ahol mindkét csomópontjáról írt naplók konfigurálása. Az NFS-megosztások nem lehet a magas rendelkezésre állású. 

Használhatja a meglévő magas rendelkezésre állású NFS-megosztások átvitelek vagy egy profil könyvtár. További információkért lásd:

- [Magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken][nfs-ha] 
- [Magas rendelkezésre állás az SAP NetWeaver SUSE Linux Enterprise Server az Azure NetApp Files SAP alkalmazások az Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [NetApp Azure Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (az NFS-megosztások létrehozása)


## <a name="test-the-cluster-setup"></a>Tesztelje a fürt beállítása

Ez a szakasz ismerteti, hogyan tesztelheti a Db2 HADR beállításai. *Minden teszt azt feltételezi, hogy Ön bejelentkezett felhasználó legfelső szintű* az IBM DB2-höz elsődleges fut, és a *azibmdb01* virtuális gépet.

Valamennyi vizsgálati eset kezdeti állapota itt kifejtett: (crm_mon - r vagy crm állapot)

- **CRM-állapot** egy pillanatkép támasztja állapotának, végrehajtási idő 
- **az r - crm_mon** támasztja állapotának folyamatos kimenete

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

Az eredeti állapotát egy SAP-rendszerrel dokumentálva tranzakciós DBACOCKPIT > Konfiguráció > áttekintés, az alábbi képen látható módon:

![DBACockpit - áttelepítés előtti](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Teszt átvétel az IBM DB2-höz


> [!IMPORTANT] 
> A teszt a Kezdés előtt ellenőrizze, hogy:
> * Támasztja a sikertelen műveleteket (crm-status) nem rendelkezik.
> * Nincsenek megkötések nélkül helye (felesleg létrejöttének migrálási teszt kizárását)
> * Az IBM Db2 HADR szinkronizálás működik. Ellenőrizze a felhasználó db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Áttelepítés a csomópont, amelyen fut az elsődleges Db2-adatbázis a következő parancs végrehajtásával:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Az áttelepítés után a crm-állapot kimenet hasonlít:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

Az eredeti állapotát egy SAP-rendszerrel dokumentálva tranzakciós DBACOCKPIT > Konfiguráció > áttekintés, az alábbi képen látható módon:

![DBACockpit – a Migrálás után](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

A "crm-erőforrás át" erőforrás-migrálás helyre vonatkozó megkötések hoz létre. Törölni kell a helyre vonatkozó megkötések. Helyre vonatkozó megkötések nem törlődnek, ha az erőforrás nem lehet feladat-visszavételt, vagy nemkívánatos átvétel vehessen. 

Az erőforrás át vissza *azibmdb01* , és törölje a helyre vonatkozó megkötések
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **CRM-erőforrás át \<res_name > <host>:** Helyre vonatkozó megkötések hoz létre, és problémákat okozhat az átvétel
- **CRM-erőforrás törlése \<res_name >**: Törli a helyre vonatkozó megkötések
- **CRM-erőforrás karbantartása \<res_name >**: Összes hiba az erőforrás törlése

### <a name="test-the-fencing-agent"></a>Az elkerítés ügynök tesztelése

Ebben az esetben teszteljük SBD szintaxiskiemeléshez, amely azt javasoljuk, hogy végrehajtja a SUSE Linux használatakor.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Fürtcsomópont *azibmdb01* újra kell indítani. Az IBM DB2-höz elsődleges HADR szerepkör helyezhető át fogja *azibmdb02*. Amikor *azibmdb01* van ismét elérhető, a Db2-példány a szerepkör egy másodlagos adatbázis-példány áthelyezése történik. 

Ha a támasztja szolgáltatás nem indul el automatikusan az elsődleges számítógép újraindítva volt az, mindenképp indítsa el manuálisan a:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>A manuális átvétel tesztelése

A manuális átvétel letesztelheti a támasztja szolgáltatás leállítása a *azibmdb01* csomópont:
<pre><code>service pacemaker stop</code></pre>

az állapot *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

A feladatátvétel után elkezdheti a szolgáltatás ismét a *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>A csomóponton, amelyen a HADR elsődleges adatbázis a Db2-folyamat leállítása

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

A Db2-példány sikertelen lesz, és támasztja jelenteni állapota a következő:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Támasztja újraindul a Db2 elsődleges adatbázispéldány ugyanazon a csomóponton, vagy azt feladatátvételt hajt végre a csomópontra, hogy a másodlagos adatbázis-példány fut, és hibajelentést.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>A Db2-folyamat a csomóponton, amelyen a másodlagos adatbázis-példány leállítása

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

A csomópont lekéri az sikertelen volt rögzített, és hibát jelentett
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

A Db2-példány beolvasása újraindítása előtt hozzá volt rendelve, a másodlagos szerepkörben.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Állítsa le a DB db2stop kényszerítése a csomóponton, amelyen a HADR elsődleges adatbázispéldány-n keresztül

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Felhasználói db2,\<sid > hajtsa végre a parancsot db2stop kényszerítése:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Észlelt hiba
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

A Db2 HADR másodlagos adatbázis-példány az elsődleges szerepre való lett előléptetve
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Virtuális gép összeomlik, a csomóponton, amelyen az elsődleges adatbázis HADR példány újraindítással

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Az elsődleges példány szerepkör a másodlagos példány támasztja lépteti elő. A régi elsődleges példány a másodlagos szerepkörben áthelyezi a virtuális gép után, és minden szolgáltatás teljes visszaállítása, a virtuális gép újraindítása után:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>A virtuális gép, amelyen a HADR elsődleges adatbázis-példány a "halt" összeomlás

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Ebben az esetben támasztja észlelni fogja, hogy a csomópont, amelyen fut az elsődleges adatbázis-példány nem válaszol.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

a következő lépés az, hogy keressen egy *agy felosztása* helyzet. Miután a fennmaradó csomópontot megállapította, hogy a csomópontot, a legutóbbi futtatás az elsődleges adatbázis-példány nem működik, egy feladatátvételi erőforrások hajtja végre.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


Esetén a "halting" a csomópont, a sikertelen csomópont újra kell indítani az Azure felügyeleti eszközök segítségével (az az Azure portal, PowerShell vagy az Azure CLI-vel) rendelkezik. Miután a sikertelen csomópont újra online állapotba kerül, a Db2-példány a másodlagos szerepkörben kezdődik.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>További lépések
- [Magas rendelkezésre állású architektúra és forgatókönyvek az SAP NetWeaver számára](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Állítsa be a támasztja a SUSE Linux Enterprise Server az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

