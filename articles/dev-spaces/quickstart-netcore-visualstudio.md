---
title: 'Hibakeresés és itefedés a Kubernetesen: Visual Studio & .NET Core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: Ez a rövid útmutató bemutatja, hogyan használhatja az Azure Dev Spaces és a Visual Studio segítségével a .NET Core alkalmazások hibakeresését és gyors iterálni az Azure Kubernetes-szolgáltatásban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Helm, szolgáltatásháló, szolgáltatásháló útválasztás, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 5d0d1541600f4c10b021d5d7f7f435f1aa5ae589
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239721"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Rövid útmutató: Hibakeresés és műveletlema a Kubernetesen: Visual Studio & .NET Core – Azure dev spaces

Ebből az útmutatóból a következőket tudhatja meg:

- Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
- Iteratív kódfejlesztés tárolókban a Visual Studio használatával.
- A fürtben futó hibakeresési kód a Visual Studio használatával.

Az Azure Dev Spaces is lehetővé teszi a hibakeresést és a következő használatával ite):
- [Java és Visual Studio kód](quickstart-java.md)
- [Node.js és Visual Studio-kód](quickstart-nodejs.md)
- [.NET Core és Visual Studio kód](quickstart-netcore.md)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs fiókja, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).
- Visual Studio 2019 Windows rendszeren az Azure Development számítási feladat telepítve van. A Windows visual Studio 2017-et a Webdevelopment munkaterheléssel és a [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools) alkalmazással is használhatja. Ha nincs telepítve a Visual Studio, töltse le [itt.](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes-szolgáltatásfürt létrehozása

AKS-fürtöt támogatott régióban kell [létrehoznia.][supported-regions] Fürt létrehozása:

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com)
1. Válassza *a + Erőforrás létrehozása > Kubernetes szolgáltatás lehetőséget.* 
1. Adja meg az _Előfizetés_, _Erőforráscsoport_, _Kubernetes-fürt nevét_, _régió_, _Kubernetes-verzió_és _DNS-névelőtag._

    ![AKS létrehozása az Azure Portalon](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Kattintson az *Áttekintés + létrehozás* elemre.
1. Kattintson *a Létrehozás gombra.*

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az Azure dev spaces engedélyezése az AKS-fürtön

Nyissa meg az AKS-fürtöt az Azure Portalon, és kattintson a *Fejlesztői terek*elemre. Módosítsa *a Fejlesztői szóközök használata lehetőséget* *igen* értékre, és kattintson a *Mentés gombra.*

![Fejlesztői tárolók engedélyezése az Azure Portalon](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Új ASP.NET webalkalmazás létrehozása

1. Nyissa meg a Visual Studiót.
1. Új projekt létrehozása.
1. Válassza *ASP.NET Core webalkalmazás lehetőséget,* majd kattintson a *Tovább*gombra.
1. Nevezze el a projekt *webfrontendját,* és kattintson *a Létrehozás gombra.*
1. Amikor a rendszer kéri, válassza a *webalkalmazást (Modell-nézet-vezérlő)* a sablonhoz.
1. Felül válassza a *.NET Core* és *ASP.NET Core 2.1* elemet.
1. Kattintson *a Létrehozás gombra.*

## <a name="connect-your-project-to-your-dev-space"></a>A projekt csatlakoztatása a fejlesztői térhez

A projektben válassza ki az **Azure Dev Spaces** az indítási beállítások legördülő az alábbiak szerint.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Az Azure dev spaces párbeszédpanelen válassza ki az *előfizetést* és az *Azure Kubernetes-fürtöt.* Hagyja, hogy *a Terület terület* alapértelmezés szerint *legyen* beállítva, és engedélyezze a *Nyilvánosan elérhető* jelölőnégyzetet. Kattintson az *OK* gombra.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Ez a folyamat a szolgáltatást az *alapértelmezett* fejlesztői területre telepíti egy nyilvánosan elérhető URL-címmel. Ha egy olyan fürtöt választ, amely nincs az Azure Dev Spaceshez konfigurálva, a rendszer egy üzenetben rákérdez, hogy szeretné-e konfigurálni. Kattintson az *OK* gombra.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Az *alapértelmezett* fejlesztési területen futó szolgáltatás nyilvános URL-címe a *Kimenet* ablakban jelenik meg:

```cmd
Starting warmup for project 'webfrontend'.
Waiting for namespace to be provisioned.
Using dev space 'default' with target 'MyAKS'
...
Successfully built 1234567890ab
Successfully tagged webfrontend:devspaces-11122233344455566
Built container image in 39s
Waiting for container...
36s

Service 'webfrontend' port 'http' is available at http://default.webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

A fenti példában a http://default.webfrontend.1234567890abcdef1234.eus.azds.io/nyilvános URL- cím . 

Válassza **a Debug** lehetőséget, majd **indítsa el a hibakeresést.** Néhány másodperc múlva elindul a szolgáltatás, és a Visual Studio megnyit egy böngészőt a szolgáltatás nyilvános URL-címével. Ha egy böngésző nem nyílik meg automatikusan, keresse meg a szolgáltatás nyilvános URL-címét a böngészőben, és lépjen kapcsolatba a fejlesztői térben futó szolgáltatással.

Előfordulhat, hogy ez a folyamat letiltotta a nyilvános hozzáférést a szolgáltatáshoz. A nyilvános hozzáférés engedélyezéséhez frissítheti a [bejövő támadások értékét a *values.yaml*][ingress-update].

## <a name="update-code"></a>Kód frissítése

Ha a Visual Studio még mindig csatlakozik a fejlesztői területhez, kattintson a leállítás gombra. A 20-as `Controllers/HomeController.cs` sor módosítása a következőre:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Mentse a módosításokat, és válassza **a Hibakeresés** lehetőséget, majd **indítsa el a hibakeresést.** Néhány másodperc múlva elindul a szolgáltatás, és a Visual Studio megnyit egy böngészőt a szolgáltatás nyilvános URL-címével. Ha egy böngésző nem nyílik meg automatikusan, keresse meg a szolgáltatás nyilvános URL-címét a böngészőben, és kattintson a *Be*gombra. Figyelje meg, hogy megjelenik a frissített üzenet.

Ahelyett, hogy újralétre, és újraüzembe egy új tárolórendszerkép minden egyes alkalommal kód szerkesztések készülnek, az Azure Dev Spaces növekményesen újrafordítja a kódot a meglévő tárolón belül, hogy egy gyorsabb szerkesztési/hibakeresési hurok.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Töréspontok beállítása és használata hibakereséshez

Ha a Visual Studio még mindig csatlakozik a fejlesztői területhez, kattintson a leállítás gombra. Nyissa `Controllers/HomeController.cs` meg és kattintson valahol a 20-as vonalon, hogy a kurzort oda tegye. Ha az *F9* leadott töréspontot szeretné beállítani, vagy kattintson *a Hibakeresés gombra,* majd *a Töréspont váltása gombra*. Ha a szolgáltatást hibakeresési módban szeretné elindítani a fejlesztői területen, nyomja meg az *F5* billentyűt, vagy kattintson *a Hibakeresés* gombra, majd *indítsa el a hibakeresést.*

Nyissa meg a szolgáltatást a böngészőben, és észreveszi, hogy nem jelenik meg üzenet. Térjen vissza a Visual Studio-ba, és figyelje meg a 20-as vonalat. A beállított töréspont szüneteltette a szolgáltatást a 20-as sorban. A szolgáltatás folytatásához nyomja le az *F5* gombot, vagy kattintson *a Hibakeresés gombra,* majd *a Folytatás gombra.* Térjen vissza a böngészőbe, és figyelje meg, hogy az üzenet megjelenik.

A szolgáltatás Kubernetes-ben való futtatása közben egy hibakeresőcsatlakoztatva teljes hozzáféréssel rendelkezik a hibakeresési információkhoz, például a hívásveremhez, a helyi változókhoz és a kivételadatokhoz.

Távolítsa el a töréspontot azáltal, hogy `Controllers/HomeController.cs` a kurzort a 20-as vonalon helyezi el, és az *F9-et*üti.

## <a name="clean-up-your-azure-resources"></a>Az Azure-erőforrások karbantartása

Nyissa meg az erőforráscsoportot az Azure Portalon, és kattintson az *Erőforráscsoport törlése gombra.* Másik lehetőségként használhatja az [aks delete](/cli/azure/aks#az-aks-delete) parancsot:

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Több tároló használata és fejlesztés csapatban](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works-up.md#how-running-your-code-is-configured
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
