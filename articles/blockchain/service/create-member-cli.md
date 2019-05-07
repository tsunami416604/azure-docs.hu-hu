---
title: Hozzon létre egy Azure Blockchain-szolgáltatás Azure CLI használatával
description: Azure CLI-vel blockchain tag létrehozásához használja az Azure Blockchain-szolgáltatás.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: e1b7558ea83c8948a8984215e15040e4d929cb1b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141383"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Gyors útmutató: Azure CLI-vel, az Azure Blockchain-szolgáltatás blockchain tag létrehozása

Az Azure Blockchain-szolgáltatás az blockchain platform segítségével hajtsa végre az üzleti logikára belüli egy intelligens szerződés. Ez a rövid útmutató bemutatja, hogyan első lépésként hozza létre az Azure CLI-vel blockchain tagja.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/bash](https://shell.azure.com/bash) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Ha inkább a helyi telepítése és használata a parancssori felület, ehhez a rövid útmutatóhoz az Azure CLI-vel 2.0.51 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-blockchain-member"></a>A blockchain tag létrehozása

A blockchain tag létrehozása az Azure Blockchain Service-ben, amely a kvórum Főkönyv protokoll egy új consortium.

Több paraméterek és teljesítenie kell a tulajdonságok is van. Cserélje le az értékeket az alábbi paramétereket.

| Paraméter | Leírás |
|---------|-------------|
| **resource-group** | Erőforráscsoport neve, az Azure Blockchain-szolgáltatási erőforrások jönnek létre. Használja az előző szakaszban létrehozott erőforráscsoportot.
| **name** | Egyedi név, amely azonosítja az Azure Blockchain-szolgáltatás blockchain tag. A név szolgál a nyilvános végpont címe. Például: `myblockchainmember.blockchain.azure.com`.
| **hely** | Az Azure régióban, amelyben létrehozza a blockchain tag. Például: `eastus`. Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet.
| **jelszó** | A tag fiók jelszavát. A tag fiók jelszavát az alapszintű hitelesítést használ, a blockchain tag nyilvános végpont hitelesítéséhez használatos.
| **consortium** | A csatlakozás, vagy hozzon létre consortium neve.
| **consortiumManagementAccountPassword** | Consortium felügyeleti jelszava. Csatlakozás konzorcium szolgál.
| **skuName** | Csomag típusa. S0 Standard és B0 használata alapszintű.

```azurecli-interactive
az resource create --resource-group myResourceGroup --name myblockchainmember --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"eastus\", \"properties\": {\"password\": \"strongMemberAccountPassword@1\", \"protocol\": \"Quorum\", \"consortium\": \"myConsortiumName\", \"consortiumManagementAccountPassword\": \"strongConsortiumManagementPassword@1\" }, \"sku\": { \"name\": \"S0\" } }"
```

Nagyjából 10 percet a blockchain tag és a támogató erőforrások létrehozása vesz igénybe.

Az alábbi látható példa kimenetében, a sikeres-létrehozási művelet.

```json
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/mymembername",
  "kind": null,
  "location": "eastus",
  "name": "mymembername",
  "properties": {
    "ConsortiumMemberDisplayName": "mymembername",
    "consortium": "myConsortiumName",
    "consortiumManagementAccountAddress": "0xfe5fbb9d1036298abf415282f52397ade5d5beef",
    "consortiumManagementAccountPassword": null,
    "consortiumRole": "ADMIN",
    "dns": "mymembername.blockchain.azure.com",
    "protocol": "Quorum",
    "provisioningState": "Succeeded",
    "userName": "mymembername",
    "validatorNodesSku": {
      "capacity": 2
    }
  },
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "S0",
    "tier": "Standard"
  },
  "type": "Microsoft.Blockchain/blockchainMembers"
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A következő rövid vagy az oktatóanyagban létrehozott blockchain tag használható. Ha már nincs rá szükség, törölheti az erőforrások törlése a `myResourceGroup` létrehozott erőforráscsoportot az Azure Blockchain-szolgáltatás.

Futtassa az alábbi paranccsal eltávolítható az erőforráscsoport és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Most, hogy létrehozta a blockchain-tag, próbálkozhat a tranzakció csomópont rövid útmutatók a kapcsolódás [Geth](connect-geth.md), [MetaMask](connect-metamask.md), vagy [Truffle](connect-truffle.md).

> [!div class="nextstepaction"]
> [Truffle használatával csatlakozni egy az Azure Blockchain-szolgáltatás hálózat](connect-truffle.md)
