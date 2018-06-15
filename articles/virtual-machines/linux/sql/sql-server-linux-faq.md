---
title: SQL Server Linux Azure virtuális gépeken – gyakori kérdések |} Microsoft Docs
description: Ez a cikk ismerteti az Linux Azure virtuális gépeken futó SQL Server rendszert futtató gyakran feltett kérdésekre adott válaszok.
services: virtual-machines-linux
documentationcenter: ''
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: jroth
ms.openlocfilehash: 56a0629249cdb5f0f098d2b7b6d36b3fbb215009
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30190870"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Gyakori kérdések az SQL Server Linux Azure virtuális gépeken

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Ez a cikk ismerteti a leggyakoribb kérdésekre vonatkozó válaszokat [SQL Server Linux Azure Virtual Machines](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Ez a cikk foglalkozik az SQL Serverhez a Linux virtuális gépek adott problémák. Ha az SQL Server Windows virtuális gépeken futnak, tekintse meg a [Windows GYIK](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Lemezképek

1. **Milyen SQL Server virtuális gép a gyűjtemény lemezképei állnak rendelkezésre?**

   Azure Linux és a Windows az összes támogatott kiadások az SQL Server összes kiadásában virtuálisgép-rendszerképek kezeli. További részletekért lásd: a teljes listáját [Linux Virtuálisgép-rendszerképek](sql-server-linux-virtual-machines-overview.md#create) és [Windows Virtuálisgép-rendszerképek](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **Meglévő SQL Server virtuális gépen lévő képek frissítése?**

   Minden két hónapon belülre esik a virtuális gép galériában képek SQL Server frissítése a legújabb Linux és Windows-frissítések. A Linux-lemezképek ilyenek például a rendszer legújabb frissítéseit. A Windows lemezképeit ilyenek például a Windows Update, beleértve a fontos SQL Server biztonsági frissítések és szervizcsomagok fontos megjelölt frissítéseket. SQL Server összegző frissítései a Linux és a Windows másképp kezeli. Linux SQL Server összegző frissítései is szerepelni fog a frissítést. De ilyenkor a Windows virtuális gépek nem frissíti az SQL Server vagy Windows Server összegző frissítések.

1. **Mi kapcsolódó SQL Server csomag is telepítve?**

   Alapértelmezés szerint az SQL Server Linux virtuális gépek vannak telepítve az SQL Server-csomagokat, olvassa el [telepített csomag](sql-server-linux-virtual-machines-overview.md#packages).

1. **SQL Server virtuálisgép-rendszerképek beolvasása távolíthatók el a gyűjtemény?**

   Igen. Azure csak egy kép / fő verzióját és kiadását tart fenn. Például egy új SQL Server szervizcsomag megjelenésekor, Azure ad hozzá egy új lemezképet szervizcsomagnak gyűjteménye. Az SQL Server-rendszerképet az előző service Pack azonnal távolítja el az Azure-portálon. Azonban célszerű továbbra is elérhető, a következő három hónapban a Powershellből kiépítéshez. Három hónap után az előző service pack kép már nem érhető el. Az eltávolítási házirend csak akkor is vonatkozik, ha egy SQL Server-verzió nem támogatott válik, amikor eléri a életciklusának végét.

## <a name="creation"></a>Létrehozása

1. **Hogyan hozható létre egy Linux Azure virtuális gépen futó SQL Server?**

   A legegyszerűbb megoldás az, hogy hozzon létre egy Linux virtuális gépet, amely tartalmazza az SQL Server. Oktatóanyag regisztrál az Azure és az SQL virtuális gép létrehozása a portálon, lásd: [egy Linux SQL Server rendszerű virtuális gép az Azure portálon](provision-sql-server-linux-virtual-machine.md). Lehetősége is van manuálisan telepíti az SQL Server a virtuális gép sem szabadon licenccel rendelkező kiadásra (fejlesztői vagy Express) vagy egy helyszíni licenc újból felhasználja. Ha később saját licenc, rendelkeznie kell [Azure frissítési garancián keresztüli Licenchordozhatósági](https://azure.microsoft.com/pricing/license-mobility).

1. **Miért nem tudom adja meg az RHEL vagy SLES SQL Server virtuális gép Azure-előfizetéssel, amely rendelkezik beállított költségkeret maximumát?**

   RHEL és SLES virtuális gépek nem költségkeret maximumát és egy ellenőrzött és érvényes fizetési mód (általában hitelkártya) az előfizetéshez tartozó előfizetés szükséges. Az RHEL vagy SLES virtuális gép kiépítése a költségkeret maximumát eltávolítása nélkül, ha az előfizetés első letiltásra, és minden virtuális gépek és szolgáltatások leállítása. Ha futtatja az ebben az állapotban kívánja újból engedélyezni az előfizetés [távolítsa el a költségkeret maximumát](https://account.windowsazure.com/subscriptions). A maradék kreditek visszaállnak az az aktuális elszámolási időszak, de az RHEL vagy SLES méretű kép emelt díjas kerül a hitelkártya ellen, ha úgy dönt, hogy továbbra is fut, és indítsa újra.

## <a name="licensing"></a>Licencelés

1. **Hogyan telepíthetők a licencelt másolatával, az SQL Server egy Azure virtuális gépen?**

   Először hozzon létre egy Linux csak az operációs rendszer virtuális gép. Ezután futtassa a [SQL Server telepítési lépéseket](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) a Linux terjesztéshez. Ha telepíti az SQL Server szabadon licenccel rendelkező kiadásainak egyikét, rendelkeznie kell egy SQL Server licence és [Azure frissítési garancián keresztüli Licenchordozhatósági](https://azure.microsoft.com/pricing/license-mobility/).

1. **Vannak-e kerüljön-a-saját-licenc használata (BYOL) Linux virtuálisgép-lemezképeket az SQL Server?**

   Jelenleg nincsenek meg az SQL Server BYOL Linux virtuális gép képek. Azonban manuálisan telepíthető az SQL Server csak Linux virtuális gép a fenti kérdések leírtaknak megfelelően.

1. **Módosíthatja a saját SQL Server licence használja, ha az egyik a használatalapú lévő képek hozták létre a virtuális gépek?**

   Nem. Nem lehet átállítani a saját licenc használata licencelési fizetési / másodperc. Kell egy új Linux virtuális gép létrehozása, az SQL Server telepítése és az adatok áttelepítését. Az előző kérdéssel állapotba hozása a saját licenc kapcsolatos további részletekért tekintse meg.

## <a name="administration"></a>Adminisztráció

1. **Kezelheti egy Linux SQL Server virtuális gép az SQL Server Management Studio (SSMS)?**

   Igen, de SSMS jelenleg csak Windows eszköz. Mindenképpen kapcsolódnia kell távolról SSMS Linux SQL Server virtuális gépen használni kívánt Windows-gépről. Helyileg a Linux az új [mssql-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) eszköz számos felügyeleti feladatokat hajthat végre. Platformok közötti adatbázis-felügyeleti eszköz megtekintéséhez lásd: [SQL Server Operations Studio (előzetes verzió)](https://docs.microsoft.com/sql/sql-operations-studio/what-is).

1. **Eltávolítható az SQL Server teljesen SQL virtuális gép alapján?**

   Igen, de Ön továbbra is az SQL virtuális gép számlázni leírtak szerint [útmutatást az SQL Server Azure virtuális gépek díjszabása](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Ha már nem kell az SQL Server, egy új virtuális gép telepítéséhez, és telepítse át az adatokat és alkalmazásokat az új virtuális gépet. Az SQL Server virtuális gép távolíthatja el.

## <a name="updating-and-patching"></a>Frissítés és a javítás

1. **Hogyan lehet frissíteni az SQL Server egy Azure virtuális gép egy új verziójához/kiadásához számára?**

   Jelenleg nem egy Azure virtuális Gépen futó SQL Server helyszíni frissítését. Hozzon létre egy új Azure virtuális gép a kívánt SQL Server verziójához/kiadásához, majd utána áttelepíteni az adatbázisokat az új kiszolgálóra történő [szabványos adatok áttelepítési technikák](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Általános kérdések

1. **SQL Server magas rendelkezésre állású megoldások Azure virtuális gépeken támogatottak?**

   Jelenleg nem. Always On rendelkezésre állási csoportok és a Feladatátvételi fürtszolgáltatást mindkét Linux, például a támasztja a fürtözési megoldást követel meg. A támogatott Linux disztribúciókról az SQL Server nem támogatja a magas rendelkezésre állású bővítmények a felhőben.

## <a name="resources"></a>További források

**Linux virtuális gépek**:

* [A Linux virtuális gép az SQL Server – áttekintés](sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux virtuális gép kiépítése](provision-sql-server-linux-virtual-machine.md)
* [SQL Server a Linux-dokumentáció](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Windows virtuális gépek**:

* [SQL Server Windows virtuális gép – áttekintés](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Egy SQL Server Windows virtuális gép kiépítése](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Gyakori kérdések (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)