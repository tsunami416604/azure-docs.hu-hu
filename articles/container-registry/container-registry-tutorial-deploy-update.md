---
title: "Azure tároló beállításjegyzék útmutató - leküldéses frissített regionális használatával végzett központi telepítésekhez"
description: "A módosított Docker-lemezkép leküldése a georeplikált Azure beállításjegyzék tartalmaz, majd a változtatások több régióba futó webalkalmazások automatikusan telepíti. A három részből sorozat három része."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 359fdcabd579d277e40f02eba2d4603ebd9f5f1f
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="push-an-updated-image-to-regional-deployments"></a>Frissített leküldése regionális központi telepítések

Ez a három részből útmutató azokat a három részt. Az a [az oktatóanyag előző](container-registry-tutorial-deploy-app.md), georeplikáció két különböző területi webalkalmazás központi telepítések konfigurálása. Ebben az oktatóanyagban először módosítja az alkalmazást, majd egy új tároló lemezkép, és hogy a georeplikált beállításjegyzék. Végül megtekintheti a módosítás Azure tároló beállításjegyzék webhookokkal, mindkét webalkalmazás példányát által automatikusan telepített.

Ebben az oktatóanyagban az adatsorozat utolsó része:

> [!div class="checklist"]
> * A webalkalmazás HTML módosítása
> * Hozza létre, és a Docker kép címke
> * A módosítás leküldése Azure tároló beállításjegyzék
> * A frissített alkalmazás két különböző régiókban megtekintése

Ha még nem konfigurálta a két *Web App az tárolókat* regionális központi telepítések, térjen vissza a sorozat előző oktatóanyaghoz [telepítés webalkalmazást az Azure-tároló beállításjegyzék](container-registry-tutorial-deploy-app.md).

## <a name="modify-the-web-application"></a>A webalkalmazás módosítása

Ebben a lépésben lehet módosítani a webes alkalmazás, amely után a frissített tároló kép leküldése Azure tároló beállításjegyzék jól látható lesz.

Keresse a `AcrHelloworld/Views/Home/Index.cshtml` fájl az alkalmazás forrás [GitHub alapján klónozták](container-registry-tutorial-prepare-registry.md#get-application-code) egy előző oktatóprogram és nyissa meg a kedvenc szövegszerkesztőjével. Adja hozzá a következő sort a meglévő alatt `<h1>` sor:

```html
<h1>MODIFIED</h1>
```

A módosított `Index.cshtml` hasonlóan kell kinéznie:

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<style>
    body {
        background-image: url('images/azure-regions.png');
        background-size: cover;
    }
    .footer {
        position: fixed;
        bottom: 0px;
        width: 100%;
    }
</style>

<h1 style="text-align:center;color:blue">Hello World from:  @ViewData["REGION"]</h1>
<h1>MODIFIED</h1>
<div class="footer">
    <ul>
        <li>Registry URL: @ViewData["REGISTRYURL"]</li>
        <li>Registry IP: @ViewData["REGISTRYIP"]</li>
        <li>Registry Region: @ViewData["REGION"]</li>
    </ul>
</div>
```

## <a name="rebuild-the-image"></a>A lemezkép újbóli létrehozása

Most, hogy a webalkalmazás frissítése, építse újra a tároló kép. Mint korábban használja a teljesen minősített lemezképnév, beleértve a bejelentkezési URL-címe, a címke:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="push-image-to-azure-container-registry"></a>Azure-tároló beállításjegyzék leküldéses kép

Most, a frissített leküldéses *acr-helloworld* a georeplikált beállításjegyzék lemezképet tároló. Itt, egyetlen most végrehajtása `docker push` központi telepítése a frissített lemezképet a beállításjegyzék replikák mindkét parancs a *USA nyugati régiója* és *USA keleti régiója* régiók.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Kimenet az alábbihoz hasonlóan kell megjelennie:

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
5b9454e91555: Pushed
d6803756744a: Layer already exists
b7b1f3a15779: Layer already exists
a89567dff12d: Layer already exists
59c7b561ff56: Layer already exists
9a2f9413d9e4: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:4c3f2211569346fbe2d1006c18cbea2a4a9dcc1eb3a078608cef70d3a186ec7a size: 1792
```

## <a name="view-the-webhook-logs"></a>A webhook naplók megtekintése

A replikált a lemezképet, amíg az Azure tároló beállításjegyzék webhookok indított alatt tekintheti meg.

A regionális webhookokkal, a tárolót, hogy üzembe helyezésekor létrehozott megjelenítéséhez *tárolók webalkalmazásait* előző oktatóanyagok esetén keresse meg a tároló beállításjegyzék, az Azure portálon, majd válassza ki **Webhookok**alatt **szolgáltatások**.

![Tároló beállításjegyzék Webhookok az Azure-portálon][tutorial-portal-01]

Válassza ki az egyes Webhook a hívások és a válaszok előzményeinek megtekintéséhez. Egy sort kell megjelennie a **leküldéses** mindkét Webhookok a naplókban lévő művelettel. A Webhook a naplóban található itt, a *USA nyugati régiója* terület tartalmazza a **leküldéses** által indított művelet a `docker push` az előző lépésben:

![Tároló beállításjegyzék Webhook bejelentkezés az Azure portálon (USA nyugati régiója)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>A frissített web app megtekintése

A Webhook értesítse a Web Apps, hogy az új lemezképet a beállításjegyzéket, amely automatikusan telepíti a frissített tároló a két regionális webalkalmazások tolni-e.

Győződjön meg arról, hogy az alkalmazás frissültek-e mindkét központi telepítés mindkét regionális webalkalmazás központi telepítés a böngészőben navigáljon. Ne feledje találja a jobb felső részén minden App Service áttekintése lapon a telepített webalkalmazás URL-CÍMÉT.

![App Service áttekintése az Azure-portálon][tutorial-portal-03]

A frissített alkalmazás megtekintéséhez jelölje ki a kapcsolat az App Service áttekintése. Íme egy példa nézetben a futó alkalmazás *USA nyugati régiója*:

![USA nyugati régiója a régióban fut módosított webalkalmazás böngésző nézet][deployed-app-westus-modified]

Győződjön meg arról, hogy a frissített tároló lemezképet is állított be a *USA keleti régiója* telepítési a böngészőben megtekintésével.

![USA keleti régiójában futó módosított webalkalmazás böngésző nézet][deployed-app-eastus-modified]

Egyetlen `docker push`, mindkét regionális webalkalmazás központi telepítés frissítése és Azure tároló beállításjegyzék kiszolgált adattárak hálózati zárja be a tároló képek.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban frissítése, és a webes alkalmazás tároló új verziójának a georeplikált beállításjegyzék leküldve. Azure tároló beállításjegyzék Webhookok webalkalmazásait a frissítés, a beállításjegyzék replikák a helyi lekérési kiváltó tárolók értesítést kap.

Az adatsorozat utolsó oktatóanyag ezen meg:

> [!div class="checklist"]
> * A webes alkalmazás HTML frissítése
> * Épül, és a Docker kép címkézett
> * A módosítás az Azure-tároló beállításjegyzék leküldött
> * A frissített app megtekinthető két különböző régiókban

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png