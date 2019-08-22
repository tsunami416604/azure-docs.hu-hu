---
title: Az Azure-on Windows Virtual Machines SQL Server GYIK | Microsoft Docs
description: Ez a cikk a SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 5b3c65361f45c8ad5bdf3adf89dc3002c229d0d0
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650410"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Gyakori kérdések az Azure-beli Windows rendszerű virtuális gépeken futó SQL Server

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Ez a cikk az [Azure-beli Windows Virtual Machines SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)futtatásával kapcsolatos leggyakoribb kérdésekre ad választ.

> [!NOTE]
> Ez a cikk a Windows rendszerű virtuális gépeken SQL Serverekre vonatkozó problémákra koncentrál. Ha SQL Server on Linux virtuális gépeket futtat, tekintse meg a [Linux gyakori](../../linux/sql/sql-server-linux-faq.md)kérdéseit.

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a>Képek

1. **Milyen SQL Server virtuálisgép-katalógus lemezképei érhetők el?** 

   Az Azure a Windows és a Linux összes kiadásában a SQL Server összes támogatott fő kiadásához virtuális gépi rendszerképeket tart fenn. További információkért tekintse meg a Windowsos virtuálisgép-rendszerképek és a Linux [rendszerű](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) virtuálisgép-lemezképek teljes listáját. [](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create)

1. **Frissültek a meglévő SQL Server virtuálisgép-katalógus rendszerképei?**

   A virtuális gép galériájában SQL Server rendszerképek a legújabb Windows-és Linux-frissítésekkel frissülnek. Windows-rendszerképek esetén ide tartoznak a Windows Update fontosként megjelölt frissítések, beleértve a fontos SQL Server biztonsági frissítéseket és szervizcsomagokat is. Linux-rendszerképek esetén ez tartalmazza a legújabb rendszerfrissítéseket. SQL Server kumulatív frissítések kezelése a Linux és a Windows rendszerben eltérően történik. A Linux esetében SQL Server kumulatív frissítések is szerepelnek a frissítésben. Ebben az esetben azonban a Windows rendszerű virtuális gépek nem frissülnek SQL Server vagy a Windows Server összesítő frissítéseivel.

1. **El SQL Server tudja távolítani a virtuális gépek lemezképeit a katalógusból?**

   Igen. Az Azure csak egy rendszerképet tart fenn főverzióként és kiadásként. Ha például új SQL Server szervizcsomagot szabadít fel, az Azure új rendszerképet hoz létre az adott szervizcsomaghoz tartozó galériához. Az előző szervizcsomag SQL Server rendszerképét a rendszer azonnal eltávolítja a Azure Portalból. A következő három hónapban azonban továbbra is elérhető a PowerShellből való kiépítés. Három hónap elteltével az előző szervizcsomag-rendszerkép már nem érhető el. Ez az eltávolítási szabályzat akkor is érvényes, ha a SQL Server verziója nem támogatott, amikor eléri az életciklusának végét.


1. **Lehet olyan régebbi rendszerképet telepíteni SQL Server, amely nem látható a Azure Portalban?**

   Igen, a PowerShell használatával. A SQL Server virtuális gépek PowerShell használatával történő üzembe helyezésével kapcsolatos további információkért lásd: [SQL Server Virtual Machines szolgáltatás kiépítése a Azure PowerShellsal](virtual-machines-windows-ps-sql-create.md).

1. **Létrehozhatok általánosított Azure SQL Server Marketplace-lemezképet a saját SQL Server VM, és felhasználhatom a virtuális gépek üzembe helyezéséhez?**

   Igen, de ezt követően regisztrálnia kell [az egyes SQL Server VMokat a SQL Server VM erőforrás](virtual-machines-windows-sql-register-with-resource-provider.md) -szolgáltatóval, hogy kezelje a SQL Server VM a portálon, valamint olyan funkciókat is használhat, mint az automatikus javítás és az automatikus biztonsági mentés. Az erőforrás-szolgáltatóval való Regisztráláskor meg kell adnia az egyes SQL Server VMokhoz tartozó licenc típusát is. 

