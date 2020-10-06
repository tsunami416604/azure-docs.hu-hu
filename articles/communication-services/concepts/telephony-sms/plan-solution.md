---
title: Az Azure kommunikációs szolgáltatások telefonos és SMS-megoldásának megtervezése
titleSuffix: An Azure Communication Services concept document
description: Megtudhatja, hogyan tervezheti meg hatékonyan a telefonszámok és a telefonos szolgáltatások használatát.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: overview
ms.custom: references_regions
ms.service: azure-communication-services
ms.openlocfilehash: ada37175988155332503c0c15e998ab284d099e5
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758506"
---
# <a name="plan-your-telephony-and-sms-solution"></a>A telefonos és SMS-megoldás megtervezése

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Az Azure kommunikációs szolgáltatások lehetővé teszik a telefonszámok használatát hanghívások készítésére és SMS-üzenetek küldésére a nyilvános kapcsolású telefonos hálózattal (PSTN). Ebben a dokumentumban áttekintjük a telefonos és SMS-megoldások kommunikációs szolgáltatásokkal történő megtervezéséhez szükséges telefonszámokat, terveket és régiókat.

## <a name="phone-number-types-in-azure-communication-services"></a>Telefonszám-típusok az Azure kommunikációs szolgáltatásokban
 
A kommunikációs szolgáltatások kétféle telefonszámot biztosítanak: **helyi** és **díjmentes**. 

### <a name="local-numbers"></a>Helyi számok
A helyi (földrajzi) számok 10 számjegyű telefonszámok, amelyek a Egyesült Államokban található helyi körzetszámokból állnak. Például `+1 (206) XXX-XXXX` egy helyi szám, amelynek körzetszáma `206` . Ez a körzetszám a Seattle városához van rendelve. Ezeket a telefonszámokat általában magánszemélyek és helyi vállalatok használják. Az Azure kommunikációs szolgáltatások helyi számokat biztosítanak a Egyesült Államokban. Ezek a számok a telefonhívások elhelyezésére, de SMS-üzenetek küldésére is használhatók. 

### <a name="toll-free-numbers"></a>Díjmentes számok
Az ingyenesen hívható telefonszámok 10 számjegyű telefonszámok, amelyek különböző körzetszámmal hívhatók meg ingyenesen. Például `+1 (800) XXX-XXXX` egy díjmentes szám a Észak-Amerika régióban. Ezeket a telefonszámokat általában az ügyfélszolgálati célokra használják. Az Azure kommunikációs szolgáltatás díjmentes számokkal rendelkezik az Egyesült Államokban. Ezek a számok a telefonhívások és SMS-üzenetek küldésére használhatók. Az ingyenesen hívható számok nem használhatók a felhasználók számára, és csak alkalmazásokhoz rendelhetők hozzá.

#### <a name="choosing-a-phone-number-type"></a>Telefonszám típusának kiválasztása

Ha a telefonszámát egy alkalmazás fogja használni (például hívások kezdeményezéséhez vagy üzenetek küldéséhez a szolgáltatás nevében), akkor kiválaszthat egy díjmentes vagy helyi (földrajzi) számot. A díjmentes számot kiválaszthatja, ha az alkalmazás SMS-üzeneteket küld és/vagy hívásokat kezdeményez.

Ha a telefonszámot egy személy (például a hívó alkalmazás felhasználója) használja, akkor a helyi (földrajzi) telefonszámot kell használni. 

Az alábbi táblázat összefoglalja a következő telefonszámokat: 

| Telefonszám típusa | Példa                              | Ország elérhetősége    | Telefonszám-képesség |Gyakori használati eset                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------|------------------------------------------------------------------------------------------------------------------- |
| Helyi (földrajzi)        | + 1 (helyi körzetszám) XXX XX XX  | USA                      | Hívás (kimenő) | Telefonszámok kiosztása a felhasználók számára az alkalmazásokban  |
| Díjmentes         | + 1 (díjmentes terület *kódja*) xxx XX XX | USA                      | Calling (kimenő), SMS (bejövő/kimenő)| Telefonszámok kiosztása interaktív hangválaszi (IVR) rendszerekhez/robotokhoz, SMS-alkalmazásokhoz                                        |


