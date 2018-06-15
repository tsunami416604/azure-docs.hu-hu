---
title: SQL-adatbázisok használata Azure veremben |} Microsoft Docs
description: Ismerje meg, hogyan telepítheti az SQL-adatbázisok Azure verem és a Gyorsműveletek központi telepítése az SQL Server erőforrás-szolgáltató adapter szolgáltatásként.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: b1cc1fad6b0831bcf0bab5ba4f37b753c3cf33ca
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206940"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>SQL-adatbázis használata a Microsoft Azure veremben
Az SQL Server erőforrás-szolgáltató adapter segítségével teszi közzé az SQL-adatbázisok a szolgáltatásként [Azure verem](azure-stack-poc.md). Az erőforrás-szolgáltató telepítése, és csatlakoztassa egy vagy több SQL Server-példányokat, után és a felhasználók hozhat létre:
- Adatbázisok natív felhőalapú alkalmazásokhoz.
- SQL alapuló webhelyeket.
- SQL alapuló munkaterhelések.
Rendszerű virtuális gép (VM), amely futtatja az SQL Server, minden alkalommal, amikor nincs.

Az erőforrás-szolgáltató nem támogatja az összes adatbázis felügyeleti funkcióit biztosítja [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Például a rugalmas adatbáziskészletek és tárcsázza adatbázis teljesítményének felfelé és lefelé automatikusan képes nem érhetők el. Azonban az erőforrás biztosítja által támogatott szolgáltatók hasonló létrehozása, olvasása, frissítése és Törlés (CRUD) típusú műveletek. Az API nem található kompatibilis SQL-adatbázis.

## <a name="sql-resource-provider-adapter-architecture"></a>SQL erőforrás-szolgáltató adapter architektúrája
Az erőforrás-szolgáltató három összetevőből áll:

- **Az SQL erőforrás szolgáltató adapter VM**, vagyis a Windows rendszert futtató virtuális gépet a szolgáltató szolgáltatásoktól.
- **Az erőforrás-szolgáltató maga**, amely feldolgozza a kiépítési kérelmekre, és tesz elérhetővé adatbázis-erőforrások.
- **SQL Server kiszolgálók**, adatbázisok kapacitást nyújt üzemeltetési kiszolgáló neve.

Meg kell hozzon létre egy (vagy több) az SQL Server példányai és/vagy a külső SQL Server-példányokat hozzáférést biztosítanak.

> [!NOTE]
> Üzemeltető kiszolgálók Azure verem telepített integrált rendszerek bérlői előfizetéssel kell létrehozni. Az alapértelmezett szolgáltató előfizetésből nem hozhatók létre. A bérlői portálon vagy egy PowerShell munkamenetből rendelkező megfelelő bejelentkezési objektumokat létre kell hozni. Minden üzemeltetési kiszolgáló terhelhető virtuális gép, és megfelelő licenccel kell rendelkeznie. A szolgáltatás-rendszergazdát a bérlő előfizetés tulajdonosának lehet.


## <a name="next-steps"></a>További lépések

[Az erőforrás-szolgáltató SQL Server telepítése](azure-stack-sql-resource-provider-deploy.md)
