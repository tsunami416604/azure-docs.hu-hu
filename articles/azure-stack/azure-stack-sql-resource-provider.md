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
ms.date: 06/21/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 55d0e51606e8768a01c0b5a7766dbafe24d97a0d
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307825"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>SQL-adatbázis használata a Microsoft Azure veremben

A használatára az SQL Server erőforrás szolgáltató API teszi közzé az SQL-adatbázisok a szolgáltatásként [Azure verem](azure-stack-poc.md). Az erőforrás-szolgáltató telepítése, és csatlakoztassa egy vagy több SQL Server-példányokat, után és a felhasználók hozhat létre:

- Adatbázisok natív felhőalapú alkalmazásokhoz.
- SQL használó webhelyekhez.
- SQL használó munkaterhelések.

Az erőforrás-szolgáltató nem adja meg az adatbázis felügyeleti képességek a [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Például, hogy automatikusan erőforrásokat rugalmas készletek nem támogatottak. Azonban hasonló erőforrás-szolgáltató támogatja létrehozása, olvasása, frissítése és Törlés (CRUD) típusú műveletek az SQL Server-adatbázis. Az erőforrás-szolgáltató API kapcsolatos további információkért lásd: [Windows Azure Pack SQL Server erőforrás szolgáltató REST API-referencia](https://msdn.microsoft.com/library/dn528529.aspx).

>[!NOTE]
Az SQL Server erőforrás-szolgáltató API nem kompatibilis az Azure SQL Database.

## <a name="sql-resource-provider-adapter-architecture"></a>SQL erőforrás-szolgáltató adapter architektúrája

Az erőforrás-szolgáltató a következő összetevőkből áll:

- **Az SQL erőforrás szolgáltató adapter virtuális gép (VM)**, vagyis a szolgáltató-szolgáltatásokat futtató Windows Server virtuális gép.
- **Az erőforrás-szolgáltató**, amely feldolgozza a kérelmeket, és hozzáférések adatbázis-erőforrásokat.
- **SQL Server kiszolgálók**, adatbázisok kapacitást nyújt üzemeltetési kiszolgáló neve.

Létre kell hoznia legalább egy példányát az SQL Server vagy külső SQL Server-példányokat hozzáférést biztosítanak.

> [!NOTE]
> Üzemeltető kiszolgálók Azure verem telepített integrált rendszerek bérlői előfizetéssel kell létrehozni. Az alapértelmezett szolgáltató előfizetésből nem hozhatók létre. A bérlői portálon vagy a PowerShell használatával a megfelelő bejelentkezés objektumokat létre kell hozni. Minden üzemeltetési kiszolgáló számlázható virtuális gépeket, és rendelkeznie kell licencekkel. A szolgáltatás-rendszergazdát a bérlő előfizetés tulajdonosának lehet.

## <a name="next-steps"></a>További lépések

[Az erőforrás-szolgáltató SQL Server telepítése](azure-stack-sql-resource-provider-deploy.md)
