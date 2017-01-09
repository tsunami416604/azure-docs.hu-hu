---
title: "Kiszolgálószintű Azure SQL Database-tűzfalszabály konfigurálása | Microsoft Docs"
description: "A cikkből megtudhatja, hogyan konfigurálhatja a tűzfalat az Azure SQL Serverhez hozzáférő IP-címekhez."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: c3b206b5-af6e-41af-8306-db12ecfc1b5d
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 11/28/2016
ms.author: rickbyh;carlrab
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: a87bb18aeacbc980fc6859c7c83a102dce0263a8


---
# <a name="create-and-manage-azure-sql-database-server-level-firewall-rules-using-the-azure-portal"></a>Kiszolgálószintű Azure SQL Database-tűzfalszabályok létrehozása és kezelése az Azure Portalon
> [!div class="op_single_selector"]
> * [Áttekintés](sql-database-firewall-configure.md)
> * [Azure Portal](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST API](sql-database-configure-firewall-settings-rest.md)
> 

A kiszolgálószintű tűzfalszabályok lehetővé teszik a rendszergazdáknak egy SQL Database-kiszolgáló elérését adott IP-címről vagy IP-címtartományból. Kiszolgálószintű tűzfalszabályokat felhasználókra vonatkoztatva is használhat, ha több, azonos hozzáférési követelményt támasztó adatbázissal rendelkezik, és nem szeretne időt tölteni azzal, hogy egyenként konfigurálja az adatbázisokat. A Microsoft a biztonság és az adatbázis hordozhatóságának fokozása érdekében adatbázisszintű tűzfalszabályok használatát javasolja, amikor erre lehetőség van. Az SQL Database-tűzfalak áttekintéséért lásd [az SQL Database-tűzfalszabályok áttekintésével](sql-database-firewall-configure.md) foglalkozó cikket.

> [!Note]
> Az üzletmenet folytonossága és a hordozható adatbázisok közötti kapcsolatról [a vészhelyreállítás hitelesítési követelményeit](sql-database-geo-replication-security-config.md)ismertető cikkből tájékozódhat.
>

[!INCLUDE [Create SQL Database firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Meglévő kiszolgálószintű tűzfalszabályok kezelése az Azure Portalon
Ismételje meg a lépéseket a kiszolgálószintű tűzfalszabályok kezeléséhez.

* Az aktuális számítógép hozzáadásához kattintson az Ügyfél IP-címének hozzáadása elemre.
* További IP-címek hozzáadásához adja meg a Szabály neve, a Kezdő IP-cím és a Záró IP-cím értékét.
* Meglévő szabály módosításához kattintson a szabály valamelyik mezőjére, és adja meg a módosításokat.
* Meglévő szabály törléséhez vigye a mutatót a szabály fölé, és várja meg, amíg a sor végén megjelenik egy X. A szabály eltávolításához kattintson az X-re.

Kattintson a **Mentés** gombra a módosítások mentéséhez.

## <a name="next-steps"></a>Következő lépések

- Az első lépéseket bemutató útmutatót lásd az [Oktatóanyag az SQL Database használatához: Kiszolgáló, kiszolgálószintű tűzfalszabály, mintaadatbázis és adatbázisszintű tűzfalszabály létrehozása, és csatlakozás az SQL Server Management Studióhoz](sql-database-get-started.md) című témakörben.
- A biztonsággal foglalkozó bevezető oktatóanyagért lásd a [biztonságos használat első lépéseit](sql-database-get-started-security.md) ismertető témakört.
- Ha nyílt forráskódú vagy külső alkalmazásokból szeretne kapcsolódni az Azure SQL Database-hez, lásd az [SQL Database gyors üzembe helyezési ügyfélkódmintáival](https://msdn.microsoft.com/library/azure/ee336282.aspx) foglalkozó cikket.
- Ha további, adatbázis-kapcsolattal rendelkező felhasználókat szeretne létrehozni, lásd az [SQL Database hitelesítési és engedélyezési szempontjai közül a hozzáférés megadásával](https://msdn.microsoft.com/library/azure/ee336235.aspx) foglalkozó cikket.

## <a name="additional-resources"></a>További források
* [Az adatbázis biztonságossá tétele](sql-database-security-overview.md)   
* [Security Center az SQL Server adatbázismotorhoz és az Azure SQL Database-hez](https://msdn.microsoft.com/library/bb510589)   






<!--HONumber=Dec16_HO4-->


