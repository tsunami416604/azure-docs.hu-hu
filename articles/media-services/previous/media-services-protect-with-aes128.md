---
title: AES-128, a dinamikus titkosítás és a kulcstovábbítást |} A Microsoft Docs
description: Tartalomkézbesítés 128 bites AES titkosítási kulcsokkal titkosítja a Microsoft Azure Media Services használatával. A Media Services emellett a legfontosabb licenctovábbítási szolgáltatása, amely a titkosítási kulcsokat biztosít a jogosult felhasználókra. Ez a témakör bemutatja, hogyan dinamikusan titkosítani az AES-128, és a kulcstovábbítást használata.
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
ms.openlocfilehash: 6f76d6aed8dc5eed3dbf673b265c404f27b0536d
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526826"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>AES-128, a dinamikus titkosítás és a kulcstovábbítást használata
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Próbálja ki a legújabb verziót, ami a [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még [v3 a v2 migrálási útmutató](../latest/migrate-from-v2-to-v3.md)

A Media Services segítségével HTTP Live Streaming (HLS), és a Smooth Streaming az AES-128 bites titkosítási kulcsok használatával titkosítja. A Media Services emellett a legfontosabb licenctovábbítási szolgáltatása, amely a titkosítási kulcsokat biztosít a jogosult felhasználókra. Ha azt szeretné, hogy a Media Services az objektum titkosítására, rendelje hozzá egy titkosítási kulcsot az eszköz, és a kulcs engedélyezési házirendek is konfigurálhatja. Adatfolyam-lejátszó kér, amikor a Media Services a megadott kulcs használatával dinamikusan titkosítani az AES-titkosítás segítségével. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. Annak megállapításához, hogy a felhasználó jogosult kulcs lekérése, a szolgáltatás kiértékeli az engedélyezési házirendeket, amelyek a kulcshoz megadott.

A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. A tartalomkulcs-hitelesítési szabályzat egy vagy több hitelesítési korlátozást tartalmazhat: ezek lehetnek nyitott vagy jogkivonat-korlátozások is. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. A Media Services a [Simple Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) és a [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) formátumú jogkivonatokat támogatja. További információkért tekintse át [a tartalomkulcs hitelesítési szabályzatának konfigurálásával](media-services-protect-with-aes128.md#configure_key_auth_policy) foglalkozó témakört.

A dinamikus titkosítás által nyújtott előnyök kihasználásához többszörös sávszélességű MP4-fájlokat vagy Smooth Streaming-forrásfájlokat tartalmazó objektummal kell rendelkeznie. Azt is konfigurálnia kell (lásd a cikk későbbi részében) az objektum továbbítási szabályzatát. Ezt követően az igényalapú streamelési kiszolgáló a streamelési URL-címben megadott formátumnak megfelelően gondoskodik arról, hogy a rendszer a kiválasztott protokollal továbbítsa a streamet. Ennek eredményeképpen kell tárolni, és csak azért kell fizetnie a fájlokat egyetlen tárolási formátumban. A Media Services összeállítja és kiszolgálja az ügyféltől érkező kéréseknek megfelelő választ.

Ez a cikk a védett médiatartalom-alkalmazások dolgozó fejlesztők számára hasznos. A cikk bemutatja, hogyan konfigurálhatja a kulcstovábbítást-engedélyezési szabályzatokat úgy, hogy csak jogosult ügyfelek kaphassák meg a titkosítási kulcsokat. Azt is bemutatja, hogyan dinamikus titkosítást használ.

Az az Advanced Encryption Standard (AES) Safari macOS rendszeren történő továbbítását tartalom titkosítása a további információkért lásd: [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
AES-titkosítás a médiatartalmak védelme áttekintését lásd: [ebben a videóban](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES-128, a dinamikus titkosítás és kulcskézbesítési szolgáltatás munkafolyamata

A Media Services kulcstovábbítást használatával és a dinamikus titkosítás segítségével AES az eszköz titkosításakor, hajtsa végre az alábbi általános lépéseket:

1. [Hozzon létre egy objektumot, és töltse fel a fájlokat az objektumba](media-services-protect-with-aes128.md#create_asset).

2. [Az adaptív sávszélességű MP4-set a fájlt tartalmazó objektum kódolása](media-services-protect-with-aes128.md#encode_asset).

3. [Hozzon létre egy tartalomkulcsot, és társítsa a kódolt objektumhoz](media-services-protect-with-aes128.md#create_contentkey). A Media Services szolgáltatásban a tartalomkulcs tartalmazza az objektum titkosítási kulcsát.

4. [A tartalomkulcs hitelesítési szabályzatának konfigurálása](media-services-protect-with-aes128.md#configure_key_auth_policy). Konfigurálnia kell a tartalomkulcs hitelesítési szabályzatát. Az ügyfélnek meg kell felelnie a szabályzatnak, mielőtt megkapná a tartalomkulcsot.

5. [Konfigurálja az adategység továbbítási szabályzatát](media-services-protect-with-aes128.md#configure_asset_delivery_policy). A továbbítási szabályzat konfigurációjához a fő licenckérési URL-cím és a egy inicializáló vektort (IV) tartalmaz. (AES-128 igényel az azonos IV titkosításra és visszafejtésre.) A konfiguráció is tartalmaz a továbbítási protokoll (például MPEG-DASH, HLS, Smooth Streaming vagy az összes) és a dinamikus titkosítás (például a boríték vagy a dinamikus titkosítás nélkül).

    Egy adott objektum különböző protokolljaira eltérő szabályzatokat is alkalmazhat. Beállíthatja például, hogy a PlayReady-titkosítás csak a Smooth/DASH-re vonatkozzon, az AES Envelope pedig csak a HLS-re. A továbbítási szabályzatban meg nem határozott protokollok streameléshez való le lesznek tiltva. (Ilyen például, ha egyetlen szabályzat hozzáadása, amely kizárólag a HLS protokollként.) Kivételt jelent, ha egyáltalán nem állít be objektumtovábbítási szabályzatot. Ebben az esetben a rendszer az összes protokollt engedélyezi.

6. [Hozzon létre egy OnDemand-lokátort](media-services-protect-with-aes128.md#create_locator) egy streamelési URL-Címének lekéréséhez.

A cikk azt is bemutatja [hogyan ügyfélalkalmazás tud kulcsot kérni a kulcskézbesítési szolgáltatásból](media-services-protect-with-aes128.md#client_request).

Annak teljes [.NET típusú példát](media-services-protect-with-aes128.md#example) a cikk végén található.

Az alábbi képen az előzőekben leírt munkafolyamatot láthatja. Itt jogkivonatos hitelesítést használtuk.

![Védelem 128 bites AES-titkosítással](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Ez a cikk további részében magyarázatokat, kódmintákat és mutató hivatkozásokat talál, amelyek bemutatják a korábban leírt biztosít.

## <a name="current-limitations"></a>Aktuális korlátozások
Az objektumhoz tartozó továbbítási szabályzat hozzáadásakor vagy módosításakor törölnie kell minden meglévő lokátort, majd létre kell hoznia egy újat.

## <a id="create_asset"></a>Hozzon létre egy objektumot, és töltse fel a fájlokat az objektumba
A videók kezeléséhez, kódolásához és streameléséhez először fel kell töltenie tartalmait a Media Services szolgáltatásba. A feltöltést követően tartalmai a biztonságos felhőtárhelyre kerülnek további feldolgozás és streamelés céljából. 

További információkért tekintse át a [fájlok Media Services-fiókba történő feltöltésével](media-services-dotnet-upload-files.md) foglalkozó témakört.

## <a id="encode_asset"></a>Az adaptív sávszélességű MP4 típusú beállításkészlettel a fájlt tartalmazó objektum kódolása
A dinamikus titkosítás segítségével egy többszörös sávszélességű MP4-fájlokat vagy Smooth Streaming-forrásfájlokat tartalmazó objektumot hoz létre. Ezt követően a jegyzék vagy töredékrész kérelemben megadott formátumnak megfelelően, az igényalapú streamelési kiszolgáló biztosítja, hogy Önnek a streamet a kiválasztott protokollal. Ezt követően csak kell tárolni, és a fájlokat egyetlen tárolási formátumban kell fizetnie. A Media Services összeállítja és kiszolgálja az ügyféltől érkező kéréseknek megfelelő választ. További információkért olvassa át [a dinamikus becsomagolást áttekintő](media-services-dynamic-packaging-overview.md) témakört.

>[!NOTE]
>A Media Services-fiók létrehozásakor a rendszer hozzáad egy alapértelmezett streamvégpontot a fiókhoz Leállítva állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak Fut állapotban kell lennie. 
>
>Is szeretné használni a dinamikus csomagolás és a dinamikus titkosítás, az objektumnak tartalmaznia kell egy adaptív sávszélességű MP4 vagy Smooth Streaming-fájlsorozattá készletét.

A kódolással kapcsolatos utasításokért lásd: [Objektum kódolása a Media Encoder Standard használatával](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Tartalomkulcs létrehozása és társítása a kódolt objektumhoz
A Media Services szolgáltatásban a tartalomkulcs tartalmazza az objektum titkosítására használható kulcsot.

További információkat a [tartalomkulcs létrehozásával](media-services-dotnet-create-contentkey.md) foglalkozó témakör tartalmaz.

## <a id="configure_key_auth_policy"></a>A tartalomkulcs hitelesítési szabályzatának konfigurálása
A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. Konfigurálnia kell a tartalomkulcs hitelesítési szabályzatát. Az ügyfélnek (lejátszó) meg kell felelnie a szabályzat, a kulcs letöltéséhez az ügyfél előtt. A tartalomkulcs-hitelesítési szabályzat egy vagy több hitelesítési korlátozást, vagy nyissa meg a, jogkivonat-korlátozási vagy IP-korlátozást is rendelkezhet.

További információkért lásd a [tartalomkulcs-hitelesítési szabályzat konfigurálásával](media-services-dotnet-configure-content-key-auth-policy.md) foglalkozó témakört.

## <a id="configure_asset_delivery_policy"></a>Objektumtovábbítási szabályzat konfigurálása
Konfigurálja az objektum továbbítási szabályzatát. Az objektumtovábbítási szabályzat konfigurálásához többek között az alábbiak tartoznak:

* A kulcs licenckérési URL-címe. 
* Az inicializáló vektort (IV) a boríték-titkosítást használja. AES-128 az azonos IV titkosításához és visszafejtéséhez szükséges. 
* Az objektumtovábbítási protokoll (például MPEG-DASH, HLS, Smooth Streaming vagy ezek mindegyike).
* A dinamikus titkosítás (például AES-boríték) típusa vagy a dinamikus titkosítás nélkül. 

További információkat lásd az [objektumtovábbítási szabályzat konfigurálását](media-services-dotnet-configure-asset-delivery-policy.md) ismertető témakörben.

## <a id="create_locator"></a>OnDemand-lokátor létrehozása a streamelési URL-cím lekéréséhez
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

A stream kipróbálásához használja az [Azure Media Services-lejátszót](https://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a id="client_request"></a>Hogyan az ügyfél kérelmezhet egy kulcsot a kulcstovábbítást?
Az előző lépésben kialakítani a jegyzékfájlt mutató URL-CÍMÉT. Az ügyfélnek kell a szükséges információk kinyerése a streamelési jegyzékfájlok használatával indítson egy a kulcstovábbítást.

### <a name="manifest-files"></a>Jegyzékfájlok
Bontsa ki az URL-címet az ügyfélnek kell (tartalom is tartalmazó kulcs azonosítója [gyerek]) értéket a jegyzékfájl. Az ügyfél megpróbálja majd a titkosítási kulcs lekérése a kulcstovábbítást. Az ügyfél is IV értékének kinyerése és felhasználhatják a stream dekódolására kell. A következő kódrészlet azt mutatja be a `<Protection>` elem a Smooth Streaming jegyzékfájl:

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

Esetén a HLS a legfelső szintű jegyzékfájl szegmens fájlok van osztva. 

Például a legfelső szintű jegyzékfájl van: http:\//test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl). Tartalmaz egy szegmens fájlnevek listáját.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Ha megnyitja egy szegmens fájlt egy szövegszerkesztőben (például a http:\//test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), #EXT-X-kulcs, amely azt jelzi, hogy a fájl titkosított tartalmazza.

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
>Ha azt tervezi, az AES-sel titkosított HLS lejátszása a Safariban, [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>A kulcs kérése a kulcstovábbítást

A következő kód bemutatja, hogyan kérést küldhet a Media Services kulcstovábbítást kulcskézbesítési URI-t (a jegyzékfájl kivonták) használatával és a egy token. (Ez a cikk nem azt ismertetik, hogyan SWTs kérhet az STS szolgáltatással.)

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

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Az AES-128 tartalmai védelmét a .NET-keretrendszerrel

### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

1. Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint.

2. Adja hozzá a következő elemek appSettings, az app.config fájlban meghatározott módon:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a id="example"></a>Példa

Írja felül a Program.cs fájlban található kódot az itt látható kóddal.
 
>[!NOTE]
>Nincs a korlát 1 000 000 szabályzat különböző Media Services-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében. Használja az ugyanazon házirend-azonosító, ha mindig ugyanazokat az napokat/hozzáférési engedélyeket. Például szolgálnak erre az olyan keresők szabályzatai, amelyek hosszú ideig érvényben maradnak (nem feltöltött szabályzatok). További információkért tekintse meg a "Korlát adathozzáférési szabályzatok" szakaszában [kezelése az eszközök és a kapcsolódó entitások a Media Services .NET SDK-val](media-services-dotnet-manage-entities.md#limit-access-policies).

Módosítsa úgy a változókat, hogy a bemeneti fájlok tárolásához Ön által használt mappákra mutassanak.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
