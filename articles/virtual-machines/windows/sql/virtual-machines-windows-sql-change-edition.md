---
title: Hogyan hajthat végre egy helyszíni frissítését SQL Server Edition Azure virtuális gépen |} A Microsoft Docs
description: Megtudhatja, hogyan módosíthatja a használni kívánt az SQL Server virtuális gép az Azure-ban.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 05d2170fe9e6055179bf49d803d4739ddc05be89
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607545"
---
# <a name="how-to-perform-an-in-place-upgrade-of-sql-server-edition-on-azure-vm"></a>Hogyan hajthat végre egy helyszíni frissítését SQL Server Edition Azure virtuális gépen

Ez a cikk ismerteti az Azure-beli Windows virtuális gépen meglévő SQL Server az SQL Server kiadása módosítása. 

Az SQL Server kiadása határozza meg a termékkulcsot, és a telepítési folyamat meg van adva. A kiadás szabja meg, milyen [funkciók](/sql/sql-server/editions-and-components-of-sql-server-2017) az SQL Server-termék érhető el. Módosíthatja a telepítési adathordozó és a költségek csökkentése, vagy a verziófrissítéshez további funkciókat engedélyezni vagy alacsonyabb szintű SQL Server-kiadást.

Ha frissítette a telepítési adathordozó használatával az SQL virtuális gép erőforrás-szolgáltató regisztrálása után az SQL Server kiadása, majd frissítse az Azure számlázási ennek megfelelően kell megadniuk az SQL Server Edition tulajdonság az SQL virtuális gép erőforrás a következő:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Keresse meg az SQL Server virtuális gép típusú erőforrást. 
1. Alatt **beállítások**válassza **konfigurálása** és a legördülő listából válassza ki a kívánt SQL Server kiadása **Edition**. 

   ![Kiadás metaadatok módosítása](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Tekintse át a figyelmeztető üzenet arról tájékoztatja, hogy módosítania kell az SQL Server edition először, és a kiadás tulajdonságának meg kell egyeznie az SQL Server-kiadást. 
1. Válassza ki **alkalmaz** a kiadás metaadat-módosítások alkalmazásához. 


## <a name="prerequisites"></a>Előfeltételek

Ehhez a használni kívánt SQL Server egy helyi módosítását, a következőkre lesz szüksége: 

- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Egy Windows [SQL Server rendszerű virtuális gép](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) regisztrálva a [SQL virtuális gép erőforrás-szolgáltató](virtual-machines-windows-sql-register-with-resource-provider.md).
- A telepítő adathordozó a kívánt SQL Server kiadása. Ügyfelek, akik [frissítési garancia](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) szerezheti be a telepítési adathordozó a [mennyiségi Licencszolgáltatási központ](https://www.microsoft.com/Licensing/servicecenter/default.aspx). A felhasználóknak nem kell szoftvert garancia használhatja a telepítési adathordozóról rendszerképből egy Marketplace-en az SQL Server rendszerű virtuális gép, amely a kívánt kiadással rendelkezik.


## <a name="upgrade-edition"></a>Kiadás frissítése

  > [!WARNING]
  > - **A szolgáltatás az SQL Server, valamint a társított szolgáltatások, például az Analysis Services és az R Services újraindul a frissítés az SQL Server kiadása.** 

Frissítse az SQL Server kiadása, szerezze be a kívánt SQL Server-kiadás az SQL Server telepítési adathordozóról, és tegye a következőket:

1. Az SQL Server telepítési adathordozójáról indítsa el a Setup.exe. 
1. Navigáljon a **karbantartási** , és válassza a **Kiadásfrissítési** lehetőséget. 

   ![Frissítse az SQL Server kiadása](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Válassza ki **tovább** amíg el nem éri a **készen áll a frissítésre edition** oldalra, és kattintson **frissítése**. A telepítési ablakra lefagyhat néhány percet, amíg a módosítás érvénybe tart, és láthatja majd egy **Complete** igazolja, hogy a kiadásfrissítés teljes oldal. 

Az SQL Server-kiadás frissítése, után, ahogyan fentebb; kell módosítania az "Edition" tulajdonság az Sql virtuális gép az Azure Portalon Ez frissíti a metaadatokat és a virtuális Géphez társított számlázás.

## <a name="downgrade-edition"></a>Alacsonyabb szintű edition

  > [!WARNING]
  > - **Alacsonyabb verziójúra változtatása az SQL Server kiadása szükséges teljes eltávolításával az SQL Server, amely további állásidő felszámítunk**. 


Alacsonyabb szolgáltatásszintre váltásához az SQL Server kiadása, szüksége lesz a teljes SQL Server eltávolítása, és telepítse újra a kívánt kiadást a telepítő adathordozó. 

Az SQL Server kiadása visszaminősítheti a következő lépések végrehajtásával:

1. Készítsen biztonsági másolatot az összes adatbázis, beleértve a rendszeradatbázisokban. 
1. Helyezze át a rendszeradatbázisok (master, model és msdb) egy új helyre. 
1. Távolítsa el teljesen az SQL Server és az összes társított szolgáltatásokat. 
1. A virtuális gép újraindításához. 
1. Az SQL Server kívánt kiadásával a adathordozó használatával az SQL Server telepítése.
1. Telepítse a legújabb szervizcsomagok és kumulatív frissítéseket.  
1. Cserélje le a rendszeradatbázisok korábban áthelyezték egy másik helyre való telepítése során létrehozott új rendszeradatbázisokban. 

Miután az SQL Server aktuális kiadása van visszaminősíteni, ahogyan fentebb; kell módosítania az "Edition" tulajdonság az SQL virtuális gép az Azure Portalon Ez frissíti a metaadatokat és a virtuális Géphez társított számlázás.

## <a name="remarks"></a>Megjegyzések

 - Az SQL Server rendszerű virtuális gép a kiadás tulajdonságának meg kell egyeznie az SQL Server telepítve van a virtuális géphez tartozó összes SQL virtuális gépek beleértve a Használatalapú és a BYOL licenctípusok kiadása.
 - Törölte-e az SQL Server VM-erőforrás, állítja vissza a kódolt edition beállításait, a kép.
  - A kiadás módosításához el az SQL virtuális gép erőforrás-szolgáltató szolgáltatása. Az erőforrás-szolgáltató SQL Server virtuális gép üzembe helyezése az Azure Portalon keresztül Piactéri lemezképet automatikusan regisztrálja. Azonban ügyfelek, akik saját telepíti az SQL Server rendszer manuálisan kell [regisztrálja az SQL Server rendszerű virtuális gép](virtual-machines-windows-sql-register-with-resource-provider.md).
- Egy SQL Server rendszerű virtuális gép hozzáadása egy rendelkezésre állási csoportban van szükség a virtuális gép újbóli létrehozását. Mint ilyen, minden olyan virtuális gépeket hozzáadni egy rendelkezésre állási készlet állítja vissza az alapértelmezett kiadásra, és a kiadás ismét módosítani kell.

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Egy Windows virtuális gép az SQL Server használatának áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
* [Az SQL Server használata a Windows virtuális gép – gyakori kérdések](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server a Windows virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Az SQL Server használata a Windows virtuális gép kibocsátási megjegyzései](virtual-machines-windows-sql-server-iaas-release-notes.md)


