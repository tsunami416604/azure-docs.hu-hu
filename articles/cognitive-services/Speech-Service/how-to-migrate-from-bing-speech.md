---
title: Áttelepítés a Bing beszédfelismerési szolgáltatásról a beszédfelismerési szolgáltatásra
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan telepítheti át egy meglévő Bing Speech-előfizetésről a beszédfelismerési szolgáltatásra az Azure Cognitive Servicesből.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: nitinme
ms.openlocfilehash: b95e16f2d8257bfffcaf2524fe7f8ce6be565689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80366592"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Áttelepítés a Bing beszédfelismerésről a beszédfelismerési szolgáltatásra

Ebből a cikkből áttelepítheti alkalmazásait a Bing Speech API-ból a beszédfelismerési szolgáltatásba.

Ez a cikk ismerteti a Bing beszédfelismerési API-k és a beszédfelismerési szolgáltatás közötti különbségeket, és stratégiákat javasol az alkalmazások áttelepítéséhez. A Bing Speech API-előfizetési kulcs nem működik a beszédfelismerési szolgáltatással; új beszédszolgáltatás-előfizetésre lesz szüksége.

Egyetlen beszédszolgáltatási előfizetési kulcs hozzáférést biztosít a következő funkciókhoz. Mindegyik funkció forgalmi díját külön állapítjuk meg, így csak a használt funkciókért kell fizetnie.

