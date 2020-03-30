---
title: Fiók létrehozása SAS-szal a .NET segítségével
titleSuffix: Azure Storage
description: Megtudhatja, hogy miként hozhat létre közös hozzáférésű fiókaláírást (SAS) a .NET ügyfélkódtár használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 9da27cef7bafa94715a42db86fc5a5675a049eb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137869"
---
# <a name="create-an-account-sas-with-net"></a>Fiók létrehozása SAS-szal a .NET segítségével

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Ez a cikk bemutatja, hogyan hozhat létre egy fiók SAS-t a [.NET Azure Storage ügyfélkódtárjával.](/dotnet/api/overview/azure/storage?view=azure-dotnet)

## <a name="create-an-account-sas"></a>Fiók SAS létrehozása

Ha sas-t szeretne létrehozni egy tárolóhoz, hívja meg a [CloudStorageAccount.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature) metódust.

A következő kódpélda létrehoz egy fiókot SAS, amely érvényes a Blob és a Fájl szolgáltatások, és megadja az ügyfél engedélyek olvasási, írási és lista engedélyek eléréséhez szolgáltatásszintű API-k. The account SAS restricts the protocol to HTTPS, so the request must be made with HTTPS. Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

## <a name="use-an-account-sas-from-a-client"></a>Ügyféltől származó fiók SAS használata

A fiók SAS használatával a Blob szolgáltatás szolgáltatásszintű API-k eléréséhez, készítse el a Blob szolgáltatás ügyfélobjektum a SAS és a Blob storage-végpont a tárfiókhoz. Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

## <a name="next-steps"></a>További lépések

- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz megosztott hozzáférésű aláírások (SAS) használatával](storage-sas-overview.md)
- [Fiók SAS létrehozása](/rest/api/storageservices/create-account-sas)
