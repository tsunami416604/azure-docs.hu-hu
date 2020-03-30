---
title: Alkalmazások kezelése a Visual Studióban
description: A Visual Studio segítségével hozhatja létre, fejlesztheti, csomagolhatja, telepítheti és hibakereséssel használhatja az Azure Service Fabric-alkalmazásokat és -szolgáltatásokat.
author: mikkelhegn
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: mikhegn
ms.openlocfilehash: d6734f5da0fb7e5c9052b26b55b2d90b068bdbbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614332"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>A Visual Studio segítségével egyszerűsítheti a Service Fabric-alkalmazások írását és kezelését
Az Azure Service Fabric-alkalmazásokat és -szolgáltatásokat a Visual Studióban kezelheti. Miután [beállította a fejlesztői környezetet,](service-fabric-get-started.md)a Visual Studio segítségével hozhat létre Service Fabric-alkalmazásokat, szolgáltatásokat adhat hozzá, vagy csomagokat adhat hozzá, regisztrálhat és telepíthet alkalmazásokat a helyi fejlesztési fürtben.

## <a name="deploy-your-service-fabric-application"></a>A Service Fabric-alkalmazás üzembe helyezése
Alapértelmezés szerint egy alkalmazás telepítése a következő lépéseket egyesíti egyetlen egyszerű műveletben:

1. Az alkalmazáscsomag létrehozása
2. Az alkalmazáscsomag feltöltése a képtárba
3. Az alkalmazástípus regisztrálása
4. A futó alkalmazáspéldányok eltávolítása
5. Alkalmazáspéldány létrehozása

A Visual **Studio,P.** pnyomással F5 üzembe helyezi az alkalmazást, és csatolja a hibakereső az összes alkalmazáspéldányok. A **Ctrl+F5 billentyűkombinációval** hibakeresés nélkül is telepíthet egy alkalmazást, vagy közzétehet egy helyi vagy távoli fürtön a közzétételi profil használatával.

### <a name="application-debug-mode"></a>Alkalmazáshiba-keresési mód
A Visual Studio egy **Alkalmazás-hibakeresési mód**nevű tulajdonságot biztosít, amely azt szabályozza, hogy a Visual Studios hogyan kezelje az alkalmazások telepítését a hibakeresés részeként.

#### <a name="to-set-the-application-debug-mode-property"></a>Az Alkalmazás-hibakeresési mód tulajdonság beállítása
1. A Service Fabric alkalmazásprojekt (*.sfproj) helyi menüjében válassza a **Tulajdonságok** (vagy nyomja meg az **F4** billentyűt) menüt.
2. A **Tulajdonságok** ablakban állítsa be az **Alkalmazáshibakeresési mód** tulajdonságot.

![Alkalmazáshiba-keresési mód tulajdonságának beállítása][debugmodeproperty]

#### <a name="application-debug-modes"></a>Alkalmazáshiba-keresési módok

1. **Alkalmazás frissítése** Ez a mód lehetővé teszi a kód gyors módosítását és hibakeresését, és támogatja a statikus webfájlok szerkesztését hibakeresés közben. Ez a mód csak akkor működik, ha a helyi fejlesztési fürt 1 csomópontos módban van. Ez az alapértelmezett alkalmazáshiba-keresési mód.
2. **Az Alkalmazás eltávolítása** hatására az alkalmazás törlődik a hibakeresési munkamenet befejezésekor.
3. **Automatikus frissítés** Az alkalmazás a hibakeresési munkamenet befejezésekor is fut. A következő hibakeresési munkamenet a központi telepítést frissítésként kezeli. A frissítési folyamat megőrzi az előző hibakeresési munkamenetben megadott adatokat.
4. **Alkalmazás megtartása** Az alkalmazás a hibakeresési munkamenet befejezésekor is fut a fürtben. A következő hibakeresési munkamenet kezdetén az alkalmazás törlődik.

