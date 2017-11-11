---
title: "Adatok betöltése az Azure storage analytics környezeteket |} Microsoft Docs"
description: "Adatok áthelyezése Azure Blob Storage-tárolóba vagy onnan máshová"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: bradsev
ms.openlocfilehash: 7d7da4f6dfed03d470c5b5706aaf412c07096120
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Adatok betöltése a tárolási környezetekbe elemzés céljából
A csapat adatok tudományos folyamathoz az szükséges, adatok keresztül a szervezetbe vagy eltérőek a tárolási környezetek dolgozni, vagy a folyamat egyes fázisaiban a legmegfelelőbb módon elemzett számos betölteni. Azure Blob Storage, SQL Azure-adatbázisok, SQL Server Azure virtuális Gépen, a HDInsight (Hadoop) és az Azure Machine Learning feldolgozásához gyakran használt adatok a célok közé 

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

Ez **menü** ezek az adatok betöltési módját leíró témakörök hivatkozásait cél környezetekben, ahol az adatok tárolása és feldolgozása.

Műszaki és üzleti igényei, valamint a kezdeti helyen formázása, és az adatok mérete határozza meg, hogy a cél környezetekben, amelybe a adatoknak kell fogyasztanak elemzéshez célok eléréséhez. Nem ritka, hogy az esetet szükséges adatok áthelyezhetővé válnak a különböző feladatok a prediktív modell létrehozásához szükséges eléréséhez több különböző környezetek között. Ez a tevékenységsorozat tartalmazhatnak, például az adatok feltárása, az előzetes feldolgozás, a tisztítás, a régebbi-mintavételi és a modell betanítási.

