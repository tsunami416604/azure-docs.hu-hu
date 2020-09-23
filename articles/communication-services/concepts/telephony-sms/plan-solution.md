---
title: Az Azure kommunikációs szolgáltatások telefonos és SMS-megoldásának megtervezése
titleSuffix: An Azure Communication Services concept document
description: Megtudhatja, hogyan tervezheti meg hatékonyan a telefonszámok és a telefonos szolgáltatások használatát.
author: stkozak
manager: rampras
services: azure-communication-services
ms.author: stkozak
ms.date: 06/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 39f88ab8b735438f60d8e20513ea5cbda43d41ee
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947108"
---
# <a name="plan-your-telephony-and-sms-solution"></a>A telefonos és SMS-megoldás megtervezése

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Ez a dokumentum az Azure kommunikációs szolgáltatások által kínált különböző telefonos csomagokat és típusokat ismerteti. Áttekintjük a döntési folyamatokat, amelyek segítenek kiválasztani a kommunikációs szolgáltatásokon keresztül elérhető hangszolgáltatót, telefonszám-típusokat, terveket és képességeket.

## <a name="about-phone-numbers-in-azure-communications-services"></a>Az Azure Communications Services telefonszámai

Az Azure kommunikációs szolgáltatások lehetővé teszik telefonszámok használatát telefonos hívások és SMS-üzenetek elhelyezésére és fogadására. Ezek a telefonszámok a hívó AZONOSÍTÓjának konfigurálására használhatók a szolgáltatás által elhelyezett kimenő hívásoknál.
  
Ha nem rendelkezik meglévő telefonszámmal a kommunikációs szolgáltatási megoldásba való importáláshoz, a legegyszerűbb lehetőség az, hogy percek alatt új telefonszámot szerezzen be az Azure kommunikációs szolgáltatásokból.

Ha van egy meglévő telefonszáma, amelyet továbbra is használni szeretne a megoldásban (például 1 – 800 – vállalat), a telefonszámot a meglévő szolgáltatótól a kommunikációs szolgáltatásokba is elvégezheti.

A következő ábrán az elérhető lehetőségek közül lehet navigálni:

:::image type="content" source="../media/plan-solution/decision-tree-basic.png" alt-text="Ábra, amely bemutatja, hogyan hozhat döntéseket a telefonszámokkal kapcsolatban.":::

Most tekintsük át a kommunikációs szolgáltatásokon keresztül elérhető telefonszám-típusokat és képességeket. 

## <a name="microsoft-direct-offer-of-phone-numbers-and-capabilities"></a>Telefonszámok és képességek Microsoft közvetlen ajánlata

Az Azure kommunikációs szolgáltatásai kiváló rugalmasságot biztosítanak a fejlesztők számára. A legtöbb telefonszámon lehetővé tesszük a "a la carte" csomagok beállításának konfigurálását. Bizonyos fejlesztőknek csak bejövő hívási tervre van szükségük; Egyesek a bejövő és a kimenő SMS-csomagok esetében is dönthetnek. Ezek a tervek kiválaszthatók a telefonos telefonszámok távközlési szolgáltatásokban való bérlete és/vagy portjának kiválasztásával.

Az elérhető csomagok az országtól és a telefonszámtól függenek. Az alábbi ábra a döntési folyamatot mutatja be: a rendelkezésre álló csomagok az országtól és a telefonszámtól függenek.

<!-- Tami/team have rejected this multiple times despite updates, says it needs to be higher res - need to work with her to get approval for this image. Commenting out to move our staging forward. :::image type="content" source="../../media/example-decision-flow.png" alt-text="Example for the decision flow"::: -->

A telefonszám típusának kiválasztása előtt tekintse át a nemzetközi telefonszám-számozási tervet.

### <a name="optional-reading-international-public-telecommunication-numbering-plan-e164"></a>Opcionális olvasás. Nemzetközi nyilvános távközlési számozási terv (E. 164)

> [!NOTE]
> Azt javasoljuk, hogy tekintse át ezeket az adatokat még akkor is, ha ismeri az E. 164 telefonszámát, hogy jobban megértse az Azure kommunikációs szolgáltatások közvetlen ajánlatában kínált számok típusát és képességeit.

A nemzetközi nyilvános távközlési számozási terv az E. 164 nemzetközi távközlési Unió (ITU) ajánlásában van meghatározva. A megfelelési számok legfeljebb 15 számjegyre korlátozódnak.

