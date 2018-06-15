---
title: Az Azure gépi tanulás használja az SQL Data Warehouse szolgáltatással |} Microsoft Docs
description: Oktatóanyag az Azure Machine Learning Azure SQL Data Warehouse adattárházzal történő, megoldások fejlesztésére irányuló használatához.
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: barbkess
editor: ''
ms.assetid: ac6bc731-6add-47a9-b3fe-68996e656f4d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: c19860c6b5b1c15d1e29ddc67f9cf9ad4618725b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31435273"
---
# <a name="use-azure-machine-learning-with-sql-data-warehouse"></a>Az Azure gépi tanulás használja az SQL Data Warehouse szolgáltatással
Az Azure Machine Learning egy teljes körűen felügyelt prediktív elemzési szolgáltatás, amely segítségével az adatok alapján prediktív modellek létrehozása az SQL Data Warehouse, és tegye közzé felhasználásához felhasználásra kész webszolgáltatásként. A prediktív elemzés alapvető és a gépi tanulás olvasásával [Bevezetés az Machine Learning Azure][Introduction to Machine Learning on Azure].  Tudja majd megismerheti, hogyan hozhat létre, betanítását, pontozása és tesztelése a gépi tanulási modell használatával a [létrehozás kísérlet oktatóanyag][Create experiment tutorial].

Ebből a cikkből megtudhatja, hogyan ehhez a következő használatával a [Azure Machine Learning Studio][Azure Machine Learning Studio]:

* Adatokat olvasni az adatbázis létrehozását, betanítását és a prediktív modell pontozása
* Az adatbázis adatok írása

## <a name="read-data-from-sql-data-warehouse"></a>Az SQL Data Warehouse-adatok olvasása
A Microsoft adatokat olvasni az AdventureWorksDW adatbázis termék tábla.

### <a name="step-1"></a>1. lépés
Új kísérlet indításához kattintson + a Machine Learning Studio ablakának alján új kísérlet, majd válassza ki és üres kipróbálásához. Válassza ki a kísérlet alapértelmezett nevét a vászon tetején, és módosítsa valami értelmesebbre, például a kerékpárgyártó árának előrejelzése.

### <a name="step-2"></a>2. lépés
Keresse meg az olvasó modul az adathalmaz a paletta és a modulok bal oldalán a kísérletvászonra. A modul húzza a kísérletvászonra.
![][drag_reader]

### <a name="step-3"></a>3. lépés
Válassza ki az olvasó modul, és töltse ki a Tulajdonságok panelen.

1. Válassza ki az Azure SQL-adatbázis az adatforrással.
2. Adatbázis-kiszolgáló nevét: írja be a kiszolgáló nevét. Használhatja a [Azure-portálon] [ Azure portal] ez kereséséhez.

![][server_name]

1. Adatbázis neve: írja be az adatbázis neve csak a megadott kiszolgálón.
2. Felhasználói fiók kiszolgálónév: írja be a felhasználó egy olyan fiók, amely rendelkezik hozzáférési engedélyekkel az adatbázishoz.
3. Kiszolgáló felhasználói fiók jelszavát: írja be a megadott felhasználói fiók jelszavát.
4. Fogadja el a kiszolgálói tanúsítvány: használja ezt a beállítást (kevésbé biztonságos), ha ki szeretné hagyni a helykiszolgáló tanúsítványának megtekintésével, az adatok olvasása előtt.
5. Adatbázis-lekérdezés: Adjon meg egy SQL-utasítást, amely leírja azokat az olvasni kívánt adatokat. Ebben az esetben azt az adatokat olvassa a következő lekérdezéssel termék táblából.

```SQL
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### <a name="step-4"></a>4. lépés
1. A kísérlet futtatásához kattintson a Futtatás a kísérletvászon alatt.
2. A kísérlet befejezése után az olvasó modul lesz egy zöld pipa jelzi, hogy sikeresen befejeződött. Figyelje meg a jobb felső sarokban futási állapota kész.

![][run]

1. Az importált adatok megtekintéséhez kattintson az autókat tartalmazó adathalmaz alsó a kimeneti portra, és válassza ki a képi megjelenítés opcióra.

## <a name="create-train-and-score-a-model"></a>Létrehozását, betanítását és pontszám modell
Most már használhatja az adatkészlethez:

* A modell létrehozása: feldolgozni az adatokat, és a jellemzők meghatározása
* A modell betanításához: kiválasztása és tanulási algoritmus alkalmazása
* Pontozni és tesztelni a modellt: új kerékpárgyártó árának előrejelzése

![][model]

Létrehozásával kapcsolatos további tudnivalókért betanítása, pontozása és tesztelése a gépi tanulási modell használja a [létrehozás kísérlet oktatóanyag][Create experiment tutorial].

## <a name="write-data-to-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse-adatok írása
Az AdventureWorksDW adatbázis ProductPriceForecast táblájához eredménykészlet írja azt.

### <a name="step-1"></a>1. lépés
Keresse meg a író modul az adathalmaz a paletta és a modulok bal oldalán a kísérletvászonra. A modul húzza a kísérletvászonra.

![][drag_writer]

### <a name="step-2"></a>2. lépés
Válassza ki azt a író modult, és töltse ki a Tulajdonságok panelen.

1. Válassza ki az Azure SQL-adatbázis adatok céljaként.
2. Adatbázis-kiszolgáló nevét: írja be a kiszolgáló nevét. Használhatja a [Azure-portálon] [ Azure portal] ez kereséséhez.
3. Adatbázis neve: írja be az adatbázis neve csak a megadott kiszolgálón.
4. Felhasználói fiók kiszolgálónév: írja be a felhasználó egy olyan fiók, amely rendelkezik írási engedéllyel az adatbázishoz.
5. Kiszolgáló felhasználói fiók jelszavát: írja be a megadott felhasználói fiók jelszavát.
6. Fogadja el a kiszolgálói tanúsítvány (nem biztonságos): válassza ezt a lehetőséget, ha nem szeretné a tanúsítvány megtekintéséhez.
7. A mentendő oszlopok vesszővel tagolt listája: Adja meg, amelyet a kimeneti adatkészlet vagy eredmény oszlopok listája.
8. Adatok táblanév: Adja meg a tábla nevét.
9. A datatable oszlopok vesszővel tagolt listája: Adja meg az új táblázat oszlopneveit. Lehet, hogy az oszlopok neveit eltérő a forrás adatkészletben, de a azonos számú oszlopot itt a kimeneti táblához meghatározó kell listában.
10. Az SQL Azure-művelet írt sorok száma: konfigurálhatja egy műveletet az SQL-adatbázisba írt sorok száma.

![][writer_properties]

### <a name="step-3"></a>3. lépés
1. A kísérlet futtatásához kattintson a Futtatás a kísérletvászon alatt.
2. A kísérlet befejezése után minden modul lesz egy zöld pipa jelzi, hogy sikeresen befejeződött.

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: [SQL Data Warehouse fejlesztői áttekintés][SQL Data Warehouse development overview].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Create experiment tutorial]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[Introduction to machine learning on Azure]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[Azure Machine Learning Studio]: https://studio.azureml.net/Home
[Azure portal]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/
