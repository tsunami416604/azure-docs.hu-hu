---
title: A PowerShell-lel az Azure CDN szolgáltatás felügyelete |} A Microsoft Docs
description: 'Útmutató: Azure CDN szolgáltatás felügyelete az Azure PowerShell-parancsmagok használatával.'
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: magattus
ms.openlocfilehash: 5dc05b08e84decf958481d19b9cfb820947fae78
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237044"
---
# <a name="manage-azure-cdn-with-powershell"></a>A PowerShell-lel az Azure CDN szolgáltatás felügyelete
PowerShell biztosít a legrugalmasabb módszer az Azure CDN-profilok és a végpontok kezeléséhez.  Használhatja PowerShell interaktív módon vagy parancsfájlok írásával felügyeleti feladatainak automatizálását.  Ez az oktatóanyag bemutatja néhány leggyakoribb feladatokat végezheti el a PowerShell-lel az Azure CDN-profilok és a végpontok kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az Azure CDN-profilok és a végpontok kezelése a PowerShell használatával, az Azure PowerShell-modul telepítve kell rendelkeznie.  Az Azure PowerShell telepítése és csatlakozás az Azure-bA a `Connect-AzAccount` parancsmagot, lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).

> [!IMPORTANT]
> Be kell jelentkeznie `Connect-AzAccount` Azure PowerShell-parancsmagok is végrehajtása előtt.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Az Azure CDN-parancsmagok listázása
Listázhatja az összes az Azure CDN-parancsmagok használatával a `Get-Command` parancsmagot.

```text
PS C:\> Get-Command -Module Az.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointNameAvailability             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSsoUrl                        2.0.0      Az.Cdn
Cmdlet          New-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          New-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          New-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Publish-AzCdnEndpointContent                  2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnCustomDomain                      2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnEndpoint                          2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnProfile                           2.0.0      Az.Cdn
Cmdlet          Set-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Set-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Set-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Start-AzCdnEndpoint                           2.0.0      Az.Cdn
Cmdlet          Stop-AzCdnEndpoint                            2.0.0      Az.Cdn
Cmdlet          Test-AzCdnCustomDomain                        2.0.0      Az.Cdn
Cmdlet          Unpublish-AzCdnEndpointContent                2.0.0      Az.Cdn
```

## <a name="getting-help"></a>Segítség kérése
Az a parancsmagok használatával is kaphat segítséget a `Get-Help` parancsmagot.  `Get-Help` használat és szintaxist biztosít, és szükség esetén a példákat mutat be.

```text
PS C:\> Get-Help Get-AzCdnProfile

NAME
    Get-AzCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzCdnProfile -examples".
    For more information, type: "get-help Get-AzCdnProfile -detailed".
    For technical information, type: "get-help Get-AzCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Meglévő Azure CDN-profilok listázása
A `Get-AzCdnProfile` paraméterek nélkül a parancsmag lekéri az összes a meglévő CDN-profilok.

```powershell
Get-AzCdnProfile
```

Ez a kimenet enumerálás parancsmagjai is irányíthatja át.

```powershell
# Output the name of all profiles on this subscription.
Get-AzCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzCdnProfile | Where-Object { $_.Sku.Name -eq "Standard_Verizon" }
```

Bármikor visszatérhet is egyetlen profilhoz a profil nevére és erőforráscsoportjára csoport megadásával.

```powershell
Get-AzCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> Fontos lehetséges, hogy ugyanazzal a névvel, több CDN-profilt, feltéve, hogy eltérő erőforráscsoportokban vannak.  Felsorolhatja az `ResourceGroupName` paraméter visszaadja az összes profil egyező nevű.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Listaelem meglévő CDN-végpontok
`Get-AzCdnEndpoint` lekérheti az egyes végpontok vagy a profilok a végpontok.  

```powershell
# Get a single endpoint.
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzCdnProfile | Get-AzCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzCdnProfile | Get-AzCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>CDN-profilok és a végpontok létrehozása
`New-AzCdnProfile` és `New-AzCdnEndpoint` CDN-profilok és a végpontok létrehozásához használhatók. A következő termékváltozatok támogatottak:
- Standard_Verizon
- Premium_Verizon
- Custom_Verizon
- Standard_Akamai
- Standard_Microsoft
- Standard_ChinaCdn

```powershell
# Create a new profile
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US"

# Create a new endpoint
New-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US" | New-AzCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Végpont neve rendelkezésre állásának ellenőrzése
`Get-AzCdnEndpointNameAvailability` végpont neve elérhető-e megjelölve objektumot adja vissza.

```powershell
# Retrieve availability
$availability = Get-AzCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Egyéni tartomány hozzáadása
`New-AzCdnCustomDomain` egyéni tartománynév hozzáadása egy meglévő végpontot.

> [!IMPORTANT]
> Be kell állítania a CNAME REKORDOT a DNS-szolgáltatónál leírtak szerint [egyéni tartomány leképezése a Content Delivery Network (CDN) végpontjára hogyan](cdn-map-content-to-custom-domain.md).  A leképezés a végpontot a módosítása előtt tesztelheti `Test-AzCdnCustomDomain`.
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>A végpont módosítása
`Set-AzCdnEndpoint` módosítja egy meglévő végpontot.

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Végleges törlés/előtti-loading CDN eszközök
`Unpublish-AzCdnEndpointContent` pon gyorsítótárazott eszközök, miközben `Publish-AzCdnEndpointContent` előre betölti az eszközök a támogatott végpontok.

```powershell
# Purge some assets.
Unpublish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzCdnProfile | Get-AzCdnEndpoint | Unpublish-AzCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Indítása/leállítása CDN-végpontok
`Start-AzCdnEndpoint` és `Stop-AzCdnEndpoint` segítségével elindíthatja és leállíthatja az egyes végpontok vagy a végpontok csoportjait.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Stop-AzCdnEndpoint

# Start all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Start-AzCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>CDN-erőforrások törlése
`Remove-AzCdnProfile` és `Remove-AzCdnEndpoint` távolítsa el a profilok és a végpontok segítségével.

```powershell
# Remove a single endpoint
Remove-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzCdnEndpoint | Remove-AzCdnEndpoint -Force

# Remove a single profile
Remove-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan automatizálhatja az Azure CDN-t a [.NET](cdn-app-dev-net.md) vagy a [Node.js](cdn-app-dev-node.md) segítségével.

A CDN-funkciókkal kapcsolatos tudnivalókért lásd: [CDN áttekintése](cdn-overview.md).

