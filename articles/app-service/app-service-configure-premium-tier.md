---
title: PremiumV2 szintű csomag konfigurálása
description: Ismerje meg, hogyan javíthatja a webes, mobil- és API-alkalmazását az Azure App Service-ben az új PremiumV2-díjszabási szintre való méretezéssel.
keywords: app service, azure app service, méret, méretezhető, app service-csomag, app service ára
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 07/25/2018
ms.custom: seodec18
ms.openlocfilehash: f7f9f6d5617ad0f9be69c47ce514d395534fd892
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672207"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>PremiumV2-szint konfigurálása az Azure App Service szolgáltatáshoz

Az új **PremiumV2** tarifacsomag gyorsabb processzorokat, SSD-tárolókat biztosít, és megduplázza a meglévő tarifacsomagok memória-mag arányát. A teljesítményelőnye, hogy pénzt takaríthat meg, ha kevesebb példányban futtatja alkalmazásait. Ebben a cikkben megtudhatja, hogyan hozhat létre egy alkalmazást **a PremiumV2-rétegben,** vagy hogyan skálázhat fel egy alkalmazást **PremiumV2-szintre.**

## <a name="prerequisites"></a>Előfeltételek

Egy alkalmazás **premiumv2-re**történő felskálázásához olyan Azure App Service-alkalmazással kell rendelkeznie, amely **a PremiumV2-nél**alacsonyabb tarifaszinten fut, és az alkalmazásnak egy Olyan App Service-telepítésben kell futnia, amely támogatja a PremiumV2-t.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2 elérhetősége

A **PremiumV2** szint elérhető az App Service-hez _Windows_ és _Linux_rendszeren egyaránt.

**A PremiumV2** a legtöbb Azure-régióban elérhető. Ha meg szeretné tudni, hogy elérhető-e az Ön régiójában, futtassa a következő Azure CLI parancsot az [Azure Cloud Shellben:](../cloud-shell/overview.md)

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Alkalmazás létrehozása a PremiumV2-rétegben

Az App Service-alkalmazás tarifacsomagja az [App Service-csomagban](overview-hosting-plans.md) van definiálva, amelyen fut. Létrehozhat egy App Service-csomagot önmagában vagy az alkalmazás létrehozása részeként.

Az App Service-csomag azure <a href="https://portal.azure.com" target="_blank">portalon</a>történő konfigurálásakor válassza a **Tarifacsomag**lehetőséget. 

Válassza **a Termelés**lehetőséget, majd válassza a **P1V2,** **P2V2**vagy **P3V2**lehetőséget, majd kattintson **az Alkalmaz**gombra.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Ha nem látja **a P1V2,** **P2V2**és **P3V2** beállításokat, vagy ha a beállítások szürkén jelennek meg, akkor **a PremiumV2** valószínűleg nem érhető el az alkalmazásszolgáltatás-csomagot tartalmazó, alapul szolgáló App Service-telepítésben. További részletekért lásd: [Felskálázás nem támogatott erőforráscsoport- és régiókombinációból.](#unsupported)

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Meglévő alkalmazás felskálázása PremiumV2 szintre

Mielőtt egy meglévő alkalmazást **PremiumV2-szintre** skálázna, győződjön meg arról, hogy a **PremiumV2** elérhető. További információt a [PremiumV2 elérhetősége című témakörben talál.](#availability) Ha nem érhető el, olvassa el a [Felskálázás nem támogatott erőforráscsoport- és régiókombinációból című témakört.](#unsupported)

Az üzemeltetési környezettől függően a felskálázás további lépéseket igényelhet. 

Az <a href="https://portal.azure.com" target="_blank">Azure Portalon</a>nyissa meg az App Service-alkalmazáslapját.

Az App Service-alkalmazás lap bal oldali navigációs lapján válassza a **Felskálázás (App Service-csomag)** lehetőséget.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Válassza **a Termelés**lehetőséget, majd válassza a **P1V2,** **P2V2**vagy **P3V2**lehetőséget, majd kattintson **az Alkalmaz**gombra.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Ha a művelet sikeresen befejeződött, az alkalmazás áttekintő oldala azt mutatja, hogy most egy **PremiumV2-rétegben** van.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Ha hibaüzenetet kap

Egyes App Service-csomagok nem skálázhatók fel a PremiumV2 szintre, ha az alapul szolgáló App Service-telepítés nem támogatja a PremiumV2-t.  További részletekért lásd: [Felskálázás nem támogatott erőforráscsoport- és régiókombinációból.](#unsupported)

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Felskálázás nem támogatott erőforráscsoport- és régiókombinációból

Ha az alkalmazás olyan App Service-telepítésben fut, ahol a **PremiumV2** nem érhető el, vagy ha az alkalmazás olyan régióban fut, amely jelenleg nem támogatja **a PremiumV2**szolgáltatást, újra kell telepítenie az alkalmazást a **PremiumV2**előnyeinek kihasználásához.  Erre két lehetősége van:

- Hozzon létre egy **új** erőforráscsoportot, majd hozzon létre egy **új** alkalmazást és az App Service-csomagot az **új** erőforráscsoportban, és válassza ki a kívánt Azure-régiót a létrehozási folyamat során.  Az új alkalmazásszolgáltatási csomag létrehozásakor ki **kell** választania a **PremiumV2-csomagot.**  Ez biztosítja az erőforráscsoport, az App Service-csomag és az Azure-régió kombinációját, így az App Service-csomag a **PremiumV2**szolgáltatást támogató App Service-telepítésben jön létre.  Ezután telepítse újra az alkalmazáskódot az újonnan létrehozott alkalmazás- és alkalmazásszolgáltatási csomagba. Ha szükséges, akkor ezt követően skála az App Service-csomag le **PremiumV2** a költségek csökkentése érdekében, és akkor is képes lesz arra, hogy sikeresen skála vissza újra a jövőben **a PremiumV2**.
- Ha az alkalmazás már fut egy meglévő **prémium** szinten, akkor klónozhatja az alkalmazást az összes alkalmazásbeállítással, kapcsolati karakterláncokkal és üzembe helyezési konfigurációval egy új alkalmazásszolgáltatási csomagba, amely **PremiumV2-t**használ.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    A **Klónozás alkalmazás** lapon létrehozhat egy App Service-csomagot a **PremiumV2** használatával a kívánt régióban, és megadhatja a klónozni kívánt alkalmazásbeállításokat és -konfigurációt.

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Az [Azure CLI](/cli/azure/install-azure-cli) vagy az [Azure PowerShell](/powershell/azure/overview)használatával automatizálhatja az alkalmazások létrehozását a **PremiumV2-rétegben** parancsfájlokkal.

### <a name="azure-cli"></a>Azure CLI

A következő parancs létrehoz egy App Service-csomagot a _P1V2_alkalmazásban. Futtathatja a Cloud Shellben. A lehetőségek `--sku` a P1V2, _P2V2_, és _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő parancs létrehoz egy App Service-csomagot a _P1V2_alkalmazásban. A lehetőségek: `-WorkerSize` _Kicsi_, _Közepes_és _Nagy_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>További erőforrások

[Alkalmazás vertikális felméretezése az Azure-ban](manage-scale-up.md)  
[Példányszám manuális vagy automatikus méretezése](../monitoring-and-diagnostics/insights-how-to-scale.md)
