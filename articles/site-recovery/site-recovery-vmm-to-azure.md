<properties
    pageTitle="VMM-felhőben lévő Hyper-V virtuális gépek replikálása az Azure-ba az Azure Site Recovery és az Azure portál használatával | Microsoft Azure"
    description="A cikkből megtudhatja, hogyan helyezze üzembe az Azure Site Recovery-t a VMM-felhőben futó Hyper-V virtuális gépeknek az Azure-ba történő replikálása, feladatátvétele és helyreállítása érdekében, az Azure portál használatával."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/10/2016"
    ms.author="raynew"/>

# VMM-felhőben lévő Hyper-V virtuális gépek replikálása az Azure-ba az Azure Site Recovery és az Azure portál használatával | Microsoft Azure

> [AZURE.SELECTOR]
- [Azure portál](site-recovery-vmm-to-azure.md)
- [Klasszikus Azure portál](site-recovery-vmm-to-azure-classic.md)
- [PowerShell ARM](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Klasszikus PowerShell](site-recovery-deploy-with-powershell.md)

Az Azure Site Recovery szolgáltatás üdvözli Önt! Ebből a cikkből megtudhatja, hogyan replikálhat System Center Virtual Machine Manager- (VMM-) felhőkben felügyelt helyszíni Hyper-V virtuális gépeket az Azure-ba az Azure portál Azure Site Recovery szolgáltatása segítségével.

> [AZURE.NOTE] Az Azure két különböző [üzembe helyezési modellt](../resource-manager-deployment-model
> ) kínál az erőforrások létrehozására és kezelésére: az Azure Resource Manager (ARM) modellt és a klasszikus modellt. Az Azure-ban két különböző portál érhető el: a klasszikus Azure portál, amely a klasszikus üzembe helyezési modellt támogatja, és az Azure portál, amely mindkét modellt támogatja. 


Az Azure portál Azure Site Recovery szolgáltatása több új funkciót kínál:

- Az Azure portálon az Azure Backup és az Azure Site Recovery szolgáltatást mostantól egy közös Recovery Services-tároló tartalmazza, így az üzletmenet-folytonossági és vészhelyreállítási (BCDR) funkciók egyetlen helyről kezelhetők. Az új, egységesített irányítópult lehetővé teszi a helyszíni helyeken, valamint a nyilvános Azure-felhőn futó műveletek figyelését és kezelését.
- Az Azure-előfizetésben kiépített Cloud Solution Provider (CSP) programmal rendelkező felhasználók mostantól az Azure portálon kezelhetik a Site Recovery-műveleteket.
- Az Azure portálon futó Site Recovery mostantól ARM-tárfiókokra is képes gépeket replikálni. Feladatátvétel során a Site Recovery ARM-alapú virtuális gépeket hoz létre az Azure-ban.
- A Site Recovery továbbra is támogatja a klasszikus tárfiókokba történő replikálást. Feladatátvétel során a Site Recovery a klasszikus modellel hozza létre a virtuális gépeket.


A cikk elolvasása után felmerülő megjegyzéseit alul, a Disqus-beszélgetésben teheti közzé. Műszaki jellegű kérdéseit az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) tegye fel.

## Áttekintés

A legtöbb vállalatnál szükség van üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiára, amely meghatározza, hogy hogyan tudnak az alkalmazások, a számítási feladatok és az adatok üzemben maradni a tervezett és nem tervezett leállások során, illetve, hogy hogyan lehet minél gyorsabban visszaállni a normál működésre. Fontos, hogy a BCDR stratégia gondoskodjon a vállalati adatok biztonságáról és helyreállíthatóságáról, és lehetővé tegye, hogy a számítási feladatok még vészhelyzet esetén se álljanak le.

Az Azure Site Recovery szolgáltatása a helyszíni fizikai kiszolgálóknak és virtuális gépeknek az Azure-felhőbe vagy egy másodlagos adatközpontba történő replikálásával segít a stratégia kidolgozásában. Ha az elsődleges helyen valamilyen okból kimaradás lép fel, a rendszer átadja a feladatokat a másodlagos helynek, így az alkalmazások és számítási feladatok nem állnak le. Ha az elsődleges helyen helyreáll a normál működés, a rendszer visszaadja a feladatokat. További információk: [What is Azure Site Recovery?](site-recovery-overview.md) (Mire használható az Azure Site Recovery szolgáltatás?)

Ebben a cikkben megtalál minden, a VMM-felhőkben futó helyszíni Hyper-V virtuális gépeknek az Azure-ba történő replikálásával kapcsolatos információt. A leírásban szerepel az architektúra áttekintése, emellett tervezéssel kapcsolatos információk, valamint az Azure, a helyszíni kiszolgálók, a replikációs beállítások és a kapacitástervezés konfigurálásához szükséges lépések. Az infrastruktúra kiépítését követően engedélyezheti a védeni kívánt gépek replikálását, és ellenőrizheti, hogy működik-e a feladatátvétel.


## Üzleti előnyök

- A Site Recovery távoli védelmet biztosít a Hyper-V virtuális gépeken futó üzleti számítási feladatok és alkalmazások számára. 
- A Recovery Services portálon egyetlen helyen beállíthatja, kezelheti és figyelheti a replikációt, a feladatátvételt és a helyreállítást. 
- Helyszíni infrastruktúrájában könnyedén elvégezheti a feladatok átadását az Azure-nak, majd visszaadhatja (helyreállíthatja) a feladatokat az Azure-ból a helyszíni Hyper-V gazdakiszolgálóknak. 
- A helyreállítási tervbe akár több gépet is bevonhat, így az egy szinthez tartozó alkalmazások számítási feladatai együtt hajtják végre a feladatátvételt. 


## Forgatókönyv-architektúra


A forgatókönyv az alábbi elemekből áll: 

- **VMM-kiszolgáló**: egy vagy több felhővel ellátott helyszíni VMM-kiszolgáló.
- **Hyper-V gazdagép vagy fürt**: VMM-felhőkben felügyelt Hyper-V gazdakiszolgálók vagy fürtök.
- **Azure Site Recovery Provider és Recovery Services Agent**: az üzembe helyezés során az Azure Site Recovery Providert a VMM-kiszolgálóra, míg a Microsoft Azure Recovery Services Agent ügynököt a Hyper-V gazdakiszolgálókra kell telepíteni. A VMM-kiszolgálóra telepített Provider HTTPS-protokollon, a 443-as porton keresztül kommunikál a Site Recoveryvel a replikáció előkészítéséhez. A Hyper-V gazdakiszolgálóra telepített ügynök alapértelmezés szerint HTTPS-protokollal, a 443-as porton replikálja az adatokat az Azure-tárterületre.
- **Azure**: szüksége lesz egy Azure-előfizetésre, egy Azure-tárfiókra a replikált adatok tárolásához, valamint egy Azure virtuális hálózatra, hogy az Azure virtuális gépek hálózati kapcsolata a feladatátvételt követően is megmaradjon.

![E2A topológia](./media/site-recovery-vmm-to-azure/architecture.png)


## Azure-előfeltételek

