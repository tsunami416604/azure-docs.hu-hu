---
title: SAP HANA horizontális felskálázás készenléti állapottal az RHEL-en lévő Azure NetApp-fájlokkal| Microsoft dokumentumok
description: Magas rendelkezésre állású útmutató az SAP NetWeaver számára a Red Hat Enterprise Linuxon az Azure NetApp-fájlokkal AZ SAP-alkalmazásokhoz
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2020
ms.author: radeltch
ms.openlocfilehash: 78f60cbd20e7801f4af26372884275d7d6782777
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754149"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>SAP HANA horizontális felskálázási rendszer üzembe helyezése készenléti csomókkal az Azure virtuális gépein az Azure NetApp-fájlok használatával Red Hat Enterprise Linux on Red Hat Enterprise Linux on 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2455582
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Ez a cikk ismerteti, hogyan telepíthet egy magas rendelkezésre állású SAP HANA-rendszert egy kibővített konfigurációban az Azure Red Hat Enterprise Linux virtuális gépek (VM-ek) készenléti állapotában, az [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) használatával a megosztott tárolókötetek használatával.  

A példakonfigurációkban, telepítési parancsokban és így tovább a HANA példány **03,** a HANA rendszerazonosító pedig **HN1.** A példák a HANA 2.0 SP4 és a Red Hat Enterprise Linux sap 7.6 alapján alapulnak. 

Mielőtt elkezdené, olvassa el a következő SAP-jegyzeteket és dokumentumokat:

* [Az Azure NetApp Files dokumentációja][anf-azure-doc] 
* Az SAP Note [1928533 a] következőket tartalmazza:  
  * Az SAP-szoftverek telepítéséhez támogatott Azure virtuálisgép-méretek listája
  * Fontos kapacitásadatok az Azure virtuális gépek méretéhez
  * Támogatott SAP szoftverek, operációs rendszer (OS) és adatbázis-kombinációk
  * A szükséges SAP kernel verzió Windows és Linux rendszeren a Microsoft Azure-ban
