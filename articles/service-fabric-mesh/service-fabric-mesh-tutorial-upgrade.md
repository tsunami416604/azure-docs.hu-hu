---
title: Oktatóanyag - Egy Azure Service Fabric Mesh alkalmazás frissítése
description: Ez az oktatóanyag egy sorozat negyedik része, és bemutatja, hogyan frissítheti az Azure Service Fabric Mesh alkalmazást közvetlenül a Visual Studióból.
author: dkkapur
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 7cdb8868f760ef0f35ab90c06b411110f871738c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75351721"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Oktatóanyag: Ismerje meg, hogyan frissíthet egy Service Fabric-alkalmazást a Visual Studio használatával

Ez az oktatóanyag egy sorozat negyedik része, és bemutatja, hogyan frissítheti az Azure Service Fabric Mesh alkalmazást közvetlenül a Visual Studióból. A frissítés kódfrissítést és konfigurációs frissítést is tartalmaz. Látni fogja, hogy a Visual Studio-n belüli frissítés és közzététel lépései megegyeznek.

Ezen oktatóanyag segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * Service Fabric Mesh szolgáltatás frissítése a Visual Studio használatával

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Service Fabric Mesh-alkalmazás létrehozása Visual Studióban](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Egy, a helyi fejlesztési fürtön futó Service Fabric Mesh-alkalmazás hibakeresése](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Service Fabric Mesh-alkalmazás üzembe helyezése](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Service Fabric Mesh-alkalmazás frissítése
> * [A Service Fabric Mesh erőforrásainak eltávolítása](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem helyezte üzembe a teendőkezelő alkalmazást, kövesse a [Service Fabric Mesh-webalkalmazás közzétételét](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md) ismertető cikk utasításait.

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Service Fabric Mesh szolgáltatás frissítése a Visual Studio használatával

Ez a cikk bemutatja, hogyan frissítheti a mikroszolgáltatás egy alkalmazáson belül. Ebben a példában módosítjuk `WebFrontEnd` a szolgáltatást egy feladatkategória megjelenítéséhez, és növeljük a megadott PROCESSZOR mennyiségét. Akkor frissítjük a telepített szolgáltatást.

## <a name="modify-the-config"></a>A konfiguráció módosítása

Service Fabric Mesh alkalmazás létrehozásakor a Visual Studio minden telepítési környezethez (felhőhöz és helyihez) egy **parameters.yaml** fájlt ad hozzá. Ezekben a fájlokban megadhatja azokat a paramétereket és azok értékeit, amelyekre a Mesh *.yaml fájlokból ( például service.yaml vagy network.yaml) hivatkozhat.  A Visual Studio néhány változót biztosít, például azt, hogy a szolgáltatás mennyi processzort használhat.

Frissítjük a `WebFrontEnd_cpu` paramétert, hogy frissítsük a cpu-erőforrásokat, `1.5` előre, hogy a **WebFrontEnd** szolgáltatás nagyobb mértékben lesz használva.

1. A **todolistapp** projekt **környezetek** > **felhő,** nyissa meg a **parameters.yaml** fájlt. Módosítsa `WebFrontEnd_cpu`a, `1.5`az értékét. A paraméter neve a szolgáltatás `WebFrontEnd_` nevével van előzve ajánlott eljárásként megkülönböztetni a különböző szolgáltatásokra vonatkozó azonos nevű paraméterektől.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Nyissa meg a **WebFrontEnd** projekt **service.yaml** fájlját a **WebFrontEnd** > **szolgáltatás erőforrásai csoportban.**

    Ne feledje, `cpu:` hogy a `"[parameters('WebFrontEnd_cpu')]"`szakasz, `resources:` van beállítva, hogy . Ha a projekt a felhőhöz készül, `'WebFrontEnd_cpu` akkor a program a**Cloud** >  **Környezetfelhő** > **parameters.yaml** `1.5`fájlból veszi az értéket, és a . Ha a projekt helyi futtatásra készül, az érték a **Környezetek** > **helyi** > **paraméterei.yaml** fájlból származik, és "0.5" lesz.

> [!Tip]
> Alapértelmezés szerint a profile.yaml fájl társának paraméterfájlja lesz használva a profile.yaml fájl értékeinek megadására.
> Például a környezetek > felhő> parameters.yaml adja meg a paraméterek értékeit környezetek > cloud > profile.yaml.
>
> Ezt felülírhatja, ha a profil.yaml fájlhoz a következőket adja hozzá:`parametersFilePath=”relative or full path to the parameters file”` Például `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` vagy`parametersFilePath=”..\CommonParameters.yaml”`

## <a name="modify-the-model"></a>A modell módosítása

Kódmódosítás bevezetéséhez vegyen `Category` fel egy `ToDoItem` tulajdonságot `ToDoItem.cs` a fájlosztályba.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Ezután `Load()` frissítse a metódust ugyanabban a fájlban, hogy a kategóriát alapértelmezett karakterláncra állítsa:

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

A `WebFrontEnd` projekt egy ASP.NET Core alkalmazás egy weboldalt, amely megmutatja a teendőlista elemeket. A `WebFrontEnd` projektben `Index.cshtml` nyissa meg és adja hozzá a következő két sort, az alábbiakban, hogy megjelenítse a tevékenység kategóriáját:

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

Az alkalmazás létrehozása és futtatása annak ellenőrzéséhez, hogy megjelenik-e egy új kategóriaoszlop a weblapon, amely felsorolja a feladatokat.

## <a name="upgrade-the-app-from-visual-studio"></a>Az alkalmazás frissítése a Visual Studio-ból

Akár kódfrissítést hajt, akár konfigurációs frissítést hajt elő (ebben az esetben mindkettőt tesszük), frissítse service Fabric Mesh alkalmazását az Azure-ban úgy, hogy a jobb gombbal a **todolistappra** kattint a Visual Studióban, majd válassza **a Közzététel parancsot...**

Ekkor megjelenik a **Service Fabric-alkalmazás közzététele** párbeszédpanel.

A **Célprofil** legördülő menüsegítségével válassza ki a telepítéshez használni kívánt profile.yaml fájlt. Frissítjük az alkalmazást a felhőben, ezért kiválasztjuk a **cloud.yaml-t** a legördülő menüben, amely az abban a `WebFrontEnd_cpu` fájlban meghatározott 1.0 értéket fogja használni.

![Service Fabric Mesh párbeszédpanel a Visual Studióban](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Válassza ki az Azure-fiókját és -előfizetését. Állítsa be a **helyet** arra a helyre, amelyet a tennivaló alkalmazás Azure-ba való eredeti közzétételekor használt. Ez a cikk használt **Kelet-USA**.

Állítsa **erőforráscsoportot** arra az erőforráscsoportra, amelyet a tennivaló alkalmazás Azure-ba való eredeti közzétételekor használt.

Állítsa be az **Azure Container Registry-t** az Azure Container registry névre, amelyet akkor hozott létre, amikor a teendőalkalmazást eredetileg közzétette az Azure-ban.

A közzétételi párbeszédpanelen nyomja meg a **Közzététel** gombot az Azure-beli teendőalkalmazás frissítéséhez.

A frissítés előrehaladásának figyelése a Visual Studio kimeneti ablakban a **Service Fabric Tools** ablaktáblájának kiválasztásával. **Output** 

Miután a rendszerkép létrehozása és az Azure Container Registry, egy **a kimenetben,** amely az Azure Portalon a központi telepítés figyeléséhez jelenik meg a for status link jelenik meg.

A frissítés befejezése után a **Service Fabric Tools** kimeneti megjeleníti az IP-címet és a port az alkalmazás url-cím formájában.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Nyisson meg egy webböngészőt, és a megadott URL-címen tekintse meg az Azure-ban futó webhelyet. Ekkor megjelenik egy kategóriaoszlopot tartalmazó weblap.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag a következőket ismertette:
> [!div class="checklist"]
> * Service Fabric Mesh alkalmazás frissítése a Visual Studio használatával

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [A Service Fabric Mesh erőforrásainak eltávolítása](service-fabric-mesh-tutorial-cleanup-resources.md)