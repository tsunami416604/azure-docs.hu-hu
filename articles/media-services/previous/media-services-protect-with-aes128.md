---
title: Használja az AES-128 dinamikus titkosítást és a kulcskézbesítési szolgáltatást | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan dinamikusan titkosíthatja az AES-128-at, és hogyan használhatja a kulcskézbesítési szolgáltatást.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 01153317b49e4543f10faa517bce7bcc01ce22d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269730"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Az AES-128 dinamikus titkosítás és a kulcskézbesítési szolgáltatás használata
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [Php](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

A Media Services segítségével http live streaming (HLS) és smooth streaming titkosítva az AES segítségével 128 bites titkosítási kulcsokat. A Media Services biztosítja azt a kulcskézbesítési szolgáltatást is, amely titkosítási kulcsokat biztosít a jogosult felhasználóknak. Ha azt szeretné, hogy a Media Services titkosítson egy eszközt, társítson egy titkosítási kulcsot az eszközhöz, és konfigurálja a kulcs engedélyezési házirendjeit is. Amikor egy lejátszó adatfolyamot kér, a Media Services a megadott kulccsal dinamikusan titkosítja a tartalmat AES titkosítással. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. Annak megállapításához, hogy a felhasználó jogosult-e a kulcs beakése, a szolgáltatás kiértékeli a kulcshoz megadott engedélyezési házirendeket.

A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. A tartalomkulcs-hitelesítési szabályzat egy vagy több hitelesítési korlátozást tartalmazhat: ezek lehetnek nyitott vagy jogkivonat-korlátozások is. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. A Media Services a [Simple Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) és a [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) formátumú jogkivonatokat támogatja. További információt [a Tartalomkulcs engedélyezési házirendjének konfigurálása](media-services-protect-with-aes128.md#configure_key_auth_policy)című témakörben talál.

A dinamikus titkosítás által nyújtott előnyök kihasználásához többszörös sávszélességű MP4-fájlokat vagy Smooth Streaming-forrásfájlokat tartalmazó objektummal kell rendelkeznie. Az eszköz kézbesítési szabályzatát is konfigurálnia kell (a cikk későbbi részében ismertetett). Ezt követően az igényalapú streamelési kiszolgáló a streamelési URL-címben megadott formátumnak megfelelően gondoskodik arról, hogy a rendszer a kiválasztott protokollal továbbítsa a streamet. Ennek eredményeképpen csak a fájlokat kell tárolnia és fizetnie egyetlen tárolási formátumban. A Media Services összeállítja és kiszolgálja az ügyféltől érkező kéréseknek megfelelő választ.

Ez a cikk azoknak a fejlesztőknek hasznos, akik védett adathordozót tartalmazó alkalmazásokon dolgoznak. A cikk bemutatja, hogyan konfigurálhatja a kulcskézbesítési szolgáltatást engedélyezési házirendekkel, hogy csak a jogosult ügyfelek fogadhassanak titkosítási kulcsokat. Azt is bemutatja, hogyan kell használni a dinamikus titkosítást.

A tartalom titkosításáról az Advanced Encryption Standard (AES) segítségével a MacOS rendszeren történő kézbesítéshez a [blogbejegyzésben talál.](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)
Az AES-titkosítással védett médiatartalmakról a [videóban](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption)olvashat.


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES-128 dinamikus titkosítási és kulcskézbesítési szolgáltatási munkafolyamat

Hajtsa végre a következő általános lépéseket, ha az eszközöket az AES-szel titkosítja a Media Services kulcskézbesítési szolgáltatásával és dinamikus titkosítással:

1. [Hozzon létre egy eszközt, és töltsön fel fájlokat az eszközbe.](media-services-protect-with-aes128.md#create_asset)

2. [Kódolja a fájlt tartalmazó eszközt az adaptív sávszélességű MP4-készletbe.](media-services-protect-with-aes128.md#encode_asset)

3. [Hozzon létre egy tartalomkulcsot, és társítsa a kódolt eszközhöz.](media-services-protect-with-aes128.md#create_contentkey) A Media Services szolgáltatásban a tartalomkulcs tartalmazza az objektum titkosítási kulcsát.

4. [Konfigurálja a tartalomkulcs engedélyezési házirendjét.](media-services-protect-with-aes128.md#configure_key_auth_policy) Konfigurálnia kell a tartalomkulcs hitelesítési szabályzatát. Az ügyfélnek meg kell felelnie a szabályzatnak, mielőtt megkapná a tartalomkulcsot.

5. [Konfigurálja egy eszköz kézbesítési házirendje](media-services-protect-with-aes128.md#configure_asset_delivery_policy). A kézbesítési házirend konfigurációja tartalmazza a kulcsbeszerzés URL-címét és az inicializálási vektort (IV). (Az AES-128 titkosításhoz és visszafejtéshez ugyanazt a iv.) A konfiguráció tartalmazza a kézbesítési protokollt (például MPEG-DASH, HLS, Smooth Streaming vagy az összes) és a dinamikus titkosítás típusát (például boríték vagy nincs dinamikus titkosítás).

    Egy adott objektum különböző protokolljaira eltérő szabályzatokat is alkalmazhat. Beállíthatja például, hogy a PlayReady-titkosítás csak a Smooth/DASH-re vonatkozzon, az AES Envelope pedig csak a HLS-re. A kézbesítési házirendben nem definiált protokollok lejátszása le van tiltva a streamelésből. (Egy példa, ha egyetlen házirendet ad hozzá, amely csak HLS protokollként van megadva.) A kivétel az, ha egyáltalán nincs eszközkézbesítési házirenddefiniálva. Ebben az esetben a rendszer az összes protokollt engedélyezi.

6. [Hozzon létre egy OnDemand lokátort](media-services-protect-with-aes128.md#create_locator) egy streamelési URL-cím lekéréséhez.

A cikk azt is [bemutatja, hogy az ügyfélalkalmazás hogyan kérhet kulcsot a kulcskézbesítési szolgáltatástól.](media-services-protect-with-aes128.md#client_request)

A cikk végén található egy teljes [.NET példa.](media-services-protect-with-aes128.md#example)

Az alábbi képen az előzőekben leírt munkafolyamatot láthatja. Itt jogkivonatos hitelesítést használtuk.

![Védje az AES-128-as](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

A cikk további részében magyarázatokat, kódpéldákat és hivatkozásokat tartalmaz olyan témakörökre, amelyek bemutatják, hogyan érheti el a korábban leírt feladatokat.

## <a name="current-limitations"></a>Aktuális korlátozások
Az objektumhoz tartozó továbbítási szabályzat hozzáadásakor vagy módosításakor törölnie kell minden meglévő lokátort, majd létre kell hoznia egy újat.

## <a name="create-an-asset-and-upload-files-into-the-asset"></a><a id="create_asset"></a>Eszköz létrehozása és fájlok feltöltése az eszközbe
A videók kezeléséhez, kódolásához és streameléséhez először fel kell töltenie tartalmait a Media Services szolgáltatásba. A feltöltést követően tartalmai a biztonságos felhőtárhelyre kerülnek további feldolgozás és streamelés céljából. 

További információkért tekintse át a [fájlok Media Services-fiókba történő feltöltésével](media-services-dotnet-upload-files.md) foglalkozó témakört.

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a><a id="encode_asset"></a>A fájlt tartalmazó objektum kódolása az adaptív sávszélességű, MP4 típusú beállításkészlettel
A dinamikus titkosítás segítségével egy többszörös sávszélességű MP4-fájlokat vagy Smooth Streaming-forrásfájlokat tartalmazó objektumot hoz létre. Ezután a jegyzékfájl- vagy töredékkérelemben megadott formátum alapján az igény szerinti streamelési kiszolgáló biztosítja, hogy a kiválasztott protokollban megkapja az adatfolyamot. Ezután csak egyetlen tárolási formátumban kell tárolnia és fizetnie a fájlokat. A Media Services összeállítja és kiszolgálja az ügyféltől érkező kéréseknek megfelelő választ. További információkért olvassa át [a dinamikus becsomagolást áttekintő](media-services-dynamic-packaging-overview.md) témakört.

>[!NOTE]
>A Media Services-fiók létrehozásakor a rendszer hozzáad egy alapértelmezett streamvégpontot a fiókhoz Leállítva állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak Fut állapotban kell lennie. 
>
>A dinamikus csomagolás és a dinamikus titkosítás használatához az eszköznek adaptív sávszélességű MP4-eket vagy adaptív sávszélességű Smooth Streaming fájlokat kell tartalmaznia.

A kódolással kapcsolatos utasításokért lásd: [Objektum kódolása a Media Encoder Standard használatával](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a name="create-a-content-key-and-associate-it-with-the-encoded-asset"></a><a id="create_contentkey"></a>Tartalomkulcs létrehozása és társítása a kódolt eszközzel
A Media Services szolgáltatásban a tartalomkulcs tartalmazza az objektum titkosítására használható kulcsot.

További információkat a [tartalomkulcs létrehozásával](media-services-dotnet-create-contentkey.md) foglalkozó témakör tartalmaz.

## <a name="configure-the-content-keys-authorization-policy"></a><a id="configure_key_auth_policy"></a>A tartalomkulcs engedélyezési házirendjének konfigurálása
A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. Konfigurálnia kell a tartalomkulcs hitelesítési szabályzatát. Az ügyfélnek (játékosnak) meg kell felelnie a házirendnek, mielőtt a kulcsot kézbesítené az ügyfélnek. A tartalomkulcs-engedélyezési házirend egy vagy több engedélyezési korlátozással rendelkezhet, akár nyitott, jogkivonat-korlátozással vagy IP-korlátozással.

További információkért lásd a [tartalomkulcs-hitelesítési szabályzat konfigurálásával](media-services-dotnet-configure-content-key-auth-policy.md) foglalkozó témakört.

## <a name="configure-an-asset-delivery-policy"></a><a id="configure_asset_delivery_policy"></a>Objektumtovábbítási szabályzat konfigurálása
Konfigurálja az objektum továbbítási szabályzatát. Az objektumtovábbítási szabályzat konfigurálásához többek között az alábbiak tartoznak:

* A kulcsbeszerzés URL-címe. 
* A borítéktitkosításhoz használandó inicializálási vektor (IV). Az AES-128-nak ugyanaz a iv szükséges a titkosításhoz és a visszafejtéshez. 
* Az objektumtovábbítási protokoll (például MPEG-DASH, HLS, Smooth Streaming vagy ezek mindegyike).
* A dinamikus titkosítás típusa (például AES boríték) vagy nincs dinamikus titkosítás. 

További információkat lásd az [objektumtovábbítási szabályzat konfigurálását](media-services-dotnet-configure-asset-delivery-policy.md) ismertető témakörben.

## <a name="create-an-ondemand-streaming-locator-to-get-a-streaming-url"></a><a id="create_locator"></a>OnDemand-lokátor létrehozása a streamelési URL-cím lekéréséhez
A felhasználók rendelkezésére kell bocsátania a Smooth Streaming, a DASH vagy a HLS streamelési URL-címét.

> [!NOTE]
> Az objektumhoz tartozó továbbítási szabályzat hozzáadásakor vagy módosításakor törölnie kell minden meglévő lokátort, majd létre kell hoznia egy újat.
> 
> 

További információk az objektumok közzétételéről és a streamelési URL-cím létrehozásáról: [Build a streaming URL](media-services-deliver-streaming-content.md) (Streamelési URL-cím létrehozása).

## <a name="get-a-test-token"></a>Tesztjogkivonat lekérése
Kérje le a kulcshitelesítési szabályzatban használt jogkivonat-korlátozásoknak megfelelő tesztjogkivonatot.

```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word "Bearer" in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

A stream kipróbálásához használja az [Azure Media Services-lejátszót](https://aka.ms/azuremediaplayer).

## <a name="how-can-your-client-request-a-key-from-the-key-delivery-service"></a><a id="client_request"></a>Hogyan kérhet az ügyfél kulcsot a kulcskézbesítési szolgáltatástól?
Az előző lépésben létreadaz URL-címet, amely egy jegyzékfájlra mutat. Az ügyfélnek ki kell bontania a szükséges információkat a streamelési jegyzékfájlokból, hogy kérést küldjön a kulcskézbesítési szolgáltatáshoz.

### <a name="manifest-files"></a>Jegyzékfájlok
Az ügyfélnek ki kell bontania az URL-címet (amely tartalomkulcs-azonosítót [kid]) is tartalmaz a jegyzékfájlból. Az ügyfél ezután megpróbálja beszerezni a titkosítási kulcsot a kulcskézbesítési szolgáltatástól. Az ügyfélnek ki kell bontania a IV értéket, és használnia kell az adatfolyam visszafejtéséhez. A következő kódrészlet `<Protection>` a Smooth Streaming jegyzékfájl elemét mutatja:

```xml
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
```

A HLS esetében a gyökérjegyzék szegmens fájlokra van felosztva. 

A gyökérjegyzék például a\/következő: http: /test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl). A szegmens fájlnevek listáját tartalmazza.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Ha az egyik szegmensfájlt szövegszerkesztőben nyitja meg\/(például http: /test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), tartalmaz #EXT-X-KEY, ami azt jelzi, hogy a fájl titkosított.

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
>Ha azt tervezi, hogy játszani AES-titkosított HLS safari, lásd [ezt a blogot](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>A kulcs kérése a kulcskézbesítési szolgáltatástól

A következő kód bemutatja, hogyan küldhet egy kérelmet a Media Services-kulcs kézbesítési szolgáltatás egy kulcs kézbesítésuri (amely kilett a jegyzékből) és egy jogkivonat használatával. (Ez a cikk nem magyarázza meg, hogyan lehet swts egy STS.)

```csharp
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
```

## <a name="protect-your-content-with-aes-128-by-using-net"></a>A tartalom védelme az AES-128 használatával a .NET használatával

### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

1. Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint.

2. Adja hozzá a következő elemeket az appBeállításokhoz az app.config fájlban meghatározottak szerint:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a name="example"></a><a id="example"></a>Példa

Írja felül a Program.cs fájlban található kódot az itt látható kóddal.
 
>[!NOTE]
>A különböző Media Services-házirendekhez (például lokátorházirendre vagy ContentKeyAuthorizationPolicy-re) legfeljebb 1 000 000 házirend van. Használja ugyanazt a házirend-azonosítót, ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja. Például szolgálnak erre az olyan keresők szabályzatai, amelyek hosszú ideig érvényben maradnak (nem feltöltött szabályzatok). További információt az [Eszközök és a kapcsolódó entitások kezelése a Media Services .NET SDK szolgáltatással című](media-services-dotnet-manage-entities.md#limit-access-policies)részében, "Hozzáférési házirendek korlátozása" című részében talál.

Módosítsa úgy a változókat, hogy a bemeneti fájlok tárolásához Ön által használt mappákra mutassanak.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