* [Beszédfelismerés](speech-to-text.md)
* [Egyéni beszédfelismerés](https://cris.ai)
* [Szövegfelolvasás](text-to-speech.md)
* [Egyéni szövegfelolvasási hangok](how-to-customize-voice-font.md)
* [Tolmácsolás](speech-translation.md) (nem tartalmaz [szövegfordítást](../translator/translator-info-overview.md))

A [beszédstabk](speech-sdk.md) a Bing Speech ügyfélkódtárak funkcionális helyettesítője, de egy másik API-t használ.

## <a name="comparison-of-features"></a>A funkciók összehasonlítása

A beszédszolgáltatás nagymértékben hasonlít a Bing Beszédfelismeréshez, a következő különbségekkel.

| Szolgáltatás | Bing – Beszédfelismerés | Speech szolgáltatás | Részletek |
|--|--|--|--|
| C# SDK | :heavy_check_mark: | :heavy_check_mark: | A beszédszolgáltatás támogatja a Windows 10, az Univerzális Windows Platform (UWP) és a .NET Standard 2.0 szolgáltatást. |
| C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | A beszédszolgáltatás támogatja a Windows t és a Linuxot. |
| Java SDK | :heavy_check_mark: | :heavy_check_mark: | A beszédszolgáltatás támogatja az Android és a Beszédeszközöket. |
| Folyamatos beszédfelismerés | 10 perc | Korlátlan (SDK-val) | A Bing beszéd- és beszédszolgáltatás websocketprotokolljai hívásonként legfeljebb 10 percet támogatnak. A beszédközbeni SDK azonban automatikusan újracsatlakozik az időhosszabbításkor vagy a kapcsolatbontáskor. |
| Részleges vagy időközi eredmények | :heavy_check_mark: | :heavy_check_mark: | WebSockets protokollal vagy SDK-val. |
| Egyéni beszédmodellek | :heavy_check_mark: | :heavy_check_mark: | A Bing Speech külön egyéni beszédfelismerési előfizetést igényel. |
| Egyéni hangbetűtípusok | :heavy_check_mark: | :heavy_check_mark: | A Bing Speech külön Egyéni hang-előfizetést igényel. |
| 24 kHz-es hangok | :heavy_minus_sign: | :heavy_check_mark: |
| Beszédszándék felismerése | Külön LUIS API-hívást igényel | Integrált (SDK-val) | A beszédfelismerési szolgáltatással luis-kulcsot is használhat. |
| Egyszerű leképezésfelismerés | :heavy_minus_sign: | :heavy_check_mark: |
| Hosszú hangfájlok kötegelt átírása | :heavy_minus_sign: | :heavy_check_mark: |
| Felismerési mód | Kézi vezérlés a végpont URI-n keresztül | Automatikus | A felismerési mód nem érhető el a beszédfelismerési szolgáltatásban. |
| Végpont honossága | Globális | Regionális | A regionális végpontok javítják a késést. |
| REST API-k | :heavy_check_mark: | :heavy_check_mark: | A beszédszolgáltatás REST API-k kompatibilisek a Bing Speech (különböző végpont). A REST API-k támogatják a szövegfelolvasást és a szöveggé bírja funkciót. |
| WebSockets protokollok | :heavy_check_mark: | :heavy_check_mark: | A beszédszolgáltatás WebSockets API-kompatibilis a Bing Speech (különböző végpont). Ha lehetséges, térjen át a beszédfelismerési SDK-ra a kód egyszerűsítése érdekében. |
| Szolgáltatás-szolgáltatás API-hívások | :heavy_check_mark: | :heavy_minus_sign: | A Bing-beszéd ben a C# szolgáltatáskönyvtáron keresztül biztosított. |
| Nyílt forráskódú SDK | :heavy_check_mark: | :heavy_minus_sign: |

A beszédfelismerési szolgáltatás időalapú díjszabási modellt használ (nem tranzakcióalapú modellt). A részleteket a [Beszédszolgáltatás díjszabása.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

## <a name="migration-strategies"></a>Migrálási stratégiák

Ha Ön vagy a szervezet olyan alkalmazásokat fejlesztés vagy éles környezetben, amelyek bing beszédfelismerési API-t használnak, frissítenie kell őket, hogy a beszédfelismerési szolgáltatás a lehető leghamarabb használhassa. Tekintse meg a [beszédszolgáltatás dokumentációját](index.yml) a rendelkezésre álló SDK-k, kódminták és oktatóanyagok.

A [beszédszolgáltatás REST API-k](rest-apis.md) kompatibilisek a Bing beszéd API-k. Ha jelenleg a Bing Speech REST API-kat használja, csak a REST-végpontot kell módosítania, és át kell váltania egy beszédszolgáltatás-előfizetési kulcsra.

A Beszédszolgáltatás WebSockets protokolljai is kompatibilisek a Bing Speech által használt protokollal. Azt javasoljuk, hogy az új fejlesztés, használja a beszédska helyett WebSockets. Célszerű a meglévő kódot az SDK-ba is áttelepíteni. A REST API-khoz azonban a Bing Speech websocketeken keresztül bing beszédfelismerést használó meglévő kód hoz csak a végpont és egy frissített kulcs módosítását igényli.

Ha egy bing beszédfelismerési ügyfélkódtárat használ egy adott programozási nyelvhez, a [beszédfelismerési SDK-ba](speech-sdk.md) való áttelepítéshez módosítani kell az alkalmazást, mert az API eltérő. A beszédstabksk egyszerűsítheti a kódot, miközben hozzáférést biztosít az új funkciókhoz. A beszédstakk számos programozási nyelven elérhető. Api-k minden platformon hasonló, megkönnyítve a többplatformos fejlesztés.

A beszédszolgáltatás nem kínál globális végpontot. Határozza meg, hogy az alkalmazás hatékonyan működik-e, ha egyetlen regionális végpontot használ az összes forgalmához. Ha nem, használja a földrajzi helymeghatározást a leghatékonyabb végpont meghatározásához. Minden használt régióban külön beszédszolgáltatási előfizetésre van szüksége.

Ha az alkalmazás hosszú élettartamú kapcsolatokat használ, és nem tud elérhető SDK-t használni, websocket-kapcsolatot használhat. A 10 perces időkorlát kezelése a megfelelő időpontokban történő újracsatlakozással.

A beszédfelismerési SDK első lépései:

1. Töltse le a [Beszéd SDK](speech-sdk.md).
1. A Beszédszolgáltatás [rövid útmutatóinak](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) és [oktatóanyagainak](how-to-recognize-intents-from-speech-csharp.md)munkája. Tekintse meg a [kódmintákat](samples.md) is, hogy tapasztalatokat szerezzen az új API-kkal kapcsolatban.
1. Frissítse az alkalmazást a beszédfelismerési szolgáltatás használatához.

## <a name="support"></a>Támogatás

A Bing Speech ügyfelei támogatási jegy megnyitásával vegyék fel a kapcsolatot az [ügyfélszolgálattal.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Akkor is felveheti velünk a kapcsolatot, ha a támogatásra [technikai támogatási csomagra](https://azure.microsoft.com/support/plans/)van szüksége.

A beszédfelismerési szolgáltatás, az SDK és az API támogatása a Beszédszolgáltatás [támogatási lapján található.](support.md)

## <a name="next-steps"></a>További lépések

* [Próbálja ki ingyen a Beszédszolgáltatást](get-started.md)
* [Rövid útmutató: Beszédfelismerés egy UWP-alkalmazásban a beszédfelismerési SDK használatával](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Lásd még
* [Beszédszolgáltatás kiadási jegyzetei](releasenotes.md)
* [Mi a beszédszolgáltatás](overview.md)
* [Beszédszolgáltatás és beszédsdka dokumentációja](speech-sdk.md#get-the-sdk)
