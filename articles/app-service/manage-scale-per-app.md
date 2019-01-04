---
title: Nagy sűrűségű üzemeltetés alkalmazásonkénti használatával méretezés – az Azure App Service |} A Microsoft Docs
description: Nagy sűrűségű üzemeltetés az Azure App Service szolgáltatásban
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/22/2018
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 49b5978fd647a4667503676528120a36495021c6
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731267"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Nagy sűrűségű üzemeltetés az Azure App Service alkalmazás skálázással
Alapértelmezés szerint méretezése App Service-alkalmazások történik méretezés a [App Service-csomag](overview-hosting-plans.md) számítógépen futnak. Amikor több alkalmazás futtatása az App Service-csomag, horizontálisan felskálázott példányonként minden alkalmazás fut a terv.

Engedélyezheti a *alkalmazásonkénti skálázás* , az App Service csomag szintje. Méretezés egy alkalmazást az App Service-csomag, amelyen azt alkalmazástól függetlenül. Ezzel a módszerrel App Service-csomag 10 példányra skálázhatók, de az alkalmazás csak öt beállítható.

> [!NOTE]
> Alkalmazásonkénti skálázás csak érhető el **Standard**, **prémium**, **prémium V2** és **elkülönített** díjcsomagok árából.
>

## <a name="per-app-scaling-using-powershell"></a>Az egyes alkalmazások méretezése a PowerShell használatával

Hozzon létre egy csomagot az alkalmazásonkénti ad méretezés a ```-PerSiteScaling $true``` paramétert a ```New-AzureRmAppServicePlan``` parancsmagot.

```powershell
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Alkalmazásonkénti ad egy meglévő App Service-csomag a skálázás engedélyezése a `-PerSiteScaling $true` paramétert a ```Set-AzureRmAppServicePlan``` parancsmagot.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Az alkalmazás szintjén konfigurálhatja az alkalmazás használhatja az App Service-csomagban lévő példányok száma.

Az alábbi példában az alkalmazás két példánya, függetlenül az alapul szolgáló app service-csomag elvégzi a horizontális felskálázást, hány példányt korlátozva.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` eltérő `$newapp.MaxNumberOfWorkers`. Alkalmazásonkénti skálázásához használt `$newapp.SiteConfig.NumberOfWorkers` meghatározni az alkalmazás méretezési jellemzőit.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Alkalmazásonkénti méretezése az Azure Resource Managerrel

Az alábbi Azure Resource Manager-sablont hoz létre:

- App Service-csomag, amely 10 példányra horizontálisan felskálázott
- egy alkalmazást, amely konfigurálva van egy legfeljebb öt példányban méretezhető.

Az App Service-csomag beállítása a **PerSiteScaling** tulajdonság igaz értékre `"perSiteScaling": true`. Az alkalmazás beállítása a **feldolgozók száma** 5 segítségével `"properties": { "numberOfWorkers": "5" }`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Ajánlott konfiguráció, a nagy sűrűségű üzemeltetés
Egy alkalmazás méretezése funkciója engedélyezve van, mindkét globális Azure-régióban és [App Service Environment-környezetek](environment/app-service-app-service-environment-intro.md). Azonban a javasolt stratégia az App Service Environment-környezetek használata a speciális funkciókat és a nagyobb kapacitás-készleteket.  

Kövesse az alábbi lépéseket az alkalmazások üzemeltetéséhez nagy sűrűségű konfigurálása:

1. Az App Service Environment konfigurálása, és válassza ki, amely a nagy sűrűségű üzemeltetés feldolgozókészletek.
2. Hozzon létre egy egyetlen App Service-csomagot, és skálázza fel a rendelkezésre álló kapacitás a feldolgozókészlet használata.
3. Állítsa be a `PerSiteScaling` jelzőt igaz értékre az App Service-csomag.
4. Új alkalmazások létrehozása és az App Service-csomag rendelve a **numberOfWorkers** tulajdonság **1**. A munkavégző készletét. a lehetséges legnagyobb kapacitású használatával Ez a konfiguráció alapján.
5. Feldolgozók száma egymástól függetlenül konfigurálhatók az egyes alkalmazások igény szerint további erőforrások megadását. Példa:
    - Egy sokak által használt alkalmazás állíthatja **numberOfWorkers** való **3** rendelkeznie, amelyet az alkalmazás további feldolgozási kapacitás. 
    - Alacsony használható alkalmazások állíthatja **numberOfWorkers** való **1**.

## <a name="next-steps"></a>További lépések

- [Az Azure App Service díjcsomagjainak részletes áttekintése](overview-hosting-plans.md)
- [Az App Service Environment bemutatása](environment/app-service-app-service-environment-intro.md)
