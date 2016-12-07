---
title: "VMM-felhőben lévő Hyper-V virtuális gépek replikálása az Azure-ba az Azure Portal használatával | Microsoft Docs"
description: "A cikkből megtudhatja, hogyan helyezze üzembe az Azure Site Recovery-t a VMM-felhőben futó Hyper-V virtuális gépeknek az Azure-ba történő replikálása, feladatátvétele és helyreállítása érdekében az Azure portál segítségével"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/31/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: 9968ac8e139b3f08fe0d59180e51fe9c19241dd5


---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-the-azure-portal"></a>VMM-felhőben lévő Hyper-V virtuális gépek replikálása az Azure-ba az Azure Portal használatával
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmm-to-azure.md)
> * [Klasszikus Azure portál](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [Klasszikus PowerShell](site-recovery-deploy-with-powershell.md)
>
>

Üdvözli az Azure Site Recovery szolgáltatás!

A Site Recovery egy olyan Azure-szolgáltatás, amely hozzájárul üzletfolytonossági és vészhelyreállítási (BCDR) stratégiájának kidolgozásához. A Site Recovery a helyszíni fizikai kiszolgálóknak és virtuális gépeknek az Azure-felhőbe vagy egy másodlagos adatközpontba történő replikálását koordinálja. Ha az elsődleges helyen valamilyen okból kimaradás lép fel, a rendszer átadja a feladatokat a másodlagos helynek, így az alkalmazások és számítási feladatok nem állnak le. Ha az elsődleges helyen helyreáll a normál működés, a rendszer visszaadja a feladatokat. További információk: [What is Azure Site Recovery?](site-recovery-overview.md) (Mire használható az Azure Site Recovery szolgáltatás?)

Ez a cikk azt ismerteti, hogy hogyan replikálhat System Center VMM-felhőkben felügyelt helyszíni Hyper-V virtuális gépeket az Azure-ba az Azure Portal Azure Site Recovery szolgáltatásának segítségével.

A cikk elolvasása után felmerülő megjegyzéseit alul, a Disqus-beszélgetésben teheti közzé. Műszaki jellegű kérdéseit az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) tegye fel.

## <a name="quick-reference"></a>Rövid összefoglalás
Telje körű üzembe helyezéshez feltétlenül javasoljuk, hogy a cikk összes lépését kövesse. Ha viszont idő szűkében van, akkor használhatja az alábbi gyors összegzést, amelyben további információkra mutató hivatkozásokat talál.

| **Terület** | **Részletek** |
| --- | --- |
| **Üzembe helyezési forgatókönyv** |VMM-felhőben lévő Hyper-V virtuális gépek replikálása az Azure-ba az Azure Portal használatával |
| **Helyszíni követelmények** |Egy vagy több, a System Center 2012 R2 verziójával futó VMM-kiszolgáló egy vagy több felhővel.<br/><br/> A felhőknek egy vagy több VMM-gazdagépcsoportot kell tartalmazniuk.<br/><br/> Legalább egy Hyper-V-kiszolgáló amelyen legalább egy Windows Server 2012 R2 verzió fut Hyper-V szerepkörrel vagy a Microsoft Hyper-V Server 2012 R2 verziója amelyen telepítve vannak a legújabb frissítések.<br/><br/> A VMM-kiszolgálóknak és a Hyper-V-gazdagépeknek internet-hozzáféréssel kell rendelkezniük, és közvetlenül vagy proxyn keresztül el kell érniük meghatározott URL-címeket. [Részletes információk](#on-premises-prerequisites). |
| **Helyszíni korlátozások** |A HTTPS-alapú proxyk nem támogatottak |
| **Szolgáltató/ügynök** |A replikált virtuális gépekhez szükség van az Azure Site Recovery Provider telepítésére.<br/><br/> A Hyper-V-gazdagépekhez szükség van a Recovery Services-ügynök telepítésére.<br/><br/> Ezeket az üzembe helyezés során fogja telepíteni. |
|  **Azure-követelmények** |Azure-fiók<br/><br/> Recovery Services-tároló<br/><br/> LRS- vagy GRS-tárfiók a tárolórégióban<br/><br/> Standard szintű tárfiók<br/><br/> Azure virtuális hálózat a tárolórégióban. [Részletes információk](#azure-prerequisites). |
|  **Azure-korlátozások** |Ha GRS-t használ, akkor a naplózáshoz egy másik LRS-fiókra is szüksége lesz<br/><br/> Az Azure-portálon létrehozott tárfiókok nem helyezhetők át erőforráscsoportok között.<br/><br/> Prémium szintű Storage-fiók nem használható. |
|  **VM-replikáció** |A virtuális gépeknek meg kell felelniük az Azure előfeltételeinek](site-recovery-best-practices.md#azure-virtual-machine-requirements)<br/><br/> |
|  **Replikációs korlátozások** |A statikus IP-című linuxos virtuális gépeket nem lehet replikálni.<br/><br/> A replikációból nem zárhatók ki egyedi lemezek. |
| **Üzembe helyezési lépések** |1) Az Azure előkészítése (előfizetés, tárolás, hálózat) -> 2) A helyszíni rendszer előkészítése (VMM és hálózatleképezés) -> 3) A Recovery Sweervices.tároló létrehozása -> 4) A VMM- és Hyper-V-gazdagépek beállítása -> 5) A replikációs beállítások konfigurálása -> 6) A replikáció engedélyezése -> 7)A replikáció és a feladatátvétel tesztelése. |

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery az Azure Portalon
Az Azure két különböző [üzembe helyezési modellel] rendelkezik (../resource-manager-deployment-model

> ) az erőforrások létrehozásához és használatához: Azure Resource Manager és klasszikus. Az Azure emellett kétféle portállal rendelkezik, a klasszikus Azure-portállal és az Azure Portal portállal. A jelen cikk az Azure-portálon történő üzembe helyezést ismerteti.
>
>

Az Azure-portál Site Recovery szolgáltatása több új funkciót kínál:

* Az Azure Backup és az Azure Site Recovery szolgáltatást mostantól egy közös Recovery Services-tároló tartalmazza, így az üzletmenet-folytonossági és vészhelyreállítási (BCDR) funkciók egyetlen helyről kezelhetők. Az egységesített irányítópult lehetővé teszi a helyszíni helyeken, valamint a nyilvános Azure-felhőben futó műveletek figyelését és kezelését.
* Az Azure-előfizetésben kiépített Cloud Solution Provider (CSP) programmal rendelkező felhasználók mostantól az Azure portálon kezelhetik a Site Recovery-műveleteket.
* Az Azure-portálról a gépeket Azure Resource Manager-tárfiókokra is lehet replikálni. Feladatátvétel során a Site Recovery Resource Manager-alapú virtuális gépeket hoz létre az Azure-ban.
* A Site Recovery továbbra is támogatja a klasszikus tárfiókokba történő replikálást. Feladatátvétel során a Site Recovery a klasszikus modellel hozza létre a virtuális gépeket.

## <a name="site-recovery-in-your-business"></a>A Site Recovery szerepe a vállalatban
A legtöbb vállalatnál szükség van üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiára, amely meghatározza, hogy hogyan tudnak az alkalmazások és az adatok üzemben maradni a tervezett és nem tervezett leállások során, illetve, hogy hogyan lehet minél gyorsabban visszaállni a normál működésre. A Site Recovery a következőket kínálja:

* Külső helyszíni védelem a Hyper-V virtuális gépeken futó üzleti alkalmazások számára.
* Egyetlen helyen állíthatja be, kezelheti és figyelheti a replikációt, a feladatátvételt és a helyreállítást.
* Egyszerű feladatátadás az Azure-nak, majd feladatátvétel (helyreállítás) az Azure-ból a helyszíni Hyper-V-gazdakiszolgálókra.
* Több virtuális gépet tartalmazó helyreállítási tervek, így az egy szinthez tartozó alkalmazások számítási feladatai együtt hajtják végre a feladatátvételt.

