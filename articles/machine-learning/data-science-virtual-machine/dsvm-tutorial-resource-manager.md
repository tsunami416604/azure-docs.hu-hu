---
title: 'Gyors útmutató: Data Science VM – Resource Manager-sablon létrehozása'
titleSuffix: Azure Data Science Virtual Machine
description: Ebben a rövid útmutatóban egy Azure Resource Manager sablonnal végezheti el a Data Science Virtual Machine gyors üzembe helyezését
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 06/10/2020
ms.service: machine-learning
ms.subservice: data-science-vm
ms.topic: quickstart
ms.openlocfilehash: 7e3e45d96839be06deec238deccdc9873492d68f
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84660249"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-a-resource-manager-template"></a>Gyors útmutató: Ubuntu-Data Science Virtual Machine létrehozása Resource Manager-sablonnal
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez a rövid útmutató bemutatja, hogyan hozhat létre Ubuntu 18,04 Data Science Virtual Machine Azure Resource Manager sablonnal. Az adatelemzési Virtual Machines az adatelemzési és gépi tanulási keretrendszerek és eszközök által előre feltöltött felhőalapú virtuális gépek. GPU-alapú számítási erőforrásokon való üzembe helyezéskor az összes eszköz és könyvtár a GPU használatára van konfigurálva. 

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree) a Kezdés előtt

* Ha a jelen dokumentumban a CLI-parancsokat a **helyi környezetből**szeretné használni, szüksége lesz az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -re

## <a name="create-a-workspace"></a>Munkaterület létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/)származik. A cikk teljes sablonja túl hosszú ahhoz, hogy megjelenjen itt. A teljes sablon megtekintéséhez lásd: [azuredeploy.js](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json). Itt látható a DSVM sajátosságait meghatározó rész:

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" range="235-276":::

A következő erőforrások vannak definiálva a sablonban:

* [Microsoft. számítási/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): felhőalapú virtuális gép létrehozása. Ebben a sablonban a virtuális gép az Ubuntu 18,04-as verzióját futtató Data Science Virtual Machine van konfigurálva.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése 

Ha az Azure CLI-vel szeretné használni a sablont, jelentkezzen be, és válassza ki az előfizetését (lásd: [Bejelentkezés az Azure CLI-vel](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)). Majd futtassa ezt:

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the authentication type (must be 'password' or 'sshPublicKey') :" authenticationType &&
read -p "Enter the login name for the administrator account (may not be 'admin'):" adminUsername &&
read -p "Enter administrator account secure string (value of password or ssh public key):" adminPasswordOrKey &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters adminUsername=$adminUsername authenticationType=$authenticationType adminPasswordOrKey=$adminPasswordOrKey && 
echo "Press [ENTER] to continue ..." &&
read
```

A fenti parancs futtatásakor írja be a következőket:

1. Azon erőforráscsoport neve, amelyet létre szeretne hozni a DSVM és a kapcsolódó erőforrások tárolásához. 
1. Az Azure-beli hely, ahol az üzembe helyezést el szeretné végezni
1. A használni kívánt hitelesítési típus (adja meg a karakterláncot `password` vagy `sshPublicKey` )
1. A rendszergazdai fiók bejelentkezési neve (ez az érték nem lehet `admin` )
1. A fiók jelszavának vagy nyilvános SSH-kulcsának értéke

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A Data Science Virtual Machine megtekintéséhez:

1. Nyissa meg a következőt: https://portal.azure.com 
1. Bejelentkezés 
1. Válassza ki az imént létrehozott erőforráscsoportot

Ekkor megjelenik az erőforráscsoport adatai: 

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Képernyőkép egy DSVM tartalmazó alapszintű erőforráscsoporthoz":::

Kattintson a virtuális gép erőforrására, hogy megnyissa az információ lapját. Itt információkat találhat a virtuális gépen, beleértve a kapcsolat részleteit is. 

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha nem szeretné használni ezt a virtuális gépet, törölje azt. Mivel a DSVM más erőforrásokhoz, például egy Storage-fiókhoz van társítva, valószínűleg törölni szeretné a teljes létrehozott erőforráscsoportot. Az erőforráscsoportot a portálon törölheti, ha a "Törlés" gombra kattint, és megerősíti a szolgáltatást. Vagy törölheti is az erőforráscsoportot a parancssori felületről a következővel: 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Data Science Virtual Machine egy Azure Resource Manager sablonból. 

> [!div class="nextstepaction"]
> [Minta programok & ML-útmutató](dsvm-samples-and-walkthroughs.md)
