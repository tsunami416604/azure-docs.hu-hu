---
title: Adatok betöltése az Azure Storage-környezetekbe – Csapatadat-elemzési folyamat
description: Ismerje meg, hogyan lehet adatokat bedolgozni különböző célkörnyezetekbe, ahol az adatok tárolása és feldolgozása történik.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f8eab59d810fb825dbebf80d01d8efd2dd0a9841
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720537"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Adatok betöltése a tárolási környezetekbe elemzés céljából

A csapat adatelemzési folyamat megköveteli, hogy az adatok at kell betölteni, vagy betölteni a legmegfelelőbb módon minden szakaszában. Az adatcélok közé tartozhatnak az Azure Blob Storage, az SQL Azure-adatbázisok, az Azure VM-en futó SQL Server, a HDInsight (Hadoop), a Synapse Analytics és az Azure Machine Learning. 

Az alábbi cikkek ismertetik, hogyan lehet adatokat bedolgozni különböző célkörnyezetekbe, ahol az adatok tárolása és feldolgozása.

* Az [Azure Blob Storage-ba/onnan](move-azure-blob.md)
* Sql [Server az Azure VM-en](move-sql-server-virtual-machine.md)
* Az [Azure SQL-adatbázisba](move-sql-azure.md)
* [Hive-táblákhoz](move-hive-tables.md)
* [SQL particionált táblákhoz](parallel-load-sql-partitioned-tables.md)
* Helyszíni [SQL Server kiszolgálóról](move-sql-azure-adf.md)

A műszaki és üzleti igények, valamint az adatok kezdeti helye, formátuma és mérete határozza meg a legjobb adatbetöltési tervet. Nem ritka, hogy a legjobb terv, hogy több lépést. Ez a feladatsorozat magában foglalhatja például az adatok feltárását, az előfeldolgozást, a tisztítást, a lemintavételezést és a modellbetanítást.  Az Azure Data Factory egy ajánlott Azure-erőforrás az adatok mozgatásának és átalakításának vezénylésére.
