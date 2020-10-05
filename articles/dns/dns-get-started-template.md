---
title: 'Gyors útmutató: Azure DNS zóna és a Record-Azure Resource Manager sablon létrehozása (ARM-sablon)'
titleSuffix: Azure DNS
description: A cikkből megtudhatja, hogyan hozhat létre DNS-zónát és -rekordot az Azure DNS-ben. Ez egy lépésenkénti útmutató, amellyel létrehozhatja és kezelheti az első DNS-zónáját és-rekordját Azure Resource Manager sablon (ARM-sablon) használatával.
services: dns
author: duongau
ms.service: dns
ms.topic: quickstart
ms.date: 09/8/2020
ms.author: duau
ms.openlocfilehash: 8e53e8ad26ddac1006a28fea2ddee9990533e8c9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "89647894"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-an-arm-template"></a>Rövid útmutató: Azure DNS zóna és rekord létrehozása ARM-sablonnal

Ez a rövid útmutató azt ismerteti, hogyan használható egy Azure Resource Manager sablon (ARM-sablon) egy olyan DNS-zóna létrehozásához, amely egy rekordot tartalmaz.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-dns-new-zone%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-azure-dns-new-zone) közül származik.

Ebben a rövid útmutatóban egy egyedi DNS-zónát fog létrehozni a azurequickstart utótaggal * <span>.</span> org*. Egy két IP-címre *mutató rekord is* a zónába kerül.

:::code language="json" source="~/quickstart-templates/101-azure-dns-new-zone/azuredeploy.json":::

Két Azure-erőforrás van definiálva a sablonban:

* [**Microsoft. Network/dnsZones**](/azure/templates/microsoft.network/dnsZones)
* [**Microsoft. Network/dnsZones/a**](/azure/templates/microsoft.network/dnsZones/A) (egy rekordnak a zónában való létrehozásához használatos)

Az Azure Traffic Manager-hoz kapcsolódó további sablonokért tekintse meg az [Azure Gyorsindítás sablonjait](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza a **kipróbálás** a következő kódrészletből lehetőséget a Azure Cloud Shell megnyitásához, majd kövesse az utasításokat az Azure-ba való bejelentkezéshez. 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-dns-new-zone/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Várjon, amíg megjelenik a-konzolon megjelenő kérdés.

1. A PowerShell-szkript másolásához válassza a **Másolás** az előző kódrészletből lehetőséget.

1. Kattintson a jobb gombbal a rendszerhéj-konzol ablaktáblára, majd válassza a **Beillesztés**lehetőséget.

1. Adja meg az értékeket.

    A sablon központi telepítése létrehoz egy zónát egy olyan rekorddal, amely két IP-címre mutat. Az erőforráscsoport neve a projekt neve **RG** hozzáfűzéssel.

    A sablon üzembe helyezése néhány másodpercig tart. Ha elkészült, a kimenet a következőhöz hasonló:

    :::image type="content" source="./media/dns-getstarted-template/create-dns-zone-powershell-output.png" alt-text="Azure DNS zóna Resource Manager-sablon PowerShell üzembe helyezési kimenete":::

A Azure PowerShell a sablon üzembe helyezésére szolgál. A Azure PowerShellon kívül használhatja a Azure Portal, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki az **erőforráscsoportok** elemet a bal oldali ablaktáblán.

1. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az erőforráscsoport alapértelmezett neve a projekt neve **RG** hozzáfűzéssel.

1. Az erőforráscsoport az alábbi, itt látható erőforrásokat tartalmazza:

    :::image type="content" source="./media/dns-getstarted-template/resource-group-dns-zone.png" alt-text="Azure DNS zóna Resource Manager-sablon PowerShell üzembe helyezési kimenete":::

1. Válassza ki a DNS-zónát a ** <span>azurequickstart</span> utótaggal. Ellenőrizze, hogy a** zóna megfelelően lett-e létrehozva egy **olyan** rekorddal, amely a **1.2.3.4** és a **1.2.3.5**értékére hivatkozik.

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-overview.png" alt-text="Azure DNS zóna Resource Manager-sablon PowerShell üzembe helyezési kimenete":::

1. Másolja az egyik névkiszolgálói nevet az előző lépésből.

1. Nyisson meg egy parancssort, és futtassa a következő parancsot:

   ```
   nslookup www.<dns zone name> <name server name>
   ```

   Például:

   ```
   nslookup www.2lwynbseszpam.azurequickstart.org ns1-09.azure-dns.com.
   ```

   A következő képernyőképhez hasonlóan kell megjelennie:

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-validation.png" alt-text="Azure DNS zóna Resource Manager-sablon PowerShell üzembe helyezési kimenete":::

A gazdagép neve: **www<span>. 2lwynbseszpam. azurequickstart.</span> a szervezeti** feloldja a **1.2.3.4** és a **1.2.3.5**, ugyanúgy, ahogy konfigurálta. Ez az eredmény ellenőrzi, hogy a névfeloldás megfelelően működik-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a DNS-zónával létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a DNS-zónát és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a következő `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozta a következőket:
* DNS-zóna
* A-rekord

Most, hogy létrehozta az első DNS-zónáját és-rekordját a Azure Resource Manager sablonnal, létrehozhat egy webalkalmazáshoz tartozó rekordokat egy egyéni tartományban.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)
