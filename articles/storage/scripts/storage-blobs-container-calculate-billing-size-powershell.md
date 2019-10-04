---
title: Az Azure PowerShell-szkript minták – teljes számlázási blobtároló méretének kiszámítása |} A Microsoft Docs
description: Az Azure Blob storage-tároló teljes méretének kiszámítása a számlázás tekintetében.
services: storage
documentationcenter: na
author: WenJason
manager: digimobile
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
origin.date: 11/07/2017
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: 02b4cfcc6d88430701f653665269532a4eb7092f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60460639"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Teljes számlázási blobtároló méretének kiszámítása

Ez a szkript kiszámítja egy tárolót az Azure Blob storage céljából számlázási költségek becslése méretét. A parancsfájl a tárolóban lévő blobok méretének összegzi.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Ez a PowerShell-szkript egy tároló méretének számlázási célból számítja ki. Tároló mérete más célokra számítja ki, ha [kiszámítása a Blob storage-tároló amelyek összméretén](../scripts/storage-blobs-container-calculate-size-powershell.md) becsült biztosító egyszerűbb parancsfájl.

## <a name="determine-the-size-of-the-blob-container"></a>A blob-tároló méretének meghatározásához.

A blob-tároló teljes méretét a maga a tároló és a méretét a tárolóban található összes blobot tartalmaz.

Az alábbi szakaszok azt ismerteti, hogyan kerül kiszámításra a tárolási kapacitás a blob-tárolók és blobok. A következő szakaszban Len(X) azt jelenti, hogy a karakterláncban szereplő karakterek száma.

### <a name="blob-containers"></a>BLOB-tárolók

A következő számítási ismerteti, hogyan lehet megbecsülni egy blobtároló felhasznált tárterület mérete:

```
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
```

A következő táblázat összefoglalja következő:
* az egyes tárolók terheléssel 48 bájt az utolsó módosítás dátuma, engedélyek, nyilvános beállítások és néhány rendszer metaadatokat tartalmaz.

* A tároló neve Unicode-ként tárolja úgy karakterek állapotba, és két szorzása.

* Minden egyes blob tárolóban tárolt metaadatok blokk a neve (ASCII), továbbá a karakterlánc hosszának tárolása.

* Az 512 bájt / aláírt azonosító tartalmazza az aláírt azonosító neve, kezdési idő, lejárati időpontot és engedélyeket.

### <a name="blobs"></a>Blobok

A következő számítások blobonkénti felhasznált tárterület mérete becslése szemléltetik.

* A blokkblobok (alap blob vagy pillanatkép):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   ```

* A lapblobok (alap blob vagy pillanatkép):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   ```

A következő táblázat összefoglalja következő:

* blob, amely tartalmazza a terheléssel 124 bájtok:
    - Utolsó módosítás időpontja
    - Méret
    - Cache-Control
    - Content-Type
    - Tartalom-Language
    - Content-Encoding
    - Content-MD5
    - Engedélyek
    - Pillanatkép-információk
    - Bérlet
    - Egyes metaadatok

* A blob neve Unicode-ként tárolja úgy karakterek állapotba, és két szorzása.

* Minden egyes tárolt metaadatok kódblokkot vegyen fel (ASCII tárolva), a név hosszának plusz a karakterlánc hossza.

* A blokkblobok:
  * a blokkblob blokklistájának 8 bájt.
  * Blokkok száma túllépi a azonosítója blokkméret (bájt).
  * Az összes véglegesítése és a nem véglegesített blokkok az adatok mérete.

    >[!NOTE]
    >A pillanatképek használata esetén ez a méret csak az alap- vagy pillanatkép BLOB egyedi adatokat is tartalmaz. A nem véglegesített blokkok nem használják a hét után, ha azok szemétgyűjtési gyűjtött. Ezt követően azok nem beleszámítanak a számlázás.

* Lapblobok:
  * Az adatok nem egymást követő laptartomány száma túllépi az 12 bájt. Láthatja, mikor történt egyedi laptartomány száma a **GetPageRanges** API-t.

  * Az adatok (bájt) összes tárolt oldalt mérete.

    >[!NOTE]
    >Pillanatképek használata esetén ez a méret a csak az alap blob vagy van megszámolni blob pillanatkép egyedi lapokat tartalmaz.

## <a name="sample-script"></a>Példaszkript