## <a name="scenario-architecture"></a>Forgatókönyv-architektúra
A forgatókönyv az alábbi elemekből áll:

* **VMM-kiszolgáló**: egy vagy több felhővel ellátott helyszíni VMM-kiszolgáló.
* **Hyper-V gazdagép vagy fürt**: VMM-felhőkben felügyelt Hyper-V gazdakiszolgálók vagy fürtök.
* **Azure Site Recovery Provider és Recovery Services Agent**: az üzembe helyezés során az Azure Site Recovery Providert a VMM-kiszolgálóra, míg a Microsoft Azure Recovery Services Agent ügynököt a Hyper-V gazdakiszolgálókra kell telepíteni. A VMM-kiszolgálóra telepített Provider HTTPS-protokollon, a 443-as porton keresztül kommunikál a Site Recoveryvel a replikáció előkészítéséhez. A Hyper-V gazdakiszolgálóra telepített ügynök alapértelmezés szerint HTTPS-protokollal, a 443-as porton replikálja az adatokat az Azure-tárterületre.
* **Azure**: szüksége lesz egy Azure-előfizetésre, egy Azure-tárfiókra a replikált adatok tárolásához, valamint egy Azure virtuális hálózatra, hogy az Azure virtuális gépek hálózati kapcsolata a feladatátvételt követően is megmaradjon.

![E2A topológia](./media/site-recovery-vmm-to-azure/architecture.png)

## <a name="azure-prerequisites"></a>Azure-előfeltételek
A következőkre lesz szüksége az Azure-ban.

