---
title: Az SQL Server kiadás helybeni módosítása
description: Ismerje meg, hogyan módosíthatja az SQL Server virtuális gép kiadását az Azure-ban.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: bd9622d3e34198402bc45e5d855d05b0c8e35eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605460"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Az SQL Server-kiadás helybeni módosítása az Azure VM-en

Ez a cikk azt ismerteti, hogyan módosíthatja az SQL Server kiadását egy Windows virtuális gépen az Azure-ban. 

Az SQL Server kiadását a termékkulcs határozza meg, és a telepítési folyamat során a telepítési adathordozót használja. A kiadás határozza meg, hogy milyen [szolgáltatások](/sql/sql-server/editions-and-components-of-sql-server-2017) érhetők el az SQL Server termékben. Az SQL Server kiadást a telepítési adathordozóval módosíthatja, és a költségek csökkentése érdekében leminősítheti, vagy további szolgáltatások engedélyezéséhez frissítheti a frissítést.

Miután az SQL Server kiadása belsőleg megváltozott az SQL Server virtuális gépre, frissítenie kell az SQL Server kiadástulajdonságát az Azure Portalon számlázási célokra. 

## <a name="prerequisites"></a>Előfeltételek

Az SQL Server kiadásának helybeni módosításához a következőkre van szükség: 

