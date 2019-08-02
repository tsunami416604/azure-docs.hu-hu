---
title: Az Azure-on Windows Virtual Machines SQL Server GYIK | Microsoft Docs
description: Ez a cikk a SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
services: virtual-machines-windows
documentationcenter: ''
author: v-shysun
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.openlocfilehash: 7f6ec1ee65727fb8c3c7d98f696c288e95ec880a
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876197"
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

1. **Létrehozhatok egy VHD-rendszerképet egy SQL Server VM?**

   Igen, de van néhány megfontolandó szempont. Ha ezt a VHD-t egy új virtuális gépre telepíti az Azure-ban, akkor a portálon nem fog megjelenni a SQL Server konfiguráció szakasz. Ezután a PowerShell használatával kell kezelnie a SQL Server konfigurációs beállításait. Azt is megterheljük, hogy milyen arányban kell fizetnie az SQL virtuális gép esetében, amelyet eredetileg a rendszerkép alapján használt. Ez akkor is igaz, ha a telepítés előtt eltávolítja SQL Server a virtuális merevlemezről. 

1. **Lehetséges a virtuálisgép-katalógusban nem látható konfigurációk beállítása (például Windows 2008 R2 + SQL Server 2012)?**

   Nem. SQL Server tartalmazó virtuálisgép-katalógusbeli rendszerképek esetén a megadott rendszerképek egyikét ki kell választania a Azure Portal vagy a [PowerShell](virtual-machines-windows-ps-sql-create.md)használatával. 


## <a name="creation"></a>Létrehozása