## <a name="phone-number-plans-in-azure-communication-services"></a>Telefonos csomagok az Azure kommunikációs szolgáltatásokban 

A legtöbb telefonszám esetében lehetővé tesszük a "a la carte" csomagok készletének konfigurálását. Bizonyos fejlesztőknek csak kimenő hívási tervre van szükségük; Előfordulhat, hogy egyesek a kimenő hívások és a kimenő SMS-csomagok közül választhatnak. Ezeket a csomagokat kiválaszthatja a telefonszámok Azure kommunikációs szolgáltatásokban való bérletének megadásával.

A rendelkezésre álló csomagok attól függnek, hogy melyik országból, a használati esethez és a kiválasztott telefonszám-típushoz tartoznak. Ezek a csomagok országonként változnak, a szabályozási követelmények miatt. Az Azure kommunikációs szolgáltatás a következő terveket kínálja:

- **Egyirányú kimenő SMS** Ez a csomag lehetővé teszi, hogy SMS-üzeneteket küldjön a felhasználóknak. Ez a csomag olyan forgatókönyvek esetén hasznos, mint az értesítések és a kétfaktoros hitelesítési riasztások. 
- **Kétirányú bejövő és kimenő SMS** Ez a csomag lehetővé teszi üzenetek küldését és fogadását a felhasználóktól a telefonszámok használatával. Ez a csomag az ügyfél-szolgáltatási forgatókönyvekben hasznos.
- **Egyirányú kimenő telefonhívás** Ezzel a csomaggal hívásokat indíthat a felhasználók számára, és konfigurálhatja a hívó AZONOSÍTÓját a szolgáltatás által elhelyezett kimenő hívásokhoz. Ez a csomag az ügyfélszolgálati és a hangalapú értesítési forgatókönyvekben hasznos.

## <a name="countryregion-availability"></a>Ország/régió elérhetősége

A következő táblázat bemutatja, hogy hol szerezhetők be különböző típusú telefonszámok, valamint a bejövő és kimenő hívási és SMS-funkciók, amelyek az ilyen típusú telefonszámokhoz vannak társítva.

|Szám típusa| Számok beolvasása a | Hívások kezdeményezése                                        | Hívások fogadása innen:                                    |Üzenetek küldése a következőnek       | Üzenetek fogadása innen: |
|-----------| ------------------ | ---------------------------------------------------  |-------------------------------------------------------|-----------------------|--------|
| Helyi (földrajzi)  | USA                 | USA, Kanada, Egyesült Királyság, Németország, Franciaország,. + További *| USA, Kanada, Egyesült Királyság, Németország, Franciaország,. + További * |Nem érhető el| Nem érhető el |
| Díjmentes | USA                 | USA                                                   | USA                                                    |USA                | USA |

* A hívási célokkal és a díjszabással kapcsolatos további információkért tekintse meg a [díjszabási oldalt](../pricing.md).

## <a name="azure-subscriptions-eligibility"></a>Azure-előfizetések jogosultsága

Telefonszám beolvasásához fizetős Azure-előfizetésre van szükség. A próbaverziós fiókokban nem szerezhetők be telefonszámok. 

## <a name="next-steps"></a>Következő lépések

### <a name="quickstarts"></a>Rövid útmutatók

- [Telefonszám beszerzése](../../quickstarts/telephony-sms/get-phone-number.md)
- [Hívás elhelyezése](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [SMS küldése](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Fogalmi dokumentáció

- [A hang- és videóhívással kapcsolatos alapfogalmak](../voice-video-calling/about-call-types.md)
- [Folyamatok meghívása](../call-flows.md)
- [Díjszabás](../pricing.md)
