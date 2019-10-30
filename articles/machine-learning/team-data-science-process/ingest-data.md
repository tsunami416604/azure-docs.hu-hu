---
title: Betöltés az Azure Storage-környezetbe – csoportos adatelemzési folyamat
description: Megtudhatja, hogyan lehet az adatgyűjtést különböző célpont-környezetekben tárolni és feldolgozni.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 515decfafe46ad0c1b5b90743688abc26a975903
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053260"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Adatok betöltése a tárolási környezetekbe elemzés céljából

A csoportos adatelemzési folyamat megköveteli, hogy az adatmennyiség különböző tárolási környezetbe kerüljön, illetve a folyamat egyes szakaszaiban a legmegfelelőbb módon legyen feldolgozható vagy elemezhető. A feldolgozásra gyakran használt adatcélállomások közé tartoznak az Azure Blob Storage, a SQL Azure SQL Server-adatbázisok, az Azure-beli virtuális gépek, a HDInsight (Hadoop) és a Azure Machine Learning. 

Az alábbi cikkek azt ismertetik, hogyan lehet az adatgyűjtést különböző célpont-környezetekben tárolni és feldolgozni.

* [Azure-Blob Storage](move-azure-blob.md)
* SQL Server az Azure-beli [virtuális gépen](move-sql-server-virtual-machine.md)
* Az [Azure SQL Database](move-sql-azure.md) -be
* A [struktúra tábláiba](move-hive-tables.md)
* [SQL-particionálású táblákhoz](parallel-load-sql-partitioned-tables.md)
* Helyszíni [SQL Server](move-sql-azure-adf.md)

A technikai és üzleti igények, valamint az adatmennyiség kezdeti helye, formátuma és mérete határozza meg azokat a célcsoportokat, amelyekben az elemzés céljainak eléréséhez meg kell adni az adatot. Nem ritka, hogy egy olyan forgatókönyv esetében, amely a prediktív modell létrehozásához szükséges különböző feladatok eléréséhez több környezet között áthelyezett adatok megkövetelését igényli. A feladatok ezen sorozata magában foglalhatja például az adatok feltárását, az előfeldolgozást, a tisztítást, a le-mintavételezést és a modellek betanítását.
