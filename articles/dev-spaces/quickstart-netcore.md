---
title: Kubernetes Dev Spaces-tér létrehozása a felhőben | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: zarhoads
ms.date: 09/26/2018
ms.topic: quickstart
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók
ms.openlocfilehash: 0638f908150c90c3b73dc3036f4407d17cceb9e3
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705703"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-vs-code"></a>Rövid útmutató: Kubernetes Dev Spaces-tér létrehozása az Azure Dev Spaces segítségével (.NET Core és VS Code)

Ebből az útmutatóból a következőket tudhatja meg:

- Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
- Iteratív kódfejlesztés tárolókban a VS Code és a parancssor használatával.
- Hibakeresés a kódban a Dev Spaces-térben a VS Code-ból

> [!Note]
> **Ha bármikor elakad**, tekintse meg a [Hibaelhárítás](troubleshooting.md) szakaszt, vagy írjon egy hozzászólást erre a lapra. A részletesebb [oktatóanyagot](get-started-netcore.md) is kipróbálhatja.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs fiókja, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/download).
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest), 2.0.43-as vagy újabb verzió.
- Az EastUS, EastUS2, CentralUS, WestUS2, WestEurope, SoutheastAsia, CanadaCentral vagy CanadaEast régióban lévő, a Kubernetes 1.9.6-os vagy újabb verzióját futtató Kubernetes-fürt, amelyen engedélyezve van a **HTTP-alkalmazásútválasztás**.

    ```cmd
    az group create --name MyResourceGroup --location <region>
    az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.11.2 --enable-addons http_application_routing --generate-ssh-keys
    ```

## <a name="set-up-azure-dev-spaces"></a>Az Azure Dev Spaces beállítása

Az Azure CLI és az Azure Dev Spaces bővítmény Windows, Mac és Linux rendszerű gépeken telepíthető és futtatható. Linux esetén a következő disztribúciók támogatottak: Ubuntu (18.04, 16.04 és 14.04), Debian 8 és 9, RHEL 7, Fedora 26+, CentOS 7, openSUSE 42.2 és SLES 12.

Az Azure Dev Spaces telepítéséhez hajtsa végre az alábbi lépéseket:

1. A Dev Spaces beállítása az AKS-fürtön: `az aks use-dev-spaces -g MyResourceGroup -n MyAKS`
1. Töltse le az [Azure Dev Spaces bővítményt](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) a VS Code-hoz. Kattintson egyszer a Telepítés lehetőségre a bővítmény Marketplace-oldalán, majd még egyszer a VS Code-ban.

## <a name="build-and-run-code-in-kubernetes"></a>Kód létrehozása és futtatása Kubernetesben

1. Mintakód letöltése a GitHubról: [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) 
1. A könyvtár módosítása a webfrontend mappára: `cd dev-spaces/samples/dotnetcore/getting-started/webfrontend`
1. Docker és Helm diagramobjektumok létrehozása: `azds prep --public`
1. A kód létrehozása és futtatása az AKS-ben. Futtassa a **webfrontend mappából** a terminálablakban a következő parancsot: `azds up`
1. Keresse meg a konzolkimenetben az `up` parancs által létrehozott URL-re vonatkozó adatokat. Ez az alábbi formátumban lesz: 

   `(pending registration) Service 'webfrontend' port 'http' will be available at <url>\r\nService 'webfrontend' port 80 (TCP) is available at http://localhost:<port>` 

   Nyissa meg ezt az URL-t egy böngészőablakban, és betöltődik a webalkalmazás. 
   
   > [!Note]
   > Első alkalommal több percet is igénybe vehet, hogy rendelkezésre álljon a nyilvános DNS. Ha az URL-cím nem oldódik fel, használhatja a konzolkimeneten megjelenő alternatív http://localhost:<portnumber> URL-címet. Ha a localhost URL-t használja, úgy tűnhet, hogy a tároló helyileg fut, de valójában az AKS-ben fut. Az Ön kényelme, valamint a helyi gép és a szolgáltatás közötti interakció elősegítése érdekében az Azure Dev Spaces egy ideiglenes SSH-csatornát hoz létre az Azure-ban futó tárolóhoz. Visszatérhet és kipróbálhatja a nyilvános URL-címet később, amikor kész a DNS-rekord.

### <a name="update-a-content-file"></a>Tartalomfájlok frissítése

1. Keresse meg például a `./Views/Home/Index.cshtml` fájlt, és szerkessze a HTML-kódot. Például módosíthatja a 70. sor korábbi szövegét (`<h2>Application uses</h2>`) egy új szövegre (`<h2>Hello k8s in Azure!</h2>`).
1. Mentse a fájlt. Néhány pillanat múlva egy, a terminálablakban megjelenő üzenet tájékoztatja, hogy a futó tárolóban lévő egyik fájl frissült.
1. Lépjen a böngészőre, és frissítse az oldalt. A weboldalnak meg kell jelenítenie a frissített HTML-tartalmat.

Mi történt? A tartalomfájlok (például HTML és CSS) módosításait nem szükséges egy .NET Core webalkalmazásban újrafordítani, így az aktív `azds up` parancs automatikusan szinkronizálja a módosított tartalomfájlokat az Azure-ban futó tárolóba, így azonnal megtekintheti a tartalmak módosításait.

