---
title: "Az SQL Server használatának áttekintése az Azure Virtual Machines szolgáltatásban (Linux rendszeren) | Microsoft Docs"
description: "Ismerje meg, hogyan futtathat teljes SQL Server-kiadásokat Azure-beli, Linux rendszerű virtuális gépeken. Ebben a cikkben közvetlen hivatkozásokat találhat az SQL Servert tartalmazó összes linuxos virtuálisgép-rendszerképhez és a kapcsolódó tartalmakhoz."
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.workload: iaas-sql-server
ms.date: 10/02/2017
ms.author: jroth
ms.openlocfilehash: f044fcba92eb7aa7f4ac44608571fab3db0e03f5
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Az SQL Server használatának áttekintése az Azure Virtual Machines szolgáltatásban (Linux rendszeren)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

Ez a témakör az SQL Server Azure-beli, Linux rendszerű virtuális gépeken történő futtatásának lehetőségeit ismerteti, valamint [hivatkozásokat tartalmaz a portálon elérhető rendszerképekhez](#option-1-create-a-sql-vm-with-per-minute-licensing).

> [!NOTE]
> Ha már ismeri az SQL Server használatát, és csak azt szeretné megtudni, hogyan helyezhet üzembe egy SQL Servert futtató, Linux rendszerű virtuális gépet, tekintse meg az [SQL Servert futtató, Linux rendszerű virtuális gép az Azure szolgáltatásban történő létrehozását](provision-sql-server-linux-virtual-machine.md) ismertető cikket. Ha pedig egy Windows rendszerű virtuális gépet szeretne létrehozni egy SQL Server futtatásához, tekintse meg az [SQL Servert futtató, Windows rendszerű virtuális gép az Azure szolgáltatásban történő létrehozását](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md) ismertető cikket.

Amennyiben Ön adatbázis-rendszergazda vagy fejlesztő, az Azure virtuális gépek lehetőséget nyújtanak a helyszíni SQL Server számítási feladatok és alkalmazások áthelyezésére a felhőbe.

## <a name="scenarios"></a>Forgatókönyvek

Számos oka lehet annak, amiért úgy dönt, az adatokat az Azure-ban tárolja. Ha az Azure-hoz fejleszti vagy arra migrálja az alkalmazását, akkor megnövelheti a teljesítményt, ha a háttéradatokat is az Azure-ban tárolja. Automatikusan hozzáférést kap több különböző adatközponthoz is a globális jelenlét érdekében, illetve vészhelyreállítás esetére. Emellett az adatok is tartósak, és magas szintű biztonság védi őket.

Az Azure virtuális gépeken futó SQL Server a relációs adatok Azure-ban való tárolásának egyik módja. Lehetősége van az Azure SQL Database szolgáltatás használatára is. A virtuális gépen futó SQL Server és az Azure SQL Database közti választásról további információt a [felhőalapú SQL Server-verzió kiválasztását (Azure SQL Database (PaaS) adatbázis vagy az Azure virtuális gépeken futó SQL Server (IaaS))](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md) ismertető cikkben talál.

## <a id="create"></a>Új SQL virtuális gép létrehozása

Az új SQL-alapú virtuális gép létrehozásának részletes útmutatóját a [Linux rendszerű SQL Server virtuális gép az Azure-ban történő létrehozását](provision-sql-server-linux-virtual-machine.md) ismertető cikkben találja.

A következő táblázat a virtuálisgép-katalógus legújabb SQL Server-rendszerképeit foglalja össze. Kattintson bármelyik hivatkozásra egy új SQL virtuális gép létrehozásának megkezdéséhez az Ön által választott verzióval, kiadással és operációs rendszerrel.

> [!TIP]
> Az ezekhez a rendszerképekhez tartozó virtuális gépek és az SQL díjszabásának megismeréséhez tekintse meg [a Linux rendszerű SQL Server-virtuálisgépek díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Verzió | Operációs rendszer | Kiadás |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> A Windows rendszerhez elérhető SQL Server-virtuálisgépek rendszerképeivel kapcsolatban lásd [az SQL Server használatának áttekintését az Azure Virtual Machines szolgáltatásban (Windows rendszeren)](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).

## <a id="packages"></a> Telepített csomagok

A Linuxon futó SQL Server konfigurálásakor a telepítő telepíti az adatbázismotor csomagjait, majd több választható csomagot is, a felhasználó igényei szerint. A Linux rendszerű SQL Server-virtuálisgépek rendszerképei automatikusan telepítik a legtöbb csomagot. A következő táblázat ismerteti, hogy melyik disztribúció melyik csomagokat telepíti.

| Disztribúció | [Adatbázismotor](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Eszközök](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server Agent](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Teljes szöveges keresés](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Magas rendelkezésreállási bővítmény](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![igen](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![igen](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![igen](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![igen](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![igen](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![nem](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![igen](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![igen](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![igen](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![igen](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![nem](./media/sql-server-linux-virtual-machines-overview/no.png) | ![nem](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![igen](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![igen](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![igen](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![igen](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![igen](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![igen](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="next-steps"></a>Következő lépések

További információkat a Linuxon futó SQL Server konfigurálásáról és használatáról [a Linuxon futó SQL Server áttekintésében](https://docs.microsoft.com/sql/linux/sql-server-linux-overview) talál.
