---
title: Állapot Store csatlakoztatja az Azure Files-alapú mennyiségi alkalmazás Service Fabric-háló tárolón belül |} A Microsoft Docs
description: Ismerje meg, hogyan-állapot tárolása az Azure Files-alapú mennyiségi a tárolóban, a Service Fabric-háló alkalmazás az Azure CLI használatával csatlakoztatja.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 94a4e17e6285893520a2f6482b32a69b1229e2fa
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090632"
---
# <a name="store-state-by-mounting-azure-files-based-volume-in-service-fabric-mesh-application"></a>Store állapot csatlakoztatja az Azure Files-alapú Service Fabric-háló alkalmazásban kötet

Ez a cikk bemutatja, hogyan állapot tárolásához az Azure Files egy Service Fabric-háló alkalmazás a tároló kötetek csatlakoztatja. Ebben a példában a számláló alkalmazásnak egy ASP.NET Core-szolgáltatás, amely egy weblap, amelyen a teljesítményszámláló értéke látható a böngészőben. 

A `counterService` perodically növeli fájlból, egy számláló értékét olvassa be, és az írási vissza a fájlt. A fájl, amely az Azure-fájlmegosztási alapját a kötet csatlakoztatva van egy mappában tárolódik. 

## <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric háló parancssori felület beállítása 
Ez a feladat végrehajtásához használhatja az Azure Cloud Shell vagy az Azure parancssori felület helyi telepítése. Az Azure Service Fabric háló parancssori bővítmény modul telepítése a következő [utasításokat](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure-ba, és állítsa be az előfizetés.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-file-share"></a>Fájlmegosztás létrehozása 
Az Azure-fájlmegosztás létrehozása a következő [utasításokat](/azure/storage/files/storage-how-to-create-file-share). A tárfiók nevét, a tárfiók-kulcsot és a fájlmegosztás nevét néven hivatkozott `<storageAccountName>`, `<storageAccountKey>`, és `<fileShareName>` az az alábbi utasításokat.

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot, amelybe az alkalmazás üzembe helyezése. Használjon egy meglévő erőforráscsoportot, és ezt a lépést kihagyhatja. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az alkalmazás és a kapcsolódó erőforrások az alábbi paranccsal hozzon létre, és adja meg a tartozó értékeket `storageAccountName`, `storageAccountKey` és `fileShareName` az előző lépésben.

A `storageAccountKey` paraméter a sablon egy `securestring`. Nem jelennek az üzembe helyezési állapot és `az mesh service show` parancsokat. Győződjön meg arról, hogy ezt helyesen van megadva a következő parancsban.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

Az előző parancs helyez üzembe egy Linux használó [mesh_rp.linux.json sablon](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json). Ha szeretne Windows-alkalmazás üzembe helyezése, [mesh_rp.windows.json sablon](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json). Windows-tárolórendszerképeket nagyobb, mint a Linuxos tárolólemezképek és központi telepítése több időt is igénybe vehet.

Néhány perc alatt a parancs a kell visszaadnia:

`counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Nyissa meg az alkalmazás
Miután sikeresen üzembe helyezte az alkalmazást, a nyilvános IP-cím beszerzése a szolgáltatási végpont, és a egy böngészőben nyissa meg. A számláló értéke másodpercenként frissítése a weblap jeleníti meg.

A telepítési parancs visszaadja a szolgáltatásvégpont nyilvános IP-címét. Igény szerint is lekérdezheti a hálózati erőforrás keresése a szolgáltatásvégpont nyilvános IP-címét. 
 
Ez az alkalmazás a hálózati erőforrás neve nem `counterAppNetwork`, beolvassa a következő paranccsal kapcsolatos információkat. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Győződjön meg arról, hogy az alkalmazás használhatja a kötet
Az alkalmazás létrehoz egy fájlt `counter.txt` a fájl megosztása belül `counter/counterService` mappát. Ez a fájl tartalma a számláló értéke nem szeretné megjeleníteni a weblapot.

A fájl letöltődik minden olyan eszköz, amely lehetővé teszi egy fájlmegosztás az Azure Files-böngészés használatával. A [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) ilyen eszköz például.

## <a name="delete-the-resources"></a>Az erőforrások törlése

Erőforrásokkal való takarékoskodáshoz a korlátozott előzetes programjában hozzárendelni, gyakran törölje az erőforrásokat. Ebben a példában kapcsolódó erőforrások törléséhez törölje az erőforráscsoportot, amelyben üzembe lett helyezve.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>További lépések

- Az Azure Files kötet mintaalkalmazás megtekintése [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Service Fabric Erőforrásmodell kapcsolatos további információkért lásd: [Service Fabric háló Erőforrásmodell](service-fabric-mesh-service-fabric-resources.md).
- Service Fabric-háló kapcsolatos további információkért olvassa el a [Service Fabric-háló áttekintése](service-fabric-mesh-overview.md).