Az **automatikus frissítési** adatok a Service Fabric alkalmazásfrissítési képességeinek alkalmazásfrissítési képességeinek alkalmazásfrissítési lehetőségeinek alkalmazásfrissítési lehetőségeinek alkalmazásfrissítési lehetőségeinek alkalmazásfrissítési lehetőségeinek alkalmazás-frissítési lehetőségeinek alkalmazás-frissítési alkalmazás-alkalmazás-alkalmazás-frissítési lehetőségeinek alkalmazás Az alkalmazások frissítéséről és arról, hogy hogyan hajthatja végre a frissítést valós környezetben, olvassa el a [Service Fabric alkalmazásfrissítés című témakört.](service-fabric-application-upgrade.md)

## <a name="add-a-service-to-your-service-fabric-application"></a>Szolgáltatás hozzáadása a Service Fabric-alkalmazáshoz
Új szolgáltatásokat adhat az alkalmazáshoz a funkció kiterjesztéséhez. Annak érdekében, hogy a szolgáltatás szerepeljen az alkalmazáscsomagban, adja hozzá a szolgáltatást az **Új hálószolgáltatás...** menüelemen keresztül.

![Új Service Fabric-szolgáltatás hozzáadása][newservice]

Válassza ki a Service Fabric projekttípusát, amelyet hozzá szeretne adni az alkalmazáshoz, és adja meg a szolgáltatás nevét.  Lásd: [A szolgáltatás keretrendszerének kiválasztása,](service-fabric-choose-framework.md) amely segít eldönteni, hogy melyik szolgáltatástípust használja.

![Válassza ki az alkalmazáshoz hozzáadni kívánt Service Fabric szolgáltatásprojekt-típust][addserviceproject]

Az új szolgáltatás hozzáadódik a megoldáshoz és a meglévő alkalmazáscsomaghoz. A szolgáltatáshivatkozások és az alapértelmezett szolgáltatáspéldány hozzáadódik az alkalmazásjegyzékhez, így a szolgáltatás létrehozása és indítása az alkalmazás következő telepítésekor történik.

![Az új szolgáltatás hozzáadódik az alkalmazásjegyzékhez][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>A Service Fabric-alkalmazás csomagolása
Az alkalmazás és szolgáltatásai fürtre való üzembe helyezéséhez létre kell hoznia egy alkalmazáscsomagot.  A csomag az alkalmazásjegyzéket, a szolgáltatásjegyzékeket és más szükséges fájlokat egy adott elrendezésben rendezi.  A Visual Studio beállítja és kezeli a csomagot az alkalmazásprojekt mappájában, a "pkg" könyvtárban.  Az **Alkalmazás** helyi menüjének **Csomag** parancsára kattintva létrejön vagy frissít az alkalmazáscsomag.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Alkalmazások és alkalmazástípusok eltávolítása a Cloud Explorer használatával
A Visual Studio segítségével alapvető fürtkezelési műveleteket hajthat végre a Cloud Explorer segítségével, amelyet a **Nézet** menüből indíthat el. Például törölheti az alkalmazásokat, és unprovision alkalmazástípusok helyi vagy távoli fürtökön.

![Alkalmazás eltávolítása][removeapplication]

> [!TIP]
> A fürtkezelési funkciókról a [Fürt megjelenítése a Service Fabric Intézővel](service-fabric-visualizing-your-cluster.md)című témakörben nyújt elő.
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
* [Service Fabric alkalmazásmodell](service-fabric-application-model.md)
* [A Service Fabric alkalmazás központi telepítése](service-fabric-deploy-remove-applications.md)
* [Alkalmazásparaméterek kezelése több környezethez](service-fabric-manage-multiple-environment-app-configuration.md)
* [A Service Fabric-alkalmazás hibakeresése](service-fabric-debugging-your-application.md)
* [A fürt megjelenítése a Service Fabric Intéző vel](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
