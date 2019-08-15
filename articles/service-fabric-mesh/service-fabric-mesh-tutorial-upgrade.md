---
title: Oktatóanyag – Azure Service Fabric Mesh-alkalmazás frissítése | Microsoft Docs
description: Megtudhatja, hogyan frissíthet egy Service Fabric alkalmazást a Visual Studióval
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 20aa65f0a8e47485e71fd03d73ff144f5290bcb7
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036085"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Oktatóanyag: Megtudhatja, hogyan frissíthet egy Service Fabric alkalmazást a Visual Studióval

Ez az oktatóanyag egy sorozat negyedik része, amely bemutatja, hogyan frissíthet egy Azure Service Fabric Mesh-alkalmazást közvetlenül a Visual studióból. A frissítés tartalmazza a kód frissítését és a konfiguráció frissítését is. Láthatja, hogy a Visual studión belüli verziófrissítés és közzététel lépései megegyeznek.

Ezen oktatóanyag segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * Service Fabric Mesh szolgáltatás frissítése a Visual Studio használatával

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Service Fabric Mesh-alkalmazás létrehozása a Visual Studióban](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Egy, a helyi fejlesztési fürtön futó Service Fabric Mesh-alkalmazás hibakeresése](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Service Fabric Mesh-alkalmazás üzembe helyezése](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Service Fabric Mesh-alkalmazás frissítése
> * [A Service Fabric Mesh erőforrásainak eltávolítása](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem helyezte üzembe a teendőkezelő alkalmazást, kövesse a [Service Fabric Mesh-webalkalmazás közzétételét](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md) ismertető cikk utasításait.

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Service Fabric Mesh szolgáltatás frissítése a Visual Studio használatával

Ez a cikk bemutatja, hogyan frissíthet egy alkalmazást egy alkalmazáson belül. Ebben a példában a `WebFrontEnd` szolgáltatást módosítjuk a feladat kategóriájának megjelenítéséhez, és növeljük a megadott CPU mennyiségét. Ezután frissítjük a telepített szolgáltatást.

## <a name="modify-the-config"></a>A konfiguráció módosítása

Service Fabric Mesh-alkalmazás létrehozásakor a Visual Studio minden központi telepítési környezet (felhő és helyi) esetében hozzáadja a **Parameters. YAML** fájlt. Ezekben a fájlokban megadhat paramétereket és azok értékeit, amelyek a rácsvonal *. YAML, például a Service. YAML vagy a Network. YAML fájlokra hivatkozhatnak.  A Visual Studio bizonyos változókat biztosít Önnek, például a szolgáltatás által használható CPU mennyiségét.

Frissítjük a `WebFrontEnd_cpu` paramétert a CPU `1.5` -erőforrások frissítéséhez, hogy a rendszer a webfrontend szolgáltatást nagyobb mértékben fogja használni.

1. A **todolistapp** projektben a **környezetek** > **felhő**alatt nyissa meg a **Parameters. YAML** fájlt. Módosítsa a `WebFrontEnd_cpu` `1.5`értéket a értékre. A paraméter neve a szolgáltatás nevével `WebFrontEnd_` van ellátva ajánlott eljárásként, hogy megkülönböztesse azt a különböző szolgáltatásokra érvényes azonos nevű paraméterektől.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Nyissa meg a webfrontend-projekt **Service. YAML** fájlját a webfrontend > **szolgáltatás-erőforrások**területen.

    Vegye figyelembe, hogy `resources:` a `cpu:` szakasz a következőre `"[parameters('WebFrontEnd_cpu')]"`van beállítva:. Ha a projekt a felhőhöz készült, a rendszer a **környezetek** > **felhő** > **Parameters. YAML** -fájlból veszi át a értéket, és `1.5`a következő lesz:. `'WebFrontEnd_cpu` Ha a projekt helyben történő futtatásra készült, az érték a **környezetek** > **helyi** > **Parameters. YAML** -fájlból fog származni, és a következő lesz: "0,5".

> [!Tip]
> Alapértelmezés szerint a profil. YAML fájlhoz tartozó, a profil. YAML fájl értékeinek megadására szolgáló fájl.
> Például a környezetek > a Cloud > Parameters. YAML megadja a környezeti paraméterek értékét > a Cloud > Profile. YAML.
>
> Ezt felülbírálhatja úgy, hogy hozzáadja a következőt a profil. YAML fájlhoz:`parametersFilePath=”relative or full path to the parameters file”` Ha például `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` vagy `parametersFilePath=”..\CommonParameters.yaml”`

## <a name="modify-the-model"></a>A modell módosítása

A kód módosításának bevezetéséhez adjon `Category` hozzá egy tulajdonságot `ToDoItem` a `ToDoItem.cs` fájlban található osztályhoz.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Ezután frissítse a `Load()` metódust ugyanabban a fájlban a kategória alapértelmezett karakterlánccá való beállításához:

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

A `WebFrontEnd` projekt egy ASP.net Core-alkalmazás, amely a feladatlistákat megjelenítő weblapokat jeleníti meg. A projektben nyissa `Index.cshtml` meg az alábbi két sort, és adja hozzá a feladat kategóriájának megjelenítéséhez: `WebFrontEnd`

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

Hozza létre és futtassa az alkalmazást annak ellenőrzéséhez, hogy megjelenik-e egy új kategória oszlop a weblapon, amely felsorolja a feladatokat.

## <a name="upgrade-the-app-from-visual-studio"></a>Az alkalmazás frissítése a Visual studióból

Legyen szó akár a kód verziófrissítéséről, akár a konfiguráció frissítéséről (ebben az esetben mindkettőt teszünk), frissítse a Service Fabric Mesh alkalmazást az Azure-ban, és kattintson a jobb gombbal a Visual Studióban a **todolistapp** elemre, majd válassza a **Közzététel** lehetőséget.

Ekkor megjelenik a **Service Fabric-alkalmazás közzététele** párbeszédpanel.

Válassza ki a telepítéshez használni kívánt profil. YAML fájlt a **cél profil** legördülő listából. Az alkalmazást a felhőben frissítjük, ezért a legördülő menüben kiválasztjuk a **Cloud. YAML** , amely a `WebFrontEnd_cpu` fájlban definiált 1,0 értéket fogja használni.

![Service Fabric Mesh párbeszédpanel a Visual Studióban](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Válassza ki az Azure-fiókját és -előfizetését. Állítsa be azt a helyet, amelyet az Azure-ban eredetileg közzétett a tennivaló alkalmazáshoz. Ez a cikk az **USA keleti**régiójában használatos.

Állítsa be az **erőforráscsoportot** arra az erőforráscsoporthoz, amelyet a tennivalók Azure-ba történő eredeti közzétételekor használt.

Állítsa be Azure Container registryt arra az Azure Container Registry-névre, amelyet akkor hozott létre, amikor eredetileg közzétette a tennivaló alkalmazást az Azure-ban.

A közzététel párbeszédpanelen a **Közzététel** gombra kattintva frissítse a tennivaló alkalmazást az Azure-ban.

A frissítés előrehaladásának figyeléséhez válassza a Visual Studio **kimeneti** ablakának **Service Fabric eszközök** paneljét. 

Miután a rendszerképet létrehozta, és leküldte a Azure Container Registryre, a kimenetben megjelenik az **állapot** hivatkozása, amelyre kattintva figyelheti a központi telepítést a Azure Portal.

Ha a frissítés befejeződött, a **Service Fabric-eszközök** kimenete URL-cím formájában megjeleníti az alkalmazás IP-címét és portját.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Nyisson meg egy webböngészőt, és a megadott URL-címen tekintse meg az Azure-ban futó webhelyet. Ekkor egy olyan weboldalt kell látnia, amely tartalmaz egy category (kategória) oszlopot.

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen része az alábbiakat ismertette:
> [!div class="checklist"]
> * Service Fabric Mesh-alkalmazás frissítése a Visual Studio használatával

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [A Service Fabric Mesh erőforrásainak eltávolítása](service-fabric-mesh-tutorial-cleanup-resources.md)