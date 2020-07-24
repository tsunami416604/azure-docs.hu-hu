---
title: 'Oktatóanyag: Ismerkedés az Azure szinapszis Analytics szolgáltatással'
description: Ebből az oktatóanyagból megismerheti az Azure szinapszis Analytics beállításának és használatának alapvető lépéseit.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 7affc5aad89fd79e6ba6480f7bf10d37f90dc5e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075861"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Ismerkedés az Azure szinapszis Analytics szolgáltatással

Ez az oktatóanyag egy lépésenkénti útmutató, amely az Azure szinapszis Analytics főbb funkcióit ismerteti. Az oktatóanyag az ideális kiindulási pont az Azure szinapszis Analytics legfontosabb forgatókönyveit bemutató idegenvezetők számára. Az oktatóanyag lépéseinek követése után egy teljesen működőképes szinapszis-munkaterülettel fog rendelkezni, amelyben megkezdheti az adatok elemzését az SQL, az SQL on-demand és a Apache Spark használatával.

A következőket fogja megtanulni:
* Szinapszis-munkaterület kiépítése egy Azure-előfizetésben
* ADLSGEN2-fiók hozzáférés-vezérlésének konfigurálása úgy, hogy zökkenőmentesen működjön a szinapszis munkaterülettel
* A NYCTaxi-mintaadatok betöltése a szinapszis munkaterületre, hogy az SQL, az SQL igény szerinti és a Spark használatával is használható legyen
* SQL-parancsfájlok és szinapszis-jegyzetfüzetek szerkesztése és futtatása a szinapszis Studio használatával
* SQL-táblák és Spark-táblázatok lekérdezése
* Adatok betöltése az SQL-táblákból Spark-dataframes
* Adatok betöltése a Spark dataframes SQL-tábláiba
* ADLSGEN2-fiók tartalmának megismerése
* A ADLSGEN2-fiókokba tartozó parketta-adatfájlok elemzése a Spark és az SQL igény szerinti használatával 
* Olyan adatfolyamat létrehozása, amely óránként egy szinapszis-jegyzetfüzetet automatikusan futtat

Kövesse az alább *látható lépéseket,* és ismerkedjen meg a számos lehetőséggel, és Ismerje meg, hogyan gyakorolhatják alapvető funkcióit.

* [1. lépés – a szinapszis-munkaterület létrehozása és beállítása](get-started-create-workspace.md)
* [2. lépés – elemzés SQL-készlet használatával](get-started-analyze-sql-pool.md)
* [3. lépés – a Spark használatának elemzése](get-started-analyze-spark.md)
* [4. lépés-az SQL igény szerinti elemzése](get-started-analyze-sql-on-demand.md)
* [5. lépés – a Storage-fiókban tárolt adatelemzés](get-started-analyze-storage.md)
* [6. lépés – a folyamatok összehangolása](get-started-pipelines.md)
* [7. lépés – az adatmegjelenítés Power BI](get-started-visualize-power-bi.md)
