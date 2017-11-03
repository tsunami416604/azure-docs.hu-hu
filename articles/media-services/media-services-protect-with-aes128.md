---
title: "AES-128 dinamikus titkosítás és a kulcs kézbesítési szolgáltatás használatával |} Microsoft Docs"
description: "A Microsoft Azure Media Services lehetővé teszi, hogy a 128 bites AES titkosítási kulccsal titkosított tartalom. Media Services is biztosít a kulcs kézbesítési szolgáltatás letölti a titkosítási kulcsok engedéllyel rendelkező felhasználók számára. Ez a témakör bemutatja, hogyan dinamikusan titkosítani az AES-128, és a kulcs kézbesítési szolgáltatás használata."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: a441e76fae0bda829cb112d307b3b436809b9c9b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>AES-128 dinamikus titkosítás és a kulcs kézbesítési szolgáltatás használatával
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Áttekintés
> [!NOTE]
> Lásd: [ez](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption) videó megtudhatja, hogyan védi meg a média tartalom AES titkosítással.
> 
> 

A Microsoft Azure Media Services lehetővé teszi, hogy Http-Live-Streaming (HLS), és zökkenőmentes adatfolyamok titkosítva az Advanced Encryption Standard (AES) (a 128 bites titkosítási kulcsok használatával). Media Services is biztosít a kulcs kézbesítési szolgáltatás letölti a titkosítási kulcsok engedéllyel rendelkező felhasználók számára. Ha azt szeretné, a Media Services az objektum titkosítására, meg kell rendelje hozzá egy titkosítási kulcsot az eszköz és engedélyezési házirendeket, a kulcs is konfigurálhatja. Ha olyan adatfolyamot kell megadni a Windows Media Player van szükség, a Media Services megadott kulcsot használja az dinamikusan titkosítani az AES titkosítással. Az adatfolyam visszafejtése, a Windows Media player a kulcs kézbesítési szolgáltatás fog igényelni a kulcsot. Döntse el, hogy a felhasználó jogosult-e a kulcs eléréséhez, hogy a szolgáltatás értékeli az engedélyezési házirendeket, amelyek a kulcshoz megadott.