```powershell

# this script will show how to get the total size of the blobs in a container
# before running this, you need to create a storage account, create a container,
#    and upload some blobs into the container
# note: this retrieves all of the blobs in the container in one command.
#       connect Azure with Login-AzAccount -EnvironmentName AzureChinaCloud before you run the script.
#       requests sent as part of this tool will incur transactional costs. 
# command line usage: script.ps1 -ResourceGroup {YourResourceGroupName} -StorageAccountName {YourAccountName} -ContainerName {YourContainerName}
#


param(
    [Parameter(Mandatory=$true)]
    [string]$ResourceGroup,

    [Parameter(Mandatory=$true)]
    [string]$StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]$ContainerName
)

#Set-StrictMode will cause Get-AzureStorageBlob returns result in different data types when there is only one blob
#Set-StrictMode -Version 2

$VerbosePreference = "Continue"

if((Get-Module -ListAvailable Az.Storage) -eq $null)
{
    throw "Azure Powershell not found! Please install from https://docs.microsoft.com/en-us/powershell/azure/install-Az-ps"
}

# function Retry-OnRequest
function Retry-OnRequest
{
    param(
        [Parameter(Mandatory=$true)]
        $Action)
    
    # It could encounter various of temporary errors, like network errors, or storage server busy errors.
    # Should retry the request on transient errors

    # Retry on storage server timeout errors
    $clientTimeOut = New-TimeSpan -Minutes 15
    $retryPolicy = New-Object -TypeName Microsoft.WindowsAzure.Storage.RetryPolicies.ExponentialRetry -ArgumentList @($clientTimeOut, 10)        
    $requestOption = @{}
    $requestOption.RetryPolicy = $retryPolicy

    # Retry on temporary network errors
    $shouldRetryOnException = $false
    $maxRetryCountOnException = 3

    do
    {
        try
        {
            return $Action.Invoke($requestOption)
        }
        catch
        {
            if ($_.Exception.InnerException -ne $null -And $_.Exception.InnerException.GetType() -Eq [System.TimeoutException] -And $maxRetryCountOnException -gt 0)
            {
                $shouldRetryOnException = $true
                $maxRetryCountOnException--
            }
            else
            {
                $shouldRetryOnException = $false
                throw
            }
        }
    }
    while ($shouldRetryOnException)

}

# function Get-BlobBytes

function Get-BlobBytes
{
    param(
        [Parameter(Mandatory=$true)]
        $Blob,
        [Parameter(Mandatory=$false)]
        [bool]$IsPremiumAccount = $false)

    # Base + blobname
    $blobSizeInBytes = 124 + $Blob.Name.Length * 2

    # Get size of metadata
    $metadataEnumerator=$Blob.ICloudBlob.Metadata.GetEnumerator()
    while($metadataEnumerator.MoveNext())
    {
        $blobSizeInBytes += 3 + $metadataEnumerator.Current.Key.Length + $metadataEnumerator.Current.Value.Length
    }

    if (!$IsPremiumAccount)
    {
        if($Blob.BlobType -eq [Microsoft.WindowsAzure.Storage.Blob.BlobType]::BlockBlob)
        {
            $blobSizeInBytes += 8
            # Default is Microsoft.WindowsAzure.Storage.Blob.BlockListingFilter.Committed. Need All
            $action = { param($requestOption) return $Blob.ICloudBlob.DownloadBlockList([Microsoft.WindowsAzure.Storage.Blob.BlockListingFilter]::All, $null, $requestOption) }                

            $blocks=Retry-OnRequest $action      

            if ($null -eq $blocks)
            {
                $blobSizeInBytes += $Blob.ICloudBlob.Properties.Length
            }
            else
            {
                $blocks | ForEach-Object { $blobSizeInBytes += $_.Length + $_.Name.Length }
            }  
        }
        elseif($Blob.BlobType -eq [Microsoft.WindowsAzure.Storage.Blob.BlobType]::PageBlob)
        {
            # It could cause server time out issue when trying to get page ranges of highly fragmented page blob 
            # Get page ranges in segment can mitigate chance of meeting such kind of server time out issue
            # See https://blogs.msdn.microsoft.com/windowsazurestorage/2012/03/26/getting-the-page-ranges-of-a-large-page-blob-in-segments/ for details.
            $pageRangesSegSize = 148 * 1024 * 1024L
            $totalSize = $Blob.ICloudBlob.Properties.Length
            $pageRangeSegOffset = 0
        
            $pageRangesTemp = New-Object System.Collections.ArrayList
        
            while ($pageRangeSegOffset -lt $totalSize)
            {
                $action = {param($requestOption) return $Blob.ICloudBlob.GetPageRanges($pageRangeSegOffset, $pageRangesSegSize, $null, $requestOption) }

                Retry-OnRequest $action | ForEach-Object { $pageRangesTemp.Add($_) }  | Out-Null
                $pageRangeSegOffset += $pageRangesSegSize
            }

            $pageRanges = New-Object System.Collections.ArrayList

            foreach ($pageRange in $pageRangesTemp)
            {
                if($lastRange -eq $Null)
                {
                    $lastRange = New-Object PageRange
                    $lastRange.StartOffset = $pageRange.StartOffset
                    $lastRange.EndOffset =  $pageRange.EndOffset
                }
                else
                {
                    if (($lastRange.EndOffset + 1) -eq $pageRange.StartOffset)
                    {
                        $lastRange.EndOffset = $pageRange.EndOffset
                    }
                    else
                    {
                        $pageRanges.Add($lastRange)  | Out-Null
                        $lastRange = New-Object PageRange
                        $lastRange.StartOffset = $pageRange.StartOffset
                        $lastRange.EndOffset =  $pageRange.EndOffset
                    }
                }
            }

            $pageRanges.Add($lastRange) | Out-Null
            $pageRanges |  ForEach-Object { 
                    $blobSizeInBytes += 12 + $_.EndOffset - $_.StartOffset 
                }
        }
        else
        {
            $blobSizeInBytes += $Blob.ICloudBlob.Properties.Length
        }
        return $blobSizeInBytes
    }
    else
    {
        $blobSizeInBytes += $Blob.ICloudBlob.Properties.Length
    }
    return $blobSizeInBytes
}

# function Get-ContainerBytes

function Get-ContainerBytes
{
    param(
        [Parameter(Mandatory=$true)]
        [Microsoft.WindowsAzure.Storage.Blob.CloudBlobContainer]$Container,
        [Parameter(Mandatory=$false)]
        [bool]$IsPremiumAccount = $false)

    # Base + name of container
    $containerSizeInBytes = 48 + $Container.Name.Length*2

    # Get size of metadata
    $metadataEnumerator = $Container.Metadata.GetEnumerator()
    while($metadataEnumerator.MoveNext())
    {
        $containerSizeInBytes += 3 + $metadataEnumerator.Current.Key.Length + $metadataEnumerator.Current.Value.Length
    }

    # Get size for SharedAccessPolicies
    $containerSizeInBytes += $Container.GetPermissions().SharedAccessPolicies.Count * 512

    # Calculate size of all blobs.
    $blobCount = 0
    $Token = $Null
    $MaxReturn = 5000

    do {
        $Blobs = Get-AzStorageBlob -Context $storageContext -Container $Container.Name -MaxCount $MaxReturn -ContinuationToken $Token
        if($Blobs -eq $Null) { break }

        #Set-StrictMode will cause Get-AzureStorageBlob returns result in different data types when there is only one blob
        if($Blobs.GetType().Name -eq "AzureStorageBlob")
        {
            $Token = $Null
        }
        else
        {
            $Token = $Blobs[$Blobs.Count - 1].ContinuationToken;
        }

        $Blobs | ForEach-Object {
                $blobSize = Get-BlobBytes $_ $IsPremiumAccount
                $containerSizeInBytes += $blobSize
                $blobCount++

                if(($blobCount % 1000) -eq 0)
                {
                    Write-Verbose("Counting {0} Sizing {1} " -f $blobCount, $containerSizeInBytes)
                }
            }
    }
    While ($Token -ne $Null)

    return @{ "containerSize" = $containerSizeInBytes; "blobCount" = $blobCount }
}

#Login-AzAccount -EnvironmentName AzureChinaCloud

$storageAccount = Get-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName -ErrorAction SilentlyContinue
if($storageAccount -eq $null)
{
    throw "The storage account specified does not exist in this subscription."
}

$storageContext = $storageAccount.Context

if (-not ([System.Management.Automation.PSTypeName]'PageRange').Type)
{
    $Source = "
        public class PageRange
        {
            public long StartOffset;
            public long EndOffset;
        }"
    Add-Type -TypeDefinition $Source
}

$containers = New-Object System.Collections.ArrayList
if($ContainerName.Length -ne 0)
{
    $container = Get-AzStorageContainer -Context $storageContext -Name $ContainerName -ErrorAction SilentlyContinue |
        ForEach-Object { $containers.Add($_) } | Out-Null
}
else
{
    Get-AzStorageContainer -Context $storageContext | ForEach-Object { $containers.Add($_) } | Out-Null
}

$sizeInBytes = 0
$IsPremiumAccount = ($storageAccount.Sku.Tier -eq "Premium")

if($containers.Count -gt 0)
{
    $containers | ForEach-Object {
        Write-Output("Calculating container {0} ..." -f $_.CloudBlobContainer.Name)
        $result = Get-ContainerBytes $_.CloudBlobContainer $IsPremiumAccount
        $sizeInBytes += $result.containerSize

        Write-Output("Container '{0}' with {1} blobs has a sizeof {2:F2} MB." -f $_.CloudBlobContainer.Name,$result.blobCount,($result.containerSize/1MB))
    }
}
else
{
    Write-Warning "No containers found to process in storage account '$StorageAccountName'."
}
```

## <a name="next-steps"></a>További lépések

- Lásd: [kiszámítása a Blob storage-tároló amelyek összméretén](../scripts/storage-blobs-container-calculate-size-powershell.md) egy egyszerű parancsfájlt, amely biztosítja a tároló méretének becslése.

- Azure Storage szolgáltatás számlázásának kapcsolatos további információkért lásd: [ismertetése Windows Azure Storage számlázási](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Az Azure PowerShell-modullal kapcsolatos további információkért lásd: [Azure PowerShell-dokumentáció](https://docs.microsoft.com/powershell/azure/overview).

- A további tárolási PowerShell szkriptminták annak [PowerShell-minták az Azure Storage](../blobs/storage-samples-blobs-powershell.md).
