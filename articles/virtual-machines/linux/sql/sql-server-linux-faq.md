---
title: "SQL Server Linux Azure virtuális gépeken – gyakori kérdések |} Microsoft Docs"
description: "Ez a cikk ismerteti az Linux Azure virtuális gépeken futó SQL Server rendszert futtató gyakran feltett kérdésekre adott válaszok."
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 10/05/2017
ms.author: jroth
ms.openlocfilehash: a001ae116e33e0b7be4431b0bc4c8bb319f4e801
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Gyakori kérdések az SQL Server Linux Azure virtuális gépeken

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Ez a témakör ismerteti a leggyakoribb kérdésekre vonatkozó válaszokat [SQL Server Linux Azure Virtual Machines](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Ez a témakör ismerteti az Linux virtuális gépeken futó SQL Server adott problémák. Ha az SQL Server Windows virtuális gépeken futnak, tekintse meg a [Windows GYIK](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Gyakori kérdések

1. **Hogyan hozható létre egy Linux Azure virtuális gépen futó SQL Server?**

   A legegyszerűbb megoldás az, hogy hozzon létre egy Linux virtuális gépet, amely tartalmazza az SQL Server. Oktatóanyag regisztrál az Azure és az SQL virtuális gép létrehozása a portálon, lásd: [egy Linux SQL Server rendszerű virtuális gép az Azure portálon](provision-sql-server-linux-virtual-machine.md). Lehetősége is van manuálisan telepíti az SQL Server a virtuális gép sem szabadon licenccel rendelkező kiadásra (fejlesztői vagy Express) vagy egy helyszíni licenc újból felhasználja. Ha később saját licenc, rendelkeznie kell [Azure frissítési garancián keresztüli Licenchordozhatósági](https://azure.microsoft.com/pricing/license-mobility).

1. **Hogyan lehet frissíteni az SQL Server egy Azure virtuális gép egy új verziójához/kiadásához számára?**

   Jelenleg nem egy Azure virtuális Gépen futó SQL Server helyszíni frissítését. Hozzon létre egy új Azure virtuális gép a kívánt SQL Server verziójához/kiadásához, majd utána áttelepíteni az adatbázisokat az új kiszolgálóra történő [szabványos adatok áttelepítési technikák](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

1. **Hogyan telepíthetők a licencelt másolatával, az SQL Server egy Azure virtuális gépen?**

   Először hozzon létre egy Linux csak az operációs rendszer virtuális gép. Ezután futtassa a [SQL Server telepítési lépéseket](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) a Linux terjesztéshez. Ha telepíti az SQL Server szabadon licenccel rendelkező kiadásainak egyikét, rendelkeznie kell egy SQL Server licence és [Azure frissítési garancián keresztüli Licenchordozhatósági](https://azure.microsoft.com/pricing/license-mobility/).

1. **Vannak-e kerüljön-a-saját-licenc használata (BYOL) Linux virtuálisgép-lemezképeket az SQL Server?**

   Jelenleg nincsenek meg az SQL Server BYOL Linux virtuális gép képek. Azonban manuálisan telepíthető az SQL Server csak Linux virtuális gép a fenti kérdések leírtaknak megfelelően.

1. **Módosíthatja a saját SQL Server licence használja, ha az egyik a használatalapú lévő képek hozták létre a virtuális gépek?**

   Nem. Nem lehet átállítani a fizetési-percalapú licencelés saját licenc használatával. Kell egy új Linux virtuális gép létrehozása, az SQL Server telepítése és az adatok áttelepítését. Az előző kérdéssel állapotba hozása a saját licenc kapcsolatos további részletekért tekintse meg.

1. **Mi kapcsolódó SQL Server csomag is telepítve?**

   Alapértelmezés szerint az SQL Server Linux virtuális gépek vannak telepítve az SQL Server-csomagokat, olvassa el [telepített csomag](sql-server-linux-virtual-machines-overview.md#packages).

1. **SQL Server magas rendelkezésre állású megoldások Azure virtuális gépeken támogatottak?**

   Jelenleg nem. Always On rendelkezésre állási csoportok és a Feladatátvételi fürtszolgáltatást mindkét Linux, például a támasztja a fürtözési megoldást követel meg. A támogatott Linux disztribúciókról az SQL Server nem támogatja a magas rendelkezésre állású bővítmények a felhőben.

1. **Miért nem tudom adja meg az RHEL vagy SLES SQL Server virtuális gép Azure-előfizetéssel, amely rendelkezik beállított költségkeret maximumát?**

   RHEL és SLES virtuális gépek nem költségkeret maximumát és egy ellenőrzött és érvényes fizetési mód (általában hitelkártya) az előfizetéshez tartozó előfizetés szükséges. Az RHEL vagy SLES virtuális gép kiépítése a költségkeret maximumát eltávolítása nélkül, ha az előfizetés első letiltásra, és minden virtuális gépek és szolgáltatások leállítása. Ha futtatja az ebben az állapotban kívánja újból engedélyezni az előfizetés [távolítsa el a költségkeret maximumát](https://account.windowsazure.com/subscriptions). A maradék kreditek visszaállnak az az aktuális elszámolási időszak, de az RHEL vagy SLES méretű kép emelt díjas kerül a hitelkártya ellen, ha úgy dönt, hogy továbbra is fut, és indítsa újra.

## <a name="resources"></a>Erőforrások

**Linux virtuális gépek**:

* [A Linux virtuális gép az SQL Server – áttekintés](sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux virtuális gép kiépítése](provision-sql-server-linux-virtual-machine.md)
* [SQL Server a Linux-dokumentáció](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Windows virtuális gépek**:

* [SQL Server Windows virtuális gép – áttekintés](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Egy SQL Server Windows virtuális gép kiépítése](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Gyakori kérdések (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)