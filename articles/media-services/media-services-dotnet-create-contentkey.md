---
title: "A .NET ContentKeys létrehozása"
description: "Ismerje meg, amelyek biztonságos hozzáférést biztosítanak az eszközök tartalomkulcs létrehozása."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: 3280a6fcde59bae360da7cb9fea4bb649f984e43
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="create-contentkeys-with-net"></a>A .NET ContentKeys létrehozása
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

A Media Services titkosított eszközök teszi lehetővé. A **ContentKey** biztonságos hozzáférést biztosít a **eszköz**s. 

Amikor létrehoz egy új eszközt (például előtt [fájlok feltöltése](media-services-dotnet-upload-files.md)), a következő titkosítási beállításokat adhat meg: **StorageEncrypted**, **CommonEncryptionProtected**, vagy **EnvelopeEncryptionProtected**. 

Eszközök kézbesítése az ügyfelek számára, amikor is [dinamikusan legyen titkosítva eszközök konfigurálása](media-services-dotnet-configure-asset-delivery-policy.md) valamelyik, a következő két titkosítások használatára: **DynamicEnvelopeEncryption** vagy **DynamicCommonEncryption**.

Titkosított eszközöknek kell társítani **ContentKey**s. A cikkből megtudhatja, hogyan hozzon létre egy tartalomkulcsot.

> [!NOTE]
> Amikor hoz létre egy új **StorageEncrypted** eszközök a Media Services .NET SDK használatával a **ContentKey** az automatikusan létrehozott és az eszköz kapcsolódik.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
A tartalom létrehozása a értékeket, hogy kell-e állítva mikor kulcsa a tartalom írja be. A következő értékek közül választhat. 

    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }

## <a id="envelope_contentkey"></a>Boríték típus ContentKey létrehozása
A következő kódrészletet hoz létre egy tartalomkulcsot a boríték titkosítási típus. Ezután a kulcs társít a megadott eszköz.

    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

Hívás

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



## <a id="common_contentkey"></a>Általános típus ContentKey létrehozása
A következő kódrészletet hoz létre egy tartalomkulcsot a közös titkosítási típus. Ezután a kulcs társít a megadott eszköz.

    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
Hívás

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

