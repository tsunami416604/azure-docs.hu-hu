---
title: Azure Stream Analytics hibakódokkal kapcsolatos hibák
description: Belső hibakódokkal kapcsolatos Azure Stream Analytics hibák elhárítása.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 2e101747322baac860cbde02aca341171aa0cc1d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658347"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Belső hibakódok Azure Stream Analytics

A tevékenységek naplóit és erőforrás-naplóit használhatja a nem várt viselkedések hibakereséséhez a Azure Stream Analytics feladatból. Ez a cikk felsorolja az összes belső hibakód leírását. A belső hibák olyan általános hibák, amelyek a Stream Analytics platformon belül történnek, ha Stream Analytics nem tudja megkülönböztetni, ha a hiba belső rendelkezésre állási hiba vagy hiba a rendszerben.

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **OK**: a Cosmos db írásához használt köteg mérete túl nagy. 
* **Javaslat**: próbálkozzon újra kisebb batch-mérettel.

## <a name="next-steps"></a>További lépések

* [Bemeneti kapcsolatok hibaelhárítása](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics kimenetek hibáinak megoldása](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics lekérdezések hibáinak megoldása](stream-analytics-troubleshoot-query.md)
* [Azure Stream Analyticsek hibakeresése erőforrás-naplók használatával](stream-analytics-job-diagnostic-logs.md)
* [AdatAzure Stream Analyticsi hibák](data-errors.md)