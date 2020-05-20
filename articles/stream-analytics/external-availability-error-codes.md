---
title: Külső rendelkezésre állási hibakódok – Azure Stream Analytics
description: A külső rendelkezésre állási hibakódokkal Azure Stream Analytics hibák elhárítása.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 1ce867d60c53b9befe5b4480693b54d2c9eba018
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650055"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Azure Stream Analytics külső rendelkezésre állási hibakódok

A tevékenységek naplóit és erőforrás-naplóit használhatja a nem várt viselkedések hibakereséséhez a Azure Stream Analytics feladatból. Ez a cikk felsorolja az összes külső rendelkezésre állási hibakód leírását. Külső rendelkezésre állási hibák akkor fordulhatnak elő, ha egy függő szolgáltatás nem érhető el.

## <a name="externalserviceunavailable"></a>ExternalServiceUnavailable

* **OK**: a szolgáltatás átmenetileg nem érhető el.
* **Javaslat**: a stream Analytics továbbra is megkísérli a szolgáltatás elérését.

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **OK**: stream Analytics hibát észlelt a EventHub-vel folytatott kommunikáció során. 


## <a name="next-steps"></a>További lépések

* [Bemeneti kapcsolatok hibaelhárítása](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics kimenetek hibáinak megoldása](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics lekérdezések hibáinak megoldása](stream-analytics-troubleshoot-query.md)
* [Azure Stream Analyticsek hibakeresése erőforrás-naplók használatával](stream-analytics-job-diagnostic-logs.md)
* [AdatAzure Stream Analyticsi hibák](data-errors.md)
