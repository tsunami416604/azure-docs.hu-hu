---
title: Azure Blockchain-szolgáltatásbeli tag létrehozása – Azure CLI
description: Hozzon létre egy Azure Blockchain-szolgáltatási tagot egy Blockchain Consortium számára az Azure CLI használatával.
ms.date: 07/16/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions
ms.openlocfilehash: f459c9f577ac806c9121a46a200dd9f04ea5481a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075237"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Rövid útmutató: Azure Blockchain Service Blockchain-tag létrehozása az Azure CLI-vel

Ebben a rövid útmutatóban egy új blockchain-tagot és-konzorciumot helyez üzembe az Azure Blockchain Service-ben az Azure CLI használatával.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Nincsenek.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/bash](https://shell.azure.com/bash) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.51 vagy újabb verziójára van szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive
az group create \
                 --name myResourceGroup \
                 --location westus2
```

## <a name="create-a-blockchain-member"></a>Blockchain-tag létrehozása

Az Azure Blockchain szolgáltatás tagja egy Blockchain-csomópont egy privát konzorcium Blockchain-hálózatában. Egy tag kiépítés esetén létrehozhat vagy csatlakozhat konzorciumi hálózathoz. Legalább egy tagnak szüksége van egy konzorciumi hálózatra. A résztvevők által igényelt blockchain-tagok száma a forgatókönyvtől függ. A konzorcium résztvevői rendelkezhetnek egy vagy több blockchain-taggal, vagy megoszthatnak más résztvevőkkel rendelkező tagokat is. A konzorciumokkal kapcsolatos további információkért lásd: [Azure Blockchain Service Consortium](consortium.md).

Több paramétert és tulajdonságot kell átadnia. Cserélje le a példában szereplő paramétereket az értékekre.

```azurecli-interactive
az resource create \
                    --resource-group myResourceGroup \
                    --name myblockchainmember \
                    --resource-type Microsoft.Blockchain/blockchainMembers \
                    --is-full-object \
                    --properties '{"location":"westus2", "properties":{"password":"strongMemberAccountPassword@1", "protocol":"Quorum", "consortium":"myConsortiumName", "consortiumManagementAccountPassword":"strongConsortiumManagementPassword@1"}, "sku":{"name":"S0"}}'
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai jönnek létre. Használja az előző szakaszban létrehozott erőforráscsoportot.
| **név** | Egy egyedi név, amely az Azure Blockchain szolgáltatás Blockchain-tagját azonosítja. A nevet a rendszer a nyilvános végpont címeként használja. Például: `myblockchainmember.blockchain.azure.com`.
| **helyen** | Az az Azure-régió, ahol a blockchain-tag létrejött. Például: `westus2`. Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet. Előfordulhat, hogy egyes régiókban nem érhetők el szolgáltatások. Az Azure Blockchain Data Manager a következő Azure-régiókban érhető el: USA keleti régiója és Nyugat-Európa.
| **alaphelyzetbe állítása** | A tag alapértelmezett tranzakciós csomópontjának jelszava. Az alapszintű hitelesítéshez használja a jelszót az blockchain-tag alapértelmezett tranzakciós csomópontjának nyilvános végponthoz való csatlakozáskor.
| **Consortium** | A csatlakozáshoz vagy létrehozáshoz használandó konzorcium neve. A konzorciumokkal kapcsolatos további információkért lásd: [Azure Blockchain Service Consortium](consortium.md).
| **consortiumAccountPassword** | A Consortium-fiók jelszavát más néven a tag fiók jelszavaként kell megadni. A tag fiók jelszava a tag számára létrehozott Ethereum-fiók titkos kulcsának titkosítására szolgál. A tag fiókja és a fiók jelszava a konzorciumok kezeléséhez.
| **skuName** | A rétegek típusa. A standard és a B0 S0 használata alapszintű. A fogalmak fejlesztéséhez, teszteléséhez és bizonyításához *használja az alapszintű* csomagot. Használja a *standard* szintű üzemi szintű üzembe helyezést. A *standard* szintet is érdemes használni, ha Blockchain Data Manager használ, vagy nagy mennyiségű privát tranzakciót küld. Az alapszintű és a standard szintű díjszabás a tag létrehozása után történő módosítása nem támogatott.

A blockchain-tag és a támogató erőforrások létrehozása körülbelül 10 percet vesz igénybe.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Használhatja a következő rövid útmutatóhoz vagy oktatóanyaghoz létrehozott blockchain tagot. Ha már nincs rá szükség, törölheti az erőforrásokat a gyors útmutatóhoz `myResourceGroup` létrehozott erőforráscsoport törlésével.

A következő parancs futtatásával távolítsa el az erőforráscsoportot és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy Azure Blockchain-szolgáltatási tagot és egy új konzorciumot. Próbálja ki a következő rövid útmutatót a Ethereum készült Azure Blockchain Development Kit használatával az Azure Blockchain-szolgáltatáshoz való csatlakoztatáshoz.

> [!div class="nextstepaction"]
> [A Visual Studio Code használata az Azure Blockchain szolgáltatáshoz való kapcsolódáshoz](connect-vscode.md)
