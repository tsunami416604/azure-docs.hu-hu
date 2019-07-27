---
title: Áttelepítés Bing Speechról a Speech Service-be
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan telepíthet át egy meglévő Bing Speech-előfizetésből az Azure Cognitive Servicesból a Speech szolgáltatásba.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: nitinme
ms.openlocfilehash: 5694894a78a46ad658ec18f210c6a82fb82df23f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559600"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Áttelepítés Bing Speechról a beszédfelismerési szolgáltatásba

Ebből a cikkből megtudhatja, hogyan telepíthet alkalmazásokat a Bing Speech APIról a Speech szolgáltatásba.

Ez a cikk a Bing Speech API-k és a Speech Services közötti különbségeket ismerteti, és stratégiákat javasol az alkalmazások áttelepítéséhez. A Bing Speech API előfizetés kulcsa nem fog működni a Speech szolgáltatással; szüksége lesz egy új Speech Services-előfizetésre.

Egyetlen Speech Services-előfizetési kulcs biztosítja az alábbi funkciók elérését. Mindegyik funkció forgalmi díját külön állapítjuk meg, így csak a használt funkciókért kell fizetnie.

* [Beszédfelismerés](speech-to-text.md)
* [Egyéni beszédfelismerés](https://cris.ai)
* [Szövegfelolvasás](text-to-speech.md)
* [Egyéni szövegfelolvasási hangok](how-to-customize-voice-font.md)
* [Tolmácsolás](speech-translation.md) (nem tartalmaz [szövegfordítást](../translator/translator-info-overview.md))

A [SPEECH SDK](speech-sdk.md) a Bing Speech ügyféloldali kódtárak funkcionális cseréje, de más API-t használ.

## <a name="comparison-of-features"></a>Funkciók összehasonlítása

A Speech Services nagymértékben hasonlít a Bing Speechre, a következő eltérésekkel.

Funkció | Bing Speech | Beszédszolgáltatások | Részletek
-|-|-|-
C++SDK | : heavy_minus_sign: | :heavy_check_mark: | A Speech Services támogatja a Windowst és a Linuxot.
Java SDK | :heavy_check_mark: | :heavy_check_mark: | A Speech Services támogatja az androidos és a beszédfelismerési eszközöket.
C# SDK | :heavy_check_mark: | :heavy_check_mark: | A Speech Services támogatja a Windows 10, a Univerzális Windows-platform (UWP) és a .NET Standard 2,0.
Folyamatos beszédfelismerés | 10 perc | Korlátlan (SDK-val) | A Bing Speech és a Speech Services WebSockets protokollok másodpercenként akár 10 percet is igénybe vehetnek. A beszédfelismerési SDK azonban automatikusan újrakapcsolódik az időtúllépéssel vagy a kapcsolat bontásával.
Részleges vagy ideiglenes eredmények | :heavy_check_mark: | :heavy_check_mark: | WebSockets protokollal vagy SDK-val.
Egyéni beszédfelismerési modellek | :heavy_check_mark: | :heavy_check_mark: | Bing Speech külön Custom Speech-előfizetést igényel.
Egyéni hangbetűkészletek | :heavy_check_mark: | :heavy_check_mark: | Bing Speech külön egyéni hangalapú előfizetést igényel.
24 KHz-es hangok | : heavy_minus_sign: | :heavy_check_mark:
Beszéd szándékának felismerése | Külön LUIS API-hívást igényel | Integrált (SDK-val) |  A Speech Service-ben LUIS-kulcsot is használhat.
Egyszerű szándék felismerése | : heavy_minus_sign: | :heavy_check_mark:
Hosszú hangfájlok kötegelt átírása | : heavy_minus_sign: | :heavy_check_mark:
Felismerési mód | Manuális végponti URI-n keresztül | Automatikus | A beszédfelismerési mód nem érhető el a Speech Service-ben.
Végpont helye | Globális | Regionális | A regionális végpontok javítják a késést.
REST API-k | :heavy_check_mark: | :heavy_check_mark: | A Speech Services REST API-jai kompatibilisek Bing Speech (eltérő végponttal). A REST API-k szöveg-beszéd és korlátozott beszéd-szöveg funkciókat támogatnak.
WebSocket-protokollok | :heavy_check_mark: | :heavy_check_mark: | A Speech Services WebSockets API kompatibilis a Bing Speech (különböző végponttal). Ha lehetséges, telepítse át a Speech SDK-ba, hogy leegyszerűsítse a kódot.
Szolgáltatások közötti API-hívások | :heavy_check_mark: | : heavy_minus_sign: | Bing Speech biztosítva a C# szolgáltatás könyvtárán keresztül.
Nyílt forráskódú SDK | :heavy_check_mark: | : heavy_minus_sign: |

A Speech Services időalapú árképzési modellt használ (nem pedig tranzakció alapú modell). A részletekért lásd a [Speech Services díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

## <a name="migration-strategies"></a>Migrálási stratégiák

Ha Ön vagy a szervezete olyan fejlesztést vagy éles üzemben lévő alkalmazásokat használ, amelyek Bing Speech API használnak, a lehető leghamarabb frissítse őket a beszédfelismerési szolgáltatások használatára. Tekintse meg a [Speech Services dokumentációját](index.yml) , amely tartalmazza az elérhető SDK-kat, a kódot és az oktatóanyagokat.

A Speech Services [REST API](rest-apis.md) -jai kompatibilisek a Bing Speech API-kkal. Ha jelenleg a Bing Speech REST API-kat használja, csak a REST-végpontot kell módosítania, és váltania kell egy Speech Services előfizetési kulcsra.

A Speech Services WebSockets protokollok is kompatibilisek az Bing Speech által használtkkal. Javasoljuk, hogy az új fejlesztéshez használja a Speech SDK-t WebSocket helyett. Érdemes áttelepíteni a meglévő kódot az SDK-ba is. A REST API-khoz hasonlóan azonban a Bing Speech websocketeken keresztül használó meglévő kódok csak a végpont és a frissített kulcs módosítását igénylik.

Ha egy Bing Speech ügyféloldali függvénytárat használ egy adott programozási nyelvhez, a [SPEECH SDK](speech-sdk.md) -ba való Migrálás megköveteli az alkalmazás módosítását, mivel az API különbözik. A beszédfelismerési SDK lehetővé teszi a kód egyszerűbb használatát, miközben az új funkciókhoz is hozzáférést biztosít.

Jelenleg a Speech SDK támogatja a C# ([részleteket](https://aka.ms/csspeech)), a Java (Android és egyéni eszközök), a Objective C (iOS) C++ , a (Windows és Linux) és a JavaScript használatát. Az API-k minden platformon hasonlóak, és megkönnyítik a többplatformos fejlesztést.

A Speech Services nem biztosít globális végpontot. Állapítsa meg, hogy az alkalmazás hatékonyan működik-e, ha egyetlen regionális végpontot használ az összes adatforgalmához. Ha nem, a leghatékonyabb végpont meghatározásához használja a térinformatikat. Minden Ön által használt régióban külön Speech Services-előfizetésre van szükség.

Ha az alkalmazás hosszú élettartamú kapcsolatokat használ, és nem használhat elérhető SDK-t, használhat WebSockets-kapcsolatot. A 10 perces időtúllépési korlátot a megfelelő időpontokban történő újrakapcsolattal kezelheti.

Ismerkedés a Speech SDK-val:

1. Töltse le a [beszéd SDK](speech-sdk.md).
1. A Speech Services rövid [útmutatók](quickstart-csharp-dotnet-windows.md) és [oktatóanyagok](how-to-recognize-intents-from-speech-csharp.md)használata. Tekintse meg a [kód mintáit](samples.md) is, és ismerkedjen meg az új API-kkal.
1. Frissítse az alkalmazást a Speech Services használatára.

## <a name="support"></a>Támogatás

Bing Speech ügyfeleknek [támogatási jegy](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)megnyitásával kell felvenniük a kapcsolatot az ügyfélszolgálattal. Ha [technikai támogatási csomagra](https://azure.microsoft.com/support/plans/)van szüksége, vegye fel velünk a kapcsolatot is.

A Speech Service, az SDK és az API támogatásához látogasson el a Speech Services [támogatási oldalára](support.md).

## <a name="next-steps"></a>További lépések

* [Próbálja ki ingyenesen a Speech Services szolgáltatást](get-started.md)
* [Rövid útmutató: Beszédfelismerés felismerése UWP-alkalmazásban a Speech SDK használatával](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Lásd még
* [A Speech Services kibocsátási megjegyzései](releasenotes.md)
* [Mi az a Speech Service](overview.md)
* [A Speech Services és a Speech SDK dokumentációja](speech-sdk.md#get-the-sdk)
