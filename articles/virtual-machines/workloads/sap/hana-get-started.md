---
title: Az SAP HANA telepítése az Azure virtuális gépeken | Microsoft Dokumentumok"
description: Útmutató az SAP HANA azure-beli virtuális gépeken történő telepítéséhez
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: e017e082472e7a4a2fab6a2845e52d3dc7acc460
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123350"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Az SAP HANA telepítése az Azure virtuális gépein
## <a name="introduction"></a>Bevezetés
Ez az útmutató segít rámutatni a megfelelő erőforrásokat a HANA azure-beli virtuális gépeken való sikeres üzembe helyezéséhez. Ez az útmutató az SAP HANA Azure-beli virtuális gépben történő telepítése előtt ellenőrizni kívánt dokumentációs erőforrásokat fogja mutatni. Így, hogy képes-hoz hajtsa végre a megfelelő lépéseket, hogy az Sap HANA az Azure-beli virtuális gépek támogatott konfigurációjával.  

> [!NOTE]
> Ez az útmutató ismerteti az SAP HANA üzembe helyezését az Azure virtuális gépeken. Az SAP HANA nagy példányokba való üzembe helyezéséről az [SAP HANA (nagy példányok) azure-beli telepítése és konfigurálása című](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)témakörben talál további információt.
 
## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató azt is feltételezi, hogy ön ismeri a következőket:
* SAP HANA és SAP NetWeaver és hogyan kell telepíteni őket a helyszínen.
* Sap HANA és SAP alkalmazáspéldányok telepítése és működtetése az Azure-ban.
* A következő dokumentumokkal dokumentált fogalmak és eljárások:
   * Az SAP üzembe helyezésének megtervezése az Azure-ban, amely magában foglalja az Azure virtuális hálózat tervezését és az Azure Storage-használatot. Lásd: [SAP NetWeaver az Azure virtuális gépekről – Tervezési és megvalósítási útmutató](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
   * Üzembe helyezési alapelvek és a virtuális gépek azure-beli üzembe helyezésének módjai. Tekintse meg [az Azure virtuális gépek üzembe helyezését az SAP-hoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
   * Magas rendelkezésre állási fogalmak az SAP HANA-hoz az [SAP HANA magas rendelkezésre állása az Azure virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)

## <a name="step-by-step-before-deploying"></a>Lépésről lépésre a telepítés előtt
Ebben a szakaszban a különböző lépések vannak felsorolva, amelyeket végre kell hajtania az SAP HANA telepítése egy Azure virtuális gépen. A sorrend et felsorolja a sorba, és mint ilyen, a következőként kell követni:

1. Nem minden lehetséges üzembe helyezési forgatókönyv támogatott az Azure-ban. Ezért ellenőrizze a dokumentum [SAP számítási feladat az Azure virtuális gépen támogatott forgatókönyvek](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) a forgatókönyv, amelyet az SAP HANA-telepítés. Ha a forgatókönyv nem szerepel a listában, azt kell feltételeznie, hogy még nem tesztelték, és ennek következtében nem támogatott
2. Feltételezve, hogy van egy durva ötlet a memóriaigény az SAP HANA-telepítés, meg kell találnia egy megfelelő Azure virtuális gép. Nem minden virtuális gép, amely rendelkezik az SAP NetWeaver tanúsítvánnyal, az [SAP támogatási megjegyzés#1928533](https://launchpad.support.sap.com/#/notes/1928533)dokumentált SAP HANA tanúsítvánnyal rendelkezik. Az SAP HANA-tanúsítvánnyal rendelkező Azure-beli virtuális gépek igazságforrása az [SAP HANA hardverkönyvtára.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Az **S-sel** kezdődő egységek [hana nagy példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) egységek, és nem az Azure virtuális gépek.
3. A különböző Azure virtuálisgép-típusok különböző minimális operációsrendszer-kiadásokkal rendelkeznek a SUSE Linux vagy a Red Hat Linux számára. A honlapon [SAP HANA hardver könyvtár ,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)meg kell, hogy kattintson egy bejegyzést a listán az SAP HANA tanúsítvánnyal egységek részletes adatokat kap az egység. A támogatott HANA-munkaterhelés mellett az SAP HANA-hoz ezekkel az egységekkel támogatott operációs rendszer-kiadások is
4. Az operációs rendszer kiadásaiszerint figyelembe kell venni bizonyos minimális kernel kiadásokat. Ezeket a minimális kiadásokat az alábbi SAP támogatási megjegyzések dokumentálják:
    - [Sap támogatási megjegyzés #2814271 AZ SAP HANA biztonsági mentés sikertelen az Azure-ban a Checksum-hiba](https://launchpad.support.sap.com/#/notes/2814271)
    - [Az SAP támogatási megjegyzése #2753418 lehetséges teljesítménycsökkenés torlatot eredményező időzítő-tartalék miatt](https://launchpad.support.sap.com/#/notes/2753418)
    - [Az SAP támogatási megjegyzése #2791572 teljesítménycsökkenés miatt hiányzik a Hyper-V-támogatása az Azure-ban](https://launchpad.support.sap.com/#/notes/2791572)
4. A virtuális gép típusának támogatott operációsrendszer-kiadás alapján ellenőriznie kell, hogy a kívánt SAP HANA-kiadás támogatott-e az operációs rendszer kiadásával. Olvassa el [az SAP támogatási megjegyzés#2235581](https://launchpad.support.sap.com/#/notes/2235581) az SAP HANA-kiadások különböző operációs rendszer-kiadások támogatási mátrixához.
5. Mivel előfordulhat, hogy az Azure VM-típus, az operációs rendszer és az SAP HANA-kiadás érvényes kombinációját találta, be kell jelentkeznie az SAP-termék rendelkezésre állási mátrixában. Az SAP availability matrix, megtudhatja, hogy az SAP-termék, amelyet futtatni szeretne az SAP HANA-adatbázis támogatott.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>A virtuális gépek részletes üzembe helyezésével és a vendég operációs rendszersel kapcsolatos szempontok
Ebben a fázisban végig kell mennie a virtuális gép(ek) üzembe helyezésének lépésein a HANA telepítéséhez, és végül optimalizálni a kiválasztott operációs rendszert a telepítés után.

1. Válassza ki az alapképet az Azure-galériából. Ha azt szeretné, hogy saját operációs rendszer lemezkép az SAP HANA, tudnia kell a különböző csomagok, amelyek szükségesek a sikeres SAP HANA telepítés. Ellenkező esetben ajánlott a SUSE és a Red Hat-rendszerképek az SAP vagy az SAP HANA az Azure-képtárból. Ezek a lemezképek tartalmazzák a sikeres HANA-telepítéshez szükséges csomagokat. Az operációs rendszer szolgáltatójával kötött támogatási szerződés alapján ki kell választania egy képet, amelyen saját licencet hoz. Vagy olyan operációsrendszer-lemezképet választ, amely támogatást tartalmaz
2. Ha olyan vendég operációsrendszer-lemezképet választott, amelyhez saját licencet kell hoznia, regisztrálnia kell az operációs rendszer lemezképét az előfizetéssel, hogy letölthesse és alkalmazhathassa a legújabb javításokat. Ez a lépés nyilvános internet-hozzáférést igényel. Kivéve, ha beállítja a privát példányát, például egy SMT-kiszolgáló az Azure-ban.
3. Döntse el a virtuális gép hálózati konfigurációját. További információkat az SAP [HANA infrastruktúra-konfigurációk és az Azure-beli műveletek](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)dokumentumában olvashat. Ne feledje, hogy nincsenek hálózati átviteli kvóták, amelyeket az Azure-ban virtuális hálózati kártyákhoz rendelhet. Ennek eredményeképpen az egyetlen célja a forgalom különböző virtuális adaptereken keresztül történő irányításának biztonsági szempontokon alapul. Bízunk benne, hogy talál egy támogatott kompromisszumot a több virtuális hálózati adaptereken keresztül történő forgalom-útválasztás összetettsége és a biztonsági szempontok által érvényesített követelmények között.
3. Alkalmazza a legújabb javításokat az operációs rendszerre, miután a virtuális gép telepítve van, és regisztrálva van. Regisztrált vagy a saját előfizetés. Vagy abban az esetben, ha úgy döntött, egy lemezkép, amely tartalmazza az operációs rendszer támogatja a virtuális gép már hozzáférhet a javításokat már. 
4. Alkalmazza az SAP HANA-hoz szükséges dallamokat. Ezek a dallamok az alábbi SAP támogatási megjegyzésekben vannak felsorolva:

    - [SAP támogatási megjegyzés #2694118 - Red Hat Enterprise Linux HA bővítmény az Azure-ban](https://launchpad.support.sap.com/#/notes/2694118)
    - [SAP támogatási megjegyzés #1984787 - SUSE LINUX Enterprise Server 12: Telepítési megjegyzések](https://launchpad.support.sap.com/#/notes/1984787) 
    - [SAP támogatási megjegyzés #2578899 - SUSE Linux Enterprise Server 15: Telepítési megjegyzés](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP támogatási megjegyzés #2002167 - Red Hat Enterprise Linux 7.x: Telepítés és frissítés](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP támogatási megjegyzés #2292690 - SAP HANA DB: Ajánlott operációsrendszer-beállítások az RHEL 7-hez](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [SAP támogatási megjegyzés #2772999 - Red Hat Enterprise Linux 8.x: Telepítés és konfiguráció](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [SAP támogatási megjegyzés #2777782 - SAP HANA DB: Ajánlott operációsrendszer-beállítások az RHEL 8-hoz](https://launchpad.support.sap.com/#/notes/2777782)
    -  [SAP támogatási megjegyzés #2455582 - Linux: A GCC 6.x-szel lefordított SAP-alkalmazások futtatása](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [SAP támogatási megjegyzés #2382421 - A hálózati konfiguráció optimalizálása HANA- és OS-szinten](https://launchpad.support.sap.com/#/notes/2382421)

1. Válassza ki az SAP HANA Azure-tárolási típusát. Ebben a lépésben el kell döntenie az SAP HANA telepítéséhez szükséges tárolási elrendezést. A csatlakoztatott Azure-lemezeket vagy a natív Azure NFS-megosztásokat fogja használni. Az Azure storage-típusok, vagy támogatott, és kombinációi különböző Azure storage-típusok, amelyek felhasználhatók, dokumentálva vannak az [SAP HANA Azure virtuálisgép-tároló konfigurációk.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) Vegye ki a dokumentált konfigurációk kiindulási pontként. Nem éles rendszerek esetén előfordulhat, hogy alacsonyabb átviteli vagy IOPS-átviteli beállításokat is konfigurálhat. Éles környezetben előfordulhat, hogy egy kicsit nagyobb átviteli és IOPS-konfigurálnia kell.
2. Győződjön meg arról, hogy [konfigurálta az Azure Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) a kötetek, amelyek tartalmazzák a DBMS tranzakciónaplók vagy a naplók ismétlése, ha m-sorozatú vagy Mv2 sorozatú virtuális gépek használata. Vegye figyelembe az Írási gyorssegéd dokumentált korlátait.
2. Ellenőrizze, hogy az [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) engedélyezve van-e a telepített virtuális gép(ek)en.

> [!NOTE]
> A különböző sap-tune profilokban vagy a jegyzetekben leírtak szerint nem minden parancs futhat sikeresen az Azure-ban. A virtuális gépek energiagazdálkodási módját módosító parancsok általában hibával térnek vissza, mivel az alapul szolgáló Azure gazdagép hardverének energiagazdálkodási módja nem módosítható.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Az Azure virtuális gépekre jellemző részletes előkészületek
Az Egyik Az Azure-specifikusegy Azure virtuálisgép-bővítmény, amely az SAP gazdagép figyelési adatokat szolgáltat. A figyelési bővítmény telepítésével kapcsolatos részleteket a következő helyen dokumentálják:

-  [Az SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) tárgyalja az SAP fokozott figyelését az Azure-beli Linux-virtuális gépekkel 
-  [Az SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) a Linux on SAPOSCOL-ról szóló információkat tárgyalja 
-  [Az SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) az SAP legfontosabb figyelési mutatóit tárgyalja a Microsoft Azure-ban
-  [Az Azure virtuális gépek üzembe helyezése az SAP NetWeaver számára](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>SAP HANA telepítés
Az Azure virtuális gépek üzembe helyezett és az operációs rendszerek regisztrálva és konfigurálva, telepítheti az SAP HANA az SAP-telepítés nek megfelelően. A jó kezdet, hogy ezt a dokumentációt, kezdje ezt az SAP honlapján [HANA források](https://www.sap.com/products/hana/implementation/resources.html)

Az SAP HANA kibővített konfigurációk az Azure Premium Storage vagy ultralemez közvetlenen csatlakoztatott lemezeit használó sap HANA-konfigurációk esetében olvassa el az [SAP HANA infrastruktúra-konfigurációk és -műveletek sajátosságait az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out)


## <a name="additional-resources-for-sap-hana-backup"></a>További források az SAP HANA biztonsági mentéséhez
Az SAP HANA-adatbázisok Azure-beli virtuális gépeken való biztonsági mentésével kapcsolatos további információkért lásd:
* [Biztonsági mentési útmutató az SAP HANA-hoz az Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [SAP HANA Azure biztonsági mentés fájlszinten](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)

## <a name="next-steps"></a>További lépések
Olvassa el a dokumentációt:

- [SAP HANA infrastruktúrakonfigurációk és -műveletek az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA Azure-beli virtuális gépek tárkonfigurációi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)





