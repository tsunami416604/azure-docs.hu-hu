---
title: Alkalmazásonkénti méretezés a nagy sűrűségű tárhelyért
description: Az App Service-csomagoktól függetlenül méretezheti az alkalmazásokat, és optimalizálhatja a csomagban lévő kibővített példányokat.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: f1ca4958fe2608d0c040ef5b93827a7e71a4151c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672350"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Nagy sűrűségű üzemeltetési szolgáltatás az Azure App Service-ben alkalmazásonkénti méretezéssel

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az App Service használatakor az appok méretezése az [App Service-csomag](overview-hosting-plans.md) azok futtatásához. Ha több alkalmazás fut ugyanabban az App Service-csomagban, minden egyes kibővített példány futtatja a csomag összes alkalmazását.

*Az alkalmazásonkénti skálázás* az App Service-csomag szintjén engedélyezhető, hogy lehetővé tegye az alkalmazás méretezését az azt tartalmazó App Service-csomagtól függetlenül. Ily módon egy App Service-csomag 10 példányra méretezhető, de egy alkalmazás csak öt használatára állítható be.

> [!NOTE]
> Az alkalmazásonkénti skálázás csak **standard,** **prémium,** **prémium v2** és **elkülönített** tarifacsomagok esetén érhető el.
>

Az alkalmazások az elérhető App Service-csomaghoz vannak rendelve, a legjobb erőfeszítéssel történő hozzáhatással a példányok közötti egyenletes elosztáshoz. Bár az egyenletes elosztás nem garantált, a platform gondoskodik arról, hogy ugyanazon alkalmazás két példánya ne legyen ugyanazon az App Service-csomagpéldányon.

A platform nem támaszkodik metrikák dönt a dolgozói elosztás. Az alkalmazások csak akkor kerülnek kiegyensúlyozásra, ha példányokat adhozzá, vagy eltávolítják az App Service-csomagból.

## <a name="per-app-scaling-using-powershell"></a>Alkalmazásonkénti méretezés a PowerShell használatával

Hozzon létre egy tervet az alkalmazásonkénti skálázás a paraméter a ```-PerSiteScaling $true``` ```New-AzAppServicePlan``` parancsmagba.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Engedélyezze az alkalmazásonkénti skálázást egy `-PerSiteScaling $true` meglévő ```Set-AzAppServicePlan``` App Service-csomaggal a paraméter nek a parancsmagba való átadva.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Az alkalmazás szintjén konfigurálja az alkalmazás által az App Service-csomagban használható példányok számát.

Az alábbi példában az alkalmazás két példányra korlátozódik, függetlenül attól, hogy az alapul szolgáló alkalmazásszolgáltatás-terv hány példányra skálázódik.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers`eltér a `$newapp.MaxNumberOfWorkers`tól. Az alkalmazásonkénti skálázás az alkalmazás méretezési jellemzőinek meghatározására használja. `$newapp.SiteConfig.NumberOfWorkers`

## <a name="per-app-scaling-using-azure-resource-manager"></a>Alkalmazásonkénti méretezés az Azure Resource Manager használatával

A következő Azure Resource Manager-sablon tágan:

- 10 példányra kiskálázott App Service-csomag
- egy alkalmazás, amely úgy van beállítva, hogy legfeljebb öt példányra méretezhető.

Az App Service-csomag a **PerSiteScaling** tulajdonságot true `"perSiteScaling": true`értékre állítja. Az alkalmazás a használni hozandó dolgozók `"properties": { "numberOfWorkers": "5" }` **számát** 5-re állítja.

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

## <a name="recommended-configuration-for-high-density-hosting"></a>Ajánlott konfiguráció a nagy sűrűségű tárhelyhez

Alkalmazásonkénti skálázás olyan funkció, amely mind a globális Azure-régiókban, mind az [App Service-környezetekben engedélyezve](environment/app-service-app-service-environment-intro.md)van. Az ajánlott stratégia azonban az App Service-környezetek használata a speciális funkciók és a nagyobb App Service-csomag kapacitásának kihasználásához.  

Az alábbi lépésekkel konfigurálhatja alkalmazásai nagy sűrűségű üzemeltetését:

1. Jelöljön ki egy App Service-csomagot nagy sűrűségű csomagként, és méretezzük ki a kívánt kapacitásra.
1. Állítsa `PerSiteScaling` a jelzőt true értékre az App Service-csomagon.
1. Új alkalmazások jönnek létre és vannak hozzárendelve az adott App Service-csomaghoz, amelynek **numberOfWorkers** tulajdonsága **1.**
   - Ezzel a konfigurációval a lehető legnagyobb sűrűséget eredményezi.
1. A dolgozók száma alkalmazásonként egymástól függetlenül konfigurálható további erőforrások biztosításához, ha szükséges. Példa:
   - A nagy használatú alkalmazások beállíthatja **numberOfWorkers** **3** több feldolgozási kapacitást az adott alkalmazáshoz.
   - Az alacsony használatú alkalmazások **a numberOfWorkers-et** **1-re**állítanák.

## <a name="next-steps"></a>További lépések

- [Az Azure App Service-csomagok részletes áttekintése](overview-hosting-plans.md)
- [Az App Service Environment bemutatása](environment/app-service-app-service-environment-intro.md)
