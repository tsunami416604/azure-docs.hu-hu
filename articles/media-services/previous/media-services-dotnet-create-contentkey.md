---
title: Tartalomkulcsok létrehozása a .NET használatával
description: Ismerje meg, hogyan hozhat létre, amelyek biztonságos hozzáférést biztosítanak az eszközök tartalomkulcs.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2019
ms.author: juliako
ms.openlocfilehash: 148e5b8dad4e204f9cf0ab4288777b65765666e8
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245675"
---
# <a name="create-contentkeys-with-net"></a>Tartalomkulcsok létrehozása a .NET használatával 
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

A Media Services lehetővé teszi, hogy hozhat létre és nyújthat a titkosított eszközökre. A **ContentKey** biztonságos hozzáférést biztosít a **eszköz**s. 

Amikor létrehoz egy új eszköz (például előtt [fájlok feltöltése](media-services-dotnet-upload-files.md)), a következő titkosítási beállításokat is megadhat: **StorageEncrypted**, **CommonEncryptionProtected**, vagy **EnvelopeEncryptionProtected**. 

Ha az ügyfelek számára teszi elérhetővé az eszközök, [dinamikusan legyen titkosítva eszközök konfigurálása](media-services-dotnet-configure-asset-delivery-policy.md) valamelyik a következő két titkosítások használatára: **DynamicEnvelopeEncryption** vagy **DynamicCommonEncryption**.

A titkosított eszközökre kell társítani **ContentKey**s. Ez a cikk ismerteti, hogyan hozhat létre egy tartalomkulcsot.

> [!NOTE]
> Egy új létrehozásakor **StorageEncrypted** eszközintelligencia, a Media Services .NET SDK használatával a **ContentKey** az automatikusan létrehozott és az eszköz kapcsolódik.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
A egyike, hogy kell-e beállítva mikor hozzon létre egy tartalom kulcs a tartalom kulcs típusa. A következő értékek közül választhat. 

```csharp
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
```

## <a id="envelope_contentkey"></a>ContentKey boríték-típus létrehozása
Az alábbi kódrészlet létrehoz egy tartalomkulcsot a boríték titkosítási típus. Majd társítja a kulcsot a megadott eszköz.

```csharp
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

call

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);
```


## <a id="common_contentkey"></a>Általános típus ContentKey létrehozása
Az alábbi kódrészlet létrehoz egy tartalomkulcsot a közös titkosítási típus. Majd társítja a kulcsot a megadott eszköz.

```csharp
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
call

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 
```

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