A telefonszám a következőkből áll

-   A "+" előtag
-   Nemzetközi tárcsázási előtag vagy ország/régió kódja (egy, kettő vagy három számjegy) 
-   *(Nem kötelező)* Egy nemzeti célcím vagy számozási terv, amelyet általában körzetszámnak nevezünk. A kód hossza az országtól függ. A Egyesült Államok három számjegyből áll. Ausztráliában és Új-Zélandon egyetlen számjegyből áll. Németország, Japán, Mexikó és néhány más ország esetében változó hosszúságú a körzetszám. Németországban például a körzetszám két-öt számjegyből állhat, míg Japánban egy-öt számjegyből állhat.
-   Egy előfizetői szám

> [!NOTE]
> A fenti besorolás nem felel meg teljesen az ITU E. 164 szabványnak, és egyszerűsített leírást kíván megadni. Az előfizetői szám például a standardon belül van felosztva. Ha jobban érdekli a nemzetközi számozási terv mélyebb megismerése, az [ITU E. 164 szabvány](https://www.itu.int/rec/T-REC-E.164) kiváló kiindulópont.  

Íme néhány példa, amely segítséget nyújt a számozási terv jobb megismeréséhez:

Az Egyesült államokbeli régióbeli telefonszám:

:::image type="content" source="../media/plan-solution/regional-us.png" alt-text="Az Egyesült államokbeli régióbeli telefonszámok – példa":::

A kanadai régióbeli telefonszám:

:::image type="content" source="../media/plan-solution/regional-canada.png" alt-text="Példa a kanadai régióbeli telefonszámra":::

Díjmentes szám a Észak-Amerika régióban:

:::image type="content" source="../media/plan-solution/tollfree-us.png" alt-text="Ingyenesen hívható szám – példa Észak-Amerika":::

Az Egyesült Királyságban egy mobiltelefon-telefonszám:

:::image type="content" source="../media/plan-solution/mobile-uk.png" alt-text="Példa mobil számra az Egyesült Királyságban":::

Ezután tekintse át az Azure kommunikációs szolgáltatásokban elérhető telefonszám-típusokat.

## <a name="phone-number-types-in-azure-communication-services"></a>Telefonszám-típusok az Azure kommunikációs szolgáltatásokban

A Microsoft regionális és díjmentesen elérhető csomagokat biztosít a rövid kódokhoz és a mobil számokhoz az országokban, ahol alkalmazható.

Az alábbi táblázat összefoglalja a következő telefonszámokat: 

| Telefonszám típusa | Példa                              | Ország elérhetősége    | Gyakori használati eset                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------------------------------------------------------------------------------------------------- |
| Regionális          | + 1 (földrajzi terület kódja) XXX XX XX  | Egyesült Államok, Kanada, Puerto Rico | Telefonszámok kiosztása a felhasználók számára az alkalmazásokban vagy az interaktív hangválasztó (IVR) rendszerek/robotok számára való hozzárendelés |
| Díjmentes         | + 1 (díjmentes terület *kódja*) xxx XX XX | Egyesült Államok, Kanada, Puerto Rico | Az interaktív hangválasztó (IVR) rendszerek/robotok, SMS-alkalmazások kiosztása                                        |

## <a name="plans"></a>Tervek 

Nézzük meg a telefonszámok számára engedélyezhető funkciókat. Ezek a képességek országonként változnak, a szabályozási követelmények miatt. Az Azure kommunikációs szolgáltatás a következő képességeket kínálja:

- **Egyirányú kimenő SMS**, hasznos értesítések és kétfaktoros hitelesítési forgatókönyvek esetén.
- **Kétirányú bejövő és kimenő SMS**, egy előre definiált csomag, amely lehetővé teszi az SMS küldését és fogadását egy csomag részeként.
- **PSTN-hívás**, kiválaszthat egy bejövő hívást, és a hívó azonosító használatával helyezheti el a kimenő hívásokat.

## <a name="next-steps"></a>Következő lépések

### <a name="quickstarts"></a>Rövid útmutatók

- [Telefonszám beszerzése](../../quickstarts/telephony-sms/get-phone-number.md)
- [Hívás elhelyezése](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [SMS küldése](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Fogalmi dokumentáció

- [Hang-és video-fogalmak](../voice-video-calling/about-call-types.md)
- [Folyamatok és SMS-folyamatok hívása](../call-flows.md)
- [Díjszabás](../pricing.md)
