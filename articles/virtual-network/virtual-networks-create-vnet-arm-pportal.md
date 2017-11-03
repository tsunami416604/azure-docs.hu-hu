---
title: "Hozzon létre egy Azure virtuális hálózatra, több alhálózattal |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre virtuális hálózatot, több alhálózattal az Azure-ban."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: f82a95ec9543b2d53ef28bf7f15315e23cf4893a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Hozzon létre egy virtuális hálózatot, több alhálózattal

Ebben az oktatóanyagban útmutató alapszintű Azure virtuális hálózat létrehozása, amely rendelkezik a nyilvános és titkos különálló alhálózatokat. Erőforrások virtuális hálózatok kommunikálhatnak egymással, és más hálózatok, virtuális hálózathoz csatlakozó erőforrásokkal. Azure-erőforrások, például a virtuális gépek, az App Service-környezetek, a virtuálisgép-méretezési csoportok, a Azure HDInsight és a felhőszolgáltatások hozhat létre a virtuális hálózaton belül az azonos vagy különböző alhálózatokon. Erőforrások létrehozása, különböző alhálózatokon lehetővé teszi bejövő és kimenő adatforgalma alhálózat egymástól függetlenül a hálózati forgalom szűrésére [hálózati biztonsági csoportok](virtual-networks-create-nsg-arm-pportal.md), és a [alhálózatok közötti forgalmat](virtual-network-create-udr-arm-ps.md) a hálózaton keresztül virtuális készülékek, például egy tűzfal, ha úgy dönt, hogy. 

