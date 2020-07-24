---
title: 'Hibakeresés és iteráció a Kubernetes-on: Visual Studio & .NET Core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: Ez a rövid útmutató bemutatja, hogyan használható az Azure dev Spaces és a Visual Studio a .NET Core-alkalmazások hibakereséséhez és gyors megismétléséhez az Azure Kubernetes Service-ben
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 8279a32ece16209c1dd5bca13d08e22b283677ee
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87007004"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Gyors útmutató: Hibakeresés és iteráció a Kubernetes-on: Visual Studio & .NET Core – Azure dev Spaces

Ebből az útmutatóból a következőket tudhatja meg:

- Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
- Iteratív kódfejlesztés tárolókban a Visual Studio használatával.
- A fürtön futó hibakeresési kód a Visual Studio használatával.

Az Azure dev Spaces Emellett lehetővé teszi a hibakeresést és a közelítést a használatával:
- [A Java és a Visual Studio Code](quickstart-java.md)
- [ANode.js és a Visual Studio Code](quickstart-nodejs.md)
- [A .NET Core és a Visual Studio Code](quickstart-netcore.md)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs fiókja, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).
- Visual Studio 2019 Windows rendszeren, amelyen telepítve van az Azure fejlesztői munkaterhelése. Ha nincs telepítve a Visual Studio, töltse le [itt](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Az [Azure CLI telepítve van](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service-fürt létrehozása

Létre kell hoznia egy AK-fürtöt egy [támogatott régióban][supported-regions]. Az alábbi parancsok létrehoznak egy *MyResourceGroup* nevű erőforráscsoportot és egy *MyAKS*nevű AK-fürtöt.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az Azure dev-helyek engedélyezése az AK-fürtön

A `use-dev-spaces` parancs használatával engedélyezze a fejlesztői szóközöket az AK-fürtön, és kövesse az utasításokat. Az alábbi parancs lehetővé teszi a dev Spaces használatát a *MyAKS* -fürtön a *MyResourceGroup* csoportban, és létrehoz egy *alapértelmezett* fejlesztői helyet.

> [!NOTE]
> A `use-dev-spaces` parancs az Azure dev Spaces CLI-t is telepíti, ha még nincs telepítve. Az Azure dev Spaces CLI nem telepíthető a Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="create-a-new-aspnet-web-app"></a>Új ASP.NET-Webalkalmazás létrehozása

1. Nyissa meg a Visual Studiót.
1. Új projekt létrehozása.
1. Válassza ki *ASP.net Core webalkalmazást* , és kattintson a *tovább*gombra.
1. Nevezze el a projekt *webfelületét* , és kattintson a *Létrehozás*gombra.
1. Ha a rendszer kéri, válassza a *webalkalmazás (Model-View-Controller)* elemet a sablonhoz.
1. Válassza a *.net Core* és a *ASP.net Core 2,1* elemet a felső részen.
1. Kattintson a *Létrehozás* lehetőségre.

## <a name="connect-your-project-to-your-dev-space"></a>A projekt összekötése a fejlesztői területtel

A projektben válassza az **Azure dev Spaces** lehetőséget az indítási beállítások legördülő listából az alább látható módon.

![Képernyőfelvétel a Visual Studio felhasználói felületéről a Kiemelt és kiválasztott IIS Express lehetőséggel és az Azure dev Spaces beállítás kiválasztásával.](media/get-started-netcore-visualstudio/LaunchSettings.png)

Az Azure dev Spaces párbeszédpanelen válassza ki az *előfizetését* és az *Azure Kubernetes-fürtöt*. Hagyja üresen a *helyet* az *alapértelmezett* értékre, és engedélyezze a *nyilvánosan elérhető* jelölőnégyzetet. Kattintson az *OK* gombra.

![Képernyőkép az Azure dev Spaces párbeszédpanelről.](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Ez a folyamat egy nyilvánosan elérhető URL-címmel telepíti a szolgáltatást az *alapértelmezett* fejlesztői tárhelyre. Ha egy olyan fürtöt választ, amely nincs az Azure Dev Spaceshez konfigurálva, a rendszer egy üzenetben rákérdez, hogy szeretné-e konfigurálni. Kattintson az *OK* gombra.

![Képernyőkép az Azure Spaces-erőforrás hozzáadása párbeszédpanelről.](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Az *alapértelmezett* fejlesztői területen futó szolgáltatás nyilvános URL-címe a *kimeneti* ablakban jelenik meg:

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

Service 'webfrontend' port 'http' is available at `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/`
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

A fenti példában a nyilvános URL-cím: `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/` . 

Válassza a **hibakeresés** lehetőséget, majd **indítsa el a hibakeresést**. Néhány másodperc elteltével a szolgáltatás elindul, és a Visual Studio megnyit egy böngészőt a szolgáltatás nyilvános URL-címével. Ha egy böngésző nem nyílik meg automatikusan, navigáljon a szolgáltatás nyilvános URL-címéhez egy böngészőben, és lépjen kapcsolatba a fejlesztői térben futó szolgáltatással.

Előfordulhat, hogy a folyamat letiltotta a szolgáltatáshoz való nyilvános hozzáférést. A nyilvános hozzáférés engedélyezéséhez frissítheti a [bejövő *értékeket a Values. YAML*][ingress-update].

## <a name="update-code"></a>Kód frissítése

Ha a Visual Studio továbbra is csatlakozik a fejlesztői területhez, kattintson a Leállítás gombra. A 20. sor módosítása a következőre `Controllers/HomeController.cs` :
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Mentse a módosításokat, és válassza a **hibakeresés** lehetőséget, majd **indítsa el a hibakeresést**. Néhány másodperc elteltével a szolgáltatás elindul, és a Visual Studio megnyit egy böngészőt a szolgáltatás nyilvános URL-címével. Ha egy böngésző nem nyílik meg automatikusan, navigáljon a szolgáltatás nyilvános URL-címéhez egy böngészőben, és kattintson *a névjegy*elemre. Figyelje meg, hogy a frissített üzenet jelenik meg.

Az új Container-lemezképek újraépítése és újratelepítése helyett az Azure dev Spaces fokozatosan újrafordítja a kódot a meglévő tárolóban, így gyorsabb szerkesztési/hibakeresési hurkot biztosít.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Töréspontok beállítása és használata hibakereséshez

Ha a Visual Studio továbbra is csatlakozik a fejlesztői területhez, kattintson a Leállítás gombra. Nyissa meg `Controllers/HomeController.cs` , majd kattintson a 20. sorban a kurzor mozgatásához. Ha a töréspontot az *F9 billentyűre* szeretné beállítani, vagy kattintson a *hibakeresés* lehetőségre, majd a *töréspontot*. Ha hibakeresési módban szeretné elindítani a szolgáltatást a fejlesztői térben, nyomja meg az *F5 billentyűt* , *vagy kattintson a hibakeresés elemre* , majd *indítsa el a hibakeresést*.

Nyissa meg a szolgáltatást egy böngészőben, és figyelje meg, hogy nem jelenik meg üzenet. Térjen vissza a Visual studióhoz, és figyelje meg, hogy a 20. sor ki van emelve. A beállított töréspont szüneteltette a szolgáltatást a 20. sorban. A szolgáltatás folytatásához nyomja meg az *F5 billentyűt* , vagy kattintson a *hibakeresés* gombra, és *folytassa a művelettel*. Térjen vissza a böngészőhöz, és figyelje meg, hogy az üzenet most megjelenik.

A szolgáltatás Kubernetes való futtatásakor a hibakeresőhöz csatolva teljes hozzáférése van a hibakeresési adatokhoz, például a hívási verem, a helyi változók és a kivételek adataihoz.

Távolítsa el a töréspontot úgy, hogy a kurzort a 20-as `Controllers/HomeController.cs` és az *F9 billentyű*megnyomásával helyezi el.

## <a name="clean-up-your-azure-resources"></a>Azure-erőforrások karbantartása

Navigáljon az erőforráscsoporthoz a Azure Portalon, majd kattintson az *erőforráscsoport törlése*elemre. Azt is megteheti, hogy az az [AK delete](/cli/azure/aks#az-aks-delete) parancsot használja:

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Több tároló használata és fejlesztés csapatban](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works-up.md#how-running-your-code-is-configured
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
