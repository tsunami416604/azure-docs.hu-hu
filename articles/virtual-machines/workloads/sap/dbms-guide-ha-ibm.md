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
ms.openlocfilehash: 3c1d0e252b5c658ab6da2b3932918f05ba651d52
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565801"
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

IBM Db2 LUW (Linux, Unix és Windows) a [HADR konfigurációs](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) egy csomópontot egy elsődleges adatbázis-példányt futtató és a egy másodlagos adatbázis-példányt futtató legalább egy csomópont állnak. Az elsődleges adatbázis-példány módosítása első replikált másodlagos adatbázis-példány szinkron vagy aszinkron módon, a konfigurációtól függ. 

Ez a cikk bemutatja, hogyan üzembe helyezése és konfigurálása a virtuális gépek, a fürt-keretrendszer telepítése és telepítése és konfigurálása IBM Db2 LUW HADR konfigurációban. A cikk nem ismertetik, hogyan telepítése és konfigurálása az IBM Db2 LUW a HADR vagy SAP Szoftvertelepítés. SAP- és IBM telepítési kézikönyvek mutató hivatkozások által érhet el ezeket a feladatokat. Azure-környezethez adott részeket való összeállításán van. 

Támogatott IBM Db2-verziók a következők 10,5 és magasabb, a SAP Megjegyzés # ismertetett[1928533].

Olvassa el az alábbi SAP-megjegyzések és hamarosan eléri a telepítés előtt először:

| SAP-Jegyzetnek | Leírás |
| --- | --- |
| [1928533] | SAP-alkalmazások az Azure-ban: Támogatott termékek és Azure virtuális gépek típusai |
| [2015553] | A Microsoft Azure-beli SAP: Támogatás előfeltételei |
| [2178632] | Metrikák figyelése a Microsoft Azure-beli SAP-kulcs |
| [2191498] | SAP használata Linux az Azure-ral: Speciális figyelés |
| [2243692] | A Microsoft Azure (IaaS) virtuális gép Linux: Az SAP-licenccel kapcsolatos problémák |
| [1984787] |SUSE LINUX Enterprise Server 12: Telepítési jegyzetek |
| [1999351] |Továbbfejlesztett Azure Monitoring for SAP hibaelhárítási |
| [2233094] |DB6: SAP-alkalmazások Azure-ban, IBM DB2-höz használatával Linux, UNIX és a Windows - további információ |
| [1612105] |DB6: Db2 magas rendelkezésre állású vész-helyreállítási (HADR) kapcsolatos gyakori kérdések |


