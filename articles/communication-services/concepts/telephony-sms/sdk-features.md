---
title: SMS ügyféloldali kódtár – áttekintés az Azure kommunikációs szolgáltatásokhoz
titleSuffix: An Azure Communication Services concept document
description: Áttekintést nyújt az SMS-ügyfél könyvtáráról és ajánlatáról.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 2d81749e7023bdbf5353e5c8da633674ea8e8ce9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947105"
---
# <a name="sms-client-library-overview"></a>SMS ügyféloldali kódtár – áttekintés

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Az Azure kommunikációs szolgáltatások SMS-ügyfél-kódtárak használatával SMS-üzenetküldést adhat az alkalmazásaihoz.

## <a name="sms-client-library-capabilities"></a>Az SMS ügyféloldali kódtár képességei

Az alábbi lista az ügyféloldali kódtárakban jelenleg elérhető funkciók készletét mutatja be.

| Szolgáltatások csoportja | Képesség                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Alapvető képességek | SMS-üzenetek küldése és fogadása </br> *Támogatott Unicode-hangulatjelek*                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Kézbesítési jelentések fogadása a küldött üzenetekhez                                            | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Minden karakterkészlet (nyelv/Unicode támogatás)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Hosszú üzenetek támogatása (legfeljebb 2048 karakter)                                           | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Hosszú üzenetek automatikus összefűzése                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
| Események            | A Event Grid használatával konfigurálhatja a webhookokat a bejövő üzenetek és a kézbesítési jelentések fogadására | ✔️   | ✔️    | ✔️    | ✔️      |
| Telefonszám      | Díjmentes számok                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Szabályozási        | Letiltások kezelésére                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| Figyelés        | A küldött és fogadott üzenetek használatának figyelése                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| PSTN-hívás      | PSTN-hívási képességek hozzáadása az SMS-kompatibilis telefonszámhoz                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés az SMS küldésével](../../quickstarts/telephony-sms/send.md)

A következő dokumentumok érdekesek lehetnek:

- Ismerkedjen meg az általános [SMS-fogalmakkal](../telephony-sms/concepts.md)
- SMS-kompatibilis [telefonszám](../../quickstarts/telephony-sms/get-phone-number.md) beszerzése
- [Az SMS-megoldás megtervezése](../telephony-sms/plan-solution.md)