---
title: Team development using .NET Core and Visual Studio
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: 895d2edbb268eab9944909ecda7193ce945bbf39
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325537"
---
# <a name="team-development-using-net-core-and-visual-studio-with-azure-dev-spaces"></a>Team development using .NET Core and Visual Studio with Azure Dev Spaces

In this tutorial, you'll learn how a team of developers can simultaneously collaborate in the same Kubernetes cluster using Dev Spaces.

## <a name="learn-about-team-development"></a>A csapatban végzett fejlesztés bemutatása

Eddig úgy futtatta alkalmazása kódját, mintha Ön dolgozna egyedül fejlesztőként az alkalmazáson. Ebben a szakaszban megismerheti, hogyan teszi zökkenőmentessé az Azure Dev Spaces a csapatban végzett fejlesztést:
* Enable a team of developers to work in the same environment, by working in a shared dev space or in distinct dev spaces as needed.
* Támogatja, hogy minden fejlesztő elszigetelten iterálhassa a saját kódját, miközben nem kell tartani attól, hogy mindeközben mások kódja működésképtelenné válik.
* Végpontok között lehet tesztelni a kódot a kód véglegesítése előtt, és nem szükséges utánzatokat létrehozni vagy függőségeket szimulálni.

### <a name="challenges-with-developing-microservices"></a>A mikroszolgáltatások fejlesztésének kihívásai
Your sample application isn't complex at the moment. A valóságban a fejlesztés során folyamatosan merülnek fel kihívások, ahogy egyre több szolgáltatást vesz fel, illetve ahogy nő a fejlesztői csapat.

* Your development machine may not have enough resources to run every service you need at once.
* Some services may need to be publicly reachable. For example, a service may need to have an endpoint that responds to a webhook.
* If you want to run a subset of services, you have to know the full dependency hierarchy between all your services. Determining this hierarchy can be difficult, especially as your number of services increase.
* Néhány fejlesztő ilyenkor szimulációba kezd, vagy utánzatokat készít a szolgáltatás függőségeiről. This approach can help, but managing those mocks can soon impact development cost. Plus, this approach leads to your development environment looking different from production, which can lead to subtle bugs occurring.
* It follows that doing any type of integration testing becomes difficult. Az integráció tesztelése a valóságban csak a véglegesítés után történhet meg, ami azt jelenti, hogy a fejlesztési ciklus későbbi szakaszaiban problémákat tapasztalhat.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Munka egy megosztott Dev Spaces-térben
Az Azure Dev Spaces segítségével beállíthat egy *megosztott* Dev Spaces-teret az Azure-ban. Minden fejlesztőnek csak az alkalmazásból rá eső részre kell koncentrálnia, és iterációs módszerrel fejlesztheti a *véglegesítés előtti kódot* egy olyan Dev Spaces-térben, amely már tartalmaz minden olyan szolgáltatást és felhőerőforrást, amelyektől az egyes forgatókönyvek függhetnek. A függőségek mindig naprakészek, a fejlesztők pedig mindig az éles környezetet tükröző módon dolgozhatnak.

### <a name="work-in-your-own-space"></a>Munkavégzés a saját térben
A szolgáltatás kódjának fejlesztése során, de még leadás előtt a kód sokszor nincs optimális állapotban. Az iteratív módszerrel újra és újra kell formálni, tesztelni kell, megoldásokkal kísérletezni. Az Azure Dev Spaces biztosítja a **tér** fogalmát, amely lehetővé teszi, hogy elszigetelten dolgozhasson, anélkül, hogy a többi csapattag munkájára kihatással lennének a fejlesztési munkálatok.

## <a name="use-dev-spaces-for-team-development"></a>Use Dev Spaces for team development
Let's demonstrate these ideas with a concrete example using our *webfrontend* -> *mywebapi* sample application. We'll imagine a scenario where a developer, Scott, needs to make a change to the *mywebapi* service, and *only* that service. The *webfrontend* won't need to change as part of Scott's update.

