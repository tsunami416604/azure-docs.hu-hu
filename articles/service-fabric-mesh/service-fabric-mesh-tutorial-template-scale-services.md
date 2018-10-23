---
title: Oktatóanyag – Az Azure Service Fabric Meshben futó alkalmazások méretezése | Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan lehet méretezni a Service Fabric Meshben futó alkalmazások szolgáltatásait.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 4d6839fea3ce0eb74cdf87396716cdc69c0cd1a0
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165981"
---
# <a name="tutorial-scale-an-application-running-in-service-fabric-mesh"></a>Oktatóanyag: Az Azure Service Fabric Meshben futó alkalmazások méretezése

Ez az oktatóanyag egy sorozat második része. Elsajátíthatja, hogyan méretezheti manuálisan egy alkalmazás több szolgáltatáspéldányát, amelyeket [előzőleg helyezett üzembe a Service Fabric Meshben](service-fabric-mesh-tutorial-template-deploy-app.md). Ha elkészült, rendelkezni fog egy kezelőfelületi szolgáltatással, amely három példányt futtat, és egy adatszolgáltatással, amely két példányt futtat.

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A kívánt számú szolgáltatáspéldány konfigurálása
> * Frissítés végrehajtása

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Alkalmazás üzembe helyezése a Service Fabric Meshben sablon használatával](service-fabric-mesh-tutorial-template-deploy-app.md)
> * A Service Fabric Meshben futó alkalmazás méretezése
> * [A Service Fabric Meshben futó alkalmazás frissítése](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Alkalmazás eltávolítása](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* [Telepítse helyileg az Azure CLI-t és a Service Fabric Mesh CLI-t](service-fabric-mesh-howto-setup-cli.md#install-the-service-fabric-mesh-cli-locally).

## <a name="manually-scale-your-services-in-or-out"></a>Szolgáltatások horizontális le- és felskálázása

Az alkalmazások a Service Fabric Meshben való üzembe helyezésének egyik legfőbb előnye, hogy a szolgáltatásokat egyszerűen lehet horizontálisan le- és felskálázni. Ez akkor hasznos, ha változó mértékű terhelés éri a szolgáltatásokat, vagy ha javítani szeretne a rendelkezésre álláson.

Ez az oktatóanyag az [előzőleg telepített](service-fabric-mesh-tutorial-template-deploy-app.md) és már futó Teendőlista-mintát használja példaként. Az alkalmazás két szolgáltatással rendelkezik: WebFrontEnd és ToDoService. Eredetileg mindkét szolgáltatás 1 replikával lett üzembe helyezve.  A WebFrontEnd szolgáltatás futó replikáinak számát a következő futtatásával tekintheti meg:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

A ToDoService szolgáltatás futó replikáinak számát a következő futtatásával tekintheti meg:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

Az alkalmazás erőforrásának üzembehelyezési sablonjában mindkét szolgáltatás rendelkezik egy *replicaCount* (replikaszám) tulajdonsággal, amellyel megadhatja, hány példányban szeretné a szolgáltatást üzembe helyezni. Egy alkalmazás több szolgáltatásból is állhat, és minden szolgáltatás egyedi *replicaCount* értékkel rendelkezik. A replikákat a rendszer együtt kezeli és helyezi üzembe. A szolgáltatásreplikák számának módosításához módosítsa minden olyan szolgáltatás *replicaCount* értékét az üzembehelyezési sablonban vagy a paraméterfájlban, amelyet horizontálisan le szeretne skálázni.  Ezt követően frissítse az alkalmazást.

### <a name="modify-the-deployment-template-parameters"></a>Az üzembehelyezési sablon paramétereinek módosítása

Ha a sablon olyan értékeket tartalmaz, amelyek az alkalmazás üzembe helyezését követően vagy üzemelő példányonként várhatóan változni fognak (amennyiben más üzemelő példányokhoz is használni kívánja a sablont), az ajánlott eljárás az értékek paraméterezése.

Korábban az alkalmazás a [mesh_rp.windows.json deployment template](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) és a [mesh_rp.windows.parameter.json parameters](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) fájl segítségével lett üzembe helyezve.

Nyissa meg helyileg a [mesh_rp.windows.parameter.json parameters](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) fájlt, és állítsa be a *frontEndReplicaCount* értéket 3-ra, a *serviceReplicaCount* értékét pedig 2-re:

```json
      "frontEndReplicaCount":{
        "value": "3"
      },
      "serviceReplicaCount":{
        "value": "2"
      }
```

Mentse a paraméterfájl módosításait.  A *frontEndReplicaCount* és a *serviceReplicaCount* paraméter a [mesh_rp.windows.json deployment template](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) *parameters* szakaszában van deklarálva:

```json
"frontEndReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    },
    "serviceReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    }
```

A WebFrontEnd szolgáltatás *replicaCount* tulajdonsága a *frontEndReplicaCount* paraméterre, a ToDoService szolgáltatás *replicaCount* tulajdonsága pedig a *serviceReplicaCount* paraméterre hivatkozik:

```json
    "services": [
    {
    "name": "WebFrontEnd",
    "properties": {
        "description": "WebFrontEnd description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('frontEndReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    },
    {
    "name": "ToDoService",
    "properties": {
        "description": "ToDoService description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('serviceReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    }
],
```

A sablon módosítása után frissítse az alkalmazást.

### <a name="upgrade-your-application"></a>Az alkalmazás frissítése

Az alkalmazást futás közben is frissítheti, ha újratelepíti a sablont és a frissített paraméterfájlt:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Ekkor elindul az alkalmazás működés közbeni frissítése, és néhány percen belül látnia kell a szolgáltatáspéldányok számának növekedését.  A WebFrontEnd szolgáltatás futó replikáinak számát a következő futtatásával tekintheti meg:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

A ToDoService szolgáltatás futó replikáinak számát a következő futtatásával tekintheti meg:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * A kívánt számú szolgáltatáspéldány konfigurálása
> * Frissítés végrehajtása

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [A Service Fabric Meshben futó alkalmazás frissítése](service-fabric-mesh-tutorial-template-upgrade-app.md)
