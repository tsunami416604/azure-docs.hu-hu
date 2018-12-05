---
title: Magas rendelkezésre állású Service Fabric Reliable lemezkötetet használata egy Azure Service Fabric-háló alkalmazásban |} A Microsoft Docs
description: Megtudhatja, hogyan állapot tárolásához az Azure Service Fabric-háló alkalmazások Service Fabric Reliable lemez alapú kötet a tárolóban, az Azure CLI használatával csatlakoztatja.
services: service-fabric-mesh
documentationcenter: .net
author: ashishnegi
manager: raunakpandya
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: 086fa31f1758fe4eb5e8e31810824794b869095e
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892301"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>A Service Fabric-háló alkalmazás magas rendelkezésre állású Service Fabric Reliable lemez alapú Kötet csatlakoztatásához 
A tárolóalapú alkalmazások megőrzése állapot gyakori módja, hogy távoli tárolók, például az Azure File Storage vagy az adatbázis, mint például az Azure Cosmos DB. Ezt a szintre váltása költséggel jár, jelentős olvasási és írási késést, a távoli tároló.

Ez a cikk bemutatja, hogyan-állapotának tárolására a magas rendelkezésre állású Service Fabric Reliable lemez egy Service Fabric-háló alkalmazás a tároló kötetek csatlakoztatja.
Service Fabric Reliable lemez helyi okozni kötetek biztosít írási belül a Service Fabric-fürt magas rendelkezésre állás érdekében replikálva. Ez a hálózati hívások eltávolítja az olvasásokhoz, és csökkenti a hálózati késést való írásra. Ha a tároló újraindítja vagy áthelyezi egy másik csomópontra, új tárolópéldány régebbi ütemezésként jelenik meg ugyanazt a kötetet. Tehát olyan hatékony és a magas rendelkezésre állású.

Ebben a példában a számláló alkalmazás rendelkezik egy ASP.NET Core-szolgáltatás, amely egy weblap, amelyen a teljesítményszámláló értéke látható a böngészőben.

A `counterService` rendszeres időközönként olvassa be a számláló értéke egy fájl növekszik, és vissza a fájl írása. A fájl, amely a Service Fabric Reliable lemez által támogatott kötet csatlakoztatva van egy mappában tárolódik.

## <a name="prerequisites"></a>Előfeltételek

Ez a feladat végrehajtásához használhatja az Azure Cloud Shell vagy az Azure parancssori felület helyi telepítése. Ez a cikk az Azure CLI-vel használni, győződjön meg arról, hogy `az --version` adja vissza a legalább `azure-cli (2.0.43)`.  Az Azure Service Fabric háló parancssori bővítmény modul a következő telepítéséhez (vagy frissítéséhez) [utasításokat](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba, és állítsa be az előfizetését.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot, amelyben az alkalmazást üzembe helyezheti. A következő parancs létrehoz egy erőforráscsoportot, nevű `myResourceGroup` kelet-USA-beli helyen. Ha az erőforráscsoport nevét az alábbi parancs módosítja, ne felejtse el az összes parancs az alábbi.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A következő parancsot helyez üzembe egy Linux alkalmazás használja a [counter.sfreliablevolume.linux.json sablon](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). Windows-alkalmazás üzembe helyezése, használja a [counter.sfreliablevolume.windows.json sablon](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Vegye figyelembe, hogy nagyobb tárolórendszerképek hosszabb időt vehet igénybe üzembe helyezéséhez.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json
```

Emellett megtekintheti az üzembe helyezés, a parancs állapota

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Figyelje meg az átjáró-erőforrás, amely erőforrás típusa nevét `Microsoft.ServiceFabricMesh/gateways`. Ez az alkalmazás nyilvános IP-cím első felhasználja.

## <a name="open-the-application"></a>Az alkalmazás megnyitása

Miután sikeresen üzembe helyezte az alkalmazást, az alkalmazás az IP-cím az átjáró erőforrás beolvasása. Az átjáró nevét, hogy észrevette használja a fenti szakaszt.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --gateway-name counterGateway
```

A kimenet tulajdonsággal kell rendelkeznie `ipAddress` Ez az a szolgáltatásvégpont nyilvános IP-címét. Egy böngészőben nyissa meg. A számláló értéke másodpercenként frissítése a weblap jelenik meg.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Győződjön meg arról, hogy az alkalmazás használhatja a kötet

Az alkalmazás létrehoz egy fájlt `counter.txt` belül a kötet `counter/counterService` mappát. Ez a fájl tartalma a számláló értéke nem szeretné megjeleníteni a weblapot.

## <a name="delete-the-resources"></a>Az erőforrások törlése

Gyakran törölje az erőforrásokat, amelyek nem használják az Azure-ban. Ebben a példában a kapcsolódó erőforrások törléséhez törölje az erőforráscsoportot, amelyben üzembe lett helyezve, (Ez töröl Mindent az erőforráscsoporthoz társított) a következő paranccsal:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>További lépések

- A Service Fabric Reliable kötetet tartalmazó lemezt mintaalkalmazás megtekintése [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- A Service Fabric-erőforrásmodellel kapcsolatos további tudnivalókért lásd a [Service Fabric Mesh-erőforrásmodellt](service-fabric-mesh-service-fabric-resources.md) bemutató cikket.
- A Service Fabric Meshsel kapcsolatos további információkért olvassa el a [Service Fabric Mesh áttekintésével](service-fabric-mesh-overview.md) foglalkozó cikket.