| Dokumentáció | 
| --- |
| [Az SAP közösségi WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik a szükséges SAP-megjegyzések mindegyikét linuxhoz |
| [Az Azure virtuális gépek tervezése és megvalósítása a linuxon futó SAP] [ planning-guide] útmutató |
| [Az Azure virtuális gépek üzembe helyezése Linuxon futó SAP-] [ deployment-guide] (Ez a cikk) |
| [Az Azure Virtual Machines DBMS üzembe helyezése Linuxon futó SAP-] [ dbms-guide] útmutató |
| [Az SAP számítási feladatok az Azure tervezési és telepítési ellenőrzőlista][azr-sap-plancheck] |
| [SUSE Linux Enterprise Server SAP alkalmazások 12 SP3 ajánlott eljárások][sles-for-sap-bp] |
| [SUSE Linux Enterprise magas rendelkezésre állású bővítmény 12 SP3][sles-ha-guide] |
| [IBM Db2 Azure virtuális gépek DBMS üzembe helyezési SAP számítási feladatokhoz][dbms-db2] |
| [IBM Db2 magas rendelkezésre állás vész-helyreállítási 11.1][db2-hadr-11.1] |
| [IBM Db2 magas rendelkezésre állás vész-helyreállítási R 10,5][db2-hadr-10.5] |

## <a name="overview"></a>Áttekintés
Magas rendelkezésre állás, a HADR az IBM Db2 LUW telepítve van, legalább két Azure virtuális gépeken, amelyek vannak üzembe helyezve egy [Azure rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) vagy keresztben [Azure-beli rendelkezésre állási zónák](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). Az alábbi ábrák a telepítés pedig két adatbázis-kiszolgáló Azure-beli virtuális gépek jeleníti meg. Mindkét adatbázis-kiszolgáló Azure-beli virtuális gépek saját csatlakoztatott tárhellyel rendelkező és futnak. Egy adatbázis-példány egy az Azure virtuális gépek HADR, a szerepkört az elsődleges példány van. Összes ügyfél csatlakozik az elsődleges példány. Adatbázis-tranzakciók az összes módosítást a Db2-tranzakciónapló helyileg tárolja. A tranzakciós napló rögzíti a helyi megmaradnak, mivel a rekordok átkerülnek TCP/IP-n keresztül az adatbázispéldányt a második kiszolgáló, a készenléti kiszolgálón vagy a készenléti példány. A készenléti példány működés közbeni előre az átvitt tranzakció rekordok naplózása a helyi adatbázis frissíti. Ezért a készenléti kiszolgáló az elsődleges kiszolgáló szinkronban másolatok.

HADR csak egy replikációs funkciót. Nincs hiba észlelése és nincs automatikus kiemelt felvásárlási vagy feladatátvételi létesítményekben rendelkezik. A kiemelt felvásárlási vagy a készenléti átviteli kell kezdeményeznie manuálisan egy adatbázis-rendszergazda. Az automatikus átvételét és a hibaészlelés érhető el, használhatja a Linux támasztja fürtszolgáltatáshoz. Támasztja a két adatbázis-kiszolgálók/példány figyeli. Ha az elsődleges adatbázis-server/példány leáll, támasztja kezdeményezi egy **automatikus** HADR átvétele a készenléti kiszolgáló, és emellett biztosítja, hogy a virtuális IP-cím van hozzárendelve az új elsődleges kiszolgáló.

![IBM Db2 magas rendelkezésre állású áttekintése](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

SAP-alkalmazáskiszolgálók csatlakozni az elsődleges adatbázishoz, és a egy virtuális gazdagépnév, illetve a virtuális IP-címet kell. Egy feladatátvételi esemény az SAP-alkalmazáskiszolgálók új elsődleges adatbázis-példányt fog csatlakozni. Azure-környezet egy [Azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) szükség egy virtuális IP-cím használata az HADR IBM Db2 szükséges módon. 

Teljes mértékben megérteni, hogyan illeszkedik a IBM Db2 LUW HADR és támasztja egy magas rendelkezésre állású SAP system telepítése a következő kép mutatja be egy magas rendelkezésre állású telepítés az SAP-rendszer IBM Db2-adatbázis alapján. Ez a cikk ismerteti a csak az IBM DB2-höz, és hogyan állítható be SAP-rendszer más összetevőitől egyéb cikkeit hivatkozik.

![IBM DB2 magas rendelkezésre ÁLLÁS teljes környezet – áttekintés](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-steps-needed"></a>Szükséges lépések magas szintű áttekintése
Az IBM Db2-konfiguráció üzembe helyezése, ezeket a lépéseket kell szerepelnie:

  + Környezet megtervezése
  + Virtuális gépek üzembe helyezése
  + SUSE Linux frissítése és fájlrendszerek konfigurálása
  + Telepítése és konfigurálása támasztja
  + Telepítés [NFS magas rendelkezésre állású][nfs-ha]
  + Telepítés [ASCS/SSZON külön fürtön][ascs-ha] 
  + IBM Db2-adatbázis telepítése elosztott és nagy rendelkezésre állási beállítás (SWPM)
  + Telepítés/létrehozás másodlagos adatbázis-csomópont és a példány és a HADR konfigurálása
  + Győződjön meg arról, hogy a HADR működik-e
  + Támasztja konfiguráció alkalmazása a IBM Db2-vezérlőbe
  + Az Azure Load Balancer konfigurálása 
  + Elsődleges és a párbeszédpanel telepítés alkalmazáskiszolgálók
  + Ellenőrzés/alkalmazkodjon az SAP-alkalmazáskiszolgálókhoz konfiguráció
  + Hajtson végre feladatátvételt / átvétel tesztek



## <a name="planning-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Az IBM Db2 LUW HADR az üzemeltető Azure-infrastruktúra megtervezése

Próbálja ki a helyezés megtervezése az üzembe helyezés végrehajtása előtt. A központi telepítése az Azure-ban HADR Db2-konfiguráció alapját készítéséhez. Fő elemeit, amelyek Testülettel Db2 LUW (SAP-környezet adatbázis része) tervezésének részeként kell.

| Témakör | Rövid leírás |
| --- | --- |
| Adja meg az Azure-erőforrás (ok) ban | Ha telepít virtuális gép, virtuális hálózatok közötti, Azure Load Balancer és más erőforrások erőforráscsoport(ok). Meglévő vagy új |
| Virtuális hálózat / alhálózat definíciója | Ahol IBM DB2-höz és az Azure Load Balancer virtuális gépek első vannak a telepítésre. Meglévő vagy újonnan létrehozott |
| IBM Db2 LUW üzemeltető virtuális gépek | Virtuális gép méretét, tárolási, hálózati, IP-cím |
| Virtuális állomásnevet és a virtuális IP-cím, az IBM Db2-adatbázis| Virtuális IP/állomásnév-kapcsolat az SAP-alkalmazáskiszolgálókhoz használt. **db-virt-hostname**, **db-virt-ip** |
| Az Azure szintaxiskiemeléshez | Az Azure az elkerítés vagy SBD szintaxiskiemeléshez (ajánlott). Módszer split agy helyzetek, amikor a rendszer letiltja |
| SBD VM | SBD virtuális gép méretét, tárolás, hálózat |
| Azure Load Balancer | Az alap vagy standard szintű (ajánlott), mintavételi port Db2-adatbázis (Javaslataink 62500) **mintavételi port** |
| Névfeloldás| Névfeloldásának működésével a környezetben. Erősen ajánlott DNS-szolgáltatás. Helyi gazdafájljában is használható. |
    
Ezekben a cikkekben található további részletek az Azure-beli Linux támasztja a használatát:

- [A SUSE Linux Enterprise Server az Azure-ban támasztja beállítása](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)



## <a name="deployment-on-suse-linux"></a>Üzembe helyezés SUSE Linux rendszeren

Az IBM Db2 LUW erőforrás-ügynök része a SUSE Linux Enterprise Server SAP-alkalmazások számára. A jelen dokumentumban ismertetett beállítása kötelező a SUSE Linux rendszerű kiszolgáló használata SAP-alkalmazások esetén. Az Azure Marketplace-en kép SUSE Enterprise Server számára, hogy az új Azure-beli virtuális gépek telepítéséhez használhatja az SAP alkalmazások 12 tartalmazza. Ügyeljen arra, amikor az Azure-beli Virtuálisgép-katalógusában a Virtuálisgép-lemezképek kiválasztásával keresztül az Azure-katalógusok SUSE által kínált különböző/szolgáltatás modell. 

### <a name="hosts---dns-updates"></a>Gazdagépek – DNS-frissítések
Győződjön meg arról, többek között a virtuális állomásnevek összes állomásnevek listáját, és frissítse a DNS-kiszolgálók megfelelő IP-cím állomásnév-hozzárendelés engedélyezése. Abban az esetben egy DNS-kiszolgáló nem létezik, vagy Ön nem módosíthatja, és a DNS-bejegyzéseket létrehozni, kihasználva a helyi állomások fájlok az egyes virtuális gépek ebben a forgatókönyvben résztvevő kell. Esetén használja a fájlok állomásbejegyzéssel, győződjön meg arról, hogy a kiegyenlítése a rendszer az SAP-környezet összes virtuális gépet kell. Ajánlás az, hogy a DNS-kiszolgálót, amely ideális terjesztve az Azure használata


### <a name="manual-deployment"></a>Manuális telepítés

Győződjön meg arról, hogy a kiválasztott operációs rendszer IBM Db2 LUW az IBM és az SAP által támogatott. Az Azure virtuális gépek és Db2-verziókban támogatott operációsrendszer-verziók listája érhető el az SAP-Jegyzetnek [1928533]. Az operációs rendszer kiadásai által az egyes Db2-kiadások listája az SAP-termék rendelkezésre állási mátrix érhető el. Kifejezetten ajánljuk, SLES 12 SP3 miatt Azure kapcsolódó teljesítménnyel kapcsolatos fejlesztések a jelen vagy újabb legalább SUSE Linux-verziók.

1. Erőforráscsoport létrehozása vagy kiválasztása
2. Hozzon létre vagy válassza ki a virtuális hálózat és alhálózat
3. Az Azure rendelkezésre állási csoport létrehozása vagy a rendelkezésre állási zónában üzembe helyezése
    + Rendelkezésre állási beállítása – két maximális frissítési tartományok beállítása
4. 1 virtuális gép létrehozása.
    + SLES használata Azure-katalógus az SAP-lemezkép
    + Válassza ki az Azure rendelkezésre állási csoport létrehozott lépés 3 vagy jelölje be a rendelkezésre állási zónában
5.  2 virtuális gép létrehozása.
    + SLES használata Azure-katalógus az SAP-lemezkép
    + Válassza ki az Azure rendelkezésre állási, a 3. lépésben létrehozott a csoportot. vagy válassza ki a rendelkezésre állási zónában - nem ugyanabban a zónában mint 3. lépés.
6. Hozzáadhat adatlemezeket a virtuális gépek – ellenőrizze a fájlrendszer beállítása a cikkben javasolt [IBM Db2 Azure virtuális gépek DBMS üzembe helyezési SAP számítási feladatokhoz][dbms-db2]

## <a name="create-the-pacemaker-cluster"></a>A támasztja fürt létrehozása
    
Kövesse a [támasztja a SUSE Linux Enterprise Server az Azure-beli beállítása][sles-pacemaker] az IBM Db2-kiszolgáló alapszintű támasztja fürt létrehozásához. 

## <a name="install-ibm-db2-luw-and-sap-environment"></a>IBM Db2 LUW és az SAP-környezet telepítése

Az IBM Db2 LUW alapján az SAP-környezet telepítésének megkezdése előtt tekintse át a (a cikk elején biztosított hivatkozások):

+ Azure-dokumentáció
+ Az SAP-dokumentáció
+ IBM-dokumentáció

Ellenőrizze a telepítés nyomtatása rossz minőségű az SAP NetWeaver-alapú alkalmazások telepítésével az IBM Db2 LUW.

A portál használatával SAP súgó útmutatókat talál az [SAP telepítési útmutató Keresőben][sap-instfind]

Szűrheti a keresést az útmutatók a szűrők beállítását a számának csökkentése érdekében:

+ akarok: "Az új rendszer telepítése"
+ Adatbázis: "A Windows, Linux és UNIX rendszerű az IBM DB2-höz"
+ SAP Netweaver verziók, veremkonfigurációját vagy operációs rendszer további szűrőket.

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>IBM Db2 LUW HADR való beállításával kapcsolatos telepítési javaslatok

Az IBM Db2 LUW elsődleges adatbázis-példány beállítása:

- Magas rendelkezésre állású vagy elosztott lehetőség használata
- Az SAP ASCS/SSZON és adatbázis-példány telepítése
- Biztonsági mentés készítése az újonnan telepített adatbázis


> [!IMPORTANT] 
> Jegyezze fel a telepítés során az "adatbázis-kommunikációhoz használt portot". Az adatbázis-példány is ugyanazt a portszámot kell lennie

A SAP homogén rendszer másolási eljárással készenléti kiszolgáló beállításához, hajtsa végre ezeket a lépéseket:

  - Rendszer másolási beállítással - adatbázispéldány - elosztott - rendszerek célként.
  - Másolás módszerként válassza ki a homogén rendszer másolás, hogy a biztonsági másolat segítségével készenléti server-példányon biztonsági másolatának visszaállítása
  - Ha egyenlege eléri a homogén rendszer példányához, az adatbázis visszaállításához a kilépési lépés, a telepítőből való kilépéshez. Elsődleges állomás egy biztonsági másolatból állítsa vissza az adatbázist. Összes ezt követő telepítéséhez fázisban már többségén futtatva volt az elsődleges kiszolgálón
- IBM DB2-höz HADR beállítása

> [!NOTE]
> Telepítési és konfigurációs adott Azure- és támasztja. A telepítési folyamatot a SAP Software kiépítés Manager során van egy explicit érdekli, hogy magas rendelkezésre állás az IBM Db2 LUW:
>+ Ne jelölje be az IBM DB2-höz pureScale
>+ Ne jelölje be a "telepítés IBM Tivoli rendszer Automation a Multiplatforms
>+ Ne válassza a "Létrehozás fürt konfigurációs fájlok"

> [!NOTE]
>Ha Linux támasztja SBD eszközt használja, a Db2 HADR paraméterek beállítása
>+ HADR társ időszak hossza (mp) (HADR_PEER_WINDOW) = 300  
>+ HADR időtúllépési értéket (HADR_TIMEOUT) = 60

> [!NOTE]
>Azure támasztja szintaxiskiemeléshez ügynök használatával:
>+ HADR társ időszak hossza (mp) (HADR_PEER_WINDOW) = 900  
>+ HADR időtúllépési értéket (HADR_TIMEOUT) = 60

Paraméterek használata akkor javasolt, a kezdeti tesztelés feladatátvétel/átvétel alapján. A kötelező átvételét és a feladatátvételi ezekkel a beállításokkal paraméter megfelelő működésének teszteléséhez. Egyéni konfigurációk eltérő lehet, mivel ezek a paraméterek korrekciós lehet szükség. 

> [!IMPORTANT]
> Képes az elsődleges adatbázis-példány indítása előtt kifejezetten az IBM DB2-höz, a rendszerindítási – az adatbázis másodlagos vagy készenléti állapotban lévő példány HADR konfigurációban működő kell lennie.

A bemutatóhoz, és ebben a dokumentumban ismertetett eljárást, az az adatbázis biztonsági AZONOSÍTÓRA van **PTR**.

##### <a name="ibm-db2-hadr-check"></a>IBM DB2-höz HADR ellenőrzése
HADR konfigurálás után pedig állapota és a csatlakoztatott az elsődleges és készenléti csomópontokon.

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

Támasztja használatakor az automatikus feladatátvételre csomópont hiba esetén szüksége a Db2-példányok és támasztja ennek megfelelően konfigurálja. Ez a szakasz azt ismerteti, ezt a konfigurációtípust.

A következő elemek van fűzve a következők egyikével:

- **[A]**  – alkalmazható minden csomópont
- **[1]**  – 1. csomópont csak érvényes 
- **a(z) [2]**  – 2. csomópont csak érvényes.

<!-- Yast is a fixed term in Linux and not a spelling error -->

**[A]**  Előfeltételeket támasztja a konfigurációhoz:
1. Mindkét felhasználó db2-adatbázis-kiszolgáló leállítása\<sid > a db2stop
2. Változás rendszerhéj-környezetet a DB2 rendszerhez\<sid > "/ bin/ksh" - felhasználó Yast eszköz használata ajánlott 


### <a name="pacemaker-configuration"></a>Konfiguráció támasztja:

**[1]**  IBM Db2 HADR támasztja konfigurációs
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]**  Létrehozása IBM Db2-erőforrások
<pre><code># Replace **bold strings** with your instance name db2sid, database SID and virtual IP address/Azure Load Balancer

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virutal IP - same as Azure Load Balancer IP
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

**[1]**  Start IBM Db2-erőforrások – támasztja helyezze ki karbantartási módból
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]**  Győződjön meg arról, hogy a fürt állapota rendben, és az erőforrások használatába. Nem számít mely erőforrásokat futtató csomóponton.
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
> Kezelése támasztja fürtözött Db2-példány támasztja eszközök segítségével kell elvégezni. Db2-parancsokkal (például db2stop) érzékeli támasztja által, az erőforrások hiba. Karbantartás esetén a csomópont vagy az erőforrások karbantartási módba helyezheti és támasztja felfüggeszti az monitorozási erőforrásokra, és normál db2 felügyeleti parancsok is használhatók.


### <a name="configure-azure-load-balancer"></a>Az Azure Load Balancer konfigurálása
Javasoljuk, hogy használja a [Azure Standard Load Balancer Termékváltozat](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. Állítsa be az Azure Load Balancer (az Azure Portalon). Először hozzon létre egy előtérbeli IP-címkészlet:

   1. Az Azure Portalon nyissa meg az Azure Load Balancer, jelölje be **előtérbeli IP-címkészlet**, és válassza ki **Hozzáadás**.
   2. Adja meg az új előtérbeli IP-címkészlet nevét (például **Db2-kapcsolat**).
   3. Állítsa be a **hozzárendelés** való **statikus** , és adja meg az IP-cím **virtuális IP-** definiált elején.
   4. Kattintson az **OK** gombra.
   5. Az új előtérbeli IP-címkészlet létrehozása után jegyezze fel a készlet IP-cím.

2. Következő lépés, hogy egy háttér-címkészlet létrehozása:

   1. Az Azure Portalon nyissa meg az Azure Load Balancer, jelölje be **háttérkészletek**, és válassza ki **Hozzáadás**.
   2. Adja meg az új háttérkészlet nevét (például **Db2-háttérrendszer**).
   3. Válassza ki **adjon hozzá egy virtuális gépet**.
   4. Válassza ki a rendelkezésre állási csoport/virtuális gépek, a 3. lépésben létrehozott IBM Db2-adatbázis üzemeltetéséhez.
   5. Válassza ki a virtuális gépek, az IBM Db2-fürt.
   6. Kattintson az **OK** gombra.

3. Harmadik lépés, hogy hozzon létre egy állapotmintát:

   1. Az Azure Portalon nyissa meg az Azure Load Balancer, jelölje be **állapotadat-mintavételek**, és válassza ki **Hozzáadás**.
   2. Adja meg az új állapotadat-mintavétel nevét (például **Db2-hp**).
   3. Válassza ki **TCP** protokoll és port **62500**. Tartsa a **időköz** értéket 5-ben és a **nem kifogástalan állapot küszöbértéke** értéke 2.
   4. Kattintson az **OK** gombra.

4. A terheléselosztási szabályok létrehozásához:

   1. Az Azure Portalon nyissa meg az Azure Load Balancerrel, jelölje be **terheléselosztási szabályok**, és válassza ki **Hozzáadás**.
   2. Adja meg az új terheléselosztó-szabály nevét (például **Db2-SID**).
   3. Válassza ki az előtérbeli IP-címet, a háttérkészlet és az állapotmintákat, amelyek a korábban létrehozott (például **Db2-frontend**).
   4. Tartsa a **protokoll** beállítása **TCP**, és adja meg a port *adatbázis kommunikációjához használt portok*.
   5. Növelje a **üresjárati időkorlát** akár 30 percig.
   6. Ügyeljen arra, hogy **Floating IP engedélyezése**.
   7. Kattintson az **OK** gombra.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>SAP-profilok használatához a virtuális IP-kapcsolat módosítása
Az SAP alkalmazásrétegre kell használnia a virtuális IP-cím definiálva, és a HADR konfigurációjának elsődleges példányhoz való csatlakozáshoz, az Azure Load Balancer konfigurálva. A következő módosítások szükségesek.

/sapmnt/\<SID >/profil/alapértelmezett. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Telepítés elsődleges és a párbeszédpanel alkalmazás kiszolgálók

Amikor telepítése elsődleges és a párbeszédpanelen az alkalmazáskiszolgálókat egy Db2-HADR konfigurációs ellen, használjon kiválasztott virtuális állomásnevet a konfiguráció. 

Abban az esetben a telepítés előtt hozza létre a Db2 HADR konfigurációt elvégzése kell módosításokat az előző bekezdésben, és a következő SAP Java-implementációk leírtak szerint.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + Java vagy a Java stack rendszerek JDBC URL-cím ellenőrzése

A j2ee-alapú konfigurációs eszköz használatával ellenőrizze, vagy frissítse a JDBC URL-CÍMÉT. Az a j2ee-kiszolgálókon a webkonfigurációs eszköz grafikus eszköz, ezért szüksége **X server** telepítve:
 
1. Jelentkezzen be a J2EE-példány elsődleges kiszolgáló, és hajtsa végre:
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
2. A bal oldali keretet válassza a biztonsági tár.
2. Válassza a megfelelő keretet, a főjdbc/készlet/ <SAPSID> /URL.
2. Módosítsa a JDBC URL-címben az állomásnév a virtuális gazdagép neve <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
5. Válassza ki hozzá.
5. A módosítások mentéséhez kattintson a lemez ikonra a bal felső sarokban.
5. Zárja be a konfigurációs eszközt.
5. A Java-példány újraindítása.

## <a name="configuration-of-log-archiving-for-hadr-setup"></a>Napló archiválásának HADR telepítő konfigurálása
Adja meg a Db2-napló HADR telepítő archiválása, azt javasoljuk, hogy konfigurálja az elsődleges, mind a napló automatikus lekérésének funkció az összes napló archív helyekről-készenléti adatbázist. Az elsődleges, mind az adatbázis összes napló archív hely, mely vagy az egyik az adatbázis példányok előfordulhat, hogy archiválja a naplófájlokat lekérdezni a naplófájlok archív képesnek kell lennie. 

A napló az archiválás csak történik az elsődleges adatbázis. Ha módosítja a HADR szerepköröket, az adatbázis-kiszolgálók, vagy ha hiba történik, az új elsődleges adatbázis felelős log archiváláshoz. Ha másik naplóban archív helyek beállítása, előfordulhat, hogy kétszer archiválja a naplók, és esetén a helyi vagy távoli sugárzott, lehetséges, hogy manuálisan kell átmásolnia az archivált naplók a régi elsődleges kiszolgálóról az új elsődleges kiszolgáló aktív napló helyét.

Azt javasoljuk, hogy hol naplók írt mindkét csomópontjáról közös NFS-megosztások konfigurálása. Az NFS nem lehet a magas rendelkezésre állású. 

Használhat meglévő magas rendelkezésre állású NFS szállítások, profilja használt. Olvasás:

- [Magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken][nfs-ha] 
- [Magas rendelkezésre állás az SAP NetWeaver SUSE Linux Enterprise Server az Azure NetApp Files SAP-alkalmazások az Azure Virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) használatával [Azure NetApp fájlok](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) NFS-megosztások létrehozása


## <a name="test-the-cluster-setup"></a>Tesztelje a fürt beállítása

Ez a szakasz ismerteti, hogyan tesztelheti a Db2 HADR beállításai. **Minden teszt azt feltételezi, hogy Ön bejelentkezett felhasználó legfelső szintű** és IBM DB2-höz elsődleges fut **azibmdb01** virtuális gépet.

Valamennyi vizsgálati eset kezdeti állapota itt kifejtett: (crm_mon - r vagy crm állapot)

- **CRM-állapot** egy pillanatkép támasztja állapot végrehajtási idő: 
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

Az eredeti állapotát egy SAP-rendszerrel dokumentálva tranzakciós DBACOCKPIT--> Configuration--> áttekintés, például:

![DBACockpit - áttelepítés előtti](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Teszt átvétel az IBM DB2-höz.


> [!IMPORTANT] 
> A teszt a Kezdés előtt győződjön meg arról, támasztja nem rendelkezik a sikertelen műveleteket (crm-állapot), és nincsenek megkötések nélkül helye (felesleg létrejöttének migrálási teszt kizárását), és az IBM Db2 HADR szinkronizálás működik. Ellenőrizze a felhasználó db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Áttelepítés a csomópont az elsődleges Db2-adatbázis futtatása a következő parancs végrehajtásával:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Miután megtörtént az áttelepítés, a crm-állapot kimenet hasonlít:
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

Az eredeti állapotát egy SAP-rendszerrel dokumentálva tranzakciós DBACOCKPIT--> Configuration--> áttekintés, például: ![DBACockpit – a Migrálás után](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

A "crm-erőforrás át" erőforrás-migrálás helyre vonatkozó megkötések hoz létre. Törölni kell a helyre vonatkozó megkötések. Helyre vonatkozó megkötések nem törlődnek, ha az erőforrás nem lehet feladat-visszavételt, vagy nem kívánatos átvétel vehessen. 

Erőforrás át vissza **azibmdb01** , és törölje a helyre vonatkozó megkötések
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>


- CRM-erőforrás át < res_name > <host> - hoz létre a helyre vonatkozó megkötések, és akkor okozhat problémát átvétele
- CRM erőforrás törlése < res_name > – törli a helyre vonatkozó megkötések
- CRM-erőforrás karbantartása < res_name > - törli az erőforrás összes hiba


### <a name="test-the-fencing-agent"></a>Az elkerítés ügynök tesztelése

Ebben az esetben teszteljük SBD szintaxiskiemeléshez, ami a használatot, a SUSE Linux ajánlott.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Fürtcsomópont **azibmdb01** újra kell indítani. IBM DB2-höz elsődleges HADR szerepkör helyezhető át fogja **azibmdb02**. Amikor **azibmdb01** van ismét elérhető, a Db2-példány a szerepkör egy másodlagos adatbázis-példány áthelyezése történik. 

Az eset, hogy a támasztja szolgáltatás nem indul el automatikusan az elsődleges számítógép újraindítva volt az ügyeljen arra, hogy indítsa el manuálisan a:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>A manuális átvétel tesztelése

A manuális átvétel letesztelheti a támasztja szolgáltatás leállítása a **azibmdb01** csomópont:
<pre><code>service pacemaker stop</code></pre>

az állapot **azibmdb02**
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

A feladatátvétel után elkezdheti a szolgáltatás ismét a **azibmdb01**.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-db2-process-on-the-node-running-the-hadr-primary-database"></a>A csomóponton futó a HADR elsődleges adatbázis Db2 folyamat leállítása

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

Támasztja fogja a Db2 azonos csomópont- vagy feladatátvétel a másodlagos adatbázis instanced futtató csomópont az elsődleges adatbázis-példány újraindítása, és a rendszer hibát jelez.

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


### <a name="kill-db2-process-on-node-that-runs-the-secondary-database-instance"></a>A másodlagos adatbázis-példányt futtató csomóponton Db2 folyamat leállítása

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

A Db2 példány get újraindítani a másodlagos szerepkört, előtt hozzá volt rendelve

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



### <a name="stop-db-via-db2stop-force-on-node-running-the-hadr-primary-database-instance"></a>Állítsa le a DB db2stop kényszerítése a HADR elsődleges adatbázis-példányt futtató csomóponton keresztül

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


### <a name="crash-vm-with-restart-on-node-running-the-hadr-primary-database-instance"></a>Összeomlás-virtuális gép az újraindítás a HADR elsődleges adatbázis-példányt futtató csomóponton

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



### <a name="crash-the-vm-running-the-hadr-primary-database-instance-with-halt"></a>A virtuális gép a HADR "halt" az elsődleges adatbázis-példányt futtató összeomlás

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Ebben az esetben a támasztja észlelni fogja, hogy a csomópont az elsődleges adatbázis-példányt futtató nem válaszol.

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

a következő lépés az, hogy keressen egy **agy felosztása** helyzet. Amint arról, hogy az elsődleges adatbázis-példány utolsó futtatta, a csomópont nem a fennmaradó csomópontot, erőforrások feladatátvétel fog hajtható végre
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


Esetén "Leállítás", a csomópont, a sikertelen csomópont van, újra kell indítani az Azure felügyeleti eszközök segítségével (portál, becsomagolja, AzureCLI,...). A sikertelen csomópont fogja a másodlagos szerepkörben, a Db2-példány után újra online állapotba kerül.

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
Tekintse meg ezt a dokumentációt:

- [Magas rendelkezésre állású architektúra és forgatókönyvek az SAP NetWeaver számára](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Beállítása támasztja a SUSE Linux adja meg.
- PRI
- SE Server az Azure-ban])https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