A következő szakaszok tartalmazzák a lépéseket, amelyek segítségével virtuális hálózat létrehozása a [Azure-portálon](#portal), az Azure parancssori felület ([Azure CLI](#azure-cli)), [Azure PowerShell](#powershell), és egy [Azure Resource Manager sablon](#resource-manager-template). Az eredmény megegyezik, függetlenül attól, hogy a virtuális hálózat létrehozásához használhat. Egy eszköz hivatkozásra kattintva az oktatóanyag szakaszt. További információk [virtuális hálózati](virtual-network-manage-network.md) és [alhálózati](virtual-network-manage-subnet.md) beállításait.

Ez a cikk nyújt tájékoztatást a Resource Manager telepítési modell, amely a üzembe helyezési modellel, azt javasoljuk, amikor új virtuális hálózatok létrehozása a virtuális hálózat létrehozása. Ha egy virtuális hálózat (klasszikus) létrehozásához szüksége, tekintse meg [hozzon létre egy virtuális hálózatot (klasszikus)](create-virtual-network-classic.md). Ha nem ismeri az Azure üzembe helyezési modellel, lásd: [megértéséhez Azure üzembe helyezési modellel](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="portal"></a>Azure-portálon

1. A böngészőben, válassza a [Azure-portálon](https://portal.azure.com). Jelentkezzen be a [Azure-fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Ha nincs Azure-fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Kattintson a portál **+ új** > **hálózati** > **virtuális hálózati**.
3. Az a **virtuális hálózat létrehozása** panelen adja meg a következő értékeket, és kattintson a **létrehozása**:

    |Beállítás|Érték|
    |---|---|
    |Név|myVNet|
    |Címtér|10.0.0.0/16|
    |Alhálózat neve|Nyilvános|
    |Alhálózati címtartomány|10.0.0.0/24|
    |Erőforráscsoport|Hagyja **hozzon létre új** kiválasztva, és írja be **myResourceGroup**.|
    |Egyes előfizetésekhez és helyekhez|Válassza ki az egyes előfizetésekhez és helyekhez.

    Ha most ismerkedik az Azure-ba, további információ [erőforráscsoportok](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [előfizetések](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), és [helyek](https://azure.microsoft.com/regions) (más néven *régiók*).
4. A portál csak egy alhálózat is létrehozhat, ha a virtuális hálózat létrehozása. Ebben az oktatóanyagban létrehoz egy második alhálózatot a virtuális hálózat létrehozása után. Később létrehozhat az internetről elérhető erőforrások a **nyilvános** alhálózat. Is létrehozhat, amelyek nem érhetők el az interneten lévő erőforrásokat a **titkos** alhálózat. A második alhálózat létrehozásához az **keresési erőforrások** a lap tetején mezőbe írja be **myVnet**. A keresési eredmények között kattintson **myVnet**. Ha ezzel a névvel több virtuális hálózat előfizetése van, ellenőrizze az erőforráscsoportok szereplő minden egyes virtuális hálózati. Győződjön meg arról, hogy kattintson a **myVnet** keresési eredmények, amelyek erőforráscsoportban **myResourceGroup**.
5. A a **myVnet** panel alatt **beállítások**, kattintson a **alhálózatok**.
6. Az a **myVnet - alhálózatok** panelen kattintson a **+ alhálózati**.
7. Az a **alhálózat hozzáadása** panelen a **neve**, adja meg **titkos**. A **-címtartományt**, adja meg **10.0.1.0/24**.  Kattintson az **OK** gombra.
8. Az a **myVnet - alhálózatok** panelt, tekintse át az alhálózatokat. Megtekintheti a **nyilvános** és **titkos** létrehozott alhálózatokat.
9. **Választható lehetőség:** végezze el a felsorolt további oktatóanyagok [további lépések](#next-steps) hálózati forgalom szűrésére minden egyes alhálózatot a hálózati biztonsági csoportokkal, egy hálózati virtuális készüléken keresztül alhálózatok közötti forgalom a bejövő és kimenő adatforgalma , vagy a virtuális hálózathoz csatlakozni más virtuális hálózatok és a helyszíni hálózatokhoz.
10. **Választható lehetőség:** törli az Ön által létrehozott ebben az oktatóanyagban szereplő lépések végrehajtásával erőforrást [törli az erőforrást](#delete-portal).

## <a name="azure-cli"></a>Azure CLI

Az Azure parancssori felület parancsait megegyeznek, hogy a parancsok a Windows, Linux vagy macOS hajtható végre. Vannak azonban parancsfájlok különbségei operációs rendszer ismertetése. A parancsfájl a következő lépések a rendszerhéjakba hajtja végre. 

1. [Telepítse és konfigurálja az Azure parancssori felület](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Ellenőrizze, hogy a telepített Azure CLI legújabb verziója. Segítség kérése parancssori felület parancsait, írja be a következőt `az <command> --help`. Ahelyett, hogy a parancssori felület és a szükséges előfeltételek telepítése, az Azure-felhő rendszerhéj is használhatja. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A felhő rendszerhéj az Azure parancssori felület előtelepített és konfigurált a fiókhoz rendelkezik. A felhő rendszerhéj használatához kattintson a felhő rendszerhéj (**> _**) gomb tetején a [portal](https://portal.azure.com) vagy egyszerűen kattintson a *kipróbálás* gombra a következő lépések a. 
2. Ha a parancssori felület helyben fut, jelentkezzen be Azure-bA a `az login` parancsot. A felhő-rendszerhéj használata, ha már jelentkezett be.
3. Tekintse át a következő parancsfájl és észrevételeit. A böngészőben másolja a parancsfájlt, és illessze be a CLI-munkamenethez:

    ```azurecli-interactive
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet named Public.
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet named Private in the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name myVnet \
      --resource-group myResourceGroup
    ```
    
4. Miután befejezte a parancsfájlt futtat, tekintse át a virtuális hálózati alhálózat. Másolja a következő parancsot, és illessze be a CLI-munkamenethez:

    ```azurecli
    az network vnet subnet list --resource-group myResourceGroup --vnet-name myVnet --output table
    ```

5. **Választható lehetőség:** végezze el a felsorolt további oktatóanyagok [további lépések](#next-steps) hálózati forgalom szűrésére minden egyes alhálózatot a hálózati biztonsági csoportokkal, egy hálózati virtuális készüléken keresztül alhálózatok közötti forgalom a bejövő és kimenő adatforgalma , vagy a virtuális hálózathoz csatlakozni más virtuális hálózatok és a helyszíni hálózatokhoz.
6. **Nem kötelező**: törli az Ön által létrehozott ebben az oktatóanyagban szereplő lépések végrehajtásával erőforrást [törli az erőforrást](#delete-cli).

## <a name="powershell"></a>PowerShell

1. Telepítse a PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) moduljának legújabb verzióját. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. A PowerShell-munkamenetben jelentkezzen be Azure-bA a [Azure-fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) használatával a `login-azurermaccount` parancsot.

3. Tekintse át a következő parancsfájl és észrevételeit. A böngészőben másolja a parancsfájlt, és illessze be a PowerShell-munkamenethez:

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location eastus
    
    # Create the public and private subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Location eastus `
      -Name myVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    ```

4. Tekintse át a virtuális hálózati alhálózat, másolja be a következő parancsot, és illessze be a PowerShell-munkamenethez:

    ```powershell
    $Vnet.subnets | Format-Table Name, AddressPrefix
    ```

5. **Választható lehetőség:** végezze el a felsorolt további oktatóanyagok [további lépések](#next-steps) hálózati forgalom szűrésére minden egyes alhálózatot a hálózati biztonsági csoportokkal, egy hálózati virtuális készüléken keresztül alhálózatok közötti forgalom a bejövő és kimenő adatforgalma , vagy a virtuális hálózathoz csatlakozni más virtuális hálózatok és a helyszíni hálózatokhoz.
6. **Nem kötelező**: törli az Ön által létrehozott ebben az oktatóanyagban szereplő lépések végrehajtásával erőforrást [törli az erőforrást](#delete-powershell).

## <a name="resource-manager-template"></a>Resource Manager-sablon

Egy virtuális hálózatot az Azure Resource Manager-sablon segítségével telepíthet. A sablonokkal kapcsolatos további tudnivalókért lásd: [Mi az erőforrás-kezelő](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment). A sablon eléréséhez és a paraméterekkel kapcsolatos további, tekintse meg a [hozzon létre egy virtuális hálózatot, két alhálózattal](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) sablont. A sablon segítségével telepíthet a [portal](#template-portal), [Azure CLI](#template-cli), vagy [PowerShell](#template-powershell).

A sablon telepítése után választható lépéseket:

1. Fejezze be a felsorolt további oktatóanyagok [további lépések](#next-steps) hálózati forgalom mindkét minden egyes alhálózatot a hálózati biztonsági csoportokkal, egy hálózati virtuális készüléken keresztül alhálózatok közötti forgalom szűrésére, vagy csatlakoztassa a virtuális más virtuális hálózatok és a helyszíni hálózatokhoz hálózaton.
2. Törli az Ön által létrehozott ebben az oktatóanyagban szereplő bármely alszakaszaiban lépések végrehajtásával erőforrást [törli az erőforrást](#delete).

### <a name="template-portal"></a>Azure-portálon

1. A böngészőben nyissa meg a [sablonlap](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets).
2. Kattintson a **az Azure telepítéséhez** gombra. Ha még nem jelentkezett be az Azure-ba, jelentkezzen be az Azure portál bejelentkezési képernyőn megjelenő.
3. Jelentkezzen be a portál használatával a [Azure-fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Ha nincs Azure-fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Adja meg a következő értékeket a Paraméterek:

    |Paraméter|Érték|
    |---|---|
    |Előfizetés|Jelölje ki az előfizetését|
    |Erőforráscsoport|myResourceGroup|
    |Hely|Válasszon ki egy helyet|
    |Vnet neve|myVNet|
    |Virtuális hálózat címelőtag|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Nyilvános|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Saját|

5. Fogadja el a használati feltételeket, és kattintson a **beszerzési** központi telepítése a virtuális hálózat.

### <a name="template-cli"></a>Az Azure parancssori felület

1. [Telepítse és konfigurálja az Azure parancssori felület](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Ellenőrizze, hogy a telepített Azure CLI legújabb verziója. Segítség kérése parancssori felület parancsait, írja be a következőt `az <command> --help`. Ahelyett, hogy a parancssori felület és a szükséges előfeltételek telepítése, az Azure-felhő rendszerhéj is használhatja. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A felhő rendszerhéj az Azure parancssori felület előtelepített és konfigurált a fiókhoz rendelkezik. A felhő rendszerhéj használatához kattintson a felhő rendszerhéj **> _** gomb tetején a [portal](https://portal.azure.com), vagy kattintson a **kipróbálás** gombra a következő lépések a. 
2. Ha a parancssori felület helyben fut, jelentkezzen be Azure-bA a `az login` parancsot. A felhő-rendszerhéj használata, ha már jelentkezett be.
3. Hozzon létre egy erőforráscsoportot a virtuális hálózat, másolja be a következő parancsot, és illessze be a CLI-munkamenethez:

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    ```
    
4. Telepítheti a sablon használatával a következő paraméterek közül:
    - **Alapértelmezett paraméterértékek**. Írja be a következő parancsot:
    
        ```azurecli-interactive
        az group deployment create --resource-group myResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
        ```
    - **Egyéni paraméterértékek**. Töltse le, és módosítsa a sablon, a sablon telepítése előtt. Is telepíthet a sablon parancssori paraméterek használatával, vagy a sablon telepítéséhez egy külön paraméterek fájllal. A sablon és a paraméterek fájlok letöltéséhez kattintson a **keresse meg a Githubon** gombra a [hozzon létre egy virtuális hálózatot, két alhálózattal](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) mintasablon lapot. A github webhelyen, kattintson a **azuredeploy.parameters.json** vagy **azuredeploy.json** fájlt. Kattintson a **Raw** gombra kattintva jelenítse meg a fájlt. A böngészőben másolja a fájl tartalmát. A tartalom mentése fájlba a számítógépen. Módosítsa a sablon paraméter értékét, vagy a sablon telepítéséhez egy külön paraméterek fájllal.  

    E módszerekkel sablonok telepítésével kapcsolatos további tudnivalókért írja be a következőt `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Telepítse a PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) moduljának legújabb verzióját. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. A PowerShell-munkamenetben, a bejelentkezéshez a [Azure-fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account), adja meg `login-azurermaccount`.
3. Hozzon létre egy erőforráscsoportot a virtuális hálózat, írja be a következő parancsot:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location eastus
    ```
    
4. Telepítheti a sablon használatával a következő paraméterek közül:
    - **Alapértelmezett paraméterértékek**. Írja be a következő parancsot:
    
        ```powershell
        New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName myResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json
        ```
        
    - **Egyéni paraméterértékek**. Töltse le, és módosítsa a sablon telepítése előtt. Is telepíthet a sablon parancssori paraméterek használatával, vagy a sablon telepítéséhez egy külön paraméterek fájllal. A sablon és a paraméterek fájlok letöltéséhez kattintson a **keresse meg a Githubon** gombra a [hozzon létre egy virtuális hálózatot, két alhálózattal](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) mintasablon lapot. A github webhelyen, kattintson a **azuredeploy.parameters.json** vagy **azuredeploy.json** fájlt. Kattintson a **Raw** gombra kattintva jelenítse meg a fájlt. A böngészőben másolja a fájl tartalmát. A tartalom mentése fájlba a számítógépen. Módosítsa a sablon paraméter értékét, vagy a sablon telepítéséhez egy külön paraméterek fájllal.  

    E módszerekkel sablonok telepítésével kapcsolatos további tudnivalókért írja be a következőt `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Erőforrások törlése

Ez az oktatóanyag befejezése után előfordulhat, hogy törölni kívánja az erőforrásokat, amelyek hozott létre, így használati költségek. Erőforráscsoport törlésével, az erőforráscsoport összes erőforrást is törli.

### <a name="delete-portal"></a>Azure-portálon

1. A portál keresési mezőbe, írja be a **myResourceGroup**. A keresési eredmények között kattintson **myResourceGroup**.
2. Az a **myResourceGroup** panelen kattintson a **törlése** ikonra.
3. A törlés megerősítéséhez a a **típus az ERŐFORRÁSCSOPORT neve** adja meg a **myResourceGroup**, és kattintson a **törlése**.

### <a name="delete-cli"></a>Az Azure parancssori felület

Adja meg egy parancssori munkamenetet a következő parancsot:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

A PowerShell-munkamenetben írja be a következő parancsot:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

- Minden virtuális hálózati és alhálózati beállításaival kapcsolatos további tudnivalókért lásd: [virtuális hálózatok kezeléséhez](virtual-network-manage-network.md#view-vnet) és [kezelheti a virtuális hálózati alhálózat](virtual-network-manage-subnet.md#create-subnet). Számos közül a virtuális hálózatok és alhálózatok éles környezetben különbözőek a követelmények teljesítéséhez.
- Bejövő és kimenő forgalmat szűrés létrehozásával és alkalmazásával [hálózati biztonsági csoportok](virtual-networks-nsg.md) alhálózatokra.
- Hozzon létre egy hálózati virtuális készüléken keresztül alhálózatok közötti forgalmat [felhasználó által definiált útvonalak](virtual-network-create-udr-arm-ps.md) és az útvonalak alkalmazása az egyes alhálózatokon.
- Hozzon létre egy [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy egy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gép egy meglévő virtuális hálózatban.
- Hozzon létre két virtuális hálózatok csatlakoztatása egy [virtuális hálózati társviszony-létesítés](virtual-network-peering-overview.md) a virtuális hálózatok között.
- A virtuális hálózati csatlakozás egy helyszíni hálózat segítségével egy [VPN-átjáró](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) körön.
