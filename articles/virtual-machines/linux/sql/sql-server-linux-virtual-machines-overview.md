---
title: Az SQL Server használatának áttekintése az Azure Virtual Machines szolgáltatásban (Linux rendszeren) | Microsoft Docs
description: Ismerje meg, hogyan futtathat teljes SQL Server-kiadásokat Azure-beli, Linux rendszerű virtuális gépeken. Ebben a cikkben közvetlen hivatkozásokat találhat az SQL Servert tartalmazó összes linuxos virtuálisgép-rendszerképhez és a kapcsolódó tartalmakhoz.
services: virtual-machines-linux
documentationcenter: ''
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: jroth
ms.openlocfilehash: 9c24536d8d5647e4a2c19afa17c35050e1f11c20
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31424122"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Az SQL Server használatának áttekintése az Azure Virtual Machines szolgáltatásban (Linux rendszeren)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

Az Azure virtuális gépeken futó SQL Serverrel teljes SQL Server-verziókat használhat a felhőben anélkül, hogy bármilyen helyszíni hardvert kellene fenntartania. Az SQL Servert futtató virtuális gépekkel a használatalapú licencelési költségek is leegyszerűsödnek.

Világszerte számos [földrajzi régióban](https://azure.microsoft.com/regions/) üzemelnek Azure virtuális gépek. Több különböző [gépméret](../sizes.md) is elérhető. A virtuális gépek rendszerkép-katalógusából a megfelelő verziójú, kiadású és operációs rendszerű, SQL Servert futtató virtuális gépet hozhat létre. Ez számos különböző SQL Server számítási feladatra teszi alkalmassá a virtuális gépeket.

## <a id="create"></a> SQL virtuális gépek – első lépések

Első lépésként válassza ki a megfelelő verziójú, kiadású és operációs rendszerű SQL Server virtuális gép rendszerképét. Az alábbi szakaszokban közvetlen hivatkozások találhatók az Azure Portalra az SQL Server virtuálisgép-katalógus rendszerképeinek letöltéséhez.

> [!TIP]
> Az SQL-rendszerképek díjszabásával kapcsolatos további információkért tekintse meg [az SQL Servert futtató Linux rendszerű virtuális gépek díjszabási lapját](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

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

## <a name="related-products-and-services"></a>Kapcsolódó termékek és szolgáltatások

### <a name="linux-virtual-machines"></a>Linux rendszerű virtuális gépek

* [Virtual Machines – áttekintés](../overview.md)

### <a name="storage"></a>Tárolás

* [A Microsoft Azure Storage bemutatása](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Hálózat

* [A Virtual Network áttekintése](../../../virtual-network/virtual-networks-overview.md)
* [IP-címek az Azure-ban](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Teljes tartománynév létrehozása az Azure Portalon](../portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [A Linux rendszeren futó SQL Server dokumentációja](https://docs.microsoft.com/sql/linux)
* [Az Azure SQL Database összehasonlítása](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>További lépések

Az SQL Server használatbavétele Azure-beli, Linux rendszerű virtuális gépeken:

* [SQL Servert futtató virtuális gép létrehozása az Azure Portalon](provision-sql-server-linux-virtual-machine.md)

Választ találhat a Linux rendszerű SQL virtuális gépek használatával kapcsolatos gyakori kérdésekre:

* [Az SQL Server használata Azure-beli, Linux rendszerű virtuális gépeken – gyakori kérdések](sql-server-linux-faq.md)
