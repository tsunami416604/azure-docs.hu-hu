<properties
    pageTitle="Csatlakozás SQL Database adatbázishoz C# lekérdezéssel | Microsoft Azure"
    description="Program írása C# programozási nyelven lekérdezéséhez és SQL-adatbázishoz való csatlakozáshoz. Információk IP-címekről, kapcsolati sztringekről, biztonságos bejelentkezésről és ingyenes Visual Studio."
    services="sql-database"
    keywords="c# database query, c# query, connect to database, SQL C#"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="04/25/2016"
    ms.author="annemill"/>


# Kapcsolódás SQL Database adatbázishoz Visual Studio használatával

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Kapcsolódás Azure SQL Database adatbázishoz a Visual Studióban. 

## Előfeltételek


SQL Database adatbázishoz való, Visual Studio használatával történő kapcsolódáshoz a következőkre van szüksége: 


- Azure-fiók és -előfizetés. Regisztrálhat [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).


- **AdventureWorksLT** mintaadatbázis az Azure SQL Database szolgáltatásban.
 - [Hozzon létre mintaadatbázist ](sql-database-get-started.md), ez csak néhány percet vesz igénybe.


- Visual Studio 2013 4. frissítés (vagy újabb). A Microsoft most *ingyenesen* biztosítja a Visual Studio Community-t.
 - [Visual Studio Community, letöltés](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Egyéb lehetőségek Visual Studio ingyenes letöltésére](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - A témakör későbbi [lépésében](#InstallVSForFree) is megtalálja annak leírását, hogy az[Azure portál](https://portal.azure.com/) hogyan vezeti Önt végig a Visual Studio telepítésén.


<a name="InstallVSForFree" id="InstallVSForFree"></a>

&nbsp;

## 1. lépés: Telepítse ingyenesen a Visual Studio Community-t


Lehetőségek Visual Studio telepítésére:

- A Visual Studio Community ingyenes telepítéséhez navigáljon a böngészőben a Visual Studio termékek azon weblapjaira, amelyek ingyenes letöltést és egyéb lehetőségeket kínálnak; vagy
- Az [Azure portál](https://portal.azure.com/) végigvezeti Önt a letöltési weblapon, ennek leírását lásd a következőkben.


### Visual Studio letöltése Azure portálon keresztül


1. Jelentkezzen be az [Azure portálon](https://portal.azure.com/) keresztül, http://portal.azure.com/.

2. Kattintson a **TALLÓZÁS* ÖSSZES** > **SQL-adatbázisok** lehetőségre. Megnyílik egy panel, amely adatbázisokat keres.

3. Az oldal tetején a Szűrő szövegmezőbe kezdje el beírni **AdventureWorksLT** adatbázisának nevét.

4. Amikor a kiszolgálón megjelenik az adatbázis sora, kattintson rá. Megnyílik egy panel az adatbázishoz.

5. Kényelmi okokból kattintson a Kis méretűvé tesz elemre az előző paneleken.

6. Kattintson az adatbázis paneljének tetején található **Megnyitás Visual Studio használatával** gombra. Megnyílik egy új Visual Studio panel a Visual Studio telepítési helyeire mutató hivatkozásokkal.

    ![Megnyitás Visual Studio használatával gomb][20-OpenInVisualStudioButton]

7. Kattintson a **Community (ingyenes) ** vagy hasonló hivatkozásra. Megjelenik egy új weblap.

8. Az új weblapon található hivatkozások segítségével telepítse a Visual Studio programot.

9. A Visual Studio telepítése után a **Megnyitás Visual Studio használatával** panelen kattintson a **Megnyitás Visual Studio használatával** gombra. Megnyílik a Visual Studio.

10. A Visual Studio egy párbeszédpanelen kéri a kapcsolati sztringek mezőinek kitöltését az **SQL Server Object Explorer** ablaktábla számára.
 - Az **SQL Server-hitelesítés** lehetőséget válassza a **Windows-hitelesítés** helyett.
 - Ne felejtse el megadni **AdventureWorksLT** adatbázisát (**Beállítások** > **Kapcsolat tulajdonságai** beállításokat a párbeszédpanelen).

11. Az **SQL Server Object Explorerben** bontsa ki a csomópontot az adatbázisra.


## 2. lépés: Mintalekérdezések futtatása

A logikai kiszolgálóhoz való csatlakozás után tud adatbázishoz csatlakozni és mintalekérdezést futtatni. 

1. Az **Object Explorerben** navigáljon a kiszolgálón egy olyan adatbázishoz, amelyre van engedélye, például az **AdventureWorks** mintaadatbázishoz.
2. Kattintson a jobb gombbal az adatbázisra, majd válassza az **Új lekérdezés** lehetőséget.

    ![Új lekérdezés. Csatlakozás SQL Database-kiszolgálóhoz: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. A lekérdezés ablakban másolja és illessze be a következő kódot.

        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;

4. Kattintson a **Végrehajt** gombra.  Sikeres lekérdezés esetén a következő képernyőfelvétel jelenik meg.

    ![Sikeres. Csatakozás SQL Database-kiszolgálóhoz: Visual Studio](./media/sql-database-connect-query-ssms/5-success.png)

## Következő lépések

[Csatlakozás SQL Database adatbázishoz  .NET (C#) használatával](sql-database-develop-dotnet-simple.md) 


<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png




<!--HONumber=Jun16_HO2-->