| **Előfeltétel** | **Részletek** |
| --- | --- |
| **Azure-fiók** |Szüksége van egy [Microsoft Azure](http://azure.microsoft.com/)-fiókra. Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is. [További információk](https://azure.microsoft.com/pricing/details/site-recovery/) a Site Recovery díjszabásáról. |
| **Azure Storage tárterület** |A replikált adatok tárolásához standard Azure-tárfiókra van szükség. LRS- vagy GRS-tárfiókot is használhat. Mi a GRS használatát javasoljuk, mivel ez akár regionális kimaradás során, illetve az elsődleges régió helyreállíthatatlansága esetében gondoskodik az adatok hibatűréséről. [További információk](../storage/storage-redundancy.md). A fióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.<br/><br/>Prémium szintű Storage-fiók nem használható.<br/><br/> A replikált adatokat az Azure Storage tárolja, ha pedig feladatátvétel következik be, a rendszer Azure virtuális gépeket hoz létre. <br/><br/> [Itt részletesebben olvashat](../storage/storage-introduction.md) az Azure Storage-ről. |
| **Azure-hálózat** |Szüksége van egy Azure virtuális hálózatra, amelyhez az Azure virtuális gépek tudnak csatlakozni, amikor a rendszer feladatátvételt hajt végre. A hálózatnak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie. |

## <a name="on-premises-prerequisites"></a>Helyszíni előfeltételek
Az alábbiakra lesz szüksége a helyszínen.

| **Előfeltétel** | **Részletek** |
| --- | --- |
| **VMM** |Egy vagy több, a System Center 2012 R2 verziójával futó VMM-kiszolgáló. Minden VMM-kiszolgálón legalább egy felhőnek kell üzemelnie. A felhők tartalmazzák a következőket:<br/><br/> Legalább egy VMM-gazdagépcsoport.<br/><br/> Minden gazdagépcsoportban legalább egy Hyper-V gazdakiszolgáló vagy fürt.<br/><br/>[Itt részletesebb tájékoztatást talál](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx) a VMM-felhők létrehozásával kapcsolatban. |
| **Hyper-V** |A Hyper-V-kiszolgálókon legalább a **Windows Server 2012 R2** verziónak kell futnia Hyper-V szerepkörrel vagy a **Microsoft Hyper-V Server 2012 R2** verziónak, és telepíteni kell rájuk a legújabb frissítéseket.<br/><br/> Minden Hyper-V kiszolgáló tartalmazzon legalább egy virtuális gépet.<br/><br/> A replikálni kívánt virtuális gépeket tartalmazó Hyper-V gazdakiszolgálót vagy fürtöt a VMM-felhőnek kell felügyelnie.<br/><br/>A Hyper-V kiszolgálókon közvetlen vagy proxyn keresztülmenő internetkapcsolatnak kell rendelkezésre állnia.<br/><br/>A Hyper-V kiszolgálókra telepíteni kell a [2961977](https://support.microsoft.com/kb/2961977) számú cikkben említett javításokat.<br/><br/>Az adatoknak az Azure-ba történő replikálásához a Hyper-V gazdakiszolgálóknak el kell érniük az internetet. |
| **Provider és Agent** |Az Azure Site Recovery üzembe helyezése során az Azure Site Recovery Providert a VMM-kiszolgálóra, míg a Microsoft Azure Recovery Services Agent ügynököt a Hyper-V-gazdagépekre kell telepíteni. A Provider és az Agent közvetlen vagy proxyn keresztüli internetkapcsolatot igényel az Azure-hoz való csatlakozáshoz. A HTTPS-alapú proxyk nem támogatottak. A VMM-kiszolgálón és a Hyper-V gazdagépeken használt proxykiszolgálónak engedélyeznie kell a következők elérését: <br/><br/> ``*.hypervrecoverymanager.windowsazure.com`` <br/><br/> ``*.accesscontrol.windows.net``<br/><br/> ``*.backup.windowsazure.com``<br/><br/> ``*.blob.core.windows.net``<br/><br/> ``*.store.core.windows.net``<br/><br/> Ha a VMM-kiszolgálón IP-címeken alapuló tűzfalszabályok működnek, ellenőrizze, hogy a szabályok engedélyezik-e az Azure-ral való kommunikációt. Engedélyeznie kell az [Azure Datacenter IP-tartományait](https://www.microsoft.com/download/confirmation.aspx?id=41653) és a HTTPS-portot (443).<br/><br/> Engedélyezze az előfizetéshez tartozó Azure-régió, illetve az USA nyugati régiójának IP-tartományait.<br/><br/> Továbbá a VMM-kiszolgálón használt proxykiszolgálónak hozzáférésre van szüksége a következőhöz: ``https://www.msftncsi.com/ncsi.txt`` |

## <a name="protected-machine-prerequisites"></a>A védett gépre vonatkozó előfeltételek
| **Előfeltétel** | **Részletek** |
| --- | --- |
| **Védett virtuális gépek** |Virtuális gép feladatainak átadása csak akkor lehetséges, ha az Azure virtuális géphez rendelt név megfelel az [Azure-előfeltételeknek](site-recovery-best-practices.md#azure-virtual-machine-requirements). A virtuális gép replikációjának bekapcsolását követően módosíthatja a nevet. <br/><br/> A védett gépek önálló lemezeinek kapacitása nem haladhatja meg az 1023 GB-t. Egy virtuális gépen legfeljebb 16 lemez működhet (ami 16 TB kapacitást jelent).<br/><br/> Közös lemezes vendégfürtök nem használhatók.<br/><br/> Az UEFI/EFI típusú rendszerindítás nem használható.<br/><br/> Ha a forrás virtuális gépen összevonták a hálózati adaptereket, az Azure-ba történő feladatátadást követően a rendszer azokat egyetlen hálózati adapterré konvertálja.<br/><br/>A statikus IP-című linuxos virtuális gépek számára nem biztosítható védelem. |

## <a name="prepare-for-deployment"></a>Felkészülés az üzembe helyezésre
Az üzembe helyezésre az alábbi műveletekkel készülhet fel:

1. [Állítson be egy Azure-hálózatot](#set-up-an-azure-network), amely az Azure virtuális gépeket fogja tartalmazni a feladatátvétel után.
2. [Állítsa be az Azure-tárfiókot](#set-up-an-azure-storage-account) a replikált adatok tárolásához.
3. [Készítse elő a VMM-kiszolgálót](#prepare-the-vmm-server) a Site Recovery üzembe helyezésére.
4. [Készüljön elő a hálózatleképezésre](#prepare-for-network-mapping). Állítsa be úgy a hálózatokat, hogy a Site Recovery üzembe helyezésekor lehessen konfigurálni a hálózatleképezéseket.

### <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása
Szükség van egy Azure-hálózatra, amelyhez a feladatátvételt követően létrehozott Azure virtuális gépek csatlakozni tudnak.

* A hálózatnak és a Recovery Services-tárolónak ugyanabban a régióban kell lennie.
* Az átadott Azure virtuális gépekhez használni kívánt erőforrásmodelltől függően az Azure-hálózatot is [Resource Manager üzemmódban](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) vagy [klasszikus üzemmódban](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) kell beállítani.
* Javasoljuk, hogy a kezdés előtt hozza létre a hálózatot. Ha most kihagyja ezt a lépést, a Site Recovery üzembe helyezése közben kell majd elvégeznie.

> [!NOTE]
> A Site Recovery üzembe helyezéséhez használt hálózatok esetében a [Migration of networks](../resource-group-move-resources.md) (Hálózatok áttelepítése) művelet az egy előfizetésen belüli erőforráscsoportok vagy előfizetések között nem támogatott.
>
>

### <a name="set-up-an-azure-storage-account"></a>Azure-tárfiók beállítása
* Az Azure-ba replikált adatok tárolásához standard Azure-tárfiókra lesz szüksége. A fióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.
* Az átadott Azure virtuális gépekhez használni kívánt erőforrásmodelltől függően a fiókot is [Resource Manager üzemmódban](../storage/storage-create-storage-account.md) vagy [klasszikus üzemmódban](../storage/storage-create-storage-account-classic-portal.md) kell beállítani.
* Javasoljuk, hogy még a kezdés előtt állítsa be a fiókot. Ha most kihagyja ezt a lépést, a Site Recovery üzembe helyezése közben kell majd elvégeznie.

> [!NOTE]
> A Site Recovery üzembe helyezéséhez használt tárfiókok esetében nem támogatott a [tárfiókok áttelepítése](../resource-group-move-resources.md) az azonos előfizetéshez tartozó vagy különböző előfizetésekben található erőforráscsoportok között.
>
>

### <a name="prepare-the-vmm-server"></a>A VMM-kiszolgáló előkészítése
* Ellenőrizze, hogy a VMM-kiszolgáló megfelel-e az [előfeltételeknek](#on-premises-prerequisites).
* A Site Recovery üzembe helyezése során megadhatja, hogy a VMM-kiszolgálón futó összes felhő elérhető legyen-e az Azure Portalon. Ha azt szeretné, hogy csak bizonyos felhők jelenjenek meg a portálon, ezt a beállítást a felhőben, a VMM felügyeleti konzoljában adhatja meg.

### <a name="prepare-for-network-mapping"></a>A hálózatleképezés előkészítése
A Site Recovery leképezése során hálózatleképezést is be kell állítania. A hálózatleképezés kapcsolatot hoz létre a forrás VMM-virtuálisgép-hálózatok és a cél Azure-hálózatok között, így biztosítja a következőket:

* A feladataikat azonos hálózatba átadó gépek képesek lesznek csatlakozni egymáshoz, még akkor is, ha nem ugyanazzal a módszerrel vagy ugyanabban a helyreállítási tervben hajtották végre a feladatátvételt.
* Ha a cél Azure-hálózatban hálózati átjáró működik, az Azure virtuális gépek képesek csatlakozni a helyszíni virtuális gépekhez.
* A hálózatleképezés beállításához a következőkre van szükség:

  * Ellenőrizze, hogy a forrás Hyper-V gazdakiszolgálón futó virtuális gépek csatlakoznak-e egy VMM-virtuálisgép-hálózathoz. Ezt a hálózatot kösse össze egy, a felhőhöz társított logikai hálózattal.
  * Rendelkezzen egy, a [fentiekben](#set-up-an-azure-network) leírt Azure-hálózattal.
* [További információk](site-recovery-network-mapping.md) a hálózatleképezés működéséről.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Kattintson az **Új** > **Felügyelet** > **Recovery Services** elemre. Vagy kattintson a **Tallózás** > **Recovery Services**-tárolók > **Hozzáadás** elemre.

    ![Új tároló](./media/site-recovery-vmm-to-azure/new-vault3.png)
3. A **Név** mezőben adja meg a tárolót azonosító rövid nevet. Ha egynél több előfizetéssel rendelkezik, válasszon egyet ezek közül.
4. [Hozzon létre egy erőforráscsoportot](../resource-group-template-deploy-portal.md), vagy válasszon ki egy meglévőt. Válassza ki a kívánt Azure-régiót. A gépeket ebbe a régióba replikálja a rendszer. A támogatott régiók megtekintéséhez olvassa el az [Azure Site Recovery – Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/) című cikknek a földrajzi elérhetőséggel foglalkozó részét.
5. Ha gyors hozzáférést szeretne a tárolóhoz az irányítópultról, kattintson a **Rögzítés az irányítópulton** > **Tároló létrehozása** elemre.

    ![Új tároló](./media/site-recovery-vmm-to-azure/new-vault-settings.png)

Az új tároló megjelenik az **Irányítópult** > **Minden erőforrás** menüben, illetve a központi **Recovery Services-tárolók** panelen.

## <a name="getting-started"></a>Első lépések
A Site Recovery szolgáltatás Bevezetés funkciója segítségével a lehető leggyorsabban üzembe helyezheti a szolgáltatást. A Bevezetés funkció ellenőrzi az előfeltételeket, és a helyes sorrendben végigvezeti a felhasználót a Site Recovery üzembe helyezési lépésein.

Az Bevezetés keretében ki kell választania a replikálni kívánt gépek típusát, valamint a replikáció célját. Be kell állítania a helyszíni kiszolgálókat, az Azure-tárfiókokat, illetve a hálózatokat. Létre kell hozni a replikációs szabályzatokat, valamint el kell végezni a kapacitástervezést. Az infrastruktúra kiépítését követően engedélyezheti a virtuális gépek replikációját. Ezenfelül elvégezheti konkrét gépek feladatátadását, de létrehozhat több gép feladatátadását tartalmazó helyreállítási terveket is.

A Bevezetés folyamat első lépéseként válassza ki a Site Recovery üzembe helyezésének módját. A Bevezetés eljárás az adott replikációs követelményektől függően némileg eltérően megy végbe.

## <a name="step-1-choose-your-protection-goals"></a>1. lépés: Védelmi célok megválasztása
Válassza ki, hogy mit szeretne replikálni, és hova.

1. Válassza ki a tárolót a **Recovery Services-tárolók** panelen, majd kattintson a **Beállítások** elemre.
2. A **Bevezetés** ablakban kattintson a **Site Recovery** > **1. lépés: Az infrastruktúra előkészítése** > **Védelmi cél** elemre.

    ![Célok megválasztása](./media/site-recovery-vmm-to-azure/choose-goals.png)
3. A **Védelmi cél** ablakban válassza **Az Azure-ba**, valamint a **Igen, a következővel: Hyper-V** lehetőséget. Az **Igen** lehetőség kiválasztásával erősítse meg, hogy VMM-mel felügyeli a Hyper-V gazdagépeket és a helyreállítási helyet. Végül kattintson az **OK** gombra.

    ![Célok megválasztása](./media/site-recovery-vmm-to-azure/choose-goals2.png)

## <a name="step-2-set-up-the-source-environment"></a>2. lépés: A forráskörnyezet beállítása
Telepítse a VMM-kiszolgálóra az Azure Site Recovery Providert, és regisztrálja a kiszolgálót a tárolóban. Telepítse az Azure Recovery Services Agent ügynököt a Hyper-V gazdagépekre.

1. Kattintson a következő elemre: **2. lépés: Az infrastruktúra előkészítése** > **Forrás**.

    ![A forrás beállítása](./media/site-recovery-vmm-to-azure/set-source1.png)
2. A **Forrás előkészítése** ablakban kattintson a **+ VMM** gombra a VMM-kiszolgálók felvételéhez.

    ![A forrás beállítása](./media/site-recovery-vmm-to-azure/set-source2.png)
3. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **System Center VMM-kiszolgáló** érték látható-e, illetve, hogy a VMM-kiszolgáló megfelel-e [az előfeltételeknek és az URL-követelményeknek](#on-premises-prerequisites).
4. Töltse le az Azure Site Recovery Provider telepítőfájlját.
5. Töltse le a regisztrációs kulcsot. Erre a telepítő futtatása során lesz szükség. A kulcs a generálásától számított öt napig érvényes.

    ![A forrás beállítása](./media/site-recovery-vmm-to-azure/set-source3.png)
6. Telepítse az Azure Site Recovery Providert a VMM-kiszolgálóra.

### <a name="set-up-the-azure-site-recovery-provider"></a>Az Azure Site Recovery Provider beállítása
1. Futtassa a Provider telepítőfájlját.
2. A **Microsoft Update** lapon kérheti a frissítések beszerzését, így a rendszer a Microsoft Update-szabályzatnak megfelelően telepíteni fogja a Providerhez kiadott frissítéseket.
3. A **Telepítés** lapon tetszés szerint fogadja el vagy módosítsa a Provider alapértelmezett telepítési helyét, majd kattintson a **Telepítés** gombra.

    ![Telepítés helye](./media/site-recovery-vmm-to-azure/provider2.png)
4. A telepítés befejezését követően a VMM-kiszolgálónak a tárolóban való regisztrálásához kattintson a **Regisztráció** gombra.
5. A **Tároló beállításai** lapon kattintson a **Tallózás** gombra, és válassza ki a tároló kulcsfájlját. Adja meg az Azure Site Recovery-előfizetést és a tároló nevét.

    ![Kiszolgáló regisztrációja](./media/site-recovery-vmm-to-azure/provider10.PNG)
6. Az **Internetkapcsolat** beállításnál adja meg, hogy a VMM-kiszolgálón futó Provider hogyan csatlakozzon a Site Recoveryhez az interneten keresztül.

   * Ha azt szeretné, hogy a Provider közvetlenül kapcsolódjon, válassza a **Közvetlen csatlakozás az Azure Site Recoveryhez proxykiszolgáló nélkül** lehetőséget.
   * Ha a meglévő proxy hitelesítést igényel, illetve ha egyéni proxyt szeretne használni, válassza a **Csatlakozás az Azure Site Recoveryhez proxykiszolgálóval** lehetőséget.
   * Ha az egyéni proxy használatát választja, meg kell adnia a címet, a portot és a hitelesítő adatokat.
   * Ha proxyt használt, ellenőrizze, hogy engedélyezte-e az [előfeltételekben](#on-premises-prerequisites) leírt URL-címek elérését.
   * Ha egyéni proxyt használ, a rendszer automatikusan létrehoz egy, a megadott hitelesítő adatokat alkalmazó VMM RunAs-fiókot (DRAProxyAccount). Állítsa be úgy a proxykiszolgálót, hogy ez a fiók elvégezhesse a hitelesítést. A VMM RunAs-fiók beállításait a VMM-konzolban módosíthatja. A **Beállítások** menüben bontsa ki a **Biztonság** > **Futtató fiókok** lehetőséget, majd módosítsa a DRAProxyAccount jelszavát. A beállítás akkor lép érvénybe, ha újraindítja a VMM szolgáltatást.

     ![internet](./media/site-recovery-vmm-to-azure/provider13.PNG)
7. Fogadja el vagy módosítsa az adatok titkosításához automatikusan létrehozott SSL-tanúsítvány helyét. Ez a tanúsítvány akkor használatos, ha bekapcsolja az Azure által védett felhőre vonatkozó adattitkosítást az Azure Site Recovery portálon. Őrizze biztonságos helyen a tanúsítványt. Ha feladatátvételt végez az Azure-ba, és korábban bekapcsolta az adattitkosítást, szüksége lesz rá a titkosítás feloldásához.
8. A **Kiszolgáló neve** mezőben adjon meg egy, a tárolóban regisztrált VMM-kiszolgálót azonosító rövid nevet. Fürtkonfiguráció használata esetén adja meg a VMM-fürtszerepkör nevét.
9. Jelölje be a **Felhőmetaadatok szinkronizálása** jelölőnégyzetet, ha szeretné a VMM-kiszolgálón futó összes felhő metaadatait szinkronizálni a tárolóval. Ezt a műveletet kiszolgálónként csak egyszer szükséges elvégezni. Ha nem szeretné az összes felhőt szinkronizálni, ne jelölje be a jelölőnégyzetet, és szinkronizálja egyenként a felhőket a VMM-konzolban, a felhők tulajdonságainál. A folyamat befejezéséhez kattintson a **Regisztráció** elemre.

    ![Kiszolgáló regisztrációja](./media/site-recovery-vmm-to-azure/provider16.PNG)
10. Elindul a regisztráció. A regisztráció befejezését követően a kiszolgáló megjelenik a tároló **Beállítások** > **Kiszolgálók** paneljén.

#### <a name="command-line-installation-for-the-azure-site-recovery-provider"></a>Az Azure Site Recovery Provider telepítése a parancssorból
Az Azure Site Recovery Provider a parancssorból is telepíthető. A Provider ezzel a módszerrel a Server Core for Windows Server 2012 R2 kiadáson is telepíthető.

1. Töltse le egy mappába a Provider telepítőfájlját és a regisztrációs kulcsot. A mappa lehet például a következő: C:\ASR.
2. Rendszergazda jogú parancssorból futtassa az alábbi parancsokat a Provider telepítőjének kibontásához:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Az összetevők telepítéséhez futtassa ezt a parancsot:

            C:\ASR> setupdr.exe /i
4. Majd az alábbi parancsok futtatásával regisztrálja a kiszolgálót a tárolóban:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Az elemek magyarázata:

* **/Credentials**: kötelező paraméter, amely a regisztrációs kulcsfájl helyét határozza meg.  
* **/FriendlyName**: kötelező paraméter, amely a Hyper-V gazdakiszolgáló nevét tartalmazza úgy, ahogy az Azure Site Recovery portálon megjelenik.
* * **/EncryptionEnabled**: nem kötelező paraméter arra az esetre, ha VMM-felhőkben futó Hyper-V virtuális gépeket replikál az Azure-ba. Adja meg, hogy szeretné-e titkosítani a virtuális gépeket az Azure-ban (inaktív adatok titkosítása). A fájlnak **.pfx** kiterjesztésűnek kell lennie. A titkosítás alapértelmezés szerint ki van kapcsolva.
* **/proxyAddress**: nem kötelező paraméter, amely a proxykiszolgáló címét határozza meg.
* **/proxyport**: nem kötelező paraméter, amely a proxykiszolgáló portját határozza meg.
* **/proxyUsername**: nem kötelező paraméter, amely a proxy felhasználónevét határozza meg (ha a proxy hitelesítést igényel).
* **/proxyPassword**: nem kötelező paraméter, amely a proxykiszolgáló hitelesítéséhez szükséges jelszavát határozza meg (ha a proxy hitelesítést igényel).

### <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Az Azure Recovery Services Agent ügynök telepítése a Hyper-V gazdagépekre
1. A Provider beállítását követően töltse le az Azure Recovery Services Agent telepítőfájlját. Végezze el a telepítést a CMM-felhőben futó összes Hyper-V-kiszolgálón.

    ![Hyper-V helyek](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)
2. Az **Előfeltételek ellenőrzése** részben kattintson a **Tovább** gombra. A rendszer automatikusan telepíti a hiányzó előfeltételeket.

    ![Recovery Services Agent, előfeltételek](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)
3. A **Telepítési beállítások** részben fogadja el vagy módosítsa a telepítés, illetve a gyorsítótár helyét. A gyorsítótár már akár 5 GB-os kapacitású meghajtókon is beállítható, de javasoljuk, hogy a gyorsítótárazáshoz használt lemezen legyen legalább 600 GB szabad hely. Ezt követően kattintson a **Telepítés** gombra.
4. A telepítés befejezését követően kattintson a **Bezárás** gombra.

    ![A MARS Agent regisztrálása](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

#### <a name="command-line-installation-for-azure-site-recovery-services-agent"></a>Az Azure Site Recovery Services Agent telepítése a parancssorból
A Microsoft Azure Recovery Services Agent a következő paranccsal telepíthető a parancssorból:

     marsagentinstaller.exe /q /nu

#### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Internetes proxyn keresztüli elérés beállítása a Site Recoveryhez Hyper-V gazdagépekről
A Hyper-V gazdagépeken futó Recovery Services Agent ügynöknek a virtuális gépek replikálása céljából az interneten keresztül el kell érnie az Azure-t. Ha proxyn keresztül éri el az internetet, adja meg a következő beállításokat:

1. Nyissa meg a Microsoft Azure Backup MMC beépülő modult a Hyper-V gazdagépen. Alapértelmezés szerint a Microsoft Azure Backup parancsikonja az asztalon vagy a következő mappában érhető el: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Kattintson a beépülő modul **Tulajdonságok módosítása** elemére.
3. A **Proxykonfiguráció** lapon adja meg a proxykiszolgálóra vonatkozó információkat.

    ![A MARS Agent regisztrálása](./media/site-recovery-vmm-to-azure/mars-proxy.png)
4. Ellenőrizze, hogy az ügynök el tudja-e érni az [előfeltételek](#on-premises-prerequisites) között megadott URL-címeket.

## <a name="step-3-set-up-the-target-environment"></a>3. lépés: A célkörnyezet beállítása
Adja meg a replikációhoz használni kívánt Azure-tárfiókot, valamint az Azure-hálózatot, amelyhez az Azure virtuális gépek a feladatátvételt követően csatlakozhatnak.

1. Kattintson az **Infrastruktúra előkészítése** > **Cél** elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg, hogy a virtuális gépek átadása után melyik üzembe helyezési modellt szeretné használni.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

   ![Tárolás](./media/site-recovery-vmm-to-azure/compatible-storage.png)
4. Ha még nem hozott létre tárfiókot, és ezt szeretné most megtenni a Resource Managerben, kattintson a **+Tárfiók** elemre.  A **Tárfiók létrehozása** panelen adja meg a fiók nevét és típusát, az előfizetést, valamint a helyet. A fióknak és a Recovery Services-tárolónak ugyanazon a helyen kell lennie.

   ![Tárolás](./media/site-recovery-vmm-to-azure/gs-createstorage.png)

   Vegye figyelembe:

   * Ha a klasszikus modellt használó tárfiókot szeretne létrehozni, azt az Azure-portálon teheti meg. [További információ](../storage/storage-create-storage-account-classic-portal.md)
   * Ha Prémium szintű tárfiókot használ a replikált adatok tárolására, a helyszíni adatokban bekövetkező változásokat rögzítő replikálási naplók tárolására be kell állítania egy standard tárfiókot is.
5. Ha még nem hozott létre Azure-hálózatot, és ezt szeretné most megtenni a Resource Managerben, kattintson a **+Hálózat** elemre. A **Virtuális hálózat létrehozása** panelen adja meg a hálózat nevét, a címtartományt, az alhálózati adatokat, az előfizetést, valamint a helyet. A hálózatnak és a Recovery Services-tárolónak ugyanazon a helyen kell lennie.

   ![Network (Hálózat)](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

   Ha a klasszikus modellt használó hálózatot szeretne létrehozni, azt az Azure-portálon teheti meg. [További információk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

### <a name="configure-network-mapping"></a>Hálózatleképezés konfigurálása
* [Itt elolvashatja](#prepare-for-network-mapping) a hálózatleképezés működésének rövid ismertetését. Ha részletesebb magyarázatra van szüksége, [olvassa el ezt](site-recovery-network-mapping.md).
* Ellenőrizze, hogy a VMM-kiszolgálón futó virtuális gépek csatlakoznak-e a virtuálisgép-hálózathoz, illetve, hogy létrehozott-e legalább egy Azure virtuális hálózatot. Egyetlen Azure-hálózatra több virtuálisgép-hálózatot is le lehet képezni.

Konfigurálja az alábbiak szerint a leképezéseket:

1. A **Beállítások** > **Site Recovery-infrastruktúra** > **Hálózatleképezések** > **Hálózatleképezés** menüpontban kattintson a **+Hálózatleképezés** ikonra.

    ![Hálózatleképezés](./media/site-recovery-vmm-to-azure/network-mapping1.png)
2. A **Hálózatleképezés hozzáadása** részben válassza ki a forrás VMM-kiszolgálót, célként pedig az **Azure-t**.
3. Ellenőrizze az előfizetést, illetve a feladatok átadását követően használatos üzembe helyezési modellt.
4. A **Forráshálózat** panelen a VMM-kiszolgálóhoz társítottak listájából válassza ki azt a helyszíni virtuálisgép-hálózatot, amelyet le kíván képezni.
5. A **Célhálózat** panelen válassza ki azt az Azure-hálózatot, amelyre a létrehozott replika Azure virtuális gépek kerülnek. Ezután kattintson az **OK** gombra.

    ![Hálózatleképezés](./media/site-recovery-vmm-to-azure/network-mapping2.png)

Ez történik a hálózatleképezés megkezdésekor:

* A forrás virtuálisgép-hálózatban található meglévő virtuális gépek a leképezés elkezdésekor csatlakoznak a célhálózathoz. A forrás virtuálisgép-hálózathoz csatlakoztatott új virtuális gépek a replikálás végrehajtásakor csatlakoznak a leképezett Azure-hálózathoz.
* Meglévő hálózatleképezés módosítása esetén a replika virtuális gépek az új beállításokkal fognak csatlakozni.
* Ha a célhálózatban már több alhálózat működik, és ezek egyikének ugyanaz a neve, mint annak, amelyen a forrás virtuális gép is található, a replika virtuális gép a feladatátvételt követően ehhez a cél alhálózathoz fog csatlakozni.
* Ha nem létezik egyező nevű alhálózat, a virtuális gép a hálózat első virtuális alhálózatához csatlakozik.

## <a name="step-4-set-up-replication-settings"></a>4. lépés: A replikációs beállítások megadása
1. Új replikációs szabályzat létrehozásához kattintson az **Infrastruktúra előkészítése** > **Replikációs beállítások** > **+Létrehozás és társítás** elemre.

    ![Network (Hálózat)](./media/site-recovery-vmm-to-azure/gs-replication.png)
2. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét.
3. A **Másolás gyakorisága** elemmel meghatározhatja, hogy milyen gyakran szeretné replikálni a módosult adatokat a kezdeti replikációt követően (ez lehet 30 másodperc, 5 perc vagy 15 perc).
4. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy hány órás legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A védelemmel ellátott gépeket az időtartamon belüli bármelyik pontra visszaállíthatja.
5. Az **Alkalmazáskonzisztens pillanatkép gyakorisága** beállítás azt határozza meg, hogy milyen gyakran hozzon létre a rendszer alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat (a beállítás értéke 1 és 12 óra között változhat). A Hyper-V két különböző pillanatképtípust használ: az egyik a standard pillanatkép, amely a virtuális gép egészét lefedő növekményes pillanatképet, a másik pedig az alkalmazáskonzisztens pillanatkép, amely a virtuális gépen futó alkalmazások adatairól készült, időponthoz kötött pillanatképet jelent. Az alkalmazáskonzisztens pillanatképek a kötet árnyékmásolása szolgáltatás (VSS) segítségével garantálják, hogy az alkalmazások konzisztens állapotban legyenek a pillanatkép készítésének időpontjában. Ne feledje, hogy az alkalmazáskonzisztens pillanatképek bekapcsolása negatív hatással lesz a forrás virtuális gépeken futó alkalmazások teljesítményére. Ügyeljen rá, hogy az itt megadott érték kisebb legyen a további beállított helyreállítási pontok számánál.
6. A **Kezdeti replikáció kezdési ideje** a kezdeti replikáció kezdésének időpontját határozza meg. A replikálási folyamat az internetes sávszélességet használja, így érdemes a műveletet olyankorra ütemezni, amikor kevesen használják az internetet.
7. **Az Azure-on tárolt adatok titkosítása** beállításnál adhatja meg, hogy szeretné-e titkosítani az Azure-tárfiókban elhelyezett inaktív adatokat. Végül kattintson az **OK** gombra.

    ![Replikációs szabályzat](./media/site-recovery-vmm-to-azure/gs-replication2.png)
8. Az újonnan létrehozott szabályzatokat a rendszer automatikusan társítja a VMM-felhővel. Kattintson az **OK** gombra. A további VMM-felhőket (és a rajtuk futó virtuális gépeket) a **Beállítások** > **Replikáció** > szabályzat neve > **VMM-felhő társítása** menüpontban társíthatja az adott replikációs szabályzathoz.

    ![Replikációs szabályzat](./media/site-recovery-vmm-to-azure/policy-associate.png)

## <a name="step-5-capacity-planning"></a>5. lépés: Kapacitástervezés
Most, hogy az alapszintű infrastruktúra készen áll, fogjon hozzá a kapacitás megtervezéséhez, és döntse el, hogy szüksége van-e további erőforrásokra.

A Site Recovery Capacity Planner szolgáltatása segít a forráskörnyezethez, a Site Recovery-összetevőkhöz, valamint a hálózathoz és a tárterülethez szükséges erőforrások lefoglalásában. A Planner segédprogram gyors módját használva a virtuális gépek, a lemezek, a tárhely átlagos számán alapuló becsléseket kérhet, vagy használhatja a részletes módot, amelyben a különböző számítási feladatok szintjén Ön adhatja meg a számokat. Előkészületek:

* Gyűjtse össze a replikálási környezetre vonatkozó adatokat, többek között a virtuális gépeket, a virtuális gépekhez tartozó lemezeket, valamint a lemezenkénti tárterületeket.
* Becsülje meg a replikált adatokra vonatkozóan a napi adatváltozás (forgalom) mértékét. Ebben a [Capacity Planner for Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) is segítséget nyújthat.

1. A segédprogram letöltéséhez kattintson a **Download** (Letöltés) gombra, majd futtassa a letöltött fájlt. Olvassa el a segédprogramhoz kapcsolódó [cikket](site-recovery-capacity-planner.md).
2. Ha elkészült, a **Have you run the Capacity Planner?** (Lefuttatta a Capacity Plannert?) kérdésnél válassza a **Yes** (Igen) lehetőséget.

   ![Kapacitástervezés](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>Hálózati sávszélességgel kapcsolatos szempontok
A Capacity Planner segítségével a replikáláshoz (a kezdeti replikáláshoz, majd a változások szinkronizálásához) szükséges sávszélességet is kiszámíthatja. A replikáláshoz felhasznált sávszélesség mértékének szabályozására a következő lehetőségek állnak rendelkezésre:

* **Throttle bandwidth** (Sávszélesség szabályozása): a másodlagos helyre replikációt végző Hyper-V-forgalom egy konkrét Hyper-V-gazdagépen halad keresztül. A gazdakiszolgálón beállíthatja a sávszélesség szabályozását.
* **Tweak bandwidth** (Sávszélesség finomhangolása): beállításkulcsok segítségével is szabályozhatja a replikáláshoz használt sávszélességet.

#### <a name="throttle-bandwidth"></a>Sávszélesség szabályozása
1. Nyissa meg a Microsoft Azure Backup MMC beépülő modult a Hyper-V gazdakiszolgálón. Alapértelmezés szerint a Microsoft Azure Backup parancsikonja az asztalon vagy a következő mappában érhető el: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Kattintson a beépülő modul **Tulajdonságok módosítása** elemére.
3. A **Szabályozás** lapon jelölje be **A biztonsági mentési műveletek internetes sávszélességének szabályozása** jelölőnégyzetet, majd állítsa be a munkaórákra és a munkaórákon kívüli időre érvényes korlátozást. 512 kB/s és 102 MB/s közötti értéket adhat meg.

    ![Sávszélesség szabályozása](./media/site-recovery-vmm-to-azure/throttle2.png)

A szabályozáshoz a [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) parancsmag is használható. Íme egy példa:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

A **Set-OBMachineSetting -NoThrottle** beállítás azt jelenti, hogy nincs szükség szabályozásra.

#### <a name="influence-network-bandwidth"></a>A hálózati sávszélesség szabályozása
Az **UploadThreadsPerVM** beállításazonosító szabályozza a lemezen adatátvitelre (kezdeti vagy változásreplikálásra) használt szálak számát. Minél magasabb a beállítás értéke, annál nagyobb hálózati sávszélességet használ a rendszer a replikáláshoz. A **DownloadThreadsPerVM** beállításazonosító határozza meg, hogy a rendszer hány szálat használ az adatátvitelhez a feladatátvétel során.

1. Lépjen a beállításjegyzék következő helyére: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.

   * Az **UploadThreadsPerVM** beállítás értékének módosításával (vagy ha nem létezik, létrehozásával) szabályozhatja a lemezek replikálásához használt szálak számát.
   * A **DownloadThreadsPerVM** beállítás értékének módosításával (vagy ha nem létezik, létrehozásával) szabályozhatja az Azure felől érkező feladatátvételi forgalomhoz használt szálak számát.
2. Az alapértelmezett érték a 4. A szükségesnél több erőforrással ellátott hálózatban érdemes módosítani a beállításazonosítók alapértelmezett értékét. A maximális érték a 32. Az optimális érték kiválasztásához kövesse figyelemmel a forgalmat.

## <a name="step-6-enable-replication"></a>6. lépés: A replikáció engedélyezése
Most már engedélyezheti a replikációt a következők szerint:

1. Kattintson **2. lépés: Az alkalmazás replikálása** > **Forrás** elemre. A replikálás első alkalommal történő engedélyezését követően kattintson a tárolóban elérhető **+Replikálás** elemre a további gépek replikációjának engedélyezéséhez.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication1.png)
2. A **Forrás** panelen válassza ki a VMM-kiszolgálót és a felhőt, amelyben a Hyper-V gazdagépek futnak. Ezután kattintson az **OK** gombra.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication-source.png)
3. A **Cél** résznél válassza ki az előfizetést, a feladatátvételt követő üzembe helyezési modellt, valamint a replikált adatok tárolására használni kívánt tárfiókot.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication-target.png)
4. Válassza ki a használni kívánt tárfiókot. Ha a rendelkezésre álló tárfiókok helyett egy másikat szeretne használni, [hozza létre](#set-up-an-azure-storage-account). A tárfiók Resource Manager-modell használatával történő létrehozásához kattintson az **Új létrehozása** elemre. Ha a klasszikus modellt használó tárfiókot szeretne létrehozni, azt az [Azure-portálon teheti meg](../storage/storage-create-storage-account-classic-portal.md). Ezután kattintson az **OK** gombra.
5. Válassza ki azt az Azure-hálózatot, valamint alhálózatot, amelyhez a feladatátvételt követően felálló Azure virtuális gépek csatlakozni fognak. Ha a megadott hálózati beállításokat az összes védelemre kijelölt gépre szeretné alkalmazni, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Ha az egyes gépeknél külön-külön szeretné beállítani az Azure-hálózatot, kattintson a **Beállítás később** elemre. Ha a rendelkezésre álló hálózatok helyett másikat szeretne használni, [hozza létre](#set-up-an-azure-network). A hálózat Resource Manager-modell használatával történő létrehozásához kattintson az **Új létrehozása** elemre. Ha a klasszikus modellt használó hálózatot szeretne létrehozni, azt az [Azure-portálon](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) teheti meg. Ha szükséges, válassza ki az alhálózatot. Ezután kattintson az **OK** gombra.
6. A **Virtuális gépek** > **Virtuális gépek kijelölése** menüben kattintással jelölje ki a replikálni kívánt virtuális gépeket. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Végül kattintson az **OK** gombra.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication5.png)
7. A **Tulajdonságok** > **Tulajdonságok konfigurálása** menüben válassza ki a kijelölt virtuális gépek operációs rendszerét, valamint az operációsrendszer-lemezképet. Végül kattintson az **OK** gombra. A további tulajdonságokat később is beállíthatja.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication6.png)
8. A **Replikációs beállítások** > **Replikációs beállítások konfigurálása** menüben válassza ki a védett virtuális gépekre alkalmazni kívánt replikációs beállításokat. Végül kattintson az **OK** gombra. A replikációs szabályzatot a **Beállítások** > **Replikációs házirendek** > szabályzat neve > **Beállítások szerkesztése** menüpontban módosíthatja. Az itt megadott módosítások a már replikálás alatt álló, illetve az újonnan hozzáadott gépekre egyaránt érvényesek.

   ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication7.png)

A **Védelem engedélyezése** feladat előrehaladását a **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.

### <a name="view-and-manage-vm-properties"></a>A virtuális gépek tulajdonságainak megtekintése és kezelése
Javasoljuk, hogy ellenőrizze a forrásgépek tulajdonságait. Ne feledje, hogy az Azure virtuális gép nevének meg kell felelnie az [Azure virtuális gépekre vonatkozó követelményeknek](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Kattintson a **Beállítások** > **Védett elemek** > **Replikált elemek** elemre, majd a részletek megtekintéséhez válassza ki a kívánt gépet.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/vm-essentials.png)
2. A **Tulajdonságok** résznél tekintheti meg a virtuális gép replikációs és feladatátvételi adatait.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/test-failover2.png)
3. A **Számítás és hálózat** > **Számítási tulajdonságok** résznél adhatja meg az Azure virtuális gép nevét és a cél méretét. Ha szükséges, írja át úgy a nevet, hogy az megfeleljen az [Azure-követelményeknek](site-recovery-best-practices.md#azure-virtual-machine-requirements). Ezenfelül itt a célhálózatra, az alhálózatra, valamint az Azure virtuális géphez rendelt IP-címre vonatkozó információk is megtekinthetők. Vegye figyelembe:

   * A cél IP-címe beállítható. Ha nem ad meg címet, a gép, amelynek a feladatait átadja, a DHCP-t fogja használni. Ha olyan címet ad meg, amely nem használható feladatátadásra, a feladatátvételi művelet sikertelen lesz. A cél IP-címe feladatátvételi tesztre is használható, amennyiben a cím elérhető a feladatátvételi teszt hálózatában.
   * A hálózati adapterek számát a cél virtuális gépek mérete határozza meg, a következők szerint:

     * Ha a forrásgépen működő hálózati adapterek száma kisebb vagy egyenlő a célgép méretéhez engedélyezett adapterek számával, a célon ugyanannyi adapter fog működni, mint a forráson.
     * Ha a forrás virtuális gépek adaptereinek száma meghaladja a célmérethez engedélyezett maximumot, a rendszer a célmérethez engedélyezett maximális számot használja.
     * Ha például a forrásgépen két hálózati adapter működik, és a célgép mérete négy adapter használatát teszi lehetővé, a célgépen két adapter fog működni. Ha azonban a forrásgépen két adapter működik, de a célgép mérete csupán egy adaptert támogat, akkor a célgépen csak egy adapter fog működni.     
     * Ha a virtuális gépen több hálózati adapter működik, azok mindegyike ugyanahhoz a hálózathoz fog csatlakozni.

     ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/test-failover4.png)
4. A **Lemezek** résznél láthatja a replikálandó virtuális gépekhez tartozó operációsrendszer- és adatlemezeket.

## <a name="step-7-test-your-deployment"></a>7. lépés: Az üzemelő példány tesztelése
Az üzemelő példány kipróbálásához futtasson feladatátvételi tesztet egy virtuális gépen, vagy alkalmazzon egy vagy több virtuális gépet tartalmazó helyreállítási tervet.

### <a name="prepare-for-failover"></a>A feladatátvétel előkészítése
* A feladatátvételi teszt futtatásához javasoljuk, hogy hozzon létre egy új, az éles Azure-hálózattól elkülönített Azure-hálózatot. Az új Azure-hálózatok létrehozásakor ez az alapértelmezés. [Itt részletesebben olvashat](site-recovery-failover.md#run-a-test-failover) a feladatátvételi teszt futtatásáról.
* Hogy az Azure-ba irányuló feladatátadás során a lehető legjobb eredményt érhesse el, telepítse a védett gépre az Azure Agent ügynököt. Ez az ügynök felgyorsítja a rendszerindítást, és segít a hibák elhárításában. Telepítse a megfelelő [linuxos](https://github.com/Azure/WALinuxAgent) vagy [windowsos](http://go.microsoft.com/fwlink/?LinkID=394789) ügynököt.
* Az üzemelő példány megfelelő teszteléséhez az szükséges, hogy a replikált gép infrastruktúrája pontosan az elvárások szerint működjön. Ha az Active Directoryt és a DNS-t szeretné tesztelni, hozzon létre egy tartományvezérlőként működő virtuális gépet DNS-szel, és replikálja azt az Azure-ba az Azure Site Recovery használatával. További információk az [Active Directoryra vonatkozó feladatátvételi szempontokat részletező cikkben](site-recovery-active-directory.md#test-failover-considerations).
* Ha a feladatátvételi teszt helyett nem tervezett feladatátvételt szeretne futtatni, vegye figyelembe a következőket:

  * Ha lehetséges, a nem tervezett feladatátvétel előtt állítsa le az elsődleges gépeket. Ezzel gondoskodik róla, hogy a forrás és a replika gépek nem fognak egyszerre futni.
  * A nem tervezett feladatátvétel leállítja az elsődleges gépek adatreplikációját, így a nem tervezett feladatátvétel elindulását követő változásadatokat nem továbbítja a rendszer. Ezenfelül ha helyreállítási terv keretében futtat nem tervezett feladatátvételt, az teljesen végigfut, amíg hiba nem történik.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően
Ha a feladatátvételt követően RDP segítségével szeretne kapcsolódni az Azure virtuális gépekhez, tegye meg a következőket:

**A helyszíni gépen, a feladatátvétel előtt**:

* Az interneten keresztüli eléréshez engedélyezze az RDP-t, ellenőrizze, hogy a **Nyilvános** résznél felvette-e a listára a TCP- és UDP-szabályokat, valamint, hogy a **Windows tűzfal** -> **Engedélyezett alkalmazások és szolgáltatások** területén az összes profil számára engedélyezve van-e az RDP.
* Helyek közötti kapcsolaton keresztüli eléréshez engedélyezze az RDP-t a gépen, és ellenőrizze, hogy a **Windows tűzfal** -> **Engedélyezett alkalmazások és szolgáltatások** területén engedélyezve van-e az RDP a **Tartomány** és a **Privát** hálózatok számára.
* Telepítse az [Azure virtuális gépet kezelő ügynököt](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) a helyszíni gépre.
* Az operációs rendszer tárolóhálózati szabályzatát állítsa OnlineAll értékre. [További információ](https://support.microsoft.com/kb/3031135)
* A feladatátvétel futtatása előtt kapcsolja ki az IPSec szolgáltatást.

**Az Azure virtuális gépen, a feladatátvételt követően**:

* Vegyen fel egy nyilvános végpontot az RDP protokollba (3389-es port), és adja meg a bejelentkezéshez szükséges hitelesítő adatokat.
* Ellenőrizze, hogy nincsenek-e érvényben tartományi szabályzatok, amelyek meggátolják, hogy nyilvános cím segítségével csatlakozzon a virtuális gépekhez.
* Próbáljon meg csatlakozni. Ha nem sikerül, ellenőrizze, hogy fut-e a virtuális gép. További hibaelhárítási tippekért olvassa el ezt a [cikket](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Ha a feladatátvételt követően a Secure Shell-ügyfél (SSH) segítségével szeretné elérni az Azure virtuális gépet, tegye a következőket:

**A helyszíni gépen, a feladatátvétel előtt**:

* Ellenőrizze, hogy az Azure virtuális gépen beállította-e, hogy a Secure Shell szolgáltatás rendszerindításkor automatikusan elinduljon.
* Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.

**Az Azure virtuális gépen, a feladatátvételt követően**:

* Az Azure-alhálózaton, amelyhez a feladatait átadó virtuális gép csatlakozik, illetve magán a gépen érvényes hálózati biztonsági csoport szabályainak engedélyezniük kell az SSH-porthoz való csatlakozást.
* Hozzon létre nyilvános végpontot, amely lehetővé teszi a bejövő kapcsolatok számára az SSH-port (alapértelmezés szerint a 22-es TCP-port) használatát.
* Ha a virtuális géphez VPN-kapcsolaton (az Express Route vagy helyek közötti VPN segítségével) keresztül fér hozzá, az ügyfél segítségével is közvetlen SSH-kapcsolatot létesíthet a virtuális géppel.

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása
1. A **Beállítások** > **Replikált elemek** listáján szereplő egyik virtuális gép feladatainak átadásához kattintson a virtuális gépre, majd a **+Feladatátvételi teszt** elemre.
2. Helyreállítási terv feladatátadásához a **Beállítások** > **Helyreállítási tervek** menüben kattintson jobb gombbal a kívánt tervre, majd válassza a **Feladatátvételi teszt** lehetőséget. Helyreállítási terv létrehozásához [kövesse ezeket az utasításokat](site-recovery-create-recovery-plans.md).
3. A **Feladatátvételi teszt** résznél válassza ki, hogy az Azure virtuális gépek melyik Azure-hálózathoz csatlakozzanak a feladatátvételt követően.
4. A feladatátvételi művelet elindításához kattintson az **OK** gombra. A folyamat előrehaladásának megtekintéséhez kattintson a virtuális gépre, és nyissa meg a tulajdonságait, vagy a **Beállítások** > **Site Recovery-feladatok** menüben figyelje a **Feladatátvételi teszt** feladat állapotát.
5. Ha a feladatátvétel elérte a **Teszt befejezése** fázist, tegye a következőket:

   1. Tekintse meg a replika virtuális gépet az Azure portálon. Ellenőrizze, hogy a virtuális gép sikeresen elindul-e.
   2. Ha beállította a virtuális gépek helyszíni hálózatról való elérését, távoli asztali kapcsolatot is kezdeményezhet a virtuális géppel.
   3. A befejezéshez kattintson a **Complete the test** (Teszt befejezése) elemre.
   4. A feladatátvételi teszttel kapcsolatos megfigyelések feljegyzéséhez és mentéséhez kattintson a **Jegyzetek** elemre.
   5. Kattintson **A feladatátvételi teszt befejeződött** elemre. Számolja fel a tesztkörnyezetet. lehetőségre a teszteléshez használt virtuális gép automatikus kikapcsolásához és törléséhez.
   6. A rendszer ekkor a Site Recovery által a feladatátvételi teszt keretében automatikusan létrehozott összes elemet és virtuális gépet eltávolítja. A feladatátvételi teszthez Ön által létrehozott elemeket a rendszer nem törli.

      > [!NOTE]
      > A szolgáltatás kényszeríti a két hétnél tovább tartó feladatátvételi tesztek befejezését.
      >
      >
6. A feladatátvétel befejezését követően a replika Azure-gépnek meg kell jelennie az Azure Portal > **Virtuális gépek** részében. Ellenőrizze, hogy a virtuális gép mérete megfelelő-e, hogy a gép a megfelelő hálózathoz csatlakozik-e, és fut-e.
7. Ha [elvégezte a feladatátvételt követő csatlakozáshoz szükséges előkészületeket](#prepare-to-connect-to-Azure-VMs-after-failover), most tudnia kell csatlakozni az Azure virtuális géphez.

## <a name="monitor-your-deployment"></a>Az üzemelő példány figyelése
A Site Recoveryben üzemelő példány konfigurációjának és állapotának figyeléséhez tegye a következőket:

1. Kattintson a tároló nevére az **Alapvető erőforrások** irányítópult megnyitásához. Itt megtalálja a Site Recovery-feladatokat, a replikációs állapotot, a replikálási terveket, a kiszolgáló állapotát, valamint az eseményeket.  Az **Alapvető erőforrások** irányítópultot testre szabva beállíthatja, hogy az Önnek legfontosabb csempék és elrendezések jelenjenek meg, így akár más Site Recovery- és Backup-tárolók állapotát is gyorsan megtekintheti.

    ![Alapvető erőforrások](./media/site-recovery-vmm-to-azure/essentials.png)
2. Az **Állapot** csempén nyomon követheti a helykiszolgálók (VMM- vagy konfigurációs kiszolgálók) problémáit, illetve megtekintheti a Site Recovery által az elmúlt 24 órában rögzített eseményeket.
3. A replikációt a **Replikált elemek**, a **Helyreállítási tervek** és a **Site Recovery-feladatok** csempéken felügyelheti és figyelheti. A feladatokat részletesen is megtekintheti a **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban.

## <a name="next-steps"></a>Következő lépések
Ha sikerült beállítania és elindítani az üzemelő példányt, [ismerkedjen meg részletesebben](site-recovery-failover.md) a feladatátvételi különféle típusaival.



<!--HONumber=Nov16_HO2-->


