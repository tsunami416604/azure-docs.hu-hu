---
title: Alkalmazás klónozása a PowerShell használatával – az Azure App Service-ben
description: Útmutató a PowerShell használatával új alkalmazás az App Service-alkalmazás klónozását.
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 53cde81ed5df97c4cb6d8360c9bb639b8bdabe20
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818136"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Az Azure App Service-alkalmazás klónozása a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A Microsoft Azure PowerShell 1.1.0-s verzió kiadása, egy új beállítás lett elérhető a `New-AzWebApp` , amellyel az App Service-ben újonnan létrehozott alkalmazást egy másik régióban, vagy ugyanabban a régióban a meglévő alkalmazás klónozása. Ez a beállítás lehetővé teszi, hogy üzembe alkalmazások számos különböző régiók között, gyorsan és egyszerűen.

Alkalmazásklónozás jelenleg csak prémium szintű app service-csomagok esetén támogatott. Az új szolgáltatást használ App Service biztonsági másolat szolgáltatás ugyanazokkal a korlátozásokkal, lásd: [biztonsági mentése az Azure App Service alkalmazás](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Meglévő alkalmazás klónozása
Forgatókönyv: USA déli középső régiójában, és a egy meglévő alkalmazást szeretne klónozza a tartalmát egy új alkalmazást, az USA északi középső régiójában. Ez az új alkalmazás létrehozása az Azure Resource Managerre vonatkozó verziójának PowerShell-parancsmag használatával elvégezhető a `-SourceWebApp` lehetőséget.

Hogy az erőforráscsoport neve, amely tartalmazza a forrás-alkalmazást, használhatja a következő PowerShell-parancsot a forrásalkalmazás lekérése (ebben az esetben nevű `source-webapp`):

```PowerShell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Hozzon létre egy új App Service-csomagban, használhatja `New-AzAppServicePlan` parancsot az alábbi példában látható módon

```PowerShell
New-AzAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

Használatával a `New-AzWebApp` parancsot, USA északi középső Régiója régióban hozza létre az új alkalmazást, és hozzákötését egy meglévő prémium szintű App Service-csomag. Ezenkívül ugyanazt az erőforráscsoportot használja a forrás-alkalmazásként, vagy egy új erőforráscsoportot, határozza meg, ahogyan az az alábbi parancsot:

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

A klón egy meglévő alkalmazást, beleértve az összes kapcsolódó üzembe helyezési pontot használni kell a `IncludeSourceWebAppSlots` paraméter. A következő PowerShell-parancs bemutatja, hogy a paraméter a `New-AzWebApp` parancsot:

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Az azonos régión belüli meglévő alkalmazás klónozása, hozzon létre egy új erőforráscsoportot kell, és a egy új app service csomag ugyanabban a régióban, és a következő PowerShell-parancs használatával klónozza az alkalmazást:

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>App Service-környezet a meglévő alkalmazás klónozása
Forgatókönyv: USA déli középső régiójában, és a egy meglévő alkalmazást szeretne klónozza a tartalmát egy új alkalmazást, egy meglévő App Service Environment (ASE).

Hogy az erőforráscsoport neve, amely tartalmazza a forrás-alkalmazást, használhatja a következő PowerShell-parancsot a forrásalkalmazás lekérése (ebben az esetben nevű `source-webapp`):

```PowerShell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Annak ismerete, az ASE nevét és az erőforráscsoport neve, amely az ASE tartozik, hozhat létre az új alkalmazás a meglévő ASE, ahogyan az alábbi parancsot:

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

A `Location` paraméter megadása kötelező örökölt ok miatt, de figyelmen hagyja, az ASE környezetben az alkalmazás létrehozásakor. 

## <a name="cloning-an-existing-app-slot"></a>Meglévő alkalmazás tárhelyek klónozása
Forgatókönyv: Klónozza a új alkalmazásra egy alkalmazás meglévő üzembe helyezési pont vagy egy új tárhely szeretné. Az új alkalmazás lehet ugyanabban a régióban, mint az eredeti alkalmazás tárolóhely, vagy egy másik régióban.

Hogy az erőforráscsoport neve, amely tartalmazza a forrás-alkalmazást, használhatja a következő PowerShell-parancsot a forrás alkalmazás tárolóhely lekérése (ebben az esetben nevű `source-appslot`) kötött `source-app`:

```PowerShell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

Az alábbi parancs bemutatja, hogy a forrás-alkalmazás egy új alkalmazásba a klón létrehozásához:

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>A Traffic Manager konfigurálása során egy alkalmazás klónozása
Többrégiós alkalmazásokat készít, és irányíthatja a forgalmat az összes ezeket az alkalmazásokat, az Azure Traffic Manager konfigurálása a helyzet arról, hogy az ügyfelek alkalmazások magas rendelkezésre állású. Ha egy meglévő alkalmazás klónozása, lehetősége van való csatlakozáshoz mindkét alkalmazást vagy egy új traffic manager-profilt, vagy egy meglévőt. Csak az Azure Resource Manager-verziót a Traffic Manager támogatja.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Új Traffic Manager-profil létrehozása közben egy alkalmazás klónozása
Forgatókönyv: Szeretne egy másik régióba alkalmazás klónozása egy Azure Resource Manager traffic manager-profilt, amely tartalmazza az mindkét konfigurálása közben. Az alábbi parancs bemutatja a klónt az új alkalmazás forrás alkalmazás egy új Traffic Manager-profil konfigurálása közben:

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Új Klónozott alkalmazás egy meglévő Traffic Manager-profil
Forgatókönyv: Már rendelkezik egy Azure Resource Manager traffic manager-profilt, és mindkét alkalmazást felvenni Végpontokként. Ehhez először létre kell állítsa össze a meglévő traffic manager-profil azonosítója. Az előfizetés-azonosító, az erőforráscsoport nevét és a meglévő traffic manager-profilnév van szüksége.

```PowerShell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Miután a traffic Manager-azonosító, az alábbi parancs bemutatja, klónt az új alkalmazás forrás alkalmazás egy meglévő Traffic Manager-profilt hozzáadásakor:

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Aktuális korlátozások
Alkalmazásklónozás ismert korlátozások a következők:

* Automatikus méretezési beállítások nem lesznek klónozva
* Ütemezett biztonsági mentési beállítások nem lesznek klónozva
* Virtuális hálózat beállításait nem lesznek klónozva
* Az App Insights nem automatikusan állította be a cél az alkalmazást
* Egyszerű hitelesítési beállítások nem lesznek klónozva
* Kudu-bővítmény nem lesznek klónozva
* Tipp szabályok nem lesznek klónozva
* Adatbázis-tartalom nem klónozták
* Kimenő IP-címek megváltozik, ha egy másik skálázási egységre klónozása

### <a name="references"></a>Referencia
* [App Service Cloning](app-service-web-app-cloning.md)
* [Az Azure App Service-alkalmazás biztonsági mentése](manage-backup.md)
* [Az Azure Traffic Manager előzetes verziójának támogatása az Azure Resource Manager](../traffic-manager/traffic-manager-powershell-arm.md)
* [Az App Service Environment bemutatása](environment/intro.md)
* [Az Azure PowerShell használata az Azure Resource Managerrel](../azure-resource-manager/manage-resources-powershell.md)

