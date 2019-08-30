---
title: PremiumV2-réteg konfigurálása – Azure App Service | Microsoft Docs
description: Ismerje meg, hogyan javíthatja a teljesítményt a webes, mobil-és API-alkalmazásokban Azure App Service az új PremiumV2 díjszabási szintjére való skálázással.
keywords: app service, azure app service, méret, méretezhető, app service-csomag, app service ára
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/25/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: aa1e522e395f869f73c0b250623665a9b175384e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067236"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Azure App Service PremiumV2-szintjeinek konfigurálása

Az új **PremiumV2** -díjszabási csomag gyorsabb processzorokat, SSD-tárolókat és a meglévő árképzési szintek memória-mag arányát adja meg. A teljesítmény előnye, hogy pénzt takaríthat meg az alkalmazások kevesebb példányon való futtatásával. Ebből a cikkből megtudhatja, hogyan hozhat létre alkalmazást **PremiumV2** -szinten, vagy hogyan méretezheti fel az alkalmazást a **PremiumV2** szintjére.

## <a name="prerequisites"></a>Előfeltételek

Egy alkalmazás **PremiumV2**való felskálázásához rendelkeznie kell egy olyan Azure app Service alkalmazással, amely alacsonyabb, mint a **PremiumV2**, és az alkalmazásnak egy olyan app Service-telepítésben kell futnia, amely támogatja a PremiumV2-t.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2 rendelkezésre állása

A **PremiumV2** szinten a _Windows_ és a _Linux_app Service is elérhető.

A **PremiumV2** a legtöbb Azure-régióban elérhető. Ha szeretné megtekinteni, hogy elérhető-e az Ön régiójában, futtassa a következő [](../cloud-shell/overview.md)Azure CLI-parancsot a Azure Cloud Shellban:

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Alkalmazás létrehozása a PremiumV2 szinten

Az App Service alkalmazás díjszabási szintje a [app Service](overview-hosting-plans.md) -csomagban van definiálva. Létrehozhat egy App Service tervet saját maga vagy az alkalmazás létrehozása részeként is.

Ha a App Service csomagot a Azure Portalban <a href="https://portal.azure.com" target="_blank"></a>konfigurálja, válassza az **árképzési szintet**. 

Válassza az **éles környezet**, majd az **P1V2**, a **P2V2**vagy a **P3V2**lehetőséget, majd kattintson az **alkalmaz**gombra.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Ha nem látja a **P1V2**, a **P2V2**és a **P3V2** lehetőséget, vagy ha a beállítások szürkén jelennek meg, akkor a **PremiumV2** valószínűleg nem érhető el a app Service csomagot tartalmazó mögöttes app Service-telepítésben. További részletekért tekintse meg a nem [támogatott erőforráscsoport és régió kombinációjának skálázása](#unsupported) című témakört.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Meglévő alkalmazás vertikális felskálázása PremiumV2-szintűre

Mielőtt egy meglévő alkalmazást **PremiumV2** -szinten méretezni, győződjön meg arról, hogy a **PremiumV2** elérhető. További információ: [PremiumV2 rendelkezésre állása](#availability). Ha nem érhető el, tekintse meg [a vertikális felskálázás egy nem támogatott erőforráscsoport és régió kombinációjával](#unsupported)című témakört.

Az üzemeltetési környezettől függően a vertikális felskálázás további lépéseket is igényelhet. 

A <a href="https://portal.azure.com" target="_blank">Azure Portal</a>nyissa meg a app Service alkalmazás lapját.

A App Service alkalmazás lap bal oldali navigációs sávján válassza a vertikális **felskálázás (App Service terv)** lehetőséget.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Válassza az **éles környezet**, majd az **P1V2**, a **P2V2**vagy a **P3V2**lehetőséget, majd kattintson az **alkalmaz**gombra.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Ha a művelet sikeresen befejeződik, az alkalmazás áttekintő lapja azt mutatja, hogy most már **PremiumV2** szinten van.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Ha hibaüzenetet kap

Egyes App Service csomagok nem méretezhetők a PremiumV2 szintjére, ha a mögöttes App Service üzemelő példány nem támogatja a PremiumV2.  További részletekért tekintse meg a nem [támogatott erőforráscsoport és régió kombinációjának skálázása](#unsupported) című témakört.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Vertikális felskálázás egy nem támogatott erőforráscsoporthoz és régió-kombinációból

Ha az alkalmazás olyan App Service üzemelő példányban fut, ahol a **PremiumV2** nem érhető el, vagy ha az alkalmazás olyan régióban fut, amely jelenleg nem támogatja a **PremiumV2**-t, akkor az **PremiumV2**kihasználásához újra kell telepítenie az alkalmazást.  Erre két lehetősége van:

- Hozzon létre egy **új** erőforráscsoportot, majd hozzon létre egy **új** alkalmazást, és app Service tervezze meg az **új** erőforráscsoportot, majd válassza ki a kívánt Azure-régiót a létrehozási folyamat során.  Az új App Service-csomag létrehozásakor ki **kell** választania a **PremiumV2** tervet.  Ezzel biztosítható, hogy az erőforráscsoport, a App Service-csomag és az Azure-régió kombinációja a **PremiumV2**-t támogató app Service-telepítésben a app Service tervet hozza létre.  Ezután telepítse újra az alkalmazás kódját az újonnan létrehozott alkalmazás-és app Service-csomagba. Ha azt szeretné, hogy a későbbiekben a **PremiumV2** -től megtakarítsa a app Service tervet, a költségek megtakarítása érdekében továbbra is sikeresen átméretezheti a biztonsági mentést a jövőben a **PremiumV2**használatával.
- Ha az alkalmazás már egy meglévő **prémium** szinten fut, akkor az alkalmazás a **PremiumV2**használó új App Service-csomagba az alkalmazás összes beállításával, a kapcsolódási karakterláncokkal és a telepítési konfigurációval is klónozott.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    A **klónozási alkalmazás** oldalon létrehozhat egy app Service tervet a **PremiumV2** használatával a kívánt régióban, és megadhatja a klónozott alkalmazás beállításait és konfigurációját.

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Az **PremiumV2** szinten automatizálhatja az alkalmazások létrehozását az [Azure CLI](/cli/azure/install-azure-cli) vagy a [Azure PowerShell](/powershell/azure/overview)használatával.

### <a name="azure-cli"></a>Azure CLI

A következő parancs egy App Service tervet hoz létre a _P1V2_-ben. A Cloud Shell futtathatja. A következő lehetőségei `--sku` : P1V2, _P2V2_és _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő parancs egy App Service tervet hoz létre a _P1V2_-ben. A _kis_, `-WorkerSize` _közepes_és _nagyméretű_beállítások.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>További források

[Alkalmazások vertikális felskálázása az Azure-ban](manage-scale-up.md)  
[Példányszám manuális vagy automatikus méretezése](../monitoring-and-diagnostics/insights-how-to-scale.md)
