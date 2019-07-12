---
title: IBM Db2 HADR beállítása az Azure-beli virtuális gépek (VM) az RHEL |} A Microsoft Docs
description: Az Azure-beli virtuális gépek (VM) az RHEL, IBM Db2 LUW magas rendelkezésre állású létesíteni.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/10/2019
ms.author: juergent
ms.openlocfilehash: b26a66eaee3a107c37d64541ec6b832331a3e2c9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812128"
---
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[1612105]: https://launchpad.support.sap.com/#/notes/1612105
[2694118]: https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:(https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Magas rendelkezésre állásának IBM Db2 LUW a Red Hat Enterprise Linux Server Azure virtuális gépeken

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
| [2002167] | Red Hat Enterprise Linux 7.x: Telepítés és frissítés |
| [2694118] | Red Hat Enterprise Linux magas RENDELKEZÉSREÁLLÁSI bővítmény az Azure-ban |
| [1999351] | Továbbfejlesztett Azure monitoring for SAP hibaelhárítási |
| [2233094] | DB6: SAP-alkalmazások az Azure-ban használt IBM DB2-höz Linux, UNIX és a Windows - további információ |
| [1612105] | DB6: Db2-HADR kapcsolatos gyakori kérdések |


| Dokumentáció | 
| --- |
| [Az SAP közösségi Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Rendelkezik a szükséges SAP-megjegyzések mindegyikét linuxhoz |
| [Az Azure virtuális gépek tervezése és megvalósítása a linuxon futó SAP][planning-guide] útmutató |
| [Az Azure virtuális gépek üzembe helyezése Linuxon futó SAP-][deployment-guide] (Ez a cikk) |
| [Az Azure Virtual Machines adatbázis felügyeleti system(DBMS) központi telepítése a linuxon futó SAP-][dbms-guide] útmutató |
| [Az SAP számítási feladatok az Azure tervezési és telepítési ellenőrzőlista][azr-sap-plancheck] |
| [A magas rendelkezésre állású bővítmény Red Hat Enterprise Linux 7 – áttekintés][rhel-ha-addon] |
| [Magas rendelkezésre állású kiegészítő felügyeleti][rhel-ha-admin] |
| [Magas rendelkezésre állású bővítmény referencia][rhel-ha-ref] |
| [RHEL magas rendelkezésre állású fürtöket – Microsoft Azure virtuális gépek a fürt tagjai terméktámogatási irányelveinek][rhel-azr-supp]
| [Telepítése és a Microsoft Azure egy Red Hat Enterprise Linux 7.4-es (és újabb verziók) magas rendelkezésre állású fürt konfigurálása][rhel-azr-inst]
| [IBM Db2 Azure virtuális gépek DBMS üzembe helyezési SAP számítási feladatokhoz][dbms-db2] |
| [IBM DB2-höz HADR 11.1][db2-hadr-11.1] |
| [IBM DB2-höz HADR 10,5][db2-hadr-10.5] |
| [RHEL magas rendelkezésre állású fürtöket – Linux, Unix és a egy fürtben Windows IBM Db2-felügyeleti szabályzat támogatása][rhel-db2-supp]



## <a name="overview"></a>Áttekintés
Magas rendelkezésre állás, a HADR az IBM Db2 LUW telepítve van, legalább két Azure virtuális gépeken, amelyek vannak üzembe helyezve egy [Azure rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) vagy keresztben [Azure-beli rendelkezésre állási zónák](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

Az alábbi ábrák a telepítés pedig két adatbázis-kiszolgáló Azure-beli virtuális gépek jelennek meg. Mindkét adatbázis-kiszolgáló Azure-beli virtuális gépek saját csatlakoztatott tárhellyel rendelkező és futnak. Egy adatbázis-példány egy az Azure virtuális gépek HADR, a szerepkört az elsődleges példány van. Összes ügyfél elsődleges példánnyal kapcsolódnak. Adatbázis-tranzakciók az összes módosítást a Db2-tranzakciónapló helyileg tárolja. A tranzakciós napló rögzíti a helyi megmaradnak, mivel a rekordok átkerülnek TCP/IP-n keresztül az adatbázispéldányt a második kiszolgáló, a készenléti kiszolgálón vagy a készenléti példány. A készenléti példány működés közbeni előre az átvitt tranzakció rekordok naplózása a helyi adatbázis frissíti. Ezzel a módszerrel a készenléti kiszolgáló marad az elsődleges kiszolgáló szinkronban.

HADR csak egy replikációs funkciót. Nincs hiba észlelése és nincs automatikus kiemelt felvásárlási vagy feladatátvételi létesítményekben rendelkezik. A kiemelt felvásárlási vagy az adatok átvitele a készenléti kiszolgáló kell kezdeményeznie manuálisan egy adatbázis-rendszergazda. Az automatikus átvételét és a hibaészlelés érhető el, használhatja a Linux támasztja fürtszolgáltatást. Támasztja a két adatbázis kiszolgálópéldányok figyeli. Ha az elsődleges adatbázis-kiszolgálópéldányra összeomlik, támasztja kezdeményezi egy *automatikus* HADR átvétele a készenléti kiszolgáló. Támasztja emellett biztosítja, hogy a virtuális IP-cím van hozzárendelve az új elsődleges kiszolgáló.

![IBM DB2-höz magas rendelkezésre állás – Áttekintés](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Szeretné, hogy az SAP alkalmazáskiszolgálók elsődleges adatbázishoz csatlakozni, szüksége van egy virtuális állomás neve és a virtuális IP-címet. Feladatátvétel esetén az SAP-alkalmazáskiszolgálók új elsődleges adatbázis-példányt fog csatlakozni. Azure-környezet egy [az Azure load balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) szükség egy virtuális IP-cím használata az HADR IBM Db2 által megkövetelt módon. 

Teljes mértékben megismerheti, hogyan illeszkedik a IBM Db2 LUW HADR és támasztja egy magas rendelkezésre állású SAP system telepítése érdekében az alábbi ábra mutatja be egy magas rendelkezésre állású telepítés az SAP-rendszer IBM Db2-adatbázis alapján. Ez a cikk ismerteti, csak az IBM DB2-höz, de más az SAP-rendszer más összetevőitől beállításával kapcsolatos cikkek mutató hivatkozásokat biztosít.

![IBM DB2-höz magas rendelkezésre állás teljes környezet – áttekintés](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>A szükséges lépések magas szintű áttekintése
Az IBM Db2-konfigurációjának üzembe helyezéséhez kövesse az alábbi lépéseket kell:

  + A környezet megtervezése.
  + A virtuális gépek üzembe helyezése.
  + Frissítse a RHEL Linux, és konfigurálja a fájlrendszerek.
  + Telepítse és konfigurálja a támasztja.
  + A telepítő [glusterfs fürt][glusterfs] or [Azure NetApp Files][anf-rhel]
  + Telepítés [ASCS/SSZON külön fürtben lévő][ascs-ha-rhel].
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
| Az Azure szintaxiskiemeléshez | Split agy helyzetek, amikor metódust letiltja. |
| Azure Load Balancer | Az alap vagy standard szintű (ajánlott), mintavételi port Db2-adatbázis (Javaslataink 62500) **mintavételi port**. |
| Névfeloldás| Névfeloldásának működésével a környezetben. Erősen ajánlott DNS-szolgáltatás. Helyi gazdafájljában is használható. |
    
Az Azure-ban Linux támasztja kapcsolatos további információkért lásd: [támasztja a Red Hat Enterprise Linux az Azure-beli beállítása][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux üzembe helyezés

Az IBM Db2 LUW erőforrás-ügynök része, a Red Hat Enterprise Linux Server magas rendelkezésre ÁLLÁS Addon. A telepítő ebben a dokumentumban leírt Red Hat Enterprise Linux for SAP kell használnia. Az Azure Marketplace-en Red Hat Enterprise Linux 7.4 SAP vagy újabb verzió esetén, amelyek segítségével új Azure-beli virtuális gépek üzembe helyezése egy képet tartalmazza. Vegye figyelembe a különböző támogatási vagy szolgáltatás modellek, hogy közben egy Virtuálisgép-lemezkép az Azure Virtuálisgép-Piacteréről by Red Hat, az Azure Marketplace-en keresztül érhetők el.

### <a name="hosts-dns-updates"></a>Hosts: DNS-frissítések
Győződjön meg arról, az összes gazdagép nevek, például virtuális állomásnevek listáját, és frissítse a DNS-kiszolgálók megfelelő IP-cím állomásnév-feloldás engedélyezéséhez. Ha egy DNS-kiszolgáló nem létezik vagy nem lehet frissíteni, és DNS-bejegyzéseket létrehozni, a helyi gazdagép fájlok az egyes virtuális gépek részt ebben a forgatókönyvben használni szeretne. Ha állomásbejegyzéssel fájlokat használ, ügyeljen arra, hogy a kiegyenlítése a rendszer az SAP-környezet minden virtuális gépre. Azonban azt javasoljuk, hogy használja-e a DNS-kiszolgálót, amely, ideális esetben kiterjeszti az Azure-bA


### <a name="manual-deployment"></a>Manuális telepítés

Győződjön meg arról, hogy a kiválasztott operációs rendszer IBM Db2 LUW az IBM és az SAP által támogatott. Az Azure virtuális gépek és Db2-verziókban támogatott operációsrendszer-verziók listája érhető el az SAP-jegyzetnek [1928533]. Az operációs rendszer kiadásai által az egyes Db2-kiadás listája az SAP-termék rendelkezésre állási mátrix érhető el. Red Hat Enterprise Linux 7.4 az SAP legalább ajánlott Azure-hoz kapcsolódó teljesítménnyel kapcsolatos fejlesztések a jelen vagy újabb miatt Red Hat Enterprise Linux verziók.

1. Hozzon létre vagy válasszon ki egy erőforráscsoportot.
1. Létrehozhat vagy kiválaszthat egy virtuális hálózatot és alhálózatot.
1. Hozzon létre egy Azure rendelkezésre állási csoportot, vagy egy rendelkezésre állási zónában üzembe helyezése.
    + A rendelkezésre állási csoport állítsa be a maximális frissítési tartományok 2-re.
1. 1 virtuális gép létrehozása.
    + Red Hat Enterprise Linux használata az SAP-lemezképet az Azure piactéren.
    + Válassza ki a 3. lépésben létrehozott Azure rendelkezésre állási csoportot, vagy rendelkezésre állási zónát válasszon ki.
1.  2 virtuális gép létrehozása.
    + Red Hat Enterprise Linux használata az SAP-lemezképet az Azure piactéren.
    + Válassza ki az Azure rendelkezésre állási csoport, a 3. lépésében létrehozott, vagy válassza ki a rendelkezésre állási zónában (nem ugyanabban a zónában mint 3. lépés).
1. Hozzáadhat adatlemezeket a virtuális gépeket, és ellenőrizze, a cikkben egy fájl rendszerbeállítás ajánlása [IBM Db2 Azure virtuális gépek DBMS üzembe helyezési SAP számítási][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>A támasztja fürt létrehozása
    
Az IBM Db2-kiszolgáló alapszintű támasztja fürtöt létrehozni, tekintse meg [támasztja a Red Hat Enterprise Linux az Azure-beli beállítása][rhel-pcs-azr]. 

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

#### <a name="red-hat-firewall-rules"></a>Red Hat-tűzfalszabályok
Red Hat Enterprise Linux tűzfal alapértelmezés szerint engedélyezve van. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>IBM Db2 LUW HADR való beállításával kapcsolatos telepítési javaslatok

Az IBM Db2 LUW elsődleges adatbázis-példány beállítása:

- A magas rendelkezésre állású vagy elosztott beállítást használja.
- Telepítse az SAP ASCS/SSZON és adatbázis-példányt.
- Az újonnan telepített adatbázis biztonsági másolatának igénybe vehet.

> [!IMPORTANT] 
> Jegyezze fel az "adatbázis kommunikációs port", amely a telepítés során. Az adatbázis-példány is ugyanazt a portszámot kell lennie.
>![SAP SWPM Port definíciója](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>IBM Db2 HADR beállítások az Azure-hoz

   Az Azure támasztja szintaxiskiemeléshez ügynök használata esetén állítsa be a következő paraméterekkel:

   - HADR társ időszak hossza (mp) (HADR_PEER_WINDOW) = 240  
   - HADR időtúllépési értéket (HADR_TIMEOUT) = 45

Azt javasoljuk, hogy a fenti paraméterek alapján a kezdeti tesztelés feladatátvétel/átvételt. Kötelező tesztelheti a feladatátvételi és átvétel megfelelő működésének ezekkel a beállításokkal paraméter. Egyéni konfigurációk eltérőek lehetnek, mert a paraméterek korrekciós lehet szükség. 

> [!NOTE]
> IBM DB2-höz, a rendszerindítási konfiguráció HADR konkrét: Ahhoz, hogy az elsődleges adatbázis-példány üzembe helyezéséig kell lennie a másodlagos vagy készenléti adatbázispéldány.

   
> [!NOTE]
> A telepítés és konfiguráció, amely kifejezetten az Azure-ban és támasztja: A telepítési folyamatot a SAP Software kiépítés Manager során egy magas rendelkezésre állás az IBM Db2 LUW explicit kérdése van:
>+ Ne válassza **IBM DB2-höz pureScale**.
>+ Ne válassza **telepítése IBM Tivoli rendszer automatizálási Multiplatforms**.
>+ Ne válassza **fürt konfigurációs fájljainak létrehozása**.
>![SAP SWPM - DB2 magas rendelkezésre ÁLLÁS beállításai](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


A készenléti kiszolgáló beállítása az SAP homogén rendszer másolási eljárás használatával, hajtsa végre ezeket a lépéseket:

1. Válassza ki a **rendszer másolási** lehetőséget > **igénylő rendszerek** > **elosztott** > **adatbázispéldány**.
1. Egy másolási módszerként válassza **homogén rendszer** , hogy a biztonsági mentés használatával biztonsági másolatának visszaállítása egy a készenléti server-példányon.
1. Ha egyenlege eléri a homogén rendszer példányához, az adatbázis visszaállításához a kilépési lépés, a telepítőből való kilépéshez. Elsődleges állomás egy biztonsági másolatból állítsa vissza az adatbázist. Összes ezt követő telepítéséhez fázisban már végre lett hajtva az elsődleges kiszolgálón.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Red Hat DB2 HADR vonatkozó tűzfalszabályok
DB2 és DB2 HADR működéséhez a közötti forgalmat engedélyező Tűzfalszabályok hozzáadása:
+ Adatbázis-kommunikációhoz használt portot. Partíciók használatával, ha túl adja hozzá ezeket a portokat.
+ HADR port (HADR_LOCAL_SVC DB2 paraméter értéke)
+ Az Azure mintavételi port
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR ellenőrzése
Bemutató célokra és ebben a cikkben leírt eljárások, az az adatbázis biztonsági AZONOSÍTÓRA van **ID2**.

Miután konfigurálta a HADR és állapota és a csatlakoztatva az elsődleges és készenléti csomópontokon, hajtsa végre a következő ellenőrzés:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N



#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N

</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2-támasztja konfiguráció

Használatakor támasztja automatikus feladatátvételhez egy csomópont meghibásodik, a Db2-példányok és támasztja ennek megfelelően konfigurálni kell. Ez a szakasz azt ismerteti, hogy ezt a konfigurációtípust.

A következő elemek van fűzve a következők egyikével:

- **[A]** : Az összes csomóponthoz alkalmazható
- **[1]** : Alkalmazható, csak az 1. csomópont 
- **[2]** : Csak csomópont 2 használható

**[A]**  Előfeltételeket támasztja a konfigurációhoz:
1. Állítsa le mindkét felhasználói db2-adatbázis-kiszolgálók\<sid > a db2stop.
1. Módosítsa a rendszerhéj-környezetet, a DB2 rendszerhez\<sid > felhasználó */bin/ksh*:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Támasztja konfiguráció

**[1]**  IBM Db2-specifikus HADR támasztja konfiguráció:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]**  Létrehozása IBM Db2-erőforrások:
<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]**  Start IBM Db2-erőforrások:
* Helyezze a támasztja ki karbantartási módból.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]**  Győződjön meg arról, hogy a fürt állapota OK, és az erőforrások használatába. Nem fontos abban, hogy melyik csomópont az erőforrások futnak.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Online: [az-idb01 az idb02]

Erőforrások teljes listája:

 rsc_st_azure   (stonith:fence_azure_arm):      Elindult az-idb01 elsődleges és tartalék kiszolgálók közötti beállítása: Db2_HADR_ID2-master [Db2_HADR_ID2] Masters: [ az-idb01 ] Slaves: [ az-idb02 ] Resource Group: g_ipnc_db2id2_ID2 vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01 nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Elindult az-idb01

Démon állapota: corosync: aktív/letiltott támasztja: aktív/letiltott pcsd: aktív/engedélyezve
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

**[A]**  Hozzáadás tűzfalszabályt a mintavételi portot:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

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

Azt javasoljuk, hogy egy közös NFS-megosztások vagy GlusterFS, ahol mindkét csomópontjáról írt naplók konfigurálása. NFS-megosztás, vagy GlusterFS rendelkezik magas rendelkezésre állású legyen. 

Használhatja meglévő magas rendelkezésre állású NFS-megosztások vagy GlusterFS átvitelek vagy egy profil könyvtár. További információkért lásd:

- [Red Hat Enterprise Linuxon futó Azure-beli virtuális gépeken üzemelő GlusterFS SAP NetWeaverhez][glusterfs] 
- [Magas rendelkezésre állás az SAP NetWeaver az Azure Virtual machines Red Hat Enterprise Linux for SAP-alkalmazások Azure NetApp fájlokkal][anf-rhel]
- [NetApp Azure Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (az NFS-megosztások létrehozása)

## <a name="test-the-cluster-setup"></a>Tesztelje a fürt beállítása

Ez a szakasz ismerteti, hogyan tesztelheti a Db2 HADR beállításai. Minden teszt azt feltételezi, IBM DB2-höz elsődleges fut a *az-idb01* virtuális gépet. Felhasználót sudo jogosultsági szinttel, vagy a legfelső szintű (nem ajánlott) kell használni.

Valamennyi vizsgálati eset kezdeti állapota itt kifejtett: (crm_mon - r vagy a számítógépek állapota)

- **számítógépek állapota** egy pillanatkép támasztja állapotának, végrehajtási idő 
- **az r - crm_mon** támasztja állapotának folyamatos kimenete

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

Az eredeti állapotát egy SAP-rendszerrel dokumentálva tranzakciós DBACOCKPIT > Konfiguráció > áttekintés, az alábbi képen látható módon:

![DBACockpit - áttelepítés előtti](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Teszt átvétel az IBM DB2-höz


> [!IMPORTANT] 
> A teszt a Kezdés előtt ellenőrizze, hogy:
> * Támasztja nem rendelkezik a sikertelen műveleteket (személyi számítógépek állapotának).
> * Nincsenek megkötések nélkül helye (felesleg létrejöttének migrálási teszt kizárását)
> * Az IBM Db2 HADR szinkronizálás működik. Ellenőrizze a felhasználó db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Áttelepítés a csomópont, amelyen fut az elsődleges Db2-adatbázis a következő parancs végrehajtásával:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

Az áttelepítés után a crm-állapot kimenet hasonlít:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

Az eredeti állapotát egy SAP-rendszerrel dokumentálva tranzakciós DBACOCKPIT > Konfiguráció > áttekintés, az alábbi képen látható módon:

![DBACockpit – a Migrálás után](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

Erőforrás-migrálás az "számítógépek erőforrás-áthelyezési" helyre vonatkozó megkötések hoz létre. Helyre vonatkozó megkötések ebben az esetben megakadályozza, hogy az az-idb01 futó IBM Db2-példány. Helyre vonatkozó megkötések nem törlődnek, ha az erőforrás nem visszavételt.

Távolítsa el a hely megtartása és a készenléti csomópont az az-idb01 indul.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
És a fürt állapotra vált:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit - eltávolított hely megtartása](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Az erőforrás át vissza *az-idb01* , és törölje a helyre vonatkozó megkötések
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **számítógépek erőforrás-áthelyezési \<res_name > <host>:** Helyre vonatkozó megkötések hoz létre, és problémákat okozhat az átvétel
- **számítógépek erőforrás törlése \<res_name >** : Törli a helyre vonatkozó megkötések
- **számítógépek erőforrás karbantartása \<res_name >** : Összes hiba az erőforrás törlése

### <a name="test-a-manual-takeover"></a>A manuális átvétel tesztelése

A manuális átvétel letesztelheti a támasztja szolgáltatás leállítása a *az-idb01* csomópont:
<pre><code>systemctl stop pacemaker</code></pre>

az állapot *az-ibdb02*
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

A feladatátvétel után elkezdheti a szolgáltatás ismét a *az-idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>A csomóponton, amelyen a HADR elsődleges adatbázis a Db2-folyamat leállítása

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

A Db2-példány sikertelen lesz, és támasztja áthelyezi a fő csomópontot és állapota a következő jelentés:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Támasztja újraindul a Db2 elsődleges adatbázispéldány ugyanazon a csomóponton, vagy azt feladatátvételt hajt végre a csomópontra, hogy a másodlagos adatbázis-példány fut, és hibajelentést.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>A Db2-folyamat a csomóponton, amelyen a másodlagos adatbázis-példány leállítása

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

A csomópont lekéri az sikertelen volt rögzített, és hibát jelentett
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

A Db2-példány beolvasása újraindítása előtt hozzá volt rendelve, a másodlagos szerepkörben.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Állítsa le a DB db2stop kényszerítése a csomóponton, amelyen a HADR elsődleges adatbázispéldány-n keresztül

Felhasználói db2,\<sid > hajtsa végre a parancsot db2stop kényszerítése:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Észlelt hiba:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

A Db2 HADR másodlagos adatbázis-példány, az elsődleges szerepkör lett előléptetve.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>A virtuális gép, amelyen a HADR elsődleges adatbázis-példány a "halt" összeomlás

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

Ebben az esetben támasztja észlelni fogja, hogy a csomópont, amelyen fut az elsődleges adatbázis-példány nem válaszol.

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

a következő lépés az, hogy keressen egy *agy felosztása* helyzet. Miután a fennmaradó csomópontot megállapította, hogy a csomópontot, a legutóbbi futtatás az elsődleges adatbázis-példány nem működik, egy feladatátvételi erőforrások hajtja végre.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


Kernelpánikot, ha a sikertelen csomópont lesz restared szintaxiskiemeléshez ügynök. Miután a sikertelen csomópont újra online állapotba kerül, el kell indítania támasztja-fürt
<pre><code>sudo pcs cluster start</code></pre> Elindítja a Db2-példány a másodlagos szerepkörben.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>További lépések
- [Magas rendelkezésre állású architektúra és forgatókönyvek az SAP NetWeaver számára](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [A Red Hat Enterprise Linux az Azure-ban támasztja beállítása][rhel-pcs-azr]
