---
title: Service Fabric Mesh Service Fabric megbízható lemez kötete
description: Megtudhatja, hogyan tárolhatja az állapotot egy Azure Service Fabric Mesh alkalmazásban úgy, hogy Service Fabric megbízható lemez-alapú kötetet csatlakoztat a tárolóhoz az Azure CLI használatával.
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: f26fe70afe7d9e2872f06ac6da7143556278b1b0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75497964"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Kiválóan elérhető Service Fabric megbízható lemez alapú kötet csatlakoztatása egy Service Fabric Mesh-alkalmazásban 
A tároló alkalmazásokkal való megőrzés közös módszere a távoli tárolás, például az Azure File Storage vagy az adatbázis, például a Azure Cosmos DB használata. Ez jelentős olvasási és írási hálózati késést okoz a távoli tárolónak.

Ez a cikk bemutatja, hogyan tárolhatja az állapotot a jól elérhető Service Fabric megbízható lemezeken azáltal, hogy egy kötetet csatlakoztat egy Service Fabric Mesh-alkalmazás tárolóján belül.
Service Fabric megbízható lemez a magas rendelkezésre állás érdekében a Service Fabric fürtön belül replikált írásokkal rendelkező helyi olvasások köteteit biztosítja. Ezzel eltávolítja a hálózati hívások olvasását, és csökkenti az írási műveletek hálózati késését. Ha a tároló újraindítja vagy áthelyezi egy másik csomópontra, az új Container-példány ugyanazt a kötetet fogja látni, mint egy régebbi. Így mind a hatékony, mind a nagyon elérhető.

Ebben a példában a számláló alkalmazásnak van egy ASP.NET Core szolgáltatása egy weboldallal, amely egy böngészőben megjeleníti a számláló értékét.

Az `counterService` időnként beolvas egy számláló értékét egy fájlból, megnöveli azt, és visszaírja a fájlba. A fájl egy olyan mappában van tárolva, amely Service Fabric megbízható lemez által támogatott kötetre van csatlakoztatva.

## <a name="prerequisites"></a>Előfeltételek

A feladat végrehajtásához használhatja az Azure CLI Azure Cloud Shell vagy helyi telepítését. Ha az Azure CLI-t ehhez a cikkhez szeretné használni, győződjön meg arról, hogy `az --version` legalább a értéket adja vissza `azure-cli (2.0.43)` .  Az alábbi [utasításokat](service-fabric-mesh-howto-setup-cli.md)követve telepítse (vagy frissítse) az Azure Service FABRIC Mesh CLI bővítmény modulját.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba, és állítsa be az előfizetését.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot, amelyben az alkalmazást üzembe helyezheti. A következő parancs egy nevű erőforráscsoportot hoz létre `myResourceGroup` a keleti Egyesült Államok egy helyen. Ha megváltoztatja az erőforráscsoport nevét az alábbi parancsban, ne felejtse el módosítani az összes következő parancsban.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A következő parancs egy linuxos alkalmazást helyez üzembe a [sabloncounter.sfreliablevolume.linux.js](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json)használatával. Windows-alkalmazás központi telepítéséhez használja a [counter.sfreliablevolume.windows.jsa sablonban](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Vegye figyelembe, hogy a nagyobb méretű tárolók lemezképei hosszabb ideig tarthatnak.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

A központi telepítés állapotát a paranccsal is megtekintheti

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Figyelje meg annak az átjáró-erőforrásnak a nevét, amelynek az erőforrástípus a típusa `Microsoft.ServiceFabricMesh/gateways` . Ezt fogja használni a rendszer az alkalmazás nyilvános IP-címének lekérése során.

## <a name="open-the-application"></a>Az alkalmazás megnyitása

Az alkalmazás sikeres üzembe helyezése után szerezze be az alkalmazás átjáró-erőforrásának IP-címeit. Használja a fenti szakaszban észlelt átjáró nevét.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

A kimenetnek rendelkeznie kell egy olyan tulajdonsággal, `ipAddress` amely a szolgáltatás végpontjának nyilvános IP-címe. Nyissa meg egy böngészőben. Ekkor megjelenik egy weblap, amelyen a számláló értéke másodpercenként frissül.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Annak ellenőrzése, hogy az alkalmazás képes-e a kötet használatára

Az alkalmazás létrehoz egy nevű fájlt `counter.txt` a kötet belső `counter/counterService` mappájában. A fájl tartalma a weblapon megjelenő számláló értéke.

## <a name="delete-the-resources"></a>Erőforrások törlése

Gyakran törölje azokat az erőforrásokat, amelyeket már nem használ az Azure-ban. Az ehhez a példához kapcsolódó erőforrások törléséhez törölje azt az erőforráscsoportot, amelyben a központi telepítés történt (amely az erőforráscsoporthoz társított összes műveletet törli) a következő paranccsal:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>További lépések

- Tekintse meg a Service Fabric megbízható kötet lemez minta alkalmazást a [githubon](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- A Service Fabric-erőforrásmodellel kapcsolatos további tudnivalókért lásd a [Service Fabric Mesh-erőforrásmodellt](service-fabric-mesh-service-fabric-resources.md) bemutató cikket.
- A Service Fabric Meshsel kapcsolatos további információkért olvassa el a [Service Fabric Mesh áttekintésével](service-fabric-mesh-overview.md) foglalkozó cikket.
