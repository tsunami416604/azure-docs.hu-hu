---
title: Külső rendelkezésre állási hibakódok – Azure Stream Analytics
description: A külső rendelkezésre állási hibakódokkal Azure Stream Analytics hibák elhárítása.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: c70a3a2d0630148a077f9c149ba40d48a6b7c0ae
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045280"
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
