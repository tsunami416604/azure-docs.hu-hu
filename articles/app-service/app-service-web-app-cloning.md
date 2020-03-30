---
title: Alkalmazás klónozása a PowerShell használatával
description: Megtudhatja, hogyan klónozhatja az App Service-alkalmazást egy új alkalmazásba a PowerShell használatával. A különböző klónozási forgatókönyvek hatálya alá tartozik, beleértve a Traffic Manager integráció.
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.custom: seodec18
ms.openlocfilehash: e7ad45ea4cb1049ed7eeb454162e23e81ed35019
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255188"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Az Azure App Service alkalmazásklónozása a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A Microsoft Azure PowerShell 1.1.0-s verziójának kiadásával `New-AzWebApp` egy új lehetőség lett hozzáadva, amely lehetővé teszi egy meglévő App Service-alkalmazás klónozását egy újonnan létrehozott alkalmazásba egy másik régióban vagy ugyanabban a régióban. Ez a beállítás lehetővé teszi az ügyfelek számára, hogy gyorsan és egyszerűen telepítsenek alkalmazásokat a különböző régiókban.

Az alkalmazásklónozás standard, prémium, prémium, prémium v2 és elkülönített alkalmazásszolgáltatási csomagok esetén támogatott. Az új szolgáltatás ugyanazokat a korlátozásokat használja, mint az App Service Backup szolgáltatás, [lásd: Egy alkalmazás biztonsági mentése az Azure App Service-ben.](manage-backup.md)

## <a name="cloning-an-existing-app"></a>Meglévő alkalmazás klónozása
Eset: Egy meglévő alkalmazás az USA déli középső régiójában, és szeretné klónozni a tartalmat egy új alkalmazásra az USA északi középső régiójában. Ez a PowerShell-parancsmag Azure Resource Manager-verziójával új alkalmazás létrehozásához `-SourceWebApp` a beállítással valósítható meg.

A forrásalkalmazást tartalmazó erőforráscsoport nevének ismerete a következő PowerShell-paranccsal lejuthat a forrásalkalmazás adatairól (ebben az esetben: `source-webapp`):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Új App Service-csomag létrehozásához `New-AzAppServicePlan` a következő példában látható módon használhatja a parancsot

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

A `New-AzWebApp` parancs segítségével létrehozhatja az új alkalmazást az USA északi középső régiójában, és egy meglévő App Service-csomaghoz kötheti. Ezenkívül használhatja ugyanazt az erőforráscsoportot, mint a forrásalkalmazás, vagy definiálhat egy új erőforráscsoportot, ahogy az a következő parancsban látható:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Egy meglévő alkalmazás klónozásához, beleértve az összes `IncludeSourceWebAppSlots` társított központi telepítési bővítőhelyet, a paramétert kell használnia.  Vegye figyelembe, hogy a `IncludeSourceWebAppSlots` paraméter csak egy teljes alkalmazás klónozásához támogatott, beleértve az összes bővítőhelyét is. A következő PowerShell-parancs bemutatja a `New-AzWebApp` paraméter használatát a paranccsal:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Ha ugyanabban a régióban szeretne klónozni egy meglévő alkalmazást, létre kell hoznia egy új erőforráscsoportot és egy új alkalmazásszolgáltatás-csomagot ugyanabban a régióban, majd a következő PowerShell-paranccsal klónozhatja az alkalmazást:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Meglévő alkalmazás klónozása App Service-környezetbe
Eset: Egy meglévő alkalmazás az USA déli középső régiójában, és szeretné klónozni a tartalmat egy új alkalmazás egy meglévő App Service-környezet (ASE).

A forrásalkalmazást tartalmazó erőforráscsoport nevének ismerete a következő PowerShell-paranccsal lejuthat a forrásalkalmazás adatairól (ebben az esetben: `source-webapp`):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Az ASE nevének és az ASE-hez tartozó erőforráscsoport nevének ismerete az új alkalmazást a meglévő ASE-ben hozhatja létre, ahogy az a következő parancsban látható:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

