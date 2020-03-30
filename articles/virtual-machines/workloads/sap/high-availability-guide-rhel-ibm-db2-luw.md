---
title: Az IBM Db2 HADR beállítása az Azure virtuális gépeken (VM-ek) RHEL-en | Microsoft dokumentumok
description: Az IBM Db2 LUW magas rendelkezésre állását az Azure virtuális gépeken (VMs) RHEL-en hozza létre.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.openlocfilehash: c6a230f6abeab45c56aab2db40b8b1defcc06d90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598697"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


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
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Az IBM Db2 LUW magas rendelkezésre állása Azure-beli virtuális gépeken Red Hat Enterprise Linux Serveren

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
| [2002167] | Red Hat Enterprise Linux 7.x: Telepítés és frissítés |
| [2694118] | Red Hat Enterprise Linux HA bővítmény az Azure-ban |
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
| [A Red Hat Enterprise Linux 7 magas rendelkezésre állású bővítményének áttekintése][rhel-ha-addon] |
| [Magas rendelkezésre állású bővítmény felügyelete][rhel-ha-admin] |
| [Magas rendelkezésre állású bővítményhivatkozás][rhel-ha-ref] |
| [Az RHEL magas rendelkezésre állású fürtjeinek támogatási szabályzatai – Microsoft Azure virtuális gépek fürttagként][rhel-azr-supp]
| [Red Hat Enterprise Linux 7.4 (és újabb) magas rendelkezésre állású fürt telepítése és konfigurálása a Microsoft Azure-ban][rhel-azr-inst]
| [IBM Db2 Azure Virtual Machines DBMS üzembe helyezése az SAP-munkaterheléshez][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [Az RHEL magas rendelkezésre állású fürtjeinek támogatási házirendje – Az IBM Db2 kezelése Linuxra, Unixra és Windows rendszerre fürtben][rhel-db2-supp]



## <a name="overview"></a>Áttekintés
A magas rendelkezésre állás elérése érdekében az IBM Db2 LUW HADR-rel legalább két Azure virtuális gépre van telepítve, amelyek egy [Azure rendelkezésre állási készletben](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) vagy az [Azure rendelkezésre állási zónáiban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)vannak telepítve. 

A következő grafikák két adatbázis-kiszolgáló Azure-virtuális gép beállítását jelenítik meg. Mindkét adatbázis-kiszolgáló Azure-beli virtuális gépek saját tárolóval rendelkeznek, és már futnak. A HADR-ben az egyik Azure-beli virtuális gép egyik adatbázis-példányának az elsődleges példány szerepköre. Minden ügyfél kapcsolódik az elsődleges példányhoz. Az adatbázis-tranzakciók minden változása helyileg megmarad a Db2 tranzakciós naplóban. Mivel a tranzakciónapló-rekordok helyi legmegőrzöttek, a rekordok TCP/IP protokollon keresztül kerülnek átvitelre a második adatbázis-kiszolgáló, a készenléti kiszolgáló vagy a készenléti példány adatbázispéldányába. A készenléti példány frissíti a helyi adatbázist az átvitt tranzakciónapló-rekordok továbbításával. Ily módon a készenléti kiszolgáló szinkronban marad az elsődleges kiszolgálóval.

A HADR csak replikációs funkció. Nem rendelkezik hibaészleléssel, és nincs automatikus átvételi vagy feladatátvételi lehetőség. A készenléti kiszolgálóra történő átvételt vagy átvitelt manuálisan kell kezdeményeznie az adatbázis rendszergazdájának. Az automatikus átvétel és a hibaészlelés eléréséhez használhatja a Linux Pacemaker fürtözési funkciót. A pacemaker figyeli a két adatbázis-kiszolgálópéldányt. Amikor az elsődleges adatbázis-kiszolgálópéldány összeomlik, a Pacemaker *automatikus* HADR-átvételt kezdeményez a készenléti kiszolgálón. A pacemaker azt is biztosítja, hogy a virtuális IP-cím hozzá legyen rendelve az új elsődleges kiszolgálóhoz.

![IBM Db2 magas rendelkezésre állás – áttekintés](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Ahhoz, hogy az SAP-alkalmazáskiszolgálók csatlakozzanak az elsődleges adatbázishoz, virtuális állomásnévre és virtuális IP-címre van szükség. Feladatátvétel esetén az SAP-alkalmazáskiszolgálók új elsődleges adatbázis-példányhoz fognak csatlakozni. Azure-környezetben egy [Azure-terheléselosztó](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) szükséges a virtuális IP-cím használatához az IBM Db2 HADR-hez szükséges módon. 

Annak érdekében, hogy teljes mértékben megérthesd, hogy az IBM DB2 LUW HADR-rel és Pacemakerrel hogyan illeszkedik egy magas rendelkezésre állású SAP rendszer beállításába, az alábbi kép áttekintést nyújt az IBM Db2 adatbázison alapuló SAP rendszer magas rendelkezésre állású beállításáról. Ez a cikk csak az IBM Db2-t ismerteti, de az SAP-rendszer más összetevőinek beállításáról szóló egyéb cikkekre mutató hivatkozásokat tartalmaz.

![IBM DB2 magas rendelkezésre állás teljes környezet áttekintése](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>A szükséges lépések magas szintű áttekintése
Az IBM Db2 konfiguráció telepítéséhez az alábbi lépéseket kell végrehajtania:

  + Tervezze meg környezetét.
  + Telepítse a virtuális gépeket.
  + Frissítse az RHEL Linuxot és konfigurálja a fájlrendszereket.
  + Pacemaker telepítése és konfigurálása.
  + [Globsterfs-fürt][glusterfs] vagy [Azure NetApp-fájlok][anf-rhel] beállítása
  + Telepítse [az ASCS/ERS-t egy külön fürtre.][ascs-ha-rhel]
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
| Azure kerítés | Módszer, hogy elkerüljék osztott agyi helyzetek ben megelőzhető. |
| Azure Load Balancer | Alapszintű vagy standard (ajánlott), a Db2 adatbázis (a mi javaslatunk: 62500) szonda-port mintavételi **portjának**használata. |
| Névfeloldás| Hogyan működik a névfeloldás a környezetben? A DNS-szolgáltatás használata erősen ajánlott. A helyi hosts fájl használható. |
    
Az Azure-beli Linux-pacemakerről a [Pacemaker beállítása az Azure-ban][rhel-pcs-azr]című témakörben talál további információt.

## <a name="deployment-on-red-hat-enterprise-linux"></a>Telepítés a Red Hat Enterprise Linux-on

Az IBM Db2 LUW erőforrásügynöke a Red Hat Enterprise Linux Server HA Addon része. Az ebben a dokumentumban ismertetett beállításhoz a Red Hat Enterprise Linux sap-hoz használható. Az Azure Piactér tartalmaz egy lemezképet a Red Hat Enterprise Linux 7.4 SAP vagy újabb sap-hoz, hogy új Azure virtuális gépek üzembe helyezéséhez használható. Vegye figyelembe a Red Hat által az Azure Piactéren keresztül kínált különböző támogatási vagy szolgáltatási modelleket, amikor kiválaszt egy virtuálisgép-lemezképet az Azure VM Piactéren.

### <a name="hosts-dns-updates"></a>Gazdagépek: DNS-frissítések
Készítsen listát az összes állomásnévről, beleértve a virtuális állomásneveket is, és frissítse a DNS-kiszolgálókat, hogy a megfelelő IP-cím állomásnév-feloldáshoz legyen. Ha egy DNS-kiszolgáló nem létezik, vagy nem tudja frissíteni és létrehozni a DNS-bejegyzéseket, akkor az ebben a forgatókönyvben részt vevő egyes virtuális gépek helyi állomásfájljait kell használnia. Ha gazdafájlok bejegyzéseit használja, győződjön meg arról, hogy a bejegyzések az SAP-rendszerkörnyezetben lévő összes virtuális gépre vonatkoznak. Azt javasoljuk azonban, hogy használja a DNS-t, amely ideális esetben kiterjed az Azure-ra


### <a name="manual-deployment"></a>Manuális üzembe helyezés

Győződjön meg arról, hogy a kiválasztott operációs rendszert támogatja az IBM/SAP az IBM Db2 LUW számára. Az Azure virtuális gépek és db2-kiadások támogatott operációs rendszerverzióinak listája az SAP [1928533]megjegyzésében érhető el. Az operációs rendszer egyes Db2-kiadások által kiadott verziók listája az SAP-termék rendelkezésre állási mátrixában érhető el. Javasoljuk, hogy legalább egy Red Hat Enterprise Linux 7.4 az SAP-hoz, mert az Azure-ral kapcsolatos teljesítménybeli fejlesztések ebben vagy később a Red Hat Enterprise Linux verziók.

1. Erőforráscsoport létrehozása vagy kijelölése.
1. Virtuális hálózat és alhálózat létrehozása vagy kijelölése.
1. Hozzon létre egy Azure rendelkezésre állási készletet, vagy telepítsen egy rendelkezésre állási zónát.
    + A rendelkezésre állási csoport esetében állítsa a maximális frissítési tartományokat 2-re.
1. Virtuális gép létrehozása 1.
    + Használja a Red Hat Enterprise Linux sap-lemezképet az Azure Marketplace-en.
    + Válassza ki a 3.
1.  Virtuális gép létrehozása 2.
    + Használja a Red Hat Enterprise Linux sap-lemezképet az Azure Marketplace-en.
    + Válassza ki az Azure rendelkezésre állási készlet létrehozott 3.
1. Adja hozzá az adatlemezeket a virtuális gépekhez, majd ellenőrizze a fájlrendszer beállításának ajánlását az [IBM Db2 Azure Virtual Machines DBMS üzembe helyezésének az SAP-munkaterheléshez][dbms-db2]című cikkben.

## <a name="create-the-pacemaker-cluster"></a>A pacemaker-fürt létrehozása
    
Az IBM Db2 kiszolgálóhoz alapvető Pacemaker-fürt létrehozásáról a [Szívritmus-szabályozó beállítása az Azure-ban a Red Hat Enterprise Linux][rhel-pcs-azr]on Beállítás című témakörben található. 

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

#### <a name="red-hat-firewall-rules"></a>Red Hat tűzfalszabályok
A Red Hat Enterprise Linux tűzfala alapértelmezés szerint engedélyezve van. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Telepítési tanácsok az IBM Db2 LUW HADR-rel történő beállításához

Az elsődleges IBM Db2 LUW adatbázispéldány beállítása:

- Használja a magas rendelkezésre állásvagy elosztott lehetőséget.
- Telepítse az SAP ASCS/ERS és az adatbázis-példány.
- Készítsen biztonsági másolatot az újonnan telepített adatbázisról.

> [!IMPORTANT] 
> Írja le a telepítés során beállított "Adatbázis-kommunikációs portot". Mindkét adatbázispéldányhoz ugyanaz a portszámnak kell lennie.
>![SAP SWPM port definíciója](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>IBM Db2 HADR beállítások az Azure-hoz

   Ha egy Azure Pacemaker vívó ügynök, állítsa be a következő paramétereket:

   - HADR társablak időtartama (másodperc) (HADR_PEER_WINDOW) = 240  
   - HADR időtúlérték (HADR_TIMEOUT) = 45

Javasoljuk, hogy az előző paraméterek alapján a kezdeti feladatátvételi/átvételi tesztelés. Ezekkel a paraméterbeállításokkal kötelező a feladatátvétel és a átvétel megfelelő működését tesztelni. Mivel az egyes konfigurációk eltérőek lehetnek, a paraméterek módosítására szorulhatnak. 

> [!NOTE]
> Az IBM Db2-re jellemző, normál indítással rendelkező HADR-konfigurációval: A másodlagos vagy készenléti adatbázispéldánynak az elsődleges adatbázispéldány elindításához fel kell állnia és futnia kell.

   
> [!NOTE]
> Az Azure-ra és a Pacemakerre jellemző telepítéshez és konfigurációhoz: Az SAP Szoftverkiépítési kezelőn keresztüli telepítési eljárás során egyértelmű kérdés merül fel az IBM Db2 LUW magas rendelkezésre állásával kapcsolatban:
>+ Ne válassza **az IBM Db2 pureScale lehetőséget**.
>+ Ne válassza **az IBM Tivoli System Automation telepítése többplatformos platformokra**lehetőséget.
>+ Ne válassza **a Fürtkonfigurációs fájlok létrehozása lehetőséget.**
>![SAP SWPM – DB2 HA beállítások](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Ha a készenléti adatbázis-kiszolgálót az SAP homogén rendszermásolási eljárással szeretné beállítani, hajtsa végre az alábbi lépéseket:

1. Jelölje be a **Rendszermásolás** beállítást > **Célrendszerek** > **elosztott** > **adatbázispéldányát.**
1. Másolási módszerként válassza a **Homogén rendszer** lehetőséget, hogy a biztonsági mentés segítségével visszaállíthassa a biztonsági másolatot a készenléti kiszolgálópéldányon.
1. Amikor eléri a kilépési lépést, hogy visszaállítsa az adatbázishomogén rendszer másolatát, lépjen ki a telepítőből. Az adatbázis visszaállítása az elsődleges állomás biztonsági másolatából. Az összes további telepítési fázis már végrehajtásra került az elsődleges adatbázis-kiszolgálón.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Red Hat tűzfal szabályok DB2 HADR
Adja meg a tűzfalszabályokat, hogy a FORGALOM a DB2 és a DB2 között a HADR dolgozni:
+ Adatbázis kommunikációs portja. Partíciók használata esetén adja hozzá azokat a portokat is.
+ HADR port (a DB2 paraméter értéke HADR_LOCAL_SVC)
+ Azure mintavételi port
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR ellenőrzés
Szemléltetési célokra és az ebben a cikkben ismertetett eljárásokra az adatbázis SID-je **ID2.**

Miután konfigurálta a HADR-t, és az állapot PEER és CONNECTED az elsődleges és készenléti csomópontokon, hajtsa végre a következő ellenőrzést:

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



## <a name="db2-pacemaker-configuration"></a>Db2 pacemaker konfiguráció

Ha csomóponthiba esetén automatikus feladatátvételre használ pacemakert, ennek megfelelően kell konfigurálnia a Db2-példányokat és a Pacemakert. Ez a szakasz az ilyen típusú konfigurációt ismerteti.

A következő elemek előtaggal vannak ellátva:

- **[A]**: Minden csomópontra alkalmazható
- **[1]**: Csak az 1. 
- **[2]**: Csak a 2- es csomópontra alkalmazható

**[A]** A pacemaker konfigurációjának előfeltétele:
1. Állítsa le mindkét adatbázis-kiszolgálót a db2\<user sid> a db2stop segítségével.
1. Módosítsa a db2\<sid> felhasználó shell környezetét */bin/ksh-ra:*
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Szívritmus-szabályozó konfigurációja

**[1]** IBM Db2 HADR-specifikus pacemaker konfiguráció:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** IBM Db2 források létrehozása:
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

**[1]** Indítsa el az IBM Db2 erőforrásait:
* A Pacemaker tegyék ki a karbantartási módból.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** Ellenőrizze, hogy a fürt állapota rendben van-e, és hogy az összes erőforrás elindult.[1] Győződjön meg arról, hogy a fürt állapota rendben van, és hogy az összes erőforrás elindult. Nem fontos, hogy melyik csomóponton futnak az erőforrások.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Az erőforrások teljes listája:

 rsc_st_azure (stonith:fence_azure_arm): Started az-idb01 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2] Masters: [ az-idb01 ] Slaves: [ az-idb02 ] Erőforráscsoport: g_ipnc_db2id2_ID2 vip_db2id2_ID2 (ocf::heartbeat:IPaddr2): Started az-idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb): Started az-idb01

Démon állapota: corosync: aktív/fogyatékkal pacemaker: aktív/letiltott pcsd: aktív/engedélyezve
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

**[A]** Tűzfalszabály hozzáadása a mintavételi porthoz:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

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
 
1. Jelentkezzen be a J2EE-példány elsődleges alkalmazáskiszolgálójára, és hajtsa végre:
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. A bal oldali keretben válassza a **Biztonsági tároló lehetőséget.**
1. A megfelelő keretben válassza `jdbc/pool/\<SAPSID>/url`ki a gombot .
1. Módosítsa a JDBC URL-címében lévő állomásnevet a virtuális állomás névre.
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. Válassza a **Hozzáadás** lehetőséget.
1. A módosítások mentéséhez válassza a lemez ikonját a bal felső sarokban.
1. Zárja be a konfigurációs eszközt.
1. Indítsa újra a Java-példányt.

## <a name="configure-log-archiving-for-hadr-setup"></a>Naplóarchiválás konfigurálása hadr beállításhoz
A DB2-napló archiválásának hadr-telepítéshez történő konfigurálásához azt javasoljuk, hogy mind az elsődleges, mind a készenléti adatbázist úgy állítsa be, hogy az automatikus napló-lekérési képesség legyen az összes naplóarchívum-helyről. Mind az elsődleges, mind a készenléti adatbázisnak képesnek kell lennie a napló-archívum fájljainak beolvasására az összes olyan naplóarchívum-helyről, ahamelybe az adatbázis-példányok bármelyike archiválhatja a naplófájlokat. 

A naplóarchiválást csak az elsődleges adatbázis végzi. Ha módosítja az adatbázis-kiszolgálók HADR szerepköreit, vagy ha hiba történik, az új elsődleges adatbázis felelős a naplóarchiválásért. Ha több naplóarchívum-helyet állított be, előfordulhat, hogy a naplók at kétszer archiválja a rendszer. Helyi vagy távoli felzárkózás esetén előfordulhat, hogy manuálisan is át kell másolnia az archivált naplókat a régi elsődleges kiszolgálóról az új elsődleges kiszolgáló aktív naplóhelyére.

Azt javasoljuk, hogy konfigurálja a közös NFS-megosztás vagy GlusterFS, ahol naplók írása mindkét csomópontról. Az NFS megosztásnak vagy a GlusterFS-nek magas rendelkezésre állásúnak kell lennie. 

A meglévő magas rendelkezésre állású NFS-megosztásokat vagy glusterfs-eket átvitelhez vagy profilkönyvtárhoz használhatja. További információkért lásd:

- [Red Hat Enterprise Linuxon futó Azure-beli virtuális gépeken üzemelő GlusterFS SAP NetWeaverhez][glusterfs] 
- [Az SAP NetWeaver magas rendelkezésre állása az Azure-beli virtuális gépeken a Red Hat Enterprise Linuxon az SAP-alkalmazásokhoz készült Azure NetApp-fájlokkal][anf-rhel]
- [Azure NetApp-fájlok](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (NFS-megosztások létrehozása)

## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

Ez a szakasz azt ismerteti, hogyan tesztelheti a Db2 HADR beállítását. Minden teszt feltételezi, hogy az IBM Db2 elsődleges fut az *az-idb01* virtuális gépen. A sudo jogosultságokkal vagy gyökérrel (nem ajánlott) rendelkező felhasználót kell használni.

Az összes teszteset kezdeti állapotát itt ismertetik: (crm_mon -r vagy db állapot)

- **a pcs állapota** a Pacemaker állapotának pillanatképe a végrehajtás időpontjában 
- **crm_mon -r** a pacemaker állapotának folyamatos kimenete

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

Az SAP-rendszer eredeti állapotát a Transaction DBACOCKPIT > Konfiguráció > áttekintése című témakör ben dokumentálja, ahogy az az alábbi képen látható:

![DBACockpit – áttelepítés előtti](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Az IBM Db2 tesztátvétele


> [!IMPORTANT] 
> A teszt megkezdése előtt győződjön meg arról, hogy:
> * A pacemaker nem rendelkezik sikertelen műveletekkel (pcs állapot).
> * Nincsenek helykorlátozások (az áttelepítési teszt maradékai)
> * Az IBM Db2 HADR szinkronizálás működik. Ellenőrizze a felhasználó\<db2 sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Telepítse át az elsődleges Db2 adatbázist futtató csomópontot a következő parancs végrehajtásával:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

Az áttelepítés után a crm állapot kimenete így néz ki:
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

Az SAP-rendszer eredeti állapotát a Transaction DBACOCKPIT > Konfiguráció > áttekintése című témakör ben dokumentálja, ahogy az az alábbi képen látható:

![DBACockpit – Áttelepítés után](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

A "pcs resource move" (pcs resource move) erőforrás-áttelepítéssel helykényszereket hoz létre. A helymegkötések ebben az esetben megakadályozzák az IBM Db2 példány futtatását az az-idb01-en. Ha a helymegkötések nem törlődnek, az erőforrás nem tud visszaadható.

Távolítsa el a helymegkötést, és a készenléti csomópont elindul az az-idb01-en.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
És a fürt állapota a következőre változik:
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

![DBACockpit – Eltávolított helykorlátozás](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Az erőforrás áttelepítése az *az-idb01-re,* és törölje a helymegkötéseket
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **pcs erőforrás \<mozog <host>res_name>:** Helymegkötéseket hoz létre, és problémákat okozhat az átvételsel
- **a számítógép \<erőforrás törlése res_name>**: Törli a helymegkötéseket
- **pcs erőforrás-karbantartás \<res_name>: **Törli az erőforrás összes hibás

### <a name="test-a-manual-takeover"></a>Kézi átvétel tesztelése

A manuális átvételt az *az-idb01* csomópont pacemaker szolgáltatásának leállításával tesztelheti:
<pre><code>systemctl stop pacemaker</code></pre>

állapot *az-ibdb02*
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

A feladatátvétel után újra indíthatja a szolgáltatást *az-idb01-en.*
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>A Db2 folyamat megölése a HADR elsődleges adatbázist futtató csomóponton

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

A Db2 példány sikertelen lesz, és a Pacemaker áthelyezi a főcsomópontot, és a következő állapotot jelenti:

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

A pacemaker újraindítja a Db2 elsődleges adatbázispéldányt ugyanazon a csomóponton, vagy átadja a feladatát a másodlagos adatbázis-példányt futtató csomópontnak, és hibát jelent.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>A Db2 folyamat leírása a másodlagos adatbázispéldányt futtató csomóponton

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

A csomópont sikertelen állapotba kerül, és a jelentett hiba
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

A Db2-példány újraindul a korábban hozzárendelt másodlagos szerepkörben.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>A DB leállítása a DB2stop force-on keresztül a HADR elsődleges adatbázispéldányt futtató csomóponton

Mivel a\<felhasználó db2 sid> végre parancs db2stop erő:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Hiba észlelve:

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

A Db2 HADR másodlagos adatbázispéldány lett előléptetve az elsődleges szerepkörbe.
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


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>A HADR elsődleges adatbázis-példányát futtató virtuális gép összeomlása "stop"

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

Ebben az esetben a Pacemaker észleli, hogy az elsődleges adatbázis-példányt futtató csomópont nem válaszol.

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

A következő lépés az, hogy ellenőrizze a *Split agyi* helyzet. Miután a túlélő csomópont megállapította, hogy az elsődleges adatbázis-példányt utoljára futtató csomópont nem, az erőforrások feladatátvétele végrehajtásra kerül.

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


Kernelpánik esetén a meghibásodott csomópontot kerítésügynök fogja újramegbámulni. Miután a meghibásodott csomópont újra online állapotba kerül, el kell indítania a pacemaker-fürtöt
<pre><code>sudo pcs cluster start</code></pre> elindítja a Db2 példányt a másodlagos szerepkörbe.

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
- [Pacemaker beállítása a Red Hat Enterprise Linuxon az Azure-ban][rhel-pcs-azr]
