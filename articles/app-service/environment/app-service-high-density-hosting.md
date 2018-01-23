---
title: "Nagy sűrűségű üzemeltetésének Azure App Service szolgáltatásban |} Microsoft Docs"
description: "Nagy sűrűségű üzemeltetésének Azure App Service"
author: btardif
manager: erikre
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 22/01/2018
ms.author: byvinyal
ms.openlocfilehash: 2ffffd3cc9f5c59f74f71d6d7d31c5ea615d11f4
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="high-density-hosting-on-azure-app-service"></a>Nagy sűrűségű üzemeltetésének Azure App Service
App Service használata esetén a rendszer leválasztja az alkalmazást a két fogalom által lefoglalt kapacitás:

* **Az alkalmazás:** jelenti. az alkalmazás és a futtatókörnyezet konfigurációját. Például a .NET verzióját, amelyet a futtatókörnyezet kell, az alkalmazás beállításait tartalmazza.
* **Az App Service-csomag:** határozza meg a kapacitás, a rendelkezésre álló készlet és a Helység mezőben az alkalmazás. Nagy (négy magok) számítógép, a négy példányt, a prémium szolgáltatások, az USA keleti régiója, előfordulhat, hogy lehet jellemzőit.

Az alkalmazások mindig kapcsolódik az App Service-csomag, de az App Service-csomag egy vagy több alkalmazás kapacitást biztosít.

Ennek eredményeképpen a platform rugalmasságot biztosít, és egyetlen meghatározott alkalmazás elkülönítése, vagy ossza meg az App Service-csomag erőforrások megosztása több alkalmazásokkal rendelkeznek.

Azonban amikor több alkalmazás az App Service-csomag megosztásához az alkalmazás egy példánya fut, hogy az alkalmazásszolgáltatási csomag összes példányán.

## <a name="per-app-scaling"></a>Egy alkalmazás skálázás
*Egy alkalmazás skálázás* egy szolgáltatás, amely az App Service-csomag szintjén engedélyezhető, és alkalmazásonként használja.

Alkalmazásonkénti skálázás arányosan egymástól függetlenül az App Service-csomag, amelyen az alkalmazás. Így az App Service-csomag is méretezhető 10 példányok, de az alkalmazás csak öt állítható be.

   >[!NOTE]
   >/-Alkalmazás-méretezés, akkor csak a **szabványos**, **prémium**, **prémium V2** és **elszigetelt** SKU App Service-csomagok
   >

### <a name="per-app-scaling-using-powershell"></a>Alkalmazásonkénti skálázás a PowerShell használatával

Hozzon létre egy csomagot, mint a konfigurált egy */ alkalmazás skálázás* történő a terv a ```-perSiteScaling $true``` attribútumot a ```New-AzureRmAppServicePlan``` parancsmag

```
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Ha azt szeretné, a szolgáltatás használatához egy meglévő App Service-csomag frissítése: 

- a cél terv beolvasása```Get-AzureRmAppServicePlan```
- helyileg a tulajdonság módosítása```$newASP.PerSiteScaling = $true```
- a módosításokat vissza az Azure-bA küldése```Set-AzureRmAppServicePlan``` 

```
# Get the new App Service Plan and modify the "PerSiteScaling" property.
$newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
$newASP

#Modify the local copy to use "PerSiteScaling" property.
$newASP.PerSiteScaling = $true
$newASP
    
#Post updated app service plan back to azure
Set-AzureRmAppServicePlan $newASP
```

Az alkalmazás szintjén adja meg az alkalmazás használhatja az app service-csomag példányok.

Az alábbi példában az alkalmazás korlátozódik két példányt, függetlenül attól, hány példányban kimenő alkalmazkodnak az alapul szolgáló app service-csomag.

```
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> $newapp. SiteConfig.NumberOfWorkers $newapp eltér. MaxNumberOfWorkers. Alkalmazásonkénti $newapp skálázás használja. A skála jellemzőit az alkalmazás SiteConfig.NumberOfWorkers.

### <a name="per-app-scaling-using-azure-resource-manager"></a>Egy alkalmazás skálázás Azure Resource Manager használatával

A következő *Azure Resource Manager sablon* hoz létre:

- App Service-csomagot, amely 10 példányok horizontálisan
- egy alkalmazás, amely konfigurálva van egy legfeljebb öt példány számára.

Az App Service-csomag állítja a **PerSiteScaling** tulajdonság igaz értékre ```"perSiteScaling": true```. Az alkalmazás beállítja a **munkavállalók száma** 5 használandó ```"properties": { "numberOfWorkers": "5" }```.

```
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
Egy alkalmazás skálázás van olyan szolgáltatás, amely globális Azure-régiók és az App Service Environment-környezetek is engedélyezve van. Azonban ajánlott, hogy kihasználják a speciális funkciók és kapacitás nagyobb készletek App Service Environment-környezetek felhasználva.  

Ezek a lépések segítségével állítsa be az alkalmazások közötti üzemeltetési nagy sűrűségű:

1. Az App Service Environment-környezet konfigurálása, és válassza ki a feldolgozókészleten, amely a nagy sűrűségű üzemeltetés van kijelölve.
1. Egy App Service-csomag létrehozása, és a méret úgy, hogy a munkavégző készletét használja a rendelkezésre álló kapacitásból.
1. Értékre van állítva a PerSiteScaling jelző igaz az App Service-csomag.
1. Új alkalmazások létrehozása és, hogy az App Service-csomag rendelve a **numberOfWorkers** tulajdonsága **1**. Ezzel a konfigurációval a munkavégző készlettől lehetséges legmagasabb sűrűség adja eredményül.
1. Dolgozók számának igény szerint további erőforrások megadását alkalmazásonkénti egymástól függetlenül konfigurálhatók. Példa:
    - A nagy igénybevételnek kitett alkalmazás állíthatja be **numberOfWorkers** való **3** kell rendelkeznie az adott alkalmazáshoz több feldolgozási kapacitás. 
    - Alacsony használható alkalmazások állítania **numberOfWorkers** való **1**.

## <a name="next-steps"></a>További lépések

- [Az Azure App Service-csomagok részletes áttekintése](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
- [Az App Service Environment bemutatása](app-service-app-service-environment-intro.md)