_Without_ using Dev Spaces, Scott would have a few ways to develop and test his update, none of which are ideal:
* Run ALL components locally, which requires a more powerful development machine with Docker installed, and potentially MiniKube.
* Run ALL components in an isolated namespace on the Kubernetes cluster. Since *webfrontend* isn't changing, using an isolated namespace is a waste of cluster resources.
* ONLY run *mywebapi*, and make manual REST calls to test. This type of testing doesn't test the full end-to-end flow.
* Add development-focused code to *webfrontend* that allows the developer to send requests to a different instance of *mywebapi*. Adding this code complicates the *webfrontend* service.

### <a name="set-up-your-baseline"></a>Set up your baseline
First we'll need to deploy a baseline of our services. This deployment will represent the "last known good" so you can easily compare the behavior of your local code vs. the checked-in version. We'll then create a child space based on this baseline so we can test our changes to *mywebapi* within the context of the larger application.

1. Clone the [Dev Spaces sample application](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Check out the remote branch *azds_updates*: `git checkout -b azds_updates origin/azds_updates`
1. Zárjon be minden F5/hibakeresési munkamenetet minden szolgáltatás esetében, de tartsa nyitva a projekteket saját Visual Studio-ablakukban.
1. Switch to the Visual Studio window with the _mywebapi_ project.
1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, majd válassza a **Tulajdonságok** lehetőséget.
1. Válassza a **Hibakeresés** lehetőséget a bal oldalon az Azure Dev Spaces-beállítások megjelenítéséhez.
1. Select **Change** to create the space that will be used when you F5 or Ctrl+F5 the service.
1. In the Space dropdown, select **\<Create New Space…\>** .
1. Make sure the parent space is set to **\<none\>** , and enter space name **dev**. Kattintson az OK gombra.
1. Press Ctrl+F5 to run _mywebapi_ without the debugger attached.
1. Switch to the Visual Studio window with the _webfrontend_ project and press Ctrl+F5 to run it as well.

> [!Note]
> Néha frissíteni kell a böngészőlapot azt követően, hogy a weboldal először megjelenik a Ctrl+F5 lenyomása után.

> [!TIP]
> The above steps manually set up a baseline, but we recommend teams use CI/CD to automatically keep your baseline up to date with committed code.
>
> Check out our [guide to setting up CI/CD with Azure DevOps](how-to/setup-cicd.md) to create a workflow similar to the following diagram.
>
> ![Example CI/CD diagram](media/common/ci-cd-complex.png)

Anyone who opens the public URL and navigates to the web app will invoke the code path you have written which runs through both services using the default _dev_ space. Now suppose you want to continue developing *mywebapi* - how can you do this and not interrupt other developers who are using the dev space? Ehhez be kell állítania a saját terét.

### <a name="create-a-new-dev-space"></a>Új Dev Spaces-tér létrehozása
A Visual Studióból létrehozhat további tereket, amelyek akkor lesznek használatban, ha a szolgáltatásában lenyomja az F5, illetve a Ctrl+F5 billentyűt. Olyan nevet adhat a tereknek, amilyet csak szeretne, és rugalmasan kezelheti a jelentését (pl.: _sprint4_ or _demo_).

Új teret a következő módon hozhat létre:
1. Switch to the Visual Studio window with the *mywebapi* project.
2. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, majd válassza a **Tulajdonságok** lehetőséget.
3. Válassza a **Hibakeresés** lehetőséget a bal oldalon az Azure Dev Spaces-beállítások megjelenítéséhez.
4. Itt módosíthatja vagy létrehozhatja azt a fürtöt és/vagy teret, amelyet a program akkor használ, ha lenyomja az F5, illetve a Ctrl+F5 billentyűt. *Győződjön meg róla, hogy a korábban létrehozott Azure Dev Spaces-tér van kiválasztva*.
5. In the Space dropdown, select **\<Create New Space…\>** .

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. In the **Add Space** dialog, set the parent space to **dev**, and enter a name for your new space. Használhatja a saját nevét (pl.: „scott”) az új térhez, így munkatársai számára is könnyen beazonosítható a munkájához használt tér. Kattintson az **OK** gombra.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Most már láthatja az AKS-fürtöt és az új, a projekttulajdonságok oldalán kiválasztott teret.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Kód frissítése a *mywebapi*-hoz

1. In the *mywebapi* project make a code change to the `string Get(int id)` method in file `Controllers/ValuesController.cs` as follows:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Állítson be egy töréspontot a frissített kódrészletben (elképzelhető, hogy korábban már megadott egyet).
3. Hit F5 to start the _mywebapi_ service, which will start the service in your cluster using the selected space. The selected space in this case is _scott_.

Ez az ábra segít megérteni, hogyan működnek a különféle terek. The purple path shows a request via the _dev_ space, which is the default path used if no space is prepended to the URL. The pink path shows a request via the _dev/scott_ space.

![](media/common/Space-Routing.png)

Az Azure Dev Spaces e beépített funkciója lehetővé teszi, hogy végpontok között tesztelhesse a kódot egy megosztott környezetben anélkül, hogy minden egyes fejlesztőnek ismételten létre kellene hoznia a saját terük teljes szolgáltatási vermét. Ez az útválasztás megköveteli, hogy a propagálási fejlécek továbbítva legyenek az alkalmazáskódban, ahogy az az útmutató előző lépésében is látható.

### <a name="test-code-running-in-the-_devscott_-space"></a>Test code running in the _dev/scott_ space
To test your new version of *mywebapi* in conjunction with *webfrontend*, open your browser to the public access point URL for *webfrontend* (for example, http://dev.webfrontend.123456abcdef.eus.azds.io) and go to the About page. Az eredeti, „Hello from webfrontend and Hello from mywebapi” üzenetnek kell megjelennie.

Most adja hozzá a „scott.s” kifejezést part to the URL so it reads something like http\://scott.s.dev.webfrontend.123456abcdef.eus.azds.io and refresh the browser. The breakpoint you set in your *mywebapi* project should get hit. Nyomja le az F5-öt a folytatáshoz. A böngészőben megjelenik az új üzenet: „Hello from webfrontend and mywebapi now says something new.” This is because the path to your updated code in *mywebapi* is running in the _dev/scott_ space.

Once you have a _dev_ space that always contains your latest changes, and assuming your application is designed to take advantage of DevSpace's space-based routing as described in this tutorial section, hopefully it becomes easy to see how Dev Spaces can greatly assist in testing new features within the context of the larger application. Rather than having to deploy _all_ services to your private space, you can create a private space that derives from _dev_, and only "up" the services you're actually working on. The Dev Spaces routing infrastructure will handle the rest by utilizing as many services out of your private space as it can find, while defaulting back to the latest version running in the _dev_ space. And better still, _multiple_ developers can actively develop different services at the same time in their own space without disrupting each other.

### <a name="well-done"></a>Remek!
Elvégezte az első lépéseket ismertető útmutatót! Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
> * Iteratív kódfejlesztés a tárolókban.
> * Két külön szolgáltatás egymástól függetlenül történő fejlesztése, és a használt Kubernetes DNS-szolgáltatás észlelésével hívásindítás egy másik szolgáltatásba.
> * A kód hatékony fejlesztése és tesztelése, csapatkörnyezetben.
> * Establish a baseline of functionality using Dev Spaces to easily test isolated changes within the context of a larger microservice application

Most, hogy megismerte az Azure Dev Spacest, [megoszthatja a létrehozott Dev Spaces-teret egy csapattaggal](how-to/share-dev-spaces.md), és megmutathatja neki, milyen egyszerű az együttműködés.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Ha egy fürt egy Azure Dev Spaces-példányát teljesen, az összes Dev Spaces-térrel és benne futó szolgáltatással együtt törölni szeretné, használja az `az aks remove-dev-spaces` parancsot. Tartsa észben, hogy ez a művelet nem vonható vissza. Újra hozzáadhat Azure Dev Spaces-támogatást a fürtön, de azt a rendszer úgy kezeli, mintha elölről kezdené a folyamatot. A régi szolgáltatások és a tárolóhelyek nem állíthatók vissza.

Az alábbi példa listázza az aktív előfizetése Azure Dev Spaces-vezérlőit, majd törli a myaks-rg erőforráscsoportban lévő myaks AKS-fürthöz társított Azure Dev Spaces-vezérlőt.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
