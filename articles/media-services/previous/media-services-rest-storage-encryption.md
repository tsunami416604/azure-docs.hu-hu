---
title: A tartalom titkosítása tárolótitkosítással az AMS REST API használatával
description: Ismerje meg, hogyan titkosíthatja a tartalmat tárolótitkosítással az AMS REST API-k használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 2a5ef1837375cc395a871f9a9860fa8bde572a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773602"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>A tartalom titkosítása tárolótitkosítással 

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).   > A Media Services v2-höz nem ad nak hozzá új szolgáltatásokat vagy funkciókat. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)
>   

Erősen ajánlott a tartalom helyi titkosítása AES-256 bites titkosítással, majd töltse fel az Azure Storage-ba, ahol tárolja titkosítva.

Ez a cikk áttekintést nyújt az AMS-tárolótitkosításról, és bemutatja, hogyan töltheti fel a titkosított tárolótartalmat:

* Hozzon létre egy tartalomkulcsot.
* Hozzon létre egy eszközt. Állítsa az AssetCreationOption beállítást StorageEncryption értékre az eszköz létrehozásakor.
  
     A titkosított eszközök tartalomkulcsokhoz vannak társítva.
* A tartalomkulcs csatolása az eszközhöz.  
* Állítsa be a titkosítással kapcsolatos paramétereket az AssetFile entitásokon.

## <a name="considerations"></a>Megfontolandó szempontok 

Ha egy tároló titkosított eszközt szeretne kézbesíteni, konfigurálnia kell az eszköz kézbesítési szabályzatát. Az eszköz streamelése előtt a streamelési kiszolgáló eltávolítja a tárolási titkosítást, és a megadott kézbesítési szabályzat használatával streameli a tartalmat. További információt az [Eszközkézbesítési házirendek konfigurálása című témakörben talál.](media-services-rest-configure-asset-delivery-policy.md)

Amikor a Media Services entitásait éri el, meghatározott fejlécmezőket és értékeket kell beállítania a HTTP-kérelmekben. További információt a [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md)című témakörben talál. 

### <a name="storage-side-encryption"></a>Tárolási oldali titkosítás

