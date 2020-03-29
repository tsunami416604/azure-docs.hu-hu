---
title: Biztonságos TLS engedélyezése a .NET segítségével
titleSuffix: Azure Storage
description: Ismerje meg, hogyan engedélyezheti a TLS 1.2-t a .NET ügyfélkódtár használatával az Azure Storage-hoz.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 81c9a8fe9513f1f8fc65ad64b34f0fb04383569b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371802"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Biztonságos TLS engedélyezése az Azure Storage-ügyfélnek

A Transport Layer Security (TLS) és a Secure Sockets Layer (SSL) olyan kriptográfiai protokollok, amelyek számítógépes hálózaton keresztül biztosítják a kommunikáció biztonságát. Az SSL 1.0, 2.0 és 3.0 sebezhetőnek bizonyult. Az RFC megtiltotta őket. A TLS 1.0 nem biztonságos blokktitkosítás (DES CBC és RC2 CBC) és Stream titkosítás (RC4) használatával bizonytalanná válik. A PCI tanácsa azt is javasolta, hogy a nagyobb TLS-verziókra való áttérést. További részletek: Transport [Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

Az Azure Storage 2015 óta leállította az SSL 3.0-t, és a TLS 1.2-t használja a nyilvános HTTPs-végpontokon, de a TLS 1.0 és a TLS 1.1 továbbra is támogatott a visszamenőleges kompatibilitás érdekében.

Az Azure Storage biztonságos és megfelelő kapcsolatának biztosítása érdekében engedélyeznie kell a TLS 1.2-es vagy újabb verzióját az ügyféloldalon, mielőtt az Azure Storage-szolgáltatás üzemeltetésére vonatkozó kérelmeket küldene.

## <a name="enable-tls-12-in-net-client"></a>A TLS 1.2 engedélyezése .NET-ügyfélben

Ahhoz, hogy az ügyfél egyeztetje a TLS 1.2-t, az operációs rendszernek és a . További részletek A [TLS 1.2 támogatása .](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12)

Az alábbi minta bemutatja, hogyan engedélyezheti a TLS 1.2-t a .NET ügyfélben.

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

Az alábbi minta bemutatja, hogyan engedélyezheti a TLS 1.2-t a PowerShell-ügyfélben.

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

## <a name="verify-tls-12-connection"></a>TLS 1.2-es kapcsolat ellenőrzése

A Fiddler segítségével ellenőrizheti, hogy a TLS 1.2-t valóban használják-e. Nyissa meg a Fiddler-t az ügyfélhálózati forgalom rögzítésének megkezdéséhez, majd hajtsa végre a fenti mintát. Ezután megtalálhatja a TLS-verziót a minta által létrevett kapcsolatban.

A következő képernyőkép egy minta az ellenőrzéshez.

![képernyőkép a TLS-verzió ellenőrzéséről a Fiddlerben](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Lásd még

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [PCI-megfelelőség a TLS-en](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [TLS engedélyezése Java-ügyfélben](https://www.java.com/en/configure_crypto.html)
