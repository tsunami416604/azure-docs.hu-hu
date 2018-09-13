---
title: Tartalom titkosítása tártitkosítással az AMS REST API-val
description: Ismerje meg, hogy a tartalom titkosítása tártitkosítással az AMS REST API-k.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2018
ms.author: juliako
ms.openlocfilehash: 12c7559f0fab2cda9a97c2edf3e3206448b787c7
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35755869"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>A storage encryption tartalom titkosítása

Helyileg az AES-256 bites titkosítást használ a tartalom titkosításához, és ezután töltse fel az Azure Storage helyén titkosítása erősen ajánlott.

Ez a cikk áttekintést nyújt az AMS tártitkosítás, és bemutatja, hogyan tölthet fel a tárolási titkosított tartalmat:

* Hozzon létre egy tartalomkulcsot.
* Hozzon létre egy objektumot. Állítsa be a AssetCreationOption StorageEncryption, az eszköz létrehozásakor.
  
     A titkosított eszközökre társítva a tartalomkulcs.
* A tartalomkulcs csatolása az eszközhöz.  
* Állítsa be a titkosítással kapcsolatos paramétereit a AssetFile entitásokat.

## <a name="considerations"></a>Megfontolandó szempontok 

Ha azt szeretné, hogy a tárolási titkosított eszköz, konfigurálnia kell az adategység továbbítási házirendjét. Az eszközintelligencia továbbítható, mielőtt a streamelési kiszolgáló eltávolítja a tárolás titkosítása, és adatfolyamként elküldi a tartalmát a megadott objektumtovábbítási szabályzat használatával. További információkért lásd: [adategység-kézbesítési házirendek konfigurálása](media-services-rest-configure-asset-delivery-policy.md).

A Media Services entitások elérésekor a be kell állítani a HTTP-kérelmekre a meghatározott fejlécmezők és értékek. További információkért lásd: [beállítása a Media Services REST API-k fejlesztését](media-services-rest-how-to-use.md). 

### <a name="storage-side-encryption"></a>Storage ügyféloldali titkosítása

