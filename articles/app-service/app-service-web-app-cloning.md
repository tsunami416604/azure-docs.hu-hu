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
ms.date: 01/13/2016
ms.author: aelnably
ms.openlocfilehash: 8f58464ac212b84623d2287205271301dbaa0ffb
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Azure App Service alkalmazás klónozása a PowerShell használatával
A Microsoft Azure PowerShell verziója 1.1.0-ás kiadása egy új beállítás klónozni egy már meglévő webalkalmazás egy újonnan létrehozott alkalmazás egy másik régióban vagy ugyanabban a régióban képes tenné a felhasználónak új AzureRMWebApp bővült. Ez lehetővé teszi az ügyfelek központi telepítése a alkalmazások számos különböző régiókban teljes gyorsan és egyszerűen.

Alkalmazás Klónozás jelenleg csak a premium szint app service-csomagokról támogatott. Az új szolgáltatás ugyanazokkal a korlátozásokkal használja, mint a Web Apps biztonsági másolat szolgáltatás című [készítsen biztonsági másolatot egy webalkalmazást az Azure App Service](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>A Klónozás egy meglévő alkalmazáshoz
Forgatókönyv: Egy létező webalkalmazása régióban déli középső Régiójában, a felhasználó szeretné klónozza a tartalom egy új webalkalmazást északi középső Régiójában régióban. Ehhez az Azure Resource Manager verziója, a PowerShell-parancsmag segítségével hozzon létre egy új webalkalmazást a - SourceWebApp kapcsolóval.

Az erőforráscsoport neve, amely tartalmazza a forrás-webalkalmazás ismeretében is használjuk a következő PowerShell-parancs adatokat lekérdezni a forrás web app (ebben az esetben a forrás-webalkalmazás neve):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Hozzon létre egy új App Service-csomag, az alábbi példában látható módon a New-AzureRmAppServicePlan parancs is használjuk

    New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

A New-AzureRmWebApp paranccsal azt az új webalkalmazás létrehozása a régióban északi középső Régiójában, és azt összekötését egy App Service-csomag meglévő prémium tarifacsomagra, továbbá a Microsoft ugyanabban az erőforráscsoportban használják a forrás web app, vagy adja meg egy új erőforráscsoportot, a következő mutatja be, amelyek:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

Egy létező webalkalmazása, beleértve az összes társított telepítési klónozását tárolóhely, a felhasználónak kell használnia a IncludeSourceWebAppSlots paraméter, a következő PowerShell-parancs bemutatja, hogy a paramétert a New-AzureRmWebApp paranccsal:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots

Klónozásához egy már meglévő webalkalmazás belül ugyanabban a régióban, a felhasználó kell hoznia egy új erőforráscsoportot és egy új app service tervezze meg ugyanabban a régióban, és a következő PowerShell-parancs segítségével klónozza a webes alkalmazás

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Egy meglévő alkalmazást az App Service-környezetek való klónozása
Forgatókönyv: Egy létező webalkalmazása régióban déli középső Régiójában, a felhasználó szeretné klónozza a tartalom egy új webalkalmazást a egy meglévő App Service környezetben (ASE).

Az erőforráscsoport neve, amely tartalmazza a forrás-webalkalmazás ismeretében is használjuk a következő PowerShell-parancs adatokat lekérdezni a forrás web app (ebben az esetben a forrás-webalkalmazás neve):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

TUDATÁBAN a ASE nevét, és az erőforráscsoport neve, amely a ASE tartozik, a felhasználó használhatja a New-AzureRmWebApp parancsot az új webalkalmazás létrehozása a meglévő ASE a, a következő mutatja be, amelyek:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

A hely paraméter örökölt ok miatt szükséges, de az app Service-környezetben létrehozása esetén, figyelmen kívül hagyja. 

## <a name="cloning-an-existing-app-slot"></a>Egy meglévő tárolóhelye klónozása
Forgatókönyv: A felhasználó szeretné klónozni egy meglévő Web App hellyel vagy egy új webalkalmazást vagy webes alkalmazás új tárhely. Az új webalkalmazásba lehet ugyanabban a régióban, mint az eredeti webalkalmazás tárhely vagy egy másik régióban.

Az erőforráscsoport neve, amely tartalmazza a forrás-webalkalmazás ismeretében is használjuk a következő PowerShell-parancs lekérése a forrás web app tárolóhely (ebben az esetben a forrás-webappslot nevű) webalkalmazás forrás-webalkalmazás kötve:

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

A következő mutatja be, a forrás webes alkalmazás egy új webalkalmazást a klón létrehozásához:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## <a name="configuring-traffic-manager-while-cloning-a-app"></a>Egy alkalmazás klónozása során a Traffic Manager konfigurálása
Több területi webalkalmazások létrehozása és konfigurálása az Azure Traffic Manager forgalom irányítására a webes alkalmazásokhoz való, egy webfarmos n fontos annak érdekében, hogy ügyfelek alkalmazások magas rendelkezésre állású, egy létező webalkalmazása klónozásakor lehetősége van a mindkét webalkalmazások csatlakozni vagy egy új traffic manager-profilt, vagy egy meglévő,-vegye figyelembe, hogy egyetlen Azure Resource Manager Traffic Manager verziója támogatott.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-a-app"></a>Egy alkalmazás klónozása során egy új Traffic Manager-profil létrehozása
Forgatókönyv: A felhasználó szeretné klónozni egy webalkalmazás egy másik régióban, az Azure erőforrás-kezelő traffic manager-profilt, amely a webes alkalmazások egyaránt lehetnek konfigurálása közben. A következő bemutatja klónt készíteni egy új webalkalmazást a forrás webalkalmazás új Traffic Manager-profil konfigurálása során:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Webalkalmazás hozzáadása az új klónozott egy meglévő Traffic Manager-profilt
Forgatókönyv: A felhasználó már rendelkezik az Azure erőforrás-kezelő traffic manager-profil, akkor mindkét webalkalmazások hozzáadása végpontként szeretne. Ehhez először igazolnia kell a meglévő traffic manager Profilazonosító összeállítása, fel kell az előfizetés-azonosító, erőforráscsoport-név és a meglévő traffic manager-profil neve.

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

Miután a traffic Manager-azonosító, a következő bemutatja klónt készíteni a forrás-webalkalmazás, egy új webalkalmazást a meglévő Traffic Manager-profil való hozzáadás során:

    $destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## <a name="current-restrictions"></a>Aktuális korlátozások
Ez a funkció jelenleg előzetes verzióban érhetők, új képességeket adhat a időbeli dolgozunk, a következő lista rendszer app Klónozás aktuális verziójának ismert korlátozásai:

* Automatikus skálázási beállításokat a rendszer nem klónozható
* Biztonsági mentés ütemezése beállítások vannak nem klónozható
* Vannak nem klónozható a VNET beállításait
* App Insights nem automatikusan be vannak állítva a célként megadott webalkalmazásban
* Egyszerű hitelesítési beállítások a rendszer nem klónozható
* A kudu bővítmény nem klónozható vannak
* Tipp szabályok nem klónozható vannak
* Adatbázis-tartalom nem klónozható vannak
* Kimenő IP-címek változik, ha a Klónozás egy másik skálázási egység

### <a name="references"></a>Referencia
* [Webes alkalmazás klónozása](app-service-web-app-cloning.md)
* [Készítsen biztonsági másolatot egy webalkalmazást az Azure App Service-ben](web-sites-backup.md)
* [Az Azure Traffic Manager előzetes Azure Resource Manager támogatása](../traffic-manager/traffic-manager-powershell-arm.md)
* [Az App Service Environment bemutatása](environment/intro.md)
* [Az Azure PowerShell használata az Azure Resource Managerrel](../azure-resource-manager/powershell-azure-resource-manager.md)

