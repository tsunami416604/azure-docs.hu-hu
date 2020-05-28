---
title: SQL Server VM egy dedikált Azure-gazdagépen
description: További információ a SQL Server VM Azure dedikált gazdagépen való futtatásának részleteiről.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: a16ec7a3fa1f41d8c9339f84be6a0ffc3842d099
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046025"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>SQL Server VM egy dedikált Azure-gazdagépen 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk a SQL Server VM [Azure dedikált gazdagéptel](/azure/virtual-machines/windows/dedicated-hosts)való használatának részleteit részletezi. Az Azure dedikált gazdagép további információit az [Azure dedikált gazdagép bevezetését](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/)ismertető blogbejegyzésben találja. 

## <a name="overview"></a>Áttekintés
Az [Azure dedikált gazdagép](/azure/virtual-machines/windows/dedicated-hosts) olyan szolgáltatás, amely fizikai kiszolgálókat biztosít, amelyek egy vagy több virtuális gép üzemeltetésére alkalmasak egyetlen Azure-előfizetéshez. A dedikált gazdagépek ugyanazok a fizikai kiszolgálók, amelyeket a Microsoft adatközpontjai használnak erőforrásként. Dedikált gazdagépeket a régión, a rendelkezésre állási zónán és a tartalék tartományon belül is kiépítheti. Ezután elhelyezheti a virtuális gépeket közvetlenül a kiépített gazdagépeken, bármilyen konfigurációban, amely a legjobban megfelel az igényeinek.

## <a name="limitations"></a>Korlátozások

- A virtuális gépek méretezési csoportjai jelenleg nem támogatottak a dedikált gazdagépeken.
- A következő virtuálisgép-sorozatok támogatottak: DSv3 és ESv3. 

## <a name="licensing"></a>Licencelés

Két különböző licencelési lehetőség közül választhat, amikor hozzáadja a SQL Server VMt egy Azure dedikált gazdagéphez. 

  - **SQL virtuális gép licencelése**: Ez a meglévő licencelési lehetőség, ahol egyenként kell fizetnie az egyes SQL Server VM licencekhez. 
  - **Dedikált gazdagép licencelése**: az Azure dedikált gazdagéphez elérhető új licencelési modell, ahol a SQL Server licencek a gazdagép szintjén vannak csomagolva és fizetve. 


A meglévő SQL Server licencek használatára vonatkozó gazdagép-szintű beállítások: 
  - SQL Server Enterprise Edition Azure Hybrid Benefit
    - Elérhető az SA vagy előfizetéssel rendelkező ügyfelek számára.
    - Licenc az összes rendelkezésre álló fizikai magot és korlátlan virtualizációt élvez (a gazdagép által támogatott maximális vCPU).
        - A Azure Hybrid Benefit Azure dedikált gazdagépre való alkalmazásával kapcsolatos további információkért tekintse meg a [Azure Hybrid Benefit gyakori kérdések](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)című témakört. 
  - SQL Server az október 1. előtt beszerzett licencek
      - A SQL Server Enterprise kiadásban a gazdagép-és a virtuális gépek licencelési lehetőségei is megadhatók. 
      - A SQL Server Standard Edition rendszerhez csak a-VM licencelési lehetőség áll rendelkezésre. 
          - Részletekért tekintse [meg a Microsoft termék használati feltételeit](https://www.microsoft.com/licensing/product-licensing/products). 
  - Ha nincs kiválasztva SQL Server dedikált gazdagép-szintű beállítás, akkor SQL Server AHB az egyes virtuális gépek szintjén lehet kiválasztani, ugyanúgy, mint a több-bérlős virtuális gépekhez.



## <a name="provisioning"></a>Kiépítés  
A SQL Server VM dedikált gazdagépre való kiépítés nem különbözik az Azure-beli virtuális gépektől. Ezt a [Azure PowerShell](../../../virtual-machines/windows/dedicated-hosts-powershell.md), a [Azure Portal](../../../virtual-machines/windows/dedicated-hosts-portal.md)és az [Azure CLI](../../../virtual-machines/linux/dedicated-hosts-cli.md)használatával teheti meg.

Egy meglévő SQL Server VM a dedikált gazdagéphez való hozzáadásának folyamata állásidőt igényel, de nem befolyásolja az adatvesztést, és nem fog adatvesztést okozni. Ennek ellenére az áthelyezés előtt biztonsági mentést kell készíteni az összes adatbázisról, beleértve a rendszeradatbázisokat is.

## <a name="virtualization"></a>Virtualizáció 

A dedikált gazdagépek egyik előnye, hogy korlátlan a virtualizáció. Rendelkezhet például 64 virtuális mag-licenccel, de úgy is beállíthatja a gazdagépet, hogy 128-virtuális mag rendelkezzen, így megduplázhatja a virtuális mag, de csak a SQL Server licencek feléért fizet. 

Mivel ez a gazdagép, jogosult a virtualizáció 1:2 arányú beállítására. 

## <a name="faq"></a>GYIK

**K: Hogyan működik a Azure Hybrid Benefit a Windows Server/SQL Server-licencek esetében az Azure dedikált gazdagépen?**

A: a meglévő Windows Server-és SQL Server-licencek értékét frissítési garanciával vagy előfizetési licencekkel vehetik igénybe, hogy a Azure Hybrid Benefit használatával kedvezményes díjszabást fizessenek az Azure dedikált gazdagépen. A Windows Server Datacenter és a SQL Server Enterprise Edition rendszerű ügyfelek korlátlan virtualizációt kapnak (több Windows Server rendszerű virtuális gépet helyezhet üzembe a gazdagépen, a mögöttes kiszolgáló fizikai kapacitása alapján), amikor a teljes gazdagépet licencbe helyezik, és Azure Hybrid Benefit használják.  Az Azure dedikált gazdagép összes Windows Server-és SQL Server-munkaterhelése külön díj nélkül jogosult a Windows Server és a SQL Server 2008/R2 további biztonsági frissítéseire. 

## <a name="next-steps"></a>További lépések

További információért tekintse át a következő cikkeket: 

* [Windows rendszerű virtuális gépek SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows rendszerű virtuális gépen SQL Server gyakori kérdések](frequently-asked-questions-faq.md)
* [A Windows rendszerű virtuális gépek SQL Server díjszabási útmutatója](pricing-guidance.md)
* [Windows rendszerű virtuális gépen SQL Server kibocsátási megjegyzései](doc-changes-updates-release-notes.md)


