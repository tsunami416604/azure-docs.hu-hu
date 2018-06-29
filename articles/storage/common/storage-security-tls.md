---
title: Azure Storage-ügyfél biztonságos TLS engedélyezése |} Microsoft Docs
description: Ismerje meg, hogy a TLS 1.2 engedélyezése az ügyfél az Azure Storage.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/25/2018
ms.author: fryu
ms.openlocfilehash: 5c21df2b3bdeee6ac7c3956fe1cafa4f947dd6dd
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036094"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Azure Storage-ügyfél biztonságos TLS engedélyezése

Ha naplózni kell Azure Storage használata a szolgáltatások alapján legújabb megfelelőségi és biztonsági követelmények, SSL 1.0, 2.0-s, 3.0 és TLS 1.0 értelmezhető nem megfelelő kommunikációs protokollokat.

SSL 1.0-s 2.0 és 3.0 található érinti. Azok az RFC által tilos rendelkezik. A TLS 1.0 nem biztonságos blokktitkosításon (DES CBC és RC2 CBC) és adatfolyam-titkosítás (RC4) nem biztonságos lesz. PCI Tanács is ajánlott az áttelepítés magasabb TLS verziói. A részleteket megtekintheti [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

Az Azure Storage 2015 leállt az SSL 3.0, és használja a TLS 1.2-es HTTPs-végponton nyilvános, de a TLS 1.0-s és a TLS 1.1 továbbra is támogatott a visszamenőleges kompatibilitás érdekében.

Annak érdekében, Azure Storage biztonságosságának és kapcsolat, lehetővé kell tenni a TLS 1.2 ügyféloldali az Azure Storage szolgáltatás működtetéséhez kérelmek elküldése előtt.

## <a name="enable-tls-12-in-net-client"></a>A TLS 1.2 engedélyezze a .NET-ügyfél

Az ügyfél egyeztetni a TLS 1.2-es, az operációs rendszer és a .NET-keretrendszer verziója is kell támogatja a TLS 1.2-es. További részletek az [támogatja a TLS 1.2](https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls#support-for-tls-12).

A következő példa bemutatja, hogyan TLS 1.2 ahhoz, hogy a .NET-ügyfél.

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

## <a name="enable-tls-12-in-powershell-client"></a>A TLS 1.2 engedélyezése a PowerShell-ügyfél

A következő példa bemutatja, hogyan TLS 1.2 engedélyezése a PowerShell-ügyfél.

```powershell

# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGropuName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzureStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers

```

## <a name="verify-tls-12-connection"></a>A TLS 1.2-es kapcsolat ellenőrzése

Fiddler segítségével győződjön meg arról, ha a TLS 1.2 ténylegesen szerepel-e. Nyissa meg az ügyfél hálózati forgalmat rögzítő elindításához Fiddler, majd hajtsa végre minta fent. Ezután a minta a kapcsolat a TLS verziója található.

Az alábbi képernyőfelvételen egy minta az ellenőrzéshez.

![Képernyőkép a TLS-verziót a Fiddler ellenőrzése](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Lásd még

* [A Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
