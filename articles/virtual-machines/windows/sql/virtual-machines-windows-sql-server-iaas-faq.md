---
title: Azure – gyakori kérdések a Windows virtuális gépeken futó SQL Server |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure virtuális gépeken futó SQL Server gyakran feltett kérdésekre adott válaszok.
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
ms.author: v-shysun
ms.openlocfilehash: f308b814da06598b95337708f7a8c84d506eed78
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57781799"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Gyakori kérdések az Azure-beli Windows virtuális gépeken futó SQL Serverhez

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Ez a cikk ismerteti a futó kapcsolatos leggyakoribb kérdésekre adott válaszokat [SQL Server a Windows virtuális gépek az Azure-ban](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Ez a cikk a Windows virtuális gépeken futó SQL Serverre konkrét problémák összpontosít. Ha az SQL Server Linuxos virtuális gépeken futnak, tekintse meg a [Linux – gyakori kérdések](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Képek

1. **Milyen az SQL Server virtuálisgép-katalógus rendszerképek érhetők el?**

   Azure Windows és Linux rendszerű SQL Server összes kiadásában az összes támogatott fő kiadásához tartozó virtuálisgép-lemezképek tart. További információkért tekintse meg a teljes listáját [Windows Virtuálisgép-rendszerképek](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) és [Linuxos Virtuálisgép-rendszerkép](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Frissülnek a meglévő SQL Server virtuálisgép-katalógus rendszerképek?**

   Minden két hónapra, SQL Server-rendszerképeket a virtuálisgép-katalógus frissülnek a legújabb Windows és Linux-frissítések. Windows-rendszerképeket ilyenek például a Windows Update, beleértve a fontos az SQL Server biztonsági frissítések és szervizcsomagok fontosként megjelölt frissítéseket. Linux-lemezképekhez ilyenek például a rendszerek legújabb frissítéseit. SQL Server összegző frissítések a Linux és Windows másképp kezeli. A Linux SQL Server összegző frissítései is megtalálhatók a frissítést. Azonban jelenleg Windows virtuális gépek nem frissíti az SQL Server vagy Windows Server összegző frissítései.

1. **Az SQL Server virtuálisgép-rendszerképek lekérése távolíthatók el a katalógusban?**

   Igen. Az Azure csak egy kép / fő verziójú és kiadású tart fenn. Például amikor egy új SQL Server szervizcsomag megjelenik, az Azure ad hozzá egy új rendszerképet a katalógusban, hogy a service Pack. Az SQL Server-lemezképet az előző service Pack azonnal törlődik az Azure Portalról. Azonban továbbra is elérhető, a következő három hónapban a Powershellből kiépítéshez. Három hónap elteltével az előző képen a service pack már nem érhető el. Ez a házirend eltávolítása is alkalmazni, ha egy SQL Server-verzió nem támogatott válik, amikor az életciklusa végére ér.


1. **Az is, amelyek nem láthatók az Azure Portalon az SQL Server régebbi lemezképének?**

   Igen, a PowerShell használatával. SQL Server virtuális gépek PowerShell-lel központi telepítésével kapcsolatos további információkért lásd: [hogyan építheti ki az SQL Servert futtató virtuális gépek az Azure PowerShell-lel](virtual-machines-windows-ps-sql-create.md).

1. **Lehetséges egy VHD-lemezképet készíteni egy SQL Server virtuális Gépet?**

   Igen, de van néhány szempontot. Ha a virtuális merevlemez telepít egy új virtuális géphez az Azure-ban, ezt megteheti ge nem az SQL Server-konfigurációs szakasz a portálon. Ezután a Powershellen keresztül az SQL Server-konfigurációs beállításokat kell kezelni. Emellett meg kell fizetni a díj az SQL virtuális gép a lemezkép eredetileg alapján. Ez igaz, akkor is, ha eltávolítja az SQL Server virtuális merevlemez üzembe helyezése előtt. 

1. **Az állítható be a virtuálisgép-katalógus (a + az SQL Server 2012 például Windows 2008 R2) nem látható konfigurációk?**

   Nem. A virtuális gép katalógus-lemezképek, amelyek tartalmazzák az SQL Server esetében jelöljön ki egy megadott rendszerképet az Azure Portalon keresztül, vagy keresztül [PowerShell](virtual-machines-windows-ps-sql-create.md). 


## <a name="creation"></a>Létrehozás

1. **Hogyan hozhatok létre Azure virtuális gépeken az SQL Server?**

   A legegyszerűbb megoldás, hogy hozzon létre egy virtuális gépet, amely tartalmazza az SQL Server. Az Azure-regisztráció és a egy SQL virtuális gép létrehozása a portálról, olvassa el [az Azure Portalon az SQL Server virtuális gép kiépítése](virtual-machines-windows-portal-sql-server-provision.md). Választhatja a használatalapú másodpercenkénti SQL Server licencelési használó virtuálisgép-lemezkép, vagy olyan lemezképet, amely lehetővé teszi, hogy a saját SQL Server-licencét is használhatja. Emellett lehetősége manuálisan az SQL Server telepítése a virtuális gép mindkettővel szabadon licenccel rendelkező kiadásra (Developer és Express) vagy az a helyi licencek újrafelhasználásával. Ha a saját licenc használata, rendelkeznie kell [az Azure frissítési garancián keresztüli Licenchordozhatóság](https://azure.microsoft.com/pricing/license-mobility/). További információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Hogyan is a helyszíni SQL Server-adatbázis migrálása a felhőbe?**

   Először hozzon létre egy Azure virtuális gép egy SQL Server-példányhoz. Majd telepítse át a helyszíni adatbázisok-példányhoz. Adatok migrálási stratégiák, lásd: [SQL Server-adatbázis áttelepítése az SQL Server Azure virtuális gép](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licencek

1. **Hogyan telepíthetem saját licenccel rendelkező példány az SQL Server-beli virtuális gépen?**

   Ehhez két módja van. Egyik kioszthatja a [virtuálisgép-lemezképek, amely támogatja a licencek](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), amely bring-your-saját licenc (használata BYOL) is nevezik. Egy másik lehetőség, hogy egy Windows Server rendszerű virtuális gép az SQL Server telepítési adathordozóról másolja, majd telepítse az SQL Server virtuális gépen. Azonban ha manuálisan telepíti az SQL Server, nincs portál integrációjához és az SQL Server IaaS-ügynök bővítmény nem támogatott, így például automatikus biztonsági mentés és az automatikus javítás nem fog működni ebben a forgatókönyvben. Ebből kifolyólag javasoljuk a katalógus BYOL-rendszerképek egyikét. Egy Azure-beli virtuális gépen (BYOL) és a saját SQL Server-adathordozó használatához rendelkeznie kell [az Azure frissítési garancián keresztüli Licenchordozhatóság](https://azure.microsoft.com/pricing/license-mobility/). További információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md).


1. **Rendelkeznie kell fizetnie, ha csak van használatban a készenléti/feladatátvételi az licenc SQL Server-beli virtuális gépen?**

   Ha rendelkezik frissítési garanciával, és használják a Licenchordozhatósági programot leírtak szerint [virtuális gép licencelés – gyakori kérdések](https://azure.microsoft.com/pricing/licensing-faq/), akkor nem kell fizetnie egy magas rendelkezésre ÁLLÁSÚ telepítésben passzív másodlagos másodpéldányként részt vevő egy SQL Server licencelése. Ellenkező esetben kell fizetnie, licenceket.

1. **Módosíthatja a saját SQL Server-licencét használja, ha létrehozták a használatalapú fizetéses katalógus rendszerképek közül egy virtuális Gépet?**

   Igen. Könnyedén áthelyezheti áthelyezése a két licencelési modell között, ha eredetileg indult el, egy használatalapú fizetéses katalógus-lemezkép. Azonban nem tud váltani a licencét a használatalapú fizetéses, ha kezdetben lépések egy BYOL-lemezképet. További információkért lásd: [módosítása egy SQL Server rendszerű virtuális gép licencelési modelljét](virtual-machines-windows-sql-ahb.md).

   > [!Note]
   > Ez a lehetőség jelenleg csak a nyilvános felhőben ügyfelek számára érhető el.

1. **BYOL-lemezképeknek vagy használjak SQL virtuális gép RP hozhat létre új SQL virtuális gép?**

   Bring-your-saját licenc (használata BYOL) lemezképek csak nagyvállalati szerződéssel rendelkező ügyfelek számára érhető el. Más ügyfelek, akik rendelkezik frissítési garanciával rendelkező SQL virtuális gép létrehozása kell használnia az SQL virtuális gép erőforrás-szolgáltató [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/licensing-faq/). 

1. **Váltása licencmodellek szükségesek üzemszünetre lesz szükség minden olyan SQL Server?**

   Nem. [Licencelési modelljének megváltoztatása](virtual-machines-windows-sql-ahb.md) nem igényel tapasztalnak állásidőt az SQL Server, a módosítás azonnali érvényben, és nem igényel a virtuális gép újraindítását. Azonban, hogy regisztrálja az SQL virtuális gép erőforrás-szolgáltató az SQL Server virtuális gép a [SQL IaaS-bővítményt](virtual-machines-windows-sql-server-agent-extension.md) előfeltétel, és újraindítja az SQL Server szolgáltatást, az SQL IaaS-bővítmény telepítése. Mint ilyen Ha az SQL IaaS-bővítményt kell telepíteni, majd el kell végezni a karbantartási időszak alatt. 

1. **Aktiválhatja a CSP-előfizetések az Azure Hybrid Benefit?**

   Igen, az Azure Hybrid Benefit a CSP-előfizetésekben érhető el. CSP-ügyfeleinek először üzembe kell helyeznie egy használatalapú fizetéses lemezképet, majd [módosítani a licencelési modellt](virtual-machines-windows-sql-ahb.md) a bring-your-saját licenc.  

1. **Az új SQL virtuális gép erőforrás-szolgáltató regisztrálása a virtuális gépem tartalomtérkép érhető el további költségek?**

   Nem. Az SQL virtuális gép erőforrás-szolgáltató csak lehetővé teszi, hogy további kezelhetőségi az SQL Server Azure virtuális gépen az nem vonatkozik külön díj. 

1. **Az SQL virtuális gép erőforrás-szolgáltató érhető el minden ügyfél számára?**
 
   Igen. Minden ügyfél tudnak az új SQL virtuális gép erőforrás-szolgáltató regisztrálásához. Azonban csak a frissítési garancia-juttatás rendelkező ügyfelek is aktiválhatja a [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) (vagy BYOL) SQL Server virtuális gépen. 

1. **Mi történik az _* Microsoft.SqlVirtualMachine_* erőforrások áthelyezésekor a VM-erőforrás vagy kihagyott?** 

   Ha a Microsoft.Compute/VirtualMachine erőforrás eldobott vagy áthelyezték, akkor a társított Microsoft.SqlVirtualMachine erőforrás a művelet aszinkron módon replikálja értesítést kap.

1. **Mi történik a virtuális géphez, ha az _* Microsoft.SqlVirtualMachine_* erőforrás már nincs használatban?**

   Amikor a rendszer eldobja a Microsoft.SqlVirtualMachine erőforrás Microsoft.Compute/VirtualMachine erőforrás nem változik. Azonban a licencelési módosítások alapértelmezés szerint a kép eredeti forrást. 

1. **Az lehet az SQL virtuális gép erőforrás-szolgáltató a saját telepített SQL Server virtuális gépek regisztrálni?**

   Igen. Ha a saját adathordozóról SQL-kiszolgálót helyezett üzembe, és telepítve van az SQL Server virtuális gép regisztrálhatja kihasználni a kezelhetőségi biztosítják az SQL IaaS-bővítményt az erőforrás-szolgáltató az SQL IaaS-bővítményt. Azonban Ön nem alakítható át a saját telepített SQL virtuális gépek használatalapú fizetéses előfizetésre.

## <a name="administration"></a>Adminisztráció

1. **Telepíthetem egy második példányt az SQL Server ugyanazon a virtuális Gépen? Módosíthatja a telepített szolgáltatások az alapértelmezett példány?**

   Igen. Az SQL Server telepítési adathordozóján található egy mappában található a **C** meghajtó. Futtatás **Setup.exe** arról a helyről az új SQL Server-példányok hozzáadása vagy módosítása másik telepített funkciók az SQL Server a gépen. Vegye figyelembe, hogy egyes funkciók, például az automatikus biztonsági mentés, automatikus javítás és az Azure Key Vault-integráció, csak az alapértelmezett példány működik, vagy egy nevesített példányt, amely megfelelően lett konfigurálva (lásd a 3 kérdés). 

1. **Eltávolíthatja az SQL Server alapértelmezett példányát?**

   Igen, de van néhány szempontot. Amint azt az előző választ, vannak-e a használó szolgáltatások a [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).  Ha az IaaS-bővítményt is eltávolítása nélkül távolítja el az alapértelmezett példány, a bővítmény továbbra is fennáll, akkor a keresett és Eseménynapló hibák léphetnek fel. Ezeket a hibákat a következő két forrásból származnak: **A Microsoft SQL Server hitelesítőadat-kezelés** és **Microsoft SQL Server IaaS-ügynök**. A hibák egyike az alábbihoz hasonló lehet:

      A hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Server-kapcsolat létrehozásakor. A kiszolgáló nem található vagy nem érhető el.

   Ha úgy dönt, az alapértelmezett példány eltávolítása, is eltávolítja a [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md) is.

1. **Használhatok az SQL Server megnevezett példánya az IaaS-bővítménnyel**?
   
   Igen, ha az elnevezett példány az egyetlen példány az SQL Server-kiszolgálón, és ha az eredeti alapértelmezett példány megfelelően el lett távolítva. Megnevezett példányt használ, tegye a következőket:
    1. SQL Server virtuális gép üzembe helyezése a piactérről. 
    1. Távolítsa el az IaaS-bővítményt.
    1. Az SQL Server teljesen eltávolítani.
    1. Telepítse az SQL Server nevesített példánnyal. 
    1. Az IaaS-bővítményének telepítése. 

1. **Eltávolítható az SQL Server teljesen az SQL virtuális gép?**

   Igen, de továbbra is az SQL virtuális gép kell fizetnie, leírtak szerint [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md). Ha már nincs szüksége az SQL Server, az új virtuális gépek üzembe helyezése, és az adatai és alkalmazásai számára az új virtuális gép áttelepítése. Az SQL Server virtuális gép távolíthatja el.
   
## <a name="updating-and-patching"></a>Frissítések és javítások

1. **Hogyan változtatható az SQL Server egy Azure-beli virtuális gépen új verzióra vagy kiadásra?**

   Helybeni frissítéseket, azok a telepítési adathordozó használatával a mennyiségi licencelési portálon az Azure virtuális Gépeken futó SQL Server frissítési garanciával rendelkező ügyfelek is. Azonban jelenleg nem lehet módosítani az SQL Server-példány kiadása. Egy új Azure virtuális gép létrehozása a kívánt SQL Server olyan verzióját, és ezután az adatbázisokat az új kiszolgálóra a standard [adatáttelepítési eljárásokkal](virtual-machines-windows-migrate-sql.md).

1. **Frissítések és szervizcsomagok alkalmazási módja egy SQL Server virtuális gépen?**

   A virtuális gépek lehetővé teszik a gazdagép vezérlését, például a frissítések alkalmazási időpontjának és módjának vezérlését. Az operációs rendszer esetén manuálisan alkalmazhatja a windows-frissítéseket, vagy engedélyezheti a nevű ütemezési szolgáltatást [automatikus javítás](virtual-machines-windows-sql-automated-patching.md). Az Automatikus javítás minden fontosként megjelölt frissítést telepít, így az e kategóriába eső SQL Server-frissítéseket is. Az SQL Server egyéb nem kötelező frissítéseit manuálisan kell telepíteni.

## <a name="general"></a>Általános kérdések

1. **Azure virtuális gépeken támogatottak az SQL Server feladatátvevő fürt példány (FCI)?**

   Igen. Is [Windows feladatátvevő fürt létrehozása a Windows Server 2016-on](virtual-machines-windows-portal-sql-create-failover-cluster.md) , és a fürttároló közvetlen tárolóhelyek (S2D) használja. Másik lehetőségként használhatja külső fürtszolgáltatás vagy a tárolási megoldások leírtak szerint [az SQL Server Azure virtuális gépek magas rendelkezésre állás és vészhelyreállítás helyreállítási](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > Jelenleg a [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md) nem támogatott az SQL Server FCI az Azure-ban. Azt javasoljuk, hogy az FCI-ben részt vevő virtuális gépek távolítsa el a bővítményt. Ez a bővítmény szolgáltatásai, például az automatikus biztonsági mentés és a javítás és a egy portál funkciók az SQL támogatja. Ezek a funkciók nem működnek az SQL virtuális gépek, az ügynök eltávolítása után.

1. **Mi a különbség az SQL virtuális gépek és az SQL Database szolgáltatás?**

   Elméleti szinten fut az SQL Server-beli virtuális gépen eltér, hogy nem fut az SQL Server egy távoli adatközpontban. Ezzel szemben a [SQL Database](../../../sql-database/sql-database-technical-overview.md) kínál az adatbázis--szolgáltatásként. Az SQL Database nem rendelkezik hozzáféréssel a az adatbázisokat üzemeltető gépeken. Teljes összehasonlításáért lásd: [felhőalapú SQL Server option: Az Azure SQL (PaaS) adatbázis vagy SQL Server Azure virtuális gépeken (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Hogyan kell telepíteni az SQL Data eszközök a saját Azure virtuális gépen**

    Töltse le és telepítse az SQL Data eszközöket [Microsoft SQL Server Data Tools – Business Intelligence Visual Studio 2013-hoz készült](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>További források

**Windows virtuális gépek**:

* [Egy Windows virtuális gép az SQL Server használatának áttekintése](virtual-machines-windows-sql-server-iaas-overview.md).
* [Egy SQL Server Windows virtuális gép kiépítése](virtual-machines-windows-portal-sql-server-provision.md)
* [Egy Azure virtuális Gépen futó SQL Server-adatbázis áttelepítése](virtual-machines-windows-migrate-sql.md)
* [Magas rendelkezésre állás és vészhelyreállítás Azure-beli virtuális gépeken az SQL Serverhez](virtual-machines-windows-sql-high-availability-dr.md)
* [Ajánlott eljárások az SQL Server teljesítményének Azure Virtual Machines szolgáltatásbeli növeléséhez](virtual-machines-windows-sql-performance.md)
* [Azure-beli virtuális gépeken futó SQL Server – alkalmazásminták és fejlesztési stratégiák](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux rendszerű virtuális gépek**:

* [Linux rendszerű virtuális gép az SQL Server használatának áttekintése](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Az SQL Server Linux virtuális gép kiépítése](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Gyakori kérdések (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [A Linux rendszeren futó SQL Server dokumentációja](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
