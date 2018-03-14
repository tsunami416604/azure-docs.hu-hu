---
title: "Az Azure Media Services kapcsolatos gyakori kérdések |} Microsoft Docs"
description: "Gyakori kérdések (GYIK)"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 5374f7f4-c189-43ef-8b7f-f2f4141e2748
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: juliako
ms.openlocfilehash: ab66994b0212593aff1384b0801f3359eb0a3751
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2018
---
# <a name="frequently-asked-questions"></a>Gyakori kérdések

Ez a cikk foglalkozik az Azure Media Services (AMS) felhasználói Közösség által kiváltott gyakran ismételt kérdések.

## <a name="general-ams-faqs"></a>Általános AMS – gyakori kérdések

K: hogyan tegye meg az adatfolyamot a Apple iOS-eszközöket

V: hozzáadása "(formátum = m3u8-aapl)" elérési út az URL-cím közölheti a folyamatos átviteli származási kiszolgálóval hátsó HLS tartalom felhasználása vissza az Apple iOS natív eszközök (a részleteket lásd: (tartalomtovábbítás) ["/ Manifest" részéhez Media-services-kézbesítése-tartalom-overview.md]),

K: hogyan, méretezhető, indexelő?

V: a fenntartott egységek esetén azonosak kódolás és indexelő feladat. Kövesse az utasításokat [méretezési kódoláshoz fenntartott egységek hogyan](media-services-scale-media-processing-overview.md). **Megjegyzés:** , hogy az indexelő teljesítmény nem érinti a fenntartott egység típusát.

K: I feltöltött, kódolt és videó közzé. Mi lehet a következő okból: a videó nem tölt meg adatfolyamként való?

A leggyakoribb okai egy A:, nem rendelkezik a streamvégpontra, amelyről kívánt lejátszását a **futtató** állapotát.  

K: feladatokat lehet elvégezni egy élő adatfolyam összeállítás?

A: az élő adatfolyamok összeállítás jelenleg nem érhető el Azure Media Services, így előre állítható össze a számítógépen kell.

K: használhatok Azure CDN élő adatfolyam-továbbítási?

V: Media Services támogatja az Azure CDN integrációja (további információkért lásd: [adatfolyam-továbbítási végpontok kezelése egy Media Services-fiók hogyan](media-services-portal-manage-streaming-endpoints.md)).  Live streaming CDN is használhatja. Az Azure Media Services Smooth Streaming, HLS és MPEG-DASH kimenetek biztosít. Ezek a formátumok adatok átvitele a HTTP Protokollt használja, és a HTTP-gyorsítótárazás előnyök. Élő adatfolyam tényleges videó/hang adatok felosztásának a töredékeket, és az egyes töredék beolvasása gyorsítótárazza a CDN. Csak adattárolási igényeinek frissíteni kell az jegyzék adatai. CDN rendszeresen frissülnek a jegyzék adatokat.

K: Does Azure Media services támogatja a tárolni lemezképeket?

A:, ha most szeretne JPEG vagy PNG lemezképeket menteni, azokat az Azure Blob Storage legyen. Nincs a abba a Media Services-fiók kivéve, ha meg szeretné tartani ezeket a videó vagy a hang eszközök társított előnye. Vagy előfordulhat, hogy a képek használják a videókódoló az átfedések kell. Media Encoder Standard felirataként Képek videók felett, és, hogy mi felsorolja JPEG és PNG támogatott formátumok bemeneti. További információkért lásd: [létrehozása átfedések](media-services-advanced-encoding-with-mes.md#overlay).

K: Hogyan tudom átmásolhatja eszközök egy Media Services-fiók egy másikra.

V: eszközök másolhat egy Media Services-fiók egy másikra történő a .NET, használatával [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) kiterjesztésmetódus érhető el a [Azure Media Services .NET SDK-bővítmények](https://github.com/Azure/azure-sdk-for-media-services-extensions/) tárházba. További információkért lásd: [ez](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) fórum szál.

K: Mik azok a fájlok elnevezési az AMS használatakor a támogatott karakterekből álló?

V: Media Services a IAssetFile.Name tulajdonság értékét használja, amikor az adatfolyam-tartalmak (például http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) a URL-címek kiépítéséhez Emiatt százalék-kódolás nem engedélyezett. Értékét a **neve** tulajdonság nem lehet a következő [százalék kódolás-fenntartott karakterek](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Emellett csak lehet egy "." a fájlnévkiterjesztés.

K: hogyan csatlakozzon a többi használatával?

V: információ az AMS API-hoz kapcsolódáshoz: [elérni az Azure Media Services API-t az Azure AD-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

K: hogyan lehet videó elforgatása a kódolási során.

Válasz: a [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) elforgatási szög által 90/180 vagy 270 támogatja. Az alapértelmezett viselkedés az "Auto", ha megkísérli a Elforgatás metaadatok észlelése a bejövő MP4/MOV fájlban, és ellensúlyozza a azt. Adja meg a következőket **források** elemben, amely a megadott json-készletek egyikét [Itt](media-services-mes-presets-overview.md):

    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
