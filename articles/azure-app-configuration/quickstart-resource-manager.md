---
title: Automatizált virtuális gépek üzembe helyezése az Azure-alkalmazás konfigurálásával – rövid útmutató
description: Ez a rövid útmutató azt ismerteti, hogyan használható a Azure PowerShell modul és Azure Resource Manager sablonok egy Azure-alkalmazás konfigurációs tárolójának üzembe helyezéséhez. Ezután a tárolóban lévő értékek használatával helyezzen üzembe egy virtuális gépet.
author: lisaguthrie
ms.author: lcozzens
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 9b609d4571d6240f428a0210aa5108ff19dc753b
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235179"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template-arm-template"></a>Gyors útmutató: automatikus virtuálisgép-telepítés alkalmazás-konfigurációval és Resource Manager-sablonnal (ARM-sablon)

Megtudhatja, hogyan használhatók Azure Resource Manager sablonok és Azure PowerShell egy Azure-alkalmazás konfigurációs tárolójának üzembe helyezéséhez, a kulcs-értékeknek az áruházba való felvételéhez, valamint a tárolóban lévő kulcs-értékek használatáról egy Azure-erőforrás, például egy Azure-beli virtuális gép üzembe helyezéséhez ebben a példában.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-templates"></a>A sablonok áttekintése

