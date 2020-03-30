---
title: ContentKeys létrehozása a .NET segítségével
description: Ez a cikk bemutatja, hogyan hozhat létre olyan tartalomkulcsokat, amelyek biztonságos hozzáférést biztosítanak az eszközökhöz.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: aebd6dee9314d6e5641988767c024790b6b721f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251153"
---
# <a name="create-contentkeys-with-net"></a>ContentKeys létrehozása a .NET segítségével 
> [!div class="op_single_selector"]
> * [Többi](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

A Media Services lehetővé teszi titkosított eszközök létrehozását és kézbesítését. A **ContentKey** biztonságos hozzáférést biztosít az eszköztárakhoz. **Asset** 

Új eszköz létrehozásakor (például [fájlok feltöltése](media-services-dotnet-upload-files.md)előtt) a következő titkosítási beállításokat adhatja meg: **StorageEncrypted**, **CommonEncryptionProtected**vagy **EnvelopeEncryptionProtected**. 

Amikor eszközöket szállít az ügyfeleknek, [beállíthatja, hogy az eszközök dinamikusan titkosíthatók legyenek](media-services-dotnet-configure-asset-delivery-policy.md) az alábbi két titkosítás egyikével: **DynamicEnvelopeEncryption** vagy **DynamicCommonEncryption**.

A titkosított eszközöket a **ContentKey**s-hez kell társítani. Ez a cikk a tartalomkulcs létrehozását ismerteti.

> [!NOTE]
> Új **StorageEncrypted** eszköz létrehozásakor a Media Services .NET SDK használatával a **ContentKey** automatikusan létrejön, és kapcsolódik az eszközhöz.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType (Tartalomkulcstípusa)
A tartalomkulcs létrehozásakor bekell állítani a tartalomkulcs egyik értékét a tartalomkulcs típusa. Válasszon az alábbi értékek közül. 

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

## <a name="create-envelope-type-contentkey"></a><a id="envelope_contentkey"></a>ContentKey borítéktípus létrehozása
A következő kódrészlet a borítéktitkosítási típusú tartalomkulcsot hozza létre. Ezután társítja a kulcsot a megadott eszközhöz.

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


## <a name="create-common-type-contentkey"></a><a id="common_contentkey"></a>ContentKey általános típus létrehozása
A következő kódrészlet a közös titkosítási típus tartalomkulcsát hozza létre. Ezután társítja a kulcsot a megadott eszközhöz.

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

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

