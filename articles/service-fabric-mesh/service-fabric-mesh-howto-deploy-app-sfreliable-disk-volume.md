---
title: Szolgáltatásháló megbízható lemezkötete szolgáltatáshálóval
description: Ismerje meg, hogyan tárolhatja az állapotot egy Azure Service Fabric Mesh alkalmazásban a Service Fabric megbízható lemez alapú kötet csatlakoztatásával a tárolón belül az Azure CLI használatával.
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: f26fe70afe7d9e2872f06ac6da7143556278b1b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497964"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Magas rendelkezésre állású service fabric megbízható lemezalapú kötet csatlakoztatása egy szolgáltatáshálóháló-alkalmazásban 
A tárolóalkalmazások állapotának megőrzésének gyakori módja a távtároló, például az Azure File Storage vagy az olyan adatbázis használata, mint az Azure Cosmos DB. Ez jelentős olvasási és írási hálózati késést von maga után a távoli tárolóba.

Ez a cikk bemutatja, hogyan tárolhatja az állapot magas rendelkezésre állású Service Fabric megbízható lemez egy kötet csatlakoztatásával a tárolón belül egy Service Fabric Mesh alkalmazás.
A Service Fabric reliable disk köteteket biztosít a helyi olvasásokhoz, és a service fabric fürtön belül replikált írásokat a magas rendelkezésre állás érdekében. Ezzel eltávolítja az olvasási hálózati hívásokat, és csökkenti az írási műveletek hálózati késését. Ha a tároló újraindul, vagy egy másik csomópontra költözik, az új tárolópéldány ugyanazt a kötetet fogja látni, mint a régebbi. Így egyszerre hatékony és magas rendelkezésre állású.

Ebben a példában a Counter alkalmazás rendelkezik egy ASP.NET Core szolgáltatás egy weboldalt, amely megjeleníti a számláló értékét a böngészőben.

A `counterService` számláló értéke rendszeresen beolvassa a fájlt, növekményeket, és írja vissza a fájlba. A fájl egy olyan mappában van tárolva, amely a Service Fabric reliable lemez által támogatott kötetre van csatlakoztatva.

## <a name="prerequisites"></a>Előfeltételek

Használhatja az Azure Cloud Shell vagy az Azure CLI helyi telepítését a feladat végrehajtásához. Ha az Azure CLI-t ezzel `az --version` a `azure-cli (2.0.43)`cikkel szeretné használni, győződjön meg arról, hogy legalább a visszatér.  Telepítse (vagy frissítse) az Azure Service Fabric Mesh CLI bővítménymodult az alábbi [utasítások szerint.](service-fabric-mesh-howto-setup-cli.md)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba, és állítsa be az előfizetését.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot, amelyben az alkalmazást üzembe helyezheti. A következő parancs létrehoz `myResourceGroup` egy erőforráscsoportot, amelyet az Egyesült Államok keleti részén lévő helyen neveznek el. Ha az alábbi parancsban módosítja az erőforráscsoport nevét, ne felejtse el módosítani az összes következő parancsban.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A következő parancs egy Linux-alkalmazást telepít a [counter.sfreliablevolume.linux.json sablon](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json)használatával. Windows-alkalmazás központi telepítéséhez használja a [counter.sfreliablevolume.windows.json sablont.](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json) Ne feledje, hogy a nagyobb tárolórendszerképek üzembe helyezése hosszabb időt vehet igénybe.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

A telepítés állapotát is láthatja a paranccsal

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Figyelje meg az erőforrástípussal `Microsoft.ServiceFabricMesh/gateways`rendelkező átjáró-erőforrás nevét . Ezt az alkalmazás nyilvános IP-címének megszerzéséhez használjuk.

## <a name="open-the-application"></a>Az alkalmazás megnyitása

Miután az alkalmazás sikeresen üzembe helyezte, lekéri az alkalmazás átjáró-erőforrásipAddress címét. Használja a fenti szakaszban észrevett átjárónevet.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

A kimenetnek rendelkeznie kell egy tulajdonsággal, `ipAddress` amely a szolgáltatás végpontjának nyilvános IP-címe. Nyissa meg egy böngészőből. Ez akarat bemutatás egy pókháló oldal -val a pult érték lét korszerűsített mind második.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Annak ellenőrzése, hogy az alkalmazás képes-e használni a kötetet

Az alkalmazás létrehoz `counter.txt` egy fájlt, `counter/counterService` amelyet a kötet mappában nevű. A fájl tartalma a weblapon megjelenő számlálóérték.

## <a name="delete-the-resources"></a>Az erőforrások törlése

Gyakran törölje a már nem használt erőforrásokat az Azure-ban. A példához kapcsolódó erőforrások törléséhez törölje azt az erőforráscsoportot, amelyben üzembe helyezték őket (amely az erőforráscsoporthoz kapcsolódó mindent töröl) a következő paranccsal:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>További lépések

- Tekintse meg a Service Fabric megbízható kötetlemez mintaalkalmazás [a GitHubon.](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter)
- A Service Fabric-erőforrásmodellel kapcsolatos további tudnivalókért lásd a [Service Fabric Mesh-erőforrásmodellt](service-fabric-mesh-service-fabric-resources.md) bemutató cikket.
- A Service Fabric Meshsel kapcsolatos további információkért olvassa el a [Service Fabric Mesh áttekintésével](service-fabric-mesh-overview.md) foglalkozó cikket.
