---
title: Engedélyezze a biztonságos TLS-ügyfél Azure Storage |} A Microsoft Docs
description: Ismerje meg, hogy a TLS 1.2 engedélyezése az Azure Storage-ügyfél.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: 2e6ceacd0a6c2b3752391156b404bdfd654b9a8c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55451105"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Biztonságos TLS engedélyezése az Azure Storage-ügyfélnek

Transport Layer Security (TLS) és a Secure Sockets Layer (SSL) a számítógép hálózaton keresztüli kommunikáció biztonságot nyújtanak titkosítási protokollok. Az SSL 1.0, 2.0 és 3.0 sebezhetők találhatók. Ezek RFC lett tiltják. A TLS 1.0 nem biztonságos, az nem biztonságos blokktitkosító (DES CBC és RC2 CBC) és a Stream titkosító (RC4) lesz. PCI Tanács is ajánlott az áttelepítés, a TLS újabb verziójának. További részletekért lásd [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

Az Azure Storage már le van állítva az SSL 3.0 2015 óta, és használja a TLS 1.2-es nyilvános HTTPs-végpontokat, de a TLS 1.0-s és a TLS 1.1 továbbra is támogatott a visszamenőleges kompatibilitás érdekében.

Az Azure Storage biztonságos és megfelelő kapcsolat biztosítása érdekében, engedélyeznie kell a TLS 1.2-es vagy újabb verzió, az ügyféloldali Azure Storage szolgáltatás kérések elküldése előtt.

## <a name="enable-tls-12-in-net-client"></a>A TLS 1.2 engedélyezze a .NET-ügyfél

Az ügyfél egyeztetni a TLS 1.2-es, az operációs rendszer és a .NET-keretrendszer verziója is támogatnia kell a TLS 1.2. További részletek az [a TLS 1.2 támogatása](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).

A következő minta bemutatja, hogyan engedélyezze a TLS 1.2 a .NET-ügyfél.

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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)] 

A következő minta bemutatja a TLS 1.2 engedélyezése a PowerShell-ügyfél.

```powershell
# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGropuName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers
```

## <a name="verify-tls-12-connection"></a>A TLS 1.2-es kapcsolat ellenőrzése

A Fiddler segítségével győződjön meg arról, ha a TLS 1.2 ténylegesen használt-e. Indítsa el az ügyfél hálózati forgalom rögzítése a Fiddler megnyitásához, majd hajtsa végre a fenti példa. Ezután a TLS-verzió található, amely a minta lehetővé teszi a kapcsolatot.

Az alábbi képernyőképen egy mintát, az ellenőrzés.

![Képernyőkép a Fiddlert a TLS-verzió ellenőrzése](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Lásd még

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [A TLS PCI-megfelelőség](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [Engedélyezze a TLS Java-ügyfél](https://www.java.com/en/configure_crypto.html)
