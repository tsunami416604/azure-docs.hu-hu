---
title: Linuxos virtuális gép létrehozása az Azure-ban sablonból
description: Az Azure CLI használata Linux os virtuális gép létrehozásához erőforrás-kezelősablonból
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: 581eadc60835b758f67ae616d4413800f1d6d718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969527"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Linuxos virtuális gép létrehozása Azure Resource Manager-sablonokkal

Ismerje meg, hogyan hozhat létre egy Linux virtuális gépet (VM) egy Azure Resource Manager-sablon és az Azure Cloud shell azure CLI használatával. Windows virtuális gép létrehozásához olvassa el A [Windows virtuális gép létrehozása Erőforrás-kezelő sablonból című témakört.](../windows/ps-template.md)

## <a name="templates-overview"></a>Sablonok – Áttekintés

Az Azure Resource Manager-sablonok Olyan JSON-fájlok, amelyek meghatározzák az Azure-megoldás infrastruktúráját és konfigurációját. A sablonok segítségével a megoldás a teljes életciklusa során ismételten üzembe helyezhető, és az erőforrások üzembe helyezése biztosan konzisztens lesz. Ha többet szeretne megtudni a sablon formátumáról és felépítéséről, olvassa el a [Rövid útmutató: Azure Resource Manager-sablonok létrehozása és üzembe helyezése az Azure Portalhasználatával című témakört.](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) Az erőforrástípusok JSON-szintaxisának megtekintéséért lásd [az Azure Resource Manager-sablonokban az erőforrások meghatározásával kapcsolatos](/azure/templates/microsoft.compute/allversions) témakört.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Az Azure virtuális gépek létrehozása általában két lépést tartalmaz:

1. Hozzon létre egy erőforráscsoportot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportot még a virtuális gép létrejötte előtt létre kell hozni.
1. Virtuális gépet hoz létre.

A következő példa létrehoz egy virtuális gép egy [Azure-gyorsindítási sablonból.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) Ehhez a központi telepítéshez csak SSH-hitelesítés engedélyezett. Amikor a rendszer kéri, adja meg saját SSH nyilvános kulcsának értékét, például a *~/.ssh/id_rsa.pub*tartalmát. Ha SSH-kulcspárt kell létrehoznia, olvassa [el az SSH-kulcspár létrehozása és használata Linuxos virtuális gépekhez az Azure-ban című témakört.](mac-create-ssh-keys.md) Itt van egy példányát a sablon:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

A CLI-parancsfájl futtatásához válassza a **Próbálja ki** az Azure Cloud rendszerhéj megnyitásához. A parancsfájl beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, majd válassza a **Beillesztés parancsot:**

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

Az utolsó Azure CLI parancs az újonnan létrehozott virtuális gép nyilvános IP-címét jeleníti meg. A virtuális géphez való csatlakozáshoz nyilvános IP-címre van szükség. Lásd a cikk következő részét.

Az előző példában megadott egy sablont a GitHubon tárolt. Le is tölthet i vagy létrehozhat egy sablont, és megadhatja a helyi elérési utat a `--template-file` paraméterrel.

Néhány további információforrás:

- Az Erőforrás-kezelő-sablonok fejlesztéséről az [Azure Resource Manager dokumentációjában](/azure/azure-resource-manager/)olvashat.
- Az Azure virtuálisgép-sémák megtekintéséhez tekintse meg az [Azure-sablon hivatkozási](/azure/templates/microsoft.compute/allversions).
- További virtuálisgép-sablonminták megtekintéséhez tekintse meg [az Azure gyorsindítási sablonjait.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Ezután ssh-t a virtuális gép a szokásos módon. Adja meg az előző parancs saját nyilvános IP-címét:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>További lépések

Ebben a példában létrehozott egy egyszerű Linux virtuális gép. További Erőforrás-kezelő sablonok, amelyek alkalmazáskeretrendszereket tartalmaznak, vagy összetettebb környezeteket hoznak létre, keresse meg az [Azure gyorsindítási sablonjait.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)

A sablonok létrehozásáról a Telepített erőforrástípusok JSON-szintaxisát és tulajdonságait tekintheti meg:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
