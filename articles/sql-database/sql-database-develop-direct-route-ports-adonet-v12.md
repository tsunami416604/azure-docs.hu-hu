---
title: 1433-as portok
description: A ADO.NET és az Azure SQL Database közötti ügyfélkapcsolatok megkerülhetik a proxyt, és közvetlenül kommunikálhatnak az adatbázissal az 1433-as portoktól eltérő portok használatával.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 04/03/2019
ms.openlocfilehash: c0012b61cf43d01afd5e7f5f52948310b5eb8420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73828061"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Az 1433-as porton túli portok az ADO.NET 4.5 szoftverrel

Ez a témakör az Azure SQL Database-kapcsolat viselkedését ismerteti a 4.5-ADO.NET ös vagy újabb verziót használó ügyfelek számára.

> [!IMPORTANT]
> A kapcsolódási architektúráról az [Azure SQL Database kapcsolódási architektúrája című témakörben](sql-database-connectivity-architecture.md)talál további információt.
>

## <a name="outside-vs-inside"></a>Kívül és belül

Az Azure SQL Database-hez való kapcsolatok esetén először meg kell kérdeznünk, hogy az ügyfélprogram az Azure felhőhatárán *kívül* vagy *belül* fut-e. Az alfejezetek két gyakori forgatókönyvet vitatnak meg.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Kívül:* Az ügyfél az asztali számítógépen fut

Az 1433-as port az egyetlen olyan port, amelynek meg kell nyitva lennie az asztalon lévő számítógépen, amelyen az SQL Database ügyfélalkalmazás található.

### <a name="inside-client-runs-on-azure"></a>*Belül:* Az ügyfél fut az Azure-on

Amikor az ügyfél fut az Azure felhőhatárán belül, azt használja, amit úgy *nevezhetünk, hogy egy közvetlen útvonalat* az SQL Database-kiszolgálóval való interakció. A kapcsolat létrejötte után az ügyfél és az adatbázis közötti további interakciók nem járnak az Azure SQL Database Gateway használatával.

A sorrend a következő:

1. ADO.NET 4.5 (vagy újabb) rövid interakciót kezdeményez az Azure-felhővel, és dinamikusan azonosított portszámot kap.

   * A dinamikusan azonosított portszám az 11000-11999 tartományban van.
2. ADO.NET ezután közvetlenül csatlakozik az SQL Database kiszolgálóhoz, köztes szoftver nélkül.
3. A lekérdezések közvetlenül az adatbázisba kerülnek, és az eredményeket közvetlenül az ügyfélnek küldik vissza.

Győződjön meg arról, hogy az Azure-ügyfélgépen lévő 11000-11999-es porttartományok ADO.NET 4.5-ös ügyfélkapcsolati műveletekhez állnak rendelkezésre az SQL Database-ADO.NET.

* A tartomány portjainak különösen mentesnek kell lenniük minden más kimenő blokkolótól.
* Az Azure virtuális gép, a **Windows tűzfal speciális biztonsági** szabályozza a port beállításait.
  
  * A tűzfal [felhasználói felületén](https://msdn.microsoft.com/library/cc646023.aspx) hozzáadhat egy szabályt, amelyhez a **TCP** protokollt az **11000-11999**szintaxisú porttartománysal együtt adja meg.

## <a name="version-clarifications"></a>A verzió pontosításai

Ez a szakasz tisztázza a termékverziókra hivatkozó monikereket. Azt is felsorolja néhány párosítás a verziók között termékek.

### <a name="adonet"></a>ADO.NET

* ADO.NET 4.0 támogatja a TDS 7.3 protokollt, de a 7.4-et nem.
* ADO.NET 4.5-ös és újabb verziói támogatják a TDS 7.4 protokollt.

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 vagy újabb verzió

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4.2 vagy újabb (a JDBC 4.0 ténylegesen támogatja a TDS 7.4-et, de nem valósítja meg az "átirányítást")

## <a name="related-links"></a>Kapcsolódó hivatkozások

* 2015. július 20-án jelent meg a ADO.NET 4.6. A blog bejelentése a .NET csapat [itt](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-net-framework-4-6.aspx)érhető el .
* ADO.NET 2012. augusztus 15-én jelent meg. A blog bejelentése a .NET csapat [itt](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx)érhető el .
  * A blogbejegyzést a ADO.NET 4.5.1 [itt](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-net-framework-4-5-1-preview.aspx)érhető el .

* Microsoft® ODBC Driver 17 SQL Serverhez® - Windows, Linux, & macOShttps://www.microsoft.com/download/details.aspx?id=56567

* Csatlakozás az Azure SQL Database V12-höz átirányítássalhttps://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [TDS protokoll verziólistája](https://www.freetds.org/userguide/tdshistory.htm)
* [SQL-adatbázis-fejlesztés – áttekintés](sql-database-develop-overview.md)
* [Az Azure SQL Database tűzfala](sql-database-firewall-configure.md)
* [Útmutató: A tűzfal beállításainak konfigurálása az SQL Database szolgáltatásban](sql-database-configure-firewall-settings.md)