1. **Hogyan létrehoz egy Azure-beli virtuális gépet SQL Server?**

   A legegyszerűbb megoldás egy SQL Servert tartalmazó virtuális gép létrehozása. Az Azure-regisztrációval és az SQL virtuális gép portálról való létrehozásával kapcsolatos oktatóanyagért lásd: [SQL Server virtuális gép kiépítése a Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Kiválaszthat egy olyan virtuálisgép-rendszerképet, amely SQL Server másodpercenkénti fizetést használ, vagy használhat olyan rendszerképet, amely lehetővé teszi a saját SQL Server licencének használatát. Lehetősége van arra is, hogy manuálisan telepítse a SQL Servert egy olyan virtuális gépre, amely vagy egy ingyenes licenccel rendelkező kiadás (Developer vagy Express), vagy egy helyszíni licenc újrafelhasználásával. Ha saját licencet használ, rendelkeznie kell [licenchordozhatóság frissítési garancia keretében az Azure](https://azure.microsoft.com/pricing/license-mobility/)-ban. További információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Hogyan telepíthetem a helyszíni SQL Server-adatbázist a felhőbe?**

   Először hozzon létre egy SQL Server példánnyal rendelkező Azure-beli virtuális gépet. Ezután telepítse át a helyszíni adatbázisait erre a példányra. Az adatáttelepítési stratégiákat lásd: [SQL Server adatbázis áttelepítése egy Azure-beli virtuális gépen SQL Server](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licencek

1. **Hogyan telepíthetem az SQL Server egy licencelt példányát egy Azure-beli virtuális gépen?**

   Ezt kétféleképpen teheti meg. Helyezze üzembe az egyik olyan [virtuálisgép-rendszerképet, amely támogatja a licencek használatát](virtual-machines-windows-sql-server-iaas-overview.md#BYOL). Ez a módszer saját licenc használata (BYOL) néven is ismert. Másik lehetőségként másolja át az SQL Server telepítési adathordozóját egy Windows Server rendszerű virtuális gépre, majd telepítse az SQL Servert a virtuális gépre. Azonban ha manuálisan telepíti az SQL Servert, nem lesz elérhető a portálintegráció, és az SQL Server IaaS-ügynök bővítménye nem lesz támogatott, így néhány funkció, például az automatikus biztonsági mentés és az automatikus javítás nem fog működni ebben a forgatókönyvben. Ezért a BYOL-katalógusból származó egyik rendszerkép használatát javasoljuk. Ha az Azure-beli virtuális gépen szeretné használni a BYOL-t vagy a saját SQL Server adathordozóját, licenchordozhatóság Frissítési Garancia keretében kell rendelkeznie [Az Azure](https://azure.microsoft.com/pricing/license-mobility/)-ban. További információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Fizetnem kell az SQL Server licenceléséért egy Azure-beli virtuális gépen, ha csak készenlétben tartom vagy feladatátvételre használom?**

   Ha rendelkezik frissítési garanciával, és használja licenchordozhatóság a [virtuális gép licencelése – gyakori kérdések](https://azure.microsoft.com/pricing/licensing-faq/)című témakörben leírtak szerint, nem kell fizetnie az egyik SQL Server a passzív másodlagos replikaként részt vevő, ha üzembe helyezést. Egyéb esetben fizetnie kell a licencért.

1. **Módosíthatok egy virtuális gépet, hogy a saját SQL Server-licencemet használja, ha az a használatalapú fizetéses katalógus egyik rendszerképéből lett létrehozva?**

   Igen. A két licencelési modell között könnyedén áthelyezheti a váltást, ha eredetileg utólagos elszámolású gyűjteményt használ. Ha azonban eredetileg a BYOL-rendszerképet használta, nem tudja átváltani a licencet használatalapú fizetéses licencre. További információ: [a SQL Server VM licencelési modelljének módosítása](virtual-machines-windows-sql-ahb.md).

   > [!Note]
   > Ez a lehetőség jelenleg csak a nyilvános Felhőbeli ügyfelek számára érhető el.

1. **BYOL-rendszerképet vagy SQL VM RP-t használjak egy új SQL-alapú virtuális gép létrehozásához?**

   A saját licenc használatát (BYOL) lehetővé tevő rendszerképek csak nagyvállalati szerződéssel rendelkező ügyfelek számára érhetők el. A frissítési garanciával rendelkező többi ügyfélnek az SQL VM erőforrás-szolgáltató használatával kell létrehoznia [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/licensing-faq/)SQL virtuális gépet. 

1. **A licencelési modell módosítása során az SQL Server le fog állni?**

   Nem. [A licencelési modell módosítása](virtual-machines-windows-sql-ahb.md) nem igényel leállást SQL Server mivel a változás azonnal hatályba lép, és nem igényli a virtuális gép újraindítását. Ahhoz azonban, hogy regisztrálja SQL Server VM az SQL VM erőforrás-szolgáltatónál, az SQL [IaaS bővítmény](virtual-machines-windows-sql-server-agent-extension.md) előfeltétele, és az SQL IaaS bővítmény telepítése újraindítja a SQL Server szolgáltatást. Ha telepíteni kell az SQL IaaS-bővítményt, ezt egy karbantartási időszakban kell megtenni. 

1. **A CSP-előfizetések aktiválja a Azure Hybrid Benefit?**

   Igen, a Azure Hybrid Benefit CSP-előfizetésekhez érhető el. A CSP-ügyfeleknek először telepíteniük kell az utólagos elszámolású lemezképet, majd [a licencelési modellt](virtual-machines-windows-sql-ahb.md) úgy kell megváltoztatnia, hogy a saját licencét használja.  

1. **Ha regisztrálom a virtuális gépemet az új SQL VM-erőforrásszolgáltatóba, további költségek merülnek fel?**

   Nem. Az SQL VM-erőforrásszolgáltató további költségek nélkül teszi lehetővé az SQL Server további kezelhetőségét az Azure-beli virtuális gépen. 

1. **Az SQL VM-erőforrásszolgáltató minden ügyfél számára elérhető?**
 
   Igen. Minden ügyfél regisztrálhat az új SQL VM-erőforrásszolgáltatóba. Azonban csak a frissítési garanciával rendelkező ügyfelek aktiválhatja a [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) (vagy a BYOL) egy SQL Server VM. 

1. **Mi történik a _Microsoft. SqlVirtualMachine_ erőforrással, ha a virtuális gép erőforrása át van helyezve vagy el van dobva?** 

   A Microsoft. számítási/VirtualMachine erőforrás eldobása vagy áthelyezése után a rendszer a társított Microsoft. SqlVirtualMachine erőforrást a művelet aszinkron replikálására értesíti.

1. **Mi történik a virtuális géppel, ha a _Microsoft. SqlVirtualMachine_ erőforrás el van dobva?**

    A Microsoft. SqlVirtualMachine-erőforrás eldobásakor a Microsoft. számítás/VirtualMachine erőforrás nincs hatással. A licencelés módosításai azonban alapértelmezés szerint visszaállnak az eredeti rendszerkép forrására. 

1. **Regisztrálhatok saját magam által üzembe helyezett SQL Servert futtató virtuális gépet az SQL VM-erőforrásszolgáltatóba?**

    Igen. Ha a saját adathordozóról telepített SQL Serverokat, és telepítette az SQL IaaS bővítményt, akkor az SQL IaaS-bővítmény által biztosított kezelhetőségi előnyök beszerzéséhez regisztrálhatja SQL Server VM az erőforrás-szolgáltatóval. Azonban a saját maga által üzembe helyezett SQL-alapú virtuális gép nem alakítható át használatalapú fizetéses modell használatára.

## <a name="administration"></a>Adminisztráció

1. **Telepíthetek egy második SQL Server-példányt ugyanazon a virtuális gépen? Módosíthatom az alapértelmezett példány telepített szolgáltatásainak körét?**

   Igen. A SQL Server telepítési adathordozó a **C** meghajtó egyik mappájában található. Futtassa az adott hely **Setup. exe** fájlját új SQL Server példányok hozzáadásához vagy a SQL Server egyéb telepített szolgáltatásainak módosításához a gépen. Vegye figyelembe, hogy egyes funkciók, például az automatikus biztonsági mentés, az automatikus javítás és a Azure Key Vault integráció, csak az alapértelmezett példányon vagy egy megfelelően konfigurált megnevezett példányon működnek (lásd a 3. kérdést). 

1. **Eltávolíthatom az SQL Server alapértelmezett példányát?**

   Igen, de bizonyos szempontokat figyelembe kell venni. Az előző válaszban leírtaknak megfelelően vannak olyan funkciók, amelyek a [SQL Server IaaS-ügynök bővítményére](virtual-machines-windows-sql-server-agent-extension.md)támaszkodnak.  Ha a IaaS bővítmény eltávolítása nélkül távolítja el az alapértelmezett példányt, a bővítmény továbbra is megkeresi, és Eseménynapló-hibákat eredményezhet. E hibáknak két forrása lehet: **Microsoft SQL Server hitelesítőadat-kezelés** és **Microsoft SQL Server IaaS-ügynök**. Az egyik hiba a következőhöz hasonló lehet:

      Hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Serverhez való kapcsolódás során. A kiszolgáló nem található vagy nem érhető el.

   Ha úgy dönt, hogy eltávolítja az alapértelmezett példányt, távolítsa el a [SQL Server IaaS-ügynök bővítményt](virtual-machines-windows-sql-server-agent-extension.md) is.

1. **Használhatom SQL Server megnevezett példányát az IaaS bővítménnyel**?
   
   Igen, ha a megnevezett példány az egyetlen példány a SQL Serveron, és az eredeti alapértelmezett példány [eltávolítása megfelelő](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md#installation)volt. Ha nincs alapértelmezett példány, és több megnevezett példány van egyetlen SQL Server VM, akkor a IaaS bővítmény telepítése sikertelen lesz. 

1. **Eltávolíthatom teljesen az SQL Servert egy SQL-alapú virtuális gépről?**

   Igen, de továbbra is díjat kell fizetnie az SQL-alapú virtuális gép számára a [SQL Server Azure-beli virtuális gépek díjszabási útmutatójában](virtual-machines-windows-sql-server-pricing-guidance.md)leírtak szerint. Ha már nincs szüksége az SQL Serverre, üzembe helyezhet egy új virtuális gépet, majd oda migrálhatja az adatokat és az alkalmazásokat. Ezt követően már eltávolíthatja az SQL Servert futtató virtuális gépet.
   
## <a name="updating-and-patching"></a>Frissítés és javítás

1. **Hogyan egy Azure-beli virtuális gépen a SQL Server egy másik verziójára/kiadására váltani?**

   Az ügyfelek a SQL Server kívánt verzióját vagy kiadását tartalmazó telepítési adathordozót használva módosíthatják SQL Server verzióját/kiadását. A kiadás módosítása után a Azure Portal segítségével módosíthatja a virtuális gép kiadási tulajdonságát, hogy pontosan tükrözze a virtuális gép számlázását. További információkért lásd: [SQL Server VM kiadásának módosítása](virtual-machines-windows-sql-change-edition.md). 


1. **Hogyan történik a frissítések és szervizcsomagok alkalmazása egy SQL Server VM?**

   A virtuális gépek lehetővé teszik a gazdagép vezérlését, például a frissítések alkalmazási időpontjának és módjának vezérlését. Az operációs rendszer esetében manuálisan is alkalmazhatja a Windows-frissítéseket, vagy engedélyezheti az [Automatikus javítás](virtual-machines-windows-sql-automated-patching.md)nevű ütemezési szolgáltatást. Az Automatikus javítás minden fontosként megjelölt frissítést telepít, így az e kategóriába eső SQL Server-frissítéseket is. Az SQL Server egyéb nem kötelező frissítéseit manuálisan kell telepíteni.

## <a name="general"></a>Általános

1. **Az Azure-beli virtuális gépeken támogatottak-e SQL Server feladatátvevő fürt példányai (a)?**

   Igen. Windows rendszerű [feladatátvevő fürtöt a Windows Server 2016-ben hozhat létre](virtual-machines-windows-portal-sql-create-failover-cluster.md) , és a fürt tárterületéhez közvetlen TÁROLÓHELYEK (S2D) is használhat. Azt is megteheti, hogy harmadik féltől származó fürtözési vagy tárolási megoldásokat is használhat a [magas rendelkezésre állás és a vész-helyreállítás SQL Server az Azure Virtual Machinesban](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)című témakörben leírtak szerint.

   > [!IMPORTANT]
   > Ekkor a [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md) nem támogatott az Azure-beli SQL Server-es verziója esetén. Javasoljuk, hogy távolítsa el a bővítményt a modulban részt vevő virtuális gépekről. Ez a bővítmény olyan szolgáltatásokat támogat, mint például az automatikus biztonsági mentés és javítás, valamint az SQL egyes portál-szolgáltatásai. Ezek a funkciók az ügynök eltávolítása után nem fognak működni az SQL virtuális gépeken.

1. **Mi a különbség az SQL virtuális gépek és a SQL Database szolgáltatás között?**

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
