---
title: "A tartalom titkosított a tárolás titkosítása AMS REST API használatával"
description: "Ismerje meg, hogy a tartalom titkosítása a tárolás titkosítása AMS REST API-k használatával."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: juliako
ms.openlocfilehash: 3c752573be7c07f800b0dce3d12d4dabd7328922
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="encrypting-your-content-with-storage-encryption"></a>A tárolás titkosítása tartalom titkosítása

Erősen ajánlott a helyileg az AES-256 bites titkosítás használata tartalom titkosításához, és ezután töltse fel az Azure Storage helyén titkosítása.

Ez a cikk áttekintést AMS tárolás titkosítása, és bemutatja, hogyan töltse fel a tárolási titkosított tartalom:

* Hozzon létre egy tartalomkulcsot.
* Hozzon létre egy eszközt. Állítsa be a AssetCreationOption StorageEncryption, amikor az eszköz hoz létre.
  
     Titkosított eszközök tartalomkulcs társítani kell rendelkeznie.
* A tartalomkulcs csatolása az eszközhöz.  
* Állítsa be a titkosítással kapcsolatos paramétereit a AssetFile entitásokat.

## <a name="considerations"></a>Megfontolandó szempontok 

Ha egy tárolási titkosított eszköz kézbesíteni szeretné, konfigurálnia kell az adategység továbbítási házirendjét. Mielőtt az eszköz továbbítható, a streamelési kiszolgáló a tárolás titkosítása eltávolítja, és az adatfolyamokat, a tartalom a megadott objektumtovábbítási szabályzat segítségével. További információkért lásd: [konfigurálása az adategység továbbítási házirendjeit](media-services-rest-configure-asset-delivery-policy.md).

