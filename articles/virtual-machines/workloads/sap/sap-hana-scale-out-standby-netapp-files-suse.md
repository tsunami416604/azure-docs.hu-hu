---
title: SAP HANA az Azure-beli virtuális gépek készenléti csomópontjaival, a SUSE Linux Enterprise Server-on Azure NetApp Files Microsoft Docs
description: Magas rendelkezésre állási útmutató az SAP NetWeaver-hez a SUSE Linux Enterprise Server Azure NetApp Files SAP-alkalmazásokhoz
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
ms.date: 04/30/2019
ms.author: radeltch
ms.openlocfilehash: 76369c1a4beb792de03cf0ccae5c86825812f103
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934173"
---
# <a name="sap-hana-scale-out-with-standby-node-on-azure-vms-with-azure-netapp-files-on-suse-linux-enterprise-server"></a>SAP HANA az Azure-beli virtuális gépek készenléti csomópontjaival, a Azure NetApp Files a SUSE Linux Enterprise Server 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]: https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Ez a cikk azt ismerteti, hogyan helyezhető üzembe az Azure Virtual Machines szolgáltatásban, és [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) a megosztott tároló kötetei számára a rendelkezésre állású HANA rendszer a kibővített konfigurációban.  
A példában a konfigurációk, a telepítési parancsok és így tovább, a HANA-példány **03** , a HANA rendszer azonosítója pedig **HN1**. A példák a HANA 2,0 SP4-es és az SAP 12 SP4-es SUSE Linux Enterprise Serveron alapulnak. 

Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat:

* [Azure NetApp Files dokumentáció][anf-azure-doc] 
* SAP-Megjegyzés [1928533], amely a következőket tartalmazta:  
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája
  * Fontos kapacitási információk Azure-beli virtuális gépek méreteihez
  * Támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk
  * A Windows és a Linux rendszerhez szükséges SAP kernel verziója Microsoft Azure
