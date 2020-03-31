---
title: Hozzon létre egy Azure Blockchain szolgáltatás tagot - Azure CLI
description: Hozzon létre egy Azure Blockchain Service-tagot egy blockchain konzorcium számára az Azure CLI használatával.
ms.date: 03/12/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.openlocfilehash: 0a3cf3d7c7f3dc0b8ece6fd6a466e42ae970b61c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79214739"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Rövid útmutató: Hozzon létre egy Azure Blockchain Service blockchain tag ot az Azure CLI használatával

Ebben a rövid útmutatóban üzembe helyez egy új blokklánc-tagot és konzorciumot az Azure Blockchain Szolgáltatásban az Azure CLI használatával.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon [https://shell.azure.com/bash](https://shell.azure.com/bash)is elindíthatja a segítségével. A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Ha a CLI helyi telepítését és használatát szeretné használni, ez a rövid útmutató az Azure CLI 2.0.51-es vagy újabb verzióját igényli. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakört.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
az group create \
                 --name myResourceGroup \
                 --location westus2
```

## <a name="create-a-blockchain-member"></a>Blokklánc-tag létrehozása

Az Azure Blockchain Service-tag egy blockchain csomópont egy privát konzorcium blockchain hálózatban. Egy tag kiépítésekor létrehozhat vagy csatlakozhat egy konzorciumi hálózathoz. A konzorciumi hálózathoz legalább egy tagra van szükség. A résztvevők által szükséges blockchain tagok száma a forgatókönyvtől függ. A konzorcium résztvevőinek lehet egy vagy több blokklánc-tagja, vagy megoszthatják a tagokat más résztvevőkkel. A konzorciumokkal kapcsolatos további információkért lásd: [Azure Blockchain Service consortium.](consortium.md)

Számos paramétert és tulajdonságot kell átadnia. Cserélje le a példa paramétereket az értékekre.

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
| **erőforráscsoport** | Erőforráscsoport neve, ahol az Azure Blockchain Service-erőforrások jönnek létre. Használja az előző szakaszban létrehozott erőforráscsoportot.
| **név** | Egy egyedi név, amely azonosítja az Azure Blockchain Service blockchain tag. A nyilvános végpontcím neve a neve. Például: `myblockchainmember.blockchain.azure.com`.
| **Helyen** | Az Azure-régió, ahol a blokklánc-tag jön létre. Például: `westus2`. Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet.
| **alaphelyzetbe állítása** | A tag alapértelmezett tranzakciós csomódának jelszava. Használja a jelszót az alapfokú hitelesítéshez, amikor a blockchain tag alapértelmezett tranzakciós csomópontnyilvános végpontjához csatlakozik.
| **Konzorcium** | A csatlakozni vagy létrehozni hozandó konzorcium neve. A konzorciumokkal kapcsolatos további információkért lásd: [Azure Blockchain Service consortium.](consortium.md)
| **consortiumAccountPassword** | A konzorciumi fiók jelszavát tagfiók-jelszónak is nevezik. A tagfiók jelszava a tag számára létrehozott Ethereum-fiók titkos kulcsának titkosítására szolgál. A tagfiók és a tagfiók jelszavát használja a konzorciumkezeléséhez.
| **skuName** | Szint típusa. Használja az S0-t a Normál és a B0 alapszintűhez. Használja az *alapszintű* szintet a fejlesztéshez, teszteléshez és a fogalmak igazolásához. Használja a *standard* szint éles szintű központi telepítések.

Körülbelül 10 percet vesz igénybe a blokklánc-tag és a támogató erőforrások létrehozása.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használhatja a blockchain tag létrehozott a következő rövid útmutató vagy oktatóanyag. Ha már nincs rá szükség, törölheti az `myResourceGroup` erőforrásokat a rövid útmutatóhoz létrehozott erőforráscsoport törlésével.

Futtassa a következő parancsot az erőforráscsoport és az összes kapcsolódó erőforrás eltávolításához.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy Azure Blockchain szolgáltatás tagot és egy új konzorciumot. Próbálja ki a következő gyorsindítást az Azure Blockchain Development Kit for Ethereum használatával az Azure Blockchain szolgáltatás egy tagjához való csatoláshoz.

> [!div class="nextstepaction"]
> [Az Azure Blockchain szolgáltatáshoz való csatlakozás hoz a Visual Studio-kód dal](connect-vscode.md)