* SAP Megjegyzés [2015553]: Az SAP által támogatott SAP-szoftvertelepítések előfeltételei az Azure-ban
* Az SAP Note [2002167] ajánlotta a Red Hat Enterprise Linux operációs rendszerbeállításait
* Az SAP Note [2009879] SAP HANA irányelveket készített a Red Hat Enterprise Linux-hoz
* SAP [2178632:]Részletes információkat tartalmaz az SAP-hoz az Azure-ban jelentett összes figyelési metrikáról
* SAP Megjegyzés [2191498]: Tartalmazza a szükséges SAP Host Agent verzió linuxos Azure-ban
* SAP Megjegyzés [2243692:]Információkat tartalmaz az SAP-licencelésről linuxos Azure-ban
* SAP Megjegyzés [1999351:]További hibaelhárítási információkat tartalmaz az SAP-hoz való azure továbbfejlesztett figyelési bővítményhez
* SAP Megjegyzés [1900823:]Az SAP HANA tárolási követelményeivel kapcsolatos információkat tartalmaz
* [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Tartalmazza az összes szükséges SAP-jegyzetet Linuxhoz
* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz Linuxon][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz Linuxon][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz Linuxon][dbms-guide]
* Általános RHEL dokumentáció
  * [Magas rendelkezésre állású bővítmény – áttekintés](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású bővítmény felügyelete](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású bővítményhivatkozás](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Red Hat Enterprise Linux hálózati útmutató](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* Azure-specifikus RHEL dokumentáció:
  * [Az SAP HANA telepítése a Red Hat Enterprise Linux ra a Microsoft Azure-ban való használatra](https://access.redhat.com/solutions/3193782)
* [NetApp SAP-alkalmazások a Microsoft Azure-ban az Azure NetApp-fájlok használatával][anf-sap-applications-azure]


## <a name="overview"></a>Áttekintés

Hana magas rendelkezésre állás elérésének egyik módja a gazdagép automatikus feladatátvétel konfigurálása. A gazdagép automatikus feladatátvételkonfigurálásához adjon hozzá egy vagy több virtuális gépet a HANA-rendszerhez, és állítsa be őket készenléti csomópontokként. Ha az aktív csomópont meghibásodik, a készenléti csomópont automatikusan átveszi az irányítást. Az Azure virtuális gépekkel bemutatott konfigurációban az [NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)használatával érhető el az Azure NetApp Files használatával.  

> [!NOTE]
> A készenléti csomópontnak hozzá kell férnie az összes adatbáziskötethez. A HANA-köteteket NFSv4-kötetként kell csatlakoztatni. A továbbfejlesztett fájl bérlet-alapú zár mechanizmus az NFSv4 protokoll használják `I/O` kerítés. 

> [!IMPORTANT]
> A támogatott konfiguráció létrehozásához telepítenie kell a HANA-adatokat és a naplóköteteket NFSv4.1 kötetként, és csatlakoztatnia kell őket az NFSv4.1 protokoll használatával. A HANA állomás automatikus feladatátvételi konfiguráció készenléti csomóponttal nem támogatja az NFSv3.

![SAP NetWeaver magas rendelkezésre állás – áttekintés](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

Az előző ábrán, amely az SAP HANA hálózati javaslatokat követi, három alhálózat jelenik meg egy Azure virtuális hálózaton belül: 
* Ügyfélkommunikációhoz
* A tárolórendszerrel való kommunikációhoz
* Belső HANA inter-node kommunikációhoz

Az Azure NetApp-kötetek külön alhálózatban vannak, [delegálva az Azure NetApp-fájloknak.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)  

Ebben a példában a konfiguráció, az alhálózatok a következők:  

  - `client`10.9.1.0/26  
  - `storage`10.9.3.0/26  
  - `hana`10.9.2.0/26  
  - `anf`10.9.0.0/26 (delegált alhálózat az Azure NetApp-fájlokhoz)

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Az Azure NetApp Files infrastruktúra beállítása 

Mielőtt folytatná az Azure NetApp Files infrastruktúra telepítését, ismerkedjen meg az [Azure NetApp Files][anf-azure-doc]dokumentációjával. 

Az Azure NetApp Files számos [Azure-régióban](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)érhető el. Ellenőrizze, hogy a kiválasztott Azure-régió kínál-e Azure NetApp-fájlokat.  

Az Azure NetApp-fájlok Azure-régiónkénti elérhetőségéről az [Azure NetApp-fájlok rendelkezésre állása az Azure régió szerint][anf-avail-matrix]című témakörben talál további információt.  

Az Azure NetApp-fájlok üzembe helyezése előtt kérjen bevezetést az Azure NetApp-fájlokba az [Azure NetApp-fájlok regisztrálása utasításokhoz.][anf-register] 

### <a name="deploy-azure-netapp-files-resources"></a>Az Azure NetApp Files erőforrásainak telepítése  

A következő utasítások feltételezik, hogy már telepítette az [Azure virtuális hálózatát.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Az Azure NetApp Files erőforrásait és a virtuális gépeket, ahol az Azure NetApp Files erőforrások lesznek csatlakoztatva, ugyanabban az Azure virtuális hálózatban vagy társviszonyban lévő Azure virtuális hálózatokban kell üzembe helyezni.  

1. Ha még nem telepítette az erőforrásokat, kérje [az Azure NetApp-fájlokba történő bevezetést.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)  

2. Hozzon létre egy NetApp-fiókot a kiválasztott Azure-régióban a [NetApp-fiók létrehozása](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)című részben található utasításokat követve.  

3. Hozzon létre egy Azure NetApp-fájlok kapacitáskészletét az [Azure NetApp-fájlok kapacitáskészletének beállítása](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)című részben található utasításokat követve.  

   A jelen cikkben bemutatott HANA-architektúra egyetlen Azure NetApp-fájlkapacitáskészletet használ az *Ultra szolgáltatás* szintjén. Az Azure-beli HANA-számítási feladatok hoz javasoljuk az Azure NetApp Files *Ultra* vagy *a Prémium szintű* szolgáltatási [szint](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)használatát.  

4. Alhálózat delegálása az Azure NetApp-fájlokba az [Alhálózat delegálása az Azure NetApp-fájlokhoz](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)című útmutatóban leírtak szerint.  

5. Az Azure NetApp-fájlok köteteinek üzembe helyezéséhez kövesse az [NFS-kötet létrehozása az Azure NetApp-fájlokhoz](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)című útmutatóutasításait követve.  

   A kötetek telepítése kor válassza ki az **NFSv4.1** verzióját. Telepítse a köteteket a kijelölt Azure NetApp Files [alhálózatban.](https://docs.microsoft.com/rest/api/virtualnetwork/subnets) 
   
   Ne feledje, hogy az Azure NetApp Files-erőforrásoknak és az Azure virtuális gépeknek ugyanabban az Azure-beli virtuális hálózatban vagy társviszonyt létesített Azure virtuális hálózatokban kell lenniük. Például **hn1**-data-mnt00001, **HN1**-log-mnt00001, és így tovább, a kötet nevét és nfs://10.9.0.4/**HN1**-data-mnt00001, nfs://10.9.0.4/**HN1**-log-mnt00001, és így tovább, a fájl elérési utak az Azure NetApp fájlok kötetek.  

   * **HN1**-data-mnt00001 kötet (nfs://10.9.0.4/**HN1**-data-mnt00001)
   * **HN1**-data-mnt00002 kötet (nfs://10.9.0.4/**HN1**-data-mnt00002)
   * **hn1**-log-mnt00001 kötet (nfs://10.9.0.4/**HN1**-log-mnt00001)
   * **HN1**-log-mnt00002 kötet (nfs://10.9.0.4/**HN1**-log-mnt00002)
   * kötet **HN1**-megosztott (nfs://10.9.0.4/**HN1**-megosztott)
   
   Ebben a példában egy külön Azure NetApp-fájlok kötetet használtunk minden HANA-adathoz és naplókötethez. A kisebb vagy nem produktív rendszerek költségoptimalizált konfigurációja érdekében az összes adatcsatolást egyetlen kötetre helyezheti, és az összes napló egy másik egyetlen kötetre van csatlakoztatva.  

### <a name="important-considerations"></a>Fontos szempontok

Az Azure NetApp-fájlok SAP HANA horizontális felskálázása a készenléti csomópontok forgatókönyvvel történő létrehozásakor vegye figyelembe az alábbi fontos szempontokat:

- A minimális kapacitású medence 4 tebibyte (TiB).  
- A minimális térfogatméret 100 gibibájt (GiB).
- Az Azure NetApp-fájloknak és minden olyan virtuális gépnek, amelyhez az Azure NetApp-fájlok kötetei csatlakoztatva lesznek, ugyanabban az Azure-beli virtuális hálózatban vagy [társviszonyban lévő virtuális hálózatokban](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) kell lennie ugyanabban a régióban.  
- A kijelölt virtuális hálózatnak rendelkeznie kell egy alhálózattal, amely delegált az Azure NetApp-fájlok.
- Az Azure NetApp Files kötet átviteli forgalma a mennyiségi kvóta és a szolgáltatásszint függvénye, az [Azure NetApp-fájlok szolgáltatási szintjén](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)dokumentált módon. A HANA Azure NetApp-kötetek méretezésekénél győződjön meg arról, hogy az eredményül kapott átviteli igény megfelel a HANA rendszerkövetelményeinek.  
- Az Azure NetApp Files [exportálási szabályzattal](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)szabályozhatja az engedélyezett ügyfeleket, a hozzáférési típust (olvasás-írás, csak olvasható és így tovább). 
- Az Azure NetApp Files szolgáltatás még nem rendelkezik zónabarát. Jelenleg a szolgáltatás nincs telepítve az Azure-régió összes rendelkezésre állási zónájában. Vegye figyelembe a lehetséges késési következményekkel jár egyes Azure-régiókban.  

> [!IMPORTANT]
> AZ SAP HANA számítási feladatok esetén az alacsony késés kritikus fontosságú. Együttműködve a Microsoft képviselőjével annak érdekében, hogy a virtuális gépek és az Azure NetApp Files kötetek közvetlen közelben legyenek telepítve.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>HanA-adatbázis méretezése az Azure NetApp-fájlokon

Az Azure NetApp Files kötet átviteli forgalma a kötet méretének és a szolgáltatás szintjének függvénye, az [Azure NetApp-fájlok szolgáltatási szintjén](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)dokumentált módon. 

Az SAP azure-beli infrastruktúrájának tervezésekor vegye figyelembe az SAP néhány minimális tárolási követelményét, amelyek minimális átviteli jellemzőket eredményeznek:

- Írás-írás a /hana/log 250 megabájt/másodperc (MB/s) 1 MB I/O mérettel.  
- Legalább 400 MB/s olvasási tevékenység a /hana/data 16 MB és 64 MB I/O méretek esetén.  
- Legalább 250 MB/s írási tevékenység a /hana/data 16 MB és 64 MB I/O méretben. 

Az [Azure NetApp Files átviteli korlát1](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) TiB kötetkvóta a következők:
- Prémium szintű tárolási szint - 64 MiB/s  
- Ultra tárolási szint - 128 MiB/s  

Az SAP minimális átviteli követelményeinek teljesítéséhez az adatokés a napló, valamint a /hana/shared irányelveinek teljesítéséhez az ajánlott méretek a következők:

| Kötet | A mérete<br>Prémium szintű tárolási szint | A mérete<br>Ultra tárolási szint | Támogatott NFS protokoll |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | 4.1.-es |
| /hana/adatok | 6.3 TiB | 3.2 TiB | 4.1.-es |
| /hana/megosztott | 1xRAM 4 dolgozó csomópontonként | 1xRAM 4 dolgozó csomópontonként | 3-as vagy 4.1-es |

Az SAP HANA konfigurációaz ebben a cikkben bemutatott elrendezés, az Azure NetApp Files Ultra Storage réteg használatával, a következő lenne:

| Kötet | A mérete<br>Ultra tárolási szint | Támogatott NFS protokoll |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | 4.1.-es |
| /hana/log/mnt00002 | 2 TiB | 4.1.-es |
| /hana/data/mnt00001 | 3.2 TiB | 4.1.-es |
| /hana/data/mnt00002 | 3.2 TiB | 4.1.-es |
| /hana/megosztott | 2 TiB | 3-as vagy 4.1-es |

> [!NOTE]
> Az Itt megadott Azure NetApp-fájlok méretezési javaslatok az SAP által az infrastruktúra-szolgáltatók számára ajánlott minimális követelményeknek való megfelelnek. Valós ügyfél-telepítések és számítási feladatok forgatókönyvek, ezek a méretek nem elegendőek. Ezeket a javaslatokat kiindulási pontként használja, és alkalmazkodjon az adott munkaterhelés követelményei alapján.  

> [!TIP]
> Az Azure NetApp-fájlok köteteit dinamikusan átméretezheti anélkül, hogy le kellene választania a köteteket, le kellene *állítania* a virtuális gépeket, vagy le kellene állítania az SAP HANA-t. Ez a megközelítés lehetővé teszi a rugalmasságot, hogy megfeleljen az alkalmazás várható és előre nem látható átviteli igényeinek.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Linuxos virtuális gépek telepítése az Azure Portalon keresztül

Először létre kell hoznia az Azure NetApp Files köteteket. Ezután tegye a következő lépéseket:
1. Hozza létre az [Azure virtuális hálózati alhálózatait](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) az [Azure virtuális hálózatában.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 
1. Telepítse a virtuális gépeket. 
1. Hozza létre a további hálózati adaptereket, és csatolja a hálózati adaptereket a megfelelő virtuális gépekhez.  

   Minden virtuális gép három hálózati adapter, amely megfelel a három`client`Azure `storage` `hana`virtuális hálózati alhálózatok ( , és . 

   További információ: [Linuxos virtuális gép létrehozása az Azure-ban több hálózati csatolókártyával.](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics)  

> [!IMPORTANT]
> AZ SAP HANA számítási feladatok esetén az alacsony késés kritikus fontosságú. Alacsony késés elérése érdekében a Microsoft képviselőjével együttműködve győződjön meg arról, hogy a virtuális gépek és az Azure NetApp-fájlok kötetei közvetlen közelben vannak telepítve. Amikor az SAP HANA Azure NetApp-fájlokat használó [új SAP HANA-rendszer bevezetésével](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) adja meg a szükséges információkat. 
 
A következő utasítások feltételezik, hogy már létrehozta az erőforráscsoportot, az Azure virtuális hálózatot `storage` `hana`és a három Azure virtuális hálózati alhálózatot: `client`és . A virtuális gépek telepítésekor jelölje ki az ügyfél alhálózatát, hogy az ügyfél hálózati adaptere a virtuális gépek elsődleges kapcsolata. Emellett konfigurálnia kell egy explicit útvonalat az Azure NetApp Files delegált alhálózatához a tároló alhálózati átjárón keresztül. 

> [!IMPORTANT]
> Győződjön meg arról, hogy a kiválasztott operációs rendszer SAP-tanúsítvánnyal rendelkezik az SAP HANA az adott virtuálisgép-típusok használata. Az SAP HANA-tanúsítvánnyal rendelkező virtuálisgép-típusok és az operációs rendszer ilyen típusú kiadásainak listáját az [SAP HANA-tanúsítvánnyal rendelkező IaaS-platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) webhelyén keresse fel. Kattintson a felsorolt virtuális gép típusának részleteire az adott típusSAP HANA által támogatott operációsrendszer-kiadások teljes listájának lekérni.  

1. Hozzon létre egy rendelkezésre állási készlet et az SAP HANA számára. Győződjön meg arról, hogy beállította a maximális frissítési tartományt.  

2. Hozzon létre három virtuális gépet (**hanadb1**, **hanadb2**, **hanadb3**) a következő lépésekkel:  

   a. Használjon egy Red Hat Enterprise Linux-lemezképet az SAP HANA által támogatott Azure-galériában. Ebben a példában rhel-SAP-HA 7.6-os képet használtunk.  

   b. Válassza ki az SAP HANA korábban létrehozott rendelkezésre állási készletét.  

   c. Válassza ki az ügyfél Azure virtuális hálózati alhálózat. Válassza [a Gyorsított hálózat lehetőséget.](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)  

   A virtuális gépek telepítésekor a rendszer automatikusan létrehozza a hálózati csatoló nevét. Az egyszerűség kedvéért az egyszerűség kedvéért az ügyfél Azure virtuális hálózati alhálózatához csatolt automatikusan létrehozott hálózati adapterekre, mint **hanadb1-client**, **hanadb2-client**és **hanadb3-client**néven hivatkozunk. 

3. Hozzon létre három hálózati adaptert, egyet `storage` minden virtuális géphez a virtuális hálózati alhálózathoz (ebben a példában **hanadb1-storage**, **hanadb2-storage**és **hanadb3-storage**).  

4. Hozzon létre három hálózati adaptert, egyet `hana` minden virtuális géphez a virtuális hálózati alhálózathoz (ebben a példában **hanadb1-hana**, **hanadb2-hana**és **hanadb3-hana**).  

5. Csatlakoztassa az újonnan létrehozott virtuális hálózati adaptereket a megfelelő virtuális gépekhez az alábbi lépésekkel:  

    a. Nyissa meg a virtuális gépet az [Azure Portalon.](https://portal.azure.com/#home)  

    b. A bal oldali ablaktáblában válassza a **Virtuális gépek**lehetőséget. Szűrje a virtuális gép nevét (például **hanadb1**), majd válassza ki a virtuális gépet.  

    c. Az **Áttekintő** ablaktáblán válassza a **Leállítás** lehetőséget a virtuális gép felszabadításához.  

    d. Válassza **a Hálózat lehetőséget,** majd csatolja a hálózati adaptert. A **Hálózati adapter csatolása** legördülő listában jelölje ki a `storage` `hana` már létrehozott hálózati csatolókat a és az alhálózatokhoz.  
    
    e. Kattintson a **Mentés** gombra. 
 
    f. Ismételje meg a b lépéslépéseit a fennmaradó virtuális gépek (példánkban **hanadb2** és **hanadb3**).
 
    g. Hagyja a virtuális gépeket leállított állapotban. Ezután lehetővé tesszük a [gyorsított hálózatkezelést](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) az összes újonnan csatlakoztatott hálózati csatolószámára.  

6. A következő lépésekkel engedélyezze a `storage` gyorsított `hana` hálózatkezelést a további hálózati adapterek és alhálózatok számára:  

    a. Nyissa meg az [Azure Cloud Shellt](https://azure.microsoft.com/features/cloud-shell/) az [Azure Portalon.](https://portal.azure.com/#home)  

    b. Hajtsa végre a következő parancsokat, hogy lehetővé tegye `storage` a `hana` gyorsított hálózatkezelést a további hálózati adapterek számára, amelyek a és az alhálózatokhoz vannak csatolva.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Indítsa el a virtuális gépeket az alábbi lépésekkel:  

    a. A bal oldali ablaktáblában válassza a **Virtuális gépek**lehetőséget. Szűrje a virtuális gép nevét (például **hanadb1**), majd jelölje ki.  

    b. Az **Áttekintő** ablaktáblán válassza a **Start**gombot.  

## <a name="operating-system-configuration-and-preparation"></a>Az operációs rendszer konfigurációja és előkészítése

A következő szakaszokban található utasítások a következők egyikével vannak ellátva:
* **[A]**: Minden csomópontra alkalmazható
* **[1]**: Csak az 1.
* **[2]**: Csak a 2- es csomópontra alkalmazható
* **[3]**: Csak a 3- as csomópontra alkalmazható

Konfigurálja és készítse elő az operációs rendszert az alábbi lépésekkel:

1. **[A]** A gazdagépfájlok karbantartása a virtuális gépeken. Az összes alhálózat tételeinek belefoglalása. Ebben a példában `/etc/hosts` a következő bejegyzések kerültek be.  

    <pre><code>
    # Storage
    10.9.3.4   hanadb1-storage
    10.9.3.5   hanadb2-storage
    10.9.3.6   hanadb3-storage
    # Client
    10.9.1.5   hanadb1
    10.9.1.6   hanadb2
    10.9.1.7   hanadb3
    # Hana
    10.9.2.4   hanadb1-hana
    10.9.2.5   hanadb2-hana
    10.9.2.6   hanadb3-hana
    </code></pre>

3. **[A]** Adjon hozzá egy hálózati útvonalat, hogy az Azure NetApp Files-hoz való kommunikáció a tárolóhálózati felületen keresztül kerülhessen.  

   Ebben a példában a további hálózati útvonal konfigurálására fog ják `Networkmanager` használni. A következő utasítások feltételezik, `eth1`hogy a tárolóhálózati kapcsolat a .  
   Először határozza meg az `eth1`eszköz kapcsolatnevét . Ebben a példában az `eth1` `Wired connection 1`eszköz kapcsolatneve .  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   Ezután konfiguráljon további útvonalat az Azure `eth1`NetApp Files delegált hálózatához a segítségével.  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    A módosítások aktiválásához indítsa újra a virtuális gépet.  

3. **[A]** Telepítse az NFS-ügyfélcsomagot.  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **[A]** Készítse elő az operációs rendszert az SAP HANA futtatására az Sap NetApp-on NFS-sel, ahogy azt a [NetApp SAP-alkalmazások a Microsoft Azure-ban az Azure NetApp-fájlok használatával][anf-sap-applications-azure]ismertetik. Hozzon létre konfigurációs fájlt */etc/sysctl.d/netapp-hana.conf* a NetApp konfigurációs beállításaihoz.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000 net.ipv4.tcp_slow_start_after_idle=0 net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** Hozzon létre konfigurációs fájlt */etc/sysctl.d/ms-az.conf* további optimalizálási beállításokkal.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

5. **[A]** Állítsa be a sunrpc beállításokat, ahogy azt a NetApp SAP alkalmazások a [Microsoft Azure-ban az Azure NetApp Files használatával][anf-sap-applications-azure].  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **[A]** Red Hat hana konfigurációhoz.

    Konfigurálja az RHEL-t az SAP [2292690], [2455582] <https://access.redhat.com/solutions/2447641>, [2593824] és .

    > [!NOTE]
    > Ha telepíti a HANA 2.0 SP04-et, az SAP HANA telepítése előtt telepítenie kell a csomagot `compat-sap-c++-7` az SAP [2593824]megjegyzésében leírtak szerint. 

## <a name="mount-the-azure-netapp-files-volumes"></a>Az Azure NetApp-fájlok köteteinek csatlakoztatása

1. **[A]** Hozzon létre csatlakoztatási pontokat a HANA adatbáziskötetekhez.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** Hozzon létre csomópont-specifikus könyvtárakat a /usr/sap könyvtárhoz a HN1 -shared.[1] Create node-specific directories for /usr/sap on **HN1**-shared.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** Ellenőrizze az NFS tartomány beállítását. Győződjön meg arról, hogy a tartomány alapértelmezett Azure NetApp Files tartományként van konfigurálva, azaz **`defaultv4iddomain.com`** a leképezés **senkire**van állítva.  

    > [!IMPORTANT]
    > Győződjön meg arról, hogy `/etc/idmapd.conf` a virtuális gép NFS-tartományát úgy **`defaultv4iddomain.com`** állítja be, hogy megfeleljen az Azure NetApp-fájlok alapértelmezett tartománykonfigurációjának: . Ha eltérés van az NFS-ügyfél (azaz a virtuális gép) és az NFS-kiszolgáló tartománykonfigurációja, azaz az Azure NetApp-konfiguráció között, akkor a virtuális gépekre csatlakoztatott `nobody`Azure NetApp-kötetek fájljaira vonatkozó engedélyek a következőképpen jelennek meg: .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`Ellenőrizze. Meg kell állítani, hogy **Y**. A címtárstruktúra `nfs4_disable_idmapping` létrehozásához hajtsa végre a csatlakoztatási parancsot. A könyvtárat nem lehet manuálisan létrehozni a /sys/modules kapcsoló alatt, mert a hozzáférés a kernel / illesztőprogramok számára van fenntartva.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   A paraméter módosításáról `nfs4_disable_idmapping` további https://access.redhat.com/solutions/1749883részleteket a talál.

6. **[A]** Csatlakoztassa a megosztott Azure NetApp-fájlok köteteit.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** Csatlakoztassa a csomópontspecifikus köteteket a **hanadb1-re.**  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** Csatlakoztassa a csomópontspecifikus köteteket a **hanadb2-re.**  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** Szerelje fel a csomópontspecifikus köteteket a **hanadb3-ra.**  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Ellenőrizze, hogy az összes HANA kötet NFS protokollnal van-e csatlakoztatva **az NFSv4 protokollverzióval.**  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/data/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /usr/sap/<b>HN1</b> from 10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/shared from 10.9.0.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    </code></pre>

## <a name="installation"></a>Telepítés  

Ebben a példában az SAP HANA üzembe helyezéséhez horizontális felskálázási konfiguráció készenléti csomópont az Azure-ral, a HANA 2.0 SP4 használtuk.  

### <a name="prepare-for-hana-installation"></a>Felkészülés a HANA telepítésére

1. **[A]** A HANA telepítése előtt állítsa be a gyökérjelszót. A telepítés befejezése után letilthatja a gyökérjelszót. Végrehajtás `root` parancsként `passwd`.  

2. **[1]** Ellenőrizze, hogy az SSH-on keresztül be tud-e jelentkezni **a hanadb2** és **a hanadb3 fájlba**anélkül, hogy jelszót kérne.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** Telepítsen további csomagokat, amelyek szükségesek a HANA 2.0 SP4.[A] További csomagok telepítése, amelyek szükségesek a HANA 2.0 SP4-hez. További információ: SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **[2], [3]** Módosítsa az SAP `data` HANA és `log` a könyvtárak tulajdonjogát **hn1**adm-re.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **[A]** Ideiglenesen tiltsa le a tűzfalat, hogy az ne zavarja a HANA telepítését. A HANA telepítése után újra engedélyezheti. 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>HANA telepítés

1. **[1]** Telepítse az SAP HANA telepítéséhez [és frissítéséhez vezető útmutatóutasításait](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)követve. Ebben a példában telepítjük az SAP HANA horizontális felskálázás a fő, egy feldolgozó és egy készenléti csomópont.  

   a. Indítsa el a **hdblcm** programot a HANA telepítőszoftver könyvtárából. Használja `internal_network` a paramétert, és adja át az alhálózat címterét, amely a belső HANA csomópontközi kommunikációhoz használatos.  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b. A parancssorba írja be a következő értékeket:

     * A **Művelet kiválasztása**mezőbe írja be az **1** értéket (telepítéshez)
     * **További alkatrészek telepítéséhez:** írja be a **2, 3**
     * Telepítési útvonal esetén: nyomja meg az Enter billentyűt (alapértelmezés szerint /hana/shared)
     * Helyi **állomásnév**esetén : az Alapértelmezett érték elfogadásához nyomja le az Enter billentyűt.
     * A **Szeretne állomásokat hozzáadni a rendszerhez?**: írja be **az y**
     * A **vesszővel tagolt állomásnevek hozzáadásához**adja meg a **hanadb2, hanadb3 értéket.**
     * **Gyökérfelhasználóneve** [root]: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához
     * A gazdagép hanadb2 szerepköreihez adja meg az **1** értéket (dolgozóesetén)
     * A host hanadb2 [default]állomás **állomásfeladat-átvételi csoportjának** esetén: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához.
     * Tárolópartíció **száma** a fogadó hanadb2 [<<assign automatically>>]: nyomja le az Enter billentyűt az alapértelmezett
     * A **worker csoport** a fogadó hanadb2 [alapértelmezett]: nyomja meg az Enter, hogy elfogadja az alapértelmezett
     * A **Select szerepek** a fogadó hanadb3: írja be **2** (a készenléti)
     * A host hanadb3 [default]állomás **állomásfeladat-átvételi csoportjának** esetén: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához.
     * A **worker csoport** a fogadó hanadb3 [alapértelmezett]: nyomja meg az Enter, hogy elfogadja az alapértelmezett
     * **SAP HANA rendszerazonosító**esetén: írja be a **HN1 értéket**
     * **A(z)** [00] példányszám esetén: írja be a **03** értéket
     * Helyi **állomásfeldolgozó-csoport** [alapértelmezett]: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához.
     * A **Rendszerhasználat kiválasztása / Enter index [4]**: írja be a **4** értéket (egyéni)
     * Az **adatkötetek helyadatainak beállítása esetén** [/hana/data/HN1]: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához.
     * A **naplókötetek helyének** [/hana/log/HN1] esetén: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához.
     * A **Maximális memóriafoglalás korlátozása?** [n]: írja be **az n**
     * A **certificate host name for Host hanadb1** [hanadb1]: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához
     * A **certificate host name for Host hanadb2** [hanadb2]: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához
     * A **certificate host name for Host hanadb3** [hanadb3]: nyomja le az Enter billentyűt az alapértelmezett érték elfogadásához
     * **Rendszergazda (hn1adm) Jelszó :** adja meg a jelszót
     * **Rendszeradatbázis-felhasználó (rendszer) jelszó**esetén: adja meg a rendszer jelszavát
     * A **Rendszeradatbázis felhasználójának (rendszer) jelszó**hoz: adja meg a rendszer jelszavát
     * Újraindítás **a rendszer újraindítása után a gép újraindítása?** [n]: írja be **az n** 
     * Mert **szeretné folytatni (y/n)**: érvényesítse az összefoglalót, és ha minden jól néz ki, **y**


2. **[1]** A global.ini ellenőrzése  

   Display global.ini, és győződjön meg arról, hogy a konfiguráció a belső SAP HANA csomópontközi kommunikáció a helyén van. Ellenőrizze a **kommunikációs** szakaszt. Rendelkeznie kell az `hana` alhálózat címterével, és `.internal` `listeninterface` a . Ellenőrizze a **internal_hostname_resolution** szakaszt. Rendelkeznie kell az alhálózathoz `hana` tartozó HANA virtuális gépek IP-címével.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.9.2.0/26</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.9.2.4</b> = <b>hanadb1</b>
    <b>10.9.2.5</b> = <b>hanadb2</b>
    <b>10.9.2.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** Állomásleképezés hozzáadása annak biztosításához, hogy az ügyfél IP-címeit az ügyfélkommunikációhoz használják. Adja `public_host_resolution`hozzá a szakaszt, és adja hozzá a megfelelő IP-címeket az ügyfél alhálózatából.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **[1]** Indítsa újra az SAP HANA-t a módosítások aktiválásához.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Ellenőrizze, hogy az ügyfélkapcsolat az `client` alhálózat IP-címeit fogja-e használni a kommunikációhoz.  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   A konfiguráció ellenőrzéséről az SAP [2183363 - Az SAP HANA belső hálózatának konfigurációja](https://launchpad.support.sap.com/#/notes/2183363)című témakörben talál további információt.  

5. **[A]** A tűzfal újbóli engedélyezése.  
   - HANA leállítása
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
       </code></pre>
   - A tűzfal újbóli engedélyezése
       <pre><code>
        # Execute as root
        systemctl start firewalld
        systemctl enable firewalld
       </code></pre>

   - A szükséges tűzfalportok megnyitása

       > [!IMPORTANT]
       > Tűzfalszabályok létrehozása a HANA inter node kommunikáció és az ügyfélforgalom engedélyezéséhez. A szükséges portok [az összes SAP-termék TCP/IP portjain](https://help.sap.com/viewer/ports)találhatók. A következő parancsok csak egy példa. Ebben a forgatókönyvben a használt rendszer szám 03.

       <pre><code>
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       </code></pre>

   - Hana indítása
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

6. Az SAP HANA optimalizálásához az alapul szolgáló Azure NetApp-fájlok tárolóhoz állítsa be a következő SAP HANA paramétereket:

   - `max_parallel_io_requests`**128**
   - `async_read_submit`**bekapcsolva**
   - `async_write_submit_active`**bekapcsolva**
   - `async_write_submit_blocks`**minden**

   További információ: [NetApp SAP Applications on Microsoft Azure using Azure using Azure.][anf-sap-applications-azure] 

   Az SAP HANA 2.0 rendszerekkel kezdve `global.ini`a paramétereket a rendszerben állíthatja be. További információ: SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   Az SAP HANA 1.0 sPS12-es és korábbi verzióihoz ezek a paraméterek a telepítés során állíthatók be, az SAP [2267798](https://launchpad.support.sap.com/#/notes/2267798)megjegyzésében leírtak szerint.  

7. Az Azure NetApp Files által használt tároló fájlméret-korlátozása 16 terabájt (TB). Az SAP HANA nem ismeri implicit módon a tárolási korlátozást, és nem hoz létre automatikusan egy új adatfájlt, amikor eléri a 16 TB-os fájlméretkorlátot. Mivel az SAP HANA megpróbálja növelni a fájlt 16 TB-on túl, ez a kísérlet hibákat eredményez, és végül egy indexkiszolgáló összeomlását. 

   > [!IMPORTANT]
   > Ha meg szeretné akadályozni, hogy az SAP HANA megpróbálja az adatfájlokat a tárolóalrendszer `global.ini` [16 TB-os határán](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) túlra növelni, állítsa be a következő paramétereket a alkalmazásban.  
   > - datavolume_striping = igaz
   > - datavolume_striping_size_gb = 15000 További információ: SAP Note [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Ne feledje, az SAP Megjegyzés [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Sap HANA feladatátvétel tesztelése 

1. Csomópont-összeomlás szimulálása egy SAP HANA munkavégző csomóponton. Tegye a következőket: 

   a. A csomópont-összeomlás szimulálása előtt futtassa a következő parancsokat **hn1**adm néven a környezet állapotának rögzítéséhez:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/HN1/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |

    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. Csomópont-összeomlás szimulálásához futtassa a következő parancsot gyökérként a munkavégző csomóponton, amely ebben az esetben **hanadb2:**  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. A feladatátvétel befejezésének figyelése. A feladatátvétel befejezése után rögzítse az állapotot, amely a következőnek fog adható meg:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Kernel pánik esetén kerülje el a késéseket az SAP `kernel.panic` HANA feladatátvételsel, ha 20 másodpercre állítja az *összes* HANA virtuális gépen. A konfiguráció a. `/etc/sysctl` A módosítás aktiválásához indítsa újra a virtuális gépeket. Ha ez a módosítás nem történik meg, a feladatátvétel 10 vagy több percet is igénybe vehet, ha egy csomópont kernel pánikot tapasztal.  

2. A névkiszolgáló takarása a következőkkel:

   a. A vizsgálat előtt ellenőrizze a környezet állapotát a következő parancsok **hn1**adm-ként való futtatásával:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
   </code></pre>

   b. Futtassa a következő parancsokat **hn1**adm néven az aktív főcsomóponton, amely ebben az esetben **hanadb1:**  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    A **hanadb3** készenléti csomópont veszi át a fő csomópontot. Itt van az erőforrás állapota a feladatátvételi teszt befejezése után:  

    <pre><code>
     # Check the instance status
     sapcontrol -nr 03  -function GetSystemInstanceList
     GetSystemInstanceList
     OK
     hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
     hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
     hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
     hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
     # Check the landscape status
     python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
     | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
     |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
     |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
     | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
     | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
     | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
     | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. Indítsa újra a HANA-példányt a **hanadb1-en** (azaz ugyanazon a virtuális gépen, ahol a névkiszolgáló meghalt). A **hanadb1** csomópont újra csatlakozik a környezethez, és megtartja készenléti szerepkörét.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Miután az SAP HANA elindult a **hanadb1-en,** a következő állapotra számíthat:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Ismét, öld meg a név szerver az aktuálisan aktív fő csomópont (azaz a **csomópont hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   A **csomópont hanadb1** folytatja a fő csomópont szerepét. A feladatátvételi teszt befejezése után az állapot a következőkre fog kinézni:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. Indítsa el az SAP HANA-t a **hanadb3-on,** amely készen áll a készenléti csomópontként való használatra.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Miután az SAP HANA elindult a **hanadb3-on,** az állapot a következőképpen néz ki:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>További lépések

* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz][dbms-guide]
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és tervezze meg az SAP HANA vészutáni helyreállítását az Azure virtuális gépeken, olvassa el [az SAP HANA magas rendelkezésre állását az Azure virtuális gépeken (VM) című témakört.][sap-hana-ha]