|Titkosítási beállítás|Leírás|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services storage titkosítása|AES-256 titkosítás, a Media Services által kezelt kulcs|Támogatott<sup>(1)</sup>|Nem támogatott<sup>(2)</sup>|
|[Tárolószolgáltatás titkosítása inaktív adatokhoz](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Az Azure Storage által kínált kiszolgálóoldali titkosítás, az Azure vagy az ügyfél által kezelt kulcs|Támogatott|Támogatott|
|[Tároló ügyféloldali titkosítása](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Az Azure storage által kínált ügyféloldali titkosítás, amelyet az ügyfél kezel a Key Vaultban|Nem támogatott|Nem támogatott|

<sup>1</sup> Bár a Media Services támogatja a tartalom tiszta/titkosítás nélküli kezelését, ez nem ajánlott.

<sup>2</sup> A Media Services v3-as rendszerben a tárolótitkosítás (AES-256 titkosítás) csak akkor támogatott visszamenőleges kompatibilitás esetén, ha az eszközök a Media Services v2-vel jöttek létre. Jelentés: v3 működik a meglévő tároló titkosított eszközök, de nem teszi lehetővé az újak létrehozását.

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-hoz való csatlakozásról az [Access the Azure Media Services API azure AD-hitelesítéssel című témakörben](media-services-use-aad-auth-to-access-ams-api.md)talál további információt. 

## <a name="storage-encryption-overview"></a>Tárolási titkosítás – áttekintés
Az AMS-tároló titkosítása a teljes fájlra alkalmazza az **AES-CTR** módtitkosítást.  Az AES-CTR mód egy blokktitkosítás, amely tetszőleges hosszúságú adatokat képes titkosítani anélkül, hogy kikellene tömíteni. Úgy működik, hogy titkosítja a számláló blokk az AES algoritmus, majd XOR-ing kimenetét AES az adatokat, hogy titkosítsa vagy visszafejti.  A használt számlálóblokk úgy épül fel, hogy az InitializationVector értékét a számláló értékének 0-7 bájtjára másolja, és a számláló 8–15 bájtja nullára van állítva. A 16 bájtos számlálóblokk ból a 8–15 bájtot (azaz a legkevésbé jelentős bájtot) egyszerű, 64 bites, előíratlan egész számként használják, amely minden további feldolgozott adatblokkhoz egy-egy nő, és hálózati bájtsorrendben marad. Ha ez az egész szám eléri a maximális értéket (0xFFFFFFFFFFFFFFFFFF), akkor a növelés nullára állítja vissza a blokkszámlálót (8–15 bájt), anélkül, hogy befolyásolná a számláló többi 64 bitjét (azaz a 0-tól 7-ig).   Az AES-CTR mód ú titkosítás biztonságának fenntartása érdekében az initializationVector értéknek minden egyes tartalomkulcshoz egyedinek kell lennie minden egyes fájlesetében, és a fájloknak 2^64 blokknál rövidebbnek kell lenniük.  Ez az egyedi érték annak biztosítására szolgál, hogy a számláló értéke soha ne legyen újra felhasználva egy adott kulccsal. A CTR móddal kapcsolatos további információkért tekintse meg ezt a [wikioldalt](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (a wikicikk az "InitializationVector" helyett a "Nonce" kifejezést használja).

**A Storage Encryption** használatával titkosíthatja a tiszta tartalmat helyileg AES-256 bites titkosítással, majd töltse fel az Azure Storage-ba, ahol az inaktív titkosítást tárolja. A tárolótitkosítással védett eszközök automatikusan titkosítatlanok lesznek, és a kódolás előtt egy titkosított fájlrendszerbe kerülnek, és opcionálisan újra titkosítva lesznek, mielőtt új kimeneti eszközként újra feltöltenék őket. A tárolótitkosítás elsődleges használati esete az, ha a kiváló minőségű bemeneti médiafájlokat erős titkosítással szeretné biztosítani a lemezen.

A tárolóval titkosított eszköz biztosításához konfigurálnia kell az eszköz kézbesítési szabályzatát, hogy a Media Services tudja, hogyan szeretné kézbesíteni a tartalmat. Az eszköz streamelése előtt a streamelési kiszolgáló eltávolítja a tárolótitkosítást, és a megadott kézbesítési házirend (például AES, közös titkosítás vagy titkosítás nélkül) segítségével streameli a tartalmat.

## <a name="create-contentkeys-used-for-encryption"></a>Titkosításhoz használt contentkeys létrehozása
A titkosított eszközök a storage-titkosítási kulcsokhoz vannak társítva. Hozza létre a titkosításhoz használandó tartalomkulcsot az eszközfájlok létrehozása előtt. Ez a szakasz a tartalomkulcs létrehozását ismerteti.

Az alábbiakban a titkosítani kívánt eszközökhöz társítani kívánt tartalomkulcsok létrehozásának általános lépéseit tetszetős lépések találhatók. 

1. A tárolótitkosításhoz véletlenszerűen hozzon létre egy 32 bájtos AES-kulcsot. 
   
    A 32 bájtos AES-kulcs az eszköz tartalomkulcsa, ami azt jelenti, hogy az adott eszközhöz társított összes fájlnak ugyanazt a tartalomkulcsot kell használnia a visszafejtés során. 
2. Hívja meg a [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) és [a GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) metódusok, hogy a megfelelő X.509 tanúsítvány, amelyet fel kell használni a tartalomkulcs titkosításához.
3. Titkosítsa a tartalomkulcsot az X.509 tanúsítvány nyilvános kulccsal. 
   
   A Media Services .NET SDK az RSA-t oaep-kóddal használja a titkosítás során.  A .NET példa a [EncryptSymmetricKeyData függvényben](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)látható.
4. Hozzon létre egy ellenőrzőösszeg-értéket a kulcsazonosító és a tartalomkulcs használatával számítva. A következő .NET példa az ellenőrzőösszeget a kulcsazonosító GUID részével és a törlési tartalomkulcssal számítja ki.

    ```csharp
            public static string CalculateChecksum(byte[] contentKey, Guid keyId)
            {
                const int ChecksumLength = 8;
                const int KeyIdLength = 16;

                byte[] encryptedKeyId = null;

                // Checksum is computed by AES-ECB encrypting the KID
                // with the content key.
                using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
                {
                    rijndael.Mode = CipherMode.ECB;
                    rijndael.Key = contentKey;
                    rijndael.Padding = PaddingMode.None;

                    ICryptoTransform encryptor = rijndael.CreateEncryptor();
                    encryptedKeyId = new byte[KeyIdLength];
                    encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
                }

                byte[] retVal = new byte[ChecksumLength];
                Array.Copy(encryptedKeyId, retVal, ChecksumLength);

                return Convert.ToBase64String(retVal);
            }
    ```

5. Hozza létre a Content kulcsot a **EncryptedContentKey** (base64 kódolású karakterláncra konvertálva), **a ProtectionKeyId**, **a ProtectionKeyType**, **contentkeytype**és az előző lépésekben kapott **Ellenőrzőösszeg** értékekkel.

    A tárolótitkosításhoz a következő tulajdonságokat kell tartalmaznia a kérelem törzsében.

    Törzstulajdonság kérése    | Leírás
    ---|---
    Azonosító | A ContentKey-azonosító a következő formátumban jön létre: "nb:kid:UUID:\<NEW GUID>".
    ContentKeyType (Tartalomkulcstípusa) | A tartalomkulcs típusa egy egész szám, amely meghatározza a kulcsot. A tárolótitkosítási formátum esetében az érték 1.
    Titkosított tartalomkulcs | Létrehozunk egy új tartalomkulcs-értéket, amely egy 256 bites (32 bájt) érték. A kulcs titkosítva van a Microsoft Azure Media Services ből lekért X.509-es tárolótitkosítási tanúsítvánnyal a GetProtectionKeyId és a GetProtectionKeyId metódusok HTTP GET Get-kérésének végrehajtásával. Példaként a következő .NET-kódot olvassa el: az [itt](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)definiált **EncryptSymmetricKeyData** metódus .
    ProtectionKeyId azonosító | Ez a biztonsági kulcs azonosítója a tartalomkulcs titkosításához használt X.509-es titkosítási x.509-es tanúsítványhoz.
    ProtectionKeyType típus | Ez a tartalomkulcs titkosításához használt védelmi kulcs titkosítási típusa. Ez az érték StorageEncryption(1) a példánkban.
    Ellenőrzőösszeg |A tartalomkulcs MD5-ös számítása. A tartalomazonosító és a tartalomkulcs titkosításával számítja ki. A példakód bemutatja az ellenőrzőösszeg kiszámítását.


### <a name="retrieve-the-protectionkeyid"></a>A ProtectionKeyId beolvasása
A következő példa bemutatja, hogyan lehet letölteni a ProtectionKeyId, a tanúsítvány ujjlenyomatát, a tanúsítvány, amelyet a tartalomkulcs titkosításakor kell használnia. Ezzel a lépéssel győződjön meg arról, hogy már rendelkezik a megfelelő tanúsítvánnyal a számítógépen.

Kérés:

    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net

Válasz:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 139
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:42:52 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>A ProtectionKeyId ProtectionKeyId kulcsának beolvasása
A következő példa bemutatja, hogyan lehet beolvasni az X.509 tanúsítványt az előző lépésben kapott ProtectionKeyId használatával.

Kérés:

    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    Host: media.windows.net

Válasz:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1227
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 05 Feb 2015 07:52:30 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
    "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}

### <a name="create-the-content-key"></a>A tartalomkulcs létrehozása
Miután lekérte az X.509 tanúsítványt, és a nyilvános kulcsával titkosította a tartalomkulcsot, hozzon létre egy **ContentKey** entitást, és ennek megfelelően állítsa be a tulajdonságértékeit.

A tartalomkulcs létrehozásakor bekell állítani a típust. A tárolótitkosítás használataesetén az értéket "1"-re kell állítani. 

A következő példa bemutatja, hogyan hozhat létre egy **ContentKeya-t** **egy ContentKeyType** készlettel a tárolótitkosításhoz ("1") és a **ProtectionKeyType** "0" értékre, jelezve, hogy a védelmi kulcs azonosítója az X.509 tanúsítvány ujjlenyomata.  

Kérés

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net
    {
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
    "ContentKeyType":"1", 
    "ProtectionKeyType":"0",
    "EncryptedContentKey":"your encrypted content key",
    "Checksum":"calculated checksum"
    }

Válasz:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 777
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
    Server: Microsoft-IIS/8.5
    request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:37:46 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
    "Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
    "LastModified":"2015-02-04T02:37:46.9684379Z",
    "ContentKeyType":1,
    "EncryptedContentKey":"your encrypted content key",
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
    "ProtectionKeyType":0,
    "Checksum":"calculated checksum"}

## <a name="create-an-asset"></a>Eszköz létrehozása
A következő példa bemutatja, hogyan hozhat létre egy eszközt.

**HTTP-kérés**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net

    {"Name":"BigBuckBunny" "Options":1}

**HTTP-válasz**

Ha sikeres, a következő választ adja vissza:

    HTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":1,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

## <a name="associate-the-contentkey-with-an-asset"></a>A ContentKey társítása egy eszközhöz
A ContentKey létrehozása után társítsa azt az eszközhöz a $links művelet használatával, ahogy az a következő példában látható:

Kérés:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net

    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Válasz:

    HTTP/1.1 204 No Content 

## <a name="create-an-assetfile"></a>Eszközfájl létrehozása
Az [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) entitás egy blobtárolóban tárolt video- vagy hangfájlt jelöl. Egy eszközfájl mindig egy eszközhöz van társítva, és egy eszköz egy vagy több eszközfájlt tartalmazhat. A Media Services kódoló feladata sikertelen, ha egy eszközfájl-objektum nincs társítva egy blobtárolóban lévő digitális fájlhoz.

Az **AssetFile** példány és a tényleges médiafájl két különálló objektum. Az AssetFile példány metaadatokat tartalmaz a médiafájlról, míg a médiafájl a tényleges médiatartalmat tartalmazza.

Miután feltöltötte a digitális médiafájlt egy blobtárolóba, a **MERGE** HTTP-kérelem melengetése segítségével frissíti az AssetFile fájlt a médiafájllal kapcsolatos információkkal (ez a cikk nem jelenik meg). 

**HTTP-kérés**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net
    Content-Length: 164

    {  
       "IsEncrypted":"true",
       "EncryptionScheme" : "StorageEncryption", 
       "EncryptionVersion" : "1.0",       
       "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector" : "397304628502661816</d:InitializationVector",
       "Options":0,
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }

**HTTP-válasz**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion": "1.0",
       "EncryptionScheme": "StorageEncryption",
       "IsEncrypted":true,
       "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector":"397304628502661816</d:InitializationVector",
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }
