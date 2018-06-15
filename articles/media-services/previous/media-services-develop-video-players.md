---
title: Videolejátszó alkalmazások készítése
description: A témakör hivatkozásokat tartalmaz a lejátszó-Keretrendszerekhez és beépülő modulok, amelyek segítségével a saját adatfolyamokat a Media Services médiafolyamainak fogadására ügyfélalkalmazások fejlesztéséhez.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: juliako
ms.openlocfilehash: 71d8b020fe96094c15965fd82615e3182e333990
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788689"
---
# <a name="develop-video-player-applications"></a>Videolejátszó alkalmazások készítése
## <a name="overview"></a>Áttekintés
Az Azure Media Services biztosította eszközökkel részletes, dinamikus ügyféloldali lejátszóalkalmazások hozhatók létre a legtöbb platformra, köztük a következőkre: iOS, Android, Windows, Windows Phone, Xbox és dekóderek. Ez a témakör hivatkozásokat is tartalmaz az SDK-khoz és lejátszó-Keretrendszerekhez, használhatja a saját médiafolyamot az Azure Media Services médiafolyamainak fogadására ügyfélalkalmazások fejlesztéséhez.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
 
## <a name="azure-media-player"></a>Azure Media Player
[Az Azure Media Player](http://aka.ms/ampinfo) webes videólejátszó lejátszásához médiatartalom a Microsoft Azure Media Services a böngészőkkel és eszközökkel számos beépített. Az Azure Media Player ipari szabványok, például a HTML5, Media forrás Extensions (MSE), és a bővített adaptív adatfolyam-továbbítási élményt nyújt a titkosított adathordozó-bővítmények (EME) használja. Ha ezeknek a szabványoknak nem érhetők el, az eszközön, vagy a böngészőben, Azure Media Player Flash és használja a Silverlight tartalék technológia. A lejátszás technológiát használja, függetlenül a fejlesztők egy egyesített JavaScript felület API-k elérésére fog rendelkezni. Ez lehetővé teszi az Azure Media Services által kiszolgált tartalmat lejátszható wide-számos eszköz és bármely extra erőfeszítést böngészők között.

Microsoft Azure Media Services lehetővé teszi, hogy a tartalom szeretne kiszolgálni DASH, Smooth Streaming és adatfolyam-továbbítási formátumokba HLS vissza tartalom lejátszása. Az Azure Media Player figyelembe veszi a különböző formátumokba, és automatikusan szerepe a legjobb hivatkozás a platform/böngésző képességei alapján. A Microsoft Azure Media Services is lehetővé teszi az eszközök dinamikus titkosítást a PlayReady-titkosítás vagy AES-128 bites titkosítás boríték. Az Azure Media Player lehetővé teszi, hogy PlayReady visszafejtése, és az AES-128 bites titkosított tartalom abban az esetben, ha megfelelően konfigurálva. 

További információk:

* [Azure Media Player](http://aka.ms/ampinfo)
* [Az Azure Media Player dokumentáció](http://aka.ms/ampdocs) 
* [Az Azure Media Player első lépések Blog](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Maradjon naprakész az Azure Media Player legújabb regisztráció](http://aka.ms/ampsignup)
* [Adja hozzá az új funkciókra vonatkozó kérések, ötleteket, visszajelzés](http://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Player alkalmazások létrehozására vonatkozó egyéb eszközök
A következő SDK-k bármelyikét is használhatja:

* [Adatfolyam-továbbítási ügyfél SDK sima](http://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Zökkenőmentes adatfolyam Windows Áruházbeli alkalmazás](media-services-build-smooth-streaming-apps.md)
* [Microsoft Media Platform: Player keretrendszer](http://playerframework.codeplex.com/) 
* [HTML5 Player keretrendszer dokumentáció](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Microsoft Smooth Streaming beépülő modul a OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licencelési Microsoft® zökkenőmentes adatfolyam ügyfél Kit eljárás](http://aka.ms/sspk) 
* [XBOX videó alkalmazásfejlesztés](http://xbox.create.msdn.com/) 

## <a name="advertising"></a>Hirdetés
Az Azure Media Services támogatást nyújt a Windows Media platformon keresztül ad beszúrási: lejátszó-Keretrendszerekhez. Ad-támogatással rendelkező lejátszó-keretrendszerekhez Windows 8, a Silverlight, a Windows Phone 8 és az iOS-eszközök érhetők el. Minden egyes player keretrendszer mintakód bemutatja, hogy a lejátszóalkalmazás megvalósításához tartalmazza. Nincsenek a media beilleszthet ads három különböző típusú:

Lineáris – teljes keret hirdetések felfüggeszti a fő videó

Nem lineáris – jelennek meg a fő videó lejátszása átfedő hirdetések, általában egy embléma vagy a Windows Media player belül elhelyezett más statikus kép

Kiegészítő – kívül a Windows Media player megjelenített ads

A fő videó idősorán bármikor ADs helyezhető. A Windows Media player kell arról, mikor számára, hogy az ad és számára, hogy mely hirdetések. Ebben az esetben a szabványos XML alapú fájlok készletből: videó Ad szolgáltatás sablon (VAST), a digitális videót több Ad lista (VMAP), a Media absztrakt alkalmazás-előkészítés sablon (OSZLOPOS) és a digitális videót Player Ad felület Definition (VPAID). NAGY fájlok adja meg, milyen ads megjelenítéséhez. VMAP fájlok idejére különböző ads lejátszásához és HATALMAS XML kódot tartalmaz. A fájlok OSZLOPOS feladatütemezési ads is tartalmazó túlnyomó XML másik módja van. VPAID fájlok határozza meg azt a videólejátszó és az ad vagy ad-kiszolgáló közötti illesztőfelületet szolgáltasson. További információkért lásd: [beszúrása Ads](https://msdn.microsoft.com/library/dn387398.aspx).

Feliratok és élő adatfolyam-továbbítási videók ads támogatással kapcsolatos információkért lásd: [támogatott kódolt feliratok és Ad beszúrási szabványok](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[MPEG-DASH adaptív streamelt videók beágyazása DASH.js-sel rendelkező HTML5-alkalmazásba](media-services-embed-mpeg-dash-in-html5.md)

[GitHub-tárházban dash.js](https://github.com/Dash-Industry-Forum/dash.js)

