---
title: Az oktatóanyag - frissítés az Azure Service Fabric-háló alkalmazás |} A Microsoft Docs
description: Ismerje meg, a Visual Studio használatával a Service Fabric-alkalmazás frissítése
services: service-fabric-mesh
documentationcenter: .net
author: tylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 7985c8e9e26126040d842ded998a953281daa2ae
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49953552"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Oktatóanyag: Ismerje meg, a Visual Studio használatával a Service Fabric-alkalmazás frissítése

Ez az oktatóanyag része, amely egy sorozat negyedik, és bemutatja, hogyan lehet egy Azure Service Fabric-háló alkalmazás frissítése közvetlenül a Visual Studióból. A frissítés a kód frissítése és a egy konfigurációfrissítéskor tartalmazza. Látni fogja, hogy a frissítés és a közzététel a Visual studióban a lépések ugyanazok.

Ezen oktatóanyag segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * A Service Fabric-háló szolgáltatás frissítése a Visual Studio használatával

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Service Fabric Mesh-alkalmazás létrehozása a Visual Studióban](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Egy, a helyi fejlesztési fürtön futó Service Fabric Mesh-alkalmazás hibakeresése](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Service Fabric Mesh-alkalmazás üzembe helyezése](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Frissítés a Service Fabric-háló alkalmazás
> * [A Service Fabric Mesh erőforrásainak eltávolítása](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem helyezte üzembe a teendőkezelő alkalmazást, kövesse a [Service Fabric Mesh-webalkalmazás közzétételét](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md) ismertető cikk utasításait.

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>A Service Fabric-háló szolgáltatás frissítése a Visual Studio használatával

Ez a cikk bemutatja az alkalmazáson belül mikroszolgáltatások egymástól függetlenül frissítése.  Ebben a példában azt fogja módosítani a `WebFrontEnd` szolgáltatás egy Feladatkategória megjelenítéséhez. Ezután frissítjük meg a telepített szolgáltatáson.

## <a name="modify-the-config"></a>A konfiguráció módosítása

Frissítések a kód módosítása vagy konfigurációs módosítások miatt lehet.  A konfiguráció módosításakor bevezetni, nyissa meg a `WebFrontEnd` projekt `service.yaml` fájlt (alatt a **szolgáltatási erőforrások** csomópont).

Az a `resources:` területén módosíthatja `cpu:` 0,5 – 1.0-t, a várható, hogy a webes kezelőfelület erősen használható. Most így kell kinéznie:

```xml
              ...
              resources:
                requests:
                  cpu: 1.0
                  memoryInGB: 1
              ...
```

## <a name="modify-the-model"></a>A modell módosítása

Kódváltoztatást bevezetni, adjon hozzá egy `Category` tulajdonságot a `ToDoItem` az osztály a `ToDoItem.cs` fájlt.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Ezután frissítse a `Load()` metódus ugyanebben a fájlban, a kategória beállítása alapértelmezett karakterláncra:

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>A szolgáltatás módosítása

A `WebFrontEnd` projekt to-do-lista elemeire megjelenítő weblap ASP.NET Core alkalmazás. Az a `WebFrontEnd` projektben nyissa meg `Index.cshtml` , és adja hozzá az alábbi két sort, lásd alább, a feladat kategória megjelenítése:

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Hozhat létre, és futtassa az alkalmazást, ellenőrizze, hogy megjelenik-e a weblap, amelyen a feladatokat tartalmazza az új kategória oszlop.

## <a name="upgrade-the-app-from-visual-studio"></a>Frissítse az alkalmazást a Visual Studióból

Függetlenül attól, hogy így a kód frissítése, vagy a konfiguráció frissítése (ebben az esetben teljesítünk, mindkettőt), a Service Fabric-háló alkalmazást Azure kattintson a jobb gombbal a frissítésre **todolistapp** a Visual Studióban, és válassza **közzététel ...**

Ekkor megjelenik a **Service Fabric-alkalmazás közzététele** párbeszédpanel.

![Service Fabric Mesh párbeszédpanel a Visual Studióban](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Válassza ki az Azure-fiókját és -előfizetését. Ügyeljen arra, hogy **hely** a helyre, amely a teendőkezelő alkalmazás az Azure-bA eredetileg közzétett használt értékre van állítva. Ebben a cikkben használt **USA keleti Régiójában**.

Ügyeljen arra, hogy **erőforráscsoport** az erőforráscsoport, a teendőkezelő alkalmazás az Azure-bA eredetileg közzétett használt értékre van állítva.

Ügyeljen arra, hogy **Azure Container Registry** az azure tárolóregisztrációs adatbázis nevét, amely során a teendőkezelő alkalmazás az Azure-bA eredetileg közzétett létrehozott értékre van állítva.

A közzététel párbeszédpanelen nyomja le az **közzététel** gombra kattintva frissítse a teendőkezelő alkalmazás az Azure-ban.

A frissítés állapotának kiválasztásával figyelheti a **Service Fabric-eszközök** ablak a Visual Studio **kimeneti** ablak. Miután a frissítés befejeződött, a **Service Fabric-eszközök** kimenet megjeleníti az IP-cím és port, az alkalmazás URL-cím formájában.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Nyisson meg egy webböngészőt, és a megadott URL-címen tekintse meg az Azure-ban futó webhelyet. Most már megtekintheti az egy weblap, amelyen kategória oszlopot tartalmaz.

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen része az alábbiakat ismertette:
> [!div class="checklist"]
> * A Visual Studio használatával a Service Fabric-háló alkalmazás frissítése

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [A Service Fabric Mesh erőforrásainak eltávolítása](service-fabric-mesh-tutorial-cleanup-resources.md)