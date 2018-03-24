---
title: SQL Server a Windows Azure virtuális gépek – gyakori kérdések |} Microsoft Docs
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
ms.date: 03/20/2018
ms.author: v-shysun
ms.openlocfilehash: 42a82a59d0cf786e80b93f124cbe04007b2a4704
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="frequently-asked-questions-for-sql-server-on-windows-azure-virtual-machines"></a>Gyakori kérdések az SQL Server a Windows Azure virtuális gépeken

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Ez a cikk ismerteti a leggyakoribb kérdésekre vonatkozó válaszokat [SQL Server a Windows Azure virtuális gépeken](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Ez a cikk Windows gépeken futó SQL Server kapcsolatos problémák összpontosít. Ha az SQL Server a Linux virtuális gépeken futnak, tekintse meg a [Linux – gyakori kérdések](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Lemezképek

1. **Milyen SQL Server virtuális gép a gyűjtemény lemezképei állnak rendelkezésre?**

   Azure Windows és Linux tart fenn minden támogatott fő verziója az SQL Server összes kiadásában virtuálisgép-lemezképeket. További részletekért lásd: a teljes listáját [Windows Virtuálisgép-rendszerképek](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) és [Linux Virtuálisgép-rendszerképek](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Meglévő SQL Server virtuális gépen lévő képek frissítése?**

   Minden két hónapon belülre esik a virtuális gép galériában képek SQL Server frissítése a legújabb Windows és Linux frissíti. A Windows lemezképeit ilyenek például a Windows Update, beleértve a fontos SQL Server biztonsági frissítések és szervizcsomagok fontos megjelölt frissítéseket. A Linux-lemezképek ilyenek például a rendszer legújabb frissítéseit. SQL Server összegző frissítései a Linux és a Windows másképp kezeli. Linux SQL Server összegző frissítései is szerepelni fog a frissítést. De ilyenkor a Windows virtuális gépek nem frissíti az SQL Server vagy Windows Server összegző frissítések.

1. **SQL Server virtuálisgép-rendszerképek beolvasása távolíthatók el a gyűjtemény?**

   Igen. Azure csak egy kép / fő verzióját és kiadását tart fenn. Például egy új SQL Server szervizcsomag megjelenésekor, Azure ad hozzá egy új lemezképet szervizcsomagnak gyűjteménye. Az SQL Server-rendszerképet az előző service Pack azonnal távolítja el az Azure-portálon. Azonban célszerű továbbra is elérhető, a következő három hónapban a Powershellből kiépítéshez. Három hónap után az előző service pack kép már nem érhető el. Az eltávolítási házirend csak akkor is vonatkozik, ha egy SQL Server-verzió nem támogatott válik, amikor eléri a életciklusának végét.

1. **Létrehozhatók a Virtuálismerevlemez-kép egy SQL Server virtuális gépről?**

   Igen, de nincs van néhány szempontokat. Ha a virtuális merevlemez telepít egy új Azure-ban, szeretne nem ge az SQL Server-konfigurációs szakasz a portálon. Az SQL Server-konfigurációs beállítások Powershellen keresztül majd kell kezelni. Is fizetnie kell az os az SQL virtuális gép, a lemezkép eredetileg alapján. Ez igaz, akkor is, ha az SQL Server eltávolítja telepítése előtt a virtuális Merevlemezt. 

1. **Az nem látható a virtuális gép gyűjtemény (a + SQL Server 2012-ben például a Windows 2008 R2) konfigurációk beállítható?**

   Nem. A virtuális gép gyűjtemény lemezképek, amely tartalmazza az SQL Server ki kell választania, egy megadott lemezképet.

## <a name="creation"></a>Létrehozása

1. **Hogyan hozható létre egy Azure virtuális gépen futó SQL Server?**

   A legegyszerűbb megoldás az, hogy hozzon létre egy virtuális gépet, amely tartalmazza az SQL Server. Oktatóanyag regisztrál az Azure és az SQL virtuális gép létrehozása a portálon, lásd: [egy SQL Server rendszerű virtuális gép az Azure portálon](virtual-machines-windows-portal-sql-server-provision.md). Kiválaszthatja, hogy a virtuálisgép-lemezkép által használt fizetési / másodperces SQL Server licencelési, vagy egy olyanra, amely lehetővé teszi a saját SQL Server-licenc is használhat. Lehetősége is van manuálisan telepíti az SQL Server a virtuális gép sem szabadon licenccel rendelkező kiadásra (fejlesztői vagy Express) vagy egy helyszíni licenc újból felhasználja. Ha később saját licenc, rendelkeznie kell [Azure frissítési garancián keresztüli Licenchordozhatósági](https://azure.microsoft.com/pricing/license-mobility/). További információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Hogyan telepíthet át a felhőbe a helyszíni SQL Server-adatbázist?**

   Először létre kell hoznia egy Azure virtuális gépet egy SQL Server-példányhoz. Telepítse át a helyszíni adatbázisokat példányhoz. Az adatok áttelepítési stratégiák, lásd: [egy SQL Server-adatbázis áttelepítése SQL Server egy Azure virtuális gép](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licencelés

1. **Hogyan telepíthetők a licencelt másolatával, az SQL Server egy Azure virtuális gépen?**

   Ehhez két módja van. Egyik létesíthet a [licencek támogató virtuálisgép-rendszerképek](virtual-machines-windows-sql-server-iaas-overview.md#BYOL). Lehetősége az SQL Server telepítési adathordozóról másolja a Windows Server virtuális géphez, és SQL Server telepítése a virtuális Gépen. Licencelés okok, rendelkeznie kell [Azure frissítési garancián keresztüli Licenchordozhatósági](https://azure.microsoft.com/pricing/license-mobility/). További információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Módosíthatja a saját SQL Server licence használja, ha az egyik a használatalapú lévő képek hozták létre a virtuális gépek?**

   Nem. Nem lehet átállítani a saját licenc használata licencelési fizetési / másodperc. Hozzon létre egy új Azure virtuális gép egyikével a [BYOL képek](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), majd telepítse át az adatbázisokat az új kiszolgálóra a szabványos [adatok áttelepítési technikák](virtual-machines-windows-migrate-sql.md).

1. **Rendelkeznie kell fizetnie licencre SQL Server egy Azure virtuális gépen, ha csak használatos készenléti vagy feladatátvételi?**

   Ha frissítési garancia, és használja License Mobility [virtuális gép Licensing FAQ](http://azure.microsoft.com/pricing/licensing-faq/) akkor nem kell fizetnie egy SQL Server magas rendelkezésre ÁLLÁSÚ telepítés passzív másodlagos másodpéldányként részt vevő licenc. Ellenkező esetben kell fizetnie a licencszerződés.


## <a name="administration"></a>Adminisztráció

1. **Telepíthető az SQL Server egy második példányát az azonos virtuális gépen? Módosíthatja az alapértelmezett példány telepített szolgáltatásokat?**

   Igen. Az SQL Server telepítési adathordozóról az egy mappában található a **C** meghajtó. Futtatás **Setup.exe** arról a helyről új SQL Server-példányok felvételéhez vagy módosíthatja a más telepítendő szolgáltatásokat az SQL Server a számítógépen. Vegye figyelembe, hogy bizonyos funkciók, például az automatikus biztonsági mentés, automatikus javítás és az Azure Key Vault-integráció csak az alapértelmezett példány működik.

1. **SQL Server alapértelmezett példányának eltávolítása**

   Igen, de nincs olyan szempontokat. Amint azt a korábbi választ, szolgáltatások, amelyek támaszkodjon a [SQL Server infrastruktúra-szolgáltatási ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md) csak az alapértelmezett példányon működik. Ha az alapértelmezett példányt eltávolítja, a bővítmény továbbra is fennáll, amelyet meg kíván keresni az és Eseménynapló hibák léphetnek fel. Ezeket a hibákat a következő két forrásból származnak: **Microsoft SQL Server hitelesítőadat-kezelés** és **Microsoft SQL Server IaaS-ügynök**. A hibák lehetnek a következőhöz hasonló:

      A hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL-kiszolgálóhoz való kapcsolódás során. A kiszolgáló nem található vagy nem érhető el.

   Ha úgy dönt, az alapértelmezett példány eltávolítása, is eltávolítja a [SQL Server infrastruktúra-szolgáltatási ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md) is.

1. **Eltávolítható az SQL Server teljesen SQL virtuális gép alapján?**

   Igen, de Ön továbbra is az SQL virtuális gép számlázni leírtak szerint [útmutatást az SQL Server Azure virtuális gépek díjszabása](virtual-machines-windows-sql-server-pricing-guidance.md). Ha már nem kell az SQL Server, egy új virtuális gép telepítéséhez, és telepítse át az adatokat és alkalmazásokat az új virtuális gépet. Az SQL Server virtuális gép távolíthatja el.
   
## <a name="updating-and-patching"></a>Frissítés és a javítás

1. **Hogyan lehet frissíteni az SQL Server egy Azure virtuális gép egy új verziójához/kiadásához számára?**

   Jelenleg nem egy Azure virtuális Gépen futó SQL Server helyszíni frissítését. Hozzon létre egy új Azure virtuális gép a kívánt SQL Server verziójához/kiadásához, majd utána áttelepíteni az adatbázisokat az új kiszolgálóra a szabványos [adatok áttelepítési technikák](virtual-machines-windows-migrate-sql.md).

1. **Hogyan frissítést és szervizcsomagot alkalmazza a rendszer egy SQL Server virtuális gépen?**

   Virtuális gépek segítségével meghatározhatja a gazdaszámítógépet, beleértve az mikor és hogyan frissítések telepítését. Az operációs rendszer, hogy kézzel alkalmazhatja a windows-frissítések, vagy engedélyezheti a nevű szolgáltatás [automatikus javítás](virtual-machines-windows-sql-automated-patching.md). Az Automatikus javítás minden fontosként megjelölt frissítést telepít, így az e kategóriába eső SQL Server-frissítéseket is. Az SQL Server egyéb nem kötelező frissítéseit manuálisan kell telepíteni.

## <a name="general"></a>Általános kérdések

1. **Azure virtuális gépeken támogatott SQL Server feladatátvevő fürt példány (FCI)?**

   Igen. Is [Windows feladatátvevő fürt létrehozása a Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) és a fürttároló közvetlen tárolóhelyek (S2D) használja. Másik megoldásként használhatja külső fürtszolgáltatás vagy a tárolási megoldások leírtak [az SQL Server Azure virtuális gépek magas rendelkezésre állási és vészhelyreállítási helyreállítási](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

1. **Mi az a különbség SQL virtuális gépek és az SQL Database szolgáltatásban?**

   Fogalmilag, SQL Server rendszert futtató Azure virtuális géphez eltér, hogy nem fut az SQL Server egy távoli adatközpontban. Ezzel szemben [SQL-adatbázis](../../../sql-database/sql-database-technical-overview.md) kínál az adatbázis-a-szolgáltatás. Az SQL Database nem rendelkezik hozzáféréssel az adatbázisok üzemeltetési gépekre. A teljes kapcsolatban lásd: [felhőalapú SQL Server-verzió: Azure SQL (PaaS) Database vagy az SQL Server Azure virtuális gépeken (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Hogyan kell telepíteni a virtuális hálózat Azure SQL-adatfájl eszközök?**

    Töltse le és telepítse az SQL-adatfájl eszközeit [Microsoft SQL Server Data Tools - Visual Studio 2013 Business Intelligence](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>További források

**Windows virtuális gépek**:

* [SQL Server Windows virtuális gép – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).
* [Egy SQL Server Windows virtuális gép kiépítése](virtual-machines-windows-portal-sql-server-provision.md)
* [Az Azure virtuális gép az SQL Server-adatbázis migrálása](virtual-machines-windows-migrate-sql.md)
* [Magas rendelkezésre álláshoz és Vészhelyreállításhoz az SQL Server Azure virtuális gépeken](virtual-machines-windows-sql-high-availability-dr.md)
* [Ajánlott eljárások az SQL Server teljesítményének Azure Virtual Machines szolgáltatásbeli növeléséhez](virtual-machines-windows-sql-performance.md)
* [Azure-beli virtuális gépeken futó SQL Server – alkalmazásminták és fejlesztési stratégiák](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux virtuális gépek**:

* [A Linux virtuális gép az SQL Server – áttekintés](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux virtuális gép kiépítése](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Gyakori kérdések (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server a Linux-dokumentáció](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
