---
title: Az SQL Server használata Azure virtuális gép kibocsátási megjegyzései |} A Microsoft Docs
description: Ismerje meg az új funkciók és fejlesztések az SQL Server-beli virtuális gépen
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2018
ms.author: mathoma
ms.openlocfilehash: 44aee447c7f935cd67ca55902c53c5f6f9eb6fda
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52500350"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Az SQL Server használata Azure virtuális gép kibocsátási megjegyzései

Az Azure lehetővé teszi, hogy az SQL Server beépített rendszerképpel rendelkező virtuális gép üzembe helyezése. Ez a cikk felsorolja az új funkciók és fejlesztések, amelyek az SQL Server Azure virtuális gépként telepítve a legújabb verzió. 


## <a name="november-2018"></a>A 2018. november
- **Új SQL típusú erőforrás-szolgáltató**: egy új erőforrás-szolgáltató az SQL virtuális gépek, amelyek jobb kezelése érdekében a virtuális gép van. További információ a virtuális gép regisztrálása: [örökölt SQL virtuális gép új erőforrás-szolgáltató regisztrálásához](virtual-machines-windows-sql-ahb.md#register-legacy-sql-vm-with-new-resource-provider).
- **Váltson a licencelési modell**: mostantól válthat az SQL virtuális gép Azure parancssori felület vagy a Powershell használatával a használatalapú-per-használat és a bring-your-own-license modell között. További információkért lásd: [SQL virtuális gép licencelési modelljét módosítása](virtual-machines-windows-sql-ahb.md)



## <a name="additional-resources"></a>További források

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
