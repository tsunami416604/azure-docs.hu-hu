---
title: Az Azure Speech Services a Bing Speech áttelepítése
titleSuffix: Azure Cognitive Services
description: Ismerje meg az Azure Speech Services a Bing Speech meglévő előfizetés áttelepítése.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: 6324da55c8af4934185fa39a106939844788adba
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848966"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Bing Speech áttelepít a beszédszolgáltatás

Ez a cikk segítségével az alkalmazások áttelepíthetők a Bing Speech API a Speech Service.

Ez a cikk a Bing Speech API-k és a beszédszolgáltatások közötti különbségeket ismerteti, és az alkalmazások áttelepítéséhez stratégiák javasol. A Bing Speech API-előfizetési kulcs nem fog működni a Speech szolgáltatással egy új beszédszolgáltatások előfizetésre lesz szüksége.

Beszédszolgáltatások egy előfizetési kulcs a következő funkciókhoz való hozzáférést. Mindegyik funkció forgalmi díját külön állapítjuk meg, így csak a használt funkciókért kell fizetnie.

* [Beszédfelismerés](speech-to-text.md)
* [Egyéni beszédfelismerés](https://cris.ai)
* [Szövegfelolvasás](text-to-speech.md)
* [Egyéni szövegfelolvasási hangok](how-to-customize-voice-font.md)
* [Tolmácsolás](speech-translation.md) (nem tartalmaz [szövegfordítást](../translator/translator-info-overview.md))

A [beszéd SDK](speech-sdk.md) a Bing Speech-klienskódtárakkal működési helyettesíti, de egy másik API-t használja.

## <a name="comparison-of-features"></a>Szolgáltatások összehasonlítása

A beszédszolgáltatások hasonlóak nagyrészt a Bing Speech, a következő eltérésekkel.

Szolgáltatás | Bing – Beszédfelismerés | Beszédszolgáltatások | Részletek
-|-|-|-
C++ SDK | : heavy_minus_sign: | :heavy_check_mark: | Beszédszolgáltatások támogatja a Windows és Linux rendszereken.
Java SDK | :heavy_check_mark: | :heavy_check_mark: | Beszédszolgáltatások támogatja az Android- és beszédfelismerés eszközök.
C# SDK | :heavy_check_mark: | :heavy_check_mark: | Beszédszolgáltatások támogatja a Windows 10-es, a Universal Windows Platform (UWP) és a .NET Standard 2.0.
Folyamatos beszédfelismerés | 10 perc | Korlátlan (az SDK-val) | Bing Speech és a Speech Services WebSockets protokollt is támogatja a hívásonként legfeljebb 10 perc. A beszédfelismerés SDK azonban automatikusan újracsatlakozik időtúllépés miatt, vagy válassza le.
Részleges vagy köztes eredményeket | :heavy_check_mark: | :heavy_check_mark: | A WebSockets protokoll vagy SDK-t.
Egyéni beszédfelismerési modelleket | :heavy_check_mark: | :heavy_check_mark: | Bing Speech külön Custom Speech-előfizetést igényel.
Egyéni hangtípust | :heavy_check_mark: | :heavy_check_mark: | Bing Speech egyéni beszédfelismerési különálló előfizetés szükséges.
24-kHz beszédhangot | : heavy_minus_sign: | :heavy_check_mark:
Beszéd szándékának felismerése | Intelligens HANGFELISMERÉSI API-hívás külön igényel | (SDK-val integrált) |  A LUIS billentyűvel a Speech szolgáltatással.
Egyszerű szándékfelismerés | : heavy_minus_sign: | :heavy_check_mark:
A Batch beszédátírási hosszú hangfájlok | : heavy_minus_sign: | :heavy_check_mark:
Felismerési mód | Manuális végpont URI-n keresztül | Automatikus | Elismerés mód Speech Service-ben nem érhető el.
Végpont helye | Globális | Regionális | Regionális végpontok javíthatja a késés.
REST API-k | :heavy_check_mark: | :heavy_check_mark: | A Speech Services REST API-k kompatibilis a Bing Speech (másik végpont). REST API-k támogatják a szöveg-beszéd átalakítás, és csak hang-szöveg transzformációs funkciót.
A websockets protokoll protokollok | :heavy_check_mark: | :heavy_check_mark: | A Speech Services WebSockets API egy kompatibilis a Bing Speech (másik végpont). Ha lehetséges, a beszéd SDK át egyszerűbbé teszik a kódolást.
Szolgáltatások közötti API-hívások | :heavy_check_mark: | : heavy_minus_sign: | A Bing Speech, a C#-szolgáltatási kódtára keresztül biztosított.
Nyílt forráskódú SDK-t | :heavy_check_mark: | : heavy_minus_sign: |

A beszédszolgáltatások időalapú díjszabási modellt (helyett egy tranzakció-alapú modell) használja. Lásd: [Speech Services díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) részleteiről.

## <a name="migration-strategies"></a>Migrálási stratégiák

Ha Ön vagy a szervezet rendelkezik a Bing Speech API-t használó alkalmazások fejlesztési vagy éles környezetben, frissítenie kell azokat a beszédszolgáltatások minél hamarabb használatára. Tekintse meg a [Speech Services – dokumentáció](index.yml) elérhető SDK-k, Kódminták és oktatóanyagok.

A beszédszolgáltatások [REST API-k](rest-apis.md) kompatibilisek a Bing Speech API-kat. Ha jelenleg használja a Bing Speech REST API-k, kell csak módosítsa a REST-végponthoz, és váltson át egy beszédszolgáltatások előfizetési kulcsot.

A Speech Services WebSockets protokollt is használta a Bing Speech összhangban legyenek. Azt javasoljuk, hogy az új fejlesztési feladatokhoz, használja a websockets protokoll helyett a Speech SDK. Célszerű az SDK-hoz, valamint a meglévő kód áttelepítése. Azonban, a REST API-k, a meglévő kódot, amely használja a websockets protokoll használatával a Bing Speech csak változások szükségesek az végpontját és a egy frissített kulcsot.

Ha egy adott programozási nyelvhez használja a Bing Speech ügyféloldali kódtár,-ba való migrálás a [beszéd SDK](speech-sdk.md) az alkalmazás módosítását igényli, mivel az API-t nem egyezik. A beszédfelismerés SDK-t is egyszerűbbé teszik a kódját, közben is betekinthet a új szolgáltatások.

A beszédfelismerés SDK jelenleg C# (Windows 10 UWP, .NET Standard), Java (Android és az egyéni eszközök), (iOS) Objective C, C++ (Windows és Linux) és a JavaScript támogatja. API-k minden platformon hasonlóak, egyszerűsített többplatformos fejlesztést.

A beszédfelismerési szolgáltatás globális végpontja nem olyan. Határozza meg, ha ha használ egy regionális végpontot az összes, a forgalom hatékony működik-e az alkalmazás. Ha nem, földrajzi hely meghatározásának segítségével határozhatja meg, a leghatékonyabb végpontot. Az egyes régiókban használhat egy külön beszédszolgáltatások előfizetés szükséges.

Ha az alkalmazás hosszú élettartamú kapcsolatokat használ, és nem használható egy elérhető SDK-t, a websockets protokoll kapcsolatot is használhatja. Újracsatlakozás a megfelelő időben kezelheti a 10 perces időtúllépési korlát.

Első lépések a beszédfelismerő SDK:

1. Töltse le a [beszéd SDK](speech-sdk.md).
1. A beszédszolgáltatások terjedő munkához [gyors útmutatók](quickstart-csharp-dotnet-windows.md) és [oktatóanyagok](how-to-recognize-intents-from-speech-csharp.md). Nézze át a [Kódminták](samples.md) élmény az új API-khoz való.
1. Frissítheti az alkalmazást, a beszéd-szolgáltatások használatára.

## <a name="support"></a>Támogatás

Bing Speech ügyfelek kell forduljon az ügyfélszolgálathoz megnyitásával egy [támogatási jegyet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Akkor is kapcsolatba léphet velünk, ha a támogatási szükség van szükség egy [technikai támogatási csomag](https://azure.microsoft.com/support/plans/).

Beszédszolgáltatás, SDK és API-támogatást, látogasson el a beszédszolgáltatások [támogatási oldal](support.md).

## <a name="next-steps"></a>További lépések

* [Beszédszolgáltatások ingyenes kipróbálása](get-started.md)
* [Rövid útmutató: A beszédfelismerés SDK használatával egy UWP-alkalmazás a beszédfelismerést](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Lásd még
* [Beszédszolgáltatások kibocsátási megjegyzései](releasenotes.md)
* [Mi az a Speech Service](overview.md)
* [Beszédszolgáltatások és a Speech SDK dokumentációja](speech-sdk.md#get-the-sdk)
