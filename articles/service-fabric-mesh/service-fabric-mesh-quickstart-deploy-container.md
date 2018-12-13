---
title: Rövid útmutató – A Hello World üzembe helyezése a Service Fabric Meshben | Microsoft Docs
description: Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe Service Fabric Mesh-alkalmazást az Azure Service Fabric Meshben.
services: service-fabric-mesh
keywords: Ne adjon hozzá kulcsszavakat és ne szerkessze azokat a keresőoptimalizálást végző szakemberrel való egyeztetés nélkül.
author: rwike77
ms.author: ryanwi
ms.date: 11/27/2018
ms.topic: quickstart
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 4be24b00c3ac4ffadf7eafdc7397f59113ec03b2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088363"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>Rövid útmutató: A Hello World üzembe helyezése a Service Fabric Meshben

A [Service Fabric Mesh](service-fabric-mesh-overview.md) segítségével egyszerűen hozhat létre és felügyelhet mikroszolgáltatás-alkalmazásokat az Azure-ban anélkül, hogy virtuális gépeket kellene kiépítenie. Ebben a rövid útmutatóban létrehozhat egy Hello World-alkalmazást az Azure-ban, és közzéteheti azt az interneten. Ez a művelet egyetlen paranccsal hajtható végre. Néhány perc elteltével a következő nézetet láthatja a böngészőben:

![A Hello World alkalmazás a böngészőben][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Ha még nem rendelkezik Azure-fiókkal, első lépésként [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="set-up-service-fabric-mesh-cli"></a>A Service Fabric Mesh parancssori felületének beállítása 
A rövid útmutató teljesítéséhez használhatja az Azure Cloud Shellt vagy az Azure CLI helyileg telepített példányát. Az Azure Service Fabric Mesh CLI-bővítmény moduljának telepítéséhez kövesse ezeket az [útmutatásokat](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure-ba, és állítsa be az előfizetését.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot, amelyben az alkalmazást üzembe helyezheti. Egy meglévő erőforráscsoportot is használhat, és úgy kihagyhatja ezt a lépést. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Az alkalmazás központi telepítése
Hozza létre az alkalmazást az erőforráscsoportban az `az mesh deployment create` paranccsal.  Futtassa a következőt:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json --parameters "{'location': {'value': 'eastus'}}" 
```

Az előző parancs helyez üzembe egy Linux használó [linux.json sablon](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json). Ha szeretne Windows-alkalmazás üzembe helyezése, [windows.json sablon](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.windows.json). A Windows-tárolórendszerképek nagyobbak, mint a Linux-tárolórendszerképek, ezért több ideig tarthat az üzembe helyezésük.

Ez a parancs, amely az alábbiakban látható JSON-kódrészlet állítja elő. Alatt a ```outputs``` a JSON-kimenetet másolási szakaszában a ```publicIPAddress``` tulajdonság.

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

Ez az információ származik a ```outputs``` az ARM-sablon részében. Ahogy az alábbi, ebben a szakaszban hivatkozik az átjáró erőforrás nyilvános IP-címének beolvasása. 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('helloWorldGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>Az alkalmazás megnyitása
Az alkalmazás sikeres üzembe helyezése után másolja a szolgáltatásvégpont nyilvános IP-címét a parancssori felület kimenetéből. Nyissa meg az IP-címet egy webböngészőben. Megjelenik egy weboldal az Azure Service Fabric Mesh logójával.

## <a name="check-the-application-details"></a>Az alkalmazás részleteinek ellenőrzése
Az alkalmazás állapotát az `az mesh app show` paranccsal ellenőrizheti. Ez a parancs hasznos információkat biztosít, amelyeket nyomon követhet.

A rövid útmutatóban az alkalmazás neve `helloWorldApp`. Az alkalmazás részleteinek begyűjtéséhez futtassa a következő parancsot:

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>Az alkalmazásnaplók megtekintése

Vizsgálja meg az üzembe helyezett alkalmazás naplóit az `az mesh code-package-log get` paranccsal:

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor készen áll az alkalmazás törlésére, futtassa az [az group delete][az-group-delete] parancsot az erőforráscsoport és az abban lévő alkalmazás és hálózati erőforrások eltávolításához.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

A Service Fabric Mesh-alkalmazások létrehozásáról és üzembe helyezésével kapcsolatos további információért lépjen tovább az oktatóanyagra.
> [!div class="nextstepaction"]
> [Többszolgáltatású webalkalmazás létrehozása és üzembe helyezése](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
