---
title: "Webes alkalmazás Klónozás PowerShell használatával"
description: "Útmutató a webalkalmazások a PowerShell használatával új webalkalmazások klónozását."
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.openlocfilehash: 30817a1a6a8079e7a896305ab0b59e48fad4d644
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Azure App Service alkalmazás klónozása a PowerShell használatával
A Microsoft Azure PowerShell verziója 1.1.0-ás kiadása egy új beállítás lett elérhető a `New-AzureRMWebApp` , amely lehetővé teszi a klón egy meglévő webes alkalmazást egy újonnan létrehozott alkalmazás egy másik régióban vagy ugyanabban a régióban. Ez a beállítás lehetővé teszi az ügyfelek központi telepítése a alkalmazások számos különböző régiókban teljes gyorsan és egyszerűen.

Alkalmazás Klónozás jelenleg csak a premium szint app service-csomagokról támogatott. Az új szolgáltatás ugyanazokkal a korlátozásokkal használja, mint a Web Apps biztonsági másolat szolgáltatás című [készítsen biztonsági másolatot egy webalkalmazást az Azure App Service](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>A Klónozás egy meglévő alkalmazáshoz
Forgatókönyv: Egy létező webalkalmazása déli középső Régiójában terület, és szeretné klónozza a tartalom egy új webalkalmazást északi középső Régiójában régióban. Az új webalkalmazás létrehozása az Azure Resource Manager verziója, a PowerShell-parancsmag segítségével is elvégezhető a `-SourceWebApp` lehetőséget.

Az erőforráscsoport neve, amely tartalmazza a forrás-webalkalmazás ismeretében segítségével a következő PowerShell-parancsot a forrás web app információért (ebben az esetben nevű `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Egy új App Service-csomag létrehozásához használhatja `New-AzureRmAppServicePlan` parancsot az alábbi példában látható módon

```PowerShell
New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

Használja a `New-AzureRmWebApp` parancs, az új webalkalmazás létrehozása a régióban északi középső Régiójában, és azt összekötését egy App Service-csomag meglévő prémium tarifacsomagra. Ezenkívül ugyanabban az erőforráscsoportban használják a forrás web app, vagy adja meg egy új erőforráscsoportot, ahogy az az alábbi parancsot:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Egy létező webalkalmazása, beleértve az összes kapcsolódó üzembe helyezési klónozását, kell használnia a `IncludeSourceWebAppSlots` paraméter. A következő PowerShell-parancs bemutatja, hogy a paraméter a `New-AzureRmWebApp` parancs:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Klónozza a régión belül egy létező webalkalmazása, hozzon létre egy új erőforráscsoportot kell, és egy új app service tervezze meg ugyanabban a régióban, és a következő PowerShell-parancs segítségével klónozza a webalkalmazás

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Egy meglévő alkalmazást az App Service-környezetek való klónozása
Forgatókönyv: Egy létező webalkalmazása déli középső Régiójában terület, és szeretné klónozza a tartalom egy új webalkalmazást a egy meglévő App Service környezetben (ASE).

Az erőforráscsoport neve, amely tartalmazza a forrás-webalkalmazás ismeretében segítségével a következő PowerShell-parancsot a forrás web app információért (ebben az esetben nevű `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

TUDATÁBAN a ASE nevét, és az erőforráscsoport neve, amely a ASE tartozik, létrehozhat a webalkalmazása a meglévő ASE, ahogy az az alábbi parancsot:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

A `Location` paraméter megadása kötelező a hagyományos ok miatt, de az app Service-környezetben létrehozásakor figyelmen kívül. 

## <a name="cloning-an-existing-app-slot"></a>Egy meglévő tárolóhelye klónozása
. Forgatókönyv: Egy meglévő Web App hellyel vagy egy új webalkalmazást vagy webes alkalmazás új tárhely klónozni szeretne. Az új webalkalmazásba lehet ugyanabban a régióban, mint az eredeti webalkalmazás tárhely vagy egy másik régióban.

Az erőforráscsoport neve, amely tartalmazza a forrás-webalkalmazás ismeretében segítségével a következő PowerShell-parancsot a forrás webes alkalmazás a tárhely információért (ebben az esetben nevű `source-webappslot`) webalkalmazás kötve `source-webapp`:

```PowerShell
$srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot
```

Az alábbi parancs bemutatja, hogy a forrás webes alkalmazás egy új webalkalmazást a klón létrehozásához:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Egy alkalmazás klónozása során a Traffic Manager konfigurálása
Több területi webalkalmazások létrehozása és konfigurálása az Azure Traffic Manager forgalom irányítására a webes alkalmazásokhoz való, a célja, hogy magas rendelkezésre állású alkalmazások vevői fontos lehetőséget. Egy már meglévő webalkalmazás klónozásakor, lehetősége van mindkét webalkalmazások csatlakozni egy új traffic manager-profil vagy a meglévők egyikét. Csak Azure Resource Manager a Traffic Manager verziója támogatott.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Egy alkalmazás klónozása során egy új Traffic Manager-profil létrehozása
Forgatókönyv: Érdemes az Azure erőforrás-kezelő traffic manager-profilt, amely tartalmazza az mindkét webalkalmazások konfigurálása közben egy webalkalmazást egy másik régióban, a Klónozás. Az alábbi parancs bemutatja a klónt készíteni egy új webalkalmazást a forrás webalkalmazás új Traffic Manager-profil konfigurálása során:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Webalkalmazás hozzáadása az új klónozott egy meglévő Traffic Manager-profilt
Forgatókönyv: Már rendelkezik az Azure erőforrás-kezelő traffic manager-profilt, és mindkét webalkalmazások végpontként hozzáadásához. Ehhez először állítsa össze a meglévő traffic manager-profil azonosítóját. Az előfizetés-azonosító, az erőforráscsoport neve és a meglévő traffic manager-profil neve van szüksége.

```PowerShell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Miután a traffic Manager-azonosító, az alábbi parancs bemutatja klónt készíteni a forrás-webalkalmazás, egy új webalkalmazást a meglévő Traffic Manager-profil való hozzáadás során:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Aktuális korlátozások
Ez a funkció jelenleg előzetes verzióban érhetők, és új képességeket időbeli kerülnek. Az alábbiakban app Klónozás aktuális verziójának ismert korlátozásai:

* Automatikus skálázási beállításokat a rendszer nem klónozható
* Biztonsági mentés ütemezése beállítások vannak nem klónozható
* Vannak nem klónozható a VNET beállításait
* App Insights nem automatikusan be vannak állítva a célként megadott webalkalmazásban
* Egyszerű hitelesítési beállítások a rendszer nem klónozható
* A kudu bővítmény nem klónozható vannak
* Tipp szabályok nem klónozható vannak
* Adatbázis-tartalom nem klónozható.
* Kimenő IP-címek változik, ha a Klónozás egy másik skálázási egység

### <a name="references"></a>Referencia
* [Webes alkalmazás klónozása](app-service-web-app-cloning.md)
* [Készítsen biztonsági másolatot egy webalkalmazást az Azure App Service-ben](web-sites-backup.md)
* [Az Azure Traffic Manager előzetes Azure Resource Manager támogatása](../traffic-manager/traffic-manager-powershell-arm.md)
* [Az App Service Environment bemutatása](environment/intro.md)
* [Az Azure PowerShell használata az Azure Resource Managerrel](../azure-resource-manager/powershell-azure-resource-manager.md)