* Az SAP Note [2015553] az SAP által támogatott SAP-szoftverek Azure-beli üzembe helyezésének előfeltételeit sorolja fel.
* Az SAP Megjegyzés [2205917] ajánlott operációsrendszer-beállításokkal SUSE Linux Enterprise Server SAP-alkalmazásokhoz
* SAP-Megjegyzés a [1944799] SAP-irányelvek az SAP-alkalmazások SUSE Linux Enterprise Server
* Az [2178632] -es SAP-Megjegyzés részletes információkat tartalmaz az Azure-beli SAP-ban jelentett összes figyelési mérőszámról.
* A [2191498] -es SAP-Megjegyzés a szükséges SAP-gazdagép ügynökének verziója az Azure-ban linuxos.
* Az [2243692] -es SAP-Megjegyzés az Azure-beli Linuxon futó SAP-licenceléssel kapcsolatos információkat tartalmaz.
* Az [1984787] -es SAP-Megjegyzés általános információkat tartalmaz a SUSE Linux Enterprise Server 12.
* Az SAP Megjegyzés [1999351] további hibaelhárítási információkat tartalmaz az SAP-hez készült Azure Enhanced monitoring bővítménnyel kapcsolatban.
* Az [1900823] -es SAP-Megjegyzés SAP HANA tárolási követelményekkel kapcsolatos információkat tartalmaz
* Az [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik minden szükséges SAP-megjegyzéssel a Linux rendszerhez.
* [Azure Virtual Machines tervezése és implementálása Linux rendszeren az SAP-ban][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-hez Linux rendszeren][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hez Linux rendszeren][dbms-guide]
* [SUSE SAP ha ajánlott eljárási útmutatók][suse-ha-guide] Az útmutatók az összes szükséges információt tartalmazzák, amelyekkel beállítható a NetWeaver HA és SAP HANA rendszer-replikáció a helyszínen. Ezeket az útmutatókat általános alaptervként használhatja. Sokkal részletesebb információkat biztosítanak.
* [SUSE magas rendelkezésre állású bővítmény – 12 SP3 kibocsátási megjegyzések][suse-ha-12sp3-relnotes]
* [NetApp SAP-alkalmazások Microsoft Azure a Azure NetApp Files használatával][anf-sap-applications-azure]
* [SAP HANA a NetApp-rendszereken az NFS](https://www.netapp.com/us/media/tr-4435.pdf)-sel. A konfigurációs útmutató a Azure NetApp Files által biztosított NFS-SAP HANA beállításával kapcsolatos információkat tartalmaz.


## <a name="overview"></a>Áttekintés

A HANA magas rendelkezésre állás elérésének egyik módja a gazdagép automatikus feladatátvétele. A gazdagép automatikus feladatátvételének konfigurálásához egy vagy több virtuális gép kerül a HANA rendszerbe, és úgy van beállítva, hogy készenléti csomópontként legyen konfigurálva. Ha az aktív csomópont meghibásodik, a rendszer automatikusan átveszi a készenléti csomópontot. A bemutatott konfigurációban az Azure Virtual Machines szolgáltatásban, amelyet az [NFS Azure NetApp Fileson](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)végez.  

A készenléti csomópontnak hozzá kell férnie az összes adatbázis-kötethez. A HANA-köteteket Nfsv4 névleképezője-kötetként kell csatlakoztatni. A Nfsv4 névleképezője protokoll továbbfejlesztett file Lease-alapú zárolási mechanizmusa `I/O` kerítéshez használatos. 

> [!IMPORTANT]
> A HANA-adatmennyiség és a naplózási kötetek NFSv 4.1-es kötetekként való üzembe helyezése és csatlakoztatása kötelező a NFSv 4.1 protokoll használatával, hogy a konfiguráció támogatott legyen. A HANA-gazdagép automatikus feladatátvételi konfigurációja készenléti csomóponttal nem támogatott a NFSv3.

![SAP NetWeaver – magas rendelkezésre állás – áttekintés](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

A SAP HANA hálózati javaslatok alapján három alhálózat jött létre egy Azure virtuális hálózaton belül: a Storage rendszerrel folytatott kommunikációhoz, a belső HANA-csomópontok közötti kommunikációhoz és az ügyfél-kommunikációhoz. Az Azure NetApp-kötetek külön alhálózatban találhatók, [Azure NetApp Files delegálva](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

Ebben a példában az alhálózatok a következők:  

  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `client` 10.23.0.0/24  
  - `anf` 10.23.1.0/26  

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>A Azure NetApp Files-infrastruktúra beállítása 

Mielőtt folytatná az Azure NetApp-fájlok infrastruktúrájának telepítését, ismerkedjen meg az [Azure NetApp Files dokumentációval][anf-azure-doc]. Az Azure NetApp-fájlok több [Azure-régióban](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)is elérhetők. Ellenőrizze, hogy a kiválasztott Azure-régió kínál-e Azure NetApp Files.  

A következő hivatkozás az Azure-régió Azure NetApp Files rendelkezésre állását mutatja: az [Azure-régió Azure NetApp Files rendelkezésre állása][anf-avail-matrix].  
Azure NetApp Files üzembe helyezése előtt a bevezetést a Azure NetApp Filesra kell kérnie, az [Azure NetApp-fájlok regisztrálásával kapcsolatos utasításokat][anf-register]követve. 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files erőforrások üzembe helyezése  

A következő lépések feltételezik, hogy már telepítette az [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)-t. A Azure NetApp Files erőforrásokat és a virtuális gépeket, ahol a Azure NetApp Files erőforrásokat csatlakoztatni kell ugyanazon az Azure-Virtual Network vagy az Azure-beli virtuális hálózatokban.  

1. Ha eddig még nem tette meg, kérje [a Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)bevezetését.  

2. Hozza létre a NetApp-fiókot a kiválasztott Azure-régióban, és kövesse a [NetApp-fiók létrehozásához szükséges utasításokat](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  

3. Azure NetApp Files kapacitás-készlet beállítása a [Azure NetApp Files kapacitás készletének beállításához szükséges útmutatás alapján](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
A cikkben bemutatott HANA-architektúra egy Azure NetApp Files kapacitás-készletet használ, amely Ultra szolgáltatási szintet jelent. Javasoljuk, hogy az Azure-beli HANA-munkaterhelések esetében Azure NetApp Files Ultra vagy prémium [szintű szolgáltatási szintet](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) .  

4. Az alhálózat delegálása az Azure NetApp-fájlokba az [alhálózat delegálása Azure NetApp Filesra](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)című részben leírtak szerint.  

5. Azure NetApp Files kötetek telepítéséhez kövesse az [utasításokat, amelyekkel Azure NetApp Files kötetet hozhat létre](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  A kötetek telepítésekor ügyeljen arra, hogy a **nfsv 4.1** verziót válassza. Jelenleg a NFSv 4.1-hez való hozzáférés további engedélyezési lehetőséget igényel. Telepítse a köteteket a kijelölt Azure NetApp Files [alhálózatban](https://docs.microsoft.com/rest/api/virtualnetwork/subnets). Ne feledje, hogy a Azure NetApp Files erőforrásoknak és az Azure-beli virtuális gépeknek ugyanabban az Azure-Virtual Networkban vagy az Azure-beli virtuális hálózatokban kell lenniük. Például <b>HN1</b>-Mnt00001, <b>HN1</b>-log-mnt00001, stb. a kötetek nevei és NFS://10.23.1.5/<b>HN1</b>-mnt00001, NFS://10.23.1.4/<b>HN1</b>-log-mnt00001 stb. a fájlok elérési útja a Azure NetApp Files kötetek számára.  

   1. Volume <b>HN1</b>-mnt00001 (NFS://10.23.1.5/<b>HN1</b>-adatforgalom-mnt00001)
   2. Volume <b>HN1</b>-mnt00002 (NFS://10.23.1.6/<b>HN1</b>-adatforgalom-mnt00002)
   3. kötet <b>HN1</b>-log-mnt00001 (NFS://10.23.1.4/<b>HN1</b>-log-mnt00001)
   4. kötet <b>HN1</b>-log-mnt00002 (NFS://10.23.1.6/<b>HN1</b>-log-mnt00002)
   5. mennyiségi <b>HN1</b>– megosztott (NFS://10.23.1.4/<b>HN1</b>-Shared)
   
   Ebben a példában a HANA-adatforgalom és a naplózási kötetek külön Azure NetApp Files használták. A kisebb vagy nem produktív rendszereken a költségesebb konfiguráció érdekében minden adatcsatolást és naplót egyetlen köteten helyezhet el.  

### <a name="important-considerations"></a>Fontos szempontok

Az SAP NetWeaver SUSE magas rendelkezésre állású architektúrán való Azure NetApp Filesának megfontolásakor vegye figyelembe a következő fontos szempontokat:

- A minimális kapacitási készlet 4 TiB.  
- A minimális kötet mérete 100 GiB
- Azure NetApp Files és az összes olyan virtuális gép, amelyben Azure NetApp Files köteteket csatlakoztatni kell, ugyanabban az Azure-Virtual Network vagy egymással azonos régióban lévő [virtuális hálózatokban](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) kell lennie.  
- A kiválasztott virtuális hálózatnak rendelkeznie kell egy, a Azure NetApp Files delegált alhálózattal.
- Az Azure NetApp-kötetek átviteli sebessége a mennyiségi kvóta és a szolgáltatási szint függvénye, a [Azure NetApp Files szolgáltatási szintjén](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)dokumentálva. A HANA Azure NetApp-kötetek méretezése esetén győződjön meg arról, hogy az eredményül kapott átviteli sebesség megfelel a HANA rendszerkövetelményeinek.  
- Azure NetApp Files az [exportálási szabályzatot](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): szabályozhatja az engedélyezett ügyfeleket, a hozzáférési típust (olvasási & írás, csak olvasható stb.). 
- Azure NetApp Files a szolgáltatás még nem ismeri a zónát. Jelenleg Azure NetApp Files funkció nincs telepítve az Azure-régió összes rendelkezésre állási zónájában. Vegye figyelembe, hogy egyes Azure-régiókban lehetséges a késés következményei.  
- Fontos, hogy az Azure NetApp-tároló közelében üzembe helyezett virtuális gépek alacsony késéssel rendelkezzenek. Az alacsony késésű SAP HANA munkaterhelések esetében kritikus fontosságú. A Microsoft képviselőjével együttműködve biztosíthatja, hogy a virtuális gépek és a Azure NetApp Files kötetek központi telepítése közel legyen.  
- A <b>SID</b>adm felhasználói azonosítójának és a virtuális gépek `sapsys`jának azonosítójának meg kell egyeznie Azure NetApp Files-konfigurációval. 

> [!IMPORTANT]
> Az alacsony késésű SAP HANA munkaterhelések esetében kritikus fontosságú. A Microsoft képviselőjével együttműködve biztosíthatja, hogy a virtuális gépek és a Azure NetApp Files kötetek központi telepítése közel legyen.  

> [!IMPORTANT]
> Ha eltérés van a <b>SID</b>adm felhasználói azonosítójának és a virtuális gép és az Azure NetApp-konfiguráció közötti `sapsys` csoportazonosító között, akkor az Azure NetApp-köteteken található, a virtuális gépekhez csatlakoztatott fájlok engedélyei `nobody`ként jelennek meg. Ügyeljen arra, hogy a <b>SID</b>adm helyes felhasználói azonosítóját és a `sapsys` csoport azonosítóját határozza meg, amikor [egy új rendszer](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) bekerül a Azure NetApp Filesba.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>HANA-adatbázis méretezése Azure NetApp Files

Az Azure NetApp-kötetek átviteli sebessége a kötet méretének és a szolgáltatási szintnek a függvénye, amely a [Azure NetApp Files szolgáltatási szintjén](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)van dokumentálva. 

Az Azure-beli SAP-infrastruktúra megtervezése során tisztában kell lennie az SAP által meghatározott minimális tárolási követelményekkel, amelyek a minimális átviteli sebességre fordíthatók le:

- Írási/olvasási műveletek engedélyezése 250 MB/s/Hana/log 1 MB I/O-mérettel  
- Legalább 400 MB/s olvasási tevékenység engedélyezése a/Hana/Data 16 MB és 64 MB I/O méret esetén  
- Az írási tevékenység engedélyezése legalább 250 MB/s/Hana/Data 16 MB és 64 MB I/O méret esetén  

A [Azure NetApp Files átviteli sebességre vonatkozó határértékek](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) 1 TiB-ra vetítve:
- Premium Storage szintű 64 MiB/s  
- Ultra Storage-réteg – 128 MiB/s  

Az adatokhoz és a naplóhoz tartozó minimális teljesítménybeli követelmények teljesítéséhez, valamint a `/hana/shared` iránymutatásainak megfelelően az ajánlott méretek a következőképpen néznek ki:

| Kötet | Méret<br /> Premium Storagei szintű | Méret<br /> Ultra Storage-rétegek | Támogatott NFS-protokoll |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /hana/shared | Max (512 GB, 1xRAM)/4 feldolgozó csomópont | Max (512 GB, 1xRAM)/4 feldolgozó csomópont | v3 vagy v 4.1 |

A cikkben bemutatott elrendezés SAP HANA konfigurációja a Azure NetApp Files Ultra Storage-rétegek használatával a következőképpen néz ki:

| Kötet | Méret<br /> Ultra Storage-rétegek | Támogatott NFS-protokoll |
| --- | --- |
| /hana/log/mnt00001 | 2 TiB | v 4.1 |
| /hana/log/mnt00002 | 2 TiB | v 4.1 |
| /hana/data/mnt00001 | 3,2 TiB | v 4.1 |
| /hana/data/mnt00002 | 3,2 TiB | v 4.1 |
| /hana/shared | 2 TiB | v3 vagy v 4.1 |

> [!NOTE]
> Az itt ismertetett Azure NetApp Files méretezési javaslatok célja, hogy megfeleljenek az SAP expresss infrastruktúra-szolgáltatók felé irányuló minimális követelményeknek. A valós ügyfelek központi telepítései és munkaterhelési forgatókönyvek esetében nem lehet elég. Ezeket a javaslatokat kiindulási pontként és alkalmazkodva használhatja az adott számítási feladatra vonatkozó követelmények alapján.  

> [!TIP]
> Azure NetApp Files kötetek átméretezése dinamikusan elvégezhető, anélkül, hogy le kellene `unmount` a köteteket, le kell állítania a virtuális gépeket, vagy le kell állítania SAP HANA. Ez lehetővé teszi a rugalmasságot, hogy megfeleljen az alkalmazás elvárásainak és a várhatóan nem várt átviteli igényeknek.

## <a name="deploy-linux-virtual-machines-via-azure-portal"></a>Linux rendszerű virtuális gépek üzembe helyezése Azure Portal használatával

Először létre kell hoznia a Azure NetApp Files köteteket. Hozza létre az Azure-beli [virtuális hálózati alhálózatokat](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) az [Azure Virtual Networkban](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Telepítse a virtuális gépeket. Hozza létre a további hálózati adaptereket, és csatlakoztassa a hálózati adaptereket a megfelelő virtuális gépekhez. Minden virtuális gépnek három hálózati adaptere van, amelyek megfelelnek a három Azure-beli virtuális hálózat alhálózatának (`storage`, `hana` és `client`).  Lásd: [Linux rendszerű virtuális gép létrehozása az Azure-ban több hálózati kártyával](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics).  

> [!IMPORTANT]
> Az alacsony késésű SAP HANA munkaterhelések esetében kritikus fontosságú. A Microsoft képviselőjével együttműködve gondoskodhat arról, hogy a virtuális gépek és a Azure NetApp Files kötetek központi telepítése a kis késleltetés elérése érdekében közel legyen. Adja meg a szükséges információkat az [új SAP HANA rendszer](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)beküldésekor, amely SAP HANA Azure NetApp Files használ.  
> 
A következő lépések azt feltételezik, hogy már létrehozta az erőforráscsoportot, az Azure-beli virtuális hálózatot és a három Azure Virtual Network alhálózatot: `storage`, `hana` és `client`.  A virtuális gépek telepítésekor válassza ki a tárolási alhálózatot, hogy a tárolási hálózati adapter a virtuális gépek elsődleges felülete legyen. Ha ez nem lehetséges, az Azure NetApp által delegált alhálózatra irányuló explicit útvonalat be kell állítani a Storage alhálózati átjárón keresztül. 

> [!IMPORTANT]
> Győződjön meg arról, hogy a kiválasztott operációs rendszer SAP-tanúsítvánnyal rendelkezik az adott virtuálisgép-típusok SAP HANAához. A SAP HANA Certified VM-típusok és operációsrendszer-kiadások listája [SAP HANA tanúsított IaaS platformokon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)kereshető. Ügyeljen rá, hogy a felsorolt virtuálisgép-típusok részletes listájának lekérése SAP HANA támogatott operációsrendszer-kiadások teljes listáját adja meg az adott virtuálisgép-típushoz.  

1. Hozzon létre egy rendelkezésre állási készletet a SAP HANAhoz. Győződjön meg arról, hogy a frissítési tartomány maximális értékre van állítva.  

2. Három Virtual Machines létrehozása (**hanadb1**, **hanadb2**, **hanadb3**)  
   - A SAP HANA által támogatott Azure Galleryben SLES4SAP-rendszerképet használhat. Ebben a példában a SLES4SAP 12 SP4-es rendszerképet használtuk.  
   - Válassza ki SAP HANA korábban létrehozott rendelkezésre állási készletet.  
   - Válassza ki a Storage Azure virtuális hálózati alhálózatot. Válassza a [gyorsított hálózat](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)lehetőséget.  
A virtuális gépek központi telepítésekor a rendszer automatikusan létrehozza a hálózati adapter nevét. A hálózati adaptereket a Storage Azure Virtual Network alhálózathoz csatolt **hanadb1-tárolóként**, **hanadb2**és **hanadb3-** tárolóként fogjuk megtekinteni. 
3. Hozzon létre három hálózati adaptert – egyet az egyes virtuális gépekhez a `hana` virtuális hálózat alhálózatához. Ebben a példában: **hanadb1-Hana**, **hanadb2-Hana**és **hanadb3-Hana**.  
4. Hozzon létre három hálózati adaptert – egyet az egyes virtuális gépekhez az **ügyfél** virtuális hálózati alhálózata számára. Ebben a példában a **hanadb1-Client**, a **hanadb2-Client**és a **hanadb3-Client**.  
5. Az újonnan létrehozott virtuális hálózati adapterek csatlakoztatása a megfelelő virtuális gépekhez  

    1. Navigáljon a [Azure Portal](https://portal.azure.com/#home)virtuális géphez.  
    2. A bal oldali navigációs ablaktáblán válassza a Virtual Machines lehetőséget. Szűrje a virtuális gép nevét, például **hanadb1**. Kattintson a virtuális gépre.  
    3. Az Áttekintés lapon válassza a Leállítás elemet a virtuális gép felszabadításához.  
    4. Válassza a hálózatkezelés, majd a hálózati adapter csatolása lehetőséget. Válassza ki a legördülő listából a "hálózati adapter csatolása" lehetőséget a már létrehozott hálózati adapterek **`hana`** és az **ügyfél** alhálózatai számára.  Mentse el. 
    5. Ismételje meg a fennmaradó virtuális gépeket. A példánkban – **hanadb2** és **hanadb3**.
    6. A virtuális gépeket most leállított állapotban hagyja. A következő lépésben engedélyezzük a [gyorsított hálózatot](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) az összes újonnan csatlakoztatott hálózati adapterhez.  

6. A **`hana`** és **`client`** alhálózatok további hálózati adapterei számára engedélyezze a gyorsított hálózatkezelést.  

    1. A [Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) megnyitása [Azure Portal](https://portal.azure.com/#home)  
    2. Hajtsa végre a következő parancsokat a gyorsított hálózatkezelés engedélyezéséhez a további hálózati adapterekhez, **`hana`** és **`client`** alhálózatokhoz csatolva.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-client</b> --accelerated-networking true
    </code></pre>
7. A virtuális gépek elindítása  

    1. A bal oldali navigációs ablaktáblán válassza a Virtual Machines lehetőséget. Szűrje a virtuális gép nevét, például **hanadb1**. Kattintson a virtuális gépre.  
    2. Az Áttekintés lapon válassza az indítás lehetőséget.  

## <a name="operating-system-configuration-and-preparation"></a>Operációs rendszer konfigurációja és előkészítése

A következő elemek előtaggal vannak ellátva a **[a]** típussal – az összes csomópontra érvényes, **[1]** – csak az 1., **[2]** csomópontra vonatkozik – csak a 2. vagy **[3]** csomópontra alkalmazható – csak a 3. csomópontra alkalmazható.

1. **[A]** megtartja a gazdagépek fájljait a virtuális gépeken. Bejegyzések belefoglalása az összes alhálózathoz. Ebben a példában a következő bejegyzések lettek hozzáadva a `/etc/hosts`hoz.  
    <pre><code>
    # Storage
    10.23.2.4   hanadb1
    10.23.2.5   hanadb2
    10.23.2.6   hanadb3
    # Client
    10.23.0.5   hanadb1-client
    10.23.0.6   hanadb2-client
    10.23.0.7   hanadb3-client
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** a DHCP és a felhő konfigurációs beállításainak módosítása a nem kívánt állomásnév-módosítások elkerüléséhez.  
    <pre><code>
    vi /etc/sysconfig/network/dhcp
    #Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-eth0
    # Edit ifcfg-eth0 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

3. **[A]** készítse elő az operációs rendszert a NetApp-rendszereken futó SAP HANA futtatásához az NFS-en a következő témakörben leírtak szerint: [SAP HANA on NetApp aff Systems with NFS konfigurációs útmutató](https://www.netapp.com/us/media/tr-4435.pdf). Hozzon létre konfigurációs fájlt a NetApp konfigurációs beállításaihoz: `/etc/sysctl.d/netapp-hana.conf`.  

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
    net.core.netdev_max_backlog = 300000
    net.ipv4.tcp_slow_start_after_idle=0
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** konfigurációs fájl létrehozása a Microsoft Azure-beli konfigurációs beállításaihoz: `/etc/sysctl.d/ms-az.conf`.  

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

4. **[A]** a sunrpc beállítások módosítása a [NetApp AFF-rendszerek SAP HANA a következőben: NFS konfigurációs útmutató](https://www.netapp.com/us/media/tr-4435.pdf).  
    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>A Azure NetApp Files kötetek csatlakoztatása

1. **[A]** csatlakoztatási pontok létrehozása a HANA-adatbázis köteteihez.  
    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** csomópont-specifikus könyvtárak létrehozása `/usr/sap` **HN1**– megosztott.  

    <pre><code>
    # Create a temporary directory to mount  <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** ellenőrizze az NFS-tartomány beállítását. Győződjön meg arról, hogy a tartomány **`localdomain`** van konfigurálva, és a leképezés értéke **senki**.  
    <pre><code>
    sudo cat  /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>localdomain</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** tiltsa le a NFSV4 NÉVLEKÉPEZŐJE-azonosító leképezését. A mount parancs végrehajtásával hozza létre a címtár-struktúrát, ahol a `nfs4_disable_idmapping` található.  A/sys/modules nem tudja manuálisan létrehozni a könyvtárat, mivel a hozzáférés a kernel/illesztőprogramok számára van fenntartva.  

    <pre><code>
    
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    # Disable NFSv4 idmapping. 
    echo "N" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    </code></pre>

5. **[A]** manuálisan hozza létre a SAP HANA csoportot és a felhasználót. A Group sapsys és a User **hn1**adm azonosítóinak az előkészítés során megadott azonosítóknak kell lenniük. Ez a példa az azonosítókat **1001**-re állítja. Ellenkező esetben nem lehet hozzáférni a kötetekhez.  A Group sapsys és a **hn1**adm és sapadm felhasználói fiókjainak azonosítóinak azonosnak kell lenniük az összes virtuális gépen.  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** csatlakoztassa a megosztott Azure NetApp Files köteteket.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** a csomópont-specifikus kötetek csatlakoztatása a **hanadb1**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** a csomópont-specifikus kötetek csatlakoztatása a **hanadb2**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** a csomópont-specifikus kötetek csatlakoztatása a **hanadb3**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** ellenőrizze, hogy az összes HANA-kötet csatlakoztatva van-e az NFS protokoll **nfsv4 névleképezője**-verziójához.  
    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>Telepítés  

Ebben a példában a SAP HANA üzembe helyezéséhez az Azure-ban készenléti csomóponttal rendelkező kibővített konfigurációban a HANA 2,0 SP4-et használtuk.  

### <a name="prepare-for-hana-installation"></a>A HANA telepítésének előkészítése

1. **[A]** adja meg a legfelső szintű jelszót a HANA telepítése előtt (a telepítés megszakadása után letilthatja a legfelső szintű jelszót). Végrehajtás `root` parancsként `passwd`.  

2. **[1]** ellenőrizze, hogy tud-e SSH-t **hanadb2** és **hanadb3**, anélkül, hogy jelszót kellene megadnia.  
    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** telepítsen további csomagokat, amelyek szükségesek a HANA 2,0 SP4-hez. További részletekért lásd az SAP Megjegyzés [2593824](https://launchpad.support.sap.com/#/notes/2593824) . 
    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2, 3]** SAP HANA `data` és `log` könyvtárak tulajdonosának módosítása az **hn1**adm-re.   
    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>HANA-telepítés

1. **[1]** telepítse a SAP HANAt a [SAP HANA 2,0 telepítési és frissítési útmutató](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)utasításait követve. Ebben a példában a főkiszolgálóval, egy feldolgozóval és egy készenléti csomóponttal rendelkező SAP HANA bővítjük.  
   Indítsa el a **hdblcm** programot a HANA telepítési szoftver könyvtárából. Használja a `internal_network` paramétert, és adja át az alhálózat számára a belső HANA-csomópontok közötti kommunikációhoz használt címterület értékét.  

    <pre><code>./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   Adja meg a következő értékeket a parancssorban.

     * Válasszon egy műveletet: **1** . lépés (telepítéshez)
     * További összetevők kiválasztása a telepítéshez: írja be a **2, 3** értéket
     * Adja meg a telepítési útvonalat: nyomja le az ENTER billentyűt (az alapértelmezett érték a/Hana/Shared)
     * Adja meg a helyi gazdagép nevét: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához.
     * Hozzá kíván adni gazdagépeket a rendszeren? Adja meg az **y** értéket
     * Adja meg a következőt: **hanadb2, hanadb3**
     * Adja meg a gyökér felhasználónevét [root]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához
     * Adja meg a gyökér felhasználói jelszavát: írja be a gyökér jelszavát
     * Szerepkörök kiválasztása a gazdagép hanadb2: adja meg az **1** értéket (feldolgozóként)
     * Adja meg a gazdagép feladatátvételi csoportját a gazdagép hanadb2 [alapértelmezett]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához
     * Adja meg a tárolási partíció számát a gazdagép hanadb2 [<<assign automatically>>]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához
     * Adja meg a munkavégző csoportot a gazdagép hanadb2 [default]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához
     * Szerepkörök kiválasztása a gazdagép hanadb3: írja be a **2** értéket (készenléti állapotba)
     * Adja meg a gazdagép feladatátvételi csoportját a gazdagép hanadb3 [alapértelmezett]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához
     * Adja meg a munkavégző csoportot a gazdagép hanadb3 [default]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához
     * Adja meg SAP HANA rendszerazonosítóját: írja be a **HN1**
     * Adja meg a példány számát [00]: ENTER **03**
     * Adja meg a helyi gazda munkavégző csoportot [alapértelmezett]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához
     * Válasszon rendszerhasználatot/adja meg a (z) [4] indexet: **4** . adja meg (egyéni)
     * Adja meg az adatkötetek helyét [/hana/data/HN1]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához.
     * Adja meg a naplózási kötetek helyét [/hana/log/HN1]: nyomja le az ENTER billentyűt az alapértelmezett érték elfogadásához.
     * Korlátozza a memória maximális kiosztását? [n]: ENTER **n**
     * Adja meg a tanúsítvány állomásneve a gazdagép hanadb1 [hanadb1]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * Adja meg a tanúsítvány állomásneve a gazdagép hanadb2 [hanadb2]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * Adja meg a tanúsítvány állomásneve a gazdagép hanadb3 [hanadb3]: az ENTER billentyű lenyomásával fogadja el az alapértelmezett értéket.
     * Adja meg a rendszergazda (hn1adm) jelszavát: adja meg a jelszót
     * Adja meg a rendszeradatbázis felhasználói (rendszer) jelszavát: írja be a rendszer jelszavát.
     * Rendszeradatbázis-felhasználó (rendszer) jelszavának megerősítése: adja meg a rendszer jelszavát
     * Újraindítja a rendszert a gép újraindítása után? [n]: ENTER **n** 
     * Folytatja (i/n): érvényesítse az összegzést, és ha minden jól látható, írja be az **y** értéket.


2. **[1]** a Global. ini ellenőrzése  

   Jelenítse meg a Global. ini fájlt, és győződjön meg arról, hogy a belső SAP HANA csomópontok közötti kommunikáció konfigurációja van érvényben. Ellenőrizze a **kommunikáció**szakaszát. Meg kell adni a címtartomány **`hana`** alhálózathoz, és `listeninterface` `.internal`re kell állítani. Ellenőrizze az **internal_hostname_resolution**szakaszt. Ennek a **`hana`** alhálózathoz tartozó HANA virtuális gépek IP-címeinek kell lennie.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** adja hozzá az állomás-hozzárendelést annak biztosításához, hogy az ügyfél IP-címei az ügyfél-kommunikációhoz legyenek használva. Adja hozzá a `public_host_resolution` szakaszt, és adja hozzá a megfelelő IP-címeket az ügyfél alhálózatáról.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** indítsa újra SAP HANA a módosítások aktiválásához.  
   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** ellenőrizze, hogy az ügyfél felülete az **ügyfél** alhálózatán lévő IP-címeket fogja-e használni a kommunikációhoz.  
   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   Lásd: SAP [-megjegyzés 2183363 – SAP HANA belső hálózat konfigurálása](https://launchpad.support.sap.com/#/notes/2183363) a konfiguráció ellenőrzésével kapcsolatos részletekért.  

6. Az Azure NetApp-fájlok tárolására szolgáló SAP HANA optimalizálásához állítsa be a következő SAP HANA paramétereket:

   - `max_parallel_io_requests` **128**
   - `async_read_submit` **bekapcsolva**
   - `async_write_submit_active` **bekapcsolva**
   - **összes** `async_write_submit_blocks`

   Részletekért lásd: [SAP HANA a NETAPP aff-rendszerek NFS-konfigurációs útmutatóval](https://www.netapp.com/us/media/tr-4435.pdf). 

   A SAP HANA 2,0 rendszertől kezdve a paramétereket a `global.ini`ban állíthatja be. Lásd: SAP-Megjegyzés [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   SAP HANA 1,0 rendszerű, SPS12-os verziók esetén ezek a paraméterek a telepítés során állíthatók be, az SAP Note [2267798](https://launchpad.support.sap.com/#/notes/2267798)-ben leírtak szerint.  

7. A Azure NetApp Files által használt tárterület fájlméret-korlátozást vagy 16 TB-ot tartalmaz. A SAP HANA nem ismeri implicit módon a tárolási korlátozásokat, és nem hoz létre automatikusan új adatfájlt, ha eléri a 16 TB-os fájlméretet. Mivel a SAP HANA 16 TB-nál nagyobb mennyiségű fájlt próbálnak növelni, ami hibákat eredményez, és végül indexeli a kiszolgáló összeomlását. 

   > [!IMPORTANT]
   > Ha meg szeretné akadályozni, hogy SAP HANA a tárolási sybsystem [16TB korlátján](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) kívüli adatfájlokat próbáljon növelni, adja meg a következő paramétereket `global.ini`ban.  
   > -  datavolume_striping = True
   > - datavolume_striping_size_gb = 15000, részletek: SAP-Megjegyzés [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Ügyeljen a [2631285](https://launchpad.support.sap.com/#/notes/2631285)-es SAP-megjegyzésre. 

## <a name="test-sap-hana-failover"></a>Feladatátvételi SAP HANA tesztelése 

1. Csomópont-összeomlás szimulálása egy SAP HANA Worker csomóponton  

   Futtassa a következő parancsokat a **hn1**adm-ként a környezet állapotának rögzítéséhez, mielőtt szimulálja a csomópont összeomlását.  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
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

   Futtassa a következő parancsot root-ként a munkavégző csomóponton, amely ebben az esetben **hanadb2** szimulálja a csomópontok összeomlását.  
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   A rendszer figyelése a feladatátvétel befejezéséhez. Ha a feladatátvétel befejezte az állapot rögzítését, az alábbihoz hasonlóan kell kinéznie.  
   <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Ha el szeretné kerülni a SAP HANA feladatátvétel késését, amikor egy csomópont kernel pánikot tapasztal, állítsa a `kernel.panic` 20 másodpercre az **összes** HANA virtuális gépen. A konfigurálás `/etc/sysctl` történik. A módosítás aktiválásához indítsa újra a virtuális gépeket. Feladatátvétel: Ha egy csomópontnál a kernel pánikot észlel, 10 vagy több percet is igénybe vehet, ha ez a változás nem történik meg.  

2. A névkiszolgáló leölése  
   Futtassa a következő parancsokat a **hn1**adm-ként a környezet állapotának ellenőrzéséhez a teszt előtt:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   Futtassa a következő parancsokat a **hn1**adm néven az aktív főcsomóponton, amely ebben az esetben **hanadb1**.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   A készenléti csomópont **hanadb3** átveszi a fő csomópontot. Az erőforrás állapota a feladatátvételi teszt befejezése után:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
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

   Indítsa újra a HANA-példányt a **hanadb1**, azaz ugyanazon a virtuális gépen, ahol a névkiszolgáló le lett megölve. A **hanadb1** csomópont újra csatlakozik a környezethez, és megtartja a készenléti szerepkörét.  
   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   A következő állapotot kell megvárnia, miután SAP HANA elindult a **hanadb1**:  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   Most pedig próbálkozzon újra a kiszolgálóval a jelenleg aktív főcsomóponton, azaz a hanadb3-on.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   A csomópont **hanadb1** folytatja a fő csomópont szerepkörét. Állapot, a feladatátvételi teszt befejezése után a következőképpen fog kinézni:
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
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

   Start SAP HANA on **hanadb3** – készen áll arra, hogy készenléti csomópontként szolgáljon.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Az állapot után SAP HANA megkezdődött a **hanadb3**.  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
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

## <a name="next-steps"></a>Következő lépések

* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* Ismerje meg, hogyan hozhat létre magas rendelkezésre állást, és hogyan tervezze meg az SAP vész-helyreállítását 
* HANA az Azure-ban (nagyméretű példányok) a [magas rendelkezésre állást és a vész-helyreállítást az Azure-ban SAP HANA (nagyméretű példányok)](hana-overview-high-availability-disaster-recovery.md).
* A magas rendelkezésre állás és a SAP HANA Azure-beli virtuális gépeken történő vész-helyreállítási tervének megismeréséhez lásd: [Az Azure-beli SAP HANA magas rendelkezésre állása Virtual Machines (VM)][sap-hana-ha]
