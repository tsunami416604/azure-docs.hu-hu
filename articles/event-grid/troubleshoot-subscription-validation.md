---
title: Azure Event Grid – előfizetés-ellenőrzés hibaelhárítása
description: Ez a cikk bemutatja, hogyan lehet elhárítani az előfizetés érvényességét.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: spelluru
ms.openlocfilehash: f292d70eaaca29e714ea35b4f61a141b2d5cd2b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83778719"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Azure Event Grid előfizetés érvényességének hibáinak megoldása
Ez a cikk tájékoztatást nyújt az esemény-előfizetés érvényességének hibaelhárításáról. 

> [!IMPORTANT]
> A webhookok végpont-ellenőrzésével kapcsolatos részletes információkért lásd: [webhook-esemény kézbesítése](webhook-event-delivery.md).

## <a name="validate-event-grid-event-subscription-using-postman"></a>Event Grid esemény-előfizetés ellenőrzése a Poster használatával
Íme egy példa a Poster használatára egy Event Grid esemény webhook-előfizetésének érvényesítéséhez: 

![Event Grid esemény-előfizetés érvényesítése a Poster használatával](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Íme egy példa a **SubscriptionValidationEvent** JSON-ra:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

A minta sikeres Válasz:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Ha többet szeretne megtudni a webhookok Event Grid esemény-ellenőrzéséről, tekintse meg a [végpont-ellenőrzés az Event Grid-eseményekkel](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)című témakört.


## <a name="validate-event-grid-event-subscription-using-curl"></a>Event Grid esemény-előfizetés ellenőrzése a curl használatával 
Itt látható a minta curl-parancs egy Event Grid esemény webhook-előfizetésének érvényesítéséhez: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>Felhőalapú esemény-előfizetés ellenőrzése a Poster használatával
Íme egy példa arra, hogyan használható a Poster egy felhőalapú esemény webhook-előfizetésének érvényesítéséhez: 

![Felhőalapú esemény-előfizetés érvényesítése a Poster használatával](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

A **http-beállítások** módszert használja a Felhőbeli eseményekkel való ellenőrzéshez. Ha többet szeretne megtudni a webhookok Felhőbeli eseményeinek ellenőrzéséről, tekintse meg a [végpont-ellenőrzés Felhőbeli eseményekkel](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)című témakört.

## <a name="error-code-403"></a>Hibakód: 403
Ha a webhook 403 (tiltott) értékkel tér vissza a válaszban, ellenőrizze, hogy a webhook egy Azure Application Gateway vagy webalkalmazási tűzfal mögött van-e. Ha igen, le kell tiltania a következő tűzfalszabályok, és végre kell hajtania a HTTP-KÖZZÉTÉTELt:

  - 920300 (a kérelemből hiányzik egy Accept fejléc, ezt kijavíthatjuk)
  - 942430 (korlátozott SQL-karakteres anomáliák észlelése (argumentumok): a speciális karakterek száma túllépve (12)
  - 920230 (több URL-kódolás észlelhető)
  - 942130 (SQL injection támadás: az SQL-tautológia észlelve.)
  - 931130 (lehetséges távoli fájlok felvételének (RFI) támadása = off-domain Reference/link)

## <a name="next-steps"></a>További lépések
Ha további segítségre van szüksége, tegye fel a problémát a [stack overflow fórumba](https://stackoverflow.com/questions/tagged/azure-eventgrid) , vagy nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/options/). 