- [Egy Azure-előfizetés](https://azure.microsoft.com/free/).
- Az SQL Server Windows rendszeren regisztrált [virtuális gépe](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) az [SQL VM erőforrás-szolgáltatónál](virtual-machines-windows-sql-register-with-resource-provider.md)regisztrálva van.
- Adathordozó beállítása az SQL Server **kívánt kiadásával.** A [Frissítési Garanciával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) rendelkező ügyfelek a mennyiségi [licencelési központtól](https://www.microsoft.com/Licensing/servicecenter/default.aspx)szerezhetik be telepítési adathordozójukat. A frissítési garanciával nem rendelkező ügyfelek használhatják a telepítőadathordozót egy Olyan Azure Marketplace SQL `C:\SQLServerFull`Server virtuális géplemezről, amely rendelkezik a kívánt kiadással (általában a rendszerben). 


## <a name="upgrade-an-edition"></a>Kiadás frissítése

> [!WARNING]
> Az SQL Server kiadásának frissítése újraindítja az SQL Server szolgáltatását, valamint a kapcsolódó szolgáltatásokat, például az Analysis Services-t és az R Services szolgáltatást. 

Az SQL Server kiadásának frissítéséhez szerezze be az SQL Server kívánt kiadásához szükséges SQL Server telepítőadathordozót, majd tegye a következőket:

1. Nyissa meg a Setup.exe programot az SQL Server telepítési adathordozójáról. 
1. Nyissa meg a **Karbantartás** lehetőséget, és válassza a **Kiadásfrissítés** lehetőséget. 

   ![Az SQL Server kiadásának frissítéséhez szükséges kijelölés](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Válassza a **Tovább** gombot, amíg el nem éri a **Ready to upgrade edition** lapot, majd válassza a **Frissítés**lehetőséget. Előfordulhat, hogy a beállítási ablak néhány percig nem válaszol, amíg a módosítás érvénybe lép. A **Teljes** oldal megerősíti, hogy a kiadás frissítése befejeződött. 

Az SQL Server-kiadás frissítése után módosítsa az SQL Server virtuális gép kiadástulajdonságát az Azure Portalon. Ez frissíti a virtuális géphez társított metaadatokat és számlázást.

## <a name="downgrade-an-edition"></a>Kiadás visszaminősítése

Az SQL Server kiadásának visszaminősítéséhez teljesen el kell távolítania az SQL Server alkalmazást, majd újra kell telepítenie a kívánt kiadástelepítő adathordozóval. 

> [!WARNING]
> Az SQL Server eltávolítása további állásidőt vonhat maga után. 

Az SQL Server kiadását az alábbi lépésekkel csökkentheti:

1. Biztonsági másolatot az összes adatbázisról, beleértve a rendszeradatbázisokat is. 
1. A rendszeradatbázisok (főadatbázis, modell és msdb) áthelyezése új helyre. 
1. Teljesen távolítsa el az SQL Servert és az összes kapcsolódó szolgáltatást. 
1. Indítsa újra a virtuális gépet. 
1. Telepítse az SQL Server alkalmazást az adathordozó használatával az SQL Server kívánt kiadásával.
1. Telepítse a legújabb szervizcsomagokat és az összesítő frissítéseket.  
1. Cserélje le a telepítés során létrehozott új rendszeradatbázisokat azokra a rendszeradatbázisokra, amelyeket korábban egy másik helyre helyezett át. 

Az SQL Server-kiadás visszaminősítése után módosítsa az SQL Server virtuális gép kiadástulajdonságát az Azure Portalon. Ez frissíti a virtuális géphez társított metaadatokat és számlázást.

## <a name="change-edition-in-portal"></a>Kiadás módosítása a portálon 

Miután módosította az SQL Server kiadását a telepítési adathordozó használatával, és regisztrálta az SQL Server virtuális gépét az [SQL VM erőforrás-szolgáltatóval,](virtual-machines-windows-sql-register-with-resource-provider.md)majd az Azure Portal segítségével módosíthatja az SQL Server virtuális gép Edition tulajdonságát számlázási célokra. Ehhez kövesse az alábbi lépéseket: 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 
1. Nyissa meg az SQL Server virtuálisgép-erőforrását. 
1. A **Beállítások csoportban**válassza a **Konfigurálás**lehetőséget. Ezután válassza ki az SQL Server kívánt kiadását az **Edition**legördülő listából. 

   ![Kiadás metaadatainak módosítása](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Tekintse át azt a figyelmeztetést, amely szerint először módosítania kell az SQL Server kiadást, és hogy a kiadástulajdonságnak meg kell egyeznie az SQL Server edition kiadással. 
1. Válassza az **Alkalmaz** lehetőséget a kiadás metaadat-módosításainak alkalmazásához. 


## <a name="remarks"></a>Megjegyzések

- Az SQL Server virtuális gép kiadásának meg kell egyeznie az összes SQL Server virtuális gépen telepített SQL Server-példány kiadásával, beleértve a felosztó-kirovó és a saját licenctípusokat is.
- Ha eldobja az SQL Server virtuálisgép-erőforrást, visszatér a lemezkép kódolható kiadásának beállításához.
- A kiadás módosításának lehetősége az SQL VM erőforrás-szolgáltató egyik jellemzője. Az Azure Marketplace-rendszerkép üzembe helyezése az Azure Portalon keresztül automatikusan regisztrálja az SQL Server virtuális gépet az erőforrás-szolgáltatóval. Az SQL Server öntelepítő ügyfeleinek azonban manuálisan kell [regisztrálniuk az SQL Server virtuális gépüket.](virtual-machines-windows-sql-register-with-resource-provider.md)
- Egy SQL Server virtuális gép hozzáadása egy rendelkezésre állási csoporthoz újra létre kell hoznia a virtuális gép. A rendelkezésre állási készlethez hozzáadott virtuális gépek visszaállnak az alapértelmezett kiadásra, és a kiadást újra módosítani kell.

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Az SQL Server áttekintése Windows virtuális gépen](virtual-machines-windows-sql-server-iaas-overview.md)
* [Gyakori kérdések az SQL Server rendszerrel kapcsolatos kérdésekről Windows VM rendszeren](virtual-machines-windows-sql-server-iaas-faq.md)
* [Az SQL Server díjszabási útmutatója Windows virtuális gépen](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Kibocsátási megjegyzések az SQL Server rendszerhez Windows vM rendszeren](virtual-machines-windows-sql-server-iaas-release-notes.md)


