---
title: Tartalom kézbesítése az ügyfeleknek | Microsoft dokumentumok
description: Ez a témakör áttekintést nyújt arról, hogy mi vesz részt a tartalom Azure Media Services használatával történő kézbesítésében.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: c8d32a6434db0fad18b9fe7c2d6e2117795eb651
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476741"
---
# <a name="deliver-content-to-customers"></a>Tartalom kézbesítése az ügyfeleknek
Amikor streamelési vagy igény szerinti videotartalmat jelenít meg az ügyfeleknek, a cél az, hogy kiváló minőségű videókat biztosítson különböző eszközökre különböző hálózati feltételek mellett.

E cél elérése érdekében a következőket teheti:

* Az adatfolyamot többsávszélességű (adaptív sávszélességű) videoadatfolyamba kódolhatja. Ez gondoskodik a minőségről és a hálózati feltételekről.
* A Microsoft Azure Media Services [dinamikus csomagolásával](media-services-dynamic-packaging-overview.md) dinamikusan újracsomagolhatja az adatfolyamot különböző protokollokba. Ez gondoskodik a különböző eszközökön való streamelésről. A Media Services a következő adaptív sávszélességű streamelési technológiák at támogatja: <br/>
    * **HTTP Live Streaming** (HLS) - add "(format=m3u8-aapl)" elérési út az URL "/Manifest" részéhez, hogy a streaming forráskiszolgáló visszaadja a HLS-tartalmat az **Apple iOS** natív eszközökön történő fogyasztásra (a részleteket lásd [a lokátorokat](#locators) és [url-eket](#URLs)),
    * **MPEG-DASH** - add "(format=mpd-time-csf)" elérési utat az URL "/Manifest" részéhez, hogy a streamelési forráskiszolgáló visszatérjen az MPEG-DASH-hez (a részleteket lásd [a lokátorok és](#locators) [URL-ek ),](#URLs)
    * **Sima Streaming**.

>[!NOTE]
>Amikor az AMS-fiók jön létre egy **alapértelmezett** streamelési végpont ot a fiók **leállított** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

Ez a cikk áttekintést nyújt a fontos tartalomkézbesítési fogalmakról.

Az ismert problémák ellenőrzéséhez olvassa el az Ismert problémák ( [Ismert ) témakört.](media-services-deliver-content-overview.md#known-issues)

## <a name="dynamic-packaging"></a>Dinamikus csomagolás
A Media Services által biztosított dinamikus csomagolással az adaptív sávszélességű MP4- vagy simított streamelésű kódolt tartalmakat a Media Services (MPEG-DASH, HLS, Smooth Streaming) által támogatott streamelési formátumokban is eltudja szállítani anélkül, hogy újra kellene csomagolnia ezekbe a streamelési formátumokba. Javasoljuk, hogy dinamikus csomagolással szállítsa a tartalmat.

A dinamikus csomagolás előnyeinek kihasználásához a mezzanine (forrás) fájlt adaptív sávszélességű MP4-fájlokba vagy adaptív sávszélességű Smooth Streaming fájlokba kell kódolnia.

A dinamikus csomagolással a fájlokat egyetlen tárolási formátumban tárolhatja és fizetheti ki. A Media Services a kérések alapján létrehozza és kiszolgálja a megfelelő választ.

A dinamikus csomagolás szabványos és prémium szintű streamelési végpontokhoz érhető el. 

További információ: [Dinamikus csomagolás](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Szűrők és dinamikus jegyzékek
A Media Services segítségével szűrőket adhat meg az eszközökhöz. Ezek a szűrők olyan kiszolgálóoldali szabályok, amelyek segítenek az ügyfeleknek például lejátszani egy videó egy adott szakaszát, vagy megadni a hang- és videointerpretációk egy részét, amelyet az ügyfél eszköze kezelni tud (az eszközhöz társított összes interpretáció helyett). Ez a szűrés *dinamikus jegyzékeken* keresztül érhető el, amelyek akkor jönnek létre, amikor az ügyfél egy vagy több megadott szűrő alapján videót szeretne streamelni.

További információt a Szűrők és dinamikus jegyzékek című [témakörben talál.](media-services-dynamic-manifest-overview.md)

## <a name="locators"></a><a id="locators"/>Lokátorok
Ahhoz, hogy a felhasználó számára olyan URL-t biztosítson, amely a tartalom streamelésére vagy letöltésére használható, először közzé kell tennie az eszközt egy lokátor létrehozásával. A lokátor egy belépési pontot biztosít az eszközben található fájlok eléréséhez. A Media Services két lokátortípust támogat:

* OnDemandOrigin lokátorok. Ezek médiaadatfolyam-streamelésre (például MPEG-DASH, HLS vagy Smooth Streaming) vagy fájlok fokozatos letöltésére szolgálnak.
* SAS URL-lokátorok. Ezek segítségével médiafájlokat tölthet le a helyi számítógépre.

A *hozzáférési házirend* azon engedélyek (például olvasás, írás és lista) és időtartam meghatározására szolgál, amelyekhez az ügyfél egy adott eszközhöz hozzáfér. Vegye figyelembe, hogy a listaengedély (AccessPermissions.List) nem használható onDemandOrigin lokátor létrehozásához.

A lokátoroknak lejárati dátumuk van. Az Azure Portal 100 évvel a jövőbeli lokátorok lejárati dátumát állítja be.

> [!NOTE]
> Ha az Azure Portalon 2015 márciusa előtt létrehozott lokátorokat, ezek a lokátorok két év elteltével lejárnak.
> 
> 

A lokátor lejárati idejének módosításához használjon [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) vagy [.NET](https://go.microsoft.com/fwlink/?LinkID=533259) API-t. Ne feledje, hogy a SAS-lokátor lejárati idejének módosításával az URL-cím is megváltozik.

A lokátorokat nem úgy tervezték, hogy kezeljék a felhasználónkénti hozzáférés-vezérlést. A Digitális jogkezelés (DRM) megoldásokkal különböző hozzáférési jogokat adhat az egyes felhasználóknak. További információ: [Securing Media](https://msdn.microsoft.com/library/azure/dn282272.aspx).

Ha létrehoz egy lokátort, előfordulhat, hogy 30 másodperces késleltetést okoz a szükséges tárolási és propagálási folyamatok az Azure Storage-ban.

## <a name="adaptive-streaming"></a>Adaptív streamelés
Az adaptív sávszélességű technológiák lehetővé teszik a videolejátszók számára a hálózati feltételek meghatározását és a több bitráta közül való kiválasztást. Ha a hálózati kommunikáció romlik, az ügyfél alacsonyabb bitrátát is választhat, így a lejátszás alacsonyabb videominőséggel folytatódhat. A hálózati feltételek javulásával az ügyfél magasabb bitrátára válthat a jobb videominőség mellett. Az Azure Media Services a következő adaptív sávszélességű technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming és MPEG-DASH.

Ahhoz, hogy a felhasználók a streamelési URL-eket, először létre kell hoznia egy OnDemandOrigin lokátor. A lokátor létrehozása megadja az adatfolyamként streamelni kívánt tartalmat tartalmazó eszköz alapelérési útját. Ahhoz azonban, hogy ezt a tartalmat streamelhesse, tovább kell módosítania ezt az elérési utat. A streamelési jegyzékfájl teljes URL-címének létrehozásához össze kell fűznie a lokátor elérési útját és a jegyzékfájl (filename.ism) fájlnevét. Ezután fűzze hozzá a **/Manifest parancsot,** és szükség esetén megfelelő formátumot a lokátor elérési úthoz.

> [!NOTE]
> A tartalmat TLS-kapcsolaton keresztül is streamelheti. Ehhez győződjön meg arról, hogy a streamelési URL-ek HTTPS-lel kezdődnek. Vegye figyelembe, hogy jelenleg az AMS nem támogatja a TLS-t egyéni tartományokkal.  
> 

Csak akkor streamelhet TLS-en keresztül, ha az a streamelési végpont, amelyből a tartalmat kézbesíti, 2014. Ha a streamelési URL-címek a 201 streaming.mediaservices.windows.net4. A "origin.mediaservices.windows.net" (a régi formátum) tartalmazó streamelési URL-ek nem támogatják a TLS-t. Ha az URL-cím a régi formátumban van, és tls-en keresztül szeretne streamelni, hozzon létre egy új streamelési végpontot. Az új streamelési végponton alapuló URL-címeket használhat a tartalom TLS-en keresztültörténő streameléséhez.

## <a name="streaming-url-formats"></a><a id="URLs"/>Streamelési URL-formátumok

### <a name="mpeg-dash-format"></a>MPEG-DASH formátum
{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HTTP Live Streaming (HLS) V4 formátumban
{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Apple HTTP Live Streaming (HLS) V3 formátum
{streaming endpoint name-media services fiók neve}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Apple HTTP Live Streaming (HLS) formátum csak hangszűrővel
Alapértelmezés szerint a HLS-jegyzékbe foglalnak csak hangsávokat. Ez szükséges a mobilhálózatok Apple Store-hitelesítéséhez. Ebben az esetben, ha egy ügyfél nem rendelkezik elegendő sávszélességgel, vagy 2G-kapcsolaton keresztül csatlakozik, a lejátszás csak hangra vált. Ez segít a tartalomstreamelés pufferelés nélküli megtartásában, de nincs videó. Bizonyos esetekben előfordulhat, hogy a lejátszó pufferelése előnyben részesítendő a csak hanggal szemben. Ha el szeretné távolítani a csak hangsávot, adjon csak **hang=hamis értéket** az URL-címhez.

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

További információ: [Dynamic Manifest Composition support and HLS output additional features](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Sima streamelési formátum
{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

Példa:

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a name="smooth-streaming-20-manifest-legacy-manifest"></a><a id="fmp4_v20"></a>Sima streamelési 2.0-s jegyzékfájl (örökölt jegyzékfájl)
Alapértelmezés szerint a Smooth Streaming jegyzékfájl formátuma tartalmazza az ismétlődő címkét (r-tag). Egyes játékosok azonban nem támogatják az r-tag. Az ilyen lejátszóval rendelkező ügyfelek olyan formátumot használhatnak, amely letiltja az r-címkét:

{streaming endpoint name-media services fiók neve}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Progresszív letöltés
A progresszív letöltéssel a teljes fájl letöltése előtt elkezdheti lejátszani a médiafájlokat. Nem lehet fokozatosan letölteni .ism* (ismv, isma, ismt, vagy ismc) fájlokat.

A tartalom fokozatos letöltéséhez használja az OnDemandOrigin típusú lokátort. A következő példa az OnDemandOrigin lokátor típusán alapuló URL-címet mutatja be:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Vissza kell fejtenie minden olyan tárolótitkosítással titkosított eszközt, amelyet az eredeti szolgáltatásból szeretne streamelni a fokozatos letöltéshez.

## <a name="download"></a>Letöltés
A tartalom ügyféleszközre való letöltéséhez létre kell hoznia egy SAS-lokátort. A SAS-lokátor hozzáférést biztosít az Azure Storage-tárolóhoz, ahol a fájl található. A letöltési URL létrehozásához be kell ágyaznia a fájlnevet az állomás és a SAS-aláírás közé.

A következő példa a SAS-lokátoron alapuló URL-címet mutatja be:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

A következő szempontokat kell figyelembe venni:

* Vissza kell fejtenie minden olyan tárolótitkosítással titkosított eszközt, amelyet az eredeti szolgáltatásból szeretne streamelni a fokozatos letöltéshez.
* A 12 órán belül be nem fejezett letöltés sikertelen lesz.

## <a name="streaming-endpoints"></a>Streamvégpontok

A streamelési végpont egy streamelési szolgáltatást jelöl, amely közvetlenül egy ügyféllejátszó alkalmazásnak vagy egy tartalomkézbesítési hálózatnak (CDN) képes tartalmat továbbítani további terjesztés céljából. A streamelési végpontszolgáltatás kimenő adatfolyama lehet egy élő adatfolyam vagy egy igény szerinti videóeszköz a Media Services-fiókban. Kétféle streamelési végpontok, **standard** és **prémium.** További információk: [Streamvégpontok áttekintése](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>Amikor az AMS-fiók jön létre egy **alapértelmezett** streamelési végpont ot a fiók **leállított** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

## <a name="known-issues"></a>Ismert problémák
### <a name="changes-to-smooth-streaming-manifest-version"></a>A Smooth Streaming jegyzékfájl verziójának módosításai
2016 júliusi szolgáltatáskiadás előtt – amikor a Media Encoder Standard, a Media Encoder Premium Workflow vagy a korábbi Azure Media Encoder által készített eszközöket dinamikus csomagolással streamelték – a visszaadott Smooth Streaming jegyzékak a 2.0-s verziónak felel meg. A 2.0-s verzióban a töredék időtartama nem használja az úgynevezett ismétlési ('r') címkéket. Példa:


    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

a 2016 júliusi szolgáltatáskiadásban a létrehozott Smooth Streaming jegyzék a 2.2-es verziónak felel meg, és a töredék időtartama ismétlődő címkéket használ. Példa:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Előfordulhat, hogy az örökölt Smooth Streaming-ügyfelek némelyike nem támogatja az ismétlődő címkéket, és nem fogja betölteni a jegyzékfájlt. A probléma enyhítése érdekében használhatja az örökölt jegyzékfájl formátum paraméter **(format=fmp4-v20)** vagy frissítse az ügyfél a legújabb verzióra, amely támogatja az ismétlődő címkéket. További információ: [Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Kapcsolódó témakörök
[Media Services-lokátorok frissítése működés közbeni tárolókulcsok után](media-services-roll-storage-access-keys.md)