A forgatókönyv megvalósításához a következőkre lesz szüksége az Azure-ban.

**Előfeltétel** | **Részletek**
--- | ---
**Azure-fiók**| Szüksége lesz egy [Microsoft Azure](http://azure.microsoft.com/)-fiókra. Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is. [További információk](https://azure.microsoft.com/pricing/details/site-recovery/) a Site Recovery díjszabásáról. 
**Azure Storage-tárterület** | A replikált adatok tárolásához standard Azure-tárfiókra lesz szüksége. LRS- vagy GRS-tárfiókot is használhat. Mi a GRS használatát javasoljuk, mivel ez akár regionális kimaradás során, illetve az elsődleges régió helyreállíthatatlansága esetében gondoskodik az adatok hibatűréséről. [További információk](../storage/storage-redundancy.md). A fióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.<br/><br/>Prémium szintű Storage-fiók nem használható.<br/><br/> A replikált adatokat az Azure Storage tárolja, ha pedig feladatátvétel következik be, a rendszer Azure virtuális gépeket hoz létre. <br/><br/> [Itt részletesebben olvashat](../storage/storage-introduction.md) az Azure Storage-ről.
**Azure-hálózat** | Szüksége lesz egy Azure virtuális hálózatra, amelyhez az Azure virtuális gépek tudnak csatlakozni, ha a rendszer feladatátvételt hajt végre. A hálózatnak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.

## Helyszíni előfeltételek

Az alábbiakra lesz szüksége a helyszínen.

**Előfeltétel** | **Részletek**
--- | ---
**VMM**| Egy vagy több, a System Center 2012 R2 verziójával futó VMM-kiszolgáló. Minden VMM-kiszolgálón legalább egy felhőnek kell üzemelnie. A felhők tartalmazzák a következőket:<br/><br/> Legalább egy VMM-gazdagépcsoport.<br/><br/> Minden gazdagépcsoportban legalább egy Hyper-V gazdakiszolgáló vagy fürt.<br/><br/>[Itt részletesebb tájékoztatást talál](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html) a VMM-felhők létrehozásával kapcsolatban.
**Hyper-V** | A Hyper-V gazdakiszolgálókon legalább a Windows Server 2012 R2 verziónak kell futnia, a Hyper-V szerepkört telepíteni kell rájuk, ezenfelül a legújabb frissítésekkel is el kell látni őket.<br/><br/> Minden Hyper-V kiszolgáló tartalmazzon legalább egy virtuális gépet.<br/><br/> A replikálni kívánt virtuális gépeket tartalmazó Hyper-V gazdakiszolgálót vagy fürtöt a VMM-felhőnek kell felügyelnie.<br/><br/>A Hyper-V kiszolgálókon közvetlen vagy proxyn keresztülmenő internetkapcsolatnak kell rendelkezésre állnia.<br/><br/>A Hyper-V kiszolgálókra telepíteni kell a [2961977](https://support.microsoft.com/kb/2961977) számú cikkben említett javításokat.<br/><br/>Az adatoknak az Azure-ba történő replikálásához a Hyper-V gazdakiszolgálóknak el kell érniük az internetet. 
**Provider és Agent** | Az Azure Site Recovery üzembe helyezése során az Azure Site Recovery Providert a VMM-kiszolgálóra, míg a Microsoft Azure Recovery Services Agent ügynököt a Hyper-V gazdagépekre kell telepíteni. A Provider és az Agent közvetlen vagy proxyn keresztüli internetkapcsolatot igényel az Azure-hoz való csatlakozáshoz. HTTPS-alapú proxyk nem használhatók. A VMM-kiszolgálón és a Hyper-V gazdagépeken használt proxykiszolgálónak engedélyeznie kell a következők elérését: <br/><br/> *.hypervrecoverymanager.windowsazure.com <br/><br/> *.accesscontrol.windows.net <br/><br/> *.backup.windowsazure.com <br/><br/> *.blob.core.windows.net <br/><br/> *.store.core.windows.net<br/><br/>Ha a VMM-kiszolgálón IP-címeken alapuló tűzfalszabályok működnek, ellenőrizze, hogy a szabályok engedélyezik-e az Azure-ral való kommunikációt. Engedélyeznie kell az [Azure Datacenter IP-tartományait](https://www.microsoft.com/download/confirmation.aspx?id=41653), illetve a HTTPS protokollt (a 433-as porton).<br/><br/>Engedélyezze az előfizetéshez tartozó Azure-régió, illetve az USA nyugati régiójának IP-tartományait.<br/><br/>Ezenfelül a VMM-kiszolgálón használt proxykiszolgálónak el kell tudnia érni a következőt is: https://www.msftncsi.com/ncsi.txt.


## A védett gépre vonatkozó előfeltételek


**Előfeltétel** | **Részletek**
--- | ---
**Védett virtuális gépek** | Virtuális gép feladatainak átadása csak akkor lehetséges, ha az Azure virtuális géphez rendelt név megfelel az [Azure-előfeltételeknek](site-recovery-best-practices.md#azure-virtual-machine-requirements). A virtuális gép replikációjának bekapcsolását követően módosíthatja a nevet. <br/><br/> A védett gépek önálló lemezeinek kapacitása nem haladhatja meg az 1023 GB-t. Egy virtuális gépen legfeljebb 64 lemez működhet (ami 64 TB kapacitást jelent).<br/><br/> Közös lemezes vendégfürtök nem használhatók.<br/><br/> Az UEFI/EFI típusú rendszerindítás nem használható.<br/><br/> Ha a forrás virtuális gépen összevonták a hálózati adaptereket, az Azure-ba történő feladatátadást követően a rendszer azokat egyetlen hálózati adapterré konvertálja.<br/><br/>A statikus IP-című linuxos virtuális gépek számára nem biztosítható védelem.

## Felkészülés az üzembe helyezésre

Az üzembe helyezésre az alábbi műveletekkel készülhet fel:

1. [Állítsa be azt az Azure-hálózatot](#set-up-an-azure-network), amelyre a rendszer 
2. feladatátvételt követően áthelyezi az Azure virtuális gépeket. 
2. [Állítsa be az Azure-tárfiókot](#set-up-an-azure-storage-account) a replikált adatok tárolásához.
4. [Készítse elő a VMM-kiszolgálót](#prepare-the-vmm-server) a Site Recovery üzembe helyezésére. 
5. [Készüljön elő a hálózatleképezésre](#prepare-for-network-mapping). Állítsa be úgy a hálózatokat, hogy a Site Recovery üzembe helyezésekor lehessen konfigurálni a hálózatleképezéseket.

### Azure-hálózat beállítása

Az Azure-hálózatra azért van szükség, hogy a feladatátvételt követően létrehozott Azure virtuális gépek csatlakozni tudjanak hozzá.

- A hálózat legyen ugyanabban a régióban, mint az a hálózat, amelyen a Recovery Services-tárolót üzembe helyezi.
- Az átadott Azure virtuális gépekhez használni kívánt erőforrásmodelltől függően az Azure-hálózatot is [ARM üzemmódban](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) vagy [klasszikus üzemmódban](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) kell beállítani.
- Javasoljuk, hogy a kezdés előtt hozza létre a hálózatot. Ha most kihagyja ezt a lépést, a Site Recovery üzembe helyezése közben kell majd elvégeznie.


### Azure-tárfiók beállítása

- Az Azure-ba replikált adatok tárolásához standard Azure-tárfiókra lesz szüksége. A fióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.
- Az átadott Azure virtuális gépekhez használni kívánt erőforrásmodelltől függően a fiókot is [ARM üzemmódban](../storage/storage-create-storage-account.md) vagy [klasszikus üzemmódban](../storage/storage-create-storage-account-classic-portal.md) kell beállítani.
- Javasoljuk, hogy még a kezdés előtt állítsa be a fiókot. Ha most kihagyja ezt a lépést, a Site Recovery üzembe helyezése közben kell majd elvégeznie.

### A VMM-kiszolgáló előkészítése

- Ellenőrizze, hogy a VMM-kiszolgáló megfelel-e az [előfeltételeknek](#on-premises-prerequisites).
- A Site Recovery üzembe helyezése során megadhatja, hogy a VMM-kiszolgálón futó összes felhő elérhető legyen-e az Azure portálon. Ha azt szeretné, hogy csak bizonyos felhők jelenjenek meg a portálon, ezt a beállítást a felhőn, a VMM felügyeleti konzoljában adhatja meg. 


### A hálózatleképezés előkészítése

A Site Recovery leképezése során hálózatleképezést is be kell állítania. A hálózatleképezés kapcsolatot hoz létre a forrás VMM-virtuálisgép-hálózatok és a cél Azure-hálózatok között, így biztosítja a következőket:

- A feladataikat azonos hálózatba átadó gépek képesek lesznek csatlakozni egymáshoz, még akkor is, ha nem ugyanazzal a módszerrel vagy ugyanabban a helyreállítási tervben hajtották végre a feladatátvételt.
- Ha a cél Azure-hálózatban hálózati átjáró működik, az Azure virtuális gépek képesek csatlakozni a helyszíni virtuális gépekhez.
- A hálózatleképezés beállításához a következő műveletek elvégzésével készülhet elő:

    - Ellenőrizze, hogy a forrás Hyper-V gazdakiszolgálón futó virtuális gépek csatlakoznak-e egy VMM-virtuálisgép-hálózathoz. Ezt a hálózatot kösse össze egy, a felhőhöz társított logikai hálózattal.
    - Rendelkezzen egy, a [fentiekben](#set-up-an-azure-network) leírt Azure-hálózattal.

- [További információk](site-recovery-network-mapping.md) a hálózatleképezés működéséről.


## Recovery Services-tároló létrehozása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Kattintson az **Új** > **Felügyelet** > **Recovery Services** elemre. Vagy kattintson a **Tallózás** > **Recovery Services**-tárolók > **Hozzáadás** elemre.

    ![Új tároló](./media/site-recovery-vmm-to-azure/new-vault3.png)

3. A **Név** mezőben adjon meg egy, a tárolót azonosító rövid nevet. Ha egynél több előfizetéssel rendelkezik, válasszon egyet ezek közül.
4. [Hozzon létre új erőforráscsoportot](../resource-group-portal.md#create-resource-group), vagy válasszon ki egy meglévőt. Válassza ki a kívánt Azure-régiót. A gépeket ebbe a régióba replikálja a rendszer. A támogatott régiók megtekintéséhez olvassa el az [Azure Site Recovery – Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/) című cikknek a földrajzi elérhetőséggel foglalkozó részét.
4. Ha szeretne gyors hozzáférést a tárolóhoz az irányítópultról, kattintson a **Rögzítés az irányítópulton** lehetőségre, majd a **Tároló létrehozása** elemre.

    ![Új tároló](./media/site-recovery-vmm-to-azure/new-vault-settings.png)

Az új tároló megjelenik az **Irányítópult** > **Minden erőforrás** menüben, illetve a központi **Recovery Services-tárolók** panelen.

## Bevezetés

A Site Recovery szolgáltatás Bevezetés funkciója segítségével a lehető leggyorsabban üzembe helyezheti a szolgáltatást. A Bevezetés funkció ellenőrzi az előfeltételeket, és végigvezeti a felhasználót a Site Recovery üzembe helyezési lépésein.

Az útmutatás keretében ki kell választania a replikálni kívánt gépek típusát, valamint a replikáció célját. Be kell állítania a helyszíni kiszolgálókat, az Azure-tárfiókokat, illetve a hálózatokat. Létre kell hozni a replikációs szabályzatokat, valamint el kell végezni a kapacitástervezést. Az infrastruktúra kiépítését követően engedélyezheti a virtuális gépek replikációját. Ezenfelül elvégezheti konkrét gépek feladatátadását, de létrehozhat több gép feladatátadását tartalmazó helyreállítási terveket is.

A Bevezetés folyamat első lépéseként válassza ki a Site Recovery üzembe helyezésének módját. A Bevezetés eljárás az adott replikációs követelményektől függően némileg eltérően megy végbe.



## 1. lépés: Védelmi célok megválasztása

Válassza ki, hogy mit szeretne replikálni, és hova.

1. Válassza ki a tárolót a **Recovery Services-tárolók** panelen, majd kattintson a **Beállítások** elemre.
2. A **Bevezetés** ablakban kattintson a **Site Recovery** > **1. lépés: Az infrastruktúra előkészítése** > **Védelmi cél** elemre.

    ![Célok megválasztása](./media/site-recovery-vmm-to-azure/choose-goals.png)

3. A **Védelmi cél** ablakban válassza **Az Azure-ba**, valamint a **Igen, a következővel: Hyper-V** lehetőséget. Az **Igen** lehetőség kiválasztásával erősítse meg, hogy VMM-mel felügyeli a Hyper-V gazdagépeket és a helyreállítási helyet. Végül kattintson az **OK** gombra.

    ![Célok megválasztása](./media/site-recovery-vmm-to-azure/choose-goals2.png)



## 2. lépés: A forráskörnyezet beállítása

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


### Az Azure Site Recovery Provider beállítása

1.  Futtassa a Provider telepítőfájlját.
2. A **Microsoft Update** lapon kérheti a frissítések beszerzését, így a rendszer a Microsoft Update-szabályzatnak megfelelően telepíteni fogja a Providerhez kiadott frissítéseket.
3. A **Telepítés** lapon tetszés szerint fogadja el vagy módosítsa a Provider alapértelmezett telepítési helyét, majd kattintson a **Telepítés** gombra.

    ![Telepítés helye](./media/site-recovery-vmm-to-azure/provider2.png)

4. A telepítés befejezését követően a VMM-kiszolgálónak a tárolóban való regisztrálásához kattintson a **Regisztráció** gombra.
5. Az **Internetkapcsolat** beállításnál adja meg, hogy a VMM-kiszolgálón futó Provider hogyan csatlakozzon a Site Recoveryhez az interneten keresztül.

    - Ha azt szeretné, hogy a Provider közvetlenül kapcsolódjon, válassza a **Connect directly without a proxy** (Közvetlen kapcsolódás proxy nélkül) lehetőséget.
    - Ha a kiszolgálón aktuálisan beállított proxyval szeretne kapcsolódni, válassza a **Connect with existing proxy settings** (Kapcsolódás a meglévő proxybeállításokkal) lehetőséget.
    - Ha az aktuálisan használt proxy hitelesítést igényel, illetve, ha egyéni proxyt szeretne használni, válassza a **Connect with custom proxy settings** (Kapcsolódás egyéni proxybeállításokkal) lehetőséget.
    - Ha az egyéni proxy használatát választja, meg kell adnia a címet, a portot és a hitelesítő adatokat.
    - Ha proxyt használt, ellenőrizze, hogy engedélyezte-e az [előfeltételekben](#on-premises-prerequisites) leírt URL-címek elérését.
    - Ha egyéni proxyt használ, a rendszer automatikusan létrehoz egy, a megadott hitelesítő adatokat alkalmazó VMM RunAs-fiókot (DRAProxyAccount). Állítsa be úgy a proxykiszolgálót, hogy ez a fiók elvégezhesse a hitelesítést. A VMM RunAs-fiók beállításait a VMM-konzolban módosíthatja. A **Beállítások** menüben bontsa ki a **Biztonság ** > **Futtató fiókok** lehetőséget, majd módosítsa a DRAProxyAccount jelszavát. A beállítás akkor lép érvénybe, ha újraindítja a VMM szolgáltatást.

    ![internet](./media/site-recovery-vmm-to-azure/provider3.png)

6. A **Tároló beállításai** lapon kattintson a **Tallózás** gombra, és válassza ki a tároló kulcsfájlját. Adja meg az Azure Site Recovery-előfizetést és a tároló nevét.

    ![Kiszolgáló regisztrációja](./media/site-recovery-vmm-to-azure/provider4.png)

7. Fogadja el vagy módosítsa az adatok titkosításához automatikusan létrehozott SSL-tanúsítvány helyét. Ez a tanúsítvány akkor használatos, ha bekapcsolja az Azure által védett felhőre vonatkozó adattitkosítást az Azure Site Recovery portálon. Őrizze biztonságos helyen a tanúsítványt. Ha feladatátvételt végez az Azure-ba, és korábban bekapcsolta az adattitkosítást, szüksége lesz rá a titkosítás feloldásához.

    ![Kiszolgáló regisztrációja](./media/site-recovery-vmm-to-azure/provider5.png)

8. A **Kiszolgáló neve** mezőben adjon meg egy, a tárolóban regisztrált VMM-kiszolgálót azonosító rövid nevet. Fürtkonfiguráció használata esetén adja meg a VMM-fürtszerepkör nevét.
13. Jelölje be a **Felhőmetaadatok szinkronizálása** jelölőnégyzetet, ha szeretné a VMM-kiszolgálón futó összes felhő metaadatait szinkronizálni a tárolóval.  Ezt a műveletet kiszolgálónként csak egyszer szükséges elvégezni. Ha nem szeretné az összes felhőt szinkronizálni, ne jelölje be a jelölőnégyzetet, és szinkronizálja egyenként a felhőket a VMM-konzolban, a felhők tulajdonságainál. A folyamat befejezéséhez kattintson a **Regisztráció** elemre.

    ![Kiszolgáló regisztrációja](./media/site-recovery-vmm-to-azure/provider6.png)

9. Elindul a regisztráció. A regisztráció befejezését követően a kiszolgáló megjelenik a tároló **Beállítások** > **Kiszolgálók** panelén.


#### Az Azure Site Recovery Provider telepítése a parancssorból

Az Azure Site Recovery Provider a parancssorból is telepíthető. A Provider ezzel a módszerrel a Server Core for Windows Server 2012 R2 kiadáson is telepíthető.

1. Töltse le egy mappába a Provider telepítőfájlját és a regisztrációs kulcsot. A mappa lehet például a következő: C:\ASR.
2. Rendszergazda jogú parancssorból futtassa az alábbi parancsokat a Provider telepítőjének kibontásához:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Az összetevők telepítéséhez futtassa ezt a parancsot:

            C:\ASR> setupdr.exe /i

4. Majd az alábbi parancs kiadásával regisztrálja a kiszolgálót a tárolóban:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Az elemek magyarázata:

- **/Credentials**: kötelező paraméter, amely a regisztrációs kulcsfájl helyét határozza meg.  
- **/FriendlyName**: kötelező paraméter, amely a Hyper-V gazdakiszolgáló nevét tartalmazza úgy, ahogy az az Azure Site Recovery portálon megjelenik.
- - **/EncryptionEnabled**: opcionális paraméter arra az esetre, ha VMM-felhőkben futó Hyper-V virtuális gépeket replikál az Azure-ba. Adja meg, hogy szeretné-e titkosítani a virtuális gépeket az Azure-ban (inaktív adatok titkosítása). A fájlnak **.pfx** kiterjesztésűnek kell lennie. A titkosítás alapértelmezés szerint ki van kapcsolva.
- **/proxyAddress**: nem kötelező paraméter, amely a proxykiszolgáló címét határozza meg.
- **/proxyport**: nem kötelező paraméter, amely a proxykiszolgáló portját határozza meg.
- **/proxyUsername**: nem kötelező paraméter, amely a proxy felhasználónevét határozza meg (ha a proxy hitelesítést igényel).
- **/proxyPassword**: nem kötelező paraméter, amely a proxykiszolgáló hitelesítéséhez szükséges jelszavát határozza meg (ha a proxy hitelesítést igényel).


### Az Azure Recovery Services Agent ügynök telepítése a Hyper-V gazdagépekre

1. A Provider beállítását követően töltse le az Azure Recovery Services Agent telepítőfájlját. Végezze el a telepítést a CMM-felhőben futó összes Hyper-V-kiszolgálón. 

    ![Hyper-V helyek](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)

2. Az **Előfeltételek ellenőrzése** lapon kattintson a **Tovább** gombra. A rendszer automatikusan telepíti a hiányzó előfeltételeket.

    ![Recovery Services Agent, előfeltételek](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)

3. A **Telepítési beállítások** lapon fogadja el vagy módosítsa a telepítés helyét, illetve a gyorsítótár helyét. A gyorsítótár már akár 5 GB-os kapacitású meghajtókon is beállítható, de javasoljuk, hogy a gyorsítótárazáshoz használt lemezen legyen legalább 600 GB szabad hely. Ezt követően kattintson a **Telepítés** gombra.
4. A telepítés befejezését követően a folyamat befejezéséhez kattintson a **Bezárás** gombra.
    
    ![A MARS Agent regisztrálása](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

#### Az Azure Site Recovery Services Agent telepítése a parancssorból

A Microsoft Azure Recovery Services Agent a következő paranccsal telepíthető a parancssorból: 

     marsagentinstaller.exe /q /nu

#### Internetes proxyn keresztüli elérés beállítása a Site Recoveryhez Hyper-V gazdagépekről

A Hyper-V gazdagépeken futó Recovery Services Agent ügynöknek a virtuális gépek replikálása céljából az interneten keresztül el kell érnie az Azure-t. Ha proxyn keresztül éri el az internetet, adja meg a következő beállításokat:

1. Nyissa meg a Microsoft Azure Backup MMC beépülő modult a Hyper-V gazdagépen. A Microsoft Azure Backup parancsikonja az asztalon vagy a következő mappában érhető el: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Kattintson a beépülő modul **Tulajdonságok módosítása** elemére.
3. A **Proxykonfiguráció** lapon adja meg a proxykiszolgálóra vonatkozó információkat.

    ![A MARS Agent regisztrálása](./media/site-recovery-vmm-to-azure/mars-proxy.png)

4. Ellenőrizze, hogy az ügynök el tudja-e érni az [előfeltételek](#on-premises-prerequisites) között megadott URL-címeket.


## 3. lépés: A célkörnyezet beállítása

Adja meg a replikációhoz használni kívánt Azure-tárfiókot, valamint az Azure-hálózatot, amelyhez az Azure virtuális gépek a feladatátvételt követően csatlakozhatnak.

1.  Kattintson az **Infrastruktúra előkészítése** > **Cél** elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2.  Adja meg, hogy a virtuális gépek átadása után melyik üzembe helyezési modellt szeretné használni.
3.  A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

    ![Tárterület](./media/site-recovery-vmm-to-azure/compatible-storage.png)   

4.  Ha még nem hozott létre tárfiókot, és szeretné most ezt megtenni az ARM-ben, kattintson a **+Tárfiók** elemre.  A **Tárfiók létrehozása** panelen adja meg a fiók nevét és típusát, az előfizetést, valamint a helyet. A fióknak és a Recovery Services-tárolónak ugyanazon a helyen kell lennie.

    ![Tárterület](./media/site-recovery-vmm-to-azure/gs-createstorage.png) 

    Vegye figyelembe:

    - Ha a klasszikus modellt használó tárfiókot szeretne létrehozni, azt az Azure portálon teheti meg. [További információk](../storage/storage-create-storage-account-classic-portal.md)
    - Ha Prémium szintű tárfiókot használ a replikált adatok tárolására, a helyszíni adatokban bekövetkező változásokat rögzítő repikálási naplók tárolására be kell állítania egy Standard szintű tárfiókot is.

4.  Ha még nem hozott létre Azure-hálózatot, és azt szeretné most megtenni az ARM-ben, kattintson a **+Hálózat** elemre. A **Virtuális hálózat létrehozása** panelen adja meg a hálózat nevét, a címtartományt, az alhálózati adatokat, az előfizetést, valamint a helyet. A hálózatnak és a Recovery Services-tárolónak ugyanazon a helyen kell lennie.

    ![Hálózat](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

    Ha a klasszikus modellt használó hálózatot szeretne létrehozni, azt az Azure portálon teheti meg. [További információk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

### Hálózatleképezés konfigurálása

- [Itt elolvashatja](#prepare-for-network-mapping) a hálózatleképezés működésének rövid ismertetését. Ha részletesebb magyarázatra vágyik, [olvassa el ezt](site-recovery-network-mapping.md). 
- Ellenőrizze, hogy a VMM-kiszolgálón futó virtuális gépek csatlakoznak-e a virtuálisgép-hálózathoz, illetve, hogy létrehozott-e legalább egy Azure virtuális hálózatot. Ne feledje, hogy egyetlen Azure-hálózatra több virtuálisgép-hálózatot is le lehet képezni.

Konfigurálja az alábbiak szerint a leképezéseket:

1. A **Beállítások** > **Site Recovery-infrastruktúra** > **Hálózatleképezések** > **Hálózatleképezés** menüpontban kattintson a **+Hálózatleképezés** ikonra.

    ![Hálózatleképezés](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. A **Hálózatleképezés hozzáadása** panelen válassza ki a forrás VMM-kiszolgálót, célként pedig az **Azure-t**.
3. Ellenőrizze az előfizetést, illetve a feladatok átadását követően használatos üzembe helyezési modellt.
4. A **Forráshálózat** panelen a VMM-kiszolgálóhoz társítottak listájából válassza ki azt a helyszíni virtuálisgép-hálózatot, amelyet le kíván képezni. 
5. A **Célhálózat** panelen válassza ki azt az Azure-hálózatot, amelyre a felálló replika Azure virtuális gépek kerüljenek. Végül kattintson az **OK** gombra.

    ![Hálózatleképezés](./media/site-recovery-vmm-to-azure/network-mapping2.png)

Ez történik a hálózatleképezés megkezdésekor:

- A forrás virtuálisgép-hálózaton futó virtuális gépek csatlakoznak a célhálózathoz. A forráshálózathoz újonnan csatlakozó virtuális gépek a replikáláskor csatlakoznak a leképezett Azure-hálózathoz. 
- Meglévő hálózatleképezés módosítása esetén a replika virtuális gépek az új beállításokkal fognak csatlakozni.
- Ha a célhálózatban már több alhálózat működik, és ezek egyikének ugyanaz a neve, mint annak, amelyen a forrás virtuális gép is található, a replika virtuális gép a feladatátvételt követően ehhez a cél alhálózathoz fog csatlakozni.
- Ha nem létezik egyező nevű alhálózat, a virtuális gép a hálózat első virtuális gépéhez csatlakozik.



## 4. lépés: A replikációs beállítások megadása


1. Új replikációs szabályzat létrehozásához kattintson az **Infrastruktúra előkészítése** > **Replikációs beállítások** > **+Létrehozás és társítás** elemre.

    ![Hálózat](./media/site-recovery-vmm-to-azure/gs-replication.png)

2. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét.
3. A **Másolás gyakorisága** elemmel meghatározhatja, hogy milyen gyakran szeretné replikálni a módosult adatokat a kezdeti replikációt követően (ez lehet 30 másodperc, 5 perc vagy 15 perc).
4. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy hány órás legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A védelemmel ellátott gépeket az időtartamon belüli bármelyik pontra visszaállíthatja.
6. Az **Alkalmazáskonzisztens pillanatkép gyakorisága** beállítás azt határozza meg, hogy milyen gyakran hozzon létre a rendszer alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat (a beállítás értéke 1 és 12 óra között változhat). A Hyper-V két különböző pillanatképtípust használ: az egyik a standard pillanatkép, amely a virtuális gép egészét lefedő növekményes pillanatképet, a másik pedig az alkalmazáskonzisztens pillanatkép, amely a virtuális gépen futó alkalmazások adatairól készült, időponthoz kötött pillanatképet jelent.  Az alkalmazáskonzisztens pillanatképek a kötet árnyékmásolása szolgáltatás (VSS) segítségével garantálják, hogy az alkalmazások konzisztens állapotban legyenek a pillanatkép készítésének időpontjában. Ne feledje, hogy az alkalmazáskonzisztens pillanatképek bekapcsolása negatív hatással lesz a forrás virtuális gépeken futó alkalmazások teljesítményére. Ügyeljen rá, hogy az itt megadott érték kisebb legyen a további beállított helyreállítási pontok számánál.
3. A **Kezdeti replikáció kezdési ideje** a kezdeti replikáció kezdésének időpontját határozza meg. A replikálási folyamat az internetes sávszélességet használja, így érdemes a műveletet olyankorra ütemezni, amikor kevesen használják az internetet.
5. **Az Azure-on tárolt adatok titkosítása** beállításnál adhatja meg, hogy szeretné-e titkosítani az Azure-tárfiókban elhelyezett inaktív adatokat. Végül kattintson az **OK** gombra.

    ![Replikációs szabályzat](./media/site-recovery-vmm-to-azure/gs-replication2.png)

6. Az újonnan létrehozott szabályzatokat a rendszer automatikusan társítja a VMM-felhővel. Kattintson az **OK** gombra. A további VMM-felhőket (és a rajtuk futó virtuális gépeket) a **Beállítások** > **Replikáció** > szabályzat neve > **VMM-felhő társítása** menüpontban társíthatja az adott replikációs szabályzathoz. 

    ![Replikációs szabályzat](./media/site-recovery-vmm-to-azure/policy-associate.png)

## 5. lépés: Kapacitástervezés

Most, hogy az alapszintű infrastruktúra készen áll, nekifoghat a kapacitás megtervezésének, és eldöntheti, hogy szüksége van-e további erőforrásokra.

A Site Recovery Capacity Planner szolgáltatása segít a forráskörnyezethez, a Site Recovery-összetevőkhöz, valamint a hálózathoz és a tárterülethez szükséges erőforrások lefoglalásában. A Planner segédprogram gyors módját használva a virtuális gépek, a lemezek, a tárhely átlagos számán alapuló becsléseket kérhet, vagy használhatja a részletes módot, amelyben a különböző számítási feladatok szintjén Ön adhatja meg a számokat. A kezdéshez a következőket kell tennie:

- Gyűjtse össze a replikálási környezetre vonatkozó adatokat, többek között a virtuális gépeket, a virtuális gépekhez tartozó lemezeket, valamint a lemezenkénti tárterületeket.
- Becsülje meg a replikált adatokra vonatkozóan a napi adatváltozás (forgalom) mértékét. Ebben a [Capacity Planner for Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) is segítséget nyújthat.

1.  A segédprogram letöltéséhez kattintson a **Download** (Letöltés) gombra, majd futtassa a letöltött fájlt. Olvassa el a segédprogramhoz kapcsolódó [cikket](site-recovery-capacity-planner.md).
2.  Ha elkészült, a **Have you run the Capacity Planner?** (Lefuttatta a Capacity Plannert?) kérdésnél válassza a **Yes** (Igen) lehetőséget.

    ![Kapacitástervezés](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### Hálózati sávszélességgel kapcsolatos szempontok

A Capacity Planner segítségével a replikáláshoz (a kezdeti replikáláshoz, majd a változások szinkronizálásához) szükséges sávszélességet is kiszámíthatja. A replikáláshoz felhasznált sávszélesség mértékének szabályozására a következő lehetőségek állnak rendelkezésre:

- **Throttle bandwidth** (Sávszélesség szabályozása): a másodlagos helyre replikációt végző Hyper-V-forgalom egy konkrét Hyper-V gazdagépen halad keresztül. A gazdakiszolgálón beállíthatja a sávszélesség szabályozását.
- **Tweak bandwidth** (Sávszélesség finomhangolása): beállításkulcsok segítségével is szabályozhatja a replikáláshoz használt sávszélességet.

#### Sávszélesség szabályozása

1. Nyissa meg a Microsoft Azure Backup MMC beépülő modult a Hyper-V gazdakiszolgálón. A Microsoft Azure Backup parancsikonja az asztalon vagy a következő mappában érhető el: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Kattintson a beépülő modul **Tulajdonságok módosítása** elemére.
3. A **Szabályozás** lapon jelölje be **A biztonsági mentési műveletek internetes sávszélességének szabályozása** jelölőnégyzetet, majd állítsa be a munkaórákra és a munkaórákon kívüli időre érvényes korlátozást. 512 kB/s és 102 MB/s közötti értéket adhat meg.

    ![Sávszélesség szabályozása](./media/site-recovery-vmm-to-azure/throttle2.png)

A szabályozáshoz a [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) parancsmag is használható. Íme egy példa:

    $mon = [System.DayOfWeek]::Monday 
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

A **Set-OBMachineSetting -NoThrottle** beállítás azt jelenti, hogy nincs szükség szabályozásra.


#### A hálózati sávszélesség szabályozása

Az **UploadThreadsPerVM** beállításazonosító szabályozza a lemezen adatátvitelre (kezdeti vagy változásreplikálásra) használt szálak számát. Minél magasabb a beállítás értéke, annál nagyobb hálózati sávszélességet használ a rendszer a replikáláshoz. A **DownloadThreadsPerVM** beállításazonosító határozza meg, hogy a rendszer hány szálat használ az adatátvitelhez a feladatátvétel során.

1. Lépjen a beállításjegyzék következő helyére: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
    
    - Az **UploadThreadsPerVM** beállítás értékének módosításával (vagy ha nem létezik, létrehozásával) szabályozhatja a lemezek replikálásához használt szálak számát. 
    - A **DownloadThreadsPerVM** beállítás értékének módosításával (vagy ha nem létezik, létrehozásával) szabályozhatja az Azure felől érkező feladatátvételi forgalomhoz használt szálak számát. 
2. Az alapértelmezett érték a 4. A szükségesnél több erőforrással ellátott hálózatban érdemes módosítani a beállításazonosítók alapértelmezett értékét. A maximális érték a 32. Az optimális érték kiválasztásához kövesse figyelemmel a forgalmat. 

## 6. lépés: A replikáció engedélyezése

Most már engedélyezheti a replikációt a következők szerint:

1. Kattintson **2. lépés: Az alkalmazás replikálása** > **Forrás** elemre. A replikálás első alkalommal történő engedélyezését követően kattintson a tárolóban elérhető **+Replikálás** elemre a további gépek replikációjának engedélyezéséhez.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication1.png)

2. A **Forrás** panelen válassza ki a VMM-kiszolgálót és a felhőt, amelyben a Hyper-V gazdagépek futnak. Végül kattintson az **OK** gombra.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication-source.png)

3. A **Cél** résznél válassza ki az előfizetést, a feladatátvételt követő üzembe helyezési modellt, valamint a replikált adatok tárolására használni kívánt tárfiókot.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication-target.png)

4. Válassza ki a használni kívánt tárfiókot. Ha a rendelkezésre álló tárfiókok helyett egy másikat szeretne használni, [hozzon létre egyet](#set-up-an-azure-storage-account). A tárfiók ARM-model használatával való létrehozásához kattintson az **Új létrehozása** elemre. Ha a klasszikus modellt használó tárfiókot szeretne létrehozni, azt az [Azure portálon teheti meg](../storage/storage-create-storage-account-classic-portal.md). Végül kattintson az **OK** gombra.
5. Válassza ki azt az Azure-hálózatot, valamint alhálózatot, amelyhez a feladatátvételt követően felálló Azure virtuális gépek csatlakozni fognak. Ha a megadott hálózati beállításokat az összes védelemre kijelölt gépre szeretné alkalmazni, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Ha az egyes gépeknél külön-külön szeretné beállítani az Azure-hálózatot, kattintson a **Beállítás később** elemre. Ha a rendelkezésre álló hálózatok helyett egy másikat szeretne használni, [hozzon létre egyet](#set-up-an-azure-network). Ha a hálózatot az ARM-modell használatával szeretné létrehozni, kattintson az **Új létrehozása** elemre. Ha a klasszikus modellt használó hálózatot szeretne létrehozni, azt az [Azure portálon teheti meg](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Ha szükséges, válassza ki az alhálózatot. Végül kattintson az **OK** gombra.
6. A **Virtuális gépek** > **Virtuális gépek kijelölése** menüben kattintással jelölje ki a replikálni kívánt virtuális gépeket. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Végül kattintson az **OK** gombra.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication5.png)

5. A **Tulajdonságok** > **Tulajdonságok konfigurálása** menüben válassza ki a kijelölt virtuális gépek operációs rendszerét, valamint az operációsrendszer-lemezképet. Végül kattintson az **OK** gombra. A további tulajdonságokat később is beállíthatja. 

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication6.png)

    
12. A **Replikációs beállítások** > **Replikációs beállítások konfigurálása** menüben válassza ki a védett virtuális gépekre alkalmazni kívánt replikációs beállításokat. Végül kattintson az **OK** gombra. A replikációs szabályzatot a **Beállítások** > **Replikációs házirendek** > szabályzat neve > **Beállítások szerkesztése** menüpontban módosíthatja. Az itt megadott módosítások a már replikálás alatt álló, illetve újonnan hozzáadott gépekre egyaránt érvényesek lesznek.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/enable-replication7.png)

A **Védelem engedélyezése** feladat előrehaladását a **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.

### A virtuális gépek tulajdonságainak megtekintése és kezelése

Javasoljuk, hogy ellenőrizze a forrásgépek tulajdonságait. Ne feledje, hogy az Azure virtuális gép nevének meg kell felelnie az [Azure virtuális gépekre vonatkozó követelményeknek](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Kattintson a **Beállítások** > **Védett elemek** > **Replikált elemek** > elemre, majd a részletek megtekintéséhez válassza ki a kívánt gépet.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/vm-essentials.png)

2. A **Tulajdonságok** résznél tekintheti meg a virtuális gép replikációs és feladatátvételi adatait.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/test-failover2.png)

3. A **Számítás és hálózat** > **Számítási tulajdonságok** résznél adhatja meg az Azure virtuális gép nevét és a cél méretét. Ha szükséges, írja át úgy a nevet, hogy az megfeleljen az [Azure-követelményeknek](site-recovery-best-practices.md#azure-virtual-machine-requirements). Ezenfelül itt a célhálózatra, az alhálózatra, valamint az Azure virtuális géphez rendelendő IP-címre vonatkozó információk is megtekinthetők. Vegye figyelembe a következőket:

    - A cél IP-címe beállítható. Ha nem ad meg címet, a gép, amelynek a feladatait átadja, a DHCP-t fogja használni. Ha olyan címet ad meg, amely nem használható feladatátadásra, a feladatátvételi művelet sikertelen lesz. A cél IP-címe feladatátvételi tesztre is használható, amennyiben a cím elérhető a feladatátvételi teszt hálózatában.
    - A hálózati adapterek számát a cél virtuális gépek mérete határozza meg, a következők szerint:

        - Ha a forrásgépen működő hálózati adapterek száma kisebb vagy egyenlő a célgép méretéhez engedélyezett adapterek számával, a célon ugyanannyi adapter fog működni, mint a forráson.
        - Ha a forrás virtuális gépek adaptereinek száma meghaladja a célmérethez engedélyezett maximumot, a rendszer a célmérethez engedélyezett maximális számot fogja használni.
        - Ha például a forrásgépen két hálózati adapter működik, és a célgép mérete négy adapter használatát teszi lehetővé, a célgépen két adapter fog működni. Ha azonban a forrásgépen két adapter működik, de a cél csupán egy adaptert támogat, akkor a célgépen is csak egy adapter fog működni.     
        - Ha a virtuális gépen több hálózati adapter működik, azok mindegyike ugyanahhoz a hálózathoz fog csatlakozni.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-azure/test-failover4.png)

5.  A **Lemezek** résznél láthatja a replikálandó virtuális gépekhez tartozó operációsrendszer- és adatlemezeket. 



## 7. lépés: Az üzemelő példány tesztelése

Az üzemelő példány kipróbálásához futtasson feladatátvételi tesztet egy virtuális gépen, vagy alkalmazzon egy vagy több virtuális gépet tartalmazó helyreállítási tervet.


### A feladatátvétel előkészítése

- A feladatátvételi teszt futtatásához javasoljuk, hogy hozzon létre egy új, az éles Azure-hálózattól elkülönített Azure-hálózatot (az új Azure-hálózatok létrehozásakor ez az alapértelmezés). [Itt részletesebben olvashat](site-recovery-failover.md#run-a-test-failover) a feladatátvételi teszt futtatásáról.
- Hogy az Azure-ba irányuló feladatátadás során a lehető legjobb eredményt érhesse el, telepítse a védett gépre az Azure Agent ügynököt. Ez az ügynök felgyorsítja a rendszerindítást, és segít a hibák elhárításában. Telepítse a megfelelő [linuxos](https://github.com/Azure/WALinuxAgent) vagy [windowsos](http://go.microsoft.com/fwlink/?LinkID=394789) ügynököt. 
- Az üzemelő példány megfelelő teszteléséhez az szükséges, hogy a replikált gép infrastruktúra pontosan az elvárások szerint működjön. Ha az Active Directoryt és a DNS-t szeretné tesztelni, hozzon létre egy tartományvezérlőként működő virtuális gépet DNS-szel, és replikálja azt az Azure-ba az Azure Site Recovery használatával. További információk az [Active Directoryra vonatkozó feladatátvételi szempontokat részletező cikkben](site-recovery-active-directory.md#considerations-for-test-failover).
- Ha a feladatátvételi teszt helyett nem tervezett feladatátvételt szeretne futtatni, vegye figyelembe a következőket:

    - Ha lehetséges, a nem tervezett feladatátvétel előtt állítsa le az elsődleges gépeket. Ezzel gondoskodik róla, hogy a forrás és a replika gépek nem fognak egyszerre futni. 
    - A nem tervezett feladatátvétel leállítja az elsődleges gépek adatreplikációját, így a nem tervezett feladatátvétel elindulását követő változásadatokat nem továbbítja a rendszer. Ezenfelül ha helyreállítási terv keretében futtat nem tervezett feladatátvételt, az teljesen végigfut, amíg hiba nem történik.

### Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Ha a feladatátvételt követően RDP segítségével szeretne kapcsolódni az Azure virtuális gépekhez, tegye meg a következőket:

**A helyszíni gépen, a feladatátvétel előtt**:

- Az interneten keresztüli eléréshez engedélyezze az RDP-t, ellenőrizze, hogy a **Nyilvános** résznél felvette-e a listára a TCP- és UDP-szabályokat, valamint, hogy a **Windows tűzfal** -> **Engedélyezett alkalmazások és szolgáltatások** területén az összes profil számára engedélyezve van-e az RDP.
- Helyek közötti kapcsolaton keresztüli eléréshez engedélyezze az RDP-t a gépen, és ellenőrizze, hogy a **Windows tűzfal** -> **Engedélyezett alkalmazások és szolgáltatások** területén engedélyezve van-e az RDP a **Tartomány** és a **Privát** hálózatok számára.
- Telepítse az [Azure virtuális gépet kezelő ügynököt](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) a helyszíni gépre.
- Az operációs rendszer tárolóhálózati szabályzatát állítsa OnlineAll értékre. [További információk]( https://support.microsoft.com/kb/3031135)
- A feladatátvétel futtatása előtt kapcsolja ki az IPSec szolgáltatást.

**Az Azure virtuális gépen, a feladatátvételt követően**:

- Vegyen fel egy nyilvános végpontot az RDP protokollba (3389-es port), és adja meg a bejelentkezéshez szükséges hitelesítő adatokat.
- Ellenőrizze, hogy nincsenek-e érvényben tartományi szabályzatok, amelyek meggátolják, hogy nyilvános cím segítségével csatlakozzon a virtuális gépekhez.
- Próbáljon meg csatlakozni. Ha nem sikerül, ellenőrizze, hogy fut-e a virtuális gép. További hibaelhárítási tippekért olvassa el ezt a [cikket](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Ha a feladatátvételt követően a Secure Shell-ügyfél (SSH) segítségével szeretné elérni az Azure virtuális gépet, tegye a következőket:

**A helyszíni gépen, a feladatátvétel előtt**:

- Ellenőrizze, hogy az Azure virtuális gépen beállította-e, hogy a Secure Shell szolgáltatás rendszerindításkor automatikusan elinduljon.
- Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.

**Az Azure virtuális gépen, a feladatátvételt követően**:

- Az Azure-alhálózaton, amelyhez a feladatait átadó virtuális gép csatlakozik, illetve magán a gépen érvényes hálózati biztonsági csoport szabályainak engedélyezniük kell az SSH-porthoz való csatlakozást.
- Hozzon létre nyilvános végpontot, amely lehetővé teszi a bejövő kapcsolatok számára az SSH-port (alapértelmezés szerint a 22-es TCP-port) használatát.
- Ha a virtuális géphez VPN-kapcsolaton (az Express Route vagy helyek közötti VPN segítségével) keresztül fér hozzá, az ügyfél segítségével is közvetlen SSH-kapcsolatot létesíthet a virtuális géppel.


### Feladatátvételi teszt futtatása

A feladatátvételi teszt futtatásához tegye a következőket:

1. A **Beállítások** > **Replikált elemek** listáján szereplő egyik virtuális gép feladatainak átadásához kattintson a virtuális gépre, majd a **+Feladatátvételi teszt** elemre.
2. Helyreállítási terv feladatátadásához a **Beállítások** > **Helyreállítási tervek** menüben kattintson jobb gombbal a kívánt tervre, majd válassza a **Feladatátvételi teszt** lehetőséget. Helyreállítási terv létrehozásához [kövesse ezeket az utasításokat](site-recovery-create-recovery-plans.md).

3. A **Feladatátvételi teszt** résznél válassza ki, hogy az Azure virtuális gépek mely Azure-hálózathoz csatlakozzanak a feladatátvételt követően.
4. A feladatátvételi művelet elindításához kattintson az **OK** gombra. A folyamat előrehaladásának megtekintéséhez kattintson a virtuális gépre, és nyissa meg a tulajdonságait, vagy a **Beállítások** > **Site Recovery-feladatok** menüben figyelje a **Feladatátvételi teszt** feladat állapotát.
5. Ha a feladatátvétel elérte a **Teszt befejezése** fázist, tegye a következőket:

    1. Tekintse meg a replika virtuális gépet az Azure portálon. Ellenőrizze, hogy a virtuális gép sikeresen elindul-e.
    2. Ha beállította a virtuális gépek helyszíni hálózatról való elérését, távoli asztali kapcsolatot is kezdeményezhet a virtuális géppel.
    3. A befejezéshez kattintson a **Complete the test** (Teszt befejezése) elemre.
    4. A feladatátvételi teszttel kapcsolatos megfigyelések feljegyzéséhez és mentéséhez kattintson a **Jegyzetek** elemre.
    5. Kattintson **A feladatátvételi teszt befejeződött** elemre. Számolja fel a tesztkörnyezetet. lehetőségre a teszteléshez használt virtuális gép automatikus kikapcsolásához és törléséhez.
    6. A rendszer ekkor a Site Recovery által a feladatátvételi teszt keretében automatikusan létrehozott összes elemet és virtuális gépet eltávolítja. A feladatátvételi teszthez Ön által létrehozott elemeket a rendszer nem törli.
    
    > [AZURE.NOTE] A szolgáltatás kényszeríti a két hétnél tovább tartó feladatátvételi tesztek befejezését.

6. A feladatátvétel befejezését követően a replika Azure-gépnek meg kell jelennie az Azure portál > **Virtuális gépek** részében. Ellenőrizze, hogy a virtuális gép mérete megfelelő-e, hogy a gép a megfelelő hálózathoz csatlakozik-e, illetve, hogy fut-e.
7. Ha [elvégezte a feladatátvételt követő csatlakozáshoz szükséges előkészületeket](#prepare-to-connect-to-Azure-VMs-after-failover), most tudnia kell csatlakozni az Azure virtuális géphez.


## Az üzemelő példány figyelése

A Site Recoveryben üzemelő példány konfigurációjának és állapotának figyeléséhez tegye a következőket:

1. Kattintson a tároló nevére az **Alapvető erőforrások** irányítópult megnyitásához. Itt megtalálja a Site Recovery-feladatokat, a replikációs állapotot, a replikálási terveket, a kiszolgáló állapotát, valamint az eseményeket.  Az Alapvető erőforrások irányítópultot testre szabva beállíthatja, hogy az Önnek legfontosabb csempék és elrendezések jelenjenek meg, így akár más Site Recovery- és Backup-tárolók állapotát is gyorsan megtekintheti.

    ![Alapvető erőforrások](./media/site-recovery-vmm-to-azure/essentials.png)

2. Az **Állapot** csempén a problematikus helykiszolgálók (VMM- vagy konfigurációs kiszolgálók) állapotát, illetve a Site Recovery által az elmúlt 24 órában rögzített eseményeket tekintheti meg.
3. A replikációt a **Replikált elemek**, a **Helyreállítási tervek** és a **Site Recovery-feladatok** csempéken felügyelheti és figyelheti. A feladatokat részletesen is megtekintheti a **Beállítások** -> **Feladatok** -> **Site Recovery-feladatok** menüpontban.


## További lépések

Ha sikerült beállítania és elindítani az üzemelő példányt, [ismerkedjen meg részletesebben](site-recovery-failover.md) a feladatátvételi különféle típusaival.



<!--HONumber=Jun16_HO2-->


