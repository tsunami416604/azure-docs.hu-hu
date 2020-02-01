---
title: Videolejátszó alkalmazások készítése
description: A témakör a Player-keretrendszerekre és-bővítményekre mutató hivatkozásokat tartalmaz, amelyek segítségével saját ügyfélalkalmazások fejleszthetők, amelyekkel a Media Services adatfolyamot lehet használni.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 39459267919747ed49e9fa3f05746294eaf741dc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906825"
---
# <a name="develop-video-player-applications"></a>Videolejátszó alkalmazások készítése
## <a name="overview"></a>Áttekintés
Az Azure Media Services biztosította eszközökkel részletes, dinamikus ügyféloldali lejátszóalkalmazások hozhatók létre a legtöbb platformra, köztük a következőkre: iOS, Android, Windows, Windows Phone, Xbox és dekóderek. Ez a témakör az SDK-k és a Player-keretrendszerek hivatkozásait is tartalmazza, amelyek segítségével saját ügyfélalkalmazások fejleszthetők, amelyekkel a Azure Media Services adatfolyamot használhatnak.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
 
## <a name="azure-media-player"></a>Azure Media Player
A [Azure Media Player](https://aka.ms/ampinfo) egy webvideós lejátszó, amely a Microsoft Azure Media Services különböző böngészők és eszközök által készített médiatartalom lejátszására készült. A Azure Media Player az iparági szabványokat, például a HTML5-t, a Media Source Extensions (MSE) és a titkosított adathordozó-bővítményeket (EME) használja a dúsított Adaptív átviteli élmény biztosításához. Ha ezek a szabványok nem érhetők el egy eszközön vagy böngészőben, Azure Media Player a Flash és a Silverlight protokollt használja tartalék technológiaként. A használt lejátszási technológiától függetlenül a fejlesztőknek egységes JavaScript-felülettel kell rendelkezniük az API-k eléréséhez. Ez lehetővé teszi, hogy a Azure Media Services által kiszolgált tartalmak a további erőfeszítések nélkül, széles körű eszközökön és böngészőkön keresztül legyenek játszhatók.

Microsoft Azure Media Services lehetővé teszi, hogy a tartalmat kötőjel, Smooth Streaming és HLS formájában lehessen kézbesíteni a tartalmak lejátszásához. Azure Media Player a különböző formátumokat veszi figyelembe, és a platform/böngésző képességei alapján automatikusan a legjobb hivatkozást játssza le. A Microsoft Azure Media Services lehetővé teszi az eszközök dinamikus titkosítását PlayReady titkosítással vagy AES-128 bites boríték-titkosítással. A Azure Media Player lehetővé teszi a PlayReady és az AES-128 bites titkosított tartalom visszafejtését, ha az megfelelően van konfigurálva. 

További információ:

* [Azure Media Player](https://aka.ms/ampinfo)
* [Azure Media Player dokumentáció](https://aka.ms/ampdocs) 
* [Azure Media Player Első lépések blog](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Regisztráljon, hogy naprakész maradjon a legújabb Azure Media Player](https://aka.ms/ampsignup)
* [Új szolgáltatásokra vonatkozó kérések, ötletek és visszajelzések hozzáadása](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Más eszközök a Player-alkalmazások létrehozásához
A következő SDK-kat is használhatja:

* [Ügyféloldali SDK Smooth Streaming](https://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Windows áruházbeli alkalmazás Smooth Streaming](media-services-build-smooth-streaming-apps.md)
* [Microsoft Media platform: Player Framework](https://playerframework.codeplex.com/) 
* [A HTML5 Player Framework dokumentációja](https://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Microsoft Smooth Streaming beépülő modul a OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [A Microsoft® Smooth Streaming ügyféloldali Porting Kit licencelése](https://aka.ms/sspk) 
* [XBOX video-alkalmazásfejlesztés](https://www.xbox.com/en-US/developers) 

## <a name="advertising"></a>Hirdetések
Azure Media Services támogatja az ad-beszúrást a Windows Media platformon keresztül: Player frameworks. A Windows 8, a Silverlight, a Windows Phone-telefon 8 és az iOS rendszerű eszközök esetében az ad-támogatással rendelkező Player-keretrendszerek érhetők el. Minden egyes Player Framework tartalmaz egy mintakód, amely bemutatja, hogyan implementálhat egy Player-alkalmazást. Az adathordozóra három különböző típusú hirdetést lehet beszúrni:

Lineáris – teljes keretű hirdetések, amelyek szüneteltetik a fő videót

Nem lineáris – a fő videóként megjelenő, általában a lejátszón elhelyezett embléma vagy más statikus rendszerkép

Companion – a lejátszón kívül megjelenített hirdetések

A hirdetéseket a fő videó idővonalának tetszőleges pontjára lehet helyezni. Meg kell adnia a játékosnak, hogy mikor kell lejátszani az ad-t és a lejátszani kívánt hirdetéseket. Ezt szabványos XML-alapú fájlok használatával végezheti el: a video ad szolgáltatás sablonja (nagy méretű), a digitális videó több ad-lista (VMAP), a média absztrakt előkészítési sablon (MAST) és a digitális videolejátszó ad Interface Definition (VPAID). A nagy fájlok meghatározzák, hogy milyen hirdetéseket kell megjeleníteni. A VMAP-fájlok a különböző hirdetések lejátszásának idejét határozzák meg, és nagy XML-kódot tartalmaznak. Az ÁRBOC-fájlok egy másik módja a hirdetések sorozatának, amely nagy XML-t is tartalmazhat. A VPAID-fájlok a videolejátszó és az ad-kiszolgáló közötti felületet határozzák meg. További információ: [hirdetések beszúrása](https://msdn.microsoft.com/library/dn387398.aspx).

A kódolt feliratozással és a hirdetések élő közvetítéssel való támogatásával kapcsolatos információkért lásd: [támogatott zárt feliratok és ad-beszúrási szabványok](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[MPEG-DASH adaptív streamelt videók beágyazása DASH.js-sel rendelkező HTML5-alkalmazásba](media-services-embed-mpeg-dash-in-html5.md)

[GitHub Dash. js-adattár](https://github.com/Dash-Industry-Forum/dash.js)

