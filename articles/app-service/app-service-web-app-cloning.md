---
title: Alkalmazás klónozása a PowerShell használatával – az Azure App Service-ben
description: Ismerje meg, hogyan klónozhatók a Web Apps új Web Apps PowerShell-lel.
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
ms.openlocfilehash: 87bae4db64c0a22790b7f52f919601f82aa548df
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53261867"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Az Azure App Service-alkalmazás klónozása a PowerShell használatával
A Microsoft Azure PowerShell 1.1.0-s verzió kiadása, egy új beállítás lett elérhető a `New-AzureRMWebApp` , amellyel egy meglévő Web Apps egy újonnan létrehozott alkalmazást, egy másik régióban, vagy ugyanabban a régióban történő klónozásához. Ez a beállítás lehetővé teszi, hogy üzembe alkalmazások számos különböző régiók között, gyorsan és egyszerűen.

Alkalmazásklónozás jelenleg csak prémium szintű app service-csomagok esetén támogatott. Az új szolgáltatás webes alkalmazások biztonsági másolat szolgáltatás használja ugyanazokkal a korlátozásokkal, lásd: [biztonsági mentése az Azure App Service webalkalmazás](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>Meglévő alkalmazás klónozása
Forgatókönyv: Egy meglévő web Apps, az USA déli középső régiójában, és szeretné klónozza a tartalmát egy új webalkalmazást az USA északi középső régiójában. Ez az új webalkalmazás létrehozása az Azure Resource Managerre vonatkozó verziójának PowerShell-parancsmag használatával elvégezhető a `-SourceWebApp` lehetőséget.

Az erőforráscsoport neve, amely tartalmazza a forrás-webalkalmazás ismerete, használhatja a következő PowerShell-parancsot a forrás webalkalmazás lekérése (ebben az esetben nevű `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Hozzon létre egy új App Service-csomagban, használhatja `New-AzureRmAppServicePlan` parancsot az alábbi példában látható módon

```PowerShell
New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

Használatával a `New-AzureRmWebApp` parancsot, USA északi középső Régiója régióban hozza létre az új webalkalmazást, és hozzákötését egy meglévő prémium szintű App Service-csomag. Ezenkívül ugyanazt az erőforráscsoportot használja a forrás-webalkalmazás, vagy egy új erőforráscsoportot, határozza meg, ahogyan az az alábbi parancsot:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

A klón egy meglévő web Apps, beleértve az összes kapcsolódó üzembe helyezési pontot használni kell a `IncludeSourceWebAppSlots` paraméter. A következő PowerShell-parancs bemutatja, hogy a paraméter a `New-AzureRmWebApp` parancsot:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

A klón egy meglévő web Apps egy régión belül hozzon létre egy új erőforráscsoportot kell, és a egy új app service csomag ugyanabban a régióban, és a következő PowerShell-parancs használatával a webes alkalmazás klónozása

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>App Service-környezet a meglévő alkalmazás klónozása
Forgatókönyv: Egy meglévő web Apps, az USA déli középső régiójában, és szeretné klónozza a tartalmát egy új webalkalmazást, egy meglévő App Service Environment (ASE).

Az erőforráscsoport neve, amely tartalmazza a forrás-webalkalmazás ismerete, használhatja a következő PowerShell-parancsot a forrás webalkalmazás lekérése (ebben az esetben nevű `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Hogy az ASE nevét és az erőforráscsoport neve, amely az ASE tartozik, hozhat létre az új webalkalmazást a meglévő ASE, ahogyan az az alábbi parancsot:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

A `Location` paraméter megadása kötelező örökölt ok miatt, de figyelmen hagyja, az ASE környezetben az alkalmazás létrehozásakor. 

## <a name="cloning-an-existing-app-slot"></a>Meglévő alkalmazás tárhelyek klónozása
Forgatókönyv: Webes alkalmazás meglévő tárhelyek, vagy egy új webalkalmazást vagy webes alkalmazás új tárhely klónozni szeretne. Az új webalkalmazást az eredeti webalkalmazás tárolóhelyen ugyanabban a régióban, vagy egy másik régióban is lehet.

Hogy az erőforráscsoport neve, amely tartalmazza a forrás-webalkalmazás, használhatja a következő PowerShell-parancsot a forrás webes alkalmazás tárolóhely lekérése (ebben az esetben nevű `source-webappslot`) webalkalmazáshoz kötött `source-webapp`:

```PowerShell
$srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot
```

Az alábbi parancs bemutatja, hogy egy klónt készíteni a forrás-webalkalmazás, egy új webalkalmazáshoz:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>A Traffic Manager konfigurálása során egy alkalmazás klónozása
Többrégiós webalkalmazások létrehozása, és irányíthatja a forgalmat a web Apps alkalmazások az Azure Traffic Manager konfigurálása, a rendszer annak érdekében, hogy az ügyfelek alkalmazások magas rendelkezésre állású helyzet. Ha a Klónozás egy létező webalkalmazása, lehetősége van mindkét webalkalmazások csatlakozni vagy egy új traffic manager-profilt, vagy egy meglévőt. Csak az Azure Resource Manager-verziót a Traffic Manager támogatja.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Új Traffic Manager-profil létrehozása közben egy alkalmazás klónozása
Forgatókönyv: Szeretne egy webalkalmazás, egy másik régióba, klónozza az Azure Resource Manager traffic manager-profilt, amely tartalmazza az mindkét webalkalmazások konfigurálása közben. Az alábbi parancs bemutatja egy új Traffic Manager-profil konfigurálása során a forrás-webalkalmazás, egy új webalkalmazásba klónja létrehozása:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Új webalkalmazás klónozni egy meglévő Traffic Manager-profil
Forgatókönyv: Már rendelkezik egy Azure Resource Manager traffic manager-profilt, és mindkét webalkalmazások végpontok hozzáadni. Ehhez először létre kell állítsa össze a meglévő traffic manager-profil azonosítója. Az előfizetés-azonosító, az erőforráscsoport nevét és a meglévő traffic manager-profilnév van szüksége.

```PowerShell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Miután a traffic Manager-azonosító, az alábbi parancs bemutatja, klónt készíteni a forrás-webalkalmazás, egy új webalkalmazást, egy meglévő Traffic Manager-profilt hozzáadásakor:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Aktuális korlátozások
Ez a funkció jelenleg előzetes verzióban érhető el, és új képességeket idővel kerülnek. Az alábbiakban a jelenlegi verziója alkalmazásklónozás ismert korlátozásai:

* Automatikus méretezési beállítások nem lesznek klónozva
* Ütemezett biztonsági mentési beállítások nem lesznek klónozva
* Virtuális hálózat beállításait nem lesznek klónozva
* Az App Insights nem automatikusan állította be a cél-webalkalmazásban
* Egyszerű hitelesítési beállítások nem lesznek klónozva
* Kudu-bővítmény nem lesznek klónozva
* Tipp szabályok nem lesznek klónozva
* Adatbázis-tartalom nem klónozták
* Kimenő IP-címek megváltozik, ha egy másik skálázási egységre klónozása

### <a name="references"></a>Referencia
* [Webes alkalmazás klónozása](app-service-web-app-cloning.md)
* [Készítsen biztonsági másolatot az Azure App Service webalkalmazás](web-sites-backup.md)
* [Az Azure Traffic Manager előzetes verziójának támogatása az Azure Resource Manager](../traffic-manager/traffic-manager-powershell-arm.md)
* [Az App Service Environment bemutatása](environment/intro.md)
* [Az Azure PowerShell használata az Azure Resource Managerrel](../azure-resource-manager/powershell-azure-resource-manager.md)

