<properties
   pageTitle="A Visual Studio és az SSDT telepítése SQL Data Warehouse-hoz | Microsoft Azure"
   description="A Visual Studio és az SQL Server Development Tools (SSDT) telepítése az Azure SQL Data Warehouse-hoz"
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
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess"/>

# Visual Studio 2015 és SSDT telepítése SQL Data Warehouse-hoz

Alkalmazások fejlesztéséhez az SQL Data Warehouse-hoz javasoljuk a Visual Studio 2015 használatát az SQL Server Data Tools (SSDT) legújabb verziójával.  A Visual Studio 2013 Update 5 és az SSDT együttes használata is támogatott a visszamenőleges kompatibilitás érdekében.  

A Visual Studio és az SSDT együttes használata révén az SQL Server Object Explorerrel vizuálisan derítheti fel a táblákat, a nézeteket, a tárolt eljárásokat és sok más objektumot az SQL Data Warehouse-ban, továbbá lekérdezéseket is futtathat.

> [AZURE.NOTE] Az SQL Data Warehouse még nem támogatja a Visual Studio-adatbázisprojekteket.  Ez a szolgáltatás egy későbbi verzióban lesz elérhető.

## 1. lépés: A Visual Studio 2015 telepítése

Kattintson az alábbi hivatkozásokra a Visual Studio 2015 letöltéséhez és telepítéséhez. Ha már telepítve van a Visual Studio 2013 vagy 2015, továbbléphet a 2. lépésre, az SSDT telepítésére.

1. [Töltse le a Visual Studio 2015-öt][].
2. Kövesse az [Installing Visual Studio][] (A Visual Studio telepítése) útmutatót az MSDN webhelyén, és válassza az alapértelmezett konfigurációkat.

## 2. lépés: Az SSDT telepítése

Az SSDT a Visual Studióhoz való telepítéséhez egyszerűen keressen a Visual Studióban az SSDT-frissítéseket az alábbi lépésekkel.

1. A Visual Studióban kattintson a **Tools** / **Extensions and Updates…** / **Updates** (Eszközök > Bővítmények és frissítések… > Frissítések) lehetőségre
2. Válassza a **Product Updates** (Termékfrissítések) lehetőséget, majd keresse a **Microsoft SQL Server Update for database tooling** (Microsoft SQL Server frissítése adatbáziseszközökkel) elemet

Ha nem található frissítés, valószínűleg a legújabb verziót használja.  Annak megerősítéséhez, hogy az SSDT telepítve van, kattintson a **Help** / **About Microsoft Visual Studio** (Súgó > A Microsoft Visual Studio névjegye) lehetőségre, és keresse meg az SQL Server Data Toolst a listában.  Az SSDT legújabb verziója a 14.0.60525.0-s verzió.  Ha a telepítés nem érhető el a Visual Studióból, ellátogathat [Az SSDT letöltése][] oldalra az SSDT manuális letöltéséhez és telepítéséhez.

## Következő lépések

Most, hogy telepítette az SSDT legújabb verzióját, készen áll az SQL Data Warehouse-hoz való [csatlakozásra][].

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[csatlakozásra]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Töltse le a Visual Studio 2015-öt]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[Az SSDT letöltése]: https://msdn.microsoft.com/library/mt204009.aspx



<!--HONumber=sep16_HO1-->


