---
title: Hozzon létre egy Azure Blockchain-szolgáltatás Azure CLI használatával
description: Azure CLI-vel blockchain tag létrehozásához használja az Azure Blockchain-szolgáltatás.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 4589a9de4c2a8fa43e4e653d4447c7a7715a6e42
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399945"
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

## <a name="create-a-blockchain-member"></a>Blokklánctagok létrehozása

A blockchain tag létrehozása az Azure Blockchain Service-ben, amely a kvórum Főkönyv protokoll egy új consortium. Több paraméterek és teljesítenie kell a tulajdonságok is van. Az értékeket cserélje le a példában paramétereket.

```azurecli-interactive
az resource create --resource-group myResourceGroup --name myblockchainmember --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"eastus\", \"properties\": {\"password\": \"strongMemberAccountPassword@1\", \"protocol\": \"Quorum\", \"consortium\": \"myConsortiumName\", \"consortiumManagementAccountPassword\": \"strongConsortiumManagementPassword@1\" }, \"sku\": { \"name\": \"S0\" } }"
```

| Paraméter | Leírás |
|---------|-------------|
| **resource-group** | Erőforráscsoport neve, az Azure Blockchain-szolgáltatási erőforrások jönnek létre. Használja az előző szakaszban létrehozott erőforráscsoportot.
| **name** | Egyedi név, amely azonosítja az Azure Blockchain-szolgáltatás blockchain tag. A név szolgál a nyilvános végpont címe. Például: `myblockchainmember.blockchain.azure.com`.
| **hely** | Az Azure régióban, amelyben létrehozza a blockchain tag. Például: `eastus`. Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet.
| **jelszó** | A tag alapértelmezett tranzakció csomópont jelszava. Használja az egyszerű hitelesítést a jelszót, blockchain-tagok alapértelmezett tranzakció csomópont nyilvános végponthoz való csatlakozáskor.
| **consortium** | A csatlakozás, vagy hozzon létre consortium neve.
| **consortiumAccountPassword** | A consortium fiók jelszavát a tag fiók jelszavát is nevezik. A tag fiók jelszavát a Ethereum-fiókot, amely a tag jön létre a titkos kulcsfájl titkosítására szolgál. A tagsági fiók, és a tag fiók jelszavának használata consortium felügyeleti.
| **skuName** | Csomag típusa. S0 Standard és B0 használata alapszintű.

Nagyjából 10 percet a blockchain tag és a támogató erőforrások létrehozása vesz igénybe.

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