|Titkosítási beállítás|Leírás|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services, tárolás titkosítása|AES-256 titkosítással, kulcsfontosságú a Media Services által felügyelt|Támogatott<sup>(1)</sup>|Nem támogatott<sup>(2)</sup>|
|[A Storage Service Encryption for Data at Rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|A kiszolgálóoldali titkosítást az Azure Storage által kínált kulcs felügyelt ügyfél vagy Azure által|Támogatott|Támogatott|
|[Storage ügyféloldali titkosítás](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Az Azure storage, a Key Vault az ügyfél által felügyelt kulcs által kínált ügyféloldali titkosítás|Nem támogatott|Nem támogatott|

<sup>1</sup> közben a Media Services támogatja a tartalom kezelésére, a titkosítatlan/bármilyen titkosítás nélkül, ez nem ajánlott.

<sup>2</sup> a Media Services v3, tárolás titkosítása (AES-256 titkosítás) van csak támogatott a visszamenőleges kompatibilitás a Media Services v2 létrehozásakor a rendszer az eszközöket. Tehát v3 együttműködik a meglévő tárhely eszközök titkosított, de nem teszi meg újakat.

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-t kapcsolódás információkért lásd: [eléréséhez az Azure Media Services API Azure AD-hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>Storage-titkosítás áttekintése
Az AMS tártitkosítás vonatkozik **AES-Parancsra** mód a titkosítás a teljes fájlt.  AES-Parancsra módja egy tetszőleges hosszúságú adatok kitöltése nélkül is titkosító blokktitkosító. Egy számláló letiltása az AES-algoritmust, majd a XOR-ing AES kimenete az adatok titkosítására vagy visszafejtésére titkosításával működik.  A használt számláló blokkot a InitializationVector értékét a számláló értéke 0 és 7 bájt másolásával jön létre, és a számláló értéke 8 – 15 bájt értéke nulla. A számláló 16 bájtos blokk bájt 8 – 15 (azaz a legalacsonyabb bájt) kell használni, mint egy egyszerű 64 bites előjel nélküli egész szám, amely minden ezt követő adatblokkja esetében eggyel növekszik feldolgozása, és a hálózati bájtsorrend megőrzi. Ha egész szám lehet eléri a maximális értéket (0xFFFFFFFFFFFFFFFF), majd növekszik, alaphelyzetbe állítja a blokk számláló (bájt 8 – 15) nulla anélkül, hogy befolyásolná a többi 64 bit a számláló (azaz a 0 és 7 bájt).   Annak érdekében, hogy az AES-Parancsra mód titkosítás a biztonság fenntartására, egy adott kulcs azonosítója egyes tartalomkulcs InitializationVector értékét minden egyes fájl egyedi, és fájlokat kell kevesebb mint 2 ^ 64 blokk hossza.  Ez az egyedi érték, hogy győződjön meg arról, hogy az egy adott kulcs soha nem újrahasznosított egy számláló értékét. A Parancsra móddal kapcsolatos további információkért lásd: [ez wikioldal](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (a wikicikk "Egyszeri" helyett "InitializationVector" kifejezést használja).

Használat **Tártitkosítás** a tiszta tartalom helyileg használja az AES-256 bit a titkosítási és majd töltse fel az Azure Storage helyén titkosítása. Storage-titkosítással védett adategységek automatikus a titkosítás és a kódolás előtt egy titkosított fájlrendszerbe kerülnek, és igény szerint újra titkosítja; az új kimeneti adategységként való feltöltés előtt. Az elsődleges használati eset, a tárolás titkosítása akkor, ha biztonságos, kiváló minőségű bemeneti médiafájlok az erős titkosítás inaktív állapotban a lemezen.

Annak érdekében, hogy a storage titkosított eszköz kézbesítéséhez, konfigurálnia kell az adategység továbbítási házirendjét, a Media Services tudja, hogyan szeretné a tartalmat. Az eszközintelligencia továbbítható, mielőtt a streamelési kiszolgáló eltávolítja a tárolás titkosítása, és streameli a tartalom a megadott továbbítási szabályzatát (például AES, common Encryption titkosítás vagy titkosítás nélkül).

## <a name="create-contentkeys-used-for-encryption"></a>A titkosításhoz használt Tartalomkulcsok létrehozása
A titkosított eszközökre társítva tárolás titkosítási kulcsokat. Hozzon létre az adategység-fájlok létrehozása előtt a titkosításhoz használandó tartalomkulcsot. Ez a szakasz ismerteti, hogyan hozhat létre egy tartalomkulcsot.

A következőkben általános lépéseket: az eszközök csak titkosítás kívánt társító tartalomkulcs létrehozása. 

1. A storage-titkosításhoz véletlenszerűen 32 bájtos AES-kulcs létrehozása. 
   
    A 32 bájtos AES-kulcsot a tartalomkulcsot az adategységhez, ami azt jelenti, hogy az eszközintelligencia szükség használja ugyanazt a tartalom kulcsot visszafejtés közben az hozzárendelt összes fájl. 
2. Hívja a [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) és [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) megszerezni a helyes X.509-tanúsítvány a tartalomkulcs titkosítására használandó módszert.
3. A tartalomkulcs X.509-tanúsítvány nyilvános kulcsával titkosítja. 
   
   A Media Services .NET SDK-t használ az RSA OAEP esetén a titkosítást.  Egy .NET példa látható a [EncryptSymmetricKeyData függvény](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Hozzon létre egy ellenőrzőösszeg-érték alapján számítja ki a kulcsazonosító és a tartalomkulcsot. Ez a példa .NET az ellenőrzőösszeg használatával a kulcs azonosítóját és a törlés tartalomkulcsot a GUID részét számítja ki.

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

5. Hozzon létre a tartalomkulcsot a a **EncryptedContentKey** (base64-kódolású karakterlánc konvertálva), **ProtectionKeyId**, **ProtectionKeyType**,  **ContentKeyType**, és **ellenőrzőösszeg** értékeket az előző lépésben kapott.

    A storage-titkosításhoz a következő tulajdonságokat a kérelem törzsében szereplő szerepelnie kell.

    Kérelem törzse tulajdonság    | Leírás
    ---|---
    Azonosító | A ContentKey azonosító jön létre a következő formátumban "nb:kid:UUID:<NEW GUID>".
    ContentKeyType | A tartalom írja be a kulcsot meghatározó egész szám. A tárolási titkosítás formátumot az érték az 1.
    EncryptedContentKey | Létrehozunk egy új content key értéket, hogy egy 256 bites (32 bájt) érték. A kulcs titkosítva van, a tárolási titkosítás X.509 tanúsítványt használ, amely a Microsoft Azure Media Services által egy HTTP GET kérés végrehajtása a GetProtectionKeyId és GetProtectionKey módszerek beolvassuk. Tegyük fel, tekintse meg a következő .NET-kód: a **EncryptSymmetricKeyData** meghatározott metódus [Itt](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtectionKeyId | Ez a védelem a tárolási titkosítás X.509-tanúsítvány, amely a tartalom kulcs titkosításához használt kulcs azonosítója.
    ProtectionKeyType | Ez egy, a védelmi kulcs, amely a tartalom kulcs titkosításához használt titkosítási típus. Ezt az értéket a példa StorageEncryption(1).
    Ellenőrzőösszeg |Az MD5-tel számított ellenőrzőösszeg a tartalomkulcsot. A tartalomkulcs-Tartalomazonosítóját titkosításával számítja ki. Példakód bemutatja, hogyan az ellenőrzőösszeg kiszámítása.


### <a name="retrieve-the-protectionkeyid"></a>A ProtectionKeyId beolvasása
Az alábbi példa bemutatja, hogyan kérheti le a ProtectionKeyId, egy tanúsítvány ujjlenyomata a tanúsítvány a tartalomkulcs titkosításakor kell használnia. Ehhez a lépéshez, győződjön meg arról, hogy már rendelkezik a megfelelő tanúsítvány a gépen.

Kérés:

    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
    x-ms-version: 2.17
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

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>A ProtectionKey lekérheti a ProtectionKeyId
Az alábbi példa bemutatja, hogyan kérheti le az X.509-tanúsítvány a ProtectionKeyId az előző lépésben kapott.

Kérés:

    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
    x-ms-version: 2.17
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
Miután X.509-tanúsítvány lekérése és a tartalom kulcs titkosításához használt nyilvános kulcsát, hozzon létre egy **ContentKey** entitás, és ennek megfelelően érték a tulajdonság beállítása.

Az, hogy kell-e beállítva mikor értékek egyike a tartalom létrehozása kulcs típusa. Storage-titkosítás használata esetén az értéket "1" kell állítani. 

Az alábbi példa bemutatja, hogyan hozhat létre egy **ContentKey** együtt egy **ContentKeyType** tárolás titkosítása ("1") beállítása és a **ProtectionKeyType** "0" értékre van állítva, jelezve, hogy a védelmi kulcs csomagazonosítója az X.509-tanúsítvány ujjlenyomata.  

Kérés

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
    x-ms-version: 2.17
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

## <a name="create-an-asset"></a>Hozzon létre egy objektumot
Az alábbi példa bemutatja, hogyan hozzon létre egy objektumot.

**HTTP-kérelem**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.17
    Host: media.windows.net

    {"Name":"BigBuckBunny" "Options":1}

**HTTP-válasz**

Ha ez sikeres, a következő választ adja vissza:

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

## <a name="associate-the-contentkey-with-an-asset"></a>Egy eszköz a ContentKey társítása
Miután létrehozta a ContentKey, társíthatja azt az objektumot a $links művelettel, az alábbi példában látható módon:

Kérés:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
    x-ms-version: 2.17
    Host: media.windows.net

    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Válasz:

    HTTP/1.1 204 No Content 

## <a name="create-an-assetfile"></a>Hozzon létre egy AssetFile
A [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) entitás a blobtárolóban tárolt video- és audiotartalmak fájlt jelöli. Egy eszköz fájl mindig egy eszköz társítva, és egy adategység tartalmazhat egy vagy több adategység-fájlok. A Media Services Encoder feladat sikertelen lesz, ha egy eszköz fájl objektumhoz nem kapcsolódik egy digitális fájlhoz a blobtárolóban.

A **AssetFile** -példány és a tényleges médiafájl két különböző objektumot. A AssetFile-példány a médiafájl kapcsolatos metaadatokat tartalmaz, amíg az adathordozó-fájl tartalmazza a tényleges médiatartalmakat.

A digitális média-fájl feltöltése a blob-tárolóba, után fog használni a **EGYESÍTÉSE** HTTP-kérelem a AssetFile frissítse a médiafájl (ebben a cikkben nem látható) kapcsolatos információkat. 

**HTTP-kérelem**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.17
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