The `Location` parameter is required due to legacy reason, but it is ignored when you create the app in an ASE. 

## <a name="cloning-an-existing-app-slot"></a>Meglévő alkalmazásbővítőhely klónozása
Eset: Egy alkalmazás meglévő központi telepítési helytárolóját egy új alkalmazásba vagy egy új tárolóhelyre szeretné klónozni. Az új alkalmazás lehet ugyanabban a régióban, mint az eredeti alkalmazásslot, vagy egy másik régióban.

A forrásalkalmazást tartalmazó erőforráscsoport nevének ismerete a következő PowerShell-paranccsal lehívhatja a forrásalkalmazás-tárolóhely adatait (ebben az esetben a neve) `source-appslot`a következőhöz `source-app`kötve:

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

A következő parancs bemutatja a forrásalkalmazás klónozását egy új alkalmazásba:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>A Traffic Manager konfigurálása alkalmazás klónozása közben
Többrégiós alkalmazások létrehozása és az Azure Traffic Manager konfigurálása az összes ilyen alkalmazáshoz való forgalom irányításához fontos forgatókönyv annak biztosítására, hogy az ügyfelek alkalmazásai magas rendelkezésre állásúak legyenek. Meglévő alkalmazás klónozásakor mindkét alkalmazást csatlakoztathatja egy új forgalomkezelő profilhoz vagy egy meglévőhöz. Csak a Traffic Manager Azure Resource Manager verziója támogatott.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Új Traffic Manager-profil létrehozása alkalmazás klónozása közben
Forgatókönyv: Szeretne klónozni egy alkalmazást egy másik régióba, miközben konfigurálja az Azure Resource Manager traffic manager profil, amely mindkét alkalmazást tartalmazza. A következő parancs bemutatja a forrásalkalmazás klónozását egy új alkalmazásba egy új Traffic Manager-profil konfigurálása közben:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Új klónozott alkalmazás hozzáadása meglévő Traffic Manager-profilhoz
Forgatókönyv: Már rendelkezik egy Azure Resource Manager traffic manager profillal, és mindkét alkalmazást végpontként szeretné hozzáadni. Ehhez először össze kell állítania a meglévő traffic manager profilazonosítót. Szüksége van az előfizetés-azonosítóra, az erőforráscsoport nevére és a meglévő forgalomkezelő profil nevére.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

A forgalmi jászol-azonosító tkövető en a következő parancs bemutatja a forrásalkalmazás klónjának létrehozását egy új alkalmazáshoz, miközben hozzáadja őket egy meglévő Traffic Manager-profilhoz:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Jelenlegi korlátozások
Az alkalmazásklónozás ismert korlátozásai:

* Az automatikus méretezési beállítások nincsenek klónozásra
* A biztonsági mentés ütemezésének beállításai nem klónozva vannak
* A VNET-beállítások nincsenek klónozásra
* Az App Insights nincs automatikusan beállítva a célalkalmazásban
* Az egyszerű hitelesítési beállítások nincsenek klónozásra
* Kudu extension nincs klónozott
* A TiP-szabályok nincsenek klónozottak
* Az adatbázis tartalma nincs klónozott
* A kimenő IP-címek akkor változnak, ha másik méretezési egységhez szeretne klónozni
* Linux-alkalmazásokhoz nem érhető el

### <a name="references"></a>Referencia
* [App Service klónozása](app-service-web-app-cloning.md)
* [Alkalmazás biztonsági és biztonsági szolgálata az Azure App Service-ben](manage-backup.md)
* [Az Azure Resource Manager támogatása az Azure Traffic Manager előzetes verziójához](../traffic-manager/traffic-manager-powershell-arm.md)
* [Az App Service Environment bemutatása](environment/intro.md)
* [Az Azure PowerShell használata az Azure Resource Managerrel](../azure-resource-manager/management/manage-resources-powershell.md)

