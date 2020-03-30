---
title: SQL Server virtuális gép egy Azure dedikált gazdagépen
description: Ismerje meg az SQL Server virtuális gép Azure dedikált gazdagépen való futtatásának részleteit.
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
ms.openlocfilehash: edb2d3fa670475d9b08fe05494035949181a9240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834352"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>SQL Server virtuális gép egy Azure dedikált gazdagépen 

Ez a cikk részletezi az SQL Server virtuális gép [Azure dedikált állomással](/azure/virtual-machines/windows/dedicated-hosts)való használatának részleteit. Az Azure dedikált gazdagépéről további információt az [Azure dedikált gazdagépének bemutatása](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/)című blogbejegyzésben talál. 

## <a name="overview"></a>Áttekintés
[Az Azure Dedicated Host](/azure/virtual-machines/windows/dedicated-hosts) egy olyan szolgáltatás, amely egy Azure-előfizetésnek szentelt fizikai kiszolgálókat biztosít – amely képes egy vagy több virtuális gép üzemeltetésére. A dedikált állomások ugyanazok a fizikai kiszolgálók, amelyeket a Microsoft adatközpontjaiban használnak, és erőforrásként szolgálnak. Egy régión, rendelkezésre állási zónán és tartalék tartományon belül dedikált állomásokat hozhat létre. Ezután a virtuális gépeket közvetlenül a kiépített gazdagépekbe helyezheti, bármilyen konfigurációban, amely a legjobban megfelel az igényeinek.

## <a name="limitations"></a>Korlátozások

- A virtuálisgép-méretezési csoportok jelenleg nem támogatottak dedikált állomásokon.
- A következő virtuális gépsorozatok támogatottak: DSv3 és ESv3. 

## <a name="licensing"></a>Licencek

Két különböző licencelési lehetőség közül választhat, amikor hozzáadja az SQL Server virtuális gép ét egy Azure dedikált gazdagéphez. 

  - **SQL virtuális gép licencelése:** Ez a meglévő licencelési lehetőség, ahol minden egyes SQL Server virtuálisgép-licenc külön-külön kell fizetnie. 
  - **Dedikált gazdalicencelés:** Az Azure dedikált gazdagépszámára elérhető új licencelési modell, ahol az SQL Server-licenceket a gazdagép szintjén kötegelik és fizetik ki. 


Állomásszintű beállítások a meglévő SQL Server-licencek használatára: 
  - SQL Server Enterprise Edition Azure hibrid előny
    - Elérhető az sa-val vagy előfizetéssel rendelkező ügyfelek számára.
    - Licenc el minden rendelkezésre álló fizikai magok és élvezze a korlátlan virtualizáció (a maximális vCPU-k által támogatott állomás).
        - Az Azure Hybrid Benefit Azure dedikált gazdagépre történő alkalmazásáról az Azure Hybrid Benefit GYIK című [témakörben talál további információt.](https://azure.microsoft.com/pricing/hybrid-benefit/faq/) 
  - Október 1-je előtt beszerzett SQL Server licencek
      - Az SQL Server Enterprise edition állomásszintű és virtuális géplicenc-licenccel is rendelkezik. 
      - Az SQL Server Standard edition csak virtuális gép licenccel rendelkezik. 
          - További információt a [Microsoft termékhasználati feltételei](https://www.microsoft.com/licensing/product-licensing/products)ben talál. 
  - Ha nincs kiválasztható SQL Server dedikált állomásszintű beállítás, akkor az SQL Server AHB is kiválasztható az egyes virtuális gépek szintjén, csakúgy, mint a több-bérlős virtuális gépek.



## <a name="provisioning"></a>Kiépítés  
Egy SQL Server virtuális gép kiépítése a dedikált állomás nem különbözik bármely más Azure virtuális gép. Ezt megteheti az [Azure PowerShell](../dedicated-hosts-powershell.md), az [Azure Portal](../dedicated-hosts-portal.md)és az [Azure CLI](../../linux/dedicated-hosts-cli.md)használatával.

A meglévő SQL Server virtuális gép dedikált állomáshoz való hozzáadásának folyamata állásidőt igényel, de nem befolyásolja az adatokat, és nem lesz adatvesztés. Mindazonáltal az áthelyezés előtt minden adatbázisról, beleértve a rendszeradatbázisokat is, biztonsági másolatot kell kapnia.

## <a name="virtualization"></a>Virtualizáció 

A dedikált gazdagép egyik előnye a korlátlan virtualizáció. Például 64 virtuális magra is rendelkezhet licencekkel, de beállíthatja, hogy az állomás 128 virtuális maggal rendelkezik, így a virtuális magok kétszeresét kapja, de csak az SQL Server-licencek felét fizeti. 

Mivel ez a házigazda, akkor jogosult beállítani a virtualizáció egy 1:2 arány. 

## <a name="faq"></a>GYIK

**K: Hogyan működik az Azure Hybrid Benefit windows Server/SQL Server licencek az Azure dedikált gazdagépen?**

V: Az ügyfelek használhatják a meglévő Windows Server és SQL Server licencek a Frissítési Garancia, vagy a feljogosító előfizetési licencek, fizetni egy kedvezményes díj az Azure dedikált gazdagép az Azure Hybrid Benefit használatával. A Windows Server Datacenter és az SQL Server Enterprise Edition ügyfelei korlátlan virtualizációt kapnak (a lehető legtöbb Windows Server virtuális gépet telepíthetik a gazdagépen az alapul szolgáló kiszolgáló fizikai kapacitásának függvényében), amikor a teljes állomást licencelik és használja az Azure Hybrid Benefit.and use Azure Hybrid Benefit.  Az Azure Dedikált gazdagépben található összes Windows Server- és SQL Server-számítási feladat díjmentesen jogosult a Windows Server és az SQL Server 2008/R2 kiterjesztett biztonsági frissítéseire is. 

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Az SQL Server áttekintése Windows virtuális gépen](virtual-machines-windows-sql-server-iaas-overview.md)
* [Gyakori kérdések az SQL Server rendszerrel kapcsolatos kérdésekről Windows VM rendszeren](virtual-machines-windows-sql-server-iaas-faq.md)
* [Az SQL Server díjszabási útmutatója Windows virtuális gépen](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Kibocsátási megjegyzések az SQL Server rendszerhez Windows vM rendszeren](virtual-machines-windows-sql-server-iaas-release-notes.md)


