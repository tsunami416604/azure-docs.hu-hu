---
title: "A PowerShell segítségével az Azure CDN kezelése |} Microsoft Docs"
description: "Útmutató: Azure CDN kezelése az Azure PowerShell-parancsmagok segítségével."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5bd2eed7b34cafa43e8f38279890405d4ae55568
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="manage-azure-cdn-with-powershell"></a>A PowerShell segítségével az Azure CDN kezelése
PowerShell biztosítja a leginkább rugalmas módszer kezeli az Azure CDN-profil és a végpontok.  Használhatja PowerShell interaktív módon vagy parancsfájlok írásával felügyeleti feladatok automatizálására.  Ez az oktatóanyag azt mutatja be, több leggyakoribb feladatokat is elérheti a PowerShell használatával kezeli az Azure CDN-profil és a végpontok.

## <a name="prerequisites"></a>Előfeltételek
Az Azure CDN-profil és a végpontok kezelése a PowerShell használatával, az Azure PowerShell modul telepítve kell rendelkeznie.  Azure PowerShell telepítése és csatlakozás az Azure használatával további információt a `Login-AzureRmAccount` parancsmag, lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Jelentkezzen be az `Login-AzureRmAccount` Azure PowerShell-parancsmagok végrehajtás előtt.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Az Azure CDN parancsmagok listázása
Az Azure CDN-parancsmaggal is listázhatja az összes a `Get-Command` parancsmag.

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

## <a name="getting-help"></a>Segítségkérés
Segítséget kaphat a parancsmagok használatával, a `Get-Help` parancsmag.  `Get-Help`használati és szintaxist, és opcionálisan a példákat mutat.

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

## <a name="listing-existing-azure-cdn-profiles"></a>Meglévő Azure CDN-profilra listázása
A `Get-AzureRmCdnProfile` paraméterek nélkül parancsmag beolvassa a meglévő CDN profilok.

```powershell
Get-AzureRmCdnProfile
```

A kimeneti számbavétel parancsmagjainak átirányítható.

```powershell
# Output the name of all profiles on this subscription.
Get-AzureRmCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzureRmCdnProfile | Where-Object { $_.Sku.Name -eq "StandardVerizon" }
```

Egyetlen profilhoz a profil nevét és az erőforrás csoport megadásával is visszaadják.

```powershell
Get-AzureRmCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> Akkor lehetséges, hogy ugyanazzal a névvel, több CDN-profilra, feltéve, hogy a különböző erőforráscsoportokra vannak.  Kihagyása a `ResourceGroupName` paraméter egyező nevű minden profilban adja vissza.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Listaelem meglévő CDN-végpontok
`Get-AzureRmCdnEndpoint`lekérheti az egyes endpoint vagy a profilok a végpontok.  

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
`New-AzureRmCdnProfile`és `New-AzureRmCdnEndpoint` hozhatók létre a CDN-profil és a végpontok.

```powershell
# Create a new profile
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US"

# Create a new endpoint
New-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US" | New-AzureRmCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Végpont név foglaltságának ellenőrzése
`Get-AzureRmCdnEndpointNameAvailability`Visszaad egy objektumot, amely azt jelzi, ha egy végpont nevének érhető el.

```powershell
# Retrieve availability
$availability = Get-AzureRmCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Egyéni tartomány hozzáadása
`New-AzureRmCdnCustomDomain`egyéni tartománynév hozzáadása egy meglévő végpontot.

> [!IMPORTANT]
> Be kell állítania a CNAME REKORDOT a DNS-szolgáltatónál leírtak [hogyan egyéni tartomány leképezése a Content Delivery Network (CDN) végpont](cdn-map-content-to-custom-domain.md).  A végpontot a módosítása előtt olvassa el a leképezés tesztelheti `Test-AzureRmCdnCustomDomain`.
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
`Set-AzureRmCdnEndpoint`módosítja egy végpontot.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzureRmCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Kiürítése/előtti-loading CDN eszközök
`Unpublish-AzureRmCdnEndpointContent`pon gyorsítótárazott eszközök, amíg a `Publish-AzureRmCdnEndpointContent` előre betölti az eszközök támogatott végpontokon.

```powershell
# Purge some assets.
Unpublish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Unpublish-AzureRmCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Indítása/leállítása CDN-végpontok
`Start-AzureRmCdnEndpoint`és `Stop-AzureRmCdnEndpoint` indítása és leállítása az egyes végpontok vagy csoportok végpontok is használható.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Stop-AzureRmCdnEndpoint

# Start all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Start-AzureRmCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>CDN-erőforrások törlése
`Remove-AzureRmCdnProfile`és `Remove-AzureRmCdnEndpoint` segítségével távolítsa el a profilok és a végpontok.

```powershell
# Remove a single endpoint
Remove-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzureRmCdnEndpoint | Remove-AzureRmCdnEndpoint -Force

# Remove a single profile
Remove-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Következő lépések
Ismerje meg, hogyan automatizálhatja az Azure CDN-t a [.NET](cdn-app-dev-net.md) vagy a [Node.js](cdn-app-dev-node.md) segítségével.

CDN-funkciókkal kapcsolatos további tudnivalókért lásd: [CDN áttekintésével](cdn-overview.md).

