---
title: Biztonságos TLS engedélyezése a .NET-tel
titleSuffix: Azure Storage
description: Megtudhatja, hogyan engedélyezheti a TLS 1,2-et az Azure Storage-hoz készült .NET ügyféloldali kódtár használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 81c9a8fe9513f1f8fc65ad64b34f0fb04383569b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75371802"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Biztonságos TLS engedélyezése az Azure Storage-ügyfélnek

A Transport Layer Security (TLS) és a SSL (SSL) olyan titkosítási protokollok, amelyek kommunikációs biztonságot biztosítanak a számítógép hálózatán. Az SSL 1,0, 2,0 és 3,0 sebezhetőnek bizonyult. Az RFC tiltja őket. A TLS 1,0 nem biztonságos a nem biztonságos blokk titkosítás (DES CBC és RC2 CBC) és a stream Cipher (RC4) használatával. A PCI-Tanács azt is javasolta, hogy az áttelepítés nagyobb TLS-verzióra történjen. További részletekért tekintse meg [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

Az Azure Storage a 2015 óta leállította az SSL 3,0-et, és a TLS 1,2-et a nyilvános HTTPs-végpontokon használja, de a TLS 1,0 és a TLS 1,1 továbbra is támogatott a visszafelé

Az Azure Storage szolgáltatáshoz való biztonságos és megfelelő kapcsolódás érdekében engedélyeznie kell a TLS 1,2-es vagy újabb verzióját az ügyféloldali oldalon, mielőtt elküldené a kérelmeket az Azure Storage szolgáltatásnak.

## <a name="enable-tls-12-in-net-client"></a>A TLS 1.2 engedélyezése .NET-ügyfélben

Ahhoz, hogy az ügyfél egyeztethesse a TLS 1.2-t, az operációs rendszernek és a .NET-keretrendszer verziójának egyaránt támogatnia kell a TLS 1.2-t. A [TLS 1,2-támogatással kapcsolatos](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12)további részletekért lásd:.

A következő példa bemutatja, hogyan történik a TLS 1.2 engedélyezése a .NET-ügyfélben.

```csharp

    static void EnableTls12()
    {
        // Enable TLS 1.2 before connecting to Azure Storage
        System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

        // Connect to Azure Storage
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName={yourstorageaccount};AccountKey={yourstorageaccountkey};EndpointSuffix=core.windows.net");
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        CloudBlobContainer container = blobClient.GetContainerReference("foo");
        container.CreateIfNotExists();
    }

```

## <a name="enable-tls-12-in-powershell-client"></a>A TLS 1.2 engedélyezése PowerShell-ügyfélben

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)] 

Az alábbi példa bemutatja, hogyan engedélyezheti a TLS 1,2-et a PowerShell-ügyfélen.

```powershell
# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGroupName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers
```

## <a name="verify-tls-12-connection"></a>TLS 1,2-kapcsolatok ellenőrzése

A Hegedűs használatával ellenőrizheti, hogy a TLS 1,2 valóban használatban van-e. Nyissa meg a hegedűst az ügyfél hálózati forgalmának megkezdéséhez, majd hajtsa végre a fenti mintát. Ezután megkeresheti a TLS-verziót abban a kapcsolatban, amelyet a minta tesz.

Az alábbi képernyőkép az ellenőrzéshez használható minta.

![képernyőkép a TLS-verzió ellenőrzéséről a Hegedűsben](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Lásd még

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [PCI-megfelelőség a TLS-ben](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [A TLS engedélyezése Java-ügyfélben](https://www.java.com/en/configure_crypto.html)
