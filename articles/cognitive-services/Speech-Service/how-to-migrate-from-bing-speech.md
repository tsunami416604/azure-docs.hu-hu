---
title: Bing Speech áttelepít a beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg a fejlesztői szempontból Bing Speech és a Speech Service közötti különbségeket, és a Speech szolgáltatással az alkalmazás migrálása a.
services: cognitive-services
author: wsturman
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: 432a17b1e3cd065bd6189ffa9f80ca5985629bb7
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239949"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Bing Speech áttelepít a beszédszolgáltatás

Ez a cikk segítségével az alkalmazások áttelepíthetők a Bing Speech API a Speech Service.

Ez a cikk a Bing Speech API-k és a Speech Service közötti különbségeket ismerteti, és az alkalmazások áttelepítéséhez stratégiák javasol. A Bing Speech API-előfizetési kulcs nem fogadja el a Speech Service; egy új beszédszolgáltatás előfizetésre lesz szüksége.

Beszédszolgáltatás egy előfizetési kulcs a következő funkciókhoz való hozzáférést. Minden egyes forgalmi díjas külön-külön, így csak használt díjkötelesek.

* [Hang-szöveg](speech-to-text.md)
* [Egyéni hang-szöveg](https://cris.ai)
* [Szöveg-hang transzformációs](text-to-speech.md)
* [Egyéni szövegfelolvasás hangok](how-to-customize-voice-font.md)
* [Beszédalapú fordítási](speech-translation.md) (nem tartalmaz [szövegfordítás](../translator/translator-info-overview.md))

A [beszéd SDK](speech-sdk.md) a Bing Speech-klienskódtárakkal működési helyettesíti, de egy másik API-t használja.

## <a name="comparison-of-features"></a>Szolgáltatások összehasonlítása

A beszédfelismerési szolgáltatás jelenleg nagymértékben funkció, a platform és a programozási nyelvparitást nyújt a Bing Speech a következő eltérésekkel.

Szolgáltatás | Bing – Beszédfelismerés | Beszédszolgáltatás | Részletek
-|-|-|-
C++ SDK | : heavy_minus_sign: | : heavy_check_mark: | Beszédszolgáltatás támogatja a Windows és Linux
Java SDK | : heavy_check_mark: | : heavy_check_mark: | Beszédszolgáltatás támogatja az Android- és beszédfelismerés eszközök
C# SDK | : heavy_check_mark: | : heavy_check_mark: | Beszédszolgáltatás támogatja a Windows 10 UWP és a .NET Standard 2.0
Folyamatos beszédfelismerés | 10 perc | Korlátlan (az SDK-val) | Bing Speech és a Speech Service WebSockets protokollt is támogatja a hívásonként legfeljebb 10 perc. A beszédfelismerés SDK azonban automatikusan újracsatlakozik időtúllépés miatt, vagy válassza le.
Részleges vagy köztes eredményeket | : heavy_check_mark: | : heavy_check_mark: | A WebSockets protokoll vagy SDK-t
Egyéni beszédfelismerési modelleket | : heavy_check_mark: | : heavy_check_mark: | Bing Speech külön Custom Speech-előfizetést igényel
Egyéni hangtípust | : heavy_check_mark: | : heavy_check_mark: | Bing Speech egyéni beszédfelismerési különálló előfizetés szükséges
24-kHz beszédhangot | : heavy_minus_sign: | : heavy_check_mark: 
Beszéd szándékának felismerése | Intelligens HANGFELISMERÉSI API-hívás külön igényel | (SDK-val integrált) |  A LUIS-kulcs a beszédfelismerési szolgáltatás is használhatók.
Egyszerű szándékfelismerés | : heavy_minus_sign: | : heavy_check_mark: 
A Batch beszédátírási hosszú hangfájlok | : heavy_minus_sign: | : heavy_check_mark:
Elismerés mód | Manuális végpont URI-n keresztül | Automatikus | Elismerés mód Speech Service-ben nem érhető el
Végpont helye | Globális | Regionális | Regionális végpontok javíthatja a késés. Egy globális végpontja figyelembe veszi a beszédfelismerési szolgáltatás alatt áll.
REST API-k | : heavy_check_mark: | : heavy_check_mark: | Beszédfelismerési szolgáltatás REST API a Bing Speech (másik végpont) esetén. REST API-k támogatják a szöveg-beszéd átalakítás, és csak hang-szöveg transzformációs funkciót.
A websockets protokoll protokollok | : heavy_check_mark: | : heavy_check_mark: | Bing Speech (másik végpont) esetén a websockets protokoll Beszédszolgáltatási API Használatába. Telepítse át a Speech SDK lemezrendszert egyszerűbbé teszik a kódolást.
Szolgáltatások közötti API-hívások | : heavy_check_mark: | : heavy_minus_sign: | A Bing Speech, a C#-szolgáltatási kódtára keresztül biztosított. 
Nyílt forráskódú SDK-t | : heavy_check_mark: | : heavy_minus_sign: |

A Speech Service díjszabási modellt időalapú (helyett egy tranzakció-alapú modell) használja. Lásd: [Speech Service díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) részleteiről.

## <a name="migration-strategies"></a>Migrálási stratégiák

Ha Ön vagy a szervezet rendelkezik a Bing Speech API-t használó alkalmazások fejlesztési vagy éles környezetben, frissítenie kell azokat a Speech Service minél hamarabb használatára. Tekintse meg a [Speech Service dokumentációjában](index.yml) elérhető SDK-k, Kódminták és oktatóanyagok.

A beszédfelismerési szolgáltatás [REST API-k](rest-apis.md) kompatibilisek a Bing Speech API-kat. Ha jelenleg használja a Bing Speech REST API-k, kell csak módosítsa a REST-végponthoz, és váltson át egy Speech Service előfizetési kulcsot.

A beszédfelismerési szolgáltatás WebSockets protokollt is használta a Bing Speech összhangban legyenek. Azt javasoljuk, hogy az új fejlesztési célként a Speech Service SDK helyett használja a websockets protokollt, és azt javasoljuk, hogy az SDK-hoz, valamint a meglévő kód áttelepítése. Azonban, a REST API-k, a meglévő kódot, amely használja a websockets protokoll használatával a Bing Speech csak változások szükségesek az végpontját és a egy frissített kulcsot.

Áttelepítés a Bing Speech ügyféloldali kódtár használja egy adott programozási nyelvhez, ha a [beszéd SDK](speech-sdk.md) az alkalmazás-módosítások szükségesek, mert az API-t nem egyezik. A beszédfelismerés SDK is egyszerűbbé teszik a kódját is betekinthet a új szolgáltatások közben.

A beszédfelismerés SDK jelenleg C# (Windows 10 UWP, .NET Standard), Java (Android és az egyéni eszközök), (iOS) Objective C, C++ (Windows és Linux) és a JavaScript támogatja. API-k minden platformon hasonlóak, egyszerűsített többplatformos fejlesztést.

A beszédfelismerési szolgáltatás jelenleg nem kínál egy globális végpontja. Határozza meg, ha az alkalmazás működik hatékonyan navigálhat az összes, a forgalom egy regionális végpontot kell. Ha nem, földrajzi hely meghatározásának segítségével határozhatja meg, a leghatékonyabb végpontot. Az egyes régiókban használhatja egy külön Speech Service-előfizetés szükséges.

Ha az alkalmazás hosszú élettartamú kapcsolatokat használ, és nem használható egy elérhető SDK-t, WebsSockets kapcsolatot használ, és kezelheti a 10 perces időtúllépési korlát újracsatlakozás a megfelelő időben.

Ismerkedés az új beszéd SDK-val:

1. Töltse le a [beszéd SDK](speech-sdk.md).
1. Munka a Speech szolgáltatással [gyors útmutatók](quickstart-csharp-dotnet-windows.md), [oktatóanyagok](how-to-recognize-intents-from-speech-csharp.md), és tekintse meg a [Kódminták](samples.md) élmény az új API-khoz való.
1. Az új beszédszolgáltatás és API-k használata az alkalmazás frissítése.

## <a name="support"></a>Támogatás

Bing Speech ügyfelek kell forduljon az ügyfélszolgálathoz megnyitásával egy [támogatási jegyet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Akkor is kapcsolatba léphet velünk, ha a támogatási szükség van szükség egy [technikai támogatási csomag](https://azure.microsoft.com/support/plans/).

Beszédszolgáltatás, SDK és API-támogatást, látogasson el a Speech Service [támogatási oldal](support.md).

## <a name="next-steps"></a>További lépések

* [Speech Service ingyenes kipróbálása](get-started.md)
* [Gyors útmutató: Recognize speech, a beszéd SDK használatával egy UWP-alkalmazás](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Lásd még
* [Beszédfelismerési szolgáltatás kibocsátási megjegyzései](releasenotes.md)
* [Mi az a Speech service](overview.md)
* [Beszédszolgáltatás és az SDK-dokumentáció](speech-sdk.md#get-the-sdk)
