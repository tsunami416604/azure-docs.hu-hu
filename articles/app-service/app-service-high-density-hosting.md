---
title: Nagy sűrűségű üzemeltetésének Azure App Service alkalmazás skálázással |} Microsoft Docs
description: Nagy sűrűségű üzemeltetésének Azure App Service
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
ms.openlocfilehash: 97e1efe34417c3bf2f23801b2112b718f55d3416
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2018
ms.locfileid: "36961933"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Nagy sűrűségű üzemeltetésének Azure App Service alkalmazás skálázással
Alapértelmezés szerint méretezni App Service apps skálázással a [App Service-csomag](azure-web-sites-web-hosting-plans-in-depth-overview.md) számítógépen futnak. Amikor több alkalmazás az App Service-csomag futnak, kiterjesztett feltünteti az alkalmazások a terv futtatja.

Engedélyezheti a *alkalmazásonkénti skálázás* , az App Service megtervezése szintjét. Méretezés egymástól függetlenül az App Service-csomag, amelyen az alkalmazás. Így az App Service-csomag is méretezhető 10 példányok, de az alkalmazás csak öt állítható be.

> [!NOTE]
> Alkalmazásonkénti skálázás csak érhető **szabványos**, **prémium**, **prémium V2** és **elszigetelt** tarifacsomag szükséges.
>

## <a name="per-app-scaling-using-powershell"></a>Alkalmazásonkénti skálázás a PowerShell használatával

Alkalmazásonkénti történő a skálázás tervek létrehozásakor a ```-perSiteScaling $true``` attribútumot a ```New-AzureRmAppServicePlan``` parancsmag

```powershell
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Egy meglévő App Service-csomag frissítése az alkalmazáson belüli skálázás: 

- a cél terv beolvasása ```Get-AzureRmAppServicePlan```
- helyileg a tulajdonság módosítása ```$newASP.PerSiteScaling = $true```
- a módosításokat vissza az Azure-bA küldése ```Set-AzureRmAppServicePlan``` 

```powershell
# Get the new App Service Plan and modify the "PerSiteScaling" property.
$newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
$newASP

#Modify the local copy to use "PerSiteScaling" property.
$newASP.PerSiteScaling = $true
$newASP
    
#Post updated app service plan back to azure
Set-AzureRmAppServicePlan $newASP
```

Az alkalmazás szintjén adja meg az alkalmazás használhatja az App Service-csomag példányok.

Az alábbi példában az alkalmazás korlátozódik két példányt, függetlenül attól, hány példányban kimenő alkalmazkodnak az alapul szolgáló app service-csomag.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` nem azonos a `$newapp.MaxNumberOfWorkers`. Alkalmazáson belüli méretezési használ `$newapp.SiteConfig.NumberOfWorkers` meghatározásához az alkalmazás méretezési jellemzőit.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Alkalmazáson belüli skálázás Azure Resource Manager használatával

A következő Azure Resource Manager sablonnal hoz létre:

- App Service-csomagot, amely 10 példányok horizontálisan
- egy alkalmazás, amely konfigurálva van egy legfeljebb öt példány számára.

Az App Service-csomag állítja a **PerSiteScaling** tulajdonság igaz értékre `"perSiteScaling": true`. Az alkalmazás beállítja a **munkavállalók száma** 5 használandó `"properties": { "numberOfWorkers": "5" }`.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="recommended-configuration-for-high-density-hosting"></a>Ajánlott konfiguráció nagy sűrűségű üzemeltetéséhez
Egy alkalmazás skálázás van olyan szolgáltatás, amely mindkét globális Azure-régiók engedélyezve van, és [App Service Environment-környezetek](environment/app-service-app-service-environment-intro.md). Azonban ajánlott, hogy kihasználják a speciális funkciók és kapacitás nagyobb készletek App Service Environment-környezetek felhasználva.  

Ezek a lépések segítségével állítsa be az alkalmazások közötti üzemeltetési nagy sűrűségű:

1. Az App Service Environment-környezet konfigurálása, és válassza ki a feldolgozókészleten, amely a nagy sűrűségű üzemeltetés van kijelölve.
1. Egy App Service-csomag létrehozása, és a méret úgy, hogy a munkavégző készletét használja a rendelkezésre álló kapacitásból.
1. Állítsa be a `PerSiteScaling` jelző IGAZ értékű, az App Service-csomag.
1. Új alkalmazások létrehozása és, hogy az App Service-csomag rendelve a **numberOfWorkers** tulajdonsága **1**. Ezzel a konfigurációval a munkavégző készlettől lehetséges legmagasabb sűrűség adja eredményül.
1. Dolgozók számának igény szerint további erőforrások megadását alkalmazásonkénti egymástól függetlenül konfigurálhatók. Példa:
    - A nagy igénybevételnek kitett alkalmazás állíthatja be **numberOfWorkers** való **3** kell rendelkeznie az adott alkalmazáshoz több feldolgozási kapacitás. 
    - Alacsony használható alkalmazások állítania **numberOfWorkers** való **1**.

## <a name="next-steps"></a>További lépések

- [Az Azure App Service-csomagok részletes áttekintése](azure-web-sites-web-hosting-plans-in-depth-overview.md)
- [Az App Service Environment bemutatása](environment/app-service-app-service-environment-intro.md)