A Media Services entitások elérésekor be kell meghatározott fejlécmezők és értékek a HTTP-kérelmekre. További információkért lásd: [a Media Services REST API fejlesztési telepítő](media-services-rest-how-to-use.md). 

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-hoz kapcsolódáshoz információkért lásd: [elérni az Azure Media Services API-t az Azure AD-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>Tárolás titkosítási – áttekintés
Az AMS tárolás titkosítása vonatkozik **AES-Parancsra** mód a titkosítás a teljes fájlt.  AES-Parancsra módja a blokktitkosításon, amelyek titkosíthatják a tetszőleges hosszúságú adatok kitöltési szükségessége nélkül. Egy számláló blokkot, amelynek a AES algoritmust, majd a XOR-ing AES kimenete az adatok titkosítására vagy visszafejtésére titkosításával működik.  A számláló blokk használt összeállított úgy, hogy a számláló értéke 0 és 7 bájtja a InitializationVector értékét, és a számláló értéke 8-15 bájtja értéke nulla. A 16 bájtos számláló blokk bájt 8-15 (Ez azt jelenti, hogy a legkisebb helyiértékű bájt) kell használni, mint egy egyszerű 64 bites előjel nélküli egész szám, amely minden ezt követő adatblokk eggyel növeli dolgoz fel, és hálózati maradnak. Ha az egész eléri a maximális értéket (0xFFFFFFFFFFFFFFFF) majd növekvő alaphelyzetbe állítja a blokk számláló (bájt 8-15) nulla nem befolyásolja a többi 64 bit a számláló (Ez azt jelenti, hogy a 0 és 7 bájt).   Ahhoz, hogy az AES-Parancsra mód titkosítási biztonságának fenntartásához, egy adott kulcs azonosítót minden tartalomkulcsot InitializationVector értékét kell minden egyes fájl egyedi, és fájlok legfeljebb 2 ^ 64 blokkok hossza.  Ez azért szükséges, hogy a számláló értéke nem fel újra egy adott kulccsal. A Parancsra móddal kapcsolatos további információkért lásd: [a wiki lapon](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (a a wikicikket "Nonce" helyett "InitializationVector" kifejezést használja).

Használjon **tárolás titkosítása** a tiszta tartalom helyileg használatával az AES-256 bit a titkosítási és majd töltse fel az Azure Storage helyén titkosítása. Storage-titkosítással védett adategységek automatikusan a titkosítás és helyezni egy titkosított fájlrendszerbe kódolás előtt, és egy új kimeneti eszközként feltöltés előtt esetleg újra titkosítja. A tárolás titkosítása elsődleges használati eset az, amikor biztonságossá tételéhez a kiváló minőségű bemeneti médiafájljait erős titkosítással aktívan a lemezen.

A tárolási titkosított eszköz kezelni, konfigurálnia kell az adategység továbbítási házirendjét, a Media Services tudja, hogyan kívánja a tartalom. Mielőtt az eszköz továbbítható, a streamelési kiszolgáló eltávolítja a tárolás titkosítása, és az adatfolyamokat a tartalmat a megadott továbbítási házirendjét (például AES, általános titkosítás vagy titkosítás nélkül) használatával.

## <a name="create-contentkeys-used-for-encryption"></a>A titkosításhoz használt ContentKeys létrehozása
A titkosított eszközökre kell lennie a tárolási titkosítási kulcs. A tartalomkulcs használt titkosítási az adategység-fájloknak létrehozása előtt létre kell hoznia. Ez a szakasz ismerteti, hogyan hozzon létre egy tartalomkulcsot.

Az alábbi lépések általános a titkosítani kívánt eszközök társítani tartalom kulcs létrehozásakor. 

1. A tárolás titkosítása véletlenszerűen 32 bájtos AES kulcs létrehozása. 
   
    Ez az a tartalomkulcsot az adategységhez, ami azt jelenti, hogy ugyanazt a tartalom kulcsot használhatja a visszafejtés során szükséges társított összes fájlt. 
2. Hívja a [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) és [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) módszerek megszerezni a helyes X.509-tanúsítvány használatával titkosítja a tartalomkulcsot.
3. A tartalomkulcs X.509-tanúsítvány nyilvános kulcsával titkosítja. 
   
   Media Services .NET SDK RSA-t használ a OAEP típusú végrehajtásakor a titkosítást.  A .NET példáját láthatja az [EncryptSymmetricKeyData függvény](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Hozzon létre egy ellenőrzőösszeget számítja ki a kulcs azonosítója és a tartalomkulcsot. A következő .NET típusú példát a GUID része a kulcsazonosító és egyértelműen tartalomkulcsot ellenőrzőösszeg számítja ki.

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

1. Hozzon létre a tartalomkulcsot a a **EncryptedContentKey** (karakterlánccá base64-kódolású), **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType**, és **ellenőrzőösszeg** értékeket az előző lépésben kapott.

    A tárolás titkosítását a következő tulajdonságok tartozhatnak a kérés törzsében.

    Kérelem törzse tulajdonság    | Leírás
    ---|---
    Azonosító | A ContentKey azonosítója, amely azt ragozott formáival létrehozása a következő formátumban "nb:kid:UUID:<NEW GUID>".
    ContentKeyType | Ez az a tartalom írja be a tartalom kulcs egész szám lehet. Az érték 1 storage-titkosítás továbbítja azt.
    EncryptedContentKey | A Microsoft hozzon létre egy új tartalom kulcs értéket, amely a 256 bites (32 bájt) értéket. A kulcs titkosított tárolási titkosítási X.509-tanúsítvány a következő HTTP GET kérelemre futtatásával GetProtectionKeyId és GetProtectionKey módszerek nem beolvasni a Microsoft Azure Media Services használatával. Tegyük fel, tekintse meg a következő .NET-kódot: a **EncryptSymmetricKeyData** definiált metódus [Itt](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtectionKeyId | Ez az a védelmi tároló titkosítási X.509-tanúsítvány, amely a tartalom kulcs titkosításához használt kulcs azonosítója.
    ProtectionKeyType | Ez egy, a védelem a tartalom kulcs titkosításához használt kulcs a titkosítási típus. Ez az érték a fenti példában StorageEncryption(1).
    Ellenőrzőösszeg |Az MD5 számított ellenőrzőösszeg a tartalomkulcsot. A tartalom azonosítója a tartalom kulccsal titkosítja számítja ki. A mintakód bemutatja, hogyan ellenőrzőösszeg számítása.


### <a name="retrieve-the-protectionkeyid"></a>A ProtectionKeyId beolvasása
A következő példa bemutatja, hogyan beolvasni a ProtectionKeyId, egy tanúsítvány-ujjlenyomat, a tanúsítványt a tartalomkulcsot titkosításakor kell használnia. Hajtsa végre ezt a lépést, győződjön meg arról, hogy már rendelkezik a megfelelő tanúsítvány a gépen.

A kérelem:

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

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>A ProtectionKey lekérdezni a ProtectionKeyId
A következő példa bemutatja, hogyan lehet lekérni az X.509-tanúsítvány a ProtectionKeyId az előző lépésben kapott.

A kérelem:

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
Az X.509 tanúsítvány lekérése és a tartalom kulcs titkosításához használt nyilvános kulcsát, akkor létre kell hoznia egy **ContentKey** entitás és a tulajdonság értékek ennek megfelelően beállítva.

A tartalom létrehozása a értékeket, hogy kell-e állítva mikor kulcs egy típus. Esetén a tárolás titkosítása értéke "1". 

A következő példa bemutatja, hogyan hozhat létre egy **ContentKey** rendelkező egy **ContentKeyType** tárolás titkosítása ("1") beállítása és a **ProtectionKeyType** azt jelzi, hogy a védelem kulcs azonosítója az X.509 tanúsítvány ujjlenyomata "0" értékre állítva.  

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

## <a name="create-an-asset"></a>Egy eszköz létrehozása
A következő példa bemutatja, hogyan hozzon létre egy eszközt.

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

Ha sikeres, a következő adja vissza:

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
Miután létrehozta a ContentKey, rendelje hozzá azt az objektumot az $links művelet használatával a következő példában látható módon:

A kérelem:

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
A [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) entitás egy blob-tárolóban tárolt video- vagy fájlt jelöli. Egy eszköz fájl mindig társítva van egy eszköz, és egy eszköz egy vagy több eszköz fájlt tartalmaz. A Media Services kódoló feladat sikertelen lesz, ha egy eszköz fájl objektumhoz nincs társítva egy digitális fájlhoz egy blob-tárolóban.

Vegye figyelembe, hogy a **AssetFile** példány és a tényleges médiafájl két különböző objektum. A AssetFile példány media fájl metaadatainak tartalmaz, míg a médiafájl tartalmazza a tényleges médiatartalmakat.

A digitális adathordozójának fájl feltöltése a blob-tárolóba, után fogja használni a **EGYESÍTÉSE** HTTP-kérelem a AssetFile frissítheti a adatainak media (ebben a cikkben nem látható). 

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
