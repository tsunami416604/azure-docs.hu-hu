<properties 
    pageTitle="使用 .NET 建立 ContentKeys" 
    description="了解如何建立提供資產安全存取的內容金鑰。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/05/2015" 
    ms.author="juliako"/>


#使用 .NET 建立 ContentKeys

> [AZURE.SELECTOR]
- [REST](media-services-rest-create-contentkey.md)
- [.NET](media-services-dotnet-create-contentkey.md)

媒體服務可讓您建立資產及傳遞已加密的資產。 A **ContentKey** 提供安全存取您 **資產**s。 

當您建立新的資產 (例如，您之前 [檔案上傳](media-services-dotnet-upload-files.md))，您可以指定下列加密選項 ︰ **StorageEncrypted**, ，**CommonEncryptionProtected**, ，或 **EnvelopeEncryptionProtected**。 

當您將資產傳遞給用戶端時，您可以 [設定的動態加密資產](media-services-dotnet-configure-asset-delivery-policy.md) 與下列兩個加密的其中一個 ︰ **DynamicEnvelopeEncryption** 或 **DynamicCommonEncryption**。

加密的資產必須與相關聯 **ContentKey**s。 本文說明如何建立內容金鑰。

>[AZURE.NOTE] 當建立新 **StorageEncrypted** 使用 Media Services.NET SDK，資產 **ContentKey** 自動建立並與資產連結。

##ContentKeyType

您在建立內容金鑰時必須設定的其中一個值就是內容金鑰類型。 選擇下列值之一。 

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

##<a id="envelope_contentkey"></a>建立信封類型 ContentKey

下列程式碼片段會建立信封加密類型的內容金鑰。 然後建立金鑰與所指定資產的關聯。

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



##<a id="common_contentkey"></a>建立一般類型 ContentKey    

下列程式碼片段會建立一般加密類型的內容金鑰。 然後建立金鑰與所指定資產的關聯。

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


##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


