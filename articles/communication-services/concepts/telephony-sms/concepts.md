---
title: SMS-fogalmak az Azure kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services concept document
description: Ismerje meg a kommunikációs szolgáltatások SMS-fogalmakat.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e5cfc1e27bae10a1c67e4506afe9db825664785f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947107"
---
# <a name="sms-concepts"></a>SMS-fogalmak

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Az Azure kommunikációs szolgáltatások lehetővé teszik SMS szöveges üzenetek küldését és fogadását a kommunikációs szolgáltatások SMS-ügyfél kódtárai használatával. Ezek az ügyféloldali kódtárak az ügyfélszolgálati forgatókönyvek, a kinevezési emlékeztetők, a kétfaktoros hitelesítés és az egyéb valós idejű kommunikációs igények támogatásához használhatók. A kommunikációs szolgáltatások SMS-je lehetővé teszi az üzenetek megbízható küldését, miközben kiteszi a kampányok által megfigyelt kézbesítési és reagálási arányt.

Az Azure Communication Services SMS ügyféloldali kódtárainak legfontosabb funkciói a következők:

-  **Egyszerű** telepítési élmény SMS-képesség alkalmazásokhoz való hozzáadásához.
- **Nagy sebességű** üzenetek támogatása a A2P (alkalmazás – személy) a Egyesült Államokban használt díjmentes számokkal.
- **Kétirányú** beszélgetések olyan forgatókönyvek támogatásához, mint például az ügyfélszolgálat, a riasztások és a találkozók emlékeztetői.
- **Megbízható kézbesítés** valós idejű kézbesítési jelentésekkel az alkalmazásból küldött üzenetekhez.
- **Elemzés** a használati minták és az ügyfelek részvételének nyomon követéséhez.
- A **letiltási** kezelési támogatással automatikusan észlelhetők és betartható a díjmentes számok. A kommunikációs szolgáltatások észlelik a leállítási és indítási üzeneteket, és a következő alapértelmezett válaszokat küldik a végfelhasználók számára: 
  - STOP – *"sikeresen leiratkozott az adott számú üzenetekre. A válasz megkezdése újrafizetésre. "*
  - START – *"sikeresen feliratkozott a számból érkező üzenetekre. A válasz leállítása lemondható. "*
  - A leállítás és az indítási üzenetek vissza lesznek továbbítva Önnek. Az Azure kommunikációs szolgáltatásokkal megfigyelheti és implementálhatja ezeket a letiltásokat, így biztosíthatja, hogy a rendszer nem küld további üzeneteket a kommunikációt kihagyó címzetteknek.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés az SMS küldésével](../../quickstarts/telephony-sms/send.md)

A következő dokumentumok érdekesek lehetnek:

- Ismerkedjen meg az [SMS ügyféloldali függvénytárával](../telephony-sms/sdk-features.md)
- SMS-kompatibilis [telefonszám](../../quickstarts/telephony-sms/get-phone-number.md) beszerzése
- [Az SMS-megoldás megtervezése](../telephony-sms/plan-solution.md)
