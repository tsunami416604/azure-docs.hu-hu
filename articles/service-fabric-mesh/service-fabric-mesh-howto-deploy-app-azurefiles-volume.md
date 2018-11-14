---
title: Állapot Store az Azure Service Fabric-háló alkalmazások csatlakoztatja az Azure Files-alapú kötet tárolón belül |} A Microsoft Docs
description: Megtudhatja, hogyan állapot tárolásához az Azure Service Fabric-háló alkalmazások az Azure Files-alapú köteten a tárolóban, az Azure CLI használatával csatlakoztatja.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: cb5b421c1bcfe888d65335f3ab7f67bed80eec34
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614259"
---
# <a name="store-state-in-an-azure-service-fabric-mesh-application-by-mounting-an-azure-files-based-volume-inside-the-container"></a>Az Azure Service Fabric-háló alkalmazások csatlakoztatja az Azure Files Store állapota alapján kötet tárolón belül

Ez a cikk bemutatja, hogyan állapot tárolásához az Azure Files egy Service Fabric-háló alkalmazás a tároló kötetek csatlakoztatja. Ebben a példában a számláló alkalmazás rendelkezik egy ASP.NET Core-szolgáltatás, amely egy weblap, amelyen a teljesítményszámláló értéke látható a böngészőben. 

A `counterService` rendszeres időközönként olvassa be a számláló értéke egy fájl növekszik, és vissza a fájl írása. A fájl, amely az Azure-fájlmegosztási alapját a kötet csatlakoztatva van egy mappában tárolódik.

## <a name="prerequisites"></a>Előfeltételek

Ez a feladat végrehajtásához használhatja az Azure Cloud Shell vagy az Azure parancssori felület helyi telepítése. Ez a cikk az Azure CLI-vel használni, győződjön meg arról, hogy `az --version` adja vissza a legalább `azure-cli (2.0.43)`.  Az Azure Service Fabric háló parancssori bővítmény modul a következő telepítéséhez (vagy frissítéséhez) [utasításokat](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba, és állítsa be az előfizetését.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

Az Azure-fájlmegosztás létrehozása a következő [utasításokat](/azure/storage/files/storage-how-to-create-file-share). A tárfiók nevét, a tárfiók-kulcsot és a fájlmegosztás nevét néven hivatkozott `<storageAccountName>`, `<storageAccountKey>`, és `<fileShareName>` az az alábbi utasításokat. Ezeket az értékeket az Azure Portalon érhetők el:
* <storageAccountName> -A **Tárfiókok**, a fájlmegosztás létrehozásakor használt tárfiók neve.
* <storageAccountKey> – Válassza ki a storage-fiókja alatt **Tárfiókok** majd **hozzáférési kulcsok** az értéket, és **key1**.
* <fileShareName> -Válassza ki a storage-fiókja alatt **Tárfiókok** majd **fájlok**. Használandó név az imént létrehozott fájlmegosztás neve.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot, amelyben az alkalmazást üzembe helyezheti. A következő parancs létrehoz egy erőforráscsoportot, nevű `myResourceGroup` kelet-USA-beli helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az alkalmazás és a kapcsolódó erőforrások az alábbi paranccsal hozzon létre, és adja meg a tartozó értékeket `storageAccountName`, `storageAccountKey` és `fileShareName` , a korábbi [fájlmegosztás létrehozása](#create-a-file-share) . lépés.

A `storageAccountKey` egy biztonságos karakterláncot paraméter a sablonban. Nem jelennek az üzembe helyezési állapot és `az mesh service show` parancsokat. Győződjön meg arról, hogy ezt helyesen van megadva a következő parancsban.

A következő parancsot helyez üzembe egy Linux alkalmazás használja a [counter.azurefilesvolume.linux.json sablon](https://sfmeshsamples.blob.core.windows.net/templates/counter/counter.azurefilesvolume.linux.json). Windows-alkalmazás üzembe helyezése, használja a [counter.azurefilesvolume.windows.json sablon](https://sfmeshsamples.blob.core.windows.net/templates/counter/counter.azurefilesvolume.windows.json). Vegye figyelembe, hogy nagyobb tárolórendszerképek hosszabb időt vehet igénybe üzembe helyezéséhez.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/counter.azurefilesvolume.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

Néhány perc alatt a parancs a kell visszaadnia `counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>`

## <a name="open-the-application"></a>Az alkalmazás megnyitása

A telepítési parancs visszaadja a szolgáltatásvégpont nyilvános IP-címét. Miután sikeresen üzembe helyezte az alkalmazást, a nyilvános IP-cím beszerzése a szolgáltatási végpont, és a egy böngészőben nyissa meg. A számláló értéke másodpercenként frissítése a weblap jelenik meg.

Ez az alkalmazás a hálózati erőforrás neve nem `counterAppNetwork`. Az alkalmazás például annak leírása, tartózkodási hely, erőforráscsoport, stb. További információ a következő paranccsal tekintheti meg:

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Győződjön meg arról, hogy az alkalmazás használhatja a kötet

Az alkalmazás létrehoz egy fájlt `counter.txt` a fájl megosztása belül `counter/counterService` mappát. Ez a fájl tartalma a számláló értéke nem szeretné megjeleníteni a weblapot.

A fájl minden olyan eszköz, amely lehetővé teszi az Azure Files-fájlmegosztást, például böngészési segítségével lehet letölteni a [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="delete-the-resources"></a>Az erőforrások törlése

Gyakran törölje az erőforrásokat, amelyek nem használják az Azure-ban. Ebben a példában a kapcsolódó erőforrások törléséhez törölje az erőforráscsoportot, amelyben üzembe lett helyezve, (Ez töröl Mindent az erőforráscsoporthoz társított) a következő paranccsal:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>További lépések

- Az Azure Files kötet mintaalkalmazás megtekintése [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- A Service Fabric-erőforrásmodellel kapcsolatos további tudnivalókért lásd a [Service Fabric Mesh-erőforrásmodellt](service-fabric-mesh-service-fabric-resources.md) bemutató cikket.
- A Service Fabric Meshsel kapcsolatos további információkért olvassa el a [Service Fabric Mesh áttekintésével](service-fabric-mesh-overview.md) foglalkozó cikket.