---
title: Fejlesztés a .NET Core, az aks-en az Azure fejlesztői, szóközök és a Visual Studio 2017 használatával
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 03/22/2019
ms.topic: quickstart
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Helm, a szolgáltatás háló, a szolgáltatás háló útválasztás, a kubectl, a k8s
manager: jeconnoc
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 9afca253bd188556ad6a3f6e081fb2eccc4c81cb
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361259"
---
# <a name="quickstart-develop-with-net-core-on-kubernetes-with-azure-dev-spaces-visual-studio-2017"></a>Gyors útmutató: Fejlesztés a .NET Core Kubernetes az Azure fejlesztési szóközöket (a Visual Studio 2017)

Ebből az útmutatóból a következőket tudhatja meg:

- Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
- Iteratív fejlesztés kód a Visual Studio 2017 használatával.
- Hibakeresés a Visual Studio 2017 használatával fürtön futó kódot.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs fiókja, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).
- Visual Studio 2017-et a Windows a webalkalmazás-fejlesztési számítási feladattal együtt. Ha nincs telepítve, [innen](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) letöltheti.
- [A Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools) telepítve.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Az Azure Kubernetes Service-fürt létrehozása

Az AKS-fürt létre kell hoznia egy [támogatott régió](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams). Fürt létrehozása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. Válassza ki *+ erőforrás létrehozása > Kubernetes-szolgáltatást*. 
1. Adja meg a _előfizetés_, _erőforráscsoport_, _Kubernetes-fürt neve_, _régió_, _Kubernetes-verzió_, és _DNS-név előtagja_.

    ![Az AKS létrehozása az Azure Portalon](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Kattintson az *Áttekintés + létrehozás* elemre.
1. Kattintson a *Create* (Létrehozás) gombra.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az AKS-fürt az Azure fejlesztési tárolóhelyek engedélyezése

Az AKS-fürt az Azure Portalon keresse meg és kattintson *fejlesztési tárolóhelyek*. Változás *fejlesztési szóközt engedélyezni* való *Igen* kattintson *mentése*.

![Fejlesztési szóközt engedélyezni az Azure Portalon](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Egy új ASP.NET-webalkalmazás létrehozása

1. Nyissa meg a Visual Studio 2017-et.
1. Új projekt létrehozása.
1. Válasszon *ASP.NET Core-webalkalmazás* és nevezze el a projektet *webfrontend*.
1. Kattintson az *OK* gombra.
1. Amikor a rendszer kéri, válassza ki a *webalkalmazás (Model-View-Controller)* a sablonhoz.
1. Válassza ki *.NET Core* és *ASP.NET Core 2.0* tetején.
1. Kattintson az *OK* gombra.

## <a name="connect-your-project-to-your-dev-space"></a>A fejlesztési tárhely-projekt csatlakoztatása

Válassza ki a projekt **Azure fejlesztési tárolóhelyek** a lent látható módon indítási beállítások legördülő listából.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Az Azure fejlesztési tárolóhelyek párbeszédpanelen válassza ki a *előfizetés* és *Azure Kubernetes-fürt*. Hagyja *terület* beállítása *alapértelmezett* , és engedélyezze a *nyilvánosan hozzáférhető* jelölőnégyzetet. Kattintson az *OK* gombra.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Ez a folyamat üzembe helyezi a szolgáltatást a *alapértelmezett* fejlesztési terület nyilvánosan elérhető URL-címet. Ha egy olyan fürtöt választ, amely nincs az Azure Dev Spaceshez konfigurálva, a rendszer egy üzenetben rákérdez, hogy szeretné-e konfigurálni. Kattintson az *OK* gombra.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

A futó szolgáltatás nyilvános URL-CÍMÉT a *alapértelmezett* fejlesztési hely jelenik meg a *kimeneti* ablakban:

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

Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

A fenti példában a nyilvános URL-je http://webfrontend.1234567890abcdef1234.eus.azds.io/. Keresse meg a szolgáltatás nyilvános URL-CÍMÉT, és a szolgáltatás fut a fejlesztési tárhely használata.

## <a name="update-code"></a>Kód frissítése

Ha a Visual Studio 2017 fejlesztői tárhely továbbra is össze van kapcsolva, kattintson a Leállítás gombra. Változtassa meg a 20 sort `Controllers/HomeController.cs` való:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

A módosítások mentéséhez és a szolgáltatás használatának megkezdéséhez **Azure fejlesztési tárolóhelyek** az indítási beállítások legördülő listából. Nyissa meg a nyilvános URL-címét a szolgáltatás egy böngészőt, és kattintson a *kapcsolatos*. Figyelje meg, hogy a frissített üzenet jelenik meg.

Újraépítése és a egy új tárolórendszerképet minden alkalommal, amikor a kód módosításokat végzett, az Azure fejlesztési tárolóhelyek növekményes újbóli üzembe helyezés helyett újrafordítja a kódot a Szerkesztés/debug gyorsabb hurkot biztosít a meglévő tárolón belül.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Beállítás és hibakeresési töréspontok használatával

Ha a Visual Studio 2017 fejlesztői tárhely továbbra is össze van kapcsolva, kattintson a Leállítás gombra. Nyissa meg `Controllers/HomeController.cs` valahol kattintson a sor létezik helyezze a kurzort a 20. Állítson be egy töréspontot, nyomja le az *F9* , vagy kattintson *Debug* majd *töréspont*. Indítsa el a szolgáltatást a fejlesztési tárhelyre hibakeresési módban, kattintson a *F5* , vagy kattintson *Debug* majd *Start Debugging*.

Egy böngészőben nyissa meg a szolgáltatást, és figyelje meg, hogy nem jelenik meg. Térjen vissza a Visual Studio 2017-et, és vizsgálja meg, 20 sor van kijelölve. A töréspont beállított sor 20 a szolgáltatás szünetel. A szolgáltatás folytatásához nyomja le az *F5* , vagy kattintson *Debug* majd *Folytatás*. Térjen vissza a böngészőben, és figyelje meg, hogy az üzenet jelenik meg.

Miközben fut a szolgáltatás a Kubernetes egy hibakeresővel, akkor a hibakeresési információkat, például a hívási verem, a helyi változókat és a kivétel adatai teljes hozzáféréssel rendelkezik.

A töréspont eltávolítása ehhez vigye a kurzort a 20 sor `Controllers/HomeController.cs` és lenyomásával *F9*.

## <a name="clean-up-your-azure-resources"></a>Az Azure-erőforrások törlése

Keresse meg az erőforráscsoportot az Azure Portalon, és kattintson a *erőforráscsoport törlése*. Másik lehetőségként használhatja a [az aks törlése](/cli/azure/aks#az-aks-delete) parancsot:

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tárolók és a csoportos fejlesztése](multi-service-netcore-visualstudio.md)
