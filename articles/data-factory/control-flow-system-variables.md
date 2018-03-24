---
title: Az Azure Data Factory rendszerváltozók |} Microsoft Docs
description: Ez a cikk ismerteti az Azure Data Factory által támogatott rendszerváltozók. Ezek a változók használhatja kifejezések adat-előállító entitások meghatározásakor.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 77285692846d2a301e091426801e20203816181a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Azure Data Factory által támogatott rendszerváltozók
Ez a cikk ismerteti az Azure Data Factory által támogatott rendszerváltozók. Ezek a változók használhatja kifejezések adat-előállító entitások meghatározásakor. 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [funkciók és a Data Factory 1-es verziójú változók](v1/data-factory-functions-variables.md).


## <a name="pipeline-scope"></a>Feldolgozási sor hatóköre:

| Változó neve | Leírás |
| --- | --- |
| @pipeline().DataFactory |A Futtatás futószalag adat-előállító nevét belül fut. | 
| @pipeline().Pipeline |A folyamat neve |
| @pipeline().RunId | Futtassa a megadott folyamat azonosítója | 
| @pipeline().TriggerType | Az eseményindító az adatcsatorna (Manuális, a Feladatütemező) definiálásra típusa | 
| @pipeline().TriggerId| Az eseményindító, amely hívja meg a folyamat azonosítója |
| @pipeline().TriggerName| Az eseményindító, amely hívja meg a folyamat neve |
| @pipeline().TriggerTime| Időpontot, amikor az eseményindító a feldolgozási sor definiálásra. Az indítási idő az a tényleges égetett idő, nem a megadott időpont. Például `13:20:08.0149599Z` helyett adja vissza `13:20:00.00Z` |

## <a name="trigger-scope"></a>Eseményindító hatóköre:

| Változó neve | Leírás |
| --- | --- |
| trigger().scheduledTime |Idő, amikor az eseményindító meghívni a folyamat futtatása lett ütemezve. Például egy eseményindító, amely akkor következik be, minden 5 perc, a változó alakítanák vissza `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:29:00Z` kulcsattribútumokkal.|
| trigger().startTime |Időpontot, amikor az eseményindító **ténylegesen** meghívni a Futtatás futószalag következik be. Például egy eseményindító, amely akkor következik be, minden 5 perc, ez a változó lehet, hogy térjen vissza ehhez hasonló `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z`, `2017-06-01T22:29:00.9935483Z` kulcsattribútumokkal.|

## <a name="next-steps"></a>További lépések
Ezek a változók használata a kifejezések kapcsolatos információkért lásd: [kifejezés nyelvi & a funkciók](control-flow-expression-language-functions.md). 
