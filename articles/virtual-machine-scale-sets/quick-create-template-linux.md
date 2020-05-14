---
title: Rövid útmutató – linuxos virtuálisgép-méretezési csoport létrehozása Azure Resource Manager sablonnal
description: Ismerje meg, hogyan hozhat létre gyorsan Linux virtuálisgép-méretezési csoportot egy mintaalkalmazást üzembe helyező és automatikus méretezési szabályokat konfiguráló Azure Resource Manager-sablonnal
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: linux
ms.date: 03/27/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: f51bfa012c62e7acdd0aa2cd16279ec68702a72c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117327"
---
# <a name="quickstart-create-a-linux-virtual-machine-scale-set-with-an-azure-resource-manager-template"></a>Gyors útmutató: linuxos virtuálisgép-méretezési csoport létrehozása Azure Resource Manager sablonnal

A virtuálisgép-méretezési csoportok segítségével azonos, automatikus skálázású virtuális gépek csoportját hozhatja létre és kezelheti. A méretezési csoportban lévő virtuális gépek számát beállíthatja manuálisan, de automatikus méretezési szabályokat is megadhat az erőforrás-használat (például processzorhasználat, memóriaigény vagy hálózati forgalom) alapján. Egy Azure-terheléselosztó ezután elosztja a forgalmat a méretezési csoportban lévő virtuálisgép-példányok között. Ebben a rövid útmutatóban egy virtuálisgép-méretezési csoportot hozunk létre, és üzembe helyezünk egy mintaalkalmazást egy Azure Resource Manager-sablon használatával.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Nincsenek.

## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása

Az Azure Resource Manager-sablonok segítségével egymáshoz kapcsolódó erőforráscsoportokat helyezhet üzembe. Egyetlen sablonban hozhatja létre a virtuálisgép-méretezési csoportot, telepítheti az alkalmazásokat és konfigurálhatja az automatikus méretezési szabályokat. Különféle változók és paraméterek segítségével a sablon többször is felhasználható meglévő méretezési csoportok frissítésére vagy újabbak létrehozására. A sablonokat az Azure Portal, az Azure CLI vagy az Azure PowerShell használatával, illetve folyamatos integrációs (CI) / folyamatos továbbítási (CD) folyamatokkal helyezheti üzembe.

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/201-vmss-bottle-autoscale/)származik.

:::code language="json" source="~/quickstart-templates/201-vmss-bottle-autoscale/azuredeploy.json" range="1-330" highlight="176-264":::

Ezek az erőforrások a sablonban vannak definiálva:

- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft. bepillantások/autoscaleSettings**](/azure/templates/microsoft.insights/autoscalesettings)

#### <a name="define-a-scale-set"></a>Méretezési csoport meghatározása

A kiemelt rész a méretezési csoport erőforrás-definíciója. Méretezési csoport sablon alapján való létrehozásához definiálnia kell a megfelelő erőforrásokat. A virtuálisgép-méretezési csoport erőforrástípus alapvető elemei a következők:

| Tulajdonság                     | A tulajdonság leírása                                  | Példa sablonérték                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | A létrehozandó Azure-erőforrástípus                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | A méretezési csoport neve                                       | myScaleSet                                |
| location                     | A méretezési csoport létrehozásának helye                     | USA keleti régiója                                   |
| sku.name                     | A méretezési csoport egyes példányainak virtuálisgép-mérete                  | Standard_A1                               |
| sku.capacity                 | Az először létrehozandó virtuálisgép-példányok száma           | 2                                         |
| upgradePolicy.mode           | A virtuálisgép-példányok frissítésének módja módosítás esetén              | Automatikus                                 |
| imageReference               | A virtuálisgép-példányokhoz használandó platform vagy egyéni rendszerkép | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | Az egyes virtuálisgép-példányokhoz tartozó név előtagja                     | myvmss                                    |
| osProfile.adminUsername      | Az egyes virtuálisgép-példányokhoz tartozó felhasználónév                        | azureuser                                 |
| osProfile.adminPassword      | Az egyes virtuálisgép-példányokhoz tartozó jelszó                        | P@ssw0rd!                                 |

