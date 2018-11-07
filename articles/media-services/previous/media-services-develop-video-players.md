---
title: Videolejátszó alkalmazások készítése
description: A témakör hivatkozásokat tartalmaz a Player keretrendszerekkel és beépülő modulokat, amelyek segítségével a saját médiafolyamainak a Media Services ügyfél alkalmazásokat fejleszthet.
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
ms.openlocfilehash: d7f55022dc526f44251814ac953787d6652e6f90
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51237043"
---
# <a name="develop-video-player-applications"></a>Videolejátszó alkalmazások készítése
## <a name="overview"></a>Áttekintés
Az Azure Media Services biztosította eszközökkel részletes, dinamikus ügyféloldali lejátszóalkalmazások hozhatók létre a legtöbb platformra, köztük a következőkre: iOS, Android, Windows, Windows Phone, Xbox és dekóderek. Ez a témakör hivatkozásokat is tartalmaz az SDK-k és a Player keretrendszerek, amelyek segítségével a saját médiafolyamainak az Azure Media Services ügyfél alkalmazásokat fejleszthet.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
 
## <a name="azure-media-player"></a>Azure Media Player
[Az Azure Media Player](https://aka.ms/ampinfo) webes videolejátszót médiatartalmak lejátszására a Microsoft Azure Media Services számos különböző böngészők és eszközök is. Az Azure Media Player iparági szabványok – például HTML5, Media forrás Extensions (MSE) és az Encrypted Media Extensions (EME) adjon meg egy streamelési megoldást használja. Ha ezek a szabványok nem érhetők el, az eszközön vagy böngészőben, az Azure Media Player használja Flashre és Silverlightra tartalék technológia. A fejlesztők a lejátszási technológiától függetlenül egy egységes JavaScript-felület API-k elérése kell. Ez lehetővé teszi az Azure Media Services által kiszolgált tartalom wide-számos eszköz és bármely extra erőfeszítést böngészőkben lejátszhatók legyenek.

A Microsoft Azure Media Services lehetővé teszi, hogy a tartalom kiszolgálása DASH, Smooth Streaming és HLS formátumban vissza tartalom lejátszása. Az Azure Media Player a különféle formátumokat figyelembe veszi, és automatikusan játszik a legjobb hivatkozás, a platformjától és böngészői képességeitől alapján. A Microsoft Azure Media Services emellett lehetővé teszi, hogy a dinamikus titkosítást, PlayReady-titkosítást az eszközök, vagy a 128 bites AES-boríték-titkosítást. Az Azure Media Player lehetővé teszi a PlayReady-titkosítás visszafejtésére és 128 bites AES-titkosított tartalmat, ha megfelelően konfigurálva. 

További információk:

* [Azure Media Player](https://aka.ms/ampinfo)
* [Az Azure Media Player dokumentációja](https://aka.ms/ampdocs) 
* [Az Azure Media Player első lépések Blog](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Maradjon naprakész a legújabb, az Azure Media Player regisztráljon](https://aka.ms/ampsignup)
* [Adja hozzá az új funkciókra vonatkozó kérések, ötleteket, visszajelzés](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Más eszközök lejátszó alkalmazások létrehozásához
A következő SDK-k bármelyikét is használhatja:

* [Smooth Streaming Client SDK](http://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Smooth Streaming Windows Store-alkalmazás](media-services-build-smooth-streaming-apps.md)
* [A Microsoft Media Platform: Player keretrendszer](http://playerframework.codeplex.com/) 
* [HTML5-alapú Player keretrendszer dokumentáció](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [A Microsoft Smooth Streaming beépülő modul OSMF-hez](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licencelési Microsoft® Smooth Streaming Ügyfélportolási készlet](https://aka.ms/sspk) 
* [Videó XBOX fejlesztése](https://xbox.create.msdn.com/) 

## <a name="advertising"></a>Hirdetés
Az Azure Media Services támogatja a reklámjelölőket keresztül a Windows Media Platform: Player keretrendszerekkel. Ad-támogatás a Player keretrendszerek érhetők el a Windows 8, a Silverlight, a Windows Phone 8 és az iOS-eszközökhöz. Minden egyes player keretrendszer, amely bemutatja, hogyan valósíthat meg egy médialejátszó alkalmazásba mintakód tartalmazza. Nincsenek szúrhat be a multimédiás ads három különböző típusú:

Lineáris – a teljes keret hirdetések fő videó megállítása

Lineáris – területre hirdetések vannak feltüntetve, a fő videó lejátszása, általában egy emblémát vagy más a lejátszón belül elhelyezett statikus kép

Kiegészítő – kívül a Windows Media player megjelenített hirdetések

Hirdetések helyezhető a fő videó idősorán bármely pontján. A Windows Media player kell mondja el, ha lejátszása az ad és melyik lejátszásához hirdetéseket. Ebben az esetben a szabványos XML-alapú fájlokat használ: a videó Ad szolgáltatás sablon (VAST), a digitális videót több Ad lista (VMAP), a Media absztrakt alkalmazás-előkészítés sablon (OSZLOPOS) és a digitális videót Player Ad Interface Definition (VPAID). HATALMAS fájlokat adja meg, milyen hirdetések megjelenítéséhez. VMAP fájlokat adja meg, mikor lejátszása különböző hirdetések és HATALMAS XML-kódot tartalmaz. OSZLOPOS fájlok módon egy másik feladatütemezési ads is tartalmazó HATALMAS XML. VPAID fájlokat adja meg a videó lejátszási és az ad vagy ad-kiszolgáló közötti illesztőfelületet. További információkért lásd: [Beszúrás, hirdetések](https://msdn.microsoft.com/library/dn387398.aspx).

A feliratozás és hirdetések támogatása az élő adatfolyam-továbbítási videók kapcsolatos információkért lásd: [támogatott kódolt feliratok és Ad beszúrási szabványok](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[MPEG-DASH adaptív streamelt videók beágyazása DASH.js-sel rendelkező HTML5-alkalmazásba](media-services-embed-mpeg-dash-in-html5.md)

[GitHub-adattár dash.js-sel](https://github.com/Dash-Industry-Forum/dash.js)