Az ebben a rövid útmutatóban használt sablonok az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/)származnak. Az [első sablon](https://azure.microsoft.comresources/templates/101-app-configuration-store/) létrehoz egy alkalmazás-konfigurációs tárolót:

:::code language="json" source="~/quickstart-templates/101-app-configuration-store/azuredeploy.json" range="1-37" highlight="27-35":::

A sablonban egyetlen Azure-erőforrás van definiálva:

- [Microsoft. AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2019-10-01/configurationstores): hozzon létre egy alkalmazás-konfigurációs tárolót.

A [második sablon](https://azure.microsoft.com/resources/templates/101-app-configuration/) egy virtuális gépet hoz létre a tárolóban található kulcs-értékekkel. Ennek a lépésnek a megkezdése előtt hozzá kell adnia a kulcs-értékeket a portál vagy az Azure CLI használatával.

:::code language="json" source="~/quickstart-templates/101-app-configuration/azuredeploy.json" range="1-217" highlight="77, 181,189":::

## <a name="deploy-the-templates"></a>A sablonok üzembe helyezése

### <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy alkalmazás-konfigurációs tárolót.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

1. Válassza ki vagy adja meg a következő értékeket.

    - **előfizetés**: válassza ki az alkalmazás konfigurációs tárolójának létrehozásához használt Azure-előfizetést.
    - **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához, ha nem szeretne meglévő erőforráscsoportot használni.
    - **Régió**: válassza ki az erőforráscsoport helyét.  Például az **USA keleti**régiója.
    - **Konfigurációs tár neve**: adjon meg egy új alkalmazás-konfigurációs tároló nevét.
    - **Hely**: határozza meg az alkalmazás konfigurációs tárolójának helyét.  Használja az alapértelmezett értéket.
    - **SKU neve**: adja meg az alkalmazás-konfigurációs tároló SKU-nevét. Használja az alapértelmezett értéket.

1. Válassza a **Felülvizsgálat + létrehozás** lehetőséget.
1. Győződjön meg arról, hogy a lapon az **Érvényesítés sikeres**volt, majd válassza a **Létrehozás**lehetőséget.

Jegyezze fel az erőforráscsoport nevét és az alkalmazás konfigurációs tárolójának nevét.  Ezekre az értékekre szüksége lesz a virtuális gép telepítésekor
### <a name="add-vm-configuration-key-values"></a>Virtuális gép konfigurációs kulcsának hozzáadása – értékek

Az alkalmazás-konfigurációs tároló létrehozása után a Azure Portal vagy az Azure CLI használatával adhat hozzá kulcs-értékeket a tárolóhoz.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd navigáljon az újonnan létrehozott alkalmazás-konfigurációs tárolóhoz.
1. A bal oldali menüben válassza a **Configuration Explorer** lehetőséget.
1. A **Létrehozás** gombra kattintva adja hozzá a következő kulcs-érték párokat:

   |Kulcs|Érték|Címke|
   |-|-|-|
   |windowsOsVersion|2019 – Datacenter|sablon|
   |diskSizeGB|1023|sablon|

   A **tartalom típusának** megtartása üres.

Az Azure CLI használatához lásd: [a kulcs-értékek használata az Azure-alkalmazás konfigurációs tárolójában](./scripts/cli-work-with-keys.md).

### <a name="deploy-vm-using-stored-key-values"></a>Virtuális gép üzembe helyezése tárolt kulcs-értékek használatával

Most, hogy hozzáadta a kulcs-értékeket a tárolóhoz, készen áll a virtuális gép üzembe helyezésére egy Azure Resource Manager sablon használatával. A sablon a létrehozott **windowsOsVersion** és **diskSizeGB** kulcsokra hivatkozik.

> [!WARNING]
> Az ARM-sablonok nem hivatkozhatnak olyan alkalmazás-konfigurációs tároló kulcsaira, amelyeken engedélyezve van a privát kapcsolat.

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy virtuális gépet az alkalmazás konfigurációs tárolójában tárolt kulcs-értékek használatával.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration%2Fazuredeploy.json)

1. Válassza ki vagy adja meg a következő értékeket.

    - **előfizetés**: válassza ki a virtuális gép létrehozásához használt Azure-előfizetést.
    - **Erőforráscsoport**: adja meg ugyanazt az erőforráscsoportot, mint az alkalmazás konfigurációs tárolóját, vagy válassza az **új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához.
    - **Régió**: válassza ki az erőforráscsoport helyét.  Például az **USA keleti**régiója.
    - **Hely**: határozza meg a virtuális gép helyét. használja az alapértelmezett értéket.
    - Rendszergazdai **Felhasználónév**: Itt adhatja meg a virtuális gép rendszergazdai felhasználónevét.
    - Rendszergazdai **jelszó**: a virtuális gép rendszergazdai jelszavának megadása.
    - **Tartománynév címkéje**: adjon meg egy egyedi tartománynevet.
    - **Storage-fiók neve**: adjon meg egy egyedi nevet a virtuális géphez társított Storage-fiókhoz.
    - **Alkalmazás konfigurációs tárolójának erőforráscsoport**: adja meg az alkalmazás konfigurációs tárolóját tartalmazó erőforráscsoportot.
    - **Alkalmazás-konfigurációs tár neve**: adja meg az Azure-alkalmazás konfigurációs tárolójának nevét.
    - **VM SKU-kulcs**: a **windowsOsVersion**meghatározása.  Ez az a kulcs-érték neve, amelyet a tárolóhoz adott hozzá.
    - **Lemez mérete kulcs**: határozza meg a **diskSizeGB**. Ez az az érték, amelyet a tárolóhoz adott hozzá.

1. Válassza a **Felülvizsgálat + létrehozás** lehetőséget.
1. Győződjön meg arról, hogy a lapon az **Érvényesítés sikeres**volt, majd válassza a **Létrehozás**lehetőséget.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd navigáljon az újonnan létrehozott virtuális géphez.
1. Válassza az **Áttekintés** lehetőséget a bal oldali menüben, és ellenőrizze, hogy az **SKU** a **2019-Datacenter**.
1. Válassza ki a bal oldali menüből a **lemezeket** , és ellenőrizze, hogy az adatlemez mérete **2013**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, az alkalmazás konfigurációs tárolóját, a virtuális gépet és az összes kapcsolódó erőforrást. Ha a későbbiekben szeretné használni az alkalmazás konfigurációs tárolóját vagy a virtuális gépet, kihagyhatja a törlését. Ha nem kívánja tovább használni ezt a feladatot, törölje az ebben a rövid útmutatóban létrehozott összes erőforrást a következő parancsmag futtatásával:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy virtuális gépet egy Azure Resource Manager sablonnal és a kulcs-értékekkel az Azure-alkalmazás konfigurációjától.

További információt az Azure-alkalmazás konfigurálásával kapcsolatos egyéb alkalmazások létrehozásáról a következő cikkben talál:

> [!div class="nextstepaction"]
> [Gyors útmutató: ASP.NET Core-alkalmazás létrehozása az Azure-alkalmazás konfigurálásával](quickstart-aspnet-core-app.md)
