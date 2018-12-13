---
title: PremiumV2 szintű csomag – az Azure App Service konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan végezhet a teljesítmény a webes, mobilos és API-alkalmazás Azure App Service-ben a méretezési lehetőségek érhetők el az új PremiumV2 tarifacsomag szerint.
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
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ac0a3500956cc81e264c8743d44aa06f018b1a45
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53258899"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>PremiumV2 szintű csomag konfigurálása az Azure App Service-ben

Az új **PremiumV2** tarifacsomag biztosítja gyorsabb processzorokkal, SSD-tárolóval és a meglévő tarifacsomagok Double típusú értékekkel a memória-mag arány. A teljesítmény előnye az pénzt sikerült menteni az kevesebb példányt az alkalmazások futtatásával. Ebből a cikkből elsajátíthatja, hogyan hozzon létre egy alkalmazást az **PremiumV2** szinten vagy az alkalmazás vertikális felskálázása **PremiumV2** szint.

## <a name="prerequisites"></a>Előfeltételek

A vertikális felskálázás egy webalkalmazás **PremiumV2**, szüksége lesz egy webalkalmazást egy tarifacsomagot alacsonyabb, mint a futó Azure App Service-ben **PremiumV2**, és a webalkalmazás az App Service-környezet, amely támogatja a futnia kell PremiumV2.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2 rendelkezésre állása

A **PremiumV2** szinten érhető el az App Service egyaránt _Windows_ , valamint _Linux_.

**PremiumV2** a legtöbb Azure-régióban érhető el. Szeretné látni, hogy elérhető-e az Ön régiójában, az alábbi Azure CLI-paranccsal futtassa a [Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>PremiumV2 szintű csomag az új alkalmazás létrehozása

App Service-alkalmazások tarifacsomagja van definiálva a [App Service-csomag](azure-web-sites-web-hosting-plans-in-depth-overview.md) azt futtató. App Service-csomagot is létrehozhat, önmagában vagy webes alkalmazás létrehozásának részeként.

Az App Service-csomag konfigurálása során a <a href="https://portal.azure.com" target="_blank">az Azure portal</a>válassza **tarifacsomag**. 

Válassza ki **éles**, majd **P1V2**, **P2V2**, vagy **P3V2**, majd kattintson a **alkalmaz**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Ha nem lát **P1V2**, **P2V2**, és **P3V2** közül, vagy ha szürkén jelennek meg a beállítások ki, majd **PremiumV2** valószínűleg nem érhető el az alapul szolgáló App Service-környezet, amely tartalmazza az App Service-csomag. Lásd: [egy nem támogatott erőforrás csoport és a régió kombinációja a vertikális felskálázás](#unsupported) további részletekért.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>PremiumV2 szintű csomag a meglévő alkalmazás vertikális felskálázása

Egy meglévő alkalmazást a skálázás előtti **PremiumV2** réteg, ellenőrizze, hogy **PremiumV2** érhető el. További információ: [PremiumV2 rendelkezésre állási](#availability). Ha nem érhető el, lásd: [egy nem támogatott erőforrás csoport és a régió kombinációja a vertikális felskálázás](#unsupported).

Attól függően, a üzemeltetési környezet vertikális felskálázása lehetséges, hogy további lépéseket igénylő. 

Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a>, az App Service-alkalmazás lap megnyitásához.

Az App Service alkalmazás lapjának bal oldali navigációs sávján válassza **vertikális felskálázás (App Service-csomag)**.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Válassza ki **éles**, majd **P1V2**, **P2V2**, vagy **P3V2**, majd kattintson a **alkalmaz**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Ha a művelet sikeres befejezését követően az alkalmazás áttekintése lapon azt mutatja, hogy most már egy **PremiumV2** szint.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Ha hibaüzenetet kap

Néhány az App Service-csomagok nem vertikális felskálázás a PremiumV2 szintű csomag, ha az alapul szolgáló App Service-környezet PremiumV2 nem támogatja.  Lásd: [egy nem támogatott erőforrás csoport és a régió kombinációja a vertikális felskálázás](#unsupported) további részletekért.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Egy nem támogatott erőforrás csoport és a régió kombinációja a vertikális felskálázás

Ha az alkalmazás fut, az App Service-környezet ahol **PremiumV2** nem érhető el, vagy ha az alkalmazás fut egy régióban jelenleg nem támogatja a **PremiumV2**, telepítse újra az alkalmazást kell használja ki **PremiumV2**.  Erre két lehetősége van:

- Hozzon létre egy **új** erőforrás csoportból, és hozzon létre egy **új** terv webalkalmazás és app Service-ben a **új** erőforráscsoport létrehozása közben a kívánt Azure-régió kiválasztása a folyamat.  Ön **kell** válassza ki a **PremiumV2** terv egyidejűleg jön létre új app service-csomag.  Ez biztosítja, hogy erőforráscsoportot, App Service-csomag együttes használata, és az Azure-régióban eredményez az App Service-csomag egy App Service-környezet, amely támogatja a létrehozás alatt álló **PremiumV2**.  Ezt követően telepítse újra az alkalmazás kódjának a újonnan létrehozott alkalmazást, és az app service-csomagot. Ha ezt követően vertikális az App Service-csomag a megfelelő **PremiumV2** menteni a költségeket, és Ön továbbra is képesek lesznek sikeresen vertikális vissza a jövőben segítségével újból **PremiumV2**.
- Ha az alkalmazás már fut egy meglévő **prémium** tier, majd az alkalmazás összes Alkalmazásbeállítások, a kapcsolati karakterláncok és a azokat egy új app service-csomagot használó központi telepítés konfigurálása klónozhat **PremiumV2**.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    Az a **alkalmazásklónozási** oldalon hozhat létre egy App Service csomag **PremiumV2** a régióban szeretne, majd adja meg az alkalmazásbeállítások és a klónozni kívánt beállításait.

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Automatizálható az alkalmazás-létrehozás a **PremiumV2** szint szkriptekkel, használja a [Azure CLI-vel](/cli/azure/install-azure-cli) vagy [Azure PowerShell-lel](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure CLI

A következő parancs létrehoz egy App Service-csomagban lévő _P1V2_. A Cloud shellben futtathatja. A lehetőségeket `--sku` P1V2, amelyek _P2V2_, és _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

A következő parancs létrehoz egy App Service-csomagban lévő _P1V2_. A lehetőségeket `-WorkerSize` vannak _kis_, _Közepes_, és _nagy_.

```PowerShell
New-AzureRmAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>További erőforrások

[Az Azure-beli alkalmazás vertikális felskálázása](web-sites-scale.md)  
[Példányszám manuális vagy automatikus méretezése](../monitoring-and-diagnostics/insights-how-to-scale.md)