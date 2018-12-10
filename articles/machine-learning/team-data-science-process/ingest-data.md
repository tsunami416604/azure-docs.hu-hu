---
title: Adatok betöltése az Azure storage-környezetek – csoportos adatelemzési folyamat
description: Adatok áthelyezése Azure Blob Storage-tárolóba vagy onnan máshová
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b53ece6cd2e61fa6f6340b7bda3021ee7d9d767c
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139707"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Adatok betöltése a tárolási környezetekbe elemzés céljából

A csoportos adatelemzési folyamat szükséges adatok betöltött vagy betölti a dolgozhatók fel és elemzi a legmegfelelőbb módon, a folyamat minden egyes szakaszhoz különböző tárolási környezetben. Feldolgozási gyakran használt adatok célok közé tartozik az Azure Blob Storage, SQL Azure adatbázis, SQL Server Azure virtuális Gépen, a HDInsight (Hadoop) és az Azure Machine Learning. 

A következő cikkek betölteni az adatokat különböző célkörnyezetekben, ahol az adatok tárolása és feldolgozása az ismertetik.

* A tárolókról [Azure Blob Storage](move-azure-blob.md)
* A [SQL Server Azure virtuális gépen](move-sql-server-virtual-machine.md)
* A [Azure SQL-adatbázis](move-sql-azure.md)
* A [Hive-táblákban](move-hive-tables.md)
* A [SQL particionált táblák](parallel-load-sql-partitioned-tables.md)
* A [a helyszíni SQL Server](move-sql-azure-adf.md)

Technikai és üzleti igények, valamint a kezdeti helyen, formázhatja, és az adatok mérete határozza meg a cél környezetekben, amelybe az adatokat kell megoldás elkezdi feldolgozni az elemzési a célok eléréséhez. Már nem ritka, hogy az esetet helyezhetők át a prediktív modell összeállításához szükséges feladatok számos eléréséhez több környezet szükséges. Ezen feladatok sorozatát lehetnek például adatfeltárás, az előfeldolgozásához, a tisztítás, a le-mintavételi és a modell betanítása.
