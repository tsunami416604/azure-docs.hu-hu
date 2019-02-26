---
title: Az oktatóanyag - frissítés az Azure Service Fabric-háló alkalmazás |} A Microsoft Docs
description: Ismerje meg, a Visual Studio használatával a Service Fabric-alkalmazás frissítése
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 23809abd06d626eb87e5d5d15d265f1769b97b66
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806733"
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

Ez a cikk bemutatja az alkalmazáson belül mikroszolgáltatások frissítése. Ebben a példában módosítjuk a `WebFrontEnd` szolgáltatás egy Feladatkategória megjelenítéséhez, és növelje a CPU-t kap. Ezután frissítjük meg a telepített szolgáltatáson.

## <a name="modify-the-config"></a>A konfiguráció módosítása

Amikor létrehoz egy Service Fabric-háló alkalmazást, a Visual studio hozzáadja egy **parameters.yaml** fájlt az egyes üzembe helyezési környezetekhez (felhőbeli és helyi). Ezeket a fájlokat meghatározhatja, paramétereit és azok értékét, majd a háló *.yaml fájlok például service.yaml vagy network.yaml lehet hivatkozni.  A Visual Studio bizonyos változókat, például a szolgáltatás használhatja a Processzor biztosít.

Frissíteni fogjuk a `WebFrontEnd_cpu` paramétert, a processzor-erőforrások `1.5` a várható, hogy a **WebFrontEnd** szolgáltatás részletesebben használható.

1. Az a **todolistapp** projekt alatt **környezetek** > **felhőalapú**, nyissa meg a **parameters.yaml** fájlt. Módosítsa a `WebFrontEnd_cpu`, értéket a következőre `1.5`. A paraméter neve előtt a szolgáltatás nevét létrehozás `WebFrontEnd_` ajánlott eljárásként, hogy megkülönböztesse a paramétereket az azonos nevű, amely a alkalmazni a különböző szolgáltatásokat.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Nyissa meg a **WebFrontEnd** projekt **service.yaml** fájlt **WebFrontEnd** > **szolgáltatási erőforrások**.

    Vegye figyelembe, hogy az a `resources:` szakaszban `cpu:` értékre van állítva `"[parameters('WebFrontEnd_cpu')]"`. Ha a projekt létrehozása folyamatban van a felhőhöz, értéke `'WebFrontEnd_cpu` veszi át a **környezetek** > **felhőalapú** > **parameters.yaml** fájlt, és lesz `1.5`. Ha a projekt helyi futtatását létrehozása van folyamatban, az értéket veszi át a **környezetek** > **helyi** > **parameters.yaml** fájlt, és "0,5" lesz.

> [!Tip]
> Alapértelmezés szerint az alkalmazásparaméter-fájlt, amely a profile.yaml fájl társ használható profile.yaml erre a fájlra vonatkozó paraméterértékeket.
> Például környezetek > felhő > parameters.yaml paraméter értékét biztosít a környezetek > felhő > profile.yaml.
>
> Ez felülírható az profile.yaml fájlt ad hozzá a következő:`parametersFilePath=”relative or full path to the parameters file”` Ha például `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` vagy `parametersFilePath=”..\CommonParameters.yaml”`

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

E végez, a kód frissítése, vagy a konfiguráció frissítése (ebben az esetben véleményét is), kattintson a jobb gombbal a Service Fabric-háló alkalmazását az Azure frissítési **todolistapp** Visual Studio, és válassza ki a **Publish...**

Ekkor megjelenik a **Service Fabric-alkalmazás közzététele** párbeszédpanel.

Használja a **célprofilt** legördülő listájában válassza ki a profile.yaml ehhez a központi telepítéshez használni kívánt fájlt. Hogy frissít az alkalmazás a felhőben, ezért kiválasztjuk a **cloud.yaml** választja a legördülő menüben, amely fogja használni a `WebFrontEnd_cpu` 1.0, az adott fájlban meghatározott értékét.

![Service Fabric Mesh párbeszédpanel a Visual Studióban](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Válassza ki az Azure-fiókját és -előfizetését. Állítsa be a **hely** a helyre, amelyet a teendőkezelő alkalmazás az Azure-bA eredetileg közzétett használt. Ebben a cikkben használt **USA keleti Régiójában**.

Állítsa be **erőforráscsoport** ahhoz az erőforráscsoporthoz, amelyet a teendőkezelő alkalmazás az Azure-bA eredetileg közzétett használt.

Állítsa be **Azure Container Registry** , az azure tárolóregisztrációs adatbázis nevét, amely során a teendőkezelő alkalmazás az Azure-bA eredetileg közzétett létrehozott.

A közzététel párbeszédpanelen nyomja le az **közzététel** gombra kattintva frissítse a teendőkezelő alkalmazás az Azure-ban.

Nyomon követheti a frissítési kiválasztásával a **Service Fabric-eszközök** ablak a Visual Studio **kimeneti** ablak. 

A rendszerkép összeállítása és az Azure Container Registrybe leküldött után egy **állapot** hivatkozás jelenik meg, amely az Azure Portal központi telepítésének figyeléséhez kattintson a kimenetet.

Miután a frissítés befejeződött, a **Service Fabric-eszközök** kimenet megjeleníti az IP-cím és port, az alkalmazás URL-cím formájában.

```json
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