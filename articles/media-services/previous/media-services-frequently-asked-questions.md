---
title: Az Azure Media Services – gyakori kérdések |} A Microsoft Docs
description: Gyakori kérdések (GYIK)
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 3164445dc28a2436d8492902d865414da8d75702
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188016"
---
# <a name="frequently-asked-questions"></a>Gyakori kérdések

Ez a cikk a gyakori kérdések az Azure Media Services (AMS) felhasználói Közösség által kiváltott foglalkozik.

## <a name="general-ams-faqs"></a>Általános AMS – gyakori kérdések

K: Hogyan tegye meg streamelése Apple iOS-eszközökön?

V: hozzáadása "(formátum = m3u8-aapl)" elérési útját adja meg a streamelési forráskiszolgáló eszközöket adja vissza a felhasználásához vissza HLS-tartalom Apple iOS rendszeren natív URL-címe "/ jegyzékfájl" részét (további információkért lásd: [történő tartalomtovábbításhoz](media-services-deliver-content-overview.md)),

K: Hogyan, méretezhető, az indexelés?

V: A szolgáltatás számára fenntartott egységek ugyanazok a kódolás és indexelési feladatokat. Kövesse az utasításokat [méretezési kódoláshoz fenntartott egységek hogyan](media-services-scale-media-processing-overview.md). **Megjegyzés:** , hogy az indexelő teljesítményét nem befolyásolja a fenntartott egység típussal.

K: E feltöltött, kódolt és közzétett egy videót. Mi lenne a OK, a videó nem lejátszani jelenik meg, adatfolyamként?

V: A leggyakoribb okok egyike, nem rendelkezik a streamvégpontra, amelyről lejátszani kívánt a **futó** állapota.  

K: Használhatom az élő stream összeállítás?

V: Az élő adatfolyamok összeállítás jelenleg nem érhető el az Azure Media Servicesben, így előre compose a számítógépen kell.

K: Az Azure CDN használata Live Streaming?

V: A Media Services az Azure CDN-integrációt támogatja (további információkért lásd: [adatfolyam-továbbítási végpontok kezelése egy Media Services-fiók hogyan](media-services-portal-manage-streaming-endpoints.md)).  Élő adások online közvetítése a CDN-t is használhatja. Az Azure Media Services biztosítja a Smooth Streaming, HLS és MPEG-DASH kimenetek. Ezek a formátumok adatok átvitele a HTTP Protokollt használja, és a get HTTP-gyorsítótár használatának előnyei. Az élő streamelés tényleges, videó vagy hang a töredékekre van osztva, és ez egyes töredék első gyorsítótárazza a CDN-t. Csak adattárolási igényeinek lesznek frissítve az alkalmazásjegyzék adatok. A CDN rendszeres időközönként frissíti a jegyzékfájl adatokat.

K: Az Azure Media services támogatja a lemezképek tárolását?

V: Ha csak JPEG vagy PNG-képek tárolásához, érdemes megtartani az Azure Blob Storage. Nem jár előnnyel, kivéve, ha meg szeretné tartani őket a videó vagy hang eszközök társított helyezné azokat a Media Services-fiók. Vagy ha lehet, hogy nincs szüksége a képek hangátfedések, a videó kódoló a használandó. Media Encoder Standard támogatja az átfedő képek, videók felett, és ez milyen felsorolja JPEG és PNG támogatott bemeneti formátumok. További információkért lásd: [létrehozása átfedések](media-services-advanced-encoding-with-mes.md#overlay).

K: Hogyan lehet másolható eszközök egy Media Services-fiókból egy másikba?

V: Eszközök átmásolni egy Media Services-fiók egy másik használata a .NET használatával [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) elérhető metódust a [Azure Media Services .NET SDK-bővítmények](https://github.com/Azure/azure-sdk-for-media-services-extensions/) tárház. További információkért lásd: [ez](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) fórum-hozzászóláslánc.

K: Mik azok a fájlok elnevezési az AMS használatakor a támogatott karakterek?

V: A Media Services a IAssetFile.Name tulajdonság értékét használja, URL-címek létrehozását, a streamelési tartalom (például http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Ebből kifolyólag százalék-kódolást nem engedélyezett. Értékét a **neve** tulajdonság nem lehet a következő [százalék-kódolás – fenntartott karakterek](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Emellett csak lehet egy "." a fájlnév kiterjesztésével.

K: Hogyan lehet csatlakozni a REST használatával?

V: Az AMS API-t kapcsolódás információkért lásd: [eléréséhez az Azure Media Services API Azure AD-hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

K: Hogyan lehet egy videó elforgatása a kódolás során?

V: A [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) szögek rotációját 90/180 vagy 270 támogatja. Alapértelmezés szerint az "Auto", ahol megpróbálja Elforgatás metaadatait a bejövő MP4/MOV fájlban, és azt kompenzálja. A következők **források** elem a json-készletek definiált egyik [Itt](media-services-mes-presets-overview.md):

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
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
