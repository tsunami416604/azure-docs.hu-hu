---
title: Azure Private link Service ARM-sablon
description: Private link Service ARM-sablon
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: 304ee8c1180c318dd6e99b6e81eb964e264951d1
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84667029"
---
# <a name="quickstart-create-a-private-link-service---resource-manager-template"></a>Rövid útmutató: Private link Service – Resource Manager-sablon létrehozása

Ebben a rövid útmutatóban egy Resource Manager-sablon használatával hoz létre egy privát kapcsolati szolgáltatást.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ezt a rövid útmutatót a [Azure Portal](create-private-link-service-portal.md), a [Azure PowerShell](create-private-link-service-powershell.md)vagy az [Azure CLI](create-private-link-service-cli.md)használatával is elvégezheti.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-link-service"></a>Privát kapcsolati szolgáltatás létrehozása

Ez a sablon létrehoz egy magánhálózati kapcsolati szolgáltatást.

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-privatelink-service/)származik.

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json" range="001-432" highlight="263-289":::

Több Azure-erőforrás van definiálva a sablonban:

- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks) : egy minden virtuális géphez
- [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers) : Load Balancer, amely elérhetővé teszi a szolgáltatást futtató virtuális gépeket
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : 2 hálózati adapter, egy az egyes virtuális gépekhez
- [**Microsoft. számítási/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : 2 virtuális gép, amely a szolgáltatást üzemelteti, a másik pedig a magánhálózati végponttal létesített kapcsolatok tesztelésére szolgál.
- [**Microsoft. számítás/virtualMachines/Extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions) : a webkiszolgálót telepítő bővítmény
- [**Microsoft. Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices) : Private link Service a szolgáltatás magánjellegűvé tétele érdekében
- [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicIpAddresses) : 2 nyilvános IP-cím, egy az egyes virtuális gépekhez
- [**Microsoft. Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints) : magánhálózati végpont a szolgáltatáshoz való hozzáféréshez

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Resource Manager-sablon üzembe helyezése az Azure-ban:

1. Válassza az **üzembe helyezés az Azure** -ban lehetőséget az Azure-ba való bejelentkezéshez és a sablon megnyitásához. A sablon létrehoz egy virtuális gépet, a standard Load balancert, a Private link Service-t, a privát végpontot, a hálózatot és a virtuális gépet az ellenőrzéshez.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. Válassza ki vagy hozza létre az erőforráscsoportot,
3. Írja be a virtuális gép rendszergazdai felhasználónevét és jelszavát.
4. Jelölje be **az Elfogadom a fenti feltételeket és kikötéseket** , majd válassza a **vásárlás**lehetőséget.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

> [!NOTE]
> az ARM-sablon egyedi nevet hoz létre a (z)<b>{UniqueID}</b> virtuális gép myConsumerVm, cserélje le a <b>{UniqueID}</b> értéket a generált értékre.

### <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Kapcsolódjon a _(z) {UniqueID}_ virtuális gép myConsumerVm az internetről a következőképpen:

1.  A portál keresősáv mezőjébe írja be a _myConsumerVm {UniqueID}_ értéket.

2.  Kattintson a **Csatlakozás** gombra. A **Kapcsolódás** gombra kattintva megnyílik a **virtuális géphez való kapcsolódás** .

3.  Válassza az **RDP-fájl letöltése**lehetőséget. Az Azure létrehoz egy RDP protokoll (_. rdp_) fájlt, és letölti a számítógépre.

4.  Nyissa meg a letöltött. rdp \* fájlt.

    a. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    b. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.
    
    > [!NOTE]
    > Előfordulhat, hogy a **More choices**  >  virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell választania**egy másik fiók használatával**.

5.  Válassza az **OK** lehetőséget.

6.  A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás**lehetőséget.

7.  Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.

### <a name="access-the-http-service-privately-from-the-vm"></a>A http-szolgáltatás elérése a virtuális gépről

Ebben a szakaszban a virtuális gépről a magánhálózati végpont használatával fog csatlakozni a http szolgáltatáshoz.

1.  A _myConsumerVm ({UniqueID})_ távoli asztal
2.  Nyisson meg egy böngészőt, és adja meg a magánhálózati végpont címeithttp://10.0.0.5/
3.  Megjelenik az alapértelmezett IIS-oldal

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs szüksége a privát kapcsolat szolgáltatással létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a Private link Service-t és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a következő `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure Private linkről](private-link-overview.md)
