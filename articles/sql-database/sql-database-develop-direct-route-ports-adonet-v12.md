---
title: "Port túl az 1433-as számú SQL-adatbázis |} Microsoft Docs"
description: "Az Azure SQL Database ADO.NET érkező ügyfélkapcsolatokat megkerülje a proxyt, és közvetlenül az adatbázist az eltérő 1433-as port használatával kommunikálni."
services: sql-database
author: MightyPen
manager: jhubbard
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 08/17/2016
ms.author: sstein
ms.openlocfilehash: 2f80e95b6d3828bf8c63e2346b6ba4d14bde70bb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Port túl az 1433-as ADO.NET 4.5
Ez a témakör ismerteti az Azure SQL Database kapcsolat viselkedését ADO.NET 4.5 vagy újabb verzióját használó ügyfelek számára. 

> [!IMPORTANT]
> Kapcsolat architektúra kapcsolatos információkért lásd: [Azure SQL adatbázis-kapcsolat architektúra](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Kívül és belül
Az Azure SQL Database-kapcsolatok esetén kell először megkérjük hogy fut-e az ügyfélprogram *kívül* vagy *belül* az Azure-felhőbe határ. A alszakaszokat két gyakori helyzetek tárgyalja.

#### <a name="outside-client-runs-on-your-desktop-computer"></a>*Külső:* ügyfél futtatja az asztali számítógépen
1433-as port csak a portot, amely az asztali számítógép, amelyen az SQL-adatbázis ügyfélalkalmazást nyitva kell lennie.

#### <a name="inside-client-runs-on-azure"></a>*Belső:* ügyfél futtatja az Azure-on
Amikor az ügyfél az Azure-felhőbe határán belül fut, az úgynevezett is egy *közvetlen útvonal* kommunikál az SQL Database-kiszolgálóhoz. A kapcsolat létrejötte után további az ügyfél és az adatbázis közötti kapcsolat nem Azure SQL adatbázis átjáró vonatkozhat.

A feladatütemezési a következőképpen történik:

1. Az ADO.NET 4.5 (vagy újabb) indít el az Azure felhőalapú rövid interakció, és dinamikusan azonosított portszámot kap.
   
   * A dinamikusan meghatározott portszám 11000-11999 vagy 14000-14999 a tartományban van.
2. ADO.NET ezután csatlakozik az SQL-adatbáziskiszolgáló közvetlenül, és nincs köztes a kettő között.
3. Lekérdezések adatbázissal, illetve az eredményeinek közvetlenül az ügyfél számára.

Győződjön meg arról, hogy a port, ADO.NET 4.5 ügyfél interakció SQL-adatbázis 11000-11999 és az Azure ügyfélgépre 14000-14999 tartományok pedig megmarad.

* A tartomány portokat különösen, bármilyen más kimenő blockers ingyenes kell megadni.
* Az Azure virtuális gépen a **fokozott biztonságú Windows tűzfal** portbeállítások szabályozza.
  
  * Használhatja a [tűzfal felhasználói felület](http://msdn.microsoft.com/library/cc646023.aspx) egy szabályt, amelynek meg hozzáadni a **TCP** porttartomány szintaxissal együtt protokoll, például **11000-11999**.

## <a name="version-clarifications"></a>Verzió pontosítások
Ez a szakasz tisztázza a monikerek, amely termékváltozatokra vonatkozik. Emellett néhány párosítása a verziók közötti termékeket sorolja fel.

#### <a name="adonet"></a>ADO.NET
* Az ADO.NET 4.0-s verzióját támogatja a TDS 7.3 protokollt, de nem 7.4.
* Az ADO.NET 4.5-ös vagy újabb verzióját támogatja a TDS 7.4-protokollt.

## <a name="related-links"></a>Kapcsolódó hivatkozások
* 2015. július 20 ADO.NET 4.6 lett szabadítva. Egy a .NET-csapat blogja közlemény érhető [Itt](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).
* Az ADO.NET 4.5 2012 augusztus 15 lett szabadítva. Egy a .NET-csapat blogja közlemény érhető [Itt](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
  
  * Az ADO.NET 4.5.1 kapcsolatos blogbejegyzést érhető [Itt](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).
* [TDS protokoll verzió lista](http://www.freetds.org/userguide/tdshistory.htm)
* [SQL adatbázis-fejlesztői áttekintés](sql-database-develop-overview.md)
* [Az Azure SQL Database-tűzfal](sql-database-firewall-configure.md)
* [Útmutató: az SQL Database tűzfalbeállításainak konfigurálása](sql-database-configure-firewall-settings.md)

