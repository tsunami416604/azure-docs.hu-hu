<properties
   pageTitle="Csatlakozás az Azure SQL Data Warehouse-hoz | Microsoft Azure"
   description="Az Azure SQL Data Warehouse-hoz való csatlakozás áttekintése"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/27/2016"
   ms.author="sonyama;barbkess"/>

# Csatlakozás az Azure SQL Data Warehouse-hoz

> [AZURE.SELECTOR]
- [Áttekintés](sql-data-warehouse-connect-overview.md)
- [Authentication](sql-data-warehouse-authentication.md)
- [Illesztőprogramok](sql-data-warehouse-connection-strings.md)

Az Azure SQL Data Warehouse-hoz való csatlakozás áttekintése. 

## A kapcsolati karakterlánc felderítése a portálon

Az SQL Data Warehouse egy Azure SQL-kiszolgálóhoz van társítva. A csatlakozáshoz szükség van a kiszolgáló teljes nevére.  Például: **myserver**.database.windows.net.

A teljes kiszolgálónév lekérdezése:

1. Nyissa meg az [Azure Portal][].
2. Kattintson az **SQL-adatbázisok** elemre, majd kattintson arra az adatbázisra, amelyhez csatlakozni kíván. Ebben a példában az AdventureWorksDW mintaadatbázist használjuk.
3. Keresse meg a teljes kiszolgálónevet.

    ![Teljes kiszolgálónév][1]

## Kapcsolati beállítások

Az SQL Data Warehouse szabványosít néhány beállítást a csatlakozás és az objektumlétrehozás során. Ezeket nem lehet felülírni.

| Adatbázis-beállítások       | Érték                        |
| :--------------------- | :--------------------------- |
| [ANSI_NULLS][]         | ON                           |
| [QUOTED_IDENTIFIERS][] | ON                           |
| [DATEFORMAT][]         | hné                          |
| [DATEFIRST][]          | 7                            |

## Kapcsolatok és lekérdezések figyelése

Ha a kapcsolat és a munkamenet létrejött, elkezdhet lekérdezéseket írni és küldeni az SQL Data Warehouse felé.  További információk a munkamenetek és lekérdezések figyeléséről: [Monitor your workload using DMVs][] (A számítási feladat figyelése DMV-k segítségével).

## Következő lépések

További információ az adatraktár lekérdezéséről a Visual Studio vagy egyéb alkalmazások használatával: [Query with Visual Studio][] (Lekérdezés a Visual Studióval). 

<!--Articles-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure Portal]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png





<!--HONumber=sep16_HO1-->