A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. A tartalomkulcs-hitelesítési szabályzat egy vagy több hitelesítési korlátozást tartalmazhat: ezek lehetnek nyitott vagy jogkivonat-korlátozások. A tokennel korlátozott szabályzatokhoz a Secure Token Service (Biztonsági jegykiadó szolgáltatás, STS) által kiadott tokennek kell tartoznia. A Media Services a [Simple Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) és a [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) formátumú tokeneket támogatja. További információkért lásd: [a tartalomkulcs hitelesítési szabályzatának konfigurálása](media-services-protect-with-aes128.md#configure_key_auth_policy).

A dinamikus titkosítás által nyújtott előnyök kihasználásához többszörös sávszélességű MP4-fájlokat vagy Smooth Streaming-forrásfájlokat tartalmazó objektummal kell rendelkeznie. Azt is konfigurálnia kell az eszköz (lásd a témakör későbbi részében) továbbítási szabályzatát. Ezt követően az igényalapú streamelési kiszolgáló a streamelési URL-címben megadott formátumnak megfelelően gondoskodik arról, hogy a rendszer a kiválasztott protokollal továbbítsa a streamet. Így elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services szolgáltatás elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ.

Ez a témakör hasznos lehet a fejlesztők számára, amely védett médiafájlok továbbításával foglalkoznak. A témakörben elsajátíthatja, hogy a kulcs kézbesítési szolgáltatás konfigurálása engedélyezési házirendeket, hogy csak az arra jogosult ügyfelek kaphassák meg a titkosítási kulcsokat. Azt is bemutatja, hogyan dinamikus titkosítás használatához.


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES-128, a dinamikus titkosítás és a kulcs kézbesítési szolgáltatás munkafolyamat

A következőkben általános lépéseket kell végrehajtani, ha AES, a Media Services kulcs kézbesítési szolgáltatás segítségével, és a dinamikus titkosítás használata az eszközök titkosításához.

1. [Hozzon létre egy eszközt, majd fájlok feltöltése az objektumba](media-services-protect-with-aes128.md#create_asset).
2. [A fájl az adaptív sávszélességű MP4-készletet tartalmazó objektum kódolása](media-services-protect-with-aes128.md#encode_asset).
3. [Hozzon létre egy tartalomkulcsot, majd társítsa a kódolt objektumhoz](media-services-protect-with-aes128.md#create_contentkey). A Media Services szolgáltatásban a tartalomkulcs tartalmazza az objektum titkosítási kulcsát.
4. [A tartalomkulcs hitelesítési szabályzatának konfigurálása](media-services-protect-with-aes128.md#configure_key_auth_policy). Ahhoz, hogy az ügyfél megkaphassa a tartalomkulcsot, Önnek be kell állítania a tartalomkulcs-hitelesítési szabályzatot, amelynek az ügyfélnek meg kell felelnie.
5. [Konfigurálja az az objektum továbbítási szabályzatát](media-services-protect-with-aes128.md#configure_asset_delivery_policy). A továbbítási szabályzat konfigurációjához tartalmazza: licenckérési URL-cím és-inicializálási vektor (IV) (az AES-128 szükséges adni, ha titkosítása és visszafejtése azonos IV), objektumtovábbítási protokoll (például MPEG DASH, HLS, Smooth Streaming vagy az összes), a a dinamikus titkosítás (például a boríték vagy a dinamikus titkosítás nélkül).

    Az adott objektum különböző protokolljaira akár eltérő szabályzatokat is alkalmazhat. Beállíthatja például, hogy a PlayReady-titkosítás csak a Smooth/DASH-re vonatkozzon, az AES Envelope pedig csak a HLS-re. A továbbítási szabályzatban meg nem határozott protokollok streameléshez való használatát a rendszer nem engedélyezi (ilyen lehet például, ha csupán egyetlen szabályzatot állít be, amely kizárólag a HLS-protokoll használatát tartalmazza). Kivételt jelent, ha egyáltalán nem állít be objektumtovábbítási szabályzatot. Ebben az esetben a rendszer az összes protokollt engedélyezi.

6. [Hozzon létre egy OnDemand-kereső](media-services-protect-with-aes128.md#create_locator) egy adatfolyam-továbbítási URL-cím beszerzése érdekében.

Ez a témakör azt is bemutatja [hogyan ügyfélalkalmazás is kérhet egy kulcsot a fő kézbesítési szolgáltatás](media-services-protect-with-aes128.md#client_request).

A teljes .NET található [példa](media-services-protect-with-aes128.md#example) a témakör végén.

Az alábbi képen a fentiekben leírt munkafolyamatot láthatja. Itt a tokenes hitelesítést használtuk.

![Védelem 128 bites AES-titkosítással](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

A témakör további részében részletes magyarázatokat, kódmintákat és olyan témakörökre mutató hivatkozásokat talál, amelyek segítenek elérni a fent leírt célokat.

## <a name="current-limitations"></a>Aktuális korlátozások
Objektumtovábbítási szabályzat hozzáadásakor vagy módosításakor törölnie kell az ahhoz tartozó meglévő lokátort (ha van), majd létre kell hoznia egy új lokátort.

## <a id="create_asset"></a>Hozzon létre egy eszközt, majd fájlok feltöltése az objektumba
A videók kezeléséhez, kódolásához és streameléséhez először fel kell töltenie tartalmait a Microsoft Azure Media Services szolgáltatásba. A feltöltést követően tartalmai a biztonságos felhőtárhelyre kerülnek további feldolgozás és streamelés céljából. 

További információk: [Upload Files into a Media Services account](media-services-dotnet-upload-files.md) (Fájlok feltöltése a Media Services-fiókba).

## <a id="encode_asset"></a>Az adaptív sávszélességű MP4 típusú beállításkészlettel fájlt tartalmazó objektum kódolása
A dinamikus titkosítás segítségével mindössze egy többszörös sávszélességű MP4-fájlokat vagy Smooth Streaming-forrásfájlokat tartalmazó objektumot kell létrehoznia. Ezt követően a jegyzék vagy töredék kérelem, az Igényalapú Streamelési megadott formátumnak megfelelően kiszolgáló biztosítja az adatfolyam kapni a kiválasztott protokollal. Így elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services szolgáltatás elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ. További információkért lásd a [Dynamic Packaging Overview](media-services-dynamic-packaging-overview.md) (A dinamikus becsomagolás áttekintése) című témakört.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
>
>Is hogy fogja tudni használni a dinamikus csomagolás és a dinamikus titkosítás az objektumot kell foglal magában adaptív sávszélességű MP4 vagy Smooth Streaming-fájlsorozattá.

A kódolással kapcsolatos utasításokért lásd: [How to encode an asset using Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) (Objektum kódolása a Media Encoder Standard használatával).

## <a id="create_contentkey"></a>Tartalomkulcs létrehozása és társítása a kódolt objektumhoz
A Media Services szolgáltatásban a tartalomkulcs tartalmazza az objektum titkosítására használható kulcsot.

További információk: [Create content key](media-services-dotnet-create-contentkey.md) (Tartalomkulcs létrehozása).

## <a id="configure_key_auth_policy"></a>A tartalomkulcs engedélyezési házirendjének konfigurálása
A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. Ahhoz, hogy az ügyfél (a lejátszó) megkaphassa a kulcsot, Önnek be kell állítania a tartalomkulcs-hitelesítési szabályzatot, amelynek az ügyfélnek meg kell felelnie. A tartalomkulcs-hitelesítési házirend rendelkezhet egy vagy több engedélyezési korlátozás: Nyissa meg a, lexikális elem: korlátozás vagy IP-korlátozás.

További információk: [A tartalomkulcs hitelesítési szabályzatának létrehozása](media-services-dotnet-configure-content-key-auth-policy.md).

## <a id="configure_asset_delivery_policy"></a>Objektumtovábbítási szabályzat konfigurálása
Konfigurálja az objektum továbbítási szabályzatát. Az objektumtovábbítási szabályzat konfigurálásához többek között az alábbiak tartoznak:

* A kulcs licenckérési URL-cím. 
* Az inicializálási vektor (IV) a boríték titkosítási használatára. AES-128 adni, ha titkosítása és visszafejtése azonos IV igényel. 
* Az adategység-továbbítási protokoll (pl. MPEG DASH, HLS, Smooth Streaming vagy ezek mindegyike).
* A dinamikus titkosítás (például AES envelope) típusú vagy a dinamikus titkosítás nélkül. 

További információk: [Objektumtovábbítási szabályzat konfigurálása ](media-services-rest-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>OnDemand-lokátor létrehozása a streamelési URL-cím lekérése érdekében
A felhasználók rendelkezésére kell bocsátania a Smooth, DASH vagy HLS streamelési URL-címét.

> [!NOTE]
> Objektumtovábbítási szabályzat hozzáadásakor vagy módosításakor törölnie kell az ahhoz tartozó meglévő lokátort (ha van), majd létre kell hoznia egy új lokátort.
> 
> 

További információk az objektumok közzétételéről és a streamelési URL-cím létrehozásáról: [Build a streaming URL](media-services-deliver-streaming-content.md) (Streamelési URL-cím létrehozása).

## <a name="get-a-test-token"></a>Tesztjogkivonat lekérése
Kérje le a kulcshitelesítési szabályzatban használt jogkivonat-korlátozásoknak megfelelő tesztjogkivonatot.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

A stream kipróbálásához használja az [AMS-lejátszót](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a id="client_request"></a>Hogyan is az ügyfél kérhet egy kulcsot a fő kézbesítési szolgáltatás?
Az előző lépésben összeállított jegyzékfájlt mutató URL-CÍMÉT. Az ügyfél a szükséges információk kinyerése adatfolyam fájlok ahhoz, hogy a kulcs kézbesítési szolgáltatás indítson egy lekérdezést kell.

### <a name="manifest-files"></a>Fájlok
Az ügyfélnek kell az URL (tartalmazó is tartalomkulcsot azonosítója (kid)) bontsa ki a jegyzékfájl közötti értéket. Az ügyfél a titkosítási kulcs beszerzése a kulcs kézbesítési szolgáltatás majd megpróbálja. Az ügyfél kell bontsa ki a IV érték és az visszafejteni az adatfolyam használata. Az alábbi kódrészletben látható a <Protection> a Smooth Streaming jegyzékfájl elemet.

    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>

HLS, ha a legfelső szintű jegyzékfájl lebontva szegmens fájlokat. 

Például a legfelső szintű jegyzékfájl van: http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl), és a szegmens fájlnevek listáját tartalmazza.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Ha egy szegmens fájlt (például http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), it should contain szövegszerkesztőben megnyitása #EXT X-kulcs, amely azt jelzi, hogy a fájl titkosítva van.

    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST

>[!NOTE] 
>Ha azt tervezi, számára, hogy az AES titkosított HLS a Safari című [ebben a blogban](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>A kulcs kér a kulcs kézbesítési szolgáltatás

A következő kód bemutatja, hogyan kérelmet küld a Media Services kulcs kézbesítési szolgáltatás egy kulcs kézbesítés URI-azonosítóhoz (a jegyzékfájl kinyert) használata, valamint a jogkivonatot (a jelen témakör nem konzultáljon a Secure Token Service Simple Web Tokens tudhat).

    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }

## <a name="protect-your-content-with-aes-128-using-net"></a>Az AES-128 tartalomvédelemre .NET használatával

### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

1. Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint. 
2. Adja hozzá a következő elemeket az app.config fájlban megadott **appSettings** szakaszhoz:

        <add key="Issuer" value="http://testacs.com"/>
        <add key="Audience" value="urn:test"/>

### <a id="example"></a>Példa

Írja felül a Program.cs fájlban található kódot az itt látható kóddal.
 
>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információ [ebben](media-services-dotnet-manage-entities.md#limit-access-policies) a témakörben érhető el.

Módosítsa úgy a változókat, hogy a bemeneti fájlok tárolásához Ön által használt mappákra mutassanak.

[!code-csharp[Main](../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

