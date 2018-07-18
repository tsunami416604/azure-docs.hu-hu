---
title: A PowerShell-lel az Azure CDN szolgáltatás felügyelete |} A Microsoft Docs
description: 'Útmutató: Azure CDN szolgáltatás felügyelete az Azure PowerShell-parancsmagok használatával.'
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2018
ms.author: mazha
ms.openlocfilehash: 15feb7b1d2873bc3f088eaad78079df2e063d73b
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114072"
---
# <a name="manage-azure-cdn-with-powershell"></a>A PowerShell-lel az Azure CDN szolgáltatás felügyelete
PowerShell biztosít a legrugalmasabb módszer az Azure CDN-profilok és a végpontok kezeléséhez.  Használhatja PowerShell interaktív módon vagy parancsfájlok írásával felügyeleti feladatainak automatizálását.  Ez az oktatóanyag bemutatja néhány leggyakoribb feladatokat végezheti el a PowerShell-lel az Azure CDN-profilok és a végpontok kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek
Az Azure CDN-profilok és a végpontok kezelése a PowerShell használatával, az Azure PowerShell-modul telepítve kell rendelkeznie.  Az Azure PowerShell telepítése és csatlakozás az Azure-bA a `Connect-AzureRmAccount` parancsmagot, lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).

> [!IMPORTANT]
> Be kell jelentkeznie `Connect-AzureRmAccount` Azure PowerShell-parancsmagok is végrehajtása előtt.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Az Azure CDN-parancsmagok listázása
Listázhatja az összes az Azure CDN-parancsmagok használatával a `Get-Command` parancsmagot.

```text
PS C:\> Get-Command -Module AzureRM.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpointNameAvailability             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfileSsoUrl                        2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Publish-AzureRmCdnEndpointContent                  2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnCustomDomain                      2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnEndpoint                          2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnProfile                           2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Start-AzureRmCdnEndpoint                           2.0.0      AzureRm.Cdn
Cmdlet          Stop-AzureRmCdnEndpoint                            2.0.0      AzureRm.Cdn
Cmdlet          Test-AzureRmCdnCustomDomain                        2.0.0      AzureRm.Cdn
Cmdlet          Unpublish-AzureRmCdnEndpointContent                2.0.0      AzureRm.Cdn
```

## <a name="getting-help"></a>Segítség kérése
Az a parancsmagok használatával is kaphat segítséget a `Get-Help` parancsmagot.  `Get-Help` használat és szintaxist biztosít, és szükség esetén a példákat mutat be.

```text
PS C:\> Get-Help Get-AzureRmCdnProfile

NAME
    Get-AzureRmCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzureRmCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzureRmCdnProfile -examples".
    For more information, type: "get-help Get-AzureRmCdnProfile -detailed".
    For technical information, type: "get-help Get-AzureRmCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Meglévő Azure CDN-profilok listázása
A `Get-AzureRmCdnProfile` paraméterek nélkül a parancsmag lekéri az összes a meglévő CDN-profilok.

```powershell
Get-AzureRmCdnProfile
```

Ez a kimenet enumerálás parancsmagjai is irányíthatja át.

```powershell
# Output the name of all profiles on this subscription.
Get-AzureRmCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzureRmCdnProfile | Where-Object { $_.Sku.Name -eq "Standard_Verizon" }
```

Bármikor visszatérhet is egyetlen profilhoz a profil nevére és erőforráscsoportjára csoport megadásával.

```powershell
Get-AzureRmCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> Fontos lehetséges, hogy ugyanazzal a névvel, több CDN-profilt, feltéve, hogy eltérő erőforráscsoportokban vannak.  Felsorolhatja az `ResourceGroupName` paraméter visszaadja az összes profil egyező nevű.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Listaelem meglévő CDN-végpontok
`Get-AzureRmCdnEndpoint` lekérheti az egyes végpontok vagy a profilok a végpontok.  

```powershell
# Get a single endpoint.
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>CDN-profilok és a végpontok létrehozása
`New-AzureRmCdnProfile` és `New-AzureRmCdnEndpoint` CDN-profilok és a végpontok létrehozásához használhatók. A következő termékváltozatok támogatottak:
- Standard_Verizon
- Premium_Verizon
- Custom_Verizon
- Standard_Akamai
- Standard_ChinaCdn

> [!NOTE]
> A Standard_Microsoft Termékváltozat nem támogatott, bár előzetes verzióban érhető el.

```powershell
# Create a new profile
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US"

# Create a new endpoint
New-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US" | New-AzureRmCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Végpont neve rendelkezésre állásának ellenőrzése
`Get-AzureRmCdnEndpointNameAvailability` végpont neve elérhető-e megjelölve objektumot adja vissza.

```powershell
# Retrieve availability
$availability = Get-AzureRmCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Egyéni tartomány hozzáadása
`New-AzureRmCdnCustomDomain` egyéni tartománynév hozzáadása egy meglévő végpontot.

> [!IMPORTANT]
> Be kell állítania a CNAME REKORDOT a DNS-szolgáltatónál leírtak szerint [egyéni tartomány leképezése a Content Delivery Network (CDN) végpontjára hogyan](cdn-map-content-to-custom-domain.md).  A leképezés a végpontot a módosítása előtt tesztelheti `Test-AzureRmCdnCustomDomain`.
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzureRmCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzureRmCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>A végpont módosítása
`Set-AzureRmCdnEndpoint` módosítja egy meglévő végpontot.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzureRmCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Végleges törlés/előtti-loading CDN eszközök
`Unpublish-AzureRmCdnEndpointContent` pon gyorsítótárazott eszközök, miközben `Publish-AzureRmCdnEndpointContent` előre betölti az eszközök a támogatott végpontok.

```powershell
# Purge some assets.
Unpublish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Unpublish-AzureRmCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Indítása/leállítása CDN-végpontok
`Start-AzureRmCdnEndpoint` és `Stop-AzureRmCdnEndpoint` segítségével elindíthatja és leállíthatja az egyes végpontok vagy a végpontok csoportjait.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Stop-AzureRmCdnEndpoint

# Start all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Start-AzureRmCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>CDN-erőforrások törlése
`Remove-AzureRmCdnProfile` és `Remove-AzureRmCdnEndpoint` távolítsa el a profilok és a végpontok segítségével.

```powershell
# Remove a single endpoint
Remove-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzureRmCdnEndpoint | Remove-AzureRmCdnEndpoint -Force

# Remove a single profile
Remove-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan automatizálhatja az Azure CDN-t a [.NET](cdn-app-dev-net.md) vagy a [Node.js](cdn-app-dev-node.md) segítségével.

A CDN-funkciókkal kapcsolatos tudnivalókért lásd: [CDN áttekintése](cdn-overview.md).