A méretezési csoport sablonjának testreszabásához módosíthatja a virtuális gép méretét vagy kezdeti kapacitását. Egy másik lehetőség, hogy más platformot vagy egyéni rendszerképet használjon.

#### <a name="add-a-sample-application"></a>Mintaalkalmazás hozzáadása

A méretezési csoport teszteléséhez telepítsen egy alapszintű webalkalmazást. A méretezési csoportok üzembe helyezésekor a virtuálisgép-bővítményekkel biztosíthatóak az üzembe helyezést követő konfigurációs és automatizálási feladatok, például az alkalmazások telepítése. A szkriptek az Azure Storage-ből vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. Ha alkalmazni szeretné valamelyik bővítményt a méretezési csoportra, egészítse ki az erőforrás előző példáját az *extensionProfile* szakasszal. A bővítményprofil általában az alábbi tulajdonságokat határozza meg:

- Bővítmény típusa
- Bővítmény kiadója
- Bővítmény verziója
- A konfigurációs vagy telepítési szkriptek helye
- A virtuálisgép-példányokon végrehajtandó parancsok

A sablon az egyéni szkriptek bővítményét használja a [Bottle](https://bottlepy.org/docs/dev/), a Python webes keretrendszer és egy egyszerű http-kiszolgáló telepítéséhez.

Két parancsfájl van definiálva a **fileUris**  -  *installserver.sh*és a *workserver.py*. A rendszer letölti ezeket a fájlokat a GitHubról, majd a *commandToExecute* futtatja `bash installserver.sh` az alkalmazás telepítését és konfigurálását.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablon üzembe helyezéséhez válassza a következő **üzembe helyezés az Azure-** ban gombot. A gomb megnyomására megnyílik az Azure Portal, betöltődik a teljes sablon, és a rendszer néhány paraméter megadását kéri (például a méretezési csomag neve, a példányok száma és a rendszergazdai hitelesítő adatok).

[![Sablon üzembe helyezése az Azure-ban](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

A Resource Manager-sablonok az Azure CLI használatával is üzembe helyezhetők:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

A kérések megválaszolásával adja meg a méretezési csoport nevét, a példányok számát és a virtuálisgép-példányok rendszergazdai hitelesítő adatait. A méretezési csoport és a támogató erőforrások létrehozása eltarthat néhány percig.

## <a name="test-the-deployment"></a>Az üzemelő példány tesztelése

Ha ellenőrizni szeretné, hogyan működik a méretezési csoport, lépjen egy böngészőben a mintául szolgáló webalkalmazáshoz. Kérje le a terheléselosztó nyilvános IP-címét az [az network public-ip list](/cli/azure/network/public-ip) paranccsal a következők szerint:

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Adja meg a terheléselosztó nyilvános IP-címét egy webböngészőben a következő formátumban *: http: \/ /publicIpAddress: 9000/do_work*. A terheléselosztó az egyik virtuálisgép-példányra terjeszti a forgalmat, ahogy az a következő példában látható:

![Alapértelmezett weboldal az NGINX-ben](media/virtual-machine-scale-sets-create-template/running-python-app.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, az [az group delete](/cli/azure/group) paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás. A `--no-wait` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét. A `--yes` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útbemutatóban egy Linux rendszerű méretezési csoportot hoztunk létre egy Azure-sablonnal, valamint az egyéni szkriptbővítménnyel egy alapszintű Python-webkiszolgálót telepítettünk a VM-példányokon. Ha bővebb információra van szüksége, lépjen tovább az Azure-beli virtuálisgép-méretezési csoportok létrehozásáról és kezeléséről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Azure-beli virtuálisgép-méretezési csoportok létrehozása és kezelése](tutorial-create-and-manage-cli.md)