### <a name="update-a-code-file"></a>Kódfájlok frissítése
A kódfájlok frissítése egy kicsit több munkát igényel, mert a .NET Core-alkalmazásnak újra létre kell hoznia és elő kell állítania az alkalmazás frissített bináris fájljait.

1. A terminálablakban nyomja le a `Ctrl+C` billentyűkombinációt (az `azds up` leállításához).
1. Nyissa meg a `Controllers/HomeController.cs` nevű kódfájlt, és szerkessze az Információ oldalon megjelenő üzenetet: `ViewData["Message"] = "Your application description page.";`
1. Mentse a fájlt.
1. Futtassa az `azds up` parancsot a terminálablakban. 

Ez a parancs újraépíti a tárolórendszerképet, és újra üzembe helyezi a Helm-diagramot. A módosított kód hatásainak a futó alkalmazásban való megtekintéséhez lépjen a webalkalmazás Információ menüjére.

Azonban a kódfejlesztésnek van egy még ennél is *gyorsabb módszere*, amelyet a következő szakaszban mutatunk be. 

## <a name="debug-a-container-in-kubernetes"></a>Tároló hibakeresése a Kubernetesben

Ebben a részben közvetlenül a VS Code-dal végezhet hibakeresést az Azure-ban futó tárolón. Azt is megtudhatja, hogyan végezheti el gyorsabban a szerkesztés-futtatás-tesztelés hurkot.

![](./media/common/edit-refresh-see.png)

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>A hibakeresési objektum inicializálása a VS Code-bővítménnyel
Először konfigurálja a kódprojektet úgy, hogy a VS Code kommunikálni tudjon az Azure-beli Dev Spaces-térrel. A VS Code Azure Dev Spaceshez készült bővítménye biztosít egy segítőparancsot a hibakeresési konfiguráció beállításához. 

Nyissa meg a **parancskatalógust** (**Nézet | Parancskatalógus** menü), és az automatikus kitöltés használatával írja be és válassza ki a következő parancsot: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 

Ez hozzáadja az Azure Dev Spaceshez készült hibakeresési konfigurációt a `.vscode` mappához. Ez a parancs nem keverendő össze az `azds prep` paranccsal, amely az üzembe helyezéshez konfigurálja a projektet.

![](./media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Az AZDS hibakeresési konfiguráció kiválasztása
1. A hibakeresési nézet megnyitásához kattintson a **tevékenységsáv** Hibakeresés ikonjára a VS Code oldalán.
1. Válassza a **.NET Core indítása (AZDS)** lehetőséget mint aktív hibakeresési konfigurációt.

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> Ha nem lát Azure Dev Spaces-parancsokat a parancspalettán, győződjön meg róla, hogy a VS Code Azure Dev Spaceshez készült bővítménye telepítve van. Győződjön meg arról, hogy a VS Code-ban megnyitott munkaterület az azds.yaml fájlt tartalmazó mappa.


### <a name="debug-the-container-in-kubernetes"></a>A tároló hibakeresése a Kubernetesben
A kód a Kubernetesben való hibakereséséhez nyomja le az **F5** billentyűt.

Az `up` parancshoz hasonlóan a kód szinkronizálva lesz a Dev Spaces-térbe, továbbá létrejön és települ egy tároló a Kubernetesben. Ezúttal persze a hibakereső a távoli tárolóhoz van csatlakoztatva.

> [!Tip]
> A VS Code-állapotsáv egy kattintható URL-címet jelenít meg.

Állítson be egy töréspontot egy kiszolgálóoldali kódfájlban, például a `Controllers/HomeController.cs` forrásfájl `Index()` függvényében. Az oldal a böngészőben való frissítésével a töréspont érintve lesz.

Ugyanúgy teljes körű hozzáférése van a hibakeresési információkhoz, mint ha helyileg futna a kód (pl. hívási verem, helyi változók, kivételek adatai stb.).

### <a name="edit-code-and-refresh"></a>Kód szerkesztése és frissítés
Miközben a hibakereső aktív, szerkessze a kódot. Például módosítsa az Információ lap üzenetét a `Controllers/HomeController.cs` fájlban. 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

Mentse a fájlt, és a **Hibakeresési műveletek panelen** kattintson a **Frissítés** gombra. 

![](media/get-started-netcore/debug-action-refresh.png)

Ahelyett, hogy a kód minden szerkesztése alkalmával újra létrehozna és üzembe helyezne egy új tárolórendszerképet, ami általában sok időt vesz igénybe, az Azure Dev Spaces növekményesen újrafordítja a kódot a meglévő tárolón belül, hogy gyorsabb szerkesztési/hibakeresési ciklust biztosítson.

Frissítse a webalkalmazást a böngészőben, és lépjen az Információ oldalra. Az egyedi üzenetnek meg kell jelennie a felhasználói felületen.

**Most tehát rendelkezésére áll egy módszer, amellyel gyorsan iterálhatja a kódot, és közvetlenül a Kubernetesben végezheti a hibakeresést.**

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan segít az Azure Dev Spaces az összetettebb alkalmazások fejlesztésében több tároló között, és hogyan egyszerűsítheti le az együttműködésen alapuló fejlesztést a kód különböző verzióinak vagy ágainak különböző terekben való kezelésével. 

> [!div class="nextstepaction"]
> [Több tároló használata és fejlesztés csapatban](team-development-netcore.md)