1. **Használhatom a saját VHD-t egy SQL Server VM üzembe helyezéséhez?**

   Igen, de ezt követően regisztrálnia kell [az egyes SQL Server VMokat a SQL Server VM erőforrás](virtual-machines-windows-sql-register-with-resource-provider.md) -szolgáltatóval, hogy kezelje a SQL Server VM a portálon, valamint olyan funkciókat is használhat, mint az automatikus javítás és az automatikus biztonsági mentés.

1. **Lehetséges a virtuálisgép-katalógusban nem látható konfigurációk beállítása (például Windows 2008 R2 + SQL Server 2012)?**

   Nem. SQL Server tartalmazó virtuálisgép-katalógusbeli rendszerképek esetén a megadott rendszerképek egyikét ki kell választania a Azure Portal vagy a [PowerShell](virtual-machines-windows-ps-sql-create.md)használatával. Azonban lehetősége van arra, hogy Windows rendszerű virtuális gépet helyezzen üzembe, és telepítse SQL Server. Ezután regisztrálnia kell [a SQL Server VMt a SQL Server VM erőforrás](virtual-machines-windows-sql-register-with-resource-provider.md) -szolgáltatóval, hogy kezelhesse a SQL Server VM a portálon, valamint olyan funkciókat is használhat, mint például az automatikus javítás és az automatikus biztonsági mentés. 


## <a name="creation"></a>Létrehozása

1. **Hogyan létrehoz egy Azure-beli virtuális gépet SQL Server?**

   A legegyszerűbb módszer egy SQL Servert tartalmazó virtuális gép létrehozása. Az Azure-regisztrációval és a portálon SQL Server VM létrehozásával kapcsolatos oktatóanyagért lásd: [SQL Server virtuális gép kiépítése a Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Kiválaszthat egy olyan virtuálisgép-rendszerképet, amely SQL Server másodpercenkénti fizetést használ, vagy használhat olyan rendszerképet, amely lehetővé teszi a saját SQL Server licencének használatát. Lehetősége van arra is, hogy manuálisan telepítse a SQL Servert egy olyan virtuális gépre, amely vagy egy ingyenes licenccel rendelkező kiadás (Developer vagy Express), vagy egy helyszíni licenc újrafelhasználásával. Győződjön meg arról, hogy regisztrálja a [SQL Server VMt a SQL Server VM erőforrás](virtual-machines-windows-sql-register-with-resource-provider.md) -szolgáltatóval, hogy kezelje a SQL Server VM a portálon, valamint olyan funkciókat használ, mint például az automatikus javítás és az automatikus biztonsági mentés. Ha saját licencet használ, rendelkeznie kell [licenchordozhatóság frissítési garancia keretében az Azure](https://azure.microsoft.com/pricing/license-mobility/)-ban. További információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Hogyan telepíthetem a helyszíni SQL Server-adatbázist a felhőbe?**

   Először hozzon létre egy SQL Server példánnyal rendelkező Azure-beli virtuális gépet. Ezután telepítse át a helyszíni adatbázisait erre a példányra. Az adatáttelepítési stratégiákat lásd: [SQL Server adatbázis áttelepítése egy Azure-beli virtuális gépen SQL Server](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licencelés

1. **Hogyan telepíthetem az SQL Server egy licencelt példányát egy Azure-beli virtuális gépen?**

   Ezt három módon teheti meg. Ha Ön nagyvállalati szerződéssel (EA) rendelkezik, a [licenceket támogató virtuálisgép](virtual-machines-windows-sql-server-iaas-overview.md#BYOL)-rendszerképek közül választhat, amelyek a saját licencek (BYOL) néven is ismertek. Ha rendelkezik frissítési [garanciával](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default), engedélyezheti a [Azure Hybrid Benefit](virtual-machines-windows-sql-ahb.md) egy meglévő utólagos elszámolású (TB) rendszerképben. Vagy átmásolhatja a SQL Server telepítési adathordozót egy Windows Server rendszerű virtuális gépre, majd telepítheti a SQL Servert a virtuális gépre. Ügyeljen arra, hogy regisztrálja SQL Server VM az [erőforrás](virtual-machines-windows-sql-register-with-resource-provider.md) -szolgáltatónál olyan funkciókhoz, mint a portál kezelése, az automatikus biztonsági mentés és az automatikus javítás. 

1. **Fizetnem kell az SQL Server licenceléséért egy Azure-beli virtuális gépen, ha csak készenlétben tartom vagy feladatátvételre használom?**

   Ha a készenléti másodlagos rendelkezésre állási csoport vagy a feladatátvételi fürtszolgáltatás számára ingyenes passzív licenccel rendelkezik, a [licencelési útmutató PDF](https://download.microsoft.com/download/7/8/C/78CDF005-97C1-4129-926B-CE4A6FE92CF5/SQL_Server_2017_Licensing_guide.pdf)-fájljában ismertetett feltételek mindegyikének meg kell felelnie a következő feltételeknek:

   1. Frissítési garanciával rendelkezik a [](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) [licencek mobilitásával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) . 
   1. A passzív SQL Server példány nem szolgál SQL Server-adatokra az ügyfeleknek, vagy nem futtat aktív SQL Server munkaterheléseket. A szolgáltatás csak az elsődleges kiszolgálóval való szinkronizálásra használatos, a passzív adatbázist pedig meleg készenléti állapotban tartja. Ha olyan adatokat tárol, mint például az aktív SQL Server munkaterheléseket futtató ügyfelek jelentései, vagy bármilyen "munka", például további biztonsági másolatok a másodlagos kiszolgálóról, akkor a fizetős licenccel rendelkező SQL Server példánynak kell lennie. 
   1. Az aktív SQL Server licencet a frissítési garancia szabályozza, és lehetővé teszi **egy** passzív másodlagos SQL Server példány használatát, amely akár a licenccel rendelkező aktív kiszolgálóval azonos mennyiségű számítási kapacitással is rendelkezik. 
   1. A másodlagos SQL Server VM a saját Licences (BYOL) vagy Azure Hybrid Benefit (AHB) [licencelési modellt](virtual-machines-windows-sql-ahb.md)használja. 

1. **Módosíthatok egy virtuális gépet, hogy a saját SQL Server-licencemet használja, ha az a használatalapú fizetéses katalógus egyik rendszerképéből lett létrehozva?**

   Igen. Az utólagos elszámolású (TB) katalógust egyszerűen átválthatja a saját licenc használatára (BYOL), ha engedélyezi a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  További információ: [a SQL Server VM licencelési modelljének módosítása](virtual-machines-windows-sql-ahb.md). Ez a lehetőség jelenleg csak a nyilvános Felhőbeli ügyfelek számára érhető el.

1. **A licencelési modell módosítása során az SQL Server le fog állni?**

   Nem. [A licencelési modell módosítása](virtual-machines-windows-sql-ahb.md) nem igényel leállást SQL Server mivel a változás azonnal hatályba lép, és nem igényli a virtuális gép újraindítását. Ahhoz azonban, hogy a SQL Server VMt a SQL Server VM erőforrás-szolgáltatóval regisztrálja, az [SQL IaaS bővítmény](virtual-machines-windows-sql-server-agent-extension.md) előfeltétele, és az SQL IaaS bővítmény _teljes_ módban történő telepítése újraindítja a SQL Server szolgáltatást. Ilyen esetben, ha az SQL IaaS bővítményt telepíteni kell, vagy a korlátozott funkcionalitás érdekében telepítse azt egy _egyszerű_ módban, vagy a karbantartási időszak során _teljes_ módba kell telepítenie. Az _egyszerűsített_ módban telepített SQL IaaS bővítmény bármikor frissíthető _teljes_ módba, de a SQL Server szolgáltatás újraindítását igényli. 

1. **Használhatom a Azure Portal több példány kezelésére ugyanazon a virtuális gépen?**

   Nem. A portál felügyelete a SQL Server VM erőforrás-szolgáltató által biztosított szolgáltatás, amely a SQL Server IaaS-ügynök bővítményre támaszkodik. Ennek megfelelően ugyanazok a korlátozások vonatkoznak az erőforrás-szolgáltatóra, mint a bővítményre. A portálon csak egy alapértelmezett példány vagy egy elnevezett példány kezelhető, feltéve, hogy megfelelően van konfigurálva. További információ ezekről a korlátozásokról: [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md). 

1. **A CSP-előfizetések aktiválja a Azure Hybrid Benefit?**

   Igen, a Azure Hybrid Benefit CSP-előfizetésekhez érhető el. A CSP-ügyfeleknek először telepíteniük kell az utólagos elszámolású lemezképet, majd [a licencelési modellt](virtual-machines-windows-sql-ahb.md) úgy kell megváltoztatnia, hogy a saját licencét használja.

1. **Regisztrálja a virtuális gépet az új SQL Server VM erőforrás-szolgáltatónál, hogy további költségekkel jár?**

   Nem. A SQL Server VM erőforrás-szolgáltató csak a további kezelhetőséget teszi lehetővé az Azure-beli virtuális gépeken való SQL Server további költségek nélkül. 

1. **Elérhető a SQL Server VM erőforrás-szolgáltató az összes ügyfél számára?**
 
   Igen, ha a SQL Server VM a nyilvános felhőben a Resource Manager-modellel lett telepítve, nem a klasszikus modellt. Minden más ügyfél regisztrálhat az új SQL Server VM erőforrás-szolgáltatóval. Azonban csak a frissítési garanciával [](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) rendelkező ügyfelek használhatják a saját licencét a [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) SQL Server VM való aktiválásával. 

1. **Mi történik az erőforrás-szolgáltató (_Microsoft. SqlVirtualMachine_) erőforrással, ha a virtuális gép erőforrása át van helyezve vagy el lett dobva?** 

   A Microsoft. számítási/VirtualMachine erőforrás eldobása vagy áthelyezése után a rendszer a társított Microsoft. SqlVirtualMachine erőforrást a művelet aszinkron replikálására értesíti.

1. **Mi történik a virtuális géppel, ha az erőforrás-szolgáltató (_Microsoft. SqlVirtualMachine_) erőforrás el van dobva?**

    A Microsoft. SqlVirtualMachine-erőforrás eldobásakor a Microsoft. számítás/VirtualMachine erőforrás nincs hatással. A licencelés módosításai azonban alapértelmezés szerint visszaállnak az eredeti rendszerkép forrására. 

1. **Lehetséges a SQL Server VM erőforrás-szolgáltatóval önálló üzembe helyezett SQL Server virtuális gépek regisztrálása?**

    Igen. Ha a saját adathordozóról telepített SQL Serverokat, és telepítette az SQL IaaS bővítményt, akkor az SQL IaaS-bővítmény által biztosított kezelhetőségi előnyök beszerzéséhez regisztrálhatja SQL Server VM az erőforrás-szolgáltatóval. Az öntelepített SQL Server VMokat azonban nem lehet utólagos elszámolású előfizetésre konvertálni.

1. **Lehetséges a licencelési modell átváltása a klasszikus modell használatával üzembe helyezett SQL Server VMon?**

   Nem. A licencelési modell módosítása klasszikus virtuális gépen nem támogatott. A virtuális gépet áttelepítheti a Azure Resource Manager modellbe, és regisztrálhatja az SQL Server VM erőforrás-szolgáltatóval. Ha a virtuális gép regisztrálva van a SQL Server VM erőforrás-szolgáltatónál, a licencelési modell módosításai elérhetők lesznek a virtuális gépen. 
   


## <a name="administration"></a>Felügyelet

1. **Telepíthetek egy második SQL Server-példányt ugyanazon a virtuális gépen? Módosíthatom az alapértelmezett példány telepített szolgáltatásainak körét?**

   Igen. A SQL Server telepítési adathordozó a **C** meghajtó egyik mappájában található. Futtassa az adott hely **Setup. exe** fájlját új SQL Server példányok hozzáadásához vagy a SQL Server egyéb telepített szolgáltatásainak módosításához a gépen. Vegye figyelembe, hogy egyes funkciók, például az automatikus biztonsági mentés, az automatikus javítás és a Azure Key Vault integráció, csak az alapértelmezett példányon vagy egy megfelelően konfigurált megnevezett példányon működnek (lásd a 3. kérdést). 

1. **Eltávolíthatom az SQL Server alapértelmezett példányát?**

   Igen, de bizonyos szempontokat figyelembe kell venni. Először is SQL Server kapcsolódó számlázás a virtuális gép licencelési modelljétől függően továbbra is előfordulhat. Másodszor, ahogy az előző válaszban is szerepel, vannak olyan funkciók, amelyek a [SQL Server IaaS-ügynök bővítményére](virtual-machines-windows-sql-server-agent-extension.md)támaszkodnak. Ha a IaaS-bővítmény eltávolítása nélkül távolítja el az alapértelmezett példányt, a bővítmény továbbra is megkeresi az alapértelmezett példányt, és Eseménynapló-hibákat eredményezhet. E hibáknak két forrása lehet: **Microsoft SQL Server hitelesítőadat-kezelés** és **Microsoft SQL Server IaaS-ügynök**. Az egyik hiba a következőhöz hasonló lehet:

      Hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Serverhez való kapcsolódás során. A kiszolgáló nem található vagy nem érhető el.

   Ha úgy dönt, hogy eltávolítja az alapértelmezett példányt, távolítsa el a [SQL Server IaaS-ügynök bővítményt](virtual-machines-windows-sql-server-agent-extension.md) is. 

1. **Használhatom SQL Server megnevezett példányát az IaaS bővítménnyel**?
   
   Igen, ha a megnevezett példány az egyetlen példány a SQL Serveron, és az eredeti alapértelmezett példány [eltávolítása megfelelő](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance)volt. Ha nincs alapértelmezett példány, és több megnevezett példány van egyetlen SQL Server VM, akkor a SQL Server IaaS-ügynök bővítmény telepítése sikertelen lesz. 

1. **Eltávolíthatom SQL Server teljesen egy SQL Server VMból?**

   Igen, de továbbra is a SQL Server VMért kell fizetnie, az [SQL Server Azure-beli virtuális gépek díjszabási útmutatójában](virtual-machines-windows-sql-server-pricing-guidance.md)leírtak szerint. Ha már nincs szüksége az SQL Serverre, üzembe helyezhet egy új virtuális gépet, majd oda migrálhatja az adatokat és az alkalmazásokat. Ezt követően már eltávolíthatja az SQL Servert futtató virtuális gépet.
   
## <a name="updating-and-patching"></a>Frissítés és javítás

1. **Hogyan válthatok másik SQL Server-verzióra/-kiadásra egy Azure-beli virtuális gépen?**

   Az ügyfelek az SQL Server kívánt verzióját vagy kiadását tartalmazó telepítési adathordozóval módosíthatják az SQL Server verzióját/kiadását. A kiadás módosítása után az Azure Portalon módosítsa a virtuális gép kiadási tulajdonságát, hogy a virtuális gép számlázása a pontos adatokkal történjen. További információkért lásd: [SQL Server VM kiadásának módosítása](virtual-machines-windows-sql-change-edition.md). A SQL Server különböző verziói esetében nem számítunk fel számlázási különbséget, így a SQL Server verziójának módosítása után nincs szükség további műveletre.

1. **Honnan szerezhető be a telepítési adathordozó a SQL Server kiadásának vagy verziójának módosításához?**

  A frissítési [garanciával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) rendelkező ügyfelek a [mennyiségi licencelési](https://www.microsoft.com/Licensing/servicecenter/default.aspx)központból szerezhetik be a telepítési adathordozót. Azok a felhasználók, akik nem rendelkeznek frissítési garanciával, a kívánt kiadással rendelkező Marketplace SQL Server VM-rendszerkép telepítési adathordozóját használhatják.

1. **Hogyan történik a frissítések és szervizcsomagok alkalmazása egy SQL Server VM?**

   A virtuális gépek lehetővé teszik a gazdagép vezérlését, például a frissítések alkalmazási időpontjának és módjának vezérlését. Az operációs rendszer esetében manuálisan is alkalmazhatja a Windows-frissítéseket, vagy engedélyezheti az [Automatikus javítás](virtual-machines-windows-sql-automated-patching.md)nevű ütemezési szolgáltatást. Az Automatikus javítás minden fontosként megjelölt frissítést telepít, így az e kategóriába eső SQL Server-frissítéseket is. Az SQL Server egyéb nem kötelező frissítéseit manuálisan kell telepíteni.

1. **Frissíthetem a SQL Server 2008/2008 R2 példányt a SQL Server VM erőforrás-szolgáltatóval való regisztráció után?**

   Igen. Bármilyen telepítési adathordozót használhat a SQL Server verziójának és kiadásának frissítésére, majd az [SQL IaaS bővítményi módot](virtual-machines-windows-sql-server-agent-extension.md#change-management-modes) az ügynöktől a _teljes_értékre is frissítheti. Ez lehetővé teszi az SQL IaaS bővítmény összes előnyének elérését, például a portál kezelhetőségét, az automatikus biztonsági mentést és az automatizált javításokat. 

## <a name="general"></a>Általános

1. **Az Azure-beli virtuális gépeken támogatottak-e SQL Server feladatátvevő fürt példányai (a)?**

   Igen. Windows rendszerű [feladatátvevő fürtöt a Windows Server 2016-ben hozhat létre](virtual-machines-windows-portal-sql-create-failover-cluster.md) , és a fürt tárterületéhez közvetlen TÁROLÓHELYEK (S2D) is használhat. Azt is megteheti, hogy harmadik féltől származó fürtözési vagy tárolási megoldásokat is használhat a [magas rendelkezésre állás és a vész-helyreállítás SQL Server az Azure Virtual Machinesban](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)című témakörben leírtak szerint.

   > [!IMPORTANT]
   > Ekkor a _teljes_ [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md) nem támogatott az Azure-beli SQL Server-es verziója esetén. Javasoljuk, hogy távolítsa el a _teljes_ bővítményt a modulban részt vevő virtuális gépekről, és telepítse a bővítményt _egyszerűsített_ módban. Ez a bővítmény támogatja a szolgáltatásokat, például az automatikus biztonsági mentést és a javítást, valamint a SQL Server egyes portál-funkcióit. Ezek a funkciók a _teljes_ ügynök eltávolítása után nem fognak működni SQL Server virtuális gépeken.

1. **Mi a különbség a SQL Server virtuális gépek és a SQL Database szolgáltatás között?**

   Az Azure-beli virtuális gépeken futó SQL Server fogalma nem különbözik a távoli adatközpontok SQL Server futtatásának. Ezzel szemben a [SQL Database](../../../sql-database/sql-database-technical-overview.md) szolgáltatásként kínált adatbázis-szolgáltatást. A SQL Database nem rendelkezik hozzáféréssel az adatbázisokat üzemeltető gépekhez. Teljes összehasonlításért tekintse [meg a Cloud SQL Server kiválasztása lehetőséget: Azure SQL-(Pásti-) adatbázis vagy SQL Server Azure-beli virtuális](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)gépeken (IaaS).

1. **Hogyan telepíteni az SQL-adateszközöket az Azure-beli virtuális gépre?**

    Töltse le és telepítse az SQL-adateszközöket [Microsoft SQL Server Adateszközökről – üzleti intelligencia a Visual Studio 2013-hez](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Támogatottak-e a SQL Server virtuális gépeken futó MSDTC-vel rendelkező elosztott tranzakciók?**
   
    Igen. A helyi DTC SQL Server 2016 SP2 és újabb rendszereken támogatott. Az Always On rendelkezésre állási csoportok használatakor azonban meg kell vizsgálni az alkalmazásokat, mivel a feladatátvétel során a repülés során végzett tranzakciók sikertelenek lesznek, és újra kell próbálkozni. A fürtözött DTC a Windows Server 2019-től kezdődően érhető el. 

## <a name="resources"></a>További források

**Windows rendszerű virtuális gépek**:

* A [Windows rendszerű virtuális gépek SQL Serverának áttekintése](virtual-machines-windows-sql-server-iaas-overview.md).
* [SQL Server Windows rendszerű virtuális gép kiépítése](virtual-machines-windows-portal-sql-server-provision.md)
* [Adatbázis migrálása SQL Server Azure-beli virtuális gépen](virtual-machines-windows-migrate-sql.md)
* [Magas rendelkezésre állás és vész-helyreállítási SQL Server az Azure-ban Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Ajánlott eljárások az SQL Server teljesítményének Azure Virtual Machines szolgáltatásbeli növeléséhez](virtual-machines-windows-sql-performance.md)
* [Azure-beli virtuális gépeken futó SQL Server – alkalmazásminták és fejlesztési stratégiák](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux rendszerű virtuális gépek**:

* [Linux rendszerű virtuális gépen lévő SQL Server áttekintése](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux rendszerű virtuális gép kiépítése](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Gyakori kérdések (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [A Linux rendszeren futó SQL Server dokumentációja](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
